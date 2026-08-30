# Prompt Flattening

Video providers receive a plain prompt string or a workflow prompt input.
`shot-specifier` files are structured for humans and tools, so every prompt file needs a
provider/model-native `## Generation Prompt` section.

The source of truth for model-specific flattening is
`skills/shot-specifier/references/model-routing.md`. `video-generator` should normally
use the `## Generation Prompt` already written by `shot-specifier`. Reconstruct it only
when the section is missing and the prompt file has an approved model route.

## Fallback Algorithm

1. Extract the `## Prompt` block and the recommended model from `## Metadata`.
2. Parse top-level tags of the form `[TAG] content`, preserving multi-line content until
   the next tag.
3. Trim whitespace and remove the literal tag labels.
4. Apply the model/workflow-specific order from
   `shot-specifier/references/model-routing.md`.
   - Seedance 2.0: action/reference intent first; style and filmstock late.
   - Kling 3.0: shot/camera structure first; action physics next; style last.
5. Drop empty fields.
6. Append only statements supported by the chosen strategy:
   - first-frame statement when a first frame is bound;
   - last-frame statement when a last frame is bound;
   - reference-preservation statement when references are supplied;
   - `No narration.` unless narration is explicitly part of a verified workflow.
7. If a model exposes a prompt-length limit, trim only according to that model's
   priorities. Never trim core action, subject, scene constraints, frame anchors, or
   audio-generation prohibitions.

Stop if the model route is unknown. A generic flattened prompt is not acceptable for
production video generation.

## Prompt File Requirement

Write the flattened text back into each prompt file:

```markdown
## Generation Prompt

{plain text sent to the selected provider or workflow prompt input}
```

The `video-generator` skill must use this section for the actual provider call or Comfy
workflow binding. The structured `## Prompt` block remains the reviewable source.
