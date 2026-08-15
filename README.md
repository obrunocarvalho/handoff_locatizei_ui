# handoff_locatizei_ui

Documentação da **interface** do Locatizei — um painel de gestão de locações para
imobiliárias — preparada para servir de fonte única a quem vai produzir animações e
demonstrações interativas do produto **sem acesso ao código**.

## Comece aqui

➜ **[docs/handoff-ui/README.md](docs/handoff-ui/README.md)** — visão do produto,
o ciclo do dinheiro e o índice dos demais arquivos.

Ou, se preferir um arquivo só:
**[docs/handoff-ui/handoff-completo.md](docs/handoff-ui/handoff-completo.md)**.

## Conteúdo

```
docs/handoff-ui/
├── README.md                 índice · o que é o produto · ciclo do dinheiro ·
│                             o que existe hoje × o que é planejado
├── design-system.md          tokens de cor, tipografia, raios, sombras, espaçamento
├── componentes.md            inventário de componentes: props, variantes, estados,
│                             classes Tailwind reais
├── telas.md                  uma seção por rota (17), com todos os estados
├── estados-e-regras.md       matrizes de status, link de pagamento, permissões,
│                             formatação brasileira, catálogo de mensagens
├── dados-demo.md             conjunto fictício coerente, com KPIs já calculados
├── fluxos-para-animar.md     oito roteiros, com o que mostrar e o que não mostrar
└── handoff-completo.md       os seis acima concatenados
```

## Garantias deste repositório

- **Sem segredos.** Nenhuma variável de ambiente, chave, token, credencial, secret de
  webhook ou endpoint interno. Nada de sandbox tampouco.
- **Sem dados reais.** Todos os nomes, documentos, emails, telefones e valores são
  inventados. Emails usam o domínio `example.com`, reservado pela IANA. Documentos têm
  formato válido e conteúdo fictício que **não passa** no dígito verificador.
- **Sem código-fonte.** Há trechos curtos de JSX e CSS citados como referência de
  aparência; não há aplicação, dependência nem build.
- **Sem invenção.** Cada token, classe, rótulo e comportamento descrito foi conferido
  contra a implementação. Onde havia lacuna, ela está declarada como lacuna.

## Escopo

Só UI. Sem autenticação, sem backend, sem infraestrutura. O que está descrito é o que
está implementado hoje; o que é roadmap aparece separado e marcado como tal.
