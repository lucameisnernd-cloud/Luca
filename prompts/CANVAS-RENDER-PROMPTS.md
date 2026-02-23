# Canvas Render Prompts — Álgebra Linear (AL-0 a AL-6)

> Copie o prompt do canvas desejado e cole no Claude Code (iPhone).
> Cada prompt é autossuficiente: lê a skill, lê o JSON, gera o HTML.

---

## AL-0 — Mapa Panorâmico

```
Lê o arquivo skills/canvas-html-renderer/SKILL.md para entender a spec completa de como renderizar um canvas. Depois lê canvases/al-canvas-0.json. Gera um arquivo HTML standalone chamado al-0.html seguindo EXATAMENTE a spec da skill. Este canvas tem:

- 9 nós (7 N-type com #MAPA e #RUBAO, mais narrativa completa com KaTeX)
- 5 conexões (cadeia produces: AL-1→AL-2→AL-3→AL-4→AL-5→AL-6)
- 2 frames: "Mapa Panorâmico — Álgebra Linear" e "§16 — Narrativa Completa"
- Contém KaTeX: $A = U\Sigma V^T$, $Ax = b$
- Layout: coluna vertical simples, todos os nós width 280px (N-type)
- Notas (N-type): borda dashed, opacity 0.85, cor cinza (#757575)

Atenção especial:
- Decision terms em bold com cor de acento (ex: **Rubão** em nó N = cor #757575)
- Tags #RUBAO e #MAPA precisam de estilo correto (se não estiverem na TAG_STYLES predefinida, usar fallback: bg #F5F5F5, color #616161)
- O mobile mode deve funcionar como lista vertical
- Footer: mostrar confidence e version quando existirem

Salva como al-0.html na raiz do repo.
```

---

## AL-1 — Fundação (EV, Base, Dimensão)

```
Lê o arquivo skills/canvas-html-renderer/SKILL.md para entender a spec completa. Depois lê canvases/al-canvas-1.json. Gera um HTML standalone chamado al-1.html.

Este canvas tem:
- 17 nós (mix de H, D, R, N tipos)
- 14 conexões (produces, depends, includes)
- 3 frames: "§0 — Por Que a Álgebra Linear Existe", "§1 — Espaços Vetoriais", "§11 — Espaços Vetoriais Abstratos"
- KaTeX pesado: axiomas com $f(ax+by) = af(x)+bf(y)$, definição de EV com 8 axiomas, combinação linear, span, independência linear $\alpha_1 v_1 + \cdots + \alpha_n v_n = \vec{0}$, soma direta $W_1 \oplus W_2$
- Tipos importantes:
  - H (Hipótese, amarelo #F9A825): "§0 Axioma Central", "Problema Central"
  - D (Definição, azul #1976D2): EV, Subespaço, CL/Geração/Independência, Base/Dimensão, Corpo K, Soma Direta, EVs Abstratos
  - R (Resultado, verde #388E3C): Teorema da Dimensão
  - N (Nota, cinza #757575): Rubões e referências

Nós D (Definição) devem ter:
- Badge "DEF" azul (#1976D2)
- Background #E3F2FD
- Borda esquerda 3px solid #1976D2
- Bold text: #1a1a2e, decision terms em cor #1976D2

Nós H (Hipótese):
- Badge "HIP" amarelo (#F9A825)
- Background #FFF8E1

Tags: #FUNDAMENTAL (fallback style), #RUBAO (fallback), #PRÉ-REQUISITO (tem style na tabela)

O frame "§1 — Espaços Vetoriais" é largo (2470px) com nós em 3+ colunas.
No mobile, tudo deve virar lista vertical ordenada por frame.

Salva como al-1.html.
```

---

## AL-2 — Ação (TL, Matrizes)

```
Lê skills/canvas-html-renderer/SKILL.md e depois canvases/al-canvas-2.json. Gera al-2.html.

Canvas complexo:
- 22 nós, 17 conexões, 3 frames
- Frames: "§2 — Transformações Lineares" (1770px wide), "§3 — Operações com Matrizes" (2460px wide!), "§10 — Mudança de Base"
- Tipos: D, Q, R, N, E, C
- KaTeX pesado: matrizes, kernel/imagem, rank-nullity $\dim(\ker T) + \dim(\text{Im}\, T) = \dim(V)$, produto matricial, transposta, inversa

Nós especiais:
- Q (Equação, roxo #7B1FA2): "Matriz de uma Transformação Linear" com notação matricial KaTeX
- E (Erro, vermelho #D32F2F): "Erro: (AB)⁻¹ = A⁻¹B⁻¹" — Badge "ERR", bg #FFEBEE, tag #ERRO-COMUM
- C (Cálculo, teal #00897B): "Exemplo: Produto AB ≠ BA" com matrizes KaTeX, tag #CALC
- R (Resultado): "Teorema do Núcleo-Imagem", "Equivalências de Invertibilidade (TFAE)"

Conexões includes:
- implies (→ com ⟹): ker+Im → Rank-Nullity
- produces (→): cadeia principal de definições
- depends (←): rubões contextualizam definições

O erro E precisa da borda vermelha e tag #ERRO-COMUM com style da tabela.
Contradicts connections: nenhuma neste canvas.

Salva como al-2.html.
```

---

## AL-3 — Análise (Determinante, Sistemas)

```
Lê skills/canvas-html-renderer/SKILL.md e canvases/al-canvas-3.json. Gera al-3.html.

Canvas mais rico em conexões:
- 22 nós, 22 conexões (!), 2 frames
- Frames: "§4 — Determinante" (2100px wide), "§5 — Sistemas Lineares" (1420px)
- Tem 2 nós E (Erro): "det(A+B) = det(A) + det(B)" e "det(A)=0 implica A=0"
- Tem 2 conexões contradicts (vermelho dashed com X no meio!)
- KaTeX intenso: determinantes via Leibniz $\sum_{\sigma \in S_n}$, Sarrus 3×3, cofatores, adjugada, Cramer

Conexões especiais que PRECISAM funcionar:
- contradicts: E → D/R (traço vermelho #E53935, dashed 6,4, com X-marker no ponto médio)
- implies: Rouché-Capelli → Estrutura das Soluções
- produces: cadeia principal det → cofator → expansão → adjugada → inversa → Cramer

Nós por tipo:
- D (azul): Determinante, Cofator, Adjugada, Sistema Ax=b, Forma Escalonada, Permutação
- Q (roxo): Expansão por Cofatores, Inversa via Adjugada
- R (verde): Propriedades do Det, Regra de Cramer, Eliminação de Gauss, Rouché-Capelli, Estrutura das Soluções
- C (teal): Cálculo det 3×3 Sarrus
- E (vermelho): 2 erros comuns
- N (cinza): Rubões e referências

Tags importantes: #CALC, #ERRO-COMUM, #TEOREMA (fallback), #FUNDAMENTAL (fallback), #PRÉ-REQUISITO

Salva como al-3.html.
```

---

## AL-4 — Geometria (PI, Ortogonalidade)

```
Lê skills/canvas-html-renderer/SKILL.md e canvases/al-canvas-4.json. Gera al-4.html.

Canvas mais compacto:
- 12 nós, 11 conexões, 2 frames
- Frames: "§6 — Produto Interno" (1070px), "§7 — Ortogonalidade" (1070px)
- KaTeX elegante: $\langle u, v \rangle$, Cauchy-Schwarz $|\langle u, v \rangle| \leq \|u\| \cdot \|v\|$, Gram-Schmidt $u_k = v_k - \sum_{j=1}^{k-1} \langle v_k, e_j \rangle e_j$, projeção ortogonal, delta de Kronecker $\delta_{ij}$

Nós por tipo:
- D (azul): Produto Interno (nó grande, 432px alto), Ortogonalidade, BON, Norma
- R (verde): Cauchy-Schwarz, Gram-Schmidt
- Q (roxo): Projeção Ortogonal (com fórmula da matriz P = A(AᵀA)⁻¹Aᵀ)
- N (cinza): 5 Rubões + 1 referência

Conexões:
- implies: PI → Cauchy-Schwarz
- produces: PI → Ortogonalidade → Gram-Schmidt → Projeção
- depends: Rubões contextualizam

Este canvas é visualmente limpo — 2 colunas principais (D|N à esquerda, D|R|Q à direita).

Salva como al-4.html.
```

---

## AL-5 — Espectro (Autovalores, Diag.)

```
Lê skills/canvas-html-renderer/SKILL.md e canvases/al-canvas-5.json. Gera al-5.html.

Canvas com 3 frames escalonados:
- 18 nós, 16 conexões, 3 frames
- Frames: "§8 — Autovalores e Autovetores" (1400px), "§9 — Diagonalização" (1050px), "§12 — Formas Quadráticas" (730px)
- KaTeX crucial: $Av = \lambda v$ (autovalor), polinômio característico $\det(A - \lambda I) = 0$, diagonalização $A = PDP^{-1}$, espectral $A = Q\Lambda Q^T$, formas quadráticas $Q(x) = x^T A x$

Nós especiais:
- 2 nós E (Erro vermelho): "Autovalores são aditivos" e "Toda matriz é diagonalizável"
- 1 conexão contradicts: E → D (Autovalores) com X vermelho
- 1 conexão exemplifies: Cálculo → Diagonalização (marcador: círculo pequeno)
- 1 conexão generalizes: Diagonalização → Espectral (marcador: triângulo outline)
- Múltiplas connexões implies

Tipos:
- D: Autovalores, Multiplicidade, Diagonalização, Forma Quadrática, Assinatura
- Q: Polinômio Característico
- R: Cayley-Hamilton, Espectral, Classificação por Autovalores, Sylvester
- C: Exemplo diagonalizar [[4,1],[2,3]]
- E: 2 erros

Tags: #CALC, #ERRO-COMUM, #TEOREMA, #FUNDAMENTAL, #RUBAO, #PRÉ-REQUISITO

Importante: o nó C (Cálculo, teal) tem KaTeX com matrizes 2x2 e passos numerados.

Salva como al-5.html.
```

---

## AL-6 — Arsenal (Decomposições, MQ)

```
Lê skills/canvas-html-renderer/SKILL.md e canvases/al-canvas-6.json. Gera al-6.html.

Canvas maior — o grand finale:
- 23 nós, 21 conexões, 3 frames
- Frames: "§13 — Decomposições Matriciais" (1770×1572px - GRANDE), "§14 — Mínimos Quadrados" (720×912px), "§15 — Traço & Propriedades Globais" (720×508px)
- KaTeX de decomposições: $A = LU$, $A = LL^T$ (Cholesky), $A = QR$, $A = U\Sigma V^T$ (SVD), pseudo-inversa $A^+$, equações normais $A^T A \hat{x} = A^T b$
- Custo computacional nos bodies: "$\frac{2n^3}{3}$ operações"

Nós por tipo:
- D (azul): LU, Cholesky, QR, Valores Singulares, Equações Normais, Traço
- R (verde): Teorema SVD (3 tags: #TEOREMA #FUNDAMENTAL #MEMORIZAR!), Eckart-Young, Mínimos Quadrados (3 tags também)
- Q (roxo): Pseudo-Inversa de Moore-Penrose
- N (cinza): 10 nós (5 Rubões + 5 referências ← Ref)

Especial:
- #MEMORIZAR tag (tem style: bg #E8F5E9, color #2E7D32)
- O nó "Teorema SVD" tem 3 tags — mostrar max 2 + "+1"
- 1 conexão generalizes: Cholesky → LU (triângulo outline, dashed)
- Nós ← Ref são mini-notas cinza com #PRÉ-REQUISITO

O frame §13 é enorme (1770×1572) com nós espalhados em grid:
- Coluna esquerda: Rubões + Refs (N-type, 280px)
- Centro: LU, Cholesky, QR em linha
- Direita: Valores Singulares, SVD, Eckart-Young

Salva como al-6.html.
```

---

## DICA: Prompt Rápido (todos de uma vez)

```
Lê skills/canvas-html-renderer/SKILL.md. Depois, para cada arquivo em canvases/ (al-canvas-0.json até al-canvas-6.json), gera um HTML standalone seguindo a spec da skill. Salva cada um como al-0.html até al-6.html na raiz do repo. Inclui dark mode automático via prefers-color-scheme e modo mobile com lista vertical para telas < 768px.
```
