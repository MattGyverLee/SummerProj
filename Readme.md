# Keyman Summer Project — Planning Repo

This is a **planning and design workspace** for a set of related summer initiatives
aimed at lowering the barrier to contributing high-quality Keyman keyboards.

It does not ship code itself. Each sub-folder is either a design document, a
specification, a reference index, or a seed for a project that will eventually
live in its own repository.

## The vision

Three audiences, one outcome — more correct keyboards, less manual coordination.

1. **Keyboard authors** — Help people build internally-consistent keyboards
   with LLM assistance. A Keyman MCP gives the model authoritative knowledge of
   the keyboard language (virtual keys, system stores, touch-layout syntax,
   compiler messages, common patterns) so it can scaffold, adapt, and validate
   keyboards instead of hallucinating syntax.

2. **Reviewers** — Help the small group of reviewers triage the steady stream
   of incoming keyboard PRs by codifying the checklist they apply today, and
   making as much of it as possible machine-checkable so a reviewer's time is
   spent only on the judgement calls.

3. **Both** — Hide GitHub from people who shouldn't have to learn it.
   Submission, versioning, package consistency, file layout, license format,
   `HISTORY.md` discipline — none of these are why someone wants to build a
   keyboard for their language. The combined toolchain should let an author
   describe what they want in natural language and produce a buildable,
   submittable package.

## Sub-projects

| Folder | What it is | Status |
|---|---|---|
| [KeymanMCP/](KeymanMCP/) | Python MCP server design — wraps the `kmc` CLI, ships a pre-built API index of the Keyman language, and exposes `generate_keyboard`, `build_keyboard`, `validate_consistency`, `copy_keyboard`, `inspect_keyboard` tools to AI assistants. | Design + plan complete; implementation pending. |
| [KMReview/](KMReview/) | Review playbook for incoming keyboard PRs. Captures the tacit knowledge of current reviewers as a structured pass/fail checklist with each item color-coded by whether it's deterministic (green), LLM-judgeable (yellow), or needs out-of-band information (red). | Criteria document drafted; needs reviewer sign-off. |
| [KeymanEcosystemMap/](KeymanEcosystemMap/) | One-page summaries of the repos in the Keyman ecosystem (`keyman`, `keyboards`, `lexical-models`, `keyman-browser`, `help.keyman.com`, `api.keyman.com`, `developer.keyman.com`, `keyman-vancouver`) plus an HTML overview diagram. Reference material for onboarding new contributors. | Reference, kept current as the ecosystem changes. |

More sub-projects may be added here as the design work expands. Anything that
graduates to active implementation should move into its own repository under
`keymanapp/` (or a personal fork) — this repo is the planning surface, not a
monorepo.

## Key documents to read first

- **Vision and team plan** — [KeymanMCP/PROJECT_PROPOSAL.md](KeymanMCP/PROJECT_PROPOSAL.md)
  (4-week intern project, team roles, timeline, success criteria)
- **What KeymanMCP actually is** — [KeymanMCP/EXECUTIVE_SUMMARY.md](KeymanMCP/EXECUTIVE_SUMMARY.md)
- **MCP tool design** — [KeymanMCP/PLAN.md](KeymanMCP/PLAN.md)
- **API index design** — [KeymanMCP/API_INDEX.md](KeymanMCP/API_INDEX.md)
- **Reviewer checklist** — [KMReview/criteria.md](KMReview/criteria.md)
- **Reviewer background notes** — [KMReview/Checking Keyman Keyboard Pull Requests.md](KMReview/Checking%20Keyman%20Keyboard%20Pull%20Requests.md)
- **Where everything lives in the Keyman world** — [KeymanEcosystemMap/keyman-ecosystem-map.html](KeymanEcosystemMap/keyman-ecosystem-map.html)

## How the pieces fit together

```
   Author (with LLM)                                  Reviewer
        |                                                |
        v                                                v
   +---------------+      submits PR to       +-------------------+
   |  KeymanMCP    |  ----------------------> |  KMReview         |
   |  (authoring)  |                          |  (triage)         |
   +---------------+                          +-------------------+
        |                                                |
        | generates                                      | applies
        | consistent files                               | green checks
        | (.kmn .kps .kvks                               | mechanically,
        |  .keyman-touch-layout                          | yellow with
        |  README HISTORY LICENSE                        | LLM assistance,
        |  welcome.htm)                                  | red by hand
        |                                                |
        +-----> keymanapp/keyboards repo <---------------+
                (see KeymanEcosystemMap)
```

The same `criteria.md` that reviewers consult is also the test oracle for
`validate_consistency` in KeymanMCP — every green check the MCP can run before
submission is one less round-trip a reviewer has to handle.

## Conventions for this repo

- Markdown documents only — no compiled artifacts here.
- Each sub-project is self-contained inside its folder, including its own
  `.claude/` settings if it has them.
- When a sub-project is ready to be implemented, open a real repo for it and
  leave the design docs here as the historical record.
