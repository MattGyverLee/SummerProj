# KeymanMCP — Executive Summary

## What It Is

KeymanMCP is a Python-based MCP (Model Context Protocol) server that gives AI assistants
(Claude, etc.) the tools to create and adapt Keyman keyboard projects that are internally
consistent, fully documented, and compatible with both desktop and touch platforms.

It interacts exclusively with the `kmc` command-line compiler — no dependency on the
Keyman Developer IDE.

## What Problems It Solves

Creating or adapting a Keyman keyboard requires coordinating at least six interdependent file types
(.kmn, .kps, .kvks, .keyman-touch-layout, README.md, HISTORY.md, LICENSE.md, welcome.htm).
They must agree on key names, layer names, file references, BCP-47 language tags, and version
numbers. A mismatch anywhere causes a silent inconsistency or a compiler error.

**When starting from scratch:** Manually creating all files and ensuring they're consistent is error-prone.

**When adapting an existing keyboard:** Cloning and renaming files requires updating dozens of internal references, and keeping the original layout structure while modifying rules leads to subtle bugs (e.g., touch and desktop layers drifting out of sync).

KeymanMCP automates both workflows — creation and adaptation — and validates consistency so an AI assistant
can guide a user from either "I want to create a new keyboard for language X" or "I want to adapt the
keyboard Y for language Z" to a buildable, documented package without requiring the user to know the file format details.

## Supported Keyboard Formats

| Format | Touch Support | Status |
|--------|--------------|--------|
| KMN (.kmn) | Full (.keyman-touch-layout + .kvks) | Fully supported |
| LDML (.xml, CLDR 45 keyboard3) | Spec generated, Keyman support pending | Generated with warning |

## Key Capabilities

**Two Workflows:**

### From Scratch
- **Generate** — Scaffold a complete new keyboard project (KMN or LDML) with all required
  files pre-populated, including touch layout, visual keyboard, and documentation stubs.
  Start typing rules from zero.

### From Existing Keyboard
- **Copy/Adapt** — Clone any keyboard from a local path, Keyman Cloud, or GitHub, rename it,
  and use it as a starting point. Modify rules, layouts, and documentation.

**Common to Both**
- **Build** — Run `kmc build` and return structured, human-readable error/warning output.
- **Validate** — Check a keyboard project for internal consistency without building:
  touch/desktop key coverage, layer parity, documentation completeness, and KPS file integrity.
- **Inspect** — Read and summarize any keyboard project's structure and metadata.

## How It Knows the Keyman Language

KeymanMCP ships with a pre-built API index of the Keyman keyboard language — all virtual key
names, system stores, touch layout properties, compiler error codes, and common KMN code
patterns — extracted from the Keyman source repository and real keyboard examples. This index
is what allows an AI to generate correct `.kmn` and `.keyman-touch-layout` code rather than
hallucinating syntax.

The index is rebuilt by running `python src/refresh.py` whenever the Keyman repository is
updated.

## Technology

- Python 3, `mcp` package (stdio transport), `pydantic`, `lxml`
- `kmc` CLI called as a subprocess — Node.js is not required at runtime
- Same architecture as FlexToolsMCP (D:\Github\FlexToolsMCP)

## Quick Start

```bash
# 1. Install kmc (requires Node.js 20+)
npm install -g @keymanapp/kmc

# 2. Install Python dependencies
pip install -r requirements.txt

# 3. Build the API index (requires D:\Github\keyman and D:\Github\keyboards)
python src/refresh.py

# 4. Register with your MCP client using mcp_config.json
```
