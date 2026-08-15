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
