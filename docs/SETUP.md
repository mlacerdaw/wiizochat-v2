# 🚀 Guia de Setup Completo - WiizoChat v2

Este guia fornece instruções detalhadas para configurar o ambiente de desenvolvimento do WiizoChat v2.

## 📋 Índice

- [Pré-requisitos](#-pré-requisitos)
- [Instalação](#-instalação)
- [Configuração do Ambiente](#-configuração-do-ambiente)
- [Configuração do Supabase](#-configuração-do-supabase)
- [Configuração das APIs](#-configuração-das-apis)
- [Executando o Projeto](#-executando-o-projeto)
- [Troubleshooting](#-troubleshooting)

---

## 🔧 Pré-requisitos

### **Sistema Operacional**
- **Windows 10/11** ou **macOS 10.15+** ou **Linux Ubuntu 18.04+**
- **8GB RAM** mínimo (16GB recomendado)
- **10GB** de espaço livre em disco

### **Software Necessário**

#### **1. Node.js e npm**
```bash
# Verificar versão (necessário 18+)
node --version
npm --version

# Instalar Node.js 18+ se necessário
# Windows: Baixar de https://nodejs.org
# macOS: brew install node@18
# Linux: curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
```

#### **2. Git**
```bash
# Verificar instalação
git --version

# Instalar se necessário
# Windows: https://git-scm.com/download/win
# macOS: brew install git
# Linux: sudo apt install git
```

#### **3. Supabase CLI**
```bash
# Instalar Supabase CLI
npm install -g supabase

# Verificar instalação
supabase --version
```

#### **4. Vercel CLI (opcional)**
```bash
# Instalar Vercel CLI
npm install -g vercel

# Verificar instalação
vercel --version
```

---

## 📥 Instalação

### **1. Clone do Repositório**
```bash
# Clone o repositório
git clone https://github.com/seu-usuario/wiizochat-v2.git
cd wiizochat-v2

# Verificar estrutura do projeto
ls -la
```

### **2. Instalação de Dependências**
```bash
# Instalar dependências do frontend
npm install

# Verificar instalação
npm list --depth=0
```

### **3. Estrutura do Projeto**
```
wiizochat-v2/
├── src/                    # Código fonte
│   ├── components/         # Componentes React
│   ├── pages/             # Páginas da aplicação
│   ├── hooks/             # Custom hooks
│   ├── context/           # Context API
│   ├── services/          # Serviços e APIs
│   ├── utils/             # Utilitários
│   └── types/             # TypeScript types
├── public/                # Arquivos estáticos
├── docs/                  # Documentação
├── supabase/              # Configuração Supabase
│   ├── functions/         # Edge Functions
│   ├── migrations/        # Migrações do banco
│   └── policies/          # Políticas RLS
├── .env.example          # Exemplo de variáveis
├── package.json          # Dependências
└── README.md             # Documentação principal
```

---

## ⚙️ Configuração do Ambiente

### **1. Variáveis de Ambiente**

#### **Criar arquivo .env.local**
```bash
# Copiar arquivo de exemplo
cp .env.example .env.local

# Editar arquivo
nano .env.local  # ou code .env.local
```

#### **Configuração Completa do .env.local**
```env
# ===========================================
# SUPABASE CONFIGURATION
# ===========================================
VITE_SUPABASE_URL=https://seu-projeto.supabase.co
VITE_SUPABASE_ANON_KEY=sua-chave-anonima-aqui

# ===========================================
# OPENAI CONFIGURATION
# ===========================================
VITE_OPENAI_API_KEY=sk-sua-chave-openai-aqui
VITE_OPENAI_MODEL=gpt-4.1-2025-04-14

# ===========================================
# WHATSAPP BUSINESS API
# ===========================================
VITE_WHATSAPP_ACCESS_TOKEN=seu-token-whatsapp
VITE_WHATSAPP_PHONE_ID=seu-phone-id
VITE_WHATSAPP_WEBHOOK_VERIFY_TOKEN=seu-verify-token

# ===========================================
# INSTAGRAM BUSINESS API
# ===========================================
VITE_INSTAGRAM_ACCESS_TOKEN=seu-token-instagram
VITE_INSTAGRAM_APP_ID=seu-app-id

# ===========================================
# GOOGLE CALENDAR API
# ===========================================
VITE_GOOGLE_CLIENT_ID=seu-client-id
VITE_GOOGLE_CLIENT_SECRET=seu-client-secret

# ===========================================
# FACEBOOK MARKETING API
# ===========================================
VITE_FACEBOOK_ACCESS_TOKEN=seu-token-facebook
VITE_FACEBOOK_APP_ID=seu-app-id

# ===========================================
# CONFIGURAÇÕES GERAIS
# ===========================================
VITE_APP_NAME=WiizoChat v2
VITE_APP_VERSION=2.0.0
VITE_APP_ENV=development

# ===========================================
# CONFIGURAÇÕES DE SEGURANÇA
# ===========================================
VITE_SESSION_TIMEOUT=480
VITE_MAX_IDLE_TIME=60
VITE_ENABLE_ACTIVITY_MONITORING=true
VITE_ENABLE_DATA_SANITIZATION=true

# ===========================================
# CONFIGURAÇÕES DE IA
# ===========================================
VITE_AI_TEMPERATURE=0.7
VITE_AI_MAX_TOKENS=2000
VITE_AI_STREAMING=true

# ===========================================
# CONFIGURAÇÕES DE NOTIFICAÇÕES
# ===========================================
VITE_ENABLE_SOUND_NOTIFICATIONS=true
VITE_NOTIFICATION_VOLUME=0.8
VITE_NOTIFICATION_INTERVAL=10000
```

### **2. Validação das Variáveis**
```bash
# Verificar se todas as variáveis estão definidas
npm run validate-env

# Testar conexão com Supabase
npm run test-supabase

# Testar conexão com OpenAI
npm run test-openai
```

---

## 🗄️ Configuração do Supabase

### **1. Criar Projeto Supabase**

#### **Via Dashboard**
1. Acesse [supabase.com](https://supabase.com)
2. Clique em "New Project"
3. Preencha os dados:
   - **Name:** wiizochat-v2
   - **Database Password:** (senha forte)
   - **Region:** (escolha mais próxima)
4. Aguarde a criação (2-3 minutos)

#### **Via CLI**
```bash
# Login no Supabase
supabase login

# Criar novo projeto
supabase projects create wiizochat-v2 --region us-east-1
```

### **2. Configurar Projeto Local**
```bash
# Link com projeto existente
supabase link --project-ref seu-project-ref

# Inicializar configuração local
supabase init

# Verificar status
supabase status
```

### **3. Executar Migrações**
```bash
# Aplicar migrações
supabase db push

# Verificar tabelas criadas
supabase db diff

# Reset do banco (se necessário)
supabase db reset
```

### **4. Configurar Edge Functions**
```bash
# Deploy das Edge Functions
supabase functions deploy openai-proxy
supabase functions deploy whatsapp-api
supabase functions deploy webhooks

# Verificar functions
supabase functions list
```

### **5. Configurar Storage**
```bash
# Criar buckets necessários
supabase storage create-bucket avatars
supabase storage create-bucket documents
supabase storage create-bucket campaigns

# Configurar políticas de acesso
supabase storage policy create --bucket avatars --policy "Public Access" --operation SELECT --target public
```

---

## 🔌 Configuração das APIs

### **1. OpenAI API**

#### **Obter Chave de API**
1. Acesse [platform.openai.com](https://platform.openai.com)
2. Vá em "API Keys"
3. Clique em "Create new secret key"
4. Copie a chave gerada

#### **Configurar Rate Limits**
```bash
# Verificar uso atual
curl -H "Authorization: Bearer $OPENAI_API_KEY" \
  https://api.openai.com/v1/usage

# Configurar limites (via dashboard)
# - Requests per minute: 1000
# - Tokens per minute: 100000
```

### **2. WhatsApp Business API**

#### **Configuração via Facebook Developers**
1. Acesse [developers.facebook.com](https://developers.facebook.com)
2. Crie um app do tipo "Business"
3. Adicione o produto "WhatsApp Business API"
4. Configure webhook:
   - **URL:** `https://seu-dominio.com/api/webhooks/whatsapp`
   - **Verify Token:** (mesmo do .env.local)
   - **Events:** messages, message_deliveries, message_reads

#### **Obter Credenciais**
```bash
# Phone Number ID
curl -X GET \
  "https://graph.facebook.com/v17.0/me/phone_numbers" \
  -H "Authorization: Bearer $WHATSAPP_ACCESS_TOKEN"

# Webhook Verification
curl -X GET \
  "https://graph.facebook.com/v17.0/me/subscribed_apps" \
  -H "Authorization: Bearer $WHATSAPP_ACCESS_TOKEN"
```

### **3. Instagram Business API**

#### **Configuração**
1. Acesse [developers.facebook.com](https://developers.facebook.com)
2. Adicione o produto "Instagram Basic Display"
3. Configure OAuth:
   - **Redirect URI:** `https://seu-dominio.com/auth/instagram/callback`
   - **Scopes:** instagram_basic, pages_show_list

#### **Obter Access Token**
```bash
# Gerar URL de autorização
echo "https://api.instagram.com/oauth/authorize?client_id=$INSTAGRAM_APP_ID&redirect_uri=https://seu-dominio.com/auth/instagram/callback&scope=user_profile,user_media&response_type=code"

# Trocar code por access token
curl -X POST \
  "https://api.instagram.com/oauth/access_token" \
  -d "client_id=$INSTAGRAM_APP_ID" \
  -d "client_secret=$INSTAGRAM_CLIENT_SECRET" \
  -d "grant_type=authorization_code" \
  -d "redirect_uri=https://seu-dominio.com/auth/instagram/callback" \
  -d "code=CODE_FROM_URL"
```

### **4. Google Calendar API**

#### **Configuração**
1. Acesse [console.developers.google.com](https://console.developers.google.com)
2. Crie um novo projeto ou selecione existente
3. Ative a API "Google Calendar API"
4. Crie credenciais OAuth 2.0:
   - **Application type:** Web application
   - **Authorized redirect URIs:** `https://seu-dominio.com/google-calendar-callback`

#### **Download das Credenciais**
```bash
# Baixar arquivo JSON das credenciais
# Salvar como: google-credentials.json
# Adicionar ao .gitignore
echo "google-credentials.json" >> .gitignore
```

---

## 🚀 Executando o Projeto

### **1. Desenvolvimento Local**
```bash
# Iniciar servidor de desenvolvimento
npm run dev

# Acessar aplicação
# http://localhost:5173
```

### **2. Verificar Funcionamento**
```bash
# Testar conexões
npm run test:connections

# Executar testes
npm run test

# Verificar build
npm run build
```

### **3. Logs e Debug**
```bash
# Ver logs do Supabase
supabase logs

# Ver logs das Edge Functions
supabase functions logs openai-proxy

# Debug do frontend
npm run dev -- --debug
```

---

## 🔧 Troubleshooting

### **Problemas Comuns**

#### **1. Erro de Conexão Supabase**
```bash
# Verificar status
supabase status

# Reiniciar serviços
supabase stop
supabase start

# Verificar logs
supabase logs --level error
```

#### **2. Erro de Variáveis de Ambiente**
```bash
# Verificar se arquivo existe
ls -la .env.local

# Verificar conteúdo
cat .env.local

# Validar formato
npm run validate-env
```

#### **3. Erro de Dependências**
```bash
# Limpar cache
npm cache clean --force

# Remover node_modules
rm -rf node_modules package-lock.json

# Reinstalar
npm install
```

#### **4. Erro de Porta em Uso**
```bash
# Verificar porta 5173
lsof -i :5173

# Matar processo
kill -9 PID_DO_PROCESSO

# Usar porta diferente
npm run dev -- --port 3000
```

#### **5. Erro de CORS**
```bash
# Verificar configuração Supabase
# Dashboard > Settings > API > CORS

# Adicionar domínio local
http://localhost:5173
http://localhost:3000
```

### **Comandos de Diagnóstico**

```bash
# Verificar versões
node --version
npm --version
supabase --version

# Verificar status do projeto
npm run status

# Verificar conectividade
npm run test:connectivity

# Verificar configurações
npm run config:check
```

### **Logs Importantes**

#### **Frontend**
```bash
# Logs do navegador
# F12 > Console

# Logs do Vite
npm run dev -- --log-level debug
```

#### **Backend (Supabase)**
```bash
# Logs gerais
supabase logs

# Logs específicos
supabase logs --level error
supabase logs --level warn
```

#### **Edge Functions**
```bash
# Logs de uma function específica
supabase functions logs openai-proxy --follow

# Logs de todas as functions
supabase functions logs --follow
```

---

## 📚 Próximos Passos

Após completar o setup:

1. **Leia a [Documentação da API](API.md)**
2. **Configure o [Guia de Deploy](DEPLOYMENT.md)**
3. **Explore a [Arquitetura do Sistema](ARCHITECTURE.md)**
4. **Contribua seguindo o [Guia de Contribuição](../CONTRIBUTING.md)**

---

## 🆘 Suporte

Se encontrar problemas:

1. **Verifique os logs** usando os comandos acima
2. **Consulte a documentação** específica
3. **Abra uma issue** no GitHub
4. **Entre em contato** com a equipe

**Email:** dev@wiizochat.com  
**Discord:** [Servidor WiizoChat](https://discord.gg/wiizochat)
