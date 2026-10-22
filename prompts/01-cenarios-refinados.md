## 1. Papel

Atue como um QA especializado em testes de integração de APIs REST.

## 2. Cenário

Considere uma loja virtual composta por:

- **Front-end:** permite consultar produtos e finalizar pedidos.
- **API de pedidos:** recebe as solicitações e coordena o processamento.
- **Serviço de estoque:** informa a disponibilidade dos produtos e permite reservar ou baixar quantidades.
- **Serviço de pagamento:** processa pagamentos e informa aprovação ou recusa.

## 3. Regras conhecidas

- A API de pedidos precisa verificar a disponibilidade do produto antes de concluir a compra.
- Um pagamento recusado não deve resultar em um pedido confirmado.
- Uma compra confirmada deve refletir corretamente o estado do pedido e a movimentação de estoque.
- Uma repetição da mesma solicitação não deve gerar pedidos ou cobranças duplicadas, caso exista suporte à idempotência.

> **Importante:** as regras acima são premissas deste cenário didático, não evidências de uma implementação real. Quando o contrato não definir o comportamento exato, marque-o como **INVESTIGAR** e formule a pergunta necessária para esclarecer a regra.

## 4. Tarefa

Gere casos de teste de integração que cubram os seguintes cenários:

1. Compra concluída com sucesso, envolvendo os três serviços.
2. Produto sem estoque.
3. Pagamento recusado.
4. Falha de comunicação entre a API e o serviço de estoque.
5. Falha de comunicação entre a API e o serviço de pagamento.
6. Repetição de uma solicitação de compra.
7. Inconsistências entre o estado do pedido, o estoque e o pagamento.
8. Respostas apresentadas pelo front-end para sucesso e falha.

Não gere apenas testes isolados de endpoints. Cada caso deve verificar a comunicação entre pelo menos dois componentes.

## 5. Formato de saída

Para cada caso de teste, apresente:

- **ID e título.**
- **Objetivo:** descreva o comportamento que será validado.
- **Componentes envolvidos:** identifique os componentes participantes da integração.
- **Pré-condições:** informe o estado necessário para executar o teste.
- **Dados de entrada:** descreva os dados necessários sem inventar estruturas não especificadas.
- **Etapas de execução:** apresente uma sequência clara, numerada e reproduzível.
- **Resultado HTTP esperado:** informe o resultado quando aplicável e definido pelo contrato. Caso contrário, marque como **INVESTIGAR**.
- **Estado final esperado:** descreva os estados esperados do pedido, do estoque e do pagamento, conforme as regras conhecidas.
- **Critério de aprovação:** estabeleça condições objetivas para determinar se o teste passou.
- **Informações não especificadas:** identifique as lacunas e formule perguntas para esclarecer cada regra pendente.

## 6. Restrições

- Não invente endpoints, payloads, códigos HTTP, mecanismos de compensação ou garantias de idempotência.
- Não apresente hipóteses como se fossem regras confirmadas do sistema.
- Quando faltar informação para definir uma expectativa verificável, identifique a lacuna como **INVESTIGAR** e formule a pergunta necessária.
- Diferencie os resultados efetivamente verificáveis das informações que dependem de um contrato técnico ainda não fornecido.
- Não presuma que uma falha de comunicação implica automaticamente cancelamento, retentativa ou liberação de estoque.
- Não considere uma compra bem-sucedida apenas porque a API retornou uma resposta de sucesso; avalie também a consistência entre os componentes envolvidos.

## 7. Diversidade e qualidade dos testes

- Priorize a diversidade dos cenários e evite casos duplicados.
- Inclua caminhos de sucesso, falhas funcionais, falhas de comunicação e problemas de consistência.
- Diferencie os testes funcionais de integração dos testes de interface.
- Nos testes de interface, verifique se o front-end apresenta corretamente o resultado recebido da API.
- Nos testes de integração, verifique a comunicação entre componentes e os efeitos observáveis das operações.
- Considere condições de repetição de solicitações e falhas parciais sem presumir comportamentos que não foram definidos.
- Garanta que cada caso tenha objetivo específico, etapas reproduzíveis e critérios de aprovação verificáveis.

## 8. Revisão crítica

Ao final, apresente uma revisão crítica contendo:

- Quantidade total de casos gerados.
- Cenários cobertos e eventuais lacunas de cobertura.
- Possíveis duplicidades entre os casos.
- Casos que não podem ser executados sem informações adicionais.
- Perguntas prioritárias para esclarecer o contrato da API e as regras de negócio.
- Limitações dos testes gerados, considerando que o cenário é didático e não foi fornecida uma implementação real.