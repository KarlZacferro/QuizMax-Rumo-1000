# QuizMax - Rumo ao 1000

Uma aplicação web de quiz para preparação do ENEM, consumindo dados de uma API pública de questões reais.

## 🎯 Características

- **2700+ Questões Reais:** Questões autênticas do ENEM de 2009 a 2023
- **Múltiplas Disciplinas:** Linguagens, Matemática, Ciências da Natureza e Ciências Humanas
- **Interface Minimalista:** Design focado em concentração e aprendizado
- **Feedback Imediato:** Respostas corrigidas instantaneamente
- **Progresso Visual:** Acompanhe seu desempenho em tempo real
- **Responsivo:** Funciona em desktop, tablet e mobile

## 🚀 Quick Start

### Pré-requisitos
- Node.js 18+
- pnpm (ou npm/yarn)

### Instalação

```bash
# Clonar repositório
git clone <repository-url>
cd quizmax-rumo-1000

# Instalar dependências
pnpm install

# Iniciar servidor de desenvolvimento
pnpm dev
```

O aplicativo estará disponível em `http://localhost:3000`

## 📁 Estrutura do Projeto

```
quizmax-rumo-1000/
├── client/
│   ├── public/              # Arquivos estáticos
│   │   └── hero-bg.png      # Imagem de fundo do hero
│   └── src/
│       ├── pages/
│       │   ├── Home.tsx      # Landing page
│       │   ├── Quiz.tsx      # Interface principal de quiz
│       │   └── NotFound.tsx  # Página 404
│       ├── components/       # Componentes reutilizáveis
│       ├── contexts/         # React contexts
│       ├── lib/              # Utilitários
│       ├── App.tsx           # Componente raiz
│       ├── main.tsx          # Entry point
│       └── index.css         # Estilos globais
├── server/                   # Código do servidor (placeholder)
├── shared/                   # Código compartilhado
├── API_ANALYSIS.md           # Análise da API ENEM
├── INTEGRACAO_BACKEND.md     # Guia de integração com backend
└── package.json
```

## 🎨 Design

O projeto segue a filosofia de **Minimalismo Educacional com Foco em Conteúdo**:

- **Paleta de Cores:** Tons neutros (cinza, branco) com azul profundo e verde para feedback
- **Tipografia:** Poppins (títulos) + Inter (corpo)
- **Layout:** Grid assimétrico (questão 70%, sidebar 30%)
- **Animações:** Transições suaves e feedback visual

## 🔌 API Utilizada

### ENEM API Pública
- **URL Base:** `https://api.enem.dev/v1`
- **Documentação:** https://docs.enem.dev
- **Repositório:** https://github.com/yunger7/enem-api

### Endpoints Principais

#### Listar Provas
```
GET /exams
```

#### Listar Questões
```
GET /exams/{year}/questions?limit=50&offset=0
```

Para mais detalhes, veja [API_ANALYSIS.md](./API_ANALYSIS.md)

## 💻 Desenvolvimento

### Scripts Disponíveis

```bash
# Iniciar servidor de desenvolvimento
pnpm dev

# Build para produção
pnpm build

# Preview do build
pnpm preview

# Verificar tipos TypeScript
pnpm check

# Formatar código
pnpm format
```

### Estrutura de Componentes

O projeto utiliza **shadcn/ui** para componentes de UI:

```typescript
import { Button } from '@/components/ui/button';
import { Card } from '@/components/ui/card';
```

### Adicionando Novos Componentes

```bash
# Adicionar novo componente shadcn/ui
pnpm dlx shadcn-ui@latest add [component-name]
```

## 🔗 Integração com Backend

Para integrar com um backend customizado:

1. Leia [INTEGRACAO_BACKEND.md](./INTEGRACAO_BACKEND.md)
2. Configure a URL da API nas variáveis de ambiente
3. Implemente os endpoints necessários

### Variáveis de Ambiente

Crie arquivo `.env.local` na raiz do projeto:

```env
VITE_API_BASE=http://localhost:8000/api
```

## 📱 Responsividade

O aplicativo é totalmente responsivo:

- **Mobile:** Layout em coluna única
- **Tablet:** Layout adaptado
- **Desktop:** Layout completo com sidebar

## ♿ Acessibilidade

- Navegação por teclado completa
- Contraste adequado de cores
- Labels semânticos
- ARIA attributes quando necessário

## 🧪 Testes

```bash
# Executar testes
pnpm test

# Testes com coverage
pnpm test:coverage
```

## 📦 Build e Deploy

### Build para Produção

```bash
pnpm build
```

Isso gera:
- `dist/public/` - Arquivos estáticos
- `dist/index.js` - Servidor Node.js

### Deploy

O projeto pode ser deployado em:
- Vercel
- Netlify
- AWS
- Google Cloud
- Qualquer servidor Node.js

## 🐛 Troubleshooting

### Erro de CORS
Se receber erros de CORS, configure o backend para aceitar requisições do frontend:

```typescript
app.use(cors({
  origin: process.env.FRONTEND_URL,
  credentials: true
}));
```

### Imagens não carregam
Verifique se as URLs das imagens da API ENEM estão acessíveis:

```bash
curl https://enem.dev/2020/questions/1-ingles/...
```

### API lenta
A API ENEM é pública e pode ter limitações de taxa. Implemente cache no backend:

```typescript
const cache = new Map();

app.get('/api/quiz/exams/:year/questions', (req, res) => {
  const cacheKey = `${req.params.year}-${JSON.stringify(req.query)}`;
  
  if (cache.has(cacheKey)) {
    return res.json(cache.get(cacheKey));
  }
  
  // Fetch from ENEM API...
  cache.set(cacheKey, data);
});
```

## 📄 Licença

Este projeto utiliza dados de domínio público conforme Lei de Direitos Autorais (Lei nº 9.610/1998).

A API ENEM é licenciada sob GNU GPL-2.0.

## 🤝 Contribuindo

Contribuições são bem-vindas! Por favor:

1. Fork o projeto
2. Crie uma branch para sua feature (`git checkout -b feature/AmazingFeature`)
3. Commit suas mudanças (`git commit -m 'Add some AmazingFeature'`)
4. Push para a branch (`git push origin feature/AmazingFeature`)
5. Abra um Pull Request

## 📞 Suporte

Para dúvidas ou problemas:

1. Consulte a [documentação da API ENEM](https://docs.enem.dev)
2. Verifique [INTEGRACAO_BACKEND.md](./INTEGRACAO_BACKEND.md)
3. Abra uma issue neste repositório

## 🙏 Agradecimentos

- [ENEM API](https://github.com/yunger7/enem-api) - Dados de questões
- [shadcn/ui](https://ui.shadcn.com/) - Componentes UI
- [Tailwind CSS](https://tailwindcss.com/) - Utilitários CSS
- [React](https://react.dev/) - Framework

## 📊 Status do Projeto

- ✅ Frontend básico completo
- ✅ Integração com API ENEM
- ✅ Interface de quiz funcional
- ⏳ Backend customizado (próximo passo)
- ⏳ Autenticação de usuários
- ⏳ Persistência de progresso
- ⏳ Analytics e relatórios

---

**Desenvolvido com ❤️ para ajudar estudantes a alcançar 1000 no ENEM**

