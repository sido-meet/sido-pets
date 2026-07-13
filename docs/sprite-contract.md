# Sprite contract

Snapshot of the hatch-pet sprite contract pinned in the README. The canonical, always-up-to-date spec lives in the OpenAI skills repo:

https://github.com/openai/skills/tree/b0401f07213a66414d84a65cb50c1d226f99485a/skills/.curated/hatch-pet

If you change anything here, you must update the pinned commit SHA in `README.md` so they stay in sync.

## Atlas

- **Format**: PNG or WebP (WebP preferred — smaller, supports transparency).
- **Dimensions**: `1536 × 1872`.
- **Grid**: 8 columns × 9 rows.
- **Cell**: `192 × 208`.
- **Background**: transparent.
- **Unused cells**: fully transparent.
- **No** labels, gutters, borders, grid lines, drop shadows outside the cell, or extra frames — the webview uses CSS background positions against the fixed row/column counts.

## Row → state mapping (9 rows)

| Row | State          | Frames | Purpose                                 |
| --- | -------------- | ------ | --------------------------------------- |
| 0   | `idle`         | 6      | Resting, breathing, blinking            |
| 1   | `running-right`| 8      | Rightward drag movement                 |
| 2   | `running-left` | 8      | Leftward drag movement                  |
| 3   | `waving`       | 4      | Greeting or attention gesture           |
| 4   | `jumping`      | 5      | Hover or playful jump                   |
| 5   | `failed`       | 8      | Blocked, failed, or cancelled reaction  |
| 6   | `waiting`      | 6      | Waiting for approval or user input      |
| 7   | `running`      | 6      | Active task work / processing           |
| 8   | `review`       | 6      | Ready / awaiting user review            |

These states mirror the agent lifecycle. Add or rename rows only when the upstream contract allows it.

## Manifest (`pet.json`)

```json
{
  "id": "brick-lion",
  "displayName": "Brick Lion",
  "description": "A tiny purple-hatted block-brick lion companion based on the user three-view reference.",
  "spritesheetPath": "spritesheet.webp"
}
```

Fields:

- `id` — folder name, lowercase kebab-case. Must match `<repo>/pets/<id>/`.
- `displayName` — human label.
- `description` — one short sentence.
- `spritesheetPath` — relative to the pet folder (always `spritesheet.webp` unless you re-encode).

## On-disk layout

```
${CODEX_HOME:-$HOME/.codex}/pets/<id>/
├── pet.json
└── spritesheet.webp
```

Drop the folder in, restart the Codex pet UI, and the new mascot is selectable.
