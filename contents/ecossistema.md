<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=3178C6&height=120&section=header&animation=twinkling"/>

# 🌐 Ecossistema TypeScript

## Bibliotecas e Frameworks Populares

### Frontend Frameworks

#### React com TypeScript

```bash
# Criar projeto React com TypeScript
npx create-react-app meu-app --template typescript

# Ou com Vite
npm create vite@latest meu-app -- --template react-ts
```

```typescript
// Componente React tipado
import React, { useState, useEffect } from 'react';

interface Usuario {
  id: number;
  nome: string;
  email: string;
}

interface Props {
  usuarios: Usuario[];
  onUsuarioSelecionado: (usuario: Usuario) => void;
}

const ListaUsuarios: React.FC<Props> = ({ usuarios, onUsuarioSelecionado }) => {
  const [filtro, setFiltro] = useState<string>('');
  const [usuariosFiltrados, setUsuariosFiltrados] = useState<Usuario[]>(usuarios);

  useEffect(() => {
    const filtrados = usuarios.filter(usuario =>
      usuario.nome.toLowerCase().includes(filtro.toLowerCase())
    );
    setUsuariosFiltrados(filtrados);
  }, [usuarios, filtro]);

  return (
    <div>
      <input
        type="text"
        placeholder="Filtrar usuários..."
        value={filtro}
        onChange={(e) => setFiltro(e.target.value)}
      />
      <ul>
        {usuariosFiltrados.map(usuario => (
          <li key={usuario.id} onClick={() => onUsuarioSelecionado(usuario)}>
            {usuario.nome} - {usuario.email}
          </li>
        ))}
      </ul>
    </div>
  );
};

export default ListaUsuarios;
```

#### Vue.js com TypeScript

```bash
# Criar projeto Vue com TypeScript
npm create vue@latest meu-projeto
# Selecionar TypeScript: Yes
```

```typescript
// Componente Vue 3 com Composition API
<template>
  <div>
    <h2>{{ titulo }}</h2>
    <input v-model="novoItem" @keyup.enter="adicionarItem" placeholder="Novo item..." />
    <ul>
      <li v-for="item in itens" :key="item.id" @click="toggleItem(item.id)">
        <span :class="{ concluido: item.concluido }">{{ item.texto }}</span>
        <button @click.stop="removerItem(item.id)">Remover</button>
      </li>
    </ul>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';

interface Item {
  id: number;
  texto: string;
  concluido: boolean;
}

interface Props {
  titulo?: string;
}

const props = withDefaults(defineProps<Props>(), {
  titulo: 'Lista de Tarefas'
});

const itens = ref<Item[]>([]);
const novoItem = ref<string>('');
let proximoId = 1;

const itensAtivos = computed(() => 
  itens.value.filter(item => !item.concluido)
);

const adicionarItem = (): void => {
  if (novoItem.value.trim()) {
    itens.value.push({
      id: proximoId++,
      texto: novoItem.value.trim(),
      concluido: false
    });
    novoItem.value = '';
  }
};

const toggleItem = (id: number): void => {
  const item = itens.value.find(item => item.id === id);
  if (item) {
    item.concluido = !item.concluido;
  }
};

const removerItem = (id: number): void => {
  const index = itens.value.findIndex(item => item.id === id);
  if (index > -1) {
    itens.value.splice(index, 1);
  }
};
</script>

<style scoped>
.concluido {
  text-decoration: line-through;
  opacity: 0.6;
}
</style>
```

#### Angular (Nativo TypeScript)

```typescript
// Serviço Angular
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable, BehaviorSubject } from 'rxjs';
import { map, catchError } from 'rxjs/operators';

export interface Produto {
  id: number;
  nome: string;
  preco: number;
  categoria: string;
  disponivel: boolean;
}

@Injectable({
  providedIn: 'root'
})
export class ProdutoService {
  private readonly apiUrl = 'https://api.exemplo.com/produtos';
  private produtosSubject = new BehaviorSubject<Produto[]>([]);
  
  public produtos$ = this.produtosSubject.asObservable();

  constructor(private http: HttpClient) {
    this.carregarProdutos();
  }

  carregarProdutos(): void {
    this.http.get<Produto[]>(this.apiUrl)
      .pipe(
        map(produtos => produtos.filter(p => p.disponivel)),
        catchError(error => {
          console.error('Erro ao carregar produtos:', error);
          return [];
        })
      )
      .subscribe(produtos => this.produtosSubject.next(produtos));
  }

  obterProdutoPorId(id: number): Observable<Produto | undefined> {
    return this.produtos$.pipe(
      map(produtos => produtos.find(p => p.id === id))
    );
  }

  adicionarProduto(produto: Omit<Produto, 'id'>): Observable<Produto> {
    return this.http.post<Produto>(this.apiUrl, produto);
  }
}
```

### Backend Frameworks

#### Express.js com TypeScript

```typescript
// Servidor Express tipado
import express, { Request, Response, NextFunction } from 'express';
import cors from 'cors';
import helmet from 'helmet';

interface Usuario {
  id: string;
  nome: string;
  email: string;
  criadoEm: Date;
}

interface CriarUsuarioRequest {
  nome: string;
  email: string;
}

interface AuthenticatedRequest extends Request {
  usuario?: Usuario;
}

class UsuarioController {
  private usuarios: Usuario[] = [];

  listarUsuarios = (req: Request, res: Response): void => {
    res.json(this.usuarios);
  };

  obterUsuario = (req: Request<{ id: string }>, res: Response): void => {
    const { id } = req.params;
    const usuario = this.usuarios.find(u => u.id === id);
    
    if (!usuario) {
      res.status(404).json({ erro: 'Usuário não encontrado' });
      return;
    }
    
    res.json(usuario);
  };

  criarUsuario = (req: Request<{}, Usuario, CriarUsuarioRequest>, res: Response): void => {
    const { nome, email } = req.body;
    
    const novoUsuario: Usuario = {
      id: Math.random().toString(36),
      nome,
      email,
      criadoEm: new Date()
    };
    
    this.usuarios.push(novoUsuario);
    res.status(201).json(novoUsuario);
  };
}

// Middleware de autenticação
const autenticar = (req: AuthenticatedRequest, res: Response, next: NextFunction): void => {
  const token = req.headers.authorization?.replace('Bearer ', '');
  
  if (!token) {
    res.status(401).json({ erro: 'Token não fornecido' });
    return;
  }
  
  // Simular validação de token
  req.usuario = {
    id: '1',
    nome: 'Usuário Teste',
    email: 'teste@email.com',
    criadoEm: new Date()
  };
  
  next();
};

// Configuração do servidor
const app = express();
const usuarioController = new UsuarioController();

app.use(helmet());
app.use(cors());
app.use(express.json());

// Rotas
app.get('/usuarios', usuarioController.listarUsuarios);
app.get('/usuarios/:id', usuarioController.obterUsuario);
app.post('/usuarios', usuarioController.criarUsuario);

// Rota protegida
app.get('/perfil', autenticar, (req: AuthenticatedRequest, res: Response) => {
  res.json(req.usuario);
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Servidor rodando na porta ${PORT}`);
});
```

#### NestJS (Framework Node.js com TypeScript)

```typescript
// Módulo NestJS
import { Module } from '@nestjs/common';
import { TypeOrmModule } from '@nestjs/typeorm';
import { UsuarioController } from './usuario.controller';
import { UsuarioService } from './usuario.service';
import { Usuario } from './usuario.entity';

@Module({
  imports: [TypeOrmModule.forFeature([Usuario])],
  controllers: [UsuarioController],
  providers: [UsuarioService],
  exports: [UsuarioService]
})
export class UsuarioModule {}

// Entity
import { Entity, PrimaryGeneratedColumn, Column, CreateDateColumn } from 'typeorm';

@Entity('usuarios')
export class Usuario {
  @PrimaryGeneratedColumn('uuid')
  id: string;

  @Column({ length: 100 })
  nome: string;

  @Column({ unique: true })
  email: string;

  @Column({ default: true })
  ativo: boolean;

  @CreateDateColumn()
  criadoEm: Date;
}

// Service
import { Injectable, NotFoundException } from '@nestjs/common';
import { InjectRepository } from '@nestjs/typeorm';
import { Repository } from 'typeorm';
import { Usuario } from './usuario.entity';

export interface CriarUsuarioDto {
  nome: string;
  email: string;
}

@Injectable()
export class UsuarioService {
  constructor(
    @InjectRepository(Usuario)
    private usuarioRepository: Repository<Usuario>
  ) {}

  async criarUsuario(dadosUsuario: CriarUsuarioDto): Promise<Usuario> {
    const usuario = this.usuarioRepository.create(dadosUsuario);
    return await this.usuarioRepository.save(usuario);
  }

  async listarUsuarios(): Promise<Usuario[]> {
    return await this.usuarioRepository.find({ where: { ativo: true } });
  }

  async obterUsuarioPorId(id: string): Promise<Usuario> {
    const usuario = await this.usuarioRepository.findOne({ where: { id } });
    
    if (!usuario) {
      throw new NotFoundException(`Usuário com ID ${id} não encontrado`);
    }
    
    return usuario;
  }
}
```

## Bibliotecas de Utilidades

### Lodash com TypeScript

```typescript
import _ from 'lodash';

interface Produto {
  id: number;
  nome: string;
  preco: number;
  categoria: string;
}

const produtos: Produto[] = [
  { id: 1, nome: 'Notebook', preco: 2500, categoria: 'Eletrônicos' },
  { id: 2, nome: 'Mouse', preco: 50, categoria: 'Eletrônicos' },
  { id: 3, nome: 'Livro', preco: 30, categoria: 'Educação' }
];

// Agrupamento tipado
const produtosPorCategoria: Record<string, Produto[]> = _.groupBy(produtos, 'categoria');

// Ordenação tipada
const produtosOrdenados: Produto[] = _.orderBy(produtos, ['preco'], ['desc']);

// Mapeamento tipado
const nomesProdutos: string[] = _.map(produtos, 'nome');

// Filtro tipado
const produtosCaros: Produto[] = _.filter(produtos, produto => produto.preco > 100);
```

### Date-fns com TypeScript

```typescript
import { format, addDays, differenceInDays, isAfter, parseISO } from 'date-fns';
import { ptBR } from 'date-fns/locale';

interface Evento {
  id: string;
  titulo: string;
  dataInicio: Date;
  dataFim: Date;
}

class GerenciadorEventos {
  private eventos: Evento[] = [];

  adicionarEvento(titulo: string, dataInicio: Date, duracaoDias: number): Evento {
    const evento: Evento = {
      id: Math.random().toString(36),
      titulo,
      dataInicio,
      dataFim: addDays(dataInicio, duracaoDias)
    };
    
    this.eventos.push(evento);
    return evento;
  }

  formatarEvento(evento: Evento): string {
    const inicio = format(evento.dataInicio, 'dd/MM/yyyy', { locale: ptBR });
    const fim = format(evento.dataFim, 'dd/MM/yyyy', { locale: ptBR });
    const duracao = differenceInDays(evento.dataFim, evento.dataInicio);
    
    return `${evento.titulo}: ${inicio} - ${fim} (${duracao} dias)`;
  }

  obterEventosFuturos(): Evento[] {
    const hoje = new Date();
    return this.eventos.filter(evento => isAfter(evento.dataInicio, hoje));
  }

  importarEventoDeJSON(json: string): Evento {
    const dados = JSON.parse(json);
    return {
      ...dados,
      dataInicio: parseISO(dados.dataInicio),
      dataFim: parseISO(dados.dataFim)
    };
  }
}
```

### Zod para Validação

```typescript
import { z } from 'zod';

// Schema de validação
const UsuarioSchema = z.object({
  nome: z.string().min(2, 'Nome deve ter pelo menos 2 caracteres'),
  email: z.string().email('Email inválido'),
  idade: z.number().int().min(18, 'Deve ser maior de idade'),
  telefone: z.string().optional(),
  endereco: z.object({
    rua: z.string(),
    cidade: z.string(),
    cep: z.string().regex(/^\d{5}-?\d{3}$/, 'CEP inválido')
  })
});

// Inferir tipo do schema
type Usuario = z.infer<typeof UsuarioSchema>;

// Função de validação
function validarUsuario(dados: unknown): Usuario {
  try {
    return UsuarioSchema.parse(dados);
  } catch (error) {
    if (error instanceof z.ZodError) {
      const mensagens = error.errors.map(err => `${err.path.join('.')}: ${err.message}`);
      throw new Error(`Dados inválidos: ${mensagens.join(', ')}`);
    }
    throw error;
  }
}

// Schema para API
const CriarUsuarioSchema = UsuarioSchema.omit({ id: true });
const AtualizarUsuarioSchema = UsuarioSchema.partial();

type CriarUsuarioRequest = z.infer<typeof CriarUsuarioSchema>;
type AtualizarUsuarioRequest = z.infer<typeof AtualizarUsuarioSchema>;
```

## Ferramentas de Desenvolvimento

### ESLint com TypeScript

```json
// .eslintrc.json
{
  "extends": [
    "@typescript-eslint/recommended",
    "@typescript-eslint/recommended-requiring-type-checking"
  ],
  "parser": "@typescript-eslint/parser",
  "parserOptions": {
    "project": "./tsconfig.json"
  },
  "plugins": ["@typescript-eslint"],
  "rules": {
    "@typescript-eslint/no-unused-vars": "error",
    "@typescript-eslint/no-explicit-any": "warn",
    "@typescript-eslint/explicit-function-return-type": "warn",
    "@typescript-eslint/no-non-null-assertion": "error",
    "@typescript-eslint/prefer-nullish-coalescing": "error",
    "@typescript-eslint/prefer-optional-chain": "error"
  }
}
```

### Prettier com TypeScript

```json
// .prettierrc
{
  "semi": true,
  "trailingComma": "es5",
  "singleQuote": true,
  "printWidth": 100,
  "tabWidth": 2,
  "useTabs": false,
  "bracketSpacing": true,
  "arrowParens": "avoid"
}
```

### Husky para Git Hooks

```json
// package.json
{
  "scripts": {
    "prepare": "husky install",
    "lint": "eslint src/**/*.{ts,tsx}",
    "lint:fix": "eslint src/**/*.{ts,tsx} --fix",
    "type-check": "tsc --noEmit"
  },
  "lint-staged": {
    "*.{ts,tsx}": [
      "eslint --fix",
      "prettier --write"
    ]
  }
}
```

```bash
# .husky/pre-commit
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

npx lint-staged
npm run type-check
```

## Bibliotecas de Teste

### Jest com TypeScript

```typescript
// usuario.test.ts
import { Usuario } from './usuario';
import { UsuarioService } from './usuario.service';

describe('UsuarioService', () => {
  let service: UsuarioService;
  
  beforeEach(() => {
    service = new UsuarioService();
  });

  describe('criarUsuario', () => {
    it('deve criar um usuário válido', () => {
      const dadosUsuario = {
        nome: 'João Silva',
        email: 'joao@email.com',
        idade: 30
      };

      const usuario = service.criarUsuario(dadosUsuario);

      expect(usuario).toMatchObject(dadosUsuario);
      expect(usuario.id).toBeDefined();
      expect(usuario.criadoEm).toBeInstanceOf(Date);
    });

    it('deve lançar erro para email inválido', () => {
      const dadosInvalidos = {
        nome: 'João',
        email: 'email-invalido',
        idade: 30
      };

      expect(() => service.criarUsuario(dadosInvalidos))
        .toThrow('Email inválido');
    });
  });

  describe('obterUsuarioPorId', () => {
    it('deve retornar usuário existente', () => {
      const usuario = service.criarUsuario({
        nome: 'Maria',
        email: 'maria@email.com',
        idade: 25
      });

      const usuarioEncontrado = service.obterUsuarioPorId(usuario.id);

      expect(usuarioEncontrado).toEqual(usuario);
    });

    it('deve retornar undefined para ID inexistente', () => {
      const resultado = service.obterUsuarioPorId('id-inexistente');
      expect(resultado).toBeUndefined();
    });
  });
});

// Mock tipado
interface MockApiClient {
  get: jest.MockedFunction<(url: string) => Promise<any>>;
  post: jest.MockedFunction<(url: string, data: any) => Promise<any>>;
}

const mockApiClient: MockApiClient = {
  get: jest.fn(),
  post: jest.fn()
};

// Teste com mock
it('deve buscar usuários da API', async () => {
  const usuariosMock = [{ id: 1, nome: 'Teste' }];
  mockApiClient.get.mockResolvedValue({ data: usuariosMock });

  const service = new UsuarioService(mockApiClient as any);
  const usuarios = await service.listarUsuarios();

  expect(mockApiClient.get).toHaveBeenCalledWith('/usuarios');
  expect(usuarios).toEqual(usuariosMock);
});
```

## Monorepos com TypeScript

### Lerna Configuration

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

### Workspace TypeScript Config

```json
// tsconfig.json (root)
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "declaration": true,
    "outDir": "dist",
    "rootDir": "src"
  },
  "references": [
    { "path": "./packages/core" },
    { "path": "./packages/ui" },
    { "path": "./packages/utils" }
  ]
}

// packages/core/tsconfig.json
{
  "extends": "../../tsconfig.json",
  "compilerOptions": {
    "outDir": "dist",
    "rootDir": "src",
    "composite": true
  },
  "include": ["src/**/*"],
  "references": [
    { "path": "../utils" }
  ]
}
```

## Recursos da Comunidade

### Repositórios Úteis

- **DefinitelyTyped** - Tipos para bibliotecas JavaScript
- **TypeScript Handbook** - Documentação oficial
- **Awesome TypeScript** - Lista curada de recursos
- **TypeScript Deep Dive** - Guia aprofundado

### Ferramentas Online

- **TypeScript Playground** - Editor online para testar código
- **JSON to TypeScript** - Gerar interfaces a partir de JSON
- **TypeScript AST Viewer** - Visualizar AST do código
- **TS Config** - Gerador de configuração tsconfig.json

### Comunidades

- **TypeScript Discord** - Chat da comunidade
- **r/typescript** - Subreddit oficial
- **Stack Overflow** - Tag typescript
- **GitHub Discussions** - Discussões no repositório oficial

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=3178C6&height=120&section=footer"/>