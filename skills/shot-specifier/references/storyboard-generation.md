# Storyboard Generation Guide

Read this before Phase 5 (Storyboard Generation). Covers image-provider selection,
prompt construction for storyboard frames, reference-role assignment, and the
generate-vs-edit decision.

---

## Tool Selection

Load `skills/nanobanana/SKILL.md` for the provider-aware image workflow. Use Codex
built-in imagegen by default. Use the Nano Banana MCP only when the user or project
explicitly selects it.

### Provider contract

- For Codex, inspect local references with `view_image`; pass an all-local reference set
  through `referenced_image_paths`; do not combine that with
  `num_last_images_to_include`; and copy the selected output into the project.
- Do not assume Codex imagegen accepts model or output-path fields.
- For Nano Banana, inspect the live MCP schema and use its actual operation and model
  fields. Do not promote Nano-specific fields into the project manifest.
- If the selected provider cannot accept the required references or preserve a
  continuity-critical subject, **STOP** and report the blocker. Do not silently switch
  providers.

### Tool Selection by Operation

| Operation | Tool |
|-----------|------|
| Character-centric storyboard frame | Identity-preserving generation with canonical character ref first |
| Environment or prop-led storyboard frame | Generation with environment/prop ref first |
| End frame that is a modified version of the start frame | Edit with strict invariants |
| Shot involving a recurring human character across multiple frames | Identity-preserving generation using canonical and in-style refs |
| Frame that combines multiple reference subjects | Multi-reference generation with explicit per-image roles |

---

## Reference Completeness Check

Before generating any frame — start, end, or key — answer these questions for the
specific shot:

1. Does a canonical reference image exist for **every named character** in this shot?
2. Does a canonical reference image exist for **every named prop** visible in this shot?
3. Does a locked reference image exist for **every recurring visual element** visible
   in this shot: fixture, interface, machinery, furniture layout, signage cluster, or
   set dressing that appears in more than two shots and would be noticed if it changed?
4. Does a canonical reference image exist for **the specific location variant** (angle ×
   lighting condition) this shot requires?

If any answer is no, generate the missing reference first using the scene inventory's
Phase 11 procedure. Do not proceed with storyboard generation until all answers are
yes. A missing prop or recurring visual element reference is the most common root cause
of cross-shot visual inconsistency: the model invents a different object, fixture
layout, interface state, or set-dressing arrangement each time.

---

## Start Frame Prompt Construction

Build the prompt using the shared order from the `nanobanana` compatibility skill:

1. **Subject:** Who or what is in frame; their key identity features
2. **Action/state:** What is happening at this exact moment
3. **Setting:** Location type; environmental details; spatial layout
4. **Composition:** Frame size; camera angle; spatial arrangement
5. **Lighting/mood:** Key source; quality; colour temperature; atmosphere
6. **Style/materiality:** Draw from prompt keyword library — global style phrase,
   location vocabulary, lighting condition vocabulary
7. **Constraint layer:** Negative constraints; exact prop positions; what must remain;
   what must not appear

End every prompt with: `"no text, no watermarks, no logos, no labels, no annotations"`

### Reference Image Assignment

Pass reference images with explicit roles. Assign each a job:

- **Identity:** character primary ref — prevents face drift
- **Pose/action:** character action ref — guides body position
- **Style:** style anchor — enforces filmstock and colour treatment
- **Environment:** location ref matching the shot's lighting condition
- **Prop:** prop primary ref — ensures prop appearance consistency
- **Recurring visual element:** locked element ref — preserves monitor layouts, screen
  colours, robots, cabinets, cargo pods, signage clusters, fixture arrays, or workstation
  layouts that recur across shots

Do not pass more references than necessary. Too many references can produce averaging
artefacts. The standard set per frame is:

- 1 style ref (always)
- 1 location ref matching the shot's lighting condition (always)
- 1 character ref per named character in frame
- 1 prop ref per named, story-critical prop in frame
- 1 recurring visual element ref per visible reference-required fixture, interface,
  machinery item, furniture layout, or set-dressing element

The prop ref is mandatory whenever a named prop appears in frame — omitting it gives the
model licence to invent the prop's appearance independently per shot, producing a
different-looking object every time it appears on screen.

Pass these paths through the selected provider's reference mechanism; do not provide
references as an unclassified general pool. Choose the smallest complete set:

- **Character-centric frame:** [character identity ref, location ref, required prop
  refs, recurring visual element refs, style anchor when available].
- **Environment or prop-led frame:** [location ref, required prop refs, recurring visual
  element refs, style anchor when available]. Add character refs only when a visible
  named character needs identity anchoring.
- **Frame derived from an existing start frame:** [start_frame, only refs needed for the
  described change]. Use edit semantics and list invariants first.

---

## End Frame Generation: Edit vs Generate

### Use edit-from-start when

- The end frame shows the same subject in the same location
- The change is: subject position shift, expression change, object state change,
  minor environmental change
- The composition is substantially the same
- More than 60% of the frame is unchanged

**Prompt format for edits:**

```text
Keep all of the following unchanged: {list everything that must stay the same}.
Change only: {exact description of what changes}.
```

State the preserved elements first. This is critical — the model defaults to changing
things when not explicitly told to preserve them.
Use an edit for end frames derived from start frames. The start frame should carry
character identity, location layout, prop appearance, and style treatment forward
naturally; the prompt should only describe the delta.

### Generate a new frame when

- The end frame is a different camera angle or framing
- The subject has moved significantly through the space
- The lighting condition has changed substantially
- The composition is more than ~40% different

For new end frames, use the start frame as an additional reference image to maintain
scene consistency, but generate from scratch rather than editing.

---

## Character Consistency Across Multiple Shots

When a character appears across multiple shots in the same sequence, make identity
preservation the primary constraint for shots 2 onwards:

1. Generate the character's first appearance using the canonical character primary ref.
2. For subsequent appearances, use both the canonical ref and the first accepted
   in-style storyboard frame, with a scene prompt describing the new context.

This maintains face and identity consistency more reliably than passing the primary ref
repeatedly, because the first storyboard frame captures the character as they appear in
this production's style (rather than the neutral white-bg reference).
This rule also applies to scene key frames: if the frame is character-centric, state
identity invariants explicitly and put the identity reference first.

---

## Quality Gates

After generating each storyboard frame, verify before moving on:

| Check | Pass condition | Fail action |
|-------|---------------|-------------|
| Subject identity matches reference | Face, clothing, distinguishing marks consistent | Regenerate with higher-weight identity reference |
| Location matches location ref | Architecture, layout, materials correct | Regenerate with explicit negative constraints for common errors |
| Lighting matches direction spec | Colour temperature, shadow direction, practical sources correct | Edit to correct lighting; or regenerate with more explicit lighting prompt |
| Recurring visual elements match locked refs | Monitor layouts, robots, fixture arrays, cabinets, cargo pods, signage, and workstation layouts remain stable | Regenerate with the missing/stronger recurring visual element refs |
| Interpolatable change (start vs end) | Subject or composition has measurably changed | If start and end are too similar, edit end frame to amplify the difference |
| Negative constraints respected | No trees, no forbidden elements, correct traffic direction, etc. | Regenerate with stronger negative constraint prompt |

---

## Common Storyboard Failures and Fixes

| Failure | Likely Cause | Fix |
|---------|-------------|-----|
| Face changes between shots | Identity constraint or reference set too weak | Put the canonical identity ref first; strengthen invariants; regenerate |
| Location architecture wrong | Location ref not passed or weighted too low | Ensure location ref is always included; add architectural specifics to prompt |
| Recurring set dressing drifts | Element treated as background location dressing | Generate a locked recurring visual element ref and pass it for every shot where visible |
| Grain/filmstock wrong for POV shot | Global style phrase applied to machine-vision shot | Use POV override from keyword library; remove grain terms |
| Unwanted elements in frame | Negative constraints not in prompt | Add explicit negative constraints; repeat the key ones twice |
| Start and end frames look identical | Change too subtle for model | Increase magnitude of described change; or explicitly state the delta in the edit prompt |
| Style drifts across shots | Style vocabulary invented per-shot | Use keyword library phrases verbatim; pass style anchor ref on all shots |
| Named prop looks like a different object across shots | Prop ref not passed, or prop ref did not exist at generation time | Pass prop primary ref on every frame containing the prop; if prop ref was missing, regenerate those frames with the locked ref |
