# Provenance

The three engine modules are vendored from
**[axtonliu/axton-obsidian-visual-skills](https://github.com/axtonliu/axton-obsidian-visual-skills)**
(MIT © Axton Liu), fetched 2026-07-14:

| Module here | Upstream path | Changes |
|---|---|---|
| `mermaid/GUIDE.md` + `mermaid/references/` | `mermaid-visualizer/SKILL.md` + `references/` | content unmodified; file renamed SKILL.md → GUIDE.md |
| `excalidraw/GUIDE.md` + `excalidraw/references/` | `excalidraw-diagram/SKILL.md` + `references/` | same |
| `canvas/GUIDE.md` + `canvas/references/` + `canvas/assets/` | `obsidian-canvas-creator/SKILL.md` + `references/` + `assets/` | same |

The top-level `SKILL.md` (the `/explainer` dispatcher: engine routing + mandatory
explanation step) is original to this repo. Inner files are renamed GUIDE.md so
only the dispatcher is discovered as a skill entry point.
