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
