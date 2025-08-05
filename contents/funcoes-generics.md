<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=3178C6&height=120&section=header&animation=twinkling"/>

# 🧑‍💻 Funções e Generics

## Funções em TypeScript

### Tipagem Básica de Funções

```typescript
// Função com tipos explícitos
function somar(a: number, b: number): number {
    return a + b;
}

// Arrow function
const multiplicar = (a: number, b: number): number => {
    return a * b;
};

// Arrow function simplificada
const dividir = (a: number, b: number): number => a / b;

// Função sem retorno
function log(mensagem: string): void {
    console.log(mensagem);
}
```

### Parâmetros Opcionais e Padrão

```typescript
// Parâmetros opcionais
function cumprimentar(nome: string, sobrenome?: string): string {
    if (sobrenome) {
        return `Olá, ${nome} ${sobrenome}!`;
    }
    return `Olá, ${nome}!`;
}

// Parâmetros com valor padrão
function criarUsuario(nome: string, idade: number = 18, ativo: boolean = true) {
    return { nome, idade, ativo };
}

// Uso
cumprimentar("João");              // "Olá, João!"
cumprimentar("João", "Silva");     // "Olá, João Silva!"
criarUsuario("Ana");               // { nome: "Ana", idade: 18, ativo: true }
criarUsuario("Carlos", 25, false); // { nome: "Carlos", idade: 25, ativo: false }
```

### Rest Parameters

```typescript
// Rest parameters
function somarTodos(...numeros: number[]): number {
    return numeros.reduce((total, num) => total + num, 0);
}

// Combinando parâmetros normais com rest
function apresentar(nome: string, ...hobbies: string[]): string {
    const hobbiesTexto = hobbies.join(", ");
    return `${nome} gosta de: ${hobbiesTexto}`;
}

// Uso
somarTodos(1, 2, 3, 4, 5);                    // 15
apresentar("João", "futebol", "leitura", "música"); // "João gosta de: futebol, leitura, música"
```

### Function Overloads

```typescript
// Declarações de sobrecarga
function processar(valor: string): string;
function processar(valor: number): number;
function processar(valor: boolean): boolean;

// Implementação
function processar(valor: string | number | boolean): string | number | boolean {
    if (typeof valor === "string") {
        return valor.toUpperCase();
    }
    if (typeof valor === "number") {
        return valor * 2;
    }
    return !valor;
}

// Uso
const resultado1 = processar("hello");  // string
const resultado2 = processar(5);        // number
const resultado3 = processar(true);     // boolean
```

## Generics

### Introdução aos Generics

```typescript
// Sem generics - limitado
function primeiroElementoString(array: string[]): string | undefined {
    return array[0];
}

function primeiroElementoNumber(array: number[]): number | undefined {
    return array[0];
}

// Com generics - reutilizável
function primeiroElemento<T>(array: T[]): T | undefined {
    return array[0];
}

// Uso
const primeiraString = primeiroElemento(["a", "b", "c"]);    // string | undefined
const primeiroNumero = primeiroElemento([1, 2, 3]);         // number | undefined
const primeiroBool = primeiroElemento([true, false]);       // boolean | undefined
```

### Múltiplos Parâmetros Genéricos

```typescript
function trocar<T, U>(a: T, b: U): [U, T] {
    return [b, a];
}

const resultado = trocar("hello", 42); // [number, string]

// Função de mapeamento
function mapear<T, U>(array: T[], fn: (item: T) => U): U[] {
    return array.map(fn);
}

const numeros = [1, 2, 3, 4];
const strings = mapear(numeros, n => n.toString()); // string[]
const dobrados = mapear(numeros, n => n * 2);       // number[]
```

### Constraints (Restrições)

```typescript
// Constraint básica
interface TemComprimento {
    length: number;
}

function logComprimento<T extends TemComprimento>(item: T): T {
    console.log(`Comprimento: ${item.length}`);
    return item;
}

logComprimento("hello");        // OK - string tem length
logComprimento([1, 2, 3]);      // OK - array tem length
// logComprimento(123);         // Erro - number não tem length

// Constraint com keyof
function obterPropriedade<T, K extends keyof T>(obj: T, chave: K): T[K] {
    return obj[chave];
}

const pessoa = { nome: "João", idade: 30, email: "joao@email.com" };
const nome = obterPropriedade(pessoa, "nome");     // string
const idade = obterPropriedade(pessoa, "idade");   // number
// const erro = obterPropriedade(pessoa, "altura"); // Erro - propriedade não existe
```

### Generics com Classes

```typescript
class Pilha<T> {
    private itens: T[] = [];
    
    empilhar(item: T): void {
        this.itens.push(item);
    }
    
    desempilhar(): T | undefined {
        return this.itens.pop();
    }
    
    topo(): T | undefined {
        return this.itens[this.itens.length - 1];
    }
    
    vazia(): boolean {
        return this.itens.length === 0;
    }
    
    tamanho(): number {
        return this.itens.length;
    }
}

// Uso
const pilhaNumeros = new Pilha<number>();
pilhaNumeros.empilhar(1);
pilhaNumeros.empilhar(2);
console.log(pilhaNumeros.topo()); // 2

const pilhaStrings = new Pilha<string>();
pilhaStrings.empilhar("hello");
pilhaStrings.empilhar("world");
```

### Generics com Interfaces

```typescript
interface Repositorio<T> {
    salvar(item: T): Promise<T>;
    buscarPorId(id: string): Promise<T | null>;
    buscarTodos(): Promise<T[]>;
    atualizar(id: string, item: Partial<T>): Promise<T>;
    deletar(id: string): Promise<void>;
}

interface Usuario {
    id: string;
    nome: string;
    email: string;
}

class RepositorioUsuario implements Repositorio<Usuario> {
    private usuarios: Usuario[] = [];
    
    async salvar(usuario: Usuario): Promise<Usuario> {
        this.usuarios.push(usuario);
        return usuario;
    }
    
    async buscarPorId(id: string): Promise<Usuario | null> {
        return this.usuarios.find(u => u.id === id) || null;
    }
    
    async buscarTodos(): Promise<Usuario[]> {
        return [...this.usuarios];
    }
    
    async atualizar(id: string, dadosAtualizacao: Partial<Usuario>): Promise<Usuario> {
        const usuario = await this.buscarPorId(id);
        if (!usuario) throw new Error("Usuário não encontrado");
        
        Object.assign(usuario, dadosAtualizacao);
        return usuario;
    }
    
    async deletar(id: string): Promise<void> {
        const index = this.usuarios.findIndex(u => u.id === id);
        if (index !== -1) {
            this.usuarios.splice(index, 1);
        }
    }
}
```

### Utility Types com Generics

```typescript
// Partial - torna todas as propriedades opcionais
type UsuarioOpcional = Partial<Usuario>;

// Required - torna todas as propriedades obrigatórias
type UsuarioCompleto = Required<UsuarioOpcional>;

// Pick - seleciona propriedades específicas
type UsuarioBasico = Pick<Usuario, "nome" | "email">;

// Omit - remove propriedades específicas
type UsuarioSemId = Omit<Usuario, "id">;

// Record - cria objeto com chaves e valores específicos
type ConfiguracaoTema = Record<"primary" | "secondary" | "accent", string>;

const tema: ConfiguracaoTema = {
    primary: "#007bff",
    secondary: "#6c757d",
    accent: "#28a745"
};
```

### Conditional Types

```typescript
// Tipo condicional básico
type TipoSeString<T> = T extends string ? string : number;

type Teste1 = TipoSeString<string>; // string
type Teste2 = TipoSeString<number>; // number

// Extrair tipo de retorno
type TipoRetorno<T> = T extends (...args: any[]) => infer R ? R : never;

type RetornoSomar = TipoRetorno<typeof somar>; // number

// Extrair tipo de array
type TipoElemento<T> = T extends (infer U)[] ? U : never;

type ElementoNumeros = TipoElemento<number[]>; // number
type ElementoStrings = TipoElemento<string[]>; // string
```

### Mapped Types Avançados

```typescript
// Torna propriedades readonly
type SomenteeLeitura<T> = {
    readonly [P in keyof T]: T[P];
};

// Torna propriedades opcionais
type Opcional<T> = {
    [P in keyof T]?: T[P];
};

// Adiciona prefixo às chaves
type ComPrefixo<T, P extends string> = {
    [K in keyof T as `${P}${string & K}`]: T[K];
};

type UsuarioComPrefixo = ComPrefixo<Usuario, "user_">;
// Resultado: { user_id: string; user_nome: string; user_email: string; }

// Filtra propriedades por tipo
type ApenasStrings<T> = {
    [K in keyof T]: T[K] extends string ? K : never;
}[keyof T];

type ChavesString = ApenasStrings<Usuario>; // "nome" | "email"
```

### Funções de Alta Ordem com Generics

```typescript
// Compose function
function compor<A, B, C>(f: (b: B) => C, g: (a: A) => B): (a: A) => C {
    return (a: A) => f(g(a));
}

// Curry function
function curry<A, B, C>(fn: (a: A, b: B) => C): (a: A) => (b: B) => C {
    return (a: A) => (b: B) => fn(a, b);
}

// Pipe function
function pipe<T>(...fns: Array<(arg: T) => T>): (value: T) => T {
    return (value: T) => fns.reduce((acc, fn) => fn(acc), value);
}

// Uso
const adicionarUm = (x: number) => x + 1;
const multiplicarPorDois = (x: number) => x * 2;
const toString = (x: number) => x.toString();

const processarNumero = pipe(adicionarUm, multiplicarPorDois, toString);
const resultado = processarNumero(5); // "12"
```

## Padrões Avançados

### Builder Pattern com Generics

```typescript
class UsuarioBuilder<T = {}> {
    private dados: T;
    
    constructor(dados: T = {} as T) {
        this.dados = dados;
    }
    
    comNome<N extends string>(nome: N): UsuarioBuilder<T & { nome: N }> {
        return new UsuarioBuilder({ ...this.dados, nome });
    }
    
    comIdade<I extends number>(idade: I): UsuarioBuilder<T & { idade: I }> {
        return new UsuarioBuilder({ ...this.dados, idade });
    }
    
    comEmail<E extends string>(email: E): UsuarioBuilder<T & { email: E }> {
        return new UsuarioBuilder({ ...this.dados, email });
    }
    
    build(): T {
        return this.dados;
    }
}

// Uso
const usuario = new UsuarioBuilder()
    .comNome("João")
    .comIdade(30)
    .comEmail("joao@email.com")
    .build(); // { nome: "João", idade: 30, email: "joao@email.com" }
```

### Type-safe Event Emitter

```typescript
type EventMap = {
    [key: string]: any[];
};

class EventEmitter<T extends EventMap> {
    private listeners: {
        [K in keyof T]?: Array<(...args: T[K]) => void>;
    } = {};
    
    on<K extends keyof T>(event: K, listener: (...args: T[K]) => void): void {
        if (!this.listeners[event]) {
            this.listeners[event] = [];
        }
        this.listeners[event]!.push(listener);
    }
    
    emit<K extends keyof T>(event: K, ...args: T[K]): void {
        const eventListeners = this.listeners[event];
        if (eventListeners) {
            eventListeners.forEach(listener => listener(...args));
        }
    }
}

// Definindo tipos de eventos
type AppEvents = {
    'user:login': [string, Date];
    'user:logout': [string];
    'data:update': [{ id: string; data: any }];
};

const emitter = new EventEmitter<AppEvents>();

// Type-safe event handling
emitter.on('user:login', (username, loginTime) => {
    console.log(`${username} logou em ${loginTime}`);
});

emitter.emit('user:login', 'joão', new Date());
```

## Boas Práticas

1. **Use generics** para reutilização de código
2. **Prefira constraints** específicas a `any`
3. **Use utility types** quando apropriado
4. **Nomeie parâmetros genéricos** de forma descritiva
5. **Evite generics desnecessários** em funções simples
6. **Use conditional types** para lógica de tipos complexa
7. **Documente generics complexos** com exemplos

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=3178C6&height=120&section=footer"/>