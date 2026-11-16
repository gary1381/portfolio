# Local PDF Toolkit — a local PDF editor for people and a PDF toolkit for AI agents

*AI engineering · document AI · product engineering* · Sep–Nov 2026 · Python, Java, Rust/WASM, JavaScript · [installers (v0.2.0)](https://github.com/gary1381/openpdf-releases) · [code](https://github.com/gary1381/local-pdf-toolkit)

> The v0.2.0 installers were released under the earlier working name "OpenPDF".

## What it is

Local PDF Toolkit runs entirely on your own computer, so documents are never uploaded. It has two faces:
- **For people:** a desktop app (Linux .deb / AppImage) and a browser version that runs in WebAssembly.
- **For programs and AI agents:** a REST API, plus an MCP server that packages the same features as tools for Claude, Cursor and other agents.

**Features:**
- Edit text, annotate, fill and sign forms (including certificate-based digital signatures).
- Organize pages, OCR English and Chinese scans, encrypt, and apply true redaction.
- Read documents the way a language model needs them: headings, reading order across columns and tables, as Markdown.
- Interface in 8 languages.

## Architecture

A gateway (FastAPI) serves the editor and the REST API and keeps editing sessions on disk. It calls four services:
- **PDFBox** (Java): text, forms, annotations, signatures.
- **qpdf:** page operations and encryption.
- **OCR** (optional): OCRmyPDF with RapidOCR / PaddleOCR models.
- **Document structure** (optional): Docling layout and table models.

The browser build runs the same services in a Web Worker, using PDFium compiled to WebAssembly. All components are open source with licenses that allow commercial use, and every one is listed in a third-party notices file.

## Results

**Reading PDFs for agents** — olmOCR-Bench (Ai2): 1,403 single-page PDFs and about 7,000 pass/fail checks, scored with the benchmark's own code.

| System | Average of 6 non-math categories | Seconds per page |
|---|---|---|
| Docling 2, default settings | 71.6 | 19.0 |
| **Local PDF Toolkit `pdf_read_markdown`** | **71.3** | **7.8 (CPU only)** |
| PyMuPDF4LLM 2.0 | 50.2 | 2.4 |
| *Marker v1.6.2 / Mistral OCR / GPT-4o (published figures)* | *71.4 / 71.9 / 71.9* | |
| *olmOCR (published)* | *78.0* | |

- Local PDF Toolkit matches Docling's accuracy at about 2.4× the speed. On the non-math categories it is level with the published Marker, Mistral OCR and GPT-4o figures.
- Its Markdown output beats its own plain-text output by 17 points and PyMuPDF4LLM by 10.
- **Weak spots:** formulas (no LaTeX output, so the two math categories score 0) and old typewritten scans.

**Redaction** — does the redacted file still give the secret away? 13 test documents of my own design, each checked afterwards by tools that took no part in the redaction.

| Tool | Clean documents (of 13) |
|---|---|
| **Local PDF Toolkit, desktop and browser** | **13** |
| PdfCraft 0.18.2, with Sanitize | 13 (but it removes every comment, bookmark and attachment) |
| Stirling-PDF 3.2.1, convert to image | 10 (and the text is no longer searchable) |
| PyMuPDF 2.0 + `scrub()` | 9 |
| Stirling-PDF 3.2.1, auto-redact | 8 |
| A black box drawn on top (control) | 0 |

Most tools remove the visible text. The leaks are in the rest of the file: comments, form fields, document properties, bookmarks, attachments, screen-reader text and the pixels of scanned pages.

## What I learned

The benchmark changed the product. One example: a tool option documented as helping multi-column pages turned out to make them much worse (a multi-column score of 58 → 13), so the documentation was corrected.

---
*Built with AI coding agents; I set the product scope and the architecture, designed the benchmarks and reviewed every result.*
