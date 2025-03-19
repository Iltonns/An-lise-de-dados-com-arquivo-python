Importar as bibliotecas permitidas
importar pandas como pd importar matplotlib.pyplot como plt importar seaborn como sns

Passo 1: Carregar uma base de dados
Os dados do arquivo Excel contendo a lista de servidores e os itens de uniforme recebidos
tabela_uniformes = pd.read_excel('Uniformes.xlsx')

Visualize a tabela de uniformes fornecidos e suas informações gerais
display(tabela_uniformes) display(tabela_uniformes.info())

Exibe as colunas relevantes para a análise de uniformes recebidos
display(tabela_uniformes[['Nome Completo do Servidor', 'Calça', 'Camiseta Preta', 'Combat Shirt', 'Camisa Polo', 'Boné', 'Cinto', ' Coldre', 'Lanterna', 'Porta Carregador', 'Bota']])

Passo 2: Filtrar servidores com todos os itens recebidos
Defina as colunas que representam os itens de uniforme para facilitar a análise
colunas_uniformes = ['Calça', 'Camiseta Preta', 'Camisa de Combate', 'Camisa Polo', 'Boné', 'Cinto', 'Coldre', 'Lanterna', 'Porta Carregador', 'Bota']

Filtra servidores que receberam todos os itens (indicado pela presença de "RECEBIDO" em todas as colunas)
servidores_completos = tabela_uniformes.loc[(tabela_uniformes[colunas_uniformes] == 'RECEBIDO').all(axis=1)]

Exiba uma lista de servidores que receberam todos os itens
display(servidores_completos[['Nome Completo do Servidor']])

Passo 3: Filtrar servidores com pendências
Filtre servidores que tenham pelo menos um item marcado como "NÃO RECEBIDO" ou "PARCIAL"
servidores_pendentes = tabela_uniformes.loc[(tabela_uniformes[colunas_uniformes] != 'RECEBIDO').any(axis=1)]

Exiba uma lista de servidores com pendências de uniformes
display(servidores_pendentes[['Nome Completo do Servidor']])

Passo 4: Listar os itens que faltam no servidor
Crie uma nova coluna que lista os itens que faltam para cada servidor
tabela_uniformes['Itens Faltantes'] = tabela_uniformes[colunas_uniformes].apply( lambda row: ', '.join(row.index[row != 'RECEBIDO']), axis=1 )

Exibe os servidores com seus valiosos itens faltantes
servidores_pendentes_com_itens = tabela_uniformes.loc[ (tabela_uniformes[colunas_uniformes] != 'RECEBIDO').any(axis=1), ['Nome Completo do Servidor', 'Itens Faltantes'] ] display(servidores_pendentes_com_itens)

Passo 5: Exportar resultados para o Excel
Crie um arquivo Excel com duas abas: uma com servidores sem pendências e outra com servidores que têm pendências
com pd.ExcelWriter('analise_uniformes.xlsx') como escritor: servidores_completos.to_excel(writer, sheet_name='Sem Pendências', index=False) servidores_pendentes_com_itens.to_excel(writer, sheet_name='Com Pendências', index=False)

Passo 6: Visualização do status de adequado por item
Contar a quantidade de servidores por status para cada item (RECEBIDO, NÃO RECEBIDO, PARCIAL)
status_counts = tabela_uniformes[colunas_uniformes].apply(pd.Series.value_counts)

Definir núcleos para o gráfico
cores_grafico = ['#66b3ff', '#ff9999', '#99ff99'] # Núcleos para cada status

Cria um gráfico de barras empilhadas para visualizar o status de cada item do uniforme
status_counts.T.plot(kind='bar', stacked=True, figsize=(12, 8), color=cores_grafico) plt.title('Status de Uniformes por Item', fontsize=16) plt.ylabel('Quantidade de Servidores', fontsize=14) plt.xlabel('Itens de Uniforme', fontsize=14) plt.legend(title='Status', loc='upper right') plt.show()
