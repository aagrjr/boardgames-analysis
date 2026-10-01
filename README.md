# Board Games

A small, static site for exploring board games and the people who make them.

- [Wishlist](https://aagrjr.github.io/boardgames-analysis/) — a ranked shortlist with game details and filters.
- [Designers & artists](https://aagrjr.github.io/boardgames-analysis/designers.html) — selected games by creator, with links to their catalogues.

The site uses self-contained HTML files and needs no installation or build step. Game ratings and availability are snapshots and may change. Stars and notes are saved only in the browser where they are entered.

## Expansões jogáveis, botão da Ludopedia e correção de dados BR — 01/10/2026

**Expansão**: você perguntou por que Galileo Galilei: Luna não aparecia. Porque o catálogo foi montado
filtrando `rank > 0`, e o BGG não dá rank a expansão. A SETI: Space Agencies só estava lá porque veio
da aba montada à mão, não do filtro. Varri os créditos dos dez criadores: eram 54 expansões de jogos
que você tem, todas invisíveis pelo mesmo motivo.

Entraram **dez expansões jogáveis completas**: Sea Salt & Paper Extra Salt e Extra Pepper, Azul:
Crystal Mosaic, Unconscious Mind Nightmares e Free Association, Galactic Cruise Accommodations e
Advancements, Darwin's Journey: Oceania, Splendor Duel: The Counterfeiters e Galileo Galilei: Luna.

Ficaram de fora cartas promo, tiles, medalhas e mini-expansões, a seu pedido. Tentei achar um sinal
automático pra separar "expansão completa" de "pacote de cartas" — peso, número de jogadores, duração,
contagem de votos. Nenhum separa: expansão herda os números do base. São 18 candidatas e julguei uma
a uma. O autoteste barra nome com promo/cards/tiles/pack/mini-expansion, mas a lista em si é
julgamento, não regra.

Duas correções de regra junto: expansão não passa pelo corte de 7,4 nem pelo piso de mil avaliações,
porque ela é julgada pelo jogo base — Azul: Crystal Mosaic tem 7,26 e Luna tem 159 votos. E expansão
só entra se o base for seu.

**Botão**: o link da Ludopedia virou botão e o texto sobre cada pessoa saiu. Os campos `txt`, `more`
e `source` sumiram do dado.

**Dados BR — um erro meu que ia além do Jamaica.** Você apontou que Jamaica tem edição da Galápagos e
eu tinha registrado "sem edição nacional". A causa: meu extrator lia só o primeiro grupo de editoras
visível, e a Ludopedia esconde o resto atrás do botão "+N". Se a bandeira brasileira está numa editora
escondida, eu lia como ausência.

Isso contamina os 37 "sem edição nacional" que a varredura produziu. **Virei todos os que ainda não
tinham editora de volta para "não verificado"** — treze linhas. É menos informação, mas é informação
verdadeira; afirmar que não tem edição nacional pode te fazer importar um jogo que está à venda aqui.
Frosted Blooms fica como "sem edição nacional" porque você conferiu pessoalmente.

Para refazer a varredura: ler **todas** as `.ludo-fh-cred` de editora, não só a primeira, e abrir o
"+N" antes de concluir ausência. E a Ludopedia devolve 429 por volta da 90ª requisição.
