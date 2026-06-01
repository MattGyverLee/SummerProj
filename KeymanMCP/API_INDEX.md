# API Index — Keyman Language Specification

## Overview

KeymanMCP's generate and validate tools rely on a pre-built index of the Keyman keyboard
language specification. This index is analogous to the LibLCM and FlexLibs indexes in
FlexToolsMCP: it lets the MCP server answer questions like "what are the valid values for
&TARGETS?" or "what are all the K_ virtual key names?" without reading source files at
runtime.

The index is built by running `python src/refresh.py` and stored in `index/keyman_api/`.
All index files are committed to the repository so that users do not need the Keyman source
repo at runtime.

---

## Index Files

| File | Size (approx) | Content |
|------|--------------|---------|
| `vkeys_v19.0.json` | ~15 KB | All K_ virtual key names + touch-only keys |
| `system_stores_v19.0.json` | ~20 KB | All &STORE names, valid values, version requirements |
| `compiler_messages_v19.0.json` | ~80 KB | All error/warning codes with severity and descriptions |
| `touch_layout_spec_v19.0.json` | ~30 KB | Key properties, sp values, special labels, flick directions |
| `kmn_patterns_v19.0.json` | ~40 KB | Code patterns from real keyboards, tagged by complexity |
| `language_index_v19.0.json` | ~180 KB | Unified merged index (what server.py loads) |

Version suffix matches the Keyman major.minor version detected from `kmc --version`.

---

## Extractor Scripts

### `src/extract_vkeys.py`

**Source**: `D:\Github\keyman\developer\src\kmc-kmn\src\kmw-compiler\keymanweb-key-codes.ts`

Parses the TypeScript file using regex to extract:
- `VKeyNames` array: all 256 standard `K_` key names (K_A..K_Z, K_0..K_9, K_F1..K_F24,
  K_BKSP, K_ENTER, K_SHIFT, K_SPACE, K_LBRKT, K_RBRKT, K_COLON, etc.)
- `TKeymanWebTouchStandardKey` enum: touch-only keys (K_LOPT=50001, K_ROPT=50002,
  K_NUMERALS=50003, K_SYMBOLS=50004, K_CURRENCIES=50005, K_UPPER=50006, K_LOWER=50007, etc.)
- Naming conventions: `T_xxxx` (user touch keys), `U_####` (Unicode shortcut keys)

Output schema:
```json
{
  "_source": { "file": "keymanweb-key-codes.ts", "version": "19.0" },
  "standard_keys": { "K_A": { "code": 65, "description": "Letter A" }, ... },
  "touch_only_keys": { "K_LOPT": { "code": 50001, "description": "Left option key" }, ... },
  "naming_patterns": {
    "T_xxxx": "User-defined touch key (any alphanumeric/underscore suffix)",
    "U_####": "Unicode shortcut (hex codepoint, e.g. U_0041 for 'A')",
    "U_####_####": "Unicode shortcut for multi-codepoint sequences"
  }
}
```

---

### `src/extract_system_stores.py`

**Sources**:
- `D:\Github\keyman\common\include\kmx_file.h` — TSS_ numeric IDs
- `D:\Github\keyman\developer\src\kmcmplib\src\Compiler.cpp` — &STORE string names, TARGETS values

Parses `kmx_file.h` for the TSS_ enum and `Compiler.cpp` for the string name array and
validation logic to produce a table of every system store with:
- `&NAME` string used in .kmn files
- Numeric TSS_ ID
- Valid values (enumerated where known, e.g. TARGETS list)
- Minimum VERSION requirement
- Brief description

Output schema:
```json
{
  "_source": { "version": "19.0" },
  "stores": {
    "&VERSION": {
      "tss_id": 8,
      "description": "Keyboard language spec version",
      "valid_values": ["9.0", "10.0", "14.0", "15.0", "16.0", "19.0"],
      "required": true,
      "min_version": null
    },
    "&TARGETS": {
      "tss_id": 38,
      "description": "Compilation target platforms",
      "valid_values": ["any", "windows", "macosx", "linux", "desktop",
                       "web", "iphone", "ipad", "androidphone", "androidtablet",
                       "mobile", "tablet"],
      "required": false,
      "min_version": "9.0"
    },
    ...
  }
}
```

---

### `src/extract_compiler_messages.py`

**Sources**:
- `D:\Github\keyman\developer\src\kmc-kmn\src\compiler\kmn-compiler-messages.ts`
- `D:\Github\keyman\developer\docs\help\reference\messages\` (332 individual .md files)

Parses the TypeScript message definitions using regex to extract error code, severity
(`SevFatal`, `SevError`, `SevWarn`, `SevHint`, `SevInfo`), identifier name, and message
text. Cross-references with the .md doc files for extended explanations.

Output schema:
```json
{
  "_source": { "version": "19.0" },
  "messages": {
    "KM02001": {
      "severity": "error",
      "identifier": "ERROR_InvalidToken",
      "message": "Invalid token '{0}'",
      "description": "An unrecognized keyword was found in the keyboard source...",
      "module": "kmc-kmn"
    },
    ...
  }
}
```

Server uses this to enrich `kmc build --log-format tsv` output with the `description` field.

---

### `src/extract_touch_spec.py`

**Sources**:
- `D:\Github\keyman\common\schemas\keyman-touch-layout\keyman-touch-layout.clean.spec.json`
- `D:\Github\keyman\common\schemas\keyman-touch-layout\README.md`
- `D:\Github\keyman\developer\docs\help\reference\file-types\keyman-touch-layout.md`

Reads the JSON schema for machine-parseable constraints, then supplements with prose from
the README and file-types doc for descriptions and special label tables.

Output schema:
```json
{
  "_source": { "version": "19.0" },
  "platforms": ["phone", "tablet"],
  "key_properties": {
    "id": { "type": "string", "pattern": "^[TKUtku]_[a-zA-Z0-9_]+$", "required": true },
    "text": { "type": "string", "required": false },
    "sp": { "type": "integer", "valid_values": [0,1,2,8,9,10],
            "descriptions": { "0": "normal", "1": "modifier/frame", "2": "active modifier",
                              "8": "deadkey", "9": "blank", "10": "spacer" } },
    "width": { "type": "integer", "default": 100, "unit": "percent of standard key" },
    "sk": { "type": "array", "description": "Longpress subkey array" },
    "flick": { "type": "object", "valid_directions": ["n","s","e","w","ne","nw","se","sw"] },
    "multitap": { "type": "array" },
    "nextlayer": { "type": "string", "description": "Layer to switch to after key press" },
    "layer": { "type": "string", "description": "Layer to activate (modifier key)" }
  },
  "special_text_labels": {
    "*BkSp*": "Backspace", "*Enter*": "Enter", "*Shift*": "Shift",
    "*Tab*": "Tab", "*Menu*": "Context menu", "*Hide*": "Hide keyboard",
    "*ABC*": "Switch to alphabetic layer", "*123*": "Switch to numeric layer",
    "*Symbol*": "Switch to symbol layer", "*Currency*": "Switch to currency layer",
    "*ZWNJ*": "Zero-width non-joiner", "*ZWJ*": "Zero-width joiner",
    "*Sp*": "Regular space", "*NBSp*": "Non-breaking space",
    ...
  },
  "reserved_layer_ids": ["default", "shift", "numeric", "symbol", "currency",
                         "phone-numeric", "tablet-numeric"]
}
```

---

### `src/extract_kmn_patterns.py`

**Source**: Selected `.kmn` files from `D:\Github\keyboards\release\`

Scans a curated set of keyboards covering four complexity levels:
- Level 1 (minimal): `nabataean_inscript`, `anni` — direct key mappings
- Level 2 (store-based): `sil_ipa`, `galaxie_greek_mnemonic` — any/index/deadkey
- Level 3 (context-aware): `khmer_angkor` — context(), platform(), layer switching
- Level 4 (advanced): `sil_korean_morse` — multi-group, call(), match/nomatch

For each keyboard, extracts the full header block and representative rule examples.
Tags each pattern with its required minimum VERSION and which features it demonstrates.

Output schema:
```json
{
  "_source": { "content_hash": "abc123", "keyboard_count": 8 },
  "patterns": {
    "minimal_header": {
      "complexity": 1,
      "description": "Minimum required header stores",
      "example": "store(&VERSION) '10.0'\nstore(&NAME) \"My Keyboard\"\n...",
      "features": ["VERSION", "NAME", "TARGETS", "LAYOUTFILE"]
    },
    "parallel_store_mapping": {
      "complexity": 2,
      "description": "Map groups of keys to Unicode using any() + index()",
      "example": "store(keys) [K_A] [K_B] ...\nstore(out) U+0041 U+0042 ...\n+ any(keys) > index(out,1)",
      "features": ["any", "index", "store"]
    },
    "layer_switching": {
      "complexity": 3,
      "description": "Auto-return to default layer after single character",
      "example": "group(PostKeystroke) readonly\n    if(&newLayer = \"\") if(&layer = 'shift') any(ShiftOutSingle) > context layer('default')",
      "features": ["begin PostKeystroke", "if", "layer", "context"]
    },
    ...
  }
}
```

---

### `src/build_language_index.py`

Loads all five extract outputs and merges them into a single `language_index_v*.json` that
`server.py` loads at startup. The merge adds cross-references, e.g. linking each system store
to the compiler error codes that reference it.

---

## refresh.py Orchestrator

Analogous to `D:\Github\FlexToolsMCP\src\refresh.py`.

```
python src/refresh.py                       # Rebuild all index files
python src/refresh.py --vkeys-only
python src/refresh.py --stores-only
python src/refresh.py --messages-only
python src/refresh.py --touch-only
python src/refresh.py --patterns-only
python src/refresh.py --skip-patterns       # Fast rebuild (skip slow keyboard scan)
python src/refresh.py --keyman-path PATH    # Override D:\Github\keyman
python src/refresh.py --keyboards-path PATH # Override D:\Github\keyboards
```

### Version Detection

```python
result = subprocess.run(["kmc", "--version"], capture_output=True, text=True)
# "19.0.352" -> major_minor = "19.0"
major_minor = ".".join(result.stdout.strip().split(".")[:2])
```

Output files are named `*_v{major_minor}.json`. Server startup loads the file matching
the detected kmc version, falling back to the latest available file if no exact match.

### When to Re-run

- After pulling updates to `D:\Github\keyman` that change a new Keyman major.minor version
- After adding keyboards to `D:\Github\keyboards` whose patterns you want indexed
- Never required for normal keyboard development — shipped index files cover current Keyman

### Dependencies

Only Python stdlib (`re`, `json`, `xml.etree.ElementTree`, `pathlib`, `subprocess`) plus
the Keyman repo files. No Node.js, no pip packages beyond what server.py already requires.
