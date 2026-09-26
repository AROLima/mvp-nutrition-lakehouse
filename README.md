# MVP — Pipeline de Dados para Análise de Indicadores Nutricionais

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

### Evidência do armazenamento em nuvem

A imagem abaixo mostra os quatro arquivos armazenados no Volume do Unity Catalog dentro do Databricks.
![Arquivos armazenados no Databricks](docs/screenshots/01_databricks_volume.png)
