# Production plan

## Gate 1 — current approval package

Deliver and approve:

- concept and disclosure posture;
- three fictional character identities and wardrobes;
- exact Brazilian Portuguese dialogue;
- one visual style anchor;
- three location references;
- one testimonial start frame per character;
- shot list, continuity rules and audio strategy.

No heavy video job runs in this gate.

## Gate 2 — voice and motion proof

After approval:

1. Record or synthesize three short Brazilian Portuguese voice tests.
2. Validate local ComfyUI reachability, installed nodes/models and workflow schemas.
3. Run one 4–5 second proof for `S01_SH002` using the approved start frame.
4. Test two approaches:
   - supplied Portuguese audio driving/matched to the picture, if the workflow supports it;
   - native audiovisual generation with exact Portuguese dialogue, only if the workflow
     demonstrates reliable Brazilian Portuguese and lip sync.
5. Review identity, mouth motion, voice, accent, hands, food state and background drift.

Gate 2 stops after one accepted proof. It does not launch all shots automatically.

## Gate 3 — clip production

- Generate one recoverable take per shot, with unique prompt IDs and no overwrite.
- Prefer local ComfyUI image-to-video using the approved Codex stills as anchors.
- Use native workflow timing and calculate valid frame counts; do not force arbitrary
  durations or 24 fps.
- Keep dialogue, ambient, SFX, music and narration as separate explicit fields.
- If generated dialogue is unreliable, generate visuals with speech-safe framing and
  use approved supplied Brazilian Portuguese audio in editorial.
- Probe every output with `ffprobe` and record resolution, fps, duration and audio.
- Require human acceptance for every character/dialogue clip.

## Gate 4 — edit and delivery

- Assemble only accepted takes.
- Cut dialogue across medium shots and inserts according to `script.md`.
- Add location atmospheres and restrained sync effects; no non-diegetic music.
- Mix dialogue for intelligibility without erasing the city.
- Add Portuguese captions and the disclosure card in post, never with image generation.
- Export 1920 × 1080 master plus an optional 1080 × 1920 reframed cutdown.

## Current provider decision

- **Stills/references:** Codex built-in imagegen, locked.
- **Dialogue video:** MiniMax H3 first-frame/image-to-video, local 4-step Turbo workflow,
  608 × 352 at 24 fps for fast proofs. Exact Brazilian Portuguese dialogue is embedded
  in the shot prompt; no narration or music.
- **Insert video:** LTX-2.3 image-to-video, local FP8 checkpoint with the installed
  distilled LoRA, engine-aligned 640 × 320 at 24 fps for fast proofs. Use for food, hands,
  location texture and other shots without intelligible speech.
- **Approval rule:** one MiniMax dialogue proof and one LTX insert proof may run now.
  Full clip production remains blocked until both are reviewed.
- **Z-Image Turbo:** explicitly excluded from still/reference generation.
