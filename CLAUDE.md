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

- `images/` — `banner.png` (fundo do header), `perfil-8bit.png` (foto de perfil, referenciada em `index.html`), e `images/techs-logos/` com logos de tecnologias e redes sociais (`react-logo.png`, `nodejs-logo.png`, `aws-logo.png`, `github-logo.png`, etc.). Novos logos vão em `techs-logos/` seguindo o sufixo `-logo.png`.
- `curriculo_emilio_calvet_final.pdf` na raiz — currículo; fonte de informações de carreira para preencher o conteúdo do site.

## Estado atual

As seções de `#container` ainda contêm **placeholder Lorem ipsum** (`#about`, `#personal-details`, `#my-interest`, `#skills`) — conteúdo real ainda não foi escrito. O objetivo do projeto é transformar isso numa página profissional (tech lead / dev fullstack) preservando o estilo visual já iniciado.

## Formatação (.editorconfig)

Indentação de 2 espaços, fim de linha `LF`, charset UTF-8, sem espaços ao final das linhas, e newline final no arquivo. Respeite isso em todos os arquivos.
