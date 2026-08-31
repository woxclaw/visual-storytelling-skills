# Users' guide

This guide describes the architecture and workflows of the
visual-storytelling-skills toolkit. It covers the shot-specifier
execution sequence, the relationships between all skills and their
supporting components, and the reference-image generation pipeline
inside the scene-inventory-extractor-v2.

______________________________________________________________________

## End-to-end video production sequence

The sequence diagram below traces the whole video-production chain. The
`scene-inventory-extractor-v2` skill prepares source analysis,
reference images, the prompt keyword library, continuity inventory,
recurring visual element references, shot-frame assets, and its Phase 13
handoff. It does not assemble final video prompts or submit video jobs.

`shot-specifier` then loads that scene pack, generates required storyboard
frames through Codex imagegen by default or an explicitly selected image
provider, runs and actions storyboard consistency
checks, consults the model-specific deep-dive skills during per-shot
routing, and writes prompt files plus `prompts/manifest.md`.
`video-generator` owns the operational run from manifest to local clips. It validates
the selected local ComfyUI workflow or Higgsfield MCP schema, binds media, applies
provider settings and audio preferences, submits jobs, monitors status, retrieves
clips, updates the generation log, and writes assembly order.

`social-realism-prompts` is an optional capture-style layer for candid phone photos,
selfies, vlogs, camera-roll images, and UGC-style shots. It shapes physically coherent
camera and motion cues; `nanobanana` still owns image execution, `shot-specifier` owns
the shot manifest, and `video-generator` owns video execution.

```mermaid
sequenceDiagram
  actor User
  participant Extractor as scene_inventory_extractor_v2
  participant ImageGen as Codex_imagegen_or_selected_provider
  participant Shot as shot_specifier
  participant SeedanceDD as seedance_2_deep_dive
  participant KlingDD as kling_3_0_deep_dive
  participant VideoGen as video_generator
  participant Provider as ComfyUI_or_Higgsfield
  participant FS as file_system

  User->>Extractor: Provide_script_or_prose
  Extractor->>FS: Read_source_and_references
  Extractor->>ImageGen: Generate_or_edit_reference_images
  ImageGen-->>FS: Write_reference_images
  Extractor->>FS: Write_scene_inventory_continuity_and_phase_13_handoff

  User->>Shot: Request_shot_specification
  Shot->>FS: Load_scene_inventory_continuity_keyword_library_and_refs
  Shot->>ImageGen: Generate_strategy_required_storyboard_frames
  ImageGen-->>FS: Write_required_frames
  Shot->>FS: Run_and_action_storyboard_consistency_checks

  loop Per_shot_model_routing
    Shot->>FS: Read_prompt_metadata_and_reference_audit
    alt Route_to_seedance_2_0
      Shot->>SeedanceDD: Load_guidance
      SeedanceDD-->>Shot: Seedance_specific_rules
    else Route_to_kling3_0
      Shot->>KlingDD: Load_guidance
      KlingDD-->>Shot: Kling_specific_rules
    end
    Shot->>FS: Update_generation_prompt_audio_preferences_overrides_and_refs
    Shot->>FS: Append_manifest_row
  end

  User->>VideoGen: Run_video_generation
  VideoGen->>FS: Load_manifest_prompts_frames_and_required_refs
  VideoGen->>Provider: Inspect_capabilities_and_validate_route

  loop Per_shot_submission
    VideoGen->>FS: Resolve_media_paths_and_verify_required_refs
    VideoGen->>Provider: Bind_required_frames_and_references
    Provider-->>VideoGen: Validated_inputs

    alt Model_seedance_2_0
      VideoGen->>SeedanceDD: Validate_reference_plan_duration_and_audio
      SeedanceDD-->>VideoGen: Seedance_submission_plan
    else Model_kling3_0
      VideoGen->>KlingDD: Validate_shot_structure_camera_and_audio
      KlingDD-->>VideoGen: Kling_submission_plan
    end

    VideoGen->>Provider: Submit_video_job
    Provider-->>VideoGen: Job_id_or_prompt_id
    VideoGen->>FS: Append_generation_log_entry

    loop Poll_until_terminal_status
      VideoGen->>Provider: get_generation_status
      Provider-->>VideoGen: queued_or_in_progress_or_completed
    end

    VideoGen->>Provider: Resolve_or_download_clip
    Provider-->>VideoGen: Video_file
    VideoGen->>FS: Write_clip_and_update_log
  end

  VideoGen->>FS: Write_assembly_order
```

*Figure 1 — End-to-end production sequence. The extractor stops after
Phase 13 and hands a checked scene pack to `shot-specifier`.
`shot-specifier` actions consistency findings before it writes prompt
files and the manifest. `video-generator` is the only skill that submits
video generation jobs, and it must verify required references,
audio preferences, model overrides, and resumable job logging before a
shot is considered complete.*

______________________________________________________________________

## Video skill architecture

The class diagram below shows the video-production portion of the
toolkit and the relationships between the skills and supporting
artefacts. `scene-inventory-extractor-v2` produces the scene pack,
continuity inventory, prompt keyword library, recurring visual element
definitions, and reference images. `shot-specifier` consumes that
package, uses the provider-aware `nanobanana` compatibility skill for
storyboard frames, consults the Seedance
or Kling deep-dive skill when a shot is routed to that model, and emits
prompt files plus the manifest. `video-generator` consumes that handoff
and submits jobs through local ComfyUI or the Higgsfield MCP. The `phoneticize` skill is
part of the repository but is independent of this visual generation
pipeline.

Codex built-in imagegen is the default image provider. The compatibility skill inspects
local references before using them, copies accepted assets into the project, and does
not silently switch to Nano Banana or another provider after a capability failure.

```mermaid
classDiagram
  class Scene_inventory_extractor_v2 {
    +run_phases()
    +generate_reference_images()
    +verify_consistency_phase_13()
    +handoff_to_shot_specifier()
  }

  class Shot_specifier {
    +load_scene_inventory()
    +plan_shots()
    +generate_storyboard_frames()
    +run_storyboard_consistency_checks()
    +route_models_per_shot()
    +assemble_video_prompts()
    +emit_prompt_manifest()
  }

  class Image_provider {
    +generate_with_references()
    +edit_with_invariants()
    +persist_project_asset()
  }

  class Video_generator {
    +load_manifest_and_prompts()
    +inspect_provider_capabilities()
    +bind_media_and_workflow_inputs()
    +submit_video_jobs()
    +monitor_and_retrieve_clips()
    +write_generation_log()
    +write_assembly_order()
  }

  class Seedance_2_deep_dive {
    +advise_when_to_use_seedance()
    +plan_multimodal_references()
    +shape_seedance_prompts()
    +suggest_duration_and_aspect()
    +troubleshoot_seedance_failures()
  }

  class Kling_3_0_deep_dive {
    +advise_when_to_use_kling()
    +plan_shot_structure()
    +plan_camera_and_motion()
    +plan_elements_and_motion_control()
    +shape_kling_prompts()
    +troubleshoot_kling_failures()
  }

  class Video_provider {
    +validate_route()
    +submit_job()
    +get_job_status()
    +retrieve_output()
  }

  class Prompt_keyword_library {
    +provide_style_phrases()
    +ensure_style_consistency()
  }

  class Continuity_inventory {
    +list_props_and_recurring_elements()
    +list_constraints()
  }

  class Recurring_visual_element_ref {
    +element_name
    +location_set
    +appearance_lock
    +reference_file
  }

  Scene_inventory_extractor_v2 --> Image_provider : uses_for_reference_images
  Scene_inventory_extractor_v2 --> Continuity_inventory : produces
  Scene_inventory_extractor_v2 --> Recurring_visual_element_ref : defines

  Shot_specifier --> Image_provider : uses_for_storyboards
  Shot_specifier --> Prompt_keyword_library : uses_for_style_language
  Shot_specifier --> Continuity_inventory : reads_for_constraints

  Shot_specifier --> Seedance_2_deep_dive : consults_when_model_seedance_2_0
  Shot_specifier --> Kling_3_0_deep_dive : consults_when_model_kling_3_0
  Shot_specifier --> Video_generator : upstream_of

  Video_generator --> Video_provider : submits_jobs_to
  Video_generator --> Seedance_2_deep_dive : uses_for_seedance_jobs
  Video_generator --> Kling_3_0_deep_dive : uses_for_kling_jobs

  Continuity_inventory --> Recurring_visual_element_ref : contains
  Prompt_keyword_library ..> Scene_inventory_extractor_v2 : produced_in_phase_2_4
```

*Figure 2 — Video skill and artefact relationships. Solid arrows show
runtime dependency or handoff direction: the extractor creates
continuity and recurring-element constraints, `shot-specifier` turns
them into per-shot prompt and reference requirements, and
`video-generator` submits only after validating those requirements
against the live ComfyUI or Higgsfield surface. The dashed arrow records provenance:
the prompt keyword library is produced by extractor Phase 2.4 and then
reused downstream for style consistency.*

______________________________________________________________________

## Reference image generation pipeline

The flowchart below shows Phase 11 of `scene-inventory-extractor-v2` in
detail, together with the consistency verification loop that follows in
Phases 12 and 13. Prop references are classified in Phase 6 as either
required-before-Phase-12 (props that appear in video frames and must be
locked before any shot-frame generation begins) or incidental (props
that can be generated after location references without blocking shot
generation). Phase 6 also identifies recurring visual elements: objects,
fixtures, interfaces, machinery, furniture layouts, or set dressing that
appear in more than two shots and would be noticed if changed. The
flowchart enforces that all required-before-Phase-12 prop primaries and
recurring visual element refs are locked before location references are
generated.
Phase 12 performs a per-shot reference check; if anything is missing,
control returns to Phase 11. Phase 13 checks both individual prop
consistency against the primary reference and cross-shot prop identity
across all frames, plus recurring visual element consistency across
every shot where each element is visible. Phase 13 findings are action
items for the agent, not informational notes.

```mermaid
flowchart TD
  A[Phase 6: Scene Inventory
  classify prop reference priority
  required-before-Phase-12 vs incidental] --> B[Phase 11 start
  Reference image generation]

  B --> C[Generate style anchors
  11.1 Style Anchor]
  C --> D[Generate all character primary and additional refs
  11.2 Character References]

  D --> E[Generate required-before-Phase-12 prop refs
  11.3 Prop References
  primary + variants]
  E --> F{All required-before-Phase-12
  prop primary refs locked?}
  F -- no --> E
  F -- yes --> G[Generate recurring visual element refs
  11.4 Recurring Visual Element References]
  G --> H{All recurring element refs
  visible in location refs locked?}
  H -- no --> G
  H -- yes --> I[Generate location refs
  11.5 Location Scouting References]
  I --> J[Generate incidental prop refs
  primary + variants]

  J --> K[Generate reference image manifest]
  K --> L[Generate video role manifest
  11.6 Video Role Manifest
  assign start_image,end_image,image,video,audio roles]

  L --> M[Phase 12: Shot-frame generation
  Pre-generation check per shot]

  M --> N{For this shot:
  refs for all named characters,
  required props,
  recurring visual elements,
  correct location variant exist?}
  N -- no --> B
  N -- yes --> O[Generate start, key, end frames
  using prompt keyword library
  and locked refs]

  O --> P[Phase 13: Consistency verification]

  P --> Q[Check prop consistency
  vs primary prop ref]
  P --> R[Check cross-shot prop identity
  view all frames with each named prop]
  P --> S[Check recurring visual element consistency
  view all frames where each element appears]

  Q --> T{Prop mismatch vs primary ref?}
  T -- yes --> U[Regenerate offending frames
  using locked prop or element refs]
  T -- no --> V[Prop consistent]

  R --> W{Prop looks like different object
  across shots?}
  W -- yes --> U
  W -- no --> X[Cross-shot prop identity confirmed]

  S --> Y{Recurring element drifts
  across shots?}
  Y -- yes --> U
  Y -- no --> Z[Recurring elements confirmed]
```

*Figure 3 — Reference image generation and consistency verification
pipeline (Phases 6, 11, 12, and 13 of `scene-inventory-extractor-v2`).
Required-before-Phase-12 props and recurring visual elements must be
fully locked before location reference generation begins when they are
visible in those locations. Phase 12 loops back to Phase 11 if any
reference is missing. Phase 13 enforces per-shot prop consistency,
cross-shot prop identity, and recurring visual element stability; the
agent must fix or explicitly route every finding before handoff.*
