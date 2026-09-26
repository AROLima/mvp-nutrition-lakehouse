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
