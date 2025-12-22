README - MVP Engenharia de Dados: Análise Histórica B3
Instituição: PUC-RJ
Disciplina: Engenharia de Dados
Caminho do Projeto: /Volumes/workspace/default/bovespa_stocks/

1. Objetivo do Trabalho
O objetivo deste MVP é construir um pipeline de dados robusto e escalável para analisar o comportamento dos principais ativos da Bovespa. O projeto visa transformar dados brutos em um modelo analítico capaz de responder questões sobre liquidez, risco e sazonalidade do mercado financeiro brasileiro.

2. Coleta e Ingestão (Camada Bronze)
Os dados foram obtidos a partir do arquivo bovespa_stocks.csv. A ingestão foi realizada utilizando Apache Spark, com uma lógica de detecção automática de delimitadores (tratando vírgula ou ponto e vírgula) para garantir a resiliência do pipeline.

Técnica: O arquivo foi carregado sem transformações para a tabela default.b3_bronze, preservando a linhagem original dos dados.

Volume: Foram ingeridos 1.031.282 registros.

3. Modelagem e Transformação (Camada Silver)
Nesta fase, o dado foi higienizado e enriquecido com métricas de engenharia financeira:

Normalização de Data: Conversão de strings (formatos ddMMyyyy e dd/MM/yyyy) para o tipo Date oficial.

Engenharia de Atributos:

daily_return: Cálculo da variação percentual diária.

volatility_21d: Cálculo do desvio padrão móvel (volatilidade) em janelas de 21 dias úteis.

Esquema Estrela (Star Schema): Os dados foram organizados em dimensões (dim_date, dim_symbol) e tabelas fato (fact_daily_quotes).

4. Agregação e Organização (Camada Gold)
Conforme requisito de organização, foi criada a tabela default.fact_monthly_quotes. Os dados foram agrupados por Ativo e Período (Mês/Ano):

Liquidez: Calculada através da Soma (SUM) do volume negociado no mês.

Preço e Risco: Calculados através da Média (AVG) dos preços de fechamento e da volatilidade.

5. Análise e Respostas às Perguntas do MVP
a. Qualidade dos Dados (Pergunta 5)
A qualidade é Suficiente e Confiável. O pipeline implementou uma flag de qualidade (dq_row_ok).

Resultado: 99,99% dos dados foram considerados válidos. Apenas 12 registros (0,001%) foram descartados por inconsistências de data ou preço, garantindo que as análises subsequentes não fossem distorcidas por ruídos.
​

b. Comportamento do Mercado e Volatilidade (Perguntas 1 e 2)
Através da tabela Gold agregada, observamos que o mercado apresentou ciclos claros de expansão e contração.

Volatilidade: Identificamos picos de risco elevados (Risco Médio > 0.15) em períodos específicos, visíveis na coluna avg_volatility.

c. Distribuição e Sazonalidade (Perguntas 3 e 4)
Distribuição: Os retornos diários apresentam uma distribuição com caudas longas, típica de ativos financeiros (curtose elevada).

Sazonalidade: A consulta SQL por mês (GROUP BY mes) revelou que os meses de dezembro apresentam, historicamente, médias de retorno superiores à média anual.

d. Rankings Top 20 (Pergunta 6)
Ativos com Maior Volume (Liquidez): Os ativos I4, PETR4 e MGLU3 lideram o ranking de volume acumulado, sendo os pilares de liquidez do dataset.
​

Ativos com Maior Volatilidade (Risco): O ativo OSXB3 apresentou a maior volatilidade média, destacando-se como o papel de maior risco no período analisado.

6. Autoavaliação
O trabalho atingiu todos os objetivos propostos. A principal dificuldade foi o tratamento de formatos de data divergentes, resolvido com a função coalesce. O uso de camadas (Medallion) e a modelagem Estrela agregada por período permitiram que o processamento de mais de 1 milhão de linhas fosse executado de forma performática no Databricks.

Instruções para Visualização das Evidências:
Acesse o workbook anexo.

Observe a Célula 2 para evidência de carga da Camada Bronze.

Observe a Célula 4 para ver a lógica de agregação por Ativo e Período.

As Células 5 e 6 (SQL) contêm os gráficos que respondem visualmente aos rankings e tendências do IBOV.
