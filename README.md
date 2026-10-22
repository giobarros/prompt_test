# Testes de Integração de APIs REST com GenAI

## 1. Sobre o projeto

Este repositório foi desenvolvido como parte de uma atividade acadêmica sobre o uso de Inteligência Artificial Generativa (GenAI) no apoio à criação de testes de integração.

O objetivo é explorar como diferentes estratégias de prompting influenciam a geração de casos de teste, considerando a comunicação entre componentes, o tratamento de falhas e a consistência dos dados em uma loja virtual.

## 2. Objetivos

- Explorar o uso de LLMs na geração de casos de teste de integração.
- Comparar prompts simples e refinados.
- Avaliar a influência das instruções na qualidade, na diversidade e no detalhamento dos casos gerados.
- Identificar lacunas, suposições e comportamentos que precisam ser confirmados pela equipe de desenvolvimento.
- Investigar cenários de falhas parciais e inconsistências entre serviços.

## 3. Cenário de integração

O cenário didático representa uma loja virtual composta pelos seguintes componentes:

- **Front-end:** permite consultar produtos, enviar solicitações de compra e visualizar os resultados.
- **API de pedidos:** recebe as solicitações e coordena o fluxo de compra.
- **Serviço de estoque:** consulta a disponibilidade dos produtos e gerencia as quantidades.
- **Serviço de pagamento:** processa os pagamentos e informa aprovação ou recusa.

### 3.1. Fluxo de compra

1. O front-end envia uma solicitação de compra para a API de pedidos.
2. A API consulta a disponibilidade do produto no serviço de estoque.
3. A API solicita o processamento do pagamento.
4. A API retorna o resultado da operação ao front-end.

Os testes consideram tanto o fluxo normal de compra quanto situações de falha na comunicação entre os componentes.

> **Importante:** o cenário é didático. Os casos de teste não comprovam o comportamento de uma aplicação real e não devem ser considerados executados contra um sistema implementado.

## 4. Estrutura do repositório

A organização proposta separa o contrato do cenário, os prompts utilizados e os casos de teste gerados.

```text
.
├── README.md
├── cenario/
│   └── contrato-cenario.md
├── prompts/
│   ├── 01-cenarios-simples.md
│   ├── 01-cenarios-refinados.md
│   └── 02-falhas-refinado.md
    └── 02-falhas-simples.md
└── casos-gerados/
    ├── 01-cenarios-simples.md
    ├── 01-cenarios-refinado.md
    ├── 02-falhas-simples.md
    └── 02-falhas-refinado.md
```

> **Observação:** essa estrutura é uma sugestão. Os nomes apresentados devem ser ajustados para corresponder exatamente aos arquivos existentes no repositório.

### 4.1. Descrição dos diretórios

- `cenario/`: contém a descrição do cenário didático e as regras conhecidas.
- `prompts/`: reúne os prompts utilizados para orientar a geração dos casos de teste.
- `casos-gerados/`: armazena os resultados gerados a partir dos diferentes prompts.
- `README.md`: apresenta o objetivo, a organização e a metodologia do projeto.

## 5. Estratégia de prompting

Foram consideradas diferentes abordagens para gerar casos de teste e analisar a influência das instruções fornecidas à LLM.

### 5.1. Prompt simples

Apresenta uma solicitação direta para a geração de casos de teste, com contexto e restrições limitados.

Essa abordagem permite observar quais informações a LLM inclui espontaneamente e quais lacunas ou suposições aparecem nos resultados.

### 5.2. Prompt refinado

Acrescenta contexto, regras conhecidas, cenários obrigatórios, critérios de aprovação e restrições para reduzir suposições indevidas.

O objetivo é obter casos mais estruturados, claros e verificáveis, com identificação explícita das informações que ainda precisam ser confirmadas.

### 5.3. Prompt focado em falhas parciais

Direciona a geração para problemas de confiabilidade e consistência, incluindo:

- Timeouts na comunicação entre serviços.
- Indisponibilidade de componentes.
- Pagamentos aprovados cuja resposta não chega à API.
- Falhas na atualização do estado do pedido.
- Repetição de solicitações.
- Solicitações concorrentes para a última unidade disponível.
- Respostas inválidas ou inesperadas.
- Recuperação após a indisponibilidade de um serviço.

Essa abordagem busca ampliar a cobertura de situações capazes de deixar os componentes em estados inconsistentes.

## 6. Cenários de teste considerados

Os casos de teste podem abranger os seguintes cenários:

### 6.1. Fluxos funcionais

- Compra concluída com sucesso.
- Produto sem estoque.
- Pagamento recusado.
- Comunicação entre o front-end e a API de pedidos.

### 6.2. Falhas de comunicação

- Indisponibilidade do serviço de estoque.
- Timeout durante a consulta de estoque.
- Indisponibilidade do serviço de pagamento.
- Timeout durante o processamento do pagamento.
- Resposta perdida após o processamento de uma operação.
- Respostas inválidas ou inesperadas de serviços dependentes.

### 6.3. Consistência e confiabilidade

- Falha na atualização do estado final do pedido.
- Repetição de solicitações após respostas inconclusivas.
- Possibilidade de pedidos ou cobranças duplicadas.
- Solicitações concorrentes para a última unidade do produto.
- Inconsistências entre os estados do pedido, do estoque e do pagamento.
- Recuperação após a indisponibilidade de um serviço.

### 6.4. Integração com a interface

- Apresentação do resultado de uma compra bem-sucedida.
- Apresentação de mensagens de falha.
- Correspondência entre a resposta da API e o estado visual apresentado ao usuário.

Os testes de integração devem verificar a comunicação entre componentes e os efeitos observáveis das operações. Os testes de interface devem verificar a apresentação correta dos resultados ao usuário.

## 7. Critérios para avaliar os resultados

A análise dos casos gerados considera os seguintes critérios:

- **Cobertura:** variedade de cenários contemplados pelos casos de teste.
- **Clareza:** facilidade de compreender os objetivos, as pré-condições, os dados e as etapas.
- **Verificabilidade:** existência de resultados esperados e critérios de aprovação objetivos.
- **Diversidade:** capacidade de explorar diferentes condições sem duplicação desnecessária.
- **Consistência:** compatibilidade das expectativas com as regras estabelecidas no cenário.
- **Rastreabilidade:** identificação das regras relacionadas a cada teste e das informações que precisam ser confirmadas.
- **Confiabilidade:** cuidado para não assumir que uma falha de comunicação significa que uma operação não foi realizada.
- **Utilidade prática:** possibilidade de transformar os casos gerados em testes executáveis após a definição do contrato técnico.

## 8. Limitações e pontos de atenção

Como o cenário não define um contrato técnico completo, algumas informações permanecem em aberto:

- Endpoints e métodos HTTP.
- Estrutura dos payloads e dos cabeçalhos.
- Códigos HTTP de sucesso e de erro.
- Políticas de timeout e retentativas.
- Suporte à idempotência e identificação de solicitações repetidas.
- Estratégias de reserva e liberação de estoque.
- Mecanismos de compensação e recuperação.
- Estados válidos e transições de estado do pedido.
- Tratamento de pagamentos cujo resultado é desconhecido pela API.
- Mensagens apresentadas pelo front-end em cada situação de falha.

Os casos que dependem dessas informações devem ser marcados como **INVESTIGAR**, acompanhados de perguntas que permitam esclarecer as regras pendentes.

Além disso, os casos gerados por uma LLM não devem ser considerados corretos automaticamente. Eles precisam ser revisados para identificar expectativas arbitrárias, lacunas, duplicidades e condições que não podem ser verificadas com as informações disponíveis.

Como não foi fornecida uma implementação real, a geração dos casos não comprova que os testes foram executados ou que os comportamentos descritos foram observados em um sistema.

## 9. Análise da influência dos prompts

A comparação entre prompts simples e refinados permite investigar como a estrutura das instruções influencia os resultados gerados pela LLM.

Um prompt mais simples pode produzir casos úteis, mas também pode deixar de explorar falhas específicas, apresentar critérios genéricos ou não identificar informações ausentes.

Um prompt refinado pode direcionar melhor a geração ao estabelecer contexto, regras, formato de saída, restrições e critérios de aprovação. Entretanto, maior detalhamento não garante, por si só, que todos os casos sejam corretos ou completos.

A avaliação deve considerar os resultados efetivamente obtidos em cada experimento, observando:

- Quais cenários foram contemplados em cada abordagem.
- Quais casos adicionais surgiram após o refinamento do prompt.
- Se houve redução de suposições não fundamentadas.
- Se os resultados apresentaram critérios de aprovação mais claros.
- Se ainda existem lacunas, duplicidades ou expectativas que precisam de confirmação.

As conclusões devem ser baseadas na comparação dos casos realmente gerados, evitando atribuir melhorias que não tenham sido observadas.

## 10. Conclusão

O uso de Inteligência Artificial Generativa pode apoiar a elaboração de casos de teste de integração e ampliar a exploração de cenários, especialmente quando os prompts apresentam contexto, regras e critérios claros.

A comparação entre diferentes estratégias de prompting permite avaliar como as instruções influenciam a cobertura, a diversidade e a qualidade dos casos gerados.

Entretanto, a qualidade dos resultados depende das informações fornecidas, da clareza das regras e da revisão crítica dos casos. Informações ausentes no contrato devem ser identificadas explicitamente, e não substituídas por suposições apresentadas como fatos.

Portanto, a GenAI deve ser utilizada como ferramenta de apoio ao trabalho de QA, complementando a análise técnica e a validação humana, e não como substituta dessas atividades.