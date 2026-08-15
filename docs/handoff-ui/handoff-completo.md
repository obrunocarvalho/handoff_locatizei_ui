<!--
  ARQUIVO GERADO — não editar à mão.
  Concatenação literal dos seis arquivos do handoff, na ordem do índice.
  A fonte da verdade são os arquivos individuais em docs/handoff-ui/.
-->

# Locatizei — handoff de UI (documento único)

Este arquivo é a **concatenação** dos seis documentos do handoff, para o caso de ser
mais prático receber tudo de uma vez. O conteúdo é idêntico ao dos arquivos
separados; os links relativos entre eles continuam funcionando porque todos vivem na
mesma pasta.

Ordem: índice · design system · componentes · telas · estados e regras · dados de
demonstração · fluxos para animar.


---

<!-- ===== README.md ===== -->

# Locatizei — handoff de UI

> Documentação da interface do Locatizei, escrita para alguém que nunca viu o produto
> e não tem acesso ao código. **Tudo aqui foi conferido linha a linha contra a
> implementação**; onde há dúvida ou lacuna, ela está declarada em vez de preenchida
> com suposição.
>
> Todos os dados usados nos exemplos são **fictícios**. Não há neste repositório
> nenhuma credencial, chave, variável de ambiente, endpoint interno ou dado de cliente.

---

## O que é o Locatizei

O Locatizei é um painel de **gestão de locações para imobiliárias** — o sistema onde
uma administradora de aluguéis substitui a planilha. Ele guarda quem é quem (pessoas
podem ser inquilinas, proprietárias ou as duas), quais são os imóveis da carteira, e
quais contratos ligam uns aos outros. A partir do contrato, o produto assume o ciclo
que consome o dia da imobiliária: **gera a agenda de cobranças, abre e vence cada
uma sozinho, avisa o inquilino por email com a marca da imobiliária, recebe o
pagamento, dá baixa e emite o recibo** — enquanto mantém a operadora informada do que
exige atenção hoje. Quem usa não é o inquilino nem o proprietário: é a equipe da
imobiliária, num painel de trabalho diário.

O produto é **whitelabel na comunicação**: o inquilino recebe emails assinados pela
imobiliária, com a cor, o CRECI e o telefone dela. A única menção à plataforma é uma
linha discreta no rodapé — *"Enviado via Locatizei"*.

---

## Quem usa, e o que cada um vê

Dois papéis, e a diferença entre eles é visível na primeira tela.

| | **Admin** *(rótulo na tela: "Gestora")* | **Operador** |
|---|---|---|
| Pills na navegação | **8** | **6** |
| Dashboard, Pessoas, Imóveis, Contratos, Cobranças, Inadimplência | ✅ | ✅ |
| Cadastrar e editar pessoa, imóvel, contrato | ✅ | ✅ |
| Registrar pagamento, isentar, editar cobrança, quitar por acordo, registrar contato | ✅ | ✅ |
| Pausar emails · ativar pagamento direto ao proprietário | ✅ | ✅ |
| **Configurações** e **Auditoria** | ✅ | ❌ *(as pills nem aparecem; a URL redireciona)* |
| **Encerrar contrato** | ✅ | ❌ *(o item não existe no menu)* |

**Ninguém mais tem acesso.** Não há portal do inquilino, portal do proprietário, nem
qualquer tela pública. O inquilino só encontra o produto pelo **email** que recebe.

---

## O ciclo do dinheiro — a espinha de tudo

```
┌─ 1 ─────────────┐
│ CONTRATO ATIVADO│  wizard de 4 etapas → 12 cobranças criadas de uma vez
│                 │  imóvel: Disponível ──► Alugado
└────────┬────────┘
         │
┌─ 2 ────▼────────┐
│ COBRANÇA GERADA │  a agenda nasce na ativação; a rodada diária (07:00, horário
│  e depois ABERTA│  de Brasília) repõe até 60 dias à frente e move os status:
│                 │  Agendada ──► Aberta ──► Vencida
└────────┬────────┘
         │
┌─ 3 ────▼────────┐
│ EMAIL DE COBRANÇA│ cadência configurável: −3d · no dia · +3 · +7 · +15
│                 │  três selos: LEMBRETE · VENCE HOJE · EM ATRASO
│                 │  assinado pela imobiliária, com a cor e o CRECI dela
└────────┬────────┘
         │
┌─ 4 ────▼────────┐
│ LINK DE PAGAMENTO│ botão "Pagar com boleto ou Pix" — só viaja quando cobra
│                 │  EXATAMENTE o saldo atual. Sem link, o email leva no lugar
│                 │  o painel "Como pagar" com as instruções da imobiliária.
└────────┬────────┘
         │
┌─ 5 ────▼────────┐
│ PAGAMENTO       │  o inquilino paga — no checkout do banco ou por fora
└────────┬────────┘
         │
┌─ 6 ────▼────────┐
│ BAIXA           │  manual, no drawer "Registrar pagamento";
│                 │  ou automática, pelo aviso do Mercado Pago
│                 │  Aberta ──► Paga   (ou Parcialmente paga, com saldo)
└────────┬────────┘
         │
┌─ 7 ────▼────────┐
│ RECIBO          │  "Pagamento confirmado" · ✓ verde · valor · forma · data
│                 │  com o bloco de saldo restante, quando o pagamento foi parcial
└─────────────────┘
```

Tudo o que sai desse trilho tem tratamento próprio e visível: **Isentada** (perdoada
com motivo), **Em negociação** (sai da fila de cobrança e da cadência de emails),
**Quitada por acordo** (paga por um valor combinado, com a diferença registrada) e
**Cancelada** (cobrança futura zerada pelo encerramento do contrato).

---

## Os arquivos

| Arquivo | O que traz |
|---|---|
| **[design-system.md](design-system.md)** | Todos os tokens de cor com valor exato, tipografia, raios, sombras, espaçamento, foco e ícones — extraídos do CSS, não estimados. Começa pelo aviso mais importante: **não existe tema escuro**. |
| **[componentes.md](componentes.md)** | Inventário dos 32 componentes de produto + 20 primitivos: props, variantes, estados visuais e as classes Tailwind reais, com JSX quando ele comunica melhor que a prosa. |
| **[telas.md](telas.md)** | Uma seção por rota (17 no total): propósito, layout, dados exibidos, ações e **todos** os estados — com dados, vazio, vazio-de-filtro, erro, sem permissão. |
| **[estados-e-regras.md](estados-e-regras.md)** | As matrizes: status × rótulo × cor × ações; os cinco estados do link de pagamento; os gates dos interruptores; formatação brasileira com o helper real; e o catálogo literal das mensagens de erro. |
| **[dados-demo.md](dados-demo.md)** | Um conjunto fictício coerente que atravessa todas as telas — 11 pessoas, 11 imóveis, 8 contratos, cobranças em todos os estados — com os KPIs já calculados e a fórmula ao lado. |
| **[fluxos-para-animar.md](fluxos-para-animar.md)** | Oito roteiros: estado inicial, ação, o que muda, o que acontece por baixo, a sensação a transmitir — e o que **não** mostrar em cada um. |
| **[handoff-completo.md](handoff-completo.md)** | Os seis arquivos acima concatenados num único documento, para o caso de ser mais prático receber tudo de uma vez. Gerado a partir dos originais, não escrito à mão. |

**Sugestão de leitura:** comece por [fluxos-para-animar.md](fluxos-para-animar.md) para
entender o que vale a pena mostrar, volte a [telas.md](telas.md) para reconstruir cada
enquadramento, e use [design-system.md](design-system.md) e
[dados-demo.md](dados-demo.md) como consulta durante a produção.

---

## O que existe hoje × o que está planejado

O handoff inteiro descreve **apenas o que está implementado e alcançável na interface
hoje**. Nada de roadmap se disfarçou de feature.

### Implementado, e o que precisa de ressalva

| Área | Situação real |
|---|---|
| Cadastros (pessoas, imóveis, contratos) | ✅ completo, com wizard de 4 etapas e rascunho |
| Ciclo financeiro (cobrança, pagamento, comprovante, inadimplência, dashboard) | ✅ completo |
| Motor de emails (cadência, templates, logs, pausas) | ✅ completo. **O interruptor-mestre vem desligado de fábrica** — cada imobiliária opta por ligar |
| Mercado Pago (link de pagamento, baixa automática, interruptor) | ✅ implementado. **Desligado por padrão em toda agência.** A conta de recebimento é vinculada pelo suporte, **não há tela para isso** |
| Rodada diária de cobranças | ✅ em produção |

### Existe na tela, mas não faz nada

Estes elementos estão visíveis e **não são funcionais**. O próprio código os marca
como pendentes:

- **busca global** no header (`⌘K`) — é um `<div>`, não um campo;
- **seletor de agência** — mostra o nome real, não abre nada;
- **sino de notificações** — `title="Notificações (em breve)"`;
- **botão de ajuda `?`** — `title="Ajuda (em breve)"`;
- **`⋯`** no cabeçalho de `/pessoas/[id]` e `/imoveis/[id]` — desabilitado de
  propósito, para não anunciar uma ação que não existe;
- **"Esqueci minha senha"** no login — texto, não link: não há fluxo de recuperação;
- **`/auditoria`** — a rota existe e renderiza **sempre** um empty state. Os eventos
  são gravados de verdade e aparecem nas *timelines* de pessoa, imóvel e contrato,
  mas esta tela ainda não os lista.

### Não existe em lugar nenhum

Modo escuro · layout mobile (o app é desktop-only) · paginação fora de `/cobrancas` ·
ordenação por clique no cabeçalho · seleção múltipla ou ação em lote · exportação ·
tela de usuários/equipe · upload da logo da imobiliária · portais de inquilino ou
proprietário · relatórios · gráficos além do donut de "Saúde da operação".

### Planejado — **não animar**

Estas coisas estão no roadmap do projeto e **não foram descritas** nos outros
arquivos. Estão listadas aqui só para deixar a fronteira explícita:

repasse ao proprietário · CRM e funil comercial · site público por imobiliária ·
assistente de IA · programa de pontos · onboarding self-service de nova imobiliária ·
painel super-admin · importação por planilha · perfis granulares além de admin e
operador · reajustes automáticos · vistorias · notificações in-app · busca global.

---

## Duas coisas que a animação precisa respeitar

**1. Desktop, modo claro.** O produto foi construído para desktop e é integralmente
claro. Uma demonstração em telefone ou em tema escuro mostraria algo que não existe.

**2. Erro nunca vira vazio.** É um princípio transversal do produto, e ele é visível:
quando uma consulta falha, aparece um alerta vermelho honesto — nunca "nenhum
resultado". Se a animação puder mostrar isso uma vez, mostre: é a diferença entre um
sistema que admite não saber e um que finge.

---

## Limites deste handoff

Escrito sem screenshots — capturar telas exigiria autenticar numa instalação real, e
este documento é deliberadamente livre de acesso e de dados. A compensação foi
descrever a estrutura com precisão e trazer o **JSX real** onde ele comunica melhor
que a prosa.

Consequência prática: as proporções em ASCII são esquemas, não medidas. As medidas
verdadeiras estão nas classes citadas — larguras de coluna, paddings, alturas e raios
são todos literais do código.

---

*Documentação da interface do Locatizei. Todos os dados de exemplo são fictícios.*


---

<!-- ===== design-system.md ===== -->

# Design system — Locatizei

> Tudo nesta página foi extraído de `src/app/globals.css`, `src/app/layout.tsx` e dos
> componentes reais. Nenhum valor foi estimado, aproximado ou inferido de screenshot.

[◄ Voltar ao índice](README.md)

---

## 1. O ponto mais importante: **não existe tema escuro**

O arquivo `globals.css` traz um comentário no topo e outro no rodapé dizendo isso
literalmente. Reproduzo os dois, verbatim:

```css
/*
 * Convenção: cada token é declarado com valor literal em :root e depois
 * espelhado em `@theme inline` como `--color-*` / `--shadow-*` / `--radius-*`
 * para que o Tailwind v4 gere as utilities (bg-*, text-*, border-*,
 * shadow-*, rounded-*) automaticamente. Não há tema dark — o produto é
 * light-only nesta fase (ver nota no fim do arquivo).
 */
```

```css
/*
 * Sem modo escuro: o design aprovado (Locatizei — Telas MVP.dc.html) é
 * inteiramente light. A infraestrutura `dark:` dos componentes shadcn
 * (button/badge/avatar/input geradas pelo CLI) continua presente nas
 * classes, mas é inerte porque a classe `.dark` nunca é aplicada — ver
 * INTEGRATION-NOTES.md. Adicionar tokens dark aqui exigiria uma decisão de
 * design que não existe ainda.
 */
```

**O que isso significa para quem vai animar:** existe **um único valor por token**.
As tabelas abaixo não têm coluna "valor escuro" porque essa coluna não existe no
código. Você vai encontrar classes como `dark:bg-input/30` dentro dos componentes
shadcn — elas são código morto: `<html>` nunca recebe `class="dark"`
(`src/app/layout.tsx` monta `className={\`${plusJakartaSans.variable} ${geistMono.variable} h-full antialiased\`}` e nada mais).

O único gancho de tema declarado é:

```css
@custom-variant dark (&:is(.dark *));
```

…que ficaria ativo se alguém colocasse `.dark` num ancestral. Ninguém coloca.

---

## 2. Mecanismo real de tema

Tailwind v4, sem `tailwind.config.js`. O arquivo de estilos é a configuração:

```css
@import "tailwindcss";
@import "tw-animate-css";
@import "shadcn/tailwind.css";
```

Cada token é declarado **duas vezes**, de propósito:

1. em `:root`, com o valor literal (`--primary: #2140d6;`);
2. em `@theme inline`, mapeado para o namespace que o Tailwind entende
   (`--color-primary: var(--primary);`).

É o segundo passo que faz o Tailwind gerar as utilities `bg-primary`,
`text-primary`, `border-primary` etc. Um token que exista só em `:root` **não
gera classe nenhuma**.

Camada base aplicada a tudo:

```css
@layer base {
  * {
    @apply border-border outline-ring/50;
  }
  body {
    @apply bg-background text-foreground;
  }
  html {
    @apply font-sans;
  }
}
```

Consequência prática: a cor de borda padrão de **todo** elemento é `--border`
(`#e5e9f2`), e o outline de foco padrão é `--ring` a 50% de opacidade.

---

## 3. Paleta completa — valores exatos

### 3.1 Papéis nucleares (shadcn)

| Token CSS | Valor | Classe Tailwind gerada | Onde aparece de verdade |
|---|---|---|---|
| `--background` | `#ffffff` | `bg-background` | fundo do `body`, do shell `(app)`, do header e da faixa de nav |
| `--foreground` | `#17244a` | `text-foreground` | texto de corpo forte; wordmark "Locatizei"; nomes em tabelas |
| `--card` | `#ffffff` | `bg-card` | todo card, dialog, drawer, popover, linha de tabela |
| `--card-foreground` | `#17244a` | `text-card-foreground` | herdado; nenhum uso direto no código de tela |
| `--popover` | `#ffffff` | `bg-popover` | `DialogContent`, `SheetContent`, `DropdownMenuContent` |
| `--popover-foreground` | `#17244a` | `text-popover-foreground` | idem |
| `--primary` | `#2140d6` | `bg-primary` / `text-primary` | pill de nav ativa, botão primário, links, ícones de ação |
| `--primary-foreground` | `#ffffff` | `text-primary-foreground` | texto sobre azul |
| `--secondary` | `#f2f4f8` | `bg-secondary` | trilho da nav em pills, trilho da barra do KPI, chips |
| `--secondary-foreground` | `#3c4863` | `text-secondary-foreground` | texto sobre `secondary` |
| `--muted` | `#f4f6fa` | `bg-muted` | campo de busca global do header, hover de botões neutros |
| `--muted-foreground` | `#66708c` | `text-muted-foreground` | subtítulos de página, textos de apoio, empty states |
| `--accent` | `#e8eefd` | `bg-accent` | badge "info" (Aberta / Alugado / Agendado), ícone do KPI Previsto |
| `--accent-foreground` | `#2140d6` | `text-accent-foreground` | texto do badge info |
| `--destructive` | `#e0342c` | `text-destructive` | mensagem de erro de campo, hover do botão "Sair" |
| `--destructive-foreground` | `#ffffff` | `text-destructive-foreground` | — |
| `--border` | `#e5e9f2` | `border-border` | borda padrão de controle (`@layer base`) |
| `--input` | `#dde3ee` | `border-input` | borda de `<Input>` e de comboboxes |
| `--ring` | `#2140d6` | `ring-ring` | anel de foco (`focus-visible:ring-3 ring-ring/50`) |

### 3.2 Marca e acentos

| Token CSS | Valor | Classe | Uso verificado |
|---|---|---|---|
| `--brand-accent` | `#7b96f4` | `bg-brand-accent` | quadrado claro da marca (`LocatizeiMark`) |
| `--brand-hint` | `#f7f9fe` | `bg-brand-hint` | fundo de chip/filtro ativo, nota informativa azul, hover de "Cadastrar nova pessoa" |
| `--brand-soft` | `#e9eefc` | `bg-brand-soft` | fundo do botão "Registrar pagamento" / "Registrar contato" / "Ver cobrança" |
| `--brand-border-tint` | `#ccd6f2` | `border-brand-border-tint` | borda de chip ativo, borda tracejada da dropzone |
| `--primary-hover` | `#1c38c0` | `hover:bg-primary-hover` | hover do botão primário (variante `default` do `Button`) |
| `--primary-active` | `#1730a6` | `bg-primary-active` | declarado; **sem uso encontrado nos componentes** |

### 3.3 Superfícies e divisores

| Token CSS | Valor | Classe | Uso verificado |
|---|---|---|---|
| `--surface-sub` | `#fafbfd` | `bg-surface-sub` | painel de resumo dentro do drawer, cards de revisão do wizard, rodapé da inadimplência, caixinha de data em "Próximos vencimentos" |
| `--surface-hint` | `#fbfcff` | `bg-surface-hint` | dropzone de upload, linha de rascunho na lista de contratos |
| `--surface-login` | `#eef1f7` | `bg-surface-login` | fundo da tela de login (único uso) |
| `--border-card` | `#e8ecf3` | `border-border-card` | borda de **todo** card/tabela/dialog do app |
| `--divider` | `#eef1f6` | `border-divider` | separador de seção dentro de card; header do shell |
| `--divider-thin` | `#f2f4f8` | `border-divider-thin` | separador entre linhas de tabela; borda inferior da faixa de nav |

### 3.4 Escala de tinta (texto)

Cinco tons além de `foreground`/`muted-foreground`. Não substitua um pelo outro:
a diferença entre `#0f1d40` e `#17244a` é visível e deliberada.

| Token CSS | Valor | Classe | Uso verificado |
|---|---|---|---|
| `--ink-title` | `#0f1d40` | `text-ink-title` | **todo** `<h1>` de página, títulos de card, valores de KPI |
| `--ink-nav` | `#3c4863` | `text-ink-nav` | item de nav inativo; **todo** `<Label>` de formulário |
| `--ink-secondary` | `#4a5570` | `text-ink-secondary` | células de tabela secundárias, texto de chip inativo |
| `--ink-subtle` | `#8a93a8` | `text-ink-subtle` | segunda linha de tabela, legendas, meta de timeline, cabeçalho de coluna |
| `--ink-placeholder` | `#98a0b3` | `text-ink-placeholder` | placeholder de busca, ícone de lupa, "(opcional)" |
| `--ink-disabled` | `#c2c9d8` | `text-ink-disabled` | chevron de linha clicável, "—" de célula sem ação, seta de paginação desabilitada, borda tracejada do badge "Rascunho" |

### 3.5 Pares semânticos (fundo / texto) — o vocabulário de status

Cada par é usado sempre junto. É daqui que saem as variantes de `Badge`.

| Par | Fundo | Texto | Variante do `Badge` | Significado no produto |
|---|---|---|---|---|
| success | `--success` `#e4f6ec` | `--success-foreground` `#12a150` | `success` | Disponível (imóvel), Ativo (contrato), Paga (cobrança) |
| owner | `--owner` `#e0f3f1` | `--owner-foreground` `#0e8577` | `owner` | Proprietário(a), Locador(a), cobrança **Isentada** |
| info | `--accent` `#e8eefd` | `--accent-foreground` `#2140d6` | `info` | Inquilino(a), Alugado, Agendado, Aberta, Concluído |
| danger | `--danger` `#fdebea` | `--danger-foreground` `#e0342c` | `danger` | Vencida, Cancelado (contrato), badge de contagem da nav |
| danger sólido | `--danger-solid` `#e0342c` | `--danger-solid-foreground` `#ffffff` | `dangerSolid` | atraso ≥ 10 dias; botão do banner de vencida |
| warning | `--warning` `#fef3df` | `--warning-foreground` `#b26e05` | `warning` | Parcialmente paga, Em revisão, Suspenso, Manutenção, "Sem email", "Emails pausados", "Pagamento direto", atraso de 1 dia |
| orange | `--orange` `#fdeee0` | `--orange-foreground` `#c2611a` | `orange` | Encerrando (contrato), atraso de 2–5 dias |
| purple | `--purple` `#efeafd` | `--purple-foreground` `#7c5cf0` | `purple` | Reservado (imóvel), Em negociação (cobrança) |
| ia | `--ia` `#ecebfd` | `--ia-foreground` `#6c5ce7` | `ia` | reservado para o Assistente — **nenhum uso hoje** além da paleta de avatar |
| neutral | `--neutral-badge` `#eef1f6` | `--neutral-badge-foreground` `#66708c` | `neutral` | Agendada, Cancelada, Indisponível, contadores de card |
| archived | `--archived` `#e9ecf2` | `--archived-foreground` `#8a93a8` | `archived` | Arquivado, Encerrado |

Tokens de gradiente/borda do "ia" existem e **não são usados** por nenhum
componente hoje: `--ia-gradient-from #f6f4ff`, `--ia-gradient-to #edeffe`,
`--ia-border #e3e3f8`.

### 3.6 Tons de alerta específicos de formulário

Família semântica de warning, mas **tons próprios** — não intercambiáveis com o par
`warning`/`warning-foreground` dos badges.

| Token CSS | Valor | Classe | Uso verificado |
|---|---|---|---|
| `--warning-border` | `#e8b658` | `border-warning-border` | borda do input com aviso de duplicado; input de valor divergente |
| `--warning-surface` | `#fffdf7` | `bg-warning-surface` | fundo desse mesmo input |
| `--warning-banner` | `#fff7e8` | `bg-warning-banner` | banner âmbar não bloqueante (duplicado, "interruptor-mestre", "desligar não cancela", impacto do encerramento) |
| `--warning-banner-border` | `#f0dcae` | `border-warning-banner-border` | borda desse banner |
| `--warning-banner-foreground` | `#7a5a10` | `text-warning-banner-foreground` | texto do banner |
| `--warning-banner-foreground-subtle` | `#a3821f` | `text-warning-banner-foreground-subtle` | segunda linha do banner de duplicado |
| `--warning-banner-button-border` | `#e2c581` | `border-warning-banner-button-border` | borda do botão "Salvar mesmo assim" |

### 3.7 Tons pontuais de tela

| Token CSS | Valor | Classe | Onde nasce |
|---|---|---|---|
| `--danger-banner` | `#fdf1f0` | `bg-danger-banner` | banner vermelho de cobrança vencida no detalhe do contrato |
| `--danger-banner-border` | `#f5cdc9` | `border-danger-banner-border` | idem |
| `--danger-banner-foreground` | `#8e2a22` | `text-danger-banner-foreground` | idem |
| `--row-selected` | `#f4f7fe` | `bg-row-selected` | linha da tabela de cobranças cujo drawer está aberto |
| `--info-banner-border` | `#e3eafc` | `border-info-banner-border` | borda da nota "Ao confirmar, a cobrança passa a…" (fundo reusa `brand-hint`) |
| `--success-hint` | `#f6fcf9` | `bg-success-hint` | fundo do empty state positivo de `/inadimplencia`; chip "Carteira ativa" |
| `--success-border` | `#cdeadb` | `border-success-border` | borda tracejada desse empty state |

### 3.8 Cores de gráfico

Definidas direto em `@theme inline` (não passam por `:root`), consumidas via
`var(--color-chart-N)` no `conic-gradient` do donut de "Saúde da operação".

| Token | Valor | Segmento |
|---|---|---|
| `--color-chart-1` | `#12a150` | Alugados |
| `--color-chart-2` | `#f0a020` | Disponíveis |
| `--color-chart-3` | `#d9dee8` | Manutenção |
| `--color-chart-4` | `#7c5cf0` | Outros (só aparece se > 0) |
| `--color-chart-5` | `#6c5ce7` | declarado; **sem uso** |

---

## 4. Tipografia

### 4.1 Famílias

Duas fontes carregadas em `src/app/layout.tsx` via `next/font/google`:

```tsx
const plusJakartaSans = Plus_Jakarta_Sans({
  variable: "--font-sans",
  subsets: ["latin"],
});

const geistMono = Geist_Mono({
  variable: "--font-geist-mono",
  subsets: ["latin"],
});
```

Mapeamento em `globals.css`:

```css
--font-sans: var(--font-sans);      /* Plus Jakarta Sans */
--font-mono: var(--font-geist-mono); /* Geist Mono */
--font-heading: var(--font-sans);    /* títulos usam a MESMA fonte */
```

- **Plus Jakarta Sans** é a fonte de tudo. `html { @apply font-sans; }`.
- **Geist Mono** está carregada e mapeada para `font-mono`, mas **nenhum componente
  de tela usa `font-mono`**. Se sua animação mostrar texto monoespaçado, ela estará
  mostrando algo que o produto não faz.
- Não há fonte separada de título: `--font-heading` aponta para a mesma família.

### 4.2 Escala real (tamanhos usados no código, não uma escala teórica)

O projeto usa **muito valor arbitrário** (`text-[13.5px]`). Isto é a escala de fato:

| Tamanho | Peso | Onde |
|---|---|---|
| `text-[26px]` + `font-bold` + `tracking-[-0.02em]` | 700 | `<h1>` de **toda** página do app |
| `text-[23px]` + `font-bold` + `tracking-[-0.02em]` | 700 | valor grande dos 3 cards do detalhe de contrato |
| `text-[22px]` + `font-bold` + `tracking-[-0.02em]` | 700 | valor dos 4 KPIs do dashboard |
| `text-[20px]` + `font-extrabold` + `tracking-[-0.02em]` | 800 | wordmark "Locatizei"; título do card de login |
| `text-lg` (18px) + `font-extrabold` | 800 | número central do donut; iniciais do avatar 56px |
| `text-lg` (18px) + `font-semibold` | 600 | título do `EmptyState` |
| `text-[17px]` + `font-bold` | 700 | **todo** `DialogTitle`/`SheetTitle` |
| `text-[16.5px]` + `font-bold` | 700 | título do empty state positivo de inadimplência |
| `text-[16px]` + `font-semibold` | 600 | "Sem cobranças a vencer" / "Nenhum pagamento registrado" |
| `text-[15px]` + `font-bold` | 700 | **todo** título de card (`Emails de cobrança`, `Mercado Pago`, `Timeline`, `Agenda de cobranças`, `Exige atenção hoje`…) |
| `text-[14.5px]` + `font-bold` | 700 | nome do proprietário no card de imóvel |
| `text-sm` (14px) + `font-semibold` | 600 | nome de pessoa em linha de tabela; label do Switch |
| `text-sm` (14px) | 400 | subtítulo de página, empty states, endereço |
| `text-[13.5px]` | 400–700 | **o tamanho mais frequente do app**: célula de tabela, item de timeline, texto do banner, `InfoRow` |
| `text-[13px]` | 400–600 | `<Label>` de formulário; célula de competência; chip de filtro; busca |
| `text-[12.5px]` | 400–700 | descrição de dialog, nota de campo, rodapé de tabela, erro de campo |
| `text-xs` (12px) | 400–700 | segunda linha de tabela, cabeçalho de coluna, badge, meta de timeline |
| `text-[11.5px]` | 400–700 | "Pessoa física/jurídica", sublinha "R$ X recebidos", iniciais 34px |
| `text-[11px]` | 400–800 | label de `InfoRow` do wizard, badge de contagem da nav, tamanho de arquivo |
| `text-[10px]` | 400 | "imóveis" sob o donut |
| `text-[9.5px]` + `font-bold` + `tracking-[0.08em]` | 700 | mês abreviado na caixinha de data |
| `text-[9px]` + `font-extrabold` | 800 | selo "PDF"/"IMG" 28px no dialog de recibo |

### 4.3 Pesos

Só cinco, e o produto usa todos: `font-medium` (500), `font-semibold` (600),
`font-bold` (700), `font-extrabold` (800) e o normal (400). Não há `font-light`
nem `font-thin` em lugar nenhum.

Regra observada: **título de card = `font-bold`**, **título de página = `font-bold`
+ `tracking-[-0.02em]`**, **wordmark e números heroicos = `font-extrabold`**.

### 4.4 Alturas de linha

Só três tratamentos aparecem:

- `leading-relaxed` (1.625) — textos de banner, descrição de dialog, itens de timeline;
- `leading-tight` (1.25) — bloco nome+papel no header; número do donut; dia na caixinha de data;
- padrão do Tailwind no resto (nenhuma classe `leading-*`).

### 4.5 Tracking

- `tracking-[-0.02em]` — todos os títulos e números grandes;
- `tracking-[0.04em]` + `uppercase` — rótulo dos cards de revisão do wizard ("IMÓVEL", "PARTES"…);
- `tracking-[0.08em]` — mês abreviado ("AGO") na caixinha de data.

---

## 5. Raios

Duas famílias convivem. A escala shadcn (derivada de `--radius: 0.625rem` = 10px) e
uma lista de raios literais do protótipo. **Os raios literais são os mais usados nas
telas.**

### 5.1 Escala derivada

```css
--radius: 0.625rem; /* 10px */
--radius-sm:  calc(var(--radius) * 0.6);  /*  6px */
--radius-md:  calc(var(--radius) * 0.8);  /*  8px */
--radius-lg:  var(--radius);              /* 10px */
--radius-xl:  calc(var(--radius) * 1.4);  /* 14px */
--radius-2xl: calc(var(--radius) * 1.8);  /* 18px */
--radius-3xl: calc(var(--radius) * 2.2);  /* 22px */
--radius-4xl: calc(var(--radius) * 2.6);  /* 26px */
```

`rounded-lg` (10px) é o raio de botão e input. `rounded-xl` (14px) é o raio de
popover, dropdown e toast. `rounded-4xl` (26px) é o raio do `Badge` — na prática
uma pílula, porque o badge tem `h-5` (20px).

### 5.2 Raios literais do protótipo

| Token | Valor | Classe | Uso |
|---|---|---|---|
| `--radius-card` | `1rem` (16px) | `rounded-card` | **todo** card, dialog, tabela, monograma do login |
| `--radius-subcard` | `0.75rem` (12px) | `rounded-subcard` | sub-card, banner, painel interno, busca do header, seletor de mês |
| `--radius-btn-sm` | `0.5625rem` (9px) | `rounded-btn-sm` | botões pequenos de linha ("Registrar pagamento", "Ver cobrança") |
| `--radius-pill` | `999px` | `rounded-pill` | pills de nav, chips de forma de pagamento, badges de contagem, barra do KPI |
| `--radius-logo` | `0.4375rem` (7px) | `rounded-logo` | os dois quadrados da marca |

Fora da escala, valores literais em `className`: `rounded-[20px]` (card de login),
`rounded-[12px]` (CTA da nav, botão de login), `rounded-[10px]` (inputs do login),
`rounded-[6px]` (checkbox de papel no formulário de pessoa).

---

## 6. Sombras

Oito sombras nomeadas, todas com valor exato no arquivo. Nenhuma `shadow-md`
genérica do Tailwind é usada nas telas (só dentro dos primitivos shadcn).

| Token | Valor | Classe | Onde |
|---|---|---|---|
| `--shadow-card` | `0 1px 2px rgba(16, 24, 40, 0.04)` | `shadow-card` | **todo** card e tabela do app |
| `--shadow-btn-primary` | `0 6px 16px rgba(33, 64, 214, 0.25)` | `shadow-btn-primary` | botão primário de submit e o CTA da nav |
| `--shadow-cta` | `0 8px 20px rgba(33, 64, 214, 0.35)` | `shadow-cta` | **só** o botão "Ativar contrato" (etapa 4 do wizard) |
| `--shadow-dropdown` | `0 12px 32px rgba(16, 24, 40, 0.12)` | `shadow-dropdown` | `PopoverContent` e `DropdownMenuContent` |
| `--shadow-toast` | `0 8px 24px rgba(16, 24, 40, 0.12)` | `shadow-toast` | **todo** toast |
| `--shadow-dialog` | `0 24px 60px rgba(16, 24, 40, 0.22)` | `shadow-dialog` | **todo** dialog |
| `--shadow-drawer` | `-24px 0 60px rgba(16, 24, 40, 0.25)` | `shadow-drawer` | o drawer de pagamento (sombra projetada para a **esquerda**) |
| `--shadow-login-card` | `0 20px 50px rgba(16, 24, 40, 0.08)` | `shadow-login-card` | card de login |

Sombra inline única do app, na linha selecionada da tabela de cobranças:

```
shadow-[inset_3px_0_0_var(--primary)]
```

— uma barra azul de 3px colada na borda esquerda da linha.

---

## 7. Espaçamento e layout

Não há escala de espaçamento própria: o projeto usa a do Tailwind (múltiplos de
0.25rem) mais valores literais pontuais.

### 7.1 Estrutura da página

```tsx
// src/app/(app)/layout.tsx
<div className="flex min-h-screen flex-col bg-background">
  <ShellHeader … />
  <PillsNav … />
  <main className="mx-auto w-full max-w-[1440px] flex-1 px-8 py-8">{children}</main>
</div>
```

- **Largura máxima de conteúdo: `1440px`**, com `px-8` (32px) de respiro lateral.
  O header e a faixa de nav repetem exatamente `max-w-[1440px]` + `px-8`, o que
  alinha logo, nav e conteúdo na mesma coluna.
- `py-8` (32px) de respiro vertical do conteúdo.

### 7.2 Ritmo vertical dentro da página

Padrão que se repete em quase toda tela:

| Distância | Classe | Entre o quê |
|---|---|---|
| 6px | `mt-1.5` | `<h1>` → subtítulo |
| 16px | `mt-4` | subtítulo → primeiro bloco; grid de KPIs → grid de cards |
| 18px | `mt-[18px]` | `<h1>`/subtítulo → barra de filtros de tabela |
| 20px | `mt-5` | card → card em página de formulário/detalhe |
| 22px | `mt-[22px]` | header de detalhe → primeiro card |

### 7.3 Padding interno

| Contexto | Classe |
|---|---|
| Card de conteúdo | `p-4` (dashboard) ou `px-5 py-[18px]` (detalhe) |
| Card de formulário | `px-7 py-[26px]` |
| Dialog | `p-[26px]` |
| Drawer (header/corpo/rodapé) | `px-6 py-[18px]` / `px-6 py-[18px]` / `px-6 py-4` |
| Tabela — cabeçalho | `px-[22px] py-3` |
| Tabela — linha | `px-[22px] py-[11px]` (cobranças) · `py-[13px]` (pessoas, imóveis, contratos, inadimplência) · `py-3` (agenda) · `py-3.5` (histórico de contratos) |
| Empty state | `p-12` |

### 7.4 Grids de tabela

As tabelas **não** usam `<table>` com larguras automáticas: cada `TableRow` recebe
`className="grid …"` + `style={{ gridTemplateColumns: GRID_COLS }}`. As constantes
reais:

| Tela | `GRID_COLS` |
|---|---|
| Cobranças | `120px 1.65fr 110px 120px 155px 215px` |
| Inadimplência | `1.25fr 1.55fr 120px 115px 120px 150px 160px` |
| Contratos | `1.7fr 1.2fr 1.25fr 120px 130px 120px` |
| Pessoas | `1.45fr 200px 170px 1.5fr 120px 30px` |
| Imóveis | `110px 1.9fr 140px 1.3fr 140px 30px` |
| Agenda do contrato | `1.2fr 1fr 1fr 150px 170px` |
| Contratos em `/pessoas/[id]` | `150px 1.7fr 1.2fr 130px 120px 30px` |
| Contratos em `/imoveis/[id]` | `1.6fr 1.3fr 130px 120px 30px` |
| Prévia de agenda (wizard) | `1fr 1fr 1fr 1fr` |

Todas com `gap-3` (12px) entre colunas.

---

## 8. Foco, estados e acessibilidade

Dois tratamentos de foco convivem, e ambos são intencionais:

**a) Anel** — o padrão dos primitivos e da maioria dos botões customizados:

```
outline-none focus-visible:ring-3 focus-visible:ring-ring/50
```

**b) Outline** — usado onde o design pediu contorno externo (README de design
citado no código: "outline 2px offset 2px primary"):

```
outline-none focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-primary
```

Aparece nas pills de navegação e no botão de mostrar/ocultar senha do login.

Estado de erro de campo: `aria-invalid` é injetado automaticamente no filho pelo
helper `Field` (repetido localmente em 8 arquivos), e o `Input` reage com
`aria-invalid:border-destructive aria-invalid:ring-3 aria-invalid:ring-destructive/20`.

Estado desabilitado do `Button`: `disabled:pointer-events-none disabled:opacity-50`.

Micro-interação global do `Button`: `active:not-aria-[haspopup]:translate-y-px` —
o botão desce 1px ao ser pressionado, **exceto** quando abre um menu.

---

## 9. Transições e animação

Não há biblioteca de animação no app. O que existe:

- `transition-colors` em pills, chips, botões de forma de pagamento e dropzone;
- `transition-all` no `Button`, `Badge` e `Switch`;
- `animate-spin` no ícone do botão "Atualizar" do dashboard, enquanto o
  `useTransition` do `router.refresh()` está pendente;
- as animações do `tw-animate-css` embutidas nos primitivos: `data-open:animate-in
  data-open:fade-in-0 data-open:zoom-in-95` para Dialog/Dropdown (`duration-100`), e
  `data-[side=right]:data-open:slide-in-from-right-10` para o Sheet
  (`transition duration-200 ease-in-out`).
- overlay de Dialog e Sheet: `bg-black/10` + `supports-backdrop-filter:backdrop-blur-xs`.

---

## 10. Responsividade — o que existe hoje

**O app foi construído para desktop.** Isso está registrado no roadmap do projeto
como um milestone ainda não executado ("Responsividade mobile do app inteiro"), e o
código confirma: as tabelas têm largura de coluna em `px`/`fr` fixos, sem variante
mobile; o shell não tem bottom nav; os grids de detalhe são `grid-cols-3` /
`grid-cols-2` sem breakpoint.

Os **únicos** breakpoints presentes no código de tela:

| Onde | Classe |
|---|---|
| Grid dos 4 KPIs | `grid-cols-1 sm:grid-cols-2 lg:grid-cols-4` |
| Grid dos 3 cards do dashboard | `grid-cols-1 lg:grid-cols-3` |
| Largura do drawer | `w-[440px] max-w-[calc(100%-2rem)] sm:max-w-[440px]` |
| Largura dos dialogs | `w-[440px]`/`w-[480px]` + `max-w-[calc(100%-2rem)]` + `sm:max-w-[…]` |
| Tamanho de fonte do `Input` | `text-base md:text-sm` |

**Nos emails**, sim, há media query real (`@media only screen and (max-width: 480px)`),
com `.mob-px` reduzindo o padding lateral para 20px e `.mob-val` baixando o valor
heroico para 30px.

> **Para a animação:** mostre o produto em desktop. Uma demonstração em telefone
> mostraria uma tela que hoje não faz reflow.

---

## 11. Ícones

`lucide-react`, sempre com `aria-hidden="true"`. Tamanhos padronizados por contexto:

| Tamanho | Onde |
|---|---|
| `size-6` (24px) | ícone dentro do círculo de 52/54px (empty state positivo, etapa 4 do wizard) |
| `size-5` (20px) | ícone do KPI dentro do círculo de 40px |
| `size-[22px]` | ícone da dropzone de documentos |
| `size-[18px]` | sino e "?" do header |
| `size-[17px]` | ícone do banner de vencida |
| `size-4` (16px) | ícone de botão (padrão do `Button`), seta de linha, chevrons do seletor de mês |
| `size-3.5` (14px) | ícone dentro de timeline, toast, banner, lupa, ⋯ |
| `size-3` (12px) | seta do delta do KPI, chevron de filtro, `CircleAlert` de erro de campo |
| `size-2` (8px) | bolinha de legenda do donut |

O `Button` força `[&_svg:not([class*='size-'])]:size-4` — todo ícone sem classe de
tamanho vira 16px dentro dele.

---

## 12. A marca

Não é um SVG. São **dois `<span>` quadrados sobrepostos**:

```tsx
export function LocatizeiMark({ className }: { className?: string }) {
  return (
    <span className={cn("relative inline-block size-5 w-8 shrink-0", className)} aria-hidden="true">
      <span className="absolute top-0 left-0 size-5 rounded-logo bg-brand-accent" />
      <span className="absolute top-0 left-3 size-5 rounded-logo bg-primary" />
    </span>
  );
}
```

20×20px cada, raio de 7px, deslocados 12px no eixo X. O de trás é
`--brand-accent` (`#7b96f4`), o da frente é `--primary` (`#2140d6`). O conjunto
ocupa 32×20px.

O wordmark põe a marca ao lado do texto:

```tsx
"flex shrink-0 items-center gap-2.5 text-[20px] font-extrabold tracking-[-0.02em] text-foreground"
```

Repare: o texto "Locatizei" usa `text-foreground` (`#17244a`), **não** `ink-title`.
O comentário do arquivo registra a escolha explicitamente.

Na tela de login a marca é outra coisa: um quadrado 56×56 (`size-14`) azul sólido
com a letra **"L"** em branco, `rounded-card`, `text-2xl font-extrabold`.

---

## 13. Sistema de cor dos avatares

Não há foto de perfil em lugar nenhum. Todo avatar é **iniciais sobre uma cor
derivada por hash do nome** (`src/lib/avatar-color.ts`):

```ts
const PALETTE = [
  { bg: "bg-accent",        fg: "text-accent-foreground" },
  { bg: "bg-success",       fg: "text-success-foreground" },
  { bg: "bg-owner",         fg: "text-owner-foreground" },
  { bg: "bg-warning",       fg: "text-warning-foreground" },
  { bg: "bg-purple",        fg: "text-purple-foreground" },
  { bg: "bg-orange",        fg: "text-orange-foreground" },
  { bg: "bg-ia",            fg: "text-ia-foreground" },
  { bg: "bg-neutral-badge", fg: "text-neutral-badge-foreground" },
] as const;

export function avatarPalette(name: string) {
  const hash = Array.from(name).reduce((acc, ch) => acc + ch.charCodeAt(0), 0);
  return PALETTE[hash % PALETTE.length]!;
}
```

Iniciais: primeira letra do primeiro nome + primeira do último. Nome de uma palavra
só vira as duas primeiras letras. Nome vazio vira `?`.

**Detalhe de forma que carrega significado:** pessoa física recebe
`rounded-full` (círculo); pessoa jurídica recebe `rounded-lg` (quadrado
arredondado). Isso vale em `/pessoas`, `/pessoas/[id]`, no card de proprietário do
imóvel e nos comboboxes. No header do shell e nos cards do dashboard o avatar é
sempre círculo.

---

## 14. Os emails têm um design system **separado**

Os templates transacionais (`src/emails/`) **não** usam Tailwind nem os tokens acima.
São tabelas HTML com estilos inline, e uma paleta própria em `src/emails/theme.ts`:

| Token | Valor | Papel |
|---|---|---|
| `color.page` | `#EEF1F4` | fundo da página do email |
| `color.card` | `#FFFFFF` | card branco central (max-width 600px) |
| `color.cardBorder` | `#E3E7EC` | borda do card |
| `color.panel` | `#F6F8FA` | painel cinza de detalhes / "Como pagar" |
| `color.panelBorder` | `#EAEEF2` | filete entre linhas do painel |
| `color.hairline` | `#ECEFF3` | filete acima do bloco de detalhes |
| `color.ink` | `#1F2933` | título e valor heroico |
| `color.inkStrong` | `#3C4653` | texto do painel |
| `color.inkDetail` | `#5A6472` | endereço sob o código do imóvel |
| `color.inkMuted` | `#66707D` | saudação, rótulos |
| `color.inkFaint` | `#8A94A0` | ressalvas, rodapé da agência |
| `color.inkPlatform` | `#A6AEB8` | "Enviado via Locatizei" |
| `color.reminderBg` / `Fg` | `#EEF1F4` / `#445263` | selo **LEMBRETE** |
| `color.dueBg` / `Fg` | `#FBF3E4` / `#7A5410` | selo **VENCE HOJE**; bloco de saldo restante do recibo |
| `color.lateBg` / `Fg` | `#FAEDE8` / `#8C3A24` | selo **EM ATRASO** |
| `color.successBg` / `Fg` | `#E7F3EB` / `#2F7D4F` | círculo do ✓ no recibo |

Fonte dos emails: `'Helvetica Neue',Helvetica,Arial,sans-serif` — **não** é a Plus
Jakarta Sans do app. Cliente de email não carrega webfont de forma confiável.

Cor de marca: a faixa do topo, o botão e os links usam
`agencies.brand_color`, escolhida pela imobiliária em Configurações. Sem escolha,
cai no padrão `DEFAULT_BRAND_COLOR = color.ink` (`#1F2933`).

---

## 15. Lacunas declaradas

- **Tema escuro:** não existe. Não há decisão de design para ele.
- `--primary-active` (`#1730a6`), `--color-chart-5` (`#6c5ce7`) e o trio
  `--ia-gradient-from` / `--ia-gradient-to` / `--ia-border` estão declarados e
  **não são consumidos** por nenhum componente.
- A variante `ia` do `Badge` existe e só é alcançada pela paleta de avatar — nenhum
  status de produto usa "ia" hoje.
- `font-mono` está configurada e não é usada em nenhuma tela.
- Não existe token de espaçamento próprio; a escala é a do Tailwind.
- Não existe página de estilo/storybook no repositório para conferência visual.

---

[◄ Voltar ao índice](README.md) · [Componentes ►](componentes.md)


---

<!-- ===== componentes.md ===== -->

# Componentes — inventário real

> Um item por arquivo de `src/components/`. Props, variantes, estados visuais e as
> classes Tailwind **exatas**. Onde o JSX comunica melhor que a prosa, o JSX está
> aqui, copiado do arquivo.

[◄ Voltar ao índice](README.md) · [◄ Design system](design-system.md)

---

## Mapa dos arquivos

```
src/components/
├── app/            shell e primitivas de produto (6)
│   ├── empty-state.tsx
│   ├── logo.tsx
│   ├── pills-nav.tsx
│   ├── shell-header.tsx
│   ├── status-badge.tsx
│   └── timeline.tsx
├── charges/        cobranças e inadimplência (7)
│   ├── charge-actions-menu.tsx
│   ├── charges-table.tsx
│   ├── contact-dialog.tsx
│   ├── delinquency-table.tsx
│   ├── payment-drawer.tsx
│   ├── receipt-dialog.tsx
│   └── settle-dialog.tsx
├── contracts/      contratos (6)
│   ├── charge-quick-actions.tsx
│   ├── contract-wizard.tsx
│   ├── contracts-table.tsx
│   ├── document-upload.tsx
│   ├── schedule-preview.tsx
│   └── terminate-dialog.tsx
├── dashboard/      dashboard (6)
│   ├── attention-card.tsx
│   ├── health-card.tsx
│   ├── kpi-cards.tsx
│   ├── month-selector.tsx
│   ├── refresh-button.tsx
│   └── upcoming-card.tsx
├── people/         pessoas (3)
├── properties/     imóveis (2)
├── settings/       configurações (2)
└── ui/             primitivos shadcn/radix (20)
```

---

# 1 · Shell (`components/app/`)

## `logo.tsx` — `LocatizeiMark`, `LocatizeiWordmark`

Nenhum arquivo de imagem. A marca são dois quadrados sobrepostos em CSS.

```tsx
export function LocatizeiMark({ className }: { className?: string }) {
  return (
    <span className={cn("relative inline-block size-5 w-8 shrink-0", className)} aria-hidden="true">
      <span className="absolute top-0 left-0 size-5 rounded-logo bg-brand-accent" />
      <span className="absolute top-0 left-3 size-5 rounded-logo bg-primary" />
    </span>
  );
}
```

`LocatizeiWordmark` = a marca + a palavra:
`"flex shrink-0 items-center gap-2.5 text-[20px] font-extrabold tracking-[-0.02em] text-foreground"`

**Props:** `{ className?: string }` nos dois. Sem variantes, sem estados.

---

## `shell-header.tsx` — `ShellHeader`

Barra superior de todo o app autenticado.

**Props:** `{ agencyName: string; userName: string; role: "admin" | "operator" }`

**Estrutura (esquerda → direita):**

```
[marca Locatizei] [busca global ⌘K ───────] ······ [seletor de agência ▾] [🔔] [?] [avatar] [nome / papel] [Sair]
```

```tsx
<header className="border-b border-divider bg-background">
  <div className="mx-auto flex max-w-[1440px] items-center gap-6 px-8 pt-4 pb-3">
```

**Rótulo de papel:** mapa fixo — `admin → "Gestora"`, `operator → "Operador"`.
(Sim: "Gestora", no feminino, é o rótulo literal no código.)

**⚠️ Quatro elementos são estáticos — não fazem nada.** O comentário do arquivo diz:
*"Busca global, seletor de agência, sino e '?' são visuais estáticos — nenhuma dessas
ações está implementada ainda (sem command palette, sem notificações reais)."*

| Elemento | Classes | Comportamento real |
|---|---|---|
| Busca global | `flex h-10 max-w-md flex-1 items-center gap-2 rounded-subcard bg-muted px-3.5 text-[13px] text-ink-placeholder` | **`<div>`, não `<input>`.** Texto fixo "Buscar pessoas, imóveis, contratos, cobranças..." + chip `⌘ K`. Não clicável, não focável. |
| Seletor de agência | `flex h-9 items-center gap-1.5 rounded-subcard border border-border px-3 text-[13px] font-medium text-ink-nav` | Mostra o nome real da agência + `ChevronDown`. Não abre nada. |
| Sino | `flex size-9 items-center justify-center rounded-full text-ink-subtle` | `title="Notificações (em breve)"`. Sem contador. |
| Ajuda | `flex size-9 items-center justify-center rounded-full border border-border text-ink-subtle` | `title="Ajuda (em breve)"` |

**O que funciona:** o avatar (iniciais + cor por hash), o nome/papel, e o botão
**Sair** — um `<form action={signOut}>` com `Button variant="ghost" size="sm"` e
`hover:text-destructive`.

---

## `pills-nav.tsx` — `PillsNav`

A segunda faixa. Pills num trilho cinza, e um CTA contextual à direita.

**Props:** `{ isAdmin: boolean; overdueCount: number }`

**Itens (nesta ordem):**

```ts
const NAV = [
  { href: "/",              label: "Dashboard" },
  { href: "/pessoas",       label: "Pessoas" },
  { href: "/imoveis",       label: "Imóveis" },
  { href: "/contratos",     label: "Contratos" },
  { href: "/cobrancas",     label: "Cobranças" },
  { href: "/inadimplencia", label: "Inadimplência" },
] as const;

const ADMIN_NAV = [
  { href: "/configuracoes", label: "Configurações" },
  { href: "/auditoria",     label: "Auditoria" },
] as const;
```

`ADMIN_NAV` só entra quando `isAdmin` é true. **Operador vê 6 pills; admin vê 8.**

**Trilho:**
`"flex items-center gap-1 overflow-x-auto rounded-pill bg-secondary p-1"`

**Pill:**

```tsx
className={cn(
  "flex shrink-0 items-center gap-1.5 rounded-pill px-5 py-[9px] text-sm font-medium whitespace-nowrap transition-colors",
  "outline-none focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-primary",
  active
    ? "bg-primary font-semibold text-primary-foreground"
    : "text-ink-nav hover:text-ink-title",
)}
```

| Estado | Fundo | Texto | Peso |
|---|---|---|---|
| Inativo | transparente (trilho `#f2f4f8`) | `#3c4863` | 500 |
| Hover | transparente | `#0f1d40` | 500 |
| Ativo | `#2140d6` | `#ffffff` | 600 |
| Foco | + outline 2px `#2140d6`, offset 2px | | |

**Badge de contagem** — só em "Inadimplência", só quando `overdueCount > 0`:

```tsx
className={cn(
  "rounded-pill px-[7px] py-px text-[11px] font-bold",
  active ? "bg-white/25 text-white" : "bg-danger text-danger-foreground",
)}
```

Ou seja: vermelho suave quando a pill está inativa, branco translúcido quando ativa.

**CTA contextual** (à direita da barra) — aparece em 3 seções, e só nelas:

```ts
const SECTION_CTA: Record<string, { label: string; href: string }> = {
  "/pessoas":   { label: "Nova pessoa",  href: "/pessoas/nova" },
  "/imoveis":   { label: "Novo imóvel",  href: "/imoveis/novo" },
  "/contratos": { label: "Novo contrato", href: "/contratos/novo" },
};
```

Classes: `"shrink-0 rounded-[12px] px-5 py-[11px] text-sm font-semibold shadow-btn-primary"`
com ícone `Plus` `size-4`. **Dashboard, Cobranças, Inadimplência, Configurações e
Auditoria não têm CTA** — o comentário do arquivo é explícito: *"Seções sem ação de
criação direta … não recebem CTA — nada é inventado."*

---

## `status-badge.tsx` — `StatusBadge`, `RoleBadge`, `LateBadge`

O tradutor central entre estado do banco e cor na tela. **Nenhuma cor hardcoded**:
tudo passa pelas variantes do `Badge`.

### `StatusBadge`

**Props:** `{ kind: "property" | "contract" | "charge"; value: string }`

Mapa completo (verbatim):

```ts
const STATUS = {
  property: {
    draft:        { label: "Rascunho",     variant: "dashed" },
    available:    { label: "Disponível",   variant: "success" },
    reserved:     { label: "Reservado",    variant: "purple" },
    rented:       { label: "Alugado",      variant: "info" },
    unavailable:  { label: "Indisponível", variant: "neutral" },
    maintenance:  { label: "Manutenção",   variant: "warning" },
    archived:     { label: "Arquivado",    variant: "archived" },
  },
  contract: {
    draft:                    { label: "Rascunho",   variant: "dashed" },
    pending_review:           { label: "Em revisão", variant: "warning" },
    scheduled:                { label: "Agendado",   variant: "info" },
    active:                   { label: "Ativo",      variant: "success" },
    suspended:                { label: "Suspenso",   variant: "warning" },
    termination_in_progress:  { label: "Encerrando", variant: "orange" },
    ending:                   { label: "Encerrando", variant: "orange" }, // DERIVADO
    terminated:               { label: "Encerrado",  variant: "archived" },
    completed:                { label: "Concluído",  variant: "info" },
    cancelled:                { label: "Cancelado",  variant: "danger" },
  },
  charge: {
    scheduled:         { label: "Agendada",           variant: "neutral" },
    open:              { label: "Aberta",             variant: "info" },
    overdue:           { label: "Vencida",            variant: "danger" },
    partially_paid:    { label: "Parcialmente paga",  variant: "warning" },
    paid:              { label: "Paga",               variant: "success" },
    waived:            { label: "Isentada",           variant: "owner" },
    cancelled:         { label: "Cancelada",          variant: "neutral" },
    under_negotiation: { label: "Em negociação",      variant: "purple" },
  },
};
```

Valor desconhecido cai em `variant: "outline"` com a **string crua** como rótulo.

`ending` não existe no banco: é calculado na hora de exibir por
`contractDisplayStatus(status, endDate, today)` — contrato `active` cujo `end_date`
cai em até **60 dias** vira "Encerrando".

### `RoleBadge`

**Props:** `{ role: string }`

```ts
const ROLE_META = {
  tenant: { label: "Inquilino(a)",     variant: "info" },
  owner:  { label: "Proprietário(a)",  variant: "owner" },
};
```

O "(a)" é deliberado: o cadastro não tem campo de gênero, então o rótulo não presume.

### `LateBadge`

**Props:** `{ days: number; intensity: LateIntensity }`

O texto é `"{days} dia"` / `"{days} dias"`. A cor escala com o atraso:

```ts
const LATE_INTENSITY_VARIANT = {
  days10plus: "dangerSolid",  // ≥ 10 dias — VERMELHO SÓLIDO, texto branco
  days6to9:   "danger",       // 6–9 dias  — vermelho suave
  days2to5:   "orange",       // 2–5 dias  — laranja
  day1:       "warning",      // 1 dia     — âmbar
};
```

Essa escalada de cor é um dos momentos mais legíveis do produto numa animação.

---

## `timeline.tsx` — `Timeline`, `formatRelative`, `ACTION_META`

Lista vertical de eventos com ícone circular tintado e conector.

**Props:** `{ events: TimelineEvent[] }` onde
`TimelineEvent = { id: string; action: string; created_at: string; actor_name?: string | null }`

**Estado vazio:** `<p className="text-sm text-muted-foreground">Nenhum evento registrado ainda.</p>`

**Estrutura de um item:**

```tsx
<li key={e.id} className="flex gap-3.5">
  <div className="flex flex-col items-center">
    <span className={cn("flex size-[30px] shrink-0 items-center justify-center rounded-full", TINT_CLASS[tint])}>
      <Icon className="size-3.5" aria-hidden="true" />
    </span>
    {!isLast && <span className="my-1 w-0.5 flex-1 bg-divider" aria-hidden="true" />}
  </div>
  <div className={cn("text-[13.5px] leading-relaxed", !isLast && "pb-[18px]")}>
    <p>
      {meta?.label ?? e.action}
      {e.actor_name ? <> por <strong>{e.actor_name}</strong></>
       : SYSTEM_ACTIONS.has(e.action) ? <> pelo <strong>Sistema</strong></>
       : null}
    </p>
    <p className="mt-0.5 text-xs text-ink-subtle">
      {formatRelative(date, now)} · {absoluteFmt.format(date)}
    </p>
  </div>
</li>
```

**Tintas:**

```ts
const TINT_CLASS = {
  neutral: "bg-neutral-badge text-neutral-badge-foreground",
  info:    "bg-accent text-accent-foreground",
  success: "bg-success text-success-foreground",
  warning: "bg-warning text-warning-foreground",
  danger:  "bg-danger text-danger-foreground",
};
```

**Catálogo completo de eventos** (rótulo · ícone lucide · tinta):

| `action` | Rótulo exibido | Ícone | Tinta |
|---|---|---|---|
| `person.created` | Pessoa cadastrada | `UserRound` | neutral |
| `person.updated` | Cadastro atualizado | `Pencil` | info |
| `property.created` | Imóvel cadastrado | `Home` | neutral |
| `property.updated` | Imóvel atualizado | `Pencil` | info |
| `contract.draft_saved` | Rascunho de contrato salvo | `Save` | neutral |
| `contract.activated` | Contrato ativado | `Check` | success |
| `document.uploaded` | Documento anexado | `FileText` | info |
| `payment.recorded` | Pagamento registrado | `Banknote` | success |
| `charge.generated` | Cobrança gerada | `CalendarPlus` | neutral |
| `charge.opened` | Cobrança aberta | `CalendarClock` | info |
| `charge.overdue` | Cobrança vencida | `TriangleAlert` | danger |
| `charge.waived` | Cobrança isentada | `HandCoins` | neutral |
| `charge.cancelled` | Cobrança cancelada | `X` | neutral |
| `charge.edited` | Cobrança editada | `Pencil` | info |
| `charge.negotiation_started` | Negociação iniciada | `Handshake` | info |
| `charge.negotiation_ended` | Negociação encerrada | `Handshake` | neutral |
| `charge.emails_paused` | Emails pausados | `CirclePause` | warning |
| `charge.emails_resumed` | Emails retomados | `CirclePlay` | info |
| `charge.settled_by_agreement` | Quitada por acordo | `Handshake` | success |
| `contact.registered` | Contato registrado | `Phone` | info |
| `contract.terminated` | Contrato encerrado | `FileX` | danger |
| `contract.emails_paused` | Emails do contrato pausados | `CirclePause` | warning |
| `contract.emails_resumed` | Emails do contrato retomados | `CirclePlay` | info |
| `contract.mp_paused` | Pagamento direto ativado | `CirclePause` | warning |
| `contract.mp_resumed` | Pagamento direto desativado | `CirclePlay` | info |
| `email.sent` | Email enviado | `Mail` | info |
| `agency.email_settings_updated` | Configurações de email atualizadas | `Pencil` | info |
| `agency.mp_enabled` | Mercado Pago ativado | `CirclePlay` | info |
| `agency.mp_disabled` | Mercado Pago desativado | `CirclePause` | warning |
| `mp.payment_divergent` | Pagamento MP em cobrança já baixada | `TriangleAlert` | warning |
| `charge.mp_link_issued` | Link de pagamento emitido | `Link2` | info |
| `charge.mp_link_updated` | Link de pagamento atualizado | `Link2` | info |
| `charge.mp_link_expired` | Link de pagamento expirado | `Link2Off` | neutral |

Fallback para ação desconhecida: ícone `Circle`, tinta neutral, rótulo = a string crua.

**Autoria.** Sete ações são gravadas sem ator e mostram **"pelo Sistema"**:

```ts
const SYSTEM_ACTIONS = new Set([
  "charge.generated", "charge.opened", "charge.overdue", "email.sent",
  "charge.mp_link_issued", "charge.mp_link_updated", "charge.mp_link_expired",
]);
```

**Carimbo de tempo:** duas informações na mesma linha — relativo + absoluto.
`formatRelative` usa `Intl.RelativeTimeFormat("pt-BR", { numeric: "auto" })` e
escalona segundo → minuto → hora → dia → semana → mês → ano. O absoluto é
`dd/MM/yyyy HH:mm`. Exemplo real na tela: `há 3 dias · 11/08/2026 09:42`.

---

## `empty-state.tsx` — `EmptyState`

**Props:** `{ title: string; description: string; actionLabel?: string; actionHref?: string }`

```tsx
<div className="flex flex-col items-center justify-center rounded-xl border border-dashed p-12 text-center">
  <h2 className="text-lg font-semibold">{title}</h2>
  <p className="mt-1 max-w-md text-sm text-muted-foreground">{description}</p>
  {actionLabel && actionHref && (
    <Button asChild className="mt-4"><Link href={actionHref}>{actionLabel}</Link></Button>
  )}
</div>
```

Repare: a borda tracejada **não tem cor declarada**, então herda o
`border-border` (`#e5e9f2`) da camada base. O botão só aparece se **os dois**
props opcionais forem passados.

Este é o empty state genérico. `/inadimplencia` tem um empty state próprio, verde e
comemorativo, descrito em [telas.md](telas.md#15--inadimplencia--a-fila).

---

# 2 · Dashboard (`components/dashboard/`)

## `kpi-cards.tsx` — `KpiCards`

Os quatro números do topo.

**Props:**

```ts
{
  previsto: number;
  recebido: number;
  vencido: number;
  inadimplenciaPercent: number | null;   // null quando previsto = 0
  previstoBarPercent: number;
  recebidoDeltaPercent: number | null;   // null quando não há mês anterior
  previousMonthLabel: string;            // "jul/26"
}
```

**Grid:** `grid grid-cols-1 gap-4 sm:grid-cols-2 lg:grid-cols-4`
**Card:** `rounded-card border border-border-card bg-card p-4 shadow-card`

| # | Título | Ícone (lucide) | Círculo 40px | Sublinha(s) |
|---|---|---|---|---|
| 1 | Previsto no mês | `CalendarDays` | `bg-accent text-primary` | barra de progresso + `"{x}% recebido no mês"` |
| 2 | Recebido | `DollarSign` | `bg-success text-success-foreground` | `"{x}% do previsto"` + delta |
| 3 | Vencido | `TriangleAlert` | `bg-danger text-danger-foreground` | `"{x}% do previsto"` + `"todas as competências"` |
| 4 | Inadimplência | `Percent` | `bg-warning text-warning-foreground` | `"do previsto no mês"` + `"todas as competências"` |

**Barra do card 1** (só ele tem):

```tsx
<div className="mt-3 h-1 overflow-hidden rounded-pill bg-secondary">
  <div className="h-1 rounded-pill bg-primary" style={{ width: `${barClamped}%` }} />
</div>
```

`barClamped = Math.min(100, Math.max(0, previstoBarPercent))` — a barra nunca
estoura, mas o **texto** pode passar de 100% (se recebeu mais que o previsto).

**Delta** (só no card Recebido, e só quando há base de comparação):

```tsx
<p className="mt-2 flex items-center gap-1.5 text-xs">
  <span className={`flex items-center gap-0.5 font-bold ${positive ? "text-success-foreground" : "text-danger-foreground"}`}>
    <Icon className="size-3" aria-hidden="true" />   {/* ArrowUp ou ArrowDown */}
    {formatPercent1(Math.abs(percent))}
  </span>
  <span className="text-ink-subtle">vs. {previousMonthLabel}</span>
</p>
```

**Vencido e Inadimplência nunca têm delta.** Decisão registrada no código: não há
snapshot histórico desses números, então um delta seria inventado.

**Estado sem dados:** com `previsto = 0`, o card 1 escreve
`"Sem cobranças previstas no mês"` e os cards 2/3/4 escrevem `"—"`.

---

## `attention-card.tsx` — `AttentionCard`

"Exige atenção hoje" — as 5 cobranças com maior atraso.

**Props:** `{ items: DashboardData["attention"]; totalCount: number }`

**Cabeçalho:** título `text-[15px] font-bold text-ink-title` + badge de contagem
`rounded-pill bg-danger px-2 py-px text-[11.5px] font-bold text-danger-foreground`
(só se `totalCount > 0`).

**Linha:**

```tsx
<div className="flex items-center gap-2.5 border-t border-divider-thin py-[11px] first:border-t-0">
  <span className={`flex size-8 shrink-0 items-center justify-center rounded-full text-xs font-bold ${palette.bg} ${palette.fg}`}>
    {initials(item.tenantName)}
  </span>
  <div className="min-w-0 flex-1">
    <p className="truncate text-sm font-semibold text-foreground">{item.tenantName}</p>
    <p className="truncate text-xs text-ink-subtle">{item.propertyLine}</p>
  </div>
  <div className="shrink-0 text-right">
    <p className="text-[13.5px] font-bold text-ink-title">{formatCurrencyBRL(item.saldo)}</p>
    <p className="mt-0.5 text-xs font-bold text-danger-foreground">
      +{item.diasAtraso} dia{item.diasAtraso === 1 ? "" : "s"}
    </p>
  </div>
  <Link href={`/cobrancas?competencia=${…}&cobrancaSelecionada=${item.chargeId}`}
        className="shrink-0 rounded-btn-sm bg-brand-soft px-2.5 py-1.5 text-[11.5px] font-semibold text-primary hover:opacity-90">
    Ver cobrança
  </Link>
</div>
```

O botão **"Ver cobrança" é um deep link**: leva a `/cobrancas` já na competência
certa e com o drawer daquela cobrança aberto. Excelente material de animação.

**Rodapé:** `Ver todas ({totalCount})` em
`mt-auto pt-3 text-center text-[13.5px] font-bold text-danger-foreground hover:underline`,
apontando para `/inadimplencia`. Só aparece com `totalCount > 0`.

**Estado vazio:** `<p className="py-4 text-sm text-ink-subtle">Nenhuma cobrança vencida no momento.</p>`

---

## `upcoming-card.tsx` — `UpcomingCard`

"Próximos vencimentos" — cobranças `open` nos próximos 7 dias.

**Props:** `{ items: DashboardData["upcoming"] }`

Cabeçalho com um chip fixo à direita: `7 dias` em
`rounded-btn-sm border border-border px-2.5 py-1 text-xs font-semibold text-ink-secondary`.

**Caixinha de data** (o detalhe visual mais característico do card):

```tsx
<div className="flex size-11 shrink-0 flex-col items-center justify-center rounded-subcard bg-surface-sub">
  <span className="text-[15px] leading-tight font-bold text-ink-title">{due.getDate()}</span>
  <span className="text-[9.5px] font-bold tracking-[0.08em] text-ink-subtle">{monthAbbrev(due)}</span>
</div>
```

`monthAbbrev` = `Intl.DateTimeFormat("pt-BR", { month: "short" })` sem o ponto,
em **caixa alta**: `AGO`, `SET`.

Lista com `max-h-[300px] overflow-y-auto`. Segunda linha da pessoa é o
**código do imóvel**, não o endereço.

**Rodapé:** `Ver todos ({items.length})` → `/cobrancas`, em `text-primary`. Note que
o número é o dos itens exibidos (≤ 7 dias), não um total geral.

**Estado vazio:** `Nenhum vencimento nos próximos 7 dias.`

---

## `health-card.tsx` — `HealthCard`

"Saúde da operação" — donut CSS + legenda + rodapé de contratos encerrando.

**Props:** `{ health: { rented; available; maintenance; other; total; endingContracts } }`

**O donut não é SVG nem canvas.** É um `conic-gradient` num `<div>` redondo com um
furo branco por cima:

```tsx
<div className="relative size-[100px] shrink-0 rounded-full" style={{ backgroundImage: gradient }}>
  <div className="absolute inset-[15px] flex flex-col items-center justify-center rounded-full bg-card">
    <span className="text-lg leading-tight font-extrabold text-ink-title">{total}</span>
    <span className="text-[10px] text-ink-subtle">imóve{total === 1 ? "l" : "is"}</span>
  </div>
</div>
```

100px de diâmetro, furo de 70px (inset 15px de cada lado). Segmentos:

| Segmento | Cor |
|---|---|
| Alugados | `var(--color-chart-1)` `#12a150` |
| Disponíveis | `var(--color-chart-2)` `#f0a020` |
| Manutenção | `var(--color-chart-3)` `#d9dee8` |
| Outros | `var(--color-chart-4)` `#7c5cf0` — **só entra na lista se > 0** |

Com `total === 0`, o gradiente vira um anel cinza inteiro
(`conic-gradient(var(--color-secondary) 0% 100%)`) e a legenda vira
`"Nenhum imóvel na carteira ativa."`

**Legenda:** bolinha `size-2` + rótulo + valor + percentual alinhado à direita numa
coluna de `w-11`.

**Rodapé:** separado por `border-t border-divider pt-3` —
`"Contratos encerrando nos próximos 60 dias"`, o número em
`text-[15px] font-bold`, e um botão-link "Ver contratos" em
`rounded-btn-sm border border-border px-3 py-1.5 text-xs font-semibold text-primary hover:bg-secondary`.

---

## `month-selector.tsx` — `MonthSelector`

**Props:** `{ month: string }` no formato `"YYYY-MM"`.

```
◄  📅 agosto/2026  ►
```

Container: `flex items-center gap-1 rounded-subcard border border-border bg-card px-1.5 py-1`
Setas: `flex size-7 items-center justify-center rounded-btn-sm text-ink-secondary hover:bg-secondary`
Rótulo: `flex items-center gap-1.5 px-1.5 text-[13px] font-semibold text-foreground`, com
`CalendarDays size-3.5 text-ink-subtle` à esquerda.

Navega por `router.push("/?mes=YYYY-MM")`. `aria-label` "Mês anterior" / "Próximo mês".

**Importante para a narrativa:** o mês selecionado governa **Previsto** e
**Recebido**. **Vencido** e **Inadimplência** são "foto atual" e não mudam — os
próprios cards escrevem `"todas as competências"` embaixo para dizer isso.

---

## `refresh-button.tsx` — `RefreshButton`

Botão quadrado de 36px ao lado do seletor de mês.

```tsx
<button
  type="button"
  onClick={() => startTransition(() => router.refresh())}
  disabled={pending}
  aria-label="Atualizar indicadores"
  title="Atualizar"
  className="flex size-9 shrink-0 items-center justify-center rounded-subcard border border-border bg-card text-ink-secondary transition-colors hover:bg-secondary disabled:opacity-60"
>
  <RotateCw className={cn("size-4", pending && "animate-spin")} aria-hidden="true" />
</button>
```

| Estado | Visual |
|---|---|
| Normal | ícone `RotateCw` estático |
| Hover | fundo `bg-secondary` |
| Carregando | ícone girando (`animate-spin`) + `opacity-60` + desabilitado |

É o **único** indicador de carregamento explícito do dashboard.

---

# 3 · Cobranças (`components/charges/`)

## `charges-table.tsx` — `ChargesTable`

O componente mais denso do produto. Filtros + tabela + paginação + drawer + dialog.

**Props:**

```ts
{
  charges: ChargeRow[];
  receiptsByCharge: Record<string, ReceiptPayment[]>;
  competencia: string;   // "YYYY-MM"
  status: string;        // "" = todos
  q: string;
  page: number;
  pageSize: number;      // 20
  totalCount: number;
  todayISO: string;
  selectedChargeId: string | null;
}
```

```ts
export type ChargeRow = {
  id, contractId, contractStatus, competence, dueDate,
  amount, paidTotal, status, emailsPaused,
  propertyCode, addressLine, tenantName,
  mpLinkUrl: string | null,   // checkout que cobra EXATAMENTE o saldo atual, ou null
  paidViaMp: boolean,         // existe baixa com origin = 'mercadopago'
};
```

**Barra de filtros** (`mt-[18px] flex items-center justify-between gap-4`):

1. **Competência** — um `<input type="month">` dentro de um `<label>` estilizado:
   `flex items-center gap-2 rounded-lg border border-brand-border-tint bg-brand-hint px-3.5 py-2 text-[13px] font-semibold text-primary`,
   com ícone `Calendar size-3.5`. O input em si é
   `bg-transparent outline-none [color-scheme:light]`.
2. **Status** — botão que abre um `Popover` `w-56 rounded-xl p-1.5 shadow-dropdown`.
   Inativo: `border-border text-ink-secondary hover:bg-muted`.
   Ativo: `border-brand-border-tint bg-brand-hint font-semibold text-primary`.
   Nove opções: Todos os status, Agendada, Aberta, Vencida, Em negociação,
   Parcialmente paga, Paga, Isentada, Cancelada.
3. **Busca** — `<Input type="search">` de `w-[320px]`, placeholder
   `"Buscar por inquilino ou imóvel..."`, com lupa absoluta à esquerda.
   **Debounce de 350 ms** antes de escrever na URL.

**Colunas:** Competência · Contrato · Vencimento · Valor (dir.) · Status · Ações (dir.)
`GRID_COLS = "120px 1.65fr 110px 120px 155px 215px"`

**Coluna Vencimento** muda de cor e de texto conforme o estado:

```ts
function dueDateCell(status, dueDate, todayISO) {
  if (status === "cancelled")                    return { text: "—",              className: "text-ink-disabled" };
  if (status === "overdue")                      return { text: formatDate(dueDate), className: "font-semibold text-danger-foreground" };
  if (status === "open" && dueDate === todayISO) return { text: "hoje",           className: "font-bold text-warning-foreground" };
  return { text: formatDate(dueDate), className: "text-ink-secondary" };
}
```

A palavra **"hoje"** em âmbar no dia do vencimento é um detalhe pequeno e muito
demonstrável.

**Coluna Contrato:** linha 1 = `{propertyCode} · {addressLine}` em
`text-[13.5px] font-semibold text-foreground`; linha 2 = nome do inquilino em
`text-xs text-ink-subtle`, com sufixo `" · contrato suspenso"` ou
`" · contrato cancelado"` quando for o caso. Cobrança **cancelada** rasura a linha 1
(`text-ink-subtle line-through`).

**Coluna Valor:** valor em `text-[13.5px] font-bold`. Se `partially_paid` e
`paidTotal > 0`, ganha a sublinha `"{X} recebidos"` em `text-[11.5px] text-ink-subtle`.
Isentada fica `text-muted-foreground`; cancelada, `text-ink-disabled`.

**Coluna Status:** o `StatusBadge` mais, se aplicável, uma sublinha de Mercado Pago
em `mt-1 text-xs text-ink-subtle`:

- `"Pagamento via Mercado Pago"` se `paidViaMp` — afirma que **existe** uma baixa
  vinda do MP, não que a cobrança está quitada;
- `"Link de pagamento"` se `mpLinkUrl` — há um checkout cobrando o saldo atual;
- nada, caso contrário. Agência sem MP não vê nada, e isso é o normal.

**Coluna Ações:** até três coisas —
`Registrar pagamento` (`rounded-btn-sm bg-brand-soft px-3 py-1.5 text-xs font-bold text-primary hover:opacity-90`)
quando o status é pagável;
`Ver recibo` (`text-xs font-semibold text-primary hover:underline`) quando há
`payment_records`; e o menu `⋯`. Sem nenhum dos dois primeiros, aparece um
`—` em `text-ink-disabled`.

**Linha selecionada** (drawer aberto):

```tsx
selected && "bg-row-selected shadow-[inset_3px_0_0_var(--primary)]"
```

**Estado vazio de filtro:**
`<p className="px-[22px] py-10 text-center text-sm text-muted-foreground">Nenhuma cobrança encontrada para esses filtros.</p>`

**Paginação** (`px-[22px] py-[13px]`), 20 por página:
`"Mostrando 1–20 de 38 cobranças"` à esquerda; à direita setas + números.
Número ativo: `border-primary bg-primary text-primary-foreground`.
Seta desabilitada vira `<span aria-disabled="true">` em `text-ink-disabled`.

---

## `charge-actions-menu.tsx` — `ChargeActionsMenu`

O menu `⋯` de cada linha e os dois dialogs que ele abre.

**Props:** `{ charge: { id, contractId, status, emailsPaused, amount, paidTotal, dueDate, mpLinkUrl } }`

**Trigger:** `flex size-7 items-center justify-center rounded-md border border-border bg-card text-ink-secondary`
com `Ellipsis size-3.5`. **Desabilitado** (`cursor-not-allowed text-ink-disabled`)
quando nenhum item se aplica ao status.

**Menu:** `align="end" className="w-52 rounded-xl p-1.5 shadow-dropdown"`

Itens, na ordem exata em que aparecem:

| Item | Aparece quando | O que faz |
|---|---|---|
| **Copiar link de pagamento** | `mpLinkUrl !== null` | Copia para a área de transferência. Único item que não muda nada — por isso vem primeiro. |
| **Pausar / Retomar emails** | status ∈ scheduled, open, overdue, partially_paid, under_negotiation | Submete direto, sem dialog. Rótulo alterna. |
| **Marcar em negociação** | status = `overdue` | Direto, sem dialog. |
| **Reverter negociação** | status = `under_negotiation` | Direto, sem dialog. |
| **Editar valor/vencimento** | status ∈ scheduled, open, overdue, under_negotiation | Abre dialog com **Motivo obrigatório**. |
| **Quitar por acordo** | status ∈ overdue, under_negotiation, partially_paid | Abre `SettleDialog`. |
| **Isentar** | status ∈ open, overdue, partially_paid, under_negotiation | `variant="destructive"` (vermelho). Abre dialog com **Motivo obrigatório**. |

**Toast do menu** — dois tons, e a diferença é deliberada:

```ts
type Toast = { tone: "success" | "error"; message: string };
```

- `success` (só "Link de pagamento copiado.") — ícone `Check`, borda
  `border-border-card`, **some sozinho em 4500 ms**;
- `error` — ícone `Info`, borda `border-danger`, `role="alert"`, **fica até o
  usuário fechar no ✕**. O comentário do código: *"uma mensagem de falha que evapora
  é uma falha que ninguém leu."*

Posição: `fixed top-6 right-6 z-50`, dentro de uma casca `role="status"
aria-live="polite"` que fica **sempre montada**.

### Dialog "Isentar cobrança"

`w-[440px] gap-0 rounded-card p-0 shadow-dialog` + `<form className="p-[26px]">`

- Título `Isentar cobrança` (`text-[17px] font-bold text-ink-title`)
- Descrição: *"Isso marca a cobrança como Isentada — não é possível reverter por aqui."*
- Campo **Motivo \*** (`Textarea rows={3}`, obrigatório)
- Botões: `Cancelar` (outline) · `Isentar cobrança` (`variant="destructive"`),
  que vira `Isentando…` enquanto pendente.

### Dialog "Editar cobrança"

Mesmo container. Dois campos lado a lado (`grid grid-cols-2 gap-3.5`): **Valor**
(`type="number" min={0.01} step="0.01"`) e **Vencimento** (`type="date"`).

**Prévia de impacto:** se a nova data mudaria o status (e a cobrança não está em
negociação), aparece:

> Isso pode mudar o status da cobrança (recalculado pelo novo vencimento).

em `mt-2.5 text-[12.5px] text-ink-subtle`. Depois vem **Motivo \*** obrigatório, e o
botão `Salvar alterações` / `Salvando…` com `shadow-btn-primary`.

Descrição do dialog: *"Altera valor e/ou vencimento — a alteração fica registrada na auditoria."*

---

## `payment-drawer.tsx` — `PaymentDrawer`

O drawer lateral de "Registrar pagamento". Provavelmente o melhor momento do
produto para animar.

**Props:** `{ charge: PaymentDrawerCharge | null; onOpenChange: (open: boolean) => void }`

```ts
export type PaymentDrawerCharge = {
  id, contractId, competence, dueDate, amount,
  paidTotal,      // Σ payment_records
  status, propertyCode, tenantName,
};
```

**Container:** `Sheet side="right"`, `showCloseButton={false}`,
`className="w-[440px] max-w-[calc(100%-2rem)] gap-0 border-l-0 p-0 shadow-drawer sm:max-w-[440px]"`

**Layout:** `<form className="flex h-full flex-col">` com três faixas —
header fixo, corpo rolável, rodapé fixo.

```
┌──────────────────────────────────────┐
│ Registrar pagamento              ✕   │  px-6 py-[18px], border-b border-divider
├──────────────────────────────────────┤
│ ┌──────────────────────────────────┐ │
│ │ IMV-0007 · Beatriz Nunes   [Venc]│ │  painel bg-surface-sub, rounded-subcard
│ │ competência agosto/2026 ·        │ │
│ │ venceu em 05/08/2026             │ │
│ │ Valor previsto  R$ 2.450,00      │ │
│ └──────────────────────────────────┘ │
│ [Data do pagamento *] [Valor recebido *]
│ ⚠ banner âmbar de divergência (cond.) │
│ [Motivo da diferença]                │
│ Forma de pagamento: (Pix)(Transf)(Din)(Boleto)
│ [Referência]                         │
│ Comprovante: ┌ ⬆ Anexar comprovante ┐│
│ ℹ Ao confirmar, a cobrança passa a…  │
├──────────────────────────────────────┤
│              [Cancelar] [Confirmar]  │  px-6 py-4, border-t border-divider
└──────────────────────────────────────┘
```

**Painel de contexto:** `rounded-subcard border border-border-card bg-surface-sub p-[13px_15px]`.
A linha de data alterna entre `"vence em"` e `"venceu em"` conforme `dueDate < hoje`.

**Campo Valor recebido:** vem **pré-preenchido com o saldo** (`amount − paidTotal`).
Se o usuário digitar outra coisa, o input ganha
`border-[1.5px] border-warning-border bg-warning-surface font-bold` e abre o banner:

```tsx
<div className="mt-2.5 flex gap-2.5 rounded-lg border border-warning-banner-border bg-warning-banner px-3.5 py-2.5">
  <TriangleAlert className="mt-0.5 size-3.5 shrink-0 text-warning-foreground" aria-hidden="true" />
  <p className="text-[12.5px] leading-relaxed text-warning-banner-foreground">
    O valor difere do previsto ({formatCurrencyBRL(saldo)}) — informe o motivo abaixo.
  </p>
</div>
```

…e o campo **Motivo da diferença** passa a ser obrigatório (o `*` aparece no label).

**Forma de pagamento** — quatro chips, não um select:

```ts
export const METHODS = [
  { value: "pix",      label: "Pix" },
  { value: "transfer", label: "Transferência" },
  { value: "cash",     label: "Dinheiro" },
  { value: "boleto",   label: "Boleto" },
] as const;
```

```tsx
className={cn(
  "rounded-pill border px-3.5 py-1.5 text-[12.5px] font-semibold transition-colors",
  "outline-none focus-visible:ring-3 focus-visible:ring-ring/50",
  active
    ? "border-[1.5px] border-primary bg-brand-hint text-primary"
    : "border-border text-ink-secondary hover:bg-muted",
)}
```

Padrão inicial: **Pix**.

**Comprovante** — dropzone opcional:

```tsx
className={cn(
  "flex items-center justify-center gap-2 rounded-lg border-[1.5px] border-dashed border-brand-border-tint bg-surface-hint px-3.5 py-3.5 text-center transition-colors",
  uploadPending ? "pointer-events-none opacity-60" : "cursor-pointer",
  "peer-focus-visible:ring-3 peer-focus-visible:ring-ring/50",
)}
```

Rótulo: ícone `Upload size-3.5 text-primary` + `"Anexar comprovante"` (ou
`"Enviando…"` durante o upload) + `"(opcional)"` em `text-ink-placeholder`.
Depois do upload vira uma linha com o nome do arquivo e um `✕` para remover.

Limites validados **no cliente antes de subir** (e de novo no servidor):
`10 MB`, e só `application/pdf`, `image/jpeg`, `image/png`. Erros literais:
`"Selecione um arquivo."`, `"Arquivo maior que 10 MB."`, `"Use PDF, JPG ou PNG."`,
`"Falha no upload. Tente novamente."`

**Nota de consequência** — recalculada a cada tecla:

```tsx
<div className="mt-3.5 flex items-center gap-2 rounded-lg border border-info-banner-border bg-brand-hint px-3.5 py-2.5">
  <Info className="size-3.5 shrink-0 text-primary" aria-hidden="true" />
  <span className="text-[12.5px] text-ink-nav">
    Ao confirmar, a cobrança passa a <strong>Paga</strong>.
  </span>
</div>
```

Se o valor não cobre o saldo, o texto vira
*"Ao confirmar, a cobrança passa a **Parcialmente paga** — restam R$ 450,00."*

**Rodapé:** `Cancelar` (outline) · `Confirmar pagamento` com `shadow-btn-primary`,
que vira `Confirmando…` e fica desabilitado enquanto pendente **ou** enquanto um
upload está em curso.

**Toast de sucesso** (fora do drawer, sobrevive ao fechamento):
título `"Pagamento registrado"`, descrição `"A cobrança passou para Paga."` ou
`"A cobrança passou para Parcialmente paga — restam R$ X."` Some em 4500 ms.

---

## `settle-dialog.tsx` — `SettleDialog`

"Quitar por acordo" — marca como paga por um valor combinado, mesmo abaixo do saldo.

**Props:** `{ charge: { id, contractId, amount, paidTotal }; open: boolean; onOpenChange }`

Container `w-[440px] gap-0 rounded-card p-0 shadow-dialog`, form `p-[26px]`.

- Título: **Quitar por acordo**
- Descrição: *"Registra um pagamento combinado com o inquilino e marca a cobrança como paga."*
- Campos: **Valor recebido \*** + **Data \*** (grid 2 col) · Forma de pagamento
  (mesmos 4 chips, mesmo `METHODS` importado do drawer) · **Referência** ·
  **Motivo do acordo \*** (obrigatório sempre, diferente do drawer) · **Comprovante**
  (mesma dropzone).

**A nota de consequência tem três textos**, escolhidos pelo valor digitado:

| Condição | Texto |
|---|---|
| `entered < saldo` | A cobrança será marcada como **Paga** pelo valor acordado. Diferença de **R$ X** registrada como acordo. |
| `entered === saldo` | A cobrança será marcada como **Paga** pelo valor integral do saldo. |
| `entered > saldo` | Valor acima do saldo — a cobrança será marcada como **Paga** por **R$ Y** (motivo obrigatório). |

Botões: `Cancelar` · `Quitar cobrança` / `Quitando…`.

---

## `receipt-dialog.tsx` — `ReceiptDialog`

Somente leitura. Lista os `payment_records` de uma cobrança e seus comprovantes.

**Props:** `{ charge: ReceiptDialogCharge | null; payments: ReceiptPayment[]; onOpenChange }`

Container `w-[480px] gap-0 rounded-card p-0 shadow-dialog`, conteúdo `p-[26px]`.

- Título: `Recibo — {propertyCode} · {tenantName}`
- Descrição: `competência agosto/2026`
- Um cartão por pagamento: `rounded-subcard border border-border-card bg-surface-sub p-3.5`
  - linha 1: `05/08/2026 · Pix` (esq.) + `R$ 2.450,00` (dir., `font-bold`)
  - `Referência: PIX E2E-8842-AGO26` ou `Referência: —`
  - se houver divergência: `Motivo da divergência: {texto}` em `text-warning-banner-foreground`
- Anexos, um por linha, com selo quadrado de 28px:

```tsx
className={cn(
  "flex size-7 shrink-0 items-center justify-center rounded-md text-[9px] font-extrabold",
  doc.mimeType === "application/pdf" ? "bg-danger text-danger-foreground" : "bg-accent text-accent-foreground",
)}
```

→ **PDF** em vermelho suave, **IMG** em azul suave. Nome do arquivo + tamanho
(`"1,2 MB"`, vírgula decimal). Botão `Baixar`, que vira `Gerando…` enquanto busca a
URL assinada.

**Estados:** `Nenhum pagamento registrado.` (lista vazia) ·
`Nenhum comprovante anexado.` (pagamento sem anexo) ·
`Não foi possível gerar o link de download. Tente novamente.` (falha).

---

## `delinquency-table.tsx` — `DelinquencyTable`

A fila de inadimplência. **Sem busca e sem paginação** — lista única, já ordenada
por dias de atraso decrescente pelo servidor.

**Props:** `{ charges: DelinquencyRow[]; totalOpenAmount: number; totalUpdatedAmount: number; anyLateCharges: boolean }`

```ts
export type DelinquencyRow = {
  id, contractId, tenantName,
  tenantEmail: string | null,
  propertyCode, addressLine, addressSubline, competence,
  amountOpen: number,       // saldo
  updatedAmount: number,    // saldo + multa + juros
  hasLateCharges: boolean,
  daysLate: number,
  lateIntensity: LateIntensity,
  emailsCount: number,
};
```

**Colunas:** Inquilino · Imóvel · Competência · Valor (dir.) · Atraso · Emails enviados · (ação)
`GRID_COLS = "1.25fr 1.55fr 120px 115px 120px 150px 160px"`

- **Inquilino:** avatar 34px + nome. **Se `tenantEmail` é null**, entra um
  `<Badge variant="warning">Sem email</Badge>` abaixo do nome — o sinal visual de que
  a cobrança automática não alcança essa pessoa.
- **Valor:** mostra o **valor atualizado** em destaque e, só quando há multa/juros,
  a sublinha `original R$ 2.450,00` em `text-[11.5px] text-ink-subtle`.
- **Atraso:** o `LateBadge`, com a escalada de cor.
- **Emails enviados:** o número em `<strong className="font-bold text-foreground">`,
  ou `—` quando zero.
- **Ação:** `Registrar contato` em
  `rounded-btn-sm bg-brand-soft px-3 py-1.5 text-xs font-bold text-primary hover:opacity-90`.

**Rodapé da tabela:** `flex items-center justify-between border-t border-divider bg-surface-sub px-[22px] py-[13px]`
com a frase *"Registre cada contato feito com o inquilino sobre a cobrança em
atraso."* à esquerda e o total à direita.

**Regra de honestidade embutida:** o sufixo `· atualizado com multa e juros: R$ X` só
aparece quando **alguma** linha tem multa ou juros > 0. Como o schema traz
`late_fine_percent` e `late_interest_percent` com default 0, uma agência que não
configurou nada veria dois números idênticos sob um rótulo que mentiria.

---

## `contact-dialog.tsx` — `ContactDialog`

**Props:** `{ charge: ContactDialogCharge | null; onOpenChange }`
com `ContactDialogCharge = { id, contractId, tenantName, competence, daysLate }`

Container `w-[480px] gap-0 rounded-card p-0 shadow-dialog`, form `p-[26px]`.

- Título: **Registrar contato**
- Descrição, que já carrega o contexto e a urgência:
  `{tenantName} · agosto/2026 · ` + `<span className="font-bold text-danger-foreground">12 dias de atraso</span>`
- **Canal** — quatro chips (mesmo estilo dos de forma de pagamento):
  `Telefone` · `WhatsApp` · `Email` · `Presencial`. Padrão: **Telefone**.
- **Data do contato \*** (`type="date"`, `w-[180px]`, default hoje)
- **Nota \*** (`Textarea rows={3}`, obrigatória)
- Nota informativa azul: *"O contato fica salvo na cobrança e aparece na timeline do contrato."*
- Botões: `Cancelar` · `Salvar contato` / `Salvando…`

**Toast:** `"Contato registrado"` / `"O contato foi salvo com sucesso."`

---

# 4 · Contratos (`components/contracts/`)

## `contracts-table.tsx` — `ContractsTable`

**Props:** `{ rows: ContractRow[]; aba: "ativos"|"rascunhos"|"todos"; q: string; counts: Record<Tab, number>; todayISO: string }`

**Barra superior:** busca à esquerda (`w-[340px]`, placeholder
`"Buscar por imóvel ou locatário..."`, debounce 350 ms) e três chips-aba à direita:
**Ativos**, **Rascunhos**, **Todos**, cada um com um contador.

```tsx
// TabChip
className={cn(
  "flex items-center gap-2 rounded-lg border px-3.5 py-2 text-[13px] font-medium transition-colors",
  "outline-none focus-visible:ring-3 focus-visible:ring-ring/50",
  active
    ? "border-brand-border-tint bg-brand-hint font-semibold text-primary"
    : "border-border text-ink-secondary hover:bg-muted",
)}
// contador
className={cn(
  "rounded-pill px-2 py-0.5 text-xs font-bold",
  active ? "bg-accent text-accent-foreground" : "bg-neutral-badge text-neutral-badge-foreground",
)}
```

**Colunas:** Imóvel · Locatário · Vigência · Aluguel (dir.) · Status · (ação)
`GRID_COLS = "1.7fr 1.2fr 1.25fr 120px 130px 120px"`

**Linha de rascunho é visualmente distinta:** ganha `bg-surface-hint`, o badge
`Rascunho` (tracejado), e no lugar do chevron um botão **Continuar**
(`rounded-lg bg-brand-soft px-3 py-1.5 text-xs font-bold text-primary hover:underline`)
que leva a `/contratos/novo?draft={id}`.

Na segunda linha da coluna Imóvel, contrato real mostra o **endereço**; rascunho
mostra `atualizado há 2 dias` (o mesmo `formatRelative` da timeline).

**Vigência:** `"01/03/2026 — 28/02/2027"`, ou `"01/03/2026 — sem prazo"`. Rascunho
sem data de fim mostra `—`.

**Linha inteira clicável** (só contratos reais): um `<Link className="absolute inset-0">`
sobreposto, com a célula de ação em `relative z-10` para o botão continuar
funcionar por cima.

**Estado vazio de filtro:** `Nenhum contrato encontrado para esses filtros.`

---

## `contract-wizard.tsx` — `ContractWizard`

Quatro etapas, um card, um stepper. **O componente mais rico do produto para animar.**

**Props:** `{ properties: WizardPropertyOption[]; people: WizardPersonOption[]; initialData: ContractWizardData; initialDraftId?: string }`

**Stepper** (`mb-6 flex items-center justify-center`):

```ts
const STEPS = [
  { n: 1, label: "Imóvel e partes" },
  { n: 2, label: "Vigência e valores" },
  { n: 3, label: "Revisão" },
  { n: 4, label: "Ativação" },
] as const;
```

Bolinha de 32px, três estados:

| Estado | Classes | Conteúdo |
|---|---|---|
| `active` | `border-primary bg-primary text-primary-foreground` | o número |
| `done` | `border-transparent bg-success text-success-foreground` | ícone `Check size-4` |
| `future` | `border-ink-disabled text-ink-disabled` | o número |

Conector entre bolinhas: `mx-1 mb-5 h-0.5 w-[52px]`, `bg-primary` se já passou,
`bg-divider` se não. O rótulo abaixo fica `text-ink-disabled` no futuro e
`font-medium text-ink-secondary` no resto.

**Card:** `mx-auto rounded-card border border-border-card bg-card shadow-card`, com
largura **`max-w-[920px]` nas etapas 1–3 e `max-w-[760px]` na etapa 4** — o card
encolhe na confirmação.

**Rodapé fixo do card** (`border-t border-divider px-7 py-4`):
`Salvar rascunho` (outline, com ícone `Save`) à esquerda; à direita
`Cancelar` (ghost) · `Voltar` (ghost, desabilitado na etapa 1) ·
`Continuar` / `Ir para ativação` (etapa 3) / `Ativar contrato` (etapa 4, com
ícone `Check` e **`shadow-cta`** — a sombra mais forte do sistema, usada só aqui).

### Etapa 1 — Imóvel e partes

**Combobox de imóvel** (`Popover` + `Command`), largura do trigger:

```tsx
className={cn(
  "flex w-full items-center gap-2.5 rounded-lg border border-input bg-background px-3.5 py-2.5 text-left text-sm text-foreground",
  "outline-none focus-visible:border-[1.5px] focus-visible:border-primary focus-visible:ring-3 focus-visible:ring-ring/50",
  open && "border-[1.5px] border-primary ring-3 ring-ring/50",
)}
```

Placeholder: `"Buscar imóvel por código ou endereço..."`. Ícones `Search` e
`ChevronsUpDown` em `text-ink-placeholder`.

**Regra visível:** só imóveis com status `available` são selecionáveis. Os demais
aparecem na lista **desabilitados e com o `StatusBadge` do seu status ao lado**, e
imóvel alugado ganha ainda a linha `Contrato ativo até 28/02/2027`. Sob o campo,
sempre: `"Apenas imóveis disponíveis podem ser selecionados."`

Ao escolher um imóvel, o **locador é preenchido automaticamente** com o proprietário
cadastrado.

**Campo de pessoa** — dois modos. Vazio, é um combobox igual ao de imóvel
(`"Buscar locador por nome ou documento..."`). Preenchido, vira um cartão:

```tsx
<div className="flex items-center gap-2.5 rounded-lg border border-border bg-surface-sub px-3.5 py-2.5">
  <span className={cn("flex size-8 … font-bold", palette.bg, palette.fg,
                      selected.kind === "pf" ? "rounded-full" : "rounded-lg")}>
    {initials(selected.name)}
  </span>
  <span className="min-w-0 flex-1">
    <span className="block truncate text-[13.5px] font-semibold text-foreground">{selected.name}</span>
    <span className="block text-xs text-ink-subtle">CPF 123.456.789-09</span>
  </span>
  {/* locatário: botão ✕ "Remover"; locador: link "Alterar" */}
</div>
```

Assimetria real: o **locatário** tem botão de remover (`✕`); o **locador** tem link
"Alterar" (porque veio do imóvel).

No rodapé de cada lista de pessoas, sempre:

```tsx
<button className="flex w-full items-center gap-2.5 rounded-lg px-2.5 py-2 text-left text-[13px] font-bold text-primary hover:bg-brand-hint">
  <span className="flex size-[22px] items-center justify-center rounded-full bg-accent"><Plus className="size-3.5" /></span>
  Cadastrar nova pessoa
  <span className="ml-auto text-xs font-medium text-ink-subtle">abre cadastro rápido</span>
</button>
```

**Validação da etapa 1** (na hora de clicar em Continuar):
`"Selecione o imóvel."` · `"Selecione o locador."` · `"Selecione o locatário."`

### Etapa 2 — Vigência e valores

Três blocos separados por `border-t border-divider pt-5`:

**Vigência** — Data de início \* · Data de fim (com um `Switch` **"Sem prazo
definido"** no canto do label; ligado, o campo vira uma caixa cinza com `—`) ·
Primeira competência \* (`type="month"`) · Dia de vencimento \* (`1–28`, com a
nota `"Entre 1 e 28, para existir em todos os meses."`).

Escolher a data de início **preenche a primeira competência** com o mês dela.

**Valores** — Valor do aluguel \* · Taxa de administração (%), com uma nota viva:
`"R$ 245,00 sobre o aluguel informado."`, recalculada a cada dígito.

**Encargos por atraso — informativos** — Multa (%) · Juros a.m. (%), com a nota:
*"Exibidos nos emails de cobrança como referência; não alteram os valores automaticamente."*

**Validação da etapa 2:** `"Informe a data de início."` · `"Informe a primeira
competência."` · `"Entre 1 e 28."` · `"Valor deve ser maior que zero."` ·
`"Fim antes do início."`

### Etapa 3 — Revisão

Quatro `ReviewCard` num `grid grid-cols-2 gap-4`
(`rounded-subcard border border-border-card bg-surface-sub px-4 py-3.5`), cada um com
o título em `text-xs font-bold tracking-[0.04em] text-ink-subtle uppercase` e um link
**Editar** que volta para a etapa certa: **IMÓVEL** e **PARTES** → etapa 1,
**VIGÊNCIA** e **VALORES** → etapa 2.

Abaixo, o título dinâmico
`"Primeiras {N} cobranças (geradas na ativação)"` e o `SchedulePreview`.

### Etapa 4 — Ativação

Centralizada. Círculo verde de 54px com `CheckCheck size-6`, título
**"Pronto para ativar"**, e o resumo de impacto num painel
`rounded-subcard border border-border-card bg-surface-sub px-5 py-4 text-left`:

```
✓  o contrato fica Ativo
🏠 o imóvel IMV-0007 passa de Disponível a Alugado
📅 12 cobranças são criadas (primeira vence 05/09/2026)
```

(ícones `Check`, `Home`, `Calendar`, todos `size-4 text-primary`)

E o aviso âmbar final:

> A ativação não pode ser desfeita. Depois de ativo, o contrato pode ser suspenso ou
> encerrado, e as cobranças podem ser ajustadas uma a uma.

**Dialog "Sair sem salvar?"** — aparece ao clicar em Cancelar **se houver alterações
não salvas**:

> Há alterações neste contrato que ainda não foram salvas como rascunho. Se sair
> agora, elas serão perdidas.

Botões: `Continuar editando` (outline) · `Sair sem salvar` (destructive).

**Toast de rascunho:** `"Rascunho salvo"` / `"Continue de onde parou em Contratos › Rascunhos."`

---

## `schedule-preview.tsx` — `SchedulePreview`

A tabela de simulação da agenda. **É a mesma função que a ativação usa para gravar**
(`generateChargeSchedule`) — a prévia é garantidamente idêntica ao resultado.

**Props:** `{ data: ContractWizardData; today: Date }`

**Colunas:** Competência · Vencimento · Valor (dir.) · Status inicial
`GRID_COLS = "1fr 1fr 1fr 1fr"`. Cabeçalho com `bg-surface-sub`.

Máximo de **12 linhas**. Status inicial de cada uma sai da regra única:
vencida `<` hoje `≤` aberta `≤` hoje+15d `<` agendada.

**Rodapé** (`mt-3 flex items-center justify-between text-[12.5px] text-ink-subtle`):
`"Depois da 12ª, novas cobranças são geradas automaticamente conforme o contrato avança."`
à esquerda, `"Total previsto: R$ 29.400,00"` em `font-bold text-ink-title` à direita.

**Estado incompleto** (faltam competência/dia/valor):

```tsx
<p className="rounded-subcard border border-dashed border-ink-disabled bg-surface-sub px-4 py-6 text-center text-sm text-muted-foreground">
  Preencha a vigência e os valores na etapa anterior para ver a simulação das cobranças.
</p>
```

---

## `terminate-dialog.tsx` — `TerminateDialog`

Apesar do nome, é **o menu `⋯` do cabeçalho do contrato** + o dialog de encerramento.

**Props:** `{ contractId, propertyCode, status, charges, defaultTerminationDate, isAdmin, emailsPaused, mpPaused }`

**Trigger:** `flex size-[38px] shrink-0 items-center justify-center rounded-lg border border-border text-ink-secondary`
com um `⋯` textual (não é ícone lucide aqui).

**Itens do menu** (`w-56 rounded-xl p-1.5 shadow-dropdown`):

| Item | Quem vê | Comportamento |
|---|---|---|
| **Pausar / Retomar emails do contrato** | qualquer papel, qualquer status | Toggle direto, sem dialog |
| **Ativar / Desativar pagamento direto ao proprietário** | qualquer papel, qualquer status | Toggle direto, com toast que explica o efeito |
| **Encerrar contrato** | **só admin**, e só se `status === "active"` | Abre o dialog |

O item de pagamento direto tem `title` diferente em cada direção:

- ligar: *"Não gera link de pagamento; os links vigentes deixam de ser oferecidos e as cobranças saem com instruções manuais."*
- desligar: *"Remove a pausa deste contrato. "* + a frase de retomada.

E a frase de retomada muda conforme o status do contrato:

```ts
const mpResumeSentence =
  status === "active"
    ? "As cobranças elegíveis voltam a receber link nas próximas rodadas automáticas, se a integração do Mercado Pago estiver ligada e configurada na agência."
    : "Como este contrato não está ativo, as rodadas automáticas não geram link para ele.";
```

**Dialog "Encerrar contrato"** (`w-[480px] rounded-card p-0 shadow-dialog`, form `p-[26px]`):

- Descrição: *"Esta ação não pode ser desfeita."*
- **Data de saída \*** (`type="date"`, default hoje)
- **Motivo \*** (`Textarea rows={3}`)
- **Painel âmbar de impacto, que reage à data escolhida:**

```tsx
<div className="mt-3.5 rounded-lg border border-warning-banner-border bg-warning-banner px-3.5 py-3">
  {/* TriangleAlert size-3.5 text-warning-foreground */}
  <p>Estas <strong>3</strong> cobranças serão canceladas:</p>
  <ul className="mt-1.5 flex flex-col gap-0.5">
    <li>outubro/2026 · R$ 2.450,00</li>
    …
  </ul>
  <p className="mt-1.5">
    O imóvel <strong>IMV-0007</strong> passa a <strong>Indisponível</strong> e NÃO volta a ser
    anunciado automaticamente.
  </p>
</div>
```

Sem cobranças futuras, o texto vira `"Nenhuma cobrança futura será cancelada."`
O recorte é o mesmo da operação real: cobranças `scheduled`/`open` com competência
posterior ao **mês** da data de saída.

- Botões: `Cancelar` · `Encerrar contrato` (destructive) / `Encerrando…`
- **Toast:** `"Contrato encerrado"` + `"3 cobranças futuras foram canceladas."`
  (com plural correto) ou `"Nenhuma cobrança futura foi cancelada."`

---

## `charge-quick-actions.tsx` — `RegisterPaymentTrigger`, `ViewReceiptTrigger`

Dois gatilhos finos que embrulham `PaymentDrawer` e `ReceiptDialog` para uso no
detalhe do contrato, onde cada botão é autossuficiente (a tabela de cobranças, ao
contrário, compartilha um único drawer via estado de URL).

**Props:** `{ charge, className, children }` (+ `payments` no segundo).
Sem visual próprio — a aparência vem do `className` de quem usa.

---

## `document-upload.tsx` — `DocumentUpload`

Dropzone + lista de documentos do contrato.

**Props:** `{ contractId: string; documents: ContractDocument[] }`

**Dropzone** (aceita **arrastar e soltar**, além do clique):

```tsx
className={cn(
  "flex flex-col items-center gap-1 rounded-subcard border-[1.5px] border-dashed border-brand-border-tint bg-surface-hint px-5 py-5 text-center transition-colors",
  pending ? "pointer-events-none opacity-60" : "cursor-pointer",
  "peer-focus-visible:ring-3 peer-focus-visible:ring-ring/50",
)}
```

Conteúdo: `Upload size-[22px] text-primary`, depois
`Arraste arquivos aqui ou **clique para enviar**` (a segunda metade em `text-primary`)
e a linha `PDF, JPG ou PNG · até 10 MB por arquivo`. Com arquivo escolhido, o nome
substitui a frase.

Botão `Enviar documento` (`variant="outline" size="sm"`), que vira `Enviando…`.

**Lista:** selo quadrado de 36px (`size-9 rounded-lg`), **PDF** vermelho suave / **IMG**
azul suave; nome do arquivo; segunda linha
`"1,2 MB · enviado em 12/08/2026 por Marina Prado"`; botão `Baixar` → `Gerando…`.

**Estado vazio:** `Nenhum documento anexado ainda.`

---

# 5 · Pessoas (`components/people/`)

## `people-table.tsx` — `PeopleTable`

**Props:** `{ people: PersonRow[]; q: string; papel: string; totalCount, tenantCount, ownerCount }`

**Filtros:** busca `w-[340px]` (`"Buscar por nome, CPF ou CNPJ..."`, debounce 350 ms)
+ três chips com contador: **Todos** · **Inquilinos** · **Proprietários**.

**Colunas:** Nome · Papéis · CPF/CNPJ · Contato · Contratos ativos (centro) · (chevron)
`GRID_COLS = "1.45fr 200px 170px 1.5fr 120px 30px"`

- **Nome:** avatar de 34px — **círculo se PF, quadrado arredondado se PJ** — com
  segunda linha `"Pessoa física"` / `"Pessoa jurídica"` em `text-[11.5px] text-ink-placeholder`.
- **Papéis:** um ou dois `RoleBadge` (uma pessoa pode ser as duas coisas).
- **CPF/CNPJ:** já formatado (`123.456.789-09` / `12.345.678/0001-95`) ou `—`.
- **Contato:** email (`text-[13px] text-foreground`) sobre telefone
  (`text-xs text-ink-subtle`); cada um vira `—` se ausente.
- **Contratos ativos:** número em `text-[13.5px] font-bold`, centralizado.

Linha inteira clicável via `<Link className="absolute inset-0">`.

**Estado vazio de filtro:** `Nenhuma pessoa encontrada para esses filtros.`

---

## `person-form.tsx` — `PersonForm`

**Props:** `{ person?: PersonFormPerson }` — sem `person` é criação, com `person` é edição.

Card: `mt-[18px] w-full max-w-[820px] rounded-card border border-border-card bg-card px-7 py-[26px] shadow-card`

**Seções, na ordem:**

1. **Tipo de pessoa \*** — dois cartões selecionáveis (`RadioGroup`):

```ts
const KIND_OPTIONS = [
  { value: "pf", label: "Pessoa física",   hint: "documento no formato CPF" },
  { value: "pj", label: "Pessoa jurídica", hint: "documento no formato CNPJ" },
];
```

```
selectableCard = "flex flex-1 cursor-pointer items-center gap-2.5 rounded-subcard border border-border px-4 py-3 transition-colors"
selecionado    = "has-[[data-state=checked]]:border-[1.5px] has-[[data-state=checked]]:border-primary has-[[data-state=checked]]:bg-brand-hint"
```

2. **Nome completo \*** + **CPF/CNPJ \*** (`grid grid-cols-[1.4fr_1fr] gap-4`).
   O rótulo do documento troca entre "CPF" e "CNPJ" conforme o tipo, e o valor é
   **reformatado ao vivo** enquanto se digita.
3. **Email** + **Telefone** (mesma grade).
4. **Papéis \*** — dois cartões com `Checkbox` (`size-[19px] rounded-[6px]`):
   `Inquilino` (*participa de contratos como locatário*) e
   `Proprietário` (*dono de imóveis da carteira*).
5. **Endereço** — CEP · Logradouro · Número (`grid-cols-[150px_1fr_110px]`), depois
   Complemento · Bairro · Cidade · UF (`grid-cols-[1fr_1fr_1fr_90px]`). UF é um
   `Select` com as 27 siglas.
6. **Observações** — `Textarea` `min-h-16`, placeholder
   `"Anotações internas sobre esta pessoa..."`.
7. Rodapé `border-t border-divider pt-5`: `Cancelar` (outline) · `Salvar pessoa` /
   `Salvando...` com `shadow-btn-primary`.

**O aviso de documento duplicado** é o momento mais interessante deste formulário.
Quando o CPF/CNPJ já existe, o input ganha
`border-warning-border bg-warning-surface focus-visible:border-warning-border` e
abre este banner:

```tsx
<div id="document-duplicate" role="status"
     className="mt-3 flex gap-3 rounded-subcard border border-warning-banner-border bg-warning-banner px-4 py-3.5">
  <AlertTriangle className="mt-0.5 size-[17px] shrink-0 text-warning-banner-foreground" />
  <div className="flex-1">
    <p className="text-[13.5px] leading-relaxed text-warning-banner-foreground">
      Já existe um cadastro com este CPF: <strong>{duplicate.name}</strong> —{" "}
      <Link href={`/pessoas/${duplicate.id}`} className="font-bold text-primary hover:underline">Ver cadastro</Link>
    </p>
    <p className="mt-0.5 text-[12.5px] text-warning-banner-foreground-subtle">
      Se for realmente outra pessoa, você pode salvar mesmo assim.
    </p>
  </div>
  <Button type="submit" variant="outline" size="sm"
    className="h-auto shrink-0 self-center rounded-btn-sm border-warning-banner-button-border bg-background px-3.5 py-[7px] text-[12.5px] font-bold text-warning-banner-foreground hover:bg-background">
    Salvar mesmo assim
  </Button>
</div>
```

É um aviso **não bloqueante**: mostra quem é o outro cadastro, oferece o link para
conferir, e deixa seguir. Mudar o documento **cancela** a confirmação anterior.

**Erros de campo** (mensagens literais): `"Informe o nome completo."` ·
`"CPF inválido."` / `"CNPJ inválido."` · `"Email inválido."` ·
`"Selecione ao menos um papel."` · `"Informe o logradouro."` · `"Informe o número."` ·
`"Informe a cidade."` · `"UF com 2 letras."`
Erro de formulário: `"Não foi possível salvar. Tente novamente."`

---

## `person-quick-create.tsx` — `PersonQuickCreate`

Cadastro mínimo em dialog, chamado de dentro do wizard de contrato e do formulário
de imóvel — sem sair da tela.

**Props:** `{ open, onOpenChange, defaultRole: "owner" | "tenant", onCreated }`

Container `w-[480px] gap-0 rounded-card p-0 shadow-dialog`, form `p-[26px]`.

- Título: **Cadastrar nova pessoa**
- Descrição: *"Cadastro rápido para usar já neste fluxo — complete o restante depois, no perfil."*
- Radio inline `Pessoa física` / `Pessoa jurídica` (`RadioGroupItem size-[17px]`)
- **Nome completo \*** (largura cheia)
- **CPF/CNPJ** + **Telefone** (`grid grid-cols-2 gap-3.5`), placeholder do telefone
  `"(19) 99999-9999"`
- Faixa azul informando o papel automático:

```tsx
<div className="mt-3.5 flex items-center gap-2 rounded-lg border border-brand-border-tint bg-brand-hint px-3.5 py-2.5">
  <Badge variant={roleMeta.variant}>{roleMeta.label}</Badge>
  <span className="text-xs text-muted-foreground">papel atribuído automaticamente neste fluxo</span>
</div>
```

- Botões: `Cancelar` · `Cadastrar e selecionar` / `Cadastrando...`

O nome do botão diz tudo: ao salvar, a pessoa **já vem selecionada** no campo que
abriu o dialog.

---

# 6 · Imóveis (`components/properties/`)

## `properties-table.tsx` — `PropertiesTable`

**Props:** `{ properties: PropertyRow[]; status: string; q: string; counts: Record<string, number> }`

**Filtros:** busca `w-[340px]` (`"Buscar por código ou endereço..."`) + chips.
Quatro chips primários visíveis e o resto num popover **"Mais ▾"**:

```ts
const PRIMARY_CHIPS = [
  { value: "",            label: "Todos" },
  { value: "rented",      label: "Alugados" },
  { value: "available",   label: "Disponíveis" },
  { value: "maintenance", label: "Manutenção" },
];
const MORE_CHIPS = [
  { value: "reserved",    label: "Reservados" },
  { value: "unavailable", label: "Indisponíveis" },
  { value: "draft",       label: "Rascunhos" },
  { value: "archived",    label: "Arquivados" },
];
```

Quando um item do "Mais" está ativo, **o próprio botão troca de rótulo** para o item
selecionado e assume o estilo ativo. Cada opção do popover mostra seu contador à
direita.

**Colunas:** Código · Endereço · Status · Proprietário · Contrato ativo · (chevron)
`GRID_COLS = "110px 1.9fr 140px 1.3fr 140px 30px"`

- **Endereço** em duas linhas: `Rua X, 123 · ap 51` e `Bairro — Cidade/UF`.
- **Contrato ativo:** link `Ver contrato` em `text-primary hover:underline`, ou `—`
  em `text-ink-disabled`.
- **Imóvel arquivado esmaece a linha inteira:** código, endereço e proprietário caem
  para `text-ink-subtle` / `text-ink-disabled`.

**Estado vazio de filtro:** `Nenhum imóvel encontrado para esses filtros.`

---

## `property-form.tsx` — `PropertyForm`

**Props:** `{ suggestedCode?: string; people: PropertyFormOwnerOption[]; property?: PropertyFormProperty }`

Mesmo card de 820px do formulário de pessoa.

1. **Código \*** — campo estreito (`grid-cols-[220px_1fr]`), com `className="font-bold"`.
   Na criação vem preenchido com o próximo código sugerido (padrão `IMV-0001`,
   `IMV-0002`, …) e a nota `"Sugerido automaticamente — você pode editar."`
2. **Endereço** — mesma grade do formulário de pessoa, mas **Logradouro, Número,
   Cidade e UF são obrigatórios** aqui.
3. **Proprietário \*** — combobox com avatar, documento e os `RoleBadge` da pessoa,
   mais o rodapé "Cadastrar nova pessoa".
4. **Status inicial** — duas opções apenas:

```ts
const STATUS_OPTIONS = [
  { value: "available", label: "Disponível", hint: "entra na carteira e pode receber contratos" },
  { value: "draft",     label: "Rascunho",   hint: "fica oculto até o cadastro ser concluído" },
];
```

**Trava de integridade** — se o imóvel em edição está em qualquer outro status
(Alugado, Reservado…), o `RadioGroup` **desaparece** e dá lugar a:

```tsx
<div className="mt-3.5 flex items-center gap-2.5 rounded-subcard border border-border bg-muted/40 px-4 py-3">
  <StatusBadge kind="property" value={property.status} />
  <p className="text-xs text-ink-subtle">
    Este status é controlado pelo ciclo de vida do contrato e não pode ser alterado por aqui.
  </p>
</div>
```

5. Rodapé: `Cancelar` · `Salvar imóvel` / `Salvando...`

**Erros literais:** `"Informe o código."` · `"Selecione o proprietário."` + os de endereço.

---

# 7 · Configurações (`components/settings/`)

## `email-settings-form.tsx` — `EmailSettingsForm`

Card `rounded-card border border-border-card bg-card px-7 py-[26px] shadow-card`,
título **Emails de cobrança**.

**Props:** `{ values: EmailSettingsValues }` com
`{ emailsEnabled, daysBefore, onDueDay, daysAfter, sendReceipt, emailReplyTo, paymentInstructions, creci, address, phone, brandColor }`

**Bloco 1 — o interruptor-mestre.** Um `Checkbox` (não um `Switch`) com o texto
`"Enviar emails de cobrança automaticamente"` em `text-sm font-semibold`, e logo
abaixo o banner âmbar:

```tsx
<p className="mt-2 rounded-subcard border border-warning-banner-border bg-warning-banner px-3.5 py-2.5 text-[12.5px] text-warning-banner-foreground">
  Interruptor-mestre: com ele desligado, nenhum email é enviado por esta agência.
</p>
```

**Bloco 2 — Cadência de envio** (`border-t border-divider pt-5`):

| Campo | Tipo | Rótulo literal |
|---|---|---|
| `daysBefore` | number, 0–30 | Lembrete (dias antes do vencimento) |
| `onDueDay` | checkbox | Enviar no dia do vencimento |
| `daysAfter` | texto livre | Avisos após o vencimento (dias, separados por vírgula) |
| `sendReceipt` | checkbox | Enviar recibo automático na baixa |

`daysAfter` é digitado como `"3, 7, 15"`. O componente tem um tratamento especial de
erro: como o zod pode devolver `daysAfter.0`, `daysAfter.3` etc., ele procura
qualquer chave que comece com `daysAfter` para não deixar o campo sem feedback.

**Bloco 3 — Contato:** `Email de resposta (reply-to)` (placeholder
`financeiro@agencia.com.br`) e `Instruções de pagamento (aparecem em todos os emails)`
(`Textarea rows={3}`).

**Bloco 4 — Identidade nos emails**, com a explicação:

> O inquilino recebe o email com a marca da imobiliária, não com a da Locatizei.
> Campos em branco somem do email — nenhum deles impede o envio.

- **Cor da marca** (placeholder `#204E6E`) + nota:
  *"Colore a faixa do cabeçalho, o botão e os links. Precisa ser escura o bastante
  para ler texto branco em cima — tons claros são recusados."*
- **CRECI** (placeholder `RJ J-04321`)
- **Endereço (rodapé do email)** (placeholder
  `Rua Visconde de Pirajá, 414 · sala 902 · Ipanema, Rio de Janeiro/RJ`)
- **Telefone** (placeholder `(21) 3210-4455`)

Botão único à direita: `Salvar configurações` / `Salvando…` com `shadow-btn-primary`.

**Toast:** `"Configurações salvas"` / `"A cadência de emails de cobrança foi atualizada."`

---

## `mp-settings-card.tsx` — `MpSettingsCard`

O card do Mercado Pago. É o componente com a lógica de estado mais explícita do
produto, e vale animá-lo justamente por isso.

**Props:**

```ts
{
  mpEnabled: boolean;              // agencies.mp_enabled
  hasCredentials: boolean | null;  // null = INDETERMINADO (a leitura falhou)
}
```

**Estrutura:**

```
┌────────────────────────────────────────────────┐
│ Mercado Pago                                   │  text-[15px] font-bold
│                                                │
│ [○──] Gerar boleto e Pix automaticamente       │  Switch + Label
│                                                │
│ parágrafo explicativo (muda com hasCredentials)│  text-[12.5px] text-ink-subtle
│                                                │
│ ┌─ faixa âmbar incondicional ────────────────┐ │
│ │ Desligar não cancela os links já enviados: │ │
│ │ quem recebeu um boleto ou Pix ainda        │ │
│ │ consegue pagar.                            │ │
│ └────────────────────────────────────────────┘ │
│ ──────────────────────────────────────────────  │  border-t border-divider pt-4
│ Conta de recebimento                            │  text-[13px] font-semibold text-ink-nav
│ Conta vinculada / Sem credenciais… / alerta     │
└────────────────────────────────────────────────┘
```

**A regra do Switch, verbatim do código:**

```ts
const blockedFromEnabling = !mpEnabled && hasCredentials !== true;
const disabled = pending || blockedFromEnabling;
```

Ou seja: **ligar** exige `hasCredentials === true`. **Desligar** está sempre
disponível — inclusive com `hasCredentials === null` —, porque uma falha de leitura
não pode trancar o admin do lado de dentro de uma integração ligada.

**Os três estados de "Conta de recebimento":**

| `hasCredentials` | Texto | Estilo |
|---|---|---|
| `true` | `Conta vinculada` | `mt-1.5 text-[12.5px] text-ink-subtle` |
| `false` | `Sem credenciais — o suporte configura a conta de recebimento` | idem |
| `null` | *"Não foi possível verificar a conta de recebimento agora. Recarregue a página; enquanto a verificação não funcionar, a integração não pode ser ligada. Desligar continua disponível enquanto ela estiver ligada — mas para religar a verificação precisa voltar a funcionar."* | `role="alert"` + `rounded-subcard border border-warning-banner-border bg-warning-banner px-3.5 py-2.5 text-[12.5px] leading-relaxed text-warning-banner-foreground` |

**O parágrafo explicativo tem duas versões**, e a diferença é substantiva:

`hasCredentials === false`:

> Sem conta de recebimento vinculada, nenhum link novo é emitido: mesmo com a
> integração ligada, a rodada automática pula a agência. A falta da conta não apaga
> os links já emitidos — enquanto a integração estiver ligada, uma cobrança que já
> recebeu link pode continuar enviando o botão de pagar, mas um pagamento feito nele
> não dá baixa automática enquanto a conta não estiver vinculada.

`true` ou `null`:

> Com a conta de recebimento vinculada e a integração ligada, em geral a rodada
> automática diária emite um link de pagamento por cobrança em aberto e, se os emails
> de cobrança estiverem ligados, eles saem com o botão de pagar. Com a integração
> desligada, nenhum link novo é emitido e os emails de cobrança saem sem o botão —
> com as instruções manuais da agência, quando elas estiverem preenchidas.

**Sem toast.** O próprio Switch é o retorno visual. O que aparece, quando falha, é
um bloco de erro `role="alert"` logo abaixo do título — junto do controle, não numa
notificação flutuante.

O `Switch` está associado à linha de status por `aria-describedby="mpEnabled-status"`,
para que um leitor de tela que anuncie "indisponível" diga também o porquê.

---

# 8 · Primitivos (`components/ui/`)

20 arquivos gerados pelo shadcn CLI sobre **radix-ui**. O que importa para reproduzir
a aparência:

## `button.tsx`

Base (trecho relevante):

```
"group/button inline-flex shrink-0 items-center justify-center rounded-lg border border-transparent
 bg-clip-padding text-sm font-medium whitespace-nowrap transition-all outline-none select-none
 focus-visible:border-ring focus-visible:ring-3 focus-visible:ring-ring/50
 active:not-aria-[haspopup]:translate-y-px
 disabled:pointer-events-none disabled:opacity-50 …"
```

| Variante | Classes |
|---|---|
| `default` | `bg-primary text-primary-foreground hover:bg-primary-hover` |
| `outline` | `border-border bg-background hover:bg-muted hover:text-foreground aria-expanded:bg-muted` |
| `secondary` | `bg-secondary text-secondary-foreground hover:bg-[color-mix(in_oklch,var(--secondary),var(--foreground)_5%)]` |
| `ghost` | `hover:bg-muted hover:text-foreground aria-expanded:bg-muted` |
| `destructive` | `bg-destructive/10 text-destructive hover:bg-destructive/20` — **fundo tingido, não sólido** |
| `link` | `text-primary underline-offset-4 hover:underline` |

| Tamanho | Altura | Padding |
|---|---|---|
| `xs` | `h-6` | `px-2 text-xs` |
| `sm` | `h-7` | `px-2.5 text-[0.8rem]` |
| `default` | `h-8` | `px-2.5` |
| `lg` | `h-9` | `px-2.5` |
| `icon` / `icon-xs` / `icon-sm` / `icon-lg` | `size-8` / `size-6` / `size-7` / `size-9` | — |

Repare: `destructive` **não** é um botão vermelho preenchido. É vermelho sobre um
fundo `destructive/10`. Se sua animação pintar "Encerrar contrato" de vermelho
sólido, estará errando.

## `badge.tsx`

```
"group/badge inline-flex h-5 w-fit shrink-0 items-center justify-center gap-1 overflow-hidden
 rounded-4xl border border-transparent px-2 py-0.5 text-xs font-medium whitespace-nowrap transition-all …"
```

Altura fixa de **20px**, raio 26px (visualmente uma pílula), texto 12px peso 500.
Variantes semânticas listadas em [design-system.md §3.5](design-system.md#35-pares-semânticos-fundo--texto--o-vocabulário-de-status).
A variante `dashed` é a exceção:
`"border-dashed border-ink-disabled bg-transparent text-muted-foreground"`.

## `input.tsx`

```
"h-8 w-full min-w-0 rounded-lg border border-input bg-transparent px-2.5 py-1 text-base
 transition-colors outline-none placeholder:text-muted-foreground
 focus-visible:border-ring focus-visible:ring-3 focus-visible:ring-ring/50
 disabled:pointer-events-none disabled:cursor-not-allowed disabled:bg-input/50 disabled:opacity-50
 aria-invalid:border-destructive aria-invalid:ring-3 aria-invalid:ring-destructive/20 md:text-sm …"
```

Altura base de 32px. **Quase toda tela sobrescreve com `h-auto py-2.5`** para chegar
aos ~40px do design.

## `switch.tsx`

`data-[size=default]:h-[18.4px] data-[size=default]:w-[32px]`, thumb `size-4`
(`bg-background`), trilho `data-checked:bg-primary data-unchecked:bg-input`.
Área de clique ampliada por `after:absolute after:-inset-x-3 after:-inset-y-2`.

## `dialog.tsx`

Overlay: `fixed inset-0 isolate z-50 bg-black/10 duration-100 supports-backdrop-filter:backdrop-blur-xs`
+ `data-open:fade-in-0` / `data-closed:fade-out-0`.
Conteúdo: centralizado com `top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2`,
`rounded-xl bg-popover p-4 ring-1 ring-foreground/10 duration-100`,
`data-open:zoom-in-95` / `data-closed:zoom-out-95`.
**Todos os dialogs do produto sobrescrevem `p-4` por `p-0` + `p-[26px]` no form, e
`rounded-xl` por `rounded-card`.**

## `sheet.tsx`

Mesmo overlay. `data-[side=right]` → `inset-y-0 right-0 h-full w-3/4 border-l`, com
`sm:max-w-sm`. Animação `slide-in-from-right-10` / `slide-out-to-right-10`,
`transition duration-200 ease-in-out`. O drawer de pagamento sobrescreve para
`w-[440px] sm:max-w-[440px] border-l-0 shadow-drawer`.

## `dropdown-menu.tsx`

Conteúdo: `rounded-lg bg-popover p-1 ring-1 ring-foreground/10 shadow-md duration-100`
+ `zoom-in-95` e slides direcionais.
Item: `rounded-md px-1.5 py-1 text-sm focus:bg-accent focus:text-accent-foreground`;
`variant="destructive"` → `text-destructive focus:bg-destructive/10`.
Nas telas, o conteúdo é sempre sobrescrito para `w-52 rounded-xl p-1.5 shadow-dropdown`.

## `table.tsx`

Envolve a `<table>` num `<div className="relative w-full overflow-x-auto">`.
`TableRow` traz `border-b transition-colors hover:bg-muted/50`. **Todas as tabelas
do produto sobrescrevem** `TableHead`/`TableCell` com `h-auto p-0` e transformam a
linha num `grid` — as classes de padding vivem na `TableRow`, não nas células.

## Os demais

`avatar.tsx`, `breadcrumb.tsx`, `card.tsx`, `checkbox.tsx`, `command.tsx`,
`input-group.tsx`, `label.tsx`, `popover.tsx`, `radio-group.tsx`, `select.tsx`,
`separator.tsx`, `textarea.tsx` — shadcn padrão, sem customização de tema além dos
tokens.

Três deles não são consumidos por nenhuma tela (verificado por varredura de imports):
`card.tsx` e `separator.tsx` **não são importados em lugar nenhum**, e
`input-group.tsx` só é importado por `command.tsx`. Ou seja: **os cards do produto não
usam o componente `Card`** — são `<div>` com as classes diretas
(`rounded-card border border-border-card bg-card shadow-card`).

---

## Padrões que se repetem — vale saber antes de animar

1. **`Field` local em vez de componente compartilhado.** O mesmo helper (label +
   filho com `aria-invalid` injetado + `<p role="alert">`) está copiado em 8
   arquivos. Duas variações de rótulo convivem: `text-[13px]` (formulários de
   página) e `text-[12.5px]` (drawer e alguns dialogs).
2. **Todo dialog é remontado ao abrir** por uma `key={resetKey}`, para que erros de
   uma tentativa anterior não vazem para a próxima.
3. **Toast é sempre `fixed top-6 right-6 z-50`**, `rounded-xl border border-border-card
   bg-card px-4 py-3 shadow-toast`, com um círculo de 24px à esquerda. Duração
   padrão: **4500 ms**. A única exceção é o toast de erro do menu de cobranças, que
   espera o clique no ✕.
4. **Busca sempre com debounce de 350 ms**, escrevendo em `?q=` da URL — o que faz o
   servidor re-renderizar. Não há filtro client-side em nenhuma lista principal.
5. **Nenhum spinner de página.** O único indicador de carregamento visível é o
   `animate-spin` do botão Atualizar; o resto usa texto no botão (`Salvando…`,
   `Confirmando…`, `Enviando…`, `Gerando…`, `Ativando...`).

---

[◄ Design system](design-system.md) · [Telas ►](telas.md)


---

<!-- ===== telas.md ===== -->

# Telas — uma seção por rota

> 17 rotas. Para cada uma: propósito, estrutura, dados exibidos, ações e **todos os
> estados** que o código produz. Layouts em ASCII são esquemas de proporção, não
> pixel-perfect — as classes exatas estão em [componentes.md](componentes.md).

[◄ Voltar ao índice](README.md)

---

## O shell, que envolve 16 das 17 telas

Tudo exceto `/login` roda dentro deste esqueleto:

```
┌──────────────────────────────────────────────────────────────────────────────┐
│ ▪▪ Locatizei   [🔍 Buscar pessoas, imóveis…      ⌘K]   [Agência ▾][🔔][?][MP Marina Prado / Gestora][Sair] │
├──────────────────────────────────────────────────────────────────────────────┤
│ (Dashboard)(Pessoas)(Imóveis)(Contratos)(Cobranças)(Inadimplência ③)(Config)(Auditoria)   [+ Novo …] │
├──────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│                          conteúdo — max-w-1440px, px-8 py-8                  │
│                                                                              │
└──────────────────────────────────────────────────────────────────────────────┘
```

- Header: `border-b border-divider`, `px-8 pt-4 pb-3`.
- Nav: `border-b border-divider-thin`, `px-8 pt-2.5 pb-1`.
- Conteúdo: `mx-auto w-full max-w-[1440px] flex-1 px-8 py-8`.
- **Sem sidebar. Sem rodapé.** A navegação é inteiramente horizontal.
- O badge vermelho em "Inadimplência" traz a contagem de cobranças vencidas em aberto.
  Se a consulta falhar, o badge **some** (nunca mostra um número falso).
- As pills "Configurações" e "Auditoria" **só existem para o papel admin**.

Rota protegida: um middleware redireciona quem não tem sessão para `/login`.
`/configuracoes` e `/auditoria` fazem um segundo teste no servidor e **redirecionam
o operador para `/`** — ele nunca vê a tela, nem por URL direta.

---

# 1 · `/login`

**Fora do shell.** Tela cheia, fundo `bg-surface-login` (`#eef1f7`), card centralizado.

```
                         ┌─────────────────────────┐
                         │          ┌────┐         │
                         │          │ L  │         │  56×56, bg-primary, rounded-card
                         │          └────┘         │
                         │        Locatizei        │  20px extrabold
                         │ Painel de gestão de     │  14px ink-subtle
                         │      locações           │
                         │                         │
                         │ Email                   │
                         │ [                     ] │
                         │                         │
                         │ Senha   Esqueci minha…  │
                         │ [                  👁 ] │
                         │                         │
                         │ [       Entrar        ] │  w-full
                         └─────────────────────────┘
                    Acesso restrito à equipe da imobiliária.
                          feito com **Locatizei**
```

**Card:** `w-full max-w-[424px] rounded-[20px] border border-border-card bg-card p-9 shadow-login-card`

**Campos:** `Email` (`type="email"`, `autoComplete="email"`) e `Senha`
(`autoComplete="current-password"`), ambos com
`h-auto rounded-[10px] border-input px-3.5 py-[11px] text-sm`.

**Botão de olho:** `absolute inset-y-0 right-0 flex w-10 items-center justify-center`,
alterna `Eye`/`EyeOff`, com `aria-pressed` e `aria-label` "Mostrar senha"/"Ocultar senha".

**Botão Entrar:** `w-full rounded-[12px] py-[11px] text-sm font-semibold`.
Carregando: `Entrando...` + desabilitado.

**Estados:**

| Estado | O que aparece |
|---|---|
| Normal | formulário limpo |
| Carregando | botão vira `Entrando...`, desabilitado |
| Credencial errada | `<p role="alert" className="rounded-[10px] bg-danger px-3 py-2 text-[12.5px] font-medium text-danger-foreground">Email ou senha inválidos.</p>` |
| Falha transitória (429/5xx) | mesma caixa, texto: `Não foi possível entrar agora. Muitas tentativas ou serviço indisponível — tente novamente em instantes.` |

**Lacuna declarada:** "Esqueci minha senha" **é um `<span>`, não um link**. Não existe
fluxo de recuperação. O comentário do código diz: *"sem fluxo de recuperação
implementado ainda — texto estático em vez de link quebrado."* A cor é
`text-primary/70`, deliberadamente meio apagada.

> **Não anime esta tela.** O handoff é sobre a experiência de uso; o login está aqui
> só para você saber que ele existe e que a marca aparece como um quadrado azul com
> "L", não como o logotipo de dois quadrados.

---

# 2 · `/` — Dashboard

A tela inicial. Título: **"Central operacional da carteira"**.

*(números do conjunto de demonstração — ver [dados-demo.md](dados-demo.md))*

```
┌──────────────────────────────────────────────────────────────────────────────┐
│ (Imóveis 11)(Proprietários 4)(Inquilinos 7)(Carteira ativa 10)  [⟳] [◄ set/2026 ►] │
│                                                                              │
│ Central operacional da carteira                          ← h1 26px bold      │
│ Acompanhe o desempenho da sua operação e foco no que realmente importa.      │
│                                                                              │
│ ┌────────────┐ ┌────────────┐ ┌────────────┐ ┌────────────┐                  │
│ │📅 Previsto │ │💲 Recebido │ │⚠ Vencido   │ │% Inadimpl. │                  │
│ │ R$ 18.100  │ │ R$ 8.150   │ │ R$ 6.080   │ │   33,6%    │                  │
│ │ ▬▬▬▬░░░░░░ │ │ 45,0% do…  │ │ 33,6% do…  │ │ do previsto│                  │
│ │ 45,0% rec. │ │ ▼12,6% vs. │ │ todas as   │ │ todas as   │                  │
│ └────────────┘ └────────────┘ └────────────┘ └────────────┘                  │
│                                                                              │
│ ┌──────────────────┐ ┌──────────────────┐ ┌──────────────────┐               │
│ │ Exige atenção ③  │ │ Próximos venc. 7d│ │ Saúde da operação│               │
│ │ (BC) Beatriz  R$ │ │ [20] Thiago   R$ │ │    ◕  10 imóveis │               │
│ │      Rua Cel…+43d│ │ SET  IMV-0005    │ │  ● Alugados   7  │               │
│ │      [Ver cobrança]│ [22] Priscila R$ │ │  ● Disponíveis 2 │               │
│ │  …               │ │  …               │ │  ● Manutenção  0 │               │
│ │  Ver todas (3)   │ │  Ver todos (3)   │ │  ● Outros      1 │               │
│ └──────────────────┘ └──────────────────┘ │ ─────────────────│               │
│                                            │ Encerrando 60d   │               │
│                                            │ 1 contrato [Ver] │               │
│                                            └──────────────────┘               │
└──────────────────────────────────────────────────────────────────────────────┘
```

**Chips-resumo do topo** — quatro links (`rounded-subcard border px-3.5 py-2 text-[13px] font-medium`):

| Chip | Contagem | Destino |
|---|---|---|
| Imóveis | todos os imóveis não excluídos | `/imoveis` |
| Proprietários | pessoas com papel `owner` | `/pessoas` |
| Inquilinos | pessoas com papel `tenant` | `/pessoas` |
| **Carteira ativa** | imóveis fora de `draft`/`archived` | `/imoveis` |

O último tem tratamento verde: `border-success-border bg-success-hint text-success-foreground`.
Os outros três são neutros, com hover `hover:border-brand-border-tint hover:bg-brand-hint`.

**Controles à direita:** o botão Atualizar (36px, `RotateCw`, gira enquanto recarrega)
e o seletor de mês.

**Os quatro KPIs** — detalhes em [componentes.md](componentes.md#kpi-cardstsx--kpicards).
A regra semântica que precisa ficar clara na animação:

| KPI | Escopo | Tem delta? |
|---|---|---|
| Previsto no mês | **só o mês selecionado** | não (tem barra de progresso) |
| Recebido | **só o mês selecionado** | **sim** — ▲/▼ vs. mês anterior |
| Vencido | **todas as competências** (foto atual) | não |
| Inadimplência | Vencido ÷ Previsto | não |

Mudar o mês no seletor muda **Previsto** e **Recebido**. Vencido e Inadimplência
ficam parados — e os próprios cards escrevem `todas as competências` para dizer isso.

**Os três cards inferiores:** `grid grid-cols-1 gap-4 lg:grid-cols-3`.

### Estados da tela

| Estado | Condição | O que aparece |
|---|---|---|
| **Com dados** | normal | tudo acima |
| **Vazio** | nenhum contrato fora de rascunho | só o `<h1>` + `EmptyState`: título *"Os indicadores aparecerão quando os primeiros contratos forem criados"*, descrição *"Cadastre pessoas e imóveis, crie um contrato e a agenda de cobranças é gerada automaticamente."*, botão **+ Novo contrato** → `/contratos/novo` |
| **Erro** | qualquer consulta falha | só o `<h1>` + banner `role="alert"`: *"Não foi possível carregar os indicadores. Tente novamente."* em `rounded-subcard border border-danger bg-danger px-4 py-3 text-sm font-medium text-danger-foreground` |
| **Carregando** | — | **não existe skeleton.** O dashboard é renderizado no servidor; a única animação de carregamento é o `animate-spin` do botão Atualizar |

**Estados internos dos cards:**
- Sem vencidas → `Nenhuma cobrança vencida no momento.` e o rodapé "Ver todas" some.
- Sem vencimentos próximos → `Nenhum vencimento nos próximos 7 dias.`
- Carteira vazia → donut cinza inteiro e `Nenhum imóvel na carteira ativa.`
- Previsto = 0 → `Sem cobranças previstas no mês` e `—` nos outros três KPIs.

**Responsivo:** os únicos breakpoints do dashboard são
`sm:grid-cols-2 lg:grid-cols-4` nos KPIs e `lg:grid-cols-3` nos cards. Abaixo de
`lg`, os três cards empilham.

---

# 3 · `/pessoas` — lista

Título **Pessoas**, subtítulo dinâmico:
`"11 cadastros · 7 inquilinos · 4 proprietários"` (com plural correto em cada um).

```
Pessoas
11 cadastros · 7 inquilinos · 4 proprietários

[🔍 Buscar por nome, CPF ou CNPJ...     ]        (Todos 11)(Inquilinos 7)(Proprietários 4)

┌──────────────────────────────────────────────────────────────────────────────┐
│ Nome            Papéis        CPF/CNPJ        Contato          Contratos  ›  │
├──────────────────────────────────────────────────────────────────────────────┤
│ (BC) Beatriz N. [Inquilino(a)] 194.630.782-05 beatriz.chaves@…     1      ›  │
│      Pessoa física                            (19) 99204-3316                │
│ ▢ SF Studio Far [Inquilino(a)] 28.914.630/…   financeiro.farol@…   2      ›  │
│      Pessoa jurídica                          (19) 3391-2264                 │
└──────────────────────────────────────────────────────────────────────────────┘
```

**Busca:** nome **ou** documento. O filtro de documento só entra se a busca tiver
dígitos (senão um `%%` vazio casaria com qualquer documento e a busca por nome
deixaria de filtrar).

**Chips:** Todos / Inquilinos / Proprietários, com contadores que **não** mudam com a
busca — são sempre os totais da agência.

**Detalhe que carrega significado:** o avatar é **círculo para pessoa física** e
**quadrado arredondado para pessoa jurídica**. A segunda linha confirma em texto.

**Ação por linha:** a linha inteira é um link para `/pessoas/{id}`.
**Ação de página:** o CTA **+ Nova pessoa** vive na barra de navegação.

### Estados

| Estado | O que aparece |
|---|---|
| Com dados | a tabela |
| **Vazio de verdade** (0 cadastros) | `EmptyState`: *"Nenhuma pessoa cadastrada"* / *"Cadastre inquilinos e proprietários — cada pessoa tem um cadastro único e pode ter vários papéis."* / botão **Nova pessoa**. Note: **substitui a página inteira**, sem título nem subtítulo |
| Vazio de filtro | dentro do card: `Nenhuma pessoa encontrada para esses filtros.` (`px-[22px] py-10 text-center text-sm text-muted-foreground`) |
| Erro | `<h1>` + banner vermelho `Não foi possível carregar a lista. Tente novamente.` |

O código é explícito sobre não confundir os dois vazios: uma falha de consulta
**nunca** vira empty state.

---

# 4 · `/pessoas/nova`

```
Pessoas › Nova pessoa
Nova pessoa
Os campos com * são obrigatórios.

┌─ card 820px ────────────────────────────────────┐
│ Tipo de pessoa *                                 │
│ [ ○ Pessoa física     ] [ ○ Pessoa jurídica    ] │
│   documento no formato CPF   …CNPJ               │
│                                                  │
│ Nome completo *              CPF *               │
│ [                        ]  [                 ]  │
│                                                  │
│ Email                        Telefone            │
│ Papéis *                                         │
│ [ ☐ Inquilino ] [ ☐ Proprietário ]               │
│ ───────────────────────────────────────────────  │
│ Endereço                                         │
│ CEP        Logradouro                  Número    │
│ Complemento  Bairro    Cidade    UF              │
│ Observações                                      │
│ ───────────────────────────────────────────────  │
│                        [Cancelar] [Salvar pessoa]│
└──────────────────────────────────────────────────┘
```

Breadcrumb `Pessoas › Nova pessoa` (separador `›` literal, item ativo em
`font-semibold text-ink-title`, links em `font-semibold text-primary`).

Formulário completo descrito em [componentes.md](componentes.md#person-formtsx--personform),
incluindo o **banner âmbar de documento duplicado** com o botão "Salvar mesmo assim".

**Estados:** normal · campo inválido (borda vermelha + mensagem com ícone
`CircleAlert size-3`) · duplicado (âmbar, não bloqueante) · salvando (`Salvando...`) ·
falha (`Não foi possível salvar. Tente novamente.` no topo do card).

---

# 5 · `/pessoas/[id]` — detalhe

```
Pessoas › Beatriz Nunes Chaves

┌────┐  Beatriz Nunes Chaves [Inquilino(a)] [Pessoa física]   [⋯] [✎ Editar]
│ BC │  Cadastrada em 12/03/2025 por Marina Prado Bandeira
└────┘

┌── Dados ────Editar┐ ┌── Contatos ──────Editar┐ ┌── Endereço ─────Editar┐
│ CPF                │ │ Email                  │ │ Logradouro            │
│ 194.630.782-05     │ │ beatriz.chaves@exampl… │ │ Rua Coronel Quirino,  │
│ Tipo               │ │ Telefone               │ │ 1420                  │
│ Pessoa física      │ │ (19) 99204-3316        │ │ Cidade                │
│                    │ │                        │ │ Cambuí — Campinas/SP  │
└────────────────────┘ └────────────────────────┘ └───────────────────────┘

┌─ Contratos ① ─────────────────────────────────────────────────────┐
│ Papel no contrato  Imóvel        Vigência       Aluguel  Status  › │
│ [Locatário(a)]  IMV-0001 · Rua…  01/04 — 30/04  R$ 2.450 [Ativo] › │
└───────────────────────────────────────────────────────────────────┘

┌─ Timeline ────────────────────────────────────────────────────────┐
│ ● Cadastro atualizado por Marina Prado Bandeira                   │
│ │  há 3 dias · 15/09/2026 09:42                                   │
│ ● Pessoa cadastrada por Marina Prado Bandeira                     │
│    há 1 ano · 12/03/2025 14:10                                    │
└───────────────────────────────────────────────────────────────────┘
```

**Cabeçalho:** avatar grande de 56px (`size-14`, `text-lg font-extrabold`), círculo
ou quadrado conforme PF/PJ. Ao lado do nome, os `RoleBadge` e um
`<Badge variant="outline">` com "Pessoa física"/"Pessoa jurídica".

**⚠️ O botão `⋯` do cabeçalho não faz nada.** É um `<div aria-hidden="true">` com
`opacity-50` e `cursor-default`. O comentário do código: *"nenhuma ação definida
ainda — tratamento visualmente desabilitado … para não anunciar uma affordance falsa."*
O botão **Editar** (outline, `border-brand-border-tint bg-brand-hint text-primary`,
com ícone `Pencil size-3.5`) funciona.

**Três `InfoCard`** (`grid grid-cols-3 gap-5`), cada um com um link "Editar" no canto
que leva ao mesmo formulário.

**Tabela de contratos:** mostra o **papel desta pessoa naquele contrato** —
`Locador(a)` (badge owner/teal) ou `Locatário(a)` (badge info/azul). Linha inteira
clicável para o contrato.

**Timeline:** eventos de `entity_type = "person"`, limite de 50.

**Estados:** pessoa inexistente → **404** do Next (`notFound()`). Sem contratos →
`Nenhum contrato para esta pessoa ainda.` Sem eventos →
`Nenhum evento registrado ainda.`

---

# 6 · `/pessoas/[id]/editar`

Breadcrumb de três níveis: `Pessoas › Beatriz Nunes › Editar`.
Título `Editar Beatriz Nunes`. O mesmo `PersonForm`, agora preenchido.
"Cancelar" volta para `/pessoas/{id}` (na criação, volta para `/pessoas`).
Pessoa inexistente → 404.

---

# 7 · `/imoveis` — lista

Título **Imóveis**, subtítulo `"11 na carteira ativa · 63,6% de ocupação"`.

A ocupação é `alugados ÷ carteira ativa`, com uma casa decimal e **vírgula** decimal.
**"Carteira ativa" aqui = total menos arquivados** — repare que essa definição é
diferente da do chip homônimo do dashboard, que também exclui rascunhos. Os dois
números podem divergir na mesma sessão; ver
[dados-demo.md §4](dados-demo.md#4--imóveis--11-cadastrados).

```
Imóveis
11 na carteira ativa · 63,6% de ocupação

[🔍 Buscar por código ou endereço...  ]   (Todos 11)(Alugados 7)(Disponíveis 2)(Manutenção 0)(Mais ▾)

┌──────────────────────────────────────────────────────────────────────────────┐
│ Código   Endereço                       Status     Proprietário  Contrato  › │
├──────────────────────────────────────────────────────────────────────────────┤
│ IMV-0001 Rua Coronel Quirino, 1420 ·ap51 [Alugado] Helena Prado V. Ver contr›│
│          Cambuí — Campinas/SP                                                │
│ IMV-0008 Rua Dr. Emílio Ribas, 615·casa2 [Disponív] Lúcia Amorim F.   —     ›│
│          Cambuí — Campinas/SP                                                │
└──────────────────────────────────────────────────────────────────────────────┘
```

**Chips:** quatro visíveis + um popover **"Mais ▾"** com Reservados, Indisponíveis,
Rascunhos, Arquivados (cada um com contador). Quando um deles está ativo, **o botão
"Mais" troca de rótulo** para o item escolhido e assume o estilo ativo.

**Busca:** em memória, sobre código, logradouro, bairro e cidade.

**Imóvel arquivado esmaece a linha inteira** (código, endereço e proprietário caem
para tons de `ink-subtle`/`ink-disabled`).

**Ações:** linha → `/imoveis/{id}`; `Ver contrato` → `/contratos/{id}` do contrato
ativo; CTA **+ Novo imóvel** na navegação.

**Estados:** vazio de verdade → `EmptyState` *"Nenhum imóvel cadastrado"* /
*"Cadastre os imóveis da carteira para usá-los nos contratos, sem recadastro."* /
botão **Novo imóvel**. Vazio de filtro → `Nenhum imóvel encontrado para esses filtros.`
Erro → banner `Não foi possível carregar a lista. Tente novamente.`

---

# 8 · `/imoveis/novo`

Breadcrumb `Imóveis › Novo imóvel`. Título `Novo imóvel`, subtítulo
`Os campos com * são obrigatórios.`

Formulário em [componentes.md](componentes.md#property-formtsx--propertyform).
O campo **Código** já vem preenchido com a sugestão automática (`IMV-0015`) e a nota
*"Sugerido automaticamente — você pode editar."*

**Estado de erro específico desta rota:** se a lista de proprietários não carregar,
o formulário **não é renderizado** — no lugar vai o banner
*"Não foi possível carregar a lista de proprietários. Tente novamente."* O raciocínio
no código: um combobox vazio silencioso só falharia na hora de salvar.

---

# 9 · `/imoveis/[id]` — detalhe

```
Imóveis › IMV-0001

IMV-0001 [Alugado]                                        [⋯] [✎ Editar]
Rua Coronel Quirino, 1420 · ap 51 — Cambuí, Campinas/SP · CEP 13025-002

┌─ Proprietário ───────────────────────────────── Ver cadastro → ┐
│ (HV)  Helena Prado Vasconcelos   helena.vasconcelos@example.com│
│       CPF 312.457.890-11                       (19) 98812-4407 │
└────────────────────────────────────────────────────────────────┘

┌─ Histórico de contratos ① ───────────────────────────────────┐
│ Locatário            Vigência        Aluguel    Status     › │
│ Beatriz Nunes Chaves 01/04 — 30/04   R$ 2.450   [Ativo]    › │
└──────────────────────────────────────────────────────────────┘

┌─ Timeline ───────────────────────────────────────────────────┐
│ ● Imóvel atualizado por Marina Prado Bandeira …              │
└──────────────────────────────────────────────────────────────┘
```

*(o histórico com contrato **Encerrado** — inteiro em `text-ink-subtle` — aparece em
`IMV-0010`, o imóvel do contrato CT-08 do conjunto de demonstração)*

**Cabeçalho:** código como `<h1>`, `StatusBadge` ao lado, e o endereço completo em
uma linha só: `Rua X, 123 · complemento — Bairro, Cidade/UF · CEP 00000-000`.
Sem endereço: `"Endereço não informado."`

**⚠️ O `⋯` aqui também é decorativo e desabilitado**, mesmo tratamento de `/pessoas/[id]`.

**Card do proprietário:** avatar de 42px, nome, documento formatado, e email/telefone
alinhados à direita. Link `Ver cadastro →`.

**Histórico de contratos:** contratos em estado terminal (`terminated`, `completed`,
`cancelled`) ficam **inteiros em `text-ink-subtle`** — apagam visualmente sem sumir.

**Estados:** imóvel inexistente → 404. Erro de carga → banner
*"Não foi possível carregar este imóvel. Tente novamente."* Sem proprietário →
`Nenhum proprietário vinculado.` Sem contratos → `Nenhum contrato para este imóvel ainda.`
Falha no histórico → `Não foi possível carregar o histórico de contratos.` (vermelho).
Falha na timeline → `Não foi possível carregar o histórico de eventos.`

---

# 10 · `/imoveis/[id]/editar`

Breadcrumb de três níveis. Título `Editar IMV-0007`. Mesmo `PropertyForm`.

**Comportamento importante:** se o imóvel está `rented`, `reserved`, `unavailable`
ou `archived`, a seção "Status inicial" perde os rádios e vira um badge somente
leitura com a explicação *"Este status é controlado pelo ciclo de vida do contrato e
não pode ser alterado por aqui."*

---

# 11 · `/contratos` — lista

Título **Contratos**, sem subtítulo. Busca + três abas com contador.

```
Contratos

[🔍 Buscar por imóvel ou locatário...]        (Ativos 7)(Rascunhos 1)(Todos 8)

┌──────────────────────────────────────────────────────────────────────────────┐
│ Imóvel               Locatário      Vigência          Aluguel  Status     ›  │
├──────────────────────────────────────────────────────────────────────────────┤
│ IMV-0001             Beatriz Nunes  01/04/25—30/04/27 R$2.450  [Ativo]    ›  │
│ Rua Coronel Quirino, 1420                                                    │
│ IMV-0002             Rodrigo Sampaio 01/10/25—23/10/26 R$1.980 [Encerrando]› │
│ Rua Dr. Sampaio Ferraz, 340                                                  │
│ IMV-0008             —              01/10/26 — —      R$2.300 [Rascunho][Continuar]│ ← bg-surface-hint
│ atualizado há 2 dias                                                         │
└──────────────────────────────────────────────────────────────────────────────┘
```

**Aba padrão: Ativos.** Rascunho é uma entidade separada (o wizard salva o estado
parcial), com tratamento visual próprio: fundo `bg-surface-hint`, badge tracejado
`Rascunho`, segunda linha `atualizado há N` e um botão **Continuar** que reabre o
wizard em `/contratos/novo?draft={id}`.

**Badge de status:** contratos reais usam o status **derivado** — um contrato `active`
terminando em ≤ 60 dias mostra **"Encerrando"** em laranja, não "Ativo".

**Estados:** sem contratos **e** sem rascunhos → `EmptyState` *"Nenhum contrato por
aqui ainda"* / *"Crie um contrato para gerar automaticamente a agenda de cobranças
do imóvel."* / botão **Novo contrato**. Vazio de filtro →
`Nenhum contrato encontrado para esses filtros.` Erro →
`Não foi possível carregar a lista. Tente novamente.`

> **Lacuna honesta:** a listagem de rascunhos vem de uma função que não expõe erro de
> consulta. Uma falha de rede ali apareceria como "nenhum rascunho" em vez de um
> alerta. Está registrado no próprio código como pendência conhecida.

---

# 12 · `/contratos/novo` — o wizard

Breadcrumb `Contratos › Novo contrato`. Título **centralizado** (único `<h1>`
centralizado do app: `mt-3.5 text-center text-[26px] font-bold`).

```
                    Contratos › Novo contrato
                          Novo contrato

        ①────────②────────③────────④
   Imóvel e   Vigência   Revisão  Ativação
    partes    e valores

              ┌─ card max-w-920px ─────────────────┐
              │                                    │
              │      conteúdo da etapa             │
              │                                    │
              ├────────────────────────────────────┤
              │ [💾 Salvar rascunho]  [Cancelar][Voltar][Continuar] │
              └────────────────────────────────────┘
```

As quatro etapas estão detalhadas em
[componentes.md](componentes.md#contract-wizardtsx--contractwizard). Resumo do que
cada uma mostra:

| Etapa | Título | Conteúdo |
|---|---|---|
| 1 | Imóvel e partes | combobox de imóvel (só `Disponível` selecionável) + locador + locatário |
| 2 | Vigência e valores | início/fim (+ switch "Sem prazo definido"), primeira competência, dia de vencimento (1–28), aluguel, taxa de administração, multa e juros |
| 3 | Revisão | 4 cartões de conferência + **simulação das primeiras 12 cobranças** |
| 4 | Ativação | resumo de impacto em 3 linhas + aviso de irreversibilidade |

**O card encolhe na etapa 4** (`max-w-[760px]`), e o botão final ganha a sombra
`shadow-cta` — a mais forte do sistema, usada **só ali**.

**Estados especiais:**
- **Rascunho retomado** (`?draft=…`): os campos vêm preenchidos e o wizard abre na
  etapa em que foi salvo. Rascunho inexistente → banner
  *"Rascunho não encontrado. Ele pode ter sido excluído."*
- **Sair com alterações não salvas:** dialog *"Sair sem salvar?"*.
- **Falha ao ativar:** bloco vermelho no topo da etapa 4, com a lista de campos
  problemáticos em `<ul>`.
- **Falha de carga:** imóveis ou pessoas indisponíveis → o wizard não é renderizado,
  banner no lugar.

---

# 13 · `/contratos/[id]` — detalhe

A tela mais densa do produto.

```
Contratos › IMV-0001

Contrato — IMV-0001 · Rua Coronel Quirino, 1420   [Ativo]                     [⋯]
Locador(a) Helena Prado Vasconcelos | Locatário(a) Beatriz Nunes Chaves
                                     | Vigência 01/04/2025 — 30/04/2027

┌─ ⚠ banner vermelho (só se houver vencida) ────────────────────────────────────┐
│ A cobrança de agosto/2026 está vencida há 43 dias — R$ 2.450,00 + 1 outra     │
│ vencida · 9 emails enviados · Total atualizado: R$ 5.043,74                   │
│ (original R$ 4.900,00)                                                        │
│                        [Registrar pagamento]  Ver na inadimplência            │
└──────────────────────────────────────────────────────────────────────────────┘

┌── Aluguel ──────────┐ ┌── Próximo vencimento ┐ ┌── Último pagamento ─┐
│ R$ 2.450,00         │ │ 05/10/2026 [Agendada]│ │ R$ 2.450,00         │
│ + taxa de adm 10% — │ │ competência out/2026 │ │ 05/07/2026 · Pix    │
│   R$ 245,00         │ │ · R$ 2.450,00        │ │                     │
│ Multa 2% · juros 1% │ │                      │ │                     │
│ /mês (informativos) │ │                      │ │                     │
└─────────────────────┘ └──────────────────────┘ └─────────────────────┘

┌─ Agenda de cobranças ⑫ ──────────────────────────────────────────────────────┐
│ Competência   Vencimento   Valor       Status                Ações           │
│ julho/2026    05/07/2026   R$2.450,00  [Paga]     [Ver recibo]               │
│ agosto/2026   05/08/2026   R$2.450,00  [Vencida]  [Registrar pagamento] ← bg-danger
│ setembro/2026 05/09/2026   R$2.450,00  [Vencida]  [Registrar pagamento] ← bg-danger
│ outubro/2026  05/10/2026   R$2.450,00  [Agendada] —                          │
└──────────────────────────────────────────────────────────────────────────────┘

┌─ Documentos ② ──────────────┐ ┌─ Timeline ──────────────────┐
│ ⬆ Arraste arquivos aqui ou  │ │ ● Email enviado pelo Sistema │
│   clique para enviar        │ │ ● Cobrança vencida pelo Sist.│
│ PDF, JPG ou PNG · até 10 MB │ │ ● Contato registrado por…    │
│ [Enviar documento]          │ │ ● Contrato ativado por…      │
│ 📄 contrato-locacao-…  1,4MB│ │                              │
└─────────────────────────────┘ └──────────────────────────────┘
```

*(o banner soma as duas vencidas: `2.534,12 + 2.509,62` = R$ 5.043,74 atualizado
sobre R$ 4.900,00 originais, e `5 + 4` = 9 emails)*

**Cabeçalho.** O `<h1>` é `Contrato — {código} · {endereço}`, seguido do
`StatusBadge` **derivado** e de até dois badges âmbar independentes:

| Badge | Quando |
|---|---|
| `Emails pausados` | `contracts.emails_paused = true` |
| `Pagamento direto` | `contracts.mp_paused = true` |

Os dois podem aparecer juntos. Abaixo, a linha de metadados com locador e locatário
**como links** para `/pessoas/{id}`, separados por `|` em `text-ink-disabled`.

**Menu `⋯`** — descrito em [componentes.md](componentes.md#terminate-dialogtsx--terminatedialog).
"Encerrar contrato" só aparece para admin com contrato ativo.

**Banner de cobrança vencida** (`rounded-subcard border border-danger-banner-border bg-danger-banner px-4 py-3`).
Ele é montado por partes, e cada parte só entra se tiver dado confiável:

- sempre: `A cobrança de **{competência}** está vencida há **{N}** dias`
- `— R$ X` (saldo) — some se os pagamentos não puderem ser lidos
- `+ 1 outra vencida` / `+ 3 outras vencidas` — só se houver mais de uma
- `· N emails enviados` — some se a contagem falhar
- `· Total atualizado: R$ Y (original R$ X)` — some se os pagamentos falharem

À direita: botão **Registrar pagamento** em vermelho sólido
(`rounded-btn-sm bg-danger-solid px-3.5 py-1.5 text-[12.5px] font-bold text-danger-solid-foreground`)
e o link `Ver na inadimplência`. Se os pagamentos não carregaram, o botão é
substituído por `Saldo e registro de pagamento indisponíveis` — nunca por um botão
que agiria sobre um saldo errado.

**Três cards de resumo** (`grid grid-cols-3 gap-5`):

| Card | Conteúdo | Estado vazio |
|---|---|---|
| Aluguel | valor + `+ taxa de administração 10% — R$ 245,00` + (se > 0) `Multa 2% · juros 1%/mês (informativos)` | — |
| Próximo vencimento | data grande + badge de status + `competência X · R$ Y` | `Sem cobranças a vencer` / `nenhuma cobrança aberta ou agendada no momento` |
| Último pagamento | valor + `05/08/2026 · Pix` | `Nenhum pagamento registrado` / `os pagamentos aparecem aqui após o registro`; em erro: `Não foi possível carregar os pagamentos deste contrato.` |

**Agenda de cobranças.** Todas as cobranças do contrato, ordenadas por competência.
Dois tratamentos de linha:

- **`overdue`** → a linha inteira ganha `bg-danger` (tinte vermelho suave). O badge
  já dá o sinal forte; aqui basta o tinte.
- **`cancelled`** → competência, vencimento e valor ficam **riscados** (`line-through`)
  em tons apagados. Cobrança cancelada aparece, não some — dívida nunca desaparece
  da vista.

Ações por linha: `Registrar pagamento` (status pagável) e/ou `Ver recibo` (há baixas);
`—` quando não há ação. Se os pagamentos falharam, a palavra é **`indisponível`**, não
`—` — porque `—` significaria "não há ação", e isso seria desonesto.

**Documentos** e **Timeline** lado a lado (`grid grid-cols-2 gap-5`). A timeline aqui
funde dois recortes: eventos do contrato **e** eventos das cobranças dele (pagamento,
geração, vencimento, isenção, contato, email enviado…), limitados a 50 depois do
merge.

**Estados:** contrato inexistente → 404. Erro de carga → banner
*"Não foi possível carregar este contrato. Tente novamente."* Agenda com falha →
*"Não foi possível carregar a agenda de cobranças."* Sem cobranças →
*"Nenhuma cobrança gerada para este contrato ainda."* Documentos com falha →
*"Não foi possível carregar os documentos deste contrato."* Timeline com falha →
*"Não foi possível carregar o histórico de eventos."*

E, quando só os pagamentos falharam, aparece uma faixa entre o título da agenda e a
tabela:

> Não foi possível carregar os pagamentos — as ações de pagamento estão indisponíveis.

---

# 14 · `/cobrancas` — a lista financeira

Título **Cobranças**, subtítulo dinâmico:
`"7 cobranças em setembro/2026 · R$ 18.100,00 previstos"`.

```
Cobranças
7 cobranças em setembro/2026 · R$ 18.100,00 previstos

[📅 2026-09] [Todos os status ▾]            [🔍 Buscar por inquilino ou imóvel...]

┌──────────────────────────────────────────────────────────────────────────────┐
│ Competência  Contrato               Vencim.   Valor   Status       Ações     │
├──────────────────────────────────────────────────────────────────────────────┤
│ setembro/26  IMV-0001 · Rua Coron…  05/09/26 R$2.450 [Vencida] [Registrar…][⋯]│
│              Beatriz Nunes Chaves                                            │
│ setembro/26  IMV-0002 · Rua Dr. S…  10/09/26 R$1.980 [Parcialm.][Registrar…][⋯]│
│              Rodrigo Sampaio Leal            R$800,00 recebidos              │
│ setembro/26  IMV-0003 · Rua Maria…  14/09/26 R$3.200 [Paga]     [Ver recibo][⋯]│
│              Camila Rezende Whitaker                                         │
│ setembro/26  IMV-0005 · Rua Sales…  20/09/26 R$1.650 [Aberta]  [Registrar…][⋯]│
│              Thiago Albuquerque Mota                                         │
├──────────────────────────────────────────────────────────────────────────────┤
│ Mostrando 1–7 de 7 cobranças                            ‹ [1] ›              │
└──────────────────────────────────────────────────────────────────────────────┘
```

*(a paginação só ganha mais de um número acima de 20 cobranças na competência; com o
conjunto de demonstração há uma página só. A célula de vencimento mostraria a palavra
**hoje** em âmbar se alguma cobrança `Aberta` vencesse em D0 — no conjunto atual isso
não acontece na competência corrente.)*

**Filtro de competência é obrigatório e sempre ativo.** O padrão é o mês corrente.
Isso significa que a tela **nunca** mostra "todas as cobranças de todos os tempos" —
é sempre um mês por vez. Detalhe importante para a narrativa: a inadimplência
(tela 15) é que atravessa competências.

**Subtítulo:** a **contagem** é de todas as cobranças da competência (sem filtro de
status), mas o **dinheiro** rotulado "previstos" exclui canceladas e isentadas — a
mesma definição do KPI "Previsto no mês" do dashboard, de propósito, para as duas
telas nunca divergirem sob o mesmo rótulo.

Tudo o mais — colunas, sublinhas de MP, drawer, menu `⋯`, paginação de 20 — está em
[componentes.md](componentes.md#charges-tabletsx--chargestable).

**Deep link:** `/cobrancas?competencia=2026-08&cobrancaSelecionada={id}` abre a página
já com o drawer daquela cobrança aberto e a linha destacada
(`bg-row-selected` + barra azul de 3px à esquerda). É o alvo do botão "Ver cobrança"
do dashboard.

### Estados

| Estado | O que aparece |
|---|---|
| Com dados | a tabela |
| **Nenhuma cobrança na agência inteira** | `EmptyState` *"Nenhuma cobrança gerada"* / *"Cobranças aparecem automaticamente quando um contrato é ativado."* / botão **Ver contratos** |
| Vazio de filtro | `Nenhuma cobrança encontrada para esses filtros.` dentro do card |
| Erro | `<h1>` + `Não foi possível carregar a lista. Tente novamente.` |
| Drawer aberto | linha destacada, painel de 440px deslizando da direita |

---

# 15 · `/inadimplencia` — a fila

Título **Inadimplência**, subtítulo dinâmico. Sem busca, sem paginação, sem filtro:
uma lista única, ordenada por dias de atraso decrescente.

```
Inadimplência
3 cobranças vencidas · R$ 6.080,00 em aberto · atualizado com multa e juros:
R$ 6.250,49 · ordenado por dias de atraso

┌──────────────────────────────────────────────────────────────────────────────┐
│ Inquilino      Imóvel             Competência  Valor     Atraso  Emails (ação)│
├──────────────────────────────────────────────────────────────────────────────┤
│ (BC) Beatriz   IMV-0001 · Rua Cor… agosto/2026 R$2.534,12 [43 dias] 5 [Registrar│
│                Cambuí — Campinas/SP   original R$2.450,00 ←dangerSolid contato]│
│ (BC) Beatriz   IMV-0001 · Rua Cor… setem./2026 R$2.509,62 [13 dias] 4 [Registrar│
│                Cambuí — Campinas/SP   original R$2.450,00 ←dangerSolid contato]│
│ (RL) Rodrigo   IMV-0002 · Rua Dr.… setem./2026 R$1.206,75 [8 dias]  4 [Registrar│
│                Cambuí — Campinas/SP   original R$1.180,00 ←danger      contato]│
├──────────────────────────────────────────────────────────────────────────────┤
│ Registre cada contato feito…   3 cobranças · R$ 6.080,00 · atualizado com    │
│                                multa e juros: R$ 6.250,49                    │
└──────────────────────────────────────────────────────────────────────────────┘
```

*(o badge âmbar **"Sem email"** apareceria sob o nome de um inquilino sem email
cadastrado — no conjunto de demonstração é o Thiago, cujas cobranças estão em dia)*

**O critério de "vencida em aberto"** é único no sistema inteiro e vale aqui, no KPI
"Vencido", no banner do contrato e no badge da navegação:

> status `overdue` **OU** status `partially_paid` com vencimento já passado.

**A coluna Valor mostra o valor atualizado** (saldo + multa + juros pro-rata dia), com
a sublinha `original R$ X` **apenas quando** há multa ou juros a somar. O total do
subtítulo e do rodapé continua sendo o **saldo em aberto** — o atualizado entra como
informação adicional ao lado, nunca no lugar.

**O badge "Sem email"** (âmbar) sob o nome é o sinal mais útil desta tela: aquele
inquilino não tem email cadastrado, então a cobrança automática não o alcança e o
contato tem de ser humano.

**A escalada de cor do atraso** (âmbar → laranja → vermelho → vermelho sólido) é o
elemento visual mais expressivo do produto. Ver a matriz em
[estados-e-regras.md](estados-e-regras.md#4--intensidade-de-atraso).

### Estados

| Estado | O que aparece |
|---|---|
| Com dados | a tabela |
| **Vazio** | um empty state **próprio, verde e comemorativo** (não o `EmptyState` genérico): círculo de 52px `bg-success` com `Check size-6`, título **"Nenhuma cobrança vencida 🎉"** em `text-[16.5px] font-bold text-ink-title`, e a linha *"Quando uma cobrança vencer sem pagamento, ela aparece aqui, priorizada por dias de atraso."* Container: `rounded-card border-[1.5px] border-dashed border-success-border bg-success-hint px-8 py-11` |
| Erro | `<h1>` + `Não foi possível carregar a lista. Tente novamente.` |

O emoji 🎉 está literalmente no código.

---

# 16 · `/configuracoes` — **admin only**

Título **Configurações**, subtítulo
*"Dados da agência, cadência de emails de cobrança e pagamento online."*

Coluna única de `max-w-[560px]`, dois cards empilhados com `gap-5`.

```
Configurações
Dados da agência, cadência de emails de cobrança e pagamento online.

┌─ Emails de cobrança ────────────────────────────────┐
│ ☑ Enviar emails de cobrança automaticamente         │
│ ┌ âmbar ─────────────────────────────────────────┐  │
│ │ Interruptor-mestre: com ele desligado, nenhum  │  │
│ │ email é enviado por esta agência.              │  │
│ └────────────────────────────────────────────────┘  │
│ ─────────────────────────────────────────────────   │
│ Cadência de envio                                    │
│ Lembrete (dias antes do vencimento)  [ 3 ]          │
│ ☑ Enviar no dia do vencimento                       │
│ Avisos após o vencimento (dias, …)  [ 3, 7, 15 ]    │
│ ☑ Enviar recibo automático na baixa                 │
│ ─────────────────────────────────────────────────   │
│ Email de resposta (reply-to)                         │
│ Instruções de pagamento (aparecem em todos os emails)│
│ ─────────────────────────────────────────────────   │
│ Identidade nos emails                                │
│ Cor da marca · CRECI · Endereço · Telefone           │
│                          [Salvar configurações]      │
└──────────────────────────────────────────────────────┘

┌─ Mercado Pago ──────────────────────────────────────┐
│ [○──] Gerar boleto e Pix automaticamente            │
│ Com a conta de recebimento vinculada e a integração │
│ ligada, em geral a rodada automática diária emite…  │
│ ┌ âmbar ─────────────────────────────────────────┐  │
│ │ Desligar não cancela os links já enviados:     │  │
│ │ quem recebeu um boleto ou Pix ainda consegue   │  │
│ │ pagar.                                          │  │
│ └────────────────────────────────────────────────┘  │
│ ─────────────────────────────────────────────────   │
│ Conta de recebimento                                 │
│ Conta vinculada                                      │
└──────────────────────────────────────────────────────┘
```

Os dois cards estão detalhados em
[componentes.md](componentes.md#7--configurações-componentssettings).

**Diferença de padrão entre eles, deliberada:**

| | Emails de cobrança | Mercado Pago |
|---|---|---|
| Controle do interruptor | **Checkbox** dentro de um form com botão Salvar | **Switch** que submete sozinho ao clicar |
| Confirmação | toast flutuante `"Configurações salvas"` | **nenhum toast** — o próprio Switch repintado é o retorno |
| Erro | banner vermelho no topo do card | banner vermelho logo abaixo do título, junto do controle |

**Estados desta rota:**

| Estado | O que aparece |
|---|---|
| Normal | os dois cards |
| **Operador** | redirecionamento para `/` no servidor. A tela nunca renderiza |
| Erro de carga | `<h1>` + `Não foi possível carregar as configurações. Tente novamente.` — **os dois cards somem** |
| Salvando (emails) | botão `Salvando…` desabilitado |
| Salvando (MP) | Switch desabilitado durante a operação |
| Falha ao salvar emails | `Não foi possível salvar as configurações. Tente novamente.` |
| Falha ao alterar MP | `Não foi possível atualizar a integração de pagamento. Tente novamente.` |

**Os gates do Switch do Mercado Pago** estão na matriz de
[estados-e-regras.md](estados-e-regras.md#6--os-gates-dos-interruptores-de-configurações).

---

# 17 · `/auditoria` — **admin only**

Esta é a tela mais curta do produto, e é honesto dizer o que ela é hoje:

```tsx
export default async function AuditoriaPage() {
  const profile = await getCurrentProfile();
  if (profile.role !== "admin") redirect("/");
  return (
    <EmptyState
      title="Sem eventos de auditoria"
      description="Toda ação relevante (criação, alteração, aprovação) ficará registrada aqui de forma imutável."
    />
  );
}
```

**É isso. O arquivo inteiro.**

A rota existe, aparece na navegação do admin, e renderiza **sempre** o mesmo empty
state — sem tabela, sem filtro, sem consulta ao banco. Não há estado "com dados",
porque não há caminho no código que produza um.

> **Para a animação: não mostre uma tela de auditoria com linhas.** Os eventos de
> auditoria são gravados de verdade (e aparecem nas *timelines* de pessoa, imóvel e
> contrato), mas a tela `/auditoria` ainda não os lista. Uma demonstração com uma
> tabela ali seria uma feature inventada.

---

## Resumo das rotas

| Rota | Papel | Título | Tem CTA na nav? |
|---|---|---|---|
| `/login` | público | Locatizei | — |
| `/` | admin + operador | Central operacional da carteira | não |
| `/pessoas` | admin + operador | Pessoas | **+ Nova pessoa** |
| `/pessoas/nova` | admin + operador | Nova pessoa | **+ Nova pessoa** |
| `/pessoas/[id]` | admin + operador | *(nome da pessoa)* | **+ Nova pessoa** |
| `/pessoas/[id]/editar` | admin + operador | Editar *(nome)* | **+ Nova pessoa** |
| `/imoveis` | admin + operador | Imóveis | **+ Novo imóvel** |
| `/imoveis/novo` | admin + operador | Novo imóvel | **+ Novo imóvel** |
| `/imoveis/[id]` | admin + operador | *(código)* | **+ Novo imóvel** |
| `/imoveis/[id]/editar` | admin + operador | Editar *(código)* | **+ Novo imóvel** |
| `/contratos` | admin + operador | Contratos | **+ Novo contrato** |
| `/contratos/novo` | admin + operador | Novo contrato | **+ Novo contrato** |
| `/contratos/[id]` | admin + operador | Contrato — *(código · endereço)* | **+ Novo contrato** |
| `/cobrancas` | admin + operador | Cobranças | não |
| `/inadimplencia` | admin + operador | Inadimplência | não |
| `/configuracoes` | **só admin** | Configurações | não |
| `/auditoria` | **só admin** | *(empty state)* | não |

O CTA é da **seção**, não da rota: qualquer página sob `/pessoas` mostra
"+ Nova pessoa".

---

## O que NÃO existe em tela nenhuma

Levantado por varredura do código, não de memória:

- **Nenhum skeleton loader.** Nenhum `<Suspense>` com fallback visual, nenhum
  `loading.tsx`. As páginas são renderizadas no servidor e chegam prontas.
- **Nenhuma paginação além de `/cobrancas`.** Pessoas, imóveis, contratos e
  inadimplência listam tudo.
- **Nenhuma ordenação por clique em cabeçalho de coluna.** As ordens são fixas.
- **Nenhuma seleção múltipla / ação em lote.** Não há checkbox de linha em lugar nenhum.
- **Nenhuma exportação** (CSV, PDF, impressão).
- **Nenhum modo escuro.**
- **Nenhuma tela de usuários/equipe.** Não há rota `configuracoes/usuarios`, nem
  qualquer CRUD de perfis.
- **Nenhum upload de logo da imobiliária.** A coluna existe no banco e os emails já
  a consomem, mas nenhuma tela escreve nela.
- **Nenhuma busca global funcional** (o campo do header é decorativo).
- **Nenhuma notificação in-app** (o sino é decorativo).
- **Nenhuma recuperação de senha.**
- **Nenhum layout mobile.** Ver [design-system.md §10](design-system.md#10-responsividade--o-que-existe-hoje).

---

[◄ Componentes](componentes.md) · [Estados e regras ►](estados-e-regras.md)


---

<!-- ===== estados-e-regras.md ===== -->

# Estados e regras — as matrizes

> As tabelas que fazem a demonstração ser fiel em vez de decorativa. Cada linha foi
> conferida contra `src/lib/domain/charges.ts`, `src/components/app/status-badge.tsx`,
> `src/components/charges/charge-actions-menu.tsx`, `src/lib/domain/mp-link.ts`,
> `src/components/settings/mp-settings-card.tsx` e `src/lib/format.ts`.

[◄ Voltar ao índice](README.md)

---

## 1 · Cobrança: status × rótulo × cor × ações

Esta é **a** matriz central do produto. Oito status no banco, e cada um habilita um
conjunto diferente de ações.

| Status (banco) | Rótulo exibido | Variante | Fundo / texto | Registrar pagamento | Ver recibo | Editar valor/venc. | Quitar por acordo | Isentar | Negociação | Pausar/retomar emails |
|---|---|---|---|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
| `scheduled` | **Agendada** | `neutral` | `#eef1f6` / `#66708c` | — | — | ✅ | — | — | — | ✅ |
| `open` | **Aberta** | `info` | `#e8eefd` / `#2140d6` | ✅ | — | ✅ | — | ✅ | — | ✅ |
| `overdue` | **Vencida** | `danger` | `#fdebea` / `#e0342c` | ✅ | — | ✅ | ✅ | ✅ | ✅ marcar | ✅ |
| `under_negotiation` | **Em negociação** | `purple` | `#efeafd` / `#7c5cf0` | ✅ | — | ✅ | ✅ | ✅ | ✅ reverter | ✅ |
| `partially_paid` | **Parcialmente paga** | `warning` | `#fef3df` / `#b26e05` | ✅ | ✅ | — | ✅ | ✅ | — | ✅ |
| `paid` | **Paga** | `success` | `#e4f6ec` / `#12a150` | — | ✅ | — | — | — | — | — |
| `waived` | **Isentada** | `owner` | `#e0f3f1` / `#0e8577` | — | — | — | — | — | — | — |
| `cancelled` | **Cancelada** | `neutral` | `#eef1f6` / `#66708c` | — | — | — | — | — | — | — |

"Ver recibo" na verdade não depende do status, e sim de **existir ao menos um
`payment_record`** na cobrança. Na prática isso coincide com `paid` e
`partially_paid` — e o caso `partially_paid` é importante: ela é **pagável E tem
recibo**, então os dois botões aparecem juntos na mesma linha.

As listas literais que governam isso:

```ts
export const PAYABLE_STATUSES        = ["open", "overdue", "partially_paid", "under_negotiation"];
export const EDITABLE_STATUSES       = ["scheduled", "open", "overdue", "under_negotiation"];
export const WAIVABLE_STATUSES       = ["open", "overdue", "partially_paid", "under_negotiation"];
export const SETTLEABLE_STATUSES     = ["overdue", "under_negotiation", "partially_paid"];
export const EMAIL_PAUSABLE_STATUSES = ["scheduled", "open", "overdue", "partially_paid", "under_negotiation"];
export const EMAIL_ELIGIBLE_STATUSES = ["open", "overdue", "partially_paid"];
export const PREVISTO_STATUSES       = ["scheduled", "open", "overdue", "partially_paid", "paid", "under_negotiation"];
```

**Quando o menu `⋯` inteiro fica desabilitado:** status `paid`, `waived` ou
`cancelled` **e** sem link de pagamento copiável. O trigger vira
`cursor-not-allowed text-ink-disabled`.

### Como o status muda

| De | Para | Gatilho |
|---|---|---|
| — | `scheduled` / `open` / `overdue` | ativação do contrato ou rodada diária: a data decide (§3) |
| `scheduled` | `open` | rodada diária, quando o vencimento entra na janela de 15 dias |
| `open` | `overdue` | rodada diária, no dia seguinte ao vencimento |
| pagável | `partially_paid` | pagamento registrado que **não** cobre o saldo |
| pagável | `paid` | pagamento que cobre o saldo, **ou** "Quitar por acordo" |
| `overdue` | `under_negotiation` | menu `⋯` → Marcar em negociação |
| `under_negotiation` | `overdue` | menu `⋯` → Reverter negociação |
| pagável | `waived` | menu `⋯` → Isentar (com motivo obrigatório) |
| `scheduled` / `open` | `cancelled` | encerramento do contrato, para competências futuras |

**Uma cobrança `paid` nunca volta atrás.** É uma regra estrutural do produto: a
correção é feita por evento compensatório, não por edição. Isso está garantido no
banco, não só na UI.

---

## 2 · Contrato: status × rótulo × cor

| Status | Rótulo | Variante | Fundo / texto | Observação |
|---|---|---|---|---|
| `draft` | **Rascunho** | `dashed` | transparente, borda tracejada `#c2c9d8`, texto `#66708c` | é um `contract_draft`, não um contrato |
| `pending_review` | **Em revisão** | `warning` | `#fef3df` / `#b26e05` | existe no enum; nenhum fluxo da UI produz |
| `scheduled` | **Agendado** | `info` | `#e8eefd` / `#2140d6` | existe no enum; nenhum fluxo da UI produz |
| `active` | **Ativo** | `success` | `#e4f6ec` / `#12a150` | |
| `suspended` | **Suspenso** | `warning` | `#fef3df` / `#b26e05` | existe no enum; nenhum fluxo da UI produz |
| `termination_in_progress` | **Encerrando** | `orange` | `#fdeee0` / `#c2611a` | existe no enum; nenhum fluxo da UI produz |
| **`ending`** ⚑ | **Encerrando** | `orange` | `#fdeee0` / `#c2611a` | **não existe no banco** — derivado na exibição |
| `terminated` | **Encerrado** | `archived` | `#e9ecf2` / `#8a93a8` | |
| `completed` | **Concluído** | `info` | `#e8eefd` / `#2140d6` | existe no enum; nenhum fluxo da UI produz |
| `cancelled` | **Cancelado** | `danger` | `#fdebea` / `#e0342c` | |

⚑ **A regra do "Encerrando":**

```ts
export function contractDisplayStatus(status: string, endDate: string | null, today: Date): string {
  if (status === "active" && endDate && parseISODate(endDate) <= addDays(startOfDay(today), 60)) {
    return "ending";
  }
  return status;
}
```

Contrato `active` com `end_date` em até **60 dias** exibe **"Encerrando"** em laranja.
O banco continua dizendo `active`. Aparece na lista de contratos e no detalhe;
**não** aparece nas tabelas de contratos dentro de `/pessoas/[id]` e `/imoveis/[id]`,
que usam o status bruto.

**Badges independentes do status**, no cabeçalho do detalhe (podem coexistir):

| Badge | Variante | Quando |
|---|---|---|
| `Emails pausados` | `warning` | `contracts.emails_paused = true` |
| `Pagamento direto` | `warning` | `contracts.mp_paused = true` |

---

## 3 · Imóvel: status × rótulo × cor × quem controla

| Status | Rótulo | Variante | Fundo / texto | Editável no formulário? |
|---|---|---|---|---|
| `draft` | **Rascunho** | `dashed` | transparente + tracejado | ✅ sim |
| `available` | **Disponível** | `success` | `#e4f6ec` / `#12a150` | ✅ sim |
| `reserved` | **Reservado** | `purple` | `#efeafd` / `#7c5cf0` | ❌ travado |
| `rented` | **Alugado** | `info` | `#e8eefd` / `#2140d6` | ❌ travado (ativação de contrato) |
| `unavailable` | **Indisponível** | `neutral` | `#eef1f6` / `#66708c` | ❌ travado (encerramento de contrato) |
| `maintenance` | **Manutenção** | `warning` | `#fef3df` / `#b26e05` | ❌ travado |
| `archived` | **Arquivado** | `archived` | `#e9ecf2` / `#8a93a8` | ❌ travado |

O formulário de imóvel **só sabe escrever `draft` e `available`**. Quando o imóvel
está em qualquer outro status, o campo vira um badge somente leitura com a frase:

> Este status é controlado pelo ciclo de vida do contrato e não pode ser alterado por aqui.

**Duas transições automáticas visíveis na demonstração:**

- ativar contrato: `available` → **`rented`**
- encerrar contrato: `rented` → **`unavailable`** (e o imóvel **não** volta a ser
  anunciado automaticamente — o dialog de encerramento avisa isso em maiúsculas)

---

## 4 · Intensidade de atraso

O `LateBadge` escala de cor com os dias. Faixas exatas:

```ts
export function lateIntensity(days: number): LateIntensity {
  if (days >= 10) return "days10plus";
  if (days >= 6)  return "days6to9";
  if (days >= 2)  return "days2to5";
  return "day1";
}
```

| Dias de atraso | Intensidade | Variante | Fundo | Texto | Leitura visual |
|---|---|---|---|---|---|
| 1 | `day1` | `warning` | `#fef3df` | `#b26e05` | âmbar suave |
| 2–5 | `days2to5` | `orange` | `#fdeee0` | `#c2611a` | laranja |
| 6–9 | `days6to9` | `danger` | `#fdebea` | `#e0342c` | vermelho suave |
| ≥ 10 | `days10plus` | `dangerSolid` | **`#e0342c`** | **`#ffffff`** | **vermelho sólido, texto branco** |

Texto do badge: `"1 dia"` (singular) ou `"12 dias"`.

O salto visual dos 9 para os 10 dias — de vermelho suave para vermelho preenchido —
é o momento mais forte da fila de inadimplência. Vale animar.

---

## 5 · O link de pagamento — os cinco estados

Esta é a regra mais sutil do produto e a mais fácil de representar errado.

O botão/menu de pagamento só aparece quando `paymentUrlFor` devolve uma URL:

```ts
export function paymentUrlFor(i: PaymentLinkInput): string | null {
  if (!i.agencyMpEnabled || i.contractMpPaused) return null;
  if (!i.mpTicketUrl || i.mpLinkAmount == null) return null;
  const saldo = roundMoney(Number(i.saldo));
  if (saldo <= 0) return null;
  return roundMoney(Number(i.mpLinkAmount)) === saldo ? i.mpTicketUrl : null;
}
```

| # | Situação | `paymentUrlFor` | Sublinha em `/cobrancas` | Item "Copiar link" no `⋯` | Botão no email |
|---|---|---|---|:--:|---|
| 1 | Agência com MP **desligado** | `null` | *(nada)* | não | painel **"Como pagar"** com as instruções manuais |
| 2 | Contrato com **pagamento direto** ligado | `null` | *(nada)* | não | painel "Como pagar" |
| 3 | Nunca houve link para esta cobrança | `null` | *(nada)* | não | painel "Como pagar" |
| 4 | **Link defasado** — existe checkout, mas ele cobra um valor diferente do saldo atual | `null` | *(nada)* | não | painel "Como pagar" |
| 5 | **Link vigente** — checkout cobra **exatamente** o saldo atual | a URL | **"Link de pagamento"** | **sim** | **botão colorido** "Pagar com boleto ou Pix" / "Regularizar pagamento" |

**Por que igualdade exata e não "existe link":** o checkout é de valor fixo. Entre a
emissão e o email pode ter entrado uma baixa parcial, ou o valor pode ter sido
editado. Mandar o inquilino pagar um boleto errado é pior do que mandá-lo pagar
manualmente. O comentário do código é explícito sobre isso.

**Estado adicional, independente dos cinco acima:**

| Situação | Sublinha em `/cobrancas` |
|---|---|
| Existe ao menos uma baixa com origem Mercado Pago | **"Pagamento via Mercado Pago"** |

Essa sublinha tem **prioridade** sobre "Link de pagamento" quando as duas se
aplicariam. E ela afirma só que *houve* um pagamento pelo MP — **não** que a cobrança
está quitada. O badge de status acima é quem diz isso.

**Uma cobrança isentada ou cancelada nunca oferece link**, mesmo que as colunas do
banco ainda tenham uma URL — há um filtro adicional por `PAYABLE_STATUSES` antes de
chamar `paymentUrlFor`.

### O que faz um link nascer

Uma rodada automática diária (07:00 no horário de Brasília) emite ou corrige links,
e ela **só olha** para cobranças dentro deste recorte, todos os cinco filtros ao
mesmo tempo:

1. agência com Mercado Pago **ligado**;
2. agência **com credenciais** de recebimento cadastradas;
3. contrato **ativo**, não excluído e **sem** "pagamento direto";
4. cobrança em `open` / `overdue` / `partially_paid`;
5. saldo devedor **maior que zero**.

Cobrança que sai desse recorte — quitada, isentada, de contrato encerrado — **não é
mais corrigida**, nunca. Isso está documentado no código com essa ênfase porque é
uma armadilha fácil de descrever errado.

---

## 6 · Os gates dos interruptores de Configurações

### 6.1 Switch do Mercado Pago

```ts
const blockedFromEnabling = !mpEnabled && hasCredentials !== true;
const disabled = pending || blockedFromEnabling;
```

Matriz completa dos 6 estados possíveis:

| `mp_enabled` | `hasCredentials` | Switch | Pode clicar? | Texto em "Conta de recebimento" |
|---|---|---|---|---|
| `false` | `true` | desligado | ✅ **pode ligar** | `Conta vinculada` |
| `false` | `false` | desligado | ❌ **bloqueado** | `Sem credenciais — o suporte configura a conta de recebimento` |
| `false` | `null` | desligado | ❌ **bloqueado** | alerta âmbar de verificação indisponível |
| `true` | `true` | ligado | ✅ pode desligar | `Conta vinculada` |
| `true` | `false` | ligado | ✅ pode desligar | `Sem credenciais — …` |
| `true` | `null` | ligado | ✅ pode desligar | alerta âmbar |

**A assimetria é deliberada:** desligar está **sempre** disponível, inclusive com a
verificação quebrada. Uma falha de leitura não pode trancar o admin do lado de dentro
de uma integração ligada. Mas o alerta avisa que é uma porta de mão única:

> Não foi possível verificar a conta de recebimento agora. Recarregue a página;
> enquanto a verificação não funcionar, a integração não pode ser ligada. Desligar
> continua disponível enquanto ela estiver ligada — mas para religar a verificação
> precisa voltar a funcionar.

**`hasCredentials === null` significa INDETERMINADO, não "não tem".** O código nunca
converte uma falha de leitura em `false` — dizer "sem credenciais" por causa de um
erro transitório seria mentir para o admin sobre a conta dele.

**O parágrafo explicativo muda com o estado**, e as duas versões estão transcritas
em [componentes.md](componentes.md#mp-settings-cardtsx--mpsettingscard).

**A faixa âmbar é incondicional** — aparece nos três estados de credencial:

> Desligar não cancela os links já enviados: quem recebeu um boleto ou Pix ainda
> consegue pagar.

Ela diz só o que é verdade nos três casos. Desligar o interruptor **não** cancela
preferências já criadas no Mercado Pago. Quem quer derrubar links vigentes de um
contrato usa "Ativar pagamento direto ao proprietário" no menu `⋯` daquele contrato.

### 6.2 Checkbox-mestre de emails

Não tem gate: pode ser ligado e desligado a qualquer momento, e é salvo junto com o
resto do formulário no botão "Salvar configurações".

| `emails_enabled` | Efeito |
|---|---|
| `false` (**padrão de fábrica**) | **nenhum email sai** desta agência, independentemente da cadência configurada |
| `true` | a cadência passa a valer |

Banner âmbar que acompanha, sempre visível:

> Interruptor-mestre: com ele desligado, nenhum email é enviado por esta agência.

### 6.3 Os dois níveis de pausa por contrato / por cobrança

Independentes entre si e do interruptor-mestre:

| Nível | Onde se liga | Efeito | Badge visível |
|---|---|---|---|
| Agência | Configurações → checkbox-mestre | corta **todos** os emails da agência | — |
| Contrato | detalhe do contrato → `⋯` → Pausar emails do contrato | corta os emails daquele contrato | `Emails pausados` no cabeçalho |
| Cobrança | `/cobrancas` → `⋯` → Pausar emails | corta os emails daquela cobrança | — (só o rótulo do menu inverte) |
| Contrato (MP) | detalhe → `⋯` → Ativar pagamento direto | corta os **links**, não os emails | `Pagamento direto` no cabeçalho |

"Pagamento direto ao proprietário" é ortogonal à pausa de emails: **a cobrança
continua saindo, só que sem botão de pagar** — com as instruções manuais no lugar.

---

## 7 · Como uma cobrança nasce com o status certo

Regra única de data, usada na simulação do wizard, na ativação e na rodada diária:

```ts
/** Regra ÚNICA de status por data: vencida < hoje ≤ aberta ≤ hoje+15d < agendada. */
export function chargeStatusFor(dueDate: Date, today: Date): "scheduled" | "open" | "overdue" {
  const t = startOfDay(today);
  const d = startOfDay(dueDate);
  if (d < t) return "overdue";
  return d <= addDays(t, OPEN_WINDOW_DAYS) ? "open" : "scheduled";
}
```

| Vencimento | Status inicial | Rótulo |
|---|---|---|
| antes de hoje | `overdue` | **Vencida** |
| hoje até hoje + **15 dias** | `open` | **Aberta** |
| depois de hoje + 15 dias | `scheduled` | **Agendada** |

**Geração da agenda** (`generateChargeSchedule`, o que o wizard simula e a ativação grava):

- máximo de **12 cobranças**;
- uma por competência (mês), começando pela maior entre a primeira competência
  configurada e o mês atual;
- para quando a competência ultrapassa a data de fim do contrato;
- vencimento = `dia de vencimento` daquele mês (por isso o campo é limitado a 1–28:
  "para existir em todos os meses").

**Reposição contínua** (rodada diária): novas cobranças são geradas até um horizonte
de **60 dias**, continuando da última competência já persistida.

> **Garantia visual:** a prévia da etapa 3 do wizard e a agenda que aparece no detalhe
> do contrato depois de ativar são produzidas pela **mesma função**. A simulação não
> "aproxima" o resultado — ela é o resultado.

---

## 8 · Cadência de emails

Uma cobrança recebe **no máximo um email por rodada**. A decisão:

| Momento | Chave | Template | Condição |
|---|---|---|---|
| `hoje` entre `vencimento − N` e `vencimento − 1` | `reminder` | **LEMBRETE** | `daysBefore > 0` e ainda não enviado |
| `hoje == vencimento` | `due_today` | **VENCE HOJE** | `onDueDay` ligado e ainda não enviado |
| `hoje ≥ vencimento + k` | `overdue_{k}` | **EM ATRASO** | `k` é o **maior** valor aplicável da lista `daysAfter` ainda não enviado |

Pré-condições, sempre: **saldo > 0** e status em `open` / `overdue` / `partially_paid`.

Configuração padrão de fábrica: `daysBefore = 3`, `onDueDay = true`,
`daysAfter = [3, 7, 15]`, `sendReceipt = true` — mas com o **interruptor-mestre
desligado**, então nada disso dispara até um admin ligar.

O recibo é um template separado, disparado **na baixa**, não pela rodada.

### Os três selos do email de cobrança

| Estado | Selo | Fundo / texto | Valor exibido | Linha sob o valor |
|---|---|---|---|---|
| `reminder` | **LEMBRETE** | `#EEF1F4` / `#445263` | saldo | `Vence em 05/09/2026` |
| `due_today` | **VENCE HOJE** | `#FBF3E4` / `#7A5410` | saldo | `Vence hoje, 05/09/2026` |
| `overdue` | **EM ATRASO** | `#FAEDE8` / `#8C3A24` | **total atualizado** | `Vencido em 05/08/2026 · há 12 dias` + (se > 0) `R$ 2.450,00 de aluguel + R$ 185,45 de multa e juros` |

Assuntos literais:

- `Aluguel Agosto/2026 — vence em 05/09`
- `Aluguel Agosto/2026 vence hoje (05/09)`
- `Aluguel Agosto/2026 em aberto — evite encargos maiores`
- `Pagamento confirmado — Aluguel Agosto/2026` *(recibo)*

Fechamento do email de atraso é diferente dos outros: em vez da linha genérica de
dúvidas, vem **"Dificuldade para pagar? Fale com a gente — buscamos uma alternativa
juntos."** Tom deliberadamente conciliador.

---

## 9 · Valor atualizado (multa e juros)

```ts
export function lateCharges(i: LateChargesInput): LateCharges {
  if (i.daysLate <= 0) return { fine: 0, interest: 0, total: roundMoney(i.saldo) };
  const fine     = roundMoney((i.saldo * i.finePercent) / 100);
  const interest = roundMoney(((i.saldo * i.interestPercent) / 100) * (i.daysLate / 30));
  return { fine, interest, total: roundMoney(i.saldo + fine + interest) };
}
```

- **multa**: percentual fixo sobre o saldo, aplicado uma vez;
- **juros**: percentual **ao mês**, pro-rata por dia (`dias / 30`);
- é **informativo**: o valor gravado da cobrança nunca muda por atraso.

**Regra de exibição honesta:** o rótulo "atualizado com multa e juros" e a sublinha
`original R$ X` **só aparecem quando há algo a somar**. Como os percentuais têm
padrão `0` no cadastro do contrato, uma agência que não configurou nada veria dois
números idênticos sob um rótulo que estaria mentindo. Então ele some.

O cálculo é feito **por cobrança**, cada uma com seu próprio saldo e seu próprio
atraso — nunca um cálculo agregado com o maior atraso, que superestimaria os juros
das cobranças mais novas.

---

## 10 · Critério único de "vencida em aberto"

O mesmo em quatro lugares: badge da navegação, KPI "Vencido", fila de
`/inadimplencia` e banner do detalhe do contrato.

```ts
export function isOverdueOpen(status: string, dueDate: Date, today: Date): boolean {
  if (status === "overdue") return true;
  return status === "partially_paid" && startOfDay(dueDate) < startOfDay(today);
}
```

> status `overdue` **OU** status `partially_paid` com vencimento já passado.

`partially_paid` implica saldo > 0 por definição — se o saldo tivesse zerado, o
status seria `paid`.

**Consequência para a demonstração:** uma cobrança **parcialmente paga e vencida**
aparece na fila de inadimplência com o badge âmbar "Parcialmente paga" em
`/cobrancas`, mas na fila de inadimplência ela é contada como vencida. Não é
inconsistência — é o mesmo critério visto de dois ângulos.

---

## 11 · Papéis e permissões

Dois papéis. `admin` e `operator`.

| Capacidade | Admin | Operador |
|---|:--:|:--:|
| Rótulo no header | **Gestora** | **Operador** |
| Ver Dashboard, Pessoas, Imóveis, Contratos, Cobranças, Inadimplência | ✅ | ✅ |
| Criar/editar pessoa, imóvel, contrato | ✅ | ✅ |
| Registrar pagamento, isentar, editar cobrança, quitar por acordo | ✅ | ✅ |
| Registrar contato | ✅ | ✅ |
| Pausar/retomar emails (contrato e cobrança) | ✅ | ✅ |
| Ativar/desativar pagamento direto ao proprietário | ✅ | ✅ |
| **Ver pill "Configurações" e "Auditoria"** | ✅ | ❌ |
| **Acessar `/configuracoes` e `/auditoria`** | ✅ | ❌ redireciona para `/` |
| **Encerrar contrato** | ✅ | ❌ item não aparece no menu |
| **Ligar/desligar Mercado Pago** | ✅ | ❌ |

Mensagens de negação (quando o servidor barra):
`"Apenas administradores podem encerrar contratos."` ·
`"Apenas administradores."` ·
`"Apenas administradores alteram a integração de pagamento."`

> **Nota para a animação:** o produto tem apenas esses dois papéis hoje. Não existe
> tela de gestão de usuários, nem convite, nem troca de papel pela interface.

---

## 12 · Formatação brasileira — os helpers reais

Todos em `src/lib/format.ts`, todos baseados em `Intl` com locale `pt-BR`.

### 12.1 Moeda

```ts
const brl = new Intl.NumberFormat("pt-BR", { style: "currency", currency: "BRL" });

export function formatCurrencyBRL(value: number): string {
  // Intl usa espaço não separável (U+00A0) após "R$"; normalizamos para espaço comum
  return brl.format(value).replace(/ /g, " ");
}
```

| Entrada | Saída |
|---|---|
| `2450` | `R$ 2.450,00` |
| `2450.5` | `R$ 2.450,50` |
| `84500` | `R$ 84.500,00` |
| `0` | `R$ 0,00` |
| `-120` | `-R$ 120,00` |

**Sempre com duas casas.** Sempre `R$` + espaço **comum** (não é espaço fino nem
`&nbsp;`). Milhar com ponto, decimal com vírgula.

Variante sem símbolo, usada nos emails (onde `R$` e o número têm tamanhos e pesos
diferentes na mesma linha):

```ts
export function formatAmountBRL(value: number): string  // 2450 → "2.450,00"
```

### 12.2 Datas

```ts
const dateFmt = new Intl.DateTimeFormat("pt-BR", { day: "2-digit", month: "2-digit", year: "numeric" });
```

| Helper | Entrada | Saída |
|---|---|---|
| `formatDate` | `"2026-08-05"` | `05/08/2026` |
| `formatDateShort` | `"2026-08-05"` | `05/08` *(só nos assuntos de email)* |
| `formatCompetence` | `Date` de agosto/2026 | `agosto/2026` *(minúsculo — corre no meio de frases)* |
| `formatCompetenceTitle` | idem | `Agosto/2026` *(maiúsculo — nos emails, isolado como rótulo)* |

**Armadilha que o código evita e que a demonstração deve respeitar:** strings
`"YYYY-MM-DD"` são convertidas por **partes locais**, nunca por `new Date(string)`
— que assume UTC e volta um dia em fuso negativo. Uma data de vencimento nunca deve
aparecer com um dia de diferença.

**Formato relativo** (timelines e rascunhos): `Intl.RelativeTimeFormat("pt-BR",
{ numeric: "auto" })`, escalonando segundo → minuto → hora → dia → semana → mês → ano.
Exemplos reais: `agora`, `há 5 minutos`, `há 3 dias`, `há 2 semanas`, `há 5 meses`.

**Data e hora absolutas** (segunda linha da timeline):
`Intl.DateTimeFormat("pt-BR", { day, month, year: "numeric", hour, minute: "2-digit" })`
→ `11/08/2026 09:42`.

**Mês abreviado** (caixinha de data do card "Próximos vencimentos"):
`Intl.DateTimeFormat("pt-BR", { month: "short" })` sem o ponto, em caixa alta → `AGO`.

**Mês curto com ano** (rótulo do delta do KPI):
`abbrev/YY` → `jul/26`.

### 12.3 Documentos

```ts
export function formatDocument(value: string, kind: "pf" | "pj"): string {
  const d = onlyDigits(value);
  if (kind === "pf" && d.length === 11)
    return d.replace(/(\d{3})(\d{3})(\d{3})(\d{2})/, "$1.$2.$3-$4");
  if (kind === "pj" && d.length === 14)
    return d.replace(/(\d{2})(\d{3})(\d{3})(\d{4})(\d{2})/, "$1.$2.$3/$4-$5");
  return value;
}
```

| Tipo | Máscara | Exemplo de forma |
|---|---|---|
| CPF (PF) | `000.000.000-00` | 11 dígitos |
| CNPJ (PJ) | `00.000.000/0000-00` | 14 dígitos |

Com um número parcial de dígitos, o valor é devolvido **como digitado** — a máscara
só entra quando o documento está completo. É por isso que o campo parece "montar" a
formatação no último caractere.

**Validação:** há verificação real de dígito verificador para CPF e CNPJ, e
repetições (`111.111.111-11`) são rejeitadas. Erros exibidos: `"CPF inválido."` /
`"CNPJ inválido."` O documento é **opcional** no cadastro, mas se preenchido tem de
ser válido.

### 12.4 Telefone

**Não há máscara de telefone.** O campo é livre — a agência digita como quiser, e o
que estiver lá é exibido literalmente. Os placeholders sugerem os formatos:
`(19) 99999-9999` no cadastro rápido, `(21) 3210-4455` em Configurações.

O único tratamento é a conversão para link discável nos emails:

```ts
export function phoneHref(phone: string | null | undefined): string | null {
  if (!phone) return null;
  const digits = phone.replace(/\D/g, "");
  if (digits.length < 8) return null;
  if (digits.length === 10 || digits.length === 11) return `+55${digits}`;
  return `+${digits}`;
}
```

`(21) 3210-4455` → `+552132104455`. Menos de 8 dígitos → sem link, só texto.

### 12.5 Percentuais

Três formatos convivem, cada um no seu lugar:

| Contexto | Formato | Exemplo |
|---|---|---|
| KPIs do dashboard | uma casa decimal fixa, vírgula | `73,8%` |
| Legenda do donut | até uma casa, sem forçar | `71,4%` · `50%` |
| Taxas do contrato | remove zeros à direita | `10%` · `2,5%` |
| Ocupação em `/imoveis` | uma casa fixa, vírgula | `71,4%` |

### 12.6 Tamanho de arquivo

```ts
function formatFileSize(bytes: number | null): string {
  if (bytes == null) return "tamanho desconhecido";
  if (bytes < 1024) return `${bytes} B`;
  const kb = bytes / 1024;
  if (kb < 1024) return `${kb.toFixed(1).replace(".", ",")} KB`;
  return `${(kb / 1024).toFixed(1).replace(".", ",")} MB`;
}
```

`340,0 KB` · `1,2 MB` — **vírgula decimal**, uma casa.

### 12.7 Plurais

O produto conjuga plural manualmente em toda contagem visível. Exemplos literais:

| Padrão | 1 | N |
|---|---|---|
| dias de atraso | `1 dia` | `12 dias` |
| cobranças | `1 cobrança` | `38 cobranças` |
| vencidas | `1 cobrança vencida` | `3 cobranças vencidas` |
| emails | `1 email enviado` | `4 emails enviados` |
| contratos | `1 contrato` | `2 contratos` |
| imóveis (donut) | `1 imóvel` | `14 imóveis` |
| cadastros | `1 cadastro` | `24 cadastros` |
| outras vencidas | `+ 1 outra vencida` | `+ 3 outras vencidas` |
| futuras canceladas | `1 cobrança futura foi cancelada` | `3 cobranças futuras foram canceladas` |

Nunca aparece "1 dia(s)" ou "1 cobrança(s)". A única notação com parêntese é
proposital: **Inquilino(a)**, **Proprietário(a)**, **Locador(a)**, **Locatário(a)** —
porque o cadastro não tem campo de gênero e o rótulo não presume.

---

## 13 · Mensagens de erro — catálogo literal

Para não inventar copy na animação.

### Erros de campo

| Campo | Mensagem |
|---|---|
| Nome | `Informe o nome completo.` |
| CPF | `CPF inválido.` |
| CNPJ | `CNPJ inválido.` |
| Email | `Email inválido.` |
| Papéis | `Selecione ao menos um papel.` |
| Logradouro | `Informe o logradouro.` |
| Número | `Informe o número.` |
| Cidade | `Informe a cidade.` |
| UF | `UF com 2 letras.` |
| Código do imóvel | `Informe o código.` |
| Proprietário | `Selecione o proprietário.` |
| Imóvel (wizard) | `Selecione o imóvel.` |
| Locador (wizard) | `Selecione o locador.` |
| Locatário (wizard) | `Selecione o locatário.` |
| Data de início | `Informe a data de início.` |
| Primeira competência | `Informe a primeira competência.` |
| Dia de vencimento | `Entre 1 e 28.` |
| Valor | `Valor deve ser maior que zero.` |
| Data de fim | `Fim antes do início.` |
| Data do pagamento | `Informe a data.` |

### Erros de formulário

| Contexto | Mensagem |
|---|---|
| Pessoa / imóvel | `Não foi possível salvar. Tente novamente.` |
| Rascunho de contrato | `Não foi possível salvar o rascunho.` |
| Ativação de contrato (falha genérica) | `Não foi possível ativar o contrato. Tente novamente.` |
| Ativação — imóvel já ocupado | `Este imóvel já possui um contrato ativo.` |
| Ativação — imóvel sumiu | `Imóvel não encontrado.` |
| Ativação — imóvel mudou de status | `Imóvel não está disponível para ativação.` |
| Ativação — pessoa sumiu | `Locador ou locatário não encontrado.` |
| Encerramento | `Não foi possível encerrar o contrato. Tente novamente.` |
| Contrato não ativo | `Este contrato não está ativo — não é possível encerrá-lo.` |
| Permissão | `Apenas administradores podem encerrar contratos.` |
| Pagamento | `Não foi possível registrar o pagamento. Tente novamente.` |
| Status inválido | `Esta cobrança não aceita mais pagamentos.` |
| Quitar por acordo | `Não foi possível quitar a cobrança. Tente novamente.` |
| Status inválido (acordo) | `Esta cobrança não pode ser quitada por acordo no status atual.` |
| Pausa de emails | `Não foi possível atualizar o envio de emails. Tente novamente.` |
| Pagamento direto | `Não foi possível atualizar o pagamento direto. Tente novamente.` |
| Configurações | `Não foi possível salvar as configurações. Tente novamente.` |
| Mercado Pago | `Não foi possível atualizar a integração de pagamento. Tente novamente.` |
| Documento | `Falha ao registrar o documento.` |
| Comprovante | `Falha ao anexar o comprovante.` |
| Clipboard | `Não foi possível copiar o link para a área de transferência.` |

### Erros de upload

`Selecione um arquivo.` · `Arquivo maior que 10 MB.` · `Use PDF, JPG ou PNG.` ·
`Falha no upload. Tente novamente.` · `Não foi possível iniciar o upload. Tente novamente.` ·
`Não foi possível gerar o link de download. Tente novamente.`

### Erros de carregamento de página

| Tela | Mensagem |
|---|---|
| Dashboard | `Não foi possível carregar os indicadores. Tente novamente.` |
| Listas (pessoas, imóveis, contratos, cobranças, inadimplência) | `Não foi possível carregar a lista. Tente novamente.` |
| Detalhe de imóvel | `Não foi possível carregar este imóvel. Tente novamente.` |
| Detalhe de contrato | `Não foi possível carregar este contrato. Tente novamente.` |
| Agenda | `Não foi possível carregar a agenda de cobranças.` |
| Pagamentos do contrato | `Não foi possível carregar os pagamentos deste contrato.` |
| Ações indisponíveis | `Não foi possível carregar os pagamentos — as ações de pagamento estão indisponíveis.` |
| Documentos | `Não foi possível carregar os documentos deste contrato.` |
| Timeline | `Não foi possível carregar o histórico de eventos.` |
| Histórico de contratos | `Não foi possível carregar o histórico de contratos.` |
| Configurações | `Não foi possível carregar as configurações. Tente novamente.` |
| Proprietários (form de imóvel) | `Não foi possível carregar a lista de proprietários. Tente novamente.` |
| Imóveis (wizard) | `Não foi possível carregar a lista de imóveis. Tente novamente.` |
| Pessoas (wizard) | `Não foi possível carregar a lista de pessoas. Tente novamente.` |
| Rascunho | `Rascunho não encontrado. Ele pode ter sido excluído.` |

**Um princípio transversal, e ele é visível na tela:** erro de consulta **nunca** vira
estado vazio. Se a consulta falhou, aparece um alerta vermelho — nunca "nenhum
resultado", que seria um falso negativo. Vale a pena mostrar isso numa animação: é a
diferença entre um produto que admite não saber e um que finge.

---

[◄ Telas](telas.md) · [Dados de demonstração ►](dados-demo.md)


---

<!-- ===== dados-demo.md ===== -->

# Dados de demonstração

> Conjunto **inteiramente fictício**, coerente de ponta a ponta: os mesmos nomes,
> códigos e valores atravessam dashboard, cobranças, inadimplência, contratos,
> pessoas e imóveis. Todos os números derivados (KPIs, contagens, percentuais) já
> estão calculados aqui, com a fórmula ao lado.

[◄ Voltar ao índice](README.md)

---

## Antes de tudo: as regras de segurança deste dataset

| Item | Como foi tratado |
|---|---|
| **Nomes** | Inventados. Combinações de nomes e sobrenomes brasileiros comuns, sem correspondência com pessoa ou empresa conhecida. Qualquer coincidência é acidental. |
| **CPF / CNPJ** | Strings com **formato válido** e conteúdo inventado. **Não passam no dígito verificador** — de propósito. Servem para desenhar a tela; não devem ser digitados no formulário real, que os rejeitaria. |
| **Emails** | Todos em `example.com`, domínio **reservado pela IANA (RFC 2606)**. É impossível que uma mensagem chegue a alguém. |
| **Telefones** | Inventados, no formato brasileiro. Não corresponde a linha real conhecida. |
| **Endereços** | Logradouros públicos reais de Campinas/SP com **numeração inventada**. Rua é infraestrutura pública, não dado pessoal; a combinação rua + número aqui não aponta para ninguém. |
| **Valores** | Aluguel residencial e comercial plausível para Campinas/SP, faixa R$ 1.650 – R$ 4.150. |
| **Datas** | **Relativas a "hoje" (D0)**, para a demonstração não envelhecer. Ver §1. |

Nenhum dado deste arquivo veio de cliente, produção ou base real. **Nenhum
identificador, token, chave, credencial, URL interna ou domínio do produto aparece
aqui** — o handoff inteiro é livre de segredo por construção.

---

## 1 · Como ler as datas

Tudo é expresso em relação a dois marcos:

- **D0** = o dia da demonstração ("hoje").
- **M0** = o mês de D0 (a competência corrente). `M−1` é o mês anterior, `M+1` o seguinte.

**Convenção adotada: D0 é o dia 18 do mês.** Essa escolha não é cosmética — ela é o
que faz o conjunto exercitar, ao mesmo tempo, cobranças vencidas, pagas, parcialmente
pagas e a vencer. Se você mudar o dia, os status mudam junto (a regra está em
[estados-e-regras.md §7](estados-e-regras.md#7--como-uma-cobrança-nasce-com-o-status-certo)).

**Exemplo concreto**, caso prefira datas fixas na tela: com D0 = **18/09/2026**,
M0 = setembro/2026 e M−1 = agosto/2026. Todas as tabelas abaixo funcionam sem
alteração — basta somar os deslocamentos.

---

## 2 · A imobiliária

| Campo | Valor |
|---|---|
| Nome | **Aurora Gestão Imobiliária** |
| Cidade | Campinas / SP |
| CRECI | `SP J-31842` |
| Endereço (rodapé dos emails) | `Av. Barão de Itapura, 850 · sala 12 · Botafogo, Campinas/SP` |
| Telefone | `(19) 3255-7140` |
| Email de resposta (reply-to) | `financeiro@example.com` |
| Cor da marca (emails) | `#1F4E5F` |
| CNPJ | `19.634.028/0001-77` |
| Instruções de pagamento manuais | *"Pix CNPJ 19.634.028/0001-77 (Aurora Gestão Imobiliária) ou transferência para Banco 999, ag. 0184, c/c 12345-6. Envie o comprovante para financeiro@example.com."* |
| Emails automáticos | **Ligados** (`emails_enabled = true`) |
| Mercado Pago | **Desligado** (`mp_enabled = false`) — ver §9 para a variante ligada |

Ela aparece no seletor de agência do header (estático) e como assinante dos emails.

### Usuários

| Nome | Papel | Rótulo no header | Iniciais |
|---|---|---|---|
| **Marina Prado Bandeira** | `admin` | **Gestora** | MB |
| **Caio Bittencourt Sá** | `operator` | **Operador** | CS |

Marina é quem aparece na maioria das telas. Caio existe para demonstrar o recorte de
permissão: sem as pills "Configurações" e "Auditoria", sem "Encerrar contrato".

---

## 3 · Pessoas — 11 cadastros

Subtítulo real da tela `/pessoas`:

> **11 cadastros · 7 inquilinos · 4 proprietários**

### Proprietários (4)

| # | Nome | Tipo | Documento | Email | Telefone | Contratos ativos |
|---|---|---|---|---|---|---|
| P1 | **Helena Prado Vasconcelos** | PF | `312.457.890-11` | `helena.vasconcelos@example.com` | `(19) 98812-4407` | 3 |
| P2 | **Otávio Meireles Cunha** | PF | `487.203.615-42` | `otavio.cunha@example.com` | `(19) 99163-2288` | 2 |
| P3 | **Construtora Vale do Atibaia Ltda** | **PJ** | `41.207.365/0001-88` | `contato@example.com` | `(19) 3242-9970` | 2 |
| P4 | **Lúcia Amorim Ferraz** | PF | `225.881.407-63` | `lucia.ferraz@example.com` | `(19) 98476-1052` | 0 |

### Inquilinos (7)

| # | Nome | Tipo | Documento | Email | Telefone | Contratos ativos |
|---|---|---|---|---|---|---|
| I1 | **Beatriz Nunes Chaves** | PF | `194.630.782-05` | `beatriz.chaves@example.com` | `(19) 99204-3316` | 1 |
| I2 | **Rodrigo Sampaio Leal** | PF | `358.914.226-70` | `rodrigo.leal@example.com` | `(19) 98357-6621` | 1 |
| I3 | **Camila Rezende Whitaker** | PF | `671.049.583-24` | `camila.whitaker@example.com` | `(19) 99711-8409` | 1 |
| I4 | **Studio Farol Design ME** | **PJ** | `28.914.630/0001-52` | `financeiro.farol@example.com` | `(19) 3391-2264` | **2** |
| I5 | **Thiago Albuquerque Mota** | PF | `903.276.145-38` | *(nenhum)* ⚠ | `(19) 98120-7743` | 1 |
| I6 | **Priscila Tavares Bonfim** | PF | `540.812.379-16` | `priscila.bonfim@example.com` | `(19) 99588-2074` | 1 |
| I7 | **Rafael Queiroz Bastos** | PF | `786.135.240-97` | `rafael.bastos@example.com` | `(19) 98903-5518` | **0** |

I7 é o **ex-inquilino**: o contrato dele foi encerrado (CT-08). O cadastro fica —
nada é apagado no produto —, com 0 contratos ativos e um histórico visível no detalhe.

⚠ **Thiago Albuquerque Mota não tem email cadastrado — de propósito.** É ele que faz
aparecer o badge âmbar **"Sem email"** na fila de inadimplência, o sinal de que a
cobrança automática não alcança aquela pessoa. Não remova esse buraco: ele é uma das
melhores coisas para demonstrar.

**Lembretes visuais:**
- P3 e I4 são **pessoas jurídicas** → avatar **quadrado arredondado**, segunda linha
  "Pessoa jurídica", documento com máscara de CNPJ.
- Todos os outros são PF → avatar **círculo**.
- A cor de cada avatar é derivada por hash do nome (soma dos códigos dos caracteres,
  módulo 8, sobre a paleta de [design-system.md §13](design-system.md#13-sistema-de-cor-dos-avatares)).
  **Não calculei os índices aqui** — calcule ou escolha qualquer entrada da paleta,
  desde que a mesma pessoa tenha sempre a mesma cor em todas as telas.
- Lúcia (P4) tem 0 contratos ativos: é a proprietária dos imóveis vagos.

---

## 4 · Imóveis — 11 cadastrados

Subtítulo real da tela `/imoveis`:

> **11 na carteira ativa · 63,6% de ocupação**

*(cálculo da tela: `alugados ÷ (total − arquivados)` = 7 ÷ 11 = 63,6%)*

| Código | Endereço | Bairro / cidade | CEP | Proprietário | Status |
|---|---|---|---|---|---|
| `IMV-0001` | Rua Coronel Quirino, 1420 · ap 51 | Cambuí — Campinas/SP | 13025-002 | Helena (P1) | **Alugado** |
| `IMV-0002` | Rua Dr. Sampaio Ferraz, 340 | Cambuí — Campinas/SP | 13024-430 | Helena (P1) | **Alugado** |
| `IMV-0003` | Rua Maria Monteiro, 1275 · ap 902 | Cambuí — Campinas/SP | 13025-152 | Otávio (P2) | **Alugado** |
| `IMV-0004` | Av. Norte-Sul, 1750 · sala 304 | Nova Campinas — Campinas/SP | 13092-108 | Vale do Atibaia (P3) | **Alugado** |
| `IMV-0005` | Rua Sales de Oliveira, 233 | Vila Industrial — Campinas/SP | 13035-270 | Otávio (P2) | **Alugado** |
| `IMV-0006` | Rua Luverci Pereira de Souza, 410 | Vila Brandina — Campinas/SP | 13092-670 | Helena (P1) | **Alugado** |
| `IMV-0007` | Av. Andrade Neves, 980 · sala 7 | Centro — Campinas/SP | 13013-160 | Vale do Atibaia (P3) | **Alugado** |
| `IMV-0008` | Rua Dr. Emílio Ribas, 615 · casa 2 | Cambuí — Campinas/SP | 13025-141 | Lúcia (P4) | **Disponível** |
| `IMV-0009` | Rua Tiradentes, 88 | Guanabara — Campinas/SP | 13023-190 | Lúcia (P4) | **Disponível** |
| `IMV-0010` | Rua Conceição, 1425 · ap 44 | Centro — Campinas/SP | 13010-916 | Otávio (P2) | **Indisponível** |
| `IMV-0011` | Av. Barão de Itapura, 2210 | Botafogo — Campinas/SP | 13073-300 | Lúcia (P4) | **Rascunho** |

**Por que `IMV-0010` está Indisponível:** o contrato dele foi encerrado (CT-08, §5).
Encerrar um contrato leva o imóvel a `Indisponível`, e ele **não volta a ser
anunciado automaticamente** — o dialog de encerramento avisa isso em letras
maiúsculas. É um estado real e vale demonstrar.

**Nenhum imóvel em Manutenção, Reservado ou Arquivado.** Isso não é esquecimento: o
formulário de imóvel só sabe escrever `Rascunho` e `Disponível`, e nenhum fluxo de
tela leva a `maintenance`, `reserved` ou `archived`. Os rótulos existem no vocabulário
de status (e nos chips de filtro), mas o produto de hoje não os produz sozinho.

### Contagens derivadas

| Onde | Nome do número | Valor | Fórmula |
|---|---|---|---|
| Chip do dashboard | Imóveis | **11** | todos os não excluídos |
| Chip do dashboard | Carteira ativa | **10** | status ∉ {rascunho, arquivado} |
| Subtítulo de `/imoveis` | na carteira ativa | **11** | status ≠ arquivado |
| Subtítulo de `/imoveis` | de ocupação | **63,6%** | 7 alugados ÷ 11 |
| Donut do dashboard | total no centro | **10** | mesma base do chip "Carteira ativa" |

> ⚠️ **Isso não é erro de transcrição: o produto tem hoje duas definições de "carteira
> ativa".** O dashboard exclui rascunhos e arquivados (10); a página de imóveis exclui
> só arquivados (11). Conferi as duas implementações. Reproduza os dois números como
> estão — se a animação "consertar" a divergência, ela estará mostrando um produto que
> não existe.

### Chips de filtro em `/imoveis`

| Chip | Contagem |
|---|---|
| Todos | 11 |
| Alugados | 7 |
| Disponíveis | 2 |
| Manutenção | 0 |
| *Mais ▾* → Reservados | 0 |
| *Mais ▾* → Indisponíveis | 1 |
| *Mais ▾* → Rascunhos | 1 |
| *Mais ▾* → Arquivados | 0 |

---

## 5 · Contratos — 8 contratos + 1 rascunho

Abas da tela `/contratos`: **Ativos 7** · **Rascunhos 1** · **Todos 8**

| # | Imóvel | Inquilino | Aluguel | Taxa adm. | Multa | Juros a.m. | Dia venc. | Vigência | Status |
|---|---|---|---|---|---|---|---|---|---|
| CT-01 | IMV-0001 | Beatriz (I1) | **R$ 2.450,00** | 10% | 2% | 1% | **5** | D0−17m → D0+7m | **Ativo** |
| CT-02 | IMV-0002 | Rodrigo (I2) | **R$ 1.980,00** | 10% | 2% | 1% | **10** | D0−11m → **D0+35d** | **Encerrando** ⚑ |
| CT-03 | IMV-0003 | Camila (I3) | **R$ 3.200,00** | 8% | 2% | 1% | **14** | D0−5m → D0+19m | **Ativo** |
| CT-04 | IMV-0004 | Studio Farol (I4) | **R$ 4.150,00** | 12% | 2% | 1% | **16** | D0−23m → *sem prazo* | **Ativo** |
| CT-05 | IMV-0005 | Thiago (I5) | **R$ 1.650,00** | 10% | 0% | 0% | **20** | D0−2m → D0+22m | **Ativo** |
| CT-06 | IMV-0006 | Priscila (I6) | **R$ 2.780,00** | 10% | 2% | 1% | **22** | D0−8m → D0+16m | **Ativo** |
| CT-07 | IMV-0007 | Studio Farol (I4) | **R$ 1.890,00** | 12% | 2% | 1% | **25** | **D0−18d** → D0+24m | **Ativo** |
| CT-08 | IMV-0010 | Rafael (I7) | R$ 2.100,00 | 10% | 2% | 1% | 5 | D0−26m → D0−2m | **Encerrado** |

⚑ **CT-02 é o único que aparece como "Encerrando"** (laranja): está `active` com fim
em 35 dias, dentro da janela de 60. É esse contrato que faz o rodapé do card "Saúde da
operação" dizer **"1 contrato"**.

**Notas de coerência:**
- **CT-05 tem multa e juros em 0%.** É o caso que faz a fila de inadimplência **não**
  mostrar a sublinha "original R$ X" para as cobranças dele. Deliberado: exercita a
  regra de que o rótulo "atualizado com multa e juros" só aparece quando há o que somar.
- **CT-04 é "sem prazo"** (`end_date` nulo) → a coluna Vigência mostra
  `16/…/2024 — sem prazo`.
- **CT-07 começou há 18 dias** — é o contrato novo, o que dá a Studio Farol dois
  imóveis. Sua primeira competência é M0.
- **CT-08 é o contrato encerrado.** Ele não aparece na aba "Ativos", só em "Todos",
  com o badge cinza **Encerrado**. É por causa dele que `IMV-0010` está Indisponível e
  que Rafael (I7) tem 0 contratos ativos.

### O rascunho

| Campo | Valor |
|---|---|
| Imóvel | `IMV-0008` |
| Locatário | *(ainda não escolhido)* → coluna mostra `—` |
| Vigência | início preenchido, fim vazio → `01/…/… — —` |
| Aluguel | R$ 2.300,00 |
| Meta da segunda linha | `atualizado há 2 dias` |
| Etapa salva | 2 (Vigência e valores) |

Na lista, essa linha tem fundo `bg-surface-hint`, badge tracejado **Rascunho** e um
botão **Continuar** no lugar do chevron.

---

## 6 · Cobranças

### 6.1 Competência M−1 (mês anterior) — 6 cobranças

*(CT-07 ainda não existia)*

| Contrato | Inquilino | Valor | Vencimento | Status | Baixas |
|---|---|---|---|---|---|
| CT-01 | Beatriz | R$ 2.450,00 | M−1 dia 5 (**D0−43**) | **Vencida** 🔴 | — |
| CT-02 | Rodrigo | R$ 1.980,00 | M−1 dia 10 | **Paga** | R$ 1.980,00 · Pix · D0−37 |
| CT-03 | Camila | R$ 3.200,00 | M−1 dia 14 | **Paga** | R$ 3.200,00 · Pix · D0−34 |
| CT-04 | Studio Farol | R$ 4.150,00 | M−1 dia 16 | **Paga** | R$ 4.150,00 · Transferência · D0−32 · comprovante PDF |
| CT-05 | Thiago | R$ 1.650,00 | M−1 dia 20 | **Isentada** 🟢 | — · motivo: *"Primeiro mês proporcional já quitado na assinatura do contrato."* |
| CT-06 | Priscila | R$ 2.780,00 | M−1 dia 22 | **Em negociação** 🟣 | — · marcada 5 dias após o vencimento |

### 6.2 Competência M0 (corrente) — 7 cobranças

**D0 = dia 18.**

| Contrato | Inquilino | Valor | Venc. | Δ vs. hoje | Status | Baixas |
|---|---|---|---|---|---|---|
| CT-01 | Beatriz | R$ 2.450,00 | dia 5 | **D0−13** | **Vencida** 🔴 | — |
| CT-02 | Rodrigo | R$ 1.980,00 | dia 10 | **D0−8** | **Parcialmente paga** 🟡 | R$ 800,00 · Pix · D0−6 |
| CT-03 | Camila | R$ 3.200,00 | dia 14 | D0−4 | **Paga** 🟢 | R$ 3.200,00 · Pix · D0−4 |
| CT-04 | Studio Farol | R$ 4.150,00 | dia 16 | D0−2 | **Paga** 🟢 | R$ 4.150,00 · Transferência · D0−2 · comprovante PDF |
| CT-05 | Thiago | R$ 1.650,00 | dia 20 | D0+2 | **Aberta** 🔵 | — |
| CT-06 | Priscila | R$ 2.780,00 | dia 22 | D0+4 | **Aberta** 🔵 | — |
| CT-07 | Studio Farol | R$ 1.890,00 | dia 25 | D0+7 | **Aberta** 🔵 | — |

Subtítulo real de `/cobrancas` com o filtro em M0:

> **7 cobranças em {mês}/{ano} · R$ 18.100,00 previstos**

*(a contagem inclui todos os status; o valor exclui canceladas e isentadas — aqui não
há nenhuma das duas em M0, então os 7 somam)*

### 6.3 Competência M+1 — 7 cobranças, todas **Agendadas**

Todas com vencimento a mais de 15 dias de D0 (o mais próximo é o dia 5, ≈ D0+17).

| Contrato | Valor | Status |
|---|---|---|
| CT-01 … CT-07 | mesmos valores de M0 | **Agendada** 🔘 |

CT-02 termina em D0+35, ainda dentro de M+1 → tem cobrança em M+1 e **não tem em M+2**.

### 6.4 Cobranças **Canceladas** (contrato encerrado)

No detalhe de **CT-08** (encerrado), as três competências posteriores ao mês da saída
aparecem **riscadas**:

| Competência | Valor | Status |
|---|---|---|
| D0−1m | R$ 2.100,00 | **Cancelada** ~~riscada~~ |
| D0 | R$ 2.100,00 | **Cancelada** ~~riscada~~ |
| D0+1m | R$ 2.100,00 | **Cancelada** ~~riscada~~ |

As anteriores continuam **Pagas** — encerrar contrato não apaga dívida nem histórico.

---

## 7 · Números derivados — já calculados

### 7.1 Dashboard, mês M0

| KPI | Valor exibido | Fórmula |
|---|---|---|
| **Previsto no mês** | **R$ 18.100,00** | Σ das 7 cobranças de M0 (nenhuma cancelada/isentada) |
| barra + sublinha | **45,0% recebido no mês** | 8.150 ÷ 18.100 |
| **Recebido** | **R$ 8.150,00** | pagamentos com data em M0: 800 + 3.200 + 4.150 |
| sublinha | **45,0% do previsto** | idem |
| delta | **▼ 12,6% vs. {M−1}** | (8.150 − 9.330) ÷ 9.330 · seta `ArrowDown`, texto em `text-danger-foreground` |
| **Vencido** | **R$ 6.080,00** | 2.450 (CT-01 M−1) + 2.450 (CT-01 M0) + 1.180 (saldo CT-02 M0) |
| sublinha | **33,6% do previsto** + `todas as competências` | 6.080 ÷ 18.100 |
| **Inadimplência** | **33,6%** | mesma fração |
| sublinha | `do previsto no mês` + `todas as competências` | — |

*(Recebido em M−1 = 1.980 + 3.200 + 4.150 = **R$ 9.330,00**)*

> Se preferir a seta **verde** na animação, basta mover a baixa de CT-04 de M−1 para
> M0 (ou reduzir uma baixa de M−1). O delta negativo aqui é honesto: no dia 18 do mês
> boa parte dos vencimentos ainda não chegou.

### 7.2 Chips do dashboard

| Chip | Valor |
|---|---|
| Imóveis | **11** |
| Proprietários | **4** |
| Inquilinos | **7** |
| Carteira ativa *(verde)* | **10** |

### 7.3 Card "Exige atenção hoje"

Badge do título: **3**. Rodapé: **Ver todas (3)**.

| Ordem | Inquilino | Segunda linha (endereço) | Saldo | Atraso |
|---|---|---|---|---|
| 1 | **Beatriz Nunes Chaves** | Rua Coronel Quirino, 1420 | **R$ 2.450,00** | **+43 dias** |
| 2 | **Beatriz Nunes Chaves** | Rua Coronel Quirino, 1420 | **R$ 2.450,00** | **+13 dias** |
| 3 | **Rodrigo Sampaio Leal** | Rua Dr. Sampaio Ferraz, 340 | **R$ 1.180,00** | **+8 dias** |

*(a mesma pessoa aparece duas vezes: são duas competências vencidas do mesmo contrato)*

### 7.4 Card "Próximos vencimentos" (7 dias)

Rodapé: **Ver todos (3)**.

| Caixinha | Inquilino | Código do imóvel | Valor |
|---|---|---|---|
| **20** / *(mês)* | Thiago Albuquerque Mota | `IMV-0005` | R$ 1.650,00 |
| **22** / *(mês)* | Priscila Tavares Bonfim | `IMV-0006` | R$ 2.780,00 |
| **25** / *(mês)* | Studio Farol Design ME | `IMV-0007` | R$ 1.890,00 |

### 7.5 Card "Saúde da operação"

Centro do donut: **10** / `imóveis`.

| Segmento | Cor | Valor | % |
|---|---|---|---|
| Alugados | `#12a150` | **7** | 70% |
| Disponíveis | `#f0a020` | **2** | 20% |
| Manutenção | `#d9dee8` | **0** | 0% |
| Outros | `#7c5cf0` | **1** | 10% |

Rodapé: `Contratos encerrando nos próximos 60 dias` → **1 contrato** *(CT-02)*.

### 7.6 Badge da navegação

Pill **Inadimplência** com badge vermelho **3**.

---

## 8 · Fila de inadimplência

Subtítulo real da tela:

> **3 cobranças vencidas · R$ 6.080,00 em aberto · atualizado com multa e juros:
> R$ 6.250,49 · ordenado por dias de atraso**

Rodapé da tabela:

> *"Registre cada contato feito com o inquilino sobre a cobrança em atraso."* ·
> **3 cobranças · R$ 6.080,00 · atualizado com multa e juros: R$ 6.250,49**

| Inquilino | Imóvel | Competência | Valor exibido | Sublinha | Atraso | Badge | Emails |
|---|---|---|---|---|---|---|---|
| **Beatriz Nunes Chaves** | `IMV-0001` · Rua Coronel Quirino, 1420 <br>*Cambuí — Campinas/SP* | M−1 | **R$ 2.534,12** | `original R$ 2.450,00` | **43 dias** | vermelho **sólido** | **5** |
| **Beatriz Nunes Chaves** | `IMV-0001` · Rua Coronel Quirino, 1420 <br>*Cambuí — Campinas/SP* | M0 | **R$ 2.509,62** | `original R$ 2.450,00` | **13 dias** | vermelho **sólido** | **4** |
| **Rodrigo Sampaio Leal** | `IMV-0002` · Rua Dr. Sampaio Ferraz, 340 <br>*Cambuí — Campinas/SP* | M0 | **R$ 1.206,75** | `original R$ 1.180,00` | **8 dias** | vermelho suave | **4** |

**Conferência do cálculo** — multa 2% (fixa) + juros 1% a.m. pro-rata dia
(`saldo × 1% × dias/30`), arredondando cada parcela:

```
Beatriz M−1: 2450,00 + 49,00 (multa) + 35,12 (juros, 43/30) = 2534,12
Beatriz M0 : 2450,00 + 49,00 (multa) + 10,62 (juros, 13/30) = 2509,62
Rodrigo M0 : 1180,00 + 23,60 (multa) +  3,15 (juros,  8/30) = 1206,75
                                             total da fila  = 6250,49
```

**Contagem de emails** — deriva da cadência padrão (`−3d`, dia do vencimento, `+3`,
`+7`, `+15`): 43 dias de atraso já passaram por todos os cinco marcos → **5**;
13 e 8 dias passaram por quatro (`−3`, dia, `+3`, `+7`) → **4**.

**Nenhuma linha do Thiago aparece aqui** — as cobranças dele estão em dia. O badge
**"Sem email"** dele aparece se e quando uma cobrança dele vencer; para exercitá-lo
numa cena, atrase a cobrança de M0 do CT-05 e observe que, sendo multa e juros 0%, a
linha dele **não** ganha a sublinha "original".

**A cobrança de Priscila em M−1 (Em negociação, R$ 2.780) NÃO aparece nesta tela.**
`under_negotiation` está fora do critério de "vencida em aberto" — marcar em
negociação tira a cobrança da fila **e** da cadência de emails. É um comportamento
real e conta uma história boa.

---

## 9 · Variante: Mercado Pago ligado

**Estado padrão do dataset: MP desligado**, que é a configuração real do piloto hoje.
Neste estado, os emails de cobrança saem com o painel **"Como pagar"** contendo as
instruções manuais da agência, e nenhuma cobrança mostra sublinha de MP.

Se a demonstração precisar do link de pagamento, use esta variante — ela é alcançável
no produto (o admin liga o interruptor, desde que o suporte já tenha vinculado a conta
de recebimento):

| Ajuste | Valor |
|---|---|
| `agencies.mp_enabled` | `true` |
| Conta de recebimento | **vinculada** → Configurações mostra `Conta vinculada` |

Efeitos no dataset:

| Cobrança | Sublinha em `/cobrancas` | Menu `⋯` | Email |
|---|---|---|---|
| CT-01 M0 (Vencida, saldo 2.450) | **Link de pagamento** | ganha **Copiar link de pagamento** | botão **"Regularizar pagamento"** |
| CT-02 M0 (Parcialmente paga, saldo 1.180) | **Link de pagamento** | ganha o item | botão **"Pagar com boleto ou Pix"** |
| CT-05 / CT-06 / CT-07 M0 (Abertas) | **Link de pagamento** | ganha o item | botão **"Pagar com boleto ou Pix"** |
| CT-03 / CT-04 M0 (Pagas) | *(nada)* | — | — |
| CT-06 M−1 (Em negociação) | *(nada)* — fora do recorte da rodada | — | — |

E, se quiser mostrar a baixa automática, marque **CT-04 M0** como paga **via Mercado
Pago**: a sublinha vira **"Pagamento via Mercado Pago"**, e a timeline do contrato
ganha os eventos `Link de pagamento emitido` → `Pagamento registrado` →
`Link de pagamento expirado`.

> **Não misture as duas versões na mesma cena.** Ou a agência tem MP, ou não tem.

---

## 10 · Documentos e comprovantes

| Onde | Arquivo | Tipo | Tamanho | Selo |
|---|---|---|---|---|
| Contrato CT-01 | `contrato-locacao-imv-0001.pdf` | PDF | 1,4 MB | **PDF** vermelho |
| Contrato CT-01 | `vistoria-entrada-imv-0001.pdf` | PDF | 3,1 MB | **PDF** vermelho |
| Contrato CT-04 | `contrato-locacao-imv-0004.pdf` | PDF | 1,9 MB | **PDF** vermelho |
| Baixa CT-04 M0 | `comprovante-transferencia.pdf` | PDF | 212,4 KB | **PDF** vermelho |
| Baixa CT-03 M−1 | `comprovante-pix.png` | PNG | 486,7 KB | **IMG** azul |

Linha de metadados na lista de documentos:
`1,4 MB · enviado em {data} por Marina Prado Bandeira`

---

## 11 · Timeline — eventos plausíveis

Para o detalhe de **CT-01** (contrato com duas cobranças vencidas), do mais recente
para o mais antigo:

| Evento | Rótulo | Ícone | Tinta | Autor | Quando |
|---|---|---|---|---|---|
| `email.sent` | Email enviado | `Mail` | info | **Sistema** | há 3 dias |
| `charge.overdue` | Cobrança vencida | `TriangleAlert` | danger | **Sistema** | há 13 dias |
| `contact.registered` | Contato registrado | `Phone` | info | Marina Prado Bandeira | há 18 dias |
| `email.sent` | Email enviado | `Mail` | info | **Sistema** | há 28 dias |
| `charge.opened` | Cobrança aberta | `CalendarClock` | info | **Sistema** | há 28 dias |
| `charge.overdue` | Cobrança vencida | `TriangleAlert` | danger | **Sistema** | há 43 dias |
| `charge.generated` | Cobrança gerada | `CalendarPlus` | neutral | **Sistema** | há 58 dias |
| `document.uploaded` | Documento anexado | `FileText` | info | Marina Prado Bandeira | há 4 meses |
| `contract.activated` | Contrato ativado | `Check` | success | Marina Prado Bandeira | há 17 meses |

Para o detalhe de **CT-06** (a cobrança em negociação):

| Evento | Rótulo | Autor | Quando |
|---|---|---|---|
| `charge.negotiation_started` | Negociação iniciada | Caio Bittencourt Sá | há 25 dias |
| `contact.registered` | Contato registrado | Caio Bittencourt Sá | há 26 dias |
| `charge.overdue` | Cobrança vencida | **Sistema** | há 30 dias |

Segunda linha de cada item: `há 3 dias · 15/09/2026 09:42` (relativo + absoluto).

---

## 12 · Textos livres — copy plausível para os campos

Para não inventar na hora de animar.

| Campo | Texto sugerido |
|---|---|
| Motivo da isenção (CT-05 M−1) | *"Primeiro mês proporcional já quitado na assinatura do contrato."* |
| Nota de contato (Beatriz) | *"Ligação atendida. Informou que o pagamento sai até sexta, após o acerto do FGTS. Combinado retorno na segunda."* |
| Nota de contato (Priscila) | *"WhatsApp respondido. Pediu parcelamento em duas vezes; encaminhado à gestora."* |
| Motivo da diferença (baixa parcial CT-02) | *"Pagamento parcial acordado por telefone; saldo previsto para o dia 25."* |
| Motivo da edição de cobrança | *"Ajuste de vencimento a pedido do inquilino, conforme conversa registrada."* |
| Motivo do encerramento (CT-08) | *"Rescisão amigável solicitada pelo inquilino. Chaves entregues e vistoria de saída realizada."* |
| Referência de pagamento | `PIX E2E-8842-SET26` · `TED 0184-77213` |
| Observações da pessoa | *"Prefere contato por WhatsApp. Trabalha em horário comercial."* |
| Instruções de pagamento (agência) | ver §2 |

---

## 13 · Resumo de coerência — confira antes de renderizar

Se qualquer um destes números aparecer diferente em duas cenas, há inconsistência:

| Número | Valor |
|---|---|
| Cadastros de pessoas | **11** (7 inquilinos, 4 proprietários) |
| Imóveis cadastrados | **11** |
| Carteira ativa (dashboard) | **10** |
| Carteira ativa (`/imoveis`) | **11** — sim, diferente, ver §4 |
| Alugados | **7** |
| Contratos ativos | **7** |
| Contratos (aba Todos) | **8** |
| Rascunhos de contrato | **1** |
| Cobranças em M0 | **7** |
| Previsto no mês | **R$ 18.100,00** |
| Recebido no mês | **R$ 8.150,00** (45,0%) |
| Vencido | **R$ 6.080,00** |
| Inadimplência | **33,6%** |
| Cobranças vencidas | **3** |
| Total atualizado da fila | **R$ 6.250,49** |
| Badge da nav | **3** |
| Contratos encerrando em 60 dias | **1** |
| Ocupação em `/imoveis` | **63,6%** |

---

[◄ Estados e regras](estados-e-regras.md) · [Fluxos para animar ►](fluxos-para-animar.md)


---

<!-- ===== fluxos-para-animar.md ===== -->

# Fluxos para animar

> Oito momentos em que o Locatizei mostra o que ele faz de melhor. Cada um é um
> roteiro: estado inicial, ação, o que muda na tela, o que acontece por baixo, a
> sensação a transmitir — e **o que não mostrar**.
>
> Todos usam o conjunto de [dados-demo.md](dados-demo.md). Os textos entre aspas são
> literais do produto.

[◄ Voltar ao índice](README.md)

---

## Antes de começar: três regras que valem para os oito

1. **Desktop.** O app não faz reflow em telas estreitas — é um milestone que ainda não
   aconteceu. Enquadre em uma janela larga (o conteúdo tem `max-w-1440px`).
2. **Modo claro.** Não existe tema escuro no produto.
3. **Nada de tela de login, nem de "carregando".** O app não tem skeleton nem spinner
   de página; as telas chegam prontas do servidor. O único indicador de carregamento
   é o texto do botão que muda (`Salvando…`, `Confirmando…`) e o ícone girando do
   botão Atualizar do dashboard.

---

# Fluxo 1 · Do alerta ao dinheiro em três cliques

**O diferencial:** o dashboard não só informa que existe uma inadimplência — ele
**leva** o operador até ela, com a competência certa e a cobrança certa já abertas.

### Estado inicial

`/` — Dashboard. Card **"Exige atenção hoje"** com badge vermelho **3**. A primeira
linha é **Beatriz Nunes Chaves**, `Rua Coronel Quirino, 1420`, **R$ 2.450,00**,
**+43 dias** em vermelho.

### O que o usuário faz

Clica no botão **"Ver cobrança"** daquela linha — o retângulo azul-claro
(`bg-brand-soft`, texto `text-primary`, 11,5px bold) na ponta direita.

### O que muda na tela

1. Navegação para `/cobrancas?competencia=2026-08&cobrancaSelecionada=…`
2. A tela de Cobranças aparece **já com o filtro de competência em agosto/2026** — não
   no mês corrente.
3. A linha da Beatriz está **destacada**: fundo `bg-row-selected` (`#f4f7fe`) e uma
   **barra azul de 3px colada na borda esquerda** da linha
   (`shadow-[inset_3px_0_0_var(--primary)]`).
4. O **drawer de 440px desliza da direita** (`slide-in-from-right-10`, 200 ms), com a
   sombra projetada para dentro da tela (`-24px 0 60px rgba(16,24,40,.25)`).
5. Dentro do drawer, o painel cinza de contexto já traz:
   `IMV-0001 · Beatriz Nunes Chaves` + badge **Vencida**,
   `competência agosto/2026 · venceu em 05/08/2026`,
   `Valor previsto **R$ 2.450,00**`.
6. O campo **Valor recebido** já vem preenchido com **2450** — o saldo, não o valor
   original.
7. No pé do corpo, a nota azul: *"Ao confirmar, a cobrança passa a **Paga**."*

### O usuário confirma

Clica em **Confirmar pagamento** (`shadow-btn-primary`). O botão vira
**"Confirmando…"** e desabilita.

### O que muda depois

- O drawer fecha.
- Um **toast** aparece em `top-6 right-6`: círculo verde com ícone `Info`, título
  **"Pagamento registrado"**, descrição **"A cobrança passou para Paga."** Some em
  4,5 segundos.
- A linha na tabela troca o badge **Vencida** (vermelho) por **Paga** (verde), a
  coluna Vencimento perde o vermelho, e as ações passam de *Registrar pagamento* para
  *Ver recibo*.
- **Volte ao dashboard na mesma cena:** o badge de "Exige atenção" cai de **3** para
  **2**, o KPI **Vencido** cai de R$ 6.080,00 para R$ 3.630,00, o **Recebido** sobe
  de R$ 8.150,00 para R$ 10.600,00, a barra do Previsto avança, e o badge da pill
  **Inadimplência** cai de 3 para 2.

### O que acontece por baixo

Um registro de pagamento é gravado; o status da cobrança é recalculado pela regra
`amount − Σ pagamentos`; um evento `payment.recorded` entra na timeline do contrato;
e — se os emails da agência estiverem ligados — um **recibo** é disparado ao inquilino.

### Sensação a transmitir

**Continuidade.** O número no dashboard não é um relatório: é um botão. O caminho do
alerta até a baixa não tem busca, não tem filtro manual, não tem "encontre a cobrança".

### O que NÃO mostrar

- ❌ Login ou qualquer tela de autenticação.
- ❌ Uma tela de "processando" entre o clique e o resultado. Não existe.
- ❌ O recibo chegando na caixa de entrada em tempo real — o envio é assíncrono e
  depende da configuração da agência.

---

# Fluxo 2 · O contrato que gera a agenda inteira sozinho

**O diferencial:** a simulação da etapa 3 **é** o resultado. Não é uma estimativa —
é a mesma função que a ativação executa para gravar.

### Estado inicial

`/contratos/novo`, etapa **2 — Vigência e valores**, com os campos preenchidos:
imóvel `IMV-0008`, locatário **Camila Rezende Whitaker**, aluguel **R$ 2.300,00**,
dia de vencimento **10**, taxa de administração **10%**.

Repare no detalhe vivo sob a taxa: **"R$ 230,00 sobre o aluguel informado."** — ele
recalcula a cada dígito.

### O que o usuário faz

Clica em **Continuar**.

### O que muda na tela

1. O **stepper** anima: a bolinha ② vira verde com um `Check`, a ③ vira azul, e o
   conector entre elas passa de `bg-divider` para `bg-primary`.
2. Aparecem quatro cartões de conferência (`bg-surface-sub`), com rótulos em caixa
   alta e espaçamento de letra: **IMÓVEL · PARTES · VIGÊNCIA · VALORES**, cada um com
   um link **Editar** que volta para a etapa certa.
3. Abaixo, o título **"Primeiras 12 cobranças (geradas na ativação)"** e uma tabela
   de quatro colunas: **Competência · Vencimento · Valor · Status inicial**.
4. As 12 linhas aparecem com badges já diferenciados: as duas primeiras **Aberta**
   (azul, porque vencem dentro dos próximos 15 dias) e as dez seguintes **Agendada**
   (cinza).
5. No rodapé da tabela, à direita: **"Total previsto: R$ 27.600,00"**.

### O usuário avança

Clica em **"Ir para ativação"**.

### Etapa 4

O card **encolhe** de 920px para 760px. No centro:

- círculo verde de 54px com o ícone `CheckCheck`;
- título **"Pronto para ativar"**;
- painel de impacto com três linhas, cada uma com seu ícone azul:
  - ✓ o contrato fica **Ativo**
  - 🏠 o imóvel **IMV-0008** passa de Disponível a **Alugado**
  - 📅 **12 cobranças** são criadas (primeira vence 10/10/2026)
- aviso âmbar: *"A ativação não pode ser desfeita. Depois de ativo, o contrato pode
  ser suspenso ou encerrado, e as cobranças podem ser ajustadas uma a uma."*

O botão final ganha a **sombra `shadow-cta`** — `0 8px 20px rgba(33,64,214,.35)`, a
mais forte do sistema, usada **só aqui**.

### O clique final

**Ativar contrato** → **"Ativando..."** → o app navega para `/contratos/{id}`.

O detalhe do contrato aparece com a **agenda de cobranças já preenchida**, e as 12
linhas são **exatamente** as da simulação: mesmas competências, mesmos vencimentos,
mesmos status. Sobreponha as duas tabelas na animação — é o argumento inteiro.

### O que acontece por baixo

Ativação transacional: cria o contrato, muda o imóvel para `rented`, grava as 12
cobranças, registra `contract.activated` na auditoria, e revalida a disponibilidade do
imóvel dentro da mesma transação (um imóvel só pode ter um contrato ativo).

### Sensação a transmitir

**Confiança antes do compromisso.** O produto mostra tudo o que vai acontecer, com
números reais, antes de pedir o clique irreversível. E cumpre exatamente.

### O que NÃO mostrar

- ❌ As etapas 1 e 2 sendo preenchidas do zero — é digitação, e é a parte menos
  interessante. Comece com os campos prontos.
- ❌ O comportamento de "Salvar rascunho" na mesma cena; ele merece um beat próprio
  (§ *variação*, abaixo).

### Variação curta: o rascunho

Clique em **Salvar rascunho** no meio do wizard → toast **"Rascunho salvo"** /
*"Continue de onde parou em Contratos › Rascunhos."* → corte para `/contratos`, aba
**Rascunhos**, onde a linha aparece com fundo levemente azulado, badge tracejado
**Rascunho** e o botão **Continuar** — que reabre o wizard **na mesma etapa**.

---

# Fluxo 3 · A escalada da inadimplência

**O diferencial:** a fila não é uma lista — é uma escala. O produto grita mais alto
conforme o atraso cresce, e diz quando não consegue falar com a pessoa.

### Estado inicial

`/inadimplencia`. Três linhas ordenadas por dias de atraso, decrescente:

| | Atraso | Badge |
|---|---|---|
| Beatriz Nunes Chaves — agosto/2026 | **43 dias** | vermelho **sólido**, texto branco |
| Beatriz Nunes Chaves — setembro/2026 | **13 dias** | vermelho **sólido**, texto branco |
| Rodrigo Sampaio Leal — setembro/2026 | **8 dias** | vermelho suave |

### A animação da escala

Anime os quatro degraus como uma sequência de estados do mesmo badge, com o número
subindo:

```
[1 dia]     âmbar     #fef3df / #b26e05
[4 dias]    laranja   #fdeee0 / #c2611a
[8 dias]    vermelho  #fdebea / #e0342c
[12 dias]   VERMELHO SÓLIDO   #e0342c / #ffffff   ← o salto
```

O corte dos 9 para os 10 dias é o momento: o badge deixa de ser um rótulo e vira um
bloco de cor cheia.

### O segundo sinal: quem o sistema não alcança

Sob o nome de um inquilino sem email cadastrado aparece o badge âmbar
**"Sem email"**. É a informação mais acionável da tela: aquela cobrança **não** está
sendo perseguida automaticamente. Alguém precisa ligar.

### A coluna que diz o quanto já se tentou

**"Emails enviados"** mostra `5`, `4`, `4` em negrito — ou `—` quando nenhum saiu. O
operador vê, de relance, quem já recebeu quatro avisos e continua devendo.

### O que o usuário faz

Clica em **Registrar contato** na primeira linha.

### O que muda

Abre um dialog de 480px. O subtítulo já carrega o contexto e a urgência:

> Beatriz Nunes Chaves · agosto/2026 · **43 dias de atraso**

*(o trecho final em `font-bold text-danger-foreground`)*

Quatro chips-pílula de canal — **Telefone · WhatsApp · Email · Presencial** — com
**Telefone** já selecionado (borda azul de 1,5px, fundo `bg-brand-hint`). A data de
hoje já preenchida. Um campo **Nota \*** obrigatório.

E uma nota azul explicando a consequência antes de acontecer:

> O contato fica salvo na cobrança e aparece na timeline do contrato.

### O usuário digita e salva

Nota: *"Ligação atendida. Informou que o pagamento sai até sexta, após o acerto do
FGTS. Combinado retorno na segunda."* → **Salvar contato** → **"Salvando…"** →
toast **"Contato registrado"** / *"O contato foi salvo com sucesso."*

### O fechamento do arco

Corte para `/contratos/{CT-01}`, card **Timeline**. O evento novo está no topo, com
ícone `Phone` num círculo azul-claro:

> **Contato registrado** por Marina Prado Bandeira
> agora · 18/09/2026 10:14

O que era uma promessa no dialog virou fato na timeline. Mostre os dois na mesma cena.

### Sensação a transmitir

**A cobrança é um relacionamento, não uma planilha.** O produto escalona o alerta,
avisa quando não consegue falar, e guarda cada conversa no lugar certo.

### O que NÃO mostrar

- ❌ Dados de contato reais em close — use os do conjunto fictício.
- ❌ O envio de um email de cobrança sendo disparado na hora. Os emails saem numa
  rodada diária de madrugada, não por clique.

---

# Fluxo 4 · O pagamento que não fecha a conta

**O diferencial:** o produto trata pagamento parcial como cidadão de primeira classe.
Ele avisa **antes** de gravar, exige a justificativa, e depois conta a verdade em todo
lugar — incluindo no recibo que o inquilino recebe.

### Estado inicial

`/cobrancas`, drawer aberto na cobrança de **Rodrigo Sampaio Leal**, setembro/2026,
**R$ 1.980,00**, vencida há 8 dias. O campo **Valor recebido** vem preenchido com
`1980`.

### O que o usuário faz

Apaga e digita **800**.

### O que muda — imediatamente, sem submit

Três coisas acontecem a cada tecla:

1. O **input muda de aparência**: borda de 1,5px em
   `--warning-border` (`#e8b658`), fundo `--warning-surface` (`#fffdf7`), texto em
   negrito.
2. Um **banner âmbar** abre logo abaixo, com o ícone `TriangleAlert`:
   > O valor difere do previsto (**R$ 1.980,00**) — informe o motivo abaixo.
3. O campo **Motivo da diferença** ganha um `*` e passa a ser obrigatório.

E a nota azul do pé **reescreve a frase**:

> Ao confirmar, a cobrança passa a **Parcialmente paga** — restam **R$ 1.180,00**.

Anime o "restam R$ X" recalculando enquanto o número muda: `800` → restam 1.180;
`1500` → restam 480; `1980` → o banner âmbar **some** e a frase volta a
*"passa a **Paga**"*.

### O usuário completa e confirma

Motivo: *"Pagamento parcial acordado por telefone; saldo previsto para o dia 25."*
Forma: **Pix**. Anexa `comprovante-pix.png` — a dropzone tracejada mostra
**"Enviando…"** e depois vira uma linha com o nome do arquivo e um `✕`.

**Confirmar pagamento** → toast:
**"Pagamento registrado"** / *"A cobrança passou para Parcialmente paga — restam R$ 1.180,00."*

### O que muda na tabela

A linha ganha:
- badge âmbar **Parcialmente paga**;
- na coluna Valor, o `R$ 1.980,00` em negrito **com uma sublinha nova**:
  `R$ 800,00 recebidos` em 11,5px cinza;
- ações: **Registrar pagamento** *e* **Ver recibo**, lado a lado — é o único status em
  que os dois convivem.

### O eco na inadimplência

Corte para `/inadimplencia`. A linha do Rodrigo mostra **R$ 1.206,75** (saldo
corrigido com multa e juros) com a sublinha `original R$ 1.180,00` — não o valor cheio
da cobrança. O saldo é o que importa.

### O eco no email

Se os emails estiverem ligados, o recibo que sai traz o bloco âmbar:

> **Saldo restante: R$ 1.180,00**
> Pagamento parcial — este valor permanece em aberto para Setembro/2026.

### Sensação a transmitir

**Honestidade aritmética.** Nada de "pago" quando não está pago. O produto conta a
diferença, pede o porquê, e repete o mesmo número em todas as telas.

### O que NÃO mostrar

- ❌ O upload de um arquivo real com conteúdo legível. Use um retângulo genérico ou
  um placeholder de PDF/imagem.
- ❌ O comprovante sendo aberto (o download passa por uma URL assinada de curta
  duração — não é uma tela).

---

# Fluxo 5 · Encerrar um contrato, com a conta na mesa

**O diferencial:** antes de uma ação irreversível, o produto lista **nominalmente** o
que vai cancelar — e a lista **reage à data** que o usuário escolhe.

### Estado inicial

`/contratos/{CT-02}` — Rodrigo Sampaio Leal, `IMV-0002`. O badge do cabeçalho diz
**Encerrando** (laranja), porque o contrato termina em 35 dias.

### O que o usuário faz

Clica no botão `⋯` de 38px no canto superior direito.

Abre um menu de 208px com três itens. O terceiro, **"Encerrar contrato"**, está em
vermelho (`variant="destructive"`) — e **só aparece porque o usuário logado é admin**.

### O dialog

480px. Título **"Encerrar contrato"**, descrição em uma linha:
*"Esta ação não pode ser desfeita."*

Dois campos: **Data de saída \*** (hoje) e **Motivo \***.

E o painel âmbar de impacto, com ícone de alerta:

> Estas **2** cobranças serão canceladas:
> · outubro/2026 · R$ 1.980,00
> · novembro/2026 · R$ 1.980,00
>
> O imóvel **IMV-0002** passa a **Indisponível** e NÃO volta a ser anunciado
> automaticamente.

### O beat central da animação

**Mude a data de saída** — puxe para dois meses à frente. O painel **reescreve
sozinho**: a lista encolhe para uma linha, e se a data passar do fim da vigência o
texto vira *"Nenhuma cobrança futura será cancelada."*

Isso não é decoração: é exatamente o mesmo recorte que a operação vai aplicar no banco
(cobranças `Agendada`/`Aberta` com competência posterior ao mês da saída). O dialog
não pode mentir sobre o que vai cancelar, e não mente.

### A confirmação

**Encerrar contrato** (botão vermelho) → **"Encerrando…"** → toast:

> **Contrato encerrado**
> 2 cobranças futuras foram canceladas.

### O que muda na tela

- O badge do cabeçalho vira **Encerrado** (cinza).
- Na agenda, as duas competências futuras aparecem **riscadas** (`line-through`) em
  tons apagados — **elas não somem**. As anteriores, pagas e vencidas, continuam
  intactas.
- Na timeline, entra **"Contrato encerrado"** com ícone `FileX` num círculo vermelho.
- Corte para `/imoveis/{IMV-0002}`: o status virou **Indisponível**.

### Sensação a transmitir

**Nada desaparece.** Encerrar não apaga histórico nem dívida — só interrompe o
futuro. E o produto avisa que o imóvel **não** volta sozinho para o mercado.

### O que NÃO mostrar

- ❌ O item "Encerrar contrato" na sessão de um operador. Ele não existe lá.
- ❌ Um contrato voltando a ficar ativo depois. Não há caminho para isso.

---

# Fluxo 6 · Os interruptores que a imobiliária controla

**O diferencial:** um produto financeiro que trata "ligar a automação" como uma
decisão consciente, com o texto mudando conforme o que ele realmente pode prometer.

### Estado inicial

`/configuracoes` — visível **só para admin**. Coluna estreita (560px), dois cards.

### Beat 1 — a cadência

Card **"Emails de cobrança"**. No topo, o checkbox-mestre e, logo abaixo, o banner
âmbar que não sai nunca:

> Interruptor-mestre: com ele desligado, nenhum email é enviado por esta agência.

Depois, a cadência em quatro controles:

```
Lembrete (dias antes do vencimento)   [ 3 ]
☑ Enviar no dia do vencimento
Avisos após o vencimento (dias, …)    [ 3, 7, 15 ]
☑ Enviar recibo automático na baixa
```

Anime isto sobre uma régua de tempo: `−3d` · `dia 0` · `+3d` · `+7d` · `+15d`, com um
envelope aparecendo em cada marco. Edite o campo para `3, 7, 15, 30` e mostre um
quinto marco nascendo na régua.

### Beat 2 — a identidade que vai no email

Role até **"Identidade nos emails"**. A explicação está lá:

> O inquilino recebe o email com a marca da imobiliária, não com a da Locatizei.
> Campos em branco somem do email — nenhum deles impede o envio.

Mude o campo **Cor da marca** de `#1F4E5F` para outro tom escuro e mostre, ao lado, a
faixa do topo do email trocando de cor junto com o botão e os links. A nota do campo
avisa o limite:

> Precisa ser escura o bastante para ler texto branco em cima — tons claros são
> recusados.

*(o produto valida contraste de verdade e recusa cores claras)*

### Beat 3 — o card do Mercado Pago e seus três estados

Este é o beat mais interessante da tela. Anime os três estados de **"Conta de
recebimento"** em sequência, mostrando o Switch reagindo:

| Estado | Switch | Texto |
|---|---|---|
| **Sem credenciais** | **desabilitado** — não dá para ligar | `Sem credenciais — o suporte configura a conta de recebimento` |
| **Verificação indisponível** | **desabilitado** para ligar | um alerta âmbar de três linhas explicando que a integração não pode ser ligada enquanto a verificação não voltar |
| **Conta vinculada** | **habilitado** | `Conta vinculada` |

E, quando o admin liga: **nenhum toast**. O próprio Switch, repintado de azul, é a
confirmação. O parágrafo acima dele reescreve para a versão que promete a rodada
automática diária.

A faixa âmbar continua ali, em todos os estados, dizendo o que desligar **não** faz:

> Desligar não cancela os links já enviados: quem recebeu um boleto ou Pix ainda
> consegue pagar.

### Sensação a transmitir

**Nenhuma automação liga sozinha, e nenhum texto promete o que o sistema não
garante.** É um produto que prefere dizer menos e estar certo.

### O que NÃO mostrar

- ❌ Uma tela de "conectar minha conta do Mercado Pago" com campos de token. **Ela
  não existe.** A conta é vinculada pelo suporte, fora da interface — o próprio texto
  da tela diz isso.
- ❌ Qualquer credencial, token, chave ou identificador, nem de sandbox.
- ❌ Esta tela na sessão de um operador. Ele é redirecionado antes de vê-la.

---

# Fluxo 7 · Quitar por acordo

**O diferencial:** o produto reconhece que negociação existe. Ele deixa marcar como
paga por menos — e registra a diferença, em vez de fingir que ela não houve.

### Estado inicial

`/cobrancas`. Menu `⋯` da cobrança de **Beatriz**, agosto/2026, saldo **R$ 2.450,00**,
43 dias de atraso.

### O menu

Sete itens possíveis, mas para uma cobrança vencida aparecem estes:

```
Pausar emails
Marcar em negociação
Editar valor/vencimento
Quitar por acordo
Isentar                    ← em vermelho
```

Clique em **Quitar por acordo**.

### O dialog

440px. Descrição: *"Registra um pagamento combinado com o inquilino e marca a cobrança
como paga."*

Campos: **Valor recebido \*** (pré-preenchido com 2450) · **Data \*** · quatro chips
de forma de pagamento · **Referência** · **Motivo do acordo \*** (sempre obrigatório
aqui) · **Comprovante**.

### O beat central: a nota que muda três vezes

Digite três valores diferentes e mostre a nota azul do rodapé reescrevendo:

| Valor digitado | Texto da nota |
|---|---|
| **2000** | A cobrança será marcada como **Paga** pelo valor acordado. Diferença de **R$ 450,00** registrada como acordo. |
| **2450** | A cobrança será marcada como **Paga** pelo valor integral do saldo. |
| **2600** | Valor acima do saldo — a cobrança será marcada como **Paga** por **R$ 2.600,00** (motivo obrigatório). |

Três frases, três situações reais, escritas separadamente. Nenhuma delas é genérica.

### A confirmação

Motivo: *"Acordo fechado por telefone: desconto de R$ 450,00 sobre multa e juros
mediante quitação imediata."* → **Quitar cobrança** → **"Quitando…"**.

### O que muda

A cobrança vira **Paga** (verde). Na timeline do contrato entra
**"Quitada por acordo"** com ícone `Handshake` num círculo **verde**. A fila de
inadimplência perde a linha, e o KPI **Vencido** cai R$ 2.450,00.

### Sensação a transmitir

**O produto acompanha a vida real.** Nem toda dívida é paga integralmente, e fingir
que sim é o que faz uma planilha divergir da realidade.

### O que NÃO mostrar

- ❌ "Quitar por acordo" numa cobrança `Aberta` — a ação só existe para vencida, em
  negociação ou parcialmente paga.
- ❌ Reverter um acordo. Não há caminho: cobrança paga é imutável no produto.

---

# Fluxo 8 · O ciclo completo do dinheiro, em uma linha do tempo

**O diferencial:** este não é um fluxo de tela — é a espinha do produto. Vale como
peça de abertura ou de encerramento.

### O roteiro em sete quadros

```
①  CONTRATO ATIVADO
    Wizard etapa 4 → "12 cobranças são criadas"
    imóvel: Disponível ──► Alugado

②  COBRANÇA ABRE SOZINHA
    Rodada diária, 07:00 (horário de Brasília)
    Agendada ──► Aberta   (15 dias antes do vencimento)
    timeline: "Cobrança aberta pelo Sistema"

③  EMAIL DE COBRANÇA SAI
    3 dias antes → selo LEMBRETE
    no dia       → selo VENCE HOJE
    depois       → selo EM ATRASO (+3, +7, +15)
    assinado pela IMOBILIÁRIA, não pela Locatizei

④  LINK DE PAGAMENTO (quando o Mercado Pago está ligado)
    botão colorido no email: "Pagar com boleto ou Pix"
    subtexto: "Pagamento em ambiente seguro do banco —
               boleto com opção de Pix."
    Sem MP: no lugar do botão, o painel "Como pagar"
            com as instruções da imobiliária

⑤  O INQUILINO PAGA

⑥  BAIXA
    manual  → drawer "Registrar pagamento" (440px, direita)
    ou automática → webhook do Mercado Pago
    Aberta ──► Paga     (ou Parcialmente paga)

⑦  RECIBO
    "Pagamento confirmado"
    ✓ verde · R$ 2.450,00 · Setembro/2026 · Pix · 18/09/2026
    "Guarde este email como comprovante de pagamento."
```

### Os três selos do email, lado a lado

Vale um quadro só para eles — são a peça de design mais acabada do produto:

| | Selo | Fundo / texto | Valor exibido |
|---|---|---|---|
| 3 dias antes | **LEMBRETE** | `#EEF1F4` / `#445263` | saldo |
| no dia | **VENCE HOJE** | `#FBF3E4` / `#7A5410` | saldo |
| em atraso | **EM ATRASO** | `#FAEDE8` / `#8C3A24` | **total atualizado** |

Todos com a mesma arquitetura: faixa colorida com a marca da imobiliária no topo,
card branco de 600px, selo centralizado, valor em 36px extrabold, painel cinza de
detalhes, e o rodapé discreto **"Enviado via Locatizei"** — a única menção à
plataforma.

### O que acontece por baixo (e vale dizer em legenda)

- A rodada é **idempotente**: rodar duas vezes não duplica nada.
- Cada cobrança recebe **no máximo um email por rodada**, e cada marco de cadência
  sai **uma vez só**.
- **Email é efeito, nunca causa**: falha de envio não bloqueia a rodada nem a baixa.
- O botão de pagar só viaja quando o link cobra **exatamente** o saldo atual. Link
  defasado ⇒ o email cai nas instruções manuais.

### Sensação a transmitir

**O aluguel se cobra sozinho.** A imobiliária cadastra o contrato uma vez; o resto é
o sistema abrindo, avisando, recebendo e dando recibo — com a cara dela, não com a
cara do fornecedor.

### O que NÃO mostrar

- ❌ Endereços de email reais (use `example.com`).
- ❌ Um boleto ou QR de Pix com código legível. Represente o checkout como uma tela
  genérica de banco, ou corte antes dela.
- ❌ Qualquer URL de webhook, identificador de transação ou credencial.
- ❌ O tempo real da rodada — ela leva minutos, não é um beat de animação. Represente
  como um corte.

---

## Tabela de escolha rápida

| # | Fluxo | Telas | Duração sugerida | Mostra o quê |
|---|---|---|---|---|
| 1 | Do alerta ao dinheiro | Dashboard → Cobranças + drawer | 20–30 s | continuidade, deep link |
| 2 | Contrato gera a agenda | Wizard 2→3→4 → Detalhe | 30–40 s | simulação ≡ resultado |
| 3 | Escalada da inadimplência | Inadimplência → dialog → Timeline | 25–35 s | escala de cor, "Sem email" |
| 4 | Pagamento parcial | Cobranças + drawer → Inadimplência | 25–35 s | honestidade aritmética |
| 5 | Encerrar contrato | Detalhe → dialog → Imóveis | 25–35 s | prévia reativa, nada some |
| 6 | Os interruptores | Configurações | 30–40 s | controle consciente |
| 7 | Quitar por acordo | Cobranças + dialog | 20–25 s | negociação como cidadã |
| 8 | Ciclo do dinheiro | montagem + emails | 40–60 s | a tese do produto |

**Se for escolher só três:** 1, 2 e 8. O primeiro mostra a operação diária, o segundo
mostra a promessa cumprida, o oitavo mostra por que o produto existe.

---

## Recapitulação do que nunca deve aparecer

Vale além dos oito fluxos:

- **Autenticação de qualquer tipo** — login, senha, recuperação, sessão.
- **Qualquer segredo** — token, chave de API, credencial, secret de webhook, variável
  de ambiente. Nenhum deles está neste handoff, e nenhum deve ser inventado.
- **Dados reais** — nome, documento, email, telefone ou endereço de pessoa real. Use
  exclusivamente [dados-demo.md](dados-demo.md).
- **URL interna ou de infraestrutura** — endpoint, painel de administração, endereço
  de banco.
- **Telas que não existem**: auditoria com linhas, busca global funcionando,
  notificações no sino, tela de usuários, upload de logo da imobiliária, exportação,
  modo escuro, layout mobile, gráfico que não seja o donut de "Saúde da operação".
- **Esperas longas** — envio de email, rodada diária, processamento de pagamento.
  Corte.

---

[◄ Dados de demonstração](dados-demo.md) · [Voltar ao índice ►](README.md)


