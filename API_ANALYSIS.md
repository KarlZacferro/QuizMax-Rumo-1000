# Análise da API ENEM

## Visão Geral
A API ENEM é uma API pública e open-source que fornece acesso a dados de provas e questões do Exame Nacional do Ensino Médio (ENEM) de 2009 a 2023. Contém mais de 2700+ questões.

**URL Base:** `https://api.enem.dev/v1`

---

## Endpoints Disponíveis

### 1. Listar Provas
**Endpoint:** `GET /exams`

**Descrição:** Lista todas as provas disponíveis

**Resposta (200):**
```json
[
  {
    "title": "ENEM 2020",
    "year": 2020,
    "disciplines": [
      {
        "label": "Ciências Humanas e suas Tecnologias",
        "value": "ciencias-humanas"
      },
      {
        "label": "Ciências da Natureza e suas Tecnologias",
        "value": "ciencias-natureza"
      },
      {
        "label": "Linguagens, Códigos e suas Tecnologias",
        "value": "linguagens"
      },
      {
        "label": "Matemática e suas Tecnologias",
        "value": "matematica"
      }
    ],
    "languages": [
      {
        "label": "Espanhol",
        "value": "espanhol"
      },
      {
        "label": "Inglês",
        "value": "ingles"
      }
    ]
  }
]
```

---

### 2. Listar Questões
**Endpoint:** `GET /exams/{year}/questions`

**Parâmetros:**
- `year` (string, obrigatório): O ano da prova (ex: "2020")
- `limit` (integer, padrão: 10): Número máximo de questões a retornar
- `offset` (integer, padrão: 0): Número da primeira questão a retornar
- `language` (string, opcional): Idioma desejado (ex: "ingles", "espanhol")

**Resposta (200):**
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
      "context": "Lorem ipsum dolor sit amet...",
      "files": [
        "https://enem.dev/2020/questions/1-ingles/6e1ca12e-9bc4-472b-8809-84e7e394714a.png"
      ],
      "correctAlternative": "A",
      "alternativesIntroduction": "Com base no texto, selecione a alternativa correta",
      "alternatives": [
        {
          "letter": "A",
          "text": "Lorem ipsum dolor sit amet...",
          "file": "https://enem.dev/2020/questions/1-ingles/6e1ca12e-9bc4-472b-8809-84e7e394714a.png",
          "isCorrect": true
        }
      ]
    }
  ]
}
```

---

### 3. Listar Prova Específica
**Endpoint:** `GET /exams/{year}`

**Descrição:** Retorna detalhes de uma prova específica

---

### 4. Listar Questão Específica
**Endpoint:** `GET /exams/{year}/questions/{index}`

**Descrição:** Retorna detalhes de uma questão específica

---

## Estrutura de Dados

### Prova (Exam)
```typescript
interface Exam {
  title: string;
  year: number;
  disciplines: Discipline[];
  languages: Language[];
}

interface Discipline {
  label: string;
  value: string;
}

interface Language {
  label: string;
  value: string;
}
```

### Questão (Question)
```typescript
interface Question {
  title: string;
  index: number;
  discipline: string;
  language: string;
  year: number;
  context: string;
  files: string[];
  correctAlternative: string;
  alternativesIntroduction: string;
  alternatives: Alternative[];
}

interface Alternative {
  letter: string;
  text: string;
  file?: string;
  isCorrect: boolean;
}
```

### Resposta Paginada
```typescript
interface PaginatedResponse {
  metadata: {
    limit: number;
    offset: number;
    total: number;
    hasMore: boolean;
  };
  questions: Question[];
}
```

---

## Limites e Considerações

- A API é pública e sem autenticação obrigatória
- Respeitar limites de taxa (rate limiting)
- Dados são de domínio público conforme Lei de Direitos Autorais (Lei nº 9.610/1998)
- Licença: GNU GPL-2.0

---

## Fluxo da Aplicação Quiz

1. **Listar Provas:** Buscar todas as provas disponíveis
2. **Selecionar Prova:** Usuário escolhe ano e disciplina
3. **Listar Questões:** Buscar questões da prova selecionada (com paginação)
4. **Exibir Questão:** Mostrar questão com contexto, imagens e alternativas
5. **Responder:** Usuário seleciona uma alternativa
6. **Verificar Resposta:** Comparar resposta com `correctAlternative`
7. **Próxima Questão:** Avançar para a próxima questão ou finalizar
8. **Resultado Final:** Exibir score e análise de desempenho
