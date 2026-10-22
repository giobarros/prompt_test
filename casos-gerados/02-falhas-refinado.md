# Casos gerados — Falhas de integração (prompt refinado)

> **Origem:** resposta gerada por uma LLM a partir do prompt refinado e do cenário didático.  
> **Observação:** os testes são propostas de teste. Não foram executados contra uma aplicação real.

## CT-FR01 — Timeout na consulta de estoque
- **Falha simulada:** o serviço de estoque excede o timeout configurado.
- **Componentes:** API de pedidos e estoque.
- **Pré-condições:** serviço de estoque controlável no ambiente de teste; produto existente.
- **Dados de entrada:** compra válida.
- **Execução:**
  1. Configurar o serviço de estoque para atrasar a resposta além do timeout.
  2. Enviar a solicitação à API de pedidos.
  3. Capturar a resposta da API.
  4. Consultar o estado do pedido e verificar se houve solicitação de pagamento.
- **Resultado esperado:** compra não confirmada sem validação de estoque.
- **Verificações:** nenhum estado deve indicar compra concluída; registrar chamadas realizadas aos serviços.
- **Investigar:** duração do timeout, código HTTP e política de retentativa.

## CT-FR02 — Serviço de estoque indisponível
- **Falha simulada:** conexão recusada ou serviço indisponível.
- **Componentes:** API de pedidos, estoque e front-end.
- **Pré-condições:** serviço de estoque interrompido ou stub configurado para falhar.
- **Dados de entrada:** compra válida.
- **Execução:** enviar compra; capturar resposta; consultar pedido e logs de chamadas aos serviços.
- **Resultado esperado:** compra não confirmada; front-end não apresenta sucesso.
- **Verificações:** ausência de confirmação e registro do erro para diagnóstico.
- **Investigar:** status HTTP, mensagem pública e política de retentativa.

## CT-FR03 — Resposta inválida do estoque
- **Falha simulada:** resposta sem campo obrigatório ou com tipo inesperado.
- **Componentes:** API de pedidos e estoque.
- **Pré-condições:** serviço dependente substituível por stub.
- **Dados de entrada:** compra válida.
- **Execução:** configurar o stub para devolver uma resposta incompatível com o contrato que vier a ser definido; enviar compra.
- **Resultado esperado:** a API não deve interpretar uma resposta inválida como confirmação de disponibilidade.
- **Verificações:** compra não confirmada e erro registrado.
- **Investigar:** esquema exato da resposta e código HTTP esperado.

## CT-FR04 — Pagamento recusado após consulta de estoque
- **Falha simulada:** o serviço de pagamento recusa a transação.
- **Componentes:** API de pedidos, estoque e pagamento.
- **Pré-condições:** produto disponível; serviço de pagamento configurado para recusar.
- **Dados de entrada:** compra válida.
- **Execução:** enviar compra; confirmar consulta de estoque; provocar recusa; consultar pedido e estoque.
- **Resultado esperado:** pedido não confirmado.
- **Verificações:** não existe pedido confirmado associado à recusa; verificar se uma reserva de estoque foi liberada.
- **Investigar:** existência de reserva temporária e regra de liberação.

## CT-FR05 — Timeout com resultado do pagamento desconhecido
- **Falha simulada:** o pagamento pode ser processado, mas a resposta não chega à API dentro do timeout.
- **Componentes:** API de pedidos e pagamento.
- **Pré-condições:** stub ou ambiente de teste capaz de processar a operação e descartar/atrasar a resposta.
- **Dados de entrada:** compra válida com identificador de correlação de teste, se disponível.
- **Execução:** enviar compra; fazer o serviço processar o pagamento; impedir que a resposta chegue dentro do limite; consultar o pedido e o estado do pagamento.
- **Resultado esperado:** a API não deve afirmar que o pagamento foi recusado apenas porque houve timeout; o resultado deve ser tratado conforme a política definida.
- **Verificações:** comparar estado do pedido com o resultado real do pagamento.
- **Investigar:** consulta posterior de status, retentativa segura e estados pendentes.

## CT-FR06 — Resposta de pagamento perdida após aprovação
- **Falha simulada:** o serviço aprova o pagamento, mas a resposta é perdida.
- **Componentes:** API de pedidos e pagamento.
- **Pré-condições:** ambiente permite inspecionar o registro de pagamento independentemente da resposta recebida pela API.
- **Dados de entrada:** compra válida.
- **Execução:** enviar compra; configurar a perda da resposta depois da aprovação; consultar o registro de pagamento e o pedido.
- **Resultado esperado:** detectar e documentar qualquer divergência entre pagamento aprovado e pedido não confirmado.
- **Verificações:** nenhuma nova cobrança deve ser presumida necessária sem consultar o estado anterior.
- **Investigar:** mecanismo de reconciliação e recuperação.

## CT-FR07 — Repetição após resposta inconclusiva
- **Falha simulada:** o cliente repete a solicitação depois de timeout.
- **Componentes:** front-end, API de pedidos e pagamento.
- **Pré-condições:** primeira solicitação pode ter sido processada; suporte a idempotência ainda precisa ser confirmado.
- **Dados de entrada:** duas solicitações equivalentes; usar a mesma chave de idempotência se o contrato definir esse mecanismo.
- **Execução:** enviar a primeira solicitação; provocar perda/atraso da resposta; repetir a solicitação; consultar pedidos e pagamentos.
- **Resultado esperado:** se houver idempotência contratada, a repetição não gera nova operação financeira nem pedido duplicado.
- **Verificações:** quantidade de pedidos e transações associadas à solicitação.
- **Investigar:** comportamento sem chave, escopo e prazo de validade da chave.

## CT-FR08 — Falha ao persistir o estado final do pedido
- **Falha simulada:** pagamento aprovado, mas atualização do pedido falha.
- **Componentes:** API de pedidos, pagamento e armazenamento do pedido.
- **Pré-condições:** ambiente permite provocar falha na persistência; pagamento controlável.
- **Dados de entrada:** compra válida.
- **Execução:** aprovar o pagamento; provocar falha na atualização do pedido; consultar pedido e pagamento.
- **Resultado esperado:** identificar a inconsistência sem mascará-la como sucesso completo.
- **Verificações:** estado do pedido, estado do pagamento e registros de erro.
- **Investigar:** mecanismo de compensação, reconciliação ou reprocessamento; não presumir estorno automático.

## CT-FR09 — Concorrência pela última unidade
- **Falha simulada:** duas compras simultâneas tentam consumir a última unidade disponível.
- **Componentes:** API de pedidos e estoque; pagamento se o fluxo chegar a essa etapa.
- **Pré-condições:** estoque inicial igual a uma unidade; ambiente de teste capaz de iniciar requisições concorrentes.
- **Dados de entrada:** duas solicitações válidas para o mesmo produto.
- **Execução:**
  1. Preparar estoque com uma unidade.
  2. Iniciar as duas solicitações quase simultaneamente usando uma barreira de sincronização.
  3. Aguardar as respostas.
  4. Consultar estoque, pedidos e pagamentos.
- **Resultado esperado:** não vender duas unidades quando só havia uma disponível.
- **Verificações:** no máximo uma compra confirmada e nenhum estado inconsistente.
- **Investigar:** garantia de atomicidade no serviço de estoque e tratamento da solicitação perdedora.

## CT-FR10 — Recuperação após indisponibilidade
- **Falha simulada:** serviço de pagamento fica indisponível e depois volta a responder.
- **Componentes:** API de pedidos, pagamento e front-end.
- **Pré-condições:** serviço pode ser desligado e restaurado no ambiente de teste.
- **Dados de entrada:** compra válida.
- **Execução:** iniciar compra durante a indisponibilidade; restaurar o serviço; observar se o sistema permite uma nova tentativa segura ou recuperação controlada.
- **Resultado esperado:** o sistema não deve duplicar pedidos ou pagamentos durante a recuperação.
- **Verificações:** estado final dos pedidos, estoque e pagamentos após a recuperação.
- **Investigar:** retentativas automáticas, fila de reprocessamento e critérios para encerrar uma compra pendente.

## CT-FR11 — Resposta inesperada do pagamento
- **Falha simulada:** serviço retorna estado desconhecido ou resposta incompleta.
- **Componentes:** API de pedidos e pagamento.
- **Pré-condições:** serviço dependente controlável por stub.
- **Dados de entrada:** compra válida.
- **Execução:** configurar resposta não prevista; enviar compra; observar o tratamento pela API.
- **Resultado esperado:** estado desconhecido não é interpretado automaticamente como aprovação.
- **Verificações:** pedido não confirmado como pago sem evidência válida; erro registrado.
- **Investigar:** estados permitidos pelo contrato e resposta HTTP apropriada.

## CT-FR12 — Front-end diante de falha de integração
- **Falha simulada:** API retorna falha após timeout de um serviço dependente.
- **Componentes:** front-end e API de pedidos.
- **Pré-condições:** cenário de falha reproduzível.
- **Dados de entrada:** compra válida.
- **Execução:** iniciar compra pela interface; provocar a falha; observar mensagem, indicador de carregamento e possibilidade de nova tentativa.
- **Resultado esperado:** a interface não apresenta sucesso falso e informa que o resultado pode precisar ser verificado antes de repetir a operação, quando aplicável.
- **Verificações:** mensagem coerente, ausência de confirmação visual indevida e comportamento seguro ao tentar novamente.
- **Investigar:** texto de interface e política de repetição para resultado inconclusivo.

## Revisão crítica
- A versão refinada inclui formas de simulação, inspeção de estado e critérios de aprovação mais claros que um checklist genérico.
- Os casos ainda precisam ser adaptados ao contrato real antes da execução: não há endpoints, payloads nem códigos HTTP definidos.
- CT-FR05 e CT-FR06 diferenciam timeout de falha efetiva do pagamento; essa distinção evita assumir que uma resposta ausente significa operação não realizada.
- CT-FR09 exige infraestrutura de concorrência e acesso ao estado do estoque para que a verificação seja confiável.
- Nenhum mecanismo de estorno, compensação ou idempotência deve ser considerado existente sem confirmação no contrato.
