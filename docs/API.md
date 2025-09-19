# 🌐 Documentação da API - WiizoChat v2

Documentação completa das APIs do WiizoChat v2 para integração externa.

## 📋 Índice

- [Visão Geral](#-visão-geral)
- [Autenticação](#-autenticação)
- [Endpoints Principais](#-endpoints-principais)
- [APIs de Integração](#-apis-de-integração)
- [Webhooks](#-webhooks)
- [Rate Limiting](#-rate-limiting)
- [Códigos de Erro](#-códigos-de-erro)
- [Exemplos de Uso](#-exemplos-de-uso)

---

## 🎯 Visão Geral

O WiizoChat v2 oferece um conjunto robusto de APIs REST para integração com sistemas externos. Todas as APIs são construídas usando Supabase Edge Functions e seguem padrões RESTful.

### **Base URL**
```
https://seu-projeto.supabase.co/functions/v1
```

### **Formato de Resposta**
```json
{
  "success": true,
  "data": { ... },
  "message": "Operação realizada com sucesso",
  "timestamp": "2024-01-15T10:30:00Z"
}
```

---

## 🔐 Autenticação

### **Métodos de Autenticação**

#### **1. API Key (Recomendado)**
```http
Authorization: Bearer sua-api-key
apikey: sua-api-key
```

#### **2. JWT Token**
```http
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

### **Obter Credenciais**

#### **API Key**
1. Acesse o dashboard do Supabase
2. Vá em Settings > API
3. Copie a `anon` key

#### **JWT Token**
```javascript
// Login via Supabase Auth
const { data, error } = await supabase.auth.signInWithPassword({
  email: 'user@example.com',
  password: 'password'
});

const token = data.session.access_token;
```

---

## 🚀 Endpoints Principais

### **1. OpenAI Proxy**

#### **POST /openai-proxy**
Proxy para OpenAI API com rate limiting e cache.

**Headers:**
```http
Content-Type: application/json
Authorization: Bearer sua-api-key
```

**Body:**
```json
{
  "action": "response",
  "payload": {
    "model": "gpt-4.1-2025-04-14",
    "messages": [
      {
        "role": "system",
        "content": "Você é um assistente de vendas."
      },
      {
        "role": "user",
        "content": "Como posso aumentar minhas vendas?"
      }
    ],
    "temperature": 0.7,
    "max_tokens": 1000,
    "tools": [
      {
        "type": "function",
        "name": "create_lead",
        "description": "Criar um novo lead"
      }
    ]
  }
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "chatcmpl-123",
    "object": "chat.completion",
    "created": 1640995200,
    "model": "gpt-4.1-2025-04-14",
    "choices": [
      {
        "index": 0,
        "message": {
          "role": "assistant",
          "content": "Para aumentar suas vendas, recomendo..."
        },
        "finish_reason": "stop"
      }
    ],
    "usage": {
      "prompt_tokens": 50,
      "completion_tokens": 100,
      "total_tokens": 150
    }
  }
}
```

### **2. WhatsApp API**

#### **POST /whatsapp-api/send-message**
Enviar mensagem via WhatsApp Business API.

**Body:**
```json
{
  "to": "+5511999999999",
  "type": "text",
  "text": {
    "body": "Olá! Esta é uma mensagem automática."
  },
  "context": {
    "conversation_id": "conv_123",
    "lead_id": "lead_456"
  }
}
```

#### **POST /whatsapp-api/send-template**
Enviar template aprovado.

**Body:**
```json
{
  "to": "+5511999999999",
  "template": {
    "name": "hello_world",
    "language": {
      "code": "pt_BR"
    },
    "components": [
      {
        "type": "body",
        "parameters": [
          {
            "type": "text",
            "text": "João"
          }
        ]
      }
    ]
  }
}
```

### **3. Webhooks Personalizados**

#### **POST /webhooks/{client_id}**
Webhook personalizado por cliente.

**Headers:**
```http
X-Webhook-Signature: sha256=hash-da-mensagem
Content-Type: application/json
```

**Body:**
```json
{
  "event": "lead.created",
  "data": {
    "lead_id": "lead_123",
    "name": "João Silva",
    "email": "joao@email.com",
    "phone": "+5511999999999",
    "source": "website",
    "created_at": "2024-01-15T10:30:00Z"
  },
  "timestamp": 1642248600
}
```

---

## 🔗 APIs de Integração

### **1. Gestão de Leads**

#### **GET /leads**
Listar leads com filtros.

**Query Parameters:**
- `page` (number): Página (padrão: 1)
- `limit` (number): Limite por página (padrão: 20)
- `pipeline` (string): Filtrar por pipeline
- `status` (string): Filtrar por status
- `search` (string): Busca por nome/email

**Response:**
```json
{
  "success": true,
  "data": {
    "leads": [
      {
        "id": "lead_123",
        "name": "João Silva",
        "email": "joao@email.com",
        "phone": "+5511999999999",
        "pipeline": "novos-leads",
        "status": "qualificado",
        "score": 85,
        "created_at": "2024-01-15T10:30:00Z",
        "updated_at": "2024-01-15T11:00:00Z"
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 20,
      "total": 150,
      "pages": 8
    }
  }
}
```

#### **POST /leads**
Criar novo lead.

**Body:**
```json
{
  "name": "João Silva",
  "email": "joao@email.com",
  "phone": "+5511999999999",
  "company": "Empresa ABC",
  "source": "website",
  "pipeline": "novos-leads",
  "custom_fields": {
    "interesse": "produto-x",
    "orcamento": 5000
  }
}
```

#### **PUT /leads/{lead_id}**
Atualizar lead existente.

**Body:**
```json
{
  "status": "qualificado",
  "score": 90,
  "notes": "Cliente muito interessado",
  "custom_fields": {
    "interesse": "produto-y",
    "orcamento": 8000
  }
}
```

#### **DELETE /leads/{lead_id}**
Excluir lead.

**Response:**
```json
{
  "success": true,
  "message": "Lead excluído com sucesso"
}
```

### **2. Conversas e Mensagens**

#### **GET /conversations**
Listar conversas.

**Query Parameters:**
- `page`, `limit`: Paginação
- `status`: Filtrar por status (active, closed, archived)
- `channel`: Filtrar por canal (whatsapp, instagram, facebook)

**Response:**
```json
{
  "success": true,
  "data": {
    "conversations": [
      {
        "id": "conv_123",
        "lead_id": "lead_456",
        "channel": "whatsapp",
        "status": "active",
        "last_message": {
          "content": "Obrigado pelo contato!",
          "timestamp": "2024-01-15T11:00:00Z"
        },
        "created_at": "2024-01-15T10:30:00Z"
      }
    ]
  }
}
```

#### **POST /conversations/{conversation_id}/messages**
Enviar mensagem em conversa.

**Body:**
```json
{
  "content": "Olá! Como posso ajudar?",
  "type": "text",
  "metadata": {
    "agent_id": "agent_123",
    "workflow_id": "wf_456"
  }
}
```

#### **GET /conversations/{conversation_id}/messages**
Listar mensagens da conversa.

**Response:**
```json
{
  "success": true,
  "data": {
    "messages": [
      {
        "id": "msg_123",
        "conversation_id": "conv_123",
        "role": "user",
        "content": "Olá, gostaria de saber sobre o produto X",
        "timestamp": "2024-01-15T10:30:00Z",
        "metadata": {}
      },
      {
        "id": "msg_124",
        "conversation_id": "conv_123",
        "role": "assistant",
        "content": "Olá! Fico feliz em ajudar com informações sobre o produto X...",
        "timestamp": "2024-01-15T10:31:00Z",
        "metadata": {
          "agent_id": "agent_123",
          "model": "gpt-4.1-2025-04-14"
        }
      }
    ]
  }
}
```

### **3. Workflows e Automação**

#### **GET /workflows**
Listar workflows.

**Response:**
```json
{
  "success": true,
  "data": {
    "workflows": [
      {
        "id": "wf_123",
        "name": "Qualificação de Leads",
        "description": "Workflow para qualificar leads automaticamente",
        "status": "active",
        "nodes": [
          {
            "id": "node_1",
            "type": "trigger",
            "config": {
              "event": "lead.created"
            }
          },
          {
            "id": "node_2",
            "type": "ai_action",
            "config": {
              "agent_id": "agent_123",
              "prompt": "Qualifique este lead baseado nas informações fornecidas"
            }
          }
        ],
        "created_at": "2024-01-15T10:30:00Z"
      }
    ]
  }
}
```

#### **POST /workflows/{workflow_id}/execute**
Executar workflow.

**Body:**
```json
{
  "trigger_data": {
    "lead_id": "lead_123",
    "event": "lead.created"
  },
  "context": {
    "user_id": "user_123",
    "company_id": "company_456"
  }
}
```

### **4. Campanhas de IA**

#### **GET /campaigns**
Listar campanhas.

**Response:**
```json
{
  "success": true,
  "data": {
    "campaigns": [
      {
        "id": "camp_123",
        "name": "Campanha Black Friday",
        "status": "active",
        "type": "whatsapp",
        "target_audience": {
          "pipeline": "novos-leads",
          "filters": {
            "score": { "min": 70 }
          }
        },
        "message_template": "Olá {{name}}, temos uma oferta especial para você!",
        "schedule": {
          "start_date": "2024-01-15T10:00:00Z",
          "end_date": "2024-01-20T18:00:00Z"
        },
        "metrics": {
          "sent": 150,
          "delivered": 145,
          "opened": 120,
          "clicked": 45
        }
      }
    ]
  }
}
```

#### **POST /campaigns**
Criar nova campanha.

**Body:**
```json
{
  "name": "Campanha Black Friday",
  "type": "whatsapp",
  "target_audience": {
    "pipeline": "novos-leads",
    "filters": {
      "score": { "min": 70 },
      "created_after": "2024-01-01T00:00:00Z"
    }
  },
  "message_template": "Olá {{name}}, temos uma oferta especial!",
  "schedule": {
    "start_date": "2024-01-15T10:00:00Z",
    "end_date": "2024-01-20T18:00:00Z"
  }
}
```

### **5. Analytics e Métricas**

#### **GET /analytics/overview**
Visão geral das métricas.

**Query Parameters:**
- `period`: Período (today, week, month, year)
- `company_id`: ID da empresa

**Response:**
```json
{
  "success": true,
  "data": {
    "period": "month",
    "metrics": {
      "leads": {
        "total": 1250,
        "new": 150,
        "qualified": 85,
        "converted": 25
      },
      "conversations": {
        "total": 890,
        "active": 45,
        "closed": 845
      },
      "messages": {
        "total": 4560,
        "sent": 2300,
        "received": 2260
      },
      "campaigns": {
        "total": 12,
        "active": 3,
        "completed": 9
      }
    },
    "trends": {
      "leads_growth": 15.5,
      "conversion_rate": 12.3,
      "response_time_avg": 2.5
    }
  }
}
```

#### **GET /analytics/leads**
Métricas detalhadas de leads.

**Query Parameters:**
- `pipeline`: Filtrar por pipeline
- `period`: Período de análise
- `group_by`: Agrupar por (day, week, month)

**Response:**
```json
{
  "success": true,
  "data": {
    "pipeline": "novos-leads",
    "period": "month",
    "metrics": {
      "total_leads": 150,
      "qualified_leads": 85,
      "converted_leads": 25,
      "conversion_rate": 16.7,
      "avg_score": 72.5
    },
    "timeline": [
      {
        "date": "2024-01-01",
        "leads": 5,
        "qualified": 3,
        "converted": 1
      }
    ]
  }
}
```

---

## 🔔 Webhooks

### **Configuração de Webhooks**

#### **Registrar Webhook**
```http
POST /webhooks/register
```

**Body:**
```json
{
  "url": "https://seu-sistema.com/webhook",
  "events": [
    "lead.created",
    "lead.updated",
    "conversation.started",
    "message.received",
    "workflow.completed"
  ],
  "secret": "seu-secret-key"
}
```

### **Eventos Disponíveis**

#### **lead.created**
```json
{
  "event": "lead.created",
  "data": {
    "lead_id": "lead_123",
    "name": "João Silva",
    "email": "joao@email.com",
    "phone": "+5511999999999",
    "pipeline": "novos-leads",
    "created_at": "2024-01-15T10:30:00Z"
  },
  "timestamp": 1642248600
}
```

#### **conversation.started**
```json
{
  "event": "conversation.started",
  "data": {
    "conversation_id": "conv_123",
    "lead_id": "lead_456",
    "channel": "whatsapp",
    "started_at": "2024-01-15T10:30:00Z"
  },
  "timestamp": 1642248600
}
```

#### **workflow.completed**
```json
{
  "event": "workflow.completed",
  "data": {
    "workflow_id": "wf_123",
    "execution_id": "exec_456",
    "status": "success",
    "results": {
      "leads_processed": 5,
      "messages_sent": 3,
      "errors": 0
    },
    "completed_at": "2024-01-15T10:35:00Z"
  },
  "timestamp": 1642248900
}
```

### **Verificação de Webhook**

O sistema envia um header de assinatura para verificar a autenticidade:

```http
X-Webhook-Signature: sha256=hash-da-mensagem
```

**Exemplo de verificação (Node.js):**
```javascript
const crypto = require('crypto');

function verifyWebhook(payload, signature, secret) {
  const hash = crypto
    .createHmac('sha256', secret)
    .update(payload)
    .digest('hex');
  
  return `sha256=${hash}` === signature;
}
```

---

## ⚡ Rate Limiting

### **Limites por Endpoint**

| Endpoint | Limite | Janela |
|----------|--------|--------|
| `/openai-proxy` | 1000 req/hora | 1 hora |
| `/whatsapp-api/*` | 1000 req/dia | 24 horas |
| `/leads` | 500 req/hora | 1 hora |
| `/conversations` | 300 req/hora | 1 hora |
| `/workflows` | 200 req/hora | 1 hora |
| `/webhooks` | 100 req/minuto | 1 minuto |

### **Headers de Rate Limit**

```http
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 999
X-RateLimit-Reset: 1642249200
```

### **Resposta de Rate Limit**

```json
{
  "success": false,
  "error": {
    "code": "RATE_LIMIT_EXCEEDED",
    "message": "Rate limit exceeded. Try again in 3600 seconds.",
    "retry_after": 3600
  }
}
```

---

## ❌ Códigos de Erro

### **Códigos HTTP**

| Código | Significado | Descrição |
|--------|-------------|-----------|
| 200 | OK | Requisição bem-sucedida |
| 201 | Created | Recurso criado com sucesso |
| 400 | Bad Request | Dados inválidos na requisição |
| 401 | Unauthorized | Token de autenticação inválido |
| 403 | Forbidden | Acesso negado |
| 404 | Not Found | Recurso não encontrado |
| 429 | Too Many Requests | Rate limit excedido |
| 500 | Internal Server Error | Erro interno do servidor |

### **Códigos de Erro Personalizados**

| Código | Descrição |
|--------|-----------|
| `INVALID_API_KEY` | Chave de API inválida |
| `MISSING_REQUIRED_FIELD` | Campo obrigatório ausente |
| `LEAD_NOT_FOUND` | Lead não encontrado |
| `CONVERSATION_NOT_FOUND` | Conversa não encontrada |
| `WORKFLOW_EXECUTION_FAILED` | Falha na execução do workflow |
| `WHATSAPP_API_ERROR` | Erro na API do WhatsApp |
| `OPENAI_API_ERROR` | Erro na API do OpenAI |

### **Exemplo de Resposta de Erro**

```json
{
  "success": false,
  "error": {
    "code": "LEAD_NOT_FOUND",
    "message": "Lead com ID 'lead_123' não foi encontrado",
    "details": {
      "lead_id": "lead_123",
      "timestamp": "2024-01-15T10:30:00Z"
    }
  }
}
```

---

## 💡 Exemplos de Uso

### **1. Integração Completa de Lead**

```javascript
// 1. Criar lead
const lead = await fetch(`${API_BASE_URL}/leads`, {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${API_KEY}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    name: 'João Silva',
    email: 'joao@email.com',
    phone: '+5511999999999',
    source: 'website',
    pipeline: 'novos-leads'
  })
});

const leadData = await lead.json();

// 2. Iniciar conversa
const conversation = await fetch(`${API_BASE_URL}/conversations`, {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${API_KEY}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    lead_id: leadData.data.id,
    channel: 'whatsapp'
  })
});

// 3. Enviar mensagem de boas-vindas
const message = await fetch(`${API_BASE_URL}/conversations/${conversationData.data.id}/messages`, {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${API_KEY}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    content: 'Olá João! Bem-vindo! Como posso ajudar?',
    type: 'text'
  })
});
```

### **2. Executar Workflow de Qualificação**

```javascript
// Executar workflow de qualificação
const workflow = await fetch(`${API_BASE_URL}/workflows/wf-qualification/execute`, {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${API_KEY}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    trigger_data: {
      lead_id: 'lead_123',
      event: 'lead.created'
    }
  })
});

const result = await workflow.json();
console.log('Workflow executado:', result.data);
```

### **3. Configurar Webhook**

```javascript
// Registrar webhook
const webhook = await fetch(`${API_BASE_URL}/webhooks/register`, {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${API_KEY}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    url: 'https://meu-sistema.com/webhook',
    events: ['lead.created', 'conversation.started'],
    secret: 'meu-secret-key'
  })
});
```

### **4. Buscar Métricas**

```javascript
// Buscar métricas do mês
const metrics = await fetch(`${API_BASE_URL}/analytics/overview?period=month`, {
  headers: {
    'Authorization': `Bearer ${API_KEY}`
  }
});

const data = await metrics.json();
console.log('Métricas:', data.data.metrics);
```

---

## 📚 Recursos Adicionais

- [Guia de Setup](SETUP.md)
- [Guia de Deploy](DEPLOYMENT.md)
- [Arquitetura do Sistema](ARCHITECTURE.md)
- [SDKs e Bibliotecas](https://github.com/wiizochat/sdks)

---

## 🆘 Suporte

- **Email:** api@wiizochat.com
- **Discord:** [Servidor WiizoChat](https://discord.gg/wiizochat)
- **Documentação:** [docs.wiizochat.com](https://docs.wiizochat.com)
