<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=3178C6&height=120&section=header&animation=twinkling"/>

# 🎨 TypeScript com Frameworks

## React com TypeScript

### Configuração Inicial

```bash
# Criar projeto React com TypeScript
npx create-react-app meu-app --template typescript

# Ou com Vite
npm create vite@latest meu-app -- --template react-ts
```

### Componentes Funcionais

```typescript
import React, { useState, useEffect } from 'react';

// Props interface
interface UsuarioProps {
  nome: string;
  idade: number;
  email?: string;
  onEdit?: (id: string) => void;
}

// Componente funcional
const Usuario: React.FC<UsuarioProps> = ({ nome, idade, email, onEdit }) => {
  const [editando, setEditando] = useState<boolean>(false);
  
  const handleEdit = () => {
    setEditando(!editando);
    onEdit?.("user-123");
  };
  
  return (
    <div className="usuario">
      <h3>{nome}</h3>
      <p>Idade: {idade}</p>
      {email && <p>Email: {email}</p>}
      <button onClick={handleEdit}>
        {editando ? 'Salvar' : 'Editar'}
      </button>
    </div>
  );
};

export default Usuario;
```

### Hooks Tipados

```typescript
import { useState, useEffect, useCallback, useMemo } from 'react';

interface ApiResponse<T> {
  data: T;
  loading: boolean;
  error: string | null;
}

// Custom hook tipado
function useApi<T>(url: string): ApiResponse<T | null> {
  const [data, setData] = useState<T | null>(null);
  const [loading, setLoading] = useState<boolean>(true);
  const [error, setError] = useState<string | null>(null);
  
  useEffect(() => {
    const fetchData = async () => {
      try {
        setLoading(true);
        const response = await fetch(url);
        const result: T = await response.json();
        setData(result);
      } catch (err) {
        setError(err instanceof Error ? err.message : 'Erro desconhecido');
      } finally {
        setLoading(false);
      }
    };
    
    fetchData();
  }, [url]);
  
  return { data, loading, error };
}

// Uso do hook
interface Usuario {
  id: string;
  nome: string;
  email: string;
}

const ListaUsuarios: React.FC = () => {
  const { data: usuarios, loading, error } = useApi<Usuario[]>('/api/usuarios');
  
  const usuariosFiltrados = useMemo(() => {
    return usuarios?.filter(user => user.nome.includes('João')) || [];
  }, [usuarios]);
  
  const handleUserClick = useCallback((userId: string) => {
    console.log('Usuário clicado:', userId);
  }, []);
  
  if (loading) return <div>Carregando...</div>;
  if (error) return <div>Erro: {error}</div>;
  
  return (
    <div>
      {usuariosFiltrados.map(usuario => (
        <Usuario
          key={usuario.id}
          nome={usuario.nome}
          idade={25}
          email={usuario.email}
          onEdit={handleUserClick}
        />
      ))}
    </div>
  );
};
```

### Context API Tipado

```typescript
import React, { createContext, useContext, useReducer, ReactNode } from 'react';

// Estado da aplicação
interface AppState {
  usuario: Usuario | null;
  tema: 'claro' | 'escuro';
  configuracoes: {
    idioma: string;
    notificacoes: boolean;
  };
}

// Ações
type AppAction =
  | { type: 'SET_USUARIO'; payload: Usuario }
  | { type: 'LOGOUT' }
  | { type: 'TOGGLE_TEMA' }
  | { type: 'UPDATE_CONFIGURACOES'; payload: Partial<AppState['configuracoes']> };

// Reducer
const appReducer = (state: AppState, action: AppAction): AppState => {
  switch (action.type) {
    case 'SET_USUARIO':
      return { ...state, usuario: action.payload };
    case 'LOGOUT':
      return { ...state, usuario: null };
    case 'TOGGLE_TEMA':
      return { ...state, tema: state.tema === 'claro' ? 'escuro' : 'claro' };
    case 'UPDATE_CONFIGURACOES':
      return {
        ...state,
        configuracoes: { ...state.configuracoes, ...action.payload }
      };
    default:
      return state;
  }
};

// Context
interface AppContextType {
  state: AppState;
  dispatch: React.Dispatch<AppAction>;
}

const AppContext = createContext<AppContextType | undefined>(undefined);

// Provider
interface AppProviderProps {
  children: ReactNode;
}

export const AppProvider: React.FC<AppProviderProps> = ({ children }) => {
  const [state, dispatch] = useReducer(appReducer, {
    usuario: null,
    tema: 'claro',
    configuracoes: {
      idioma: 'pt-BR',
      notificacoes: true
    }
  });
  
  return (
    <AppContext.Provider value={{ state, dispatch }}>
      {children}
    </AppContext.Provider>
  );
};

// Hook para usar o context
export const useApp = (): AppContextType => {
  const context = useContext(AppContext);
  if (!context) {
    throw new Error('useApp deve ser usado dentro de AppProvider');
  }
  return context;
};
```

## Vue.js com TypeScript

### Configuração com Vue 3

```bash
# Criar projeto Vue com TypeScript
npm create vue@latest meu-projeto
# Selecionar TypeScript: Yes

# Ou com Vite
npm create vite@latest meu-projeto -- --template vue-ts
```

### Composition API

```typescript
<template>
  <div class="usuario-card">
    <h3>{{ usuario.nome }}</h3>
    <p>Email: {{ usuario.email }}</p>
    <p>Posts: {{ posts.length }}</p>
    <button @click="carregarPosts" :disabled="carregando">
      {{ carregando ? 'Carregando...' : 'Carregar Posts' }}
    </button>
    <ul v-if="posts.length > 0">
      <li v-for="post in posts" :key="post.id">
        {{ post.titulo }}
      </li>
    </ul>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted, watch } from 'vue';

// Interfaces
interface Usuario {
  id: string;
  nome: string;
  email: string;
}

interface Post {
  id: string;
  titulo: string;
  conteudo: string;
  autorId: string;
}

// Props
interface Props {
  usuario: Usuario;
}

const props = defineProps<Props>();

// Emits
interface Emits {
  postsCarregados: [posts: Post[]];
  erro: [mensagem: string];
}

const emit = defineEmits<Emits>();

// Estado reativo
const posts = ref<Post[]>([]);
const carregando = ref<boolean>(false);
const erro = ref<string | null>(null);

// Computed
const postsPublicos = computed(() => {
  return posts.value.filter(post => post.conteudo.length > 0);
});

// Métodos
const carregarPosts = async (): Promise<void> => {
  try {
    carregando.value = true;
    erro.value = null;
    
    const response = await fetch(`/api/usuarios/${props.usuario.id}/posts`);
    const data: Post[] = await response.json();
    
    posts.value = data;
    emit('postsCarregados', data);
  } catch (err) {
    const mensagem = err instanceof Error ? err.message : 'Erro desconhecido';
    erro.value = mensagem;
    emit('erro', mensagem);
  } finally {
    carregando.value = false;
  }
};

// Watchers
watch(
  () => props.usuario.id,
  (novoId, idAnterior) => {
    if (novoId !== idAnterior) {
      posts.value = [];
      carregarPosts();
    }
  }
);

// Lifecycle
onMounted(() => {
  carregarPosts();
});
</script>
```

### Composables (Custom Hooks)

```typescript
// composables/useApi.ts
import { ref, Ref } from 'vue';

interface UseApiReturn<T> {
  data: Ref<T | null>;
  loading: Ref<boolean>;
  error: Ref<string | null>;
  execute: () => Promise<void>;
}

export function useApi<T>(url: string): UseApiReturn<T> {
  const data = ref<T | null>(null);
  const loading = ref<boolean>(false);
  const error = ref<string | null>(null);
  
  const execute = async (): Promise<void> => {
    try {
      loading.value = true;
      error.value = null;
      
      const response = await fetch(url);
      if (!response.ok) {
        throw new Error(`HTTP ${response.status}: ${response.statusText}`);
      }
      
      const result: T = await response.json();
      data.value = result;
    } catch (err) {
      error.value = err instanceof Error ? err.message : 'Erro desconhecido';
    } finally {
      loading.value = false;
    }
  };
  
  return { data, loading, error, execute };
}

// Uso do composable
<script setup lang="ts">
import { useApi } from '@/composables/useApi';

interface Usuario {
  id: string;
  nome: string;
  email: string;
}

const { data: usuarios, loading, error, execute } = useApi<Usuario[]>('/api/usuarios');

onMounted(() => {
  execute();
});
</script>
```

## Angular com TypeScript

### Componente Angular

```typescript
// usuario.component.ts
import { Component, Input, Output, EventEmitter, OnInit, OnDestroy } from '@angular/core';
import { Observable, Subject, takeUntil } from 'rxjs';
import { UsuarioService } from './usuario.service';

interface Usuario {
  id: string;
  nome: string;
  email: string;
  ativo: boolean;
}

@Component({
  selector: 'app-usuario',
  template: `
    <div class="usuario-card">
      <h3>{{ usuario.nome }}</h3>
      <p>Email: {{ usuario.email }}</p>
      <p>Status: {{ usuario.ativo ? 'Ativo' : 'Inativo' }}</p>
      <button (click)="toggleStatus()" [disabled]="atualizando">
        {{ atualizando ? 'Atualizando...' : 'Toggle Status' }}
      </button>
    </div>
  `,
  styleUrls: ['./usuario.component.scss']
})
export class UsuarioComponent implements OnInit, OnDestroy {
  @Input() usuario!: Usuario;
  @Output() usuarioAtualizado = new EventEmitter<Usuario>();
  
  atualizando = false;
  private destroy$ = new Subject<void>();
  
  constructor(private usuarioService: UsuarioService) {}
  
  ngOnInit(): void {
    // Configurações iniciais
  }
  
  ngOnDestroy(): void {
    this.destroy$.next();
    this.destroy$.complete();
  }
  
  toggleStatus(): void {
    this.atualizando = true;
    
    const usuarioAtualizado: Usuario = {
      ...this.usuario,
      ativo: !this.usuario.ativo
    };
    
    this.usuarioService.atualizarUsuario(usuarioAtualizado)
      .pipe(takeUntil(this.destroy$))
      .subscribe({
        next: (usuario) => {
          this.usuario = usuario;
          this.usuarioAtualizado.emit(usuario);
          this.atualizando = false;
        },
        error: (erro) => {
          console.error('Erro ao atualizar usuário:', erro);
          this.atualizando = false;
        }
      });
  }
}
```

### Service Angular

```typescript
// usuario.service.ts
import { Injectable } from '@angular/core';
import { HttpClient, HttpErrorResponse } from '@angular/common/http';
import { Observable, throwError } from 'rxjs';
import { catchError, map } from 'rxjs/operators';

interface ApiResponse<T> {
  data: T;
  message: string;
  success: boolean;
}

@Injectable({
  providedIn: 'root'
})
export class UsuarioService {
  private readonly baseUrl = '/api/usuarios';
  
  constructor(private http: HttpClient) {}
  
  obterUsuarios(): Observable<Usuario[]> {
    return this.http.get<ApiResponse<Usuario[]>>(this.baseUrl)
      .pipe(
        map(response => response.data),
        catchError(this.handleError)
      );
  }
  
  obterUsuarioPorId(id: string): Observable<Usuario> {
    return this.http.get<ApiResponse<Usuario>>(`${this.baseUrl}/${id}`)
      .pipe(
        map(response => response.data),
        catchError(this.handleError)
      );
  }
  
  criarUsuario(usuario: Omit<Usuario, 'id'>): Observable<Usuario> {
    return this.http.post<ApiResponse<Usuario>>(this.baseUrl, usuario)
      .pipe(
        map(response => response.data),
        catchError(this.handleError)
      );
  }
  
  atualizarUsuario(usuario: Usuario): Observable<Usuario> {
    return this.http.put<ApiResponse<Usuario>>(`${this.baseUrl}/${usuario.id}`, usuario)
      .pipe(
        map(response => response.data),
        catchError(this.handleError)
      );
  }
  
  deletarUsuario(id: string): Observable<void> {
    return this.http.delete<void>(`${this.baseUrl}/${id}`)
      .pipe(
        catchError(this.handleError)
      );
  }
  
  private handleError(error: HttpErrorResponse): Observable<never> {
    let errorMessage = 'Erro desconhecido';
    
    if (error.error instanceof ErrorEvent) {
      // Erro do lado do cliente
      errorMessage = `Erro: ${error.error.message}`;
    } else {
      // Erro do lado do servidor
      errorMessage = `Código: ${error.status}, Mensagem: ${error.message}`;
    }
    
    console.error(errorMessage);
    return throwError(() => new Error(errorMessage));
  }
}
```

## Node.js com TypeScript

### Express Server

```typescript
// server.ts
import express, { Request, Response, NextFunction } from 'express';
import cors from 'cors';
import helmet from 'helmet';
import { UsuarioController } from './controllers/UsuarioController';
import { errorHandler } from './middleware/errorHandler';

interface CustomRequest extends Request {
  usuario?: {
    id: string;
    nome: string;
    role: 'admin' | 'user';
  };
}

const app = express();
const PORT = process.env.PORT || 3000;

// Middlewares
app.use(helmet());
app.use(cors());
app.use(express.json());

// Middleware de autenticação
const authMiddleware = (req: CustomRequest, res: Response, next: NextFunction): void => {
  const token = req.headers.authorization?.replace('Bearer ', '');
  
  if (!token) {
    res.status(401).json({ error: 'Token não fornecido' });
    return;
  }
  
  try {
    // Validar token (implementação simplificada)
    req.usuario = {
      id: '123',
      nome: 'João',
      role: 'user'
    };
    next();
  } catch (error) {
    res.status(401).json({ error: 'Token inválido' });
  }
};

// Rotas
const usuarioController = new UsuarioController();

app.get('/api/usuarios', authMiddleware, usuarioController.listar);
app.get('/api/usuarios/:id', authMiddleware, usuarioController.obterPorId);
app.post('/api/usuarios', authMiddleware, usuarioController.criar);
app.put('/api/usuarios/:id', authMiddleware, usuarioController.atualizar);
app.delete('/api/usuarios/:id', authMiddleware, usuarioController.deletar);

// Error handling
app.use(errorHandler);

app.listen(PORT, () => {
  console.log(`Servidor rodando na porta ${PORT}`);
});
```

### Controller Tipado

```typescript
// controllers/UsuarioController.ts
import { Request, Response } from 'express';
import { UsuarioService } from '../services/UsuarioService';
import { CreateUsuarioDto, UpdateUsuarioDto } from '../dtos/UsuarioDto';

export class UsuarioController {
  private usuarioService = new UsuarioService();
  
  listar = async (req: Request, res: Response): Promise<void> => {
    try {
      const usuarios = await this.usuarioService.listarTodos();
      res.json({ data: usuarios, success: true });
    } catch (error) {
      res.status(500).json({ 
        error: 'Erro interno do servidor',
        success: false 
      });
    }
  };
  
  obterPorId = async (req: Request<{ id: string }>, res: Response): Promise<void> => {
    try {
      const { id } = req.params;
      const usuario = await this.usuarioService.obterPorId(id);
      
      if (!usuario) {
        res.status(404).json({ 
          error: 'Usuário não encontrado',
          success: false 
        });
        return;
      }
      
      res.json({ data: usuario, success: true });
    } catch (error) {
      res.status(500).json({ 
        error: 'Erro interno do servidor',
        success: false 
      });
    }
  };
  
  criar = async (req: Request<{}, {}, CreateUsuarioDto>, res: Response): Promise<void> => {
    try {
      const novoUsuario = await this.usuarioService.criar(req.body);
      res.status(201).json({ data: novoUsuario, success: true });
    } catch (error) {
      res.status(400).json({ 
        error: 'Dados inválidos',
        success: false 
      });
    }
  };
  
  atualizar = async (
    req: Request<{ id: string }, {}, UpdateUsuarioDto>, 
    res: Response
  ): Promise<void> => {
    try {
      const { id } = req.params;
      const usuarioAtualizado = await this.usuarioService.atualizar(id, req.body);
      
      if (!usuarioAtualizado) {
        res.status(404).json({ 
          error: 'Usuário não encontrado',
          success: false 
        });
        return;
      }
      
      res.json({ data: usuarioAtualizado, success: true });
    } catch (error) {
      res.status(400).json({ 
        error: 'Dados inválidos',
        success: false 
      });
    }
  };
  
  deletar = async (req: Request<{ id: string }>, res: Response): Promise<void> => {
    try {
      const { id } = req.params;
      const deletado = await this.usuarioService.deletar(id);
      
      if (!deletado) {
        res.status(404).json({ 
          error: 'Usuário não encontrado',
          success: false 
        });
        return;
      }
      
      res.status(204).send();
    } catch (error) {
      res.status(500).json({ 
        error: 'Erro interno do servidor',
        success: false 
      });
    }
  };
}
```

## Dicas Gerais

1. **Configure o tsconfig.json** adequadamente para cada framework
2. **Use tipos específicos** do framework (React.FC, Observable, etc.)
3. **Aproveite a inferência de tipos** quando possível
4. **Use interfaces** para props e dados da API
5. **Configure ESLint e Prettier** para consistência
6. **Use type guards** para validação de dados
7. **Aproveite utility types** para transformações de tipos

---

[🔙 Voltar ao índice principal](../README.md)

<img width=100% src="https://capsule-render.vercel.app/api?type=waving&color=3178C6&height=120&section=footer"/>