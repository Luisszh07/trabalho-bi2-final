# Decisões de Modelagem

## O que virou fato e por quê

A tabela `Fato_Municipio` centraliza todas as métricas numéricas do problema: gastos, arrecadação, PIB, população e IDH. Esses valores só fazem sentido analisados em conjunto com as dimensões geográficas e classificatórias.

## O que virou dimensão e por quê

- **Dim_Estado:** agrupa municípios por UF e região, habilitando análises geográficas sem duplicar dados na fato.
- **Dim_Porte_Populacional:** permite segmentar municípios por tamanho, essencial para comparações justas de gasto per capita.
- **Dim_Faixa_IDH:** permite filtrar e agrupar municípios por nível de desenvolvimento, central para o problema de negócio.
- **Dim_Calendario:** obrigatória pelo enunciado. Registra o período de referência dos dados, ainda que sem relacionamento físico ativo no modelo final (ver seção 4.3).

## Surrogate Keys

Todas as dimensões utilizam surrogate keys inteiras geradas via coluna de índice no Power Query. As siglas de estado e os nomes de município são mantidos na fato como atributos descritivos, mas as chaves de relacionamento são sempre as surrogate keys (SK_Estado, SK_Porte, SK_IDH), trazidas para a Fato_Municipio através de mesclagem de consultas (merge) com cada dimensão.

## Colunas Calculadas na Fato

- **Gasto_Per_Capita** e **Arrecadacao_Per_Capita:** calculadas no Power Query dividindo os totais pela população, com o ajuste de escala necessário (multiplicação por 1.000 no caso da Arrecadacao_Propria, originalmente expressa em milhares de reais na fonte). Calcular essas métricas no Power Query, em vez de via medida DAX, evita recálculo redundante em cada visual e melhora a performance do modelo.
- **PIB_Per_Capita:** diferente das demais métricas per capita, optou-se por utilizar diretamente o campo `GDP_CAPITA`, já calculado e validado pela fonte oficial (IBGE), em vez de uma fórmula manual `GDP ÷ Populacao`. Essa decisão foi tomada após identificação de inconsistências de escala durante a validação manual do cálculo, eliminando o risco de erro e a necessidade de decidir entre as diferentes referências populacionais disponíveis no dataset (IBGE_RES_POP, POP_GDP).
- **Prop_Gastos_Publicos:** calculada como `Gasto_Publico_VA ÷ VA_Total`, substituindo o indicador originalmente planejado de "Gasto em Saúde e Educação", que não pôde ser implementado por ausência de campos de despesa orçamentária discriminados por função no dataset original (ver detalhamento na seção 4.2).
- **Faixa_Porte** e **Faixa_IDH:** criadas como colunas calculadas condicionais e, em seguida, utilizadas como base para a criação das respectivas dimensões manuais no Power Query.

## Correções de Mapeamento de Campos Identificadas Durante o Desenvolvimento

Ao longo do desenvolvimento do projeto, a validação cruzada dos dados com o Data Dictionary oficial do dataset revelou divergências entre o mapeamento de campos originalmente planejado e os campos corretos da fonte. As principais correções aplicadas foram:

| Campo planejado inicialmente | Problema identificado | Campo correto utilizado |
|---|---|---|
| `COMP_T` (Gasto_Total) | Contagem de empresas do setor de serviços domésticos, zerada na quase totalidade dos municípios | `MUN_EXPENDIT` |
| `POP_LAST_CENSO` (Populacao) | Coluna inexistente no dataset | `IBGE_RES_POP` |
| `REGION` (Regiao) | Coluna inexistente no dataset | Coluna calculada a partir de `STATE` |
| `GDP ÷ Populacao` (PIB_Per_Capita) | Erro de escala (GDP expresso em milhares de reais) e inconsistência de referência populacional | Campo oficial `GDP_CAPITA`, renomeado diretamente |
| `Gasto_Saude + Gasto_Educacao` (Prop_Gasto_Social) | Campos de despesa por função inexistentes no dataset | `GVA_PUBLIC ÷ GVA_TOTAL` (Prop_Gastos_Publicos) |

Essas correções foram identificadas através de validação estatística dos campos (comparação de ordens de grandeza, verificação contra campos oficiais já calculados na fonte, e consulta ao Data Dictionary do dataset), e estão documentadas de forma transparente para fins de revisão acadêmica do processo de modelagem.
