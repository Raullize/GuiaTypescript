<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=3178C6&height=120&section=header&animation=twinkling"/>

# 📂 Tipos Básicos e Avançados

## Tipos Primitivos

### String
```typescript
let nome: string = "João";
let sobrenome: string = 'Silva';
let nomeCompleto: string = `${nome} ${sobrenome}`;
```

### Number
```typescript
let idade: number = 25;
let altura: number = 1.75;
let hexadecimal: number = 0xf00d;
let binario: number = 0b1010;
let octal: number = 0o744;
```

### Boolean
```typescript
let ativo: boolean = true;
let inativo: boolean = false;
```

### Null e Undefined
```typescript
let valorNulo: null = null;
let valorIndefinido: undefined = undefined;
```

## Tipos de Coleção

### Array
```typescript
// Sintaxe com colchetes
let numeros: number[] = [1, 2, 3, 4, 5];
let nomes: string[] = ["Ana", "Bruno", "Carlos"];

// Sintaxe com Generic
let idades: Array<number> = [20, 25, 30];
let cidades: Array<string> = ["SP", "RJ", "MG"];
```

### Tuple
```typescript
// Tupla com tipos fixos
let pessoa: [string, number] = ["João", 30];
let coordenada: [number, number] = [10.5, 20.3];

// Tupla com elementos opcionais
let dados: [string, number?] = ["teste"];
```

## Tipos Especiais

### Any
```typescript
let qualquerCoisa: any = 42;
qualquerCoisa = "agora é string";
qualquerCoisa = true;
```

### Unknown
```typescript
let valorDesconhecido: unknown = 4;
// Precisa de verificação de tipo
if (typeof valorDesconhecido === "number") {
    console.log(valorDesconhecido + 1);
}
```

### Void
```typescript
function log(mensagem: string): void {
    console.log(mensagem);
}
```

### Never
```typescript
function erro(mensagem: string): never {
    throw new Error(mensagem);
}

function loopInfinito(): never {
    while (true) {}
}
```

## Union Types

```typescript
let id: string | number;
id = "abc123";
id = 123;

function formatarId(id: string | number): string {
    if (typeof id === "string") {
        return id.toUpperCase();
    }
    return id.toString();
}
```

## Intersection Types

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
    nome: "Ana",
    idade: 28,
    cargo: "Desenvolvedora",
    salario: 5000
};
```

## Literal Types

```typescript
// String literal
type Direcao = "norte" | "sul" | "leste" | "oeste";
let minhaDirecao: Direcao = "norte";

// Number literal
type DadoValido = 1 | 2 | 3 | 4 | 5 | 6;
let resultado: DadoValido = 4;

// Boolean literal
type Verdadeiro = true;
let sempre: Verdadeiro = true;
```

## Tipos Condicionais

```typescript
type TipoSeString<T> = T extends string ? string : number;

type Teste1 = TipoSeString<string>; // string
type Teste2 = TipoSeString<number>; // number
```

## Mapped Types

```typescript
type Opcional<T> = {
    [P in keyof T]?: T[P];
};

type Pessoa = {
    nome: string;
    idade: number;
    email: string;
};

type PessoaOpcional = Opcional<Pessoa>;
// Resultado: { nome?: string; idade?: number; email?: string; }
```

## Utility Types

### Partial
```typescript
interface Usuario {
    id: number;
    nome: string;
    email: string;
}

function atualizarUsuario(id: number, dados: Partial<Usuario>) {
    // dados pode ter qualquer propriedade de Usuario, mas todas opcionais
}
```

### Required
```typescript
type UsuarioCompleto = Required<Partial<Usuario>>;
// Torna todas as propriedades obrigatórias
```

### Pick
```typescript
type UsuarioBasico = Pick<Usuario, "id" | "nome">;
// Seleciona apenas id e nome
```

### Omit
```typescript
type UsuarioSemId = Omit<Usuario, "id">;
// Remove a propriedade id
```

### Record
```typescript
type Notas = Record<string, number>;
const minhasNotas: Notas = {
    matematica: 9.5,
    portugues: 8.7,
    historia: 7.8
};
```

## Type Guards

```typescript
function isString(valor: unknown): valor is string {
    return typeof valor === "string";
}

function processar(entrada: unknown) {
    if (isString(entrada)) {
        // TypeScript sabe que entrada é string aqui
        console.log(entrada.toUpperCase());
    }
}
```

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=3178C6&height=120&section=footer"/>