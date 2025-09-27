MVP — Previsão do Preço da Ação PETR4 (Bovespa)
1. Definição do Problema

Descrição do problema:
O objetivo deste projeto é prever o preço de fechamento diário da ação PETR4 (Petrobras), utilizando dados históricos de mercado.

Tipo de problema:
Este é um problema de aprendizado supervisionado — regressão, pois desejamos prever uma variável numérica contínua (preço de fechamento).

Premissas / hipóteses:

O preço de fechamento é influenciado por variáveis como abertura, máxima, mínima, volume e retornos passados.

Padrões históricos podem fornecer informações úteis para prever preços futuros.

Não há eventos externos extraordinários modelados (ex.: crises políticas, guerras, choques de petróleo).

Restrições / condições dos dados:

O dataset contém apenas dados da ação PETR4, eliminando a necessidade de atributos categóricos como Symbol.

As datas foram mantidas apenas como índice temporal, não como variável de entrada.

Atributos do dataset:

Date: data da negociação (índice temporal).

Open: preço de abertura.

High: preço máximo do dia.

Low: preço mínimo do dia.

Close: preço de fechamento (variável alvo).

Volume: volume negociado.

Atributos derivados criados:

Return: retorno diário percentual.

MA5, MA10: médias móveis de 5 e 10 dias.

2. Preparação de Dados

Divisão treino/teste:

Os dados foram separados de forma temporal em 80% treino (2515 instâncias) e 20% teste (628 instâncias).

Essa divisão respeita a ordem cronológica, evitando data leakage.

Validação cruzada:

Não foi utilizada validação cruzada tradicional, pois em séries temporais isso causa mistura de informações do futuro com o passado.

Em vez disso, utilizou-se a divisão temporal e baselines comparativos.

Transformações de dados:

Normalização (MinMaxScaler) → aplicada em modelos sensíveis a escala, como KNN.

Padronização (StandardScaler) → aplicada em modelos lineares como regressão linear e Ridge.

Seleção de atributos:

Atributos descartados: Symbol e Date.

Atributos mantidos: Open, High, Low, Volume.

Atributos criados: Return, MA5, MA10.

3. Modelagem e Treinamento

Algoritmos escolhidos:

Baseline Naive: previsão do preço de amanhã = preço de hoje.

Baseline MA5: previsão do preço de amanhã = média móvel dos últimos 5 dias.

Regressão Linear e Ridge Regression: modelos clássicos de regressão para prever Close.

Outros algoritmos poderiam ser testados (SVR, Random Forest, LSTM), mas optamos pela simplicidade no MVP.

Hiperparâmetros:

Ridge Regression → testado com valores de regularização alpha, escolhido o que reduziu overfitting.

Treinamento:

O modelo foi ajustado sobre os dados de treino.

Não houve problema de underfitting, mas observou-se que baselines simples já fornecem bons resultados, indicando previsibilidade limitada dos dados.

4. Avaliação de Resultados

Métricas utilizadas:

MAE (Erro Absoluto Médio): mede erro médio absoluto.

RMSE (Raiz do Erro Quadrático Médio): penaliza mais erros grandes.

MAPE (Erro Percentual Absoluto Médio): expressa erro relativo em %.

Resultados:

Modelo	MAE	RMSE	MAPE
Baseline Naive	0.7458	1.0357	-
Baseline MA5	0.9237	1.2121	-
Ridge	0.5295	0.7503	2.03%

Análise dos resultados:

O modelo Ridge superou os baselines em todas as métricas, reduzindo o erro absoluto médio em aproximadamente 30% em relação ao baseline mais simples (Naive).

O MAPE de 2.03% significa que, em média, o erro relativo foi de apenas 2% sobre o preço da ação — um bom desempenho para um MVP.

O modelo não apresentou sinais severos de overfitting.

5. Conclusão

Este MVP demonstrou a viabilidade de prever o preço de fechamento diário da ação PETR4 utilizando aprendizado supervisionado.

Principais pontos:

A divisão temporal dos dados foi respeitada para evitar vazamento de informação.

Comparações com baselines mostraram que o modelo Ridge traz valor real.

As métricas alcançadas são adequadas para um primeiro protótipo.

Limitações:

O modelo não considera fatores externos (ex.: indicadores macroeconômicos, preço do petróleo, notícias políticas).

Modelos mais sofisticados (LSTM, CNNs para séries temporais, ensembles) poderiam capturar padrões mais complexos.
