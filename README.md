# Agent Pets

Mascots that mirror the working state of AI coding agents — sync them across machines like dotfiles.

Each pet is a PNG/WebP sprite sheet whose frames map to an agent's lifecycle states (idle, running, failed, waiting, review, etc.). Drop a folder into `~/.codex/pets/` (or whatever `${CODEX_HOME}/pets/` resolves to) and the Codex desktop pet UI picks it up automatically.

## Install

```bash
git clone git@github.com:sido-meet/agent-pets.git
```

Copy into the Codex load directory (default `~/.codex/pets/`):

```bash
mkdir -p ~/.codex/pets
cp -R agent-pets/pets/* ~/.codex/pets/
```

Or symlink so the repo stays the source of truth:

```bash
ln -s "$(pwd)/agent-pets/pets" ~/.codex/pets
```

The pet loader resolves `${CODEX_HOME:-$HOME/.codex}/pets/` — see the [hatch-pet contract](https://github.com/openai/skills/tree/b0401f07213a66414d84a65cb50c1d226f99485a/skills/.curated/hatch-pet) for the canonical spec.

## Pets

| ID           | Display name   | Size  | States                                                    |
| ------------ | -------------- | ----- | --------------------------------------------------------- |
| `brick-lion` | Brick Lion     | 2.9 MB | idle, running-right/left, waving, jumping, failed, waiting, running, review |
| `meimei`     | Meimei 莓莓     | 2.4 MB | same 9-row atlas as above                                 |

Each pet is one self-contained directory:

```
pets/<id>/
├── pet.json          ← manifest (id, displayName, description, spritesheetPath)
└── spritesheet.webp  ← 8 cols × 9 rows, 1536×1872, 192×208 per cell, transparent
```

⚠️ Both checked-in sheets are currently **1536 × 2288** (2 extra rows) and carry a non-standard `spriteVersionNumber` field. See [docs/sprite-contract.md](docs/sprite-contract.md#deviation-checked-in-sheets-are-11-rows).

Full sprite contract: [`docs/sprite-contract.md`](docs/sprite-contract.md).

## Add a new pet

1. Generate the sprite sheet with the **hatch-pet** skill from the OpenAI skills repo (pinned commit `b0401f07`):
   https://github.com/openai/skills/tree/b0401f07213a66414d84a65cb50c1d226f99485a/skills/.curated/hatch-pet
2. Drop the produced folder into `pets/<your-pet-id>/`. Make sure both `pet.json` and `spritesheet.webp` are present.
3. Commit and push:

   ```bash
   git add pets/<your-pet-id>/
   git commit -m "Add <your-pet-id>"
   git push
   ```

When the upstream hatch-pet contract changes (cells, grid, manifest fields), this README's pinned link becomes stale — update the SHA then.

> Pinned `b0401f07` is current: both `references/codex-pet-contract.md` and `references/animation-rows.md` are byte-identical to upstream `main` (`49f948f`). Verified 2026-10-02.

## Repo layout

```
agent-pets/
├── README.md
├── .gitignore
├── docs/
│   └── sprite-contract.md
└── pets/
    └── <pet-id>/
        ├── pet.json
        └── spritesheet.webp
```

Everything outside `pets/` is documentation. Only `pets/<id>/{pet.json,spritesheet.webp}` is read by Codex at runtime.
