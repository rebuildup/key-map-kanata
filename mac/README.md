# macOS US ANSI

`kanata-us.kbd` is the macOS port of the current Windows custom configuration.

## Assumptions

The Mac uses the `pc-setup` hidutil Onishi mapping as the low-level 1:1 layout.

Kanata on macOS grabs the physical keyboard through the Karabiner DriverKit path, so this config describes the raw US ANSI physical keys. Kanata emits through the Karabiner virtual keyboard and the global hidutil mapping is expected to produce the final Onishi character mapping.

This ordering is intentionally verified on the real Mac before this configuration is treated as settled. If the virtual keyboard is not affected by the global hidutil mapping on the current macOS build, the base/output strategy must be changed rather than silently duplicating the layout.

## Preserved behaviour

- startup/base layer: Onishi
- F1: toggle Onishi / QWERTY
- hold Caps: extra layer
- extra navigation/numeric layout is kept by physical key position
- Windows Print Screen is replaced with macOS `Command+Shift+3`

The JIS-only `ro`, `mhnk`, `hnk`, and `kana` keys do not exist on the US keyboard. In the extra layer, the three useful thumb actions are moved by position:

```text
left Command -> Escape
Space        -> Enter
right Command -> Backspace
```

## Smoke test

Keep the pc-setup hidutil Onishi mapping enabled, then run Kanata manually before installing a daemon:

```bash
sudo kanata --no-wait --cfg ./mac/kanata-us.kbd
```

Check these in order:

1. Base typing is still Onishi and is not double-remapped.
2. F1 switches to QWERTY; typing `qwerty` physical positions produces QWERTY.
3. F1 switches back to Onishi.
4. Holding Caps activates the extra layer.
5. In the extra layer, left Command / Space / right Command produce Escape / Enter / Backspace.
6. `Left Ctrl + Space + Escape` exits Kanata.

Do not install the persistent LaunchDaemon until this smoke test passes.
