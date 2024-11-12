# Passo 1: Importar base de dados
import pandas as pd 

# Carrega os dados de cancelamento de clientes a partir de um arquivo CSV
tabela = pd.read_csv('cancelamentos_sample.csv')

# Passo 2: Visualizar a base de dados
# Exibe uma amostra da tabela para verificar as primeiras informações
display(tabela)

# Remove a coluna 'CustomerID', que não é relevante para a análise
tabela = tabela.drop(columns=['CustomerID'], errors='ignore')

# Passo 3: Limpeza e verificação de dados
# Verifica informações gerais da tabela (tipo de dados, valores ausentes, etc.)
display(tabela.info())

# Remove linhas com valores ausentes para garantir uma análise precisa
tabela = tabela.dropna()

# Exibe novamente as informações para confirmar a limpeza dos dados
display(tabela.info())

# Passo 4: Análise inicial dos cancelamentos
# Conta o número de clientes que cancelaram e que não cancelaram
display(tabela['cancelou'].value_counts())

# Calcula a porcentagem de clientes que cancelaram
display(tabela['cancelou'].value_counts(normalize=True).map('{:.1%}'.format))

# Passo 5: Visualização dos dados
import matplotlib.pyplot as plt
import seaborn as sns

# Define uma paleta de cores para os gráficos, uma cor diferente para cada coluna
cores = sns.color_palette("husl", len(tabela.columns))

# Gera histogramas para cada coluna, visualizando a distribuição dos dados
for i, coluna in enumerate(tabela.columns):  
    plt.figure(figsize=(10, 6))
    plt.hist(tabela[coluna], bins=10, color=cores[i], edgecolor='black')
    plt.title(f'Distribuição dos Dados da Coluna: {coluna}', fontsize=16)
    plt.xlabel(coluna, fontsize=14)
    plt.ylabel('Frequência', fontsize=14)
    plt.grid(True)
    plt.show()
