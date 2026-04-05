# Rating Prediction Challenge

## 1. Visão geral do desafio

Este trabalho resolve o problema de predição de `rating` (classes de 1 a 5) a partir de avaliações em português, utilizando apenas modelos tradicionais de Machine Learning, conforme o desafio.

Objetivo principal:
- Treinar com `train.csv` e prever `rating` para `test.csv`.

Restrições atendidas:
- Sem uso de dados externos.
- Validação local para evitar data leakage.
- Saída final no formato `id,rating`, compatível com `sample_submission.csv`.

Métrica de referência:
- F1 Score (com foco em Macro F1 para avaliação equilibrada entre classes).

---

## 2. Qualidade técnica

### 2.1 Organização do pipeline
O notebook foi estruturado de forma sequencial, cobrindo todas as etapas de um projeto de mineração de dados:
1. Entendimento do problema.
2. Carga e inspeção dos dados.
3. EDA.
4. Pré-processamento e engenharia linguística.
5. Modelagem.
6. Validação e tuning.
7. Avaliação.
8. Análise de erros.
9. Submissão.

### 2.2 Reprodutibilidade
Foram adotadas práticas para manter consistência dos resultados:
- Uso de sementes (`random_state`) nos modelos e validação.
- Validação estratificada.
- Processamento padronizado para treino e teste.

### 2.3 Confiabilidade da entrega
A geração do arquivo final inclui validações explícitas:
- Mesmo número de linhas do `test.csv`.
- Mesma ordem dos `id`.
- Classe prevista no intervalo `[1, 5]`.
- Tipo inteiro para `rating`.

---

## 3. Features (engenharia de atributos)

### 3.1 Princípios adotados
A modelagem final foi feita sem TF-IDF e sem Bag of Words, priorizando atributos linguísticos interpretáveis e coerentes com o domínio de reviews.

### 3.2 Features estruturais
- `title_word_count`, `text_word_count`.
- `title_exclam`, `text_exclam` (sinais de intensidade).
- `title_upper`, `text_upper` (ênfase textual).

### 3.3 Features morfossintáticas e léxicas
- `adj_count`, `verb_count`.
- `match_count` (padrões linguísticos com `Matcher`).
- `pos_mood_count`, `neg_mood_count` (léxico de polaridade customizado).

### 3.4 Features normalizadas e derivadas
- Percentuais de intensidade: `title_upper_pct`, `text_upper_pct`, `title_exclam_pct`, `text_exclam_pct`.
- Razões e composições:
  - `adj_ratio_text`, `verb_ratio_text`, `match_ratio_text`.
  - `title_len_ratio`.
  - `mood_balance`, `mood_total`.
  - `exclam_total_pct`.

### 3.5 Justificativa técnica
Essas features capturam sinais importantes de subjetividade e intensidade, úteis para diferenciar classes próximas de rating. Além disso, são fáceis de interpretar e explicáveis em contexto acadêmico.

---

## 4. Metodologia

### 4.1 Pré-processamento
- Tratamento de nulos em `title` e `text`.
- Limpeza de ruídos (URLs, espaços extras).
- Processamento com spaCy (`pt_core_news_lg`) para POS tagging e lematização.

### 4.2 Estratégia de validação
- `StratifiedKFold` para preservar proporção das classes por fold.
- Métrica de seleção: `f1_macro`.
- Avaliação com predições OOF para leitura mais robusta do desempenho.

### 4.3 Modelos tradicionais comparados
- `LinearSVC`.
- `RandomForestClassifier`.
- `XGBClassifier`.

### 4.4 Tuning
- `RandomizedSearchCV` com espaço de busca controlado.
- Versão rápida/ultra-rápida para viabilizar execução dentro do tempo disponível.

---

## 5. Análise (EDA + análise de erros)

### 5.1 EDA
A análise exploratória verificou:
- Níveis de dados ausentes.
- Distribuição de classes.
- Diferenças de comprimento textual por rating.
- Termos frequentes por classe para construir intuição linguística.

### 5.2 Análise de erros
Após treino e avaliação, foram examinados:
- Pares `classe real x classe prevista` com maior frequência de erro.
- Confusões entre classes próximas (especialmente 4 e 5).
- Exemplos qualitativos para identificar padrões ambíguos de linguagem.

Resultado prático da análise:
- Classes vizinhas tendem a compartilhar vocabulário e intensidade semelhantes, justificando parte das confusões.

---

## 6. Desempenho

### 6.1 Indicadores usados
- Macro F1 (principal para comparação de modelos).
- Relatório de classificação por classe.
- Matriz de confusão.

### 6.2 Interpretação
- Macro F1 garante que todas as classes tenham peso semelhante na avaliação.
- O desempenho não deve ser lido apenas pela média; a matriz de confusão mostra exatamente onde o modelo falha.

### 6.3 Trade-off adotado
- Tuning mais profundo pode melhorar desempenho, mas aumenta muito custo computacional.
- Em ambiente com limite de tempo, a versão rápida preserva qualidade suficiente para benchmark e entrega.

---

## 7. Apresentação

### 7.1 Roteiro de apresentação sugerido
1. Problema e restrições do desafio.
2. Pipeline completo (do dado bruto à submissão).
3. Principais features e por que foram escolhidas.
4. Modelos comparados e critério de escolha.
5. Resultados (Macro F1 + matriz de confusão).
6. Limitações e próximos passos.

### 7.2 Pontos fortes para destacar
- Aderência total aos requisitos do desafio.
- Modelagem explicável e tecnicamente justificável.
- Processo reproduzível e com validação correta.

---

## 8. Conformidade com o documento do desafio

Checklist:
- Compreender o problema: atendido.
- Realizar EDA: atendido.
- Implementar pipeline de pré-processamento: atendido.
- Projetar features relevantes e justificadas: atendido.
- Comparar modelos tradicionais: atendido.
- Justificar escolha do modelo final: atendido.
- Avaliar desempenho com F1: atendido.
- Interpretar resultados: atendido.
- Gerar submissão no formato correto: atendido.
- Não usar dados externos: atendido.

---

## 9. Conclusão

A solução foi construída com foco em clareza metodológica, qualidade técnica e conformidade com o desafio. A combinação de features linguísticas interpretáveis, validação estratificada e modelos tradicionais permite uma abordagem sólida para o desafio de predição de ratings em português.

Como próximos passos, recomenda-se:
1. Rodar tuning mais profundo apenas no modelo mais promissor.
2. Refinar features de intensidade para reduzir confusão entre classes vizinhas.
3. Consolidar os melhores resultados em relatório final com tabelas e gráficos da execução final.
