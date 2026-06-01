# KeymanMCP — Project Proposal

**Project Name:** KeymanMCP (Model Context Protocol Server for Keyman Keyboard Development)

**Duration:** 4 weeks (concurrent, collaborative)

**Team Composition:**
- 1 AI/Keyman Specialist (Tech Lead)
- 1 Keyman Trainer (Project Lead / Product Owner)
- 3 Interns (Development Team)

---

## Executive Summary

KeymanMCP is a Python-based MCP (Model Context Protocol) server that enables AI assistants (Claude, Copilot, Gemini) to create and adapt Keyman keyboard projects that are internally consistent, fully documented, and compatible with both desktop and touch platforms.

The tool eliminates manual file coordination by automating the creation, validation, and building of Keyman keyboards — reducing the learning curve for new keyboard developers and enabling AI-assisted keyboard development workflows.

---

## Problem Statement

### Creating a Keyboard from Scratch

Creating a Keyman keyboard requires manual coordination of at least 6 interdependent file types:
- `.kmn` (keyboard source code)
- `.kps` (package descriptor)
- `.kvks` (desktop visual keyboard layout)
- `.keyman-touch-layout` (mobile/tablet touch layout)
- `README.md`, `HISTORY.md`, `LICENSE.md` (documentation)
- `welcome.htm` (welcome screen on first install)

These files must agree on key names, layer definitions, file references, BCP-47 language tags, and version numbers. **A single mismatch causes silent inconsistencies or compiler errors**.

### Adapting an Existing Keyboard

When modifying an existing keyboard (e.g., "make Khmer Angkor keyboard work for a different language"):
- Must rename and update dozens of internal references across all 6+ files
- Manual renaming is error-prone and time-consuming
- Touch and desktop layouts can drift out of sync during modifications
- Testing consistency requires inspection across all file types

**Current friction:**
- **From scratch:** New keyboard developers spend days learning file format details before writing their first rule
- **Adapting:** Developers manually clone and rename keyboards, introducing bugs
- **Both:** AI assistants hallucinate syntax without authoritative specifications
- **Both:** Consistency checking requires manual inspection across 6+ files
- **Both:** Touch and desktop layouts often drift out of sync

---

## Project Goals

### Primary Goals
1. **Create from scratch:** Enable AI assistants to guide users from "I need a keyboard for language X" to a buildable, documented, internally-consistent Keyman package **without requiring manual file coordination**.

2. **Adapt existing:** Enable AI assistants to help users clone, rename, and modify existing keyboards (e.g., "adapt Khmer Angkor for language Y") with all file and internal references automatically updated and kept in sync.

### Secondary Goals
1. Create an extensible API index of the Keyman keyboard language specification (virtual keys, system stores, touch layout properties, compiler error codes, code patterns)
2. Establish patterns for future MCP tools in the Keyman ecosystem
3. Demonstrate best practices for AI-assisted language technology development

---

## Key Capabilities

### Starting from Scratch

#### 1. Generate
Scaffold a complete new keyboard project (KMN or LDML format) with all required files pre-populated:
- `.kmn` or `.xml` keyboard source with header stores and minimal rules (ready for developer to edit)
- `.kps` package source with correct metadata and file list
- `.kvks` visual keyboard skeleton (desktop)
- `.keyman-touch-layout` JSON (phone + tablet with default/shift/numeric layers)
- `README.md`, `HISTORY.md`, `LICENSE.md`, `welcome.htm` documentation stubs
- `.kpj` project file

### Adapting an Existing Keyboard

#### 2. Copy/Adapt
Clone any existing keyboard from a local path, Keyman Cloud, or GitHub and automatically rename it:
- Run `kmc copy` to clone the project structure
- Rename all files to new keyboard ID
- Update all internal references:
  - Keyboard ID in `.kmn`/`.xml` header
  - Filenames in `.kps` and `.kpj`
  - File references in `.kps` and `.kmn`
  - Display name in `store(&NAME)` (optional)
  - Version reset to '1.0'
- Preserve all layer names, rules, and touch layout structure for the developer to modify
- Return list of files updated + any warnings about potential missed references

### Common to Both Workflows

#### 3. Build
Compile a keyboard project using the `kmc` CLI and return structured, human-readable output:
- Parse `kmc build` TSV error/warning output
- Enrich each compiler message with full description and context
- Return success/failure summary with actionable error details

#### 4. Validate
Check a keyboard project for internal consistency **without compiling**:
- **Touch/desktop key coverage**: Verify all keys defined in `.keyman-touch-layout` (phone + tablet, all layers) appear in `.kvks` (all shift states) and vice versa
- **Layer parity**: Confirm layer names match across `.kvks`, `.keyman-touch-layout`, and KMN rules (or LDML layers)
- **Documentation completeness**: Verify presence and non-emptiness of all required documentation files
- **KPS integrity**: Verify all files listed in `.kps` exist on disk; check metadata completeness (name, copyright, version, language tags)

#### 5. Inspect
Read and summarize any keyboard project:
- Extract keyboard ID, name, version, language tags, copyright, target platforms
- Report file inventory (which expected files are present / missing)
- Summarize touch layout structure (layers, key counts per layer, phone vs tablet)
- Summarize desktop layout structure (layers, key counts)
- Report any detected issues (missing files, empty metadata, mismatched IDs)

---

## Supported Keyboard Formats

| Format | Touch Support | Status |
|--------|--------------|--------|
| **KMN (.kmn)** | Full (.keyman-touch-layout + .kvks) | Fully supported |
| **LDML (.xml)** | Spec generated (CLDR 45), Keyman runtime support pending | Generated with warning |

---

## Technology & Architecture

**Language:** Python 3

**Core Dependencies:**
- `mcp` (Python MCP library, stdio transport)
- `pydantic` (data validation)
- `lxml` (XML parsing for .kps and .kvks files)

**CLI Tool:** `kmc` (Keyman Next Generation Compiler) — called as subprocess, **not bundled**

**Architecture Pattern:** Modular, follows FlexToolsMCP (existing AI-assisted SIL language technology MCP server)

**Key Innovation:** Pre-built **API Index** of the entire Keyman keyboard language specification:
- All 256+ virtual key names (K_A, K_SHIFT, K_BKSP, etc.)
- All 30+ system stores (&VERSION, &NAME, &TARGETS, etc.) with valid values
- All ~120 compiler error codes with descriptions
- Touch layout key properties and special labels
- Common KMN code patterns extracted from real keyboards

This index allows the server to generate correct `.kmn` and `.keyman-touch-layout` code rather than hallucinating syntax.

---

## Project Timeline & Milestones

### Week 1: Foundations (All hands)
**Goal:** Get everything running end-to-end; lay groundwork for parallel development

**AI Specialist + Intern A:**
- API index extraction framework
  - Implement 6 extractor scripts (vkeys, system stores, compiler messages, touch spec, KMN patterns, merge)
  - Build `refresh.py` orchestrator (analogous to FlexToolsMCP `refresh.py`)
  - Generate initial index files for Keyman 19.0
  - Commit index to repo

**AI Specialist + Interns B, C:**
- MCP server core + parsers
  - `server.py` — MCP server entry point, tool registration, async dispatch
  - Session state management, kmc version detection, index loading
  - Implement 5 file parsers: `kmn.py`, `kps.py`, `kvks.py`, `touch.py`, `ldml.py`
  - `build_keyboard` tool (simplest — wraps `kmc build`)

**By end of Week 1:** Team can run `python src/server.py` and call `build_keyboard` on test keyboards

---

### Week 2: Core Tools (All hands, parallel)
**Goal:** Implement the 3 remaining core tools with tests

**Intern A + AI Specialist (review):**
- `validate_consistency` tool
  - 4-point consistency checker (key coverage, layer parity, docs, KPS)
  - Integration tests with real keyboards from `D:\Github\keyboards\release\`

**Intern B + AI Specialist (review):**
- `generate_keyboard` tool
  - Scaffold new projects (KMN + LDML with touch warning)
  - Default touch layout generation (phone + tablet, default/shift/numeric layers)
  - Documentation stub generation

**Intern C + AI Specialist (review):**
- `copy_keyboard` + `inspect_keyboard` tools
  - Clone, rename, and update all internal references
  - File inventory and structure analysis
  - Consistency reporting

**By end of Week 2:** All 5 MCP tools functional; basic tests passing

---

### Week 3: Testing & Refinement (All hands)
**Goal:** Comprehensive testing, integration, documentation

**All Interns + Keyman Trainer:**
- End-to-end verification testing
  - Create new keyboard from scratch, validate, build ✓
  - Adapt existing keyboard (clone + rename), validate, build ✓
  - Inspect keyboard structure and detect errors ✓
  - Test with Keyman Cloud keyboards and local examples
  - Validate error messages are helpful

**AI Specialist:**
- Performance tuning, error handling
  - Profile API index loading time
  - Graceful degradation (missing kmc, missing index, file not found, etc.)
  - Refine error messages based on Trainer feedback

**Keyman Trainer:**
- UAT documentation + training materials
  - Test tool outputs with non-technical users
  - Document expected workflows
  - Identify gaps or confusing error messages

**By end of Week 3:** All tests passing; user documentation complete; Trainer sign-off

---

### Week 4: Polish & Handoff (All hands)
**Goal:** Production-ready, documented, deployable

**All:**
- Final refinements based on Week 3 feedback
  - Fix edge cases and error handling issues
  - Clarify documentation
  - Optimize performance bottlenecks

**AI Specialist:**
- Deployment & configuration
  - `mcp_config.json` for MCP client registration
  - `.env.example` setup instructions
  - `requirements.txt` finalization

**All:**
- Documentation handoff
  - `EXECUTIVE_SUMMARY.md`, `PLAN.md`, `API_INDEX.md`, `PROJECT_PROPOSAL.md` (all complete) ✓
  - Inline code comments + docstrings
  - Trainer-led knowledge transfer session

**By end of Week 4:** Production-ready KeymanMCP server; team knowledge transfer complete

---

## Success Criteria

### Technical
- [OK] All 5 MCP tools functional and tested
- [OK] API index covers Keyman 19.0 specification (vkeys, stores, messages, touch spec, patterns)
- [OK] `validate_consistency` catches all 4 classes of errors (key coverage, layer parity, docs, KPS)
- [OK] Parsers handle KMN and LDML source files correctly
- [OK] `kmc build` error output enriched with descriptions from index
- [OK] No external Node.js dependency at runtime (kmc called as subprocess)

### User Experience
- [OK] New keyboard scaffolding takes <5 seconds
- [OK] Keyboard adaptation (clone + rename) takes <2 seconds
- [OK] Consistency validation provides actionable, specific error messages
- [OK] AI assistant can generate valid `.kmn` code using the API index
- [OK] Keyman Trainer can explain tool output to non-technical users
- [OK] Developers can modify adapted keyboards without file coordination errors

### Project Maturity
- [OK] Complete documentation (PLAN.md, API_INDEX.md, inline code comments)
- [OK] All 5 verification tests pass
- [OK] Error handling and graceful degradation (e.g., missing kmc on PATH)
- [OK] Committed index files so users don't need Keyman source repo at runtime

---

## Roles & Responsibilities

### Keyman Trainer (Project Lead / Product Owner)
- **Week 1:** Define success criteria; review early tool outputs
- **Weeks 1–3:** Weekly review of tool outputs; provide keyboard examples for testing
- **Week 3:** Lead end-to-end verification testing
- **Deliverable:** User documentation and training materials

### AI/Keyman Specialist (Tech Lead)
- **Week 0:** Design API extraction framework; set coding standards; mentor interns on architecture
- **Weeks 1–4:** Code review; pair programming with interns on critical sections; solve architectural issues
- **Week 1:** Lead API index extraction and server core implementation
- **Week 3–4:** Performance tuning and error handling refinement
- **Deliverable:** API index framework; core server; architectural documentation

### Intern A (API & Index Developer)
- **Week 1:** Implement 6 extractor scripts; `refresh.py` orchestrator; generate Keyman 19.0 index
- **Week 2:** Validation tool implementation and testing
- **Weeks 3–4:** Integration testing; documentation

### Intern B (Parser & Generation Developer)
- **Week 1:** Implement 5 file parsers (.kmn, .kps, .kvks, .touch, .ldml)
- **Week 2:** Generate keyboard tool implementation
- **Weeks 3–4:** Integration testing; documentation

### Intern C (Server & Tooling Developer)
- **Week 1:** MCP server core + dispatcher; `build_keyboard` tool
- **Week 2:** Copy/Adapt and Inspect tools; testing
- **Weeks 3–4:** Integration testing; documentation

---

## Resource Requirements

### Hardware
- 3–4 development machines (Windows 11 Pro recommended)
- 1 reference server machine for testing (optional)

### Software
- Python 3.10+
- `kmc` CLI (npm install -g @keymanapp/kmc)
- Git + GitHub
- D:\Github\keyman, D:\Github\keyboards (local clones, ~5 GB total)

### Knowledge Base
- Keyman keyboard language (KMN) syntax — **provided by Trainer**
- Keyman `.kps` package format — **provided by Trainer**
- Touch layout JSON format — **documented in API_INDEX.md**
- MCP protocol basics — **documented in online MCP docs**

### Estimated Effort
- **Total team effort:** ~350–450 person-hours over 4 weeks
  - AI Specialist: 15–18 hours/week (60–72 total)
  - Keyman Trainer: 6–8 hours/week (24–32 total)
  - Interns: 30–35 hours/week each (360–420 total for 3)

---

## Risks & Mitigation

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|-----------|
| kmc version incompatibility | Medium | High | Build index extraction to handle multiple Keyman versions; test against 19.0 and 16.0 early |
| Touch layout format complexity | Medium | Medium | Prototype touch layout generation in Week 1; involve Trainer in Week 2 validation |
| LDML format under-documented | Low | Low | Generate CLDR 45-compliant spec; mark as "supported with warnings" |
| Parser fragility | Medium | Medium | Use robust XML parsers (lxml) for .kvks/.kps; test with diverse keyboard examples |
| Scope creep | Medium | Medium | Lock scope in kickoff; document future enhancements in ROADMAP.md |
| Team coordination issues | Low–Medium | Medium | Daily 15-min standups; clear task boundaries per intern; code review process |

---

## Deliverables Summary

| Week | Milestone | Deliverable(s) |
|------|-----------|-----------------|
| Week 1 | Foundations | API index extractors + refresh.py + server core + parsers + build_keyboard tool |
| Week 2 | Core Tools | All 5 MCP tools (generate, copy, inspect, validate, build) + basic tests |
| Week 3 | Testing & Refinement | All tests passing + UAT complete + trainer sign-off + user docs |
| Week 4 | Polish & Handoff | Production-ready server + deployment config + knowledge transfer |
| **Final** | **Delivery** | **KeymanMCP v1.0 — Production-ready MCP server + full documentation** |

---

## Future Enhancements (Post-Project)

- `search_kmn_patterns` — search for code examples by pattern name or feature
- `check_unicode_coverage` — verify keyboard includes all necessary Unicode characters
- `generate_test_cases` — auto-generate regression test XML files
- `suggest_optimizations` — ML-based suggestions for reducing rule complexity
- LDML runtime support once Keyman adds full touch layout support
- Support for Keyman 18.0+ as new versions release

---

## Questions for Stakeholders

1. **Keyman Trainer:** Are there specific keyboard examples (language, complexity, regions) you'd like tested in the verification suite?
2. **AI Specialist:** Should we version-gate features by minimum Keyman version (e.g., touch layouts require 9.0+, LDML requires 15.0+)?
3. **All:** What is the process for integrating with user MCP clients (Claude, Copilot, Gemini)?
4. **All:** Should we publish this as an open-source package to PyPI or GitHub Packages?
5. **All:** Should we set up CI/CD (GitHub Actions) for automated testing on each commit?

---

## Approval & Sign-Off

| Role | Name | Signature | Date |
|------|------|-----------|------|
| Keyman Trainer (Product Owner) | | | |
| AI/Keyman Specialist (Tech Lead) | | | |
| Project Sponsor | | | |
