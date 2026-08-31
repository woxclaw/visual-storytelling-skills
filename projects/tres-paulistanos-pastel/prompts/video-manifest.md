# Video manifest — dual-route fast draft

All spoken words are Brazilian Portuguese. MiniMax carries dialogue; LTX carries only
ambient sound and non-dialogue inserts. Both routes use the approved Codex-generated
stills as first-frame references.

## Model routing

| Shots | Route | Draft raster | FPS | Frame rule | Audio |
|---|---|---:|---:|---|---|
| S01_SH002, S02_SH002, S03_SH002 | MiniMax H3 FL2VA Turbo 4-step | 608 × 352 | 24 | `17k + 5`; proof 73 frames | Native PT-BR speech + ambience |
| S00_SH001–002, S01_SH001/003, S02_SH001/003, S03_SH001/003, S04_SH001 | LTX-2.3 I2V | 640 × 320 engine-aligned | 24 | `8k + 1`; proof 49 frames | Ambience/SFX only |
| S04_SH002 disclosure | Editorial | 1920 × 1080 | 24 | 4 s | City ambience fades |

The draft rasters favor iteration speed. LTX snapped the requested 640 × 360 decode to
640 × 320 on its latent grid; accepted inserts are center-cropped/conformed to the
1920 × 1080 timeline. Text and disclosure are added in post, never generated into the
image.

## MiniMax proof — S01_SH002 Claire

- First frame: `shots/S01_SH002/start.png`
- Duration: 73 frames / about 3.04 s
- Seed: `280828`
- Exact spoken line: `Eu gosto quando o pastel chega tão quente.`
- Review: identity, fair skin tone, coral blouse, pastry integrity, Portuguese accent,
  consonant timing, hands, background drift and absence of extra speech/music.

Prompt:

```text
Over three seconds, Claire, a fictional 28-year-old paulistana woman with fair skin and
mixed French and German ancestry, stands beside a pastel stall in Bixiga and gives a
short street testimony directly to the nearby camera. She keeps the same face, hair,
coral blouse, posture, and background as the supplied first frame. She blinks once,
shifts her weight slightly, lifts the pastel a few centimeters, then speaks naturally in
Brazilian Portuguese with a São Paulo accent, exact audible dialogue: “Eu gosto quando
o pastel chega tão quente.” Her lips match the Portuguese words. One continuous take,
eye-level handheld smartphone with very small physically motivated drift, no zoom, no
cut. Daylight filtered through the fair canopy, stainless-steel stall, ordinary feira
movement softened behind her. Natural skin texture, candid social realism, minimally
produced phone video. Native ambient feira sounds and faint fryer sizzle under clear
close speech. No narration, no subtitles, no on-screen text, no background music, no
extra spoken words.
```

## LTX proof — S00_SH002 pastel insert

- First frame: `refs/style/style-anchor-01.png`
- Duration: 49 frames / about 2.04 s
- Seed: `300830`
- Review: pastry geometry, hands, paper sleeve, oil motion, camera restraint, absence of
  intelligible speech/text/music.

Prompt:

```text
Over two seconds in one continuous close-up, the skimmer lowers the freshly fried
rectangular pastel a few centimeters into the white paper sleeve, pauses as one last
drop of oil falls, and the receiving hand steadies the sleeve. Preserve the exact hands,
pastry shape, stainless-steel stall, tents, daylight, and composition from the supplied
first frame. The camera is a nearby handheld smartphone with only slight natural grip
drift; no zoom and no cut. Warm daylight, crisp pastry bubbles, restrained documentary
social realism, minimally produced phone footage. Audio is only the feira ambience:
gentle fryer sizzle, paper rustle, and distant indistinct crowd murmur. No intelligible
speech, no narration, no subtitles, no on-screen text, no music.
```

## Expansion after approval

Split each full testimony into short MiniMax clauses, then cover sentence joins with LTX
inserts. This keeps front-facing lip sync brief while preserving each approved line in
full. Never translate or paraphrase the Portuguese dialogue during generation.
