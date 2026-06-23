# 4.2 KPIs e OKRs

## KPIs

Os cinco KPIs foram definidos a partir do problema de negócio descrito na seção 4.1, cobrindo gastos públicos, desenvolvimento humano e capacidade fiscal dos municípios.

### KPI 1 — Gasto Público Per Capita

**Fórmula:** Campo `MUN_EXPENDIT` (gasto total municipal, em R$) ÷ campo `IBGE_RES_POP` (população residente, Censo IBGE 2010).

**Unidade:** R$ por habitante

**Meta:** Média nacional como referência; municípios abaixo de 50% da média são considerados críticos.

**Justificativa:** Principal indicador de investimento por cidadão. Permite comparar o esforço financeiro dos municípios independentemente do porte.

> **Observação de tratamento de dados:** o campo originalmente identificado para essa fórmula (`COMP_T`) revelou-se incorreto durante a validação — tratava-se da contagem de empresas do setor de serviços domésticos (zerada em praticamente todos os municípios), não de despesa municipal. O campo correto, `MUN_EXPENDIT`, foi confirmado junto ao Data Dictionary oficial do dataset.

### KPI 2 — IDH Municipal Médio por Região

**Fórmula:** Média aritmética do campo `IDHM` agrupada por região geográfica (`Regiao`, coluna calculada a partir do `Estado`).

**Unidade:** Índice de 0 a 1

**Meta:** Nenhuma região abaixo de 0,65 (limiar de desenvolvimento médio).

**Justificativa:** Permite identificar quais regiões concentram municípios com menor desenvolvimento humano e priorizar análises de equidade.

### KPI 3 — Arrecadação Própria Per Capita

**Fórmula:** (Campo `TAXES` × 1.000) ÷ campo `IBGE_RES_POP`.

**Unidade:** R$ por habitante

**Meta:** Acompanhar evolução; municípios com menos de R$ 200/hab são classificados como de baixa autonomia fiscal.

**Justificativa:** Mede a capacidade do município de financiar seus serviços sem depender de transferências. Quanto menor, maior a dependência federal/estadual.

> **Observação de tratamento de dados:** o campo `TAXES` está expresso em milhares de reais na fonte original (conforme Data Dictionary), exigindo multiplicação por 1.000 antes da divisão pela população para resultar em R$/habitante corretamente.

### KPI 4 — Proporção de Gasto com Serviços Públicos

**Fórmula:** Campo `GVA_PUBLIC` (Valor Adicionado Bruto do setor público) ÷ campo `GVA_TOTAL` (Valor Adicionado Bruto total do município) × 100.

**Unidade:** % do valor adicionado total

**Meta:** Acompanhar a participação do setor público na economia local entre faixas de IDH.

**Justificativa:** Indica o peso da administração pública, defesa, educação, saúde e seguridade social na economia do município, servindo como proxy do investimento em serviços públicos diante da ausência, no dataset original, de despesa orçamentária detalhada por função (saúde e educação separadamente).

> **Observação de tratamento de dados:** o KPI original previa medir especificamente "Gasto em Saúde e Educação ÷ Gasto Total". Após validação junto ao Data Dictionary, constatou-se que o dataset **não possui** campos de despesa orçamentária discriminados por função (apenas a contagem de empresas dos setores de Educação e Saúde, e o Valor Adicionado Bruto agregado do setor público). O KPI foi reformulado para utilizar `GVA_PUBLIC ÷ GVA_TOTAL`, com a ressalva de que essa métrica é uma proxy ampla (administração, defesa, educação, saúde e seguridade social), não um indicador exclusivo de saúde e educação. Linhas com proporção superior a 100% (inconsistência identificada na fonte original) foram removidas na etapa de limpeza.

### KPI 5 — PIB Per Capita Municipal

**Fórmula:** Campo `GDP_CAPITA` (já calculado e validado pela fonte oficial — IBGE).

**Unidade:** R$ por habitante

**Meta:** Referência: média nacional; municípios abaixo de 50% da média são priorizados na análise de equidade.

**Justificativa:** Mede a riqueza gerada localmente. Cruzado com IDH e gasto público, revela se o crescimento econômico se traduz em qualidade de vida.

> **Observação de tratamento de dados:** optou-se por utilizar o campo `GDP_CAPITA`, já calculado pela fonte original, em vez de uma coluna calculada manualmente a partir de `GDP ÷ Populacao`. Essa decisão evita riscos de inconsistência de escala (o campo `GDP` está em milhares de reais) e elimina a necessidade de definir qual referência populacional usar entre as disponíveis no dataset.

## OKRs

Os dois OKRs traduzem as prioridades analíticas do projeto em objetivos mensuráveis, vinculados diretamente aos KPIs acima.

### OKR 1 — Mapear a Desigualdade Fiscal entre Municípios

**Objective:** Gerar uma visão clara e acessível da distribuição dos gastos públicos municipais no Brasil, evidenciando as disparidades entre regiões e portes populacionais.

- **KR1:** Identificar os 20 municípios com menor Gasto Público Per Capita e cruzar com seu IDHM até a entrega do projeto. (KPI 1)
- **KR2:** Demonstrar que pelo menos 3 regiões têm IDH Médio abaixo de 0,70 no dashboard final. (KPI 2)
- **KR3:** Mapear todos os municípios com Arrecadação Própria Per Capita abaixo de R$ 200/hab e classificá-los por estado. (KPI 3)

### OKR 2 — Evidenciar a Relação entre Investimento e Desenvolvimento Humano

**Objective:** Demonstrar, com evidência nos dados, se municípios que mais investem per capita apresentam melhores indicadores de IDH, ou se outros fatores explicam as diferenças.

- **KR1:** Construir um visual de dispersão IDH × Gasto Per Capita e identificar outliers relevantes até a apresentação. (KPI 1 e KPI 2)
- **KR2:** Verificar se municípios com IDH abaixo de 0,60 têm participação do setor público abaixo de 40% do valor adicionado total. (KPI 4)
