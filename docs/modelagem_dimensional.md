# 4.3 Modelagem Dimensional — Star Schema

## Granularidade

A tabela fato foi modelada no nível do município: cada linha representa um município brasileiro com seus indicadores socioeconômicos. O dataset Brazilian Cities já vem estruturado nessa granularidade — uma linha por cidade.

**Justificativa:** o município é a unidade de análise do problema de negócio. Agregar para estado ou região pode ser feito via dimensões, mas a granularidade mínima precisa ser o município para permitir análises de equidade individual.

Após a etapa de limpeza (detalhada na seção 4.4), a tabela fato final contém **3.485 municípios**, de um total de 5.573 municípios disponíveis na fonte original. A redução decorre da remoção de linhas com valores nulos ou inválidos nos campos obrigatórios para os KPIs (IDHM, MUN_EXPENDIT, TAXES, GDP, GVA_PUBLIC, GVA_TOTAL) e da remoção de outliers identificados na fonte (ex.: proporções de gasto público superiores a 100%).

## Tabela Fato — Fato_Municipio

**Origem:** BRAZIL_CITIES.csv (arquivo único do dataset).

- **SK_Estado (FK)** — chave para Dim_Estado
- **SK_Porte (FK)** — chave para Dim_Porte_Populacional
- **SK_IDH (FK)** — chave para Dim_Faixa_IDH
- **SK_Calendario (FK)** — chave para Dim_Calendario, valor fixo (1) em todas as linhas
- **Municipio** — nome do município (origem: `CITY`)
- **Estado** — sigla da UF (origem: `STATE`)
- **Regiao** — região geográfica, coluna calculada a partir do Estado (não existe campo de região direto na fonte)
- **Populacao** — população residente do Censo IBGE 2010 (origem: `IBGE_RES_POP`)
- **IDHM** — índice de desenvolvimento humano municipal
- **Gasto_Total** — gasto público municipal total, em R$ (origem: `MUN_EXPENDIT`)
- **Arrecadacao_Propria** — arrecadação própria do município, em R$ (origem: `TAXES`, multiplicado por 1.000 a partir da unidade original em milhares)
- **PIB_Total** — PIB municipal, em R$ (origem: `GDP`, em milhares de reais)
- **Gasto_Publico_VA** — Valor Adicionado Bruto do setor público (origem: `GVA_PUBLIC`)
- **VA_Total** — Valor Adicionado Bruto total do município (origem: `GVA_TOTAL`)
- **Gasto_Per_Capita** — coluna calculada: `Gasto_Total ÷ Populacao`
- **Arrecadacao_Per_Capita** — coluna calculada: `(Arrecadacao_Propria × 1.000) ÷ Populacao`
- **PIB_Per_Capita** — origem direta do campo oficial `GDP_CAPITA`, validado contra a metodologia do IBGE
- **Prop_Gastos_Publicos** — coluna calculada: `Gasto_Publico_VA ÷ VA_Total`
- **Faixa_IDH** — classificação textual do IDHM (também usada para criar a Dim_Faixa_IDH)
- **Faixa_Porte** — classificação textual da Populacao (também usada para criar a Dim_Porte_Populacional)

## Dimensões

### Dim_Estado

**Origem:** colunas `STATE` (Estado) e `Regiao` (calculada) da Fato_Municipio, por referência.

- **SK_Estado (PK)** — surrogate key gerada
- **Estado** — sigla da UF (ex.: SP, RJ, GO)
- **Nome_Estado** — nome completo do estado, coluna calculada a partir da sigla
- **Regiao** — região geográfica (Norte, Nordeste, Centro-Oeste, Sudeste, Sul)

> A Dim_Estado contém **26 linhas**, não 27. O Distrito Federal não aparece, pois seu único município (Brasília) não possui valor de `MUN_EXPENDIT` na fonte original, sendo removido pelo critério de limpeza dos campos obrigatórios dos KPIs.

### Dim_Porte_Populacional

**Origem:** tabela criada manualmente no Power Query, com as faixas de porte conforme classificação IBGE.

- **SK_Porte (PK)** — surrogate key gerada
- **Faixa_Porte** — classificação: Muito Pequena (até 5.000 hab); Pequena (5.001–20.000); Médio (20.001–100.000); Grande (acima de 100.000)
- **Limite_Inferior** — valor mínimo da faixa
- **Limite_Superior** — valor máximo da faixa

### Dim_Faixa_IDH

**Origem:** tabela criada manualmente no Power Query, com as faixas de IDH conforme classificação do PNUD.

- **SK_IDH (PK)** — surrogate key gerada
- **Faixa_IDH** — classificação: Muito Baixo (< 0,500); Baixo (0,500–0,599); Médio (0,600–0,699); Alto (0,700–0,799); Muito Alto (≥ 0,800)
- **Limite_Inferior** — valor mínimo da faixa
- **Limite_Superior** — valor máximo da faixa

### Dim_Calendario

**Origem:** construída via código M no Power Query. Como o dataset é um snapshot (IDH do Censo 2010, dados financeiros de 2016), a dimensão calendário registra apenas o ano de referência principal.

- **SK_Calendario (PK)** — surrogate key gerada
- **Ano** — ano de referência (2010)
- **Descricao** — "Censo IBGE"
- **Fonte** — "IBGE / FINBRA / IPEA"

> A Dim_Calendario possui relacionamento físico 1:N com a Fato_Municipio através do campo **SK_Calendario**, adicionado na Fato_Municipio com valor fixo (1) para todas as linhas, já que o dataset representa um único período de referência (2010). Essa estrutura mantém o modelo preparado para a inclusão de múltiplos períodos no futuro, conforme já previsto na justificativa original da dimensão.

### Tabela_Consultas (_Medidas)

Tabela vazia, criada no Power Query exclusivamente para organizar as medidas DAX do modelo. Não possui relacionamento com nenhuma outra tabela e não armazena dados — serve apenas como contêiner visual para as medidas (ex.: Total Municipios, Gasto Per Capita Medio, Pct Gasto Servicos Publicos, etc.), evitando que elas fiquem dispersas dentro da Fato_Municipio.

## Relacionamentos

Os relacionamentos seguem o padrão star schema: dimensão (1) → fato (N), com filtro fluindo da dimensão para a fato em direção única.

- Dim_Estado → Fato_Municipio (1:N)
- Dim_Porte_Populacional → Fato_Municipio (1:N)
- Dim_Faixa_IDH → Fato_Municipio (1:N)
- Dim_Calendario → Fato_Municipio (1:N)

O diagrama do star schema está disponível no arquivo `modelo_dimensional.png`, na pasta `docs/` do repositório.
