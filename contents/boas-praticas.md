<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=3178C6&height=120&section=header&animation=twinkling"/>

# Boas Práticas e Dicas

## Configuração e Setup

### tsconfig.json Otimizado

```json
{
  "compilerOptions": {
    // Target e Module
    "target": "ES2020",
    "module": "ESNext",
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "moduleResolution": "node",
    
    // Strict Type Checking
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true,
    "noImplicitReturns": true,
    "noImplicitThis": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    
    // Module Resolution
    "baseUrl": "./",
    "paths": {
      "@/*": ["src/*"],
      "@components/*": ["src/components/*"],
      "@utils/*": ["src/utils/*"],
      "@types/*": ["src/types/*"]
    },
    
    // Emit
    "outDir": "./dist",
    "rootDir": "./src",
    "removeComments": true,
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true,
    
    // Interop
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "forceConsistentCasingInFileNames": true,
    "skipLibCheck": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist", "**/*.test.ts"]
}
```

## Tipagem Eficiente

### Prefira Interfaces a Types para Objetos

```typescript
// ✅ Bom - Interface para objetos
interface Usuario {
  id: string;
  nome: string;
  email: string;
  criadoEm: Date;
}

// ✅ Bom - Type para unions e computações
type Status = 'ativo' | 'inativo' | 'pendente';
type UsuarioComStatus = Usuario & { status: Status };

// ❌ Evitar - Type para objetos simples quando interface seria melhor
type UsuarioType = {
  id: string;
  nome: string;
  email: string;
};
```

### Use Utility Types

```typescript
interface Usuario {
  id: string;
  nome: string;
  email: string;
  senha: string;
  criadoEm: Date;
  atualizadoEm: Date;
}

// ✅ Para criação (sem campos automáticos)
type CriarUsuario = Omit<Usuario, 'id' | 'criadoEm' | 'atualizadoEm'>;

// ✅ Para atualização (campos opcionais)
type AtualizarUsuario = Partial<Pick<Usuario, 'nome' | 'email'>>;

// ✅ Para resposta pública (sem senha)
type UsuarioPublico = Omit<Usuario, 'senha'>;

// ✅ Para seleção específica
type UsuarioBasico = Pick<Usuario, 'id' | 'nome' | 'email'>;
```

### Evite `any` - Use Alternativas

```typescript
// ❌ Evitar
function processar(dados: any): any {
  return dados.algumMetodo();
}

// ✅ Use unknown para entrada desconhecida
function processar(dados: unknown): string {
  if (typeof dados === 'object' && dados !== null && 'toString' in dados) {
    return String(dados.toString());
  }
  return 'Dados inválidos';
}

// ✅ Use generics para flexibilidade
function processar<T>(dados: T, processador: (item: T) => string): string {
  return processador(dados);
}

// ✅ Use union types para opções limitadas
function processar(dados: string | number | boolean): string {
  return String(dados);
}
```

## Estrutura de Projeto

### Organização de Arquivos

```
src/
├── types/
│   ├── index.ts          # Exporta todos os tipos
│   ├── api.ts            # Tipos relacionados à API
│   ├── usuario.ts        # Tipos do domínio Usuario
│   └── comum.ts          # Tipos compartilhados
├── utils/
│   ├── index.ts
│   ├── validacao.ts
│   ├── formatacao.ts
│   └── api.ts
├── services/
│   ├── index.ts
│   ├── UsuarioService.ts
│   └── ApiService.ts
├── components/
│   ├── comum/
│   ├── usuario/
│   └── index.ts
└── index.ts
```

### Barrel Exports

```typescript
// types/index.ts
export * from './usuario';
export * from './api';
export * from './comum';

// types/usuario.ts
export interface Usuario {
  id: string;
  nome: string;
  email: string;
}

export interface CriarUsuario {
  nome: string;
  email: string;
  senha: string;
}

export type StatusUsuario = 'ativo' | 'inativo';

// Uso em outros arquivos
import { Usuario, CriarUsuario, StatusUsuario } from '@/types';
```

## Funções e Métodos

### Tipagem de Funções

```typescript
// ✅ Bom - Tipos explícitos para parâmetros e retorno
function calcularIdade(nascimento: Date): number {
  const hoje = new Date();
  return hoje.getFullYear() - nascimento.getFullYear();
}

// ✅ Bom - Arrow function com tipos
const formatarMoeda = (valor: number, moeda: string = 'BRL'): string => {
  return new Intl.NumberFormat('pt-BR', {
    style: 'currency',
    currency: moeda
  }).format(valor);
};

// ✅ Bom - Função com callback tipado
function processarArray<T, U>(
  array: T[],
  processador: (item: T, index: number) => U
): U[] {
  return array.map(processador);
}

// ✅ Bom - Função assíncrona
async function buscarUsuario(id: string): Promise<Usuario | null> {
  try {
    const response = await fetch(`/api/usuarios/${id}`);
    if (!response.ok) return null;
    return await response.json() as Usuario;
  } catch {
    return null;
  }
}
```

### Type Guards

```typescript
// ✅ Type guard para verificação de tipo
function isString(valor: unknown): valor is string {
  return typeof valor === 'string';
}

function isUsuario(obj: unknown): obj is Usuario {
  return (
    typeof obj === 'object' &&
    obj !== null &&
    'id' in obj &&
    'nome' in obj &&
    'email' in obj &&
    typeof (obj as Usuario).id === 'string' &&
    typeof (obj as Usuario).nome === 'string' &&
    typeof (obj as Usuario).email === 'string'
  );
}

// Uso
function processarDados(dados: unknown): string {
  if (isUsuario(dados)) {
    // TypeScript sabe que dados é Usuario aqui
    return `Usuário: ${dados.nome}`;
  }
  
  if (isString(dados)) {
    // TypeScript sabe que dados é string aqui
    return dados.toUpperCase();
  }
  
  return 'Dados inválidos';
}
```

## Error Handling

### Tipos de Erro Customizados

```typescript
// ✅ Classe de erro customizada
class ApiError extends Error {
  constructor(
    message: string,
    public statusCode: number,
    public code?: string
  ) {
    super(message);
    this.name = 'ApiError';
  }
}

class ValidationError extends Error {
  constructor(
    message: string,
    public field: string,
    public value: unknown
  ) {
    super(message);
    this.name = 'ValidationError';
  }
}

// ✅ Result type para operações que podem falhar
type Result<T, E = Error> = 
  | { success: true; data: T }
  | { success: false; error: E };

async function buscarUsuarioSeguro(id: string): Promise<Result<Usuario, ApiError>> {
  try {
    const response = await fetch(`/api/usuarios/${id}`);
    
    if (!response.ok) {
      return {
        success: false,
        error: new ApiError('Usuário não encontrado', response.status)
      };
    }
    
    const usuario = await response.json() as Usuario;
    return { success: true, data: usuario };
  } catch (error) {
    return {
      success: false,
      error: new ApiError('Erro de rede', 0)
    };
  }
}
```

## Validação e Sanitização

### Validação com Type Guards

```typescript
// ✅ Validação de email
function isValidEmail(email: string): boolean {
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  return emailRegex.test(email);
}

// ✅ Validação de objeto completo
function validateUsuario(obj: unknown): obj is CriarUsuario {
  if (typeof obj !== 'object' || obj === null) {
    return false;
  }
  
  const usuario = obj as Record<string, unknown>;
  
  return (
    typeof usuario.nome === 'string' &&
    usuario.nome.length > 0 &&
    typeof usuario.email === 'string' &&
    isValidEmail(usuario.email) &&
    typeof usuario.senha === 'string' &&
    usuario.senha.length >= 6
  );
}

// ✅ Função de validação com mensagens
type ValidationResult = 
  | { valid: true }
  | { valid: false; errors: string[] };

function validateUsuarioDetalhado(obj: unknown): ValidationResult {
  const errors: string[] = [];
  
  if (typeof obj !== 'object' || obj === null) {
    return { valid: false, errors: ['Dados devem ser um objeto'] };
  }
  
  const usuario = obj as Record<string, unknown>;
  
  if (typeof usuario.nome !== 'string' || usuario.nome.length === 0) {
    errors.push('Nome é obrigatório');
  }
  
  if (typeof usuario.email !== 'string' || !isValidEmail(usuario.email)) {
    errors.push('Email deve ser válido');
  }
  
  if (typeof usuario.senha !== 'string' || usuario.senha.length < 6) {
    errors.push('Senha deve ter pelo menos 6 caracteres');
  }
  
  return errors.length === 0 ? { valid: true } : { valid: false, errors };
}
```

## Performance e Otimização

### Lazy Loading de Tipos

```typescript
// ✅ Import dinâmico para tipos pesados
type LazyComponent = () => Promise<{ default: React.ComponentType<any> }>;

const LazyUsuarioForm: LazyComponent = () => import('./UsuarioForm');

// ✅ Conditional types para otimização
type OptionalFields<T, K extends keyof T> = Omit<T, K> & Partial<Pick<T, K>>;

// ✅ Mapped types eficientes
type ReadonlyDeep<T> = {
  readonly [P in keyof T]: T[P] extends object ? ReadonlyDeep<T[P]> : T[P];
};
```

### Evite Tipos Muito Complexos

```typescript
// ❌ Evitar - Tipo muito complexo
type ComplexType<T> = T extends (...args: any[]) => infer R
  ? R extends Promise<infer U>
    ? U extends Array<infer V>
      ? V extends object
        ? { [K in keyof V]: V[K] extends string ? V[K] : never }
        : never
      : never
    : never
  : never;

// ✅ Melhor - Quebrar em tipos menores
type ExtractReturnType<T> = T extends (...args: any[]) => infer R ? R : never;
type ExtractPromiseType<T> = T extends Promise<infer U> ? U : never;
type ExtractArrayType<T> = T extends Array<infer V> ? V : never;
type StringFields<T> = {
  [K in keyof T]: T[K] extends string ? T[K] : never;
};
```

## Documentação e Comentários

### JSDoc para Tipos

```typescript
/**
 * Representa um usuário do sistema
 * @example
 * ```typescript
 * const usuario: Usuario = {
 *   id: '123',
 *   nome: 'João Silva',
 *   email: 'joao@email.com',
 *   criadoEm: new Date()
 * };
 * ```
 */
interface Usuario {
  /** Identificador único do usuário */
  id: string;
  
  /** Nome completo do usuário */
  nome: string;
  
  /** 
   * Email do usuário (deve ser único no sistema)
   * @format email
   */
  email: string;
  
  /** Data de criação do registro */
  criadoEm: Date;
}

/**
 * Busca um usuário pelo ID
 * @param id - ID do usuário a ser buscado
 * @returns Promise que resolve para o usuário ou null se não encontrado
 * @throws {ApiError} Quando há erro de comunicação com a API
 * 
 * @example
 * ```typescript
 * const usuario = await buscarUsuario('123');
 * if (usuario) {
 *   console.log(usuario.nome);
 * }
 * ```
 */
async function buscarUsuario(id: string): Promise<Usuario | null> {
  // implementação
}
```

## Testing

### Tipos para Testes

```typescript
// ✅ Tipos específicos para testes
type MockUsuario = Partial<Usuario> & Pick<Usuario, 'id'>;

function createMockUsuario(overrides: Partial<Usuario> = {}): Usuario {
  return {
    id: '123',
    nome: 'Usuário Teste',
    email: 'teste@email.com',
    criadoEm: new Date(),
    ...overrides
  };
}

// ✅ Type assertions para testes
function assertIsUsuario(obj: unknown): asserts obj is Usuario {
  if (!isUsuario(obj)) {
    throw new Error('Objeto não é um usuário válido');
  }
}

// Uso em testes
test('deve processar usuário', () => {
  const dados = JSON.parse(response);
  assertIsUsuario(dados);
  
  // TypeScript sabe que dados é Usuario aqui
  expect(dados.nome).toBe('João');
});
```

## Checklist de Boas Práticas

### ✅ Configuração
- [ ] `strict: true` no tsconfig.json
- [ ] ESLint configurado com regras TypeScript
- [ ] Prettier para formatação consistente
- [ ] Path mapping configurado
- [ ] Source maps habilitados

### ✅ Tipagem
- [ ] Evitar `any` sempre que possível
- [ ] Usar interfaces para objetos
- [ ] Usar types para unions e computações
- [ ] Implementar type guards quando necessário
- [ ] Usar utility types apropriados

### ✅ Código
- [ ] Funções com tipos explícitos
- [ ] Error handling tipado
- [ ] Validação de entrada
- [ ] Documentação com JSDoc
- [ ] Testes com tipos apropriados

### ✅ Estrutura
- [ ] Organização clara de arquivos
- [ ] Barrel exports para módulos
- [ ] Separação de tipos por domínio
- [ ] Imports organizados e limpos

---

[Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=3178C6&height=120&section=footer"/>