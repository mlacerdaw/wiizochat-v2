# 🚀 Guia de Deploy - WiizoChat v2

Guia completo para fazer deploy do WiizoChat v2 em produção.

## 📋 Índice

- [Visão Geral](#-visão-geral)
- [Pré-requisitos](#-pré-requisitos)
- [Deploy do Frontend (Vercel)](#-deploy-do-frontend-vercel)
- [Deploy do Backend (Supabase)](#-deploy-do-backend-supabase)
- [Configuração de Domínio](#-configuração-de-domínio)
- [Configuração de SSL](#-configuração-de-ssl)
- [Monitoramento](#-monitoramento)
- [Troubleshooting](#-troubleshooting)

---

## 🎯 Visão Geral

O WiizoChat v2 utiliza uma arquitetura moderna com:

- **Frontend:** Vercel (React + Vite)
- **Backend:** Supabase (PostgreSQL + Edge Functions)
- **CDN:** Vercel Edge Network
- **SSL:** Automático via Vercel e Supabase

### **Arquitetura de Deploy**

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Frontend      │    │   Backend       │    │   External      │
│   (Vercel)      │◄──►│   (Supabase)    │◄──►│   APIs          │
│                 │    │                 │    │                 │
│ • React App     │    │ • PostgreSQL    │    │ • OpenAI        │
│ • Static Files  │    │ • Edge Functions│    │ • WhatsApp      │
│ • CDN           │    │ • Auth          │    │ • Instagram     │
└─────────────────┘    └─────────────────┘    └─────────────────┘
```

---

## 🔧 Pré-requisitos

### **Contas Necessárias**
- [Vercel](https://vercel.com) - Deploy frontend
- [Supabase](https://supabase.com) - Backend e banco
- [GitHub](https://github.com) - Repositório de código

### **Ferramentas**
```bash
# Vercel CLI
npm install -g vercel

# Supabase CLI
npm install -g supabase

# Git (já deve estar instalado)
git --version
```

### **Configurações Iniciais**
```bash
# Login no Vercel
vercel login

# Login no Supabase
supabase login
```

---

## 🌐 Deploy do Frontend (Vercel)

### **1. Preparação do Projeto**

#### **Configurar package.json**
```json
{
  "name": "wiizochat-v2",
  "version": "2.0.0",
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "preview": "vite preview",
    "vercel-build": "npm run build"
  },
  "engines": {
    "node": ">=18.0.0"
  }
}
```

#### **Configurar vercel.json**
```json
{
  "version": 2,
  "builds": [
    {
      "src": "package.json",
      "use": "@vercel/static-build",
      "config": {
        "distDir": "dist"
      }
    }
  ],
  "routes": [
    {
      "src": "/(.*)",
      "dest": "/index.html"
    }
  ],
  "env": {
    "VITE_SUPABASE_URL": "@supabase-url",
    "VITE_SUPABASE_ANON_KEY": "@supabase-anon-key"
  }
}
```

### **2. Deploy via CLI**

#### **Deploy Inicial**
```bash
# No diretório do projeto
vercel

# Seguir as instruções:
# ? Set up and deploy "~/wiizochat-v2"? [Y/n] y
# ? Which scope do you want to deploy to? [Seu usuário]
# ? Link to existing project? [N] n
# ? What's your project's name? wiizochat-v2
# ? In which directory is your code located? ./
```

#### **Deploy de Produção**
```bash
# Deploy para produção
vercel --prod

# Ou usar alias
vercel -p
```

### **3. Deploy via GitHub**

#### **Conectar Repositório**
1. Acesse [vercel.com/dashboard](https://vercel.com/dashboard)
2. Clique em "New Project"
3. Importe do GitHub
4. Selecione o repositório `wiizochat-v2`
5. Configure as variáveis de ambiente
6. Clique em "Deploy"

#### **Configurar Auto-Deploy**
```yaml
# .github/workflows/deploy.yml
name: Deploy to Vercel

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
      - run: npm ci
      - run: npm run build
      - uses: amondnet/vercel-action@v20
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }}
          vercel-org-id: ${{ secrets.ORG_ID }}
          vercel-project-id: ${{ secrets.PROJECT_ID }}
          working-directory: ./
```

### **4. Configurar Variáveis de Ambiente**

#### **Via Dashboard Vercel**
1. Acesse o projeto no dashboard
2. Vá em Settings > Environment Variables
3. Adicione as variáveis:

```env
VITE_SUPABASE_URL=https://seu-projeto.supabase.co
VITE_SUPABASE_ANON_KEY=sua-chave-anonima
VITE_OPENAI_API_KEY=sk-sua-chave-openai
VITE_WHATSAPP_ACCESS_TOKEN=seu-token-whatsapp
VITE_WHATSAPP_PHONE_ID=seu-phone-id
VITE_INSTAGRAM_ACCESS_TOKEN=seu-token-instagram
VITE_GOOGLE_CLIENT_ID=seu-client-id
VITE_FACEBOOK_ACCESS_TOKEN=seu-token-facebook
```

#### **Via CLI**
```bash
# Adicionar variável
vercel env add VITE_SUPABASE_URL

# Listar variáveis
vercel env ls

# Remover variável
vercel env rm VITE_SUPABASE_URL
```

### **5. Configurar Domínio Customizado**

#### **Adicionar Domínio**
```bash
# Adicionar domínio
vercel domains add wiizochat.com

# Verificar status
vercel domains ls
```

#### **Configurar DNS**
```
# Registro A
@ 76.76.19.61

# Registro CNAME
www cname.vercel-dns.com
```

---

## 🗄️ Deploy do Backend (Supabase)

### **1. Configurar Projeto**

#### **Criar Projeto**
```bash
# Via CLI
supabase projects create wiizochat-v2 --region us-east-1

# Ou via dashboard
# https://supabase.com/dashboard
```

#### **Link com Projeto Local**
```bash
# Link com projeto existente
supabase link --project-ref seu-project-ref

# Verificar status
supabase status
```

### **2. Deploy das Migrações**

#### **Executar Migrações**
```bash
# Aplicar todas as migrações
supabase db push

# Verificar status
supabase db diff

# Reset do banco (se necessário)
supabase db reset
```

#### **Estrutura de Migrações**
```
supabase/migrations/
├── 20240115000001_create_users_table.sql
├── 20240115000002_create_companies_table.sql
├── 20240115000003_create_leads_table.sql
├── 20240115000004_create_conversations_table.sql
└── 20240115000005_create_workflows_table.sql
```

### **3. Deploy das Edge Functions**

#### **Estrutura das Functions**
```
supabase/functions/
├── openai-proxy/
│   ├── index.ts
│   └── deno.json
├── whatsapp-api/
│   ├── index.ts
│   └── deno.json
└── webhooks/
    ├── index.ts
    └── deno.json
```

#### **Deploy Individual**
```bash
# Deploy de uma function específica
supabase functions deploy openai-proxy

# Deploy de todas as functions
supabase functions deploy

# Verificar functions
supabase functions list
```

#### **Exemplo de Edge Function**
```typescript
// supabase/functions/openai-proxy/index.ts
import { serve } from "https://deno.land/std@0.168.0/http/server.ts"
import { createClient } from 'https://esm.sh/@supabase/supabase-js@2'

const corsHeaders = {
  'Access-Control-Allow-Origin': '*',
  'Access-Control-Allow-Headers': 'authorization, x-client-info, apikey, content-type',
}

serve(async (req) => {
  if (req.method === 'OPTIONS') {
    return new Response('ok', { headers: corsHeaders })
  }

  try {
    const { messages, model, temperature } = await req.json()
    
    // Implementação da proxy para OpenAI
    const response = await fetch('https://api.openai.com/v1/chat/completions', {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${Deno.env.get('OPENAI_API_KEY')}`,
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        model: model || 'gpt-4.1-2025-04-14',
        messages,
        temperature: temperature || 0.7,
      }),
    })

    const data = await response.json()

    return new Response(JSON.stringify(data), {
      headers: { ...corsHeaders, 'Content-Type': 'application/json' },
      status: 200,
    })
  } catch (error) {
    return new Response(JSON.stringify({ error: error.message }), {
      headers: { ...corsHeaders, 'Content-Type': 'application/json' },
      status: 500,
    })
  }
})
```

### **4. Configurar Políticas RLS**

#### **Exemplo de Política**
```sql
-- Habilitar RLS
ALTER TABLE leads ENABLE ROW LEVEL SECURITY;

-- Política para usuários autenticados
CREATE POLICY "Users can view their own leads" ON leads
  FOR SELECT USING (auth.uid() = user_id);

-- Política para inserção
CREATE POLICY "Users can insert their own leads" ON leads
  FOR INSERT WITH CHECK (auth.uid() = user_id);

-- Política para atualização
CREATE POLICY "Users can update their own leads" ON leads
  FOR UPDATE USING (auth.uid() = user_id);
```

### **5. Configurar Storage**

#### **Criar Buckets**
```bash
# Criar buckets necessários
supabase storage create-bucket avatars
supabase storage create-bucket documents
supabase storage create-bucket campaigns
```

#### **Configurar Políticas de Storage**
```sql
-- Política para avatars
CREATE POLICY "Public Access" ON storage.objects
  FOR SELECT USING (bucket_id = 'avatars');

-- Política para documentos
CREATE POLICY "Authenticated Access" ON storage.objects
  FOR ALL USING (auth.role() = 'authenticated' AND bucket_id = 'documents');
```

---

## 🌍 Configuração de Domínio

### **1. Configurar DNS**

#### **Registros DNS Necessários**
```
# Domínio principal
@ A 76.76.19.61

# Subdomínio para API
api CNAME cname.vercel-dns.com

# Subdomínio para webhooks
webhooks CNAME cname.vercel-dns.com

# Subdomínio para admin
admin CNAME cname.vercel-dns.com
```

### **2. Configurar Subdomínios**

#### **Via Vercel Dashboard**
1. Acesse o projeto
2. Vá em Settings > Domains
3. Adicione subdomínios:
   - `api.wiizochat.com`
   - `webhooks.wiizochat.com`
   - `admin.wiizochat.com`

#### **Via CLI**
```bash
# Adicionar subdomínio
vercel domains add api.wiizochat.com
vercel domains add webhooks.wiizochat.com
vercel domains add admin.wiizochat.com
```

### **3. Configurar Supabase**

#### **Adicionar Domínios Permitidos**
1. Acesse Supabase Dashboard
2. Vá em Settings > API
3. Adicione em "Site URL":
   - `https://wiizochat.com`
   - `https://api.wiizochat.com`
   - `https://admin.wiizochat.com`

#### **Configurar CORS**
```sql
-- Adicionar domínios permitidos
UPDATE auth.config 
SET site_url = 'https://wiizochat.com',
    additional_redirect_urls = '["https://api.wiizochat.com", "https://admin.wiizochat.com"]';
```

---

## 🔒 Configuração de SSL

### **1. SSL Automático**

#### **Vercel**
- SSL é configurado automaticamente
- Certificados Let's Encrypt
- Renovação automática

#### **Supabase**
- SSL habilitado por padrão
- Certificados gerenciados automaticamente

### **2. Verificar SSL**

#### **Testar Certificados**
```bash
# Verificar certificado principal
openssl s_client -connect wiizochat.com:443 -servername wiizochat.com

# Verificar subdomínios
openssl s_client -connect api.wiizochat.com:443 -servername api.wiizochat.com
```

#### **Ferramentas Online**
- [SSL Labs](https://www.ssllabs.com/ssltest/)
- [SSL Checker](https://www.sslchecker.com/)

---

## 📊 Monitoramento

### **1. Vercel Analytics**

#### **Habilitar Analytics**
1. Acesse o projeto no Vercel
2. Vá em Analytics
3. Habilite "Web Analytics"

#### **Métricas Disponíveis**
- Page Views
- Unique Visitors
- Bounce Rate
- Core Web Vitals

### **2. Supabase Monitoring**

#### **Dashboard de Métricas**
1. Acesse Supabase Dashboard
2. Vá em Reports
3. Visualize métricas:
   - Database Performance
   - API Usage
   - Storage Usage
   - Auth Metrics

#### **Logs e Debugging**
```bash
# Ver logs gerais
supabase logs

# Ver logs de uma function específica
supabase functions logs openai-proxy --follow

# Ver logs de erro
supabase logs --level error
```

### **3. Monitoramento Customizado**

#### **Health Check Endpoint**
```typescript
// pages/api/health.ts
export default function handler(req, res) {
  const health = {
    status: 'ok',
    timestamp: new Date().toISOString(),
    services: {
      database: 'connected',
      openai: 'connected',
      whatsapp: 'connected'
    }
  }
  
  res.status(200).json(health)
}
```

#### **Uptime Monitoring**
- [UptimeRobot](https://uptimerobot.com/)
- [Pingdom](https://www.pingdom.com/)
- [StatusCake](https://www.statuscake.com/)

---

## 🔧 Troubleshooting

### **Problemas Comuns**

#### **1. Erro de Build no Vercel**
```bash
# Verificar logs de build
vercel logs

# Build local para testar
npm run build

# Verificar variáveis de ambiente
vercel env ls
```

#### **2. Erro de Conexão Supabase**
```bash
# Verificar status
supabase status

# Verificar logs
supabase logs --level error

# Testar conexão
supabase db ping
```

#### **3. Erro de CORS**
```typescript
// Configurar CORS nas Edge Functions
const corsHeaders = {
  'Access-Control-Allow-Origin': '*',
  'Access-Control-Allow-Headers': 'authorization, x-client-info, apikey, content-type',
  'Access-Control-Allow-Methods': 'GET, POST, PUT, DELETE, OPTIONS',
}
```

#### **4. Erro de Rate Limit**
```bash
# Verificar limites
vercel limits

# Verificar uso de API
supabase projects list
```

### **Comandos de Diagnóstico**

```bash
# Status geral do projeto
vercel status

# Verificar domínios
vercel domains ls

# Verificar deployments
vercel ls

# Verificar logs
vercel logs --follow

# Status do Supabase
supabase status

# Verificar functions
supabase functions list

# Verificar migrações
supabase db diff
```

### **Logs Importantes**

#### **Vercel**
- Build logs: Dashboard > Deployments > [Deploy] > Build Logs
- Function logs: Dashboard > Functions > [Function] > Logs
- Edge logs: Dashboard > Edge Functions > Logs

#### **Supabase**
- Database logs: Dashboard > Logs > Database
- Auth logs: Dashboard > Logs > Auth
- Edge Function logs: Dashboard > Edge Functions > [Function] > Logs

---

## 📚 Próximos Passos

Após o deploy:

1. **Configure monitoramento** contínuo
2. **Implemente backup** automático
3. **Configure alertas** para problemas
4. **Documente** o processo de deploy
5. **Treine** a equipe no processo

---

## 🆘 Suporte

- **Email:** deploy@wiizochat.com
- **Discord:** [Servidor WiizoChat](https://discord.gg/wiizochat)
- **Documentação:** [docs.wiizochat.com](https://docs.wiizochat.com)

---

**Última atualização:** Janeiro 2024
