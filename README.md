# 📄 CV Site - Currículo Online Interativo

Um currículo digital moderno e responsivo, desenvolvido com **Vue 3**, **Vite** e **Tailwind CSS**. Apresenta sua trajetória profissional, habilidades e experiência de forma elegante e interativa.

## 🌟 Características

- **Design responsivo** - Funciona perfeitamente em desktop, tablet e mobile
- **Tema escuro/claro** - Alternância automática conforme preferência do sistema
- **Performance otimizada** - Build rápido com Vite e CSS otimizado com Tailwind
- **Markdown-friendly** - CV estruturado em formato Markdown com suporte a front-matter
- **Sem dependências externas pesadas** - Apenas Vue 3, Tailwind CSS e bibliotecas essenciais
- **Fácil de personalizar** - Toda a estrutura do CV em um arquivo Markdown

## 🚀 Início Rápido

### Pré-requisitos
- Node.js 16+ e npm/yarn

### Instalação

```bash
# Clonar repositório
git clone https://github.com/slowdata/slowdata.github.io.git
cd slowdata.github.io

# Instalar dependências
npm install

# Iniciar servidor de desenvolvimento
npm run dev
```

O projeto será acessível em `http://localhost:5173`

### Build para Produção

```bash
npm run build
```

Os arquivos otimizados serão gerados na pasta `dist/`.

### Preview da Build

```bash
npm run preview
```

## 📋 Estrutura do Projeto

```
src/
├── App.vue              # Componente principal
├── main.js              # Ponto de entrada
├── style.css            # Estilos globais
├── components/          # Componentes reutilizáveis
└── data/
    └── cv.md            # Seu currículo em Markdown
```

## ✏️ Editando seu Currículo

Edite o arquivo `src/data/cv.md` para personalizar seu currículo. O arquivo usa front-matter YAML para metadados:

```markdown
---
name: "Seu Nome"
title: "Seu Título Profissional"
profile: "Uma breve descrição sobre você..."
links:
  github: "https://github.com/seu-usuario"
  linkedin: "https://linkedin.com/in/seu-perfil"
  email: "seu-email@example.com"
skills:
  - "Vue 3"
  - "React"
  - "TypeScript"
experience:
  - company: "Empresa"
    position: "Cargo"
    period: "2023 - Presente"
education:
  - school: "Universidade"
    degree: "Engenharia de Software"
    year: "2023"
training:
  certifications:
    - "Certificação Profissional"
---

## Resumo

Seu conteúdo em Markdown aqui...
```

## 🎨 Personalização

### Cores e Temas
Customize as cores no arquivo `tailwind.config.js` ou ajuste o CSS em `src/style.css`.

### Tipografia
O projeto usa a fonte **Inter** do Google Fonts. Altere em `index.html` para usar outra fonte.

## 🛠️ Stack Tecnológico

- **Vue 3** - Framework reativo
- **Vite** - Bundler moderno e rápido
- **Tailwind CSS** - Framework CSS utilitário
- **Marked** - Parser Markdown
- **Front-matter** - Extração de metadados YAML

## 📦 Dependências

- `vue` - Framework progressivo
- `tailwindcss` - Utilitários CSS
- `marked` - Conversor Markdown → HTML
- `front-matter` - Parser YAML front-matter

## 🌐 Deploy

Este projeto é facilmente deployable em:

- **GitHub Pages** - Configure em `.github/workflows/` para CI/CD automático
- **Vercel** - Push automático no commit
- **Netlify** - Conecte seu repositório
- **Qualquer servidor estático** - Basta servir a pasta `dist/`

## 📄 Licença

Este projeto é de código aberto. Sinta-se à vontade para usá-lo como base para seu próprio currículo.
