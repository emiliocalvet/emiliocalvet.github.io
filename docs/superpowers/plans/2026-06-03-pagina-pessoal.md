# Página Pessoal — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Reescrever a página pessoal de Emilio Calvet (hoje placeholder) como um cartão de visitas técnico minimalista P&B, em HTML/CSS puro, responsivo e sem build.

**Architecture:** Página única estática. `index.html` (estrutura) + `styles/style.css` (sistema de design com variáveis CSS). Assets locais (foto otimizada, logos SVG monocromáticos, ícones sociais). Zero JavaScript, zero dependências, zero etapa de build — `git push` para `master` publica no GitHub Pages.

**Tech Stack:** HTML5, CSS3 (variáveis, grid, flexbox), fonte Google "Blinker", logos via Simple Icons (baixados localmente).

---

## Restrições e convenções (ler antes de começar)

- **Sem build / sem dependências / sem framework JS.** Não criar `package.json`. (Restrição central da spec.)
- **`.editorconfig`:** indentação de 2 espaços, fim de linha `LF`, UTF-8, sem espaços no fim das linhas, newline final. Respeitar em todos os arquivos.
- **Idioma:** Português Brasileiro, `<html lang="pt-br">`.
- **Git:** commitar a cada tarefa, **direto na `master` mas SEM `git push`**. `master` é produção (push = no ar). O push final só após o usuário revisar a página rodando. Mensagens de commit terminam com:
  ```
  Co-Authored-By: Claude Opus 4.8 (1M context) <noreply@anthropic.com>
  ```
- **Spec de referência:** `docs/superpowers/specs/2026-06-03-pagina-pessoal-design.md`.
- **Mockup aprovado (referência visual):** `.superpowers/brainstorm/51170-1780455477/content/mockup-v4.html`.

## Como verificar (usado nas tarefas)

A verificação é **visual + checagem de assets** (não há test runner, por design).

1. **Servir:** na raiz do projeto, rodar `python3 -m http.server 8000` (deixar rodando em background) e abrir `http://localhost:8000`.
2. **Console:** abrir o DevTools → Console/Network e confirmar **nenhum 404** e nenhum erro.
3. **Responsivo:** no DevTools, alternar entre largura desktop (~1200px) e mobile (~375px).
4. **Checagem automática de assets locais** (sem dependências) — script reutilizado em várias tarefas:
   ```bash
   python3 - <<'PY'
   import re, os, sys
   html = open('index.html', encoding='utf-8').read()
   refs = re.findall(r'(?:src|href)="(?!https?:|mailto:|data:|#)([^"]+)"', html)
   missing = [r for r in refs if not os.path.exists(r)]
   print(f"refs locais: {len(refs)} | faltando: {missing}")
   sys.exit(1 if missing else 0)
   PY
   ```
   Esperado: `faltando: []` e exit 0.

## Estrutura de arquivos

| Arquivo | Responsabilidade | Ação |
|---|---|---|
| `index.html` | Estrutura da página única (head/meta + 5 seções) | Reescrever |
| `styles/style.css` | Sistema de design P&B (variáveis, componentes, responsivo) | Reescrever |
| `images/perfil.jpg` | Foto profissional otimizada (~150–250 KB) | Criar |
| `images/favicon.svg` | Favicon monograma "EC" | Criar |
| `images/social/*.svg` | Ícones sociais monocromáticos (github, linkedin, whatsapp, telegram, email) | Criar |
| `images/techs-logos/*.svg` | Logos do stack, monocromáticos `#0a0a0a` | Criar (baixar) |
| `images/banner.png` | Banner antigo, não mais usado | Remover |
| `images/perfil-8bit.png` | Avatar antigo, substituído | Remover |
| `32288E49-...png` (raiz) | Foto-fonte original | Remover após otimizar |
| `curriculo_emilio_calvet_final.pdf` | Alvo do botão de download | Versionar (git add) |

---

## Task 1: Otimizar a foto de perfil

**Files:**
- Create: `images/perfil.jpg`
- Source: `32288E49-DF95-4A53-AED9-3F62AFA33B47.png` (raiz)

- [ ] **Step 1: Gerar a versão otimizada com `sips` (nativo do macOS)**

```bash
sips -s format jpeg -Z 480 -s formatOptions 82 \
  "32288E49-DF95-4A53-AED9-3F62AFA33B47.png" \
  --out images/perfil.jpg
```

- [ ] **Step 2: Verificar tamanho e dimensões**

```bash
ls -la images/perfil.jpg | awk '{print $5" bytes"}'
sips -g pixelWidth -g pixelHeight images/perfil.jpg
```
Esperado: arquivo entre ~80 KB e ~250 KB, largura 480px. Se ficar acima de 250 KB, baixar a qualidade para `72` e repetir o Step 1.

- [ ] **Step 3: Commit**

```bash
git add images/perfil.jpg
git commit -m "feat: adiciona foto de perfil otimizada

Co-Authored-By: Claude Opus 4.8 (1M context) <noreply@anthropic.com>"
```

---

## Task 2: Baixar logos do stack e criar ícones sociais

Logos monocromáticos via Simple Icons CDN (`https://cdn.simpleicons.org/<slug>/0a0a0a` devolve o SVG já preenchido em `#0a0a0a`). Ícones sem cobertura no Simple Icons ficam como **tile de texto** (tratado no HTML do Task 6) e NÃO são baixados aqui: **Java, AWS, pfSense, Loki**.

**Files:**
- Create: `images/techs-logos/*.svg` (vários)
- Create: `images/social/github.svg`, `linkedin.svg`, `whatsapp.svg`, `telegram.svg`, `email.svg`

- [ ] **Step 1: Baixar os logos do stack que existem no Simple Icons**

```bash
mkdir -p images/techs-logos
for slug in typescript javascript python go dart springboot nodedotjs nestjs \
            express fastapi react nextdotjs angular flutter postgresql mongodb \
            redis docker kubernetes githubactions nginx caddy traefikproxy \
            sonarqube grafana prometheus zabbix; do
  curl -fsSL "https://cdn.simpleicons.org/${slug}/0a0a0a" -o "images/techs-logos/${slug}.svg" \
    && echo "ok  ${slug}" || echo "FALHOU ${slug}"
done
```

- [ ] **Step 2: Validar que cada arquivo baixado é um SVG válido**

```bash
for f in images/techs-logos/*.svg; do
  head -c 200 "$f" | grep -q "<svg" && echo "ok  $f" || echo "INVALIDO $f"
done
```
Esperado: todos `ok`. Se algum `FALHOU`/`INVALIDO` (slug mudou no Simple Icons), tratar aquele item como **tile de texto** no Task 6 (remover o `<img>`, manter só o `<span>`).

- [ ] **Step 3: Baixar os ícones sociais (preto; serão invertidos para branco no hero via CSS)**

```bash
mkdir -p images/social
curl -fsSL "https://cdn.simpleicons.org/github/0a0a0a"   -o images/social/github.svg
curl -fsSL "https://cdn.simpleicons.org/linkedin/0a0a0a" -o images/social/linkedin.svg
curl -fsSL "https://cdn.simpleicons.org/whatsapp/0a0a0a" -o images/social/whatsapp.svg
curl -fsSL "https://cdn.simpleicons.org/telegram/0a0a0a" -o images/social/telegram.svg
```

- [ ] **Step 4: Criar o ícone de e-mail (envelope) como SVG preto**

Criar `images/social/email.svg`:

```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="#0a0a0a"><path d="M1.5 5.25A2.25 2.25 0 0 1 3.75 3h16.5a2.25 2.25 0 0 1 2.25 2.25v13.5A2.25 2.25 0 0 1 20.25 21H3.75a2.25 2.25 0 0 1-2.25-2.25V5.25Zm2.06-.75 8.44 6.33L20.44 4.5H3.56ZM21 6.31l-8.4 6.3a1 1 0 0 1-1.2 0L3 6.31v12.44c0 .41.34.75.75.75h16.5a.75.75 0 0 0 .75-.75V6.31Z"/></svg>
```

- [ ] **Step 5: Verificar os 5 ícones sociais**

```bash
for f in images/social/*.svg; do head -c 200 "$f" | grep -q "<svg" && echo "ok  $f" || echo "INVALIDO $f"; done
ls images/social
```
Esperado: `github.svg linkedin.svg whatsapp.svg telegram.svg email.svg`, todos `ok`.

- [ ] **Step 6: Commit**

```bash
git add images/techs-logos images/social
git commit -m "feat: adiciona logos do stack e icones sociais monocromaticos

Co-Authored-By: Claude Opus 4.8 (1M context) <noreply@anthropic.com>"
```

---

## Task 3: Estrutura HTML base + fundação do CSS

Cria o esqueleto completo do `index.html` (head/meta + 5 seções com marcadores) e a base do `styles/style.css` (variáveis, reset, container, componentes compartilhados). As seções são preenchidas nas Tasks 4–7.

**Files:**
- Modify (reescrever): `index.html`
- Modify (reescrever): `styles/style.css`
- Create: `images/favicon.svg`

- [ ] **Step 1: Criar o favicon** — `images/favicon.svg`:

```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 100 100"><rect width="100" height="100" rx="22" fill="#0a0a0a"/><text x="50" y="69" font-family="Arial, sans-serif" font-size="54" font-weight="700" fill="#ffffff" text-anchor="middle">EC</text></svg>
```

- [ ] **Step 2: Reescrever `index.html` com o esqueleto completo**

```html
<!DOCTYPE html>
<html lang="pt-br">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Emilio Calvet — Tech Lead · Full Stack</title>
  <meta name="description" content="Emilio Calvet — Tech Lead e Desenvolvedor Full Stack. Arquitetura de sistemas, desenvolvimento full-stack e infraestrutura cloud/DevOps.">
  <link rel="icon" href="./images/favicon.svg">

  <meta property="og:type" content="website">
  <meta property="og:title" content="Emilio Calvet — Tech Lead · Full Stack">
  <meta property="og:description" content="Tech Lead e Desenvolvedor Full Stack — arquitetura, full-stack e cloud/DevOps.">
  <meta property="og:image" content="https://emiliocalvet.github.io/images/perfil.jpg">
  <meta property="og:url" content="https://emiliocalvet.github.io">

  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Blinker:wght@400;600;700&display=swap" rel="stylesheet">
  <link rel="stylesheet" href="./styles/style.css">
</head>

<body>
  <header class="hero">
    <!-- HERO_CONTENT -->
  </header>

  <main>
    <section class="section about">
      <!-- ABOUT_CONTENT -->
    </section>

    <section class="section stack">
      <!-- STACK_CONTENT -->
    </section>

    <section class="section contact">
      <!-- CONTACT_CONTENT -->
    </section>
  </main>

  <footer class="footer">
    <!-- FOOTER_CONTENT -->
  </footer>
</body>

</html>
```

- [ ] **Step 3: Reescrever `styles/style.css` com a fundação do design system**

```css
:root {
  --ink: #0a0a0a;
  --bg: #ffffff;
  --bg-alt: #fafafa;
  --muted: #666666;
  --muted-light: #999999;
  --border: #ececec;
  --on-dark: #f2f2f2;
  --on-dark-muted: #cfcfcf;
  --radius: 12px;
  --radius-lg: 22px;
  --maxw: 760px;
}

* {
  margin: 0;
  box-sizing: border-box;
}

html {
  scroll-behavior: smooth;
}

body {
  font-family: 'Blinker', ui-sans-serif, system-ui, sans-serif;
  color: var(--ink);
  background: var(--bg);
  line-height: 1.5;
  -webkit-font-smoothing: antialiased;
}

img {
  display: block;
  max-width: 100%;
}

a {
  color: inherit;
}

/* Seções */
.section {
  max-width: var(--maxw);
  margin: 0 auto;
  padding: 48px 24px;
}

.section + .section {
  border-top: 1px solid var(--border);
}

.label {
  font-size: 12px;
  text-transform: uppercase;
  letter-spacing: 2px;
  color: var(--muted-light);
  font-weight: 700;
  margin-bottom: 14px;
}

.section h2 {
  font-size: 24px;
  font-weight: 700;
  letter-spacing: -0.3px;
  margin-bottom: 16px;
}

/* Botões */
.btn {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  font-weight: 700;
  font-size: 14px;
  padding: 12px 22px;
  border-radius: var(--radius);
  text-decoration: none;
  transition: transform 0.15s ease, box-shadow 0.15s ease;
}

.btn:hover {
  transform: translateY(-2px);
}

.btn--light {
  background: #ffffff;
  color: var(--ink);
}

.btn--dark {
  background: var(--ink);
  color: #ffffff;
}

/* Ícones sociais (padrão claro; variante escura no hero) */
.social {
  display: flex;
  gap: 12px;
  justify-content: center;
  flex-wrap: wrap;
}

.social a {
  width: 42px;
  height: 42px;
  border: 1px solid var(--border);
  border-radius: 10px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: #ffffff;
  transition: transform 0.15s ease, box-shadow 0.15s ease;
}

.social a:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 14px rgba(0, 0, 0, 0.10);
}

.social img {
  width: 20px;
  height: 20px;
}
```

- [ ] **Step 4: Verificar o esqueleto**

Servir (`python3 -m http.server 8000`) e abrir `http://localhost:8000`. Esperado: página em branco com fundo branco, **sem erros no console** e a aba mostrando o favicon "EC" + título. Rodar a checagem de assets (seção "Como verificar" → script). Esperado: `faltando: []`.

- [ ] **Step 5: Commit**

```bash
git add index.html styles/style.css images/favicon.svg
git commit -m "feat: esqueleto HTML e fundacao do design system P&B

Co-Authored-By: Claude Opus 4.8 (1M context) <noreply@anthropic.com>"
```

---

## Task 4: Seção Hero

**Files:**
- Modify: `index.html` (substituir `<!-- HERO_CONTENT -->`)
- Modify: `styles/style.css` (acrescentar regras do hero)

- [ ] **Step 1: Substituir o marcador `<!-- HERO_CONTENT -->` em `index.html`**

```html
    <img class="hero__photo" src="./images/perfil.jpg" alt="Foto de Emilio Calvet">
    <h1 class="hero__name">Emilio Calvet</h1>
    <p class="hero__title">Tech Lead · Desenvolvedor Full Stack</p>

    <nav class="social" aria-label="Redes sociais">
      <a href="https://github.com/emiliocalvet" target="_blank" rel="noopener" aria-label="GitHub">
        <img src="./images/social/github.svg" alt="GitHub">
      </a>
      <a href="https://www.linkedin.com/in/emiliocalvet/" target="_blank" rel="noopener" aria-label="LinkedIn">
        <img src="./images/social/linkedin.svg" alt="LinkedIn">
      </a>
      <a href="https://api.whatsapp.com/send?phone=5585999925102" target="_blank" rel="noopener" aria-label="WhatsApp">
        <img src="./images/social/whatsapp.svg" alt="WhatsApp">
      </a>
      <a href="https://t.me/emiliocalvet" target="_blank" rel="noopener" aria-label="Telegram">
        <img src="./images/social/telegram.svg" alt="Telegram">
      </a>
      <a href="mailto:emiliocalvet@gmail.com" aria-label="E-mail">
        <img src="./images/social/email.svg" alt="E-mail">
      </a>
    </nav>

    <a class="btn btn--light" href="./curriculo_emilio_calvet_final.pdf" download>
      ↓ Baixar currículo (PDF)
    </a>
```

- [ ] **Step 2: Acrescentar as regras do hero ao fim de `styles/style.css`**

```css
/* Hero */
.hero {
  background: #0a0a0a;
  background-image: radial-gradient(circle at 50% 0%, #1c1c1c, #0a0a0a 70%);
  color: var(--on-dark);
  text-align: center;
  padding: 48px 24px 36px;
  border-bottom-left-radius: var(--radius-lg);
  border-bottom-right-radius: var(--radius-lg);
}

.hero__photo {
  width: 150px;
  height: 150px;
  border-radius: 50%;
  margin: 0 auto 18px;
  border: 3px solid #ffffff;
  box-shadow: 0 10px 28px rgba(0, 0, 0, 0.5);
  object-fit: cover;
}

.hero__name {
  font-size: clamp(28px, 7vw, 40px);
  font-weight: 700;
  letter-spacing: -0.5px;
}

.hero__title {
  font-size: 15px;
  color: var(--on-dark-muted);
  margin-top: 6px;
  letter-spacing: 0.3px;
}

.hero .social {
  margin: 20px 0 24px;
}

.hero .social a {
  background: #141414;
  border-color: #333333;
}

.hero .social a:hover {
  background: #1f1f1f;
  box-shadow: none;
}

.hero .social img {
  filter: invert(1);
}
```

- [ ] **Step 3: Verificar**

Recarregar `http://localhost:8000`. Esperado: faixa preta com a foto circular (anel branco), nome grande, subtítulo, 5 ícones brancos em caixas escuras, e o botão branco "Baixar currículo". Clicar no botão **baixa o PDF**. Sem 404 no console. Conferir em 375px (tudo centralizado, ícones podem quebrar linha sem estourar). Rodar a checagem de assets → `faltando: []`.

- [ ] **Step 4: Commit**

```bash
git add index.html styles/style.css
git commit -m "feat: secao hero (foto, nome, sociais, CTA curriculo)

Co-Authored-By: Claude Opus 4.8 (1M context) <noreply@anthropic.com>"
```

---

## Task 5: Seção Sobre

**Files:**
- Modify: `index.html` (substituir `<!-- ABOUT_CONTENT -->`)
- Modify: `styles/style.css`

- [ ] **Step 1: Substituir o marcador `<!-- ABOUT_CONTENT -->` em `index.html`**

```html
    <p class="label">Sobre</p>
    <p class="about__text">
      Tech Lead com perfil generalista, atuando entre arquitetura de sistemas,
      desenvolvimento full-stack (back-end, front-end web e mobile) e infraestrutura
      cloud/DevOps. Trabalho hands-on em monolitos modulares, microsserviços e SaaS
      multi-tenant. Graduado em Engenharia de Software pela UFC, com MBA em Arquitetura
      de Software em andamento na Full Cycle.
    </p>
    <div class="chips">
      <span class="chip">Arquitetura de sistemas</span>
      <span class="chip">Full-stack</span>
      <span class="chip">Cloud / DevOps</span>
      <span class="chip">SaaS multi-tenant</span>
      <span class="chip">IA / busca vetorial</span>
    </div>
```

- [ ] **Step 2: Acrescentar as regras de Sobre/chips ao fim de `styles/style.css`**

```css
/* Sobre */
.about__text {
  font-size: 16px;
  line-height: 1.65;
  color: #333333;
}

/* Chips */
.chips {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
  margin-top: 18px;
}

.chip {
  font-size: 13px;
  border: 1px solid var(--border);
  background: var(--bg-alt);
  color: #222222;
  padding: 6px 12px;
  border-radius: 999px;
}
```

- [ ] **Step 3: Verificar**

Recarregar. Esperado: abaixo do hero, label "SOBRE", o parágrafo legível e a linha de 5 chips arredondados. Conferir leitura confortável em 375px. Sem erros no console.

- [ ] **Step 4: Commit**

```bash
git add index.html styles/style.css
git commit -m "feat: secao Sobre com resumo e chips

Co-Authored-By: Claude Opus 4.8 (1M context) <noreply@anthropic.com>"
```

---

## Task 6: Seção Stack técnico

Grade de logos monocromáticos agrupados + grupo final em chips. Tiles **com ícone**: `<div class="tech"><img...><span>Nome</span></div>`. Tiles **só texto** (sem logo no Simple Icons — Java, AWS, pfSense, Loki): `<div class="tech tech--text"><span>Nome</span></div>`. Se algum download do Task 2 tiver falhado, converter aquele tile para `tech--text`.

**Files:**
- Modify: `index.html` (substituir `<!-- STACK_CONTENT -->`)
- Modify: `styles/style.css`

- [ ] **Step 1: Substituir o marcador `<!-- STACK_CONTENT -->` em `index.html`**

```html
    <p class="label">Stack técnico</p>
    <h2>Tecnologias</h2>

    <div class="stack-group">
      <p class="stack-group__label">Linguagens</p>
      <div class="tech-grid">
        <div class="tech tech--text"><span>Java</span></div>
        <div class="tech"><img src="./images/techs-logos/typescript.svg" alt="TypeScript"><span>TypeScript</span></div>
        <div class="tech"><img src="./images/techs-logos/javascript.svg" alt="JavaScript"><span>JavaScript</span></div>
        <div class="tech"><img src="./images/techs-logos/python.svg" alt="Python"><span>Python</span></div>
        <div class="tech"><img src="./images/techs-logos/go.svg" alt="Go"><span>Go</span></div>
        <div class="tech"><img src="./images/techs-logos/dart.svg" alt="Dart"><span>Dart</span></div>
      </div>
    </div>

    <div class="stack-group">
      <p class="stack-group__label">Back-end</p>
      <div class="tech-grid">
        <div class="tech"><img src="./images/techs-logos/springboot.svg" alt="Spring Boot"><span>Spring Boot</span></div>
        <div class="tech"><img src="./images/techs-logos/nodedotjs.svg" alt="Node.js"><span>Node.js</span></div>
        <div class="tech"><img src="./images/techs-logos/nestjs.svg" alt="NestJS"><span>NestJS</span></div>
        <div class="tech"><img src="./images/techs-logos/express.svg" alt="Express"><span>Express</span></div>
        <div class="tech"><img src="./images/techs-logos/fastapi.svg" alt="FastAPI"><span>FastAPI</span></div>
      </div>
    </div>

    <div class="stack-group">
      <p class="stack-group__label">Front-end</p>
      <div class="tech-grid">
        <div class="tech"><img src="./images/techs-logos/react.svg" alt="React"><span>React</span></div>
        <div class="tech"><img src="./images/techs-logos/nextdotjs.svg" alt="Next.js"><span>Next.js</span></div>
        <div class="tech"><img src="./images/techs-logos/angular.svg" alt="Angular"><span>Angular</span></div>
      </div>
    </div>

    <div class="stack-group">
      <p class="stack-group__label">Mobile</p>
      <div class="tech-grid">
        <div class="tech"><img src="./images/techs-logos/flutter.svg" alt="Flutter"><span>Flutter</span></div>
        <div class="tech"><img src="./images/techs-logos/react.svg" alt="React Native"><span>React Native</span></div>
      </div>
    </div>

    <div class="stack-group">
      <p class="stack-group__label">Bancos de dados</p>
      <div class="tech-grid">
        <div class="tech"><img src="./images/techs-logos/postgresql.svg" alt="PostgreSQL"><span>PostgreSQL</span></div>
        <div class="tech"><img src="./images/techs-logos/mongodb.svg" alt="MongoDB"><span>MongoDB</span></div>
        <div class="tech"><img src="./images/techs-logos/redis.svg" alt="Redis"><span>Redis</span></div>
      </div>
    </div>

    <div class="stack-group">
      <p class="stack-group__label">Cloud &amp; DevOps</p>
      <div class="tech-grid">
        <div class="tech tech--text"><span>AWS</span></div>
        <div class="tech"><img src="./images/techs-logos/docker.svg" alt="Docker"><span>Docker</span></div>
        <div class="tech"><img src="./images/techs-logos/kubernetes.svg" alt="Kubernetes"><span>Kubernetes</span></div>
        <div class="tech"><img src="./images/techs-logos/githubactions.svg" alt="GitHub Actions"><span>GitHub Actions</span></div>
        <div class="tech"><img src="./images/techs-logos/nginx.svg" alt="Nginx"><span>Nginx</span></div>
        <div class="tech"><img src="./images/techs-logos/caddy.svg" alt="Caddy"><span>Caddy</span></div>
        <div class="tech"><img src="./images/techs-logos/traefikproxy.svg" alt="Traefik"><span>Traefik</span></div>
        <div class="tech tech--text"><span>pfSense</span></div>
        <div class="tech"><img src="./images/techs-logos/sonarqube.svg" alt="SonarQube"><span>SonarQube</span></div>
      </div>
    </div>

    <div class="stack-group">
      <p class="stack-group__label">Observabilidade</p>
      <div class="tech-grid">
        <div class="tech"><img src="./images/techs-logos/grafana.svg" alt="Grafana"><span>Grafana</span></div>
        <div class="tech"><img src="./images/techs-logos/prometheus.svg" alt="Prometheus"><span>Prometheus</span></div>
        <div class="tech tech--text"><span>Loki</span></div>
        <div class="tech"><img src="./images/techs-logos/zabbix.svg" alt="Zabbix"><span>Zabbix</span></div>
      </div>
    </div>

    <div class="stack-group">
      <p class="stack-group__label">Ferramentas &amp; Bibliotecas</p>
      <div class="chips">
        <span class="chip">Firebase</span>
        <span class="chip">FCM</span>
        <span class="chip">OneSignal</span>
        <span class="chip">Keycloak / SSO Gov.br</span>
        <span class="chip">Google ML Kit</span>
        <span class="chip">OpenCV</span>
        <span class="chip">JasperReports</span>
        <span class="chip">MinIO</span>
        <span class="chip">Passbolt</span>
        <span class="chip">MCP</span>
        <span class="chip">Git</span>
        <span class="chip">Linux</span>
      </div>
    </div>
```

- [ ] **Step 2: Acrescentar as regras do stack ao fim de `styles/style.css`**

```css
/* Stack */
.stack-group {
  margin-bottom: 24px;
}

.stack-group:last-child {
  margin-bottom: 0;
}

.stack-group__label {
  font-size: 13px;
  font-weight: 700;
  color: #555555;
  margin-bottom: 10px;
}

.tech-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(84px, 1fr));
  gap: 12px;
}

.tech {
  aspect-ratio: 1.1;
  border: 1px solid var(--border);
  border-radius: var(--radius);
  background: var(--bg-alt);
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 8px;
  padding: 8px;
  transition: transform 0.15s ease, box-shadow 0.15s ease;
}

.tech:hover {
  transform: translateY(-3px);
  box-shadow: 0 8px 18px rgba(0, 0, 0, 0.08);
}

.tech img {
  width: 30px;
  height: 30px;
}

.tech span {
  font-size: 11px;
  color: #444444;
  font-weight: 600;
  text-align: center;
}

.tech--text span {
  font-size: 14px;
  color: var(--ink);
  font-weight: 700;
}
```

- [ ] **Step 3: Verificar**

Recarregar. Esperado: 8 grupos rotulados; tiles com logos pretos + nome; tiles "Java/AWS/pfSense/Loki" só com o nome em destaque; último grupo em chips. Hover eleva o tile. **Nenhum 404** no console (todos os `.svg` carregam). Em 375px a grade reflui para menos colunas sem estourar. Rodar a checagem de assets → `faltando: []`.

- [ ] **Step 4: Commit**

```bash
git add index.html styles/style.css
git commit -m "feat: secao Stack tecnico (logos monocromaticos + chips)

Co-Authored-By: Claude Opus 4.8 (1M context) <noreply@anthropic.com>"
```

---

## Task 7: Seção Contato + Footer + versionar currículo

**Files:**
- Modify: `index.html` (substituir `<!-- CONTACT_CONTENT -->` e `<!-- FOOTER_CONTENT -->`)
- Modify: `styles/style.css`
- Add to git: `curriculo_emilio_calvet_final.pdf`

- [ ] **Step 1: Substituir `<!-- CONTACT_CONTENT -->` em `index.html`**

```html
    <p class="label">Contato</p>
    <h2>Vamos conversar</h2>
    <a class="contact__email" href="mailto:emiliocalvet@gmail.com">emiliocalvet@gmail.com</a>
    <div class="contact__cta">
      <a class="btn btn--dark" href="./curriculo_emilio_calvet_final.pdf" download>↓ Baixar currículo (PDF)</a>
    </div>
    <nav class="social" aria-label="Redes sociais">
      <a href="https://github.com/emiliocalvet" target="_blank" rel="noopener" aria-label="GitHub">
        <img src="./images/social/github.svg" alt="GitHub">
      </a>
      <a href="https://www.linkedin.com/in/emiliocalvet/" target="_blank" rel="noopener" aria-label="LinkedIn">
        <img src="./images/social/linkedin.svg" alt="LinkedIn">
      </a>
      <a href="https://api.whatsapp.com/send?phone=5585999925102" target="_blank" rel="noopener" aria-label="WhatsApp">
        <img src="./images/social/whatsapp.svg" alt="WhatsApp">
      </a>
      <a href="https://t.me/emiliocalvet" target="_blank" rel="noopener" aria-label="Telegram">
        <img src="./images/social/telegram.svg" alt="Telegram">
      </a>
    </nav>
```

- [ ] **Step 2: Substituir `<!-- FOOTER_CONTENT -->` em `index.html`**

```html
    <p>© 2026 Emilio Calvet</p>
```

- [ ] **Step 3: Acrescentar as regras de contato/footer ao fim de `styles/style.css`**

```css
/* Contato */
.contact {
  text-align: center;
}

.contact__email {
  display: inline-block;
  font-size: 18px;
  font-weight: 700;
  border-bottom: 2px solid var(--ink);
  padding-bottom: 2px;
  text-decoration: none;
  margin-bottom: 20px;
}

.contact__cta {
  margin-bottom: 8px;
}

.contact .social {
  margin-top: 18px;
}

/* Footer */
.footer {
  background: #0a0a0a;
  color: #aaaaaa;
  text-align: center;
  padding: 24px;
  font-size: 13px;
  border-top-left-radius: var(--radius-lg);
  border-top-right-radius: var(--radius-lg);
}
```

- [ ] **Step 4: Verificar**

Recarregar. Esperado: seção "Contato" centralizada com e-mail sublinhado clicável (`mailto:` abre o cliente de e-mail), botão escuro de currículo (baixa o PDF) e 4 ícones sociais **pretos sobre fundo branco** (sem inverter). Footer preto com o copyright. Sem 404.

- [ ] **Step 5: Commit (incluindo o PDF do currículo)**

```bash
git add index.html styles/style.css curriculo_emilio_calvet_final.pdf
git commit -m "feat: secao Contato, footer e versiona curriculo PDF

Co-Authored-By: Claude Opus 4.8 (1M context) <noreply@anthropic.com>"
```

---

## Task 8: Responsividade, acessibilidade e limpeza

**Files:**
- Modify: `styles/style.css` (ajustes finos responsivos)
- Remove: `images/banner.png`, `images/perfil-8bit.png`, `32288E49-DF95-4A53-AED9-3F62AFA33B47.png`

- [ ] **Step 1: Acrescentar ajustes finos de espaçamento mobile ao fim de `styles/style.css`**

```css
/* Ajustes mobile */
@media (max-width: 480px) {
  .section {
    padding: 40px 18px;
  }

  .hero {
    padding: 40px 18px 32px;
  }

  .tech-grid {
    grid-template-columns: repeat(auto-fill, minmax(72px, 1fr));
  }
}
```

- [ ] **Step 2: Auditoria de acessibilidade rápida (manual)**

Confirmar no HTML final:
- `<img>` do hero/contato e todos os logos têm `alt`.
- Cada `<a>` de ícone social tem `aria-label`.
- Links externos têm `rel="noopener"`.
- Existe exatamente um `<h1>` (o nome no hero).

Corrigir qualquer item que falhe antes de prosseguir.

- [ ] **Step 3: Remover assets antigos não usados**

```bash
git rm images/banner.png images/perfil-8bit.png
rm -f "32288E49-DF95-4A53-AED9-3F62AFA33B47.png"
```

- [ ] **Step 4: Verificar que nada referencia os assets removidos**

```bash
grep -rn -e "banner.png" -e "perfil-8bit" -e "32288E49" index.html styles/style.css || echo "ok: nenhuma referencia restante"
```
Esperado: `ok: nenhuma referencia restante`.

- [ ] **Step 5: Verificação visual completa**

Recarregar em desktop (~1200px) e mobile (~375px). Conferir todas as 5 seções fiéis ao mockup, sem 404, transições de hover suaves. Rodar a checagem de assets locais → `faltando: []`.

- [ ] **Step 6: Commit**

```bash
git add -A
git commit -m "chore: ajustes responsivos, a11y e remocao de assets antigos

Co-Authored-By: Claude Opus 4.8 (1M context) <noreply@anthropic.com>"
```

---

## Task 9: Atualizar CLAUDE.md e revisão final

**Files:**
- Modify: `CLAUDE.md`

- [ ] **Step 1: Atualizar a seção "Estado atual" do `CLAUDE.md`**

O `CLAUDE.md` atual diz que as seções estão com Lorem ipsum. Substituir o parágrafo da seção **"## Estado atual"** por:

```markdown
## Estado atual

Página implementada como cartão de visitas técnico (estilo minimalista P&B): topo (foto, nome, título, sociais, CTA de currículo), Sobre, Stack técnico (logos monocromáticos + chips) e Contato. Conteúdo real, sem placeholders. Design e plano documentados em `docs/superpowers/`.
```

Também atualizar a seção **"## Assets"**: trocar a menção a `perfil-8bit.png`/`banner.png` por `images/perfil.jpg` (foto), `images/favicon.svg`, `images/social/` (ícones sociais) e logos SVG monocromáticos em `images/techs-logos/`.

- [ ] **Step 2: Revisão final contra a spec**

Abrir `http://localhost:8000` e percorrer os 6 critérios de aceitação da spec (seção 11). Confirmar cada um. Rodar a checagem de assets uma última vez → `faltando: []`.

- [ ] **Step 3: Commit**

```bash
git add CLAUDE.md
git commit -m "docs: atualiza CLAUDE.md com o estado real da pagina

Co-Authored-By: Claude Opus 4.8 (1M context) <noreply@anthropic.com>"
```

- [ ] **Step 4: Entregar para revisão do usuário (NÃO fazer push)**

Parar o servidor local. Avisar o usuário que a página está pronta localmente, listar os commits feitos (`git log --oneline`) e **pedir que ele rode/abra a página e aprove antes do `git push`** (push = publicação no GitHub Pages).

---

## Self-review (cobertura da spec)

- **Objetivo / cartão de visitas** → Tasks 4–7 (hero, sobre, stack, contato). ✓
- **Minimalista P&B / continuidade** → Task 3 (variáveis, Blinker, faixas pretas arredondadas). ✓
- **Sem build/framework/JS** → respeitado; nenhum `package.json`. ✓
- **5 seções (Hero, Sobre, Stack, Contato, Footer)** → Tasks 4, 5, 6, 7. ✓
- **Copy do Sobre + precisão da formação** → Task 5 (sem Ciência da Computação como graduação). ✓
- **Taxonomia completa do stack (com Redis) + divisão logos/chips** → Task 6. ✓
- **Foto otimizada / logos SVG mono locais / favicon** → Tasks 1, 2, 3. ✓
- **Contato: e-mail visível + botão currículo (PDF versionado)** → Tasks 4 e 7. ✓
- **Responsividade e acessibilidade** → Task 8. ✓
- **Remoção de banner/8-bit** → Task 8. ✓
- **Critérios de aceitação revisados** → Task 9. ✓

Sem placeholders de plano; nomes de classes consistentes entre HTML e CSS (`hero`, `social`, `tech`, `tech--text`, `chip`, `stack-group`, `btn`); ordem das tarefas garante que assets existam antes de serem referenciados.
