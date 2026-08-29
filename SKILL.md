---
name: ollama-cloud-usage-report
description: Gera relatório HTML completo com TODAS as variações cloud dos modelos Ollama, extraindo Usage Cost (barrinha de 4 níveis) e Context. Inclui variações cloud de modelos híbridos.
version: 2.0.0
author: OpenWork User
tags: [ollama, cloud, research, report, html, usage, cost]
agents: [opencode, claude-code, hermes-agent, codex, cursor]
compatibility: Requires browser tools (browser_navigate, browser_screenshot, browser_eval) and file writing capabilities. Works in OpenCode, Claude Code, Hermes Agent, and other OpenWork-compatible harnesses.
---

# Ollama Cloud Usage Report

Esta skill pesquisa TODAS as variações cloud disponíveis na Ollama e gera um relatório HTML completo.

## Fluxo de Execução (CRÍTICO)

### 1. Listar Modelos Iniciais

1. Navegue até `https://ollama.com/search?c=cloud`
2. Extraia todos os links de modelos base (ex: `/library/gemma4`, `/library/qwen3.5`, etc.)

### 2. Para CADA Modelo Base - Extrair TODAS as Variações Cloud

**IMPORTANTE:** Não pare na página principal do modelo! Muitos modelos têm múltiplas variações cloud.

Para cada modelo base:

1. **Navegue até a página de tags:**
   - URL: `https://ollama.com/library/{model-name}/tags`
   - OU: Na página do modelo, clique em "View all →" na seção "Models"

2. **Para CADA linha na tabela de variações:**
   - Verifique se a variação tem `:cloud` no nome (ex: `gemma4:cloud`, `gemma4:31b-cloud`)
   - OU verifique se tem a barra de usage de 4 níveis (spans com `bg-neutral-900` ou `bg-neutral-800` e `bg-neutral-200`)
   
3. **Para cada variação cloud encontrada, extraia:**
   - **Nome completo**: Nome da variação (ex: `gemma4:31b-cloud`)
   - **Usage**: 
     - Conte spans com classe `bg-neutral-900` ou `bg-neutral-800` (preenchidos)
     - Conte spans com classe `bg-neutral-200` (vazios)
     - Total deve ser 4 barras
   - **Nível**: Texto abaixo das barras (high, medium, low, extra high)
   - **Context**: Valor na coluna "Context" + unidade (tokens)

### 3. Modelos Híbridos Conhecidos

Estes modelos têm BOTH versões cloud E locais - extraia APENAS as variações cloud:

| Modelo Base | Variações Cloud |
|-------------|-----------------|
| gemma4 | gemma4:cloud, gemma4:31b-cloud |
| qwen3.5 | qwen3.5:cloud, qwen3.5:397b-cloud |
| nemotron-3-super | nemotron-3-super:cloud |
| nemotron-3-nano | nemotron-3-nano:30b-cloud |
| gpt-oss | gpt-oss:20b-cloud, gpt-oss:120b-cloud |

### 4. Gerar HTML

Crie um arquivo único `ollama-cloud-usage-report.html` no diretório atual do workspace com:

```html
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <title>Ollama Cloud Usage Report</title>
  <style>
    .bar { display: inline-block; width: 24px; height: 8px; margin: 0 1px; border-radius: 4px; }
    .bar.filled { background: #171717; }
    .bar.empty { background: #e5e5e5; }
    table { border-collapse: collapse; width: 100%; }
    th, td { border: 1px solid #e5e5e5; padding: 12px; text-align: left; }
    th { background: #f5f5f5; font-weight: 600; }
  </style>
</head>
<body>
  <h1>Ollama Cloud Usage Report</h1>
  <table>
    <thead>
      <tr>
        <th>Modelo</th>
        <th>Usage (Barras)</th>
        <th>Nível</th>
        <th>Context</th>
      </tr>
    </thead>
    <tbody>
      <!-- Uma linha por variação cloud -->
    </tbody>
  </table>
</body>
</html>
```

### 5. Formato das Barras de Usage

Para cada variação cloud, gere 4 spans:
- Barras preenchidas: `<span class="bar filled"></span>`
- Barras vazias: `<span class="bar empty"></span>`

Exemplos:
- **1/4 (Low)**: 1 filled + 3 empty
- **2/4 (Medium)**: 2 filled + 2 empty
- **3/4 (High)**: 3 filled + 1 empty
- **4/4 (Extra High)**: 4 filled + 0 empty

### 6. Resumo Estatístico

Inclua no topo do HTML:
- Total de variações cloud
- Total de modelos base
- Distribuição por nível (Extra High, High, Medium, Low)
- Lista de modelos híbridos com suas variações cloud

## Output

A skill gera um arquivo único `ollama-cloud-usage-report.html` no workspace atual com:
- ✅ TODAS as variações cloud (não apenas o modelo base)
- ✅ Variações cloud de modelos híbridos (gemma4, qwen3.5, etc.)
- ✅ Tabela única consolidada
- ✅ Barras de usage visuais (4 níveis)
- ✅ Resumo estatístico completo
- ✅ Estilização profissional para browser

## Regras Críticas

1. **SEMPRE navegue até /tags ou clique em "View all"** - Não confie apenas na página principal do modelo
2. **Uma linha por variação cloud** - Se um modelo tem 2 variações cloud, são 2 linhas na tabela
3. **Ignore variações locais** - Apenas variações com `:cloud` no nome OU com barras de usage
4. **Modelos híbridos têm múltiplas entradas** - gemma4 aparece 2x se tiver 2 variações cloud

## Exemplo de Saída Esperada

Para gemma4, a tabela deve ter 2 linhas:

| Modelo | Usage | Nível | Context |
|--------|-------|-------|---------|
| gemma4:cloud | █░░░ | Low | 128K |
| gemma4:31b-cloud | █░░░ | Low | 256K |
