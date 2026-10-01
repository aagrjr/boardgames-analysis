# AGENTS

## Propósito

Duas páginas de uma só peça, sem dependências e sem build, para decisões de compra de board game:

- **`index.html`** — wishlist ranqueada: o que comprar em seguida, com peso, duração, sobreposição
  com a coleção e veredito. A lista dos jogos fora da rodada fica no fim.
- **`designers.html`** — catálogos por designer/artista em abas: o que ainda falta das pessoas cujo
  trabalho já funciona na coleção.

Publicadas em GitHub Pages a partir de `main` na raiz: <https://aagrjr.github.io/boardgames-analysis/>.
Um push na `main` republica; o build leva menos de um minuto. As duas páginas se linkam entre si e há
autoteste exigindo que o link exista.

A **página é em inglês**; `README.md` e este arquivo ficam em pt-BR.

## Fonte de verdade dos dados pessoais

O export da coleção do BGG (`collection.csv`, baixado pelo usuário) é a fonte de notas pessoais,
partidas, `own`/`prevowned` e peso. **Sempre reconferir contra o export antes de afirmar qualquer
coisa sobre a coleção** — várias correções nesta história vieram de afirmações feitas de memória.

Campos úteis: `rating`, `numplays`, `avgweight`, `average`, `usersrated`, `rank`, `own`,
`prevowned`, `bggbestplayers`, `bggrecplayers`, `itemtype` (filtrar por `standalone`).

Atenção a dois detalhes do export:
- `own=0` **e** `prevowned=0` com partidas registradas significa **jogou na mesa de alguém e nunca
  comprou** — é diferente de "comprou e vendeu". Essa distinção já foi confundida.
- Há **linhas duplicadas** (o mesmo `objectid` aparece duas vezes, às vezes com notas diferentes).
  Deduplicar por `objectid` antes de somar partidas.
- **O export atrasa em relação às compras.** Um jogo comprado depois do último download aparece com
  `own=0`. A compra fica registrada no `EXCLUIDOS` do `index.html` com motivo começando em `owned` ou
  `already bought`; `designers.html` usa isso como fallback de posse. Foi o caso do Entropy, que
  aparecia como "removido da wishlist" sendo que tinha acabado de ser comprado.

## API do BGG

A `xmlapi2` responde **401 Unauthorized** sem sessão. O que funciona é a API interna do próprio site,
chamada de dentro de uma aba já aberta em `boardgamegeek.com`:

- `/api/geekitems?objectid=<id>&objecttype=thing&subtype=boardgame` — designers, artistas, mecânicas,
  jogadores e duração. Os créditos ficam em `item.links.boardgamedesigner` e `.boardgameartist`.
- `/api/geekitem/linkeditems?ajax=1&linkdata_index=boardgamedesigner&objectid=<pessoa>&objecttype=person&pageid=<n>&showcount=50&sort=rank&subtype=boardgamedesigner`
  — catálogo de uma pessoa. Máximo de 50 por página; paginar com `pageid`. Para artista, trocar os
  dois `boardgamedesigner` por `boardgameartist`.
- Não existe endpoint de enquete: **melhor número de jogadores só está disponível para os jogos que
  estão no export** (`bggbestplayers`). Para os demais, só dá para mostrar a faixa min–max.
- `api.geekdo.com` é bloqueado por CORS; usar caminho relativo na origem `boardgamegeek.com`.
- Páginas de jogo caem em desafio do Cloudflare. A busca (`/geeksearch.php`) funciona e já traz nota,
  número de votos e rank.

Os créditos levantados em 30/09/2026 estão em `designers.html`; se precisar refazer, é esse caminho.

## Constantes medidas na coleção — não reinventar

Tudo abaixo foi calculado do export, não estimado. São a base dos vereditos das duas páginas.

**Partidas médias por faixa de peso** (jogos com `own=1`, `standalone`):

| Peso | Jogos | Média |
|---|---|---|
| < 2,0 | 44 | 7,66 |
| 2,0–2,5 | 23 | 4,48 |
| 2,5–3,0 | 12 | 4,42 |
| 3,0–3,5 | 10 | 4,40 |
| 3,5+ | 13 | 2,54 |

**A linha dos 3,35.** Cortando os pesados em dois: de 3,00 a 3,35 a mediana é **4 partidas**; acima
de 3,35 cai para **2**, com 5 de 14 em 0–1 partida. É o corte mais nítido da coleção e o que
`designers.html` usa para classificar.

**Cooperativos têm teto próprio, por volta de 2,1.** Os sete ativos (Cross Clues 23 partidas,
Dorfromantik 19, Hanabi 10, Bomb Busters 9, ito 8, Just One 5, Sherlock Consulting Detective 3) estão
todos em peso ≤ 2,66. Todo cooperativo acima de 2,5 que foi comprado saiu com 1 ou 2 partidas, exceto
Pandemic Legacy S1, que é campanha feita para terminar.

**"Melhor com 2" só prevê partidas acima de peso 2,5.** No grupo de engine builders de natureza-ciência,
melhor-com-2 tem mediana de 6,5 partidas contra 2,0 de melhor-com-3+. **Nos jogos leves o efeito some**
(6,24 contra 6,76, medianas iguais em 4): filler de grupo é o que mais vai à mesa. Não usar
"melhor com 4" como objeção para jogo leve.

**Deckbuilder competitivo funciona; cooperativo não.** Competitivos: Clank! 6 partidas, Dune: Imperium 5,
Clank!: Catacombs 5, SW Deckbuilding 5, Arnak 4. Cooperativos: Aeon's End (peso 2,93) e Marvel Champions
(2,96), uma partida cada, nenhum comprado.

## Uma entrada por jogo em `designers.html`

**Edições alternativas do mesmo jogo** não convivem: fica uma só. Darwin's Journey exclui a
Collector's Edition; Rococo: Deluxe substitui o Rococo base; Glen More II: Chronicles substitui o
Glen More por ser reimplementação. No Kraftwagen fica a **edição mais nova** (Age of Engineering,
2024), a pedido. O **Stockpile é a exceção deliberada**: o base e a Epic Edition ficam os dois,
também a pedido.

**Expansões entram**, marcadas com a etiqueta `expansion` e o campo `exp` apontando o nome do jogo
base. O veredito delas **não passa pela linha dos 3,35** — expansão não abre uma noite de jogo nova,
aprofunda uma que já existe, então é julgada pelo base: se o base é da coleção, é 🟢; se não é,
é 🟡 com o aviso de que o base faz falta. A Fireland (peso 4,19) é o caso de teste disso.

**Edição brasileira** fica no campo `br` com o nome da editora nacional, ou ausente quando não há.
Levantado na Ludopedia em 01/10/2026: a busca do site não devolve resultado por `fetch`, mas o slug de
`/jogo/<slug>` é previsível a partir do nome em inglês (minúsculas, sem acento, não-alfanumérico vira
hífen) e acertou 58 dos 61. **Os três erros do slug não eram ausência de página**: `CO₂: Second Chance`
mora em `/jogo/co-second-chance` (o `₂` subscrito some em vez de virar `2`), `Masters of Renaissance`
precisa do subtítulo inteiro, e o `Age of Steam` base não tem página — quem tem é a
`age-of-steam-deluxe-edition`. Slug que falha pede variação antes de concluir que não há edição nacional.

Os **61 slugs foram revalidados** um a um: todos resolvem. A página não guarda a URL — `ludoSlug()`
deriva do nome, e `LUDO_FIXO` carrega só as três exceções. Há autoteste cobrindo a regra de derivação,
as três exceções e o formato da URL. A editora sai dos links `a[href*="/editora/"]` da página, com a brasileira
em primeiro. Título em português (Rá, Entropia, SETI: Agências Espaciais) confirma.

Editoras brasileiras que apareceram: Devir Brasil, Mosaico Jogos, MeepleBR Jogos, Grok Games,
Asmodee (Galápagos), Jelly Monster, Mandala Jogos, Fire on Board, Precisamente Jogos,
Vem pra Mesa Jogos, Bucaneiros Jogos e Jogo Secco. **A Ludopedia não distingue lançado de anunciado**,
então o campo afirma só que existe editora nacional listada — diferente do `brasil.s` do `index.html`,
que separa `released` de `announced` porque ali a checagem foi jogo a jogo.

**Status de wishlist não pertence a essa página.** Ela responde "o que falta deste catálogo", não "o
que você decidiu sobre isso" — um jogo descartado da wishlist continua sendo uma lacuna do catálogo, e
misturar as duas coisas já produziu uma linha errada.

## Editando os dados

Em `index.html`, os jogos estão no array `JOGOS` no topo do `<script>`, **um registro por linha**, e os
jogos fora da rodada em `EXCLUIDOS`, como `["nome", "motivo"]`. Em `designers.html`, o array `JOGOS`
tem um registro por linha com `tabs` indicando em quais abas o jogo aparece (um jogo pode estar em mais
de uma: Rococo: Deluxe é Cramer e O'Toole).

Ao editar por script, usar `assert txt.count(a) == 1` antes de substituir. Registro de `index.html`
ocupa uma linha; para remover um, cuidado com o parser — já houve uma tentativa que cortou metade do
registro porque assumiu uma linha só.

**Contagens não devem ser escritas à mão.** O subtítulo das duas páginas se conta sozinho a partir dos
dados, e há autoteste amarrando o texto renderizado ao cálculo. Um subtítulo fixo já ficou errado por
seis dias sem ninguém ver.

## Autoteste

Abrir qualquer das duas páginas com `#test` no fim da URL e olhar o console:
`Board-game checks passed` e `Designer-catalogue checks passed`.

**Rodar sempre depois de editar os dados à mão.** Dois testes de `index.html` ficaram quebrados por
dias depois de edições manuais — a ordem esperada comparava com um literal de `JSON.stringify` escrito
com espaço após as vírgulas, formato que `JSON.stringify` nunca produz.

Loop de verificação usado aqui:

```
cd board-games && python3 -m http.server 8731
# abrir http://127.0.0.1:8731/index.html#test e ler o console
pkill -f "http.server 8731"
```

O navegador cacheia: acrescentar `?v=N` na URL ao reabrir depois de editar.

## Como dar conselho nesta base

O usuário quer **recomendação com dado atrás, não enquete de opções**. O que funcionou:

- Medir na coleção dele antes de opinar; ele reverte conclusões quando o número contradiz a impressão.
- **Evidência direta sobre o jogo específico vale mais que média de categoria.** Se ele já teve e
  vendeu, isso derruba qualquer estatística de faixa.
- Separar "gostou" de "jogou". Notas 9+ com uma partida são o padrão mais comum da coleção.
- Quando ele decide contra a recomendação, registrar no README como referência e seguir — sem
  reabrir o assunto depois.
