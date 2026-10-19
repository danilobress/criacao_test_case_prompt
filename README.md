# Teste de Software Orientado a Prompts

Este repositório contém a especificação formal, os payloads de referência, os prompts para criação dos casos de teste e os casos de teste funcionais e de contrato, gerados a partir dos prompts.

---

## 📁 Estrutura de Arquivos

```text
├── openapi.yaml                       # Contrato OpenAPI API de Pedidos (POST /pedidos)
├── stock-api-openapi.yaml             # Contrato OpenAPI API interna de Estoque (POST /v1/estoque/reservar)
├── index.html                         # Swagger UI com os dois endpoints (alternância no topo)
├── prompt_teste_funcional_api.md      # Prompt para casos de teste funcionais e de integração
├── prompt_teste_contrato_api.md       # Prompt para casos de teste de contrato
├── casos_testes_apis/                 # Casos de teste gerados
│   ├── CT_Funcional_API_POST_Pedidos.md          # CTs Funcionais para POST /pedidos
│   ├── CT_Funcional_API_POST_Estoque_Reservar.md # CTs Funcionais para POST /v1/estoque/reservar
│   ├── CT_Contrato_API_POST_Pedidos.md           # CTs de Contrato para POST /pedidos
│   └── CT_Contrato_API_POST_Estoque_Reservar.md  # CTs de Contrato para POST /v1/estoque/reservar
├── payloads/                          # Payloads JSON de referência
│   ├── pedido-sucesso.json                        
│   ├── pedido-estoque-insuficiente.json           
│   ├── pedido-quantidade-invalida.json            
│   ├── resposta-201-criado.json
│   ├── resposta-400-bad-request.json
│   ├── resposta-422-estoque-insuficiente.json
│   ├── resposta-503-servico-indisponivel.json
│   ├── resposta-504-gateway-timeout.json
│   ├── estoque-reserva-sucesso.json               
│   ├── estoque-reserva-saldo-insuficiente.json    
│   ├── estoque-resposta-200-reservado.json
│   ├── estoque-resposta-400-bad-request.json
│   ├── estoque-resposta-422-saldo-insuficiente.json
│   └── estoque-resposta-500-erro-interno.json
└── README.md                          # Documentação do projeto
```

---

## 🏗️ O Cenário de Integração (E-Commerce & Estoque)

- **Serviço de Finalização de Pedidos**: recebe o payload do carrinho, valida o schema e orquestra a transação.
- **API de Gestão de Estoque/Produtos**: verifica a disponibilidade e efetua a reserva temporária do saldo físico.

### Regras de Negócio

| Regra | Resultado |
| :--- | :--- |
| `quantidade` inteira ≥ 1 e `precoUnitario` ≥ 0.01 por item | Violação → `400 Bad Request` |
| Pedido com pelo menos 1 item (`minItems: 1`) | Violação → `400 Bad Request` |
| Quantidade solicitada ≤ saldo físico | Estoque `200 OK` → Pedidos `201 Created` |
| Quantidade solicitada > saldo físico | `422` com `itensIndisponiveis` |
| Violação sintática, JSON inválido ou campo ausente | `400 Bad Request` |
| Estoque excede o tempo limite de resposta | Pedidos retorna `504 Gateway Timeout` |
| Estoque inacessível (conexão recusada) ou Circuit Breaker aberto | Pedidos retorna `503 Service Unavailable` |
| Falha interna no banco de estoque | Estoque retorna `500` |

> **Atenção:** o contrato de Pedidos não define o comportamento quando o Estoque responde `500`. É uma lacuna de especificação ainda em aberto (sem caso de teste correspondente).

---

## 🖥️ Como Visualizar a Documentação (Swagger UI)

### Opção 1: Abrir diretamente no Navegador (Recomendado)
Abra o arquivo [`index.html`](./index.html) no navegador (Chrome, Edge, Firefox).
- Swagger UI oficial integrado.
- Alterne no topo entre a **API de Pedidos** e a **API de Estoque**.
- O botão *"Try it out"* oferece exemplos de request nas **duas** APIs (pedido válido / acima do saldo; reserva válida / acima do saldo) e o header `Prefer` simula os status documentados.

### Opção 2: Swagger Editor Oficial (Online)
1. Acesse [https://editor.swagger.io](https://editor.swagger.io).
2. Use **File > Import file** e selecione [`openapi.yaml`](./openapi.yaml) ou [`stock-api-openapi.yaml`](./stock-api-openapi.yaml).

### Opção 3: Extensão do VS Code
1. Instale a extensão **Swagger Viewer**.
2. Abra um dos arquivos `.yaml` e pressione `Shift + Alt + E`.

> O conteúdo dos YAMLs está **duplicado** dentro do `index.html`. Ao alterar um contrato, atualize os dois lugares.

---

## ⚡ Como Rodar os Mock Servers Localmente

Usa o [Prism](https://stoplight.io/open-source/prism) (requer Node.js / `npx`). Cada API roda em uma **porta própria**:

| API | Contrato | Porta |
| :--- | :--- | :--- |
| Pedidos | `openapi.yaml` | `4010` |
| Estoque | `stock-api-openapi.yaml` | `4011` |

```powershell
# API de Pedidos
npx --yes @stoplight/prism-cli mock openapi.yaml -p 4010

# API de Estoque (em outro terminal)
npx --yes @stoplight/prism-cli mock stock-api-openapi.yaml -p 4011
```

> O mock devolve respostas de exemplo do contrato conforme o header `Prefer`. Ele valida o **contrato**, não a lógica de negócio nem a integração entre os serviços.

### Exemplos de Chamadas (PowerShell, usando `curl.exe`)

#### API de Pedidos (porta 4010)

```powershell
# 201 Created
curl.exe -i -X POST http://127.0.0.1:4010/pedidos `
  -H "Content-Type: application/json" `
  -d "@payloads/pedido-sucesso.json"

# 400 Bad Request
curl.exe -i -X POST http://127.0.0.1:4010/pedidos `
  -H "Content-Type: application/json" -H "Prefer: code=400" `
  -d "@payloads/pedido-quantidade-invalida.json"

# 422 Estoque Insuficiente
curl.exe -i -X POST http://127.0.0.1:4010/pedidos `
  -H "Content-Type: application/json" -H "Prefer: code=422" `
  -d "@payloads/pedido-estoque-insuficiente.json"

# 503 Serviço de Estoque Indisponível
curl.exe -i -X POST http://127.0.0.1:4010/pedidos `
  -H "Content-Type: application/json" -H "Prefer: code=503" `
  -d "@payloads/pedido-sucesso.json"

# 504 Gateway Timeout
curl.exe -i -X POST http://127.0.0.1:4010/pedidos `
  -H "Content-Type: application/json" -H "Prefer: code=504" `
  -d "@payloads/pedido-sucesso.json"
```

#### API de Estoque (porta 4011)

```powershell
# 200 Reserva efetuada
curl.exe -i -X POST http://127.0.0.1:4011/v1/estoque/reservar `
  -H "Content-Type: application/json" `
  -d "@payloads/estoque-reserva-sucesso.json"

# 400 Bad Request
curl.exe -i -X POST http://127.0.0.1:4011/v1/estoque/reservar `
  -H "Content-Type: application/json" -H "Prefer: code=400" `
  -d "@payloads/estoque-reserva-sucesso.json"

# 422 Saldo insuficiente
curl.exe -i -X POST http://127.0.0.1:4011/v1/estoque/reservar `
  -H "Content-Type: application/json" -H "Prefer: code=422" `
  -d "@payloads/estoque-reserva-saldo-insuficiente.json"

# 500 Erro interno
curl.exe -i -X POST http://127.0.0.1:4011/v1/estoque/reservar `
  -H "Content-Type: application/json" -H "Prefer: code=500" `
  -d "@payloads/estoque-reserva-sucesso.json"
```

---

## 🧪 Casos de Teste

O repositório é composto por duas suítes complementares de testes para APIs RESTful, geradas a partir de seus respectivos prompts para GenIA:
1. **Casos de Teste Funcionais e de Integração:** Gerados via [`prompt_teste_funcional_api.md`](./prompt_teste_funcional_api.md), aplicando as técnicas de **Análise de Valor Limítrofe (AVL)** e **Particionamento em Classes de Equivalência (PCE)** sobre as regras de negócio de checkout e estoque.
2. **Casos de Teste de Contrato de API:** Gerados via [`prompt_teste_contrato_api.md`](./prompt_teste_contrato_api.md), validando estritamente a **forma do envelope** (JSON Schema, tipagens, formatos, headers e status codes).

> **Unicidade Global de IDs:** Os identificadores de casos de teste são estritamente únicos em todo o diretório `casos_testes_apis/`, sem sobreposição entre arquivos da mesma categoria.

### 1. Testes Funcionais e de Integração (Regras de Negócio, AVL e PCE)
Gerados via [`prompt_teste_funcional_api.md`](./prompt_teste_funcional_api.md):

| Arquivo | Endpoint | Quantidade de CTs |
| :--- | :--- | :--- |
| [`CT_Funcional_API_POST_Pedidos.md`](./casos_testes_apis/CT_Funcional_API_POST_Pedidos.md) | `POST /pedidos` | 26 (`CT-Funcional-001` a `CT-Funcional-026`) |
| [`CT_Funcional_API_POST_Estoque_Reservar.md`](./casos_testes_apis/CT_Funcional_API_POST_Estoque_Reservar.md) | `POST /v1/estoque/reservar` | 19 (`CT-Funcional-027` a `CT-Funcional-045`) |

### 2. Testes de Contrato de API (Forma do Envelope, Schemas e Headers)
Gerados via [`prompt_teste_contrato_api.md`](./prompt_teste_contrato_api.md):

| Arquivo | Endpoint | Quantidade de CTs |
| :--- | :--- | :--- |
| [`CT_Contrato_API_POST_Pedidos.md`](./casos_testes_apis/CT_Contrato_API_POST_Pedidos.md) | `POST /pedidos` | 18 (`CT-Contrato-001` a `CT-Contrato-018`) |
| [`CT_Contrato_API_POST_Estoque_Reservar.md`](./casos_testes_apis/CT_Contrato_API_POST_Estoque_Reservar.md) | `POST /v1/estoque/reservar` | 15 (`CT-Contrato-019` a `CT-Contrato-033`) |

Pontos importantes para executar a suíte:
- **Dois ambientes:** `AMB-INT` (serviços com persistência/oráculos) para regras de negócio, atomicidade, concorrência e resiliência; `AMB-MOCK` (Prism) para validação de envelope e schemas dos contratos via header `Prefer`.
- **Independência:** cada CT reinicia a massa de dados conforme sua pré-condição; nenhum depende de outro.
- **Rastreabilidade:** os CTs referenciam os arquivos de [`payloads/`](./payloads) como entrada e como resposta de referência.
- **Fora do escopo e premissas arquiteturais (sem definição nos contratos):**
  * **Lacunas contratuais:** Conflito `409`, busca de produto inexistente, política de expiração ativa de TTL no banco e o comportamento do serviço de Pedidos diante de código `500` emitido pelo Estoque permanecem como comportamentos não especificados formalmente nos contratos.
