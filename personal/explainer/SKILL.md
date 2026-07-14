---
name: explainer
description: Draw a diagram AND explain it, using the engine the user names — Mermaid (inline markdown, renders in Claude/GitHub), Excalidraw (.excalidraw file for excalidraw.com/Obsidian), or Obsidian Canvas (.canvas file). Use when the user asks to visualize, diagram, chart, flowchart, mindmap, or explain a concept/system/process visually (画图, 流程图, 思维导图, 可视化, 架构图), or invokes /explainer. If no engine is named, default to Mermaid.
---

# Explainer

Turn a concept, process, system, or comparison into a diagram **plus a concise explanation**. Three rendering engines are available; the user names the engine in their request.

## Step 1 — Pick the engine

| User says | Engine | Read this guide | Output |
|---|---|---|---|
| "mermaid", nothing specified (default) | **Mermaid** | [mermaid/GUIDE.md](mermaid/GUIDE.md) | ```mermaid``` code block inline — renders in Claude, GitHub, and any .md |
| "excalidraw", "标准excalidraw", "动画" | **Excalidraw** | [excalidraw/GUIDE.md](excalidraw/GUIDE.md) | `.excalidraw` / `.md` file saved to cwd — open at excalidraw.com or in Obsidian |
| "canvas", "obsidian canvas" | **Canvas** | [canvas/GUIDE.md](canvas/GUIDE.md) | `.canvas` file saved to cwd — open in Obsidian |

Only read the guide for the engine chosen — each guide is self-contained (its own
`references/` and `assets/` live beside it).

## Step 2 — Generate

Follow the chosen guide exactly (diagram-type selection, layout, syntax-error
prevention rules, file format). The guides are authoritative for their engine.

## Step 3 — Explain

After the diagram, always add a short explanation section:

- **What you're looking at** — 2-4 sentences walking the reader through the diagram
  top-to-bottom (or the reading order that fits the layout).
- **Key insight** — the one thing the diagram makes obvious that prose wouldn't.
- Keep it tight; the diagram carries the structure, the text carries the "so what".

## Notes

- Mermaid is the only engine that renders inline in chat and in plain markdown —
  that's why it's the default when the user doesn't name one.
- For Excalidraw/Canvas, after saving the file, tell the user the file path and
  exactly how to open it.
