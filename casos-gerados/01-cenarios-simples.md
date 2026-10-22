# Casos gerados — Cenários de integração (prompt simples)

> **Origem:** resposta gerada por uma LLM a partir do prompt simples.  
> **Observação:** como o prompt não fornece contrato técnico, os casos abaixo são genéricos e alguns resultados dependem de regras ainda não definidas. Não são testes executados.

## CT-S01 — Compra com sucesso
- **Objetivo:** verificar se uma compra pode ser concluída quando há estoque e o pagamento é aprovado.
- **Pré-condições:** produto disponível; serviço de pagamento operacional.
- **Dados de entrada:** solicitação de compra com um produto disponível e dados de pagamento válidos.
- **Etapas:**
  1. Enviar uma solicitação de compra à API de pedidos.
  2. Verificar se a API consulta o estoque.
  3. Verificar se a API solicita o pagamento.
  4. Consultar o resultado do pedido.
- **Resultado esperado:** compra concluída; pagamento aprovado; estoque atualizado.
- **Ponto a confirmar:** formato da resposta, códigos HTTP e momento exato da baixa do estoque.

## CT-S02 — Compra de produto sem estoque
- **Objetivo:** verificar a tentativa de compra de um produto indisponível.
- **Pré-condições:** quantidade disponível insuficiente.
- **Dados de entrada:** compra de quantidade superior à disponível.
- **Etapas:** enviar a compra e observar as chamadas aos serviços dependentes.
- **Resultado esperado:** compra não confirmada e nenhuma cobrança efetivada.
- **Ponto a confirmar:** se o sistema reserva estoque antes do pagamento e qual resposta retorna.

## CT-S03 — Pagamento recusado
- **Objetivo:** verificar o fluxo quando o pagamento é recusado.
- **Pré-condições:** produto disponível; serviço de pagamento retorna recusa.
- **Dados de entrada:** solicitação de compra válida com meio de pagamento recusado pelo serviço.
- **Etapas:** enviar a compra e consultar o estado final do pedido e do estoque.
- **Resultado esperado:** pedido não confirmado.
- **Ponto a confirmar:** se o estoque é reservado previamente e, em caso afirmativo, como é liberado.

## CT-S04 — Serviço de estoque indisponível
- **Objetivo:** verificar o comportamento da API quando não consegue consultar o estoque.
- **Pré-condições:** serviço de estoque indisponível.
- **Dados de entrada:** solicitação de compra válida.
- **Etapas:** enviar a compra e observar a resposta da API.
- **Resultado esperado:** não confirmar a compra sem validar a disponibilidade.
- **Ponto a confirmar:** código HTTP, política de retentativa e mensagem exibida ao usuário.

## CT-S05 — Serviço de pagamento indisponível
- **Objetivo:** verificar o comportamento quando o serviço de pagamento não responde.
- **Pré-condições:** estoque disponível; serviço de pagamento indisponível.
- **Dados de entrada:** solicitação de compra válida.
- **Etapas:** enviar a compra e verificar o estado do pedido e do estoque.
- **Resultado esperado:** pedido não deve ser apresentado como pago sem confirmação do serviço.
- **Ponto a confirmar:** se a compra fica pendente, é cancelada ou passa por compensação.

## CT-S06 — Repetição da mesma solicitação
- **Objetivo:** investigar se repetir uma solicitação pode duplicar o pedido ou a cobrança.
- **Pré-condições:** uma compra foi enviada, mas a resposta não foi recebida pelo cliente.
- **Dados de entrada:** duas solicitações equivalentes, enviadas em sequência.
- **Etapas:** enviar a primeira solicitação; reenviá-la; consultar pedidos e pagamentos.
- **Resultado esperado:** depende da existência de uma política de idempotência.
- **Ponto a confirmar:** uso de chave de idempotência e regra para reconhecer solicitações repetidas.

## CT-S07 — Resposta de erro apresentada no front-end
- **Objetivo:** verificar se o front-end comunica corretamente uma falha devolvida pela API.
- **Pré-condições:** API retorna uma falha de negócio ou de comunicação.
- **Dados de entrada:** tentativa de compra que resulte em falha.
- **Etapas:** iniciar a compra pela interface e observar a mensagem apresentada.
- **Resultado esperado:** mensagem compreensível, sem indicar compra concluída.
- **Ponto a confirmar:** textos e comportamentos definidos para cada tipo de erro.

## Revisão crítica
- Os casos cobrem fluxos positivos, negativos e algumas falhas de comunicação.
- O prompt simples não determina contrato, payloads, códigos HTTP ou estados válidos; por isso, os casos ainda não são diretamente executáveis.
- Alguns resultados esperados são princípios gerais, não garantias comprovadas de uma implementação.
- A repetição de requisição e o estado após falhas exigem regras explícitas antes de serem usados como critérios definitivos.
