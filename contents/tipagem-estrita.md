<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=3178C6&height=120&section=header&animation=twinkling"/>

# 🔒 Tipagem Estrita

## Configuração Strict Mode

### Habilitando Strict Mode

```json
// tsconfig.json
{
  "compilerOptions": {
    "strict": true,  // Habilita todas as verificações rigorosas
    
    // Ou configure individualmente:
    "noImplicitAny": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true,
    "strictBindCallApply": true,
    "strictPropertyInitialization": true,
    "noImplicitThis": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "noUncheckedIndexedAccess": true
  }
}
```

## noImplicitAny

Evita o uso implícito do tipo `any`.

```typescript
// ❌ Erro com noImplicitAny: true
function somar(a, b) {  // Parameter 'a' implicitly has an 'any' type
    return a + b;
}

// ✅ Correto
function somar(a: number, b: number): number {
    return a + b;
}

// ✅ Alternativa com inferência
function somar(a: number, b: number) {  // Retorno inferido como number
    return a + b;
}

// ✅ Para casos específicos onde any é necessário
function processar(data: any): any {
    return data;
}
```

## strictNullChecks

Evita erros de null e undefined.

```typescript
// Com strictNullChecks: true

// ❌ Erro
function obterNome(usuario: { nome: string | null }): string {
    return usuario.nome.toUpperCase(); // Object is possibly 'null'
}

// ✅ Correto - Verificação explícita
function obterNome(usuario: { nome: string | null }): string {
    if (usuario.nome === null) {
        throw new Error('Nome não pode ser null');
    }
    return usuario.nome.toUpperCase();
}

// ✅ Correto - Operador de coalescência nula
function obterNome(usuario: { nome: string | null }): string {
    return (usuario.nome ?? 'Anônimo').toUpperCase();
}

// ✅ Correto - Optional chaining
interface Usuario {
    perfil?: {
        nome?: string;
    };
}

function obterNomePerfil(usuario: Usuario): string {
    return usuario.perfil?.nome ?? 'Nome não informado';
}

// ✅ Type guards para null/undefined
function isNotNull<T>(value: T | null | undefined): value is T {
    return value !== null && value !== undefined;
}

function processarUsuarios(usuarios: (Usuario | null)[]): Usuario[] {
    return usuarios.filter(isNotNull);
}
```

## strictFunctionTypes

Verificação rigorosa de tipos de função.

```typescript
// Com strictFunctionTypes: true

interface Animal {
    nome: string;
}

interface Cachorro extends Animal {
    raca: string;
}

// ❌ Erro - Contravariance em parâmetros
let handlerAnimal: (animal: Animal) => void;
let handlerCachorro: (cachorro: Cachorro) => void;

// Isso seria inseguro:
// handlerAnimal = handlerCachorro; // Erro!

// ✅ Correto - Covariance em retornos
let getAnimal: () => Animal;
let getCachorro: () => Cachorro;

getAnimal = getCachorro; // OK - Cachorro é um Animal

// ✅ Exemplo prático
interface EventHandler<T> {
    (event: T): void;
}

interface ClickEvent {
    type: 'click';
    x: number;
    y: number;
}

interface MouseEvent extends ClickEvent {
    button: number;
}

const handleClick: EventHandler<ClickEvent> = (event) => {
    console.log(`Click em ${event.x}, ${event.y}`);
};

const handleMouse: EventHandler<MouseEvent> = (event) => {
    console.log(`Mouse button ${event.button} em ${event.x}, ${event.y}`);
};

// handleClick = handleMouse; // Erro! MouseEvent é mais específico
```

## strictPropertyInitialization

Garante que propriedades de classe sejam inicializadas.

```typescript
// Com strictPropertyInitialization: true

class Usuario {
    // ❌ Erro - Property 'nome' has no initializer
    nome: string;
    
    // ✅ Correto - Inicialização no construtor
    email: string;
    
    // ✅ Correto - Inicialização direta
    ativo: boolean = true;
    
    // ✅ Correto - Propriedade opcional
    telefone?: string;
    
    // ✅ Correto - Definite assignment assertion
    id!: string; // Será inicializado em outro lugar
    
    constructor(email: string) {
        this.email = email;
        this.inicializarId();
    }
    
    private inicializarId(): void {
        this.id = Math.random().toString(36);
    }
}

// ✅ Padrão com inicialização garantida
class ContadorSeguro {
    private _valor: number;
    
    constructor(valorInicial: number = 0) {
        this._valor = valorInicial;
    }
    
    get valor(): number {
        return this._valor;
    }
    
    incrementar(): void {
        this._valor++;
    }
}

// ✅ Com factory method
class Produto {
    readonly id: string;
    readonly nome: string;
    readonly preco: number;
    
    private constructor(id: string, nome: string, preco: number) {
        this.id = id;
        this.nome = nome;
        this.preco = preco;
    }
    
    static criar(nome: string, preco: number): Produto {
        const id = `prod_${Date.now()}`;
        return new Produto(id, nome, preco);
    }
}
```

## noImplicitThis

Evita uso implícito de `this`.

```typescript
// Com noImplicitThis: true

// ❌ Erro
function configurarEvento() {
    this.addEventListener('click', function() {
        console.log(this); // 'this' implicitly has type 'any'
    });
}

// ✅ Correto - Tipagem explícita de this
interface ElementoHTML {
    addEventListener(event: string, handler: () => void): void;
}

function configurarEvento(this: ElementoHTML): void {
    this.addEventListener('click', function() {
        console.log('Clicado!');
    });
}

// ✅ Correto - Arrow functions preservam this
class ComponenteUI {
    private elemento: HTMLElement;
    
    constructor(elemento: HTMLElement) {
        this.elemento = elemento;
    }
    
    configurarEventos(): void {
        this.elemento.addEventListener('click', () => {
            this.onClick(); // 'this' se refere à instância da classe
        });
    }
    
    private onClick(): void {
        console.log('Componente clicado!');
    }
}

// ✅ Callback com this tipado
interface CallbackContext {
    nome: string;
    processar(): void;
}

function executarCallback(this: CallbackContext, dados: any[]): void {
    console.log(`Processando ${dados.length} itens para ${this.nome}`);
    this.processar();
}

const contexto: CallbackContext = {
    nome: 'Processador',
    processar() {
        console.log('Processamento concluído');
    }
};

executarCallback.call(contexto, [1, 2, 3]);
```

## noImplicitReturns

Garante que todas as rotas de código retornem um valor.

```typescript
// Com noImplicitReturns: true

// ❌ Erro - Not all code paths return a value
function obterStatus(ativo: boolean): string {
    if (ativo) {
        return 'Ativo';
    }
    // Falta return para o caso false
}

// ✅ Correto
function obterStatus(ativo: boolean): string {
    if (ativo) {
        return 'Ativo';
    }
    return 'Inativo';
}

// ✅ Correto - Operador ternário
function obterStatus(ativo: boolean): string {
    return ativo ? 'Ativo' : 'Inativo';
}

// ✅ Correto - Switch com default
function obterCorStatus(status: 'ativo' | 'inativo' | 'pendente'): string {
    switch (status) {
        case 'ativo':
            return 'green';
        case 'inativo':
            return 'red';
        case 'pendente':
            return 'yellow';
        default:
            // TypeScript garante que todos os casos foram cobertos
            const _exhaustive: never = status;
            throw new Error(`Status não tratado: ${_exhaustive}`);
    }
}

// ✅ Função com múltiplas condições
function calcularDesconto(valor: number, categoria: string, vip: boolean): number {
    let desconto = 0;
    
    if (categoria === 'premium') {
        desconto = 0.2;
    } else if (categoria === 'gold') {
        desconto = 0.15;
    } else {
        desconto = 0.1;
    }
    
    if (vip) {
        desconto += 0.05;
    }
    
    return Math.min(desconto, 0.5); // Máximo 50% de desconto
}
```

## noUncheckedIndexedAccess

Torna acessos a índices mais seguros.

```typescript
// Com noUncheckedIndexedAccess: true

const usuarios = ['João', 'Maria', 'Pedro'];

// ❌ Problema - pode ser undefined
const primeiro = usuarios[0]; // tipo: string | undefined
const nome = primeiro.toUpperCase(); // Erro: Object is possibly 'undefined'

// ✅ Correto - Verificação explícita
const primeiro = usuarios[0];
if (primeiro !== undefined) {
    const nome = primeiro.toUpperCase(); // OK
}

// ✅ Correto - Optional chaining
const nome = usuarios[0]?.toUpperCase();

// ✅ Correto - Valor padrão
const nome = (usuarios[0] ?? 'Anônimo').toUpperCase();

// ✅ Type guard para arrays
function isValidIndex<T>(array: T[], index: number): index is keyof T[] {
    return index >= 0 && index < array.length;
}

function obterElementoSeguro<T>(array: T[], index: number): T | undefined {
    return isValidIndex(array, index) ? array[index] : undefined;
}

// ✅ Para objetos com índices
interface DicionarioUsuarios {
    [id: string]: {
        nome: string;
        email: string;
    };
}

const usuarios: DicionarioUsuarios = {
    '1': { nome: 'João', email: 'joao@email.com' },
    '2': { nome: 'Maria', email: 'maria@email.com' }
};

// Acesso seguro
const usuario = usuarios['1']; // tipo: { nome: string; email: string; } | undefined
if (usuario) {
    console.log(usuario.nome); // OK
}

// ✅ Função auxiliar para acesso seguro
function obterUsuario(id: string): { nome: string; email: string; } | null {
    return usuarios[id] ?? null;
}
```

## Utility Types para Tipagem Estrita

### NonNullable

```typescript
// Remove null e undefined de um tipo
type StringNaoNulo = NonNullable<string | null | undefined>; // string

function processarTexto(texto: string | null | undefined): string {
    const textoSeguro: NonNullable<typeof texto> = texto ?? '';
    return textoSeguro.toUpperCase();
}
```

### Required

```typescript
// Torna todas as propriedades obrigatórias
interface UsuarioOpcional {
    nome?: string;
    email?: string;
    idade?: number;
}

type UsuarioCompleto = Required<UsuarioOpcional>;
// { nome: string; email: string; idade: number; }

function validarUsuario(usuario: UsuarioOpcional): UsuarioCompleto {
    if (!usuario.nome || !usuario.email || !usuario.idade) {
        throw new Error('Dados incompletos');
    }
    
    return usuario as UsuarioCompleto;
}
```

### Exact Types

```typescript
// Tipo que não permite propriedades extras
type Exact<T, U extends T> = T & Record<Exclude<keyof U, keyof T>, never>;

interface Configuracao {
    host: string;
    porta: number;
}

function criarServidor<T extends Configuracao>(config: Exact<Configuracao, T>): void {
    // Só aceita exatamente as propriedades de Configuracao
}

criarServidor({ host: 'localhost', porta: 3000 }); // OK
// criarServidor({ host: 'localhost', porta: 3000, extra: true }); // Erro!
```

## Padrões de Validação Estrita

### Type Guards Avançados

```typescript
// Type guard para objetos
function isUsuarioValido(obj: any): obj is { nome: string; email: string; idade: number } {
    return (
        typeof obj === 'object' &&
        obj !== null &&
        typeof obj.nome === 'string' &&
        typeof obj.email === 'string' &&
        typeof obj.idade === 'number' &&
        obj.idade > 0
    );
}

// Type guard para arrays
function isArrayDeStrings(arr: unknown): arr is string[] {
    return Array.isArray(arr) && arr.every(item => typeof item === 'string');
}

// Type guard genérico
function hasProperty<T, K extends string>(
    obj: T,
    prop: K
): obj is T & Record<K, unknown> {
    return typeof obj === 'object' && obj !== null && prop in obj;
}

// Uso dos type guards
function processarDados(dados: unknown): void {
    if (isUsuarioValido(dados)) {
        // dados é tipado como { nome: string; email: string; idade: number }
        console.log(`Usuário: ${dados.nome}, ${dados.email}`);
    }
    
    if (isArrayDeStrings(dados)) {
        // dados é tipado como string[]
        dados.forEach(item => console.log(item.toUpperCase()));
    }
}
```

### Branded Types

```typescript
// Tipos com "marca" para maior segurança
type UserId = string & { readonly __brand: 'UserId' };
type Email = string & { readonly __brand: 'Email' };
type Senha = string & { readonly __brand: 'Senha' };

// Funções de criação
function criarUserId(id: string): UserId {
    if (id.length === 0) {
        throw new Error('ID não pode ser vazio');
    }
    return id as UserId;
}

function criarEmail(email: string): Email {
    if (!email.includes('@')) {
        throw new Error('Email inválido');
    }
    return email as Email;
}

function criarSenha(senha: string): Senha {
    if (senha.length < 8) {
        throw new Error('Senha deve ter pelo menos 8 caracteres');
    }
    return senha as Senha;
}

// Uso seguro
function autenticarUsuario(id: UserId, email: Email, senha: Senha): boolean {
    // Garantia de que os tipos estão corretos
    console.log(`Autenticando ${id} com email ${email}`);
    return true;
}

// Não é possível misturar tipos
const id = criarUserId('user123');
const email = criarEmail('user@example.com');
const senha = criarSenha('minhasenha123');

autenticarUsuario(id, email, senha); // OK
// autenticarUsuario(email, id, senha); // Erro! Tipos incompatíveis
```

## Configuração Avançada

### tsconfig.json Completo para Tipagem Estrita

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "node",
    "lib": ["ES2022", "DOM", "DOM.Iterable"],
    
    // Strict checks
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true,
    "strictBindCallApply": true,
    "strictPropertyInitialization": true,
    "noImplicitThis": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "noUncheckedIndexedAccess": true,
    
    // Additional checks
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "exactOptionalPropertyTypes": true,
    "noImplicitOverride": true,
    "noPropertyAccessFromIndexSignature": true,
    
    // Output
    "outDir": "./dist",
    "rootDir": "./src",
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true,
    
    // Module resolution
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "forceConsistentCasingInFileNames": true,
    "skipLibCheck": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist", "**/*.test.ts"]
}
```

## Benefícios da Tipagem Estrita

1. **Detecção Precoce de Erros** - Problemas encontrados em tempo de compilação
2. **Melhor IntelliSense** - Autocompletar mais preciso
3. **Refatoração Segura** - Mudanças com menor risco
4. **Documentação Viva** - Tipos servem como documentação
5. **Maior Confiança** - Código mais previsível e confiável
6. **Menos Bugs em Produção** - Verificações rigorosas previnem erros

## Dicas para Adoção

1. **Ative gradualmente** - Comece com algumas verificações
2. **Use type guards** - Para validação em runtime
3. **Prefira unknown a any** - Mais seguro para dados externos
4. **Documente exceções** - Quando usar `!` ou `as`
5. **Configure ESLint** - Para regras adicionais de tipagem
6. **Treine a equipe** - Garanta entendimento dos conceitos

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=3178C6&height=120&section=footer"/>