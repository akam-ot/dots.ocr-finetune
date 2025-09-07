# Fine-tuning dots.ocr on Custom OCR Tasks (Colab)

This repository contains a Google Colab notebook that walks through fine-tuning **dots.ocr** on your own images. It covers the full workflow end-to-end: auto-labeling, manual correction, dataset assembly, training, and quick evaluation.

---

## Features

* Auto-label with the base dots.ocr model to bootstrap labels
* Manual correction flow using simple Markdown files
* Data prep utility to create a training JSONL from corrected labels
* Colab-friendly training loop (small batches, gradient checkpointing)
* Quick test cell and an optional Gradio demo

---

## Repository Structure

```
dots.ocr-finetune/
├─ dotsocr_finetune_colab.ipynb
└─ README.md
```

---

## Quick Start

1. **Open in Colab**
   Open the notebook in Colab.

2. **GPU runtime**
   In Colab: Runtime → Change runtime type → Hardware accelerator → GPU (I have tested only A100 and L4)

3. **Upload images**
   Create a folder in Google Drive: `MyDrive/images`, and place images (`.jpg/.jpeg/.png/.pdf`).

4. **Follow the cells in order**

   * Setup and dependency install
   * Auto-label (optional if you already have ground-truth)
   * Manual corrections (edit the generated `.md` files)
   * Prepare training data (resized images + JSONL)
   * Train and test

---

## Data Layout

After auto-labeling, each sample directory contains an image, Markdown text, and a small JSON metadata file:

```
autolabel/
├── sample1/
│   ├── sample1.jpg
│   ├── sample1.md      # you edit this with corrected text
│   └── sample1.json
├── sample2/
│   ├── sample2.jpg
│   ├── sample2.md
│   └── sample2.json
└── ...
```

Place your **corrected** `autolabel` folder under `MyDrive/autolabel` before running the “Prepare Training Data” cell.

---

## Training JSONL Format

The notebook writes a training file named `train_ocr_resized.jsonl` (one JSON object per line). Each line has a chat-style `messages` field:

```json
{"messages":[
  {"role":"user","content":[
    {"type":"image","image":"/content/resized_images/sample1.jpg"},
    {"type":"text","text":"prompt_ocr"}
  ]},
  {"role":"assistant","content":"Corrected OCR text for sample 1."}
]}
{"messages":[
  {"role":"user","content":[
    {"type":"image","image":"/content/resized_images/sample2.jpg"},
    {"type":"text","text":"prompt_ocr"}
  ]},
  {"role":"assistant","content":"Corrected OCR text for sample 2."}
]}
```

Notes:

* Use **absolute local paths** for `"image"` (e.g., `/content/resized_images/...`).
* The assistant content is a **single string** (the corrected text).
* One JSON object per line (JSONL), UTF-8.

---


## Requirements

The notebook installs dependencies directly.

---

## Credits

* **dots.ocr** (model and tools): Rednote HiLab — GitHub and model card linked above.
* Training workflow: Taken from [wjbmattingly's trainig repo](https://github.com/wjbmattingly)), this notebook consolidates a straightforward, Colab-first workflow.
