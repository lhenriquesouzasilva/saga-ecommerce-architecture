Saga Pattern — Arquitetura de Compra Distribuída
Blueprint de arquitetura de microsserviços utilizando o padrão Saga por orquestração

1. Contexto do projeto

Este projeto apresenta uma blueprint de arquitetura para coordenar o processo de compra de um comércio eletrônico migrado de um monólito para uma arquitetura baseada em microsserviços.

O fluxo envolve três domínios independentes: Order Service, Inventory Service e Payment Service. Cada serviço possui seu próprio banco de dados, impossibilitando o uso de uma única transação ACID local para garantir a consistência de todas as operações.

2. Objetivo

Modelar uma solução baseada no padrão Saga para coordenar transações distribuídas, assegurar a recuperação diante de falhas e manter a consistência eventual dos dados.

3. Decisão arquitetural

Foi escolhida a Saga baseada em orquestração. Um Saga Orchestrator controla as etapas da compra, registra o estado do processo e determina as ações seguintes de acordo com os resultados recebidos dos serviços participantes.

Essa abordagem foi escolhida por facilitar a visualização do fluxo, a identificação de falhas e a execução das transações compensatórias.

4. Fluxo de sucesso

O pedido é registrado com estado PENDING.

O estoque é reservado.

O pagamento é processado e aprovado.

O pedido é confirmado.

A Saga é finalizada como COMPLETED.

5. Fluxo de falha e compensação

Quando o pagamento é recusado após a reserva do estoque, o orquestrador inicia a compensação. O Inventory Service libera a reserva e o Order Service cancela o pedido. Após confirmar as compensações, a Saga é finalizada como COMPENSATED.

6. Resiliência

A solução prevê persistência do estado da Saga, consumidores idempotentes, Transactional Outbox, retentativas controladas, Circuit Breaker, timeout, dead-letter queue e monitoramento.

7. Diagramas

diagrams/saga-architecture-overview.png: componentes e comunicação entre os serviços.

diagrams/sequence-success-flow.png: sequência de compra concluída.

diagrams/sequence-compensation-flow.png: sequência de falha e compensação.

Os arquivos editáveis dos diagramas também são disponibilizados no diretório diagrams/.

8. Limitações

A arquitetura adota consistência eventual e não oferece rollback ACID global. As compensações são operações de negócio independentes e podem exigir novas tentativas ou intervenção operacional.

9. Referência

RICHARDSON, Chris. Pattern: Saga. Microservices.io. Disponível em: https://microservices.io/patterns/data/saga.html. Acesso em: 9 out. 2026.
