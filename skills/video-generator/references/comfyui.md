# ComfyUI Video Execution

Read this reference only for `provider=comfyui`. It defines the portable local execution
contract; always rediscover the active instance instead of assuming one machine's model
paths or workflow IDs.

## Discovery

Prefer an installed `comfy` CLI or Comfy MCP. Otherwise use the local HTTP/WebSocket
API. Determine the workspace and base URL from user configuration, the running process,
or the service definition. Keep local backends bound to loopback unless the user has
configured an authenticated remote endpoint.

Before running a heavy graph, record:

- ComfyUI version and reachable base URL;
- device and available VRAM from `/system_stats`;
- required node classes from `/object_info`;
- installed checkpoint, diffusion-model, text-encoder, VAE, LoRA, and upscaler files;
- workflow path, format, version/hash, expected inputs, and output nodes;
- whether any node is a remote partner/API node.

Local model nodes and Comfy partner/API nodes are different providers. Partner nodes may
spend credits and require authentication. Do not run them without explicit approval;
when using the CLI, that means never adding `--allow-spend` implicitly.

## Supported runner surfaces

### Comfy CLI

The current CLI accepts both API-format graphs and frontend/UI workflow JSON. For UI
workflows it queries `/object_info`, converts the graph client-side, and can print the
exact API graph before submission.

Use the equivalent of:

```text
comfy --workspace <comfy-root> --where local run \
  --workflow <workflow.json> --print-prompt --host <host> --port <port>

comfy --workspace <comfy-root> --where local run \
  --workflow <workflow.json> --json --no-watch --host <host> --port <port>

comfy --workspace <comfy-root> --where local jobs wait <prompt_id>
```

Treat command spelling as discoverable: inspect `comfy --help`, `comfy run --help`, and
`comfy jobs --help` before automation. Parse structured JSON/NDJSON rather than terminal
presentation. `--print-prompt` is the preferred non-generation validation step.

The workflow must already contain the shot-specific prompt, media paths, seed, timing,
and output prefix, or be produced by a deterministic workflow renderer. Do not edit an
opaque workflow with ad-hoc text substitution.

### Comfy MCP

When a Comfy MCP is connected, inspect its tool schema. Common logical operations are:

- `validate_workflow` with a workflow path;
- `run_workflow` returning a `prompt_id`;
- `job` with a wait/status action.

Use the live names and fields. Submit with non-blocking execution when the caller owns
the wait loop, and persist `prompt_id` before waiting.

### Direct API fallback

Use the direct API only when the CLI/MCP is unavailable or the integration already
owns the API lifecycle:

1. Convert or export the workflow to API format: node IDs mapped to `class_type` and
   `inputs`.
2. `POST /prompt` with `{ "prompt": <graph>, "client_id": <stable-id> }`.
3. Persist the returned `prompt_id` immediately.
4. Monitor `/ws` or poll `/history/{prompt_id}` with bounded backoff.
5. Read output file metadata from history.
6. Retrieve non-local output through `/view`; for a same-machine instance, resolve the
   validated output path beneath the configured Comfy output directory.

Never send frontend `nodes`/`links` JSON directly to `/prompt`.

## Workflow contract

Store source workflows under a versioned project path and record a SHA-256 hash for
each submitted graph. A shot's binding map should name semantic fields instead of
depending on undocumented node positions:

```yaml
profile: minimax-h3-i2v
workflow: workflows/minimax-h3-i2v-v1.json
bindings:
  prompt: <node-id>.prompt
  first_frame: <node-id>.image
  last_frame: <node-id>.image
  width: <node-id>.width
  height: <node-id>.height
  length: <node-id>.length
  seed: <node-id>.seed
  output_prefix: <node-id>.filename_prefix
```

Validate that the node IDs and input names still exist after conversion. Prefer stable
named subgraphs or an explicit renderer when workflow revisions regularly change IDs.

## Known native model patterns

Discover these nodes and their exact live inputs before use:

- **MiniMax H3 text/image-to-video:** text-only or optional first/last frames depending
  on the node; video length follows the model's valid frame grid rather than arbitrary
  seconds.
- **MiniMax H3 reference-to-video:** may accept image, video, and audio reference lists,
  with prompt tags such as `<Picture 1>`, `<Video 1>`, and `<Audio 1>`. Keep list order
  aligned with tag numbers.
- **LTX-2.3:** separate text-to-video, image-to-video, and first/last-frame workflow
  profiles. Validate guide frames, audio/video latent handling, and any distilled LoRA
  against the installed graph.
- **Still-image workflows such as Z-Image:** these are valid upstream frame providers,
  but Codex built-in `image_gen` remains the default image provider unless the user or
  project selects ComfyUI for stills.

Do not import Higgsfield duration labels or media-handle assumptions into these graphs.
For each workflow, calculate submitted frame count from its actual rules and record any
rounding. H3 commonly uses a frame grid shaped like `17k + 5`; verify this from the live
node implementation before relying on it.

## Media inputs and outputs

- Inspect all local input images before generation and verify that workflow load nodes
  resolve them from an allowed Comfy input path.
- Copy or upload inputs through the supported surface; do not rewrite arbitrary paths
  into load nodes if the server cannot access them.
- Require a `SaveVideo` or equivalent output node with a shot/take-specific prefix.
- Resolve every history output by `prompt_id`. Do not select a file merely because it
  is the newest item in the output directory.
- Copy accepted outputs into `generated/{shot_id}/v{take}.mp4` or the corresponding
  sub-clip path. Preserve the original Comfy locator in the log.
- Probe video and audio streams with `ffprobe`; native audio presence is an observed
  output property, not an assumption from the workflow name.

## Resume and failure handling

- If `prompt_id` is queued or running, wait instead of resubmitting.
- If history shows completion, resolve and verify its recorded outputs before rerunning.
- If execution failed, record the node error and graph hash. Create a new take only
  after correcting the workflow or input.
- Use a unique output prefix for every shot and take.
- Cancellation or queue deletion must target the exact prompt ID and be explicitly
  requested or necessary to stop a known bad active job.

## Evidence from the compatibility review

The reviewed local installation already demonstrated the needed architecture: native
MiniMax H3 and LTX-2.3 workflow families, local `SaveVideo` outputs with H.264/AAC
streams, an operational prompt queue, and batch runners that persist prompt IDs and
manifests. This proves feasibility, but it is not a substitute for live discovery when
the skill runs on a later version or another machine.
