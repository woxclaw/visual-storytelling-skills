---
name: nanobanana
description: >
  Generate or edit visual-storytelling reference images, style anchors, and storyboard
  frames with continuity-aware prompts. Use Codex built-in image generation by default;
  use Nano Banana only when the user or project explicitly selects it. Handles local
  references, edit invariants, multi-image role assignment, and project-safe outputs.
---

# Visual Story Image Generation

This skill keeps its historical `nanobanana` name for compatibility, but it is
provider-aware. Shape the image request once, then execute it through the selected image
provider without leaking provider-specific fields into scene or shot manifests.

## Provider selection

1. Use Codex's built-in `image_gen` capability by default.
2. Use the Nano Banana MCP only when the user explicitly asks for Nano Banana or the
   project manifest names `image_provider: nanobanana`.
3. Do not silently switch providers after a failure. Report the failed capability and
   ask before using another provider.

The provider changes execution mechanics, not the creative prompt or continuity rules.

## Shared workflow

1. Classify the operation as new generation or edit.
2. Assign every input image one role: identity, pose/action, environment, prop,
   recurring element, style, edit target, or compositing input.
3. Use the smallest complete reference set. Do not pass every project image.
4. Build prompts in this order: subject, action/state, setting, composition,
   lighting/mood, style/materiality, then constraints and invariants.
5. For edits, list what must remain unchanged before describing the delta.
6. Generate one distinct asset per call. Variants of the same asset may share a prompt,
   but different characters, locations, props, or frames need separate calls.
7. Inspect the result for identity, layout, prop, lighting, text, and negative-constraint
   fidelity. Iterate with one targeted change at a time.
8. Save project-bound finals at the project path requested by the calling skill. Never
   overwrite an existing asset unless the user explicitly requested replacement.

For reusable prompt structures, read
[references/frameworks.md](./references/frameworks.md). For production examples, read
[references/examples.md](./references/examples.md).

## Codex built-in image generation

Use the built-in `image_gen` tool for generation, editing, multi-reference composition,
and identity-sensitive variants.

- For a new image without references, omit both reference-selection mechanisms.
- Before using a local image as an edit target or reference, inspect it with
  `view_image`.
- When every required input is a local file, pass the smallest complete set through
  `referenced_image_paths`.
- When required inputs exist only as recent conversation images, use
  `num_last_images_to_include` with the smallest value that includes them all.
- Never provide both `referenced_image_paths` and `num_last_images_to_include`.
- Built-in imagegen has no separate `character_consistency` or `multi_image_fusion`
  operation. Express identity preservation, reference roles, and compositing intent in
  the prompt and provide the relevant images together.
- Do not assume an output-path or model argument exists. Generate first, then move or
  copy the selected result from Codex's generated-images location into the project.
- Preserve edits non-destructively with a versioned sibling filename unless replacement
  was explicitly requested.

For a derived storyboard frame, state invariants explicitly, for example:

```text
Keep the character identity, wardrobe, location geometry, prop design, lighting
direction, palette, and aspect ratio unchanged. Change only the subject position and
camera framing described below.
```

## Nano Banana MCP

When Nano Banana is the selected provider, inspect the live MCP schema before calling
it. Map the shared intent to the available operations, commonly:

- `generate_image` for new images;
- `edit_image` for targeted changes;
- `character_consistency` for identity-led variants;
- `multi_image_fusion` for combining references.

Use the live parameter names. `referenceImagePaths`, `output_path`, and a particular
Gemini model ID are Nano-specific implementation details, not project-manifest fields.
If the manifest pins a model, require that exact model; otherwise choose from the live
schema and record the resolved model. Keep output paths within the MCP's allowed local
output area, then copy the accepted asset into the project if necessary.

## Prompt rules

- Prefer concrete visual constraints over decorative synonyms.
- Quote exact text and specify placement and typographic character.
- For counts, grids, rows, layouts, labels, or room sizes, state them numerically and
  spatially.
- End continuity plates and storyboard prompts with the project's artefact constraints,
  normally `no text, no watermarks, no logos, no labels, no annotations`.
- For recurring characters, keep one canonical identity reference and reuse it. A prior
  in-style storyboard frame may supplement, but should not silently replace, the
  canonical reference.
- For additional location or prop variants, explicitly bind geometry and identity to
  the primary reference and describe only the changed angle, condition, or state.

## Deliverables

For every generated project asset, record:

- provider and resolved model when exposed;
- final project-local path;
- prompt or prompt hash;
- reference paths and their roles;
- whether the operation was generation or edit;
- any unresolved continuity warning.

If only a prompt was requested, return the prompt and reference-role mapping without
calling an image provider.
