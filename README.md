# Este projeto tem como objetivo analisar e gerenciar a distribuição de uniformes para servidores, com base em uma planilha de dados em Excel. A análise é realizada utilizando a biblioteca Pandas para manipulação dos dados, Matplotlib e Seaborn para visualizações gráficas. O código segue os seguintes passos principais:

# Carregamento dos Dados:
A base de dados é carregada a partir de um arquivo Excel (Uniformes.xlsx), contendo informações sobre os servidores e os itens de uniforme recebidos.

# Filtragem de Dados:

Servidores com todos os itens recebidos: Filtra os servidores que receberam todos os itens de uniforme (marcados como "RECEBIDO").

Servidores com pendências: Identifica servidores que têm pelo menos um item marcado como "NÃO RECEBIDO" ou "PARCIAL".

Listagem de itens faltantes: Cria uma coluna adicional que lista os itens que estão pendentes para cada servidor.

# Exportação dos Resultados:
Os resultados são exportados para um arquivo Excel com duas abas: uma para servidores sem pendências e outra para servidores com pendências.

# Visualização Gráfica:
Um gráfico de barras empilhadas é gerado para visualizar o status de distribuição de cada item de uniforme (RECEBIDO, NÃO RECEBIDO, PARCIAL), permitindo uma análise rápida e intuitiva.

# Conclusão:
O projeto permite identificar falhas na distribuição de uniformes, listar servidores com pendências e gerar insights para melhorar o processo de entrega. Além disso, os resultados são organizados e exportados de forma clara para facilitar a tomada de decisões.

# Objetivo Principal:
Automatizar a análise da distribuição de uniformes, identificar pendências e fornecer relatórios e visualizações que auxiliem na gestão eficiente dos recursos.

# Tecnologias Utilizadas:
Pandas: Para manipulação e análise de dados.

# Matplotlib/Seaborn: Para criação de gráficos e visualizações.

Excel: Para armazenamento dos dados de entrada e exportação dos resultados.

# Este projeto é uma solução eficiente para gestão de inventário e distribuição de recursos, com foco em clareza, organização e tomada de decisão baseada em dados.
