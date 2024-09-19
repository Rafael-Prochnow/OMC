import pandas as pd
import numpy as np

# Criando o DataFrame original
data = {
    'Col_1': [0.807167, 0.605319, 0.569760, 0.939346, 0.455739, 0.511409],
    'Col_2': [0.770902, 0.448547, 0.703526, 0.135167, 0.467528, np.nan],
    'Col_3': [0.327308, 0.115701, 0.527340, 0.350031, np.nan, np.nan],
    'Col_4': [0.461648, 0.677750, 0.644858, np.nan, np.nan, np.nan],
    'Col_5': [0.446126, 0.534371, np.nan, np.nan, np.nan, np.nan],
    'Col_6': [0.223795, np.nan, np.nan, np.nan, np.nan, np.nan]
}

df = pd.DataFrame(data, index=['Row_1', 'Row_2', 'Row_3', 'Row_4', 'Row_5', 'Row_6'])

# DataFrame para armazenar os resultados
result_df = pd.DataFrame(columns=['A', 'B'])

# Função para pegar a diagonal a partir de uma coluna específica
def get_diagonal_sum_from_column(start_col):
    diagonal_values = [df.iloc[i, start_col - i] for i in range(start_col + 1) if start_col - i >= 0]
    diagonal_sum = sum([val for val in diagonal_values if pd.notnull(val)])
    return diagonal_values, diagonal_sum

# Iterar sobre as colunas e calcular a soma das diagonais
for start_col in range(len(df.columns)-1, -1, -1):
    diagonal_values, diagonal_sum = get_diagonal_sum_from_column(start_col)
    
    # Verificar se a diagonal tem valores não nulos
    if not all(pd.isnull(diagonal_values)):
        # Criar um DataFrame temporário para adicionar ao resultado
        temp_df = pd.DataFrame({'A': [f'Col_{start_col + 1}'], 'B': [diagonal_sum]})
        
        # Garantir que o DataFrame temporário não seja totalmente vazio antes de concatenar
        if not temp_df.isnull().all().all():
            result_df = pd.concat([result_df, temp_df], ignore_index=True)

# Exibir o DataFrame resultante
print(result_df)
