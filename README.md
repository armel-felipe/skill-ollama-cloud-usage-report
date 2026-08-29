# Ollama Cloud Usage Report

Uma skill multi-harness para gerar relatórios HTML completos com todas as variações cloud dos modelos Ollama.

## 🎯 O que faz

Esta skill pesquisa o catálogo da Ollama e extrai:
- **Todas as variações cloud** de cada modelo
- **Usage Cost** (indicador visual de 4 níveis)
- **Context window** (em tokens)
- Inclui variações cloud de modelos híbridos (que também têm versões para download local)

## 📦 Instalação Rápida

### Usando secure-skill-installer (Recomendado)

Se você tem a skill `secure-skill-installer` instalada, basta pedir:

> "Instale a skill ollama-cloud-usage-report do repositório armel-felipe/ollama-cloud-usage-report globalmente para OpenCode"

A skill vai:
1. ✅ Validar o repositório
2. ✅ Pedir confirmação de escopo (global/local)
3. ✅ Executar `npx skills add` com segurança
4. ✅ Auditar os arquivos instalados
5. ✅ Reportar qualquer risco encontrado

### Instalação Manual

A skill fica disponível em todos os seus projetos.

#### OpenCode / OpenWork
```bash
npx skills add armel-felipe/ollama-cloud-usage-report --skill ollama-cloud-usage-report --agent opencode --global
```

#### Claude Code
```bash
npx skills add armel-felipe/ollama-cloud-usage-report --skill ollama-cloud-usage-report --agent claude-code --global
```

#### Hermes Agent
```bash
npx skills add armel-felipe/ollama-cloud-usage-report --skill ollama-cloud-usage-report --agent hermes-agent --global
```

#### Codex
```bash
npx skills add armel-felipe/ollama-cloud-usage-report --skill ollama-cloud-usage-report --agent codex --global
```

#### Cursor
```bash
npx skills add armel-felipe/ollama-cloud-usage-report --skill ollama-cloud-usage-report --agent cursor --global
```

### Opção 2: Instalação Local (Por Projeto)

A skill fica disponível apenas no projeto atual.

```bash
# Navegue até o projeto
cd /caminho/do/projeto

# Instale para OpenCode
npx skills add armel-felipe/ollama-cloud-usage-report --skill ollama-cloud-usage-report --agent opencode

# Ou para Claude Code
npx skills add armel-felipe/ollama-cloud-usage-report --skill ollama-cloud-usage-report --agent claude-code
```

### Opção 3: Instalação via URL

```bash
npx skills add https://github.com/armel-felipe/ollama-cloud-usage-report --skill ollama-cloud-usage-report --agent opencode --global
```

### Opção 4: Instalação Manual

1. Clone o repositório:
```bash
git clone https://github.com/armel-felipe/ollama-cloud-usage-report.git
```

2. Copie o `SKILL.md` para o diretório de skills do seu harness:

**OpenCode (Global):**
```bash
cp ollama-cloud-usage-report/SKILL.md ~/.config/opencode/skills/ollama-cloud-usage-report/
```

**OpenCode (Local):**
```bash
cp ollama-cloud-usage-report/SKILL.md /seu/projeto/.opencode/skills/ollama-cloud-usage-report/
```

**Claude Code (Global):**
```bash
cp ollama-cloud-usage-report/SKILL.md ~/.claude/skills/ollama-cloud-usage-report/
```

## 🚀 Uso

Depois de instalada, execute a skill no seu workspace:

### Via Comando Direto
```
/ollama-cloud-usage-report
```

### Via Prompt Natural
> "Gere um relatório de usage dos modelos cloud da Ollama"

> "Quero ver todas as variações cloud com suas barrinhas de custo"

> "Pesquise os modelos cloud da Ollama e gere um relatório HTML"

## 📊 Output

A skill gera um arquivo `ollama-cloud-usage-report.html` com:

- ✅ Tabela completa com todas as variações cloud
- ✅ Barras visuais de usage (4 níveis: Low, Medium, High, Extra High)
- ✅ Context window de cada modelo
- ✅ Resumo estatístico
- ✅ Estilização profissional para visualização em browser

## 📋 Exemplo de Saída

| Modelo | Usage | Nível | Context |
|--------|-------|-------|---------|
| glm-5.3:cloud | ███░ | High | 1M tokens |
| gemma4:cloud | █░░░ | Low | 128K tokens |
| gemma4:31b-cloud | █░░░ | Low | 256K tokens |
| kimi-k3:cloud | ████ | Extra High | — |
| qwen3.5:cloud | ██░░ | Medium | 256K tokens |

## 🔍 Modelos Híbridos

A skill identifica corretamente modelos que têm BOTH versões cloud e locais:

| Modelo Base | Variações Cloud Extraídas |
|-------------|---------------------------|
| **gemma4** | `gemma4:cloud`, `gemma4:31b-cloud` |
| **qwen3.5** | `qwen3.5:cloud`, `qwen3.5:397b-cloud` |
| **nemotron-3-super** | `nemotron-3-super:cloud` |
| **nemotron-3-nano** | `nemotron-3-nano:30b-cloud` |
| **gpt-oss** | `gpt-oss:20b-cloud`, `gpt-oss:120b-cloud` |

## 📁 Estrutura do Repositório

```
ollama-cloud-usage-report/
├── SKILL.md                          # Definição da skill (skills.sh padrão)
├── README.md                         # Este arquivo
├── LICENSE                           # MIT License
└── ollama-cloud-usage-report.html    # Exemplo de output (gerado)
```

## 🔧 Harnesses Suportados

| Harness | Instalação Global | Instalação Local |
|---------|------------------|------------------|
| **OpenCode** | ✅ `--agent opencode --global` | ✅ `--agent opencode` |
| **Claude Code** | ✅ `--agent claude-code --global` | ✅ `--agent claude-code` |
| **Hermes Agent** | ✅ `--agent hermes-agent --global` | ✅ `--agent hermes-agent` |
| **Codex** | ✅ `--agent codex --global` | ✅ `--agent codex` |
| **Cursor** | ✅ `--agent cursor --global` | ✅ `--agent cursor` |

## 🛠️ Comandos Úteis

### Listar skills instaladas (Global)
```bash
npx skills list --global --agent opencode
```

### Listar skills instaladas (Local)
```bash
npx skills list --agent opencode
```

### Atualizar skill
```bash
npx skills update ollama-cloud-usage-report --global
```

### Remover skill
```bash
npx skills remove --skill ollama-cloud-usage-report --agent opencode --global
```

## 📝 Requisitos

- **Python 3** (para ferramentas de auditoria)
- **Node.js/npm** (para `npx skills` CLI)
- **Acesso à internet** (para navegar em ollama.com)
- **Browser tools** (suporte a navegação no harness)

## 🔒 Segurança

Esta skill:
- ✅ Não executa código não confiável
- ✅ Não instala dependências externas
- ✅ Não requer credenciais ou API keys
- ✅ Apenas lê dados públicos de ollama.com
- ✅ Gera arquivo HTML estático no workspace

## 📊 Estatísticas Atuais

- **21 variações cloud** de **15 modelos base**
- Distribuição por nível:
  - 4/4 Extra High: 2 variações
  - 3/4 High: 9 variações
  - 2/4 Medium: 7 variações
  - 1/4 Low: 3 variações

## 🤝 Contributing

1. Fork o repositório
2. Crie uma branch para sua feature (`git checkout -b feature/AmazingFeature`)
3. Commit suas mudanças (`git commit -m 'Add some AmazingFeature'`)
4. Push para a branch (`git push origin feature/AmazingFeature`)
5. Abra um Pull Request

## 📄 License

Distribuído sob a licença MIT. Veja `LICENSE` para mais informações.

## 👤 Author

**OpenWork User**

## 🔗 Links

- **Repositório**: https://github.com/armel-felipe/ollama-cloud-usage-report
- **skills.sh**: https://skills.sh
- **OpenCode**: https://openwork.ai
- **Ollama**: https://ollama.com

---

<div align="center">

**Feito com ❤️ para a comunidade OpenWork**

[Reportar Bug](https://github.com/armel-felipe/ollama-cloud-usage-report/issues) · [Sugerir Feature](https://github.com/armel-felipe/ollama-cloud-usage-report/issues)

</div>
