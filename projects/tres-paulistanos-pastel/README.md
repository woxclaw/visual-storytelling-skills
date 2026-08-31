# Três Paulistanos, Três Pastéis

Pacote de aprovação para um curta ficcional de aproximadamente 60 segundos, falado em
português brasileiro, sobre três paulistanos comentando o pastel de feira em três pontos
da cidade: Bixiga, Praça da Sé e MASP.

Este projeto é uma encenação com personagens e imagens sintéticos. Ele não representa
depoimentos reais, não retrata vendedores identificáveis e não documenta uma feira ou
barraca específica. A cartela final proposta informa: **“Personagens e imagens gerados
por IA. Obra ficcional.”**

## Estado atual

Esta pasta contém o pacote de pré-produção e dois workflows locais de prova. Os stills
continuam ancorados no Codex built-in imagegen. A rota MiniMax H3 cobre depoimentos com
fala nativa em português brasileiro; a rota LTX-2.3 cobre inserts de comida e ambiente.
As provas rápidas são deliberadamente menores que o master e precisam de aprovação
humana antes da produção do restante dos planos.

## Ordem de leitura

1. `approval.md`
2. `creative-brief.md`
3. `character-bible.md`
4. `script.md`
5. `shot-list.md`
6. `production-plan.md`
7. `prompts/video-manifest.md`
8. `workflows/README.md`

## Decisões já fixadas

- Idioma falado: português brasileiro.
- Formato principal: 16:9, 1920 × 1080, aproximadamente 60 s.
- Estética: registro de celular observacional, naturalista, sem polimento publicitário.
- Stills e referências: Codex built-in imagegen.
- Movimento: ComfyUI local, com MiniMax H3 para fala e LTX-2.3 para inserts.
- Provas rápidas verificadas: MiniMax 608 × 352; LTX 640 × 320; 24 fps.
- O raster LTX é alinhado à grade do modelo e será recortado/conformado para 16:9 em
  editorial após aprovação.
- Master: conformar e finalizar em 1920 × 1080 somente após aprovação das provas.
- Música não diegética: nenhuma.
- Pessoas ao fundo: poucas, não identificáveis, sem rostos nítidos.
