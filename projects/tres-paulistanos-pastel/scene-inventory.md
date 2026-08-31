# Scene inventory — approval handoff 0.1

## Creative pillars

- **Aesthetic — Rua em pausa:** smartphone contemporâneo, pele e comida com textura
  natural, cidade presente, sem acabamento de campanha.
- **Storytelling — três respostas:** uma ideia e um gesto por personagem; as falas
  continuam sobre inserts para proteger o ritmo e reduzir exposição de lip sync.
- **Camera grammar:** plano geral curto para geografia, plano médio observacional para
  fala, insert físico para a segunda metade de cada testemunho.
- **Format:** 16:9, entrega 1920 × 1080; referências geradas a 1672 × 941.
- **Cadence:** intenção de 30 fps; valor final segue workflow ComfyUI validado.
- **Color:** detalhe digital natural, luz diurna neutra levemente quente, sombras
  abertas, saturação moderada, sem emulação de película.
- **Audio:** português brasileiro, ambiente da cidade, fritura e papel; sem música.

O vocabulário canônico de prompt está em `prompt-keywords.md`.

## Narrative spine

1. O pastel sai do óleo: desejo imediato.
2. Claire: a feira como impaciência e bagunça boa.
3. Luciana: a feira como pausa dentro do Centro.
4. Rafael: a simplicidade do pastel contra a pressão conceitual da Paulista.
5. Três gestos encerram a ideia; cartela explicita ficção e IA.

## Scene records

### SC-00 — abertura

- **Location:** barraca ficcional não identificável.
- **Characters:** mãos anônimas, sem rosto.
- **Change:** pastel sai do óleo e entra no papel.
- **Sound:** óleo, metal, papel, feira distante.

### SC-01 — Bixiga

- **Location:** Praça Dom Orione, contexto dominical da feira de antiguidades.
- **Character:** Claire.
- **Objective/change:** do autocontrole de esperar ao riso por morder cedo demais.
- **Props:** pastel e papel; bolsa de lona.
- **Reset:** pastel intacto no início, uma mordida depois, migalhas/óleo no papel.

### SC-02 — Praça da Sé

- **Location:** praça, com barraca ficcional de comida de rua no entorno.
- **Character:** Luciana.
- **Objective/change:** ela interrompe a rotina, olha o fluxo e define a pausa.
- **Props:** copo de caldo de cana, pastel e papel, bolsa transversal.
- **Reset:** copo dois-terços cheio na direita; pastel com uma mordida na esquerda.

### SC-03 — MASP

- **Location:** espaço público sob/ao lado do vão livre, feira de antiguidades ao fundo.
- **Character:** Rafael.
- **Objective/change:** pausa seca antes da frase “O pastel não precisa”.
- **Props:** pastel de queijo, papel, guardanapo.
- **Reset:** pastel na direita; guardanapo só entra depois do pequeno gesto à esquerda.

### SC-04 — fecho

- **Location:** montagem dos três lugares e balcão neutro.
- **Change:** comida vira gesto e depois vestígio; entra cartela de ficção/IA.

## Reference manifest

| ID | File | Purpose |
|---|---|---|
| STYLE-01 | `refs/style/style-anchor-01.png` | textura/cor/comida |
| CHAR-CLAIRE-01 | `refs/characters/claire/primary.png` | identidade e roupa |
| CHAR-LUCIANA-01 | `refs/characters/luciana/primary.png` | identidade e roupa |
| CHAR-RAFAEL-01 | `refs/characters/rafael/primary.png` | identidade e roupa |
| LOC-BIXIGA-01 | `refs/locations/bixiga/establishing-late-morning.png` | geografia SC-01 |
| LOC-SE-01 | `refs/locations/se/establishing-late-morning.png` | geografia SC-02 |
| LOC-MASP-01 | `refs/locations/masp/establishing-late-morning.png` | geografia SC-03 |
| FRAME-S01-02 | `shots/S01_SH002/start.png` | start frame Claire |
| FRAME-S02-02 | `shots/S02_SH002/start.png` | start frame Luciana |
| FRAME-S03-02 | `shots/S03_SH002/start.png` | start frame Rafael |

## Video role manifest

| Ref ID | Video role | Used in | Notes |
|---|---|---|---|
| FRAME-S01-02 | `start_image` | S01_SH002 | identidade/local/estilo já baked in |
| FRAME-S02-02 | `start_image` | S02_SH002 | identidade/local/props já baked in |
| FRAME-S03-02 | `start_image` | S03_SH002 | identidade/local/prop já baked in |
| CHAR-CLAIRE-01 | `image` | SC-01 shots | usar só se workflow aceitar identity ref |
| CHAR-LUCIANA-01 | `image` | SC-02 shots | usar só se workflow aceitar identity ref |
| CHAR-RAFAEL-01 | `image` | SC-03 shots | usar só se workflow aceitar identity ref |
| STYLE-01 | `image` | all | usar só se workflow aceitar style ref |

## Handoff status

Approval-stage references exist and no BLOCK issue remains. Full per-shot prompt files,
end frames, inserts and provider timing intentionally wait for approval and live ComfyUI
schema validation.
