<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=3178C6&height=120&section=header&animation=twinkling"/>

# 🌿 Ferramentas e Integrações

## IDEs e Editores

### Visual Studio Code

#### Extensões Essenciais

```json
// .vscode/extensions.json
{
  "recommendations": [
    "ms-vscode.vscode-typescript-next",
    "bradlc.vscode-tailwindcss",
    "esbenp.prettier-vscode",
    "dbaeumer.vscode-eslint",
    "ms-vscode.vscode-json",
    "formulahendry.auto-rename-tag",
    "christian-kohler.path-intellisense",
    "ms-vscode.vscode-typescript-tslint-plugin"
  ]
}
```

#### Configurações do Workspace

```json
// .vscode/settings.json
{
  "typescript.preferences.quoteStyle": "single",
  "typescript.preferences.includePackageJsonAutoImports": "auto",
  "typescript.suggest.autoImports": true,
  "typescript.updateImportsOnFileMove.enabled": "always",
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true,
    "source.organizeImports": true
  },
  "files.associations": {
    "*.tsx": "typescriptreact"
  }
}
```

#### Snippets Personalizados

```json
// .vscode/typescript.json
{
  "Interface": {
    "prefix": "interface",
    "body": [
      "interface ${1:InterfaceName} {",
      "  ${2:property}: ${3:type};",
      "}"
    ],
    "description": "Create TypeScript interface"
  },
  "Type Guard": {
    "prefix": "typeguard",
    "body": [
      "function is${1:Type}(obj: unknown): obj is ${1:Type} {",
      "  return (",
      "    typeof obj === 'object' &&",
      "    obj !== null &&",
      "    '${2:property}' in obj",
      "  );",
      "}"
    ],
    "description": "Create type guard function"
  }
}
```

### WebStorm/IntelliJ IDEA

#### Configurações Recomendadas

```typescript
// Configurações de Code Style
// File → Settings → Editor → Code Style → TypeScript

// Configurações de Inspections
// File → Settings → Editor → Inspections → TypeScript

// Live Templates personalizados
// File → Settings → Editor → Live Templates
```

## Linting e Formatação

### ESLint com TypeScript

#### Instalação

```bash
npm install --save-dev eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin
```

#### Configuração

```javascript
// .eslintrc.js
module.exports = {
  parser: '@typescript-eslint/parser',
  plugins: ['@typescript-eslint'],
  extends: [
    'eslint:recommended',
    '@typescript-eslint/recommended',
    '@typescript-eslint/recommended-requiring-type-checking'
  ],
  parserOptions: {
    ecmaVersion: 2020,
    sourceType: 'module',
    project: './tsconfig.json'
  },
  rules: {
    // TypeScript específicas
    '@typescript-eslint/no-unused-vars': 'error',
    '@typescript-eslint/explicit-function-return-type': 'warn',
    '@typescript-eslint/no-explicit-any': 'error',
    '@typescript-eslint/prefer-const': 'error',
    '@typescript-eslint/no-inferrable-types': 'off',
    
    // Imports
    '@typescript-eslint/consistent-type-imports': 'error',
    
    // Naming conventions
    '@typescript-eslint/naming-convention': [
      'error',
      {
        'selector': 'interface',
        'format': ['PascalCase'],
        'prefix': ['I']
      },
      {
        'selector': 'typeAlias',
        'format': ['PascalCase']
      },
      {
        'selector': 'enum',
        'format': ['PascalCase']
      }
    ]
  },
  ignorePatterns: ['dist/', 'node_modules/']
};
```

### Prettier

#### Configuração

```json
// .prettierrc
{
  "semi": true,
  "trailingComma": "es5",
  "singleQuote": true,
  "printWidth": 80,
  "tabWidth": 2,
  "useTabs": false,
  "bracketSpacing": true,
  "arrowParens": "avoid",
  "endOfLine": "lf"
}
```

```javascript
// .prettierignore
dist/
node_modules/
*.min.js
*.min.css
```

## Build Tools

### Webpack com TypeScript

```javascript
// webpack.config.js
const path = require('path');

module.exports = {
  entry: './src/index.ts',
  module: {
    rules: [
      {
        test: /\.tsx?$/,
        use: 'ts-loader',
        exclude: /node_modules/,
      },
    ],
  },
  resolve: {
    extensions: ['.tsx', '.ts', '.js'],
    alias: {
      '@': path.resolve(__dirname, 'src'),
      '@components': path.resolve(__dirname, 'src/components'),
      '@utils': path.resolve(__dirname, 'src/utils')
    }
  },
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist'),
  },
  devtool: 'source-map'
};
```

### Vite com TypeScript

```typescript
// vite.config.ts
import { defineConfig } from 'vite';
import { resolve } from 'path';

export default defineConfig({
  resolve: {
    alias: {
      '@': resolve(__dirname, 'src'),
      '@components': resolve(__dirname, 'src/components'),
      '@utils': resolve(__dirname, 'src/utils')
    }
  },
  build: {
    target: 'es2020',
    sourcemap: true,
    rollupOptions: {
      output: {
        manualChunks: {
          vendor: ['react', 'react-dom'],
          utils: ['lodash', 'date-fns']
        }
      }
    }
  },
  esbuild: {
    target: 'es2020'
  }
});
```

### Rollup com TypeScript

```javascript
// rollup.config.js
import typescript from '@rollup/plugin-typescript';
import resolve from '@rollup/plugin-node-resolve';
import commonjs from '@rollup/plugin-commonjs';
import { terser } from 'rollup-plugin-terser';

export default {
  input: 'src/index.ts',
  output: [
    {
      file: 'dist/bundle.cjs.js',
      format: 'cjs',
      sourcemap: true
    },
    {
      file: 'dist/bundle.esm.js',
      format: 'esm',
      sourcemap: true
    }
  ],
  plugins: [
    resolve(),
    commonjs(),
    typescript({
      tsconfig: './tsconfig.json',
      declaration: true,
      declarationDir: 'dist/types'
    }),
    terser()
  ],
  external: ['react', 'react-dom']
};
```

## Testing

### Jest com TypeScript

#### Configuração

```javascript
// jest.config.js
module.exports = {
  preset: 'ts-jest',
  testEnvironment: 'node',
  roots: ['<rootDir>/src'],
  testMatch: [
    '**/__tests__/**/*.+(ts|tsx|js)',
    '**/*.(test|spec).+(ts|tsx|js)'
  ],
  transform: {
    '^.+\\.(ts|tsx)$': 'ts-jest'
  },
  collectCoverageFrom: [
    'src/**/*.{ts,tsx}',
    '!src/**/*.d.ts',
    '!src/index.ts'
  ],
  moduleNameMapping: {
    '^@/(.*)$': '<rootDir>/src/$1',
    '^@components/(.*)$': '<rootDir>/src/components/$1'
  },
  setupFilesAfterEnv: ['<rootDir>/src/setupTests.ts']
};
```

#### Exemplo de Teste

```typescript
// src/utils/math.test.ts
import { somar, dividir } from './math';

describe('Funções matemáticas', () => {
  describe('somar', () => {
    it('deve somar dois números positivos', () => {
      expect(somar(2, 3)).toBe(5);
    });
    
    it('deve somar números negativos', () => {
      expect(somar(-2, -3)).toBe(-5);
    });
  });
  
  describe('dividir', () => {
    it('deve dividir números corretamente', () => {
      expect(dividir(10, 2)).toBe(5);
    });
    
    it('deve lançar erro ao dividir por zero', () => {
      expect(() => dividir(10, 0)).toThrow('Divisão por zero');
    });
  });
});
```

### Vitest com TypeScript

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config';
import { resolve } from 'path';

export default defineConfig({
  test: {
    globals: true,
    environment: 'jsdom',
    setupFiles: ['./src/setupTests.ts']
  },
  resolve: {
    alias: {
      '@': resolve(__dirname, 'src')
    }
  }
});
```

## Documentação

### TypeDoc

#### Instalação e Configuração

```bash
npm install --save-dev typedoc
```

```json
// typedoc.json
{
  "entryPoints": ["src/index.ts"],
  "out": "docs",
  "theme": "default",
  "includeVersion": true,
  "excludePrivate": true,
  "excludeProtected": true,
  "excludeExternals": true,
  "readme": "README.md",
  "name": "Minha Biblioteca TypeScript",
  "tsconfig": "tsconfig.json"
}
```

#### Script no package.json

```json
{
  "scripts": {
    "docs": "typedoc",
    "docs:serve": "typedoc --watch --serve"
  }
}
```

### Storybook com TypeScript

```typescript
// .storybook/main.ts
import type { StorybookConfig } from '@storybook/react-vite';

const config: StorybookConfig = {
  stories: ['../src/**/*.stories.@(js|jsx|ts|tsx|mdx)'],
  addons: [
    '@storybook/addon-essentials',
    '@storybook/addon-docs',
    '@storybook/addon-controls'
  ],
  framework: {
    name: '@storybook/react-vite',
    options: {}
  },
  typescript: {
    check: false,
    reactDocgen: 'react-docgen-typescript',
    reactDocgenTypescriptOptions: {
      shouldExtractLiteralValuesFromEnum: true,
      propFilter: (prop) => (prop.parent ? !/node_modules/.test(prop.parent.fileName) : true)
    }
  }
};

export default config;
```

## CI/CD

### GitHub Actions

```yaml
# .github/workflows/ci.yml
name: CI

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    
    strategy:
      matrix:
        node-version: [16.x, 18.x, 20.x]
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Use Node.js ${{ matrix.node-version }}
      uses: actions/setup-node@v3
      with:
        node-version: ${{ matrix.node-version }}
        cache: 'npm'
    
    - name: Install dependencies
      run: npm ci
    
    - name: Type check
      run: npm run type-check
    
    - name: Lint
      run: npm run lint
    
    - name: Test
      run: npm run test:coverage
    
    - name: Build
      run: npm run build
    
    - name: Upload coverage to Codecov
      uses: codecov/codecov-action@v3
      with:
        file: ./coverage/lcov.info
```

### GitLab CI

```yaml
# .gitlab-ci.yml
stages:
  - install
  - test
  - build
  - deploy

variables:
  NODE_VERSION: "18"

install_dependencies:
  stage: install
  image: node:$NODE_VERSION
  script:
    - npm ci
  artifacts:
    paths:
      - node_modules/
    expire_in: 1 hour

type_check:
  stage: test
  image: node:$NODE_VERSION
  script:
    - npm run type-check
  dependencies:
    - install_dependencies

lint:
  stage: test
  image: node:$NODE_VERSION
  script:
    - npm run lint
  dependencies:
    - install_dependencies

test:
  stage: test
  image: node:$NODE_VERSION
  script:
    - npm run test:coverage
  coverage: '/Lines\s*:\s*(\d+\.?\d*)%/'
  artifacts:
    reports:
      coverage_report:
        coverage_format: cobertura
        path: coverage/cobertura-coverage.xml
  dependencies:
    - install_dependencies

build:
  stage: build
  image: node:$NODE_VERSION
  script:
    - npm run build
  artifacts:
    paths:
      - dist/
  dependencies:
    - install_dependencies
```

## Monorepo Tools

### Lerna com TypeScript

```json
// lerna.json
{
  "version": "independent",
  "npmClient": "npm",
  "command": {
    "publish": {
      "conventionalCommits": true,
      "message": "chore(release): publish",
      "registry": "https://registry.npmjs.org/"
    },
    "bootstrap": {
      "ignore": "component-*",
      "npmClientArgs": ["--no-package-lock"]
    }
  },
  "packages": ["packages/*"]
}
```

### Nx com TypeScript

```json
// nx.json
{
  "extends": "nx/presets/npm.json",
  "tasksRunnerOptions": {
    "default": {
      "runner": "nx/tasks-runners/default",
      "options": {
        "cacheableOperations": ["build", "lint", "test", "e2e"]
      }
    }
  },
  "targetDefaults": {
    "build": {
      "dependsOn": ["^build"]
    }
  }
}
```

## Ferramentas de Análise

### SonarQube

```properties
# sonar-project.properties
sonar.projectKey=meu-projeto-typescript
sonar.projectName=Meu Projeto TypeScript
sonar.projectVersion=1.0
sonar.sources=src
sonar.tests=src
sonar.test.inclusions=**/*.test.ts,**/*.spec.ts
sonar.typescript.lcov.reportPaths=coverage/lcov.info
sonar.coverage.exclusions=**/*.test.ts,**/*.spec.ts
```

### Bundle Analyzer

```bash
# Webpack Bundle Analyzer
npm install --save-dev webpack-bundle-analyzer

# Rollup Bundle Analyzer
npm install --save-dev rollup-plugin-analyzer

# Vite Bundle Analyzer
npm install --save-dev rollup-plugin-visualizer
```

## Scripts Úteis

```json
// package.json
{
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "preview": "vite preview",
    "type-check": "tsc --noEmit",
    "type-check:watch": "tsc --noEmit --watch",
    "lint": "eslint src --ext .ts,.tsx",
    "lint:fix": "eslint src --ext .ts,.tsx --fix",
    "format": "prettier --write src/**/*.{ts,tsx}",
    "format:check": "prettier --check src/**/*.{ts,tsx}",
    "test": "vitest",
    "test:coverage": "vitest --coverage",
    "test:ui": "vitest --ui",
    "docs": "typedoc",
    "clean": "rimraf dist",
    "analyze": "npm run build && npx vite-bundle-analyzer dist"
  }
}
```

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=3178C6&height=120&section=footer"/>