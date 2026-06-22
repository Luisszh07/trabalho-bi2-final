# Brazilian Cities BI — Análise de Gastos Públicos e Desenvolvimento Municipal

Projeto de Business Intelligence desenvolvido para a disciplina de Banco de Dados / Modelagem Dimensional — CEUB.

## Autores

- Luís Henrique Sampaio Lopes — luis.hl@sempreceub.com
- Sócrates Trevisan Silva — socrates.trevisan@sempreceub.com

## Dataset

**Brazilian Cities — A collection of 79 attributes from Brazilian Cities**  
Fonte: Kaggle  
Link: https://www.kaggle.com/datasets/crisparada/brazilian-cities/data?select=BRAZIL_CITIES.csv  
Licença: Pública (Kaggle)  
Dados socioeconômicos reais de municípios brasileiros, com origem em IBGE, IPEA e FINBRA.  
3.485 municípios após limpeza de dados (remoção de registros com nulos nos campos obrigatórios).

## Problema de Negócio

Os municípios brasileiros com menor IDH e menor capacidade de arrecadação própria recebem gastos públicos per capita proporcionais às suas necessidades, ou a desigualdade fiscal aprofunda as disparidades socioeconômicas já existentes?

## Principais KPIs

| KPI | Descrição |
|-----|-----------|
| Gasto Per Capita | Gasto público total ÷ população do município |
| IDH Médio por Região | Média do IDHM agrupada por região geográfica |
| Arrecadação Per Capita | Arrecadação própria ÷ população |
| % Gasto em Serviços Públicos | GVA público ÷ GVA total |
| PIB Per Capita | PIB municipal ÷ população |

## Estrutura do Repositório

```
.
├── README.md
├── docs/
│   ├── problema_de_negocio.md
│   ├── kpis_okrs.md
│   ├── modelagem_dimensional.md
│   ├── modelo_dimensional.png
│   └── decisoes_de_modelagem.md
├── data/
│   └── README.md
├── pbip/
│   └── brazilian-cities-bi.pbip (+ pastas associadas)
└── apresentacao/
    └── slides.pdf
```

## Como abrir o projeto

1. Clone o repositório: `git clone https://github.com/Luisszh07/trabalho-bi2-final`
2. Baixe o arquivo `BRAZIL_CITIES.csv` em: https://www.kaggle.com/datasets/crisparada/brazilian-cities
3. Coloque o CSV na pasta `data/`
4. Abra o Power BI Desktop e acesse `pbip/tranlho bi2 final.pbip`
5. Ao abrir, atualize a fonte de dados apontando para o CSV em `data/`

## Modelo Dimensional

Star schema com 1 tabela fato e 4 dimensões:

- **Fato_Municipio** — gasto, arrecadação, PIB, IDH, população por município (3.485 linhas)
- **Dim_Estado** — UF, nome completo e região geográfica (26 linhas)
- **Dim_Porte_Populacional** — faixas de porte conforme IBGE (4 linhas)
- **Dim_Faixa_IDH** — faixas de IDH conforme PNUD (5 linhas)
- **Dim_Calendario** — período de referência: Censo IBGE 2010 (1 linha)

![Modelo Dimensional](docs/modelo_dimensional.png)
