<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=3178C6&height=120&section=header&animation=twinkling"/>

# 🎯 Classes e Herança

## Classes Básicas

### Sintaxe Fundamental

```typescript
class Pessoa {
    // Propriedades
    nome: string;
    idade: number;
    
    // Construtor
    constructor(nome: string, idade: number) {
        this.nome = nome;
        this.idade = idade;
    }
    
    // Métodos
    apresentar(): string {
        return `Olá, eu sou ${this.nome} e tenho ${this.idade} anos.`;
    }
    
    aniversario(): void {
        this.idade++;
        console.log(`Parabéns! Agora tenho ${this.idade} anos.`);
    }
}

// Uso da classe
const pessoa = new Pessoa("João", 25);
console.log(pessoa.apresentar());
pessoa.aniversario();
```

## Modificadores de Acesso

### Public, Private e Protected

```typescript
class ContaBancaria {
    public titular: string;           // Acessível de qualquer lugar
    private saldo: number;           // Apenas dentro da classe
    protected numeroConta: string;   // Classe e subclasses
    
    constructor(titular: string, saldoInicial: number) {
        this.titular = titular;
        this.saldo = saldoInicial;
        this.numeroConta = this.gerarNumeroConta();
    }
    
    // Método público
    public depositar(valor: number): void {
        if (valor > 0) {
            this.saldo += valor;
            console.log(`Depósito de R$ ${valor} realizado.`);
        }
    }
    
    // Método público
    public consultarSaldo(): number {
        return this.saldo;
    }
    
    // Método privado
    private gerarNumeroConta(): string {
        return Math.random().toString(36).substr(2, 9);
    }
    
    // Método protegido
    protected validarOperacao(valor: number): boolean {
        return valor > 0 && valor <= this.saldo;
    }
}
```

### Propriedades Readonly

```typescript
class Usuario {
    readonly id: number;
    readonly criadoEm: Date;
    nome: string;
    
    constructor(id: number, nome: string) {
        this.id = id;
        this.criadoEm = new Date();
        this.nome = nome;
    }
    
    // this.id = 123; // Erro! Propriedade readonly
}
```

## Herança

### Extends e Super

```typescript
class Animal {
    protected nome: string;
    protected idade: number;
    
    constructor(nome: string, idade: number) {
        this.nome = nome;
        this.idade = idade;
    }
    
    mover(): void {
        console.log(`${this.nome} está se movendo.`);
    }
    
    dormir(): void {
        console.log(`${this.nome} está dormindo.`);
    }
}

class Cachorro extends Animal {
    private raca: string;
    
    constructor(nome: string, idade: number, raca: string) {
        super(nome, idade); // Chama o construtor da classe pai
        this.raca = raca;
    }
    
    // Sobrescrevendo método
    mover(): void {
        console.log(`${this.nome} está correndo!`);
    }
    
    // Novo método específico
    latir(): void {
        console.log(`${this.nome}: Au au!`);
    }
    
    // Getter
    get informacoes(): string {
        return `${this.nome} é um ${this.raca} de ${this.idade} anos.`;
    }
}

const dog = new Cachorro("Rex", 3, "Labrador");
dog.mover();        // "Rex está correndo!"
dog.latir();        // "Rex: Au au!"
console.log(dog.informacoes); // Usando getter
```

## Classes Abstratas

```typescript
abstract class Forma {
    protected cor: string;
    
    constructor(cor: string) {
        this.cor = cor;
    }
    
    // Método concreto
    mostrarCor(): void {
        console.log(`Esta forma é ${this.cor}`);
    }
    
    // Método abstrato - deve ser implementado pelas subclasses
    abstract calcularArea(): number;
    abstract desenhar(): void;
}

class Retangulo extends Forma {
    private largura: number;
    private altura: number;
    
    constructor(cor: string, largura: number, altura: number) {
        super(cor);
        this.largura = largura;
        this.altura = altura;
    }
    
    calcularArea(): number {
        return this.largura * this.altura;
    }
    
    desenhar(): void {
        console.log(`Desenhando um retângulo ${this.cor}`);
    }
}

class Circulo extends Forma {
    private raio: number;
    
    constructor(cor: string, raio: number) {
        super(cor);
        this.raio = raio;
    }
    
    calcularArea(): number {
        return Math.PI * this.raio * this.raio;
    }
    
    desenhar(): void {
        console.log(`Desenhando um círculo ${this.cor}`);
    }
}
```

## Getters e Setters

```typescript
class Temperatura {
    private _celsius: number = 0;
    
    // Getter
    get celsius(): number {
        return this._celsius;
    }
    
    // Setter
    set celsius(valor: number) {
        if (valor < -273.15) {
            throw new Error("Temperatura não pode ser menor que zero absoluto");
        }
        this._celsius = valor;
    }
    
    // Getter calculado
    get fahrenheit(): number {
        return (this._celsius * 9/5) + 32;
    }
    
    // Setter calculado
    set fahrenheit(valor: number) {
        this._celsius = (valor - 32) * 5/9;
    }
    
    get kelvin(): number {
        return this._celsius + 273.15;
    }
}

const temp = new Temperatura();
temp.celsius = 25;
console.log(temp.fahrenheit); // 77
temp.fahrenheit = 100;
console.log(temp.celsius);    // 37.77...
```

## Métodos e Propriedades Estáticas

```typescript
class Matematica {
    static readonly PI = 3.14159;
    
    static somar(a: number, b: number): number {
        return a + b;
    }
    
    static calcularCircunferencia(raio: number): number {
        return 2 * this.PI * raio;
    }
}

// Uso sem instanciar a classe
console.log(Matematica.PI);
console.log(Matematica.somar(5, 3));
console.log(Matematica.calcularCircunferencia(10));

class Contador {
    private static instancias = 0;
    private id: number;
    
    constructor() {
        Contador.instancias++;
        this.id = Contador.instancias;
    }
    
    static obterTotalInstancias(): number {
        return Contador.instancias;
    }
    
    obterID(): number {
        return this.id;
    }
}
```

## Implementação de Interfaces

```typescript
interface Voador {
    voar(): void;
    altitude: number;
}

interface Nadador {
    nadar(): void;
    profundidade: number;
}

// Implementando múltiplas interfaces
class Pato implements Voador, Nadador {
    altitude: number = 0;
    profundidade: number = 0;
    
    voar(): void {
        this.altitude = 100;
        console.log("Pato voando!");
    }
    
    nadar(): void {
        this.profundidade = 2;
        console.log("Pato nadando!");
    }
    
    grasnar(): void {
        console.log("Quack!");
    }
}
```

## Generics em Classes

```typescript
class Lista<T> {
    private itens: T[] = [];
    
    adicionar(item: T): void {
        this.itens.push(item);
    }
    
    obter(indice: number): T | undefined {
        return this.itens[indice];
    }
    
    obterTodos(): T[] {
        return [...this.itens];
    }
    
    filtrar(predicado: (item: T) => boolean): T[] {
        return this.itens.filter(predicado);
    }
    
    get tamanho(): number {
        return this.itens.length;
    }
}

// Uso com diferentes tipos
const listaNumeros = new Lista<number>();
listaNumeros.adicionar(1);
listaNumeros.adicionar(2);

const listaStrings = new Lista<string>();
listaStrings.adicionar("hello");
listaStrings.adicionar("world");
```

## Decorators (Experimental)

```typescript
// Habilitar no tsconfig.json: "experimentalDecorators": true

function log(target: any, propertyName: string, descriptor: PropertyDescriptor) {
    const method = descriptor.value;
    
    descriptor.value = function (...args: any[]) {
        console.log(`Chamando ${propertyName} com argumentos:`, args);
        const result = method.apply(this, args);
        console.log(`${propertyName} retornou:`, result);
        return result;
    };
}

class Calculadora {
    @log
    somar(a: number, b: number): number {
        return a + b;
    }
    
    @log
    multiplicar(a: number, b: number): number {
        return a * b;
    }
}
```

## Padrões Comuns

### Singleton

```typescript
class Singleton {
    private static instancia: Singleton;
    
    private constructor() {
        // Construtor privado
    }
    
    static obterInstancia(): Singleton {
        if (!Singleton.instancia) {
            Singleton.instancia = new Singleton();
        }
        return Singleton.instancia;
    }
    
    metodoPublico(): void {
        console.log("Método do Singleton");
    }
}

const singleton1 = Singleton.obterInstancia();
const singleton2 = Singleton.obterInstancia();
console.log(singleton1 === singleton2); // true
```

### Factory

```typescript
abstract class Veiculo {
    abstract acelerar(): void;
}

class Carro extends Veiculo {
    acelerar(): void {
        console.log("Carro acelerando");
    }
}

class Moto extends Veiculo {
    acelerar(): void {
        console.log("Moto acelerando");
    }
}

class FabricaVeiculos {
    static criarVeiculo(tipo: "carro" | "moto"): Veiculo {
        switch (tipo) {
            case "carro":
                return new Carro();
            case "moto":
                return new Moto();
            default:
                throw new Error("Tipo de veículo inválido");
        }
    }
}
```

## Boas Práticas

1. **Use modificadores de acesso** apropriados
2. **Prefira composição** sobre herança quando possível
3. **Use classes abstratas** para comportamentos comuns
4. **Implemente interfaces** para contratos
5. **Use getters/setters** para validação
6. **Evite herança muito profunda**
7. **Use generics** para reutilização

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=3178C6&height=120&section=footer"/>