# 05 · PAINEL (editor `/painel/index.html`)

> Etapa 4 do mapeamento do Conheça Meu Trabalho.
> Fonte: repositório `lencinaai/conheca-meu-trabalho`, branch `master`, commit `c3783eb`.
> Arquivo principal: `painel/index.html` (5.445 linhas, HTML + CSS + JS em um único arquivo).
> Todas as referências de linha deste documento apontam para esse arquivo, salvo indicação diferente.

---

## 1. O que o painel é

O painel é o editor da página do usuário. Ele carrega o perfil do Firestore, monta um formulário
inteiro a partir desse perfil, mostra uma prévia ao vivo da página pública dentro de um `iframe`, e
grava de volta no Firestore quando a pessoa clica em "Salvar e publicar".

Três decisões estruturais explicam quase tudo no arquivo:

1. **Um objeto só.** Todo o estado editável vive em `PERFIL`, um objeto JSON único. O formulário
   escreve nele por caminho de string (`galeria.itens.2.titulo`), e não por campos nomeados.
2. **Re-render total.** Quase toda ação chama `buildForm()`, que recria o HTML inteiro do formulário
   e refaz os listeners. Não existe atualização cirúrgica de um campo isolado.
3. **Prévia isolada.** A página pública roda em um `iframe` separado (`pagina.html`) e recebe o
   `PERFIL` por `postMessage`. O painel nunca renderiza a página em si.

---

## 2. Mapa do arquivo

`painel/index.html` tem cinco blocos `<script>` e um bloco `<style>`. A ordem no arquivo não é a
ordem lógica de execução: o núcleo do painel é o quarto bloco.

| Faixa | Bloco | Conteúdo |
| --- | --- | --- |
| 3 a 23 | `<head>` | Meta tags, PWA (`manifest.webmanifest`), `noindex,nofollow`, fontes Google, Font Awesome |
| 24 a 731 | `<style>` | CSS completo do painel: paleta grafite/vidro/dourado, gate de login, grid do admin, prévia, modais |
| 733 a 1167 | `<body>` | Toda a estrutura estática: boot, gate, app, views das subpáginas, modais |
| 1168 a 1178 | scripts externos | Firebase compat (app, firestore, auth) e as libs locais |
| 1179 a 1512 | script 1 | Presets de tema e Modelos Detalhados (`/admin/presets`, `/admin/modelos-detalhados`) |
| 1513 a 1726 | script 2 | Organizar blocos (modo de reordenação na barra lateral) |
| 1727 a 2597 | script 3 | Capa / primeira dobra (`/admin/capa`) |
| 2599 a 5430 | script 4 | Núcleo: estado, auth, roteamento, `buildForm`, uploads, planos, salvar |
| 5432 a 5443 | script 5 | Registro do service worker (`/sw.js`) |

### Dependências carregadas (linhas 1168 a 1178)

| Arquivo | Papel |
| --- | --- |
| `firebase-app-compat` / `firestore-compat` / `auth-compat` (10.12.0) | SDK do Firebase em modo compat |
| `../lib/firebase.js` | Expõe `window.PP`: `auth`, `db`, `getPerfil`, `savePerfil`, `logout` |
| `../lib/cloudinary.js` | Expõe `window.CLD`: `upload`, `del`, `opt` (upload assinado com fallback) |
| `../lib/presets.js` | `window.PRESETS`: os modelos de profissão (17+) |
| `../lib/plano.js?v=20260806a` | `window.PLANO`: fonte única dos planos e entitlements |
| `../lib/presets-theme.js` | Aplicação dos presets visuais no tema |
| `../lib/modelos-detalhados.js` | Catálogo dos modelos premium |
| `../lib/blocos.js` | `window.BLOCOS`: ordem canônica das seções da página |
| `../lib/hero.js?v=20260724c` | Modelos de capa e cálculo do hero |

Observação: as libs são carregadas por `<script>` global, sem módulos. Toda a comunicação entre
blocos acontece por `window.*` e por funções globais.

---

## 3. Estado global

Declarado no topo do script 4 (linhas 2600 a 2614).

| Variável | Papel |
| --- | --- |
| `PERFIL` | O objeto editado. Fonte única de verdade do conteúdo da página |
| `SLUG` | O `@` atual, que forma o endereço público |
| `ORIGSLUG` | O `@` com que a sessão começou. Serve para detectar troca de endereço no salvar |
| `REALMODE` | `true` quando há sessão Firebase. `false` cai no modo demonstração (baixa JSON) |
| `SAVED_SNAPSHOT` | Serialização do último estado publicado, base da guarda de alterações |
| `SUPPRESS_UNLOAD` | Desliga o aviso nativo do navegador quando a saída já foi confirmada |

Constantes de produto no mesmo bloco: `DOMINIO` (`conhecameutrabalho.com`), `SUPORTE_EMAIL`,
`SUPORTE_WPP`, `SUPORTE_IG`, `FONT_OPTS`, `SKIN_FONT_DEFAULTS`, `REDE_OPTS` (44 redes, linhas 2633
a 2690) e `SERV_ICON_OPTS` (linhas 2691 a 2782).

Dois parâmetros de URL são capturados logo no carregamento e guardados em `localStorage`:
`?cupom=sigla` vira `cupomFundador`, e `?assinar=1` (ou o legado `?pro=1`) vira `ppIntentPro`, que
abre o checkout assim que a conta entra.

---

## 4. Ciclo de vida

```
carregamento
   └─ #boot (splash) cobre a tela
        └─ restauração de sessão (linha 3439)
             ├─ sem sessão  → #gate (login / criar conta)
             └─ com sessão  → enterApp(user)
                                ├─ lê users/{uid} → nome, slug
                                ├─ PP.getPerfil(slug) → PERFIL (ou blankPerfil)
                                ├─ REALMODE = true, markSaved()
                                ├─ frame.src = ../pagina.html?p=SLUG&edit=1
                                ├─ buildForm() → bind() → setupUploads() → setupSectionToggles()
                                └─ showAdminView(location.pathname)
```

- **`#boot`** (linha 736) é um splash que evita o flash da tela de login em cada F5.
- **`#gate`** (linha 739) é a tela de entrada, com o carrossel 3D de páginas fictícias montado por
  `montarGateRing()` (linha 2874) e duas views alternadas: `#viewLogin` e `#viewSignup`.
- **`enterApp(user)`** (linha 2969) é a porta de entrada do app: resolve o slug, carrega o perfil,
  liga o `iframe` da prévia, monta o formulário e tenta `POST /api/claim-pro` para ativar um plano
  pago já pago no checkout.
- **`blankPerfil(nome)`** (linha 2944) clona o preset `branco` para uma conta nova.

### Autenticação

Tudo pelo Firebase Auth em modo compat.

| Fluxo | Onde | Notas |
| --- | --- | --- |
| Login | linha 3477 | `#loginBtn`, erros traduzidos por `authErro()` (linha 2931) |
| Criar conta | linha 3488 | `#signupBtn`, valida senha e confirmação, grava `users/{uid}` |
| Esqueci a senha | linha 2919 | `#forgotPw`, e-mail de redefinição do próprio Firebase |
| Trocar e-mail / senha | `setupAccount()` (linha 3987) | Ambos reautenticam antes |
| Apagar conta | `setupAccount()` | Cascata: imagens no Cloudinary (`collectImages`), `perfis/`, `users/`, login |
| Sair | linha 4950 | Passa pela guarda de alterações não salvas |

---

## 5. Roteamento e views

O painel é uma SPA com rotas reais via `history.pushState`. O roteador vive no IIFE da linha 3210.

```js
VIEWS = {
  '/admin/conta':              'viewConfig',
  '/admin/configuracoes':      'viewConfig',
  '/admin/leads':              'viewLeads',
  '/admin/modelos':            'viewModelos',
  '/admin/planos':             'viewPlanos',
  '/admin/presets':            'viewPresets',
  '/admin/modelos-detalhados': 'viewModelosDet',
  '/admin/capa':               'viewCapa'
}
```

- `/admin` é a home: barra lateral com o formulário mais a prévia.
- Qualquer outra rota esconde `#admMain` e mostra a `<section class="adm-view">` correspondente.
- `go(path)` empurra o histórico e chama `showAdminView(path)`; `popstate` refaz o caminho inverso.
- `setNavActive(path)` marca o item ativo no cabeçalho.
- Ao entrar em `/admin/capa`, o roteador dá `src` ao `#capaFrame` (linha 3236) e reempurra a prévia.

### Layout da home

```
┌───────────────────────── header .adm-head ─────────────────────────┐
│ nav: Início · Profissões · Presets · Modelos Detalhados · Capa ·   │
│      Leads · Configurações      | selo do plano · compartilhar ·   │
│                                   Ver planos · Sair                │
├──────────────── aside .adm-side ─────────┬─────── .adm-preview ────┤
│ [Fechar]  [Organizar blocos]             │ desktop/celular · zoom  │
│ #form  (todos os grupos <details>)       │ URL pública + copiar    │
│ [Abrir página]  [Salvar e publicar]      │ ┌─ iframe #frame ─────┐ │
│                                          │ │  pagina.html?edit=1 │ │
└──────────────────────────────────────────┴─┴─────────────────────┴─┘
                     .adm-tabbar (somente celular)
        Editar · Prévia · Modelos · Leads · Config
```

No celular, `#admTabbar` (linha 1090) alterna metades da mesma home: `Editar` e `Prévia` usam a
classe `mob-preview` em `#admMain`, e as outras abas viram rotas.

---

## 6. O editor: `buildForm()`

`buildForm()` (linhas 4230 a 4508) é o coração do painel. Ele:

1. Guarda quais grupos estavam abertos e a posição do scroll, para não pular ao topo a cada
   alteração.
2. Normaliza o `PERFIL` (garante arrays e objetos que podem faltar em perfis antigos).
3. Atualiza o selo do plano.
4. Escreve `#form.innerHTML` inteiro com uma sequência de `<details class="group">`.
5. Restaura os grupos abertos e o scroll.
6. Rechama `bind()`, `setupUploads()`, `setupSectionToggles()` e afins.

### Grupos do formulário, na ordem em que são escritos

| Grupo | id | Linha |
| --- | --- | --- |
| Modelo (profissão) | `group--modelo` (oculto; migrou para `/admin/modelos`) | 4246 |
| Identidade | `grp-identidade` | 4257 |
| Cores e fontes | `grp-cores` | 4307 |
| Contato | `grp-contato` | 4321 |
| Sobre | `grp-sobre` | 4330 |
| Cursos / Formação | `grp-cursos` | 4341 |
| Redes sociais | `grp-redes` | 4353 |
| Galeria / Trabalhos | `grp-galeria` | 4363 |
| Segunda galeria | `grp-galeria2` | 4379 |
| Serviços | `grp-servicos` | 4393 |
| Local de atendimento | `grp-local` | 4406 |
| Depoimentos | `grp-depoimentos` | 4420 |
| Agenda de eventos | `grp-eventosAgenda` | 4434 |
| Eventos realizados | `grp-eventosCobertos` | 4455 |
| Marcas e parcerias | `grp-marcas` | 4475 |

Os ids `grp-*` não são decorativos: o click-to-edit da prévia os usa como alvo (seção 9).

### Helpers de campo

| Helper | Linha | O que gera |
| --- | --- | --- |
| `fText(label, valor, path, linhas)` | dentro do script 4 | `input` ou `textarea` com `data-path` |
| `upslot(path, url, ratio, locked, ph)` | 3634 | Slot de upload com barra Trocar / Ajustar / Excluir |
| `alignIcons(cur, attr, path)` | 3645 | Trio de ícones de alinhamento |
| `fontSelectHTML(role, p)` | 3555 | Seletor de fonte customizado (`.fpick-*`) |
| `secToggleBtn(key)` | 3955 | Olho de ocultar/exibir a seção |
| `mediaBlock(prefix, obj, opts)` | 3696 | Bloco de mídia universal (seção 7) |

### `bind()` e a escrita por caminho

`bind()` (linhas 4538 a 4645) liga tudo o que o HTML recém-escrito precisa:

- `[data-path]`: no `input`/`change`, chama `setPath(PERFIL, path, valor)` e `pushPreview()`.
- `[data-align-path]`: grava o alinhamento e alterna a classe `on`.
- `[data-galcarousel]`, `[data-monograma]`: toggles booleanos diretos no `PERFIL`.
- Campo de WhatsApp: máscara `maskWhats()` enquanto digita.
- `input[data-path^="redes."][data-path$=".url"]`: `detectRede(url)` escolhe o ícone sozinho.
- `[data-add]`: acrescenta um item ao array certo, checando o teto de blocos do plano, e refaz o
  formulário.
- `[data-rm]`: apaga as imagens do card no Cloudinary e remove o item do array.
- `input[data-font-role]`: grava a fonte por papel (`display`, `body`) em `PERFIL.tema`.
- `[data-gopro]`: atalho para `/admin/planos`.

`setPath` (4526) e `getPath` (3652) resolvem caminhos como `galeria.itens.2.titulo`. `rmPath` (3808)
faz o `splice` correspondente. Esse trio é o que permite um formulário genérico sobre um JSON livre.

---

## 7. Bloco de mídia universal (`mediaBlock`)

Um componente só, reusado em galeria, segunda galeria, serviços, agenda, eventos realizados e
marcas. Ele expõe cinco modos, escolhidos por uma faixa de botões `data-setmodo="prefixo|modo"`:

| Modo | Campos que passam a valer | Plano |
| --- | --- | --- |
| `single` | `capa` | Grátis |
| `carrossel` | `capa` + `fotos[]` (até 5 no modo de teste) | Elite |
| `antes_depois` | `antes` + `depois` | Elite |
| `agenda` | `capa` (16/9) + `data` + `hora` | Grátis |
| `card` | Sem foto: `icon` + `titulo` + `desc` | Grátis |

Campos opcionais comuns a todos: título, descrição, alinhamento da descrição, data, valor, local,
preço, condição de pagamento (apenas no template corretor), nome e link do botão. Sem link, o botão
do card abre o WhatsApp.

No modo carrossel aparece ainda o **lead gate** (`data-leadgate`): pedir nome e telefone para
liberar as fotos, com dois modos (`bloquear` e `suave`) e texto de convite editável. É recurso
Elite, e alimenta a subpágina Leads.

`midiaModo(obj)` (linha 3657) infere o modo quando o campo não existe, mantendo retrocompatibilidade
com o antigo `tipo:'comparacao'`.

---

## 8. Uploads e recorte

`setupUploads()` (linhas 3809 a 3952) liga todos os caminhos de imagem.

- **Slot vazio**: clique abre o seletor de arquivo; arrastar e soltar também funciona.
- **Slot com imagem**: a barra `.upacts` assume, com `data-uptrocar`, `data-upajustar` e
  `data-updelete`.
- **Fluxo do envio**: `uploadFoto(file)` chama `CLD.upload(file, 'paginas-pro/' + SLUG)`, depois abre
  o modal de recorte, grava a URL em `PERFIL` junto de `...Pos`, `...Scale` e `...Ratio` (derivados
  por `posPath`, `scalePath` e `ratioPath`, linhas 5427 a 5429) e apaga a imagem antiga no
  Cloudinary.
- **Modal de recorte** (`#cropModal`, linhas 1100 a 1131, lógica em 5267 a 5387): arrasta e amplia a
  imagem, com cinco proporções (`1/1`, `4/5`, `9/16`, `16/9`, `4/3`), botão de centralizar e
  confirmação.
- **Múltiplas fotos**: `data-addmidia` (carrossel, qualquer prefixo) e `data-addlocalfoto` (fotos do
  local) aceitam seleção múltipla e respeitam os tetos do plano.
- **Remoção**: `data-rmmidia` e `data-rmlocalfoto` tiram do array e chamam `CLD.del(url)`.

Toda remoção de imagem tenta apagar o arquivo no Cloudinary. Isso vale também no `data-rm` de um
card inteiro e na exclusão da conta.

---

## 9. Prévia ao vivo

- **`pushPreview(only)`** (linha 4661) envia `{type:'preview', perfil:PERFIL}` por `postMessage` para
  o `iframe` visível: `#capaFrame` quando a subpágina Capa está aberta, `#frame` no resto. O
  parâmetro `only` força um destino específico, usado nos eventos de `load` para não deixar uma
  prévia que terminou de carregar em segundo plano girando o loader para sempre.
- **Origem**: `location.origin`, com `*` apenas em `file:`.
- **Click-to-edit** (linha 5388): a página pública manda `{type:'edit-focus', section, field}`; o
  painel abre o `<details>` de id igual a `section`, rola até ele e pisca o campo `data-path` ou
  `data-upload` correspondente. Quando a subpágina Capa está aberta, o mesmo evento é redirecionado
  para os campos de `#capaTextos` por um mapa próprio, sem sair da Capa.
- **Zoom e aparelho** (linhas 3122 a 3209): desktop e celular (390x760), escala calculada por
  `pvZoomLayout()`, zoom manual de 50% a 300%, e reencaixe no `resize` e ao recolher a barra
  lateral.

---

## 10. Subpáginas

### Profissões (`/admin/modelos`)

`fillModelos()` (linha 3024) monta a grade a partir de `MODELOS_CARDS` (linha 3018) e de
`window.PRESETS`. Cada card `data-mod-tpl` aplica o preset por `applyPreset(key)` (linha 3775), que
troca textos de exemplo preservando `@`, nome, WhatsApp e foto.

### Presets de tema (`/admin/presets`) e Modelos Detalhados (`/admin/modelos-detalhados`)

Script 1 (linhas 1179 a 1512). Lê `Presets/index.json`, monta a grade, e abre um modal de detalhe
por preset com quatro ações: Visualizar (abre `pagina.html?preview=1` em um modal), Aplicar,
Restaurar padrão e Remover. A persistência é feita em `PERFIL.selectedPresetId`,
`PERFIL.selectedModeloPremium` e `PERFIL.userThemeOverrides`, e é salva junto no "Salvar e publicar"
comum. Recurso Elite: `#presetsLock` e `#modelosDetLock` cobrem a grade quando o plano não atende.

### Capa (`/admin/capa`)

Script 3 (linhas 1727 a 2597). É a subpágina mais complexa depois do núcleo.

- **Modelos de capa** (`data-captype`): `profile`, `hero_widescreen`, `hero_with_png`, `card`. Os
  dois modelos hero exigem Elite.
- **Por aparelho** (`data-capdev`): desktop, tablet e celular têm ajustes próprios, com botões de
  "usar também no celular / no tablet" para copiar de um para o outro.
- **Grupos**: Textos da capa, Ajuste automático, Imagem de fundo (16/9), Bloco de texto, PNG
  transparente da pessoa, Elementos reposicionados.
- **Arraste na prévia**: `capaDragSync()` (linha 4686) liga o modo de arraste dentro do `iframe`; o
  `iframe` devolve `{type:'capa-move', target, device, x, y}` e o painel grava em `heroElements`,
  `identityContentSettings`, `personaSettings` ou `heroSettings.height`.
- **Histórico próprio**: `_capaPush`, `_capaUndoDo` e `_capaRedoDo` (linhas 1998 a 2045) dão desfazer
  e refazer só para a capa, com os botões `#capaUndo` e `#capaRedo`.
- **Validação de arquivo**: `_validarImagemCapa()` checa tipo, peso e dimensão mínima antes de subir
  (fundo até 8 MB e 640x360; PNG até 5 MB e 200x200).
- **Salvar**: `#capaSaveBtn` chama o mesmo `salvarTudo()` do Início. É o mesmo `PERFIL`.

### Organizar blocos

Script 2 (linhas 1513 a 1726). Não é uma rota: é um modo aplicado ao próprio `#form`. Ao ligar, os
cards da barra lateral viram uma lista arrastável (`form--organize`), com setas de subir e descer
(`data-orgdir`) e uma barra de ações (Concluir, Cancelar, Ordem padrão). Só as seções do meio
reordenam; Identidade, Contato, Números e Redes ficam fixos porque vivem no hero e no rodapé. A
ordem é gravada em `PERFIL.ordemSecoes` e normalizada por `BLOCOS.normalize()`. Recurso Elite.

### Planos (`/admin/planos`)

`fillPlanos()` (linha 5144) monta a grade a partir de `window.PLANO`, com alternância mensal/anual
(`data-cycle`). `checkoutURL()` (linha 5135) monta o link do checkout Cakto, aplicando o cupom de
indicação guardado no `localStorage`. `resgatarCodigo()` (linha 4993) troca um código de presente
por plano Elite via `/api/founder`.

### Leads (`/admin/leads`)

`fillLeads()` (linha 3362) lê os contatos capturados pelo lead gate. `_ldRender()` desenha a lista e
`_ldWire()` (linha 3399) implementa o modo de seleção: Gerenciar entra no modo, Selecionar todos
marca tudo, Excluir manda `DELETE /api/lead` com os ids, Cancelar sai. O contador do cabeçalho
(`#leadBadge`) acompanha.

### Configurações (`/admin/configuracoes` e `/admin/conta`)

Mesma view (`#viewConfig`) com quatro abas laterais (`data-cfgtab`): Conta, Segurança, Suporte,
Privacidade e conta. Conta mostra e-mail, plano e o bloco de convite; Segurança troca e-mail e
senha; Suporte envia mensagem por `/api/suporte` e oferece WhatsApp, e-mail e Instagram;
Privacidade abre os documentos em modal (`data-doc`) e concentra a exclusão da conta.

---

## 11. Planos e travas

Duas camadas, propositalmente diferentes.

**Camada de edição.** `lim()` (linha 4047) devolve os limites do plano atual, e `limTeste()` (linha
4055) devolve limites maiores e fixos: 6 blocos (20 nos templates de loja), 6 fotos de local, 5
extras e todos os modos. A pessoa pode montar recursos de plano superior e ver o resultado; o painel
só avisa, por `avisoRecursoPlano()`.

**Camada de publicação.** `planGateAudit(perfil)` (linha 4108) percorre o perfil inteiro e lista
tudo que está acima do plano: carrossel, antes/depois, lead gate, excesso de blocos, presets,
modelos detalhados, reordenação, capas hero. Se houver pendências, `salvarTudo()` abre
`abrirPlanGateModal(audit)` (linha 4184) com três saídas: Fazer upgrade, Remover itens e publicar
(`planGateSanitize`, linha 4143) ou Continuar editando.

A fonte única de verdade é `lib/plano.js`. Existem apenas dois planos, `free` e `elite`; qualquer
valor pago legado (`pro`, `premium`, `intermediario`) é normalizado para `elite`, e qualquer valor
desconhecido cai em `free` por segurança.

---

## 12. Salvar, publicar e a guarda de alterações

`salvarTudo(opts)` (linha 4903):

1. Roda a auditoria de plano, salvo quando chamada com `skipPlanGate`.
2. Com sessão real, chama `PP.savePerfil(SLUG, PERFIL)`.
3. Se o `@` mudou, atualiza `users/{uid}.slug`, apaga o documento `perfis/{slugAntigo}` para não
   deixar URL duplicada viva, e recarrega a prévia no endereço novo.
4. Chama `markSaved()` e avisa por toast.
5. Sem sessão, ou se o banco falhar, baixa um `.json` de backup e deixa claro que não publicou.

A guarda de alterações compara `snapshot()` (o `PERFIL` serializado mais o `@`) com
`SAVED_SNAPSHOT`. Ela protege duas saídas: fechar ou recarregar a aba (aviso nativo do navegador) e
o botão Sair (modal `#leaveModal`, com Salvar e sair, Continuar editando e Sair sem salvar). No
"Salvar e sair", a saída só acontece se a publicação der certo.

---

## 13. Integrações de backend

| Endpoint | Uso no painel |
| --- | --- |
| `POST /api/claim-pro` | Ativa o plano pago logo após o login, quando o pagamento já foi feito |
| `POST /api/suporte` | Envia a mensagem do formulário de suporte |
| `DELETE /api/lead` | Exclui os contatos selecionados na subpágina Leads |
| `/api/founder` | Resgate de código de presente para o plano Elite |
| `/api/cloudinary` | Assinatura de upload e exclusão de imagem, chamada por `lib/cloudinary.js` |
| Firestore `perfis/{slug}` | Documento da página |
| Firestore `users/{uid}` | Nome, telefone e `slug` do dono |

---

## 14. PWA

O painel é instalável: `manifest.webmanifest`, `theme-color`, ícones Apple e `noindex,nofollow`. O
script 5 registra `/sw.js` e recarrega a página uma vez quando um service worker novo assume o
controle, garantindo que a pessoa não fique com uma versão antiga em cache.

---

## 15. Pontos de atenção encontrados no mapeamento

1. **Handlers sem elemento.** `fbLeads`, `fbConfig`, `fbSuporte`, `fbConta` e `leadsPop` têm
   listeners nas linhas 3072 a 3080, mas nenhum elemento com esses ids existe no HTML. `fbConfig` e
   `fbConta` inclusive só mostram um toast de "chega na próxima etapa". É código morto de uma versão
   anterior do rodapé flutuante.
2. **Chave com nome trocado.** `eventosCobertsoTitulo` (e não `eventosCobertosTitulo`) aparece no
   painel, em `pagina.html` e em `lib/presets.js`. Está consistente nos três lugares, então funciona;
   é dívida de nomenclatura, não bug.
3. **Grupo Modelo escondido.** O `<details class="group--modelo">` continua sendo escrito por
   `buildForm()` com atributo `hidden`, porque a escolha de profissão migrou para `/admin/modelos`.
   O HTML segue sendo gerado a cada render.
4. **Re-render completo a cada tecla de estrutura.** Adicionar ou remover qualquer item chama
   `buildForm()`, que refaz o formulário inteiro e reata todos os listeners. É o que exige o truque
   de preservar grupos abertos e scroll.
5. **Regra de escrita do projeto.** Os textos de interface seguem a regra registrada em
   `.planning/STATE.md`: português completo, sem travessão e sem gírias. Vale para qualquer copy nova
   escrita no painel.

---

## 16. Para a próxima etapa

O inventário completo de botões, com id, rótulo, localização, ação e trava de plano, está em
`06-INVENTARIO-BOTOES.md`.
