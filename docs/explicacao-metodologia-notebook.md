# Explicação da metodologia do notebook

Este documento explica, de forma simples e objetiva, o que foi feito em cada etapa do notebook e por que cada escolha foi adotada.

Objetivo do desafio:
- Prever a coluna rating (1 a 5) com base em título e texto de reviews em português.
- Usar modelos tradicionais de Machine Learning.
- Avaliar com Macro F1.
- Evitar data leakage.

---

## Etapa 1 - Introdução e objetivo

O notebook abre definindo o problema como classificação multiclasse de sentimento/opinião em 5 níveis.

Justificativa:
- Alinha o trabalho ao desafio oficial.
- Deixa clara a métrica principal (F1), que influencia todas as escolhas seguintes.

---

## Etapa 2 - Ambiente e carga de dados

Foi preparado o ambiente com bibliotecas de:
- manipulação de dados (pandas, numpy),
- visualização (matplotlib, seaborn),
- NLP (spaCy),
- modelagem (scikit-learn, xgboost).

Arquivos carregados:
- train.csv
- test.csv
- sample_submission.csv

Justificativa:
- Separar treino/teste desde o início evita mistura indevida.
- Ter sample_submission garante formato correto da saida final.

---

## Etapa 3 - Análise exploratória (EDA)

### O que foi analisado
- valores nulos,
- distribuição das classes de rating,
- comprimento de título/texto por classe,
- termos frequentes por classe (uso exploratório para entendimento de padrão linguístico).

### Por que isso importa
- Permite diagnosticar desbalanceamento e qualidade dos dados.
- Mostra sinais de subjetividade e intensidade textual.
- Ajuda a decidir quais features linguísticas valem a pena extrair.

Observação importante:
- A parte de termos frequentes foi usada como exploração inicial.
- Na modelagem final (Etapas 5+), não foi usado TF-IDF nem BoW, conforme restrição atual.

---

## Etapa 4 - Pré-processamento NLP e engenharia linguística base

### O que foi feito
1. Limpeza de ruído:
- concatenação de título + texto,
- remoção de URLs e espaços extras,
- tratamento de nulos.

2. Features estruturais:
- contagem de palavras em título/texto,
- contagem de entonação (!/?),
- contagem de termos em caixa alta.

3. POS tagging com spaCy:
- contagem de adjetivos,
- contagem de verbos,
- padrões sintáticos simples com Matcher,
- contagem de termos de polaridade positiva/negativa.

4. Percentuais de intensidade:
- uppercase e entonação normalizados por tamanho do texto.

### Justificativa
- Reviews com notas extremas costumam ter maior carga subjetiva e intensidade.
- Adjetivos, verbos e pontuação são sinais fortes para diferenciar classes.
- Normalizar por tamanho reduz viés de textos longos.

---

## Etapa 5 - Engenharia de features para modelagem (sem TF-IDF e sem BoW)

### Escolha principal
A modelagem foi feita apenas com features numéricas linguísticas/estruturais.

### Features usadas
- base: contagens e percentuais da Etapa 4,
- derivadas:
  - proporção de adjetivos, verbos e padrões,
  - razão de tamanho título/texto,
  - balanço de polaridade (positivo - negativo),
  - total de polaridade,
  - intensidade total de entonação.

### Seleção de atributos
Foi aplicado RFE para reduzir ruído e manter apenas os atributos mais relevantes para os modelos de árvore.

### Justificativa
- Cumpre a restrição de não usar vetorização textual clássica na etapa de modelagem.
- Mantém interpretabilidade: cada feature tem significado linguístico claro.
- Reduz custo computacional e risco de overfitting em atributos redundantes.

---

## Etapa 6 - Estratégia de validação

### Escolha
StratifiedKFold com embaralhamento e seed fixa.

### Justificativa
- Preserva proporção das classes em cada fold.
- Gera estimativa de desempenho mais estável que uma única divisão.
- Seed fixa melhora reprodutibilidade.

---

## Etapa 7 - Seleção e tuning de modelos (foco no que foi desenvolvido)

### Modelos avaliados
- LinearSVC (versão ultra-rápida),
- RandomForest,
- XGBoost.

Todos são modelos tradicionais, como exigido no desafio.

### Busca de hiperparâmetros
- RandomizedSearchCV para explorar combinações de forma eficiente.
- Versão ultra-rápida aplicada para viabilizar execução no tempo:
  - menos iterações,
  - menos folds no tuning,
  - espaços de busca menores,
  - troca de SVC com kernel rbf por LinearSVC (grande ganho de tempo).

### Justificativa
- Random search oferece bom equilíbrio entre qualidade e custo.
- LinearSVC evita gargalo de treino do kernel rbf.
- Mantém comparação justa entre famílias de modelos tradicionais.

---

## Etapa 8 - Avaliação de resultados

### O que foi feito
- seleção do melhor modelo por score de validação,
- predições OOF,
- Macro F1,
- classification report,
- matriz de confusão.

### Justificativa
- Macro F1 trata todas as classes com peso igual.
- OOF fornece visão mais realista do desempenho geral.
- Matriz de confusão mostra onde o modelo confunde classes próximas.

---

## Etapa 9 - Análise de erros

### O que foi analisado
- pares de classe real vs classe prevista com maior frequência de erro,
- foco em confusões próximas (ex.: 4 vs 5),
- exemplos qualitativos de reviews mal classificados.

### Justificativa
- Ajuda a explicar limites do modelo.
- Mostra caminhos de melhoria com base em erro real, não em suposição.

---

## Etapa 10 - Geração da submissão

### O que foi feito
- treino final do melhor modelo com todos os dados de treino,
- predição no conjunto de teste,
- validações de integridade:
  - quantidade de linhas,
  - ordem/ids,
  - intervalo de classes [1, 5],
  - tipo inteiro,
- exportação para datasets/submission.csv.

### Justificativa
- Garante conformidade com o formato exigido.
- Evita erros comuns de submissao.

---

## Etapa 11 - Conclusões e metodologia

### Síntese da decisão técnica
- Pipeline linguístico interpretável,
- modelos tradicionais,
- validação estratificada,
- foco em Macro F1,
- sem TF-IDF/BoW na modelagem final.

### Trade-offs assumidos
- Versão ultra-rápida reduz tempo, mas pode perder um pouco de desempenho comparada a tuning mais profundo.
- Em contexto de prazo, o ganho de viabilidade operacional compensa.

---

## Resumo final (em uma frase)

A solução prioriza interpretabilidade, aderência ao edital e robustez prática: extrai sinais linguísticos relevantes, valida corretamente com modelos tradicionais e entrega submissão consistente no formato exigido.
