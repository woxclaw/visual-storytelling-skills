# Reproducible ComfyUI workflows

Validated against local ComfyUI `0.33.0` at `127.0.0.1:8189` on an RTX 4090.
No graph contains partner/API nodes or spends credits.

## MiniMax H3

- File: `minimax-h3-i2v-fast-v1.json`
- SHA-256: `da0d5783e94be09e34d548bb2c6e7b8a86efda1480ae30c917975c1db480cfa3`
- Model: `minimax_h3_fl2va_pruned_fp8_scaled.safetensors`
- LoRA: `minimax_h3_fl2v_turbo_4step_v1.0_768p_comfyui_bf16.safetensors`
- Semantic inputs: first frame `114.image`; draft raster selector
  `115.{aspect_ratio,megapixels,multiple}`; prompt/dimensions/length
  `105/104.{prompt,width,height,length}`; seed `105/15.noise_seed`; fps `105/91.fps`;
  output `92.filename_prefix` and `92.format`.
- Proof binding: `start.png`, 608 × 352, 73 frames, 24 fps, seed 280828.
- Validation: passes; two unreachable utility-node warnings are harmless and pruned.

## LTX-2.3

- File: `ltx-2.3-i2v-fast-v2.json`
- SHA-256: `d1b60deae806e936f00a130b68ad8fc0237b82f00c98fb359a7ca03d9e7550e9`
- Checkpoint: `ltx-2.3-22b-dev-fp8.safetensors`
- LoRA: `ltx-2.3-22b-distilled-lora-384.safetensors`
- Semantic inputs: first frame `269.image`; prompt/seed `320.value` and
  `320.noise_seed`; delivery width/height `320/312.value` and `320/299.value`;
  duration/fps `320/301.value` and `320/300.value`; output
  `75.filename_prefix` and `75.format`.
- Audio latent batch `320/305.batch_size` must remain `1`; it is not the FPS field.
- Compatibility binding: the gallery's unavailable newer distilled LoRA is rebound to
  the installed distilled LoRA. Its unavailable Gemma prompt-enhancer LoRA is set to
  zero strength and the enhancer switch remains false, so the authored prompt is used.
- Proof binding: `style-anchor-01.png`, fast first-pass latent and 640 × 320 verified
  engine-aligned output, 49 frames, 24 fps, seed 300830. The UI request was 640 × 360;
  the live decode snapped height to 320.
- Validation: passes with one benign integer-to-number edge warning on audio frame rate.

`ltx-2.3-i2v-fast-v1.json` is retained only as the original local blueprint snapshot; it
has no output node and is not the production graph.

### LTX production

- Production baseline: `ltx-2.3-i2v-fast-v3.json`
- SHA-256: `a863d7db907fb32cb28d9f11588a866c84d09a3bf48e828f6cd9561a187623d9`
- Per-shot immutable bindings live under `production/ltx/`.
- Native spoken-dialogue tests are retained as diagnostics only: they generated
  pseudo-subtitles. Accepted-picture candidates use quiet portrait behavior and receive
  the reviewed PT-BR stem during editorial assembly.

## Wan 2.2 TI2V 5B

- File: `wan-2.2-5b-ti2v-fast-v1.json`
- SHA-256: `c35a405596da5dad79ca017bbb58991567bad7e784d3ef2fa89cdcb616911386`
- Diffusion model: `wan2.2_ti2v_5B_fp16.safetensors`
- Text encoder: `umt5_xxl_fp8_e4m3fn_scaled.safetensors`
- VAE: `wan2.2_vae.safetensors`
- Semantic inputs: first frame `56.image`; latent width/height/length
  `55.{width,height,length}`; positive/negative prompts `6.text` and `7.text`;
  seed/steps/CFG `3.{seed,steps,cfg}`; fps `57.fps`; output
  `58.filename_prefix`, `58.format`, and `58.format.codec`.
- Fast draft binding: 640 × 352, 24 fps, 49 frames for 2 s, 97 for 4 s, and
  193 for 8 s; 20 steps, CFG 5, UniPC/simple.
- Per-shot immutable bindings live under `production/wan/`.
- Wan is a silent picture route. Portuguese speech is bound in post from the same
  reviewed local stem as the LTX comparison cut.
