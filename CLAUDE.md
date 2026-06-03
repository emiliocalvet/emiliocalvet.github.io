# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## O que é este projeto

Página pessoal / portfólio de Emilio Calvet, hospedada no **GitHub Pages**. Como o repositório se chama `emiliocalvet.github.io`, o conteúdo da branch `master` é publicado automaticamente em `https://emiliocalvet.github.io` — **não há pipeline de build nem CI**: o que está em `master` é exatamente o que vai ao ar.

Site estático em **HTML + CSS puro**, sem framework, sem JavaScript, sem `package.json` e sem dependências instaláveis. O idioma do conteúdo é **Português Brasileiro** (`<html lang="pt-br">`).

## Desenvolvimento

Não há comandos de build/lint/test. Para visualizar localmente, sirva a raiz do projeto, por exemplo:

```bash
python3 -m http.server 8000   # depois abra http://localhost:8000
```

Abrir `index.html` direto no navegador (`file://`) também funciona, já que todos os caminhos são relativos (`./styles/...`, `./images/...`).

Deploy = `git push` para `master`. Cuidado ao alterar `index.html` ou `styles/style.css`: a mudança fica pública assim que o push for feito.

## Arquitetura e convenções

- **Página única**: `index.html` é a estrutura, `styles/style.css` é todo o estilo. Não fragmente em múltiplos arquivos sem necessidade clara.
- **Layout do `index.html`**: `header` (banner com `.perfil-photo`, `#perfil-label` com nome/cargo, `#social-media` com links) → `#container` com vários `div.content-block` (seções "Sobre", "Detalhes Pessoais", "Interesses", "Habilidades") → `footer`. Cada seção nova segue o padrão `<div id="..." class="content-block"><h2>...</h2>...</div>`.
- **CSS por seletores de ID + combinadores descendentes** (ex.: `header #social-media .social-logo img`), não BEM nem classes utilitárias. Mantenha esse estilo ao adicionar regras.
- **Tema**: preto/branco, cantos arredondados (`border-radius`), fonte Google `Blinker`. Responsividade via `@media (min-width: 600px)`.
- **Nomenclatura mista PT/EN** já existente no código (`perfil-photo`, `techs-logo`, `social-logo`). Acompanhe o padrão local em vez de padronizar tudo.

## Assets

- `images/perfil.jpg` — foto de perfil otimizada (usada no header hero).
- `images/favicon.svg` — favicon monograma "EC".
- `images/social/` — ícones sociais SVG monocromáticos: github, linkedin, whatsapp, telegram, email.
- `images/techs-logos/` — logos SVG monocromáticos do stack técnico.
- `files/curriculo-emilio-calvet.pdf` — currículo, alvo do botão de download (movido da raiz).

## Estado atual

Página implementada como cartão de visitas técnico (estilo minimalista P&B): topo (foto, nome, título, sociais, CTA de currículo), Sobre, Stack técnico (logos monocromáticos + chips) e Contato. Conteúdo real, sem placeholders. Design e plano documentados em `docs/superpowers/`.

## Formatação (.editorconfig)

Indentação de 2 espaços, fim de linha `LF`, charset UTF-8, sem espaços ao final das linhas, e newline final no arquivo. Respeite isso em todos os arquivos.
