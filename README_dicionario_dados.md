# Dicionário de Dados — Base de Cadeia de Suprimentos

## Visão Geral

| Item | Detalhe |
|---|---|
| **Arquivo principal** | `data/base_cadeia_suprimentos.csv` |
| **Período** | Janeiro/2022 a Dezembro/2024 |
| **Volume estimado** | ~45.000 registros |
| **Granularidade** | Um registro por pedido de compra |
| **Arquivos de referência** | `ref_produtos.csv`, `ref_fornecedores.csv`, `ref_regioes.csv` |

---

## Tabela Principal: `base_cadeia_suprimentos.csv`

### Identificação e Temporalidade

| Coluna | Tipo | Exemplo | Descrição |
|---|---|---|---|
| `id_pedido` | string | PED000001 | Identificador único do pedido (formato PEDxxxxxx) |
| `data_pedido` | date | 2022-03-15 | Data em que o pedido foi emitido |
| `ano` | int | 2022 | Ano do pedido (derivado de data_pedido) |
| `mes` | int | 3 | Mês do pedido, de 1 a 12 |
| `trimestre` | string | T1 | Trimestre do pedido (T1 a T4) |

### Produto

| Coluna | Tipo | Exemplo | Descrição |
|---|---|---|---|
| `id_produto` | string | P001 | Identificador do produto (chave para ref_produtos.csv) |
| `produto` | string | Parafuso Aço Inox M8 | Nome completo do produto |
| `categoria` | string | Fixadores | Categoria do produto. Valores: Fixadores, Ferramentas, EPI, Materiais, Eletroeletrônicos, Manutenção |
| `giro_produto` | string | alto | Velocidade de saída do produto. Valores: alto, medio, baixo |

### Fornecedor

| Coluna | Tipo | Exemplo | Descrição |
|---|---|---|---|
| `id_fornecedor` | string | F001 | Identificador do fornecedor (chave para ref_fornecedores.csv) |
| `fornecedor` | string | Metalprime Ind. e Com. | Nome do fornecedor |
| `pais_origem` | string | Brasil | País de origem do fornecedor |
| `tipo_fornecedor` | string | nacional | Classificação geográfica. Valores: nacional, regional, internacional |
| `confiabilidade_forn` | float | 0.95 | Índice de confiabilidade do fornecedor (0 a 1). Reflete a proporção histórica de entregas no prazo |

### Pedido e Valores

| Coluna | Tipo | Exemplo | Descrição |
|---|---|---|---|
| `quantidade_pedida` | int | 320 | Quantidade de unidades solicitadas no pedido |
| `preco_unitario` | float | 4.68 | Preço unitário negociado (pode variar do preço base por negociação) |
| `valor_total_pedido` | float | 1497.60 | Valor total do pedido = quantidade_pedida × preco_unitario |
| `prazo_pagamento_dias` | int | 30 | Prazo de pagamento acordado com o fornecedor em dias (30, 45 ou 60) |

### Logística e Entrega

| Coluna | Tipo | Exemplo | Descrição |
|---|---|---|---|
| `prazo_entrega_acordado_dias` | int | 5 | Prazo de entrega acordado em dias a partir da data do pedido |
| `data_entrega_prevista` | date | 2022-03-20 | Data de entrega comprometida pelo fornecedor |
| `data_entrega_real` | date | 2022-03-21 | Data em que a entrega efetivamente ocorreu |
| `atraso_dias` | int | 1 | Número de dias de atraso em relação à data prevista (0 = no prazo ou adiantado) |
| `status_entrega` | string | Entregue no Prazo | Situação da entrega. Valores: Entregue no Prazo, Entregue com Atraso, Em Trânsito |
| `regiao_destino` | string | Sudeste | Região brasileira de destino da entrega |
| `estado_destino` | string | São Paulo | Estado de destino da entrega |

### Estoque

| Coluna | Tipo | Exemplo | Descrição |
|---|---|---|---|
| `estoque_disponivel` | int | 420 | Quantidade em estoque no momento em que o pedido foi emitido |
| `estoque_minimo` | int | 500 | Ponto de pedido: nível mínimo aceitável de estoque antes de gerar novo pedido |
| `estoque_maximo` | int | 5000 | Capacidade máxima de armazenamento do produto |
| `ruptura_estoque` | bool | True | Indica se o estoque estava abaixo do mínimo no momento do pedido (True/False) |

### Custos Operacionais

| Coluna | Tipo | Exemplo | Descrição |
|---|---|---|---|
| `custo_frete` | float | 138.50 | Custo de frete do pedido em reais (varia com distância e modal) |
| `custo_armazenagem` | float | 22.40 | Custo de armazenagem estimado para o lote em reais |

---

## Tabelas de Referência

### `ref_produtos.csv`

| Coluna | Descrição |
|---|---|
| `id_produto` | Chave do produto |
| `produto` | Nome do produto |
| `categoria` | Categoria de negócio |
| `preco_unitario_base` | Preço base de referência (sem variação de negociação) |
| `estoque_minimo` | Ponto de reposição |
| `estoque_maximo` | Capacidade máxima |
| `giro` | Classificação de giro: alto, medio, baixo |

### `ref_fornecedores.csv`

| Coluna | Descrição |
|---|---|
| `id_fornecedor` | Chave do fornecedor |
| `fornecedor` | Nome do fornecedor |
| `pais_origem` | País de origem |
| `tipo_fornecedor` | nacional / regional / internacional |
| `prazo_base_dias` | Prazo médio de entrega em dias |
| `confiabilidade` | Proporção de entregas no prazo (0 a 1) |
| `custo_frete_base` | Custo base de frete em reais |

### `ref_regioes.csv`

| Coluna | Descrição |
|---|---|
| `regiao` | Grande região brasileira |
| `estado` | Estado |
| `peso_demanda` | Proporção da demanda total representada por aquela região |

---

## Notas sobre a Geração dos Dados

- **Sazonalidade:** a demanda varia mês a mês e por categoria. EPIs têm pico no período de auditoria CIPA (mar/abr e set/out), eletroeletrônicos têm pico no Natal (nov/dez) e ferramentas têm pico no início de obras (mar/abr).
- **Atrasos:** gerados probabilisticamente com base na confiabilidade de cada fornecedor. Fornecedores internacionais têm maiores variações de prazo.
- **Preços:** variam ±8–12% em torno do preço base para simular negociações e inflação.
- **Estoque:** níveis calculados no momento do pedido, com tendência de queda em períodos de alta demanda.
- **Ruptura:** um pedido tem ruptura quando o estoque disponível no momento está abaixo do estoque mínimo — sinal de falha no planejamento.
