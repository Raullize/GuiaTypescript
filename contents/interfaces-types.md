<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=3178C6&height=120&section=header&animation=twinkling"/>

# Interfaces e Types

## Interfaces

Interfaces definem a estrutura de objetos, especificando quais propriedades e métodos um objeto deve ter.

### Sintaxe Básica

```typescript
interface Pessoa {
    nome: string;
    idade: number;
    email: string;
}

const usuario: Pessoa = {
    nome: "João",
    idade: 30,
    email: "joao@email.com"
};
```

### Propriedades Opcionais

```typescript
interface Configuracao {
    host: string;
    porta?: number;        // Propriedade opcional
    ssl?: boolean;         // Propriedade opcional
}

const config1: Configuracao = {
    host: "localhost"
};

const config2: Configuracao = {
    host: "localhost",
    porta: 3000,
    ssl: true
};
```

### Propriedades Readonly

```typescript
interface Usuario {
    readonly id: number;   // Não pode ser modificado
    nome: string;
    email: string;
}

const user: Usuario = {
    id: 1,
    nome: "Ana",
    email: "ana@email.com"
};

// user.id = 2; // Erro! id é readonly
user.nome = "Ana Silva"; // OK
```

### Métodos em Interfaces

```typescript
interface Calculadora {
    somar(a: number, b: number): number;
    subtrair(a: number, b: number): number;
    multiplicar?: (a: number, b: number) => number; // Método opcional
}

class MinhaCalculadora implements Calculadora {
    somar(a: number, b: number): number {
        return a + b;
    }
    
    subtrair(a: number, b: number): number {
        return a - b;
    }
}
```

### Extensão de Interfaces

```typescript
interface Animal {
    nome: string;
    idade: number;
}

interface Mamifero extends Animal {
    tipoPelo: string;
}

interface Cachorro extends Mamifero {
    raca: string;
    latir(): void;
}

const meuCachorro: Cachorro = {
    nome: "Rex",
    idade: 3,
    tipoPelo: "curto",
    raca: "Labrador",
    latir() {
        console.log("Au au!");
    }
};
```

### Interfaces com Índices

```typescript
interface Dicionario {
    [chave: string]: string;
}

const traducoes: Dicionario = {
    "hello": "olá",
    "goodbye": "tchau",
    "thank you": "obrigado"
};

interface ArrayNumerico {
    [indice: number]: number;
}

const numeros: ArrayNumerico = [1, 2, 3, 4, 5];
```

## Type Aliases

Type aliases criam novos nomes para tipos existentes, oferecendo mais flexibilidade que interfaces.

### Tipos Primitivos

```typescript
type ID = string | number;
type Status = "ativo" | "inativo" | "pendente";
type Callback = () => void;

let userId: ID = "user123";
let userStatus: Status = "ativo";
let onComplete: Callback = () => console.log("Concluído!");
```

### Tipos de Objeto

```typescript
type Produto = {
    id: number;
    nome: string;
    preco: number;
    categoria?: string;
};

const produto: Produto = {
    id: 1,
    nome: "Notebook",
    preco: 2500.00
};
```

### Union Types

```typescript
type Resultado = string | number | boolean;
type Evento = "click" | "hover" | "focus" | "blur";

function processar(valor: Resultado): string {
    if (typeof valor === "string") {
        return valor.toUpperCase();
    }
    return valor.toString();
}
```

### Intersection Types

```typescript
type Pessoa = {
    nome: string;
    idade: number;
};

type Funcionario = {
    cargo: string;
    salario: number;
};

type PessoaFuncionario = Pessoa & Funcionario;

const funcionario: PessoaFuncionario = {
    nome: "Carlos",
    idade: 35,
    cargo: "Desenvolvedor",
    salario: 6000
};
```

## Interface vs Type

### Quando usar Interface

```typescript
// ✅ Para definir contratos de objetos
interface ApiResponse {
    data: any;
    status: number;
    message: string;
}

// ✅ Para extensão
interface ExtendedResponse extends ApiResponse {
    timestamp: Date;
}

// ✅ Para implementação em classes
class HttpClient implements ApiResponse {
    data: any;
    status: number;
    message: string;
}
```

### Quando usar Type

```typescript
// ✅ Para union types
type Theme = "light" | "dark";

// ✅ Para tipos computados
type Keys = keyof Pessoa;

// ✅ Para tipos condicionais
type NonNullable<T> = T extends null | undefined ? never : T;

// ✅ Para intersection types
type MixedType = TypeA & TypeB;
```

## Generics em Interfaces e Types

### Interface Genérica

```typescript
interface Container<T> {
    valor: T;
    obter(): T;
    definir(valor: T): void;
}

class StringContainer implements Container<string> {
    constructor(public valor: string) {}
    
    obter(): string {
        return this.valor;
    }
    
    definir(valor: string): void {
        this.valor = valor;
    }
}
```

### Type Genérico

```typescript
type Resposta<T> = {
    sucesso: boolean;
    dados: T;
    erro?: string;
};

type RespostaUsuario = Resposta<Usuario>;
type RespostaProdutos = Resposta<Produto[]>;

const resposta: RespostaUsuario = {
    sucesso: true,
    dados: {
        id: 1,
        nome: "João",
        email: "joao@email.com"
    }
};
```

## Mapped Types

```typescript
// Torna todas as propriedades opcionais
type Partial<T> = {
    [P in keyof T]?: T[P];
};

// Torna todas as propriedades obrigatórias
type Required<T> = {
    [P in keyof T]-?: T[P];
};

// Torna todas as propriedades readonly
type Readonly<T> = {
    readonly [P in keyof T]: T[P];
};

// Exemplo de uso
type UsuarioOpcional = Partial<Usuario>;
type UsuarioReadonly = Readonly<Usuario>;
```

## Conditional Types

```typescript
type IsArray<T> = T extends any[] ? true : false;

type Test1 = IsArray<string[]>;  // true
type Test2 = IsArray<number>;    // false

// Extrair tipo de retorno de função
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

type FuncReturn = ReturnType<() => string>; // string
```

## Utility Types Avançados

```typescript
// Pick - Seleciona propriedades específicas
type UsuarioBasico = Pick<Usuario, "nome" | "email">;

// Omit - Remove propriedades específicas
type UsuarioSemId = Omit<Usuario, "id">;

// Record - Cria tipo com chaves e valores específicos
type Notas = Record<string, number>;

// Exclude - Remove tipos de union
type SemString = Exclude<string | number | boolean, string>; // number | boolean

// Extract - Extrai tipos de union
type ApenasString = Extract<string | number | boolean, string>; // string
```

## Boas Práticas

1. **Use interfaces para objetos** que podem ser estendidos
2. **Use types para unions** e tipos computados
3. **Prefira readonly** para dados imutáveis
4. **Use generics** para reutilização
5. **Documente interfaces complexas** com comentários
6. **Evite any** - use unknown quando necessário

```typescript
/**
 * Representa um usuário do sistema
 * @example
 * const user: Usuario = {
 *   id: 1,
 *   nome: "João",
 *   email: "joao@email.com"
 * }
 */
interface Usuario {
    /** Identificador único do usuário */
    readonly id: number;
    /** Nome completo do usuário */
    nome: string;
    /** Email válido do usuário */
    email: string;
}
```

---

[Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=3178C6&height=120&section=footer"/>