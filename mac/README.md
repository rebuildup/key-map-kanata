# macOS US ANSI

`kanata-us.kbd` is the macOS US ANSI configuration used with the global Onishi `hidutil` mapping from `rebuildup/pc-setup`.

The physical keyboard is US ANSI. Kanata sees raw physical usages, emits through the Karabiner VirtualHID keyboard, and the global `hidutil` mapping produces the final Onishi layout.

## Current layout

The base layer remains Onishi and `F1` still switches Onishi / QWERTY.

The custom layer is now centered on Space instead of Caps:

- `Space tap`: Space
- `Space hold`: HUB
- `Space + H/J/K/L`: Left / Down / Up / Right
- `Space + Q`: Command+F
- `Space + R`: Shift+Enter
- `Space + T`: Shift+Delete
- `Space + LShift tap`: Eisu
- `Space + RShift tap`: Kana
- `Space + LShift hold`: left-hand symbols + right-hand numpad
- `Space + RShift hold`: left-hand numpad + right-hand symbols
- while one side Shift layer is held, tap the opposite Shift: arm one-shot Automation
- `Space + M`: persistent Mouse mode

Mouse mode provides both WASD and HJKL movement so either hand can operate the pointer. Shift is precision speed, Command is turbo speed, and Space/Escape exits Mouse mode.

Automation includes F1-F12, Redo, clipboard screenshots, dynamic macro record/play, repeated mouse click, media/brightness/volume controls, Mouse mode, and Kanata live reload.

## Visual cheat sheet

Open `mac/layout.html` locally in a browser. The tabs show HUB, left/right Shift layers, Automation, and Mouse mode by physical US key position.

## Validation

This branch validates `mac/kanata-us.kbd` with the official macOS Kanata 1.12.0 binary in GitHub Actions.

For a local parser check:

```bash
kanata --check --cfg ./mac/kanata-us.kbd
```

For a manual runtime smoke test, keep the pc-setup `hidutil` Onishi mapping enabled and run:

```bash
sudo kanata --no-wait --cfg ./mac/kanata-us.kbd
```

Check at minimum:

1. Base typing is Onishi and is not double-remapped.
2. F1 switches to QWERTY and back.
3. Space tap remains a normal space.
4. Space + H/J/K/L produces Vim-style arrows.
5. Space + LShift tap selects Eisu; Space + RShift tap selects Kana.
6. Both mirrored numpads and symbol layers match `layout.html`.
7. Mouse mode enters with Space + M and exits with Space or Escape.
8. `Left Ctrl + Space + Escape` still emergency-exits Kanata.

## pc-setup integration

`rebuildup/pc-setup` owns DriverKit installation, the system LaunchDaemon, permissions, and the pinned config revision. Its macOS Kanata installer downloads this file from an exact commit SHA rather than following a mutable branch.
