# 4.1 Problema de Negócio

## Contexto

O Brasil possui mais de 5.500 municípios, com realidades socioeconômicas extremamente distintas entre si. Capitais e grandes centros urbanos concentram arrecadação própria e investimento público, enquanto boa parte dos municípios do interior — principalmente nas regiões Norte e Nordeste — depende quase exclusivamente de repasses federais e estaduais para custear seus serviços básicos.

Esse desequilíbrio levanta uma questão central para a gestão pública: o investimento per capita realizado pelos municípios acompanha, de fato, suas necessidades de desenvolvimento humano? Ou a desigualdade fiscal apenas reproduz e aprofunda as disparidades sociais já existentes?

## Pergunta de Negócio

> Os municípios brasileiros com menor IDH e menor capacidade de arrecadação própria recebem gastos públicos per capita proporcionais às suas necessidades, ou a desigualdade fiscal aprofunda as disparidades socioeconômicas já existentes?

## Fonte de Dados

Utilizamos o dataset **Brazilian Cities**, disponível publicamente no Kaggle, com 79 atributos socioeconômicos por município brasileiro, consolidando informações de fontes oficiais como **IBGE** (Censo 2010), **IPEA** e **FINBRA** (dados financeiros municipais, 2016).

Link: https://www.kaggle.com/datasets/crisparada/brazilian-cities

## Stakeholders

| Stakeholder | Interesse no projeto |
|---|---|
| Gestores públicos federais | Monitorar a distribuição de recursos entre municípios e regiões |
| Secretarias estaduais de planejamento | Comparar municípios de um mesmo estado, identificando prioridades de investimento |
| Pesquisadores e acadêmicos | Analisar a relação entre investimento público e desenvolvimento humano |
| Jornalistas e órgãos de transparência | Identificar municípios com baixa eficiência ou baixa autonomia fiscal |

## Hipóteses de Trabalho

1. **H1** — Municípios de menor porte populacional apresentam maior Gasto Per Capita, mas isso não necessariamente reflete maior eficiência na aplicação dos recursos.
2. **H2** — Regiões Sudeste e Sul concentram a maior parcela do PIB nacional, ampliando a dependência fiscal das regiões Norte e Nordeste em relação a transferências governamentais.
3. **H3** — Municípios com IDH mais baixo não recebem, necessariamente, maior investimento per capita proporcional às suas carências — a relação entre gasto público e desenvolvimento humano não é automática.
4. **H4** — A arrecadação própria per capita está fortemente correlacionada ao porte populacional e à atividade econômica local, e não distribuída de forma equitativa entre as regiões.

## Limitações Conhecidas da Fonte de Dados

Durante o tratamento dos dados (detalhado na seção de Power Query), identificamos que o dataset original:

- Não possui uma coluna de despesa orçamentária detalhada por função (ex.: gasto específico em saúde ou educação), apenas o valor agregado de despesa municipal total (`MUN_EXPENDIT`) e o Valor Adicionado Bruto por setor econômico (`GVA_*`).
- Possui valor nulo de despesa municipal (`MUN_EXPENDIT`) para o Distrito Federal, fazendo com que essa unidade federativa seja excluída da análise final.
- Apresenta inconsistências pontuais em alguns municípios (outliers), tratadas na etapa de limpeza dos dados.

Essas limitações foram documentadas e tratadas de forma transparente ao longo do projeto, sem comprometer a validade da análise para os municípios e indicadores efetivamente disponíveis.
