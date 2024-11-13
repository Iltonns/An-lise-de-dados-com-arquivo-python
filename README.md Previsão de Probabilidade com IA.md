# Importação das bibliotecas necessárias
import sqlite3
import pandas as pd
import gradio as gr
import numpy as np
from sklearn import tree as tr
from matplotlib import pyplot as plt
%matplotlib inline

# Conexão com o banco de dados
conn = sqlite3.connect('database.db')

# Consulta para selecionar dados da atividade de voo e histórico de lealdade
consulta_atividade = """
SELECT 
    *
FROM 
    flight_activity fa 
LEFT JOIN 
    flight_loyalty_history flh 
ON 
    (fa.loyalty_number = flh.loyalty_number)
"""
df_atividade = pd.read_sql_query(consulta_atividade, conn)

# Exibir as primeiras linhas para verificar se a consulta foi bem-sucedida
df_atividade.head()

# Contagem de dados faltantes em cada coluna
df_atividade.isna().sum()

# Seleciona apenas as colunas numéricas para análise
colunas = [
    "year", "month", "flights_booked", "flights_with_companions", 
    "total_flights", "distance", "points_accumulated", "salary", 
    "clv", "loyalty_card"
]
df_colunas_numericas = df_atividade.loc[:, colunas]

# Remover linhas com dados faltantes
df_dados_completos = df_colunas_numericas.dropna()

# Verificar se ainda existem dados faltantes
df_dados_completos.isna().sum()

# Preparação dos dados para o treinamento
X_atributos = df_dados_completos.drop(columns="loyalty_card")
y_rotulos = df_dados_completos["loyalty_card"]

# Definição do modelo de Árvore de Decisão
modelo = tr.DecisionTreeClassifier(max_depth=4)

# Treinamento do modelo
modelo_treinado = modelo.fit(X_atributos, y_rotulos)

# Exibição da árvore de decisão treinada
plt.figure(figsize=(20, 10))
tr.plot_tree(modelo_treinado, filled=True)
plt.show()

# Previsão de lealdade para um novo cliente (exemplo)
X_novo = X_atributos.sample()  # Seleciona uma amostra dos atributos
previsao = modelo_treinado.predict_proba(X_novo)
print(
    "Probabilidade - Aurora: {:.1f}% - Nova: {:.1f}% - Star: {:.1f}%".format(
        100 * previsao[0][0], 
        100 * previsao[0][1], 
        100 * previsao[0][2]
    )
)

# Função para prever a lealdade do cliente com base nos atributos
def predict(*args):
    X_novo = np.array([args]).reshape(1, -1)
    previsao = modelo_treinado.predict_proba(X_novo)
    return {
        "Aurora": previsao[0][0], 
        "Nova": previsao[0][1], 
        "Star": previsao[0][2]
    }

# Interface gráfica com Gradio para entrada de dados e previsão
demo = gr.Interface(
    title="Projeto 01: Imersão na área de Dados",
    description="**Inteligência Artificial para calcular a propensão de compra de clientes**"
                "\n\n*Utilize os sliders para ajustar os valores de entrada.*",
    fn=predict,
    inputs=[
        gr.Radio([2017, 2018], label="year"),
        gr.Slider(label="month", minimum=1, maximum=12, step=1, randomize=True),
        gr.Slider(label="flights_booked", minimum=0, maximum=21, step=1, randomize=True),
        gr.Slider(label="flights_with_companions", minimum=0, maximum=11, step=1, randomize=True),
        gr.Slider(label="total_flights", minimum=0, maximum=32, step=1, randomize=True),
        gr.Slider(label="distance", minimum=0, maximum=6293, step=1, randomize=True),
        gr.Slider(label="points_accumulated", minimum=0.00, maximum=676.50, step=0.1, randomize=True),
        gr.Slider(label="salary", minimum=-58486.00, maximum=407228.00, step=0.1, randomize=True),
        gr.Slider(label="clv", minimum=2119.89, maximum=83325.38, step=0.1, randomize=True)
    ],
    outputs=gr.Label(label='Previsão'),
    allow_flagging="never"
)

# Lançamento do painel de interface para previsão online
demo.launch(share=True)
