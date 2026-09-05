<div align="right">
  <strong>English</strong> | <a href="README.md">中文</a>
</div>

# ScreenPin

A macOS screenshot & pin-to-screen tool: select any region → the image stays pinned on top → save anytime.

Press the hotkey to freeze the screen into a snapshot, then drag to select (transient UI like dropdown menus can also be captured). The pinned image can be dragged, annotated, opacity-adjusted, and set to click-through so it never blocks your workflow.

Interface language follows the system: Chinese on Chinese systems, English for everything else.

Pure Swift / AppKit / ScreenCaptureKit, zero third-party dependencies. Lives in the menu bar (no Dock icon).

## Build

```bash
./build.sh            # release build + assemble ScreenPin.app + self-signed cert
./build.sh --install  # same as above, plus install to /Applications (recommended)
open /Applications/ScreenPin.app
```

Build prefers an existing unlocked `ScreenPin Local Dev` certificate; falls back to ad-hoc signing if missing.
The certificate, private key, and local cert setup script are not committed. Using a stable development cert keeps screen recording permission valid across rebuilds.
Local development signing is not Developer ID signing or Apple notarization.

## First launch: grant Screen Recording permission

The first snip will trigger a system permission dialog (System Settings → Privacy & Security → Screen Recording → check ScreenPin).
If it still doesn't work after granting, restart the app once.

## Usage

| Action | How |
| --- | --- |
| Start snipping | Global hotkey `⌃⌥X`, or menu-bar ✂ icon → Snip. The screen is frozen first, then drag to select (dropdown menus are capturable too) |
| Selection | Mouse drag; `ESC` or single-click (selection < 4px) to cancel |
| Move pinned image | Drag from anywhere |
| Annotate | `B` brush, `A` arrow, `T` text (click to place input box, auto-wrap on overflow); `C` cycles colors; `Enter` confirms and exits layer-by-layer (preserving annotations): during text input = commit text, in annotate mode = exit annotation back to drag, normal pin = confirm and close; `ESC` cancels and discards: during text input = cancel this input, in annotate mode = exit annotation and discard this round's marks, normal pin = discard pin; also click the top-left "✕ Exit Annotation" button to keep annotations and exit annotate mode; `⌘Z` undoes last stroke; clipboard auto-updates to the latest annotated image after each stroke, so `⌘V` pastes directly; annotations are baked into the image on save/copy |
| Save to Desktop | Right-click → Save to Desktop, or `⌘S` (silently saves `~/Desktop/ScreenPin_<timestamp>.png`, border flashes on success); `⇧⌘S` saves to Desktop and copies the **file path text** to clipboard (handy for pasting to an agent) |
| Copy to clipboard | Right-click → Copy, or `⌘C` |
| Adjust opacity | Right-click → Opacity (100/75/50/25%) |
| Click-through | Right-click → Click-through (mouse events pass through to windows below; clear via menu-bar "Close All Pins") |
| Discard pin | `ESC` (discard) / `⌘W` / right-click → Close; or `Enter` to confirm and close (image is already in clipboard) |

## Change hotkey

Edit `keyCode` / `modifiers` constants in `Sources/ScreenPin/HotkeyManager.swift`, then re-run `./build.sh`.

## Debug logs

Runtime logs go to `~/Library/Logs/ScreenPin.log` (snip coordinates, target display, success/failure reasons).
Auto-rolls to `ScreenPin.old.log` at >512KB (only one generation kept). Delete either file when no longer needed.
Diagnostic probe: `/Applications/ScreenPin.app/Contents/MacOS/ScreenPin --check-permission`.

## Structure

```
Sources/ScreenPin/
├── ScreenPinApp.swift          # @main + menu bar
├── HotkeyManager.swift         # Carbon global hotkey
├── SnipOverlayController.swift # Full-screen snapshot selection overlay
├── CaptureService.swift        # ScreenCaptureKit snapshot + permission pre-check
├── PinWindowController.swift   # Topmost floating image window (brush/arrow/text annotations)
├── SaveService.swift           # Save to Desktop / clipboard
└── Localization.swift          # L() localization entry (key is English)
```
