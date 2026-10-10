1. DA-001: Adoção da Saga por orquestração

Decisão: utilizar um Saga Orchestrator persistente para coordenar Order Service, Inventory Service e Payment Service.

Justificativa: o fluxo possui etapas sequenciais, dependências explícitas e compensações definidas. A orquestração facilita o rastreamento e a recuperação de falhas.

2. DA-002: Comunicação assíncrona

Os serviços utilizarão um broker de mensagens para transportar comandos e respostas. O estado de cada etapa será persistido para permitir a retomada após indisponibilidades.

3. DA-003: Transações compensatórias

A reserva de estoque será compensada por ReleaseStock. O pedido será cancelado por CancelOrder. Se uma compensação falhar, a Saga permanecerá pendente até a recuperação.

4. DA-004: Idempotência

Os consumidores registrarão identificadores únicos das mensagens processadas. Operações financeiras e de estoque também utilizarão chaves idempotentes de negócio, evitando efeitos duplicados.

5. DA-005: Transactional Outbox

As alterações locais e as mensagens de saída serão registradas atomicamente. Um processo de publicação enviará as mensagens ao broker, permitindo retentativas sem perder os eventos pendentes.

6. DA-006: Retry, timeout e Circuit Breaker

Falhas transitórias serão tratadas com retentativas limitadas e backoff exponencial. O Circuit Breaker protegerá serviços temporariamente indisponíveis. Timeouts resultarão em consulta de estado ou recuperação segura, sem presumir que a operação não ocorreu.

7. DA-007: Dead-letter queue

Mensagens que excederem as tentativas previstas serão encaminhadas para uma DLQ, acompanhadas de informações de diagnóstico. O reprocessamento dependerá da investigação e da correção da causa.

8. DA-008: Persistência e recuperação

O estado da Saga será armazenado de forma durável, incluindo etapa atual, tentativas, prazos, identificadores e erros. Após uma reinicialização, o orquestrador retomará o processo a partir do último estado confirmado.

9. DA-009: Observabilidade

O sistema registrará as transições de estado e utilizará identificadores de correlação para acompanhar pedidos e mensagens. Métricas incluirão duração das etapas, falhas, retentativas, compensações pendentes e mensagens encaminhadas à DLQ.

10. Riscos residuais

A Saga não garante isolamento global nem reversão instantânea. Falhas prolongadas de compensação, respostas financeiras desconhecidas e concorrência por estoque exigem políticas específicas de reconciliação, expiração de reservas e intervenção operacional.
