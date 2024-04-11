
# Análise de Dados sobre pacientes com Acidente Vascular Cerebral (AVC)

Este notebook tem como objetivo analisar uma base de dados sobre AVC, focando em diferentes aspectos como tipo de trabalho, idade, gênero, e BMI (Índice de Massa Corporal), para entender melhor a correlação desses fatores com a incidência de AVCs.

## Atividades

### 1. Carregar as bibliotecas e fazendo filtros iniciais

Vamos começar explorando a relação entre o tipo de trabalho e o número de casos de AVC. O objetivo é entender se certos tipos de trabalho estão mais associados a um risco elevado de AVC.
Primeiro, vamos importar as bilbiotecas a serem utilizadas e fazer filtros iniciais.

```python
import missingno as msno
import altair as alt
import pandas as pd

df = pd.read_csv('healthcare-dataset-stroke-data.csv')

df_adults_with_stroke = df[(df['stroke'] == 1) & (df['age'] > 18)]

msno.matrix(df)
```

### 2. Gráfico da Quantidade de pacientes com AVCs por Tipo de trabalho

Será que o ramo de trabalho dos pacientes dessa base pode nos dizer algo a respeito da quantida de pacientes que tiveram AVC?

```python
chart1 = alt.Chart(df_adults_with_stroke).mark_bar().encode(
    x=alt.X('work_type', title='Tipo de Trabalho'),  # Atualizado para incluir título e ocultar a legenda
    y=alt.Y('count():Q', title='Contagem de Pacientes'),  # Atualizado para incluir o título personalizado
    color=alt.Color('work_type', legend=None),  # Oculta a legenda de cores
    tooltip=['work_type', 'count()']
).properties(
    title='Número de Pacientes com AVC por Tipo de Trabalho',
    width=600  # Aumenta a largura da área do gráfico
)

chart1
```

### 3. Gráfico da Quantidade de pacientes com AVCs por Idade

A idade é um fator de risco bem conhecido para o AVC. Esta análise visa visualizar a distribuição de casos de AVC em diferentes faixas etárias.

```python
chart2 = alt.Chart(df_adults_with_stroke).mark_bar().encode(
    x=alt.X('age', bin=True, title='Idade'),
    y=alt.Y('count()', title='Quantidade de Pacientes'),
    tooltip=['count()', alt.Tooltip('age', bin=True)]
).properties(title='Quantidade de Pacientes que tiveram AVCs por Idade')

chart2
```

### 3. Gráfico com a Porcentagem de Pessoas do Sexo Masculino e Feminino com AVC

O gênero pode influenciar o risco de AVC. Por isso, vamos analisar a porcentagem de casos de AVC entre os gêneros masculino e feminino.

```python
chart3 = alt.Chart(gender_counts).mark_bar().encode(
    x=alt.X('gender', title='Sexo'),
    y=alt.Y('percentage:Q', title='Porcentagem', axis=alt.Axis(format='.0%'), scale=alt.Scale(domain=[0, 1])),
    color=alt.Color('gender', legend=None),
    tooltip=['gender', alt.Tooltip('percentage:Q', format='.2%')]
).properties(title='Porcentagem de Pessoas que Tiveram AVC por Sexo', width=400)

chart3
```

### 4. Gráfico relacionando a idade e o sexo de pessoas que tiveram AVC

Explorando a interação entre duas variáveis na quantidade de pacientes com AVC.

```python
# Agrupando por idade e gênero, contando o número de AVCs
df_grouped = df_adults_with_stroke.groupby(['age', 'gender']).size().reset_index(name='count_avc')

# Criando o gráfico
chart5 = alt.Chart(df_grouped).mark_circle().encode(
    x=alt.X('age', title='Idade'),
    y=alt.Y('gender', title='Gênero'),
    size=alt.Size('count_avc:Q', title='Quantidade de Pessoas com AVC', legend=None),  # Oculta a legenda
    color=alt.Color('gender', title='Gênero'),
    tooltip=['age', 'gender', 'count_avc']
).properties(title='Correlação entre Idade, Gênero e Número de Pessoas com AVC', height=150)


chart5
```

### 5. Gráfico relacionando a idade, o gênero e o sexo de pessoas que tiveram AVC

Explorando a interação entre 3 variáveis na quantidade de pacientes com AVC.

```python
# Agrupando por idade, gênero e tipo de trabalho, contando o número de AVCs
df_grouped = df_adults_with_stroke.groupby(['age', 'gender', 'work_type']).size().reset_index(name='count_avc')

# Criando o gráfico
chart = alt.Chart(df_grouped).mark_circle().encode(
    x=alt.X('age', title='Idade'),
    y=alt.Y('gender', title='Gênero'),
    size=alt.Size('count_avc:Q', title='Quantidade de Pessoas com AVC', scale=alt.Scale(range=[0, 1000]), legend=None),
    color=alt.Color('gender', title='Gênero'),
    tooltip=['age', 'gender', 'work_type', 'count_avc'],
    column=alt.Column('work_type', title='Tipo de Trabalho')
).properties(
    title='Relação entre Idade, Gênero, Tipo de Trabalho e Número de Pessoas com AVC',
    width=180,
    height=180
)

chart
```
