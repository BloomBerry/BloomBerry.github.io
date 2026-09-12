---
name: paper-review
description: Review an academic paper (arXiv or PDF URL) and produce a Korean blog post in this repo's _post format. Use when the user gives a paper URL and asks for a review, summary, or "_post 형식" writeup. Outputs a markdown file under _posts/.
---

# Paper Review → _post Markdown

Turn a paper URL into a Korean review saved as a Jekyll `_post` in this repo.

**Input:** a paper URL (arXiv abstract/PDF link, or direct PDF).
**Output:** `_posts/YYYY-MM-DD-<Slug>.md` matching the house style below.

## Workflow

1. **Fetch the paper.** Use `WebFetch` on the PDF URL (for arXiv, `https://arxiv.org/pdf/<id>`).
   - WebFetch uses a small model and often returns a thin/incorrect summary. It also saves the raw PDF locally and prints the path (`.../tool-results/webfetch-*.pdf`).
   - **Always then `Read` that saved PDF path with `pages: "1-N"`** to get the real content (title, authors, numbers, tables, figures). Do not write the review off the WebFetch summary alone — verify the actual title and every number against the PDF pages.
   - If the arXiv number in the request resolves to a different paper than the user named, note the real title in your final reply.

2. **Extract the essentials** from the PDF: title, authors/affiliation, venue/date, the task, motivation, contributions, method, key benchmark numbers, ablations, limitations. Locate every numbered Table and Figure and what each shows.

3. **Write the post** to `_posts/YYYY-MM-DD-<Slug>.md`:
   - `YYYY-MM-DD` = today's date. `<Slug>` = short paper name (e.g. `Ministral-3`).
   - Follow the format spec below exactly.

4. **Report** the file path and a 3-5 line summary of the paper. List how many table/figure placeholders were inserted.

## Format spec (match existing posts)

Look at a recent post such as `_posts/2026-09-11-EDGE-KOPA.md` for tone before writing.

- **Front matter** — only a title:
  ```
  ---
  title: "[Category] Short Title: One-line Korean gist"
  ---
  ```
  `[Category]` is a tag like `[LLM]`, `[Agent]`, `[VLM]`, `[RAG]` — pick what fits.

- **Body language: Korean.** Technical terms, model names, and metric names stay in English. Tone is concise, analytical, bullet-driven (not prose paragraphs).

- **Header block** repeats the H1 title, then a metadata bullet list:
  - `- paper: <url>`
  - other links if present (`- github:`, `- webpage:`, `- models:`)
  - venue / license / date / citation count if known
  - `- 저자: ...`
  - `- downstream task: ...`
  - `- 주요 용어` — a sub-list defining 3-5 key terms the paper coins, each with a crisp Korean definition. Use LaTeX (`$...$`, `$$...$$`) for notation.

- **Numbered sections** in this order (adapt to the paper): `# 1. Motivation`, `# 2. Contribution`, `# 3. Related Works` (optional), then method sections (`# 4. ...`, `# 5. ...`), `# 6. Experiments`, `# 7. Conclusion & Limitations`.
  - Use `##`/`###` subsections and `**bold**` lead-ins for named ideas.
  - Use `$\to$`, `pp` (percentage points), and markdown tables for result comparisons — mirror the existing posts.
  - Add a short **Takeaways** list at the end.

- **Tables and figures: DO NOT embed images.** Where a table or figure belongs, insert a placeholder on its own line so the user can attach the crop themselves:
  - `tableX 첨부 (한 줄 설명)` — e.g. `table2 첨부 (Base 모델 벤치마크 비교)`
  - `figureX 첨부 (한 줄 설명)`
  - `algorithmX 첨부 (...)` for algorithm blocks.
  - Use the paper's own numbering. Place the placeholder right where the paper references it.

## Notes
- Get numbers right — quote them from the PDF pages, not memory.
- Keep the review faithful; flag limitations the authors state.
- If the paper isn't on arXiv, WebFetch the given PDF URL directly; the Read-the-saved-PDF step is the same.
