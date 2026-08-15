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
