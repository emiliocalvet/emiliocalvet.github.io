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

- **Página única**: `index.html` é a estrutura, `styles/style.css` é todo o estilo. Não fragmente sem necessidade clara.
- **Layout do `index.html`**: `header.hero` (foto, `h1.hero__name`, subtítulo, `nav.social`, botão `.btn` de currículo) → `main` com `section.section` (`.about`, `.stack`, `.contact`) → `footer.footer`. Cada bloco do stack é um `.stack-group` com `.tech-grid` de `.tech` (logo + label) ou `.tech--text` (só texto, para techs sem logo).
- **CSS por classes (estilo BEM-ish)**: `.hero`, `.section`, `.label`, `.btn`/`.btn--light`/`.btn--dark`, `.social`, `.tech`/`.tech--text`, `.chip`, `.stack-group`. Variáveis em `:root` (`--ink`, `--bg`, `--border`, `--radius`, `--maxw`...). Acrescente novas regras ao fim do arquivo seguindo esse padrão.
- **Tema**: preto/branco, cantos arredondados (`border-radius`), fonte Google `Blinker`. Mobile via `@media (max-width: 480px)`; o nome usa `clamp()` para escalar.
- **Logos do stack**: SVG monocromáticos (`#0a0a0a`) em `images/techs-logos/`, do Simple Icons. Techs sem ícone (Java, AWS, pfSense, Loki, SonarQube, Zabbix) entram como `.tech--text`.

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
