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
