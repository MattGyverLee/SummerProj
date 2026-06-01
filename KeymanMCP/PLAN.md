# KeymanMCP — Full Implementation Plan

## Context

KeymanMCP helps AI assistants create and adapt Keyman keyboards. It wraps the `kmc` CLI
compiler with MCP tools, adds consistency validation across all keyboard file types, and
uses a pre-built API index of the Keyman language spec to generate correct code.

Architecture follows the same pattern as D:\Github\FlexToolsMCP.

## Project Structure

```
D:\Github\KeymanMCP\
  src/
    server.py                    # MCP server — all tool definitions and dispatch
    parsers/
      __init__.py
      kmn.py                     # Parse .kmn: header stores, groups, rule keys
      ldml.py                    # Parse LDML .xml: layers, keys, flicks, transforms
      touch.py                   # Parse .keyman-touch-layout JSON
      kvks.py                    # Parse .kvks XML: visual keyboard layers/keys
      kps.py                     # Parse .kps XML: file list, metadata, BCP-47 tags
    # Index builder scripts (see API_INDEX.md)
    extract_vkeys.py
    extract_system_stores.py
    extract_compiler_messages.py
    extract_touch_spec.py
    extract_kmn_patterns.py
    build_language_index.py
    refresh.py                   # Orchestrates all extractors
  index/
    keyman_api/                  # Built by refresh.py; committed to repo
      vkeys_v19.0.json
      system_stores_v19.0.json
      compiler_messages_v19.0.json
      touch_layout_spec_v19.0.json
      kmn_patterns_v19.0.json
      language_index_v19.0.json  # Unified index loaded by server
  mcp_config.json
  requirements.txt
  .env.example
  EXECUTIVE_SUMMARY.md
  PLAN.md
  API_INDEX.md
```

## MCP Tools

### 1. `generate_keyboard`

Scaffold a complete new keyboard project from scratch.

**Inputs**
| Parameter | Type | Description |
|-----------|------|-------------|
| `keyboard_id` | str | Snake-case identifier (e.g. `my_keyboard`) |
| `name` | str | Display name |
| `language_bcp47` | str | BCP-47 language tag (e.g. `en`, `km`) |
| `type` | `kmn` or `ldml` | Keyboard format |
| `output_path` | str | Directory to create the project in |
| `targets` | list[str] | Platforms: `windows`, `mac`, `linux`, `web`, `phone`, `tablet` |

**Process**
1. Run `kmc generate keyman-keyboard <id>` (KMN) or `kmc generate ldml-keyboard <id>` (LDML)
2. Populate documentation stubs: `README.md`, `HISTORY.md`, `LICENSE.md`,
   `source/readme.htm`, `source/welcome.htm`
3. For KMN: add default `.keyman-touch-layout` (phone + tablet with default/shift/numeric
   layers) and `.kvks` skeleton using key names from the vkeys index
4. For LDML + touch targets: generate CLDR 45-compliant flick/layer touch spec; append
   `[WARN] LDML touch keyboard support is not yet fully implemented in Keyman...`
5. Return list of files created + any kmc output

**Key source files used**
- `index/keyman_api/language_index_v*.json` — valid store names, key names
- `index/keyman_api/touch_layout_spec_v*.json` — valid sp values, layer IDs, special labels

---

### 2. `build_keyboard`

Compile a keyboard project using `kmc build`.

**Inputs**
| Parameter | Type | Description |
|-----------|------|-------------|
| `path` | str | `.kpj`, `.kmn`, `.xml`, or `.kps` |
| `debug` | bool | Add `--debug` flag |
| `for_publishing` | bool | Add `--for-publishing` flag (validates for Keyman Cloud) |

**Process**
1. Run `kmc build <path> --log-format tsv [flags]`
2. Parse TSV output: columns are `severity | file | line | col | code | message`
3. Enrich each error code with human-readable description from `compiler_messages` index
4. Return structured result: `{ success, error_count, warning_count, messages: [...] }`

---

### 3. `validate_consistency`

Analyze a keyboard project for internal consistency without compiling.

**Inputs**: `project_path` (str)

**Checks**

| Check | Method |
|-------|--------|
| **Touch/desktop key coverage** | Compare key IDs across `.keyman-touch-layout` (all layers, phone + tablet) and `.kvks` (all shift states). Flag keys in one but not the other. |
| **Layer parity** | Verify layer names match across `.kvks`, `.keyman-touch-layout`, and KMN stores/groups (or LDML `<layers>`). |
| **Documentation completeness** | Check existence + non-emptiness of `README.md`, `HISTORY.md`, `LICENSE.md`, `source/readme.htm`, `source/welcome.htm`; flag absence of optional `source/welcome/` screenshots. |
| **KPS package contents** | Parse `.kps` XML; verify every `<File>` entry exists on disk; verify required metadata fields (name, copyright, version, BCP-47 tag). |

**Returns** structured report: `{ pass: [...], warn: [...], fail: [...] }` with per-check details.

---

### 4. `copy_keyboard`

Clone and rename an existing keyboard, updating all internal references to allow safe adaptation.

**Inputs**
| Parameter | Type | Description |
|-----------|------|-------------|
| `source` | str | Local path, `cloud:<id>`, or GitHub URL |
| `output_path` | str | Destination directory for the adapted keyboard |
| `new_id` | str | New keyboard ID (renames files + internal refs) |
| `new_name` | str | New display name (optional; defaults to new_id) |
| `dry_run` | bool | Preview only |

**Process**
1. Run `kmc copy <source> -o <output_path> [--dry-run]`
2. If `new_id` differs from source ID:
   - Rename all files to new keyboard ID (e.g., `khmer_angkor.kmn` → `my_keyboard.kmn`)
   - Update internal references:
     - Keyboard ID in `.kmn`/`.xml` header
     - Filenames in `.kps` and `.kpj`
     - `store(&NAME)` if `new_name` provided
     - `&KEYBOARDVERSION` reset to '1.0'
     - `&BITMAP` and `&VISUALKEYBOARD` file references updated
3. Preserve all layer names, touch layout structure, and rule logic for modification
4. Return list of files created/renamed + warnings if any internal references may have been missed

---

### 5. `inspect_keyboard`

Read and summarize an existing keyboard project.

**Inputs**: `project_path` (str)

**Returns**
- Keyboard ID, name, version, language BCP-47 tags, copyright, targets
- File inventory: which expected files are present / missing
- Touch layout summary: layers, key count per layer, phone vs tablet
- Desktop layout summary: layers, key count
- KMN/LDML summary: rule/group count or LDML transform count
- Any detected issues (missing files, empty metadata, mismatched IDs)

---

## Server Startup

```python
# Check kmc on PATH
result = subprocess.run(["kmc", "--version"], capture_output=True, text=True)
if result.returncode != 0:
    print("[ERROR] kmc not found on PATH. Install: npm install -g @keymanapp/kmc",
          file=sys.stderr)
    sys.exit(1)

# Parse version (e.g. "19.0.352" -> "19.0")
kmc_version = result.stdout.strip().split('.')[0:2]  # "19.0"

# Load language index
index = KeymanAPIIndex.load(kmc_version)
print(f"[OK] Keyman API index loaded (v{kmc_version})", file=sys.stderr)
```

## Session State

```python
@dataclass
class SessionState:
    kmc_version: str = ""
    last_project_path: str = ""
```

## mcp_config.json

```json
{
  "mcpServers": {
    "keyman": {
      "command": "python",
      "args": ["src/server.py"],
      "cwd": "D:\\Github\\KeymanMCP"
    }
  }
}
```

## requirements.txt

```
mcp>=1.0.0
pydantic>=2.0.0
lxml>=5.0.0
```

## .env.example

```
# Path to the Keyman source repository (used by refresh.py only)
# KEYMAN_REPO_PATH=D:\Github\keyman

# Path to the keyboards repository (used by refresh.py --patterns only)
# KEYBOARDS_REPO_PATH=D:\Github\keyboards
```

## Verification

1. `kmc generate` + `generate_keyboard` — scaffold a KMN keyboard; confirm all files created
2. `build_keyboard` on a new scaffold — should return success with no errors
3. `validate_consistency` on `D:\Github\keyboards\release\k\khmer_angkor` — should pass all checks
4. `validate_consistency` on a keyboard with a file removed — should report failure
5. `generate_keyboard` with `type=ldml` and touch target — confirm LDML touch warning in output
6. `copy_keyboard` with `new_id` — confirm all filenames and internal references updated
7. `inspect_keyboard` on `khmer_angkor` — confirm accurate layer/key counts returned
