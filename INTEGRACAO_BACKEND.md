# Guia de Integração Backend - QuizMax

## Visão Geral

Este documento descreve como integrar o **QuizMax Frontend** com um **backend customizado** e como entregar a **API ENEM** para o time de backend.

---

## 1. Arquitetura Atual

### Frontend (QuizMax)
- **Tecnologia:** React 19 + TypeScript + Tailwind CSS
- **Localização:** `/client/src/`
- **Servidor de Desenvolvimento:** `http://localhost:3000`
- **Build:** `pnpm build`

### API Utilizada
- **Fonte:** ENEM API Pública (https://api.enem.dev/v1)
- **Documentação:** https://docs.enem.dev
- **Repositório:** https://github.com/yunger7/enem-api

---

## 2. Estrutura do Frontend

### Páginas Principais

#### Home (`client/src/pages/Home.tsx`)
- Landing page com apresentação da aplicação
- Botão para iniciar o quiz
- Seção de features

#### Quiz (`client/src/pages/Quiz.tsx`)
- Interface principal de quiz
- Seleção de ano e disciplina
- Exibição de questões
- Sistema de feedback (acerto/erro)
- Tela de resultados

### Componentes Utilizados
- **shadcn/ui:** Componentes UI reutilizáveis
- **Lucide React:** Ícones
- **Axios:** Requisições HTTP
- **Wouter:** Roteamento

---

## 3. Endpoints da API ENEM

### Listar Provas
```
GET https://api.enem.dev/v1/exams
```

**Resposta:**
```json
[
  {
    "title": "ENEM 2020",
    "year": 2020,
    "disciplines": [
      { "label": "Ciências Humanas e suas Tecnologias", "value": "ciencias-humanas" },
      { "label": "Ciências da Natureza e suas Tecnologias", "value": "ciencias-natureza" },
      { "label": "Linguagens, Códigos e suas Tecnologias", "value": "linguagens" },
      { "label": "Matemática e suas Tecnologias", "value": "matematica" }
    ],
    "languages": [
      { "label": "Espanhol", "value": "espanhol" },
      { "label": "Inglês", "value": "ingles" }
    ]
  }
]
```

### Listar Questões
```
GET https://api.enem.dev/v1/exams/{year}/questions
```

**Parâmetros:**
- `limit` (int, padrão: 10): Número máximo de questões
- `offset` (int, padrão: 0): Número da primeira questão
- `language` (string, opcional): Idioma desejado

**Resposta:**
```json
{
  "metadata": {
    "limit": 10,
    "offset": 0,
    "total": 180,
    "hasMore": true
  },
  "questions": [
    {
      "title": "Questão 1 - ENEM 2020",
      "index": 1,
      "discipline": "linguagens",
      "language": "ingles",
      "year": 2020,
      "context": "Lorem ipsum...",
      "files": ["https://enem.dev/2020/questions/1-ingles/..."],
      "correctAlternative": "A",
      "alternativesIntroduction": "Com base no texto...",
      "alternatives": [
        {
          "letter": "A",
          "text": "Lorem ipsum...",
          "file": null,
          "isCorrect": true
        }
      ]
    }
  ]
}
```

---

## 4. Como Entregar a API para o Backend

### Opção 1: Usar a API Pública Diretamente
A API ENEM é **pública e sem autenticação**. O backend pode fazer requisições diretas:

```bash
curl https://api.enem.dev/v1/exams
curl https://api.enem.dev/v1/exams/2020/questions?limit=50&offset=0
```

### Opção 2: Criar um Backend Wrapper (Recomendado)

Se o backend deseja adicionar funcionalidades customizadas (autenticação, cache, analytics), crie um wrapper:

```typescript
// backend/routes/api/exams.ts
import axios from 'axios';

const ENEM_API = 'https://api.enem.dev/v1';

export async function getExams() {
  const response = await axios.get(`${ENEM_API}/exams`);
  return response.data;
}

export async function getQuestions(year: number, options?: {
  limit?: number;
  offset?: number;
  language?: string;
}) {
  const response = await axios.get(`${ENEM_API}/exams/${year}/questions`, {
    params: options,
  });
  return response.data;
}
```

### Opção 3: Self-hosting da API ENEM

Se deseja total controle, hospede sua própria instância:

1. Clone o repositório: `git clone https://github.com/yunger7/enem-api.git`
2. Siga as instruções em https://docs.enem.dev/self-hosting
3. Configure as variáveis de ambiente
4. Deploy em seu servidor

---

## 5. Integração com Backend Customizado

### Passo 1: Criar Endpoints Backend

Crie endpoints que o frontend possa consumir:

```typescript
// backend/routes/quiz.ts
app.get('/api/quiz/exams', async (req, res) => {
  try {
    const exams = await getExamsFromENEMAPI();
    res.json(exams);
  } catch (error) {
    res.status(500).json({ error: 'Failed to fetch exams' });
  }
});

app.get('/api/quiz/exams/:year/questions', async (req, res) => {
  try {
    const { year } = req.params;
    const { limit = 10, offset = 0, language } = req.query;
    
    const questions = await getQuestionsFromENEMAPI(year, {
      limit: parseInt(limit as string),
      offset: parseInt(offset as string),
      language: language as string,
    });
    
    res.json(questions);
  } catch (error) {
    res.status(500).json({ error: 'Failed to fetch questions' });
  }
});
```

### Passo 2: Atualizar URL da API no Frontend

Modifique o arquivo `client/src/pages/Quiz.tsx`:

```typescript
// Antes:
const API_BASE = 'https://api.enem.dev/v1';

// Depois:
const API_BASE = process.env.VITE_API_BASE || 'https://api.enem.dev/v1';
```

### Passo 3: Configurar Variáveis de Ambiente

Crie arquivo `.env.local` no diretório `client/`:

```env
VITE_API_BASE=http://localhost:8000/api/quiz
```

---

## 6. Funcionalidades Que Podem Ser Adicionadas no Backend

### Autenticação e Usuários
```typescript
// Salvar progresso do usuário
POST /api/users/:userId/progress
{
  "year": 2020,
  "discipline": "linguagens",
  "questionsAnswered": 45,
  "correctAnswers": 38,
  "timestamp": "2024-11-27T10:30:00Z"
}
```

### Análise de Desempenho
```typescript
// Obter estatísticas do usuário
GET /api/users/:userId/stats
{
  "totalQuestionsAnswered": 150,
  "correctAnswers": 120,
  "accuracyRate": 0.8,
  "performanceByDiscipline": {
    "linguagens": 0.85,
    "matematica": 0.75,
    "ciencias-natureza": 0.82,
    "ciencias-humanas": 0.78
  }
}
```

### Simulados Customizados
```typescript
// Criar simulado com questões selecionadas
POST /api/simulations
{
  "name": "Simulado 1",
  "year": 2020,
  "disciplines": ["linguagens", "matematica"],
  "questionCount": 50
}
```

### Ranking e Competição
```typescript
// Obter ranking global
GET /api/rankings?limit=100
{
  "rankings": [
    { "userId": "user1", "score": 950, "rank": 1 },
    { "userId": "user2", "score": 920, "rank": 2 }
  ]
}
```

---

## 7. Estrutura de Pastas Recomendada para Backend

```
backend/
├── src/
│   ├── routes/
│   │   ├── quiz.ts          # Endpoints de quiz
│   │   ├── users.ts         # Endpoints de usuários
│   │   └── progress.ts      # Endpoints de progresso
│   ├── services/
│   │   ├── enemApi.ts       # Wrapper da API ENEM
│   │   ├── userService.ts   # Lógica de usuários
│   │   └── progressService.ts
│   ├── models/
│   │   ├── User.ts
│   │   ├── Progress.ts
│   │   └── Question.ts
│   ├── middleware/
│   │   ├── auth.ts
│   │   └── errorHandler.ts
│   └── index.ts
├── .env.example
├── package.json
└── README.md
```

---

## 8. Variáveis de Ambiente

### Frontend (`.env.local`)
```env
VITE_API_BASE=http://localhost:8000/api
VITE_APP_TITLE=QuizMax
```

### Backend (`.env`)
```env
PORT=8000
NODE_ENV=development
ENEM_API_URL=https://api.enem.dev/v1
DATABASE_URL=postgresql://...
JWT_SECRET=your_secret_key
```

---

## 9. Fluxo de Dados

```
┌─────────────────────────────────────────────────────────────┐
│                    Frontend (React)                         │
│  - Home Page                                                │
│  - Quiz Interface                                           │
│  - Results Page                                             │
└────────────────────────┬────────────────────────────────────┘
                         │
                         │ HTTP Requests
                         │
┌────────────────────────▼────────────────────────────────────┐
│                  Backend (Node.js/Express)                  │
│  - Authentication                                           │
│  - User Management                                          │
│  - Progress Tracking                                        │
│  - Analytics                                                │
└────────────────────────┬────────────────────────────────────┘
                         │
                         │ HTTP Requests
                         │
┌────────────────────────▼────────────────────────────────────┐
│              ENEM API (Pública ou Self-hosted)              │
│  - Exams                                                    │
│  - Questions                                                │
│  - Disciplines                                              │
└─────────────────────────────────────────────────────────────┘
```

---

## 10. Checklist de Integração

- [ ] Backend criado com endpoints de quiz
- [ ] Autenticação implementada
- [ ] Banco de dados configurado
- [ ] Variáveis de ambiente definidas
- [ ] CORS configurado (se necessário)
- [ ] Testes de API realizados
- [ ] Documentação de API criada
- [ ] Deployment planejado
- [ ] Monitoramento configurado
- [ ] Backup de dados configurado

---

## 11. Recursos Adicionais

- **ENEM API GitHub:** https://github.com/yunger7/enem-api
- **ENEM API Docs:** https://docs.enem.dev
- **Frontend Repo:** Este projeto
- **React Documentation:** https://react.dev
- **Express.js Guide:** https://expressjs.com

---

## 12. Suporte e Dúvidas

Para dúvidas sobre a integração:

1. Consulte a documentação da API ENEM: https://docs.enem.dev
2. Verifique o repositório do frontend
3. Abra uma issue no repositório do backend

---

**Última atualização:** 27 de novembro de 2024
