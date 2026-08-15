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
