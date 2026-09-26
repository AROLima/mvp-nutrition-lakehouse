# MVP Pipeline de Dados para Análise de Indicadores Nutricionais

## Evolução da obesidade e do excesso de peso em adultos no Brasil

Este projeto foi desenvolvido com o objetivo de construir um pipeline de dados em nuvem utilizando conceitos de Engenharia de Dados.
Foram utilizados dados sobre obesidade e excesso de peso em adultos, separados por sexo e país. O processamento foi realizado no Databricks com PySpark e organizado seguindo as camadas Bronze, Silver e Gold.
Ao final do pipeline, os dados tratados foram utilizados para analisar a evolução desses indicadores no Brasil entre 1980 e 2024 e realizar uma comparação com outros países da América do Sul.

---
# 1. Contexto de Negócio e Perguntas

## 1.1 Contexto

A obesidade e o excesso de peso são indicadores relacionados ao estado nutricional da população adulta.
A análise histórica desses indicadores permite observar mudanças ao longo do tempo e comparar diferenças entre grupos.
Neste trabalho, foram utilizados dados históricos para analisar como a obesidade e o excesso de peso evoluíram no Brasil, com foco na comparação entre homens e mulheres.
Também foi realizada uma comparação com outros países da América do Sul utilizando o ano mais recente disponível na base.

## 1.2 Objetivo

O objetivo do projeto foi construir um pipeline capaz de receber os dados brutos, aplicar as transformações necessárias, organizar os dados para análise e responder às seguintes perguntas:

1. Como a obesidade evoluiu entre homens e mulheres no Brasil entre 1980 e 2024?
2. Como o excesso de peso evoluiu entre homens e mulheres no mesmo período?
3. A diferença entre homens e mulheres aumentou ou diminuiu ao longo da série histórica?
4. Como a obesidade feminina no Brasil em 2024 se compara à de outros países da América do Sul?

---
## 1.3 Fonte dos dados

Os dados utilizados têm como fonte original a Organização Mundial da Saúde, por meio do Global Health Observatory, e foram obtidos através da plataforma Our World in Data.

Foram utilizados quatro arquivos CSV:
- obesidade em mulheres;
- obesidade em homens;
- excesso de peso em mulheres;
- excesso de peso em homens.

Cada arquivo possui informações de país, código do país, ano e valor do indicador.
Cada conjunto possui 9.270 registros. Considerando as quatro fontes, foram carregados **37.080 registros brutos**.
A série utilizada cobre o período entre **1980 e 2024**.
Os indicadores utilizados são referentes à população adulta com 18 anos ou mais e são padronizados por idade.

## 1.4 Indicadores utilizados

**Obesidade:** percentual estimado de adultos com Índice de Massa Corporal (IMC) igual ou superior a 30 kg/m².
**Excesso de peso:** percentual estimado de adultos com IMC igual ou superior a 25 kg/m².
Os dados possuem valores separados entre homens e mulheres.

---

# 2. Plataforma de Nuvem e Carga dos Dados

## 2.1 Plataforma utilizada

Todo o pipeline foi desenvolvido no **Databricks Free Edition**.

O Databricks foi utilizado para:

- armazenamento dos arquivos de origem;
- criação e execução dos notebooks;
- processamento dos dados com PySpark;
- persistência das tabelas em formato Delta;
- gerenciamento das tabelas através do Unity Catalog;
- execução das consultas analíticas;
- criação das visualizações utilizadas na análise final.

Dessa forma, as etapas de ingestão, transformação, modelagem e análise foram executadas dentro do ambiente de nuvem do Databricks.

### Evidência do armazenamento em nuvem

A imagem abaixo mostra os quatro arquivos armazenados no Volume do Unity Catalog dentro do Databricks.
![Arquivos armazenados no Databricks](docs/01_databricks_volume.png)

## 2.3 Ingestão dos dados

Os quatro arquivos foram carregados separadamente utilizando PySpark em um notebook do Databricks.
Na camada Bronze, a inferência automática de esquema foi desabilitada para manter inicialmente os campos como texto.
Os arquivos utilizados foram:

```text
share-of-females-defined-as-obese.csv
share-of-females-defined-as-overweight.csv
share-of-men-defined-as-obese.csv
share-of-men-defined-as-overweight.csv
```

Durante a primeira tentativa de persistência das tabelas em formato Delta, foi identificado um problema nos nomes originais das colunas.
A coluna contendo o indicador apresentava caracteres especiais não aceitos diretamente pelo Delta.
Por esse motivo, os campos foram padronizados para:

```text
entity
code
year
raw_value
```
Essa alteração foi realizada apenas nos nomes das colunas.

---

# 3. Modelagem e Catálogo de Dados

## 3.1 Organização das camadas

O pipeline foi dividido nas camadas Bronze, Silver e Gold.

### Bronze

A camada Bronze contém os dados provenientes dos arquivos CSV com o mínimo de alterações necessário para sua persistência.

Foram criadas quatro tabelas:

- `bronze_obesity_female`
- `bronze_obesity_male`
- `bronze_overweight_female`
- `bronze_overweight_male`

Cada tabela corresponde diretamente a uma das quatro fontes utilizadas.

### Silver

Na camada Silver, os quatro conjuntos foram padronizados e reunidos em uma única estrutura.

Foi criada a tabela:

`silver_nutrition`

Nessa etapa foram realizados:

- conversão dos tipos;
- padronização das colunas;
- inclusão do sexo;
- inclusão do indicador;
- união das quatro fontes.

### Gold

Na camada Gold, os dados foram organizados em um modelo dimensional.

Foram criadas as seguintes tabelas:

- `gold_fact_nutrition`
- `gold_dim_country`
- `gold_dim_time`
- `gold_dim_sex`
- `gold_dim_indicator`

---

## 3.2 Modelo dimensional

Foi utilizado um modelo do tipo **Star Schema**.

A tabela `gold_fact_nutrition` concentra a medida de prevalência e se relaciona com quatro dimensões.

```text
                       gold_dim_time
                            |
                            |
gold_dim_country -- gold_fact_nutrition -- gold_dim_sex
                            |
                            |
                    gold_dim_indicator
```

A granularidade da tabela fato corresponde a uma observação para cada combinação de:

`país + ano + sexo + indicador`

A medida armazenada é `prevalence_pct`.

---

## 3.3 Catálogo de Dados

### Tabelas Bronze

As quatro tabelas Bronze possuem a mesma estrutura.

| Campo | Tipo | Descrição | Origem |
|---|---|---|---|
| `entity` | string | Nome do país ou localidade | `Entity` |
| `code` | string | Código do país | `Code` |
| `year` | string | Ano da observação | `Year` |
| `raw_value` | string | Valor original do indicador | Coluna de prevalência |

### `silver_nutrition`

| Campo | Tipo | Descrição | Domínio |
|---|---|---|---|
| `country` | string | Nome do país | País/localidade |
| `country_code` | string | Código do país | Ex.: BRA, ARG, CHL |
| `year` | integer | Ano da observação | 1980 a 2024 |
| `sex` | string | Sexo relacionado ao indicador | Female, Male |
| `indicator` | string | Indicador analisado | Obesity, Overweight |
| `prevalence_pct` | double | Prevalência estimada em percentual | 0 a 100 |

### `gold_dim_country`

| Campo | Tipo | Descrição |
|---|---|---|
| `country_id` | integer | Identificador da dimensão país |
| `country` | string | Nome do país |
| `country_code` | string | Código do país |

### `gold_dim_time`

| Campo | Tipo | Descrição |
|---|---|---|
| `time_id` | integer | Identificador da dimensão tempo |
| `year` | integer | Ano da observação |

### `gold_dim_sex`

| Campo | Tipo | Descrição |
|---|---|---|
| `sex_id` | integer | Identificador da dimensão sexo |
| `sex` | string | Sexo relacionado ao registro |

### `gold_dim_indicator`

| Campo | Tipo | Descrição |
|---|---|---|
| `indicator_id` | integer | Identificador da dimensão indicador |
| `indicator` | string | Indicador nutricional |

### `gold_fact_nutrition`

| Campo | Tipo | Descrição | Origem |
|---|---|---|---|
| `country_id` | integer | Chave da dimensão país | `gold_dim_country` |
| `time_id` | integer | Chave da dimensão tempo | `gold_dim_time` |
| `sex_id` | integer | Chave da dimensão sexo | `gold_dim_sex` |
| `indicator_id` | integer | Chave da dimensão indicador | `gold_dim_indicator` |
| `prevalence_pct` | double | Prevalência estimada | `silver_nutrition` |

