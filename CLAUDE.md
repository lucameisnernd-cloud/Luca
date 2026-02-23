# Luca Cloud Environment — Claude Code Instructions

## Skills Disponíveis

### Canvas HTML Renderer (`skills/canvas-html-renderer/SKILL.md`)

Gera um HTML standalone que renderiza um canvas do Cognitive Canvas com fidelidade visual pixel-perfect.

**Trigger:** Quando Luca pedir para renderizar/visualizar um canvas.

**Como usar:**
1. Ler o SKILL.md para entender a arquitetura completa
2. Receber o JSON do canvas (Luca vai colar ou indicar qual canvas)
3. Seguir o template e as regras de estilo do SKILL.md para gerar o HTML
4. Salvar o resultado como `.html`

**O SKILL.md contém:**
- Sistema de cores completo (9 tipos de nó, 10 tags, 8 tipos de edge)
- Tipografia exata (Inter, JetBrains Mono, KaTeX)
- Anatomia do nó (header, badge, title, body, footer)
- Bold Engine (10 categorias de ênfase)
- Decision Terms (palavras com cor de acento)
- Edge rendering (curvas bezier, markers SVG)
- Frame rendering
- Normalização de viewport
- Template HTML completo
- Modo mobile (lista vertical para iPhone)
