# Consistency report

- **Project:** Três Paulistanos, Três Pastéis
- **Date:** 2026-08-30
- **Frames checked:** 10 assets; 3 testimonial start frames against 7 canonical refs
- **BLOCK issues:** 0 unresolved
- **WARN issues:** 1 resolved locally; 2 carried to downstream review

## Results

| Check | S01_SH002 | S02_SH002 | S03_SH002 |
|---|---|---|---|
| Character identity | PASS | PASS | PASS |
| Wardrobe | PASS | PASS | PASS |
| Location character | PASS | PASS | PASS |
| Required food/drink prop | PASS | PASS | PASS |
| Hand anatomy | PASS | PASS | PASS |
| Lighting/style | PASS | PASS | PASS |
| Text/logo suppression | PASS | PASS | PASS |

## WARN items

### CV-001 — S02_SH002 — hand continuity

- **Status:** resolved in prep documents.
- **Finding:** generated frame placed Luciana's cup in her right hand and pastel in her
  left, opposite the preliminary written note.
- **Action:** because this is the first canonical scene frame and hand choice has no
  narrative meaning, `character-bible.md` and `continuity-inventory.md` now lock the
  generated configuration. No prior asset is contradicted.

### CV-002 — all dialogue frames — eye-line precision

- **Status:** downstream constraint.
- **Finding:** stills read close to lens; motion must keep the interviewer's position a
  few centimeters beside the lens so the piece feels like testimony, not selfie/vlog.
- **Constraint:** preserve the accepted starting eye line; no direct-to-selfie phone arm.

### CV-003 — location fidelity — synthetic geography

- **Status:** human approval required.
- **Finding:** locations are original synthetic approximations, not survey photographs.
- **Constraint:** treat them as narrative references, not factual architectural records;
  reject any future frame that materially invents a different landmark silhouette.

## Summary

The cast identities, wardrobes, location cues and visual treatment are coherent enough
for approval and a single motion proof. End frames and motion interpolatability are not
yet evaluated because this gate intentionally generated start frames only.

## Cast revision 2026-08-30

The original Bixiga character was superseded by Claire, 28, following user direction.
Claire's canonical reference and `S01_SH002/start.png` were regenerated with Codex
imagegen and visually checked together. Her face, fair complexion, freckles, hair,
wardrobe and tote are consistent across the two current assets. The prior character and
frame are retained only under `archive/superseded/joana/` and are not valid downstream
references.
