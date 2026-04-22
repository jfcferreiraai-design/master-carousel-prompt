---
name: instagram-carrossel
description: >
  Creates high-quality Instagram carousels as swipeable HTML previews with
  export-ready slides (1080×1350px PNG). Handles the full workflow: brand
  setup, slide copy, visual design system (colors, fonts, components), HTML
  generation, and Playwright-based export. Use this skill whenever the user
  asks to create, design, or generate an Instagram carousel, carrossel,
  slides para Instagram, or any Instagram multi-image post — even if they
  don't explicitly say "carousel" or "skill". Also trigger for requests to
  "create a post with multiple slides", "fazer carrossel", or "exportar slides
  para o Instagram".
---

# Gerador de Carrossel para Instagram

Produz carrosséis em HTML totalmente independentes e deslizáveis, onde cada slide já está pronto para exportação como PNG de 1080×1350px para o Instagram.

---

## Etapa 1: Levantar Informações da Marca

Antes de gerar qualquer coisa, pergunte ao usuário (caso ainda não tenha informado):

1. **Nome da marca** — aparece no primeiro e no último slide
2. **@ do Instagram** — exibido no cabeçalho do frame
3. **Cor principal da marca** — código hex ou descrição para Claude escolher
4. **Logotipo** — caminho SVG, inicial da marca, ou pular
5. **Preferência de fonte** — consultar a tabela de tipografia abaixo ou Google Fonts específica
6. **Tom de voz** — profissional, descontraído, divertido, impactante, minimalista, etc.
7. **Imagens** — foto de perfil, prints, fotos de produto, etc.
8. **Idioma dos slides** — padrão: **Português (BR)** salvo indicação contrária
9. **Formato do carrossel** — padrão (7 slides) ou sequência alternativa (ver seção de sequências)

Caso o usuário forneça URL do site ou materiais visuais da marca, extraia cores e estilo a partir deles.

Se o usuário disser "faz um carrossel sobre X" sem detalhar a marca, pergunte antes de criar. Não assuma padrões.

---

## Lidando com Imagens do Usuário

**Esta seção vale desde a primeira geração do HTML — não apenas na exportação.**

Quando o usuário fornecer o caminho de um arquivo de imagem (ex.: `/home/user/gestante.png`, `/mnt/user-data/uploads/foto.jpg`):

### ⚠️ Regras Invioláveis

1. **NUNCA use caminhos relativos** (`gestante.png`) — quebra em qualquer contexto de navegador fora da pasta exata do HTML.
2. **NUNCA use `background: url(filepath)`** — gera strings base64 inline de 1.5MB+ que travam o parser do browser.
3. **SEMPRE incorpore como `data:` URI em base64** — funciona no preview, na exportação e em qualquer ambiente.
4. **SEMPRE gere o HTML via Python** (`Path.write_text()`) — heredocs em shell interpolam `$` e crases, corrompendo as strings base64.

### Passo a passo: incorporar uma imagem

```bash
# 1. Verifique o formato real (a extensão pode mentir)
file /path/to/image.png
```

```python
import base64
from pathlib import Path

# 2. Leia e codifique
img_path = Path("/path/to/image.png")
# Use "image/jpeg" se o comando `file` indicar JPEG, senão "image/png"
mime = "image/jpeg"  # ou "image/png"
b64 = base64.b64encode(img_path.read_bytes()).decode()
data_uri = f"data:{mime};base64,{b64}"

# 3. Injete no template HTML como variável Python — nunca via shell
html = f"""
<div style="position:relative;width:100%;height:100%;">
  <img src="{data_uri}"
       style="position:absolute;inset:0;width:100%;height:100%;object-fit:cover;z-index:0;">
  <div style="position:absolute;inset:0;background:rgba(255,255,255,0.35);z-index:1;"></div>
  <!-- conteúdo do slide aqui, z-index:2 -->
</div>
"""

Path("/home/claude/carousel.html").write_text(html, encoding="utf-8")
```

### Imagem como fundo de slide (caso mais frequente)

```html
<!-- Dentro da div do slide, antes de qualquer conteúdo -->
<img src="{data_uri}"
     style="position:absolute;inset:0;width:100%;height:100%;object-fit:cover;z-index:0;">
<!-- Sobreposição semi-transparente para manter o texto legível -->
<div style="position:absolute;inset:0;background:rgba(255,255,255,0.35);z-index:1;"></div>
<!-- Todo conteúdo do slide precisa ter z-index:2 ou maior -->
```

Para slides escuros, troque para `rgba(0,0,0,0.45)` na sobreposição.

### Erros frequentes com imagens

| Erro | O que acontece | Solução |
|------|---------------|---------|
| `<img src="gestante.png">` | Imagem quebrada — caminho relativo só funciona se HTML e imagem estão na mesma pasta | Sempre use `data:` URI em base64 |
| `background: url('data:...')` inline com 1.5MB de base64 | Parser do navegador trava, contexto de 1.3M tokens | Use tag `<img>` com `object-fit:cover` |
| Gerar HTML via `echo` ou heredoc no shell | Caracteres `$` e crases no base64 são interpolados e corrompem a string | Sempre use Python com `Path.write_text()` |
| Assumir que extensão `.png` = formato PNG | O arquivo pode ser JPEG; tipo MIME errado quebra a renderização | Rode o comando `file` para detectar o formato real |

---

## Etapa 2: Montar o Sistema de Cores

A partir da **única cor principal** fornecida pelo usuário, derive a paleta completa de 6 tokens:

```
BRAND_PRIMARY   = {cor do usuário}                    // Destaque principal — barra de progresso, ícones, tags
BRAND_LIGHT     = {primary clareada ~20%}             // Destaque secundário — tags em fundo escuro, pills
BRAND_DARK      = {primary escurecida ~30%}           // Texto de CTA, âncora de gradiente
LIGHT_BG        = {off-white quente ou frio}          // Fundo de slides claros (nunca #fff puro)
LIGHT_BORDER    = {levemente mais escuro que LIGHT_BG}// Divisores em slides claros
DARK_BG         = {quase-preto com matiz da marca}    // Fundo de slides escuros
```

**Regras de derivação:**
- LIGHT_BG: off-white com nuance que complementa a cor principal (quente → creme, fria → cinza-branco)
- DARK_BG: quase-preto com leve matiz da marca (quente → #1A1918, fria → #0F172A)
- LIGHT_BORDER: sempre ~1 tom abaixo do LIGHT_BG
- Gradiente da marca: `linear-gradient(165deg, BRAND_DARK 0%, BRAND_PRIMARY 50%, BRAND_LIGHT 100%)`

---

## Etapa 3: Definir a Tipografia

Com base na preferência do usuário, selecione uma **fonte de título** e uma **fonte de corpo** do Google Fonts.

| Estilo | Fonte de Título | Fonte de Corpo |
|--------|----------------|----------------|
| Editorial / premium | Playfair Display | DM Sans |
| Moderno / limpo | Plus Jakarta Sans (700) | Plus Jakarta Sans (400) |
| Acolhedor / acessível | Lora | Nunito Sans |
| Técnico / afiado | Space Grotesk | Space Grotesk |
| Expressivo / marcante | Fraunces | Outfit |
| Clássico / confiável | Libre Baskerville | Work Sans |
| Arredondado / amigável | Bricolage Grotesque | Bricolage Grotesque |

**Escala de tamanhos (fixa para todas as marcas):**
- Títulos: 28–34px, weight 600, letter-spacing -0.3 a -0.5px, line-height 1.1–1.15
- Corpo: 14px, weight 400, line-height 1.5–1.55
- Tags/rótulos: 10px, weight 600, letter-spacing 2px, caixa alta
- Números de etapa: fonte de título, 26px, weight 300
- Texto pequeno: 11–12px

Aplique via classes CSS `.serif` (fonte de título) e `.sans` (fonte de corpo) em todos os slides.

---

## Slide 1 — Regras de Gancho

O primeiro slide precisa travar o scroll em menos de 1 segundo. Priorize estes formatos:

| Formato do gancho | Exemplo |
|---|---|
| Afirmação polêmica | "Você está usando IA errado" |
| Número + benefício | "7 ferramentas que substituem seu designer" |
| Pergunta que incomoda | "Por que seus carrosséis têm 0 salvamentos?" |
| Resultado concreto | "Esse post gerou 4.200 seguidores em 3 dias" |
| Inversão de expectativa | "Mais esforço no design = menos alcance" |

**Diretrizes:**
- Jamais comece com o nome da marca como título
- Sempre que possível, inclua prova visual no Slide 1 (print, resultado, número real)
- O gancho deve prometer valor que os slides seguintes entregam

---

## Sequências de Slides

### Padrão (7 slides — sequência default)

| # | Tipo | Fundo | Função |
|---|------|-------|--------|
| 1 | Hero | LIGHT_BG | Gancho — frase de impacto, logo, marca d'água opcional |
| 2 | Problema | DARK_BG | Dor — o que está quebrado, frustrante ou ultrapassado |
| 3 | Solução | Gradiente da marca | A resposta — o que resolve, box de citação/prompt opcional |
| 4 | Features | LIGHT_BG | O que você recebe — lista de funcionalidades com ícones |
| 5 | Detalhes | DARK_BG | Profundidade — personalização, specs, diferenciais |
| 6 | Como funciona | LIGHT_BG | Passos — fluxo numerado ou processo |
| 7 | CTA | Gradiente da marca | Chamada para ação — logo, tagline, botão CTA. **Sem seta. Barra de progresso cheia.** |

### Listicle (5–10 slides)

| # | Tipo | Fundo |
|---|------|-------|
| 1 | Hero | LIGHT_BG |
| 2–N | Item N | Alternando LIGHT/DARK |
| Último | CTA | Gradiente da marca |

Ideal para: "X ferramentas", "X erros", "X dicas"

### Tutorial (7 slides)

| # | Tipo | Fundo |
|---|------|-------|
| 1 | Hero | LIGHT_BG |
| 2 | Contexto / Por quê | DARK_BG |
| 3–5 | Passo 1, 2, 3 | Alternando |
| 6 | Resultado esperado | DARK_BG |
| 7 | CTA | Gradiente da marca |

### Comparação (5 slides)

| # | Tipo | Fundo |
|---|------|-------|
| 1 | Hero (o que será comparado) | LIGHT_BG |
| 2 | Opção A | LIGHT_BG |
| 3 | Opção B | DARK_BG |
| 4 | Veredicto | Gradiente da marca |
| 5 | CTA | DARK_BG |

**Regras gerais para todas as sequências:**
- Abra com gancho — o primeiro slide existe para parar o scroll
- Encerre o CTA com gradiente da marca — sem seta, barra de progresso em 100%
- Alterne fundos claros e escuros para criar ritmo visual
- Adapte a sequência ao tema — nem todo carrossel precisa de todos os slides

---

## Arquitetura do Slide

### Formato
- Proporção: **4:5** (padrão de carrossel do Instagram)
- Cada slide é autossuficiente — todos os elementos de UI fazem parte da imagem
- Fundos LIGHT_BG e DARK_BG se alternam para criar ritmo visual

### Elementos Obrigatórios em Todo Slide

#### 1. Barra de Progresso (base de todos os slides)

Indica a posição no carrossel. Preenche conforme o usuário desliza.

- Posição: absolute na base, largura total, padding horizontal 28px, padding inferior 20px
- Trilha: 3px de altura, bordas arredondadas
- Preenchimento: `((slideIndex + 1) / totalSlides) * 100%`
- Slides claros: trilha `rgba(0,0,0,0.08)`, preenchimento `BRAND_PRIMARY`, contador `rgba(0,0,0,0.3)`
- Slides escuros: trilha `rgba(255,255,255,0.12)`, preenchimento `#fff`, contador `rgba(255,255,255,0.4)`
- Rótulo ao lado da barra: formato "1/7", 11px, weight 500

```javascript
function progressBar(index, total, isLightSlide) {
  const pct = ((index + 1) / total) * 100;
  const trackColor = isLightSlide ? 'rgba(0,0,0,0.08)' : 'rgba(255,255,255,0.12)';
  const fillColor = isLightSlide ? BRAND_PRIMARY : '#fff'; // substitua pelo valor hex real de BRAND_PRIMARY
  const labelColor = isLightSlide ? 'rgba(0,0,0,0.3)' : 'rgba(255,255,255,0.4)';
  return `<div style="position:absolute;bottom:0;left:0;right:0;padding:16px 28px 20px;z-index:10;display:flex;align-items:center;gap:10px;">
    <div style="flex:1;height:3px;background:${trackColor};border-radius:2px;overflow:hidden;">
      <div style="height:100%;width:${pct}%;background:${fillColor};border-radius:2px;"></div>
    </div>
    <span style="font-size:11px;color:${labelColor};font-weight:500;">${index + 1}/${total}</span>
  </div>`;
}
```

⚠️ **Atenção:** Substitua sempre `BRAND_PRIMARY` pelo valor hex real antes de renderizar. Nunca deixe como nome de variável no HTML final.

#### 2. Seta de Deslizar (borda direita — em todos os slides EXCETO o último)

Chevron discreto que incentiva o usuário a continuar deslizando. Removido no último slide.

- Posição: absolute à direita, altura total, 48px de largura
- Fundo: gradiente de transparente → tom sutil
- Chevron: SVG 24×24, traços arredondados
- Slides claros: fundo `rgba(0,0,0,0.06)`, traço `rgba(0,0,0,0.25)`
- Slides escuros: fundo `rgba(255,255,255,0.08)`, traço `rgba(255,255,255,0.35)`

```javascript
function swipeArrow(isLightSlide) {
  const bg = isLightSlide ? 'rgba(0,0,0,0.06)' : 'rgba(255,255,255,0.08)';
  const stroke = isLightSlide ? 'rgba(0,0,0,0.25)' : 'rgba(255,255,255,0.35)';
  return `<div style="position:absolute;right:0;top:0;bottom:0;width:48px;z-index:9;display:flex;align-items:center;justify-content:center;background:linear-gradient(to right,transparent,${bg});">
    <svg width="24" height="24" viewBox="0 0 24 24" fill="none">
      <path d="M9 6l6 6-6 6" stroke="${stroke}" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"/>
    </svg>
  </div>`;
}
```

---

## Componentes Reutilizáveis

### Pills com riscado
```html
<span style="font-size:11px;padding:5px 12px;border:1px solid rgba(255,255,255,0.1);border-radius:20px;color:#6B6560;text-decoration:line-through;">{Ferramenta antiga}</span>
```

### Pills de tag
```html
<span style="font-size:11px;padding:5px 12px;background:rgba(255,255,255,0.06);border-radius:20px;color:{BRAND_LIGHT};">{Rótulo}</span>
```

### Box de citação / prompt
```html
<div style="padding:16px;background:rgba(0,0,0,0.15);border-radius:12px;border:1px solid rgba(255,255,255,0.08);">
  <p class="sans" style="font-size:13px;color:rgba(255,255,255,0.5);margin-bottom:6px;">{Rótulo}</p>
  <p class="serif" style="font-size:15px;color:#fff;font-style:italic;line-height:1.4;">"{Texto da citação}"</p>
</div>
```

### Lista de funcionalidades
```html
<div style="display:flex;align-items:flex-start;gap:14px;padding:10px 0;border-bottom:1px solid {LIGHT_BORDER};">
  <span style="color:{BRAND_PRIMARY};font-size:15px;width:18px;text-align:center;">{ícone}</span>
  <div>
    <span class="sans" style="font-size:14px;font-weight:600;color:{DARK_BG};">{Título}</span>
    <span class="sans" style="font-size:12px;color:#8A8580;">{Descrição}</span>
  </div>
</div>
```

### Passos numerados
```html
<div style="display:flex;align-items:flex-start;gap:16px;padding:14px 0;border-bottom:1px solid {LIGHT_BORDER};">
  <span class="serif" style="font-size:26px;font-weight:300;color:{BRAND_PRIMARY};min-width:34px;line-height:1;">01</span>
  <div>
    <span class="sans" style="font-size:14px;font-weight:600;color:{DARK_BG};">{Título do passo}</span>
    <span class="sans" style="font-size:12px;color:#8A8580;">{Descrição do passo}</span>
  </div>
</div>
```

### Amostras de cor
```html
<div style="width:32px;height:32px;border-radius:8px;background:{color};border:1px solid rgba(255,255,255,0.08);"></div>
```

### Botão CTA (somente no último slide)
```html
<div style="display:inline-flex;align-items:center;gap:8px;padding:12px 28px;background:{LIGHT_BG};color:{BRAND_DARK};font-family:'{BODY_FONT}',sans-serif;font-weight:600;font-size:14px;border-radius:28px;">
  {Texto do CTA}
</div>
```

### Tag / Rótulo de Categoria
```html
<span class="sans" style="display:inline-block;font-size:10px;font-weight:600;letter-spacing:2px;color:{color};margin-bottom:16px;">{TEXTO DA TAG}</span>
```
- Slides claros: `BRAND_PRIMARY`
- Slides escuros: `BRAND_LIGHT`
- Slides com gradiente: `rgba(255,255,255,0.6)`

### Bloco de Logo (primeiro e último slides)
- Se tiver ícone de logo: círculo de 40px (fundo BRAND_PRIMARY) + ícone centralizado + nome da marca ao lado
- Se for iniciais: círculo de 40px com a primeira letra em branco
- Nome da marca: 13px, weight 600, letter-spacing 0.5px

---

## Regras de Layout

- Padding do conteúdo: `0 36px` padrão
- Slides com barra de progresso na base: `0 36px 52px` para não sobrepor a barra
- **Slides Hero/CTA:** `justify-content: center`
- **Slides com muito conteúdo:** `justify-content: flex-end`
- **O conteúdo jamais pode sobrepor a barra de progresso** — use `padding-bottom: 52px`

---

## Frame do Instagram (Invólucro de Preview)

Ao exibir no chat, envolva em um frame estilo Instagram:

- **Cabeçalho:** Avatar (círculo BRAND_PRIMARY + logo) + handle + subtítulo
- **Viewport:** proporção 4:5, trilha deslizável/arrastável com todos os slides
- **Indicadores:** Bolinhas pequenas abaixo do viewport
- **Ações:** Ícones SVG de curtir, comentar, compartilhar, salvar
- **Legenda:** Handle + descrição curta + timestamp "2 HOURS AGO"

Inclua interação de arrasto/swipe via ponteiro para o preview. Os slides continuam sendo imagens prontas para exportação.

**Importante:** `.ig-frame` deve ter exatamente **420px de largura**. O viewport do carrossel é 420×525px. NÃO altere essa largura — a exportação depende dela.

---

## Fluxo de Revisão

**Siga sempre este fluxo. Nunca pule direto para a exportação sem aprovação.**

1. Gere o preview em HTML primeiro — nunca vá direto para a exportação
2. Mostre o preview e pergunte: **"Quais slides precisam de ajuste antes de exportar?"**
3. Corrija apenas os slides mencionados — nunca regenere o carrossel inteiro, a não ser que a direção mude fundamentalmente
4. Só avance para a exportação quando o usuário confirmar explicitamente (ex.: "pode exportar", "aprovado", "ok")

---

## Exportando Slides como PNGs Prontos para o Instagram

Depois que o usuário aprovar o preview, exporte cada slide como PNG individual de **1080×1350px**.

### Regras Essenciais de Exportação

1. **Use Python para gerar o HTML** — nunca use scripts shell com interpolação de variáveis. Sempre use `Path.write_text()` ou `open().write()`.

2. **Incorpore imagens em base64** — todas as imagens enviadas pelo usuário precisam ser codificadas como URIs `data:image/jpeg;base64,...`. Verifique o formato real com o comando `file` — um arquivo `.png` pode conter um JPEG.

3. **Mantenha a largura de layout em 420px** — use o `device_scale_factor` do Playwright para escalar até 1080px de saída SEM alterar o viewport de layout.

### Instalar Playwright (apenas se necessário)

Antes de rodar o script de exportação, verifique e instale somente se ausente:

```bash
python3 -c "import playwright" 2>/dev/null || pip3 install playwright
python3 -c "from playwright.sync_api import sync_playwright; sync_playwright().__enter__().chromium" 2>/dev/null || python3 -m playwright install chromium
```

### Script de Exportação

```python
import asyncio
from pathlib import Path
from playwright.async_api import async_playwright

INPUT_HTML = Path("/path/to/carousel.html")
OUTPUT_DIR = Path("/path/to/output/slides")
OUTPUT_DIR.mkdir(exist_ok=True)

TOTAL_SLIDES = 7  # Ajuste conforme o número de slides do seu carrossel

VIEW_W = 420
VIEW_H = 525
SCALE = 1080 / 420  # = 2.5714...

async def export_slides():
    async with async_playwright() as p:
        browser = await p.chromium.launch()
        page = await browser.new_page(
            viewport={"width": VIEW_W, "height": VIEW_H},
            device_scale_factor=SCALE,
        )

        html_content = INPUT_HTML.read_text(encoding="utf-8")
        await page.set_content(html_content, wait_until="networkidle")
        await page.wait_for_timeout(3000)  # Aguarda o carregamento das Google Fonts

        # Oculta o chrome do frame IG, mostrando apenas o viewport do slide
        await page.evaluate("""() => {
            document.querySelectorAll('.ig-header,.ig-dots,.ig-actions,.ig-caption')
                .forEach(el => el.style.display='none');

            const frame = document.querySelector('.ig-frame');
            frame.style.cssText = 'width:420px;height:525px;max-width:none;border-radius:0;box-shadow:none;overflow:hidden;margin:0;';

            const viewport = document.querySelector('.carousel-viewport');
            viewport.style.cssText = 'width:420px;height:525px;aspect-ratio:unset;overflow:hidden;cursor:default;';

            document.body.style.cssText = 'padding:0;margin:0;display:block;overflow:hidden;';
        }""")
        await page.wait_for_timeout(500)

        for i in range(TOTAL_SLIDES):
            await page.evaluate("""(idx) => {
                const track = document.querySelector('.carousel-track');
                track.style.transition = 'none';
                track.style.transform = 'translateX(' + (-idx * 420) + 'px)';
            }""", i)
            await page.wait_for_timeout(400)

            await page.screenshot(
                path=str(OUTPUT_DIR / f"slide_{i+1}.png"),
                clip={"x": 0, "y": 0, "width": VIEW_W, "height": VIEW_H}
            )
            print(f"Slide {i+1}/{TOTAL_SLIDES} exportado")

        await browser.close()

asyncio.run(export_slides())
```

### Por que Funciona

- **`device_scale_factor=2.5714`** renderiza em alta resolução — um elemento de 420px vira 1080px na saída. O layout permanece em 420px.
- **`clip`** captura apenas o viewport do carrossel, sem o chrome do navegador.
- **`wait_for_timeout(3000)`** dá tempo para as Google Fonts carregarem.
- **`track.style.transition = 'none'`** desativa a animação de swipe para que os slides encaixem instantaneamente.

### Erros Comuns na Exportação

| Erro | O que dá errado | Solução |
|------|----------------|---------|
| Definir viewport em 1080×1350 | O layout reflui — fontes ficam minúsculas, espaçamentos quebram | Mantenha o viewport em 420×525 e use `device_scale_factor` |
| Usar shell scripts para gerar o HTML | Sinais `$` e crases são interpolados | Sempre use Python para gerar o HTML |
| Não aguardar as fontes | Títulos renderizam com fontes fallback do sistema | `wait_for_timeout(3000)` após o carregamento da página |
| Não ocultar o chrome do frame IG | A exportação inclui cabeçalho, indicadores, legenda | Oculte `.ig-header,.ig-dots,.ig-actions,.ig-caption` |
| Alterar a largura do `.ig-frame` | Todo o layout se desloca | Sempre mantenha em exatamente 420px |
| Deixar `BRAND_PRIMARY` como nome de variável no CSS | A cor renderiza como inválida / invisível | Sempre interpole os valores hex reais no HTML |

---

## Princípios de Design

1. **Todo slide é pronto para exportação** — seta e barra de progresso fazem parte da imagem do slide
2. **Alternância claro/escuro** — gera ritmo visual entre os deslizes
3. **Pareamento de fontes título + corpo** — fonte display para impacto, corpo para legibilidade
4. **Paleta derivada da marca** — todas as cores nascem de uma cor principal, mantendo coesão
5. **Revelação progressiva** — a barra de progresso preenche e a seta guia para frente
6. **Último slide é especial** — sem seta, barra de progresso cheia, CTA claro
7. **Componentes padronizados** — mesmo estilo de tag, lista e espaçamento em todos os slides
8. **Padding do conteúdo respeita a UI** — texto nunca sobrepõe barra de progresso ou seta
9. **Copy com gancho na frente** — o Slide 1 existe para parar o scroll, não para apresentar a marca
10. **Iteração rápida** — mostre o preview, corrija slides específicos, não reconstrua do zero
