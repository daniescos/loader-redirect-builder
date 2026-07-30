# Loader Redirect Link Builder

Ferramenta para gerar links `LoaderScreenGenericRedirect` (deep link nativo + smart link universal) seguindo as regras do guia "Redirecionamento Logado Para Sites Externos".

Site estático, 100% client-side (sem backend, sem custo de hospedagem).

## Regras

As regras implementadas em `index.html` estão documentadas em [`REGRAS.md`](./REGRAS.md), resumo do guia oficial `MA-Redirecionamento Logado Para Sites Externos-300726-123459.pdf` (nesta mesma pasta). Ao editar `index.html`, confira `REGRAS.md` pra manter os dois consistentes.

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

Todas essas opções aceitam o mesmo `index.html` sem nenhuma alteração no código.
