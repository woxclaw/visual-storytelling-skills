# Review candidates — Wan v1 / LTX v1

Generated locally on ComfyUI 0.33.0, RTX 4090, 640-class fast draft settings.

## LTX v1 picture candidates

All listed files are H.264 + stereo AAC and were checked with ffprobe. Portrait,
geography, insert and closing candidates use positive-only prompts after an earlier
text-trigger diagnostic. The clean Claire portrait is the reference behavior for the
dialogue shots.

- generated/ltx-v1/shots/S00_SH001/fbe719df_000.mp4
- generated/ltx-v1/shots/S01_SH001-clean/5b062b34_000.mp4
- generated/ltx-v1/shots/S01_SH002-portrait/2a69c78c_000.mp4
- generated/ltx-v1/shots/S01_SH003-clean/10422e8f_000.mp4
- generated/ltx-v1/shots/S02_SH001-clean/d851d17d_000.mp4
- generated/ltx-v1/shots/S02_SH002-clean/4c35fdbd_000.mp4
- generated/ltx-v1/shots/S02_SH003-clean/6251fe3b_000.mp4
- generated/ltx-v1/shots/S03_SH001-clean/6b569b33_000.mp4
- generated/ltx-v1/shots/S03_SH002-clean/2f0ef5b4_000.mp4
- generated/ltx-v1/shots/S03_SH003-clean/9c674093_000.mp4
- generated/ltx-v1/shots/S04_SH002-clean/132461cd_000.mp4

## Wan v1 picture candidates

All listed files are silent H.264, 640 × 352 at 24 fps. Wan is intentionally bound
to the shared Portuguese stems during editorial assembly.

- generated/wan-v1/shots/S00_SH002/df0231d5_000.mp4
- generated/wan-v1/shots/S01_SH001/c662a427_000.mp4
- generated/wan-v1/shots/S01_SH002/50da49bb_000.mp4
- generated/wan-v1/shots/S01_SH003/b9b0cf8d_000.mp4
- generated/wan-v1/shots/S02_SH001/9493aa16_000.mp4
- generated/wan-v1/shots/S02_SH002/5095e0ef_000.mp4
- generated/wan-v1/shots/S02_SH003/481eec39_000.mp4
- generated/wan-v1/shots/S03_SH001/b759ab83_000.mp4
- generated/wan-v1/shots/S03_SH002/85e58244_000.mp4
- generated/wan-v1/shots/S03_SH003/3bfbda03_000.mp4
- generated/wan-v1/shots/S04_SH002/61ad85e7_000.mp4

## Portuguese audio stems

- generated/audio-stems/S01_SH002/675557c3_000.mp4 — Whisper first pass:
  “Eu gosto quando o pastel chega tão quente que você precisa esperar, e não espera.
  A primeira mordida faz aquela bagunça no papel. Pra mim, feira isso.” The final
  “é” is missing; do not accept without a pickup.
- generated/audio-stems/S02_SH002/6b0f0128_000.mp4 — first pass has multiple
  substitutions after “dois minutos de sombra”; reject pending a clause pickup.
- generated/audio-stems/S03_SH002/039f89ea_000.1.mp4 — first pass is close but adds
  an initial “a”: “A Paulista inventa a moda pra tudo…”; reject pending pickup.

These are review candidates, not final accepts. Editorial captions and the exact
fiction/AI disclosure are still to be added after human approval.

## Assembled review movies

- `generated/review-cuts/tres-paulistanos-pastel-ltx-v1-review.mp4` — 42.52 s, H.264 + stereo AAC.
- `generated/review-cuts/tres-paulistanos-pastel-wan-v1-picture-review.mp4` — 42.50 s, silent picture-only cut.
- `generated/review-cuts/tres-paulistanos-pastel-wan-v1-dialogue-review.mp4` — 42.50 s, Wan picture with provisional concatenated Portuguese stems.

These are review cuts, not publish-ready masters: the Portuguese stems still need exact-word pickups, and the Wan dialogue is a provisional editorial alignment.

## MiniMax H3 Turbo proof

- `generated/minimax-h3-turbo-proof/394a7237_000.mp4` — 5.17 s, 864 × 480, H.264 + stereo AAC.
- `workflows/minimax-h3-turbo-t2v-proof.json` — verified custom `MiniMaxH3TurboLoRA` and `MiniMaxH3TurboSampler` path, 8 steps, `simple` scheduler, runtime-confirmed in the ComfyUI journal.

## MiniMax H3 Turbo movie

- `generated/review-cuts/tres-paulistanos-pastel-minimax-h3-turbo-review.mp4` — 15.53 s, 864 × 480, H.264 + stereo AAC; concatenates the three full Turbo testimonial clips.
- Source clips: `generated/minimax-h3-turbo-full/e135448e_000.mp4` (Claire/Bixiga), `45beab1f_000.mp4` (Luciana/Praça da Sé), and `c92ce0d4_000.mp4` (Rafael/MASP).

The H3 Turbo movie is a model-rendered review cut with native generated ambience/audio. It is separate from the editorial Portuguese-stem mux; exact spoken-word Portuguese still requires the approved pickup stems.
