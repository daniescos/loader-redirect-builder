# Loader Redirect Link Builder

Ferramenta interna para gerar links `LoaderScreenGenericRedirect` — a tela invisível do app Minha Claro que recebe parâmetros por deep link, resolve o domínio de destino a partir de um `type` e redireciona o usuário logado (WebView ou navegador externo). Gera os dois formatos válidos, já validados contra as regras oficiais, prontos pra copiar.

Site estático, single-file (`index.html`), 100% client-side — sem backend, sem build, sem custo de hospedagem.

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

## Deploy no GitHub Pages (gratuito)

1. Crie um repositório novo no GitHub (pode ser público ou privado — Pages funciona nos dois, mas repo privado exige plano pago pra Pages público; se for uso interno da equipe, use **público** e compartilhe só a URL).
2. Neste diretório, rode:
   ```bash
   git init
   git add .
   git commit -m "Loader redirect link builder"
   git branch -M main
   git remote add origin https://github.com/SEU_USUARIO/loader-redirect-builder.git
   git push -u origin main
   ```
3. No GitHub: **Settings → Pages → Source → Deploy from a branch → branch `main` / folder `/ (root)`** → Save.
4. Em ~1 minuto o site fica disponível em:
   `https://SEU_USUARIO.github.io/loader-redirect-builder/`

## Atualizações futuras

Qualquer alteração no `index.html`, basta:
```bash
git add . && git commit -m "ajuste X" && git push
```
O Pages atualiza automaticamente em segundos.

## Alternativas de hospedagem gratuita (caso prefira)

| Opção | Vantagem |
|---|---|
| GitHub Pages | Já integrado ao seu fluxo de git, zero config extra |
| Vercel | Deploy automático a cada push, domínio custom fácil |
| Netlify | Drag-and-drop do arquivo direto no painel, sem git |
| Cloudflare Pages | Rápido, ilimitado, bom se já usa Cloudflare |

Todas essas opções aceitam o mesmo conteúdo da pasta (`index.html` + `assets/`) sem nenhuma alteração no código.

## Notas técnicas

- Tema claro por padrão (paleta e tipografia alinhadas ao design system da equipe), com alternância pra tema escuro pelo botão no header — a escolha fica salva no navegador.
- Fontes (Barlow / Barlow Condensed) carregadas via Google Fonts CDN — requer internet no primeiro carregamento.
