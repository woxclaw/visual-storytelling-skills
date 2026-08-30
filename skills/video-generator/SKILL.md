---
name: video-generator
description: >
  Execute resumable video-generation jobs from shot-specifier manifests through either
  local ComfyUI workflows or the Higgsfield MCP. Use when validating provider
  capabilities, binding prompts and reference media, submitting and monitoring jobs,
  retrieving local clips, handling retakes, recording provenance, or writing final
  assembly order.
---

# Video Generator

Turn a completed `shot-specifier` handoff into reviewed local video files. This skill
owns the operational gap between “prompt written” and “clip exists on disk”: provider
discovery, input binding, submission, monitoring, output retrieval, verification,
retakes, resume state, and assembly order.

Do not generate missing storyboard images here. Return incomplete image work to
`nanobanana` and incomplete scene or shot planning to the upstream skill that owns it.

## Provider selection

Use the provider named by the manifest or explicitly chosen by the user:

- `comfyui`: local or authenticated ComfyUI workflow execution;
- `higgsfield`: Higgsfield MCP generation.

If neither is named, prefer a reachable local ComfyUI instance whose installed nodes,
models, and workflows satisfy the shot. Otherwise ask for a provider decision. Never
silently switch providers after jobs have started, and never route a local workflow
through paid Comfy partner nodes without explicit approval.

Provider choice is per shot. A project may mix providers, but every log row must retain
the actual provider, model, workflow, and job identifier.

## Read before running

- For ComfyUI discovery, workflow validation, queueing, output retrieval, and the known
  MiniMax H3/LTX patterns, read [references/comfyui.md](./references/comfyui.md).
- For Higgsfield uploads and state, read
  [references/media-upload-and-state.md](./references/media-upload-and-state.md).
- For Higgsfield live-schema constraints, read
  [references/model-routing.md](./references/model-routing.md).
- For model-native prompt reconstruction, read
  [references/prompt-flattening.md](./references/prompt-flattening.md).
- For key-frame subdivision, read
  [references/key-frame-decomposition.md](./references/key-frame-decomposition.md).
- Load `seedance-2-deep-dive` or `kling-3-0-deep-dive` only for matching Higgsfield
  routes.

## Required inputs

The handoff must contain `prompts/manifest.md`, one prompt file per shot, and all local
files named by each row. Each row needs:

| Field | Meaning |
| --- | --- |
| `provider` | `comfyui` or `higgsfield` |
| `model` | Provider model alias or exact resolved model |
| `strategy` | `text_to_video`, `image_to_video`, `start_end_image`, `reference_to_video`, `multi_shot`, or `motion_control` |
| `duration` | Desired duration; validate against the selected route |
| `fps` | Desired or route-native frame rate |
| `aspect` | Desired display aspect ratio |
| `target_resolution` | Intended pixel dimensions |
| `prompt_file` | Project-relative prompt file |
| `required_refs` | Continuity-critical inputs and their semantic roles |
| `audio` | Generated, supplied, none, and mute intent |
| `review_gate` | `required` or `optional` |
| `count` | Number of requested takes, normally `1` |

For ComfyUI, also require a versioned `workflow` path or a resolvable workflow profile
plus its input-binding map. For Higgsfield, require the resolution hint and model
overrides used by its live schema.

Start and end frames are conditional inputs, not universal requirements. Require only
the media roles needed by the selected strategy and verified provider capability.

## Capability check

Before the first job for each provider:

1. Prove the provider is reachable and record its version or live schema.
2. Discover installed models, nodes, accepted media roles, duration/frame constraints,
   resolution rules, audio behavior, concurrency, cancellation, and output retrieval.
3. Resolve each manifest alias to an actual model and workflow. Do not invent IDs or
   assume a filename proves that a workflow can execute.
4. Validate the exact workflow graph or MCP request without spending credits or running
   the heavy model when a validation/dry-run surface exists.
5. Record the observed contract in `generated/provider_schema_notes.md`.

Stop when a required identity, anchor, duration, audio, or recovery capability is
missing. A provider-specific default may be accepted only when it is non-critical and
the exception is recorded.

## Workflow

1. **Audit the manifest.** Resolve all paths from the project root. Confirm each prompt,
   required reference, workflow, and supplied audio file exists.
2. **Resolve the execution plan.** Group jobs by provider and model/workflow. Record the
   submission order and concurrency limit before a large run.
3. **Validate prompts.** Use each prompt file's `## Generation Prompt`. Reconstruct it
   only when missing and a supported model-specific rule exists.
4. **Validate continuity inputs.** Compare `required_refs` with the continuity inventory
   and storyboard consistency report. Each required item must be passed to the provider,
   baked into a required frame, or recorded as a blocker.
5. **Bind media by role.** Map semantic roles such as `first_frame`, `last_frame`,
   `identity_reference`, `style_reference`, `reference_video`, and `supplied_audio` to
   the chosen provider's actual fields or workflow nodes.
6. **Apply timing.** Convert duration to the route's valid frame count using the verified
   FPS and frame-grid rules. Record desired and submitted values when rounding occurs.
7. **Apply audio intent.** Distinguish generated ambience, generated dialogue, supplied
   audio, deliberate silence, and downstream mute intent. Stop if forced generated audio
   conflicts with dialogue, lip-sync, music timing, narration, supplied audio, or a
   deliberate silent beat.
8. **Submit one recoverable job.** Capture the returned job ID or Comfy `prompt_id`
   immediately, then append the generation-log row before submitting more jobs.
9. **Monitor to a terminal state.** Use provider events or bounded polling. A long queue
   is not failure by itself. Do not resubmit while a recoverable job remains active.
10. **Retrieve the local output.** For local ComfyUI, resolve the `SaveVideo` history
    output and copy it into the project when it is outside the story project. For remote
    providers, download the result. Never leave a selected project asset only at a
    provider URL or outside the project.
11. **Probe the file.** Use `ffprobe` to record codecs, duration, FPS, frame count,
    dimensions, audio streams, and file size. Treat differences from labels as review
    data; fail missing, empty, corrupt, or materially truncated output.
12. **Run the review gate.** Mark `accepted`, `retake`, or `blocked`. Required gates must
    not advance automatically without a recorded review decision.
13. **Resume safely.** Reuse completed local files and poll existing active job IDs.
    Retakes get a new take number and never overwrite earlier output.
14. **Write assembly order.** Use project-relative selected clip paths and preserve
    sub-clip order, boundary intent, transitions, and mute intent.
15. **Package when requested.** Once all required clips are accepted, hand the local
    generation log and assembly order to `media-project`.

## Provider execution

### ComfyUI

Prefer the installed `comfy` CLI or an exposed Comfy MCP because they support workflow
validation and structured job state. The normal lifecycle is:

```text
validate workflow -> submit UI/API workflow -> prompt_id -> wait/history ->
resolve SaveVideo output -> copy to project -> ffprobe -> log
```

The CLI may convert frontend workflow JSON to API format. Direct `POST /prompt` requires
an API-format graph. Query `/object_info` before binding nodes and use `/history` plus
`/view` for output metadata and retrieval. See `references/comfyui.md` for the complete
contract and spend boundary.

### Higgsfield

Inspect the connected MCP schema, upload or register local media, map the manifest to
the live `generate_video` equivalent, submit, poll, and download. Preserve the existing
Higgsfield model-specific references and never drop required images to fit a narrower
route. Use the Higgsfield references linked above for exact rules.

## Generation log

Use [templates/generation-log.md](./templates/generation-log.md). Append a row as soon
as submission returns an identifier. At minimum record:

- shot, sub-clip, take, provider, model, workflow and workflow hash/version;
- job ID or `prompt_id`, status, prompt hash, seed, desired and submitted timing;
- semantic reference roles and source paths;
- provider output locator and project-local file;
- file size, actual dimensions, FPS, frame count, codecs, and audio presence;
- review state, retry lineage, continuity flags, and notes.

An unlogged job is a lost job. A completed remote job without a verified local file is
not a completed shot.

## Reference discipline

Reference priority is strategy-specific:

- first/last anchors when the route and shot design require them;
- principal character identity;
- active hero prop and recurring visual elements;
- location and style;
- motion video or supplied audio when the strategy depends on it.

Do not force anchors onto text-to-video workflows. Do not omit required anchors from an
image-to-video or start/end workflow. When a route has a reference limit, stop if the
limit excludes a continuity-critical item that is not already baked into a supplied
frame.

## Stop conditions

Stop before generation when:

- the chosen provider is unavailable, unauthenticated where required, or cannot expose
  a schema/capability surface;
- the model, workflow, or required node/model file is unavailable;
- the workflow cannot be validated or contains unresolved inputs;
- a required reference, frame, motion input, or audio input cannot be bound;
- timing, frame count, aspect, or dimensions cannot be mapped safely;
- paid partner/API nodes would run without explicit approval;
- generated-audio behavior conflicts with the production audio plan;
- job state cannot be logged, monitored, cancelled where necessary, or resumed;
- a successful output cannot be copied into the project and verified.

Do not silently fall back to a different provider, model family, reduced reference set,
or text-only strategy. Return the exact capability mismatch for a production decision.
