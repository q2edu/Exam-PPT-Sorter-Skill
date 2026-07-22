---
name: exam-ppt-sorter
description: Clean, identify, relabel, renumber, sort, rebuild, pack, and validate exam-question PowerPoint decks for any grade, form, year, subject, curriculum, or language. Use for PPTX question banks that need raster-image extraction, removal of handwriting/answers/shadows/scan noise, curriculum-section classification, correction of source labels, removal of old outer question numbers, section-reset numbering, multiple-choice vs written-question ordering, paper-saving student layouts, role-locking between a content source and layout reference, multi-agent batch coordination, or evidence-based comparison against a source/reference deck.
---

# Exam PPT Sorter

## Bundled Repository Rules

At the start of every task using this skill, read [references/AGENTS.md](references/AGENTS.md) in full and apply it as the default repository and delivery policy. Also read any `AGENTS.md` found in the active project. Follow system, developer, user, and active-project instructions when they are more specific or conflict with the bundled defaults.

## Bundled Visual Reference

Inspect [assets/f2-sorted-clean-layout-reference.pptx](assets/f2-sorted-clean-layout-reference.pptx) when building or repairing a sorted student deck and no stronger user reference exists. Although its filename and sample content come from one form, treat it as grade- and subject-agnostic geometry only: compact section headers, question numbers placed close to question content, upright and normally proportioned text, preserved image sizing, and lower-right source-label placement. Never use its questions, curriculum, grade, section map, labels, counts, or ordering as task data. A user-provided reference or explicit layout instruction always overrides it.

## Lock Content and Reference Roles Before Work

Never infer file roles from whichever deck is easiest to parse, from file order, or from similar grade/form filenames such as `Grade 2` versus `Grade 3` or `F2` versus `F3`. Before extraction or building, assign and record these roles:

- `CONTENT_SOURCE`: the only deck or dataset authorized to supply question images, OCR text, question identity, section membership, question type, and default source labels.
- `LAYOUT_REFERENCE`: a read-only example authorized to supply only page size, margins, typography, spacing, label geometry, header style, and other explicitly requested visual rules.
- `LABEL_REFERENCE`: optional; authorized to correct labels only after one-to-one question identity matching.
- `OUTPUT`: a new descriptive path that is never any input path.

Write a small role ledger under `work/` containing absolute paths, hashes, grade/form/year, subject, curriculum or syllabus, language, requested grouping axis, slide/question counts, and the allowed contribution of each file. Confirm the role assignment using file contents, not filenames alone: inspect grade/form markers, subject and curriculum markers, section titles, representative OCR, question counts, and media hashes. Record this as `CONTENT_SCOPE`. If the user's wording or attachments leave the content source or scope ambiguous, stop and ask before building.

Treat these as hard contamination barriers:

- Never copy question images, OCR, classification, numbering, labels, or question counts from `LAYOUT_REFERENCE` into the output.
- Never let any bundled or user-supplied layout reference become the question source, even when its grade, form, year, or subject looks similar.
- Build the question manifest exclusively from `CONTENT_SOURCE`; attach a `content_source_hash` or stable source ID to every row.
- Before export, assert that every output question maps one-to-one to the content manifest.
- After export, compare embedded question-media hashes against the content manifest and assert that no reference question-media hash appears, except separately whitelisted decorative assets.
- Report the content source and layout reference by filename in the final QA summary so a role swap is visible.

## Outcome

Produce a student-ready exam-question PPTX for the requested grade, form, year, subject, curriculum, and language while preserving all printed question content. Keep the source deck unchanged, retain the identity and source label of every question, and prove completeness with automated and visual QA before delivery.

Treat these as non-negotiable:

- Do not alter printed words, numbers, formulas, diagrams, option labels, subparts, line breaks, or answer space.
- Do not match questions or labels by slide number after either deck has been repacked, filtered, or reordered.
- Do not crop question content to satisfy a target size. Move the whole question to the next page when it does not fit.
- Do not deliver from a contact sheet alone. Render and inspect every page at readable resolution.
- Do not overwrite the source PPTX. Create a descriptive output and keep intermediates under `work/`.

## Select the Operation Mode

Choose the narrowest mode that satisfies the request:

- **Original-position cleanup**: extract and clean raster media, then replace the same PPTX media while preserving every shape's geometry and relationship.
- **Label-only correction**: match questions against a corrected reference, change only verified source-label text, and prove all unrelated content is unchanged.
- **Sorted student rebuild**: extract stable question blocks, classify them by the requested section scheme, remove old outer numbering, add section-reset numbering, and repack them into a paper-saving student deck.
- **Combined mode**: run cleanup and identity QA first, then rebuild from the accepted cleaned assets.

Do not rebuild a deck when the user asked only for original-position replacement or label correction.

## End-to-End Workflow

Use explicit phase gates. Do not skip extraction, identity mapping, or final comparison merely because a deck looks correct.

1. **Audit sources**: inventory every PPTX/PDF, slide count, picture count, labels, dimensions, grade/form/year, subject, curriculum, section markers, and existing output. Record hashes before editing.
2. **Extract questions**: export each question image or question block and record its slide, shape, relationship, media filename, pixel size, crop, and source label.
3. **Establish identity**: build a stable question manifest before sorting, cleaning, relabeling, or repacking.
4. **Clean images**: remove only requested handwriting, answers, shadows, skew, and insignificant scan noise; preserve the printed question exactly.
5. **Normalize numbering**: remove only the old outer main question number, then add clean native numbering that resets at each requested section boundary.
6. **Classify and order**: classify by the requested curriculum grouping and question type, then audit low-confidence cases.
7. **Build the student layout**: pack as many readable questions as possible per requested page size, with a fixed number gutter and a non-overlapping source label at each question's lower-right.
8. **Validate and supervise**: compare old and new decks, run geometry/count/diff checks, render every page, and perform an independent supervisor pass.
9. **Deliver and archive**: keep sources and the selected final deck easy to find; move intermediates into `work/` or a clearly named archive instead of deleting them.

For a long batch, report progress at phase boundaries and at regular intervals. Include completed/total counts, the current phase, defects found, and a realistic ETA. Recalculate the ETA after the pilot or first completed batch instead of presenting an untested estimate as certain.

## Question Manifest and Identity

Create one manifest row per question with at least:

- `question_id`: stable project-local ID
- `source_id`: source paper/deck identity
- `grade_or_form`, `academic_year`, `subject`, `curriculum`, and `language` when known
- `question_no_in_file`: original question number when known
- `section_key` and `section_title`: chapter, unit, topic, domain, paper part, or another user-selected grouping
- `question_type`: `mcq`, `written`, or another explicit source-defined type
- `source_label`
- `slide_index` and `shape_index`
- `relationship_id` and `media_filename` when the item comes from a PPTX picture
- `image_path`, pixel width, pixel height, and crop values
- exact file/media hash
- OCR text and OCR confidence or review status
- cleanup, renumbering, relabeling, and QA status

Use the strongest available identity in this order:

1. Exact hash of the original image or extracted question block.
2. PPTX relationship/media mapping plus original shape metadata.
3. Stable question-block ID and source question number.
4. Perceptual hash plus normalized OCR text and geometry.
5. Manual review for unresolved or conflicting matches.

Never transfer labels by slide index when deck counts or order differ. If a reference deck has been repacked, match each question through the hierarchy above and produce a mapping table with match method and confidence. Require one-to-one matches: no duplicate target, no missing source, and no unexplained extra question.

For label-only corrections, assert that exactly the expected label shapes changed and that picture bytes, picture geometry, section numbering, and unrelated labels remained unchanged.

## Extracting PPTX Images Safely

Inspect `ppt/slides/_rels/slideN.xml.rels` or the extraction manifest because one slide can reference multiple media files. Preserve the relationship between each picture shape and `ppt/media/...` resource.

When replacing an image in its original position:

- Copy the source deck first.
- Keep the media filename, relationship ID, shape position, size, rotation, crop, and z-order unchanged.
- Preserve the original pixel dimensions and aspect ratio unless the user explicitly requests a new crop.
- Replace only the intended media files inside the copied PPTX.
- Confirm the resulting ZIP opens without errors and the slide relationships still resolve.

If the source uses one large scan containing several questions, segment stable question blocks before cleanup and rebuilding. Include a small safety margin, but do not retain neighboring main question numbers when the user wants fresh numbering.

## Cleaning Question Images

Use deterministic pixel-local operations when they can safely remove a mark without re-rendering printed content. Use the image editing tool when handwriting, answers, shadows, page curvature, or perspective distortion require semantic reconstruction.

When using AI image editing:

1. Also load the `imagegen` or batch image editing skill.
2. Inspect the local image first.
3. Run a pilot on 1-2 representative images, including formulas, faint print, handwriting, and shadows.
4. Use a strict prompt: remove only handwriting, student answers, ticks/circles, shadows, skew, and insignificant scan noise; preserve every printed word, number, formula, diagram, option, line break, layout, aspect ratio, and language; forbid new text, translation, re-typesetting, decorative effects, and cropping.
5. Compare the pilot against the original at full size. Verify that the requested marks actually disappeared and that printed content did not change.
6. Batch only after the pilot passes. Treat every generated image as non-deterministic and subject to review.

Interpret `answers` as handwritten or student-added responses/selection marks by default. Preserve printed answer-key content unless the user explicitly asks to remove it. Reject an edit if it leaves a requested answer mark, invents or changes text, modifies a symbol/exponent, removes diagram lines, changes an option, alters layout, or crops content. Preserve the original image and record the rejection so it can be retried safely.

## Removing Old Main Question Numbers

Remove only the outer main numeric prefix, such as `1.`, `18.`, or `23`. Preserve all of these unless the user explicitly says otherwise:

- option labels such as `A`, `B`, `C`, `D`
- alphabetic subparts such as `(a)`, `(b)`, `(c)` or `a.`, `b.`
- Roman-numeral subparts such as `(i)`, `(ii)`, `(iii)`
- numbers, indices, powers, fractions, equations, graph labels, and diagram annotations inside the question

Detect the main number from OCR word boxes and connected components near the outer question edge. Do not auto-delete a digit merely because it is leftmost. Require spatial evidence that it is a standalone prefix aligned with the first question line.

Use this removal order:

1. Crop a clean outer gutter only when the entire main number is isolated and the safety margin proves no question content enters the crop.
2. Otherwise erase or inpaint only the verified number bounding box on a copy.
3. For a combined or ambiguous component, use a reviewed per-image bounding box instead of a global rule.

Audit each removal with a localized image diff. Unedited images must remain pixel-identical; edited images may differ only inside the approved removal region plus a small antialiasing margin. Specifically inspect multi-digit prefixes so partial removal cannot leave a residual digit such as `8.` from `18.`.

Add the new sequence as native PowerPoint text in a fixed far-left gutter. Start at `1.` for each requested section, increment once per question, and never number section-divider slides. Align the question image/content to a consistent content x-position after removal.

## Classification and Ordering

Store OCR text beside each extracted item. Use the selected grade/form/year, subject, curriculum, and section map first, then inspect low-confidence OCR/classification cases. Never reuse another grade's section map without an explicit crosswalk.

Prefer explicit paper or section metadata for question type. If unavailable, use user-supplied multiple-choice ranges or stable repeated section boundaries. Within each requested section or grouping, use this order unless the user specifies otherwise:

1. Multiple-choice questions in original source order.
2. Written/application questions in original source order.

Do not silently drop duplicates. Distinguish true duplicates from repeated questions in different source papers through `source_id` and exact identity.

## Student-Ready Packed Layout

Use the requested template size. For true A4 portrait, use approximately `8.2677 x 11.6929` inches. Treat `7.5 x 10.833333` inches as a project-specific compact portrait size, not standard A4. Favor paper efficiency, but keep every question readable and intact.

Use a compact section header by default for paper-saving student layouts. A section may be a chapter, unit, topic, domain, paper part, or another user-selected grouping. Use a full section-divider slide only when the user requests it or the source template requires it. Keep `divider`, `header`, and `none` as explicit layout choices.

Treat each question as a layout unit containing:

- a fixed left number gutter
- the question image/content region
- a reserved footer or adjacent empty area for the source label at the question's lower-right
- a small inter-question gap

Pack questions top-to-bottom and place another question on the page whenever the complete unit fits. If it does not fit, move the complete unit to the next page. Never crop the bottom of a question, split a question across pages, overlap labels, or shrink one question dramatically merely to avoid one extra page.

Do not claim that raster question text equals an exact point size based only on image dimensions. Judge readability from rendered output at normal print scale. Normalize visibly inconsistent source scales where possible, while preserving formulas and spacing.

Place each source label as a native PowerPoint shape anchored to the question unit, not to the slide globally. Follow the user's explicit placement. If none is given, use the question's lower-right in reserved white space below or beside the image:

- black fill and border
- white bold Arial text
- consistent height and padding
- width based on label text or a consistent project maximum
- no clipping, wrapping, or overlap with question content

Do not guess what â€œlower-rightâ€ means. Measure at least three labels in the user's reference:

- If their right edges share one slide x-coordinate, align every label to a fixed page-right guide while keeping each label vertically attached to its question.
- If their right edges follow different question-image widths, align each label to that question image's right edge.
- Record the chosen anchor mode (`page-right` or `question-right`) in the layout plan and validate it numerically across all labels.

When trimming outer white borders from question images, save an exact pixel crop and a crop manifest containing the original size, crop box, cropped size, and before/after hashes. Scale the preferred PowerPoint frame by the crop ratios so printed text keeps the same physical size:

- `new_width = old_width * cropped_pixel_width / original_pixel_width`
- `new_height = old_height * cropped_pixel_height / original_pixel_height`

Do not place a tightly cropped image back into the old full frame because that enlarges the printed text and changes the reference layout. Use a larger safety pad below written questions when blank space may be intentional answer space.

Keep section headers and page numbers separate from question numbering and source labels.

## Building and Subagent Swarms

Use `scripts/build_sorted_exam_ppt.py` when classified JSON and extracted images already exist and its simple label-band layout matches the request. Treat it as a basic builder, not a substitute for the manifest, cleanup, relabeling, renumbering, or full QA workflow. Adapt the project pipeline when the requested lower-right label, number gutter, or packing behavior differs.

Use a subagent swarm only when the user or active agent policy permits delegation and the work is large enough to benefit from independent, bounded batches. Keep the primary agent as coordinator and final integrator. The primary agent owns the role ledger, canonical manifest, layout specification, final PPTX, and delivery decision.

Recommended roles:

- **Source auditor**: independently inventories `CONTENT_SOURCE`, `LAYOUT_REFERENCE`, and optional `LABEL_REFERENCE`; returns a role ledger and flags any grade/form/year, subject, curriculum, language, or content mismatch.
- **Identity/classification workers**: process non-overlapping question-ID or media-ID ranges and return result manifests only.
- **Cleanup workers**: edit non-overlapping image/media IDs, write to separate batch folders, and return before/after hashes plus acceptance status.
- **Layout workers**: prepare non-overlapping complete section/page ranges or layout plans; never write the shared final PPTX.
- **Visual QA workers**: inspect assigned non-overlapping rendered slide ranges and report defects without editing source assets.
- **Supervisor**: independently reconciles counts, hashes, role separation, label-anchor mode, and high-risk pages before the integrator exports or delivers.

Every subagent handoff must state immutable input paths and hashes, the exact non-overlapping range, allowed actions, forbidden files, output directory, result schema, and acceptance checks. Give workers only the context needed for their batch; do not let a layout reference masquerade as a content source in a worker prompt.

Apply these concurrency rules:

- Partition cleaning by non-overlapping image/media IDs.
- Let workers write cleaned assets and result manifests, never the same PPTX.
- Partition layout builds by complete page/section ranges.
- Use one integrator to merge outputs in deterministic order and preserve page size.
- Use a separate supervisor to compare counts, mappings, diffs, and rendered pages.
- Avoid concurrent writes to the same PPTX, JSON, CSV, or output folder.
- Do not allow a worker to change the canonical role ledger or manifest in place; submit a proposed delta for the integrator to validate and merge.
- Require the integrator to reject duplicate IDs, missing IDs, overlapping batch ranges, unexpected hashes, or results from the wrong grade/form/year, subject, curriculum, language, or source.
- Require the supervisor to rebuild its checks from raw manifests and rendered artifacts rather than trusting worker summaries.

Checkpoint after every batch so interrupted work can resume without repeating accepted images.

## Validation Gates

Do not deliver until all applicable gates pass.

### Structural checks

- PPTX ZIP integrity passes and PowerPoint can open/render it.
- Slide dimensions are correct.
- Every relationship points to an existing media file.
- Expected slide, question, picture, label, and section counts reconcile with the manifest.
- Every source question appears exactly once unless duplication was explicitly requested.
- Numbering starts at `1.` and is consecutive within every section configured to reset.

### Geometry checks

- No picture, number, label, header, or page number is out of bounds.
- No source label overlaps any question image/content.
- No question units overlap.
- No text clips or wraps unintentionally.
- No question is split, cropped, or reduced below the project's readable scale.

### Content and diff checks

- Cleaned images contain no requested handwriting, student answer, tick/circle, or shadow residue.
- Printed text, formulas, symbols, diagrams, options, and subparts match the source.
- Old main question numbers are fully removed without deleting subparts.
- Unchanged media are byte- or pixel-identical.
- Expected relabel operations are the only label changes.
- Exact-hash mappings are preferred; all fallback matches have an audit record.

### Visual supervisor pass

Render every slide to PNG or PDF and inspect at full readable size. Use contact sheets only for navigation. Use a separate supervisor agent/process when available, otherwise perform a clearly separate second-pass review. Independently inspect high-risk pages: first/last question of every section, grade- or subject-specific notation, multi-digit old numbering, faint scans, formulas/exponents, diagrams, long written questions, label corrections, and page breaks.

Run the repository pipeline's `validate` command when available. Treat a programmatic pass as necessary but not sufficient; fix every supervisor defect and rerun the complete validation set.

## Delivery and Folder Cleanup

Deliver a clearly named final PPTX plus a concise QA summary containing:

- source and reference filenames
- final question/slide/section counts
- cleaned, relabeled, and renumbered counts
- identity-match methods and unresolved items, if any
- structural and visual validation results

Keep source files, maintained pipeline scripts, and the chosen final deck in the project root when that matches the user's organization. Move intermediate decks, temporary one-off scripts, manifests, OCR JSON/CSV, extracted blocks, renders, and scratch files into `work/` or a dated archive. Never move maintained project pipelines or delete user files merely to make the folder look clean. If PowerPoint locks a file, report it and ask the user to close it before moving.
