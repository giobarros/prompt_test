## 1. Papel

Atue como um engenheiro de QA especializado em confiabilidade e testes de integração de APIs REST.

## 2. Cenário

Considere o seguinte fluxo de compra:

1. O front-end envia uma solicitação para a API de pedidos.
2. A API consulta o serviço de estoque.
3. A API solicita o processamento ao serviço de pagamento.
4. A API retorna o resultado ao front-end.

Os componentes podem sofrer indisponibilidade, timeout, respostas inesperadas e falhas durante operações que envolvem mais de um serviço.

## 3. Objetivo

Gerar casos de teste de integração focados em falhas parciais e consistência dos dados.

## 4. Cenários obrigatórios

Cubra obrigatoriamente os seguintes cenários:

1. Estoque indisponível antes do início da compra.
2. Timeout na consulta de estoque.
3. Pagamento recusado.
4. Timeout durante o processamento do pagamento.
5. Pagamento aprovado, mas resposta perdida na comunicação com a API.
6. Falha ao atualizar o estado final do pedido.
7. Repetição da requisição após uma resposta inconclusiva.
8. Duas solicitações concorrentes para a última unidade do produto.
9. Resposta inválida ou inesperada de um serviço dependente.
10. Recuperação após a indisponibilidade de um serviço.

## 5. Informações obrigatórias para cada caso

Para cada caso de teste, informe:

- **ID e título.**
- **Falha simulada:** descreva a falha que será provocada.
- **Componentes envolvidos:** identifique os serviços participantes da integração.
- **Pré-condições e estado inicial:** descreva a situação necessária antes da execução.
- **Dados de entrada:** informe os dados necessários para reproduzir o cenário.
- **Etapas de execução:** apresente uma sequência numerada e reproduzível.
- **Como simular a falha:** explique como provocar ou controlar a falha no ambiente de teste.
- **Resultado esperado na API:** descreva a resposta esperada ou marque como **INVESTIGAR** quando o contrato não definir o comportamento.
- **Estado esperado do pedido, estoque e pagamento:** descreva os estados finais esperados, considerando as regras conhecidas.
- **Verificação de duplicidade ou inconsistência:** explique como identificar pedidos duplicados, cobranças repetidas, divergências de estoque ou estados incompatíveis.
- **Critério de aprovação:** estabeleça condições objetivas para avaliar o resultado.
- **Regras que precisam ser confirmadas pela equipe:** registre as dúvidas e lacunas do contrato.

## 6. Regras para análise das falhas

- Não presuma que uma falha HTTP significa que a operação não foi realizada.
- Diferencie timeout antes do processamento de timeout após o processamento, quando aplicável.
- Considere a possibilidade de uma operação ter sido concluída em um serviço, mesmo que a resposta não tenha chegado ao serviço solicitante.
- Avalie os efeitos de falhas parciais sobre o estado do pedido, do estoque e do pagamento.
- Investigue se uma operação pode ser repetida com segurança e quais mecanismos permitem identificar solicitações repetidas.
- Considere condições de concorrência que possam provocar venda acima da quantidade disponível.
- Avalie a recuperação após a indisponibilidade de um serviço e a consistência dos dados após sua retomada.
- Não presuma que os serviços utilizam transações distribuídas, retentativas automáticas, filas, compensações ou mecanismos de idempotência.
- Não invente códigos HTTP, endpoints, payloads ou mecanismos de compensação.
- Quando o contrato não definir como o sistema deve se comportar, marque o item como **INVESTIGAR** e formule uma pergunta objetiva para a equipe responsável.
- Diferencie requisitos confirmados, hipóteses de teste e comportamentos que precisam ser esclarecidos.

## 7. Formato de saída

Apresente os casos em uma tabela organizada, com as seguintes colunas:

| Coluna | Conteúdo |
|---|---|
| ID e título | Identificação única e descrição do cenário. |
| Falha simulada | Falha ou condição que será provocada. |
| Componentes envolvidos | Serviços e componentes participantes. |
| Pré-condições e estado inicial | Estado do sistema antes da execução. |
| Dados de entrada | Dados necessários para reproduzir o cenário. |
| Etapas de execução | Sequência de ações para executar o teste. |
| Como simular a falha | Método para provocar a condição de falha. |
| Resultado esperado na API | Resposta esperada ou indicação **INVESTIGAR**. |
| Estado esperado | Estado do pedido, estoque e pagamento. |
| Verificação de consistência | Como detectar duplicidades e divergências entre os serviços. |
| Critério de aprovação | Condições objetivas para aprovar o teste. |
| Regras a confirmar | Perguntas e informações pendentes. |

Se uma tabela única ficar excessivamente extensa, divida os casos em tabelas menores, preservando todas as colunas e informações obrigatórias.

## 8. Priorização e qualidade

- Elimine casos duplicados e diferencie cenários que tenham causas ou resultados distintos.
- Priorize falhas capazes de deixar os serviços em estados inconsistentes.
- Dê atenção especial a pagamentos aprovados cuja confirmação não chegou à API, falhas na atualização do pedido e solicitações concorrentes.
- Garanta que cada caso tenha objetivo específico, passos reproduzíveis e critérios verificáveis.
- Não estabeleça estados finais arbitrários quando as regras de negócio ainda não estiverem definidas.
- Quando não houver informações suficientes para determinar o resultado correto, descreva o que precisa ser observado e o que a equipe deve esclarecer.

## 9. Revisão crítica

Ao final, apresente uma análise contendo:

- Quantidade total de casos gerados.
- Priorização por risco e impacto potencial.
- Principais riscos de inconsistência identificados.
- Casos que dependem de instrumentação, logs ou acesso aos estados internos dos serviços.
- Lacunas no contrato que impedem definir resultados esperados conclusivos.
- Perguntas prioritárias para a equipe de desenvolvimento.
- Possíveis cenários adicionais que mereçam investigação, sem duplicar os casos existentes.