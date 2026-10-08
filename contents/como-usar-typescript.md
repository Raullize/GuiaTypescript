<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=3178C6&height=120&section=header&animation=twinkling"/>

# Como usar o TypeScript?

## Instalação

### Instalação Global
```bash
npm install -g typescript
```

### Instalação Local (Recomendada)
```bash
npm install --save-dev typescript
```

### Verificar Instalação
```bash
tsc --version
```

## Configuração Inicial

### 1. Inicializar um Projeto TypeScript
```bash
# Criar tsconfig.json
tsc --init

# Ou criar manualmente
npm init -y
npm install --save-dev typescript @types/node
```

### 2. Estrutura de Projeto Básica
```
meu-projeto/
├── src/
│   ├── index.ts
│   └── utils/
├── dist/
├── tsconfig.json
└── package.json
```

## Arquivo de Configuração (tsconfig.json)

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

## Compilação

### Compilação Manual
```bash
# Compilar todos os arquivos
tsc

# Compilar arquivo específico
tsc src/index.ts

# Compilar e observar mudanças
tsc --watch
```

### Scripts no package.json
```json
{
  "scripts": {
    "build": "tsc",
    "dev": "tsc --watch",
    "start": "node dist/index.js"
  }
}
```

## Primeiro Programa

### src/index.ts
```typescript
function saudacao(nome: string): string {
    return `Olá, ${nome}!`;
}

const usuario = "TypeScript";
console.log(saudacao(usuario));
```

### Compilar e Executar
```bash
# Compilar
npm run build

# Executar
npm start
```

## Ferramentas de Desenvolvimento

### ts-node (Execução Direta)
```bash
# Instalar
npm install --save-dev ts-node

# Executar diretamente
npx ts-node src/index.ts
```

### nodemon (Auto-reload)
```bash
# Instalar
npm install --save-dev nodemon

# Script no package.json
"dev": "nodemon --exec ts-node src/index.ts"
```

## IDEs e Editores Recomendados

- **Visual Studio Code**: Suporte nativo ao TypeScript
- **WebStorm**: Excelente suporte e refatoração
- **Sublime Text**: Com plugins TypeScript
- **Vim/Neovim**: Com LSP e plugins

## Extensões Úteis (VS Code)

- TypeScript Importer
- Bracket Pair Colorizer
- Auto Rename Tag
- GitLens
- Prettier
- ESLint

---

[Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=3178C6&height=120&section=footer"/>