---
name: ollama-cloud-usage-report
description: Gera um relatório HTML completo com TODAS as variações cloud dos modelos Ollama, extraindo custo de input, cached e output por 1M tokens, além de Context e Size. Inclui variações cloud de modelos híbridos e usa Null quando o modelo não oferece preço cached.
version: 3.0.0
author: OpenWork User
tags: [ollama, cloud, research, report, html, pricing]
agents: [opencode, claude-code, hermes-agent, codex, cursor]
compatibility: Requires browser tools and file writing capabilities. Works in OpenCode, Claude Code, Hermes Agent, and other OpenWork-compatible harnesses.
---

# Ollama Cloud Usage Report

Esta skill pesquisa TODAS as variações cloud disponíveis na Ollama e gera um relatório HTML consolidado. O formato atual da Ollama exibe preços explícitos por 1M tokens; não use mais o antigo sistema de Usage com quatro barras ou níveis Low/Medium/High/Max.

## Fluxo de execução

### 1. Listar modelos iniciais

1. Navegue até `https://ollama.com/search?c=cloud`.
2. Extraia todos os links de modelos base, como `/library/gemma4` e `/library/qwen3.5`.
3. Remova duplicatas e preserve o nome do modelo base.

### 2. Para cada modelo base, extrair todas as variações cloud

Não pare na página principal do modelo: muitos modelos têm várias variações cloud.

Para cada modelo base:

1. Navegue até `https://ollama.com/library/{model-name}/tags` ou clique em `View all` na seção `Models`.
2. Para cada linha da tabela de tags, selecione a variação se:
   - o nome contém `:cloud`; ou
   - a página identifica explicitamente a tag como uma versão cloud.
3. Ignore tags exclusivamente locais, sem indicação cloud.
4. Abra a página de cada variação cloud quando necessário para obter os detalhes completos.
5. Registre uma linha independente para cada variação cloud encontrada, inclusive quando duas variações pertencem ao mesmo modelo base.

### 3. Campos obrigatórios por variação

Extraia os seguintes campos da página da variação:

- **Modelo**: nome completo da tag, por exemplo `glm-5.3-flash:cloud`.
- **Input**: preço monetário exibido em `Cost /1M tokens` sob `input`.
- **Cached**: preço monetário exibido sob `cached`. Se a página não exibir esse campo, ou se não houver preço cached para o modelo, use exatamente `Null` (sem inventar ou estimar um valor).
- **Output**: preço monetário exibido sob `output`.
- **Context**: valor e unidade exibidos na seção `Context`, por exemplo `1M tokens`.
- **Size**: valor e unidade exibidos na seção `Size`, por exemplo `321B parameters`, `64GB` ou `N/A` quando a página não informar o tamanho.

A unidade de preço deve ser sempre registrada como **por 1M tokens**. Preserve o texto monetário exibido pela Ollama, incluindo o símbolo `$` e zeros à esquerda quando houver. Não transforme preços em porcentagens, níveis ou barras.

Exemplo visível na página de `glm-5.3-flash:cloud`:

| Campo | Valor |
|---|---|
| Input | `$0.15` |
| Cached | `$0.03` |
| Output | `$0.50` |
| Context | `1M tokens` |
| Size | `321B parameters` |

### 4. Modelos híbridos conhecidos

Estes modelos podem ter versões cloud e locais. Extraia apenas as variações cloud, mas não se limite a esta lista: sempre confira a página de tags atual.

| Modelo base | Exemplos de variações cloud |
|---|---|
| gemma4 | `gemma4:cloud`, `gemma4:31b-cloud` |
| qwen3.5 | `qwen3.5:cloud`, `qwen3.5:397b-cloud` |
| nemotron-3-super | `nemotron-3-super:cloud` |
| nemotron-3-nano | `nemotron-3-nano:30b-cloud` |
| gpt-oss | `gpt-oss:20b-cloud`, `gpt-oss:120b-cloud` |

### 5. Regras de extração e validação

- Use a estrutura e as formas de navegação da versão anterior (`/search?c=cloud`, páginas `/tags` e páginas individuais).
- Procure o rótulo `Cost /1M tokens` e os rótulos `input`, `cached` e `output` próximos a ele.
- Não conte spans de cor, não procure `bg-neutral-900`, `bg-neutral-800` ou `bg-neutral-200` e não gere níveis Low/Medium/High/Max.
- Se um preço obrigatório (`input` ou `output`) não puder ser lido, marque como `N/A` e registre a página para revisão; não adivinhe.
- Se `cached` estiver ausente, use `Null`, inclusive no HTML e no resumo.
- Verifique que cada linha tenha exatamente os campos Modelo, Input, Cached, Output, Context e Size.
- Elimine duplicatas pelo nome completo da variação.
- Ao final, informe se alguma página não pôde ser lida ou se algum campo obrigatório ficou `N/A`.

## 6. Gerar HTML

Crie um único arquivo `ollama-cloud-usage-report.html` no diretório atual do workspace. O relatório deve ser legível no navegador, responsivo e ter aparência profissional.

Use uma tabela consolidada com estas colunas, nesta ordem:

1. Modelo
2. Input / 1M tokens
3. Cached / 1M tokens
4. Output / 1M tokens
5. Context
6. Size

Exemplo mínimo de linha:

```html
<tr>
  <td><code>glm-5.3-flash:cloud</code></td>
  <td>$0.15</td>
  <td>$0.03</td>
  <td>$0.50</td>
  <td>1M tokens</td>
  <td>321B parameters</td>
</tr>
```

Para um modelo sem cached:

```html
<td>Null</td>
```

Inclua no topo:

- título `Ollama Cloud Usage Report`;
- data da coleta;
- total de variações cloud;
- total de modelos base;
- quantidade de registros com `cached` disponível;
- quantidade de registros com `cached = Null`;
- observação de que os preços são por 1M tokens;
- lista de modelos híbridos identificados e suas variações cloud.

Inclua uma seção de notas metodológicas informando que os dados foram coletados das páginas públicas da Ollama e que `Null` significa que a página não oferece preço cached, não que o preço seja zero.

## Output

A skill gera um arquivo `ollama-cloud-usage-report.html` com:

- ✅ todas as variações cloud encontradas;
- ✅ variações cloud de modelos híbridos;
- ✅ tabela única consolidada;
- ✅ preços atuais de input, cached e output por 1M tokens;
- ✅ `Null` para cached ausente;
- ✅ Context e Size;
- ✅ resumo estatístico e notas metodológicas;
- ✅ estilização profissional para navegador.

## Estrutura de dados recomendada

Antes de renderizar o HTML, organize cada registro neste formato lógico:

```json
{
  "model": "glm-5.3-flash:cloud",
  "input_per_1m_tokens": "$0.15",
  "cached_per_1m_tokens": "$0.03",
  "output_per_1m_tokens": "$0.50",
  "context": "1M tokens",
  "size": "321B parameters"
}
```

Quando cached não estiver disponível:

```json
{
  "cached_per_1m_tokens": "Null"
}
```

Não inclua o antigo campo `usage_level`, a contagem de barras ou classificações Low/Medium/High/Max no relatório novo.
