# board-games

Página única pra decidir a próxima compra de board game: wishlist ranqueada e os jogos que
ficaram de fora, com peso, duração, sobreposição com a coleção e veredito.

**A página é toda em inglês** (o relatório-fonte é em inglês). Este README fica em pt-BR.

**Sem dependências e sem build.** Publicado em GitHub Pages:

- <https://aagrjr.github.io/boardgames-analysis/> — wishlist de compra
- <https://aagrjr.github.io/boardgames-analysis/designers.html> — catálogos por designer/artista,
  com edição brasileira por jogo e filtros de peso e disponibilidade nacional

Ou abra os `.html` direto no navegador.

Atualizada em 30/09/2026: 8 opções candidatas + Magical Athlete como compra comprometida com rank #9, 48 fora da rodada.
Os números do subtítulo da página são calculados a partir dos dados desde 30/09/2026 — não existe mais contagem escrita à mão pra envelhecer.
Jogo comprado sai da tabela e vira uma linha na lista de fora.

> Notas, pesos e durações do BGG são aproximados e mudam com o tempo.
>
> ⭐, ✕ e anotações **nunca saem do seu navegador** — ficam em `localStorage`.

## O que a página faz

- Farol no veredito: 🟢 comprar / encaixe forte, 🟡 testar antes / condicional, 🔴 passar,
  🛒 compra já decidida. Legenda no topo
- "Main concern" aparece como linha separada abaixo do veredito, quando o jogo tem uma
- Chip de 2 jogadores ("excellent/good at 2" em verde, "fair at 2" em amarelo, "poor at 2"
  em vermelho) — 2P é o formato principal, então aparece em toda linha que tem essa avaliação
- Coluna de jogadores: melhor contagem segundo o BGG, com a faixa oficial da caixa embaixo
- Chip ⭐ quero; o chip "descartados" alterna entre a lista ativa e a dos descartados
- Ordenação por ordem da lista (padrão: todos os jogos por rank), nome,
  nota BGG, peso, duração máxima, melhor nº de jogadores ou novidade, com botão ↑/↓ pra inverter
- Tabela completa como vista padrão no desktop; cards no celular, onde ela não cabe.
  O chip alterna as duas a qualquer momento
- Cabeçalhos com botões acessíveis; a tabela e o seletor usam a mesma ordenação. Compras comprometidas mantêm seu rank e seguem a ordenação selecionada
- ⭐ quero e ✕ descartar em um clique nos cards
- Anotações por jogo
- No card, sobreposição mecânica e concorrentes de mesa ficam num `<details>` (na tabela
  são colunas próprias, como no relatório original)
- Lista dos jogos fora da rodada (vendidos, ruins a 2, saíram da wishlist, avaliados e não
  incluídos) num `<details>` no fim, com o motivo de cada um

## Onde fica o que você marca

`localStorage`, prefixo `board-games:`. Não existe botão de salvar. Mas é **por navegador**:
o que você marca no celular não aparece no desktop, e some se limpar dados do site.

## Editar os dados

Os jogos estão no array `JOGOS` no topo do `<script>`. Campos:

- `n`: prioridade de compra; `null` mostra `—`. Magical Athlete mantém o rank #8
- `bgg`: `null` = n/d; `bggAprox:1` mostra `~`; `bggNota` vira o `*` com tooltip ao lado
- `peso`, e `tempo` como faixa `[min,max]`
- `brasil`: `{s, txt}` com a situação da edição brasileira — `released`, `announced` ou `none` — e o texto completo. Vira um chip na tabela e no card
- `ano`: ano de lançamento segundo o BGG (vem do export da coleção); aparece embaixo do nome na tabela e na linha de prioridade do card
- `rating`, `plays`, `ratingGame`, `score` e `confidence`: notas pessoais e estimativas mantidas nos dados, mas **não exibidas** desde 14/09/2026
- `evidence`: texto de evidência, num `<details>` "Evidence" no card e na célula de veredito da tabela
- `melhor`: melhor contagem segundo o BGG. `players`: faixa oficial. `jog`: nº pra ordenar
- `novidade`: estrelas (aceita `.5`). `dois`: opcional, vira o chip de 2 jogadores
- `v`: `good` / `warn` / `bad` / `buying` — define cor e farol do veredito
- `tipo`: campo histórico; os cards mostram prioridade de compra e identificam compras comprometidas
- `preocupacao`: opcional, vira a linha "Main concern" abaixo do veredito

O campo `n` define a "recommended purchase order" (padrão da página). Os jogos fora da rodada ficam no
array `EXCLUIDOS`, como `["nome", "motivo"]`.

Abra com `#test` no fim da URL e olhe o console: o autoteste checa 8 candidatos + 1 compra comprometida,
48 excluídos, a coerência do subtítulo com os dados, prioridades, evidência nas duas vistas, ausência da coluna de nota pessoal, mediana, texto de notas
e todas as ordenações nos dois sentidos. Também verifica a troca entre cabeçalhos e seletor.

Dados e decisões reconciliados com collection.csv e as notas da conversa em 03/09/2026.
Trajan usa nota pessoal 9,3; Concordia identifica a nota 9,3 do jogo-base.
Viticulture, incluindo Tuscany e visitantes, foi excluído por agregar pouco à coleção; o motivo da venda anterior continua incerto.
Star Wars: Rebellion foi excluído: decisão explícita de não recomprar.
Notas de prazer e prioridade de compra ficam separadas. Estatísticas de candidatos excluem Magical Athlete.

Fate of the Fellowship fica em #6: nota pessoal 8,5, uma partida registrada, GOOD CANDIDATE.
É uma recompra em consideração; o motivo da venda anterior é desconhecido.

Great Western Trail: Second Edition entrou em #3 em 08/09/2026, sem expansão.
A nota 9,8 em duas partidas pertence à edição original; Second Edition não tem nota pessoal própria.

Gaia Project reintegrado em #4 (nota pessoal 10,0; uma partida); Arcs + The Blighted Reach em #8 (9,0 do jogo-base; uma partida).
Gaia Project considera apenas o jogo-base; Arcs inclui The Blighted Reach. Magical Athlete permanece comprometido em #13.

Arcs: notas de The Blighted Reach a dois adicionadas à evidência, com fontes.
Campanha incluída na compra em #8: testar a dois antes. A nota pessoal atualizada para 9,0 refere-se apenas ao jogo-base; peso ~4,55 refere-se à campanha. Duração exibida por ato, campanha de três atos. Preferências e notas existentes de Arcs preservadas.

Twilight Struggle removido por decisão do usuário: considera o design ultrapassado e não quer recomprar. Nota histórica 9,2 em uma partida preservada como evidência, não como prioridade.
Revive removido da wishlist por decisão do usuário em 09/09/2026.
Survive the Island removido por decisão do usuário: foco em grupos de 3–5, pouca prioridade para sessões a dois. Nota histórica 8,1 em seis partidas pertence à edição anterior.
Magical Athlete continua comprometido e ranqueado em #13. Total: 12 candidatos + 1 comprometido.

Great Western Trail: New Zealand removido por decisão do usuário; Second Edition permanece em #3. Consultar Caixinha Boardgames e Playeasy primeiro.

Speakeasy removido por decisão do usuário; Gaia Project permanece em #4 como opção pesada preferida. Comparação sem usar a nota histórica de Gaia; Speakeasy nunca foi jogado.

Clinic: Deluxe Edition adicionado em #12 como testar antes, apenas jogo-base. Bom encaixe potencial a dois; regras de posicionamento, movimentação e administração são as ressalvas. Estimativa 8–9 de baixa confiança, sem nota pessoal.

Pipeline removido da wishlist por decisão do usuário em 09/09/2026. Nota pessoal histórica 9,0 após uma partida preservada como evidência de gosto, separada da prioridade de compra.

Trismegistus (edição 2026) adicionado em #7: testar antes a dois, apenas jogo-base. Ambos adoram Unconscious Mind; afinidade favorece a candidatura sem confirmar nota pessoal. Estimativa provisória 8,5–9,5 de baixa confiança.

Preços e estoque conferidos nos sites de Caixinha Boardgames e Playeasy em 09/09/2026. Campo `shopping` por jogo, visível nos cards: loja, estado, URL do produto, preço normal, Pix e observação. Sem frete; consulta pontual, sem atualização automática. “Not found” indica anúncio correspondente não localizado; “Sold out” indica esgotado confirmado. Preços de esgotados são apenas referência. Arcs separa base disponível e campanha esgotada/não encontrada. Edições alternativas não substituem as da wishlist.

## Ordem vigente — sem notas pessoais anteriores

Reordenada em 09/09/2026 por preferência qualitativa, adequação a dois e contribuição à coleção, sem usar nenhuma nota numérica pessoal anterior. Notas e estimativas preservadas como referência histórica, sem determinar prioridade. Esta ordem substitui os ranks históricos descritos acima.

1. Clank! Legacy: Acquisitions Incorporated
2. Concordia: Special Edition
3. Wondrous Creatures
4. The Lord of the Rings: Fate of the Fellowship
5. Agent Avenue
6. Jisogi: Anime Studio Tycoon
7. The Game Makers
8. Magical Athlete

Gaia Project comprado em 09/09/2026: agora pertencente à coleção, fora da wishlist. Demais prioridades renumeradas, sem usar notas pessoais anteriores.

Village (somente base, sem Big Box) e The Game Makers adicionados em 11/09/2026 como candidatos secundários. Sem notas pessoais anteriores na prioridade; dados BGG e preços do Village verificados em 14/09/2026 (peso ~3,1, BGG ~7,5, esgotado na Caixinha a R$304,90, não encontrado na Playeasy); The Game Makers conferido em 14/09/2026 pelo export do BGG (peso 2,94, BGG 7,53, 60–90 min, melhor com 2–3; chip de 2 jogadores passou para good at 2). Arcs corrigido para 2 partidas, com nota BGG do base (7,99) e da The Blighted Reach (8,85).

Coluna "Your rating / estimate" removida em 14/09/2026 a pedido: notas e estimativas continuam nos dados e aparecem só dentro do texto de evidência, sem coluna, sem destaque no card e sem opção de ordenação.

Observações de duração (`tempoNota`, como "per act · 3-act campaign") saíram da coluna Playtime da tabela em 14/09/2026, porque alargavam a coluna; continuam nos cards. Na tabela, o aviso de duração por ato da The Blighted Reach segue no "Main concern". Magical Athlete continua comprometido e ranqueado.

Trismegistus removido por decisão do usuário em 12/09/2026; ordem restante preservada e renumerada.

Clinic: Deluxe Edition removido por decisão do usuário em 12/09/2026; demais prioridades preservadas e renumeradas.

Arcs separado em 12/09/2026: base #2, pouco acima de GWT #3; The Blighted Reach sozinho #9. Compras separadas; a expansão requer o jogo-base. Notas salvas antigas de Arcs continuam associadas à opção de campanha.

A linha #9 contém somente The Blighted Reach. Preços e nota pessoal do jogo-base removidos dessa linha; a nota 9,0 continua apenas no Arcs base.

Village removido em 14/09/2026 a pedido: já foi da coleção e vendido (8,7 em 6 partidas pelo export do BGG); na comparação direta, Trajan foi o preferido. The Game Makers passou para #11 e Magical Athlete para #12.

Ano de lançamento (BGG) adicionado em 14/09/2026 a partir do export da coleção. Concordia: Special Edition aparece como 2027 porque é o ano registrado na entrada da edição no BGG.

Trajan removido em 14/09/2026 a pedido: encaixe fraco na coleção. Com peso ~3,6, fica entre os Euros de otimização mais leves que vocês jogam muito (Five Tribes, White Castle, Castles of Burgundy) e as noites pesadas. Nota histórica 9,3 em 2 partidas preservada no motivo. The Blighted Reach passou para #8, Jisogi #9, The Game Makers #10 e Magical Athlete #11.

Arcs (base) e The Blighted Reach comprados juntos numa promoção em 18/09/2026. Os dois saíram da tabela e viraram linhas na lista de fora. Prioridades renumeradas: Concordia SE #2, Wondrous #3, Fate of the Fellowship #4, Agent Avenue #5, Jisogi #6, The Game Makers #7 e Magical Athlete #8.

Situação de edição brasileira registrada em 18/09/2026: Sina da Sociedade já lançado pela Galápagos; Clank! Legacy e Magical Athlete anunciados pela Asmodee/Galápagos sem data; Criaturas Maravilhosas anunciado pela Mosaico; Agent Avenue anunciado pela Forjamundo para 2026; Concordia SE, Jisogi e The Game Makers sem edição nacional. Fonte: fichas do Ludopedia e buscas em lojas.

Slay the Spire: The Board Game adicionado em 24/09/2026 a pedido, direto em #2 — atrás só de Clank! Legacy e à frente de Concordia SE.
Motivo: é o único candidato que acerta peso (2,91), melhor contagem (2 jogadores pela enquete do BGG) e gênero ao mesmo tempo.
Snapshot BGG de 24/09/2026: nota 8,61 com 14.434 votos, rank geral 15, 1–4 jogadores.
Ele já aparecia no export da coleção com `wishlistpriority` 1 ("Must have") e todos os flags zerados, ou seja, saiu da wishlist em algum momento — motivo desconhecido, registrado na evidência.
Edição nacional da Grok Games, "Slay the Spire: O Jogo de Tabuleiro", já lançada e amplamente presente na Ludopedia.
Evidência a favor: deckbuilders giram na coleção (Dune: Imperium 9,5/5 partidas, Clank!: Catacombs 9,0/5, Arnak 9,0/4, SW Deckbuilding 8,9/5, Clank! 8,4/6).
Ressalva registrada em "Main concern": cooperativos mais densos travaram em 1–2 partidas (Spirit Island 8,4, Aeon's End 8,5, Mage Knight 8,0, Marvel Champions 7,5); as exceções foram Pandemic Legacy S1 (10,0/9), de campanha, e Bomb Busters (9,2/9), leve. Duração também pesa: 90–150 min, com setup repetido a cada ato.
Demais prioridades renumeradas: Concordia SE #3, Wondrous #4, Fate of the Fellowship #5, Agent Avenue #6, Entropy #7, Jisogi #8, The Game Makers #9, Ants #10, Quacks #11 e Magical Athlete #12.

Correção no autoteste em 24/09/2026: a verificação da ordem de compra comparava com um literal de `JSON.stringify` escrito com espaço depois das vírgulas, formato que `JSON.stringify` nunca produz — ou seja, ela falhava desde a última edição manual da lista. Passou a comparar os nomes unidos por ` | `, que é legível e de fato passa.

## Ordem por dados — calculada e revertida em 29/09/2026

> **Revertido no mesmo dia, a seu pedido: a ordem voltou ao critério anterior.** O cálculo abaixo fica
> registrado como referência, porque as medidas continuam válidas e úteis, mas **não** define mais o campo `n`.
> O que ficou da mudança: o campo `bggVotos`, os ranks e notas atualizados, e dois autotestes corrigidos.

A ordem deixou de ser definida por faixa de veredito e passou a ser calculada. O motivo: `TRY FIRST` e
`CONDITIONAL` não são medidas, são recados pra você decidir — ordenar por eles era ordenar por opinião.

`score = partidas esperadas × ajuste de 2 jogadores × nota encolhida`

**Partidas esperadas** vêm da sua própria coleção, por faixa de peso (102 jogos com `own=1`):
`<2,0 → 7,66` · `2,0–2,5 → 4,48` · `2,5–3,0 → 4,42` · `3,0–3,5 → 4,40` · `3,5+ → 2,54`.
Cooperativos usam medida própria, porque o padrão deles é outro: leves (<2,5) → 8,25;
pesados (>2,5) → mediana 2,0 (valores 9, 3, 2, 2, 1, 1).

**Ajuste de 2 jogadores**, pela enquete do BGG: melhor com 2 → ×1,15; recomendado a 2 → ×1,00; não recomendado → ×0,80.

**Nota encolhida** pelo número de votos: `(nota × votos + 7,5 × 1500) / (votos + 1500)`. Isso pune nota alta com
base fina sem nenhum juízo de valor — Entropy cai de 7,75 para 7,57 com 559 votos, Quacks quase não se move com 60.317.

Resultado: Agent Avenue 66,1 · Quacks 59,7 · Wondrous Creatures 40,2 · The Game Makers 38,2 ·
Clank! Legacy 37,0 · Concordia SE 35,4 · Jisogi 33,3 · Entropy 22,1 · Fate of the Fellowship 16,5.

Duas decisões de método, registradas porque afetam o resultado:
- **Concordia SE** usa os dados do Concordia base (8,07 / 46.434 votos). A entrada da edição tem 5,07 com 865 votos,
  que é reclamação sobre a edição e não avaliação do jogo.
- **Fate of the Fellowship** usa a mediana dos cooperativos pesados em vez da faixa de peso, porque existe medida
  específica pra esse caso na sua coleção.

**Limitação conhecida:** o modelo favorece jogo leve por construção, já que "partidas esperadas" é o fator dominante.
Clank! Legacy cai para #5 por peso, não por qualidade. Ordenado só pela nota encolhida, a lista seria
Clank! Legacy 8,37 · Fate 8,25 · Concordia 8,05 · Wondrous 7,94 · Quacks 7,79 · Entropy 7,57 · Jisogi 7,55 ·
Game Makers 7,52 · Agent Avenue 7,51 — quase a ordem anterior. Você escolheu tempo de mesa × qualidade.

Campo `bggVotos` adicionado por jogo, porque é o dado que sustenta a ordem e faltava.
Ranks e notas do BGG atualizados em 29/09/2026: Wondrous 201→191, Fate 58→56, Agent Avenue 511→505,
Entropy 4.184→3.919, Jisogi 3.086→2.940, The Game Makers 3.062→2.843, Quacks 80→81.

Dois autotestes estavam quebrados desde as edições manuais e foram corrigidos: a posição esperada do Entropy
e a checagem de ordenação por rank do BGG, que ainda apontava para o rank 15 do Slay the Spire, já removido.

Cooperativos medidos em 29/09/2026: os sete ativos na coleção (Cross Clues 23 partidas, Dorfromantik 19, Hanabi 10,
Bomb Busters 9, ito 8, Just One 5, Sherlock Consulting Detective 3) estão todos em peso ≤ 2,66. Todos os cooperativos
acima de 2,5 que foram comprados saíram com 1 ou 2 partidas, exceto Pandemic Legacy S1, que é campanha feita pra
terminar. Aeon's End, Mage Knight e Marvel Champions aparecem com partidas mas `own=0` e `prevowned=0`: foram jogados
sem nunca terem sido comprados, e não contam como cooperativos que falharam.

Ordem vigente após a reversão, igual à de antes do cálculo: Clank! Legacy #1, Concordia SE #2, Wondrous Creatures #3,
Fate of the Fellowship #4, Agent Avenue #5, Entropy #6, Jisogi #7, The Game Makers #8, Quacks #9, Magical Athlete #10.
As inconsistências já apontadas continuam de pé por decisão sua: Entropy (TRY FIRST) acima de Jisogi (GOOD CANDIDATE),
Agent Avenue (BUY) abaixo de dois vereditos mais fracos, e Fate of the Fellowship em #4 apesar de ser cooperativo
acima do teto de peso medido.

## Entropy comprado — 30/09/2026

Entropia (Mosaico Jogos) comprado em 30/09/2026 e movido para a lista de fora. Prioridades renumeradas:
Clank! Legacy #1, Concordia SE #2, Wondrous Creatures #3, Fate of the Fellowship #4, Agent Avenue #5,
Jisogi #6, The Game Makers #7, Quacks #8, Magical Athlete #9.

A análise de 29/09/2026 recomendava esperar e testar o Gaia Project uma segunda vez antes de comprar; a compra foi
feita de todo modo. Fica registrado o que a análise apontou, que segue valendo como referência e não como objeção:
peso 3,50 cai acima da linha de 3,35 onde os seus pesados passam de mediana 4 partidas para mediana 2, e os cinco
Euros da família Luciani que você já jogou (Marco Polo 9,3, Teotihuacan 9,0, Newton 8,9, Tzolk'in 8,4, Carnegie 8,4)
nunca tinham virado compra. Entropy é o primeiro.

Ponto de comparação útil pra depois: o teste real é quantas partidas ele acumula nos próximos meses. A mediana da
faixa acima de 3,35 é 2 partidas; Ark Nova (6) e SETI (5) são as exceções que mostram que dá pra furar essa média.

## Slay the Spire — análise registrada em 29/09/2026

Analisado a pedido depois de já ter sido removido. Os dados confirmaram a sua decisão. O achado que fecha o caso:
separando os deckbuilders por modo, os competitivos giram (Clank! 6 partidas, Dune: Imperium 5, Clank!: Catacombs 5,
SW Deckbuilding 5, Arnak 4) e os cooperativos não passaram da primeira partida — Aeon's End: War Eternal (peso 2,93,
nota 8,5, 1 partida) e Marvel Champions (peso 2,96, nota 7,5, 1 partida), nenhum dos dois comprado. São os dois
vizinhos mais próximos do Slay the Spire na coleção, com peso dentro de 0,05 do dele (2,91).
Preço da edição Grok na LudoStore em 29/09/2026: R$ 760,00 no boleto.
