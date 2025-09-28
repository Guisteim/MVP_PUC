# README — MVP PETR4 Forecast (Versão Atualizada)

## Definição do Problema

**Descrição do problema:**  
O projeto busca prever o preço de fechamento **do próximo pregão (t+1)** da ação PETR4, com base em séries temporais históricas de preços, volumes e indicadores técnicos.

**Premissas e hipóteses:**  
- O preço futuro pode ser parcialmente explicado pelo comportamento passado.  
- Indicadores técnicos como retornos defasados, médias móveis e volatilidade contribuem para capturar tendências.  
- A previsão é de horizonte curto (um dia), não se aplicando a horizontes maiores.  

**Restrições e condições para seleção dos dados:**  
- Somente dados históricos de PETR4 foram utilizados.  
- As variáveis são construídas a partir do próprio histórico da ação (sem dados externos).  
- Para evitar *data leakage*, apenas informações passadas foram consideradas.  

**Descrição do dataset:**  
- Variáveis originais: `Date`, `Open`, `High`, `Low`, `Close`, `Volume`, `Symbol`.  
- Variáveis criadas: retornos simples e logarítmicos (`ret`, `logret`), lags de preços e retornos (1, 2, 3, 5, 10, 20 dias), médias móveis (`ma5`, `ma10`, `ma20`), volatilidades (`std5`, `std10`, `std20`), efeitos de calendário (`dow`, `month`).  
- Target: `y` = preço de fechamento do próximo dia (t+1).  

---

## Preparação de Dados

- O dataset foi dividido em **treino (80%)** e **teste (20%)**, respeitando a ordem temporal.  
- Não foi utilizada validação cruzada tradicional pois, em problemas de séries temporais, ela viola a dependência temporal dos dados e pode gerar data leakage. Optou-se por uma divisão     temporal (treino: primeiros 80%, teste: últimos 20%) que reflete melhor a aplicação real do modelo., mas houve busca de hiperparâmetros por GridSearch em Ridge, Random Forest e     XGBoost.  
- Para o Ridge Regression foi aplicada **padronização dos atributos**.  
- Features irrelevantes (como `Date`, `y`) foram removidas do treino.  

---

## Modelagem e Treinamento

**Modelos testados:**  
- **Baseline 1:** Naive (y_{t+1} = preço de fechamento do dia anterior).  
- **Baseline 2:** Média móvel de 5 dias.  
- **Modelos supervisionados:** Ridge Regression, Random Forest, XGBoost.  

**Ajuste de hiperparâmetros:**  
- Ridge: parâmetro de regularização α.  
- Random Forest: número de árvores e profundidade.  
- XGBoost: taxa de aprendizado, número de estimadores e profundidade.  

**Treinamento e observações:**  
- O **Ridge Regression** apresentou melhor desempenho em termos de MAE e MAPE.  
- Random Forest e XGBoost tiveram resultados razoáveis, mas não superaram o Ridge.  
- Não foi observado underfitting; os resultados refletem a complexidade esperada para previsões de séries financeiras.  

---

## Avaliação de Resultados

**Métricas utilizadas:**  
- MAE (Mean Absolute Error).  
- RMSE (Root Mean Squared Error).  
- MAPE (Mean Absolute Percentage Error).  

**Resultados obtidos:**  
- **Baseline Naive:** MAE = 0.8080 | RMSE = 1.3794.  
- **Baseline MA5:** MAE = 1.0763 | RMSE = 1.6882.  
- **Melhor modelo — Ridge:** MAE = 1.0616 | RMSE = 1.9065 | MAPE = 3.07%.  

**Conclusões:**  
- O Ridge foi o modelo de melhor desempenho geral, mesmo que o baseline Naive tenha apresentado MAE ligeiramente inferior.  
- O MAPE em torno de 3% indica previsões com boa precisão relativa.  
- O modelo conseguiu capturar tendências, mas ainda há desafios devido à alta volatilidade do mercado de ações.  

---

## Previsão Operacional (t+1)

O notebook foi atualizado para estimar a **cotação de fechamento do próximo dia útil** com base no melhor modelo selecionado.  

**Resultado mais recente:**  
- **Próximo dia útil:** 2025-02-14.  
- **Previsão de fechamento (t+1) — Ridge:** **R$ 37,43**.  

> Observação: a função `BDay(1)` foi usada para avançar ao próximo dia útil, sem considerar feriados da B3. Para cenários de produção recomenda-se usar calendários oficiais de mercado.  

---

## Melhor Solução Encontrada

A **Ridge Regression** foi considerada a melhor solução prática por equilibrar simplicidade, interpretabilidade e robustez, atingindo erros médios aceitáveis e previsões consistentes para aplicações de curto prazo.  

