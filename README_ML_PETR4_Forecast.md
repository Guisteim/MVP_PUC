# README — MVP PETR4 Forecast

## Definição do Problema

**Descrição do problema:**  
O objetivo do projeto é prever o preço de fechamento do próximo dia (t+1) da ação PETR4, utilizando dados históricos de preços, volume e indicadores derivados.

**Premissas e hipóteses:**  
- O comportamento passado do preço da ação contém informações úteis para prever o preço do próximo dia.  
- Indicadores técnicos (médias móveis, volatilidade, retornos defasados) ajudam a melhorar a capacidade preditiva.  
- A previsão é de curto prazo (um dia à frente), não contemplando horizontes maiores.  

**Restrições e condições de seleção dos dados:**  
- Dados exclusivamente de PETR4 (ações da Petrobras).  
- Apenas variáveis derivadas do histórico da própria ação foram utilizadas (sem dados macroeconômicos ou de outras empresas).  
- Período longo (mais de 10 anos de histórico), com divisão temporal (últimos 20% reservados para teste).  

**Descrição do dataset:**  
- Variáveis originais: `Date`, `Open`, `High`, `Low`, `Close`, `Volume`, `Symbol`.  
- Variáveis derivadas: retornos simples e logarítmicos, defasagens (1,2,3,5,10,20 dias), médias móveis (5,10,20), volatilidades (5,10,20), variáveis de calendário (`dow`, `month`).  
- Target: `y` = preço de fechamento do próximo dia.  

---

## Preparação de Dados

- O dataset foi dividido em treino (80%) e teste (20%), respeitando a ordem temporal.  
- Não foi utilizada validação cruzada temporal clássica, mas GridSearchCV foi empregado em alguns modelos com esquema de validação temporal.  
- Foram aplicadas transformações de normalização/padronização apenas para modelos lineares (Ridge).  
- Feature selection: variáveis irrelevantes foram descartadas (ex.: `Date`, `y`).  

---

## Modelagem e Treinamento

**Modelos testados:**  
- Baseline 1: Modelo ingênuo (previsão = preço do dia anterior).  
- Baseline 2: Média móvel de 5 dias.  
- Modelos supervisionados: Ridge Regression, Random Forest, XGBoost.  

**Ajuste de hiperparâmetros:**  
- Ridge: validação para parâmetro de regularização.  
- Random Forest: número de árvores e profundidade.  
- XGBoost: taxa de aprendizado, número de estimadores e profundidade.  

**Treinamento e observações:**  
- O modelo Ridge apresentou melhor desempenho em termos de MAE e RMSE.  
- Não houve evidência clara de underfitting.  
- Ensembles (Random Forest e XGBoost) tiveram performance competitiva, mas não superaram o Ridge.  

---

## Avaliação de Resultados

**Métricas utilizadas:**  
- MAE (Mean Absolute Error).  
- RMSE (Root Mean Squared Error).  
- MAPE (Mean Absolute Percentage Error).  

**Resultados:**  
- **Baseline Naive:** MAE = 0.7458 | RMSE = 1.0357.  
- **Baseline MA5:** MAE = 0.9237 | RMSE = 1.2121.  
- **Melhor modelo — Ridge:** MAE = 0.5295 | RMSE = 0.7503 | MAPE = 2.03%.  

**Conclusões:**  
- O modelo Ridge foi a melhor solução, reduzindo significativamente o erro em relação aos baselines.  
- A previsão conseguiu manter erros percentuais médios próximos de 2%, o que é adequado para aplicações práticas.  
- Não foram detectados sinais severos de overfitting.  

---

## Melhor Solução Encontrada

O modelo **Ridge Regression** foi selecionado como melhor solução para previsão do preço de fechamento de PETR4 (t+1), devido à sua robustez, simplicidade e performance superior frente aos modelos de referência (Naive e MA5) e outros algoritmos testados.  

