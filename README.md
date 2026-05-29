O código implementa a mixagem usando **dois players do YouTube sobrepostos** com uma técnica de crossfade por opacidade CSS. Veja como funciona:

---

**Estrutura de dois players**

Há dois iframes do YouTube (`playerA` e `playerB`) empilhados um sobre o outro via CSS `position: absolute`. Um fica visível (`opacity: 1`, `z-index: 2`) e o outro escondido (`opacity: 0`, `z-index: 1`). A variável `activeIsA` controla qual está na frente.

---

**Como o crossfade é disparado**

A função `checkAndScheduleCrossfade()` roda a cada 500ms e monitora:
- A duração total do vídeo atual (`getDuration()`)
- O tempo atual (`getCurrentTime()`)
- O tempo restante (`duration - currentTime`)

Quando o tempo restante é menor ou igual ao `crossfadeSec` (padrão: 5s), ela dispara `performCrossfade()`.

---

**O que o crossfade faz**

1. Carrega o próximo vídeo no player **inativo** e manda tocar
2. Aplica uma transição CSS `opacity` nos dois slots com a duração definida (ex: `opacity 5s ease`)
3. O player ativo vai de `1 → 0` e o inativo vai de `0 → 1` simultaneamente
4. Após o tempo de crossfade, um `setTimeout` finaliza a troca: para o player antigo, limpa as transições e atualiza `activeIsA`

---

**Limitação importante**

O crossfade é **apenas visual** — a mistura de áudio não acontece de verdade. O YouTube não expõe controle de volume via API IFrame (o método `setVolume()` existe, mas só funciona em contextos muito específicos), então os dois áudios tocam simultaneamente durante o fade, sem um diminuindo enquanto o outro aumenta.
