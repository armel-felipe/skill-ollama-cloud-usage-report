---
name: ollama-cloud-usage-report
description: Gera um relatório HTML completo com TODAS as variações cloud dos modelos Ollama, extraindo custo de input, cached e output por 1M tokens, CPMT pelo pior caso, capacidades Available, Context e Size. Inclui variações cloud de modelos híbridos, usa Null quando cached não existe e ordena por CPMT crescente.
version: 5.0.0
author: OpenWork User
tags: [ollama, cloud, research, report, html, pricing, cpmt]
agents: [opencode, claude-code, hermes-agent, codex, cursor]
compatibility: Requires browser tools and file writing capabilities. Works in OpenCode, Claude Code, Hermes Agent, and other OpenWork-compatible harnesses.
---

# Ollama Cloud Usage Report

Pesquise todas as variações cloud disponíveis na Ollama e gere um relatório HTML autocontido, ordenado pelo CPMT (custo total por 1M tokens no pior caso). O formato antigo de barras e níveis Low/Medium/High/Max não deve ser usado. A saída deve seguir o contrato visual Executive dark abaixo em todas as execuções.

## Fluxo de coleta

1. Abra `https://ollama.com/search?c=cloud` e extraia todos os modelos base.
2. Para cada modelo base, abra `https://ollama.com/library/{model-name}/tags` ou `View all`.
3. Registre uma linha para cada tag que contenha `:cloud` ou seja explicitamente identificada como cloud; ignore tags exclusivamente locais.
4. Abra a página individual de cada variação cloud para extrair os campos completos.
5. Elimine duplicatas pelo nome completo da variação.

## Campos por variação

Extraia:

- **Modelo**: nome completo da tag.
- **Input**: preço por 1M tokens; preserve `base / peak` quando ambos forem exibidos.
- **Cached**: preço por 1M tokens; use exatamente `Null` quando ausente.
- **Output**: preço por 1M tokens; preserve `base / peak` quando ambos forem exibidos.
- **CPMT**: soma numérica dos valores de pior caso.
- **Available**: tags do card, mantendo somente `tools`, `vision` e `thinking`; omita `cloud`.
- **Context**: valor e unidade exibidos.
- **Size**: valor e unidade exibidos, ou `N/A` se ausente.

As tags ficam abaixo da descrição do modelo e acima do bloco Cost/Context/Size. Preserve a ordem em que aparecem no card. Se nenhuma capacidade elegível existir, exiba `—`.

## Regra de CPMT

Use o preço peak quando existir. Caso não exista peak, use o preço normalmente publicado. Para cada componente:

```text
select(price) = price.peak quando existir; caso contrário price.normal
numeric(price) = 0 quando o preço for Null; caso contrário select(price)
CPMT = numeric(input) + numeric(cached) + numeric(output)
```

`Null` deve continuar visível na coluna Cached, mas vale zero somente na soma. Exemplos:

```text
glm-5.3 = 1.40 + 0.26 + 4.40 = 6.06
deepseek-v4-flash = 0.44 + 0.014 + 1.32 = 1.774
qwen3.5 = 0.60 + 0 + 3.60 = 4.20
```

Ordene todos os registros por CPMT crescente antes de renderizar o HTML. Se input ou output não puder ser lido, use `N/A`, registre a URL para revisão e não invente o CPMT.

## Estrutura da tabela

Use exatamente esta ordem:

1. Modelo
2. Input / 1M tokens
3. Cached / 1M tokens
4. Output / 1M tokens
5. CPMT / 1M tokens
6. Available
7. Context
8. Size

Renderize cada item de Available como uma pill visual. Não renderize `cloud` nessa coluna.

## Direção visual obrigatória: Executive dark

Crie somente `ollama-cloud-usage-report.html` no workspace. Use uma interface de relatório Executive dark, sóbria e orientada a dados:

- Fundo quase preto e superfícies elevadas em grafite, com bordas sutis e tipografia sans-serif compacta do sistema.
- Roxo suave para CPMT e métricas principais; contraste suficiente para texto, dados base e valores peak.
- Cards de resumo com números grandes e uma `metric rail` de seis métricas: variações cloud, modelos base, menor CPMT, maior CPMT, cached disponível e cached `Null`.
- Pills distintas e legíveis para `tools`, `vision` e `thinking`; ausência de capacidades deve ser `—`.
- Tabela como ranking de custo, com CPMT visualmente dominante, ordenada do menor para o maior valor.
- Rolagem horizontal da tabela em telas estreitas e layout responsivo em desktop/mobile.

## Contrato do layout

O HTML deve conter, nesta ordem, as seções semânticas abaixo:

1. **Header:** logo oficial Ollama à esquerda do título `Ollama Cloud Usage Report`, subtítulo/metadata de coleta e chip `PEAK PRICING`.
2. **Metric rail:** as seis métricas de resumo descritas acima.
3. **Method note:** explicação curta de CPMT worst case e do tratamento de `Null`.
4. **Ranking table:** caption, cabeçalhos com `scope="col"` e as oito colunas Modelo, Input, Cached, Output, CPMT, Available, Context e Size.
5. **Capability legend:** contagens de `tools`, `vision` e `thinking` usando as mesmas pills.
6. **Methodology footer:** origem, data, limitações e páginas não lidas.

O logotipo deve ser o asset oficial `/public/ollama.png` usado como referência e embutido no próprio HTML como imagem inline/base64, com `src="data:image/png;base64,..."`, `alt="Ollama"`, tamanho discreto e alinhamento ao título. Nunca use caminho relativo, URL remota ou placeholder textual no lugar do logo.

## Autocontenção e proibições

O relatório precisa abrir e manter o logo visível sem internet. É proibido incluir:

- qualquer `<link>` ou `<script src="...">`;
- `@import`, CDN, stylesheet externo ou fonte externa;
- imagem remota, URL `http://`/`https://` em `src`, CSS ou conteúdo visual;
- arquivos CSS, JavaScript ou fontes auxiliares para o relatório.

Todo CSS deve ficar em exatamente um bloco `<style>` no HTML. JavaScript inline não é necessário para o relatório estático e não deve ser adicionado sem requisito explícito.

## Validação antes de finalizar

Confirme que:

- a tabela está em ordem não decrescente de CPMT;
- `Null` aparece visualmente onde cached não existe e entra como zero na soma;
- `cloud` não aparece em Available;
- Available contém somente `tools`, `vision`, `thinking` ou `—`;
- cada linha possui CPMT, Available, Context e Size;
- o HTML tem exatamente um bloco `<style>`;
- existe `data:image/png;base64` no `src` do logo e o mesmo elemento tem `alt="Ollama"`;
- não existe `<link>`, `@import`, CDN, fonte externa, imagem remota ou URL `http(s)` de recurso;
- existem header, metric rail, method note, ranking table, capability legend e methodology footer;
- o header contém `Ollama Cloud Usage Report` e `PEAK PRICING`;
- a tabela tem caption e os oito cabeçalhos semânticos na ordem especificada;
- as pills e a legenda usam somente `tools`, `vision` e `thinking`;
- páginas não lidas e campos `N/A` são listados no resumo final.

## Saída

Gere o arquivo `ollama-cloud-usage-report.html` e informe o total de variações, o intervalo de CPMT, as capacidades encontradas e qualquer limitação de coleta.
