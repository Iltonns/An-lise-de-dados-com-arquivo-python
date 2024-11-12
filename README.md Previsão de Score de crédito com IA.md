# Importar as bibliotecas necessárias
import pandas as pd
from sklearn.preprocessing import LabelEncoder
from sklearn.model_selection import train_test_split
from sklearn.ensemble import RandomForestClassifier  # Modelo de Árvore de Decisão
from sklearn.neighbors import KNeighborsClassifier  # Modelo de K-Nearest Neighbors (KNN)
from sklearn.metrics import accuracy_score  # Para medir a acurácia

# Passo 1: Carregar e visualizar os dados
# Carrega a base de dados contendo informações dos clientes
tabela = pd.read_csv("clientes.csv")
display(tabela)

# Verifica se há valores vazios ou colunas com tipos incorretos
print(tabela.info())
print("Colunas da Tabela:", tabela.columns)

# Passo 2: Preparação dos dados
# Codificador para transformar colunas categóricas em valores numéricos
codificador = LabelEncoder()

# Converte colunas categóricas em valores numéricos, exceto a coluna 'score_credito' que é o alvo
for coluna in tabela.columns:
    if tabela[coluna].dtype == "object" and coluna != "score_credito":
        tabela[coluna] = codificador.fit_transform(tabela[coluna])

# Verifica novamente a estrutura dos dados após a codificação
print(tabela.info())

# Passo 3: Definir as variáveis X e y
# X contém os dados para treino (todas as colunas exceto 'score_credito' e 'id_cliente')
x = tabela.drop(["score_credito", "id_cliente"], axis=1)
# y é a variável alvo que queremos prever (score de crédito)
y = tabela["score_credito"]

# Passo 4: Dividir os dados em conjuntos de treino e teste
# Separa os dados em 70% para treino e 30% para teste
x_treino, x_teste, y_treino, y_teste = train_test_split(x, y, test_size=0.3, random_state=1)

# Passo 5: Treinar os modelos de IA
# Cria modelos de árvore de decisão e KNN
modelo_arvore = RandomForestClassifier()
modelo_knn = KNeighborsClassifier()

# Treina ambos os modelos
modelo_arvore.fit(x_treino, y_treino)
modelo_knn.fit(x_treino, y_treino)

# Passo 6: Acurácia de referência para comparação
# Calcula a acurácia se o modelo sempre previsse a categoria mais comum ('Standard')
contagem_scores = tabela["score_credito"].value_counts()
print("Acurácia se o modelo chutasse tudo 'Standard':", contagem_scores['Standard'] / sum(contagem_scores))

# Passo 7: Avaliar a performance dos modelos
# Gera previsões para os dados de teste com ambos os modelos
previsao_arvore = modelo_arvore.predict(x_teste)
previsao_knn = modelo_knn.predict(x_teste.to_numpy())

# Compara as previsões com os valores reais e exibe a acurácia
print("Acurácia da Árvore de Decisão:", accuracy_score(y_teste, previsao_arvore))
print("Acurácia do KNN:", accuracy_score(y_teste, previsao_knn))

# Passo 8: Preparação para novas previsões
# Carrega a base de dados original para prever score de crédito dos clientes
clientes = pd.read_csv("clientes.csv")
display(clientes)

# Codifica novamente as colunas categóricas (exceto 'score_credito')
for coluna in clientes.columns:
    if clientes[coluna].dtype == "object" and coluna != "score_credito":
        clientes[coluna] = codificador.fit_transform(clientes[coluna])

# Seleciona apenas 'id_cliente' e 'score_credito' para visualização final
clientes_selecionados = clientes[['id_cliente', 'score_credito']]
display(clientes_selecionados)

# Remove 'id_cliente' e 'score_credito' para prever score de crédito com o modelo treinado
clientes_sem_id = clientes.drop(["id_cliente", "score_credito"], axis=1)

# Passo 9: Previsões com o modelo Random Forest
# Calcula as previsões para todos os clientes e exibe
previsoes = modelo_arvore.predict(clientes_sem_id)
print("Previsões para os clientes:", previsoes)

# Passo 10: Importância das características no modelo Random Forest
# Identifica as características mais importantes para o modelo Random Forest
colunas = list(x_teste.columns)
importancia = pd.DataFrame(index=colunas, data=modelo_arvore.feature_importances_)
importancia = importancia * 100  # Converte para percentual
print("Importância das características para o score de crédito:")
print(importancia)
