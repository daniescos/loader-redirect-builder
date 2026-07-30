# Loader Redirect Link Builder

Ferramenta interna para gerar links `LoaderScreenGenericRedirect` — a tela invisível do app Minha Claro que recebe parâmetros por deep link, resolve o domínio de destino a partir de um `type` e redireciona o usuário logado (WebView ou navegador externo). Gera os dois formatos válidos, já validados contra as regras oficiais, prontos pra copiar.

Site estático, single-file (`index.html`), 100% client-side — sem backend, sem build, sem custo de hospedagem.

## Acesso

Ferramenta restrita ao time — protegida por **Cloudflare Access** (login por código enviado ao e-mail, sem senha).

- Link: `https://loader-redirect-builder.pages.dev` (confirmar nome exato do projeto após criação no Cloudflare).
- Se seu e-mail ainda não tem acesso, a própria tela de login mostra a opção de **solicitar acesso** — digite seu e-mail corporativo e aguarde aprovação.
- Aprovação: **Daniel Escosteguy** (`daniel.escosteguy.3@globalhitss.com.br`).

## O que a ferramenta faz

- Monta o **deep link nativo** (`minhaclaro://LoaderScreenGenericRedirect?...`), usado dentro do app (push, banners internos).
- Monta o **smart link universal** (`https://app-minhaclaro.clarobrasil.mobi/?deeplink=...`), usado em canais externos (SMS, e-mail, WhatsApp).
- Estima a **URL final de destino**, reconstruindo `URL_base_do_type + path + query`.
- Valida em tempo real: `path` começando com `/` (barra duplicada), parâmetro reservado reaproveitado nos extras, e parâmetro que o app sobrescreve automaticamente (`token`/`accessTokenId` conforme o `type`).

## Como usar

A UI tem 3 seções de entrada + o painel de saída:

**1 · Destino** — escolhe o `type` (domínio/serviço de destino) e opcionalmente um `path`, concatenado direto após a URL base do `type`.

**2 · Comportamento** — toggles para `external` (WebView vs navegador externo), `tokenAA`, `tokenPing` e `contractSession` (cookies de sessão injetados na WebView; sem efeito quando `external=true`).

**3 · Parâmetros extras** — os parâmetros mais usados (`affiliateId`, `utm_source`, `utm_medium`, `utm_campaign`, `utm_id`, `utm_term`) já vêm prontos como toggle: liga só o que for usar, preenche o valor. Pra qualquer outro parâmetro fora dessa lista, usa "+ adicionar parâmetro".

O painel à direita atualiza os dois links e a URL final a cada mudança, com botão de copiar em cada um.

### Valores válidos de `type`

| `type` | Precisa login? |
|---|---|
| `residencial` | Sim — Token Ping |
| `flex` | Sim — Token AA/Flex |
| `minha_claro` | Sim — token de autenticação |
| `claro_tv_mais` | Sim — Token Ping |
| `claro_store` | Sim — login por MSISDN |
| `planos_celular` | Não |
| `claro_site` | Não |

Regras completas (URL base de cada `type`, parâmetros auto-injetados, concatenação de `path`, etc.) estão em [`REGRAS.md`](./REGRAS.md), resumo do guia oficial `MA-Redirecionamento Logado Para Sites Externos-300726-123459.pdf` (nesta mesma pasta). Ao editar `index.html`, confira `REGRAS.md` pra manter os dois consistentes.

## Deploy (Cloudflare Pages + Access)

Publicado no **Cloudflare Pages**, deploy automático a cada `git push` na `main` via GitHub Action (`.github/workflows/deploy.yml`). Setup inicial (uma vez só):

1. Criar conta gratuita em https://dash.cloudflare.com/sign-up.
2. **Workers & Pages → Create → Pages** → criar projeto `loader-redirect-builder` (método "Direct Upload").
3. **Meu Perfil → API Tokens** → criar token com template "Edit Cloudflare Pages"; copiar também o **Account ID** (barra lateral do dashboard).
4. No repositório GitHub, em **Settings → Secrets and variables → Actions**, adicionar:
   - `CLOUDFLARE_API_TOKEN`
   - `CLOUDFLARE_ACCOUNT_ID`
5. Dar `git push` — a Action publica em `https://loader-redirect-builder.pages.dev`.
6. Ativar **Zero Trust** no dashboard (escolher um nome de time — gratuito até 50 usuários).
7. **Access → Applications → Add an application → Self-hosted**, apontando pro domínio `.pages.dev` do projeto.
8. Criar política: Action = **Allow**, e-mails permitidos (começa com o e-mail do Daniel, cresce conforme aprova gente).
9. Habilitar **solicitação de acesso (self-serve)** nas configurações da aplicação — quem não tem permissão vê um formulário de e-mail + motivo; Daniel recebe e aprova no painel (Access → Applications → pedidos pendentes).
10. Identidade padrão: **One-time PIN** (código por e-mail) — já vem habilitado, sem precisar configurar SSO.

Qualquer alteração no `index.html`/`README.md`, basta `git add . && git commit -m "ajuste X" && git push` — o Cloudflare Pages atualiza automaticamente.

## Notas técnicas

- Tema claro por padrão (paleta e tipografia alinhadas ao design system da equipe), com alternância pra tema escuro pelo botão no header — a escolha fica salva no navegador.
- Fontes (Barlow / Barlow Condensed) carregadas via Google Fonts CDN — requer internet no primeiro carregamento.
