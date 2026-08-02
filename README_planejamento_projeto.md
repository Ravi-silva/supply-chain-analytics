# Planejamento do Projeto — Planejamento da Cadeia de Suprimentos

## Objetivo do Projeto

Analisar e otimizar a cadeia de suprimentos de uma empresa de distribuição industrial, identificando ineficiências operacionais e propondo estratégias baseadas em dados para reduzir rupturas de estoque, melhorar o nível de serviço dos fornecedores e diminuir custos logísticos.

O projeto cobre o ciclo completo de um projeto de Ciência de Dados aplicado a Supply Chain: da exploração dos dados até a construção de modelos preditivos e geração de recomendações acionáveis para o negócio.

---

## Contexto de Negócio

A empresa distribui materiais industriais (ferramentas, EPIs, fixadores, materiais elétricos e de manutenção) para clientes em todo o Brasil. Ela trabalha com fornecedores nacionais e internacionais, com prazos de entrega que variam de 4 a 42 dias.

**Principais dores identificadas:**
- Rupturas de estoque em produtos de alto giro durante períodos de pico
- Atrasos frequentes de fornecedores internacionais que comprometem o abastecimento
- Custo logístico elevado sem visibilidade clara dos principais drivers
- Ausência de previsão de demanda estruturada — compras baseadas em experiência empírica

---

## Estrutura do Projeto (Notebooks)

O projeto é dividido em etapas sequenciais, cada uma entregando valor incremental:

### Etapa 1 — Análise Exploratória de Dados (EDA)
**Objetivo:** Entender a base, identificar padrões e levantar hipóteses de negócio.

Tópicos a cobrir:
- Distribuição dos pedidos por categoria, fornecedor, região e período
- Análise da sazonalidade da demanda por categoria e produto
- Distribuição dos atrasos por tipo de fornecedor e origem
- Análise dos níveis de estoque e frequência de rupturas
- Correlações entre variáveis (ex: atraso × tipo de fornecedor, ruptura × giro)
- Análise dos custos de frete e armazenagem

Perguntas-guia:
- Quais categorias têm maior frequência de ruptura?
- Quais fornecedores entregam fora do prazo com mais frequência?
- Existe sazonalidade clara na demanda?
- Quais estados concentram a maior parte dos pedidos?

---

### Etapa 2 — Limpeza e Preparação dos Dados
**Objetivo:** Preparar a base para modelagem.

Tópicos a cobrir:
- Verificação e tratamento de inconsistências
- Conversão de tipos (datas, booleanos)
- Criação de features derivadas:
  - `cobertura_estoque`: estoque_disponivel / demanda_media_diaria
  - `lead_time_real`: diferença entre data_entrega_real e data_pedido
  - `custo_total_pedido`: valor_total + frete + armazenagem
  - `indice_servico_fornecedor`: proporção de entregas no prazo por fornecedor
- Codificação de variáveis categóricas para modelos

---

### Etapa 3 — Classificação ABC-XYZ
**Objetivo:** Priorizar produtos para estratégias diferenciadas de gestão de estoque.

**Classificação ABC** (por valor de consumo acumulado):
- A: top 20% dos produtos que representam ~80% do valor (Lei de Pareto)
- B: próximos 30% dos produtos (~15% do valor)
- C: restantes 50% dos produtos (~5% do valor)

**Classificação XYZ** (por variabilidade da demanda):
- X: demanda estável (coeficiente de variação < 20%)
- Y: demanda com variação moderada (CV entre 20% e 50%)
- Z: demanda altamente variável (CV > 50%)

**Entregável:** Matriz ABC-XYZ com recomendações de política de reposição para cada quadrante.

---

### Etapa 4 — Análise de Desempenho de Fornecedores
**Objetivo:** Criar um scorecard de fornecedores para suportar decisões de sourcing.

Métricas a calcular:
- **OTIF (On Time In Full):** percentual de pedidos entregues no prazo e na quantidade correta
- **Lead Time médio e desvio padrão** por fornecedor
- **Frequência de ruptura causada** por atrasos de cada fornecedor
- **Custo médio de frete** por fornecedor
- **Análise de risco de concentração:** dependência excessiva de um único fornecedor

Técnicas:
- Ranking e scorecard ponderado
- Clusterização de fornecedores (K-Means) por perfil de desempenho
- Visualização em radar chart

---

### Etapa 5 — Otimização de Estoque
**Objetivo:** Calcular parâmetros ótimos de estoque com base nos dados históricos.

Conceitos aplicados:
- **Ponto de Pedido (PP):** nível de estoque que dispara novo pedido = demanda_media × lead_time + estoque_segurança
- **Estoque de Segurança:** buffer contra variações de demanda e lead time = Z × √(lead_time × σ²_demanda + demanda² × σ²_lead_time)
- **Lote Econômico de Compra (EOQ):** quantidade ótima de pedido que minimiza custos totais de estoque
- **Cobertura de Estoque:** quantos dias o estoque atual aguenta sem reposição

Comparação:
- Parâmetros atuais (implícitos na base) vs. parâmetros calculados
- Estimativa de redução de rupturas com os novos parâmetros

---

### Etapa 6 — Previsão de Demanda
**Objetivo:** Construir modelos de forecasting para apoiar o planejamento de compras.

Modelos a explorar:

| Modelo | Quando usar | Biblioteca |
|---|---|---|
| Média Móvel | Baseline simples | pandas |
| Suavização Exponencial (ETS) | Série com tendência/sazonalidade | statsmodels |
| SARIMA | Série com sazonalidade explícita | statsmodels |
| Prophet | Múltiplas sazonalidades, feriados | prophet |
| XGBoost/LightGBM | Features externas, alta performance | scikit-learn |

Métricas de avaliação:
- MAE (Mean Absolute Error)
- MAPE (Mean Absolute Percentage Error)
- RMSE (Root Mean Squared Error)

Estratégia de validação:
- Walk-forward validation (respeita a ordem temporal dos dados — nunca usar K-Fold aleatório em séries temporais)
- Horizon de previsão: 30, 60 e 90 dias

---

### Etapa 7 — Dashboard e Recomendações Finais
**Objetivo:** Consolidar os insights em visualizações executivas e recomendações acionáveis.

Entregas:
- Dashboard interativo com Plotly (visão de KPIs, estoque, fornecedores, demanda)
- Relatório com as 5 principais recomendações para a operação
- Estimativa de impacto financeiro das melhorias propostas


---

## Técnicas e Ferramentas

### Análise e Manipulação de Dados
- `pandas`, `numpy` — manipulação e cálculos
- `scipy` — estatísticas avançadas

### Visualização
- `matplotlib`, `seaborn` — gráficos estáticos
- `plotly` — gráficos interativos e dashboards

### Machine Learning e Forecasting
- `scikit-learn` — clusterização (KMeans), pré-processamento, métricas
- `statsmodels` — SARIMA, ETS, decomposição de séries temporais
- `prophet` — forecasting com sazonalidades múltiplas
- `xgboost` ou `lightgbm` — modelos de gradient boosting para forecasting

### Otimização
- `scipy.optimize` — para cálculo de EOQ e parâmetros de estoque

---

## Conceitos de Supply Chain que o Aluno Vai Aprender

| Conceito | O que é | Onde aparece no projeto |
|---|---|---|
| Classificação ABC | Priorização por valor de consumo | Etapa 3 |
| Classificação XYZ | Priorização por variabilidade | Etapa 3 |
| OTIF | Métrica de nível de serviço do fornecedor | Etapa 4 |
| Lead Time | Tempo entre pedido e recebimento | Etapas 2, 4 e 5 |
| Ponto de Pedido | Quando emitir novo pedido | Etapa 5 |
| Estoque de Segurança | Buffer contra incertezas | Etapa 5 |
| EOQ | Quantidade ótima de compra | Etapa 5 |
| Ruptura de Estoque | Estoque abaixo do mínimo | Etapas 1, 3 e 5 |
| Cobertura de Estoque | Dias de autonomia do estoque | Etapa 5 |
| Forecasting | Previsão de demanda futura | Etapa 6 |
| Walk-forward Validation | Validação correta de séries temporais | Etapa 6 |

---

## Resultados Esperados ao Final do Projeto

Ao concluir este projeto, o aluno será capaz de:

1. Realizar uma EDA completa em dados de supply chain com interpretação de negócio
2. Classificar produtos usando ABC-XYZ e propor políticas diferenciadas de gestão
3. Avaliar e ranquear fornecedores com base em métricas operacionais reais (OTIF, lead time, custo)
4. Calcular parâmetros ótimos de estoque (ponto de pedido, estoque de segurança, EOQ)
5. Construir e comparar modelos de previsão de demanda com validação temporal correta
6. Apresentar insights de forma visual e comunicar recomendações acionáveis

---

## Referências e Leituras Recomendadas

- **Supply Chain Management: Strategy, Planning, and Operation** — Chopra & Meindl (livro referência da área)
- **Forecasting: Principles and Practice** — Hyndman & Athanasopoulos (gratuito em [otexts.com/fpp3](https://otexts.com/fpp3))
- **Documentação do Prophet** — [facebook.github.io/prophet](https://facebook.github.io/prophet/)
- **Artigo:** "ABC-XYZ Analysis: A Practical Guide" — disponível em blogs de supply chain analytics
