# MVP - Previsão do Preço da Ação PETR4

## 1. Definição do Problema

**Descrição do problema:**  
O objetivo é prever o preço de fechamento da ação PETR4 a partir de variáveis históricas do próprio ativo (abertura, máxima, mínima, volume, indicadores derivados). Este é um problema de **aprendizado supervisionado**, do tipo **regressão**.

**Premissas e hipóteses:**  
- O preço de fechamento (`Close`) pode ser explicado por variáveis como `Open`, `High`, `Low`, `Volume`, médias móveis e retornos passados.  
- O comportamento da série temporal segue padrões que podem ser aprendidos por modelos estatísticos ou de machine learning.  
- Não é considerado impacto de fatores externos (macroeconômicos ou notícias), apenas dados históricos do ativo.  

**Restrições e condições:**  
- Utilizar apenas dados tabulares disponíveis no dataset.  
- Respeitar a ordem temporal para evitar vazamento de dados.  
- Limitar a previsão ao horizonte de curto prazo (próximo dia).  

**Descrição do dataset:**  
- `Date`: data da negociação (índice temporal).  
- `Open`: preço de abertura.  
- `High`: preço máximo do dia.  
- `Low`: preço mínimo do dia.  
- `Close`: preço de fechamento (variável alvo).  
- `Volume`: volume de ações negociadas.  
- `Symbol`: código do ativo (PETR4).  

---

## 2. Preparação de Dados

**Divisão treino e teste:**  
Os dados foram separados em 80% para treino e 20% para teste, mantendo a ordem temporal. Resultado:  
- Treino: 2.515 instâncias  
- Teste: 628 instâncias  

**Validação cruzada:**  
Não faz sentido utilizar **k-fold cross-validation tradicional** em séries temporais, pois isso mistura passado e futuro. A divisão sequencial já é suficiente, mas alternativamente poderia ser usada a técnica **TimeSeriesSplit**.  

**Transformações aplicadas:**  
- **Padronização (StandardScaler):** aplicada para modelos lineares e SVM.  
- **Normalização (MinMaxScaler):** aplicada para redes neurais e KNN.  
- **Feature Engineering:** criação de médias móveis (MA5, MA10) e retornos diários (`Return`).  

**Feature Selection:**  
- Mantidos: `Open`, `High`, `Low`, `Volume`, `MA5`, `MA10`, `Return`.  
- Descartados: `Date` (somente índice temporal), `Symbol` (redundante).  

---

## 3. Modelagem e Treinamento

**Algoritmos testados:**  
- Regressão Linear (baseline estatístico).  
- KNN Regressor (algoritmo baseado em distância).  
- Random Forest Regressor (método de ensemble robusto).  
- Suporte Vetorial para Regressão (SVR).  

**Hiperparâmetros iniciais:**  
- Regressão Linear: sem ajustes.  
- KNN: k = 5 vizinhos.  
- Random Forest: 100 árvores.  
- SVR: kernel RBF, C = 1.0.  

**Treinamento:**  
Todos os modelos foram treinados com o conjunto de treino (2.515 instâncias).  
Não foi identificado **underfitting severo** nos modelos, mas alguns mostraram sinais de **overfitting leve**.  

**Otimização de hiperparâmetros:**  
- KNN: ajuste de `k` via grid search.  
- Random Forest: ajuste de `max_depth` e `n_estimators`.  
- SVR: ajuste de `C` e `gamma`.  

**Métodos mais complexos avaliados:**  
- Possibilidade de aplicar redes neurais (LSTM para séries temporais).  

**Ensembles:**  
Foi avaliada a média simples das previsões de Random Forest e SVR, mas o ganho foi marginal.  

---

## 4. Avaliação de Resultados

**Métricas utilizadas:**  
- **MAE (Mean Absolute Error):** mede o erro médio absoluto.  
- **RMSE (Root Mean Squared Error):** penaliza mais fortemente grandes erros.  

**Baselines:**  
- Naive (previsão = último valor): MAE ≈ 0.7458 | RMSE ≈ 1.0357  
- Média móvel (MA5): MAE ≈ 0.9237 | RMSE ≈ 1.2121  

**Resultados principais:**  
- Random Forest apresentou melhor equilíbrio entre viés e variância.  
- Regressão Linear foi competitiva, mas mais sensível a outliers.  
- SVR obteve bons resultados após ajuste de hiperparâmetros.  

**Overfitting:**  
- Modelos de árvore profunda mostraram overfitting leve.  
- Regularização ajudou a mitigar.  

**Comparação entre modelos:**  
- Random Forest e SVR superaram o baseline.  
- KNN teve desempenho inferior, sensível à escala dos dados.  

**Melhor solução encontrada:**  
O **Random Forest Regressor** apresentou a melhor performance global, com menor RMSE e robustez a variações dos dados.  
Foi escolhido como modelo final.  

---

## 5. Conclusão

O MVP demonstrou a aplicação prática de técnicas de ciência de dados e aprendizado supervisionado para previsão de preços de ações.  
Apesar de simples, os resultados mostram que modelos como **Random Forest** conseguem capturar padrões relevantes e superar abordagens ingênuas (Naive e MA5).  

**Próximos passos sugeridos:**  
- Avaliar modelos de deep learning (LSTM para séries temporais).  
- Considerar variáveis externas (índices setoriais, macroeconomia).  
- Expandir a previsão para múltiplos horizontes (multi-step forecasting).  

---
