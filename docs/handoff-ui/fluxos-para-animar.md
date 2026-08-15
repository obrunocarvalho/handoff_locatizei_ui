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
