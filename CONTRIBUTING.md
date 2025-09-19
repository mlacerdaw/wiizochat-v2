# 🤝 Guia de Contribuição - WiizoChat v2

Obrigado por considerar contribuir com o WiizoChat v2! Este documento fornece diretrizes e informações para contribuições.

## 📋 Índice

- [Código de Conduta](#-código-de-conduta)
- [Como Contribuir](#-como-contribuir)
- [Configuração do Ambiente](#-configuração-do-ambiente)
- [Padrões de Código](#-padrões-de-código)
- [Processo de Pull Request](#-processo-de-pull-request)
- [Reportar Bugs](#-reportar-bugs)
- [Sugerir Melhorias](#-sugerir-melhorias)

---

## 📜 Código de Conduta

### **Nossos Compromissos**

Estamos comprometidos em manter um ambiente acolhedor e inclusivo para todos os contribuidores. Esperamos que todos os participantes:

- Sejam respeitosos e inclusivos
- Aceitem críticas construtivas
- Foquem no que é melhor para a comunidade
- Demonstrem empatia com outros membros

### **Comportamentos Inaceitáveis**

- Uso de linguagem ou imagens sexualizadas
- Trolling, comentários insultuosos ou ataques pessoais
- Assédio público ou privado
- Publicação de informações privadas sem permissão
- Outros comportamentos inadequados em ambiente profissional

---

## 🚀 Como Contribuir

### **Tipos de Contribuição**

#### **1. Reportar Bugs**
- Use o template de issue para bugs
- Inclua informações detalhadas sobre o problema
- Forneça passos para reproduzir o bug

#### **2. Sugerir Melhorias**
- Use o template de feature request
- Descreva claramente a funcionalidade desejada
- Explique por que seria útil

#### **3. Contribuir com Código**
- Fork o repositório
- Crie uma branch para sua feature
- Siga os padrões de código
- Submeta um pull request

#### **4. Melhorar Documentação**
- Corrija erros de digitação
- Adicione exemplos
- Melhore a clareza das explicações

---

## ⚙️ Configuração do Ambiente

### **1. Fork e Clone**

```bash
# Fork o repositório no GitHub
# Depois clone seu fork
git clone https://github.com/seu-usuario/wiizochat-v2.git
cd wiizochat-v2

# Adicionar upstream
git remote add upstream https://github.com/wiizochat/wiizochat-v2.git
```

### **2. Instalação**

```bash
# Instalar dependências
npm install

# Configurar variáveis de ambiente
cp .env.example .env.local

# Executar migrações do banco
supabase db push
```

### **3. Verificar Instalação**

```bash
# Executar testes
npm test

# Verificar linting
npm run lint

# Verificar tipos TypeScript
npm run type-check

# Executar build
npm run build
```

---

## 📝 Padrões de Código

### **1. TypeScript**

#### **Configuração**
- Use TypeScript para todo código novo
- Mantenha `strict: true` no tsconfig.json
- Defina tipos explícitos para funções públicas

#### **Exemplo de Função**
```typescript
interface CreateLeadParams {
  name: string;
  email: string;
  phone: string;
  pipeline: string;
}

interface Lead {
  id: string;
  name: string;
  email: string;
  phone: string;
  pipeline: string;
  created_at: string;
}

export async function createLead(
  params: CreateLeadParams
): Promise<Lead> {
  // Implementação
}
```

### **2. React Components**

#### **Estrutura de Componente**
```typescript
import React, { useState, useEffect } from 'react';
import { cn } from '@/lib/utils';

interface ButtonProps {
  variant?: 'primary' | 'secondary' | 'danger';
  size?: 'sm' | 'md' | 'lg';
  disabled?: boolean;
  children: React.ReactNode;
  onClick?: () => void;
  className?: string;
}

export const Button: React.FC<ButtonProps> = ({
  variant = 'primary',
  size = 'md',
  disabled = false,
  children,
  onClick,
  className,
}) => {
  return (
    <button
      className={cn(
        'rounded-lg font-medium transition-colors',
        {
          'bg-blue-600 text-white hover:bg-blue-700': variant === 'primary',
          'bg-gray-200 text-gray-900 hover:bg-gray-300': variant === 'secondary',
          'bg-red-600 text-white hover:bg-red-700': variant === 'danger',
        },
        {
          'px-3 py-1.5 text-sm': size === 'sm',
          'px-4 py-2 text-base': size === 'md',
          'px-6 py-3 text-lg': size === 'lg',
        },
        disabled && 'opacity-50 cursor-not-allowed',
        className
      )}
      disabled={disabled}
      onClick={onClick}
    >
      {children}
    </button>
  );
};
```

### **3. Naming Conventions**

#### **Arquivos e Pastas**
```
components/
├── ui/                    # Componentes base
│   ├── Button.tsx
│   ├── Input.tsx
│   └── Modal.tsx
├── forms/                 # Componentes de formulário
│   ├── LeadForm.tsx
│   └── ContactForm.tsx
└── layout/                # Componentes de layout
    ├── Header.tsx
    ├── Sidebar.tsx
    └── Footer.tsx
```

#### **Variáveis e Funções**
```typescript
// ✅ Bom
const userEmail = 'user@example.com';
const isAuthenticated = true;
const handleSubmit = () => {};

// ❌ Ruim
const user_email = 'user@example.com';
const is_authenticated = true;
const handle_submit = () => {};
```

#### **Constantes**
```typescript
// ✅ Bom
const API_BASE_URL = 'https://api.example.com';
const MAX_RETRY_ATTEMPTS = 3;

// ❌ Ruim
const apiBaseUrl = 'https://api.example.com';
const maxRetryAttempts = 3;
```

### **4. Estrutura de Pastas**

```
src/
├── components/            # Componentes React
│   ├── ui/               # Componentes base
│   ├── forms/            # Formulários
│   └── layout/           # Layout
├── pages/                # Páginas da aplicação
├── hooks/                # Custom hooks
├── context/              # Context API
├── services/             # Serviços e APIs
├── utils/                # Utilitários
├── types/                # Definições TypeScript
├── constants/            # Constantes
└── lib/                  # Configurações de bibliotecas
```

### **5. Imports**

#### **Ordem dos Imports**
```typescript
// 1. React e bibliotecas externas
import React, { useState, useEffect } from 'react';
import { useRouter } from 'next/router';
import { toast } from 'react-hot-toast';

// 2. Imports internos (componentes, hooks, etc.)
import { Button } from '@/components/ui/Button';
import { useAuth } from '@/hooks/useAuth';
import { createLead } from '@/services/leads';

// 3. Imports de tipos
import type { Lead, CreateLeadParams } from '@/types/lead';

// 4. Imports de utilitários
import { cn } from '@/lib/utils';
import { formatDate } from '@/utils/date';
```

### **6. Comentários**

#### **JSDoc para Funções**
```typescript
/**
 * Cria um novo lead no sistema
 * @param params - Parâmetros para criação do lead
 * @param params.name - Nome do lead
 * @param params.email - Email do lead
 * @param params.phone - Telefone do lead
 * @param params.pipeline - Pipeline do lead
 * @returns Promise com o lead criado
 * @throws {Error} Quando os parâmetros são inválidos
 */
export async function createLead(params: CreateLeadParams): Promise<Lead> {
  // Implementação
}
```

#### **Comentários Inline**
```typescript
// ✅ Bom - Explica o "porquê"
// Verificar se o usuário tem permissão antes de prosseguir
if (!user.hasPermission('create_lead')) {
  throw new Error('Unauthorized');
}

// ❌ Ruim - Explica o "o que" (óbvio)
// Incrementar contador
counter++;
```

---

## 🔄 Processo de Pull Request

### **1. Antes de Criar o PR**

#### **Sincronizar com Upstream**
```bash
# Buscar mudanças do repositório principal
git fetch upstream

# Fazer merge das mudanças
git checkout main
git merge upstream/main

# Atualizar sua branch
git checkout sua-branch
git merge main
```

#### **Executar Testes**
```bash
# Executar todos os testes
npm test

# Executar linting
npm run lint

# Verificar tipos
npm run type-check

# Executar build
npm run build
```

### **2. Criando o Pull Request**

#### **Template do PR**
```markdown
## 📝 Descrição
Breve descrição das mudanças realizadas.

## 🔗 Issue Relacionada
Fixes #123

## 🧪 Como Testar
1. Passo 1
2. Passo 2
3. Passo 3

## 📸 Screenshots (se aplicável)
Adicione screenshots das mudanças visuais.

## ✅ Checklist
- [ ] Código segue os padrões do projeto
- [ ] Testes foram adicionados/atualizados
- [ ] Documentação foi atualizada
- [ ] Não há breaking changes
- [ ] Build passa sem erros
```

#### **Título do PR**
```
feat: adicionar funcionalidade de export de leads
fix: corrigir bug na validação de email
docs: atualizar documentação da API
style: melhorar layout do dashboard
refactor: otimizar performance do componente
test: adicionar testes para serviço de leads
```

### **3. Durante a Revisão**

#### **Responder a Comentários**
- Seja respeitoso e construtivo
- Faça as mudanças solicitadas
- Explique suas decisões quando necessário
- Peça esclarecimentos se não entender

#### **Atualizar o PR**
```bash
# Fazer mudanças
git add .
git commit -m "fix: corrigir validação de email"

# Atualizar PR
git push origin sua-branch
```

### **4. Após Aprovação**

#### **Merge do PR**
- Use "Squash and merge" para commits pequenos
- Use "Merge commit" para mudanças complexas
- Remova a branch após merge

```bash
# Deletar branch local
git branch -d sua-branch

# Deletar branch remota
git push origin --delete sua-branch
```

---

## 🐛 Reportar Bugs

### **Template de Bug Report**

```markdown
## 🐛 Descrição do Bug
Descrição clara e concisa do bug.

## 🔄 Passos para Reproduzir
1. Vá para '...'
2. Clique em '...'
3. Role até '...'
4. Veja o erro

## 🎯 Comportamento Esperado
Descrição do que deveria acontecer.

## 📸 Screenshots
Adicione screenshots se aplicável.

## 🖥️ Ambiente
- OS: [ex: Windows 10, macOS 12.0, Ubuntu 20.04]
- Browser: [ex: Chrome 91, Firefox 89, Safari 14]
- Versão: [ex: 2.0.0]

## 📋 Informações Adicionais
Qualquer outra informação relevante.
```

### **Informações Importantes**

#### **Logs de Erro**
```bash
# Logs do console do navegador
# F12 > Console > copiar erros

# Logs do terminal
npm run dev 2>&1 | tee logs.txt
```

#### **Versão e Ambiente**
```bash
# Versão do Node.js
node --version

# Versão do npm
npm --version

# Dependências
npm list --depth=0
```

---

## 💡 Sugerir Melhorias

### **Template de Feature Request**

```markdown
## 🚀 Funcionalidade Desejada
Descrição clara da funcionalidade desejada.

## 💭 Motivação
Por que esta funcionalidade seria útil?

## 📋 Descrição Detalhada
Como a funcionalidade deveria funcionar?

## 🎯 Casos de Uso
Exemplos de como seria usada.

## 📸 Mockups/Exemplos
Adicione mockups ou exemplos visuais.

## 🔄 Alternativas Consideradas
Outras soluções que você considerou.
```

### **Diretrizes para Sugestões**

#### **Antes de Sugerir**
- Verifique se já não existe uma issue similar
- Consulte a documentação existente
- Pense na implementação e impacto

#### **Sugestões Bem Estruturadas**
- Seja específico e detalhado
- Explique o valor para os usuários
- Considere a complexidade de implementação
- Forneça exemplos práticos

---

## 🧪 Testes

### **Executar Testes**

```bash
# Todos os testes
npm test

# Testes em modo watch
npm run test:watch

# Testes com coverage
npm run test:coverage

# Testes específicos
npm test -- --testNamePattern="Button"
```

### **Escrever Testes**

#### **Exemplo de Teste de Componente**
```typescript
import { render, screen, fireEvent } from '@testing-library/react';
import { Button } from '@/components/ui/Button';

describe('Button', () => {
  it('renders with correct text', () => {
    render(<Button>Click me</Button>);
    expect(screen.getByText('Click me')).toBeInTheDocument();
  });

  it('calls onClick when clicked', () => {
    const handleClick = jest.fn();
    render(<Button onClick={handleClick}>Click me</Button>);
    
    fireEvent.click(screen.getByText('Click me'));
    expect(handleClick).toHaveBeenCalledTimes(1);
  });

  it('is disabled when disabled prop is true', () => {
    render(<Button disabled>Click me</Button>);
    expect(screen.getByRole('button')).toBeDisabled();
  });
});
```

#### **Exemplo de Teste de Hook**
```typescript
import { renderHook, act } from '@testing-library/react';
import { useCounter } from '@/hooks/useCounter';

describe('useCounter', () => {
  it('should increment counter', () => {
    const { result } = renderHook(() => useCounter(0));
    
    act(() => {
      result.current.increment();
    });
    
    expect(result.current.count).toBe(1);
  });
});
```

---

## 📚 Recursos Adicionais

### **Documentação**
- [Guia de Setup](docs/SETUP.md)
- [Documentação da API](docs/API.md)
- [Guia de Deploy](docs/DEPLOYMENT.md)
- [Arquitetura do Sistema](docs/ARCHITECTURE.md)

### **Ferramentas**
- [TypeScript Handbook](https://www.typescriptlang.org/docs/)
- [React Testing Library](https://testing-library.com/docs/react-testing-library/intro/)
- [Tailwind CSS](https://tailwindcss.com/docs)
- [Supabase Docs](https://supabase.com/docs)

### **Comunidade**
- [Discord](https://discord.gg/wiizochat)
- [GitHub Discussions](https://github.com/wiizochat/wiizochat-v2/discussions)
- [Email](mailto:dev@wiizochat.com)

---

## 🙏 Agradecimentos

Obrigado por contribuir com o WiizoChat v2! Suas contribuições ajudam a tornar a plataforma melhor para todos os usuários.

---

**Última atualização:** Janeiro 2024
