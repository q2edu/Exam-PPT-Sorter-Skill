# Repository Guidelines

## Project Structure & Module Organization

This repository contains Python pipelines and generated exam-question artifacts. Keep the main scripts at the repository root:

- `sort_f3_pipeline.py`: extracts, classifies, builds, and validates the F3 deck.
- `sort_f123_pipeline.py`: classifies and builds the combined F1/F2/F3 deck from existing OCR data.
- `work/sort_f3_classification/`: intermediate JSON, OCR output, extracted question blocks, and rendered block images.
- Root-level `.pptx`, `.pdf`, and `.csv` files are source inputs or generated deliverables. Use descriptive output names that preserve the source deck and sorting rule.

Avoid editing `_archive_previous_outputs_20260713/` unless intentionally recovering an old artifact.

## Build, Test, and Development Commands

Run commands from the repository root.

```powershell
python sort_f3_pipeline.py extract
python sort_f3_pipeline.py classify
python sort_f3_pipeline.py build
python sort_f3_pipeline.py validate
```

These steps extract question images, classify them by chapter/type, build the packed PowerPoint, then verify picture counts, labels, bounds, and overlaps.

```powershell
python sort_f123_pipeline.py classify
python sort_f123_pipeline.py build
python sort_f123_pipeline.py validate
```

Use this flow for the combined F1/F2/F3 output after OCR data exists in `work/sort_f3_classification/question-ocr.json`.

Required Python packages include `Pillow` and `python-pptx`.

## Coding Style & Naming Conventions

Use Python 3, 4-space indentation, and standard-library types where practical. Keep constants near the top of each pipeline (`ROOT`, `WORK`, output paths, slide dimensions, chapter maps). Prefer explicit function names such as `classify`, `build`, `validate`, `add_question`, and `question_size`.

For generated files, use source-aware names like `sort f3 new_by_chapter_all_questions_packed_label_bands.pptx`. For intermediate JSON, use lowercase hyphenated names under `work/sort_f3_classification/`.

## Testing Guidelines

There is no separate unit-test suite. Treat each pipeline's `validate` command as the required regression check after changing layout, classification, or output paths. For classification changes, inspect the generated CSV (`question-classification-*.csv`) for obvious chapter drift before rebuilding final decks.

## AI Cleanup of Question Images in PPTX

Use this workflow when a question-bank PPTX contains raster question images with handwritten answers, pen circles, ticks, annotations, or light scan noise that need to be cleaned and put back in their original positions.

- Preserve the source deck. Export a distinct, descriptively named PPTX for every cleanup attempt, and keep extracted and generated image assets under `work/`.
- Prefer the built-in image editing tool with the user's current account. It does not need an API key. For a local image, first inspect it with `view_image` so it is available as the edit target. If this bridge fails under a restricted sandbox, full local-file access may be required; confirm this with one image before attempting a batch.
- The built-in image editing path does not expose a model selector. Do not claim that it used `gpt-image-2`; use the API/CLI path only when the user explicitly chooses it and has configured an API key.
- For more than a few images, start with a pilot slide or 1-2 representative question images. Select a slide the user has not already manually cleaned, so the before/after result is meaningful.
- A single slide can reference multiple image files. Inspect `ppt/slides/_rels/slideN.xml.rels` (or the extraction manifest) to identify every `ppt/media/image*.png` resource used on the target slide. Replace all intended resources together, while leaving all other media unchanged.
- Use a tightly constrained edit prompt: remove only handwritten marks and insignificant scan noise; preserve every printed word, number, formula, diagram, option label, line break, page layout, and aspect ratio; forbid new text, translation, re-typesetting, cropping, and decorative effects.
- Treat generated output as non-deterministic. It can remove handwriting and visibly improve readability, but may re-render printed text, fonts, or spacing even with strict instructions. Do not assume pixel-level or character-level fidelity. A human must inspect each pilot result before approving a larger batch.
- Copy selected generated images into a workspace edit folder using the original PPTX media filenames (for example, `image89.png`). Replace the matching `ppt/media/...` files in a copied PPTX so the slide XML relationships, placements, and sizes remain unchanged.
- Verify every output before delivery: the PPTX ZIP has no errors, the slide count is unchanged, the target slide's relationships still point to the intended media files, and PowerPoint can export/render the changed slide for a visual check. Inspect the rendered slide at full size for handwriting remnants, missing content, changed symbols, spelling, formulas, diagrams, and unintended cropping.

## Commit & Pull Request Guidelines

This directory currently has no local Git history, so use clear, conventional commit messages such as `fix: prevent label overlap in packed slides` or `data: update F3 classification csv`.

Pull requests should include a short summary, affected source files, commands run, and validation results. Attach or link screenshots/PDF previews when slide layout changes.

## Agent-Specific Instructions

Preserve source PDFs/PPTX files unless the user explicitly asks to replace them. Write new outputs with distinct names, and keep generated intermediates inside `work/`.
