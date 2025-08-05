<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=3178C6&height=120&section=header&animation=twinkling"/>

# ⭐ Recursos Especiais

## Template Literal Types

Tipos baseados em template strings que permitem criar tipos dinâmicos.

### Básico

```typescript
// Tipos literais de template
type Saudacao = `Olá, ${string}!`;
type EventoClick = `click-${string}`;
type CSSProperty = `--${string}`;

// Exemplos de uso
const saudacao: Saudacao = "Olá, João!"; // ✅
const evento: EventoClick = "click-button"; // ✅
const cssVar: CSSProperty = "--primary-color"; // ✅

// const invalido: Saudacao = "Oi, João!"; // ❌ Erro
```

### Combinações Avançadas

```typescript
// Union types em templates
type Tamanho = "small" | "medium" | "large";
type Cor = "red" | "blue" | "green";
type ClasseCSS = `${Tamanho}-${Cor}`;

// Resulta em: "small-red" | "small-blue" | "small-green" | "medium-red" | ...

// Função que usa o tipo
function aplicarClasse(elemento: HTMLElement, classe: ClasseCSS): void {
    elemento.className = classe;
}

aplicarClasse(document.body, "large-blue"); // ✅
// aplicarClasse(document.body, "huge-yellow"); // ❌ Erro
```

### Manipulação de Strings

```typescript
// Utility types para strings
type Uppercase<S extends string> = intrinsic;
type Lowercase<S extends string> = intrinsic;
type Capitalize<S extends string> = intrinsic;
type Uncapitalize<S extends string> = intrinsic;

// Exemplos práticos
type EventHandler<T extends string> = `on${Capitalize<T>}`;
type ClickHandler = EventHandler<"click">; // "onClick"
type MouseHandler = EventHandler<"mouseOver">; // "onMouseOver"

// Sistema de rotas tipadas
type Route = "/" | "/users" | "/products" | "/settings";
type APIRoute = `api${Route}`;
// Resulta em: "api/" | "api/users" | "api/products" | "api/settings"

// Gerador de métodos HTTP
type HTTPMethod = "GET" | "POST" | "PUT" | "DELETE";
type APIMethod<T extends string> = `${Lowercase<HTTPMethod>}${Capitalize<T>}`;

type UserMethods = APIMethod<"user">;
// Resulta em: "getUser" | "postUser" | "putUser" | "deleteUser"
```

### Extração de Padrões

```typescript
// Extrair partes de strings
type ExtractRouteParams<T extends string> = 
    T extends `${string}/:${infer Param}/${infer Rest}`
        ? Param | ExtractRouteParams<`/${Rest}`>
        : T extends `${string}/:${infer Param}`
        ? Param
        : never;

type UserRoute = "/users/:id/posts/:postId";
type Params = ExtractRouteParams<UserRoute>; // "id" | "postId"

// Sistema de validação de email
type Email<T extends string> = T extends `${string}@${string}.${string}` ? T : never;

type ValidEmail = Email<"user@example.com">; // "user@example.com"
// type InvalidEmail = Email<"invalid-email">; // never

// Função que só aceita emails válidos
function enviarEmail<T extends string>(email: Email<T>): void {
    console.log(`Enviando email para: ${email}`);
}

enviarEmail("user@example.com"); // ✅
// enviarEmail("invalid"); // ❌ Erro
```

## Conditional Types Avançados

### Tipos Condicionais Complexos

```typescript
// Verificar se um tipo é array
type IsArray<T> = T extends (infer U)[] ? true : false;

type Test1 = IsArray<string[]>; // true
type Test2 = IsArray<string>; // false

// Extrair tipo de retorno de função
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

type FuncReturn = ReturnType<() => string>; // string
type AsyncReturn = ReturnType<() => Promise<number>>; // Promise<number>

// Tipo condicional para APIs
type APIResponse<T> = T extends "success" 
    ? { status: "success"; data: any }
    : T extends "error"
    ? { status: "error"; message: string }
    : { status: "loading" };

type SuccessResponse = APIResponse<"success">;
type ErrorResponse = APIResponse<"error">;
type LoadingResponse = APIResponse<"loading">;
```

### Distribuição em Union Types

```typescript
// Conditional types são distributivos
type ToArray<T> = T extends any ? T[] : never;

type StringOrNumberArray = ToArray<string | number>;
// Resulta em: string[] | number[] (não (string | number)[])

// Filtrar tipos de uma union
type NonNullable<T> = T extends null | undefined ? never : T;

type CleanType = NonNullable<string | null | number | undefined>;
// Resulta em: string | number

// Extrair tipos de função de uma union
type FunctionTypes<T> = T extends (...args: any[]) => any ? T : never;

type Mixed = string | number | (() => void) | ((x: number) => string);
type OnlyFunctions = FunctionTypes<Mixed>;
// Resulta em: (() => void) | ((x: number) => string)
```

## Mapped Types Avançados

### Transformações Complexas

```typescript
// Tornar propriedades opcionais baseado em condição
type OptionalByType<T, U> = {
    [K in keyof T]: T[K] extends U ? T[K]? : T[K];
};

interface User {
    id: number;
    name: string;
    email: string;
    avatar?: string;
}

type UserWithOptionalStrings = OptionalByType<User, string>;
// { id: number; name?: string; email?: string; avatar?: string; }

// Prefixar chaves
type Prefixed<T, P extends string> = {
    [K in keyof T as `${P}${string & K}`]: T[K];
};

type PrefixedUser = Prefixed<User, "user_">;
// { user_id: number; user_name: string; user_email: string; user_avatar?: string; }

// Filtrar propriedades por tipo
type PickByType<T, U> = {
    [K in keyof T as T[K] extends U ? K : never]: T[K];
};

type UserStrings = PickByType<User, string>;
// { name: string; email: string; avatar?: string; }
```

### Key Remapping

```typescript
// Remapear chaves com transformações
type Getters<T> = {
    [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K];
};

type UserGetters = Getters<User>;
// {
//     getId: () => number;
//     getName: () => string;
//     getEmail: () => string;
//     getAvatar: () => string | undefined;
// }

// Criar setters
type Setters<T> = {
    [K in keyof T as `set${Capitalize<string & K>}`]: (value: T[K]) => void;
};

// Combinar getters e setters
type AccessorPattern<T> = Getters<T> & Setters<T>;

type UserAccessors = AccessorPattern<Pick<User, "name" | "email">>;
```

## Recursive Types

### Tipos Recursivos

```typescript
// Estrutura de árvore
interface TreeNode<T> {
    value: T;
    children?: TreeNode<T>[];
}

// JSON tipado
type JSONValue = 
    | string
    | number
    | boolean
    | null
    | JSONValue[]
    | { [key: string]: JSONValue };

// Caminho profundo em objeto
type DeepKeyOf<T> = {
    [K in keyof T]: T[K] extends object 
        ? K | `${string & K}.${DeepKeyOf<T[K]>}`
        : K;
}[keyof T];

interface NestedObject {
    user: {
        profile: {
            name: string;
            settings: {
                theme: string;
            };
        };
    };
    posts: Array<{ title: string }>;
}

type DeepKeys = DeepKeyOf<NestedObject>;
// "user" | "posts" | "user.profile" | "user.profile.name" | "user.profile.settings" | "user.profile.settings.theme"
```

### Operações Recursivas

```typescript
// Tornar tudo opcional recursivamente
type DeepPartial<T> = {
    [K in keyof T]?: T[K] extends object ? DeepPartial<T[K]> : T[K];
};

// Tornar tudo readonly recursivamente
type DeepReadonly<T> = {
    readonly [K in keyof T]: T[K] extends object ? DeepReadonly<T[K]> : T[K];
};

// Achatar tipos aninhados
type Flatten<T> = T extends (infer U)[] 
    ? Flatten<U>
    : T extends object
    ? { [K in keyof T]: Flatten<T[K]> }
    : T;

type Nested = { a: { b: { c: string[][] } } };
type Flattened = Flatten<Nested>;
// { a: { b: { c: string } } }
```

## Higher-Order Types

### Tipos de Ordem Superior

```typescript
// Functor pattern
type Functor<T> = {
    map<U>(fn: (value: T) => U): Functor<U>;
    value: T;
};

// Maybe/Optional pattern
type Maybe<T> = {
    readonly _tag: "Some" | "None";
    readonly value?: T;
};

const Some = <T>(value: T): Maybe<T> => ({ _tag: "Some", value });
const None: Maybe<never> = { _tag: "None" };

// Funções para Maybe
function map<T, U>(maybe: Maybe<T>, fn: (value: T) => U): Maybe<U> {
    return maybe._tag === "Some" && maybe.value !== undefined
        ? Some(fn(maybe.value))
        : None;
}

function flatMap<T, U>(maybe: Maybe<T>, fn: (value: T) => Maybe<U>): Maybe<U> {
    return maybe._tag === "Some" && maybe.value !== undefined
        ? fn(maybe.value)
        : None;
}

// Either pattern para tratamento de erros
type Either<L, R> = 
    | { readonly _tag: "Left"; readonly left: L }
    | { readonly _tag: "Right"; readonly right: R };

const Left = <L>(left: L): Either<L, never> => ({ _tag: "Left", left });
const Right = <R>(right: R): Either<never, R> => ({ _tag: "Right", right });

// Função que pode falhar
function dividir(a: number, b: number): Either<string, number> {
    return b === 0 ? Left("Divisão por zero") : Right(a / b);
}
```

### Builder Pattern Tipado

```typescript
// Builder pattern com tipos
class QueryBuilder<T = {}> {
    private query: T;
    
    constructor(query: T = {} as T) {
        this.query = query;
    }
    
    select<K extends string>(
        fields: K[]
    ): QueryBuilder<T & { select: K[] }> {
        return new QueryBuilder({ ...this.query, select: fields });
    }
    
    where<K extends string, V>(
        field: K, 
        value: V
    ): QueryBuilder<T & { where: Record<K, V> }> {
        return new QueryBuilder({ 
            ...this.query, 
            where: { [field]: value } as Record<K, V>
        });
    }
    
    orderBy<K extends string>(
        field: K, 
        direction: "asc" | "desc" = "asc"
    ): QueryBuilder<T & { orderBy: { field: K; direction: "asc" | "desc" } }> {
        return new QueryBuilder({ 
            ...this.query, 
            orderBy: { field, direction }
        });
    }
    
    build(): T {
        return this.query;
    }
}

// Uso do builder
const query = new QueryBuilder()
    .select(["name", "email"])
    .where("active", true)
    .orderBy("createdAt", "desc")
    .build();

// Tipo inferido:
// {
//     select: ("name" | "email")[];
//     where: Record<"active", boolean>;
//     orderBy: { field: "createdAt"; direction: "desc" };
// }
```

## Type-Level Programming

### Computações em Tipos

```typescript
// Aritmética em tipos (limitada)
type Length<T extends readonly any[]> = T['length'];
type Head<T extends readonly any[]> = T extends readonly [infer H, ...any[]] ? H : never;
type Tail<T extends readonly any[]> = T extends readonly [any, ...infer Rest] ? Rest : [];

// Reverter array
type Reverse<T extends readonly any[]> = T extends readonly [...infer Rest, infer Last]
    ? [Last, ...Reverse<Rest>]
    : [];

type Original = [1, 2, 3, 4];
type Reversed = Reverse<Original>; // [4, 3, 2, 1]

// Concatenar arrays
type Concat<T extends readonly any[], U extends readonly any[]> = [...T, ...U];

type Combined = Concat<[1, 2], [3, 4]>; // [1, 2, 3, 4]

// Includes (verificar se contém)
type Includes<T extends readonly any[], U> = T extends readonly [infer First, ...infer Rest]
    ? First extends U
        ? true
        : Includes<Rest, U>
    : false;

type HasNumber = Includes<["a", "b", 3], 3>; // true
type HasString = Includes<[1, 2, 3], "a">; // false
```

### State Machine Types

```typescript
// Máquina de estados tipada
type State = "idle" | "loading" | "success" | "error";

type Event = 
    | { type: "FETCH" }
    | { type: "SUCCESS"; data: any }
    | { type: "ERROR"; error: string }
    | { type: "RESET" };

type Transition<S extends State, E extends Event> = 
    S extends "idle"
        ? E extends { type: "FETCH" }
            ? "loading"
            : S
        : S extends "loading"
        ? E extends { type: "SUCCESS" }
            ? "success"
            : E extends { type: "ERROR" }
            ? "error"
            : S
        : S extends "success" | "error"
        ? E extends { type: "RESET" }
            ? "idle"
            : E extends { type: "FETCH" }
            ? "loading"
            : S
        : never;

// Função de transição tipada
function transition<S extends State, E extends Event>(
    state: S,
    event: E
): Transition<S, E> {
    // Implementação da lógica de transição
    switch (state) {
        case "idle":
            return event.type === "FETCH" ? "loading" as Transition<S, E> : state as Transition<S, E>;
        case "loading":
            if (event.type === "SUCCESS") return "success" as Transition<S, E>;
            if (event.type === "ERROR") return "error" as Transition<S, E>;
            return state as Transition<S, E>;
        case "success":
        case "error":
            if (event.type === "RESET") return "idle" as Transition<S, E>;
            if (event.type === "FETCH") return "loading" as Transition<S, E>;
            return state as Transition<S, E>;
        default:
            return state as Transition<S, E>;
    }
}

// Uso
const newState1 = transition("idle", { type: "FETCH" }); // "loading"
const newState2 = transition("loading", { type: "SUCCESS", data: {} }); // "success"
```

## Phantom Types

### Tipos Fantasma para Segurança

```typescript
// Branded types para unidades
type Brand<T, B> = T & { readonly __brand: B };

type Meters = Brand<number, "meters">;
type Seconds = Brand<number, "seconds">;
type MetersPerSecond = Brand<number, "meters/second">;

// Funções construtoras
const meters = (value: number): Meters => value as Meters;
const seconds = (value: number): Seconds => value as Seconds;

// Operações tipadas
function calculateSpeed(distance: Meters, time: Seconds): MetersPerSecond {
    return (distance / time) as MetersPerSecond;
}

// Uso
const distance = meters(100);
const time = seconds(10);
const speed = calculateSpeed(distance, time);

// Erro em tempo de compilação
// const wrongSpeed = calculateSpeed(time, distance); // ❌ Tipos incompatíveis

// Sistema de permissões
type Permission = Brand<string, "permission">;
type Role = Brand<string, "role">;

const createPermission = (perm: string): Permission => perm as Permission;
const createRole = (role: string): Role => role as Role;

function hasPermission(user: { role: Role }, required: Permission): boolean {
    // Lógica de verificação
    return true;
}

const adminRole = createRole("admin");
const readPermission = createPermission("read");

hasPermission({ role: adminRole }, readPermission); // ✅
// hasPermission({ role: readPermission }, adminRole); // ❌ Erro de tipo
```

## Dicas e Truques Avançados

### Performance de Tipos

```typescript
// Evitar tipos muito complexos
// ❌ Pode causar problemas de performance
type VeryComplexType<T> = T extends any ? 
    T extends string ? 
        T extends `${infer A}${infer B}${infer C}${infer D}` ?
            // ... muitas verificações aninhadas
            never : never : never : never;

// ✅ Melhor abordagem
type SimpleCheck<T> = T extends string ? T : never;
type ComplexLogic<T> = SimpleCheck<T> extends never ? never : /* lógica simples */;

// Usar cache de tipos
type CachedResult<T> = T extends infer U ? U : never;

// Limitar recursão
type SafeRecursive<T, Depth extends number = 5> = 
    Depth extends 0 ? never :
    T extends object ? 
        { [K in keyof T]: SafeRecursive<T[K], Prev<Depth>> } :
        T;

type Prev<T extends number> = T extends 5 ? 4 :
    T extends 4 ? 3 :
    T extends 3 ? 2 :
    T extends 2 ? 1 :
    T extends 1 ? 0 : never;
```

### Debugging de Tipos

```typescript
// Utilitários para debug
type Debug<T> = T extends infer U ? { [K in keyof U]: U[K] } : never;
type Expand<T> = T extends (...args: infer A) => infer R
    ? (...args: Expand<A>) => Expand<R>
    : T extends infer O
    ? { [K in keyof O]: O[K] }
    : never;

// Forçar erro para ver tipo
type InspectType<T> = T extends never ? never : T;
// const _inspect: InspectType<ComplexType> = null!; // Hover para ver o tipo

// Verificar se dois tipos são iguais
type Equal<X, Y> = (<T>() => T extends X ? 1 : 2) extends (<T>() => T extends Y ? 1 : 2) ? true : false;

type Test = Equal<string, string>; // true
type Test2 = Equal<string, number>; // false
```

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=3178C6&height=120&section=footer"/>