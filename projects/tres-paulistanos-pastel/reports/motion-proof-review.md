# Motion proof technical review

## Ready for human approval

### MiniMax H3 — Claire dialogue

- Artifact: `generated/minimax-proof/6ceca4f4_000.mp4`
- Job: `6ceca4f4-5fb9-4efe-82ff-6d0cfac760ce`
- Probe: H.264 608 × 352, 24 fps, 3.041667 s; AAC stereo, 32 kHz, 3.042 s.
- Visual spot check: Claire, navy overshirt, rust/coral top, canvas bag, pastel and the
  Bixiga fair remain recognizable at midpoint. No generated text is visible.
- Human listening gate: verify the exact phrase `Eu gosto quando o pastel chega tão
  quente.`, Brazilian Portuguese pronunciation, São Paulo cadence, lip sync and absence
  of added words or music. Technical inspection proves an audio stream, not its wording.

### LTX-2.3 — pastel insert

- Artifact: `generated/ltx-proof/0a1e0d61_000.mp4`
- Job: `0a1e0d61-0838-495a-8a58-a726e91ad337`
- Probe: H.264 640 × 320, 24 fps, 2.041667 s; AAC stereo, 48 kHz, 2.01 s.
- Visual spot check: stall, skimmer, sleeve and hand remain legible. At midpoint the
  pastry bends slightly while lifting; decide whether this reads as acceptable soft
  pastry motion or as a geometry defect.
- Human listening gate: confirm only non-intelligible fair ambience/SFX and no speech or
  music.

## Diagnostics retained

- MiniMax job `651545c6-bd4f-4318-b09c-17552296456e` proved that the outer resolution
  selector overrides inner dimensions; its 640 × 640 take is diagnostic only.
- LTX jobs `8a77fddd-a885-4883-9f55-b7045579b9cd` and
  `f636d3db-9e5f-4bb4-9e1f-3367c9fa26c4` exposed hidden duration/FPS/audio-batch
  coupling. The fixed graph uses duration 2, FPS 24, 49 frames and audio batch 1.
- LTX's live decoder snapped requested 640 × 360 to 640 × 320. Use that truthful draft
  raster and conform accepted clips to 16:9 in editorial.

No further character/dialogue clips should run until the two proof artifacts are
reviewed.
