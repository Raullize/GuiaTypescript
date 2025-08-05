<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=3178C6&height=120&section=header&animation=twinkling"/>

# 📚 Glossário TypeScript

## A

### Abstract Class
**Classe Abstrata** - Uma classe que não pode ser instanciada diretamente, servindo apenas como base para outras classes.

```typescript
abstract class Animal {
  abstract emitirSom(): void;
  
  mover(): void {
    console.log('O animal está se movendo');
  }
}
```

### Ambient Declaration
**Declaração Ambiente** - Declarações que descrevem tipos para código JavaScript existente.

```typescript
declare var jQuery: (selector: string) => any;
```

### Any
**Tipo Any** - Tipo que desabilita a verificação de tipos, permitindo qualquer valor.

```typescript
let valor: any = 42;
valor = "texto";
valor = true;
```

### Array
**Array** - Tipo que representa uma lista de elementos do mesmo tipo.

```typescript
let numeros: number[] = [1, 2, 3];
let nomes: Array<string> = ["João", "Maria"];
```

### Assertion
**Asserção de Tipo** - Forma de informar ao TypeScript sobre o tipo de uma variável.

```typescript
let valor: unknown = "texto";
let tamanho = (valor as string).length;
```

## B

### Boolean
**Booleano** - Tipo primitivo que representa valores verdadeiro ou falso.

```typescript
let ativo: boolean = true;
let concluido: boolean = false;
```

### Branded Types
**Tipos Marcados** - Técnica para criar tipos distintos baseados em tipos primitivos.

```typescript
type UserId = string & { readonly __brand: 'UserId' };
```

## C

### Class
**Classe** - Modelo para criar objetos com propriedades e métodos.

```typescript
class Pessoa {
  nome: string;
  
  constructor(nome: string) {
    this.nome = nome;
  }
  
  falar(): void {
    console.log(`${this.nome} está falando`);
  }
}
```

### Conditional Types
**Tipos Condicionais** - Tipos que dependem de uma condição para serem determinados.

```typescript
type IsString<T> = T extends string ? true : false;
```

### Constructor
**Construtor** - Método especial chamado quando uma instância de classe é criada.

```typescript
class Carro {
  constructor(public marca: string, public modelo: string) {}
}
```

## D

### Declaration Files
**Arquivos de Declaração** - Arquivos .d.ts que contêm apenas informações de tipo.

```typescript
// tipos.d.ts
declare module 'biblioteca-externa' {
  export function funcao(): string;
}
```

### Decorator
**Decorador** - Função que modifica classes, métodos ou propriedades.

```typescript
function log(target: any, propertyKey: string, descriptor: PropertyDescriptor) {
  // lógica do decorador
}

class MinhaClasse {
  @log
  metodo() {}
}
```

### Destructuring
**Desestruturação** - Sintaxe para extrair valores de arrays ou propriedades de objetos.

```typescript
const [primeiro, segundo] = [1, 2];
const { nome, idade } = { nome: "João", idade: 30 };
```

## E

### Enum
**Enumeração** - Forma de definir um conjunto de constantes nomeadas.

```typescript
enum Cor {
  Vermelho,
  Verde,
  Azul
}
```

### Extends
**Estende** - Palavra-chave para herança de classes ou extensão de interfaces.

```typescript
interface Animal {
  nome: string;
}

interface Cachorro extends Animal {
  raca: string;
}
```

## F

### Function
**Função** - Bloco de código reutilizável que pode receber parâmetros e retornar valores.

```typescript
function somar(a: number, b: number): number {
  return a + b;
}
```

### Function Overloading
**Sobrecarga de Função** - Definir múltiplas assinaturas para uma função.

```typescript
function processar(valor: string): string;
function processar(valor: number): number;
function processar(valor: any): any {
  return valor;
}
```

## G

### Generic
**Genérico** - Tipo que pode trabalhar com diferentes tipos mantendo a segurança de tipos.

```typescript
function identidade<T>(arg: T): T {
  return arg;
}
```

### Guard
**Type Guard** - Função que verifica o tipo de uma variável em tempo de execução.

```typescript
function isString(valor: any): valor is string {
  return typeof valor === 'string';
}
```

## I

### Index Signature
**Assinatura de Índice** - Permite que um objeto tenha propriedades com nomes dinâmicos.

```typescript
interface Dicionario {
  [chave: string]: string;
}
```

### Inference
**Inferência de Tipo** - Capacidade do TypeScript de determinar tipos automaticamente.

```typescript
let numero = 42; // TypeScript infere que é number
```

### Interface
**Interface** - Contrato que define a estrutura de um objeto.

```typescript
interface Usuario {
  nome: string;
  idade: number;
  email?: string;
}
```

### Intersection Types
**Tipos de Interseção** - Combinação de múltiplos tipos em um.

```typescript
type Pessoa = { nome: string };
type Funcionario = { cargo: string };
type PessoaFuncionario = Pessoa & Funcionario;
```

## K

### Keyof
**Keyof** - Operador que retorna as chaves de um tipo como union type.

```typescript
type ChavesUsuario = keyof { nome: string; idade: number }; // "nome" | "idade"
```

## L

### Literal Types
**Tipos Literais** - Tipos que representam valores específicos.

```typescript
type Direcao = "norte" | "sul" | "leste" | "oeste";
```

## M

### Mapped Types
**Tipos Mapeados** - Tipos criados transformando propriedades de outros tipos.

```typescript
type Opcional<T> = {
  [K in keyof T]?: T[K];
};
```

### Module
**Módulo** - Arquivo que exporta e importa funcionalidades.

```typescript
// math.ts
export function somar(a: number, b: number): number {
  return a + b;
}

// app.ts
import { somar } from './math';
```

## N

### Namespace
**Namespace** - Forma de organizar código em grupos lógicos.

```typescript
namespace Geometria {
  export function calcularArea(raio: number): number {
    return Math.PI * raio * raio;
  }
}
```

### Never
**Never** - Tipo que representa valores que nunca ocorrem.

```typescript
function erro(mensagem: string): never {
  throw new Error(mensagem);
}
```

### Null
**Null** - Tipo que representa ausência intencional de valor.

```typescript
let valor: string | null = null;
```

### Number
**Number** - Tipo primitivo para números.

```typescript
let idade: number = 25;
let preco: number = 99.99;
```

## O

### Object
**Object** - Tipo que representa qualquer valor não primitivo.

```typescript
let obj: object = { nome: "João" };
```

### Optional Properties
**Propriedades Opcionais** - Propriedades que podem ou não estar presentes.

```typescript
interface Config {
  host: string;
  porta?: number;
}
```

### Overload
**Sobrecarga** - Definir múltiplas assinaturas para funções ou métodos.

```typescript
class Calculadora {
  somar(a: number, b: number): number;
  somar(a: string, b: string): string;
  somar(a: any, b: any): any {
    return a + b;
  }
}
```

## P

### Partial
**Partial** - Utility type que torna todas as propriedades opcionais.

```typescript
type UsuarioParcial = Partial<{ nome: string; idade: number }>;
// { nome?: string; idade?: number; }
```

### Pick
**Pick** - Utility type que seleciona propriedades específicas.

```typescript
type NomeIdade = Pick<{ nome: string; idade: number; email: string }, 'nome' | 'idade'>;
```

### Primitive Types
**Tipos Primitivos** - Tipos básicos: string, number, boolean, null, undefined, symbol, bigint.

```typescript
let texto: string = "Hello";
let numero: number = 42;
let ativo: boolean = true;
```

## R

### Readonly
**Readonly** - Modificador que torna propriedades somente leitura.

```typescript
interface Usuario {
  readonly id: string;
  nome: string;
}
```

### Record
**Record** - Utility type para criar objetos com chaves e valores específicos.

```typescript
type Cores = Record<'vermelho' | 'verde' | 'azul', string>;
```

### Required
**Required** - Utility type que torna todas as propriedades obrigatórias.

```typescript
type UsuarioCompleto = Required<{ nome?: string; idade?: number }>;
```

### Rest Parameters
**Parâmetros Rest** - Permite que uma função aceite um número variável de argumentos.

```typescript
function somar(...numeros: number[]): number {
  return numeros.reduce((a, b) => a + b, 0);
}
```

## S

### String
**String** - Tipo primitivo para texto.

```typescript
let nome: string = "João";
let template: string = `Olá, ${nome}!`;
```

### Symbol
**Symbol** - Tipo primitivo para identificadores únicos.

```typescript
let sym: symbol = Symbol("chave");
```

## T

### Template Literal Types
**Tipos de Template Literal** - Tipos baseados em template strings.

```typescript
type Evento = `on${Capitalize<string>}`;
```

### Tuple
**Tupla** - Array com número fixo de elementos de tipos específicos.

```typescript
let coordenada: [number, number] = [10, 20];
```

### Type Alias
**Alias de Tipo** - Nome alternativo para um tipo.

```typescript
type ID = string | number;
type Usuario = {
  id: ID;
  nome: string;
};
```

### Type Assertion
**Asserção de Tipo** - Informar ao TypeScript sobre o tipo de uma variável.

```typescript
let valor: any = "texto";
let tamanho = (valor as string).length;
```

### Type Guard
**Type Guard** - Função que verifica tipos em tempo de execução.

```typescript
function isNumber(valor: any): valor is number {
  return typeof valor === 'number';
}
```

## U

### Undefined
**Undefined** - Tipo que representa valor não definido.

```typescript
let valor: string | undefined;
```

### Union Types
**Tipos de União** - Tipo que pode ser um de vários tipos.

```typescript
type StringOuNumero = string | number;
```

### Unknown
**Unknown** - Tipo seguro para valores desconhecidos.

```typescript
let valor: unknown = obterValor();
if (typeof valor === 'string') {
  console.log(valor.toUpperCase());
}
```

### Utility Types
**Tipos Utilitários** - Tipos pré-definidos para transformações comuns.

```typescript
// Partial, Required, Pick, Omit, Record, etc.
type UsuarioOpcional = Partial<Usuario>;
```

## V

### Void
**Void** - Tipo que representa ausência de retorno.

```typescript
function log(mensagem: string): void {
  console.log(mensagem);
}
```

## W

### Widening
**Alargamento de Tipo** - Processo onde TypeScript expande tipos literais.

```typescript
let x = "hello"; // tipo: string (não "hello")
const y = "hello"; // tipo: "hello"
```

## Termos Avançados

### Covariance
**Covariância** - Relação de tipos onde um subtipo pode ser usado no lugar de um supertipo.

### Contravariance
**Contravariância** - Relação de tipos onde um supertipo pode ser usado no lugar de um subtipo.

### Structural Typing
**Tipagem Estrutural** - Sistema onde tipos são compatíveis baseados em sua estrutura.

### Nominal Typing
**Tipagem Nominal** - Sistema onde tipos são compatíveis baseados em seus nomes.

### Duck Typing
**Duck Typing** - "Se anda como pato e faz quack como pato, então é um pato".

### Type Erasure
**Apagamento de Tipo** - Processo onde informações de tipo são removidas durante compilação.

### Ambient Context
**Contexto Ambiente** - Contexto onde declarações descrevem código existente.

### Declaration Merging
**Fusão de Declarações** - Processo onde múltiplas declarações são combinadas.

```typescript
interface Usuario {
  nome: string;
}

interface Usuario {
  idade: number;
}

// Resultado: interface Usuario { nome: string; idade: number; }
```

### Module Augmentation
**Aumento de Módulo** - Adicionar declarações a módulos existentes.

```typescript
declare module "biblioteca-externa" {
  interface ExistingInterface {
    novaPropriedade: string;
  }
}
```

### Higher-Order Types
**Tipos de Ordem Superior** - Tipos que operam em outros tipos.

```typescript
type MapToString<T> = {
  [K in keyof T]: string;
};
```

### Phantom Types
**Tipos Fantasma** - Tipos que existem apenas em tempo de compilação.

```typescript
type Phantom<T, U> = T & { readonly __phantom: U };
type Metros = Phantom<number, 'metros'>;
type Segundos = Phantom<number, 'segundos'>;
```

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=3178C6&height=120&section=footer"/>