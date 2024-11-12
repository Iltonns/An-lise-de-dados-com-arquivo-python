# Importando bibliotecas
import sqlite3
import pandas as pd

# Passo 1: Abrindo a conexão com o banco de dados e coletando os dados
con = sqlite3.connect("database.db")

# Consulta SQL para unir tabelas de atividade de voo e histórico de fidelidade
consulta_atividade = """
  SELECT
    *
  FROM
    flight_activity fa
  LEFT JOIN
    flight_loyalty_history flh ON (fa.loyalty_number = flh.loyalty_number)
"""

# Executa a consulta e carrega os dados em um DataFrame
df_atividade = pd.read_sql_query(consulta_atividade, con)

# Visualiza as primeiras linhas do DataFrame resultante
df_atividade.head()

# Fecha a conexão com o banco de dados
con.close()

# Verifica o tipo do objeto resultante e exibe as primeiras linhas novamente
print(type(df_atividade))
df_atividade.head()

# Seleciona colunas específicas para análise
colunas = ['loyalty_number', 'year', 'month', 'total_flights']
df2 = df_atividade.loc[:, colunas]
df2.head()

# Passo 2: Análise básica do DataFrame
# Conta o número de linhas e colunas presentes
print("Total de linhas:", df_atividade.shape[0])
print("Total de colunas:", df_atividade.shape[1])

# Exibe informações gerais do DataFrame
df_atividade.info()

# Passo 3: Cálculo de estatísticas específicas
# Soma total de voos na coluna 'total_flights'
coluna = 'total_flights'
print("Soma de voos totais:", df_atividade.loc[:, coluna].sum())

# Calcula a média, mínima e máxima distância percorrida
coluna = 'distance'
print("Média de distância:", df_atividade.loc[:, coluna].mean())
print("Distância mínima:", df_atividade.loc[:, coluna].min())
print("Distância máxima:", df_atividade.loc[:, coluna].max())

# Verifica colunas com valores nulos e conta o número de nulos
print("Valores nulos por coluna:")
print(df_atividade.isna().sum())

# Passo 4: Seleção de colunas e remoção de dados faltantes
# Seleciona colunas de interesse para o modelo
colunas = ['year', 'month', 'flights_booked', 'flights_with_companions', 'total_flights', 'distance',
           'points_accumulated', 'points_redeemed', 'salary', 'clv', 'loyalty_card']

df_colunas_selecionadas = df_atividade.loc[:, colunas]

# Remove linhas com dados faltantes e verifica se há valores nulos
df_treinamento = df_colunas_selecionadas.dropna()
print("Valores nulos após remoção:", df_treinamento.isna().sum().sum())

# Exibe o total de linhas restantes após remoção de nulos e as primeiras linhas
print("Total de linhas após remoção de nulos:", df_treinamento.shape[0])
df_treinamento.head()

# Passo 5: Treinamento do modelo de árvore de decisão
from sklearn import tree as tr

# Define as variáveis de entrada (X) e alvo (y)
x = df_treinamento.drop(columns='loyalty_card')
y = df_treinamento['loyalty_card']

# Cria e treina o modelo de árvore de decisão
modelo = tr.DecisionTreeClassifier(max_depth=5)
modelo_treinado = modelo.fit(x, y)

# Visualiza a árvore de decisão treinada
tr.plot_tree(modelo_treinado, filled=True)

# Passo 6: Previsão de probabilidade de aquisição de cartões
# Seleciona uma amostra aleatória para prever a probabilidade de aquisição de cartões
x_novo = x.sample()
previsao = modelo_treinado.predict_proba(x_novo)

# Exibe a probabilidade de aquisição para cada tipo de cartão
print('Probabilidade - Aurora: {:.2f}% - Nova: {:.2f}% - Star: {:.2f}%'.format(
    100 * previsao[0, 0], 100 * previsao[0, 1], 100 * previsao[0, 2]))

# Passo 7: Criando uma Interface com Gradio
import gradio as gr
import numpy as np

# Função de previsão para a interface
def predict(*args):
    x_novo = np.array([args]).reshape(1, -1)
    previsao = modelo_treinado.predict_proba(x_novo)
    return {'Aurora': previsao[0, 0], 'Nova': previsao[0, 1], 'Star': previsao[0, 2]}

# Configurando a interface Gradio
with gr.Blocks() as demo:
    gr.Markdown("# Propensão de Compra de Cartões")

    with gr.Row():
        gr.Markdown("### Atributos do Cliente")
        with gr.Column():
            # Sliders para os atributos do cliente
            year = gr.Slider(label='Ano', minimum=2017, maximum=2018, step=1, randomize=True)
            month = gr.Slider(label='Mês', minimum=1, maximum=12, step=1, randomize=True)
            flights_booked = gr.Slider(label='Voos Reservados', minimum=0, maximum=21, step=1, randomize=True)
            flights_with_companions = gr.Slider(label='Voos com Acompanhantes', minimum=0, maximum=11, step=1, randomize=True)
            total_flights = gr.Slider(label='Total de Voos', minimum=0, maximum=32, step=1, randomize=True)
            distance = gr.Slider(label='Distância Percorrida', minimum=0, maximum=6293, step=1, randomize=True)
            points_accumulated = gr.Slider(label='Pontos Acumulados', minimum=0.0, maximum=676.5, step=0.1, randomize=True)
            salary = gr.Slider(label='Salário', minimum=58486.0, maximum=407228.0, step=1, randomize=True)
            clv = gr.Slider(label='Valor do Cliente (CLV)', minimum=2119.89, maximum=83325.38, step=0.1, randomize=True)

    # Botão de previsão e exibição do resultado
    predict_btn = gr.Button(value='Previsão')
    label = gr.Label("Propensão de Compra do Cliente")

    # Configuração do clique do botão para realizar a previsão
    predict_btn.click(
        fn=predict,
        inputs=[year, month, flights_booked, flights_with_companions, total_flights,
                distance, points_accumulated, salary, clv],
        outputs=[label]
    )

# Lança a aplicação com o Gradio
demo.launch(debug=True, share=False)
