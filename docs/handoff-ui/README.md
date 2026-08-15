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
