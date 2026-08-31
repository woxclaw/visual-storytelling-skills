---
name: social-realism-prompts
description: >
  Create or apply naturalistic candid social-media capture direction for synthetic
  images and videos: phone photos, selfies, vlogs, UGC-style scenes, and deliberately
  unpolished camera-roll aesthetics. Use for prompt-only work or Codex image generation
  and editing. Do not use for the social-realist art or cinema movement, documentary
  fact claims, or undisclosed impersonation of real people.
---

# Candid Social-Media Realism

Create plausible amateur-capture aesthetics without turning “realism” into a pile of
random defects. The goal is naturalistic synthetic media with a coherent camera,
lighting, environment, and moment—not concealment of synthetic origin.

## Choose the mode

Determine what the user actually requested:

- **Prompt only:** return a reusable image or video prompt. Do not call a generator.
- **Generate an image:** use Codex built-in `image_gen` through the installed `imagegen`
  skill.
- **Edit an image:** inspect the target and references, then use Codex built-in
  `image_gen` edit semantics through the installed `imagegen` skill.
- **Prepare a video shot:** write provider-aware capture/style direction and hand it to
  `shot-specifier` or `video-generator` when execution is requested. This skill does not
  invent a video-provider API.

If the request says “make this image look more natural,” treat it as an edit rather than
returning a prompt unless the user explicitly asked for prompt text only.

## Core principle: coherent imperfection

Natural-looking amateur media usually has a few causally related imperfections:

1. **Unscripted moment:** mid-sentence, between expressions, reacting to something, or
   completing an ordinary action.
2. **Capture device:** front/rear phone camera, focal behavior, autofocus, stabilization,
   exposure, and compression appropriate to that device and situation.
3. **Human texture:** plausible skin, hair, wardrobe, posture, and micro-expression
   without caricaturing bodies or adding arbitrary blemishes.
4. **Available light:** illumination that belongs to the location. It may be flattering
   or awkward; it need not always be “bad.”
5. **Casual framing:** a believable reason for the angle, crop, obstruction, or camera
   movement.
6. **Lived-in setting:** only the ordinary detail justified by the scene.
7. **Capture context:** camera roll, story, video call, informal vlog, UGC demonstration,
   or another clearly named context.

Choose the smallest set that explains the image. Do not require defects from a fixed
number of categories, and do not combine contradictions such as midday sun with
low-light shadow noise.

## Workflow

1. Establish modality, scene, subject, action, intended platform/context, orientation,
   and whether an input image is an edit target or reference.
2. Read the nearest preset only:
   - image: [references/image-presets.md](./references/image-presets.md)
   - video: [references/video-presets.md](./references/video-presets.md)
3. Add only physically compatible snippets when needed:
   - image: [references/realism-commands-image.md](./references/realism-commands-image.md)
   - video: [references/realism-commands-video.md](./references/realism-commands-video.md)
4. Adapt the preset's subject, wardrobe, setting, action, and capture physics. Presets
   are examples, not mandatory wording.
5. Check for contradictions, unsafe behavior, identity ambiguity, impossible mirror or
   phone geometry, and gratuitous degradation.
6. Execute or deliver according to the selected mode.

## Prompt structure

Use this order when it helps:

```text
[Capture context and device]
[Scene, subject, and ordinary action]
[Unscripted expression or timing]
[Wardrobe and human detail]
[Camera position, framing, and motion]
[Available light and exposure behavior]
[Environment details]
[Texture/compression cues justified by capture conditions]
[Constraints and disclosure-safe intent]
```

Keep prompts concise enough that action and identity remain dominant. One well-motivated
imperfection is better than several decorative flaws.

## Codex image generation and editing

For actual images, follow the installed `imagegen` skill. In particular:

- Use built-in `image_gen` by default.
- Inspect every local edit target and local reference with `view_image` before the call.
- When all inputs are local, pass the smallest complete set with
  `referenced_image_paths`.
- When required inputs exist only as recent conversation images, use the smallest
  sufficient `num_last_images_to_include` value.
- Never pass both reference mechanisms.
- Do not assume built-in imagegen accepts a model or destination-path argument.
- Generate first, inspect the result, then copy or move a selected project-bound output
  from Codex's generated-images location into the project.
- Preserve edits non-destructively unless replacement was explicitly requested.

For edits, list invariants before the requested change:

```text
Keep the person's identity, pose, wardrobe, scene geometry, and factual content
unchanged. Change only the capture treatment described below: natural phone-camera
exposure, plausible texture, and casual framing. Do not add people, objects, text, or
evidence-like details.
```

Reference images used only for lighting, framing, or capture style must not silently
replace the subject's identity or factual scene content.

## Video direction

Video realism depends on temporal behavior, not just a grain overlay:

- Use continuous, non-repeating movement appropriate to the action.
- Give camera movement a physical cause: walking steps, a hand adjustment, table
  vibration, or a fixed dashboard mount.
- Use natural pauses, glances, interrupted gestures, and real-time reframing sparingly.
- Select FPS from the target platform and provider profile. Do not force 24 FPS when a
  30 FPS or variable-phone-capture look is more plausible.
- Keep generation footage decodable and synchronized. Add severe compression, dropped
  frames, jump cuts, or audio drift in post only when explicitly requested.
- “Raw” means minimally produced; it does not mean corrupt or unsafe.

When execution is requested, put these directions into the provider-aware shot manifest
and let `video-generator` validate duration, FPS, frame count, audio, workflow, and
output retrieval.

## Safety and authenticity boundaries

- Default to fictional, clearly adult subjects when age is not specified and the scene
  could be intimate, such as bedrooms, bathrooms, swimwear, or changing rooms.
- Do not reproduce an identifiable person's likeness without authorization.
- Do not fabricate evidence, eyewitness media, news footage, medical proof, criminal
  allegations, or fake documentation of a real event.
- Do not instruct dangerous capture behavior. Driving scenes use a parked vehicle, a
  passenger camera, or a fixed mount while the driver's hands remain on the wheel.
- Do not add private information, readable addresses, license plates, credentials, or
  incidental bystanders unless explicitly needed and appropriate.
- For publication, follow current platform and jurisdictional disclosure requirements.
  When the user asks about a specific platform's rules, verify the current policy rather
  than relying on this skill.

## Deliverables

For prompt-only requests, return one strong prompt in a code block. Offer a small number
of targeted swaps only when useful.

For generated or edited images, report:

- the final prompt;
- built-in imagegen as the execution path;
- reference roles;
- the final saved path for project-bound output;
- any limitation or unresolved realism/identity issue.

For video preparation, return the prompt plus capture profile: orientation, FPS intent,
camera source, motion behavior, audio intent, and post-processing notes.
