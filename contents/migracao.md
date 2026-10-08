<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=3178C6&height=120&section=header&animation=twinkling"/>

# Migrando de JavaScript para TypeScript

## Estratégia de Migração

### Abordagem Gradual (Recomendada)

A migração gradual permite que você converta seu projeto JavaScript para TypeScript de forma incremental, mantendo a funcionalidade existente.

#### Passo 1: Configuração Inicial

```bash
# Instalar TypeScript
npm install --save-dev typescript @types/node

# Criar tsconfig.json
npx tsc --init
```

#### Passo 2: Configuração Permissiva

```json
// tsconfig.json - Configuração inicial permissiva
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "lib": ["ES2020", "DOM"],
    "allowJs": true,           // Permite arquivos .js
    "checkJs": false,         // Não verifica arquivos .js inicialmente
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": false,          // Desabilita verificações rigorosas inicialmente
    "noImplicitAny": false,   // Permite any implícito
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

#### Passo 3: Renomeação Gradual

```bash
# Renomear arquivos .js para .ts
mv src/utils.js src/utils.ts
mv src/api.js src/api.ts
mv src/components/Button.js src/components/Button.ts
```

## Padrões de Migração

### 1. Convertendo Funções

#### Antes (JavaScript)

```javascript
// utils.js
function formatCurrency(value, currency) {
    return new Intl.NumberFormat('pt-BR', {
        style: 'currency',
        currency: currency || 'BRL'
    }).format(value);
}

function calculateAge(birthDate) {
    const today = new Date();
    const birth = new Date(birthDate);
    return today.getFullYear() - birth.getFullYear();
}

function debounce(func, wait) {
    let timeout;
    return function executedFunction(...args) {
        const later = () => {
            clearTimeout(timeout);
            func(...args);
        };
        clearTimeout(timeout);
        timeout = setTimeout(later, wait);
    };
}

module.exports = { formatCurrency, calculateAge, debounce };
```

#### Depois (TypeScript)

```typescript
// utils.ts
export function formatCurrency(value: number, currency: string = 'BRL'): string {
    return new Intl.NumberFormat('pt-BR', {
        style: 'currency',
        currency
    }).format(value);
}

export function calculateAge(birthDate: string | Date): number {
    const today = new Date();
    const birth = new Date(birthDate);
    return today.getFullYear() - birth.getFullYear();
}

export function debounce<T extends (...args: any[]) => any>(
    func: T,
    wait: number
): (...args: Parameters<T>) => void {
    let timeout: NodeJS.Timeout | undefined;
    
    return function executedFunction(...args: Parameters<T>): void {
        const later = (): void => {
            clearTimeout(timeout);
            func(...args);
        };
        
        clearTimeout(timeout);
        timeout = setTimeout(later, wait);
    };
}
```

### 2. Convertendo Classes

#### Antes (JavaScript)

```javascript
// User.js
class User {
    constructor(name, email, age) {
        this.name = name;
        this.email = email;
        this.age = age;
        this.isActive = true;
        this.createdAt = new Date();
    }
    
    getDisplayName() {
        return `${this.name} (${this.email})`;
    }
    
    isAdult() {
        return this.age >= 18;
    }
    
    deactivate() {
        this.isActive = false;
    }
    
    static fromJSON(json) {
        const data = JSON.parse(json);
        return new User(data.name, data.email, data.age);
    }
}

module.exports = User;
```

#### Depois (TypeScript)

```typescript
// User.ts
export interface UserData {
    name: string;
    email: string;
    age: number;
}

export class User {
    public readonly name: string;
    public readonly email: string;
    public readonly age: number;
    public isActive: boolean;
    public readonly createdAt: Date;
    
    constructor(name: string, email: string, age: number) {
        this.name = name;
        this.email = email;
        this.age = age;
        this.isActive = true;
        this.createdAt = new Date();
    }
    
    getDisplayName(): string {
        return `${this.name} (${this.email})`;
    }
    
    isAdult(): boolean {
        return this.age >= 18;
    }
    
    deactivate(): void {
        this.isActive = false;
    }
    
    static fromJSON(json: string): User {
        const data: UserData = JSON.parse(json);
        return new User(data.name, data.email, data.age);
    }
}
```

### 3. Convertendo APIs e Requests

#### Antes (JavaScript)

```javascript
// api.js
const axios = require('axios');

class ApiClient {
    constructor(baseURL) {
        this.client = axios.create({ baseURL });
    }
    
    async getUsers() {
        const response = await this.client.get('/users');
        return response.data;
    }
    
    async getUserById(id) {
        const response = await this.client.get(`/users/${id}`);
        return response.data;
    }
    
    async createUser(userData) {
        const response = await this.client.post('/users', userData);
        return response.data;
    }
    
    async updateUser(id, userData) {
        const response = await this.client.put(`/users/${id}`, userData);
        return response.data;
    }
    
    async deleteUser(id) {
        await this.client.delete(`/users/${id}`);
    }
}

module.exports = ApiClient;
```

#### Depois (TypeScript)

```typescript
// api.ts
import axios, { AxiosInstance, AxiosResponse } from 'axios';

export interface User {
    id: string;
    name: string;
    email: string;
    age: number;
    isActive: boolean;
    createdAt: string;
}

export interface CreateUserRequest {
    name: string;
    email: string;
    age: number;
}

export interface UpdateUserRequest {
    name?: string;
    email?: string;
    age?: number;
}

export interface ApiResponse<T> {
    data: T;
    message: string;
    success: boolean;
}

export class ApiClient {
    private client: AxiosInstance;
    
    constructor(baseURL: string) {
        this.client = axios.create({ baseURL });
    }
    
    async getUsers(): Promise<User[]> {
        const response: AxiosResponse<ApiResponse<User[]>> = await this.client.get('/users');
        return response.data.data;
    }
    
    async getUserById(id: string): Promise<User> {
        const response: AxiosResponse<ApiResponse<User>> = await this.client.get(`/users/${id}`);
        return response.data.data;
    }
    
    async createUser(userData: CreateUserRequest): Promise<User> {
        const response: AxiosResponse<ApiResponse<User>> = await this.client.post('/users', userData);
        return response.data.data;
    }
    
    async updateUser(id: string, userData: UpdateUserRequest): Promise<User> {
        const response: AxiosResponse<ApiResponse<User>> = await this.client.put(`/users/${id}`, userData);
        return response.data.data;
    }
    
    async deleteUser(id: string): Promise<void> {
        await this.client.delete(`/users/${id}`);
    }
}
```

## Lidando com Bibliotecas Externas

### Instalando Type Definitions

```bash
# Bibliotecas populares com tipos incluídos
npm install axios          # Já inclui tipos
npm install date-fns       # Já inclui tipos

# Bibliotecas que precisam de @types separados
npm install --save-dev @types/lodash
npm install --save-dev @types/express
npm install --save-dev @types/node
npm install --save-dev @types/jest
```

### Criando Declarações Customizadas

```typescript
// types/global.d.ts
// Para bibliotecas sem tipos disponíveis
declare module 'biblioteca-sem-tipos' {
    export function funcaoExemplo(param: string): number;
    export interface ConfigInterface {
        opcao1: boolean;
        opcao2: string;
    }
}

// Para variáveis globais
declare global {
    interface Window {
        minhaVariavelGlobal: {
            config: Record<string, any>;
            version: string;
        };
    }
}

// Para módulos CSS
declare module '*.module.css' {
    const classes: { [key: string]: string };
    export default classes;
}

// Para assets
declare module '*.png' {
    const src: string;
    export default src;
}

declare module '*.svg' {
    const src: string;
    export default src;
}
```

## Configuração Progressiva

### Fase 1: Configuração Básica

```json
// tsconfig.json - Fase 1
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "allowJs": true,
    "checkJs": false,
    "strict": false,
    "noImplicitAny": false,
    "outDir": "./dist",
    "rootDir": "./src",
    "esModuleInterop": true,
    "skipLibCheck": true
  }
}
```

### Fase 2: Verificações Básicas

```json
// tsconfig.json - Fase 2
{
  "compilerOptions": {
    // ... configurações anteriores
    "noImplicitAny": true,
    "strictNullChecks": false,
    "noImplicitReturns": true,
    "noUnusedLocals": true
  }
}
```

### Fase 3: Strict Mode

```json
// tsconfig.json - Fase 3
{
  "compilerOptions": {
    // ... configurações anteriores
    "strict": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true,
    "noImplicitThis": true
  }
}
```

## Ferramentas de Migração

### TypeScript Migration Tool

```bash
# Instalar ferramenta de migração
npm install -g typescript-migration-tool

# Executar migração automática
tmt migrate src/
```

### Scripts de Migração Customizados

```javascript
// scripts/migrate.js
const fs = require('fs');
const path = require('path');

function renameJsToTs(dir) {
    const files = fs.readdirSync(dir);
    
    files.forEach(file => {
        const fullPath = path.join(dir, file);
        const stat = fs.statSync(fullPath);
        
        if (stat.isDirectory()) {
            renameJsToTs(fullPath);
        } else if (file.endsWith('.js') && !file.endsWith('.test.js')) {
            const newPath = fullPath.replace('.js', '.ts');
            fs.renameSync(fullPath, newPath);
            console.log(`Renamed: ${fullPath} -> ${newPath}`);
        }
    });
}

renameJsToTs('./src');
```

## Problemas Comuns e Soluções

### 1. Propriedades Dinâmicas

```typescript
// ❌ Problema
const obj = {};
obj.novaProp = 'valor'; // Erro: Property 'novaProp' does not exist

// ✅ Solução 1: Index signature
interface ObjetoDinamico {
    [key: string]: any;
}

const obj: ObjetoDinamico = {};
obj.novaProp = 'valor'; // OK

// ✅ Solução 2: Record type
const obj: Record<string, any> = {};
obj.novaProp = 'valor'; // OK

// ✅ Solução 3: Type assertion
const obj = {} as any;
obj.novaProp = 'valor'; // OK (mas não recomendado)
```

### 2. Imports de Módulos

```typescript
// ❌ Problema - CommonJS
const express = require('express');

// ✅ Solução - ES6 imports
import express from 'express';
// ou
import * as express from 'express';

// Para compatibilidade
import express = require('express');
```

### 3. Callbacks e Event Handlers

```typescript
// ❌ Problema
button.addEventListener('click', function(event) {
    // event é 'any'
});

// ✅ Solução
button.addEventListener('click', (event: MouseEvent) => {
    // event é tipado corretamente
    console.log(event.clientX, event.clientY);
});
```

### 4. Arrays e Objetos Complexos

```typescript
// ❌ Problema
const users = [];
users.push({ name: 'João' }); // Erro: Argument of type '{ name: string; }' is not assignable

// ✅ Solução
interface User {
    name: string;
    email?: string;
}

const users: User[] = [];
users.push({ name: 'João' }); // OK
```

## Checklist de Migração

### ✅ Preparação
- [ ] Backup do projeto
- [ ] Instalar TypeScript e tipos necessários
- [ ] Configurar tsconfig.json inicial
- [ ] Configurar build scripts

### ✅ Migração Gradual
- [ ] Renomear arquivos .js para .ts
- [ ] Adicionar tipos básicos
- [ ] Resolver erros de compilação
- [ ] Adicionar interfaces para dados
- [ ] Configurar tipos para bibliotecas externas

### ✅ Refinamento
- [ ] Ativar strict mode gradualmente
- [ ] Adicionar type guards
- [ ] Melhorar tipagem de funções
- [ ] Documentar tipos complexos
- [ ] Configurar linting

### ✅ Finalização
- [ ] Testes passando
- [ ] Build funcionando
- [ ] Documentação atualizada
- [ ] CI/CD configurado

## Dicas Importantes

1. **Migre gradualmente** - Não tente converter tudo de uma vez
2. **Use `any` temporariamente** - É melhor que não migrar
3. **Priorize arquivos críticos** - Comece pelos mais importantes
4. **Configure CI/CD** - Para detectar problemas cedo
5. **Documente o processo** - Para futuras referências
6. **Treine a equipe** - Garanta que todos entendam TypeScript

---

[Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=3178C6&height=120&section=footer"/>