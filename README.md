# Ollama Cloud Usage Report

Skill multi-harness para gerar relatórios HTML completos com todas as variações cloud dos modelos Ollama.

## O que faz

- Pesquisa `https://ollama.com/search?c=cloud` e as páginas `/tags`.
- Inclui todas as variações cloud, inclusive modelos híbridos.
- Extrai custo de **input**, **cached** e **output** por 1M tokens.
- Extrai **Context** e **Size**.
- Usa `Null` quando o modelo não oferece preço `cached`.
- Gera `ollama-cloud-usage-report.html`.

O formato antigo de barras e níveis Low/Medium/High/Max não é mais usado.

## Instalação

### Via secure-skill-installer (recomendado)

```bash
npx skills add github:armel-felipe/skill-ollama-cloud-usage-report
```

### OpenCode / OpenWork

```bash
npx skills add armel-felipe/skill-ollama-cloud-usage-report --skill ollama-cloud-usage-report --agent opencode --global
```

### Claude Code

```bash
npx skills add armel-felipe/skill-ollama-cloud-usage-report --skill ollama-cloud-usage-report --agent claude-code --global
```

## Exemplo de dados

| Modelo | Input / 1M | Cached / 1M | Output / 1M | Context | Size |
|---|---:|---:|---:|---|---|
| `glm-5.3-flash:cloud` | `$0.15` | `$0.03` | `$0.50` | `1M tokens` | `321B parameters` |
| `modelo-sem-cache:cloud` | `$0.10` | `Null` | `$0.40` | `128K tokens` | `N/A` |

`Null` significa que a página da Ollama não oferece preço cached; não significa preço zero.

## Estrutura

```text
skill-ollama-cloud-usage-report/
├── SKILL.md
├── README.md
├── LICENSE
├── agents/skills/ollama-cloud-usage-report/SKILL.md
├── .claude/skills/ollama-cloud-usage-report/SKILL.md
└── opencode/skills/ollama-cloud-usage-report/SKILL.md
```

## Requisitos

- Browser tools para acessar `ollama.com`.
- Capacidade de escrever arquivos no workspace.
- Acesso à internet.

## Segurança

A skill apenas lê páginas públicas da Ollama e gera um HTML estático. Não requer API keys nem executa código externo.

## Licença

MIT. Veja `LICENSE`.
