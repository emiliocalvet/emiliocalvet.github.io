# Design — Página Pessoal de Emilio Calvet

**Data:** 2026-06-03
**Status:** Aprovado para implementação
**Repositório:** `emiliocalvet.github.io` (GitHub Pages — `master` = produção)

## 1. Objetivo

Transformar a página pessoal (hoje em estado inicial, com placeholders Lorem ipsum) em um **cartão de visitas técnico**: uma presença profissional equilibrada para linkar no LinkedIn, GitHub e assinatura de e-mail. O visitante típico é um recrutador, tech lead ou potencial cliente que quer entender, em poucos segundos: **quem é**, **o que faz** e **como falar com ele**.

## 2. Princípios

- **Simples, bonita e responsiva** — uma página única, leve, que funciona bem no celular e no desktop.
- **Minimalista preto & branco** — continuidade do estilo já iniciado no projeto (header preto, cantos arredondados, fonte Blinker). Sóbrio e atemporal.
- **Sem build, sem framework** — preserva a simplicidade do GitHub Pages, onde `git push` para `master` já publica.

## 3. Arquitetura técnica

- **HTML + CSS puro, página única.** Reescrever `index.html` e `styles/style.css` (hoje placeholders).
- **Sem etapa de build, sem dependências instaláveis, sem framework JS.** Nada de `package.json`.
- **Variáveis CSS** (`:root { --ink; --bg; --muted; --border; ... }`) para a paleta P&B e espaçamentos, facilitando ajustes futuros de cor e tipografia.
- **JavaScript:** nenhum é necessário para a funcionalidade. Opcional e mínimo: `scroll-behavior: smooth` via CSS (sem JS). Nada de bibliotecas.
- **Idioma:** Português Brasileiro (`<html lang="pt-br">`).
- **Fonte:** manter **Blinker** (Google Fonts, já carregada) como fonte da marca, com fallback `system-ui, sans-serif`.

## 4. Estrutura da página (single page, scroll vertical)

Container central com largura máxima ~760px, centralizado, com respiro lateral em telas pequenas.

### 4.1 Topo / Hero (faixa preta, cantos inferiores arredondados)
- Fundo near-black com leve gradiente radial (sem imagem de banner pesada).
- **Foto profissional** circular, com anel branco e sombra suave.
- Nome **"Emilio Calvet"** (display grande).
- Subtítulo **"Tech Lead · Desenvolvedor Full Stack"**.
- Linha de **ícones sociais** monocromáticos (contorno branco): GitHub, LinkedIn, WhatsApp, Telegram, E-mail.
- **CTA primário:** botão branco **"Baixar currículo (PDF)"**.

### 4.2 Sobre (seção clara)
- Label "Sobre".
- Parágrafo enxuto (copy na seção 5).
- Linha de **chips** de palavras-chave: Arquitetura de sistemas · Full-stack · Cloud / DevOps · SaaS multi-tenant · IA / busca vetorial.

### 4.3 Stack técnico (seção clara)
- Label "Stack técnico", título "Tecnologias".
- Grupos com **logos monocromáticos** (tiles quadradas com leve elevação no hover) — itens na seção 6.
- O grupo **Ferramentas & Bibliotecas** usa **chips de texto** (não logos), por serem itens sem logo consistente.

### 4.4 Contato (seção clara, centralizada)
- Label "Contato", título "Vamos conversar".
- **E-mail visível** e clicável (`mailto:`): emiliocalvet@gmail.com.
- Botão **"Baixar currículo (PDF)"** (variante escura).
- Linha de ícones sociais repetida (GitHub, LinkedIn, WhatsApp, Telegram).

### 4.5 Footer (faixa preta, cantos superiores arredondados)
- Minimalista: `© 2026 Emilio Calvet`.

## 5. Conteúdo (copy)

- **Subtítulo:** `Tech Lead · Desenvolvedor Full Stack`
- **Sobre:**
  > Tech Lead com perfil generalista, atuando entre arquitetura de sistemas, desenvolvimento full-stack (back-end, front-end web e mobile) e infraestrutura cloud/DevOps. Trabalho hands-on em monolitos modulares, microsserviços e SaaS multi-tenant. Graduado em Engenharia de Software pela UFC, com MBA em Arquitetura de Software em andamento na Full Cycle.
- **Chips do Sobre:** Arquitetura de sistemas · Full-stack · Cloud / DevOps · SaaS multi-tenant · IA / busca vetorial

> Nota de precisão: o texto **não** afirma graduação em Ciência da Computação (cursada, não concluída). Apenas Engenharia de Software (concluída) + MBA em andamento.

## 6. Stack técnico — taxonomia final

**Com logos monocromáticos:**

| Grupo | Itens |
|---|---|
| Linguagens | Java, TypeScript, JavaScript, Python, Go, Dart |
| Back-end | Spring Boot, Node.js, NestJS, Express, FastAPI |
| Front-end | React, Next.js, Angular |
| Mobile | Flutter, React Native |
| Bancos de dados | PostgreSQL, MongoDB, Redis |
| Cloud & DevOps | AWS, Docker, Kubernetes, GitHub Actions, Nginx, Caddy, Traefik, pfSense, SonarQube |
| Observabilidade | Grafana, Prometheus, Loki, Zabbix |

**Como chips de texto:**

| Grupo | Itens |
|---|---|
| Ferramentas & Bibliotecas | Firebase, FCM, OneSignal, Keycloak / SSO Gov.br, Google ML Kit, OpenCV, JasperReports, MinIO, Passbolt, MCP, Git, Linux |

## 7. Sistema visual

- **Paleta:** tinta `#0a0a0a`, fundo `#ffffff`, seções alternadas sutis `#fafafa`, texto secundário `#666`, bordas `#ececec`. Sem cores de acento (P&B puro).
- **Tipografia:** Blinker. Nome em peso forte (700) e tamanho grande; labels de seção em maiúsculas, espaçadas, cinza.
- **Componentes:** tile de logo (quadrada, borda clara, hover com leve `translateY` + sombra), chip (pílula com borda), botão CTA (claro no hero / escuro no contato), ícone social (quadrado arredondado com contorno).
- **Cantos arredondados** e sombras suaves, como no estilo atual.

## 8. Assets

- **Foto:** otimizar a foto profissional (original `32288E49-...png`, 1,7 MB) para ~150–250 KB; salvar em `images/` (ex.: `images/perfil.jpg` ou `.webp`) e referenciar no hero. Substitui o `perfil-8bit.png`.
- **Logos:** padronizar em **SVG monocromáticos** (ex.: Simple Icons / Devicon), baixados **localmente** em `images/techs-logos/` (sem CDN externo), renderizados na cor tinta. Reaproveitar os logos já existentes quando possível (aplicando tratamento monocromático). Para marcas sem ícone adequado (ex.: pfSense), usar **tile com rótulo de texto** no mesmo estilo das demais.
- **Currículo:** `curriculo_emilio_calvet_final.pdf` (já na raiz) é o alvo do botão de download.
- **Banner antigo:** `images/banner.png` (1,8 MB) deixa de ser usado — pode ser removido.
- **Favicon (opcional):** monograma "EC" simples.

## 9. Responsividade e acessibilidade

- **Mobile-first**, coluna única; grade de logos com `repeat(auto-fill, minmax(...))` que se adapta.
- Imagens com `alt`; ícones sociais com `aria-label`; contraste P&B já alto.
- Links externos com `rel="noopener"`.

## 10. Fora de escopo (pode ser adicionado depois sem quebrar a estrutura)

- Linha do tempo de experiência (Colmeia, Golden Cloud).
- Seção detalhada de formação acadêmica.
- Destaque da publicação acadêmica (BRAJETS).
- Internacionalização (versão em inglês), dark mode toggle, blog, animações elaboradas.

## 11. Critérios de aceitação

1. `index.html` e `styles/style.css` reescritos, sem nenhum Lorem ipsum.
2. As 5 seções (Hero, Sobre, Stack, Contato, Footer) presentes e fiéis ao mockup aprovado.
3. Stack com todos os itens da seção 6, na divisão logos/chips definida.
4. Foto profissional otimizada no hero; botão de currículo baixa o PDF; e-mail clicável.
5. Layout responsivo (testado em largura de celular e desktop) e P&B consistente.
6. Continua sem build: abrir/servir a raiz exibe a página pronta.

## 12. Decisões abertas

- Formato final da foto otimizada (`.jpg` vs `.webp`) — decidir na implementação pelo melhor custo/qualidade.
- Conjunto exato de ícones SVG por marca — resolver caso a caso na implementação, com fallback de tile-texto.
