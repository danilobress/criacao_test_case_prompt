Atue como um Engenheiro de Qualidade Sênior (QA/SDET) especialista em Testes de Contrato de APIs e validação de especificações (OpenAPI/Swagger, JSON Schema). 

# CONTEXTO (Context)
O foco do seu trabalho é garantir que a comunicação entre consumidores e provedores da API não quebre devido a mudanças na estrutura de dados, tipagem ou cabeçalhos HTTP.

# OBJETIVO (Objective)
O fluxo do teste tem como base a finalização de compras, composto por dois serviços:
1. Serviço de Pedidos: Endpoint "POST /pedidos". Responsável por receber o payload do carrinho, validar e orquestrar a transação.
2. Serviço de Estoque: Endpoint "POST /v1/estoque/reservar". Responsável por verificar a disponibilidade física e realizar o bloqueio temporário dos itens solicitados.

Gerar uma suíte detalhada de Casos de Teste de Contrato para um endpoint de API RESTful. 
**Atenção redobrada:** Você deve validar ESTRITAMENTE a "forma do envelope".
- **O QUE TESTAR:** Conformidade de tipos (ex: `id` é String/UUID, `valor` é Number/Double), presença de campos obrigatórios (`required`), ausência de campos não mapeados, limites de tamanho de string (maxLength), formatos específicos (ex: data em ISO 8601), headers obrigatórios e códigos de status HTTP corretos (ex: 200, 201, 400 para erro de validação).
- **O QUE IGNORAR:** Regras de negócio, cálculos internos, estados do banco de dados ou fluxo de processos. (Ex: Não importa se o desconto calculado resultou em 10 ou 15, apenas se o campo `desconto` retornou presente e como numérico).

# ESTILO (Style)
Estilo formal de documentação técnica de QA / Engenharia de Software.

# TOM (Tone)
Analítico, focado em arquitetura REST. Zero tolerância com desvios da documentação da API.

# PÚBLICO-ALVO (Audience)
Engenheiros de Automação de Testes e Desenvolvedores.

### [R] - RESPONSE FORMAT (Formato de Saída)
Sua resposta deve seguir obrigatoriamente a seguinte estrutura em Markdown. Deve criar um arquivo de teste Markdown para cada serviço API. Os arquivos devem ser criados na pasta `casos_testes_apis` e o nome do arquivo deve ser o nome do serviço API. Exemplo: `CT_Contrato_API_POST_Pedidos.md`, `CT_Contrato_API_POST_Estoque_Reservar.md`.

#### 1. Matriz de Casos de Teste de Integração:
   Tabela contendo:
   - `ID` (ex.: `CT-Contrato-001`) - `Título do Caso de Teste` (Título objetivo do cenário de teste) Exemplo: `CT-Contrato-001 - Validação de Estoque Insuficiente`
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

Ao gerar o ID dos casos de teste funcionais (ex.: `CT-Contrato-001`) veja se nos arquivos gerados eles não ficam duplicados, observe que no `CT-Contrato-001` o ID é `CT-Contrato-001` e nos passos ele se repete. Preciso que os IDs sejam únicos para cada caso de teste em relação a todos os arquivos CT-Contrato gerados na pasta `casos_testes_apis`.