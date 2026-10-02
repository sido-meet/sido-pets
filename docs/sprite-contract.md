# Sprite contract

Snapshot of the hatch-pet sprite contract pinned in the README. The canonical, always-up-to-date spec lives in the OpenAI skills repo:

https://github.com/openai/skills/tree/b0401f07213a66414d84a65cb50c1d226f99485a/skills/.curated/hatch-pet

If you change anything here, you must update the pinned commit SHA in `README.md` so they stay in sync.

> **Heads-up — pinned SHA is behind the assets.** The SHA above still documents the older 9-row / `1536 × 1872` atlas, but every sprite sheet in `pets/` is 11 rows / `1536 × 2288`. The numbers below are measured from the checked-in sheets, not read off upstream. Re-verify the SHA and the row-9/10 state names against the live spec before relying on them.

## Atlas

- **Format**: PNG or WebP (WebP preferred — smaller, supports transparency).
- **Dimensions**: `1536 × 2288`.
- **Grid**: 8 columns × 11 rows.
- **Cell**: `192 × 208`.
- **Background**: transparent.
- **Unused cells**: fully transparent.
- **No** labels, gutters, borders, grid lines, drop shadows outside the cell, or extra frames — the webview uses CSS background positions against the fixed row/column counts.

## Row → state mapping (11 rows)

| Row | State          | Frames | Purpose                                 |
| --- | -------------- | ------ | --------------------------------------- |
| 0   | `idle`         | 7      | Resting, breathing, blinking            |
| 1   | `running-right`| 8      | Rightward drag movement                 |
| 2   | `running-left` | 8      | Leftward drag movement                  |
| 3   | `waving`       | 4      | Greeting or attention gesture           |
| 4   | `jumping`      | 5      | Hover or playful jump                   |
| 5   | `failed`       | 8      | Blocked, failed, or cancelled reaction  |
| 6   | `waiting`      | 6      | Waiting for approval or user input      |
| 7   | `running`      | 6      | Active task work / processing           |
| 8   | `review`       | 6      | Ready / awaiting user review            |
| 9   | *(unnamed)*    | 8      | Extra state, added after the 9-row spec |
| 10  | *(unnamed)*    | 8      | Extra state, added after the 9-row spec |

These states mirror the agent lifecycle. Add or rename rows only when the upstream contract allows it.

Frame counts are what the checked-in sheets actually contain — the pinned 9-row spec claims 6 for `idle`, but every sheet here ships 7. Rows 9 and 10 exist in the current assets but postdate the pinned SHA; fill in their state names once upstream confirms them.

## Manifest (`pet.json`)

```json
{
  "id": "meimei",
  "displayName": "Meimei 莓莓",
  "description": "圆润的3D草莓小精灵，戴着草莓帽，陪你专注与探索。",
  "spriteVersionNumber": 2,
  "spritesheetPath": "spritesheet.webp"
}
```

Fields:

- `id` — folder name, lowercase kebab-case. Must match `<repo>/pets/<id>/`.
- `displayName` — human label.
- `description` — one short sentence.
- `spriteVersionNumber` — atlas revision the sheet was generated against. Bump when re-exporting a sheet at a new grid size; both pets here are `2`.
- `spritesheetPath` — relative to the pet folder (always `spritesheet.webp` unless you re-encode).

## On-disk layout

```
${CODEX_HOME:-$HOME/.codex}/pets/<id>/
├── pet.json
└── spritesheet.webp
```

Drop the folder in, restart the Codex pet UI, and the new mascot is selectable.
