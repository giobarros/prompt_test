# Casos gerados — Falhas de integração (prompt simples)

> **Origem:** resposta gerada por uma LLM a partir do prompt simples.  
> **Observação:** checklist inicial; resultados específicos dependem do contrato da aplicação.

## CT-FS01 — Timeout no serviço de estoque
- **Falha simulada:** a consulta ao estoque demora mais que o limite de espera.
- **Entrada:** solicitação válida de compra.
- **Procedimento:** enviar a compra com o serviço de estoque configurado para responder com atraso.
- **Resultado esperado:** a API não confirma a compra sem a verificação necessária.
- **Investigar:** duração do timeout, status HTTP e retentativas.

## CT-FS02 — Serviço de estoque indisponível
- **Falha simulada:** serviço de estoque fora do ar.
- **Procedimento:** enviar uma compra enquanto o serviço não responde.
- **Resultado esperado:** a falha é tratada e não há confirmação indevida do pedido.
- **Investigar:** formato do erro e comportamento de recuperação.

## CT-FS03 — Pagamento recusado
- **Falha simulada:** o serviço de pagamento retorna uma recusa.
- **Procedimento:** enviar uma compra válida e observar o resultado.
- **Resultado esperado:** o pedido não fica confirmado.
- **Investigar:** liberação de estoque previamente reservado.

## CT-FS04 — Timeout no pagamento
- **Falha simulada:** a API não recebe resposta dentro do prazo.
- **Procedimento:** configurar atraso no serviço de pagamento e iniciar a compra.
- **Resultado esperado:** não informar pagamento aprovado sem confirmação.
- **Investigar:** se o pagamento pode ter sido processado apesar do timeout.

## CT-FS05 — Resposta inválida do serviço dependente
- **Falha simulada:** o serviço de estoque ou pagamento devolve uma resposta incompleta ou malformada.
- **Procedimento:** substituir a resposta normal por uma resposta inválida no ambiente de teste.
- **Resultado esperado:** a API trata a resposta sem confirmar indevidamente a compra.
- **Investigar:** validações de contrato e código de erro esperado.

## CT-FS06 — Repetição após erro de comunicação
- **Falha simulada:** a resposta da primeira compra se perde e o cliente tenta novamente.
- **Procedimento:** enviar a compra, interromper a resposta e reenviar a solicitação.
- **Resultado esperado:** investigar se ocorre duplicidade de pedido ou cobrança.
- **Investigar:** política de idempotência.

## CT-FS07 — Falha parcial após aprovação do pagamento
- **Falha simulada:** o pagamento é aprovado, mas a API não consegue atualizar o pedido.
- **Procedimento:** provocar falha na atualização do estado após a aprovação do pagamento.
- **Resultado esperado:** identificar se o sistema fica com pagamento aprovado e pedido não confirmado.
- **Investigar:** mecanismo de reconciliação ou compensação.

## CT-FS08 — Duas compras concorrentes para a última unidade
- **Falha simulada:** duas solicitações tentam comprar simultaneamente a última unidade.
- **Procedimento:** iniciar duas compras em paralelo e consultar pedidos e estoque.
- **Resultado esperado:** investigar se o sistema impede vender mais unidades do que existem.
- **Investigar:** atomicidade da reserva e comportamento em concorrência.

## Revisão crítica
- O prompt simples permite identificar riscos comuns, mas não define detalhes suficientes para execução reproduzível.
- A resposta é um checklist: faltam payloads concretos, mecanismos de simulação, observabilidade e verificações detalhadas do estado final.
- Os resultados esperados devem ser confirmados com o contrato antes de serem usados como critérios de aceitação.
