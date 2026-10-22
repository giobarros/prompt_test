# Casos gerados — Cenários de integração (prompt refinado)

> **Origem:** resposta gerada por uma LLM a partir do prompt refinado e do cenário didático.  
> **Observação:** as regras abaixo são premissas do cenário, não evidências de uma aplicação real. Endpoints, payloads e códigos HTTP não foram inventados.

## CT-R01 — Compra concluída com sucesso
- **Objetivo:** verificar o encadeamento entre API de pedidos, estoque e pagamento.
- **Componentes:** front-end, API de pedidos, estoque e pagamento.
- **Pré-condições:** produto disponível; serviços operacionais; dados de compra válidos.
- **Dados de entrada:** produto existente, quantidade dentro do estoque e dados de pagamento aceitos pelo serviço.
- **Etapas:**
  1. Enviar a compra pelo front-end.
  2. Confirmar que a API consulta a disponibilidade do produto.
  3. Confirmar que o pagamento é solicitado após a validação exigida pelo fluxo.
  4. Consultar o resultado retornado pela API.
  5. Verificar o estado final do pedido, pagamento e estoque.
- **Resultado esperado:** pedido confirmado somente após as condições de sucesso; pagamento aprovado; estoque consistente com a compra.
- **Critério de aprovação:** os componentes apresentam estados compatíveis e o front-end informa sucesso apenas quando a API confirma a conclusão.
- **Investigar:** ordem exata das operações, códigos HTTP e formato dos estados.

## CT-R02 — Produto sem estoque
- **Objetivo:** garantir que uma compra não seja confirmada quando não há quantidade suficiente.
- **Componentes:** API de pedidos e serviço de estoque; front-end.
- **Pré-condições:** estoque menor que a quantidade solicitada.
- **Dados de entrada:** quantidade solicitada maior que a quantidade disponível.
- **Etapas:** enviar a compra; verificar a consulta ao estoque; inspecionar se houve chamada ao pagamento; consultar o pedido.
- **Resultado esperado:** pedido não confirmado. A cobrança não deve ser efetivada para uma compra rejeitada por falta de estoque.
- **Critério de aprovação:** não há pedido confirmado nem cobrança efetivada.
- **Investigar:** se a API chama ou não o serviço de pagamento nesse fluxo e qual resposta HTTP é definida.

## CT-R03 — Pagamento recusado
- **Objetivo:** validar o tratamento da recusa de pagamento entre serviços.
- **Componentes:** API de pedidos, estoque, pagamento e front-end.
- **Pré-condições:** produto disponível; serviço de pagamento configurado para recusar a transação.
- **Dados de entrada:** compra válida e dados que produzam uma recusa controlada no serviço de pagamento.
- **Etapas:** enviar a compra; confirmar a consulta de estoque; observar a recusa do pagamento; consultar o estado final do pedido e do estoque.
- **Resultado esperado:** pedido não confirmado; recusa refletida na resposta; estoque sem inconsistência.
- **Critério de aprovação:** o front-end não apresenta sucesso e não existe pedido confirmado associado ao pagamento recusado.
- **Investigar:** se uma reserva temporária de estoque precisa ser liberada e em qual momento.

## CT-R04 — Falha ao consultar o estoque
- **Objetivo:** verificar que a API não conclui uma compra sem obter a validação necessária.
- **Componentes:** API de pedidos, estoque e front-end.
- **Pré-condições:** o serviço de estoque retorna erro ou fica indisponível.
- **Dados de entrada:** compra válida.
- **Etapas:** simular falha no serviço de estoque; enviar a compra; verificar a resposta da API; consultar se pedido ou pagamento foram criados.
- **Resultado esperado:** compra não confirmada; nenhuma cobrança deve ser considerada concluída sem o processamento previsto.
- **Critério de aprovação:** a interface informa que não foi possível concluir a compra e os estados não indicam sucesso.
- **Investigar:** status HTTP, retentativas e se deve ser criado um pedido pendente.

## CT-R05 — Falha ao processar pagamento
- **Objetivo:** verificar o estado do pedido quando o serviço de pagamento está indisponível.
- **Componentes:** API de pedidos, estoque, pagamento e front-end.
- **Pré-condições:** estoque disponível; pagamento indisponível.
- **Dados de entrada:** compra válida.
- **Etapas:** permitir a consulta ao estoque; interromper ou simular erro no pagamento; consultar o pedido e o estoque.
- **Resultado esperado:** pedido não deve ser marcado como pago sem confirmação.
- **Critério de aprovação:** a API e o front-end não informam uma compra paga sem evidência de aprovação.
- **Investigar:** estado pendente/cancelado e política de liberação do estoque.

## CT-R06 — Requisição repetida
- **Objetivo:** verificar o comportamento de uma solicitação enviada mais de uma vez.
- **Componentes:** front-end, API de pedidos e pagamento.
- **Pré-condições:** o cliente não recebeu a resposta da primeira tentativa.
- **Dados de entrada:** mesma solicitação enviada duas vezes; usar a mesma chave de idempotência somente se o contrato vier a defini-la.
- **Etapas:** enviar a primeira solicitação; reenviar; consultar os pedidos e as transações de pagamento.
- **Resultado esperado:** se houver idempotência contratada, a repetição não deve criar pedido ou cobrança duplicada.
- **Critério de aprovação:** conforme a regra de idempotência definida.
- **Investigar:** se existe chave de idempotência, sua validade e o comportamento sem essa chave.

## CT-R07 — Inconsistência entre estados
- **Objetivo:** detectar uma situação em que o pedido, o estoque e o pagamento indiquem resultados incompatíveis.
- **Componentes:** API de pedidos, estoque e pagamento.
- **Pré-condições:** ambiente de teste permite consultar os estados de cada serviço.
- **Dados de entrada:** compra válida seguida de uma falha controlada em uma etapa do fluxo.
- **Etapas:** executar a compra; provocar a falha; consultar separadamente o estado de cada componente; comparar com as regras do cenário.
- **Resultado esperado:** nenhum estado deve contradizer as regras de negócio estabelecidas.
- **Critério de aprovação:** estados finais coerentes ou estado intermediário explicitamente definido.
- **Investigar:** mecanismo de compensação e política de recuperação.

## CT-R08 — Mensagem no front-end após falha
- **Objetivo:** verificar a integração entre a resposta da API e a interface.
- **Componentes:** front-end e API de pedidos.
- **Pré-condições:** possibilidade de provocar uma falha conhecida.
- **Dados de entrada:** tentativa de compra que resulte em falha.
- **Etapas:** iniciar a compra pela interface; provocar a falha; verificar a mensagem e o estado visual do pedido.
- **Resultado esperado:** a interface comunica a falha sem apresentar a compra como concluída.
- **Critério de aprovação:** mensagem coerente com a resposta recebida e ausência de indicação enganosa de sucesso.
- **Investigar:** textos e tratamento específico de cada categoria de erro.

## Revisão crítica
- Os casos foram estruturados com componentes, etapas e critérios verificáveis.
- Ainda não são executáveis diretamente: faltam endpoints, payloads, códigos HTTP e meios concretos de consultar os estados dos serviços.
- CT-R06 depende de uma política de idempotência que não foi especificada.
- CT-R07 precisa de uma regra explícita de recuperação/compensação para definir o estado final exato.
