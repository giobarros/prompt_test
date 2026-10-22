# Contrato do Cenário Didático — Loja Virtual

## 1. Componentes

- **Front-end:** envia solicitações de compra e apresenta os resultados.
- **API de pedidos:** coordena o fluxo de compra.
- **Serviço de estoque:** consulta e altera a disponibilidade dos produtos.
- **Serviço de pagamento:** processa pagamentos.

## 2. Regras adotadas

- Uma compra só pode ser confirmada quando as condições necessárias para sua conclusão forem atendidas.
- Um pagamento recusado não pode resultar em pedido confirmado.
- O estado final do pedido deve ser consistente com as operações realizadas nos serviços dependentes.
- A duplicidade de pedidos e cobranças deve ser investigada em situações de repetição de requisições.

## 3. Informações não definidas

- Endpoints e métodos HTTP.
- Estrutura dos payloads e dos cabeçalhos.
- Códigos HTTP de sucesso e de erro.
- Estratégia de reserva e liberação de estoque.
- Política de timeout e retentativas.
- Suporte à idempotência e forma de identificação de requisições repetidas.
- Política de compensação quando uma operação parcial falha.
- Estados válidos e transições de estado do pedido.
- Mensagens exibidas pelo front-end.

## 4. Orientações para a geração dos testes

- Os casos que dependam das informações não definidas devem ser marcados como **INVESTIGAR**.
- Não considerar as regras didáticas como prova de que uma aplicação real possui essas garantias.