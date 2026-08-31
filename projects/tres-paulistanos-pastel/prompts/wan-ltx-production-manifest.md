# Wan + LTX production manifest — draft v1

Status: production in progress after preliminary-material approval.

## Delivery routes

| Version | Picture | Spoken track | Draft raster | FPS |
|---|---|---|---:|---:|
| Wan v1 | Wan 2.2 TI2V 5B, local ComfyUI | Shared reviewed local PT-BR dialogue stem | 640 × 352 | 24 |
| LTX v1 | LTX-2.3 I2V, local ComfyUI | Shared reviewed local PT-BR dialogue stem + LTX ambience | 640 × 320 engine-aligned | 24 |

Wan 2.2 5B is a silent picture model. A 12-second native-dialogue LTX diagnostic also
baked gibberish subtitles into the picture and is excluded. Both clean visual cuts
therefore use the same reviewed Brazilian-Portuguese dialogue stems produced through
the already-approved local MiniMax speech route; this is an editorial sound binding,
not a claim that Wan or the accepted LTX picture generated speech. Neither production
workflow contains paid/API nodes.

## Shared constraints

- 16:9 delivery, conformed to 1920 × 1080 only after picture approval.
- One continuous camera setup per generated shot; no model-generated cuts.
- Candid São Paulo street-documentary realism, modest phone-camera drift, natural skin,
  ordinary feira detail, no beauty-ad finish.
- Preserve the supplied first frame, wardrobe, handedness, prop state and geography.
- No brands, logos, invented readable signs, captions, subtitles or watermarks in
  generated pixels.
- All intelligible speech is the exact Brazilian-Portuguese script below. No translation,
  paraphrase, narration, extra words or background music.
- Disclosure is editorial text only: “Personagens e imagens gerados por IA. Obra
  ficcional.”

## Shot bindings

| Shot | Start asset | Generated duration | Seed — LTX / Wan | Sound |
|---|---|---:|---|---|
| S00_SH001 | shots/S00_SH001/start.png | 2 s | 310832 / 310831 | fryer sizzle |
| S00_SH002 | shots/S00_SH002/start.png | 2 s | 300830 / 310833 | paper + stall |
| S01_SH001 | shots/S01_SH001/start.png | 4 s | 311101 / 321101 | Bixiga ambience |
| S01_SH002 | shots/S01_SH002/start.png | 8 s picture | 311112 / 321102 | Claire dialogue stem |
| S01_SH003 | shots/S01_SH003/start.png | 4 s | 311103 / 321103 | Claire stem continues |
| S02_SH001 | shots/S02_SH001/start.png | 4 s | 312101 / 322101 | Sé ambience |
| S02_SH002 | shots/S02_SH002/start.png | 8 s picture | 312112 / 322102 | Luciana dialogue stem |
| S02_SH003 | shots/S02_SH003/start.png | 4 s | 312103 / 322103 | Luciana stem continues |
| S03_SH001 | shots/S03_SH001/start.png | 4 s | 313101 / 323101 | Paulista ambience |
| S03_SH002 | shots/S03_SH002/start.png | 8 s picture | 313112 / 323102 | Rafael dialogue stem |
| S03_SH003 | shots/S03_SH003/start.png | 4 s | 313103 / 323103 | Rafael stem continues |
| S04_SH001 | reuse S01/S02/S03 insert takes | 4 s edit | — | three-location ambience |
| S04_SH002 | shots/S04_SH002/start.png | 2 s + 2 s card | 314002 / 324002 | city fade |

The sound stems intentionally give every complete line room to finish. Editorial
picture cuts away after about eight seconds while the same stem continues over the
matching food insert.

## Action prompts

### S00_SH001

Over two seconds, boiling oil bubbles around one rectangular pastel while a metal
skimmer enters slowly from frame right, lifts it a few centimetres, and lets one last
drop drain. Preserve the supplied pastry, fryer, daylight and composition. Extreme
close handheld phone shot, tiny grip drift, no zoom or cut. Ordinary feira realism.

### S00_SH002

Over two seconds, the skimmer lowers the freshly fried pastel into the white paper
sleeve; the receiving hand steadies the sleeve and the paper creases naturally.
Preserve hands, pastry geometry and stall. Continuous close handheld phone shot.

### S01_SH001 / S02_SH001 / S03_SH001

Over four seconds, hold the supplied location geography while nearby pedestrians move
at ordinary speed and the featured character receives a pastel at the fictional stall.
One restrained eye-level handheld phone take, only a small lateral settling motion;
landmarks remain distant and undistorted.

### S01_SH002 — Claire

Claire keeps the supplied fair-skinned face, medium-brown hair, coral blouse under navy
outer layer, pastry and Bixiga background. She blinks, shifts her weight slightly and
makes one small pastry gesture while speaking naturally to the nearby camera in a
contemporary São Paulo accent. Exact dialogue: “Eu gosto quando o pastel chega tão
quente que você precisa esperar — e não espera. A primeira mordida faz aquela bagunça
no papel. Pra mim, feira é isso.” One unhurried continuous eye-level phone take.

### S01_SH003

Claire's bitten pastel remains in her left hand inside its paper sleeve. Over four
seconds, two crumbs fall, her fingers settle and the translucent grease mark expands
slightly. Tight chest-and-hands insert; no face and no independent speech.

### S02_SH002 — Luciana

Luciana keeps the supplied medium-brown face, dark curls, moss-green shirt, dark jeans,
burgundy crossbody bag, pastel, caldo de cana and Praça da Sé background. She glances briefly toward the street,
returns to camera and makes one small cup gesture while speaking naturally in a
contemporary São Paulo accent. Exact dialogue: “No Centro, pastel é uma pausa de
verdade. Queijo, caldo de cana, dois minutos de sombra… você volta pra rua outra
pessoa.” One unhurried continuous eye-level phone take.

### S02_SH003

Luciana steadies the two-thirds-full caldo de cana cup in her right hand and the bitten
pastel in her left. Her moss-green shirt and burgundy crossbody strap remain fixed.
Condensation moves subtly and the cup tilts only a few degrees. Tight chest-and-hands
insert; no face and no independent speech.

### S03_SH002 — Rafael

Rafael keeps the supplied medium-brown face, short curls, light stubble, olive jacket,
pastel and MASP-background composition. He gives a brief amused half-smile, raises the
pastel slightly and speaks naturally in a contemporary São Paulo accent. Exact
dialogue: “A Paulista inventa moda pra tudo. O pastel não precisa. Massa crocante,
queijo puxando e um guardanapo que nunca dá conta.” One unhurried continuous eye-level
phone take.

### S03_SH003

Rafael holds the bitten pastel in his right hand and the used napkin in his left. A
short cheese strand stretches and breaks; the napkin folds once under his fingers.
Tight chest-and-hands insert; no face and no independent speech.

### S04_SH002

Over two seconds, the supplied thin white wrapping paper settles almost imperceptibly
on the stainless counter while nearby daylight shifts and distant market shapes move
softly. Preserve the grease mark and crumbs. No people, hands, pastry or generated text.

## Review gates

1. Confirm Claire's LTX take: identity, fair skin, exact PT-BR words, accent, lip timing,
   hands, pastry integrity, and no extra speech/music.
2. Confirm one Wan silent-picture take: temporal action, reference adherence, geometry
   and camera restraint.
3. Generate remaining character and insert candidates.
4. Assemble both watermarked review cuts; captions and the fiction/AI disclosure are
   editorial overlays.
