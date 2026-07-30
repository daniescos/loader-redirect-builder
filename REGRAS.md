# Regras — LoaderScreenGenericRedirect

Fonte: `MA-Redirecionamento Logado Para Sites Externos-300726-123459.pdf` (guia oficial, mesma pasta). Este arquivo é o resumo em markdown usado como referência pra manter o `index.html` consistente.

## O que é

`LoaderScreenGenericRedirect` é tela invisível do app que:
1. Recebe parâmetros via deep link (query string).
2. Descobre pelo `type` o domínio/URL base de destino.
3. Monta URL final: `URL_base + path + "?" + parâmetros_extras`.
4. Abre em WebView (padrão) ou navegador externo (`external=true`).

Regra não seguida → tela de erro genérica ("Não conseguimos concluir o seu redirecionamento").

## Dois formatos equivalentes

| Formato | Uso | Exemplo |
|---|---|---|
| Deep link nativo | Dentro do app, push | `minhaclaro://LoaderScreenGenericRedirect?type=residencial&path=minhas-assinaturas` |
| Smart link universal | Canais externos (SMS, e-mail, WhatsApp) | `https://app-minhaclaro.clarobrasil.mobi/?deeplink=LoaderScreenGenericRedirect?type=residencial&path=minhas-assinaturas` |

Mesmos parâmetros e regras nos dois — só muda o envelope. No smart link o `?` interno **não** é escapado (não vira `%3F`).

## Parâmetros de configuração

| Parâmetro | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `type` | string (seção "Valores de type") | **Sim** | — | Domínio/serviço de destino. Case-insensitive. |
| `path` | string | Não | `""` | Concatenado logo após URL base. Não deve começar com `/`. |
| `external` | boolean | Não | `false` | `true` = navegador externo. `false`/omitido = WebView. |
| `tokenAA` | boolean | Não | `true` | Injeta cookie "Token AA". Só com `external=false`. |
| `tokenPing` | boolean | Não | `false` | Injeta cookie "Token Ping". Só com `external=false`. |
| `contractSession` | boolean | Não | `false` | Injeta cookie sessão de contrato. Só com `external=false`. |

Booleanos devem ser literalmente `true`/`false` na query string — qualquer outro valor é ignorado e o padrão aplicado.

## Valores válidos de `type`

| `type` | URL base | Precisa login? |
|---|---|---|
| `residencial` | `https://minhaclaroresidencial.claro.com.br/` | Sim — Token Ping |
| `flex` | `https://flex.claro.com.br/` | Sim — Token AA/Flex |
| `minha_claro` | `https://minhaclaro.claro.com.br/` | Sim — token de autenticação |
| `claro_tv_mais` | `https://www.clarotvmais.com.br/` | Sim — Token Ping |
| `claro_store` | URL de marketplace gerada dinamicamente (já vem com `?origin=MINHA_CLARO_MOVEL`) | Sim (login por MSISDN) |
| `planos_celular` | `https://planoscelular.claro.com.br/` | Não |
| `claro_site` | `https://claro.com.br/` | Não |

Únicos 7 valores válidos. Qualquer outro (erro de grafia, ou `type` ausente) → tela de erro. Para os que precisam login: sem token no momento do redirect, também cai em erro mesmo com URL correta.

## Parâmetros extras

Qualquer parâmetro fora da lista acima (`type`, `path`, `external`, `tokenAA`, `tokenPing`, `contractSession`) é repassado direto pra URL final sem transformação de nome, só URL-encode do valor. Parâmetros repetidos são passados como estão, sem tratamento de array.

Exemplos: `affiliateId`, `redirect-feature`, `redirect`/`action`, `utm_*`, `q`.

Na UI (`index.html`), os seis mais usados (`affiliateId`, `utm_source`, `utm_medium`, `utm_campaign`, `utm_id`, `utm_term`) aparecem como campos fixos com toggle na seção 3 — liga só o que for preencher. Qualquer outro nome de parâmetro vai em "+ adicionar parâmetro".

## Regra de concatenação de `path`

```
URL_final = URL_base_do_type + path + "?" + parâmetros_extras
```

Concatenação literal (string + string), sem inserir `/`. Como toda URL base já termina com `/`:

- ✅ `path=minhas-assinaturas` → `.../minhas-assinaturas`
- ❌ `path=/minhas-assinaturas` → `..//minhas-assinaturas` (barra duplicada)

## Parâmetros injetados automaticamente

Sobrescrevem qualquer valor manual com mesmo nome — não enviar manualmente:

| `type` | Parâmetro injetado |
|---|---|
| `flex` | `token=<token do usuário>` |
| `claro_tv_mais` | `accessTokenId=<token do usuário>` |

## Checklist pra criar URL nova

1. Escolher `type` válido.
2. Confirmar se exige login — só usar em contexto autenticado.
3. Definir `path` (sem `/` inicial).
4. Definir `external`.
5. Ajustar `tokenAA`/`tokenPing`/`contractSession` só se padrão não servir.
6. Adicionar parâmetros extras necessários (UTMs, filtros, IDs).
7. Gerar as duas versões (deep link + smart link).
8. Testar em homologação antes de produção/campanha.
