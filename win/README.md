# Windows 用 Kanata 設定

## ファイル

- `kanata-jis.kbd`: JIS キーボード + ChgKey (Scancode Map) で大西配列化済みの環境向け。
  macOS 版 `mac/kanata-us.kbd` の移植 (Onishi + Space HUB)。
- `kay-map-win.kbd`: 以前の簡易版 (大西 passthrough + F12 で QWERTY 切替)。参照用。

## 前提: ChgKey (Scancode Map) が有効であること

この設定は **ChgKey がレジストリの `Scancode Map` で大西配列化した後の
キーが kanata に届く** 前提で書かれている (kanata 側では大西変換をしない)。

この構造は実測で確認済み:

- 物理 W キー押下時、LL hook (winIOv2 バックエンド) が観測する
  `KBDLLHOOKSTRUCT` は `vk=0x4C(L) scan=0x26` = **Scancode Map 適用後**の値。
- 逆に `SendInput` で注入したスキャンコードは `Scancode Map` を
  通過しない (scan 0x11 は 0x11 のまま観測)。kanata の出力経路も
  同様にリマップされないため、deflayer のキー名がそのまま
  レイヤー変換 (ja-JP) を受けてアプリへ届く。

kanata が生のスキャンコードを読む Mac 版とは構造が逆になる:
Mac は defsrc = 物理配置 + kanata 自身が大西変換を担当。
Windows は defsrc = ChgKey 適用後のキー名 + 大西変換は ChgKey が担当。

`Scancode Map` が空 (ChgKey 未適用) の環境では大西配列にならないので注意。

## バックエンド

winIOv2 バイナリ (`kanata_windows_tty_winIOv2_x64.exe` / GUI 版も同系) で
使う想定。`deflocalkeys-winiov2` を使うため標準 LLHOOK バイナリ
(`kanata.exe`) ではファイル自体が読めない。

かなキー (scan 0x70) は kanata 標準の `kana` 名が KEY_HANGEUL に解決されて
一致しないため、`(deflocalkeys-winiov2 kana 241)` で VK_DBE_KATAKANA 側に
上書きしている。

## JIS 配列対応 (記号の出力変換)

kanata の出力は ja-JP レイヤーで変換されるため、レイヤーから emit する
キー名は「US レイアウトで意図した文字」ではなく
「JIS でその文字を出すスキャンコード」を使う。

| 意図 | emit | 意図 | emit |
| ---- | ---- | ---- | ---- |
| `(` | `S-8` | `)` | `S-9` |
| `[` | `]` | `]` | `\` |
| `{` | `S-]` | `}` | `S-\` |
| `@` | `[` | `^` | `=` |
| `~` | `S-=` | `` ` `` | `S-[` |
| `:` | `'` | `;` | `;` |
| `"` | `S-2` | `'` | `S-7` |
| `\` | `¥` | `\|` | `S-¥` |
| `_` | `S-ro` | `=` | `S--` |
| `+` | `S-;` | `*` | `S-'` |
| `<` | `S-,` | `>` | `S-.` |
| `?` | `S-/` | `-` | `-` |

注意: 名前 `yen` は 0x2B (US の `\` 位置) の別キー。`¥` (0x7D) を使う。

## レイヤー概要

- BASE: F1 = Onishi <-> QWERTY 切替 (ChgKey で F1⇄Caps 済み)、
  Caps 押し中 = extra (数字盤・矢印)、Space tap = Space / hold = HUB。
- HUB (Space 押し中): HJKL 矢印、Q 検索 (Ctrl+F)、YUIOP 行頭/語/文頭/語/行末、
  [ ] 前/次タブ (暫定)、M マウスモード、
  LShift tap = 半角/全角・hold = 左シンボル+右テンキー、
  RShift tap = かな・hold = 右テンキー+左シンボル、
  逆側 Shift タップ = Automation レイヤー (one-shot)。
- MOUSE: WASD/HJKL 移動、Shift 精密、Alt 高速、Space/Esc 戻る。

Mac 版からの主な差分:

- ファイル間のキーコマンド: `M-*` → `C-*` 系 (redo = C-y、app 切替 = A-tab 等)。
- スクリーンショット: `M-S-s` (領域) / `prnt` (全画面) / `A-prnt` (アクティブ窓)。
- IME 切替 tap: Mac の `eisu` → 半角/全角 (`grv`)、`kana` はそのまま (0x70)。
- Home/End/文頭文末: Mac の Cmd 系 → Windows の Ctrl/Home/End 系。

## 検証・起動

```powershell
# 構文チェック (v1.12.0 winIOv2)
kanata_windows_tty_winIOv2_x64.exe --check --cfg win\kanata-jis.kbd

# 起動
kanata_windows_tty_winIOv2_x64.exe --cfg win\kanata-jis.kbd

# 緊急終了: LCtrl + Space + Esc 同時押し
```

## 既知の未検証点

- レイヤー内の `C-S-tab` / `C-tab` (前/次タブ) は暫定。
- 実機での通電テスト (かな tap、テンキー、マウスモード) は
  `--check` までは確認済み。動作一式の確認はこれから。
