# MiniMax H3 Turbo custom-node audit

Audited `/home/miranda/ComfyUI/custom_nodes/ComfyUI-MiniMax-H3-Turbo` and its bundled
`example_workflows/minimax_h3_t2v_turbo.json` on 2026-08-31.

## Correct graph wiring

The installed pack exposes two nodes:

1. `UNETLoader.MODEL → MiniMaxH3TurboLoRA.MODEL`
2. `MiniMaxH3TurboLoRA.MODEL` feeds both `BasicGuider` and `ModelSamplingAV`
   (or the equivalent model-conditioning branch used by the official H3 graph).
3. `MiniMaxH3TurboSampler.SAMPLER → SamplerCustomAdvanced.sampler`
4. `BasicScheduler` feeds `SamplerCustomAdvanced.sigmas`.
5. `SamplerCustomAdvanced` output goes to both `VAEDecode` and `VAEDecodeAudio`,
   then `CreateVideo`/`SaveVideo`.

The bundled example uses node ids 134 (`MiniMaxH3TurboLoRA`) and 135
(`MiniMaxH3TurboSampler`), with links 249→134, 134→126/124, and 135→125.

## Settings learned from the node source and README

- Turbo LoRA checkpoint: `minimax_h3_turbo_v4_step600_ema.safetensors` is the
  preferred current checkpoint if installed; the older
  `minimax_h3_fl2v_turbo_4step_v1.0_768p_comfyui_bf16.safetensors` remains a valid
  fallback for this project.
- LoRA strength: `1.0` by default. The custom node applies the update in activation
  space (`low_vram=false`, bypass mode), preserving quality on quantized/pruned bases.
- `low_vram=true` merges the LoRA and is softer on int8/fp8/pruned weights; use only
  after an OOM.
- `MiniMaxH3TurboSampler` is not a generic sampler replacement. It is required for
  older ComfyUI audio/video dual schedules and is safe on recent ComfyUI, where it
  detects native `ModelSamplingAV` and falls back to a single-schedule Euler step.
- `BasicScheduler.scheduler = simple`; useful Turbo range is 4–8 steps. Four is the
  minimum, six is a good quality/speed compromise, and more than eight is discouraged.
- H3 output is jointly generated video plus synchronized stereo audio; keep the audio
  VAE branch and do not treat the graph as silent picture generation.
- Resolution is a multiple of 32, normally a 768-pixel short edge. Frame length is
  `17*k + 5` at 24 fps (for example 73 frames ≈ 3 seconds, 124 ≈ 5 seconds).

## Finding for this project

`workflows/minimax-h3-i2v-fast-v1.json` contains `MiniMaxH3ImageToVideo`,
`LoraLoaderModelOnly`, `SamplerCustomAdvanced`, and stock scheduler nodes, but no
`MiniMaxH3TurboLoRA` or `MiniMaxH3TurboSampler` node classes. Its prior proof therefore
validated MiniMax H3 I2V behavior, not the installed custom-node Turbo path.

Before claiming a MiniMax Turbo render, create a fresh I2V graph from the official H3
workflow, preserve the first-frame input and audio VAE, insert the two custom nodes as
above, set `simple` + 4–8 steps, and validate that the submitted API graph contains
both custom node types. Runtime logs should include the custom pack’s canaries:
`[MiniMaxH3TurboLoRA]` and `[H3TURBO sampler]`.
