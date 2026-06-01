Now I have the complete, accurate picture. Let me update my assessment:

---

OK, this changes the picture significantly. Let me revise with what I now know.

## Revised Assessment: What "Doing It Their Way" Actually Takes

### What's MORE done than I initially said

- **The WASM build compiles and links with Embind** — `-lembind` is in the Meson config, emscripten cross-files exist for all platforms, and it builds `libkeymancore.a` for WASM
- **Core LDML tests already run on WASM** — via Node.js as an exe_wrapper, with NODEFS for filesystem access
- **JS interop already works** — `EM_JS` macros bridge normalization (NFC/NFD) and regex to JavaScript's native implementations, replacing ICU on WASM
- **The output is `libkeymancore.a`** (static lib), but Embind is linked — so the infrastructure for exposing C++ classes to JS is ready

### What's actually missing

The gap is narrower than I said. **There are zero `EMSCRIPTEN_BINDINGS` blocks anywhere in Core.** Embind is linked, the library compiles to WASM, tests run — but nobody has written the JS-facing API bindings yet. And on the web side, there's zero reference to `wasm`, `core-processor`, or `km_core` in `web/src/engine/`.

Here's the concrete work, in the order the KM team would want to see it:

### Piece 1: Embind Bindings (~200 LOC C++)
New file `core/src/wasm/core_wasm_bindings.cpp`:

```cpp
#include <emscripten/bind.h>
// Wrap the key Core API functions:
// - km_core_keyboard_load_from_blob()
// - km_core_state_create() 
// - km_core_state_context_set_if_needed()
// - km_core_process_event()
// - km_core_state_get_actions()  → returns km_core_actions
// - km_core_state_dispose(), km_core_keyboard_dispose()
```

The `km_core_actions` struct (defined in `core/include/keyman/keyman_core_api.h`) is what comes back — it contains: `output` (string to insert), `code_points_to_delete` (backspaces), `new_caps_lock_state`, `emit_keystroke` (pass-through), etc. This is a clean, flat struct — trivial to bind.

### Piece 2: TypeScript WASM Wrapper (~400 LOC TS)
New package `web/src/engine/core-processor/`:

```
KeyEvent → (vk_code, modifier_state) 
  → wasm km_core_process_event()
  → km_core_actions {output, code_points_to_delete, ...}
  → RuleBehavior {transcription: {insert, deleteLeft}}
```

Key translation: `km_core_actions` → `RuleBehavior`. The mapping is nearly 1:1:
| `km_core_actions` field | `RuleBehavior` / `TextTransform` field |
|---|---|
| `output` (UTF-32 string) | `transform.insert` |
| `code_points_to_delete` | `transform.deleteLeft` |
| `emit_keystroke` | `triggerKeyDefault` |
| `do_alert` | `beep` |
| `new_caps_lock_state` | system store update |
| `persist_options` | `saveStore` |

### Piece 3: Touch Layout from KMXPlus (~250 LOC TS)
Convert `Layr` (touch) + `Keys` sections → `TouchLayoutPlatform` JSON.

The KMXPlus reader already exists in `common/web/types/src/kmx/kmx-plus/`. The `LayrList` where `hardware.value == 'touch'` has rows of key IDs. Each key ID maps to a `KeysKeys` entry with: `to` (display/output), `width`, `longPress`, `multiTap`, `flicks`, `switch` (layer change). These map directly to the `.keyman-touch-layout` JSON structure.

### Piece 4: Integration Plumbing (~50 LOC TS)
In `KeyboardProcessor.processKeystroke()` — dispatch to WASM Core for LDML keyboards, fall through to `KeyboardInterface` for KMN keyboards.

### Piece 5: Build/Package Wiring (~100 LOC config)
- Add `core_wasm_bindings.cpp` to `core/src/meson.build`
- Change output from `.a` to `.js` + `.wasm` (Emscripten linker flag change)
- Add the `core-processor` package to `web/src/engine/` with its `package.json`, `tsconfig.json`
- Wire it into the web build

## What the KM team will scrutinize

1. **Context synchronization** — the WASM Core maintains its own context buffer; it must stay in sync with the web engine's `OutputTarget`. The `km_core_state_context_set_if_needed()` API exists for exactly this, but getting it right at every focus change, selection change, and undo is subtle.

2. **Memory management** — WASM heap objects (`km_core_state`, `km_core_keyboard`) must be properly disposed. Leaks accumulate fast when switching keyboards.

3. **Test parity** — Core's LDML tests pass natively and in WASM (via Node). The web integration needs equivalent coverage showing that the same keyboards produce the same output through the web pipeline.

4. **No regressions for KMN** — the dispatch must cleanly separate LDML (→ WASM Core) from KMN (→ existing JS processor).

## Recommended PR Strategy

**PR 1** (smallest, highest signal): Embind bindings + change Meson to output `.js`/`.wasm`. Proves the C++ Core is callable from JS. Self-contained, easy to review.

**PR 2**: Touch layout extractor from KMXPlus. Pure TypeScript, no WASM dependency. Can be reviewed independently.

**PR 3**: TypeScript WASM wrapper + integration plumbing. This is the big one — ties it all together.

This keeps each PR focused and reviewable. Want me to start with PR 1 (the Embind bindings)?

Both background research agents have now completed, confirming everything in my assessment above. The picture is clear — Core WASM compiles, Embind is linked, but zero `EMSCRIPTEN_BINDINGS` blocks exist and zero web-side integration code exists.

Ready to start coding whenever you give the word. PR 1 (Embind bindings) would be the cleanest first move.

All three research agents are done. The LDML research confirms the Core C++ processor is **production-ready** for the features that matter — key lookup, transforms, reorders, markers, normalization. The only gaps are caps lock key-up handling and emoji grapheme backspace, neither of which blocks a pre-alpha.

So: ~1,050 lines across 4 files, 3 focused PRs. Say the word.