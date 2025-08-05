<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=3178C6&height=120&section=header&animation=twinkling"/>

# ⚙️ Configuração do Projeto

## tsconfig.json - Configuração Principal

O arquivo `tsconfig.json` é o coração da configuração do TypeScript. Ele define como o compilador deve processar seus arquivos.

### Configuração Básica

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
  "exclude": ["node_modules", "dist", "**/*.test.ts"]
}
```

### Opções Importantes do Compilador

#### Target e Module
```json
{
  "compilerOptions": {
    "target": "ES2020",        // Versão do JavaScript de saída
    "module": "commonjs",     // Sistema de módulos
    "lib": ["ES2020", "DOM"], // Bibliotecas incluídas
    "moduleResolution": "node" // Como resolver módulos
  }
}
```

#### Diretórios
```json
{
  "compilerOptions": {
    "outDir": "./dist",        // Pasta de saída
    "rootDir": "./src",       // Pasta raiz dos fontes
    "baseUrl": "./",          // URL base para imports
    "paths": {                // Mapeamento de caminhos
      "@/*": ["src/*"],
      "@utils/*": ["src/utils/*"]
    }
  }
}
```

#### Verificações Rigorosas
```json
{
  "compilerOptions": {
    "strict": true,                    // Ativa todas as verificações
    "noImplicitAny": true,            // Proíbe any implícito
    "strictNullChecks": true,         // Verificação rigorosa de null
    "strictFunctionTypes": true,      // Verificação rigorosa de funções
    "noImplicitReturns": true,        // Todas as rotas devem retornar
    "noUnusedLocals": true,           // Detecta variáveis não usadas
    "noUnusedParameters": true        // Detecta parâmetros não usados
  }
}
```

## Configurações por Ambiente

### Desenvolvimento
```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "outDir": "./dist",
    "rootDir": "./src",
    "sourceMap": true,        // Gera source maps
    "incremental": true,      // Compilação incremental
    "tsBuildInfoFile": ".tsbuildinfo"
  }
}
```

### Produção
```json
{
  "compilerOptions": {
    "target": "ES2018",
    "module": "commonjs",
    "outDir": "./dist",
    "rootDir": "./src",
    "removeComments": true,   // Remove comentários
    "declaration": true,      // Gera arquivos .d.ts
    "declarationMap": true    // Source maps para declarations
  }
}
```

## Estrutura de Projeto Recomendada

```
meu-projeto/
├── src/
│   ├── index.ts
│   ├── types/
│   │   └── index.ts
│   ├── utils/
│   │   └── helpers.ts
│   ├── services/
│   │   └── api.ts
│   └── components/
│       └── Button.ts
├── tests/
│   └── index.test.ts
├── dist/
├── node_modules/
├── tsconfig.json
├── tsconfig.build.json
├── package.json
└── README.md
```

## Configurações Múltiplas

### tsconfig.build.json (Para Build)
```json
{
  "extends": "./tsconfig.json",
  "compilerOptions": {
    "removeComments": true,
    "declaration": true
  },
  "exclude": [
    "**/*.test.ts",
    "**/*.spec.ts"
  ]
}
```

### tsconfig.test.json (Para Testes)
```json
{
  "extends": "./tsconfig.json",
  "compilerOptions": {
    "types": ["jest", "node"]
  },
  "include": [
    "src/**/*",
    "tests/**/*"
  ]
}
```

## Package.json - Scripts Úteis

```json
{
  "scripts": {
    "build": "tsc -p tsconfig.build.json",
    "dev": "ts-node-dev --respawn --transpile-only src/index.ts",
    "start": "node dist/index.js",
    "test": "jest",
    "type-check": "tsc --noEmit",
    "clean": "rimraf dist"
  },
  "devDependencies": {
    "typescript": "^4.9.0",
    "ts-node": "^10.9.0",
    "ts-node-dev": "^2.0.0",
    "@types/node": "^18.0.0"
  }
}
```

## Configuração com ESLint

### .eslintrc.js
```javascript
module.exports = {
  parser: '@typescript-eslint/parser',
  plugins: ['@typescript-eslint'],
  extends: [
    'eslint:recommended',
    '@typescript-eslint/recommended'
  ],
  parserOptions: {
    ecmaVersion: 2020,
    sourceType: 'module',
    project: './tsconfig.json'
  },
  rules: {
    '@typescript-eslint/no-unused-vars': 'error',
    '@typescript-eslint/explicit-function-return-type': 'warn'
  }
};
```

## Configuração com Prettier

### .prettierrc
```json
{
  "semi": true,
  "trailingComma": "es5",
  "singleQuote": true,
  "printWidth": 80,
  "tabWidth": 2,
  "useTabs": false
}
```

## Configuração para Monorepo

### tsconfig.json (Raiz)
```json
{
  "files": [],
  "references": [
    { "path": "./packages/core" },
    { "path": "./packages/utils" },
    { "path": "./packages/web" }
  ]
}
```

### tsconfig.json (Pacote)
```json
{
  "extends": "../../tsconfig.base.json",
  "compilerOptions": {
    "outDir": "./dist",
    "rootDir": "./src",
    "composite": true
  },
  "references": [
    { "path": "../core" }
  ]
}
```

## Dicas de Configuração

1. **Use `strict: true`** para máxima segurança de tipos
2. **Configure `paths`** para imports mais limpos
3. **Use `incremental: true`** para builds mais rápidos
4. **Separe configurações** por ambiente
5. **Configure source maps** para debugging
6. **Use `composite: true`** em monorepos

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=3178C6&height=120&section=footer"/>