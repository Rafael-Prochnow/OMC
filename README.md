
import pandas as pd
import numpy as np

# Inicializa um dicionário para armazenar as variações
variations = []

# Loop para as colunas, excluindo a coluna de clusters 'clus'
for col in df.drop('clus', axis=1).columns:
    # Verifica o tipo de dado da coluna
    if df.dtypes[col] == object:
        # Para variáveis categóricas, calcula os percentuais por cluster
        for cl in np.sort(df.clus.unique()):
            vc = 100 * df.loc[df.clus == cl, col].value_counts(normalize=True)
            vc_total = 100 * df[col].value_counts(normalize=True)
            # Calcula a variação absoluta entre os clusters e o total
            for cat in vc.index:
                cluster_pct = vc.get(cat, 0)
                total_pct = vc_total.get(cat, 0)
                variation = abs(cluster_pct - total_pct)
                variations.append({'Cluster': cl, 'Coluna': col, 'Categoria': cat, 'Variação (%)': variation})
    else:
        # Para variáveis numéricas, calcula a média por cluster
        for cl in np.sort(df.clus.unique()):
            cluster_mean = df.loc[df.clus == cl, col].mean()
            total_mean = df[col].mean()
            # Calcula a variação entre a média do cluster e o total
            variation = abs(cluster_mean - total_mean)
            variations.append({'Cluster': cl, 'Coluna': col, 'Média': cluster_mean, 'Variação': variation})

# Cria um DataFrame para exibir as principais variações
variations_df = pd.DataFrame(variations)

# Filtra as maiores variações
top_variations = variations_df.sort_values(by='Variação (%)', ascending=False).head(10)

# Exibe as maiores variações
print(top_variations)




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
