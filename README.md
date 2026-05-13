# paper2mindmap

**Turn academic papers into structured mind maps — with multimodal AI that reads both text *and* figures.**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/downloads/)

---

## What is paper2mindmap?

Reading a dense 12-page paper shouldn't take an hour. `paper2mindmap` extracts the full text of a PDF, splits it by section, runs each section through a multimodal LLM (MiniMax Mimo) for structured summarization, captures and interprets every chart and figure, then assembles the result into a hierarchical **Markdown mind map** — ready to open in XMind, Markmap, or any mind-mapping tool of your choice.

It is one of the very few open-source tools that performs true **multimodal literature deconstruction**: it doesn't just OCR the text; it *sees* the figures.

---

## Core Features

- **PDF Text Extraction** — Pulls clean, reading-order text from single- and multi-column academic PDFs using `pdfplumber`. No garbage characters, no scrambled columns.
- **Section-Aware Chunking** — Automatically detects section headings (Introduction, Methods, Results, etc.) and partitions the paper into semantically coherent chunks before sending them to the model.
- **AI-Powered Structured Summarization** — Each chunk is processed with a carefully tuned prompt that produces a consistent JSON-structured summary: research question, method, key result, limitation, and contribution.
- **Multimodal Figure Understanding** — Charts, diagrams, and tables are rendered to images and passed to MiniMax Mimo for visual interpretation. Bar charts become readable takeaways; architecture diagrams become plain-language descriptions.
- **Hierarchical Mind Map Output** — The final output is a clean, indented Markdown file following the `# Title` → `## Topic` → `### Subtopic` convention. Drop it directly into XMind, Markmap, Obsidian, or any tool that understands Markdown outlines.

---

## Quick Start

### Prerequisites

- Python 3.10 or later
- A [MiniMax](https://platform.minimaxi.com) API key (Mimo model access)

### Installation

```bash
# Clone the repo
git clone https://github.com/your-org/paper2mindmap.git
cd paper2mindmap

# Create a virtual environment (recommended)
python -m venv .venv
source .venv/bin/activate   # Windows: .venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

### Configuration

Set your MiniMax API key as an environment variable:

```bash
export MINIMAX_API_KEY="sk-your-api-key-here"
```

Alternatively, create a `.env` file in the project root:

```
MINIMAX_API_KEY=sk-your-api-key-here
```

### Usage

```bash
# Basic: process a single paper
python paper2mindmap.py example.pdf

# Specify output path
python paper2mindmap.py paper.pdf -o my-mindmap.md

# Adjust chunk size (default: 2000 tokens)
python paper2mindmap.py paper.pdf --chunk-size 1500

# Skip figure extraction (text-only mode)
python paper2mindmap.py paper.pdf --no-figures

# Verbose logging
python paper2mindmap.py paper.pdf -v
```

---

## Output Format

The generated file is a standard Markdown document where heading depth (`#` → `##` → `###` → `####`) encodes the mind-map hierarchy.

### Mind Map Template

Every output follows this structural template:

```markdown
# Paper Title

## Research Problem
### Core Question
### Motivation & Gap
### Research Objectives

## Methodology
### Approach Overview
### Model / Framework Design
### Dataset
### Evaluation Protocol

## Key Findings
### Main Results
### Ablation Studies
### Surprising Observations

## Figures & Visual Insights
### Figure 1: [Caption]
### Figure 2: [Caption]

## Contributions
### Theoretical Contributions
### Practical / Engineering Contributions

## Limitations & Future Work
### Acknowledged Limitations
### Suggested Follow-ups
```

### Import into Mind-Map Tools

| Tool | How to Import |
|---|---|
| **Markmap** | Open [markmap.js.org](https://markmap.js.org), paste the Markdown content directly. |
| **XMind** | File → Import → Markdown, select the generated `.md` file. |
| **Obsidian** | Open the file in your vault, use the built-in Outline panel or the Markmap community plugin. |
| **Mermaid** | Convert with a script: headings become `mindmap` graph nodes. |
| **Any text editor** | The Markdown is human-readable as-is — no tool required. |

---

## Project Roadmap

| Version | Milestone | Status |
|---|---|---|
| **v0.1** | PDF text extraction + section chunking + basic LLM summarization pipeline | ✅ Done |
| **v0.2** | MiniMax Mimo multimodal integration: text + figure understanding | ✅ Done |
| **v0.3** | PDF figure auto-screenshot + visual grounding + per-figure multimodal parsing | 🔄 In Progress |
| **v0.4** | Batch processing: process a whole `papers/` directory at once | 📋 Planned |
| **v0.5** | Interactive mind map editor (web UI) with inline paper highlights | 📋 Planned |
| **v1.0** | Multi-model support (GPT-4V, Gemini), citation graph extraction, reference linking | 📋 Planned |

---

## How It Works (Architecture)

```
┌──────────┐    ┌──────────────┐    ┌─────────────────┐
│ PDF File │───▶│ pdfplumber   │───▶│ Section Chunks  │
└──────────┘    │ Extract Text │    │ + Figure Images │
                └──────────────┘    └───────┬─────────┘
                                            │
                   ┌────────────────────────┘
                   ▼
          ┌─────────────────┐
          │ MiniMax Mimo    │  ◀── Multimodal prompt per chunk
          │ (Text + Vision) │      (text context + figure screenshots)
          └───────┬─────────┘
                  │
                  ▼
         ┌──────────────────┐      ┌──────────────────┐
         │ Structured JSON  │─────▶│ Markdown Mind    │
         │ (per-section)    │      │ Map (.md)        │
         └──────────────────┘      └──────────────────┘
```

---

## Contributing

We welcome contributions from researchers, developers, and anyone who reads papers and wants better tools.

### Ways to Contribute

- **Add a PDF parser plugin** for a journal-specific layout (NeurIPS, ICML, arXiv two-column, etc.).
- **Improve the summarization prompt** — better prompts produce better mind maps. Share what works for your field.
- **Add support for another multimodal model** (GPT-4V, Gemini Pro Vision, Qwen-VL).
- **Report bugs** — especially with tricky PDF layouts. Open an issue with a screenshot.

### Development Setup

```bash
git clone https://github.com/your-org/paper2mindmap.git
cd paper2mindmap
pip install -r requirements-dev.txt  # includes pytest, ruff, mypy
pre-commit install
```

### Conventions

- Type hints on all public functions.
- Tests in `tests/`, run with `pytest`.
- Format with `ruff format`; lint with `ruff check`.
- Keep the core pipeline (`paper2mindmap.py`) under 500 lines — extract heavy logic into `lib/`.

### Pull Request Process

1. Open an issue describing the change first.
2. Fork the repo, create a feature branch.
3. Add or update tests that cover your change.
4. Run `pytest && ruff check .` before pushing.
5. Submit the PR against `main`. Tag the issue number in the description.

---

## FAQ

**Q: Why MiniMax Mimo instead of GPT-4V or Gemini?**
Mimo offers competitive multimodal accuracy at a fraction of the cost per image-token, making it practical to process papers with 20+ figures without breaking the bank.

**Q: Can it handle Chinese / multilingual papers?**
Yes. Mimo is natively multilingual. The output language will match the paper's language.

**Q: Does it work with scanned PDFs?**
For text, `pdfplumber` requires selectable text layers. Fully scanned (image-only) PDFs need OCR preprocessing (e.g., Tesseract) before running through the pipeline — planned for v0.5.

**Q: How long does it take per paper?**
A 12-page paper with 8 figures takes roughly 30–60 seconds on current hardware, depending on Mimo API latency.

---

## License

MIT © 2025 paper2mindmap contributors

---

*If this tool saves you time, consider starring the repo and sharing your mind maps with the community.*
