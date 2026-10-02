# Sprite contract

Snapshot of the hatch-pet sprite contract pinned in the README. The canonical, always-up-to-date spec lives in the OpenAI skills repo:

https://github.com/openai/skills/tree/b0401f07213a66414d84a65cb50c1d226f99485a/skills/.curated/hatch-pet

The two reference files this document is derived from — `references/codex-pet-contract.md` and `references/animation-rows.md` — are **byte-identical** at the pinned SHA and at upstream `main` (`49f948f`), so the pin is current as of 2026-10-02. If you change anything here, you must update the pinned commit SHA in `README.md` so they stay in sync.

## Atlas

- **Format**: PNG or WebP.
- **Dimensions**: `1536 × 1872`.
- **Grid**: 8 columns × 9 rows.
- **Cell**: `192 × 208`.
- **Background**: transparent.
- **Unused cells**: fully transparent.
- **No** labels, gutters, borders, grid lines, drop shadows outside the cell, or extra frames — the webview uses CSS background positions against the fixed row and column counts.

## Row → state mapping (9 rows)

| Row | State           | Used cols | Durations                                  | Purpose                                 |
| --- | --------------- | --------- | ------------------------------------------ | --------------------------------------- |
| 0   | `idle`          | 0–5       | 280, 110, 110, 140, 140, 320 ms            | Resting, breathing, blinking            |
| 1   | `running-right` | 0–7       | 120 ms each, final 220 ms                  | Rightward drag movement                 |
| 2   | `running-left`  | 0–7       | 120 ms each, final 220 ms                  | Leftward drag movement                  |
| 3   | `waving`        | 0–3       | 140 ms each, final 280 ms                  | Greeting or attention gesture           |
| 4   | `jumping`       | 0–4       | 140 ms each, final 280 ms                  | Anticipation, lift, peak, descent, settle |
| 5   | `failed`        | 0–7       | 140 ms each, final 240 ms                  | Blocked, failed, or cancelled reaction  |
| 6   | `waiting`       | 0–5       | 150 ms each, final 260 ms                  | Waiting for approval or user input      |
| 7   | `running`       | 0–5       | 120 ms each, final 220 ms                  | Active task work / processing           |
| 8   | `review`        | 0–5       | 150 ms each, final 280 ms                  | Ready / awaiting user review            |

These states mirror the agent lifecycle. `running` is *task work*, not locomotion — avoid jogging, sprinting, raised knees, or directional travel. `running-left` must be redrawn or mirrored with frame order and timing preserved, not copied from the right-facing frames unless the design is symmetric.

## Manifest (`pet.json`)

```json
{
  "id": "meimei",
  "displayName": "Meimei 莓莓",
  "description": "圆润的3D草莓小精灵，戴着草莓帽，陪你专注与探索。",
  "spritesheetPath": "spritesheet.webp"
}
```

Fields:

- `id` — folder name, lowercase kebab-case. Must match `<repo>/pets/<id>/`.
- `displayName` — human label.
- `description` — one short sentence.
- `spritesheetPath` — relative to the pet folder (always `spritesheet.webp` unless you re-encode).

The app loads pets from the **folder name** under `${CODEX_HOME:-$HOME/.codex}/pets/`, so the folder is the source of truth; `id` should match it.

## Deviation: checked-in sheets are 11 rows

Both sprite sheets in `pets/` measure **1536 × 2288** and carry an extra `spriteVersionNumber: 2` field, neither of which appears anywhere in the upstream contract (`validate_atlas.py` and `compose_atlas.py` both hardcode `ROWS = 9`; `qa-rubric.md` requires exact `1536x1872`). Upstream has not changed — the assets are what drifted.

Measured against the sheets:

- **Rows 0–8 sit at the correct offsets.** The extra height is exactly `2 × 208 = 416 px` appended below the 9-row grid, and cells are the correct `192 × 208`, so the webview's fixed CSS background positions for rows 0–8 still land correctly. In practice the pets animate.
- **Rows 9–10 are never addressed.** The app only reads 9 rows, so the 2 extra rows are dead weight (roughly 18% of each file) and their content is invisible.
- **`spriteVersionNumber` is not in the upstream manifest shape.** Either a newer app-side field the skills repo has not documented, or a generation artifact. Harmless if ignored, but unverified.

To bring a sheet back onto contract, crop the top `1536 × 1872` — the 9 contract rows are already in place, so this is lossless with respect to anything the app renders. Do not crop blind: if a future app version really does read 11 rows, the top-crop is what would break.

## On-disk layout

```
${CODEX_HOME:-$HOME/.codex}/pets/<id>/
├── pet.json
└── spritesheet.webp
```

Drop the folder in, restart the Codex pet UI, and the new mascot is selectable.
