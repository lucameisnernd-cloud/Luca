# Canvas HTML Renderer — Skill Operacional

> **Propósito:** Gerar um arquivo HTML standalone que renderiza um canvas do Cognitive Canvas
> com fidelidade visual idêntica ao app Electron. Funciona offline, sem React, sem build.
> Otimizado para leitura em iPhone/mobile.

---

## QUANDO ATIVAR

O usuário diz algo como:
- "renderiza o canvas X"
- "gera o HTML do canvas"
- "quero ver o canvas no celular"
- "CANVAS:RENDER" ou "CANVAS:HTML"

---

## FLUXO DE EXECUÇÃO

```
1. LER o arquivo JSON do canvas (vault path ou fornecido pelo usuário)
2. PARSEAR nodes[], conns[], frames[], viewport
3. APLICAR Bold Engine (10 categorias) nos bodies
4. CALCULAR posições e dimensões
5. GERAR HTML standalone com tudo inline (CSS + SVG + HTML)
6. SALVAR como arquivo .html
```

---

## FORMATO DO CANVAS JSON

```json
{
  "version": 2,
  "id": "canvas-id",
  "name": "Nome do Canvas",
  "nodes": [
    {
      "id": "n123",
      "type": "D",           // H|D|C|R|P|N|E|Q|F
      "title": "Título do Nó",
      "body": "Corpo com **bold** e $LaTeX$",
      "tags": ["#TODO", "#CALC"],
      "x": 120,
      "y": 200,
      "w": 300,              // largura (default 300)
      "h": 180,              // altura (estimada)
      "confidence": "A",     // opcional
      "version": "v0.1",     // opcional
      "frameId": "f456"      // opcional - a qual frame pertence
    }
  ],
  "conns": [
    {
      "id": "c789",
      "type": "produces",    // depends|produces|implies|equiv|contradicts|generalizes|exemplifies|pipeline
      "sourceId": "n123",
      "targetId": "n456",
      "label": ""            // opcional
    }
  ],
  "frames": [
    {
      "id": "f456",
      "title": "Título da Seção",
      "x": 80, "y": 60,
      "width": 800, "height": 600
    }
  ],
  "viewport": { "x": 0, "y": 0, "z": 1.0 }
}
```

---

## SISTEMA DE CORES — NODE_COLORS

Cada tipo de nó tem um conjunto fixo de cores:

```
┌──────┬──────────────┬─────────────────┬─────────────────┬─────────────────┬─────────────────┐
│ Type │ Nome         │ bg (light)      │ border (light)  │ darkBg          │ darkBorder      │
├──────┼──────────────┼─────────────────┼─────────────────┼─────────────────┼─────────────────┤
│  H   │ Hipótese     │ #FFF8E1         │ #F9A825         │ #2A2518         │ #FFB74D         │
│  D   │ Definição    │ #E3F2FD         │ #1976D2         │ #1A2332         │ #64B5F6         │
│  C   │ Cálculo      │ #E0F2F1         │ #00897B         │ #1A2A28         │ #4DB6AC         │
│  R   │ Resultado    │ #E8F5E9         │ #388E3C         │ #1A2A1C         │ #66BB6A         │
│  P   │ Prova        │ #E8EAF6         │ #3949AB         │ #1C1E32         │ #7986CB         │
│  N   │ Nota         │ #F5F5F5         │ #757575         │ #252530         │ #9E9E9E         │
│  E   │ Erro         │ #FFEBEE         │ #D32F2F         │ #2A1A1A         │ #EF5350         │
│  Q   │ Equação      │ #F3E5F5         │ #7B1FA2         │ #261A2E         │ #BA68C8         │
│  F   │ Frame        │ #FAFAFA         │ #9E9E9E         │ #222230         │ #757575         │
└──────┴──────────────┴─────────────────┴─────────────────┴─────────────────┴─────────────────┘
```

### Badge Colors (sempre o mesmo que border)

Badge = pill colorido no header, texto branco (#FFFFFF), fontSize 9px, fontWeight 700, padding 3px 6px, borderRadius 3px.

### Badge Labels (NODE_LABELS.short)

```
H → "HIP"    D → "DEF"    C → "CALC"    R → "RES"
P → "PRV"    N → "NOTA"   E → "ERR"     Q → "EQ"
```

---

## SISTEMA DE CORES — TAGS (TAG_STYLES)

```
┌──────────────────┬─────────────┬─────────────┬─────────────┬─────────────┐
│ Tag              │ bg (light)  │ color (lt)  │ darkBg      │ darkColor   │
├──────────────────┼─────────────┼─────────────┼─────────────┼─────────────┤
│ #IMPORTANTE      │ #FFF3E0     │ #E65100     │ #3D2B10     │ #FFB74D     │
│ #TODO            │ #E3F2FD     │ #1565C0     │ #102840     │ #64B5F6     │
│ #REVISAR         │ #FCE4EC     │ #C62828     │ #3D1525     │ #EF9A9A     │
│ #PROVA           │ #E8EAF6     │ #283593     │ #1C1E3D     │ #9FA8DA     │
│ #CALC            │ #E0F2F1     │ #00695C     │ #0D3330     │ #80CBC4     │
│ #WOLFRAM         │ #FFF8E1     │ #F57F17     │ #3D3510     │ #FFD54F     │
│ #INTUIÇÃO        │ #F3E5F5     │ #6A1B9A     │ #2D1540     │ #CE93D8     │
│ #ERRO-COMUM      │ #FFEBEE     │ #B71C1C     │ #3D1515     │ #EF9A9A     │
│ #MEMORIZAR       │ #E8F5E9     │ #2E7D32     │ #103D15     │ #81C784     │
│ #PRÉ-REQUISITO   │ #ECEFF1     │ #37474F     │ #1A2530     │ #90A4AE     │
└──────────────────┴─────────────┴─────────────┴─────────────┴─────────────┘
```

Tag rendering: fontSize 9px, fontWeight 500, padding 2px 5px, borderRadius 4px.

Tag sem match na tabela: bg #F5F5F5 (light) / #333 (dark), color #616161 (light) / #9E9E9E (dark).

Max 2 tags visíveis no header. Se mais: mostrar `+N`.

---

## SISTEMA DE CORES — EDGES (EDGE_STYLES)

```
┌──────────────┬─────────────┬─────────────┬───────────┬───────┬────────┐
│ Type         │ stroke (lt) │ darkStroke   │ dash      │ width │ symbol │
├──────────────┼─────────────┼─────────────┼───────────┼───────┼────────┤
│ depends      │ #78909C     │ #90A4AE     │ solid     │ 1.5   │ ←      │
│ produces     │ #546E7A     │ #78909C     │ solid     │ 1.5   │ →      │
│ implies      │ #37474F     │ #607D8B     │ solid     │ 1.5   │ ⟹      │
│ equiv        │ #607D8B     │ #90A4AE     │ solid     │ 1.5   │ ⟺      │
│ contradicts  │ #E53935     │ #EF5350     │ 6,4       │ 2.0   │ ⊥      │
│ generalizes  │ #5C6BC0     │ #7986CB     │ 4,3       │ 1.5   │ ⊃      │
│ exemplifies  │ #26A69A     │ #4DB6AC     │ 2,3       │ 1.5   │ ∈      │
│ pipeline     │ #42A5F5     │ #64B5F6     │ solid     │ 2.5   │ |>     │
└──────────────┴─────────────┴─────────────┴───────────┴───────┴────────┘
```

### Edge Labels

```
depends → "depende"    produces → "produz"      implies → "implica"
equiv → "equivale"     contradicts → "contradiz" generalizes → "generaliza"
exemplifies → "exemplifica"  pipeline → "pipeline"
```

---

## TIPOGRAFIA — SISTEMA COMPLETO

### Fontes Requeridas

```html
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&family=JetBrains+Mono:wght@400;500&display=swap" rel="stylesheet">
<link href="https://cdn.jsdelivr.net/npm/katex@0.16.28/dist/katex.min.css" rel="stylesheet">
<script src="https://cdn.jsdelivr.net/npm/katex@0.16.28/dist/katex.min.js"></script>
```

### Hierarquia Tipográfica

```
┌───────────────┬───────────┬──────┬────────┬─────────────────┬─────────────────┐
│ Elemento      │ Font      │ Size │ Weight │ Color (light)   │ Color (dark)    │
├───────────────┼───────────┼──────┼────────┼─────────────────┼─────────────────┤
│ Frame title   │ Inter     │ 16px │ 700    │ #111827         │ #C8C8D8         │
│ Node title    │ Inter     │ 13px │ 700    │ #111827         │ #E0E0E0         │
│ Node body     │ Inter     │ 11px │ 400    │ #6b7280         │ #8B8B9E         │
│ Body bold     │ Inter     │ 11px │ 700    │ #1a1a2e         │ #D4D4D8         │
│ Body decision │ Inter     │ 11px │ 700    │ [node border]   │ [node dkBorder] │
│ Badge text    │ Inter     │ 9px  │ 700    │ #FFFFFF          │ #FFFFFF          │
│ Tag text      │ Inter     │ 9px  │ 500    │ [tag color]     │ [tag darkColor] │
│ Footer        │ JB Mono   │ 9px  │ 400    │ #9ca3af         │ #606070         │
│ KaTeX         │ KaTeX     │ 1em  │ varies │ inherited       │ inherited       │
└───────────────┴───────────┴──────┴────────┴─────────────────┴─────────────────┘
```

---

## RENDERIZAÇÃO DE TEXTO — Bold Engine (10 Categorias)

O corpo de cada nó já vem com marcações `**bold**` e `$math$` no JSON.
O HTML precisa interpretar:

### 1. Split por padrão: `**bold**` e `$math$`

```javascript
const parts = line.split(/(\*\*[^*]+\*\*|\$[^$]+\$)/g);
```

Para cada parte:
- `$formula$` → renderizar com KaTeX inline
- `**texto**` → `<span style="font-weight:700; color:BOLD_COLOR">texto</span>`
- Se o texto bold é um "decision term" → usar `accentColor` (= border color do tipo do nó)
- Texto normal → `<span>texto</span>` com cor body (#6b7280)

### 2. Decision Terms (Cat.6) — Recebem Cor de Acento

```
comprar, vender, hold, manter, buy, sell, recomendação,
alto, médio, baixo, favorável, desfavorável, bullish, bearish,
cuidado, atenção, risco, erro, erro comum, não, não confundir, não impactam,
confirma, contradiz, invalida, verificar, checklist,
estável, crescente, decrescente, stable outlook,
projetar, calcular, descontar, somar, aplicar, comparar, alinhar,
sempre
```

Match: case-insensitive, exact word.
Se match → cor = border color do tipo do nó (ex: D → #1976D2, E → #D32F2F).

### 3. KaTeX Rendering

```javascript
katex.renderToString(formula, {
  throwOnError: false,
  displayMode: false,
  output: 'html',
  strict: false,
});
```

Inline, com `vertical-align: baseline`.

---

## ANATOMIA DO NÓ — Estrutura HTML Exata

```
┌─────────────────────────────────────────────────────┐
│ ┌─ HEADER ────────────────────────────────────────┐ │
│ │ [BADGE] Título do Nó            [TAG1] [TAG2]  │ │
│ └─────────────────────────────────────────────────┘ │
│                                                     │
│   Corpo do nó com **bold** e $math$                 │
│   Segunda linha do corpo                            │
│   **Decision term** em cor de acento                │
│                                                     │
│ ┌─ FOOTER (opcional) ────────────────────────────┐ │
│ │ [A] confiável                        v0.1      │ │
│ └─────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────┘
  ▲
  │ Borda esquerda: 3px solid [border color, 100% opacity]
  │ Outras bordas: 1px solid [border color, 30% opacity (light) / 50% opacity (dark)]
```

### CSS Exato do Nó

```css
.canvas-node {
  position: absolute;
  /* left, top, width from data */
  font-family: Inter, sans-serif;
}

.canvas-node-inner {
  background-color: /* NODE_COLORS[type].bg */;
  border-radius: 10px;
  border-top: 1px solid /* borderColor + opacity */;
  border-right: 1px solid /* borderColor + opacity */;
  border-bottom: 1px solid /* borderColor + opacity */;
  border-left: 3px solid /* borderColor FULL opacity */;
  box-shadow: 0 1px 3px rgba(0,0,0,0.08); /* light */
  /* dark: 0 2px 8px rgba(0,0,0,0.35) */
  overflow: hidden;
}

/* N-type (Nota): dashed borders, opacity 0.85 */
.canvas-node-inner.note {
  border-style: dashed;
  border-left-style: solid;
  opacity: 0.85;
}

.canvas-node-header {
  display: flex;
  align-items: center;
  gap: 6px;
  padding: 8px 12px;
  background-color: rgba(0,0,0,0.03); /* light */
  /* dark: rgba(255,255,255,0.04) */
  border-bottom: 1px solid rgba(0,0,0,0.05); /* light */
  /* dark: rgba(255,255,255,0.06) */
  min-height: 32px;
}

.canvas-node-badge {
  background-color: /* NODE_COLORS[type].badge */;
  color: #FFFFFF;
  font-size: 9px;
  font-weight: 700;
  line-height: 1;
  padding: 3px 6px;
  border-radius: 3px;
  letter-spacing: 0.5px;
  flex-shrink: 0;
}

.canvas-node-title {
  font-size: 13px;
  font-weight: 700;
  color: #111827; /* light */  /* dark: #E0E0E0 */
  line-height: 1.3;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  flex: 1;
  min-width: 0;
}

.canvas-node-body {
  padding: 10px 12px;
  font-size: 11px;
  font-weight: 400;
  color: #6b7280; /* light */  /* dark: #8B8B9E */
  line-height: 1.45;
}

.canvas-node-footer {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 6px 12px;
  border-top: 1px solid rgba(0,0,0,0.04); /* light */
  /* dark: rgba(255,255,255,0.04) */
  font-size: 9px;
  font-family: 'JetBrains Mono', monospace;
  color: #9ca3af; /* light */  /* dark: #606070 */
  line-height: 1.2;
}
```

### Larguras por Tipo

```
H: 300px    D: 300px    C: 300px    R: 300px
P: 320px    N: 280px    E: 260px    Q: 300px
```

---

## FRAMES — Estrutura Visual

```css
.canvas-frame {
  position: absolute;
  border: 2.5px dashed #DADCE0; /* light */
  /* dark: #444455 */
  border-radius: 12px;
  background: rgba(0,0,0,0.025); /* light */
  /* dark: rgba(255,255,255,0.03) */
}

.canvas-frame-title {
  position: absolute;
  top: 16px;
  left: 16px;
  font-size: 16px;
  font-weight: 700;
  color: #111827; /* light */  /* dark: #C8C8D8 */
  font-family: Inter, sans-serif;
  line-height: 1.2;
  letter-spacing: -0.01em;
}

.canvas-frame-count {
  font-size: 10px;
  font-weight: 600;
  color: #9ca3af; /* light */  /* dark: #808090 */
  background: rgba(0,0,0,0.06); /* light */
  /* dark: rgba(255,255,255,0.08) */
  padding: 2px 8px;
  border-radius: 10px;
  margin-left: 10px;
}
```

---

## EDGES — Renderização SVG

### Cálculo de Portas (getNodePorts)

Dados source(x,y,w,h) e target(x,y,w,h):
1. Calcular centros: scx = source.x + source.w/2, etc.
2. dx = tcx - scx, dy = tcy - scy
3. Se |dy| > |dx| * 0.4 → conexão VERTICAL
   - dy > 0: source bottom → target top (com offset lateral proporcional)
   - dy < 0: source top → target bottom
4. Senão → conexão HORIZONTAL
   - dx > 0: source right → target left
   - dx < 0: source left → target right

### Offset lateral (para evitar sobreposição)

```javascript
sOffset = clamp(-source.w*0.3, (tcx-scx)*0.15, source.w*0.3)
tOffset = clamp(-target.w*0.3, (scx-tcx)*0.15, target.w*0.3)
```

### Curva Bezier (createSmoothPath)

```javascript
offset = min(dist * 0.35, 120)

// Vertical:
cx1 = sx, cy1 = sy + offset * sign(dy)
cx2 = tx, cy2 = ty - offset * sign(dy)

// Horizontal:
cx1 = sx + offset * sign(dx), cy1 = sy
cx2 = tx - offset * sign(dx), cy2 = ty

path = `M ${sx} ${sy} C ${cx1} ${cy1}, ${cx2} ${cy2}, ${tx} ${ty}`
```

### Markers por Tipo

| Type | Marker no Target |
|------|-----------------|
| produces, implies, pipeline | Triângulo preenchido (seta) |
| depends | Triângulo preenchido (seta no target) |
| equiv | Diamante no meio |
| contradicts | Círculo + X vermelho no meio |
| generalizes | Triângulo outline (não preenchido) |
| exemplifies | Círculo pequeno perto do target |

### Edge Labels

Label no ponto médio: rect 72x20 com borderRadius 4, texto fontSize 9 fontWeight 600.
Fundo: rgba(255,255,255,0.94) light / rgba(30,30,46,0.94) dark.

### Opacidade das Edges

- Intra-frame (mesma frameId): opacity 0.5
- Inter-frame (frameIds diferentes): opacity 1.0
- contradicts: sempre mostra label

---

## TEMPLATE HTML COMPLETO

Gerar um HTML standalone com esta estrutura:

```html
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=3.0, user-scalable=yes">
  <title>{{CANVAS_NAME}}</title>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&family=JetBrains+Mono:wght@400;500&display=swap" rel="stylesheet">
  <link href="https://cdn.jsdelivr.net/npm/katex@0.16.28/dist/katex.min.css" rel="stylesheet">
  <script src="https://cdn.jsdelivr.net/npm/katex@0.16.28/dist/katex.min.js"></script>
  <style>
    /* === RESET & BASE === */
    * { margin: 0; padding: 0; box-sizing: border-box; }
    body {
      font-family: Inter, sans-serif;
      background: #F8F9FA; /* light */
      overflow: auto;
      -webkit-text-size-adjust: 100%;
    }

    /* === DARK MODE (prefers-color-scheme ou class="dark") === */
    body.dark { background: #0F0F17; }

    /* === CANVAS CONTAINER === */
    .canvas-viewport {
      position: relative;
      /* largura/altura calculadas para conter todos os elementos */
      margin: 20px auto;
      /* opcional: padding para mobile */
    }

    /* === MOBILE: render como lista vertical === */
    @media (max-width: 768px) {
      .canvas-viewport {
        display: flex;
        flex-direction: column;
        gap: 16px;
        padding: 16px;
        width: 100%;
      }
      .canvas-viewport .canvas-node {
        position: relative !important;
        left: auto !important;
        top: auto !important;
        width: 100% !important;
      }
      .canvas-viewport .canvas-frame {
        position: relative !important;
        left: auto !important;
        top: auto !important;
        width: 100% !important;
        height: auto !important;
        padding: 16px;
        margin-bottom: 8px;
      }
      .canvas-viewport svg.edges-layer {
        display: none; /* edges não fazem sentido em lista */
      }
    }

    /* Todos os estilos inline nos elementos, mas estes são os defaults */
    .katex { font-size: 1em; }
    .katex .katex-html { vertical-align: baseline; }
  </style>
</head>
<body>

  <!-- HEADER: nome do canvas e metadata -->
  <header style="padding:16px 20px; border-bottom:1px solid rgba(0,0,0,0.08);">
    <h1 style="font-size:18px; font-weight:700; color:#111827; font-family:Inter,sans-serif;">
      {{CANVAS_NAME}}
    </h1>
    <p style="font-size:11px; color:#9ca3af; font-family:'JetBrains Mono',monospace; margin-top:4px;">
      {{NODE_COUNT}} nós · {{CONN_COUNT}} conexões · {{FRAME_COUNT}} frames
    </p>
  </header>

  <!-- CANVAS AREA -->
  <div class="canvas-viewport" style="width:{{VIEWPORT_W}}px; height:{{VIEWPORT_H}}px; position:relative;">

    <!-- FRAMES (renderizar ANTES dos nós, z-index mais baixo) -->
    {{FRAMES_HTML}}

    <!-- SVG LAYER para edges -->
    <svg class="edges-layer"
         style="position:absolute; top:0; left:0; width:100%; height:100%; pointer-events:none; z-index:1;"
         xmlns="http://www.w3.org/2000/svg">
      {{EDGES_SVG}}
    </svg>

    <!-- NODES (z-index acima das edges) -->
    {{NODES_HTML}}

  </div>

  <!-- DARK MODE TOGGLE (opcional) -->
  <script>
    // Auto-detect dark mode
    if (window.matchMedia('(prefers-color-scheme: dark)').matches) {
      document.body.classList.add('dark');
    }

    // KaTeX rendering para elementos com data-katex
    document.querySelectorAll('[data-katex]').forEach(el => {
      try {
        katex.render(el.getAttribute('data-katex'), el, {
          throwOnError: false,
          displayMode: false,
          output: 'html',
          strict: false,
        });
      } catch(e) {
        el.textContent = el.getAttribute('data-katex');
      }
    });
  </script>

</body>
</html>
```

---

## COMO GERAR CADA FRAME

```html
<div class="canvas-frame" style="
  position: absolute;
  left: {{FRAME_X}}px;
  top: {{FRAME_Y}}px;
  width: {{FRAME_W}}px;
  height: {{FRAME_H}}px;
  border: 2.5px dashed {{FRAME_BORDER_COLOR}};
  border-radius: 12px;
  background: {{FRAME_BG}};
  z-index: 0;
">
  <div style="position:absolute; top:16px; left:16px; display:flex; align-items:center; gap:10px;">
    <span style="font-size:16px; font-weight:700; color:{{TITLE_COLOR}}; font-family:Inter,sans-serif; line-height:1.2; letter-spacing:-0.01em;">
      {{FRAME_TITLE}}
    </span>
    <span style="font-size:10px; font-weight:600; color:{{BADGE_COLOR}}; background:{{BADGE_BG}}; padding:2px 8px; border-radius:10px; font-family:Inter,sans-serif;">
      {{NODE_COUNT}} nós
    </span>
  </div>
</div>
```

---

## COMO GERAR CADA NÓ

```html
<div class="canvas-node" style="
  position: absolute;
  left: {{NODE_X}}px;
  top: {{NODE_Y}}px;
  width: {{NODE_W}}px;
  z-index: 2;
">
  <div style="
    background-color: {{BG}};
    border-radius: 10px;
    border-top: 1px solid {{BORDER_WITH_OPACITY}};
    border-right: 1px solid {{BORDER_WITH_OPACITY}};
    border-bottom: 1px solid {{BORDER_WITH_OPACITY}};
    border-left: 3px solid {{BORDER_COLOR}};
    {{BORDER_STYLE_IF_NOTE}}
    box-shadow: {{SHADOW}};
    overflow: hidden;
    font-family: Inter, sans-serif;
    {{OPACITY_IF_NOTE}}
  ">
    <!-- HEADER -->
    <div style="
      display: flex;
      align-items: center;
      gap: 6px;
      padding: 8px 12px;
      background: {{HEADER_BG}};
      border-bottom: 1px solid {{HEADER_BORDER}};
      min-height: 32px;
    ">
      <!-- Badge -->
      <span style="
        background-color: {{BADGE_COLOR}};
        color: #FFFFFF;
        font-size: 9px;
        font-weight: 700;
        line-height: 1;
        padding: 3px 6px;
        border-radius: 3px;
        letter-spacing: 0.5px;
        flex-shrink: 0;
      ">{{BADGE_LABEL}}</span>

      <!-- Title -->
      <span style="
        font-size: 13px;
        font-weight: 700;
        color: {{TITLE_COLOR}};
        line-height: 1.3;
        overflow: hidden;
        text-overflow: ellipsis;
        white-space: nowrap;
        flex: 1;
        min-width: 0;
      ">{{NODE_TITLE}}</span>

      <!-- Tags (max 2) -->
      {{TAGS_HTML}}
    </div>

    <!-- BODY -->
    <div class="canvas-node-body" style="
      padding: 10px 12px;
      font-size: 11px;
      font-weight: 400;
      color: {{BODY_COLOR}};
      line-height: 1.45;
    ">
      {{RENDERED_BODY}}
    </div>

    <!-- FOOTER (se confidence ou version existir) -->
    {{FOOTER_HTML}}
  </div>
</div>
```

---

## COMO GERAR CADA TAG

```html
<span style="
  background-color: {{TAG_BG}};
  color: {{TAG_COLOR}};
  font-size: 9px;
  font-family: Inter, sans-serif;
  font-weight: 500;
  line-height: 1;
  padding: 2px 5px;
  border-radius: 4px;
  white-space: nowrap;
  display: inline-flex;
  align-items: center;
">{{TAG_TEXT}}</span>
```

---

## COMO GERAR CADA EDGE (SVG)

```xml
<g>
  <!-- Visible path -->
  <path d="{{PATH_D}}"
        fill="none"
        stroke="{{STROKE_COLOR}}"
        stroke-width="{{WIDTH}}"
        stroke-dasharray="{{DASH}}"
        stroke-linecap="round"
        stroke-linejoin="round"
        opacity="{{OPACITY}}" />

  <!-- Marker (arrowhead, X, diamond, etc.) -->
  {{MARKER_SVG}}

  <!-- Label (para contradicts sempre, outros opcionalmente) -->
  {{LABEL_SVG}}
</g>
```

### Label SVG

```xml
<rect x="{{MX-36}}" y="{{MY-11}}" width="72" height="20" rx="4"
      fill="rgba(255,255,255,0.94)" stroke="{{STROKE_COLOR}}" stroke-width="0.7" />
<text x="{{MX}}" y="{{MY+3}}" text-anchor="middle"
      font-size="9" font-family="Inter,sans-serif" fill="{{STROKE_COLOR}}" font-weight="600">
  {{LABEL_TEXT}}
</text>
```

### Marker: Arrowhead (produces, implies, pipeline, depends)

```xml
<!-- Calcular ângulo final da curva bezier -->
<!-- size = 7 + strokeWidth * 0.5 -->
<polygon points="{{P1X}},{{P1Y}} {{P2X}},{{P2Y}} {{P3X}},{{P3Y}}"
         fill="{{STROKE_COLOR}}" opacity="{{OPACITY}}" />
```

### Marker: Contradicts (X no meio)

```xml
<circle cx="{{MX}}" cy="{{MY}}" r="10" fill="{{COLOR}}" opacity="0.12" />
<circle cx="{{MX}}" cy="{{MY}}" r="8" fill="none" stroke="{{COLOR}}" stroke-width="1" opacity="0.4" />
<line x1="{{MX-6}}" y1="{{MY-6}}" x2="{{MX+6}}" y2="{{MY+6}}"
      stroke="{{COLOR}}" stroke-width="2.5" stroke-linecap="round" />
<line x1="{{MX+6}}" y1="{{MY-6}}" x2="{{MX-6}}" y2="{{MY+6}}"
      stroke="{{COLOR}}" stroke-width="2.5" stroke-linecap="round" />
```

### Marker: Equiv (diamante no meio)

```xml
<polygon points="{{MX}},{{MY-5}} {{MX+5}},{{MY}} {{MX}},{{MY+5}} {{MX-5}},{{MY}}"
         fill="{{COLOR}}" opacity="{{OPACITY}}" />
```

### Marker: Generalizes (triângulo outline)

```xml
<polygon points="..." fill="none" stroke="{{COLOR}}" stroke-width="1.5" opacity="{{OPACITY}}" />
```

### Marker: Exemplifies (círculo pequeno)

```xml
<circle cx="{{ECX}}" cy="{{ECY}}" r="3.5" fill="{{COLOR}}" opacity="{{OPACITY}}" />
```

---

## RENDERIZAÇÃO DO BODY — Algoritmo Completo

Para cada nó, processar `node.body` assim:

```javascript
function renderBody(text, nodeType, isDark) {
  const colors = NODE_COLORS[nodeType];
  const bodyColor = isDark ? '#8B8B9E' : '#6b7280';
  const boldColor = isDark ? '#D4D4D8' : '#1a1a2e';
  const accentColor = isDark ? colors.darkBorder : colors.border;

  let html = '';
  const lines = text.split('\n');

  lines.forEach((line, i) => {
    if (i > 0) html += '<br>';

    const parts = line.split(/(\*\*[^*]+\*\*|\$[^$]+\$)/g);

    parts.forEach(part => {
      if (part.startsWith('$') && part.endsWith('$') && part.length > 2) {
        // KaTeX
        const formula = part.slice(1, -1);
        html += `<span data-katex="${escapeHtml(formula)}" style="display:inline; vertical-align:baseline;"></span>`;
      } else if (part.startsWith('**') && part.endsWith('**')) {
        // Bold
        const inner = part.slice(2, -2);
        const isDecision = DECISION_TERMS.has(inner.toLowerCase().trim());
        const color = isDecision ? accentColor : boldColor;
        html += `<span style="font-weight:700; color:${color}">${escapeHtml(inner)}</span>`;
      } else if (part) {
        html += `<span>${escapeHtml(part)}</span>`;
      }
    });
  });

  return html;
}
```

---

## VIEWPORT: NORMALIZAÇÃO PARA HTML

O canvas usa coordenadas absolutas (x, y podem ser negativos).
Para o HTML, normalizar:

```javascript
// 1. Encontrar bounding box de TODOS os elementos (nodes + frames)
const allX = [...nodes.map(n => n.x), ...frames.map(f => f.x)];
const allY = [...nodes.map(n => n.y), ...frames.map(f => f.y)];
const allXEnd = [...nodes.map(n => n.x + (n.w || 300)), ...frames.map(f => f.x + f.width)];
const allYEnd = [...nodes.map(n => n.y + (n.h || 200)), ...frames.map(f => f.y + f.height)];

const minX = Math.min(...allX);
const minY = Math.min(...allY);
const maxX = Math.max(...allXEnd);
const maxY = Math.max(...allYEnd);

// 2. Offset para que tudo comece em (20, 20)
const offsetX = -minX + 20;
const offsetY = -minY + 20;

// 3. Aplicar a cada elemento
node.htmlX = node.x + offsetX;
node.htmlY = node.y + offsetY;
frame.htmlX = frame.x + offsetX;
frame.htmlY = frame.y + offsetY;

// 4. Viewport total
const viewportW = maxX - minX + 40;  // 20px padding each side
const viewportH = maxY - minY + 40;
```

---

## MODO MOBILE — Lista Vertical

Quando o viewport for < 768px (celular), o CSS media query transforma tudo em lista:
- Nodes ficam `position: relative`, largura 100%
- Frames ficam separadores visuais
- Edges SVG são escondidos
- Cada frame vira um "grupo" com título acima dos seus nós

A ordenação dos nós na lista segue:
1. Agrupar por frameId
2. Dentro de cada frame, ordenar por y (top to bottom)
3. Nós sem frame vão por último

---

## CHECKLIST DE QUALIDADE

Antes de entregar o HTML, verificar:

- [ ] Todas as fontes carregam (Inter, JetBrains Mono, KaTeX)
- [ ] Cores dos nós correspondem exatamente à tabela NODE_COLORS
- [ ] Badge label correto por tipo (DEF, CALC, ERR, etc.)
- [ ] Bold text usa cor #1a1a2e (light) / #D4D4D8 (dark)
- [ ] Decision terms usam cor de acento do tipo do nó
- [ ] KaTeX renderiza inline corretamente
- [ ] Tags com cores da tabela TAG_STYLES
- [ ] Borda esquerda 3px solid, outras 1px com opacity
- [ ] N-type: dashed borders, opacity 0.85
- [ ] Frames: 2.5px dashed #DADCE0, borderRadius 12px
- [ ] Edges: curvas bezier suaves, markers corretos por tipo
- [ ] contradicts: sempre com label + X vermelho
- [ ] Viewport normalizado (sem coords negativas no HTML)
- [ ] Mobile: lista vertical funcional
- [ ] Dark mode: cores corretas via prefers-color-scheme

---

## EXEMPLO DE USO

**Input do Luca:** "renderiza o canvas al-canvas-0"

**Ação:**
1. Ler `/Users/lucameisner/CognitiveCanvas/al-canvas-0.json`
2. Parsear 9 nodes, 5 conns, 2 frames
3. Normalizar coordenadas
4. Gerar HTML com todos os estilos inline
5. Salvar como `al-canvas-0.html`
6. Retornar: "HTML gerado com 9 nós, 5 conexões, 2 frames. Abre no browser."

---

## VAULT PATH

Local dos canvas JSON: `/Users/lucameisner/CognitiveCanvas/`
Pattern: `*.json`

Para listar canvases disponíveis:
```bash
ls /Users/lucameisner/CognitiveCanvas/*.json
```
