# Repository Guidelines

## Scope and Source Roles

Apply the active project's `AGENTS.md`, repository conventions, and user instructions first. Do not assume a fixed grade, form, year, subject, curriculum, language, chapter count, question count, or pipeline name.

Before editing, record a role ledger under `work/` with:

- `CONTENT_SOURCE`: the only source of question content and identity
- `LAYOUT_REFERENCE`: read-only visual geometry and styling
- `LABEL_REFERENCE`: optional identity-matched label corrections
- `CONTENT_SCOPE`: grade/form/year, subject, curriculum or syllabus, language, and grouping axis
- `OUTPUT`: a new descriptive path

Reject a role assignment that conflicts with inspected grade/form markers, subject names, section titles, representative OCR, counts, or hashes.

## Project Structure

Keep maintained project pipelines where the repository already places them. Put generated intermediates under `work/`, grouped by task. Keep source PPTX/PDF/CSV files unchanged. Put the selected final deliverable in the user-requested location or the project root when that matches the repository convention.

Use descriptive output names containing the applicable grade/form/year, subject, source deck, and requested operation. Do not use an output name that implies another grade or subject.

## Build and Validation Commands

Discover existing commands from the active project instead of assuming a pipeline name copied from another grade, subject, or prior task. Prefer the project's phase commands when available:

```text
extract -> classify -> build -> validate
```

Run the project validation command after changing extraction, classification, layout, labels, numbering, crop logic, or output paths. If no validator exists, perform equivalent count, identity, geometry, ZIP, render, and visual checks.

## Implementation Style

Use explicit constants for paths, page dimensions, curriculum maps, grouping rules, and output names. Prefer clear functions such as `audit_sources`, `extract`, `classify`, `build`, `validate`, and `add_question`.

Never hardcode one grade's chapter map, MCQ ranges, subject vocabulary, or expected counts as a universal default. Store scope-specific values in task configuration or manifests.

## Question Identity and Reference Isolation

Build the canonical question manifest only from `CONTENT_SOURCE`. Match questions through exact hashes, PPTX relationship/media identity, stable question IDs, or reviewed OCR/perceptual evidence. Never transfer content or labels by slide number after repacking.

After export, reconcile every output question and embedded question image against the content manifest. Assert that no question-media hash from `LAYOUT_REFERENCE` appears unless it is an explicitly whitelisted decorative asset.

## AI Cleanup of Question Images

Use deterministic pixel-local cleanup when safe. For semantic image editing, inspect a pilot first and preserve every printed word, number, formula, diagram, option, subpart, line break, language, and answer space.

For each accepted edit, record the source image, target image, media filename, relationship or stable question ID, dimensions, crop, and before/after hashes. Reject changes that invent or alter printed content.

## Packed Layout

Use the requested page size and grouping scheme. Treat a section as a chapter, unit, topic, domain, paper part, or another user-selected grouping. Reset numbering only at configured boundaries.

Measure the user's reference to determine whether labels align to a fixed page-right guide or each question image's right edge. Record and validate the chosen anchor mode.

When trimming outer white borders, crop exact pixels and scale the PowerPoint frame by the crop ratios so the printed text keeps its physical size.

## Multi-Agent Work

When delegation is permitted, partition work by non-overlapping question/media IDs or complete section/page ranges. Workers write separate batch assets and result manifests, never the same PPTX or shared JSON. One integrator owns the canonical manifest and final deck; a separate supervisor verifies scope, counts, hashes, geometry, and rendered pages.

## Delivery

Deliver a distinct final PPTX and a concise QA summary naming the content source, layout reference, content scope, question/slide/section counts, identity method, unresolved items, and validation result. Never overwrite or delete a user source merely to tidy the folder.
