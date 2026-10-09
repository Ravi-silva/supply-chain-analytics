# Supply Chain Analytics — Planejamento da Cadeia de Suprimentos

Projeto de Ciência de Dados aplicado a Supply Chain: da análise exploratória até a previsão de demanda e o cálculo de parâmetros de estoque, para uma distribuidora de materiais industriais (ferramentas, EPIs, fixadores, materiais elétricos e de manutenção).

O objetivo é responder a três perguntas de negócio:

1. **Quais fornecedores** comprometem o abastecimento e quais merecem mais volume?
2. **Quais produtos** devem ser prioridade na gestão de estoque?
3. **Quanto comprar e quando**, com base em uma previsão de demanda em vez de experiência empírica?

> **Sobre os dados:** a base é sintética, gerada para fins de estudo. Ela simula pedidos de compra de janeiro/2022 a dezembro/2024, com 20 produtos, 10 fornecedores, 6 categorias e 13 estados de destino. Os números abaixo descrevem essa base, não uma empresa real.

## Principais resultados

| Tema | Achado |
|---|---|
| Fornecedores | Os internacionais concentram os atrasos. O pior caso tem OTIF de 59,4% e frete médio de R$ 548, com apenas 0,7% dos pedidos. |
| Concentração | Três fornecedores somam 60,2% do valor total comprado, o que é um risco mesmo sendo fornecedores confiáveis. |
| Curva ABC | 7 produtos (35% do portfólio) respondem por 76,0% do valor consumido. |
| Curva XYZ | Nenhum produto tem demanda estável: 45% são classe Y e 55% são classe Z. |
| Quadrante crítico | 6 produtos AY concentram R$ 12,6 MM, cerca de 69% do valor total. Foram o foco da previsão e da otimização. |
| Previsão de demanda | Na validação walk-forward, o melhor modelo por produto ficou com MAPE entre 24% e 50%. SARIMA venceu na maioria dos itens. |
| Estoque | Os 6 itens AY têm excesso de estoque, não risco de falta: a cobertura recomendada com nível de serviço de 98% fica entre 23 e 49 dias. |

A leitura do último ponto depende de uma ressalva registrada no notebook 06: o estoque disponível da base aparece acima do estoque máximo cadastrado e precisaria ser validado em um cenário real.

## Etapas do projeto

| Notebook | Etapa | O que entrega |
|---|---|---|
| [01_eda.ipynb](01_eda.ipynb) | Análise exploratória | Distribuição dos pedidos, sazonalidade, atrasos, rupturas e custos logísticos |
| [02_limpeza_preparacao.ipynb](02_limpeza_preparacao.ipynb) | Limpeza e preparação | Tratamento de tipos e criação de features (lead time real, cobertura de estoque, custo total) |
| [03_desempenho_fornecedores.ipynb](03_desempenho_fornecedores.ipynb) | Desempenho de fornecedores | Scorecard com OTIF, lead time e frete, e clusterização dos fornecedores com K-Means |
| [04_classificacao_abc_xyz.ipynb](04_classificacao_abc_xyz.ipynb) | Classificação ABC-XYZ | Matriz ABC-XYZ com política de reposição recomendada por quadrante |
| [05_previsao_demanda.ipynb](05_previsao_demanda.ipynb) | Previsão de demanda | Comparação de Holt-Winters, SARIMA, Gradient Boosting e Prophet nos itens AY, com previsão para 3 meses |
| [06_otimizacao_estoque.ipynb](06_otimizacao_estoque.ipynb) | Otimização de estoque | Estoque de segurança, ponto de pedido e lote econômico (EOQ) para os itens AY |

Os notebooks são sequenciais: cada um usa arquivos gerados pelos anteriores na pasta `data/`.

## Estrutura do repositório

```
├── 01_eda.ipynb … 06_otimizacao_estoque.ipynb   # as seis etapas da análise
├── data/                                         # base original, bases tratadas, resultados e gráficos
├── README_dicionario_dados.md                    # descrição de cada coluna da base
└── README_planejamento_projeto.md                # escopo e planejamento do projeto
```

## Como executar

É necessário ter Python 3 e Jupyter instalados.

```bash
git clone https://github.com/Ravi-silva/supply-chain-analytics.git
cd supply-chain-analytics
pip install pandas numpy scipy matplotlib seaborn scikit-learn statsmodels prophet jupyter
jupyter notebook
```

Depois, execute os notebooks em ordem, do 01 ao 06.

## Tecnologias

- **Manipulação de dados:** pandas, numpy, scipy
- **Visualização:** matplotlib, seaborn
- **Modelagem:** scikit-learn (K-Means, Gradient Boosting), statsmodels (Holt-Winters, SARIMA), prophet

## Documentação complementar

- [Dicionário de dados](README_dicionario_dados.md)
- [Planejamento do projeto](README_planejamento_projeto.md)
