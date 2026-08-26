# 06 · INVENTÁRIO DE BOTÕES DO PAINEL

> Etapa 4 do mapeamento do Conheça Meu Trabalho.
> Fonte: repositório `lencinaai/conheca-meu-trabalho`, branch `master`, commit `c3783eb`.
> Arquivo: `painel/index.html`. Todas as linhas citadas são desse arquivo.
> Documento irmão: `05-PAINEL.md`, que explica a arquitetura por trás destes botões.

## Método

Foram inventariados todos os elementos clicáveis do painel: `<button>`, links `<a>` que executam
ação, e elementos com `data-*` que recebem `click`. Estão separados em duas naturezas:

- **Estático**: existe no HTML do arquivo, com `id` fixo.
- **Gerado**: escrito por JavaScript em tempo de execução, normalmente com atributo `data-*` no
  lugar do `id`, e pode aparecer muitas vezes na tela.

Legenda da coluna **Plano**: `livre` funciona no plano Grátis; `Elite` exige o plano pago, com
comportamento descrito na seção 17.

---

## 1. Gate de entrada (login e criar conta)

Linhas 739 a 776. Visível somente enquanto não há sessão.

| Rótulo | Seletor | Natureza | Ação | Plano |
| --- | --- | --- | --- | --- |
| Entrar | `#loginBtn` | Estático (756) | Login por e-mail e senha no Firebase (3477) | livre |
| Criar minha conta grátis | `#signupBtn` | Estático (766) | Cria conta, grava `users/{uid}` (3488) | livre |
| Olho da senha (login) | `[data-eye="senha"]` | Estático (754) | Alterna `type` do campo entre `password` e `text` (2910) | livre |
| Olho da senha (cadastro) | `[data-eye="suSenha"]` | Estático (764) | Idem | livre |
| Olho da senha (repetir) | `[data-eye="suSenha2"]` | Estático (765) | Idem | livre |
| Esqueci minha senha | `#forgotPw` | Estático (758) | Envia e-mail de redefinição do Firebase (2919) | livre |
| Criar agora, é grátis | `#toSignup` | Estático (760) | Troca para `#viewSignup` (2906) | livre |
| Entrar (voltar do cadastro) | `#toLogin` | Estático (771) | Troca para `#viewLogin` (2906) | livre |
| Política de Privacidade | `[data-doc="/privacidade.html"]` | Estático (767) | Abre o documento em modal (5237) | livre |
| Termos de Uso | `[data-doc="/termos.html"]` | Estático (767) | Idem | livre |

---

## 2. Cabeçalho do admin (`.adm-head`)

Linhas 778 a 806.

| Rótulo | Seletor | Natureza | Ação | Plano |
| --- | --- | --- | --- | --- |
| Logo, Conheça Meu Trabalho · admin | `.adm-brand` | Estático (779) | Link para `/admin` | livre |
| Início | `.adm-nav a[href="/admin"]` | Estático (781) | `go('/admin')` (3254) | livre |
| Profissões | `#navModelos` | Estático (782) | `go('/admin/modelos')` | livre |
| Presets | `#navPresets` | Estático (783) | `go('/admin/presets')` | livre |
| Modelos Detalhados | `#navModelosDet` | Estático (784) | `go('/admin/modelos-detalhados')` | livre |
| Capa | `#navCapa` | Estático (785) | `go('/admin/capa')` | livre |
| Leads | `#navLeads` | Estático (786) | `go('/admin/leads')` | livre |
| Configurações | `#navConfig` | Estático (787) | `go('/admin/configuracoes')` | livre |
| Compartilhar | `#shareBtn` | Estático (792) | Abre e fecha o popover `#sharePop` (3083) | livre |
| Copiar o link da sua página | `#shareCpMyLink` | Estático (795) | Copia `publicPageLink()` (3096) | livre |
| Abrir no WhatsApp | `#shareWa` | Estático (799) | Abre `wa.me` com a mensagem de indicação (3095) | livre |
| Copiar mensagem de indicação | `#shareCpMsg` | Estático (800) | Copia `inviteMsg()` (3097) | livre |
| Ver planos | `#navPro` | Estático (803) | `go('/admin/planos')` | livre |
| Sair | `#logoutBtn` | Estático (804) | Guarda de alterações e depois `PP.logout()` (4950) | livre |

O selo do plano (`#planBadge`, linha 790) é imagem informativa, sem clique.

---

## 3. Barra lateral da home (`.adm-side`)

Linhas 808 a 819.

| Rótulo | Seletor | Natureza | Ação | Plano |
| --- | --- | --- | --- | --- |
| Fechar (barra lateral) | `#admCollapse` | Estático (811) | Adiciona `collapsed` em `#admMain` e reencaixa a prévia (3067) | livre |
| Abrir (barra lateral) | `#admExpand` | Estático (819) | Remove `collapsed` (3068) | livre |
| Organizar blocos | `#organizarBtn` | Estático (812) | `toggleOrganizar()` (1568) | Elite |
| Abrir página | `#openPublic` | Estático (815) | Abre `/{SLUG}` em nova aba (2987) | livre |
| Salvar e publicar | `#saveBtn` | Estático (816) | `salvarTudo()` (4903) | livre |

---

## 4. Barra da prévia (`.adm-preview`)

Linhas 821 a 843.

| Rótulo | Seletor | Natureza | Ação | Plano |
| --- | --- | --- | --- | --- |
| Ver no computador | `#pvDesktop` | Estático (825) | `setPvMode(false)` (3127) | livre |
| Ver no celular | `#pvMobile` | Estático (826) | `setPvMode(true)` | livre |
| Diminuir zoom | `#pvZoomOut` | Estático (829) | `pvZoomSet(atual - 10)` (3184) | livre |
| Zoom (controle deslizante) | `#pvZoomRange` | Estático (830) | `pvZoomSet(valor)`, de 50 a 300 | livre |
| Aumentar zoom | `#pvZoomIn` | Estático (831) | `pvZoomSet(atual + 10)` | livre |
| Voltar ao tamanho real | `#pvZoomReset` | Estático (832) | `pvZoomSet(100)` | livre |
| Copiar o link da sua página | `#copyUrl` | Estático (835) | Copia o link e troca o ícone por um visto (3197) | livre |

---

## 5. Barra de abas do celular (`#admTabbar`)

Linhas 1090 a 1096. Só aparece em telas pequenas.

| Rótulo | Seletor | Natureza | Ação | Plano |
| --- | --- | --- | --- | --- |
| Editar | `[data-tab="edit"]` | Estático (1091) | `mobHome(false)` (3272) | livre |
| Prévia | `[data-tab="preview"]` | Estático (1092) | `mobHome(true)` | livre |
| Modelos | `[data-tab="modelos"]` | Estático (1093) | `go('/admin/modelos')` | livre |
| Leads | `[data-tab="leads"]` | Estático (1094) | `go('/admin/leads')` | livre |
| Config | `[data-tab="config"]` | Estático (1095) | `go('/admin/configuracoes')` | livre |

---

## 6. Botões gerados dentro do formulário

Estes são os botões que `buildForm()`, `mediaBlock()` e os helpers escrevem. Cada linha da tabela
representa um padrão que pode aparecer dezenas de vezes na tela, uma vez por item editável.

### 6.1 Estrutura dos grupos

| Rótulo | Seletor | Onde nasce | Ação | Plano |
| --- | --- | --- | --- | --- |
| Cabeçalho do grupo | `details.group > summary` | `buildForm` (4246 em diante) | Abre e fecha a seção | livre |
| Olho de ocultar seção | `[data-sectoggle="chave"]` | `secToggleBtn` (3955) | Entra ou sai de `PERFIL.secoes_ocultas` (3959) | livre |
| Fechar grupo | `.group-close` | `addGroupCloseButtons` (4509) | Fecha o `<details>` a partir do rodapé do grupo | livre |

### 6.2 Itens de lista (adicionar e remover)

`[data-add]` acrescenta um item ao array e `[data-rm]` remove, apagando antes as imagens do card no
Cloudinary. Ambos são tratados em `bind()` (4575 e 4595).

| Rótulo | Seletor | Limite | Plano |
| --- | --- | --- | --- |
| + Adicionar número | `[data-add="metric"]` | Até 4 | livre |
| + Adicionar curso | `[data-add="curso"]` | Sem teto | livre |
| + Adicionar rede | `[data-add="rede"]` | Sem teto | livre |
| + Adicionar item da galeria | `[data-add="imovel"]` | Teto de blocos do plano | Elite acima de 3 |
| + Adicionar item da segunda galeria | `[data-add="imovel2"]` | Teto de blocos do plano | Elite acima de 3 |
| + Adicionar serviço | `[data-add="servicos"]` | Teto de blocos do plano | Elite acima de 3 |
| + Adicionar depoimento | `[data-add="depoimentos"]` | Sem teto | livre |
| + Adicionar evento na agenda | `[data-add="evagenda"]` | Teto de blocos do plano | Elite acima de 3 |
| + Adicionar evento realizado | `[data-add="evcoberto"]` | Teto de blocos do plano | Elite acima de 3 |
| + Adicionar marca | `[data-add="marca"]` | Teto de blocos do plano | Elite acima de 3 |
| Lixeira do item | `[data-rm="caminho.i"]` | Um por item de qualquer lista | livre |

### 6.3 Bloco de mídia universal (`mediaBlock`, 3696)

| Rótulo | Seletor | Ação | Plano |
| --- | --- | --- | --- |
| Imagem | `[data-setmodo="prefixo\|single"]` | Define `obj.modo='single'` (3908) | livre |
| Carrossel | `[data-setmodo="prefixo\|carrossel"]` | Define o modo e garante `fotos[]` | Elite |
| Antes/Depois | `[data-setmodo="prefixo\|antes_depois"]` | Define o modo e garante `antes` e `depois` | Elite |
| Agenda | `[data-setmodo="prefixo\|agenda"]` | Define o modo e garante `data` e `hora` | livre |
| Card | `[data-setmodo="prefixo\|card"]` | Zera todas as imagens e desliga o lead gate | livre |
| + Foto (carrossel) | `[data-addmidia="prefixo"]` | Upload múltiplo até o teto de extras (3934) | Elite |
| Remover foto do carrossel | `[data-rmmidia="prefixo\|url"]` | Tira do array e apaga no Cloudinary (3946) | livre |
| Fazer upgrade (aviso do bloco) | `[data-gopro]` | `verPRO()`, vai para `/admin/planos` (4642) | livre |

### 6.4 Slots de imagem (`upslot`, 3634)

| Rótulo | Seletor | Ação | Plano |
| --- | --- | --- | --- |
| Corpo do slot vazio | `[data-upload="caminho"]` | Abre o seletor de arquivo; aceita arrastar e soltar (3812) | livre |
| Trocar | `[data-uptrocar="caminho"]` | Reabre o seletor de arquivo (3848) | livre |
| Ajustar | `[data-upajustar="caminho"]` | Abre o modal de recorte na imagem atual (3850) | livre |
| Excluir | `[data-updelete="caminho"]` | Limpa o campo e apaga no Cloudinary (3858) | livre |

### 6.5 Fotos do local

| Rótulo | Seletor | Ação | Plano |
| --- | --- | --- | --- |
| + Foto (local) | `[data-addlocalfoto="1"]` | Upload múltiplo até o teto de fotos do local (3880) | Elite acima de 1 |
| Remover foto do local | `[data-rmlocalfoto="url"]` | Tira do array e apaga no Cloudinary (3891) | livre |

### 6.6 Escolhas e opções

| Rótulo | Seletor | Ação | Plano |
| --- | --- | --- | --- |
| Card de profissão | `[data-tpl="chave"]` | `applyPreset(chave)` (3878) | livre |
| Perfil 1 · Capa | `[data-setopt="mobileHero\|capa"]` | Define o modelo de topo no celular (3891 e 3896) | livre |
| Perfil 2 · Foto em card | `[data-setopt="mobileHero\|classico"]` | Idem, com aviso se a capa avançada estiver ligada | livre |
| Modelo 1 · Clássico | `[data-setopt="mobileFeed\|classico"]` | Define o feed do celular | livre |
| Modelo 2 · 3 em 3 | `[data-setopt="mobileFeed\|feed3"]` | Idem | livre |
| Ícones de alinhamento | `[data-align-path]` | Grava o alinhamento do texto (4545) | livre |
| Cartão de fonte | `[data-font]` e `.fpick-opt` | Define a fonte do papel (4605 e 4633) | livre |
| Abrir seletor de fonte | `.fpick-btn` | Abre e fecha o menu de fontes (4623) | livre |
| Edição avançada da capa | `#capaShortcutBtn` | Vai para `/admin/capa` (5245) | Elite nos modelos hero |
| Desligar capa avançada | `#capaOffBtn` | Volta `coverLayoutType` para `profile` (5251) | livre |
| Não achou a sua profissão | `#sugestaoBtn` | Abre e-mail para o suporte com assunto pronto (4250) | livre |

---

## 7. Subpágina Profissões (`/admin/modelos`)

| Rótulo | Seletor | Natureza | Ação | Plano |
| --- | --- | --- | --- | --- |
| Card de profissão | `[data-mod-tpl="chave"]` | Gerado (3032) | Confirma e aplica o preset da profissão (3040) | livre |

---

## 8. Subpágina Presets de tema (`/admin/presets`)

| Rótulo | Seletor | Natureza | Ação | Plano |
| --- | --- | --- | --- | --- |
| Ver planos (aviso de trava) | `#presetsLockCta` | Estático (1005) | Link para `/admin/planos` | livre |
| Card do preset | `.preset-card` | Gerado (1400) | Abre o modal de detalhe | livre |
| Visualizar | `#pdPreview` | Gerado (1416) | Abre a prévia do preset em modal (1424) | livre |
| Aplicar preset / Reaplicar preset | `#pdApply` | Gerado (1418) | `aplicarPreset(id)` (1430) | Elite |
| Restaurar padrão do preset | `#pdRestore` | Gerado (1419) | `restaurarPreset(id)` (1431) | Elite |
| Remover preset | `#pdRemove` | Gerado (1420) | `removerPreset()` (1432) | livre |
| Fechar o modal de detalhe | `.pd-close` | Gerado (1410) | `_closePdModal()` | livre |

---

## 9. Subpágina Modelos Detalhados (`/admin/modelos-detalhados`)

| Rótulo | Seletor | Natureza | Ação | Plano |
| --- | --- | --- | --- | --- |
| Card do modelo | `.preset-card` | Gerado (1474) | Abre o modal de detalhe | livre |
| Visualizar | `#pmPreview` | Gerado (1490) | Abre a prévia do modelo (1500) | livre |
| Aplicar modelo / Reaplicar modelo | `#pmApply` | Gerado (1492) | `aplicarModeloDetalhado(id)` (1501) | Elite |
| Restaurar padrão do modelo | `#pmRestore` | Gerado (1494) | `restaurarModeloDetalhado(id)` (1502) | Elite |
| Remover modelo | `#pmRemove` | Gerado (1495) | Limpa `selectedModeloPremium` | livre |
| Fechar o modal de detalhe | `.pd-close` | Gerado (1484) | `_closePdModal()` | livre |

O modal de prévia compartilhado pelas duas subpáginas tem ainda **Aplicar**
(`#presetPreviewApply`, 1274) e **Fechar** (`#presetPreviewClose`, 1277).

---

## 10. Subpágina Capa (`/admin/capa`)

### 10.1 Barra de ferramentas

| Rótulo | Seletor | Natureza | Ação | Plano |
| --- | --- | --- | --- | --- |
| Fechar (barra lateral) | `#capaCollapse` | Estático (1016) | Recolhe `#capaSplit` (3070) | livre |
| Abrir (barra lateral) | `#capaExpand` | Estático (1030) | Expande `#capaSplit` (3071) | livre |
| Como editar | `#capaHelpBtn` | Estático (1019) | Abre o modal de ajuda (4833) | livre |
| Entendi (fecha a ajuda) | `#capaHelpClose` | Estático (1072) | Fecha `#capaHelpOv` | livre |
| Editar / Editando | `#capaEditToggle` | Estático (1022) | Liga e desliga o arraste na prévia (4816) | livre |
| Abrir página | `#capaOpenPublic` | Estático (1026) | Abre `/{SLUG}` em nova aba (4845) | livre |
| Salvar e publicar | `#capaSaveBtn` | Estático (1027) | `salvarTudo()` (4841) | livre |
| Desfazer | `#capaUndo` | Estático (1036) | `_capaUndoDo()` (2002) | livre |
| Refazer | `#capaRedo` | Estático (1037) | `_capaRedoDo()` (2018) | livre |
| Ver no computador | `#capaPvDesktop` | Estático (1040) | `capaPvSetDevice('desktop')` (4788) | livre |
| Ver no tablet | `#capaPvTablet` | Estático (1041) | `capaPvSetDevice('tablet')` | livre |
| Ver no celular | `#capaPvMobile` | Estático (1042) | `capaPvSetDevice('mobile')` | livre |
| Diminuir zoom | `#capaZoomOut` | Estático (1045) | `capaZoomSet(atual - 10)` (4858) | livre |
| Zoom (controle deslizante) | `#capaZoomRange` | Estático (1046) | `capaZoomSet(valor)` | livre |
| Aumentar zoom | `#capaZoomIn` | Estático (1047) | `capaZoomSet(atual + 10)` | livre |
| Voltar ao tamanho real | `#capaZoomReset` | Estático (1048) | `capaZoomSet(100)` | livre |

### 10.2 Formulário da capa (gerado por `fillCapa`, 2150)

| Rótulo | Seletor | Ação | Plano |
| --- | --- | --- | --- |
| Modelo de capa | `[data-captype="chave"]` | Troca `coverLayoutType` (2406) | Elite em `hero_widescreen` e `hero_with_png` |
| Aparelho | `[data-capdev="desktop\|tablet\|mobile"]` | Troca o aparelho editado (2425) | livre |
| Gerar ajuste para o aparelho | `#capaAutoResp` | Calcula posições automáticas (2490) | livre |
| Trocar (imagem de fundo) | `#capaHeroTrocar` | Abre o seletor de arquivo (2553) | livre |
| Ajustar (imagem de fundo) | `#capaHeroAjustar` | Rola até o grupo de foco (2571) | livre |
| Excluir (imagem de fundo) | `#capaHeroDel` | Limpa `heroImageUrl` e apaga no Cloudinary (2570) | livre |
| Usar a cor do tema (gradiente) | `#capaFadeColorReset` | Remove a cor custom do gradiente (2453) | livre |
| Usar este gradiente e foco também no celular | `#capaHeroSameMobile` | Copia os ajustes do desktop (2438) | livre |
| Trocar (PNG da pessoa) | `#capaPngTrocar` | Abre o seletor de arquivo (2562) | Elite |
| Ajustar (PNG da pessoa) | `#capaPngAjustar` | Rola até o grupo de posição (2580) | Elite |
| Excluir (PNG da pessoa) | `#capaPngDel` | Limpa `personaPngUrl` e apaga no Cloudinary (2579) | Elite |
| Âncora do PNG | `[data-anchor="valor"]` | Define `personaAnchor` (2484) | Elite |
| Posição rápida do PNG | `[data-personapreset="chave"]` | Aplica um preset de posição (2515) | Elite |
| Usar esta posição também no celular | `#capaSameMobile` | Copia a posição do desktop (2524) | Elite |
| Usar esta posição também no tablet | `#capaSameTablet` | Idem para o tablet (2530) | Elite |
| Alinhamento do conteúdo | `[data-align-icon="content"]` | Define o alinhamento do bloco de texto (2498) | livre |
| Restaurar posição e tamanho (por elemento) | `[data-el-reset="chave"]` | Remove o ajuste livre daquele elemento (2586) | livre |
| Restaurar todos os elementos deste aparelho | `#capaElResetAll` | Limpa `heroElements[aparelho]` (2592) | livre |
| Restaurar posições padrão | `#capaReset` | Apaga todos os ajustes de posição da capa (2581) | livre |

### 10.3 Arraste na prévia

Não são botões, mas são a principal interação da subpágina: com o modo Editar ligado, o `iframe`
devolve `{type:'capa-move', target, device, x, y}` e o painel grava em `heroElements`,
`identityContentSettings`, `personaSettings` ou `heroSettings.height` (4862).

---

## 11. Modo Organizar blocos

Ligado pelo `#organizarBtn` da barra lateral. Recurso Elite.

| Rótulo | Seletor | Natureza | Ação | Plano |
| --- | --- | --- | --- | --- |
| Subir bloco | `[data-orgdir="-1"]` | Gerado (1614) | `_orgMove(card, -1)` (1646) | Elite |
| Descer bloco | `[data-orgdir="1"]` | Gerado (1615) | `_orgMove(card, 1)` | Elite |
| Concluir | `#orgDone` | Gerado (1673) | `_orgExit(false)`, mantém a ordem (1677) | Elite |
| Cancelar | `#orgCancel` | Gerado (1674) | `_orgExit(true)`, restaura o snapshot (1678) | Elite |
| Ordem padrão | `#orgReset` | Gerado (1675) | `_orgResetOrder()` (1679) | Elite |

O arraste dos cards (`dragstart`, `dragover`, `drop`, linhas 1690 a 1711) é a interação principal;
as setas existem como alternativa acessível e para o celular.

---

## 12. Subpágina Planos (`/admin/planos`)

| Rótulo | Seletor | Natureza | Ação | Plano |
| --- | --- | --- | --- | --- |
| Mensal | `[data-cycle="mensal"]` | Estático (982) | Alterna a grade para os preços mensais | livre |
| Anual | `[data-cycle="anual"]` | Estático (983) | Alterna a grade para os preços anuais | livre |
| CTA do plano | `.plan-cta` | Gerado (5171) | Abre o checkout Cakto por `checkoutURL()` (5135) | livre |
| Disponível em breve | `.plan-cta.soon` | Gerado (5171) | Desabilitado | livre |

---

## 13. Subpágina Leads (`/admin/leads`)

Barra de ações gerada por `_ldRender()` e ligada em `_ldWire()` (3399).

| Rótulo | Seletor | Natureza | Ação | Plano |
| --- | --- | --- | --- | --- |
| Gerenciar | `#ldBtnGer` | Gerado (3384) | Entra no modo de seleção | livre |
| Selecionar todos | `#ldBtnAll` | Gerado (3387) | Marca ou desmarca todos os contatos | livre |
| Excluir | `#ldBtnDel` | Gerado (3389) | `DELETE /api/lead` com os ids selecionados (3414) | livre |
| Cancelar | `#ldBtnCan` | Gerado (3390) | Sai do modo de seleção | livre |
| Caixa de seleção da linha | `.lead-cb` | Gerado | Marca o contato e atualiza o contador | livre |

O botão Excluir nasce desabilitado e só liga quando há pelo menos um contato marcado. Antes de
apagar, usa o `confirm()` nativo do navegador.

---

## 14. Subpágina Configurações (`/admin/configuracoes` e `/admin/conta`)

### 14.1 Navegação lateral

| Rótulo | Seletor | Natureza | Ação | Plano |
| --- | --- | --- | --- | --- |
| Conta | `[data-cfgtab="conta"]` | Estático (857) | Mostra o painel `conta` (3344) | livre |
| Segurança | `[data-cfgtab="seguranca"]` | Estático (858) | Mostra o painel `seguranca` | livre |
| Suporte | `[data-cfgtab="suporte"]` | Estático (859) | Mostra o painel `suporte` | livre |
| Privacidade e conta | `[data-cfgtab="perigo"]` | Estático (860) | Mostra o painel `perigo` | livre |

### 14.2 Segurança

| Rótulo | Seletor | Natureza | Ação | Plano |
| --- | --- | --- | --- | --- |
| Salvar novo e-mail | `#ctSalvarEmail` | Estático (883) | Reautentica e chama `updateEmail` (3293) | livre |
| Salvar nova senha | `#ctSalvarSenha` | Estático (892) | Reautentica e chama `updatePassword` (3302) | livre |

### 14.3 Suporte

| Rótulo | Seletor | Natureza | Ação | Plano |
| --- | --- | --- | --- | --- |
| WhatsApp | `#supWpp` | Estático (903) | Abre a conversa com o suporte | livre |
| E-mail | `#supEmailBtn` | Estático (904) | Abre o cliente de e-mail | livre |
| Instagram | `#supIg` | Estático (905) | Abre o perfil do produto | livre |
| Enviar mensagem | `#supSend` | Estático (912) | `POST /api/suporte` (3106) | livre |
| Ou copie o e-mail | `#supCopy` | Estático (913) | Copia o e-mail do suporte (3104) | livre |

### 14.4 Privacidade e conta

| Rótulo | Seletor | Natureza | Ação | Plano |
| --- | --- | --- | --- | --- |
| Política de Privacidade | `[data-doc="/privacidade.html"]` | Estático (923) | Abre o documento em modal (5237) | livre |
| Termos de Uso | `[data-doc="/termos.html"]` | Estático (924) | Idem | livre |
| Apagar minha conta | `#ctApagar` | Estático (929) | Revela a confirmação `#ctApagarConf` (3315) | livre |
| Sim, apagar a minha conta | `#ctApagarOk` | Estático (932) | Reautentica e apaga imagens, perfil, usuário e login (3317) | livre |
| Cancelar | `#ctApagarCancel` | Estático (933) | Esconde a confirmação (3316) | livre |

### 14.5 Blocos gerados dentro de Configurações › Conta

| Rótulo | Seletor | Natureza | Ação | Plano |
| --- | --- | --- | --- | --- |
| Ver o plano Elite | `#goProBtn` | Gerado (5073) | `verPRO()` (4647) | livre |
| Ativar (código Elite) | `#fundBtn` | Gerado (5079) | `resgatarCodigo()` via `/api/founder` (4993) | livre |
| Copiar (link de convite) | `#copInv` | Gerado (5049) | Copia `inviteLink()` (4651) | livre |
| Copiar convite pronto | `#copInvMsg` | Gerado (5051) | Copia `inviteMsg()` (4652) | livre |

---

## 15. Modais

### 15.1 Recorte de imagem (`#cropModal`, 1100)

| Rótulo | Seletor | Ação |
| --- | --- | --- |
| Fechar | `#cropCancelBtn` (1107) | Cancela o recorte (5381) |
| Quadrado, Retrato, Vertical, Paisagem, Clássico | `[data-ratio]` (1115 a 1119) | `setRatio()` (5334) |
| Centralizar | `#cropCenterBtn` (1130) | Zera deslocamento e escala (5382) |
| Confirmar | `#cropConfirmBtn` (1129) | Grava posição, escala e proporção (5380) |

### 15.2 Alterações não salvas (`#leaveModal`, 1140)

| Rótulo | Seletor | Ação |
| --- | --- | --- |
| Salvar e sair | `#leaveSave` (1142) | Publica e só então sai (4982) |
| Continuar editando | `#leaveCancel` (1145) | Fecha o modal (4979) |
| Sair sem salvar | `#leaveConfirm` (1146) | Executa a saída pendente (4980) |

### 15.3 Trava de plano ao publicar (`#planGateModal`, 1155)

| Rótulo | Seletor | Ação |
| --- | --- | --- |
| Fazer upgrade | `#planGateUpgrade` (1158) | Vai para `/admin/planos` (4187) |
| Remover itens e publicar | `#planGateRemove` (1159) | `planGateSanitize()` e publica (4143) |
| Continuar editando | `#planGateCancel` (1160) | Fecha sem publicar |

### 15.4 Confirmação do site (`#sconfOv`, 1078)

Substitui o `confirm()` nativo em decisões do editor, como sair da capa avançada.

| Rótulo | Seletor | Ação |
| --- | --- | --- |
| Cancelar | `#sconfCancel` (1083) | Resolve `confirmSite` como `false` (3589) |
| Continuar | `#sconfOk` (1084) | Resolve `confirmSite` como `true` |

### 15.5 Documento (`#docModal`, gerado em 5205)

| Rótulo | Seletor | Ação |
| --- | --- | --- |
| Fechar | `#docModalX` (5211) | Fecha o modal (5218) |
| Entendi | `#docModalOk` (5213) | Fecha o modal (5219) |

### 15.6 Ajuda da capa (`#capaHelpOv`, 1062)

| Rótulo | Seletor | Ação |
| --- | --- | --- |
| Entendi | `#capaHelpClose` (1072) | Fecha a ajuda (4835) |

---

## 16. Resumo por área

| Área | Entradas no inventário |
| --- | --- |
| Gate de entrada | 10 |
| Cabeçalho do admin | 14 |
| Barra lateral da home | 5 |
| Barra da prévia | 7 |
| Barra de abas do celular | 5 |
| Formulário (padrões gerados) | 39 |
| Subpágina Profissões | 1 |
| Presets de tema | 7 |
| Modelos Detalhados | 6 |
| Capa | 35 |
| Organizar blocos | 5 |
| Planos | 4 |
| Leads | 5 |
| Configurações | 20 |
| Modais | 15 |
| **Total** | **178** |

Os itens do formulário e da capa são padrões: um único `[data-rm]` na tabela pode virar trinta
lixeiras na tela, uma por card.

---

## 17. Botões que dependem do plano

Consolidado a partir de `lib/plano.js` e das checagens do painel. Existem apenas dois planos,
`free` e `elite`.

| Botão ou grupo | Recurso em `PLANO.REGRAS` | Comportamento no plano Grátis |
| --- | --- | --- |
| Carrossel (`data-setmodo`, `data-addmidia`) | `carrossel` | Deixa montar e avisa; barra na publicação |
| Antes/Depois (`data-setmodo`) | `antesDepois` | Deixa montar e avisa; barra na publicação |
| Pedir contato, lead gate (`data-leadgate`) | `leadGate` | Deixa ligar e avisa; barra na publicação |
| Adicionar bloco acima de 3 (`data-add`) | `maxImagens` | Deixa criar até 6 e avisa; barra na publicação |
| + Foto do local acima de 1 (`data-addlocalfoto`) | `localFotos` | Deixa subir até 6 e avisa; barra na publicação |
| Aplicar preset de tema (`#pdApply`) | `presetsTema` | Grade coberta por `#presetsLock` |
| Aplicar modelo detalhado (`#pmApply`) | `modelosDetalhados` | Grade coberta por `#modelosDetLock` |
| Organizar blocos (`#organizarBtn`) | `reordenarBlocos` | Recurso Elite |
| Capa widescreen (`data-captype="hero_widescreen"`) | `heroWidescreen` | Recurso Elite |
| Capa com PNG (`data-captype="hero_with_png"`) | `heroComPng` | Recurso Elite |
| Logo em marcas e parcerias | `marcasImagem` | Recurso Elite |

A regra geral do produto aparece com clareza no inventário: **o painel deixa experimentar, e a
trava real acontece no Salvar e publicar**, por `planGateAudit()`. Os botões de plano superior
continuam clicáveis, apenas mostram o aviso `avisoRecursoPlano()`.

---

## 18. Achados do inventário

1. **Cinco handlers sem elemento correspondente.** `#fbLeads`, `#fbConfig`, `#fbSuporte`, `#fbConta`
   e `#leadsPop` recebem `addEventListener` nas linhas 3072 a 3080, mas nenhum desses ids existe no
   HTML. `#fbConfig` e `#fbConta` só mostram o toast "chega na próxima etapa". É resto de um rodapé
   flutuante que foi substituído pelo `#admTabbar`.
2. **Dois handlers de cupom órfãos.** `#copLink` e `#cupLink`, ligados em `setupPlanoHandlers()`
   (4650), não são mais escritos por `planoBlocoHTML()`: o bloco de cupom de divulgação do plano
   fundador saiu da geração. Os handlers ficaram.
3. **Grupo Modelo escondido, mas ainda gerado.** O `<details class="group--modelo">` com a grade
   `[data-tpl]` continua sendo escrito por `buildForm()` com `hidden`, porque a escolha de profissão
   migrou para `/admin/modelos` e os cards `[data-mod-tpl]`. São duas grades para a mesma finalidade,
   uma delas invisível.
4. **Dois caminhos para Salvar e publicar.** `#saveBtn` (home) e `#capaSaveBtn` (capa) chamam a mesma
   `salvarTudo()`. Correto, e vale registrar: qualquer mudança na regra de publicação precisa valer
   para os dois.
5. **Dois caminhos para Abrir página.** `#openPublic` e `#capaOpenPublic` repetem a mesma linha de
   `window.open`, sem função compartilhada.
6. **Confirmação nativa em Leads.** A exclusão de contatos usa `confirm()` do navegador, enquanto o
   resto do painel usa `confirmSite()` (`#sconfOv`). É a única quebra de padrão de confirmação.
7. **Botão sem rótulo textual.** Vários controles são apenas ícone (`#pvZoomIn`, `#capaUndo`,
   `#shareBtn`, olhos de senha). Os da prévia e da capa têm `aria-label`; `#shareBtn` e os olhos de
   senha têm apenas `title`.
