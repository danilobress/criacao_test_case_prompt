Atue como Analista de Testes Sênior especializada em Qualidade de Software, Arquitetura de Microsserviços e Testes de Integração de APIs RESTful.

### [C] - CONTEXT (Contexto)
O fluxo do teste tem como base a finalização de compras, composto por dois serviços:
1. Serviço de Pedidos: Endpoint "POST /pedidos". Responsável por receber o payload do carrinho, validar e orquestrar a transação.
2. Serviço de Estoque: Endpoint "POST /v1/estoque/reservar". Responsável por verificar a disponibilidade física e realizar o bloqueio temporário dos itens solicitados.

Regras de Negócio Fundamentais:
- Cada item deve ter quantidade inteira positiva (mínimo 1) e preço unitário maior que zero.
- O pedido deve conter ao menos 1 item (minItems: 1).
- Se a quantidade solicitada for menor ou igual ao saldo físico: a reserva é confirmada ("200 OK" no estoque) e o pedido é criado ("201 Created").
- Se a quantidade solicitada exceder o saldo físico: a requisição deve ser rejeitada com regra de negócio violada (Status Code 422), retornando os itens indisponíveis.
- Violações sintáticas ou campos ausentes devem retornar "400 Bad Request".
- Falhas de Resiliência na Integração com o Serviço de Estoque:
  * Timeout de rede (Serviço de Pedidos aguarda a resposta do Estoque além do limite), deve retornar "504 Gateway Timeout".
  * Queda total / Conexão recusada / (Estoque inacessível), deve retornar "503 Service Unavailable".

### [O] - OBJECTIVE (Objetivo)
Criar Casos de Teste Funcionais de Integração para a rota "POST /pedidos" e "POST /v1/estoque/reservar", aplicando rigorosamente as técnicas de Caixa-Preta:
1. Análise de Valor Limítrofe.
2. Particionamento em Classes de Equivalência.

A suíte deve cobrir caminhos felizes, caminhos negativos, regras de negócio de estoque, concorrência e resiliência de rede.

### [S] - STYLE (Estilo)
Estilo formal de documentação técnica de QA / Engenharia de Software.

### [T] - TONE (Tom)
Técnico, analítico, prescritivo e orientado a padrões de mercado.

### [A] - AUDIENCE (Audiência)
Engenheiros de Software, Arquitetos de Soluções e Engenheiro de Testes.

### [R] - RESPONSE FORMAT (Formato de Saída)
Sua resposta deve seguir obrigatoriamente a seguinte estrutura em Markdown. Deve criar um arquivo de teste Markdown para cada serviço API. Os arquivos devem ser criados na pasta `casos_testes_apis` e o nome do arquivo deve ser o nome do serviço API. Exemplo: `CT_Funcional_API_POST_Pedidos.md`, `CT_Funcional_API_POST_Estoque_Reservar.md`.

#### 1. Matriz de Casos de Teste de Integração:
   Tabela contendo:
   - `ID` (ex.: `CT-Funcional-001`) - `Título do Caso de Teste` (Título objetivo do cenário de teste) Exemplo: `CT-Funcional-001 - Validação de Estoque Insuficiente`
   - `Cenário / Objetivo do Teste` (Descrição do cenário de teste)
   - `Critério de Aceitação` (Critérios que devem ser atendidos para que o teste seja considerado bem-sucedido)
   - `Pré-condição` (Itens necessários para a execução do teste)
   - `Passo a Passo` (Passos para executar o teste. Gerar a quantidade necessário para o teste. Segue estrutura de exemplo de Passos abaixo)
        - `Passo 1` (Identificação)
            - `Ação` (Passo descrito de forma sucinta)
            - `Dados de Entrada` (Caso tiver. Exemplos: Headers, Payload e outros pontos necessários)
            - `Resultado Esperado` (Resultado esperado de forma sucinta)
        - `Passo 2` (Identificação)
            - `Ação` (Passo descrito de forma sucinta)
            - `Dados de Entrada` (Caso tiver. Exemplos: Headers, Payload e outros pontos necessários)
            - `Resultado Esperado` (Resultado esperado de forma sucinta)
        - `Passo 3` (Identificação)
            - `Ação` (Passo descrito de forma sucinta)
            - `Dados de Entrada` (Caso tiver. Exemplos: Headers, Payload e outros pontos necessários)
            - `Resultado Esperado` (Resultado esperado de forma sucinta)

Ao gerar o ID dos casos de teste funcionais (ex.: `CT-Funcional-001`) veja se nos arquivos gerados eles não ficam duplicados, observe que no `CT-Funcional-001` o ID é `CT-Funcional-001` e nos passos ele se repete. Preciso que os IDs sejam únicos para cada caso de teste em relação a todos os arquivos CT-Funcional gerados na pasta `casos_testes_apis`.