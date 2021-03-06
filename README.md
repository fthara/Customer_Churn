# Customer Churn com Spark

Este projeto tem o objetivo de prever o custumer churn ou rotatividade de clientes em uma determinada empresa Operadora de Telecom. 

O Customer Churn refere-se a uma decisão tomada pelo cliente sobre o término do relacionamento comercial e também à perda de clientes. A fidelidade do cliente e a rotatividade de clientes sempre somam 100%. Se uma empresa tem uma taxa de fidelidade de 60%, então a taxa de perda de clientes é de 40%. De acordo com a regra de lucratividade do cliente 80/20, 20% dos clientes estão gerando 80% da receita. Portanto, é muito importante prever os usuários que provavelmente abandonarão o relacionamento comercial e os fatores que afetam as decisões do cliente.

Para iniciar o projeto precisamos primeiro ler carregar as bibliotecas básicas do python.


```python
#Bibliotecas para ler o dataframe e manipular os dados.
import pandas as pd
import numpy as np

#Bibliotecas para construir gráficos em Python.
import matplotlib.pyplot as plt
import seaborn as sns
```

Agora vamos carregar o dataset.


```python
# Permite ver todas as colunas e linhas do dataframe
pd.set_option('display.max_columns', None)
# Carregando o dataset
df = pd.read_csv("projeto4_telecom_treino.csv")
# Visualizando as 5 primeras linhas do dataset.
df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Unnamed: 0</th>
      <th>state</th>
      <th>account_length</th>
      <th>area_code</th>
      <th>international_plan</th>
      <th>voice_mail_plan</th>
      <th>number_vmail_messages</th>
      <th>total_day_minutes</th>
      <th>total_day_calls</th>
      <th>total_day_charge</th>
      <th>total_eve_minutes</th>
      <th>total_eve_calls</th>
      <th>total_eve_charge</th>
      <th>total_night_minutes</th>
      <th>total_night_calls</th>
      <th>total_night_charge</th>
      <th>total_intl_minutes</th>
      <th>total_intl_calls</th>
      <th>total_intl_charge</th>
      <th>number_customer_service_calls</th>
      <th>churn</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>KS</td>
      <td>128</td>
      <td>area_code_415</td>
      <td>no</td>
      <td>yes</td>
      <td>25</td>
      <td>265.1</td>
      <td>110</td>
      <td>45.07</td>
      <td>197.4</td>
      <td>99</td>
      <td>16.78</td>
      <td>244.7</td>
      <td>91</td>
      <td>11.01</td>
      <td>10.0</td>
      <td>3</td>
      <td>2.70</td>
      <td>1</td>
      <td>no</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>OH</td>
      <td>107</td>
      <td>area_code_415</td>
      <td>no</td>
      <td>yes</td>
      <td>26</td>
      <td>161.6</td>
      <td>123</td>
      <td>27.47</td>
      <td>195.5</td>
      <td>103</td>
      <td>16.62</td>
      <td>254.4</td>
      <td>103</td>
      <td>11.45</td>
      <td>13.7</td>
      <td>3</td>
      <td>3.70</td>
      <td>1</td>
      <td>no</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>NJ</td>
      <td>137</td>
      <td>area_code_415</td>
      <td>no</td>
      <td>no</td>
      <td>0</td>
      <td>243.4</td>
      <td>114</td>
      <td>41.38</td>
      <td>121.2</td>
      <td>110</td>
      <td>10.30</td>
      <td>162.6</td>
      <td>104</td>
      <td>7.32</td>
      <td>12.2</td>
      <td>5</td>
      <td>3.29</td>
      <td>0</td>
      <td>no</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>OH</td>
      <td>84</td>
      <td>area_code_408</td>
      <td>yes</td>
      <td>no</td>
      <td>0</td>
      <td>299.4</td>
      <td>71</td>
      <td>50.90</td>
      <td>61.9</td>
      <td>88</td>
      <td>5.26</td>
      <td>196.9</td>
      <td>89</td>
      <td>8.86</td>
      <td>6.6</td>
      <td>7</td>
      <td>1.78</td>
      <td>2</td>
      <td>no</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>OK</td>
      <td>75</td>
      <td>area_code_415</td>
      <td>yes</td>
      <td>no</td>
      <td>0</td>
      <td>166.7</td>
      <td>113</td>
      <td>28.34</td>
      <td>148.3</td>
      <td>122</td>
      <td>12.61</td>
      <td>186.9</td>
      <td>121</td>
      <td>8.41</td>
      <td>10.1</td>
      <td>3</td>
      <td>2.73</td>
      <td>3</td>
      <td>no</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Vizualizando o número de linhas e colunas do dataset.
df.shape
```




    (3333, 21)



Para começar nossa análise dos dados vamos verificar primeiro se existem valores nulos no dataset. Para isso irei usar a função isna(), que seleciona todos os valores NA de cada linha e sum() que soma os valores NA encontrados.


```python
df.isna().sum()
```




    Unnamed: 0                       0
    state                            0
    account_length                   0
    area_code                        0
    international_plan               0
    voice_mail_plan                  0
    number_vmail_messages            0
    total_day_minutes                0
    total_day_calls                  0
    total_day_charge                 0
    total_eve_minutes                0
    total_eve_calls                  0
    total_eve_charge                 0
    total_night_minutes              0
    total_night_calls                0
    total_night_charge               0
    total_intl_minutes               0
    total_intl_calls                 0
    total_intl_charge                0
    number_customer_service_calls    0
    churn                            0
    dtype: int64



Como podemos ver não existem valores NA em nosso dataset, portanto não precisamos excluir ou mudar algum valor por este motivo.

# Análise Explroatória

Vamos começar nossa análise exploratória com a coluna "Unnamed: 0". Aparentemente ela parece ser apenas Id's de números em sequência. Se esse for o caso ela será excluída do nosso modelo, pois não irá representar nada a ele.

Para ter certeza se essa feature contem apenas números sequenciais vou ler os valores únicos dela, colocar em uma lista e depois verificar o tamanho da lista. Se a lista tiver o mesmo tamanho da quantidade de linhas, significa que ela não possui valores repetidos e portanto não fará diferença para o modelo.


```python
unnamed = df['Unnamed: 0'].unique()
len(unnamed)
```




    3333




```python
# Permite visualizar as 5 últimas linhas do dataset
df.tail()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Unnamed: 0</th>
      <th>state</th>
      <th>account_length</th>
      <th>area_code</th>
      <th>international_plan</th>
      <th>voice_mail_plan</th>
      <th>number_vmail_messages</th>
      <th>total_day_minutes</th>
      <th>total_day_calls</th>
      <th>total_day_charge</th>
      <th>total_eve_minutes</th>
      <th>total_eve_calls</th>
      <th>total_eve_charge</th>
      <th>total_night_minutes</th>
      <th>total_night_calls</th>
      <th>total_night_charge</th>
      <th>total_intl_minutes</th>
      <th>total_intl_calls</th>
      <th>total_intl_charge</th>
      <th>number_customer_service_calls</th>
      <th>churn</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>3328</th>
      <td>3329</td>
      <td>AZ</td>
      <td>192</td>
      <td>area_code_415</td>
      <td>no</td>
      <td>yes</td>
      <td>36</td>
      <td>156.2</td>
      <td>77</td>
      <td>26.55</td>
      <td>215.5</td>
      <td>126</td>
      <td>18.32</td>
      <td>279.1</td>
      <td>83</td>
      <td>12.56</td>
      <td>9.9</td>
      <td>6</td>
      <td>2.67</td>
      <td>2</td>
      <td>no</td>
    </tr>
    <tr>
      <th>3329</th>
      <td>3330</td>
      <td>WV</td>
      <td>68</td>
      <td>area_code_415</td>
      <td>no</td>
      <td>no</td>
      <td>0</td>
      <td>231.1</td>
      <td>57</td>
      <td>39.29</td>
      <td>153.4</td>
      <td>55</td>
      <td>13.04</td>
      <td>191.3</td>
      <td>123</td>
      <td>8.61</td>
      <td>9.6</td>
      <td>4</td>
      <td>2.59</td>
      <td>3</td>
      <td>no</td>
    </tr>
    <tr>
      <th>3330</th>
      <td>3331</td>
      <td>RI</td>
      <td>28</td>
      <td>area_code_510</td>
      <td>no</td>
      <td>no</td>
      <td>0</td>
      <td>180.8</td>
      <td>109</td>
      <td>30.74</td>
      <td>288.8</td>
      <td>58</td>
      <td>24.55</td>
      <td>191.9</td>
      <td>91</td>
      <td>8.64</td>
      <td>14.1</td>
      <td>6</td>
      <td>3.81</td>
      <td>2</td>
      <td>no</td>
    </tr>
    <tr>
      <th>3331</th>
      <td>3332</td>
      <td>CT</td>
      <td>184</td>
      <td>area_code_510</td>
      <td>yes</td>
      <td>no</td>
      <td>0</td>
      <td>213.8</td>
      <td>105</td>
      <td>36.35</td>
      <td>159.6</td>
      <td>84</td>
      <td>13.57</td>
      <td>139.2</td>
      <td>137</td>
      <td>6.26</td>
      <td>5.0</td>
      <td>10</td>
      <td>1.35</td>
      <td>2</td>
      <td>no</td>
    </tr>
    <tr>
      <th>3332</th>
      <td>3333</td>
      <td>TN</td>
      <td>74</td>
      <td>area_code_415</td>
      <td>no</td>
      <td>yes</td>
      <td>25</td>
      <td>234.4</td>
      <td>113</td>
      <td>39.85</td>
      <td>265.9</td>
      <td>82</td>
      <td>22.60</td>
      <td>241.4</td>
      <td>77</td>
      <td>10.86</td>
      <td>13.7</td>
      <td>4</td>
      <td>3.70</td>
      <td>0</td>
      <td>no</td>
    </tr>
  </tbody>
</table>
</div>



Como eu suspeitava essa feature possui apenas valores em sequência, portanto a mesma será excluída de meu dataset.


```python
df.drop("Unnamed: 0", axis=1, inplace=True)
```

Agora iremos analizar a variável target "churn" que basicamente significa a tomada de descisão, positiva ou negativa, do cliente sair ou não da carteira da empresa. Vamos ver quantos valores positivos e negativos nosso dataset tem.


```python
#Gerando o gráfico
sns.set(style="whitegrid")
plt.figure(figsize=(10,6))

ax = sns.countplot(x=df.churn, data=df)
ax.xaxis.set_label_text("Tipo de Churn",fontdict= {'size':16})
ax.yaxis.set_label_text("Número de Churns", fontdict={'size':16})
ax.set_title("Quantidade de Churns", fontdict={'size':18})
plt.show()
```


![png](customer_churn_files/customer_churn_17_0.png)


Como podemos ver em nossa variável target, a maioria dos clientes não tem a pretenção de abandonar o serviço, mas para a empresa o foco é naqueles clientes que estão pensando em abandonar e oferecer algo para tentar fazê-lo desistir da ideia, portanto uma ferramenta que pode ser útil na realização do modelo preditivo é o balanceamento do dataset, evitando assim um grande número de falsos positivos.

Agora vamos começar a analizar as variáveis qualitativas do dataset, começando pela coluna "state"

É interessante saber quantos estados diferentes temos em nosso dataset e como eles estão divididos proporcionalmente. Para iremos fazer um gráfico para poder visualizar de forma mais clara.


```python
# Calculando o número de estados diferentes.
states = len(df.state.unique())
print("O dataset possui {} estados diferentes" .format(states))
#Gerando o gráfico
sns.set(style="whitegrid")
plt.figure(figsize=(10,15))

ax = sns.countplot(y=df.state, order = df.state.value_counts().index)
ax.xaxis.set_label_text("Número de Clientes",fontdict= {'size':16})
ax.yaxis.set_label_text("Estados", fontdict={'size':16})
ax.set_title("Quantidade Clientes por Estados", fontdict={'size':18})
plt.show()
```

    O dataset possui 51 estados diferentes



![png](customer_churn_files/customer_churn_19_1.png)


Podemos perceber que nosso dataset possui muitos estados diferentes, isso pode deixar o modelo um pouco lento. Outro ponto interessante para ver nessa feature é a quantidade de clientes por estados que irão continuar ou não com o serviço.


```python
#Gerando o gráfico
sns.set(style="whitegrid")
plt.figure(figsize=(10,20))

ax = sns.countplot(y=df.state, hue=df.churn, order = df.state.value_counts().index)
ax.xaxis.set_label_text("Número de Clientes",fontdict= {'size':16})
ax.yaxis.set_label_text("Estados", fontdict={'size':16})
ax.set_title("Quantidade Clientes por Estados e Churn", fontdict={'size':18})
plt.show()
```


![png](customer_churn_files/customer_churn_21_0.png)


Podemos ver que em alguns estados a taxa de cancelamentos são menores do que em outros, como no estado 'WW'onde os números de 'não's são maiores do que os outros, mas os 'sim's são menores do que os segundo e terceiro estados abaixo dele.

Visto isso vamos criar uma nova coluna em nosso dataset que terá a porcetagem de 'sim's em cada estado essa coluna servirá de base para nossa predição.


```python
# criando a nova feature
states_churn = df.groupby(['state', 'churn']).agg({'churn': 'count'})
state_pcts = states_churn.groupby(level=0).apply(lambda x: 100 * x / float(x.sum()))
states_pcts_yes = state_pcts.filter(like='yes', axis=0)
states_pcts_yes = states_pcts_yes.reset_index(0).reset_index(drop=True)
states_pcts_yes = states_pcts_yes.set_index('state')
states_pcts_yes
states = states_pcts_yes.to_dict()
states = states["churn"]
states
```




    {'AK': 5.769230769230769,
     'AL': 10.0,
     'AR': 20.0,
     'AZ': 6.25,
     'CA': 26.470588235294116,
     'CO': 13.636363636363637,
     'CT': 16.216216216216218,
     'DC': 9.25925925925926,
     'DE': 14.754098360655737,
     'FL': 12.698412698412698,
     'GA': 14.814814814814815,
     'HI': 5.660377358490566,
     'IA': 6.818181818181818,
     'ID': 12.32876712328767,
     'IL': 8.620689655172415,
     'IN': 12.67605633802817,
     'KS': 18.571428571428573,
     'KY': 13.559322033898304,
     'LA': 7.8431372549019605,
     'MA': 16.923076923076923,
     'MD': 24.285714285714285,
     'ME': 20.967741935483872,
     'MI': 21.91780821917808,
     'MN': 17.857142857142858,
     'MO': 11.11111111111111,
     'MS': 21.53846153846154,
     'MT': 20.58823529411765,
     'NC': 16.176470588235293,
     'ND': 9.67741935483871,
     'NE': 8.19672131147541,
     'NH': 16.071428571428573,
     'NJ': 26.470588235294116,
     'NM': 9.67741935483871,
     'NV': 21.21212121212121,
     'NY': 18.072289156626507,
     'OH': 12.820512820512821,
     'OK': 14.754098360655737,
     'OR': 14.102564102564102,
     'PA': 17.77777777777778,
     'RI': 9.23076923076923,
     'SC': 23.333333333333332,
     'SD': 13.333333333333334,
     'TN': 9.433962264150944,
     'TX': 25.0,
     'UT': 13.88888888888889,
     'VA': 6.4935064935064934,
     'VT': 10.95890410958904,
     'WA': 21.21212121212121,
     'WI': 8.974358974358974,
     'WV': 9.433962264150944,
     'WY': 11.688311688311689}




```python
df['Perct_state'] = round(df['state'].map(states),2)
df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>state</th>
      <th>account_length</th>
      <th>area_code</th>
      <th>international_plan</th>
      <th>voice_mail_plan</th>
      <th>number_vmail_messages</th>
      <th>total_day_minutes</th>
      <th>total_day_calls</th>
      <th>total_day_charge</th>
      <th>total_eve_minutes</th>
      <th>total_eve_calls</th>
      <th>total_eve_charge</th>
      <th>total_night_minutes</th>
      <th>total_night_calls</th>
      <th>total_night_charge</th>
      <th>total_intl_minutes</th>
      <th>total_intl_calls</th>
      <th>total_intl_charge</th>
      <th>number_customer_service_calls</th>
      <th>churn</th>
      <th>Perct_state</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>KS</td>
      <td>128</td>
      <td>area_code_415</td>
      <td>no</td>
      <td>yes</td>
      <td>25</td>
      <td>265.1</td>
      <td>110</td>
      <td>45.07</td>
      <td>197.4</td>
      <td>99</td>
      <td>16.78</td>
      <td>244.7</td>
      <td>91</td>
      <td>11.01</td>
      <td>10.0</td>
      <td>3</td>
      <td>2.70</td>
      <td>1</td>
      <td>no</td>
      <td>18.57</td>
    </tr>
    <tr>
      <th>1</th>
      <td>OH</td>
      <td>107</td>
      <td>area_code_415</td>
      <td>no</td>
      <td>yes</td>
      <td>26</td>
      <td>161.6</td>
      <td>123</td>
      <td>27.47</td>
      <td>195.5</td>
      <td>103</td>
      <td>16.62</td>
      <td>254.4</td>
      <td>103</td>
      <td>11.45</td>
      <td>13.7</td>
      <td>3</td>
      <td>3.70</td>
      <td>1</td>
      <td>no</td>
      <td>12.82</td>
    </tr>
    <tr>
      <th>2</th>
      <td>NJ</td>
      <td>137</td>
      <td>area_code_415</td>
      <td>no</td>
      <td>no</td>
      <td>0</td>
      <td>243.4</td>
      <td>114</td>
      <td>41.38</td>
      <td>121.2</td>
      <td>110</td>
      <td>10.30</td>
      <td>162.6</td>
      <td>104</td>
      <td>7.32</td>
      <td>12.2</td>
      <td>5</td>
      <td>3.29</td>
      <td>0</td>
      <td>no</td>
      <td>26.47</td>
    </tr>
    <tr>
      <th>3</th>
      <td>OH</td>
      <td>84</td>
      <td>area_code_408</td>
      <td>yes</td>
      <td>no</td>
      <td>0</td>
      <td>299.4</td>
      <td>71</td>
      <td>50.90</td>
      <td>61.9</td>
      <td>88</td>
      <td>5.26</td>
      <td>196.9</td>
      <td>89</td>
      <td>8.86</td>
      <td>6.6</td>
      <td>7</td>
      <td>1.78</td>
      <td>2</td>
      <td>no</td>
      <td>12.82</td>
    </tr>
    <tr>
      <th>4</th>
      <td>OK</td>
      <td>75</td>
      <td>area_code_415</td>
      <td>yes</td>
      <td>no</td>
      <td>0</td>
      <td>166.7</td>
      <td>113</td>
      <td>28.34</td>
      <td>148.3</td>
      <td>122</td>
      <td>12.61</td>
      <td>186.9</td>
      <td>121</td>
      <td>8.41</td>
      <td>10.1</td>
      <td>3</td>
      <td>2.73</td>
      <td>3</td>
      <td>no</td>
      <td>14.75</td>
    </tr>
  </tbody>
</table>
</div>



Agora vamos analisar a feature "area_code". Que indica a código de área de onde o serviço é prestado. A análise é parecida com a análise acima.


```python
#Gerando o gráfico
sns.set(style="whitegrid")
plt.figure(figsize=(10,6))

ax = sns.countplot(x=df.area_code, order = df.area_code.value_counts().index)
ax.xaxis.set_label_text("Área Code",fontdict= {'size':16})
ax.yaxis.set_label_text("Número de Áreas Codes", fontdict={'size':16})
ax.set_title("Quantidade Clientes por Área Code", fontdict={'size':18})
plt.show()
```


![png](customer_churn_files/customer_churn_26_0.png)


Temos 3 tipos de "area_code" onde o código 415 tem mais clientes e os outros dois tem uma quantidade bem parecida. Vamos comparar essa variável com a variável churn.


```python
#Gerando o gráfico
sns.set(style="whitegrid")
plt.figure(figsize=(10,6))

ax = sns.countplot(x=df.area_code, hue=df.churn, order = df.area_code.value_counts().index)
ax.xaxis.set_label_text("Área Code",fontdict= {'size':16})
ax.yaxis.set_label_text("Número de Áreas Codes", fontdict={'size':16})
ax.set_title("Quantidade Clientes Satisfeitos ou Não por Áreas Codes", fontdict={'size':18})
plt.show()
```


![png](customer_churn_files/customer_churn_28_0.png)


Visualmente a coluna 'area_code' não aprenta ter uma relação muito direta com a variável target, mas mesmo assim acredito ser um fator relevante juntamente com outras colunas.

Vamos analisar agora a feature "international_plan" que mostra qual o tipo de plano do cliente.


```python
#Gerando o gráfico
sns.set(style="whitegrid")
plt.figure(figsize=(10,6))

ax = sns.countplot(x=df.international_plan, hue=df.churn, order = df.international_plan.value_counts().index)
ax.xaxis.set_label_text("International Plan",fontdict= {'size':16})
ax.yaxis.set_label_text("Número de International Plans", fontdict={'size':16})
ax.set_title("Quantidade Clientes por International Plans", fontdict={'size':18})
plt.show()
```


![png](customer_churn_files/customer_churn_30_0.png)


Podemos perceber pelo gráfico que aqueles que não têm plano internacional não costumam cancelar o plano comparado àqueles que possuem. Um dos motivos pode estar ligado ao valor alto cobrado em planos internacionais.

A última feature qualitativa a ser analizada é a "voice_mail_plan".


```python
#Gerando o gráfico
sns.set(style="whitegrid")
plt.figure(figsize=(10,6))

ax = sns.countplot(x=df.voice_mail_plan, hue=df.churn, order = df.voice_mail_plan.value_counts().index)
ax.xaxis.set_label_text("Voice Mail Plan",fontdict= {'size':16})
ax.yaxis.set_label_text("Número de Voice Mail Plans", fontdict={'size':16})
ax.set_title("Quantidade Clientes por Voice Mail Plan", fontdict={'size':18})
plt.show()
```


![png](customer_churn_files/customer_churn_32_0.png)


Esta variável não parece influenciar tanto na decisão quanto a anterior, mas também manterei para o primeiro modelo para ver qual será o resultado.

Agora iremos análizar as variáveis quantitativas do dataset. Para começar nossa análise iremos utilizar o pairplot que mostra qual a relação entre todas as variáveis uma a uma.


```python
#Gerando o gráfico
sns.pairplot(df, hue="churn", diag_kind="hist")
plt.show()
```


![png](customer_churn_files/customer_churn_35_0.png)


O gráfico não ficou muito bom para visualizar, mas conseguimos extrair algumas coisas dele. Primeiramente podemos ver na diagonal os histogramas, onde podemos perceber que a maioria dos gráficos apresentam uma distribuição normal. Isso é muito bom para o modelo. Também podemos ver em alguns gráficos de disperção que existe apenas uma linha na diagonal. Isso pode significar que a correlação entre eles é muito alta e isso pode atrapalhar na tomada de decisão do modelo,  deixando-o instável. Portanto uma dessas variáveis terão que ser excluídas. Podemos ver também que, na segunda variável quantitativa ("number_vmail_messages"), o número de zeros é muito alto, precisamos verificar se isso é um fato normal ou alguma anormalidade nessa feature.

Outro gráfico que ajuda a visualização e o entedimento de nossa análize é o heatmap, ou mapa de calor, que irá mostrar a correlação entre as variáveis.


```python
# Calculando a correlação
df_corr = df.corr()
# Criando o gráfico heatmap
sns.set(style="whitegrid")
plt.figure(figsize=(15, 15))
sns.heatmap(df_corr, cmap='coolwarm', annot=True)
plt.show()
```


![png](customer_churn_files/customer_churn_37_0.png)


Conforme foi citado acima, as variáveis "total_day_minutes" e "total_day_charge" tem uma correlação igual a 1 e portanto teremos que excluir uma das duas para que o modelo não sofra com problemas de estabilidade. O mesmo problema também acoontece com as variáveis "total_eve_minutes" e "total_eve_charge", "total_night_minutes" e "total_night_charge" e "total_intl_minutes" e "total_intl_charge".

Agora vamos gerar os boxplots de cada feature dividos pela decisão do cliente continuar ou não com os serviços. Esse gráficos serão importantes para verificar se o dataset possui valores outliers.


```python
fig, ax = plt.subplots(3,4,figsize=(17, 12))
sns.boxplot(y=df.account_length, x=df.churn, ax=ax[0][0])
sns.boxplot(y=df.number_vmail_messages, x=df.churn, ax=ax[0][1])
sns.boxplot(y=df.total_day_minutes, x=df.churn, ax=ax[0][2])
sns.boxplot(y=df.total_day_calls, x=df.churn, ax=ax[0][3])

sns.boxplot(y=df.total_day_charge, x=df.churn, ax=ax[1][0])
sns.boxplot(y=df.total_eve_minutes, x=df.churn, ax=ax[1][1])
sns.boxplot(y=df.total_eve_calls, x=df.churn, ax=ax[1][2])
sns.boxplot(y=df.total_eve_charge, x=df.churn, ax=ax[1][3])

sns.boxplot(y=df.total_night_minutes, x=df.churn, ax=ax[2][0])
sns.boxplot(y=df.total_night_calls, x=df.churn, ax=ax[2][1])
sns.boxplot(y=df.total_night_charge, x=df.churn, ax=ax[2][2])
sns.boxplot(y=df.total_intl_minutes, x=df.churn, ax=ax[2][3])
plt.show()

fig, (ax1, ax2, ax3) = plt.subplots(1,3,figsize=(12.5, 3.3))
sns.boxplot(y=df.total_intl_calls, x=df.churn, ax=ax1)
sns.boxplot(y=df.total_intl_charge, x=df.churn, ax=ax2)
sns.boxplot(y=df.number_customer_service_calls, x=df.churn, ax=ax3)
plt.show()
```


![png](customer_churn_files/customer_churn_39_0.png)



![png](customer_churn_files/customer_churn_39_1.png)


Visualizando cada gráfico podemos ver que cada um deles possuem alguns outliers, mas inicialmente decidi mantê-los para o primeiro modelo preditivo, pois acredito que eles não irão interferir nos cálculos.

Outro fato interessante que podemos ver pelos gráficos é que a variável "number_vmail_messages", que possui muitos valores iguais a zero, quando dividido pela feature churn temos que a maioria dos clientes que decidem fazer o cancelamento do serviço também utilizam pouco esse serviço em específico ("number_vmail_messages"). Portanto a quantidade grande de zeros pode ser um diferencial para a tomada de decisão do modelo que será gerado.

Vamos salvar este dataset modificado até o momento e continuar nossa análise utilizando o spark para fazer as modificações necessárias e rodas o algoritmos de machine learning.


```python
df.to_csv('dfTrain.csv', header=True, index=False)
```

# Spark para limpeza dos dados

Para a utilizaçao do spark vamos inicialmente carregar as bibliotecas que serão utilizadas neste projeto.


```python
from pyspark.sql import Row
from pyspark.ml.linalg import Vectors
from pyspark.ml.feature import PCA
from pyspark.ml.classification import LogisticRegression
from pyspark.ml.evaluation import BinaryClassificationEvaluator
import pyspark.sql.functions as F
```

Agora vamos criar uma sessão que fará a conexão da máquina com o spark


```python
# Criando o SparkSession
spSession = SparkSession.builder.master("local").appName("ChurnClients").getOrCreate()
```

Criada a conexão vamos ler o dataset salvo anteriormente em formato RDD. Resilient Distributed Datasets (RDD) é uma estrutura de dados fundamental do Spark. É uma coleção imutável de objetos distribuídos. Cada conjunto de dados em RDD é dividido em partições lógicas, que podem ser computadas em diferentes nós do cluster.


```python
# Lendo o arquivo em formato RDD
dfRDD = sc.textFile('dfTrain.csv')
```

Agora vamos salvar o dataset na memória cache do computador, o que fará com que o programa rode com mais eficiência


```python
dfRDD.cache()
```




    dfTrain.csv MapPartitionsRDD[1] at textFile at NativeMethodAccessorImpl.java:0




```python
# Contando o número de linhas do dataset (incluindo o cabeçalho)
dfRDD.count()
```




    3334




```python
# Lendo as primeiras 5 linhas do dataset
dfRDD.take(5)
```




    ['state,account_length,area_code,international_plan,voice_mail_plan,number_vmail_messages,total_day_minutes,total_day_calls,total_day_charge,total_eve_minutes,total_eve_calls,total_eve_charge,total_night_minutes,total_night_calls,total_night_charge,total_intl_minutes,total_intl_calls,total_intl_charge,number_customer_service_calls,churn,Perct_state',
     'KS,128,area_code_415,no,yes,25,265.1,110,45.07,197.4,99,16.78,244.7,91,11.01,10.0,3,2.7,1,no,18.57',
     'OH,107,area_code_415,no,yes,26,161.6,123,27.47,195.5,103,16.62,254.4,103,11.45,13.7,3,3.7,1,no,12.82',
     'NJ,137,area_code_415,no,no,0,243.4,114,41.38,121.2,110,10.3,162.6,104,7.32,12.2,5,3.29,0,no,26.47',
     'OH,84,area_code_408,yes,no,0,299.4,71,50.9,61.9,88,5.26,196.9,89,8.86,6.6,7,1.78,2,no,12.82']




```python
# Removendo o cabeçalho do arquivo
firstLine = dfRDD.first()
dfRDD2 = dfRDD.filter(lambda x: x != firstLine)
dfRDD2.count()
```




    3333



Agora que o arquivo está lido e sem o cabeçalho vamos realizar as transformações necessárias para a execução do modelo preditivo. Para isso iremos criar uma função que irá transformar as features do tipo qualitativa no tipo quantitativa.


```python
# Criando a função
def TransformaDados(inputStr) :
    attList = inputStr.replace("\"", "").split(",")
    
    Area_code_415 = 1.0 if attList[2] ==  "area_code_415" else 0.0
    Area_code_408 = 1.0 if attList[2] ==  "area_code_408" else 0.0
    Area_code_510 = 1.0 if attList[2] ==  "area_code_510" else 0.0
    International_plan = 1.0 if attList[3] ==  "yes" else 0.0
    Voice_mail_plan = 1.0 if attList[4] ==  "yes" else 0.0
    Churn = 1.0 if attList[19] ==  "yes" else 0.0
    
    # Cria a linha com os objetos transformados
    linhas = Row(state = attList[0], account_length = float(attList[1]), area_code_415 = Area_code_415, area_code_408 =
                Area_code_408, area_code_510 = Area_code_510, international_plan = International_plan, voice_mail_plan =
                Voice_mail_plan, number_vmail_messages = float(attList[5]), total_day_minutes = attList[6], total_day_calls =
                float(attList[7]), total_day_charge = attList[8], total_eve_minutes = attList[9], total_eve_calls =
                float(attList[10]), total_eve_charge = attList[11], total_night_minutes = attList[12], total_night_calls =
                float(attList[13]), total_night_charge = attList[14], total_intl_minutes = attList[15], total_intl_calls =
                float(attList[16]), total_intl_charge = attList[17], number_customer_service_calls = float(attList[18]),
                churn = Churn, Perct_state = attList[20])
    return(linhas)
```


```python
# Lendo a função em um novo RDD
dfRDD3 = dfRDD2.map(TransformaDados)
dfRDD3.collect()[:5]
```




    [Row(Perct_state='18.57', account_length=128.0, area_code_408=0.0, area_code_415=1.0, area_code_510=0.0, churn=0.0, international_plan=0.0, number_customer_service_calls=1.0, number_vmail_messages=25.0, state='KS', total_day_calls=110.0, total_day_charge='45.07', total_day_minutes='265.1', total_eve_calls=99.0, total_eve_charge='16.78', total_eve_minutes='197.4', total_intl_calls=3.0, total_intl_charge='2.7', total_intl_minutes='10.0', total_night_calls=91.0, total_night_charge='11.01', total_night_minutes='244.7', voice_mail_plan=1.0),
     Row(Perct_state='12.82', account_length=107.0, area_code_408=0.0, area_code_415=1.0, area_code_510=0.0, churn=0.0, international_plan=0.0, number_customer_service_calls=1.0, number_vmail_messages=26.0, state='OH', total_day_calls=123.0, total_day_charge='27.47', total_day_minutes='161.6', total_eve_calls=103.0, total_eve_charge='16.62', total_eve_minutes='195.5', total_intl_calls=3.0, total_intl_charge='3.7', total_intl_minutes='13.7', total_night_calls=103.0, total_night_charge='11.45', total_night_minutes='254.4', voice_mail_plan=1.0),
     Row(Perct_state='26.47', account_length=137.0, area_code_408=0.0, area_code_415=1.0, area_code_510=0.0, churn=0.0, international_plan=0.0, number_customer_service_calls=0.0, number_vmail_messages=0.0, state='NJ', total_day_calls=114.0, total_day_charge='41.38', total_day_minutes='243.4', total_eve_calls=110.0, total_eve_charge='10.3', total_eve_minutes='121.2', total_intl_calls=5.0, total_intl_charge='3.29', total_intl_minutes='12.2', total_night_calls=104.0, total_night_charge='7.32', total_night_minutes='162.6', voice_mail_plan=0.0),
     Row(Perct_state='12.82', account_length=84.0, area_code_408=1.0, area_code_415=0.0, area_code_510=0.0, churn=0.0, international_plan=1.0, number_customer_service_calls=2.0, number_vmail_messages=0.0, state='OH', total_day_calls=71.0, total_day_charge='50.9', total_day_minutes='299.4', total_eve_calls=88.0, total_eve_charge='5.26', total_eve_minutes='61.9', total_intl_calls=7.0, total_intl_charge='1.78', total_intl_minutes='6.6', total_night_calls=89.0, total_night_charge='8.86', total_night_minutes='196.9', voice_mail_plan=0.0),
     Row(Perct_state='14.75', account_length=75.0, area_code_408=0.0, area_code_415=1.0, area_code_510=0.0, churn=0.0, international_plan=1.0, number_customer_service_calls=3.0, number_vmail_messages=0.0, state='OK', total_day_calls=113.0, total_day_charge='28.34', total_day_minutes='166.7', total_eve_calls=122.0, total_eve_charge='12.61', total_eve_minutes='148.3', total_intl_calls=3.0, total_intl_charge='2.73', total_intl_minutes='10.1', total_night_calls=121.0, total_night_charge='8.41', total_night_minutes='186.9', voice_mail_plan=0.0)]



## Pré-processamento dos dados

Feitas essas tranformações vamos fazer o pré processamento dos dados selecionando as variáveis que irão ser utilizadas no modelo e tranformando o dataset em uma matriz com a variável target separada das outras variáveis, que ficarão em um vetor denso para cada linha. Também utilizaremos uma técnica para a redução de dimensionalidade de nossas variáveis, isso permitirá que o programa seja rodado de forma mais suave.

Como foi dito anteriormente, vamos excluir apenas aquelas variáveis que estavam causando o problema de alta correlação entre elas, o restante será mantido.


```python
# Criando um LabeledPoint (target, Vector[features])
def transformVar(row) :
    obj = (row["churn"], Vectors.dense(row["account_length"], row["area_code_415"], row["area_code_408"],
                                     row["area_code_510"], row["international_plan"], row["voice_mail_plan"],
                                     row["number_vmail_messages"], row["total_day_minutes"], row["total_day_calls"],
                                     row["total_eve_minutes"], row["total_eve_calls"], row["total_night_minutes"],
                                     row["total_night_calls"], row["total_intl_minutes"], row["total_intl_calls"],
                                     row["number_customer_service_calls"], row["Perct_state"]))
    return obj
```


```python
dfRDD4 = dfRDD3.map(transformVar)
dfRDD4.collect()
```




    [(0.0,
      DenseVector([128.0, 1.0, 0.0, 0.0, 0.0, 1.0, 25.0, 265.1, 110.0, 197.4, 99.0, 244.7, 91.0, 10.0, 3.0, 1.0, 18.57])),
     (0.0,
      DenseVector([107.0, 1.0, 0.0, 0.0, 0.0, 1.0, 26.0, 161.6, 123.0, 195.5, 103.0, 254.4, 103.0, 13.7, 3.0, 1.0, 12.82])),
     (0.0,
      DenseVector([137.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 243.4, 114.0, 121.2, 110.0, 162.6, 104.0, 12.2, 5.0, 0.0, 26.47])),
     (0.0,
      DenseVector([84.0, 0.0, 1.0, 0.0, 1.0, 0.0, 0.0, 299.4, 71.0, 61.9, 88.0, 196.9, 89.0, 6.6, 7.0, 2.0, 12.82])),
     (0.0,
      DenseVector([75.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 166.7, 113.0, 148.3, 122.0, 186.9, 121.0, 10.1, 3.0, 3.0, 14.75])),
     (0.0,
      DenseVector([118.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 223.4, 98.0, 220.6, 101.0, 203.9, 118.0, 6.3, 6.0, 0.0, 10.0])),
     (0.0,
      DenseVector([121.0, 0.0, 0.0, 1.0, 0.0, 1.0, 24.0, 218.2, 88.0, 348.5, 108.0, 212.6, 118.0, 7.5, 7.0, 3.0, 16.92])),
     (0.0,
      DenseVector([147.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 157.0, 79.0, 103.1, 94.0, 211.8, 96.0, 7.1, 6.0, 0.0, 11.11])),
     (0.0,
      DenseVector([117.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 184.5, 97.0, 351.6, 80.0, 215.8, 90.0, 8.7, 4.0, 1.0, 7.84])),
     (0.0,
      DenseVector([141.0, 1.0, 0.0, 0.0, 1.0, 1.0, 37.0, 258.6, 84.0, 222.0, 111.0, 326.4, 97.0, 11.2, 5.0, 0.0, 9.43])),
     (1.0,
      DenseVector([65.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 129.1, 137.0, 228.5, 83.0, 208.8, 111.0, 12.7, 6.0, 4.0, 12.68])),
     (0.0,
      DenseVector([74.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 187.7, 127.0, 163.4, 148.0, 196.0, 94.0, 9.1, 5.0, 0.0, 9.23])),
     (0.0,
      DenseVector([168.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 128.8, 96.0, 104.9, 71.0, 141.1, 128.0, 11.2, 2.0, 1.0, 6.82])),
     (0.0,
      DenseVector([95.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 156.6, 88.0, 247.6, 75.0, 192.3, 115.0, 12.3, 5.0, 3.0, 20.59])),
     (0.0,
      DenseVector([62.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 120.7, 70.0, 307.2, 76.0, 203.0, 99.0, 13.1, 6.0, 4.0, 6.82])),
     (1.0,
      DenseVector([161.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 332.9, 67.0, 317.8, 97.0, 160.6, 128.0, 5.4, 9.0, 4.0, 18.07])),
     (0.0,
      DenseVector([85.0, 0.0, 1.0, 0.0, 0.0, 1.0, 27.0, 196.4, 139.0, 280.9, 90.0, 89.3, 75.0, 13.8, 4.0, 1.0, 12.33])),
     (0.0,
      DenseVector([93.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 190.7, 114.0, 218.2, 111.0, 129.6, 121.0, 8.1, 3.0, 3.0, 10.96])),
     (0.0,
      DenseVector([76.0, 0.0, 0.0, 1.0, 0.0, 1.0, 33.0, 189.7, 66.0, 212.8, 65.0, 165.7, 108.0, 10.0, 5.0, 1.0, 6.49])),
     (0.0,
      DenseVector([73.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 224.4, 90.0, 159.5, 88.0, 192.8, 74.0, 13.0, 2.0, 1.0, 25.0])),
     (0.0,
      DenseVector([147.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 155.1, 117.0, 239.7, 93.0, 208.8, 133.0, 10.6, 4.0, 0.0, 12.7])),
     (1.0,
      DenseVector([77.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 62.4, 89.0, 169.9, 121.0, 209.6, 64.0, 5.7, 6.0, 5.0, 13.64])),
     (0.0,
      DenseVector([130.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 183.0, 112.0, 72.9, 99.0, 181.8, 78.0, 9.5, 19.0, 0.0, 6.25])),
     (0.0,
      DenseVector([111.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 110.4, 103.0, 137.3, 102.0, 189.6, 105.0, 7.7, 6.0, 2.0, 23.33])),
     (0.0,
      DenseVector([132.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 81.1, 86.0, 245.2, 72.0, 237.0, 115.0, 10.3, 2.0, 0.0, 6.49])),
     (0.0,
      DenseVector([174.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 124.3, 76.0, 277.1, 112.0, 250.7, 115.0, 15.5, 5.0, 3.0, 8.2])),
     (0.0,
      DenseVector([57.0, 0.0, 1.0, 0.0, 0.0, 1.0, 39.0, 213.0, 115.0, 191.1, 112.0, 182.7, 115.0, 9.5, 3.0, 0.0, 11.69])),
     (0.0,
      DenseVector([54.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 134.3, 73.0, 155.5, 100.0, 102.1, 68.0, 14.7, 4.0, 3.0, 20.59])),
     (0.0,
      DenseVector([20.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 190.0, 109.0, 258.2, 84.0, 181.5, 102.0, 6.3, 6.0, 0.0, 11.11])),
     (0.0,
      DenseVector([49.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 119.3, 117.0, 215.1, 109.0, 178.7, 90.0, 11.1, 1.0, 1.0, 5.66])),
     (0.0,
      DenseVector([142.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 84.8, 95.0, 136.7, 63.0, 250.5, 148.0, 14.2, 6.0, 2.0, 8.62])),
     (0.0,
      DenseVector([75.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 226.1, 105.0, 201.5, 107.0, 246.2, 98.0, 10.3, 5.0, 1.0, 16.07])),
     (0.0,
      DenseVector([172.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 212.0, 121.0, 31.2, 115.0, 293.3, 78.0, 12.6, 10.0, 3.0, 7.84])),
     (1.0,
      DenseVector([12.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 249.6, 118.0, 252.4, 119.0, 280.2, 90.0, 11.8, 3.0, 1.0, 6.25])),
     (0.0,
      DenseVector([57.0, 0.0, 1.0, 0.0, 0.0, 1.0, 25.0, 176.8, 94.0, 195.0, 75.0, 213.5, 116.0, 8.3, 4.0, 0.0, 14.75])),
     (0.0,
      DenseVector([72.0, 1.0, 0.0, 0.0, 0.0, 1.0, 37.0, 220.0, 80.0, 217.3, 102.0, 152.8, 71.0, 14.7, 6.0, 3.0, 14.81])),
     (0.0,
      DenseVector([36.0, 0.0, 1.0, 0.0, 0.0, 1.0, 30.0, 146.3, 128.0, 162.5, 80.0, 129.3, 109.0, 14.5, 6.0, 0.0, 5.77])),
     (0.0,
      DenseVector([78.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 130.8, 64.0, 223.7, 116.0, 227.8, 108.0, 10.0, 5.0, 1.0, 16.92])),
     (0.0,
      DenseVector([136.0, 1.0, 0.0, 0.0, 1.0, 1.0, 33.0, 203.9, 106.0, 187.6, 99.0, 101.7, 107.0, 10.5, 6.0, 3.0, 5.77])),
     (0.0,
      DenseVector([149.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 140.4, 94.0, 271.8, 92.0, 188.3, 108.0, 11.1, 9.0, 1.0, 26.47])),
     (0.0,
      DenseVector([98.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 126.3, 102.0, 166.8, 85.0, 187.8, 135.0, 9.4, 2.0, 3.0, 14.81])),
     (1.0,
      DenseVector([135.0, 0.0, 1.0, 0.0, 1.0, 1.0, 41.0, 173.1, 85.0, 203.9, 107.0, 122.2, 78.0, 14.6, 15.0, 0.0, 24.29])),
     (0.0,
      DenseVector([34.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 124.8, 82.0, 282.2, 98.0, 311.5, 78.0, 10.0, 4.0, 2.0, 20.0])),
     (0.0,
      DenseVector([160.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 85.8, 77.0, 165.3, 110.0, 178.5, 92.0, 9.2, 4.0, 3.0, 12.33])),
     (0.0,
      DenseVector([64.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 154.0, 67.0, 225.8, 118.0, 265.3, 86.0, 3.5, 3.0, 1.0, 8.97])),
     (0.0,
      DenseVector([59.0, 0.0, 1.0, 0.0, 0.0, 1.0, 28.0, 120.9, 97.0, 213.0, 92.0, 163.1, 116.0, 8.5, 5.0, 2.0, 14.1])),
     (0.0,
      DenseVector([65.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 211.3, 120.0, 162.6, 122.0, 134.7, 118.0, 13.2, 5.0, 3.0, 21.92])),
     (0.0,
      DenseVector([142.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 187.0, 133.0, 134.6, 74.0, 242.2, 127.0, 7.4, 5.0, 2.0, 14.75])),
     (1.0,
      DenseVector([119.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 159.1, 114.0, 231.3, 117.0, 143.2, 91.0, 8.8, 3.0, 5.0, 12.33])),
     (0.0,
      DenseVector([97.0, 1.0, 0.0, 0.0, 0.0, 1.0, 24.0, 133.2, 135.0, 217.2, 58.0, 70.6, 79.0, 11.0, 3.0, 1.0, 11.69])),
     (0.0,
      DenseVector([52.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 191.9, 108.0, 269.8, 96.0, 236.8, 87.0, 7.8, 5.0, 3.0, 6.82])),
     (0.0,
      DenseVector([60.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 220.6, 57.0, 211.1, 115.0, 249.0, 129.0, 6.8, 3.0, 1.0, 12.68])),
     (0.0,
      DenseVector([10.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 186.1, 112.0, 190.2, 66.0, 282.8, 57.0, 11.4, 6.0, 2.0, 6.49])),
     (0.0,
      DenseVector([96.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 160.2, 117.0, 267.5, 67.0, 228.5, 68.0, 9.3, 5.0, 2.0, 13.89])),
     (1.0,
      DenseVector([87.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 151.0, 83.0, 219.7, 116.0, 203.9, 127.0, 9.7, 3.0, 5.0, 11.69])),
     (0.0,
      DenseVector([81.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 175.5, 67.0, 249.3, 85.0, 270.2, 98.0, 10.2, 3.0, 1.0, 12.68])),
     (0.0,
      DenseVector([141.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 126.9, 98.0, 180.0, 62.0, 140.8, 128.0, 8.0, 2.0, 1.0, 13.64])),
     (1.0,
      DenseVector([121.0, 0.0, 1.0, 0.0, 0.0, 1.0, 30.0, 198.4, 129.0, 75.3, 77.0, 181.2, 77.0, 5.8, 3.0, 3.0, 13.64])),
     (0.0,
      DenseVector([68.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 148.8, 70.0, 246.5, 164.0, 129.8, 103.0, 12.1, 3.0, 3.0, 8.97])),
     (0.0,
      DenseVector([125.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 229.3, 103.0, 177.4, 126.0, 189.3, 95.0, 12.0, 8.0, 1.0, 14.75])),
     (0.0,
      DenseVector([174.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 192.1, 97.0, 169.9, 94.0, 166.6, 54.0, 11.4, 4.0, 1.0, 12.33])),
     (0.0,
      DenseVector([116.0, 1.0, 0.0, 0.0, 0.0, 1.0, 34.0, 268.6, 83.0, 178.2, 142.0, 166.3, 106.0, 11.6, 3.0, 2.0, 26.47])),
     (0.0,
      DenseVector([74.0, 0.0, 0.0, 1.0, 0.0, 1.0, 33.0, 193.7, 91.0, 246.1, 96.0, 138.0, 92.0, 14.6, 3.0, 2.0, 17.86])),
     (0.0,
      DenseVector([149.0, 0.0, 1.0, 0.0, 0.0, 1.0, 28.0, 180.7, 92.0, 187.8, 64.0, 265.5, 53.0, 12.6, 3.0, 3.0, 13.33])),
     (0.0,
      DenseVector([38.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 131.2, 98.0, 162.9, 97.0, 159.0, 106.0, 8.2, 6.0, 2.0, 16.18])),
     (0.0,
      DenseVector([40.0, 1.0, 0.0, 0.0, 0.0, 1.0, 41.0, 148.1, 74.0, 169.5, 88.0, 214.1, 102.0, 6.2, 5.0, 2.0, 21.21])),
     (0.0,
      DenseVector([43.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 251.5, 105.0, 212.8, 104.0, 157.8, 67.0, 9.3, 4.0, 0.0, 11.69])),
     (0.0,
      DenseVector([113.0, 0.0, 1.0, 0.0, 1.0, 0.0, 0.0, 125.2, 93.0, 206.4, 119.0, 129.3, 139.0, 8.3, 8.0, 0.0, 17.86])),
     (0.0,
      DenseVector([126.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 211.6, 70.0, 216.9, 80.0, 153.5, 60.0, 7.8, 1.0, 1.0, 13.89])),
     (1.0,
      DenseVector([150.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 178.9, 101.0, 169.1, 110.0, 148.6, 100.0, 13.8, 3.0, 4.0, 25.0])),
     (0.0,
      DenseVector([138.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 241.8, 93.0, 170.5, 83.0, 295.3, 104.0, 11.8, 7.0, 3.0, 26.47])),
     (0.0,
      DenseVector([162.0, 0.0, 0.0, 1.0, 0.0, 1.0, 46.0, 224.9, 97.0, 188.2, 84.0, 254.6, 61.0, 12.1, 2.0, 0.0, 17.86])),
     (0.0,
      DenseVector([147.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 248.6, 83.0, 148.9, 85.0, 172.5, 109.0, 8.0, 4.0, 3.0, 9.68])),
     (0.0,
      DenseVector([90.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 203.4, 146.0, 226.7, 117.0, 152.4, 105.0, 7.3, 4.0, 1.0, 21.21])),
     (0.0,
      DenseVector([85.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 235.8, 109.0, 157.2, 94.0, 188.2, 99.0, 12.0, 3.0, 0.0, 5.66])),
     (0.0,
      DenseVector([50.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 157.1, 90.0, 223.3, 72.0, 181.4, 111.0, 6.1, 2.0, 1.0, 17.86])),
     (1.0,
      DenseVector([82.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 300.3, 109.0, 181.0, 100.0, 270.1, 73.0, 11.7, 4.0, 0.0, 9.26])),
     (1.0,
      DenseVector([144.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 61.6, 117.0, 77.1, 85.0, 173.0, 99.0, 8.2, 7.0, 4.0, 18.07])),
     (0.0,
      DenseVector([46.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 214.1, 72.0, 164.4, 104.0, 177.5, 113.0, 8.2, 3.0, 2.0, 17.86])),
     (0.0,
      DenseVector([70.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 170.2, 98.0, 155.2, 102.0, 228.6, 76.0, 15.0, 2.0, 1.0, 24.29])),
     (0.0,
      DenseVector([144.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 201.1, 99.0, 303.5, 74.0, 224.0, 119.0, 13.2, 2.0, 1.0, 9.43])),
     (0.0,
      DenseVector([116.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 215.4, 104.0, 204.8, 79.0, 278.5, 109.0, 12.6, 5.0, 3.0, 14.1])),
     (0.0,
      DenseVector([55.0, 0.0, 1.0, 0.0, 0.0, 1.0, 25.0, 165.6, 123.0, 136.1, 95.0, 175.7, 90.0, 11.0, 2.0, 3.0, 13.64])),
     (0.0,
      DenseVector([70.0, 1.0, 0.0, 0.0, 0.0, 1.0, 24.0, 249.5, 101.0, 259.7, 98.0, 222.7, 68.0, 9.8, 4.0, 1.0, 14.81])),
     (1.0,
      DenseVector([106.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 210.6, 96.0, 249.2, 85.0, 191.4, 88.0, 12.4, 1.0, 2.0, 25.0])),
     (0.0,
      DenseVector([128.0, 0.0, 0.0, 1.0, 0.0, 1.0, 29.0, 179.3, 104.0, 225.9, 86.0, 323.0, 78.0, 8.6, 7.0, 0.0, 10.96])),
     (1.0,
      DenseVector([94.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 157.9, 105.0, 155.0, 101.0, 189.6, 84.0, 8.0, 5.0, 4.0, 12.68])),
     (0.0,
      DenseVector([111.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 214.3, 118.0, 208.5, 76.0, 182.4, 98.0, 12.0, 2.0, 1.0, 9.43])),
     (0.0,
      DenseVector([74.0, 1.0, 0.0, 0.0, 0.0, 1.0, 35.0, 154.1, 104.0, 123.4, 84.0, 202.1, 57.0, 10.9, 9.0, 2.0, 13.56])),
     (1.0,
      DenseVector([128.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 237.9, 125.0, 247.6, 93.0, 208.9, 68.0, 13.9, 4.0, 1.0, 26.47])),
     (0.0,
      DenseVector([82.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 143.9, 61.0, 194.9, 105.0, 109.6, 94.0, 11.1, 2.0, 1.0, 9.26])),
     (1.0,
      DenseVector([155.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 203.4, 100.0, 190.9, 104.0, 196.0, 119.0, 8.9, 4.0, 0.0, 7.84])),
     (0.0,
      DenseVector([80.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 124.3, 100.0, 173.0, 107.0, 253.2, 62.0, 7.9, 9.0, 1.0, 20.0])),
     (0.0,
      DenseVector([78.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 252.9, 93.0, 178.4, 112.0, 263.9, 105.0, 9.5, 7.0, 3.0, 20.97])),
     (0.0,
      DenseVector([90.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 179.1, 71.0, 190.6, 81.0, 127.7, 91.0, 10.6, 7.0, 3.0, 6.25])),
     (0.0,
      DenseVector([104.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 278.4, 106.0, 81.0, 113.0, 163.2, 137.0, 9.8, 5.0, 1.0, 5.77])),
     (0.0,
      DenseVector([73.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 160.1, 110.0, 213.3, 72.0, 174.1, 72.0, 13.0, 4.0, 0.0, 20.59])),
     (0.0,
      DenseVector([99.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 198.2, 87.0, 207.3, 76.0, 190.9, 113.0, 8.7, 3.0, 4.0, 6.25])),
     (1.0,
      DenseVector([120.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 212.1, 131.0, 209.4, 104.0, 167.2, 96.0, 5.3, 5.0, 1.0, 21.54])),
     (1.0,
      DenseVector([77.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 251.8, 72.0, 205.7, 126.0, 275.2, 109.0, 9.8, 7.0, 2.0, 12.33])),
     (0.0,
      DenseVector([98.0, 0.0, 0.0, 1.0, 0.0, 1.0, 21.0, 161.2, 114.0, 252.2, 83.0, 160.2, 92.0, 4.4, 8.0, 4.0, 6.82])),
     (0.0,
      DenseVector([108.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 178.3, 137.0, 189.0, 76.0, 129.1, 102.0, 14.6, 5.0, 0.0, 16.92])),
     (0.0,
      DenseVector([135.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 151.7, 82.0, 119.0, 105.0, 180.0, 100.0, 10.5, 6.0, 0.0, 10.96])),
     (0.0,
      DenseVector([95.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 135.0, 99.0, 183.6, 106.0, 245.3, 102.0, 12.5, 9.0, 1.0, 13.56])),
     (0.0,
      DenseVector([122.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 170.5, 94.0, 173.7, 109.0, 248.6, 75.0, 11.3, 2.0, 1.0, 12.68])),
     (0.0,
      DenseVector([95.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 238.1, 65.0, 187.2, 98.0, 190.0, 115.0, 11.8, 4.0, 4.0, 6.25])),
     (0.0,
      DenseVector([36.0, 0.0, 0.0, 1.0, 0.0, 1.0, 29.0, 281.4, 102.0, 202.2, 76.0, 187.2, 113.0, 9.0, 6.0, 2.0, 21.92])),
     (0.0,
      DenseVector([93.0, 0.0, 0.0, 1.0, 0.0, 1.0, 21.0, 117.9, 131.0, 164.5, 115.0, 217.0, 86.0, 9.8, 3.0, 1.0, 9.68])),
     (0.0,
      DenseVector([141.0, 1.0, 0.0, 0.0, 0.0, 1.0, 32.0, 148.6, 91.0, 131.1, 97.0, 219.4, 142.0, 10.1, 1.0, 1.0, 13.64])),
     (0.0,
      DenseVector([157.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 229.8, 90.0, 147.9, 121.0, 241.4, 108.0, 9.6, 7.0, 3.0, 13.89])),
     (0.0,
      DenseVector([120.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 165.0, 100.0, 317.2, 83.0, 119.2, 86.0, 8.3, 8.0, 1.0, 21.92])),
     (0.0,
      DenseVector([103.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 185.0, 117.0, 223.3, 94.0, 222.8, 91.0, 12.6, 2.0, 2.0, 16.92])),
     (0.0,
      DenseVector([98.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 161.0, 117.0, 190.9, 113.0, 227.7, 113.0, 12.1, 4.0, 4.0, 10.0])),
     (0.0,
      DenseVector([125.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 126.7, 108.0, 206.0, 90.0, 247.8, 114.0, 13.3, 7.0, 1.0, 14.75])),
     (0.0,
      DenseVector([63.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 58.9, 125.0, 169.6, 59.0, 211.4, 88.0, 9.4, 3.0, 1.0, 6.25])),
     (1.0,
      DenseVector([36.0, 0.0, 0.0, 1.0, 1.0, 1.0, 42.0, 196.8, 89.0, 254.9, 122.0, 138.3, 126.0, 20.0, 6.0, 0.0, 20.97])),
     (0.0,
      DenseVector([64.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 162.6, 83.0, 152.3, 109.0, 57.5, 122.0, 14.2, 3.0, 1.0, 26.47])),
     (1.0,
      DenseVector([74.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 282.5, 114.0, 219.9, 48.0, 170.0, 115.0, 9.4, 4.0, 1.0, 21.21])),
     (0.0,
      DenseVector([112.0, 0.0, 0.0, 1.0, 0.0, 1.0, 36.0, 113.7, 117.0, 157.5, 82.0, 177.6, 118.0, 10.0, 3.0, 2.0, 11.11])),
     (0.0,
      DenseVector([97.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 239.8, 125.0, 214.8, 111.0, 143.3, 81.0, 8.7, 5.0, 2.0, 12.33])),
     (0.0,
      DenseVector([46.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 210.2, 92.0, 227.3, 77.0, 200.1, 116.0, 13.1, 7.0, 1.0, 8.2])),
     (0.0,
      DenseVector([41.0, 0.0, 1.0, 0.0, 0.0, 1.0, 22.0, 213.8, 102.0, 141.8, 86.0, 142.2, 123.0, 7.2, 3.0, 0.0, 25.0])),
     (0.0,
      DenseVector([121.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 190.7, 103.0, 183.5, 117.0, 220.8, 103.0, 9.8, 4.0, 3.0, 24.29])),
     (0.0,
      DenseVector([193.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 170.9, 124.0, 132.3, 95.0, 112.9, 89.0, 11.6, 3.0, 1.0, 21.54])),
     (0.0,
      DenseVector([130.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 154.2, 119.0, 110.2, 98.0, 227.4, 117.0, 9.2, 5.0, 2.0, 21.21])),
     (0.0,
      DenseVector([85.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 201.4, 52.0, 229.4, 104.0, 252.5, 106.0, 12.0, 3.0, 1.0, 6.25])),
     (1.0,
      DenseVector([162.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 70.7, 108.0, 157.5, 87.0, 154.8, 82.0, 9.1, 3.0, 4.0, 21.54])),
     (1.0,
      DenseVector([61.0, 0.0, 0.0, 1.0, 0.0, 1.0, 27.0, 187.5, 124.0, 146.6, 103.0, 225.7, 129.0, 6.4, 6.0, 4.0, 21.54])),
     (0.0,
      DenseVector([92.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 91.7, 90.0, 193.7, 123.0, 175.0, 86.0, 9.2, 4.0, 2.0, 25.0])),
     (0.0,
      DenseVector([131.0, 0.0, 1.0, 0.0, 0.0, 1.0, 36.0, 214.2, 115.0, 161.7, 117.0, 264.7, 102.0, 9.5, 4.0, 3.0, 8.2])),
     (0.0,
      DenseVector([90.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 145.5, 92.0, 217.7, 114.0, 146.9, 123.0, 10.9, 2.0, 3.0, 8.2])),
     (0.0,
      DenseVector([75.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 166.3, 125.0, 158.2, 86.0, 256.7, 80.0, 6.1, 5.0, 1.0, 26.47])),
     (0.0,
      DenseVector([78.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 231.0, 115.0, 230.4, 140.0, 261.4, 120.0, 9.5, 3.0, 1.0, 26.47])),
     (0.0,
      DenseVector([82.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 200.3, 96.0, 201.2, 102.0, 206.1, 60.0, 7.1, 1.0, 4.0, 25.0])),
     (0.0,
      DenseVector([163.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 197.0, 109.0, 202.6, 128.0, 206.4, 80.0, 9.1, 10.0, 1.0, 20.0])),
     (0.0,
      DenseVector([91.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 129.9, 112.0, 173.3, 83.0, 247.2, 130.0, 11.2, 3.0, 3.0, 10.0])),
     (0.0,
      DenseVector([75.0, 1.0, 0.0, 0.0, 0.0, 1.0, 21.0, 175.8, 97.0, 217.5, 106.0, 237.5, 134.0, 5.3, 4.0, 5.0, 18.07])),
     (0.0,
      DenseVector([91.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 203.1, 106.0, 210.1, 113.0, 195.6, 129.0, 12.0, 3.0, 3.0, 12.7])),
     (0.0,
      DenseVector([127.0, 0.0, 0.0, 1.0, 0.0, 1.0, 36.0, 183.2, 117.0, 126.8, 76.0, 263.3, 71.0, 11.2, 8.0, 1.0, 5.77])),
     (0.0,
      DenseVector([113.0, 1.0, 0.0, 0.0, 0.0, 1.0, 23.0, 205.0, 101.0, 152.0, 60.0, 158.6, 59.0, 10.2, 5.0, 2.0, 21.21])),
     (0.0,
      DenseVector([110.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 148.5, 115.0, 276.4, 84.0, 193.6, 112.0, 12.4, 3.0, 1.0, 14.75])),
     (0.0,
      DenseVector([120.0, 1.0, 0.0, 0.0, 0.0, 1.0, 39.0, 200.3, 68.0, 220.4, 97.0, 253.8, 116.0, 10.5, 4.0, 0.0, 24.29])),
     (0.0,
      DenseVector([157.0, 1.0, 0.0, 0.0, 0.0, 1.0, 28.0, 192.6, 107.0, 195.5, 74.0, 109.7, 139.0, 6.8, 5.0, 3.0, 21.92])),
     (0.0,
      DenseVector([103.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 246.5, 47.0, 195.5, 84.0, 200.5, 96.0, 11.7, 4.0, 1.0, 10.96])),
     (1.0,
      DenseVector([117.0, 0.0, 1.0, 0.0, 1.0, 0.0, 0.0, 167.1, 86.0, 177.5, 87.0, 249.4, 132.0, 14.1, 7.0, 2.0, 10.96])),
     (0.0,
      DenseVector([140.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 231.9, 101.0, 160.1, 94.0, 110.4, 98.0, 14.3, 6.0, 3.0, 21.92])),
     (0.0,
      DenseVector([127.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 146.7, 91.0, 203.5, 78.0, 203.4, 110.0, 13.7, 3.0, 1.0, 21.21])),
     (0.0,
      DenseVector([83.0, 0.0, 1.0, 0.0, 1.0, 0.0, 0.0, 271.5, 87.0, 216.3, 126.0, 121.1, 105.0, 11.7, 4.0, 1.0, 13.89])),
     (0.0,
      DenseVector([121.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 181.5, 121.0, 218.4, 98.0, 161.6, 103.0, 8.5, 5.0, 1.0, 7.84])),
     (0.0,
      DenseVector([145.0, 0.0, 1.0, 0.0, 0.0, 1.0, 43.0, 257.7, 97.0, 162.1, 95.0, 286.9, 86.0, 11.1, 4.0, 2.0, 9.23])),
     (0.0,
      DenseVector([113.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 193.8, 99.0, 221.4, 125.0, 172.3, 67.0, 10.6, 6.0, 1.0, 6.82])),
     (0.0,
      DenseVector([117.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 102.8, 119.0, 206.7, 91.0, 299.0, 105.0, 10.1, 7.0, 1.0, 8.2])),
     (0.0,
      DenseVector([65.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 187.9, 116.0, 157.6, 117.0, 227.3, 86.0, 7.5, 6.0, 1.0, 12.82])),
     (0.0,
      DenseVector([56.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 226.0, 112.0, 248.5, 118.0, 140.5, 142.0, 6.9, 11.0, 1.0, 9.23])),
     (0.0,
      DenseVector([96.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 260.4, 115.0, 146.0, 46.0, 269.5, 87.0, 11.5, 4.0, 5.0, 14.75])),
     (0.0,
      DenseVector([151.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 178.7, 116.0, 292.1, 138.0, 265.9, 101.0, 9.8, 4.0, 0.0, 7.84])),
     (1.0,
      DenseVector([83.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 337.4, 120.0, 227.4, 116.0, 153.9, 114.0, 15.8, 7.0, 0.0, 12.82])),
     (0.0,
      DenseVector([139.0, 0.0, 0.0, 1.0, 0.0, 1.0, 23.0, 157.6, 129.0, 247.0, 96.0, 259.2, 112.0, 13.7, 2.0, 0.0, 6.49])),
     (0.0,
      DenseVector([6.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 183.6, 117.0, 256.7, 72.0, 178.6, 79.0, 10.2, 2.0, 1.0, 11.11])),
     (0.0,
      DenseVector([115.0, 0.0, 0.0, 1.0, 0.0, 1.0, 24.0, 142.1, 124.0, 183.4, 129.0, 164.8, 114.0, 9.6, 4.0, 1.0, 12.7])),
     (0.0,
      DenseVector([87.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 136.3, 97.0, 172.2, 108.0, 137.5, 101.0, 7.1, 5.0, 0.0, 23.33])),
     (0.0,
      DenseVector([141.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 217.1, 110.0, 241.5, 111.0, 253.5, 103.0, 12.0, 6.0, 0.0, 6.49])),
     (0.0,
      DenseVector([141.0, 0.0, 0.0, 1.0, 0.0, 1.0, 36.0, 187.5, 99.0, 241.4, 116.0, 229.5, 105.0, 10.5, 5.0, 3.0, 6.82])),
     (0.0,
      DenseVector([62.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 98.9, 103.0, 135.4, 122.0, 236.6, 82.0, 12.2, 1.0, 1.0, 21.92])),
     (0.0,
      DenseVector([146.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 206.3, 151.0, 148.6, 89.0, 167.2, 91.0, 6.1, 3.0, 1.0, 14.75])),
     (0.0,
      DenseVector([92.0, 1.0, 0.0, 0.0, 0.0, 1.0, 33.0, 243.1, 92.0, 213.8, 92.0, 228.7, 104.0, 12.1, 2.0, 2.0, 14.75])),
     (0.0,
      DenseVector([185.0, 0.0, 0.0, 1.0, 0.0, 1.0, 31.0, 189.8, 126.0, 163.3, 133.0, 264.8, 126.0, 7.5, 3.0, 1.0, 14.81])),
     (0.0,
      DenseVector([148.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 202.0, 102.0, 243.2, 128.0, 261.3, 90.0, 10.9, 3.0, 1.0, 9.26])),
     (0.0,
      DenseVector([94.0, 0.0, 1.0, 0.0, 0.0, 1.0, 38.0, 170.1, 124.0, 193.3, 116.0, 105.9, 73.0, 12.8, 4.0, 1.0, 6.25])),
     (0.0,
      DenseVector([32.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 230.9, 87.0, 187.4, 90.0, 154.0, 53.0, 6.3, 2.0, 0.0, 10.0])),
     (0.0,
      DenseVector([68.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 237.1, 105.0, 223.5, 105.0, 97.4, 79.0, 13.2, 2.0, 1.0, 13.64])),
     (0.0,
      DenseVector([64.0, 0.0, 1.0, 0.0, 0.0, 1.0, 27.0, 182.1, 91.0, 169.7, 98.0, 164.7, 86.0, 10.6, 5.0, 2.0, 16.07])),
     (0.0,
      DenseVector([25.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 119.3, 87.0, 211.5, 101.0, 268.9, 86.0, 10.5, 4.0, 3.0, 9.68])),
     (0.0,
      DenseVector([65.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 116.8, 87.0, 178.9, 93.0, 182.4, 150.0, 14.1, 2.0, 1.0, 14.1])),
     (0.0,
      DenseVector([179.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 219.2, 92.0, 149.4, 125.0, 244.7, 104.0, 6.1, 5.0, 0.0, 7.84])),
     (0.0,
      DenseVector([94.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 252.6, 104.0, 169.0, 125.0, 170.9, 106.0, 11.1, 7.0, 2.0, 8.2])),
     (0.0,
      DenseVector([62.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 147.1, 91.0, 190.4, 107.0, 195.2, 115.0, 12.2, 3.0, 0.0, 17.86])),
     (0.0,
      DenseVector([127.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 202.1, 103.0, 229.4, 86.0, 195.2, 113.0, 11.5, 3.0, 2.0, 21.92])),
     (0.0,
      DenseVector([116.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 173.5, 93.0, 194.1, 76.0, 208.0, 112.0, 16.2, 10.0, 3.0, 20.0])),
     (0.0,
      DenseVector([70.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 232.1, 122.0, 292.3, 112.0, 201.2, 112.0, 0.0, 0.0, 3.0, 18.57])),
     (0.0,
      DenseVector([94.0, 0.0, 0.0, 1.0, 1.0, 1.0, 23.0, 197.1, 125.0, 214.5, 136.0, 282.2, 103.0, 9.5, 5.0, 4.0, 9.43])),
     (1.0,
      DenseVector([126.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 58.2, 94.0, 138.7, 118.0, 136.8, 91.0, 11.9, 1.0, 5.0, 5.77])),
     (0.0,
      DenseVector([67.0, 0.0, 1.0, 0.0, 0.0, 1.0, 36.0, 115.6, 111.0, 237.7, 94.0, 169.9, 103.0, 9.9, 12.0, 2.0, 18.07])),
     (0.0,
      DenseVector([19.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 186.1, 98.0, 254.3, 57.0, 214.0, 127.0, 14.6, 7.0, 2.0, 16.07])),
     (0.0,
      DenseVector([170.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 259.9, 68.0, 245.0, 122.0, 134.4, 121.0, 8.4, 3.0, 3.0, 6.49])),
     (0.0,
      DenseVector([73.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 214.3, 145.0, 268.5, 135.0, 241.2, 92.0, 10.8, 13.0, 1.0, 9.68])),
     (0.0,
      DenseVector([106.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 158.7, 74.0, 64.3, 139.0, 198.5, 103.0, 10.2, 4.0, 1.0, 18.07])),
     (0.0,
      DenseVector([93.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 271.6, 71.0, 229.4, 108.0, 77.3, 121.0, 10.9, 3.0, 2.0, 6.25])),
     (0.0,
      DenseVector([164.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 160.6, 111.0, 163.2, 126.0, 187.1, 112.0, 9.0, 3.0, 1.0, 11.69])),
     (0.0,
      DenseVector([51.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 232.4, 109.0, 187.4, 95.0, 231.2, 107.0, 9.1, 3.0, 1.0, 21.21])),
     (0.0,
      DenseVector([107.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 133.8, 85.0, 180.5, 94.0, 112.2, 115.0, 8.9, 4.0, 0.0, 13.64])),
     (0.0,
      DenseVector([130.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 176.9, 109.0, 90.7, 104.0, 238.0, 69.0, 9.5, 2.0, 1.0, 25.0])),
     (0.0,
      DenseVector([80.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 209.9, 74.0, 195.1, 77.0, 208.2, 119.0, 8.8, 4.0, 2.0, 13.56])),
     (0.0,
      DenseVector([94.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 137.5, 118.0, 203.2, 88.0, 150.0, 131.0, 13.4, 2.0, 0.0, 20.59])),
     (0.0,
      DenseVector([118.0, 0.0, 1.0, 0.0, 0.0, 1.0, 23.0, 289.5, 52.0, 166.6, 111.0, 119.1, 88.0, 9.5, 4.0, 1.0, 14.75])),
     (0.0,
      DenseVector([117.0, 1.0, 0.0, 0.0, 0.0, 1.0, 23.0, 198.1, 86.0, 177.0, 86.0, 180.5, 92.0, 6.8, 6.0, 1.0, 24.29])),
     (0.0,
      DenseVector([78.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 149.7, 119.0, 182.2, 115.0, 261.5, 126.0, 9.7, 8.0, 0.0, 9.43])),
     (1.0,
      DenseVector([208.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 326.5, 67.0, 176.3, 113.0, 181.7, 102.0, 10.7, 6.0, 2.0, 25.0])),
     (1.0,
      DenseVector([131.0, 0.0, 0.0, 1.0, 1.0, 1.0, 26.0, 292.9, 101.0, 199.7, 97.0, 255.3, 127.0, 13.8, 7.0, 4.0, 20.97])),
     (0.0,
      DenseVector([63.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 83.0, 64.0, 177.0, 106.0, 245.7, 89.0, 13.0, 3.0, 0.0, 9.26])),
     (0.0,
      DenseVector([53.0, 1.0, 0.0, 0.0, 0.0, 1.0, 24.0, 145.7, 146.0, 220.5, 136.0, 249.9, 96.0, 13.1, 5.0, 3.0, 17.86])),
     (0.0,
      DenseVector([62.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 182.3, 101.0, 328.2, 93.0, 245.0, 131.0, 11.2, 1.0, 2.0, 14.75])),
     (0.0,
      DenseVector([97.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 218.0, 86.0, 184.0, 94.0, 240.5, 110.0, 6.4, 8.0, 3.0, 24.29])),
     (0.0,
      DenseVector([105.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 140.6, 109.0, 178.6, 51.0, 217.0, 83.0, 6.8, 3.0, 2.0, 21.92])),
     (0.0,
      DenseVector([157.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 152.7, 105.0, 257.5, 80.0, 198.1, 93.0, 9.4, 4.0, 1.0, 21.21])),
     (0.0,
      DenseVector([66.0, 1.0, 0.0, 0.0, 0.0, 1.0, 36.0, 106.7, 76.0, 209.8, 77.0, 190.4, 117.0, 12.1, 2.0, 1.0, 11.11])),
     (0.0,
      DenseVector([122.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 243.8, 98.0, 83.9, 72.0, 179.8, 84.0, 13.7, 8.0, 2.0, 12.68])),
     (0.0,
      DenseVector([38.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 194.4, 94.0, 186.7, 95.0, 223.3, 90.0, 10.8, 5.0, 3.0, 14.1])),
     (0.0,
      DenseVector([106.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 213.9, 95.0, 151.9, 70.0, 260.1, 124.0, 12.2, 5.0, 3.0, 24.29])),
     (0.0,
      DenseVector([99.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 217.2, 112.0, 246.7, 89.0, 226.1, 89.0, 15.8, 7.0, 3.0, 9.23])),
     (0.0,
      DenseVector([99.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 241.1, 72.0, 155.6, 98.0, 188.2, 109.0, 11.6, 10.0, 1.0, 7.84])),
     (0.0,
      DenseVector([144.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 203.5, 100.0, 247.6, 103.0, 194.3, 94.0, 11.9, 11.0, 0.0, 6.25])),
     (0.0,
      DenseVector([82.0, 1.0, 0.0, 0.0, 0.0, 1.0, 24.0, 155.2, 131.0, 244.5, 106.0, 122.4, 68.0, 10.7, 3.0, 1.0, 17.78])),
     (0.0,
      DenseVector([86.0, 0.0, 1.0, 0.0, 0.0, 1.0, 31.0, 167.6, 139.0, 113.0, 118.0, 246.9, 121.0, 12.2, 6.0, 1.0, 6.25])),
     (1.0,
      DenseVector([70.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 226.7, 98.0, 228.1, 115.0, 73.2, 93.0, 17.6, 4.0, 2.0, 12.7])),
     (0.0,
      DenseVector([93.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 179.3, 93.0, 178.6, 98.0, 225.2, 131.0, 11.5, 6.0, 3.0, 7.84])),
     (0.0,
      DenseVector([93.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 151.4, 89.0, 186.4, 76.0, 172.5, 120.0, 10.9, 3.0, 0.0, 12.7])),
     (0.0,
      DenseVector([120.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 180.0, 80.0, 224.2, 82.0, 265.4, 91.0, 4.7, 7.0, 3.0, 12.7])),
     (1.0,
      DenseVector([136.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 250.2, 121.0, 267.1, 118.0, 151.0, 114.0, 13.0, 2.0, 1.0, 24.29])),
     (0.0,
      DenseVector([106.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 223.0, 121.0, 110.1, 98.0, 188.7, 107.0, 7.1, 12.0, 0.0, 10.0])),
     (0.0,
      DenseVector([81.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 183.6, 116.0, 152.6, 98.0, 212.2, 99.0, 12.2, 6.0, 3.0, 21.21])),
     (0.0,
      DenseVector([127.0, 0.0, 1.0, 0.0, 0.0, 1.0, 22.0, 166.0, 114.0, 174.5, 103.0, 244.9, 68.0, 10.2, 6.0, 1.0, 9.43])),
     (0.0,
      DenseVector([65.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 136.1, 112.0, 272.9, 96.0, 220.2, 104.0, 4.4, 2.0, 1.0, 21.54])),
     (0.0,
      DenseVector([35.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 149.3, 113.0, 242.2, 122.0, 174.3, 104.0, 8.9, 6.0, 2.0, 20.97])),
     (0.0,
      DenseVector([88.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 65.4, 97.0, 168.2, 76.0, 236.0, 113.0, 13.8, 1.0, 2.0, 14.75])),
     (0.0,
      DenseVector([65.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 213.4, 111.0, 234.5, 94.0, 250.1, 123.0, 2.7, 4.0, 1.0, 12.68])),
     (0.0,
      DenseVector([123.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 206.9, 85.0, 244.7, 78.0, 221.5, 136.0, 7.7, 2.0, 3.0, 11.11])),
     (0.0,
      DenseVector([126.0, 0.0, 1.0, 0.0, 0.0, 1.0, 27.0, 186.2, 78.0, 189.6, 83.0, 76.5, 139.0, 9.6, 3.0, 2.0, 6.82])),
     (0.0,
      DenseVector([104.0, 1.0, 0.0, 0.0, 0.0, 1.0, 23.0, 280.2, 136.0, 220.5, 92.0, 136.9, 102.0, 13.3, 3.0, 4.0, 6.49])),
     (0.0,
      DenseVector([45.0, 1.0, 0.0, 0.0, 0.0, 1.0, 22.0, 196.6, 84.0, 313.2, 92.0, 163.3, 108.0, 11.9, 3.0, 0.0, 13.56])),
     (1.0,
      DenseVector([93.0, 0.0, 1.0, 0.0, 1.0, 0.0, 0.0, 312.0, 109.0, 129.4, 100.0, 217.6, 74.0, 10.5, 2.0, 0.0, 24.29])),
     (0.0,
      DenseVector([63.0, 1.0, 0.0, 0.0, 1.0, 1.0, 36.0, 199.0, 110.0, 291.3, 111.0, 197.6, 92.0, 11.0, 6.0, 1.0, 12.82])),
     (0.0,
      DenseVector([100.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 203.1, 96.0, 217.0, 126.0, 180.9, 122.0, 13.5, 2.0, 3.0, 14.75])),
     (0.0,
      DenseVector([53.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 168.8, 97.0, 220.3, 87.0, 154.3, 113.0, 10.9, 2.0, 0.0, 21.21])),
     (0.0,
      DenseVector([92.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 173.1, 140.0, 240.3, 105.0, 233.2, 117.0, 9.0, 5.0, 1.0, 12.33])),
     (1.0,
      DenseVector([139.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 134.4, 106.0, 211.3, 98.0, 193.6, 125.0, 10.2, 2.0, 5.0, 17.86])),
     (0.0,
      DenseVector([110.0, 0.0, 1.0, 0.0, 0.0, 1.0, 40.0, 202.6, 103.0, 118.8, 128.0, 234.9, 98.0, 9.0, 9.0, 2.0, 13.33])),
     (0.0,
      DenseVector([110.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 74.5, 117.0, 200.8, 98.0, 192.2, 101.0, 9.8, 7.0, 3.0, 8.62])),
     (0.0,
      DenseVector([215.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 83.6, 148.0, 120.9, 91.0, 226.6, 110.0, 10.7, 9.0, 0.0, 11.69])),
     (0.0,
      DenseVector([73.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 192.2, 86.0, 168.6, 116.0, 139.8, 87.0, 9.4, 6.0, 1.0, 10.0])),
     (0.0,
      DenseVector([138.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 220.2, 89.0, 88.3, 125.0, 195.3, 79.0, 12.9, 5.0, 0.0, 26.47])),
     (1.0,
      DenseVector([137.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 135.1, 95.0, 134.1, 102.0, 223.1, 81.0, 12.3, 2.0, 2.0, 21.21])),
     (0.0,
      DenseVector([36.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 253.4, 77.0, 182.4, 151.0, 275.8, 103.0, 8.4, 2.0, 1.0, 12.68])),
     (0.0,
      DenseVector([85.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 225.0, 81.0, 176.9, 63.0, 194.3, 110.0, 7.1, 2.0, 3.0, 9.43])),
     (1.0,
      DenseVector([108.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 198.5, 99.0, 267.8, 60.0, 354.9, 75.0, 9.4, 3.0, 0.0, 6.49])),
     (0.0,
      DenseVector([22.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 110.3, 107.0, 166.5, 93.0, 202.3, 96.0, 9.5, 5.0, 0.0, 23.33])),
     (0.0,
      DenseVector([107.0, 1.0, 0.0, 0.0, 0.0, 1.0, 37.0, 60.0, 102.0, 102.2, 80.0, 261.8, 106.0, 11.1, 3.0, 0.0, 9.23])),
     (0.0,
      DenseVector([51.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 214.8, 94.0, 149.7, 58.0, 283.4, 66.0, 10.2, 5.0, 0.0, 12.68])),
     (0.0,
      DenseVector([94.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 181.8, 85.0, 202.4, 98.0, 245.9, 97.0, 9.2, 2.0, 4.0, 6.25])),
     (0.0,
      DenseVector([119.0, 0.0, 0.0, 1.0, 0.0, 1.0, 23.0, 154.0, 114.0, 278.0, 137.0, 228.4, 112.0, 11.8, 4.0, 2.0, 9.68])),
     (1.0,
      DenseVector([33.0, 1.0, 0.0, 0.0, 0.0, 1.0, 29.0, 157.4, 99.0, 117.9, 80.0, 279.2, 79.0, 13.9, 11.0, 4.0, 14.1])),
     (0.0,
      DenseVector([106.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 207.9, 91.0, 172.0, 109.0, 191.8, 143.0, 14.4, 7.0, 4.0, 26.47])),
     (0.0,
      DenseVector([82.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 207.0, 90.0, 232.9, 83.0, 172.4, 108.0, 9.1, 8.0, 3.0, 21.54])),
     (0.0,
      DenseVector([86.0, 0.0, 0.0, 1.0, 0.0, 1.0, 41.0, 119.0, 101.0, 230.0, 134.0, 236.9, 58.0, 9.5, 3.0, 0.0, 21.92])),
     (0.0,
      DenseVector([97.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 143.7, 117.0, 273.0, 82.0, 178.3, 81.0, 10.9, 3.0, 0.0, 25.0])),
     (0.0,
      DenseVector([106.0, 0.0, 1.0, 0.0, 0.0, 1.0, 32.0, 165.9, 126.0, 216.5, 93.0, 173.1, 86.0, 14.1, 8.0, 4.0, 12.7])),
     (0.0,
      DenseVector([108.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 138.6, 122.0, 172.3, 117.0, 231.6, 92.0, 9.8, 3.0, 1.0, 9.26])),
     (0.0,
      DenseVector([114.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 84.7, 118.0, 249.9, 86.0, 193.4, 95.0, 14.5, 8.0, 1.0, 25.0])),
     (1.0,
      DenseVector([92.0, 0.0, 1.0, 0.0, 1.0, 0.0, 0.0, 62.6, 111.0, 180.6, 126.0, 221.7, 80.0, 10.4, 2.0, 1.0, 18.57])),
     (0.0,
      DenseVector([59.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 155.2, 79.0, 235.3, 123.0, 169.4, 80.0, 8.7, 4.0, 1.0, 13.89])),
     (0.0,
      DenseVector([24.0, 0.0, 0.0, 1.0, 0.0, 1.0, 25.0, 164.9, 110.0, 209.3, 105.0, 231.2, 55.0, 6.7, 9.0, 1.0, 17.86])),
     (0.0,
      DenseVector([151.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 134.5, 88.0, 143.1, 112.0, 223.9, 61.0, 15.4, 1.0, 1.0, 8.62])),
     (0.0,
      DenseVector([117.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 143.3, 103.0, 211.3, 108.0, 185.2, 96.0, 11.5, 3.0, 1.0, 9.68])),
     (0.0,
      DenseVector([78.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 168.3, 110.0, 221.2, 73.0, 241.0, 136.0, 12.5, 1.0, 1.0, 23.33])),
     (0.0,
      DenseVector([155.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 262.4, 55.0, 194.6, 113.0, 146.5, 85.0, 8.3, 6.0, 2.0, 16.18])),
     (0.0,
      DenseVector([114.0, 0.0, 0.0, 1.0, 0.0, 1.0, 30.0, 206.2, 79.0, 260.0, 91.0, 291.6, 83.0, 11.4, 6.0, 1.0, 9.43])),
     (0.0,
      DenseVector([114.0, 0.0, 0.0, 1.0, 0.0, 1.0, 28.0, 225.8, 94.0, 193.0, 117.0, 232.4, 100.0, 8.4, 9.0, 4.0, 9.23])),
     (0.0,
      DenseVector([119.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 138.3, 89.0, 170.5, 78.0, 263.9, 98.0, 13.5, 6.0, 3.0, 16.07])),
     (0.0,
      DenseVector([64.0, 0.0, 0.0, 1.0, 0.0, 1.0, 48.0, 94.4, 104.0, 136.2, 101.0, 147.4, 89.0, 4.5, 4.0, 0.0, 11.11])),
     (0.0,
      DenseVector([118.0, 0.0, 1.0, 0.0, 1.0, 0.0, 0.0, 160.0, 123.0, 175.4, 96.0, 184.8, 99.0, 9.9, 3.0, 2.0, 16.92])),
     (0.0,
      DenseVector([101.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 206.6, 105.0, 224.9, 117.0, 249.9, 100.0, 14.6, 3.0, 0.0, 17.78])),
     (0.0,
      DenseVector([117.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 134.7, 121.0, 180.0, 83.0, 200.9, 104.0, 7.7, 3.0, 1.0, 14.75])),
     (0.0,
      DenseVector([49.0, 1.0, 0.0, 0.0, 0.0, 1.0, 28.0, 214.4, 78.0, 235.2, 100.0, 206.2, 107.0, 8.0, 13.0, 3.0, 10.0])),
     (0.0,
      DenseVector([139.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 192.8, 104.0, 234.4, 96.0, 203.2, 101.0, 13.0, 3.0, 3.0, 11.69])),
     (0.0,
      DenseVector([92.0, 0.0, 1.0, 0.0, 0.0, 1.0, 28.0, 151.1, 90.0, 194.8, 79.0, 239.2, 114.0, 10.0, 3.0, 1.0, 17.78])),
     (0.0,
      DenseVector([83.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 221.4, 103.0, 231.8, 103.0, 122.5, 100.0, 9.8, 5.0, 3.0, 21.21])),
     (0.0,
      DenseVector([148.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 218.9, 88.0, 208.0, 85.0, 203.3, 99.0, 11.1, 4.0, 0.0, 5.66])),
     (1.0,
      DenseVector([144.0, 0.0, 1.0, 0.0, 0.0, 1.0, 48.0, 189.8, 96.0, 123.4, 67.0, 214.2, 106.0, 6.5, 2.0, 2.0, 13.33])),
     (0.0,
      DenseVector([131.0, 1.0, 0.0, 0.0, 0.0, 1.0, 25.0, 192.7, 85.0, 225.9, 105.0, 254.2, 59.0, 10.9, 6.0, 2.0, 10.0])),
     (0.0,
      DenseVector([146.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 204.4, 135.0, 219.1, 90.0, 222.7, 114.0, 10.5, 6.0, 3.0, 10.96])),
     (0.0,
      DenseVector([143.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 172.3, 97.0, 174.0, 108.0, 188.2, 119.0, 13.0, 4.0, 2.0, 20.59])),
     (0.0,
      DenseVector([81.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 198.4, 93.0, 210.9, 108.0, 193.3, 71.0, 10.4, 6.0, 2.0, 17.86])),
     (0.0,
      DenseVector([48.0, 1.0, 0.0, 0.0, 0.0, 1.0, 37.0, 211.7, 115.0, 159.9, 84.0, 144.1, 80.0, 12.2, 1.0, 1.0, 5.77])),
     (0.0,
      DenseVector([86.0, 1.0, 0.0, 0.0, 0.0, 1.0, 28.0, 221.6, 74.0, 288.4, 100.0, 240.3, 105.0, 9.0, 2.0, 1.0, 21.92])),
     (0.0,
      DenseVector([71.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 197.9, 108.0, 181.5, 109.0, 281.4, 56.0, 6.7, 5.0, 3.0, 14.75])),
     (0.0,
      DenseVector([145.0, 0.0, 1.0, 0.0, 0.0, 1.0, 24.0, 147.5, 90.0, 175.7, 108.0, 252.1, 102.0, 15.6, 3.0, 2.0, 13.33])),
     (0.0,
      DenseVector([137.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 206.4, 122.0, 128.0, 102.0, 194.5, 84.0, 8.8, 5.0, 2.0, 21.92])),
     (0.0,
      DenseVector([137.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 205.9, 88.0, 209.3, 86.0, 289.9, 84.0, 14.5, 4.0, 2.0, 18.57])),
     (0.0,
      DenseVector([167.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 207.6, 88.0, 132.4, 63.0, 255.2, 98.0, 14.1, 5.0, 0.0, 10.0])),
     (1.0,
      DenseVector([89.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 303.9, 95.0, 260.9, 114.0, 312.1, 89.0, 5.3, 3.0, 1.0, 14.75])),
     (0.0,
      DenseVector([199.0, 1.0, 0.0, 0.0, 0.0, 1.0, 34.0, 230.6, 121.0, 219.4, 99.0, 299.3, 94.0, 8.0, 2.0, 0.0, 16.22])),
     (0.0,
      DenseVector([132.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 99.5, 110.0, 129.1, 80.0, 125.1, 124.0, 9.7, 3.0, 0.0, 8.2])),
     (0.0,
      DenseVector([94.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 177.1, 112.0, 194.0, 112.0, 146.7, 108.0, 5.9, 4.0, 1.0, 8.97])),
     (1.0,
      DenseVector([96.0, 1.0, 0.0, 0.0, 0.0, 1.0, 37.0, 172.7, 93.0, 120.1, 116.0, 216.1, 86.0, 10.3, 5.0, 5.0, 16.22])),
     (0.0,
      DenseVector([96.0, 0.0, 0.0, 1.0, 0.0, 1.0, 18.0, 172.7, 86.0, 133.4, 113.0, 259.5, 70.0, 9.8, 3.0, 1.0, 8.97])),
     (0.0,
      DenseVector([166.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 204.2, 115.0, 179.9, 152.0, 216.8, 109.0, 9.5, 5.0, 1.0, 12.68])),
     (0.0,
      DenseVector([74.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 85.7, 83.0, 247.7, 67.0, 142.4, 85.0, 10.1, 5.0, 2.0, 9.26])),
     (0.0,
      DenseVector([36.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 157.6, 117.0, 184.3, 58.0, 240.4, 99.0, 11.9, 1.0, 0.0, 20.0])),
     (0.0,
      DenseVector([113.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 215.5, 129.0, 218.7, 117.0, 207.1, 91.0, 6.6, 9.0, 4.0, 20.97])),
     (0.0,
      DenseVector([94.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 181.5, 98.0, 199.9, 88.0, 287.7, 114.0, 6.6, 5.0, 1.0, 17.86])),
     (0.0,
      DenseVector([67.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 171.7, 80.0, 110.4, 81.0, 195.4, 111.0, 11.9, 4.0, 2.0, 24.29])),
     (1.0,
      DenseVector([127.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 266.6, 106.0, 264.8, 168.0, 207.2, 119.0, 5.9, 2.0, 1.0, 12.7])),
     (1.0,
      DenseVector([121.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 170.4, 108.0, 350.5, 68.0, 297.0, 87.0, 11.2, 3.0, 0.0, 9.23])),
     (0.0,
      DenseVector([158.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 158.0, 106.0, 292.5, 114.0, 241.1, 89.0, 9.1, 4.0, 1.0, 6.82])),
     (0.0,
      DenseVector([136.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 92.0, 117.0, 253.6, 77.0, 214.1, 90.0, 10.3, 10.0, 1.0, 6.25])),
     (0.0,
      DenseVector([196.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 234.0, 109.0, 249.5, 114.0, 173.1, 70.0, 9.1, 5.0, 2.0, 11.11])),
     (1.0,
      DenseVector([113.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 272.1, 111.0, 268.5, 118.0, 213.8, 105.0, 8.5, 10.0, 1.0, 10.96])),
     (1.0,
      DenseVector([122.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 296.4, 99.0, 214.8, 89.0, 133.9, 107.0, 11.4, 3.0, 4.0, 12.68])),
     (0.0,
      DenseVector([112.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 194.4, 101.0, 190.3, 82.0, 183.4, 107.0, 11.4, 2.0, 0.0, 9.23])),
     (0.0,
      DenseVector([209.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 227.2, 128.0, 258.4, 92.0, 183.5, 74.0, 8.9, 4.0, 3.0, 13.33])),
     (1.0,
      DenseVector([62.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 248.7, 109.0, 220.0, 118.0, 265.7, 78.0, 13.2, 2.0, 1.0, 17.86])),
     (0.0,
      DenseVector([110.0, 1.0, 0.0, 0.0, 0.0, 1.0, 38.0, 236.3, 102.0, 195.9, 112.0, 183.5, 82.0, 9.7, 6.0, 1.0, 25.0])),
     (0.0,
      DenseVector([16.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 205.6, 69.0, 169.5, 93.0, 220.1, 64.0, 10.9, 3.0, 0.0, 6.49])),
     (0.0,
      DenseVector([73.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 94.1, 136.0, 280.3, 122.0, 205.0, 77.0, 9.8, 4.0, 0.0, 16.92])),
     (0.0,
      DenseVector([128.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 125.2, 99.0, 205.4, 107.0, 254.4, 111.0, 18.9, 2.0, 0.0, 12.33])),
     (0.0,
      DenseVector([39.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 60.4, 158.0, 306.2, 120.0, 123.9, 46.0, 12.4, 3.0, 1.0, 16.92])),
     (0.0,
      DenseVector([103.0, 1.0, 0.0, 0.0, 0.0, 1.0, 28.0, 121.0, 105.0, 270.4, 100.0, 160.5, 76.0, 7.7, 4.0, 2.0, 14.81])),
     (0.0,
      DenseVector([119.0, 1.0, 0.0, 0.0, 0.0, 1.0, 29.0, 117.8, 66.0, 256.8, 114.0, 147.6, 76.0, 7.6, 3.0, 3.0, 9.23])),
     (0.0,
      DenseVector([173.0, 0.0, 0.0, 1.0, 0.0, 1.0, 21.0, 232.4, 96.0, 211.9, 118.0, 273.0, 102.0, 5.0, 5.0, 3.0, 12.33])),
     (1.0,
      DenseVector([128.0, 0.0, 0.0, 1.0, 1.0, 1.0, 32.0, 223.5, 81.0, 188.8, 74.0, 154.9, 101.0, 9.4, 2.0, 2.0, 13.33])),
     (0.0,
      DenseVector([86.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 176.3, 79.0, 259.2, 97.0, 287.4, 78.0, 6.2, 3.0, 0.0, 16.92])),
     (0.0,
      DenseVector([114.0, 1.0, 0.0, 0.0, 0.0, 1.0, 32.0, 125.2, 79.0, 177.8, 105.0, 232.4, 89.0, 12.9, 3.0, 1.0, 11.69])),
     (0.0,
      DenseVector([104.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 138.7, 107.0, 256.9, 113.0, 234.9, 74.0, 10.0, 3.0, 0.0, 6.49])),
     (0.0,
      DenseVector([148.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 86.3, 134.0, 246.6, 92.0, 251.6, 91.0, 11.3, 4.0, 1.0, 14.1])),
     (0.0,
      DenseVector([129.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 207.0, 91.0, 154.9, 121.0, 245.1, 112.0, 13.4, 5.0, 3.0, 6.49])),
     (0.0,
      DenseVector([100.0, 0.0, 0.0, 1.0, 0.0, 1.0, 30.0, 58.8, 104.0, 219.5, 107.0, 152.3, 118.0, 7.1, 3.0, 0.0, 20.97])),
     (0.0,
      DenseVector([121.0, 0.0, 1.0, 0.0, 0.0, 1.0, 35.0, 68.7, 95.0, 209.2, 69.0, 197.4, 42.0, 11.4, 4.0, 1.0, 10.0])),
     (0.0,
      DenseVector([143.0, 0.0, 1.0, 0.0, 0.0, 1.0, 33.0, 239.2, 109.0, 235.5, 112.0, 156.3, 95.0, 9.5, 4.0, 1.0, 14.81])),
     (0.0,
      DenseVector([76.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 198.3, 130.0, 217.1, 86.0, 188.4, 96.0, 12.5, 3.0, 0.0, 6.82])),
     (0.0,
      DenseVector([158.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 205.2, 97.0, 240.6, 77.0, 79.7, 108.0, 14.4, 12.0, 0.0, 6.25])),
     (0.0,
      DenseVector([116.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 192.1, 98.0, 312.9, 135.0, 130.2, 94.0, 7.9, 2.0, 3.0, 12.7])),
     (1.0,
      DenseVector([54.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 272.6, 83.0, 248.7, 74.0, 197.4, 111.0, 9.5, 2.0, 1.0, 20.59])),
     (1.0,
      DenseVector([86.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 128.3, 121.0, 197.1, 93.0, 138.4, 152.0, 12.2, 5.0, 7.0, 10.0])),
     (0.0,
      DenseVector([108.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 169.6, 99.0, 264.1, 87.0, 206.3, 78.0, 9.3, 4.0, 0.0, 14.75])),
     (0.0,
      DenseVector([66.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 201.3, 95.0, 152.8, 66.0, 233.2, 101.0, 7.5, 4.0, 1.0, 20.59])),
     (0.0,
      DenseVector([151.0, 0.0, 1.0, 0.0, 0.0, 1.0, 17.0, 214.7, 97.0, 138.5, 90.0, 169.1, 44.0, 8.6, 4.0, 1.0, 13.56])),
     (0.0,
      DenseVector([99.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 169.2, 70.0, 271.5, 77.0, 170.2, 104.0, 10.6, 2.0, 0.0, 23.33])),
     (0.0,
      DenseVector([55.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 194.1, 121.0, 176.6, 110.0, 302.8, 136.0, 7.0, 7.0, 2.0, 21.21])),
     (0.0,
      DenseVector([77.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 233.8, 104.0, 266.5, 94.0, 212.7, 104.0, 7.6, 3.0, 2.0, 14.1])),
     (0.0,
      DenseVector([78.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 225.1, 67.0, 199.2, 127.0, 175.5, 102.0, 14.6, 2.0, 0.0, 5.77])),
     (1.0,
      DenseVector([89.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 213.0, 63.0, 176.6, 71.0, 262.6, 126.0, 9.1, 1.0, 1.0, 14.81])),
     (0.0,
      DenseVector([101.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 183.9, 115.0, 255.9, 101.0, 275.0, 145.0, 10.8, 11.0, 1.0, 17.86])),
     (0.0,
      DenseVector([44.0, 1.0, 0.0, 0.0, 0.0, 1.0, 34.0, 221.8, 105.0, 161.7, 85.0, 227.7, 62.0, 14.0, 7.0, 0.0, 8.62])),
     (0.0,
      DenseVector([98.0, 0.0, 1.0, 0.0, 0.0, 1.0, 21.0, 64.6, 98.0, 176.1, 86.0, 244.8, 84.0, 0.0, 0.0, 2.0, 12.68])),
     (0.0,
      DenseVector([64.0, 0.0, 0.0, 1.0, 0.0, 1.0, 37.0, 154.6, 92.0, 83.4, 103.0, 165.9, 99.0, 13.3, 3.0, 1.0, 23.33])),
     (0.0,
      DenseVector([141.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 260.2, 131.0, 179.2, 120.0, 135.0, 119.0, 7.2, 8.0, 3.0, 6.49])),
     (0.0,
      DenseVector([81.0, 1.0, 0.0, 0.0, 0.0, 1.0, 33.0, 161.6, 117.0, 123.0, 90.0, 261.3, 101.0, 12.2, 5.0, 1.0, 8.97])),
     (0.0,
      DenseVector([162.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 220.6, 117.0, 155.2, 121.0, 186.7, 89.0, 10.5, 11.0, 1.0, 10.96])),
     (0.0,
      DenseVector([83.0, 1.0, 0.0, 0.0, 0.0, 1.0, 41.0, 155.9, 122.0, 162.3, 107.0, 127.6, 105.0, 13.1, 5.0, 3.0, 6.25])),
     (1.0,
      DenseVector([100.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 107.0, 63.0, 105.7, 67.0, 243.1, 74.0, 12.8, 3.0, 4.0, 12.7])),
     (0.0,
      DenseVector([59.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 182.5, 104.0, 204.7, 95.0, 229.9, 100.0, 11.3, 8.0, 4.0, 5.77])),
     (0.0,
      DenseVector([179.0, 1.0, 0.0, 0.0, 1.0, 1.0, 38.0, 220.1, 78.0, 234.3, 71.0, 237.3, 85.0, 10.1, 4.0, 4.0, 20.0])),
     (0.0,
      DenseVector([79.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 152.2, 112.0, 177.2, 132.0, 96.4, 87.0, 5.3, 3.0, 1.0, 20.0])),
     (0.0,
      DenseVector([117.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 181.5, 95.0, 205.1, 88.0, 204.0, 82.0, 14.7, 9.0, 2.0, 5.77])),
     (1.0,
      DenseVector([64.0, 0.0, 1.0, 0.0, 1.0, 0.0, 0.0, 236.2, 77.0, 218.6, 85.0, 194.1, 97.0, 13.2, 2.0, 2.0, 21.54])),
     (0.0,
      DenseVector([31.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 166.1, 105.0, 79.3, 93.0, 213.7, 98.0, 12.7, 2.0, 1.0, 20.97])),
     (0.0,
      DenseVector([124.0, 0.0, 1.0, 0.0, 1.0, 0.0, 0.0, 244.6, 89.0, 188.8, 80.0, 206.0, 114.0, 11.3, 4.0, 1.0, 26.47])),
     (0.0,
      DenseVector([122.0, 0.0, 1.0, 0.0, 0.0, 1.0, 23.0, 134.2, 85.0, 227.3, 132.0, 122.4, 96.0, 8.5, 2.0, 2.0, 9.68])),
     (0.0,
      DenseVector([37.0, 0.0, 1.0, 0.0, 1.0, 1.0, 39.0, 149.7, 122.0, 211.1, 75.0, 114.3, 90.0, 9.2, 4.0, 1.0, 8.2])),
     (0.0,
      DenseVector([90.0, 0.0, 1.0, 0.0, 0.0, 1.0, 29.0, 150.1, 109.0, 264.7, 103.0, 178.4, 97.0, 5.8, 4.0, 1.0, 23.33])),
     (1.0,
      DenseVector([159.0, 0.0, 1.0, 0.0, 1.0, 0.0, 0.0, 257.1, 53.0, 312.2, 127.0, 183.0, 82.0, 8.8, 6.0, 1.0, 13.64])),
     (0.0,
      DenseVector([148.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 124.4, 83.0, 179.7, 81.0, 253.0, 99.0, 11.3, 6.0, 0.0, 14.75])),
     (0.0,
      DenseVector([39.0, 1.0, 0.0, 0.0, 0.0, 1.0, 36.0, 141.7, 121.0, 232.3, 113.0, 222.1, 131.0, 12.0, 5.0, 1.0, 12.82])),
     (0.0,
      DenseVector([77.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 230.0, 87.0, 103.2, 138.0, 309.6, 136.0, 11.3, 3.0, 2.0, 21.54])),
     (0.0,
      DenseVector([194.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 162.3, 88.0, 213.7, 118.0, 192.1, 81.0, 10.9, 2.0, 0.0, 14.75])),
     (1.0,
      DenseVector([154.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 350.8, 75.0, 216.5, 94.0, 253.9, 100.0, 10.1, 9.0, 1.0, 13.64])),
     (0.0,
      DenseVector([112.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 193.3, 96.0, 264.1, 123.0, 128.6, 115.0, 9.1, 3.0, 4.0, 16.18])),
     (0.0,
      DenseVector([45.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 78.2, 127.0, 253.4, 108.0, 255.0, 100.0, 18.0, 3.0, 1.0, 24.29])),
     (0.0,
      DenseVector([132.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 83.4, 110.0, 232.2, 137.0, 146.7, 114.0, 7.6, 5.0, 1.0, 18.57])),
     (0.0,
      DenseVector([128.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 195.6, 99.0, 267.8, 120.0, 164.9, 76.0, 16.0, 2.0, 0.0, 16.92])),
     (0.0,
      DenseVector([135.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 201.8, 81.0, 225.0, 114.0, 204.4, 82.0, 10.3, 6.0, 1.0, 16.18])),
     (0.0,
      DenseVector([56.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 197.0, 110.0, 222.8, 102.0, 225.3, 91.0, 10.6, 6.0, 2.0, 9.68])),
     (1.0,
      DenseVector([151.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 218.0, 57.0, 114.4, 88.0, 269.2, 95.0, 12.4, 1.0, 0.0, 26.47])),
     (0.0,
      DenseVector([32.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 164.8, 98.0, 229.9, 96.0, 167.3, 108.0, 14.8, 2.0, 2.0, 18.07])),
     (0.0,
      DenseVector([90.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 179.2, 77.0, 210.7, 99.0, 276.9, 58.0, 9.2, 6.0, 2.0, 6.25])),
     (0.0,
      DenseVector([87.0, 1.0, 0.0, 0.0, 0.0, 1.0, 21.0, 214.0, 113.0, 180.0, 114.0, 134.5, 82.0, 10.6, 5.0, 0.0, 13.33])),
     (0.0,
      DenseVector([138.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 170.5, 87.0, 118.2, 116.0, 187.9, 111.0, 11.2, 7.0, 2.0, 9.26])),
     (0.0,
      DenseVector([79.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 205.7, 123.0, 214.5, 108.0, 226.1, 106.0, 6.7, 18.0, 1.0, 9.68])),
     (1.0,
      DenseVector([95.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 165.5, 84.0, 286.2, 112.0, 198.9, 89.0, 11.5, 2.0, 1.0, 11.11])),
     (0.0,
      DenseVector([127.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 221.0, 100.0, 160.7, 113.0, 233.1, 96.0, 6.8, 4.0, 2.0, 18.57])),
     (0.0,
      DenseVector([137.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 242.1, 118.0, 191.0, 93.0, 218.6, 50.0, 14.7, 2.0, 3.0, 13.33])),
     (0.0,
      DenseVector([97.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 151.6, 107.0, 155.4, 96.0, 240.0, 112.0, 14.7, 4.0, 1.0, 14.75])),
     (0.0,
      DenseVector([149.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 176.2, 87.0, 145.0, 81.0, 249.5, 92.0, 5.7, 4.0, 0.0, 14.1])),
     (0.0,
      DenseVector([117.0, 1.0, 0.0, 0.0, 1.0, 1.0, 22.0, 196.0, 82.0, 322.7, 82.0, 225.6, 120.0, 3.7, 5.0, 1.0, 12.68])),
     (0.0,
      DenseVector([84.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 159.5, 125.0, 247.1, 90.0, 187.9, 82.0, 7.2, 4.0, 2.0, 12.82])),
     (0.0,
      DenseVector([137.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 230.2, 113.0, 220.4, 79.0, 204.7, 111.0, 10.7, 7.0, 4.0, 18.57])),
     (0.0,
      DenseVector([99.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 146.7, 64.0, 274.0, 99.0, 321.3, 98.0, 8.9, 1.0, 3.0, 16.22])),
     (0.0,
      DenseVector([54.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 210.5, 102.0, 204.5, 83.0, 127.8, 53.0, 8.5, 5.0, 1.0, 16.07])),
     (0.0,
      DenseVector([85.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 102.0, 95.0, 270.2, 139.0, 148.2, 105.0, 10.7, 3.0, 1.0, 8.97])),
     (0.0,
      DenseVector([150.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 126.0, 99.0, 238.5, 73.0, 285.1, 100.0, 10.2, 6.0, 3.0, 21.54])),
     (0.0,
      DenseVector([43.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 168.4, 125.0, 243.8, 89.0, 214.7, 102.0, 11.1, 2.0, 1.0, 9.43])),
     (0.0,
      DenseVector([35.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 105.6, 129.0, 258.2, 129.0, 213.1, 77.0, 8.7, 3.0, 0.0, 16.92])),
     (0.0,
      DenseVector([98.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 206.5, 92.0, 176.2, 152.0, 232.8, 115.0, 12.4, 5.0, 5.0, 24.29])),
     (0.0,
      DenseVector([112.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 217.1, 76.0, 205.2, 100.0, 185.7, 91.0, 9.4, 3.0, 2.0, 17.78])),
     (1.0,
      DenseVector([16.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 229.6, 78.0, 205.7, 108.0, 166.2, 91.0, 10.8, 2.0, 0.0, 8.97])),
     (0.0,
      DenseVector([98.0, 1.0, 0.0, 0.0, 0.0, 1.0, 22.0, 278.3, 89.0, 93.4, 143.0, 107.6, 42.0, 9.7, 5.0, 0.0, 9.43])),
     (0.0,
      DenseVector([84.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 138.6, 102.0, 199.0, 93.0, 204.1, 137.0, 7.8, 4.0, 0.0, 25.0])),
     (1.0,
      DenseVector([94.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 234.4, 103.0, 279.3, 109.0, 234.2, 121.0, 2.0, 2.0, 1.0, 14.1])),
     (0.0,
      DenseVector([84.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 181.5, 129.0, 130.7, 112.0, 186.5, 118.0, 8.5, 4.0, 1.0, 8.62])),
     (1.0,
      DenseVector([66.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 167.3, 91.0, 230.0, 68.0, 191.7, 118.0, 10.6, 5.0, 1.0, 9.26])),
     (0.0,
      DenseVector([98.0, 1.0, 0.0, 0.0, 0.0, 1.0, 31.0, 121.0, 105.0, 218.9, 98.0, 226.7, 110.0, 12.0, 1.0, 1.0, 14.81])),
     (0.0,
      DenseVector([74.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 221.1, 124.0, 110.8, 94.0, 240.1, 112.0, 10.6, 3.0, 0.0, 12.33])),
     (0.0,
      DenseVector([96.0, 0.0, 1.0, 0.0, 0.0, 1.0, 26.0, 145.8, 108.0, 192.2, 89.0, 165.1, 96.0, 9.9, 2.0, 1.0, 13.89])),
     (0.0,
      DenseVector([119.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 222.8, 122.0, 163.2, 107.0, 160.6, 112.0, 11.2, 6.0, 1.0, 13.56])),
     (0.0,
      DenseVector([73.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 183.4, 80.0, 242.0, 115.0, 201.4, 100.0, 7.5, 3.0, 4.0, 12.82])),
     (0.0,
      DenseVector([92.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 264.3, 91.0, 160.9, 115.0, 198.6, 73.0, 9.3, 5.0, 0.0, 8.97])),
     (0.0,
      DenseVector([21.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 146.0, 78.0, 109.7, 79.0, 247.4, 108.0, 6.8, 7.0, 0.0, 8.62])),
     (1.0,
      DenseVector([122.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 157.1, 134.0, 184.9, 122.0, 197.2, 59.0, 8.5, 5.0, 4.0, 14.75])),
     (0.0,
      DenseVector([133.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 127.3, 108.0, 251.3, 81.0, 135.0, 88.0, 10.3, 3.0, 1.0, 9.23])),
     (0.0,
      DenseVector([145.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 187.9, 110.0, 197.0, 117.0, 167.0, 108.0, 4.8, 4.0, 2.0, 25.0])),
     (0.0,
      DenseVector([25.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 178.8, 90.0, 141.2, 72.0, 203.0, 99.0, 8.4, 5.0, 2.0, 14.1])),
     (0.0,
      DenseVector([64.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 97.2, 80.0, 186.2, 90.0, 189.0, 92.0, 10.4, 6.0, 2.0, 21.21])),
     (0.0,
      DenseVector([85.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 259.8, 85.0, 242.3, 117.0, 168.8, 72.0, 5.4, 1.0, 0.0, 8.2])),
     (0.0,
      DenseVector([126.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 256.5, 112.0, 199.5, 90.0, 188.3, 122.0, 7.0, 5.0, 3.0, 21.54])),
     (0.0,
      DenseVector([76.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 169.5, 77.0, 124.0, 87.0, 219.4, 92.0, 10.0, 3.0, 0.0, 14.1])),
     (1.0,
      DenseVector([113.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 239.7, 47.0, 282.9, 110.0, 238.4, 88.0, 8.7, 3.0, 2.0, 14.75])),
     (1.0,
      DenseVector([224.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 171.5, 99.0, 160.0, 103.0, 212.4, 102.0, 5.0, 2.0, 1.0, 14.75])),
     (0.0,
      DenseVector([117.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 239.9, 84.0, 174.8, 106.0, 209.5, 93.0, 9.8, 2.0, 0.0, 6.25])),
     (0.0,
      DenseVector([128.0, 0.0, 1.0, 0.0, 0.0, 1.0, 34.0, 142.3, 73.0, 194.8, 79.0, 239.3, 81.0, 16.0, 6.0, 1.0, 13.33])),
     (0.0,
      DenseVector([115.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 184.1, 98.0, 327.0, 73.0, 212.5, 106.0, 7.5, 6.0, 2.0, 21.21])),
     (0.0,
      DenseVector([141.0, 1.0, 0.0, 0.0, 0.0, 1.0, 28.0, 206.9, 126.0, 264.4, 126.0, 171.8, 124.0, 9.3, 11.0, 2.0, 9.68])),
     (0.0,
      DenseVector([51.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 259.9, 114.0, 176.2, 94.0, 77.2, 112.0, 15.3, 1.0, 1.0, 17.86])),
     (0.0,
      DenseVector([100.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 203.8, 122.0, 283.1, 76.0, 197.3, 83.0, 12.5, 3.0, 0.0, 26.47])),
     (0.0,
      DenseVector([96.0, 1.0, 0.0, 0.0, 0.0, 1.0, 45.0, 248.8, 124.0, 140.3, 77.0, 263.6, 102.0, 10.3, 2.0, 3.0, 12.68])),
     (0.0,
      DenseVector([112.0, 1.0, 0.0, 0.0, 0.0, 1.0, 16.0, 221.6, 110.0, 130.2, 123.0, 200.0, 108.0, 11.3, 3.0, 1.0, 9.26])),
     (0.0,
      DenseVector([129.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 192.9, 131.0, 185.5, 101.0, 205.2, 130.0, 10.9, 4.0, 1.0, 16.92])),
     (0.0,
      DenseVector([163.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 122.4, 129.0, 113.4, 108.0, 180.2, 97.0, 12.5, 7.0, 1.0, 20.97])),
     (0.0,
      DenseVector([67.0, 1.0, 0.0, 0.0, 0.0, 1.0, 40.0, 104.9, 65.0, 216.3, 93.0, 217.4, 128.0, 9.6, 9.0, 1.0, 16.07])),
     (0.0,
      DenseVector([140.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 173.2, 91.0, 196.8, 106.0, 209.3, 128.0, 11.2, 5.0, 3.0, 6.25])),
     (0.0,
      DenseVector([49.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 119.4, 69.0, 273.3, 92.0, 214.4, 153.0, 12.4, 7.0, 2.0, 14.1])),
     (1.0,
      DenseVector([46.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 250.3, 100.0, 260.6, 90.0, 195.0, 104.0, 13.3, 2.0, 2.0, 18.57])),
     (0.0,
      DenseVector([148.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 178.3, 98.0, 282.6, 110.0, 181.0, 98.0, 11.4, 4.0, 1.0, 8.2])),
     (0.0,
      DenseVector([112.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 243.4, 77.0, 182.1, 97.0, 259.2, 94.0, 12.8, 2.0, 1.0, 21.92])),
     (0.0,
      DenseVector([78.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 155.0, 106.0, 175.3, 101.0, 155.6, 125.0, 11.8, 5.0, 2.0, 23.33])),
     (0.0,
      DenseVector([61.0, 0.0, 1.0, 0.0, 0.0, 1.0, 31.0, 288.7, 101.0, 203.8, 102.0, 203.2, 49.0, 8.6, 3.0, 0.0, 17.78])),
     (0.0,
      DenseVector([58.0, 0.0, 0.0, 1.0, 0.0, 1.0, 29.0, 240.4, 80.0, 118.9, 91.0, 164.2, 108.0, 11.2, 3.0, 1.0, 20.59])),
     (0.0,
      DenseVector([155.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 190.3, 123.0, 301.3, 96.0, 214.6, 134.0, 8.0, 3.0, 1.0, 9.68])),
     (1.0,
      DenseVector([100.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 278.0, 76.0, 176.7, 74.0, 219.5, 126.0, 8.3, 4.0, 0.0, 12.82])),
     (0.0,
      DenseVector([113.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 155.0, 93.0, 330.6, 106.0, 189.4, 123.0, 13.5, 3.0, 1.0, 11.69])),
     (0.0,
      DenseVector([81.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 153.5, 99.0, 197.6, 102.0, 198.5, 86.0, 6.3, 2.0, 2.0, 21.92])),
     (0.0,
      DenseVector([135.0, 0.0, 0.0, 1.0, 0.0, 1.0, 27.0, 273.4, 141.0, 154.0, 99.0, 245.8, 112.0, 12.3, 6.0, 1.0, 20.0])),
     (0.0,
      DenseVector([99.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 155.3, 93.0, 265.7, 95.0, 145.7, 67.0, 12.4, 4.0, 0.0, 12.7])),
     (0.0,
      DenseVector([59.0, 0.0, 0.0, 1.0, 0.0, 1.0, 29.0, 133.1, 114.0, 221.2, 82.0, 131.6, 103.0, 6.8, 3.0, 1.0, 20.0])),
     (0.0,
      DenseVector([135.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 246.8, 129.0, 187.8, 121.0, 154.5, 109.0, 12.6, 5.0, 1.0, 11.11])),
     (0.0,
      DenseVector([85.0, 0.0, 1.0, 0.0, 1.0, 0.0, 0.0, 165.4, 107.0, 196.0, 126.0, 349.2, 110.0, 9.6, 7.0, 2.0, 8.97])),
     (0.0,
      DenseVector([70.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 59.5, 103.0, 257.2, 106.0, 208.3, 86.0, 11.1, 6.0, 0.0, 25.0])),
     (0.0,
      DenseVector([88.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 138.3, 116.0, 236.0, 138.0, 179.1, 110.0, 9.6, 4.0, 3.0, 25.0])),
     (0.0,
      DenseVector([55.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 286.7, 100.0, 134.4, 121.0, 192.9, 122.0, 6.9, 5.0, 2.0, 9.68])),
     (0.0,
      DenseVector([75.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 117.3, 114.0, 201.1, 61.0, 107.9, 82.0, 12.2, 3.0, 1.0, 14.81])),
     (0.0,
      DenseVector([79.0, 0.0, 0.0, 1.0, 0.0, 1.0, 21.0, 264.3, 79.0, 202.8, 118.0, 173.4, 92.0, 6.3, 3.0, 4.0, 12.33])),
     (0.0,
      DenseVector([85.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 127.9, 107.0, 271.2, 124.0, 202.2, 76.0, 12.5, 5.0, 0.0, 10.0])),
     (0.0,
      DenseVector([86.0, 0.0, 1.0, 0.0, 0.0, 1.0, 23.0, 225.5, 107.0, 246.3, 105.0, 245.7, 81.0, 9.8, 2.0, 0.0, 18.57])),
     (0.0,
      DenseVector([91.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 149.0, 115.0, 245.3, 105.0, 260.0, 94.0, 8.3, 3.0, 0.0, 13.33])),
     (0.0,
      DenseVector([149.0, 1.0, 0.0, 0.0, 0.0, 1.0, 20.0, 198.9, 77.0, 274.0, 88.0, 190.7, 76.0, 14.3, 9.0, 1.0, 7.84])),
     (1.0,
      DenseVector([97.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 256.4, 125.0, 273.9, 100.0, 222.7, 101.0, 11.1, 1.0, 1.0, 12.82])),
     (1.0,
      DenseVector([88.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 264.8, 124.0, 245.4, 112.0, 160.5, 115.0, 14.8, 2.0, 1.0, 16.92])),
     (0.0,
      DenseVector([60.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 98.2, 88.0, 180.5, 69.0, 223.6, 69.0, 9.3, 2.0, 2.0, 6.25])),
     (0.0,
      DenseVector([54.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 159.8, 99.0, 264.0, 64.0, 115.7, 70.0, 9.7, 7.0, 2.0, 13.56])),
     (0.0,
      DenseVector([11.0, 1.0, 0.0, 0.0, 0.0, 1.0, 28.0, 190.6, 86.0, 220.1, 122.0, 180.3, 80.0, 6.0, 3.0, 3.0, 9.26])),
     (0.0,
      DenseVector([109.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 184.0, 120.0, 120.4, 119.0, 153.7, 86.0, 11.0, 3.0, 0.0, 21.21])),
     (0.0,
      DenseVector([90.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 261.8, 128.0, 220.6, 104.0, 136.6, 91.0, 9.6, 5.0, 1.0, 13.89])),
     (0.0,
      DenseVector([115.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 147.9, 109.0, 228.4, 117.0, 299.7, 90.0, 9.6, 9.0, 3.0, 9.23])),
     (0.0,
      DenseVector([144.0, 1.0, 0.0, 0.0, 0.0, 1.0, 18.0, 106.4, 109.0, 108.1, 113.0, 208.4, 111.0, 10.1, 5.0, 1.0, 12.82])),
     (0.0,
      DenseVector([91.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 133.7, 75.0, 195.3, 87.0, 280.5, 89.0, 5.9, 2.0, 0.0, 21.21])),
     (0.0,
      DenseVector([105.0, 1.0, 0.0, 0.0, 0.0, 1.0, 23.0, 193.5, 85.0, 220.2, 90.0, 272.4, 111.0, 8.5, 5.0, 0.0, 9.68])),
     (1.0,
      DenseVector([71.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 178.2, 113.0, 167.8, 94.0, 182.1, 111.0, 13.6, 3.0, 3.0, 21.21])),
     (1.0,
      DenseVector([132.0, 0.0, 0.0, 1.0, 0.0, 1.0, 36.0, 226.2, 103.0, 181.6, 125.0, 258.8, 102.0, 10.5, 5.0, 3.0, 12.7])),
     (0.0,
      DenseVector([112.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 170.4, 103.0, 200.2, 71.0, 258.3, 100.0, 11.6, 4.0, 1.0, 24.29])),
     (0.0,
      DenseVector([86.0, 1.0, 0.0, 0.0, 0.0, 1.0, 32.0, 70.9, 163.0, 166.7, 121.0, 244.9, 105.0, 11.1, 5.0, 3.0, 6.25])),
     (0.0,
      DenseVector([41.0, 0.0, 0.0, 1.0, 0.0, 1.0, 34.0, 194.4, 63.0, 254.9, 110.0, 160.2, 115.0, 17.2, 9.0, 2.0, 10.0])),
     (0.0,
      DenseVector([44.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 240.3, 146.0, 164.6, 83.0, 240.7, 106.0, 10.6, 2.0, 1.0, 8.2])),
     (0.0,
      DenseVector([78.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 75.0, 116.0, 248.7, 87.0, 176.0, 83.0, 9.5, 6.0, 3.0, 21.21])),
     (0.0,
      DenseVector([149.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 69.1, 117.0, 136.3, 100.0, 181.7, 53.0, 6.3, 3.0, 1.0, 8.62])),
     (1.0,
      DenseVector([72.0, 0.0, 0.0, 1.0, 0.0, 1.0, 33.0, 96.6, 59.0, 315.4, 98.0, 163.3, 117.0, 6.2, 4.0, 4.0, 9.43])),
     (0.0,
      DenseVector([139.0, 1.0, 0.0, 0.0, 0.0, 1.0, 20.0, 214.6, 101.0, 235.1, 132.0, 162.8, 132.0, 14.8, 12.0, 0.0, 21.92])),
     (0.0,
      DenseVector([74.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 148.5, 111.0, 146.5, 42.0, 289.2, 83.0, 9.9, 6.0, 3.0, 20.0])),
     (0.0,
      DenseVector([50.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 258.1, 106.0, 161.4, 106.0, 225.1, 110.0, 11.7, 5.0, 1.0, 13.89])),
     (0.0,
      DenseVector([141.0, 0.0, 0.0, 1.0, 0.0, 1.0, 23.0, 149.7, 112.0, 162.5, 118.0, 220.3, 115.0, 7.6, 2.0, 3.0, 14.81])),
     (0.0,
      DenseVector([140.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 149.8, 134.0, 164.4, 98.0, 294.7, 124.0, 8.1, 2.0, 1.0, 6.25])),
     (0.0,
      DenseVector([99.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 190.4, 102.0, 158.1, 107.0, 271.5, 92.0, 11.2, 4.0, 2.0, 12.33])),
     (0.0,
      DenseVector([166.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 181.4, 108.0, 253.8, 54.0, 112.3, 94.0, 11.6, 6.0, 1.0, 5.66])),
     (0.0,
      DenseVector([124.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 151.1, 123.0, 187.4, 104.0, 255.4, 93.0, 5.3, 3.0, 1.0, 21.21])),
     (0.0,
      DenseVector([74.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 155.7, 116.0, 173.7, 63.0, 257.4, 97.0, 8.1, 4.0, 0.0, 24.29])),
     (0.0,
      DenseVector([117.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 149.9, 95.0, 256.1, 110.0, 212.7, 92.0, 13.3, 13.0, 2.0, 14.81])),
     (0.0,
      DenseVector([85.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 222.3, 132.0, 231.5, 101.0, 223.5, 75.0, 11.0, 2.0, 3.0, 14.81])),
     (0.0,
      DenseVector([36.0, 1.0, 0.0, 0.0, 0.0, 1.0, 16.0, 149.4, 111.0, 131.8, 113.0, 132.7, 87.0, 6.7, 2.0, 0.0, 13.89])),
     (0.0,
      DenseVector([102.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 233.8, 103.0, 221.6, 131.0, 146.9, 106.0, 12.8, 3.0, 0.0, 16.92])),
     (0.0,
      DenseVector([76.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 204.2, 100.0, 292.6, 139.0, 244.3, 105.0, 10.5, 2.0, 0.0, 12.68])),
     (0.0,
      DenseVector([165.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 242.9, 126.0, 209.8, 65.0, 228.4, 126.0, 0.0, 0.0, 1.0, 10.96])),
     (0.0,
      DenseVector([130.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 150.4, 119.0, 230.5, 99.0, 186.3, 76.0, 12.3, 4.0, 1.0, 6.82])),
     (0.0,
      DenseVector([78.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 208.9, 119.0, 252.4, 132.0, 280.2, 120.0, 12.8, 7.0, 0.0, 12.68])),
     (1.0,
      DenseVector([55.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 191.9, 91.0, 256.1, 110.0, 203.7, 101.0, 14.3, 6.0, 1.0, 10.0])),
     (1.0,
      DenseVector([92.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 130.7, 113.0, 260.6, 122.0, 244.2, 98.0, 9.4, 2.0, 2.0, 20.97])),
     (0.0,
      DenseVector([129.0, 1.0, 0.0, 0.0, 0.0, 1.0, 33.0, 119.6, 104.0, 278.7, 88.0, 263.4, 175.0, 5.9, 2.0, 2.0, 9.23])),
     (0.0,
      DenseVector([18.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 273.6, 93.0, 114.6, 116.0, 250.6, 120.0, 8.2, 4.0, 1.0, 24.29])),
     (0.0,
      DenseVector([161.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 156.1, 114.0, 180.3, 63.0, 179.6, 115.0, 11.1, 9.0, 2.0, 12.7])),
     (0.0,
      DenseVector([93.0, 1.0, 0.0, 0.0, 0.0, 1.0, 36.0, 178.7, 134.0, 178.6, 102.0, 126.8, 82.0, 8.0, 4.0, 2.0, 26.47])),
     (0.0,
      DenseVector([144.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 177.5, 93.0, 287.4, 75.0, 180.5, 118.0, 11.9, 3.0, 2.0, 10.0])),
     (1.0,
      DenseVector([75.0, 0.0, 1.0, 0.0, 1.0, 0.0, 0.0, 211.3, 61.0, 105.6, 119.0, 175.9, 63.0, 9.7, 4.0, 4.0, 20.97])),
     (0.0,
      DenseVector([95.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 175.2, 91.0, 244.4, 109.0, 75.8, 95.0, 7.5, 2.0, 1.0, 9.43])),
     (0.0,
      DenseVector([126.0, 1.0, 0.0, 0.0, 0.0, 1.0, 23.0, 114.3, 102.0, 190.3, 103.0, 240.4, 111.0, 12.6, 7.0, 3.0, 13.33])),
     (0.0,
      DenseVector([124.0, 1.0, 0.0, 0.0, 0.0, 1.0, 28.0, 251.4, 104.0, 225.1, 89.0, 251.9, 121.0, 7.5, 5.0, 1.0, 12.7])),
     (1.0,
      DenseVector([93.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 216.9, 61.0, 207.4, 120.0, 221.7, 110.0, 17.5, 5.0, 1.0, 21.92])),
     (0.0,
      DenseVector([109.0, 1.0, 0.0, 0.0, 1.0, 1.0, 26.0, 217.2, 138.0, 145.5, 111.0, 280.7, 76.0, 9.3, 3.0, 0.0, 21.92])),
     (0.0,
      DenseVector([80.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 206.3, 97.0, 154.9, 98.0, 263.6, 82.0, 12.4, 12.0, 0.0, 9.68])),
     (0.0,
      DenseVector([41.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 159.3, 66.0, 125.9, 75.0, 261.9, 76.0, 11.1, 5.0, 1.0, 5.77])),
     (0.0,
      DenseVector([136.0, 1.0, 0.0, 0.0, 0.0, 1.0, 31.0, 143.1, 88.0, 236.6, 65.0, 227.8, 120.0, 11.4, 5.0, 2.0, 12.82])),
     (1.0,
      DenseVector([92.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 154.0, 122.0, 329.8, 88.0, 288.0, 117.0, 5.6, 2.0, 3.0, 11.11])),
     (0.0,
      DenseVector([143.0, 0.0, 1.0, 0.0, 0.0, 1.0, 24.0, 186.6, 69.0, 222.0, 116.0, 234.9, 138.0, 11.6, 5.0, 1.0, 18.57])),
     (1.0,
      DenseVector([118.0, 1.0, 0.0, 0.0, 0.0, 1.0, 26.0, 170.8, 114.0, 199.5, 125.0, 169.7, 98.0, 9.6, 5.0, 5.0, 21.54])),
     (0.0,
      DenseVector([193.0, 0.0, 1.0, 0.0, 0.0, 1.0, 17.0, 124.0, 102.0, 202.9, 81.0, 205.1, 129.0, 12.3, 3.0, 1.0, 10.96])),
     (0.0,
      DenseVector([73.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 198.3, 94.0, 279.3, 101.0, 146.2, 87.0, 14.8, 8.0, 3.0, 8.2])),
     (0.0,
      DenseVector([62.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 172.8, 101.0, 204.8, 97.0, 240.8, 90.0, 9.1, 8.0, 2.0, 6.49])),
     (0.0,
      DenseVector([30.0, 1.0, 0.0, 0.0, 0.0, 1.0, 30.0, 217.4, 74.0, 213.8, 86.0, 227.2, 104.0, 6.6, 3.0, 0.0, 14.75])),
     (1.0,
      DenseVector([60.0, 0.0, 1.0, 0.0, 1.0, 1.0, 29.0, 265.9, 113.0, 215.8, 94.0, 108.1, 82.0, 14.0, 12.0, 0.0, 10.0])),
     (0.0,
      DenseVector([148.0, 0.0, 0.0, 1.0, 0.0, 1.0, 14.0, 93.6, 137.0, 193.8, 72.0, 144.9, 84.0, 17.5, 5.0, 1.0, 12.33])),
     (0.0,
      DenseVector([96.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 98.2, 100.0, 307.2, 88.0, 182.5, 120.0, 7.6, 1.0, 2.0, 21.54])),
     (0.0,
      DenseVector([52.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 214.7, 68.0, 158.6, 138.0, 123.4, 114.0, 9.4, 4.0, 2.0, 14.75])),
     (0.0,
      DenseVector([87.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 168.2, 92.0, 224.7, 100.0, 169.5, 99.0, 12.9, 3.0, 1.0, 9.68])),
     (0.0,
      DenseVector([41.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 202.9, 97.0, 153.8, 104.0, 113.5, 92.0, 9.0, 3.0, 3.0, 8.97])),
     (0.0,
      DenseVector([112.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 261.4, 108.0, 154.5, 102.0, 130.9, 90.0, 11.6, 2.0, 1.0, 9.43])),
     (1.0,
      DenseVector([88.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 73.3, 86.0, 161.4, 82.0, 239.6, 76.0, 8.2, 3.0, 4.0, 23.33])),
     (0.0,
      DenseVector([122.0, 0.0, 1.0, 0.0, 0.0, 1.0, 27.0, 253.7, 84.0, 229.2, 109.0, 190.5, 123.0, 9.2, 5.0, 7.0, 13.56])),
     (0.0,
      DenseVector([61.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 45.0, 108.0, 151.3, 74.0, 152.9, 94.0, 9.8, 6.0, 2.0, 11.11])),
     (0.0,
      DenseVector([87.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 231.3, 105.0, 171.7, 108.0, 67.7, 136.0, 13.0, 6.0, 1.0, 8.62])),
     (0.0,
      DenseVector([176.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 47.4, 125.0, 167.8, 90.0, 163.1, 107.0, 10.5, 8.0, 2.0, 14.75])),
     (0.0,
      DenseVector([30.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 227.4, 88.0, 182.5, 100.0, 191.7, 134.0, 12.5, 3.0, 0.0, 21.92])),
     (0.0,
      DenseVector([95.0, 1.0, 0.0, 0.0, 0.0, 1.0, 22.0, 40.9, 126.0, 133.4, 90.0, 264.2, 91.0, 11.9, 7.0, 0.0, 26.47])),
     (0.0,
      DenseVector([46.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 124.8, 133.0, 157.3, 143.0, 199.3, 72.0, 8.6, 4.0, 2.0, 12.33])),
     (0.0,
      DenseVector([100.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 68.5, 110.0, 337.1, 115.0, 205.2, 99.0, 12.1, 9.0, 0.0, 9.26])),
     (0.0,
      DenseVector([47.0, 1.0, 0.0, 0.0, 0.0, 1.0, 37.0, 163.5, 77.0, 203.1, 102.0, 232.0, 87.0, 7.8, 4.0, 2.0, 18.07])),
     (0.0,
      DenseVector([77.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 163.0, 112.0, 219.1, 89.0, 233.4, 66.0, 6.7, 3.0, 2.0, 10.0])),
     (0.0,
      DenseVector([98.0, 1.0, 0.0, 0.0, 0.0, 1.0, 38.0, 213.7, 61.0, 253.0, 104.0, 207.7, 73.0, 10.7, 5.0, 2.0, 14.1])),
     (0.0,
      DenseVector([125.0, 1.0, 0.0, 0.0, 0.0, 1.0, 36.0, 201.3, 117.0, 42.2, 78.0, 125.7, 104.0, 5.4, 3.0, 1.0, 14.75])),
     (0.0,
      DenseVector([67.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 310.4, 97.0, 66.5, 123.0, 246.5, 99.0, 9.2, 10.0, 4.0, 7.84])),
     (0.0,
      DenseVector([194.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 48.4, 101.0, 281.1, 138.0, 218.5, 87.0, 18.2, 1.0, 1.0, 8.2])),
     (0.0,
      DenseVector([128.0, 1.0, 0.0, 0.0, 0.0, 1.0, 40.0, 171.2, 88.0, 145.7, 109.0, 196.8, 93.0, 14.0, 6.0, 1.0, 25.0])),
     (0.0,
      DenseVector([190.0, 1.0, 0.0, 0.0, 0.0, 1.0, 22.0, 166.5, 93.0, 183.0, 92.0, 121.0, 102.0, 8.5, 3.0, 0.0, 13.89])),
     (0.0,
      DenseVector([165.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 216.6, 126.0, 190.8, 104.0, 224.7, 123.0, 12.4, 8.0, 0.0, 14.1])),
     (0.0,
      DenseVector([59.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 107.8, 113.0, 216.6, 125.0, 217.5, 92.0, 9.9, 3.0, 2.0, 18.07])),
     (0.0,
      DenseVector([47.0, 0.0, 1.0, 0.0, 0.0, 1.0, 28.0, 141.3, 94.0, 168.0, 108.0, 113.5, 84.0, 7.8, 2.0, 1.0, 10.0])),
     (0.0,
      DenseVector([150.0, 1.0, 0.0, 0.0, 0.0, 1.0, 29.0, 209.9, 77.0, 158.0, 52.0, 141.9, 113.0, 6.6, 1.0, 0.0, 9.23])),
     (1.0,
      DenseVector([152.0, 1.0, 0.0, 0.0, 1.0, 1.0, 20.0, 237.5, 120.0, 253.4, 94.0, 265.2, 80.0, 14.2, 3.0, 9.0, 17.86])),
     (0.0,
      DenseVector([26.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 234.5, 109.0, 216.5, 129.0, 191.6, 94.0, 3.5, 6.0, 3.0, 16.18])),
     (0.0,
      DenseVector([79.0, 0.0, 0.0, 1.0, 0.0, 1.0, 31.0, 103.1, 90.0, 243.0, 135.0, 76.4, 92.0, 12.2, 8.0, 3.0, 24.29])),
     (0.0,
      DenseVector([95.0, 0.0, 0.0, 1.0, 0.0, 1.0, 27.0, 129.5, 106.0, 248.9, 90.0, 268.0, 115.0, 11.9, 3.0, 1.0, 9.23])),
     (1.0,
      DenseVector([69.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 279.8, 90.0, 248.7, 91.0, 171.0, 118.0, 8.4, 10.0, 2.0, 8.97])),
     (1.0,
      DenseVector([95.0, 0.0, 0.0, 1.0, 1.0, 1.0, 41.0, 136.8, 91.0, 200.8, 61.0, 133.7, 67.0, 10.3, 9.0, 5.0, 10.96])),
     (0.0,
      DenseVector([31.0, 1.0, 0.0, 0.0, 0.0, 1.0, 31.0, 100.1, 54.0, 246.3, 97.0, 255.0, 131.0, 5.9, 3.0, 0.0, 16.22])),
     (0.0,
      DenseVector([121.0, 0.0, 1.0, 0.0, 0.0, 1.0, 31.0, 237.1, 63.0, 205.6, 117.0, 196.7, 85.0, 10.1, 5.0, 4.0, 14.75])),
     (1.0,
      DenseVector([111.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 172.8, 58.0, 183.1, 108.0, 158.8, 104.0, 7.9, 3.0, 4.0, 5.77])),
     (0.0,
      DenseVector([157.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 224.5, 111.0, 200.7, 99.0, 116.6, 118.0, 11.5, 2.0, 2.0, 18.07])),
     (1.0,
      DenseVector([44.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 288.1, 112.0, 258.0, 92.0, 192.4, 90.0, 10.2, 4.0, 3.0, 14.81])),
     (0.0,
      DenseVector([61.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 78.2, 103.0, 195.9, 149.0, 108.0, 100.0, 10.1, 6.0, 2.0, 13.89])),
     (0.0,
      DenseVector([65.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 148.7, 80.0, 259.0, 94.0, 149.5, 107.0, 12.7, 6.0, 2.0, 9.68])),
     (0.0,
      DenseVector([74.0, 1.0, 0.0, 0.0, 0.0, 1.0, 25.0, 194.6, 84.0, 119.9, 103.0, 175.5, 75.0, 13.1, 2.0, 2.0, 8.2])),
     (0.0,
      DenseVector([123.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 159.5, 77.0, 303.8, 92.0, 226.9, 120.0, 12.0, 4.0, 0.0, 26.47])),
     (0.0,
      DenseVector([58.0, 0.0, 1.0, 0.0, 0.0, 1.0, 20.0, 194.5, 110.0, 213.7, 89.0, 236.6, 92.0, 9.5, 2.0, 1.0, 25.0])),
     (1.0,
      DenseVector([74.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 174.1, 96.0, 251.1, 94.0, 257.6, 123.0, 8.3, 5.0, 2.0, 20.59])),
     (0.0,
      DenseVector([125.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 131.8, 97.0, 136.7, 100.0, 308.2, 119.0, 7.7, 6.0, 2.0, 13.64])),
     (0.0,
      DenseVector([80.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 160.6, 103.0, 237.0, 109.0, 245.1, 88.0, 10.7, 1.0, 1.0, 10.96])),
     (0.0,
      DenseVector([53.0, 0.0, 1.0, 0.0, 0.0, 1.0, 18.0, 146.8, 107.0, 310.0, 84.0, 178.7, 130.0, 7.2, 7.0, 0.0, 9.23])),
     (0.0,
      DenseVector([99.0, 0.0, 1.0, 0.0, 0.0, 1.0, 28.0, 200.7, 88.0, 264.2, 116.0, 172.7, 102.0, 9.1, 5.0, 1.0, 11.69])),
     (0.0,
      DenseVector([99.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 145.6, 106.0, 98.3, 106.0, 230.8, 83.0, 10.9, 5.0, 1.0, 12.33])),
     (0.0,
      DenseVector([66.0, 1.0, 0.0, 0.0, 0.0, 1.0, 29.0, 229.4, 104.0, 257.4, 84.0, 231.5, 119.0, 8.0, 1.0, 2.0, 16.22])),
     (0.0,
      DenseVector([97.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 211.0, 76.0, 189.0, 100.0, 123.0, 102.0, 4.7, 4.0, 3.0, 20.97])),
     (0.0,
      DenseVector([75.0, 0.0, 0.0, 1.0, 0.0, 1.0, 37.0, 121.5, 97.0, 271.4, 110.0, 248.7, 97.0, 11.3, 5.0, 2.0, 6.25])),
     (0.0,
      DenseVector([85.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 216.0, 73.0, 188.2, 117.0, 147.1, 98.0, 3.6, 7.0, 2.0, 24.29])),
     (0.0,
      DenseVector([108.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 293.0, 88.0, 160.6, 101.0, 143.9, 87.0, 10.0, 6.0, 2.0, 12.68])),
     (1.0,
      DenseVector([133.0, 0.0, 1.0, 0.0, 1.0, 1.0, 32.0, 221.1, 137.0, 264.9, 99.0, 168.9, 108.0, 15.4, 4.0, 2.0, 16.18])),
     (0.0,
      DenseVector([51.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 181.5, 108.0, 196.9, 87.0, 187.2, 119.0, 10.3, 2.0, 1.0, 14.75])),
     (0.0,
      DenseVector([186.0, 1.0, 0.0, 0.0, 0.0, 1.0, 26.0, 74.3, 107.0, 177.3, 116.0, 296.3, 90.0, 14.5, 3.0, 2.0, 17.86])),
     (0.0,
      DenseVector([44.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 62.3, 92.0, 275.0, 82.0, 138.7, 108.0, 10.8, 3.0, 1.0, 8.97])),
     (0.0,
      DenseVector([64.0, 0.0, 1.0, 0.0, 0.0, 1.0, 31.0, 228.6, 88.0, 248.5, 109.0, 167.1, 124.0, 9.0, 1.0, 3.0, 12.7])),
     (1.0,
      DenseVector([44.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 228.1, 121.0, 276.5, 79.0, 279.8, 77.0, 9.9, 5.0, 2.0, 9.43])),
     (0.0,
      DenseVector([114.0, 1.0, 0.0, 0.0, 0.0, 1.0, 36.0, 309.9, 90.0, 200.3, 89.0, 183.5, 105.0, 14.2, 2.0, 1.0, 13.33])),
     (0.0,
      DenseVector([92.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 201.9, 74.0, 226.8, 119.0, 217.5, 80.0, 13.7, 6.0, 3.0, 12.7])),
     (0.0,
      DenseVector([110.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 149.8, 112.0, 180.0, 93.0, 140.0, 119.0, 11.7, 4.0, 2.0, 14.1])),
     (0.0,
      DenseVector([90.0, 0.0, 1.0, 0.0, 0.0, 1.0, 30.0, 183.8, 76.0, 229.7, 95.0, 144.1, 124.0, 7.7, 3.0, 1.0, 13.64])),
     (0.0,
      DenseVector([72.0, 0.0, 1.0, 0.0, 0.0, 1.0, 21.0, 186.7, 108.0, 335.0, 86.0, 187.2, 119.0, 16.5, 4.0, 1.0, 16.22])),
     (1.0,
      DenseVector([113.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 209.4, 151.0, 347.3, 113.0, 246.0, 116.0, 7.4, 2.0, 1.0, 12.68])),
     (0.0,
      DenseVector([171.0, 1.0, 0.0, 0.0, 0.0, 1.0, 25.0, 223.2, 77.0, 183.2, 118.0, 150.8, 90.0, 10.2, 3.0, 3.0, 17.78])),
     (0.0,
      DenseVector([104.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 164.2, 109.0, 155.4, 90.0, 168.9, 117.0, 10.7, 8.0, 1.0, 9.68])),
     (0.0,
      DenseVector([165.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 150.5, 75.0, 193.1, 93.0, 311.6, 93.0, 10.3, 2.0, 1.0, 20.97])),
     (1.0,
      DenseVector([104.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 234.2, 128.0, 293.1, 92.0, 183.9, 79.0, 9.8, 6.0, 0.0, 13.33])),
     (0.0,
      DenseVector([110.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 55.3, 102.0, 164.7, 124.0, 200.7, 108.0, 10.2, 5.0, 1.0, 20.0])),
     (0.0,
      DenseVector([90.0, 0.0, 1.0, 0.0, 1.0, 0.0, 0.0, 221.8, 97.0, 203.8, 134.0, 215.8, 154.0, 8.4, 4.0, 1.0, 25.0])),
     (0.0,
      DenseVector([114.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 169.6, 85.0, 58.9, 86.0, 179.3, 124.0, 7.4, 8.0, 1.0, 16.07])),
     (1.0,
      DenseVector([101.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 89.7, 118.0, 260.1, 79.0, 170.1, 93.0, 13.5, 11.0, 5.0, 14.75])),
     (0.0,
      DenseVector([117.0, 0.0, 1.0, 0.0, 0.0, 1.0, 14.0, 80.2, 81.0, 219.0, 103.0, 122.6, 102.0, 8.6, 2.0, 1.0, 8.97])),
     (0.0,
      DenseVector([109.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 218.9, 105.0, 299.9, 87.0, 158.6, 110.0, 11.3, 4.0, 2.0, 10.0])),
     (0.0,
      DenseVector([82.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 125.7, 96.0, 207.6, 137.0, 183.1, 103.0, 12.9, 2.0, 1.0, 17.78])),
     (0.0,
      DenseVector([92.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 176.3, 85.0, 93.4, 125.0, 207.2, 107.0, 9.6, 1.0, 2.0, 14.75])),
     (0.0,
      DenseVector([82.0, 0.0, 0.0, 1.0, 0.0, 1.0, 29.0, 207.2, 111.0, 254.1, 137.0, 169.3, 92.0, 9.5, 5.0, 2.0, 20.97])),
     (0.0,
      DenseVector([90.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 205.7, 138.0, 161.9, 83.0, 269.7, 104.0, 12.5, 6.0, 2.0, 9.43])),
     (0.0,
      DenseVector([87.0, 0.0, 1.0, 0.0, 0.0, 1.0, 28.0, 151.4, 95.0, 152.4, 97.0, 250.1, 109.0, 0.0, 0.0, 1.0, 5.66])),
     (0.0,
      DenseVector([124.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 157.5, 70.0, 130.7, 79.0, 193.4, 98.0, 9.6, 4.0, 3.0, 17.86])),
     (0.0,
      DenseVector([39.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 160.4, 68.0, 102.6, 103.0, 235.3, 106.0, 9.1, 5.0, 2.0, 18.07])),
     (0.0,
      DenseVector([84.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 159.0, 80.0, 167.9, 128.0, 167.6, 101.0, 12.3, 5.0, 1.0, 6.25])),
     (0.0,
      DenseVector([75.0, 0.0, 0.0, 1.0, 0.0, 1.0, 46.0, 214.1, 62.0, 200.9, 111.0, 246.8, 126.0, 9.2, 6.0, 0.0, 12.82])),
     (0.0,
      DenseVector([102.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 102.6, 89.0, 246.0, 77.0, 170.5, 140.0, 9.1, 4.0, 2.0, 21.92])),
     (1.0,
      DenseVector([62.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 159.7, 86.0, 197.5, 76.0, 121.6, 105.0, 13.9, 6.0, 0.0, 16.92])),
     (0.0,
      DenseVector([143.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 202.8, 109.0, 165.8, 104.0, 143.9, 71.0, 4.6, 4.0, 1.0, 9.43])),
     (0.0,
      DenseVector([53.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 57.5, 95.0, 265.5, 131.0, 244.3, 128.0, 11.6, 6.0, 3.0, 21.92])),
     (0.0,
      DenseVector([30.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 169.9, 144.0, 225.2, 118.0, 169.7, 93.0, 11.4, 7.0, 1.0, 9.68])),
     (1.0,
      DenseVector([112.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 335.5, 77.0, 212.5, 109.0, 265.0, 132.0, 12.7, 8.0, 2.0, 11.11])),
     (0.0,
      DenseVector([129.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 139.5, 119.0, 289.3, 105.0, 129.4, 97.0, 13.1, 8.0, 0.0, 9.23])),
     (0.0,
      DenseVector([63.0, 1.0, 0.0, 0.0, 0.0, 1.0, 29.0, 142.3, 107.0, 118.7, 56.0, 240.1, 91.0, 6.6, 8.0, 1.0, 16.18])),
     (0.0,
      DenseVector([28.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 187.8, 94.0, 248.6, 86.0, 208.8, 124.0, 10.6, 5.0, 0.0, 11.69])),
     (0.0,
      DenseVector([111.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 146.2, 55.0, 261.5, 83.0, 163.2, 116.0, 8.7, 3.0, 3.0, 11.69])),
     (0.0,
      DenseVector([91.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 231.8, 120.0, 150.6, 106.0, 269.2, 129.0, 11.6, 7.0, 3.0, 17.78])),
     (0.0,
      DenseVector([90.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 193.7, 83.0, 154.2, 79.0, 299.0, 60.0, 12.7, 3.0, 1.0, 13.56])),
     (0.0,
      DenseVector([151.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 156.4, 108.0, 233.4, 118.0, 195.7, 141.0, 7.7, 6.0, 4.0, 14.1])),
     (1.0,
      DenseVector([105.0, 1.0, 0.0, 0.0, 1.0, 1.0, 29.0, 220.7, 82.0, 217.7, 110.0, 190.5, 100.0, 13.2, 6.0, 1.0, 21.21])),
     (0.0,
      DenseVector([41.0, 0.0, 1.0, 0.0, 0.0, 1.0, 37.0, 239.8, 110.0, 221.9, 115.0, 189.1, 100.0, 7.3, 1.0, 2.0, 9.26])),
     (0.0,
      DenseVector([48.0, 0.0, 0.0, 1.0, 0.0, 1.0, 43.0, 172.0, 111.0, 200.2, 64.0, 233.1, 96.0, 8.0, 5.0, 1.0, 13.89])),
     (0.0,
      DenseVector([166.0, 0.0, 1.0, 0.0, 1.0, 1.0, 35.0, 128.2, 138.0, 274.5, 113.0, 298.9, 130.0, 8.8, 7.0, 2.0, 21.21])),
     (0.0,
      DenseVector([79.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 130.2, 119.0, 290.9, 121.0, 194.8, 140.0, 14.0, 6.0, 3.0, 12.7])),
     (0.0,
      DenseVector([153.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 195.4, 107.0, 154.6, 96.0, 142.8, 97.0, 11.6, 6.0, 1.0, 6.49])),
     (1.0,
      DenseVector([110.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 293.3, 79.0, 188.5, 90.0, 266.9, 91.0, 14.5, 4.0, 0.0, 18.57])),
     (0.0,
      DenseVector([163.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 191.3, 89.0, 193.9, 87.0, 268.4, 121.0, 12.8, 4.0, 1.0, 18.57])),
     (0.0,
      DenseVector([126.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 122.4, 88.0, 143.8, 111.0, 157.0, 106.0, 11.5, 3.0, 1.0, 9.26])),
     (0.0,
      DenseVector([105.0, 0.0, 1.0, 0.0, 0.0, 1.0, 33.0, 209.6, 68.0, 146.9, 140.0, 121.0, 131.0, 10.6, 3.0, 2.0, 20.97])),
     (0.0,
      DenseVector([172.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 215.7, 140.0, 146.3, 84.0, 264.6, 83.0, 7.1, 1.0, 3.0, 7.84])),
     (0.0,
      DenseVector([126.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 161.4, 110.0, 220.6, 125.0, 249.2, 78.0, 5.1, 2.0, 0.0, 9.26])),
     (0.0,
      DenseVector([97.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 144.2, 91.0, 226.7, 137.0, 144.6, 72.0, 13.8, 4.0, 3.0, 25.0])),
     (1.0,
      DenseVector([95.0, 0.0, 1.0, 0.0, 1.0, 1.0, 37.0, 220.2, 109.0, 185.3, 99.0, 205.1, 82.0, 4.1, 2.0, 0.0, 26.47])),
     (0.0,
      DenseVector([87.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 256.2, 105.0, 160.7, 102.0, 249.4, 80.0, 7.4, 2.0, 4.0, 14.75])),
     (0.0,
      DenseVector([97.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 112.7, 119.0, 217.7, 109.0, 152.1, 76.0, 6.5, 5.0, 1.0, 10.96])),
     (1.0,
      DenseVector([76.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 299.5, 125.0, 226.7, 92.0, 210.7, 134.0, 13.7, 4.0, 0.0, 14.81])),
     (0.0,
      DenseVector([140.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 194.8, 107.0, 170.9, 99.0, 225.1, 93.0, 13.9, 4.0, 0.0, 25.0])),
     (0.0,
      DenseVector([169.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 100.8, 112.0, 230.0, 69.0, 193.6, 95.0, 9.5, 2.0, 0.0, 20.59])),
     (0.0,
      DenseVector([68.0, 0.0, 1.0, 0.0, 0.0, 1.0, 22.0, 82.5, 97.0, 289.9, 94.0, 180.0, 114.0, 4.8, 4.0, 3.0, 9.68])),
     (0.0,
      DenseVector([122.0, 1.0, 0.0, 0.0, 0.0, 1.0, 34.0, 146.4, 104.0, 89.7, 103.0, 220.0, 91.0, 15.6, 4.0, 2.0, 26.47])),
     (0.0,
      DenseVector([36.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 177.9, 129.0, 224.6, 87.0, 306.3, 102.0, 10.8, 6.0, 2.0, 11.11])),
     (0.0,
      DenseVector([120.0, 0.0, 0.0, 1.0, 0.0, 1.0, 27.0, 153.5, 84.0, 194.0, 73.0, 256.5, 94.0, 10.2, 7.0, 5.0, 13.64])),
     (0.0,
      DenseVector([121.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 150.7, 105.0, 197.3, 133.0, 169.0, 116.0, 9.2, 15.0, 1.0, 18.57])),
     (0.0,
      DenseVector([64.0, 0.0, 1.0, 0.0, 0.0, 1.0, 19.0, 180.1, 106.0, 127.5, 92.0, 237.4, 118.0, 7.5, 3.0, 0.0, 16.18])),
     (0.0,
      DenseVector([13.0, 1.0, 0.0, 0.0, 0.0, 1.0, 31.0, 265.3, 94.0, 147.6, 95.0, 259.3, 117.0, 12.9, 1.0, 1.0, 20.59])),
     (0.0,
      DenseVector([106.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 128.6, 83.0, 134.0, 114.0, 210.6, 113.0, 11.4, 2.0, 0.0, 14.75])),
     (0.0,
      DenseVector([88.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 161.5, 92.0, 173.5, 108.0, 206.2, 95.0, 7.9, 4.0, 2.0, 9.68])),
     (0.0,
      DenseVector([74.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 165.3, 120.0, 198.5, 106.0, 208.5, 102.0, 9.8, 3.0, 1.0, 6.49])),
     (0.0,
      DenseVector([83.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 195.0, 92.0, 210.5, 83.0, 180.6, 92.0, 11.0, 13.0, 0.0, 8.62])),
     (0.0,
      DenseVector([49.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 213.8, 79.0, 265.1, 93.0, 239.8, 128.0, 15.6, 7.0, 0.0, 14.75])),
     (0.0,
      DenseVector([111.0, 0.0, 0.0, 1.0, 0.0, 1.0, 24.0, 205.5, 114.0, 219.3, 99.0, 215.9, 95.0, 14.0, 4.0, 1.0, 13.64])),
     (0.0,
      DenseVector([50.0, 1.0, 0.0, 0.0, 0.0, 1.0, 22.0, 252.9, 112.0, 177.9, 99.0, 158.4, 146.0, 8.5, 4.0, 3.0, 20.59])),
     (0.0,
      DenseVector([153.0, 0.0, 1.0, 0.0, 0.0, 1.0, 28.0, 235.6, 74.0, 227.9, 37.0, 170.3, 103.0, 15.4, 9.0, 0.0, 9.43])),
     (0.0,
      DenseVector([88.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 192.0, 91.0, 127.6, 127.0, 155.6, 125.0, 7.5, 5.0, 1.0, 20.97])),
     (0.0,
      DenseVector([131.0, 1.0, 0.0, 0.0, 0.0, 1.0, 39.0, 69.1, 122.0, 101.3, 136.0, 104.8, 94.0, 9.1, 4.0, 0.0, 8.97])),
     (1.0,
      DenseVector([79.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 261.7, 97.0, 210.6, 48.0, 256.7, 83.0, 6.0, 3.0, 3.0, 11.11])),
     (0.0,
      DenseVector([140.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 235.5, 81.0, 257.2, 130.0, 103.1, 111.0, 11.5, 4.0, 2.0, 18.07])),
     (0.0,
      DenseVector([105.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 213.4, 100.0, 204.9, 52.0, 179.7, 93.0, 9.5, 6.0, 1.0, 16.22])),
     (0.0,
      DenseVector([54.0, 1.0, 0.0, 0.0, 0.0, 1.0, 39.0, 206.9, 143.0, 127.8, 72.0, 199.2, 120.0, 9.2, 1.0, 3.0, 20.0])),
     (0.0,
      DenseVector([87.0, 1.0, 0.0, 0.0, 0.0, 1.0, 22.0, 263.8, 65.0, 103.4, 115.0, 208.1, 109.0, 8.5, 3.0, 3.0, 11.69])),
     (0.0,
      DenseVector([96.0, 0.0, 0.0, 1.0, 0.0, 1.0, 31.0, 183.4, 126.0, 195.5, 106.0, 180.1, 93.0, 10.5, 5.0, 1.0, 26.47])),
     (1.0,
      DenseVector([79.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 157.6, 85.0, 194.1, 92.0, 231.5, 86.0, 9.4, 10.0, 5.0, 26.47])),
     (0.0,
      DenseVector([55.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 175.6, 147.0, 161.8, 118.0, 289.5, 55.0, 9.3, 4.0, 0.0, 17.86])),
     (0.0,
      DenseVector([130.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 242.5, 101.0, 102.8, 114.0, 142.4, 89.0, 9.3, 2.0, 2.0, 5.77])),
     (0.0,
      DenseVector([34.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 151.0, 102.0, 131.4, 101.0, 186.6, 86.0, 9.9, 7.0, 0.0, 6.49])),
     (0.0,
      DenseVector([139.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 138.1, 103.0, 164.5, 100.0, 134.9, 63.0, 8.3, 2.0, 1.0, 13.64])),
     (1.0,
      DenseVector([109.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 264.7, 69.0, 305.0, 120.0, 197.4, 86.0, 9.5, 9.0, 1.0, 20.59])),
     (0.0,
      DenseVector([65.0, 0.0, 1.0, 0.0, 0.0, 1.0, 31.0, 282.3, 70.0, 152.0, 89.0, 225.5, 93.0, 12.0, 4.0, 1.0, 13.33])),
     (0.0,
      DenseVector([63.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 211.2, 80.0, 237.7, 93.0, 259.2, 58.0, 12.3, 2.0, 0.0, 8.2])),
     (0.0,
      DenseVector([152.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 197.1, 126.0, 130.1, 76.0, 78.1, 100.0, 7.4, 4.0, 3.0, 10.96])),
     (0.0,
      DenseVector([147.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 205.3, 95.0, 166.7, 128.0, 240.6, 84.0, 7.8, 4.0, 1.0, 9.68])),
     (0.0,
      DenseVector([112.0, 1.0, 0.0, 0.0, 0.0, 1.0, 22.0, 181.8, 110.0, 228.1, 123.0, 262.7, 141.0, 9.2, 4.0, 2.0, 14.81])),
     (0.0,
      DenseVector([120.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 252.0, 120.0, 150.2, 106.0, 151.8, 96.0, 9.6, 1.0, 2.0, 14.1])),
     (0.0,
      DenseVector([27.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 193.8, 102.0, 118.9, 104.0, 135.9, 124.0, 9.2, 3.0, 0.0, 20.59])),
     (0.0,
      DenseVector([171.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 231.2, 135.0, 188.7, 74.0, 206.9, 124.0, 12.3, 1.0, 1.0, 11.69])),
     (0.0,
      DenseVector([101.0, 1.0, 0.0, 0.0, 0.0, 1.0, 33.0, 200.1, 108.0, 188.9, 122.0, 205.1, 90.0, 15.5, 4.0, 0.0, 14.81])),
     (0.0,
      DenseVector([32.0, 0.0, 1.0, 0.0, 0.0, 1.0, 26.0, 266.7, 109.0, 232.3, 107.0, 212.8, 98.0, 16.3, 4.0, 1.0, 9.43])),
     (0.0,
      DenseVector([3.0, 1.0, 0.0, 0.0, 0.0, 1.0, 36.0, 118.1, 117.0, 221.5, 125.0, 103.9, 89.0, 11.9, 6.0, 2.0, 16.22])),
     (0.0,
      DenseVector([151.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 175.3, 106.0, 144.3, 87.0, 160.2, 88.0, 11.8, 5.0, 0.0, 8.62])),
     (0.0,
      DenseVector([60.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 125.1, 99.0, 248.8, 62.0, 211.3, 79.0, 11.2, 3.0, 3.0, 13.64])),
     (0.0,
      DenseVector([119.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 176.8, 90.0, 224.7, 81.0, 204.6, 77.0, 7.5, 15.0, 1.0, 14.75])),
     (0.0,
      DenseVector([43.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 241.9, 101.0, 129.4, 121.0, 264.8, 104.0, 5.9, 3.0, 1.0, 7.84])),
     (0.0,
      DenseVector([42.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 241.2, 134.0, 116.5, 114.0, 152.2, 91.0, 10.6, 4.0, 0.0, 16.92])),
     (0.0,
      DenseVector([84.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 217.1, 99.0, 236.0, 68.0, 118.3, 120.0, 9.4, 4.0, 1.0, 12.68])),
     (0.0,
      DenseVector([65.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 195.4, 110.0, 181.2, 109.0, 178.5, 105.0, 8.9, 4.0, 0.0, 18.07])),
     (1.0,
      DenseVector([75.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 222.4, 78.0, 327.0, 111.0, 208.0, 104.0, 8.7, 9.0, 1.0, 25.0])),
     (0.0,
      DenseVector([116.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 189.5, 90.0, 189.8, 118.0, 205.8, 83.0, 13.1, 2.0, 1.0, 18.57])),
     (0.0,
      DenseVector([107.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 123.1, 100.0, 158.4, 82.0, 256.1, 82.0, 9.3, 5.0, 0.0, 9.43])),
     (0.0,
      DenseVector([189.0, 1.0, 0.0, 0.0, 0.0, 1.0, 38.0, 256.7, 98.0, 150.5, 120.0, 123.0, 87.0, 11.4, 3.0, 3.0, 8.2])),
     (0.0,
      DenseVector([123.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 159.1, 94.0, 241.6, 119.0, 202.4, 120.0, 6.5, 1.0, 1.0, 9.68])),
     (0.0,
      DenseVector([110.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 100.1, 90.0, 233.3, 93.0, 204.4, 57.0, 11.1, 8.0, 3.0, 5.77])),
     (0.0,
      DenseVector([63.0, 1.0, 0.0, 0.0, 0.0, 1.0, 32.0, 30.9, 113.0, 187.0, 113.0, 230.8, 101.0, 8.6, 7.0, 1.0, 13.64])),
     (0.0,
      DenseVector([176.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 223.2, 76.0, 214.4, 131.0, 154.4, 80.0, 10.1, 2.0, 3.0, 20.97])),
     (0.0,
      DenseVector([108.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 187.4, 101.0, 199.9, 126.0, 216.1, 107.0, 12.6, 8.0, 1.0, 23.33])),
     (0.0,
      DenseVector([13.0, 0.0, 0.0, 1.0, 0.0, 1.0, 21.0, 315.6, 105.0, 208.9, 71.0, 260.1, 123.0, 12.1, 3.0, 3.0, 17.86])),
     (0.0,
      DenseVector([71.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 277.5, 104.0, 131.8, 121.0, 126.9, 101.0, 8.2, 2.0, 1.0, 13.64])),
     (0.0,
      DenseVector([88.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 189.8, 111.0, 197.3, 101.0, 234.5, 111.0, 14.9, 3.0, 2.0, 18.57])),
     (0.0,
      DenseVector([137.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 147.2, 119.0, 192.8, 91.0, 172.7, 105.0, 10.2, 4.0, 1.0, 21.54])),
     (0.0,
      DenseVector([82.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 185.8, 36.0, 276.5, 134.0, 192.1, 104.0, 5.7, 7.0, 4.0, 8.2])),
     (0.0,
      DenseVector([92.0, 0.0, 0.0, 1.0, 0.0, 1.0, 29.0, 155.4, 110.0, 188.5, 104.0, 254.9, 118.0, 8.0, 4.0, 3.0, 26.47])),
     (0.0,
      DenseVector([165.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 154.2, 91.0, 268.6, 108.0, 188.8, 99.0, 10.9, 4.0, 6.0, 8.97])),
     (0.0,
      DenseVector([96.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 97.6, 98.0, 105.5, 118.0, 220.2, 105.0, 11.6, 9.0, 1.0, 20.59])),
     (0.0,
      DenseVector([156.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 178.8, 94.0, 178.4, 97.0, 169.2, 77.0, 7.5, 3.0, 1.0, 20.0])),
     (0.0,
      DenseVector([63.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 149.3, 104.0, 273.6, 75.0, 206.6, 72.0, 9.1, 4.0, 0.0, 21.21])),
     (0.0,
      DenseVector([37.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 206.0, 89.0, 186.0, 88.0, 307.1, 86.0, 8.4, 11.0, 0.0, 16.07])),
     (0.0,
      DenseVector([98.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 216.8, 86.0, 190.8, 114.0, 187.5, 79.0, 11.0, 9.0, 0.0, 6.82])),
     (0.0,
      DenseVector([121.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 103.3, 110.0, 129.1, 82.0, 167.1, 113.0, 10.7, 3.0, 0.0, 9.43])),
     (0.0,
      DenseVector([94.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 139.4, 95.0, 159.1, 92.0, 128.2, 129.0, 7.7, 3.0, 0.0, 9.23])),
     (0.0,
      DenseVector([99.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 191.2, 110.0, 163.9, 102.0, 243.6, 114.0, 14.1, 3.0, 1.0, 18.57])),
     (0.0,
      DenseVector([163.0, 0.0, 0.0, 1.0, 0.0, 1.0, 23.0, 160.0, 104.0, 189.4, 64.0, 229.9, 118.0, 10.4, 7.0, 1.0, 20.59])),
     (0.0,
      DenseVector([161.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 221.7, 95.0, 193.0, 82.0, 194.1, 113.0, 6.5, 4.0, 3.0, 11.11])),
     (0.0,
      DenseVector([99.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 62.9, 81.0, 231.0, 64.0, 168.9, 121.0, 8.5, 5.0, 1.0, 5.66])),
     (0.0,
      DenseVector([108.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 215.6, 78.0, 195.3, 119.0, 194.4, 65.0, 3.6, 5.0, 1.0, 13.64])),
     (0.0,
      DenseVector([84.0, 0.0, 0.0, 1.0, 0.0, 1.0, 42.0, 165.3, 97.0, 223.5, 118.0, 260.8, 72.0, 7.6, 7.0, 3.0, 16.22])),
     (0.0,
      DenseVector([83.0, 1.0, 0.0, 0.0, 1.0, 1.0, 32.0, 94.7, 111.0, 154.4, 98.0, 200.4, 109.0, 10.6, 6.0, 2.0, 12.33])),
     (0.0,
      DenseVector([139.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 203.2, 81.0, 152.5, 99.0, 197.8, 76.0, 9.7, 3.0, 2.0, 9.26])),
     (0.0,
      DenseVector([69.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 195.3, 70.0, 216.7, 108.0, 259.9, 119.0, 12.5, 4.0, 3.0, 9.43])),
     (0.0,
      DenseVector([129.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 143.7, 114.0, 297.8, 98.0, 212.6, 86.0, 11.4, 8.0, 4.0, 11.69])),
     (0.0,
      DenseVector([106.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 114.4, 104.0, 78.3, 101.0, 232.7, 78.0, 0.0, 0.0, 2.0, 11.11])),
     (0.0,
      DenseVector([158.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 222.8, 101.0, 203.0, 128.0, 210.6, 106.0, 6.9, 2.0, 2.0, 6.49])),
     (0.0,
      DenseVector([168.0, 1.0, 0.0, 0.0, 0.0, 1.0, 22.0, 175.9, 70.0, 211.7, 105.0, 174.5, 81.0, 7.3, 5.0, 2.0, 13.33])),
     (1.0,
      DenseVector([115.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 249.9, 95.0, 242.5, 104.0, 151.7, 121.0, 15.3, 6.0, 1.0, 9.43])),
     (0.0,
      DenseVector([57.0, 0.0, 1.0, 0.0, 1.0, 1.0, 30.0, 234.5, 130.0, 195.2, 116.0, 268.8, 94.0, 11.4, 4.0, 2.0, 14.81])),
     (0.0,
      DenseVector([67.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 210.7, 116.0, 219.2, 86.0, 179.7, 83.0, 7.2, 6.0, 1.0, 6.25])),
     (0.0,
      DenseVector([127.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 182.3, 124.0, 169.9, 110.0, 184.0, 116.0, 9.3, 3.0, 1.0, 5.77])),
     (0.0,
      DenseVector([78.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 190.3, 88.0, 194.5, 89.0, 256.5, 109.0, 11.7, 5.0, 2.0, 5.77])),
     (0.0,
      DenseVector([100.0, 1.0, 0.0, 0.0, 0.0, 1.0, 38.0, 177.1, 88.0, 163.7, 108.0, 242.7, 72.0, 7.4, 2.0, 0.0, 16.22])),
     (1.0,
      DenseVector([103.0, 0.0, 0.0, 1.0, 0.0, 1.0, 36.0, 87.2, 92.0, 169.3, 110.0, 166.7, 80.0, 10.9, 5.0, 6.0, 13.89])),
     (0.0,
      DenseVector([113.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 215.6, 96.0, 193.4, 127.0, 105.4, 115.0, 13.5, 3.0, 1.0, 13.56])),
     (0.0,
      DenseVector([78.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 137.4, 109.0, 237.6, 49.0, 206.7, 136.0, 14.0, 11.0, 3.0, 21.92])),
     (0.0,
      DenseVector([129.0, 0.0, 0.0, 1.0, 0.0, 1.0, 36.0, 192.8, 103.0, 177.0, 83.0, 216.5, 118.0, 16.4, 5.0, 1.0, 14.1])),
     (0.0,
      DenseVector([57.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 149.3, 100.0, 200.2, 110.0, 231.7, 101.0, 11.9, 3.0, 2.0, 9.43])),
     (0.0,
      DenseVector([82.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 143.7, 116.0, 170.7, 99.0, 287.7, 95.0, 7.8, 5.0, 1.0, 9.43])),
     (0.0,
      DenseVector([64.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 224.8, 111.0, 190.0, 101.0, 221.4, 110.0, 9.2, 2.0, 1.0, 26.47])),
     (0.0,
      DenseVector([86.0, 0.0, 0.0, 1.0, 0.0, 1.0, 39.0, 261.2, 122.0, 214.2, 101.0, 154.9, 101.0, 12.7, 5.0, 2.0, 21.54])),
     (0.0,
      DenseVector([151.0, 1.0, 0.0, 0.0, 0.0, 1.0, 26.0, 196.5, 98.0, 175.8, 111.0, 221.8, 124.0, 13.4, 5.0, 0.0, 20.97])),
     (1.0,
      DenseVector([94.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 271.2, 105.0, 202.6, 105.0, 221.6, 51.0, 11.5, 3.0, 3.0, 11.69])),
     (0.0,
      DenseVector([90.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 207.2, 121.0, 292.5, 104.0, 226.3, 103.0, 8.0, 1.0, 2.0, 11.69])),
     (0.0,
      DenseVector([48.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 300.4, 94.0, 133.2, 103.0, 197.4, 94.0, 7.2, 5.0, 2.0, 12.68])),
     (0.0,
      DenseVector([85.0, 0.0, 1.0, 0.0, 0.0, 1.0, 37.0, 229.6, 123.0, 132.3, 90.0, 211.9, 76.0, 9.5, 8.0, 2.0, 9.68])),
     (0.0,
      DenseVector([93.0, 1.0, 0.0, 0.0, 1.0, 1.0, 20.0, 187.5, 110.0, 169.8, 94.0, 175.3, 127.0, 12.1, 4.0, 1.0, 26.47])),
     (0.0,
      DenseVector([169.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 57.1, 98.0, 199.7, 78.0, 274.7, 103.0, 6.5, 6.0, 3.0, 9.26])),
     (1.0,
      DenseVector([68.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 162.1, 86.0, 155.0, 86.0, 189.7, 87.0, 11.0, 9.0, 5.0, 13.89])),
     (1.0,
      DenseVector([91.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 145.0, 89.0, 175.8, 102.0, 223.7, 151.0, 16.7, 3.0, 2.0, 13.56])),
     (0.0,
      DenseVector([68.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 159.5, 123.0, 240.8, 93.0, 210.3, 76.0, 11.4, 3.0, 1.0, 18.57])),
     (0.0,
      DenseVector([101.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 190.7, 72.0, 208.6, 103.0, 203.8, 111.0, 8.8, 8.0, 1.0, 21.92])),
     (0.0,
      DenseVector([67.0, 0.0, 0.0, 1.0, 0.0, 1.0, 20.0, 230.6, 40.0, 189.1, 58.0, 162.2, 115.0, 9.4, 2.0, 1.0, 13.89])),
     (0.0,
      DenseVector([66.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 34.0, 133.0, 278.6, 61.0, 129.6, 120.0, 11.5, 3.0, 0.0, 8.2])),
     (0.0,
      DenseVector([116.0, 1.0, 0.0, 0.0, 0.0, 1.0, 17.0, 193.4, 112.0, 240.6, 131.0, 248.1, 98.0, 11.4, 3.0, 5.0, 12.7])),
     (0.0,
      DenseVector([158.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 202.0, 126.0, 163.5, 86.0, 195.4, 84.0, 10.4, 6.0, 1.0, 7.84])),
     (0.0,
      DenseVector([78.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 191.7, 122.0, 241.4, 88.0, 203.5, 86.0, 9.1, 5.0, 1.0, 20.59])),
     (0.0,
      DenseVector([119.0, 1.0, 0.0, 0.0, 0.0, 1.0, 26.0, 161.3, 97.0, 250.3, 110.0, 142.4, 92.0, 6.6, 8.0, 1.0, 21.21])),
     (0.0,
      DenseVector([120.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 150.6, 85.0, 119.0, 128.0, 232.9, 123.0, 6.4, 2.0, 1.0, 21.92])),
     (0.0,
      DenseVector([155.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 184.6, 102.0, 196.0, 117.0, 226.5, 122.0, 7.8, 1.0, 1.0, 13.56])),
     (0.0,
      DenseVector([106.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 220.7, 120.0, 270.2, 95.0, 121.6, 113.0, 8.7, 5.0, 1.0, 7.84])),
     (0.0,
      DenseVector([87.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 167.3, 119.0, 198.5, 119.0, 133.1, 88.0, 11.0, 6.0, 1.0, 9.68])),
     (0.0,
      DenseVector([146.0, 1.0, 0.0, 0.0, 0.0, 1.0, 32.0, 154.0, 80.0, 185.5, 91.0, 148.2, 107.0, 8.2, 4.0, 3.0, 10.0])),
     (0.0,
      DenseVector([101.0, 1.0, 0.0, 0.0, 0.0, 1.0, 29.0, 121.1, 116.0, 186.4, 100.0, 241.7, 75.0, 10.1, 6.0, 0.0, 11.11])),
     (0.0,
      DenseVector([22.0, 0.0, 0.0, 1.0, 0.0, 1.0, 23.0, 182.1, 94.0, 164.6, 59.0, 128.8, 102.0, 12.7, 4.0, 3.0, 13.64])),
     (0.0,
      DenseVector([90.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 109.6, 88.0, 137.6, 108.0, 159.7, 121.0, 11.0, 5.0, 2.0, 25.0])),
     (0.0,
      DenseVector([41.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 209.9, 105.0, 121.9, 105.0, 253.7, 104.0, 9.6, 4.0, 1.0, 18.07])),
     (0.0,
      DenseVector([69.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 167.5, 76.0, 242.1, 92.0, 101.2, 103.0, 11.4, 4.0, 2.0, 14.1])),
     (0.0,
      DenseVector([33.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 213.9, 88.0, 239.8, 119.0, 148.7, 71.0, 9.8, 14.0, 2.0, 11.69])),
     (0.0,
      DenseVector([112.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 115.8, 108.0, 243.3, 111.0, 184.6, 78.0, 13.1, 5.0, 1.0, 13.89])),
     (0.0,
      DenseVector([108.0, 0.0, 0.0, 1.0, 0.0, 1.0, 30.0, 276.6, 99.0, 220.1, 113.0, 177.9, 95.0, 9.8, 6.0, 2.0, 7.84])),
     (0.0,
      DenseVector([136.0, 1.0, 0.0, 0.0, 0.0, 1.0, 21.0, 179.4, 88.0, 181.1, 97.0, 320.7, 120.0, 9.5, 4.0, 2.0, 21.21])),
     (0.0,
      DenseVector([128.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 187.3, 84.0, 270.8, 95.0, 206.4, 68.0, 10.1, 5.0, 1.0, 16.18])),
     (0.0,
      DenseVector([27.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 201.2, 128.0, 227.2, 100.0, 145.8, 91.0, 8.4, 3.0, 2.0, 16.18])),
     (0.0,
      DenseVector([161.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 189.6, 78.0, 267.4, 117.0, 184.5, 137.0, 1.3, 6.0, 1.0, 11.69])),
     (0.0,
      DenseVector([33.0, 1.0, 0.0, 0.0, 0.0, 1.0, 35.0, 186.8, 124.0, 261.0, 69.0, 317.8, 103.0, 15.0, 5.0, 0.0, 9.43])),
     (0.0,
      DenseVector([120.0, 1.0, 0.0, 0.0, 0.0, 1.0, 31.0, 153.5, 83.0, 219.1, 96.0, 237.4, 76.0, 11.4, 4.0, 0.0, 12.7])),
     (0.0,
      DenseVector([113.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 187.6, 97.0, 208.2, 118.0, 158.9, 101.0, 8.7, 6.0, 2.0, 26.47])),
     (1.0,
      DenseVector([122.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 230.9, 132.0, 243.2, 99.0, 182.4, 57.0, 11.0, 2.0, 0.0, 17.78])),
     (0.0,
      DenseVector([148.0, 1.0, 0.0, 0.0, 0.0, 1.0, 26.0, 244.9, 150.0, 118.0, 138.0, 236.0, 91.0, 15.2, 4.0, 2.0, 9.43])),
     (0.0,
      DenseVector([74.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 230.9, 93.0, 223.0, 78.0, 157.8, 101.0, 9.7, 2.0, 3.0, 26.47])),
     (0.0,
      DenseVector([106.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 187.1, 104.0, 250.2, 117.0, 144.9, 81.0, 11.0, 3.0, 1.0, 20.59])),
     (0.0,
      DenseVector([179.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 170.7, 54.0, 191.1, 108.0, 214.6, 107.0, 13.3, 4.0, 1.0, 17.86])),
     (1.0,
      DenseVector([149.0, 1.0, 0.0, 0.0, 1.0, 1.0, 28.0, 126.9, 97.0, 166.9, 102.0, 145.2, 77.0, 8.8, 3.0, 5.0, 8.97])),
     (0.0,
      DenseVector([77.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 189.5, 112.0, 207.0, 95.0, 214.1, 91.0, 9.2, 7.0, 0.0, 12.33])),
     (1.0,
      DenseVector([127.0, 0.0, 1.0, 0.0, 1.0, 0.0, 0.0, 176.9, 110.0, 167.9, 100.0, 182.2, 138.0, 7.7, 2.0, 1.0, 16.92])),
     (0.0,
      DenseVector([80.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 161.1, 99.0, 198.8, 81.0, 228.4, 116.0, 10.6, 4.0, 1.0, 14.1])),
     (0.0,
      DenseVector([106.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 169.4, 107.0, 197.2, 71.0, 202.2, 79.0, 10.7, 4.0, 1.0, 20.59])),
     (0.0,
      DenseVector([61.0, 1.0, 0.0, 0.0, 0.0, 1.0, 20.0, 254.4, 133.0, 161.7, 96.0, 251.4, 91.0, 10.5, 4.0, 0.0, 10.0])),
     (0.0,
      DenseVector([135.0, 0.0, 0.0, 1.0, 1.0, 1.0, 24.0, 127.7, 54.0, 215.0, 105.0, 234.3, 84.0, 5.8, 4.0, 2.0, 9.68])),
     (0.0,
      DenseVector([115.0, 1.0, 0.0, 0.0, 0.0, 1.0, 26.0, 170.5, 107.0, 217.2, 77.0, 225.7, 71.0, 13.6, 5.0, 6.0, 7.84])),
     (0.0,
      DenseVector([167.0, 0.0, 1.0, 0.0, 1.0, 0.0, 0.0, 219.1, 100.0, 242.9, 90.0, 168.9, 101.0, 10.1, 4.0, 2.0, 9.68])),
     (0.0,
      DenseVector([107.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 273.5, 104.0, 183.8, 68.0, 153.8, 67.0, 11.0, 9.0, 2.0, 21.54])),
     (0.0,
      DenseVector([112.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 161.9, 138.0, 200.9, 114.0, 134.0, 134.0, 10.7, 4.0, 1.0, 9.43])),
     (0.0,
      DenseVector([35.0, 0.0, 0.0, 1.0, 0.0, 1.0, 27.0, 241.7, 87.0, 142.0, 101.0, 288.9, 68.0, 9.4, 4.0, 1.0, 8.97])),
     (0.0,
      DenseVector([103.0, 0.0, 1.0, 0.0, 1.0, 0.0, 0.0, 62.8, 124.0, 170.4, 66.0, 280.2, 78.0, 9.4, 4.0, 3.0, 18.57])),
     (0.0,
      DenseVector([107.0, 1.0, 0.0, 0.0, 0.0, 1.0, 22.0, 281.1, 83.0, 143.7, 130.0, 239.4, 128.0, 11.2, 9.0, 1.0, 11.11])),
     (0.0,
      DenseVector([69.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 228.2, 70.0, 263.7, 80.0, 142.6, 60.0, 10.7, 5.0, 3.0, 17.78])),
     (0.0,
      DenseVector([85.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 209.8, 82.0, 194.5, 94.0, 200.4, 85.0, 11.3, 3.0, 0.0, 13.33])),
     (1.0,
      DenseVector([24.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 265.6, 86.0, 208.8, 102.0, 182.5, 105.0, 11.1, 6.0, 2.0, 26.47])),
     (0.0,
      DenseVector([90.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 214.9, 97.0, 117.8, 117.0, 133.7, 78.0, 11.8, 2.0, 2.0, 10.0])),
     (0.0,
      DenseVector([137.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 110.5, 79.0, 223.2, 111.0, 169.5, 64.0, 10.5, 3.0, 3.0, 20.97])),
     (0.0,
      DenseVector([92.0, 1.0, 0.0, 0.0, 1.0, 1.0, 45.0, 281.1, 88.0, 198.0, 103.0, 94.3, 76.0, 7.5, 3.0, 0.0, 6.25])),
     (0.0,
      DenseVector([38.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 137.8, 86.0, 286.3, 76.0, 167.0, 77.0, 14.1, 3.0, 2.0, 10.96])),
     (1.0,
      DenseVector([69.0, 0.0, 0.0, 1.0, 1.0, 1.0, 33.0, 271.5, 98.0, 253.4, 102.0, 165.4, 85.0, 8.2, 2.0, 1.0, 21.21])),
     (0.0,
      DenseVector([45.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 112.8, 108.0, 218.8, 120.0, 240.2, 106.0, 9.0, 3.0, 2.0, 8.97])),
     (0.0,
      DenseVector([73.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 187.3, 118.0, 239.7, 90.0, 167.5, 108.0, 15.1, 2.0, 1.0, 5.66])),
     (0.0,
      DenseVector([92.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 197.0, 84.0, 269.3, 105.0, 158.9, 105.0, 10.8, 4.0, 1.0, 14.75])),
     (0.0,
      DenseVector([113.0, 1.0, 0.0, 0.0, 0.0, 1.0, 32.0, 180.4, 89.0, 129.4, 124.0, 166.9, 124.0, 8.4, 2.0, 1.0, 6.25])),
     (1.0,
      DenseVector([68.0, 0.0, 1.0, 0.0, 1.0, 0.0, 0.0, 148.5, 126.0, 219.4, 125.0, 198.5, 121.0, 14.5, 7.0, 1.0, 6.49])),
     (0.0,
      DenseVector([135.0, 1.0, 0.0, 0.0, 0.0, 1.0, 22.0, 197.1, 113.0, 259.4, 95.0, 134.7, 135.0, 14.6, 5.0, 2.0, 14.81])),
     (0.0,
      DenseVector([100.0, 1.0, 0.0, 0.0, 0.0, 1.0, 26.0, 153.7, 115.0, 137.8, 146.0, 213.5, 104.0, 15.9, 5.0, 1.0, 6.25])),
     (0.0,
      DenseVector([96.0, 1.0, 0.0, 0.0, 0.0, 1.0, 27.0, 261.3, 96.0, 220.9, 101.0, 179.4, 97.0, 11.3, 2.0, 1.0, 17.86])),
     (0.0,
      DenseVector([108.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 246.2, 102.0, 202.4, 134.0, 180.1, 95.0, 9.4, 5.0, 1.0, 20.97])),
     (0.0,
      DenseVector([84.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 191.0, 88.0, 318.8, 119.0, 247.3, 79.0, 6.5, 4.0, 0.0, 12.7])),
     (0.0,
      DenseVector([134.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 208.3, 86.0, 253.6, 89.0, 291.0, 86.0, 12.6, 3.0, 1.0, 21.21])),
     (0.0,
      DenseVector([72.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 253.0, 73.0, 219.3, 78.0, 210.8, 89.0, 9.8, 4.0, 0.0, 20.59])),
     (0.0,
      DenseVector([83.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 202.3, 87.0, 201.5, 111.0, 101.7, 82.0, 6.8, 4.0, 0.0, 10.0])),
     (0.0,
      DenseVector([137.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 174.4, 120.0, 156.3, 98.0, 136.5, 121.0, 10.2, 5.0, 0.0, 9.43])),
     (0.0,
      DenseVector([56.0, 1.0, 0.0, 0.0, 0.0, 1.0, 30.0, 127.1, 89.0, 172.1, 116.0, 194.6, 111.0, 12.1, 3.0, 1.0, 14.81])),
     (0.0,
      DenseVector([61.0, 0.0, 0.0, 1.0, 1.0, 1.0, 16.0, 143.5, 76.0, 242.6, 58.0, 147.7, 95.0, 11.3, 3.0, 0.0, 12.82])),
     (0.0,
      DenseVector([171.0, 0.0, 0.0, 1.0, 0.0, 1.0, 17.0, 186.9, 94.0, 240.0, 138.0, 200.9, 64.0, 5.8, 3.0, 1.0, 14.75])),
     (0.0,
      DenseVector([123.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 194.0, 118.0, 242.0, 114.0, 146.3, 108.0, 12.1, 4.0, 1.0, 8.2])),
     (0.0,
      DenseVector([58.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 234.8, 89.0, 106.8, 131.0, 178.5, 122.0, 9.9, 6.0, 0.0, 12.7])),
     (0.0,
      DenseVector([156.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 123.7, 96.0, 103.0, 80.0, 189.4, 82.0, 13.1, 4.0, 1.0, 5.77])),
     (0.0,
      DenseVector([166.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 173.9, 103.0, 276.4, 83.0, 190.8, 113.0, 15.3, 5.0, 0.0, 8.97])),
     (0.0,
      DenseVector([75.0, 0.0, 0.0, 1.0, 0.0, 1.0, 41.0, 130.9, 115.0, 203.4, 110.0, 171.7, 68.0, 12.4, 4.0, 1.0, 21.21])),
     (1.0,
      DenseVector([75.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 314.6, 102.0, 169.8, 86.0, 285.1, 100.0, 5.7, 3.0, 2.0, 13.56])),
     (0.0,
      DenseVector([83.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 227.9, 78.0, 207.5, 115.0, 211.7, 100.0, 12.1, 5.0, 1.0, 12.82])),
     (0.0,
      DenseVector([243.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 95.5, 92.0, 163.7, 63.0, 264.2, 118.0, 6.6, 6.0, 2.0, 13.89])),
     (0.0,
      DenseVector([153.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 185.3, 127.0, 208.0, 73.0, 206.1, 124.0, 15.1, 3.0, 1.0, 9.68])),
     (0.0,
      DenseVector([150.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 146.3, 133.0, 202.7, 95.0, 234.7, 103.0, 13.1, 3.0, 1.0, 17.86])),
     (0.0,
      DenseVector([92.0, 0.0, 0.0, 1.0, 0.0, 1.0, 16.0, 184.0, 99.0, 76.4, 134.0, 185.1, 96.0, 12.7, 3.0, 2.0, 9.43])),
     (0.0,
      DenseVector([80.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 105.8, 110.0, 43.9, 88.0, 189.6, 87.0, 13.1, 5.0, 0.0, 17.86])),
     (0.0,
      DenseVector([134.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 178.0, 110.0, 153.8, 64.0, 236.6, 105.0, 11.7, 4.0, 1.0, 10.0])),
     (0.0,
      DenseVector([77.0, 0.0, 0.0, 1.0, 0.0, 1.0, 24.0, 149.4, 74.0, 123.9, 72.0, 174.3, 84.0, 10.1, 6.0, 1.0, 17.78])),
     (0.0,
      DenseVector([147.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 209.4, 104.0, 132.5, 78.0, 149.4, 123.0, 11.3, 3.0, 2.0, 14.75])),
     (0.0,
      DenseVector([74.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 172.1, 105.0, 211.7, 99.0, 182.2, 105.0, 11.6, 6.0, 1.0, 11.11])),
     (0.0,
      DenseVector([138.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 169.3, 82.0, 217.9, 147.0, 184.2, 77.0, 9.4, 9.0, 1.0, 8.62])),
     (0.0,
      DenseVector([143.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 119.1, 117.0, 287.7, 136.0, 223.0, 100.0, 12.2, 4.0, 0.0, 12.7])),
     (0.0,
      DenseVector([64.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 194.2, 147.0, 173.4, 87.0, 268.7, 114.0, 5.5, 2.0, 2.0, 5.66])),
     (0.0,
      DenseVector([120.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 198.8, 56.0, 230.1, 73.0, 119.8, 81.0, 9.9, 3.0, 2.0, 20.97])),
     (1.0,
      DenseVector([121.0, 0.0, 1.0, 0.0, 1.0, 0.0, 0.0, 167.7, 94.0, 93.7, 121.0, 241.3, 115.0, 13.4, 1.0, 3.0, 13.64])),
     (0.0,
      DenseVector([88.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 202.2, 86.0, 216.8, 93.0, 239.4, 99.0, 11.8, 2.0, 2.0, 16.07])),
     (1.0,
      DenseVector([87.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 322.5, 106.0, 204.6, 93.0, 186.2, 128.0, 9.4, 4.0, 2.0, 23.33])),
     (0.0,
      DenseVector([100.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 216.2, 107.0, 215.6, 84.0, 138.4, 127.0, 10.2, 3.0, 0.0, 12.68])),
     (0.0,
      DenseVector([104.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 76.4, 116.0, 115.6, 74.0, 226.3, 94.0, 9.4, 3.0, 3.0, 12.7])),
     (0.0,
      DenseVector([27.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 72.7, 75.0, 208.6, 117.0, 65.8, 71.0, 9.9, 3.0, 1.0, 14.81])),
     (0.0,
      DenseVector([81.0, 1.0, 0.0, 0.0, 0.0, 1.0, 31.0, 210.4, 100.0, 225.5, 97.0, 168.7, 120.0, 9.7, 4.0, 0.0, 8.62])),
     (0.0,
      DenseVector([64.0, 0.0, 0.0, 1.0, 1.0, 1.0, 33.0, 127.2, 93.0, 162.9, 104.0, 247.4, 109.0, 8.1, 13.0, 0.0, 16.18])),
     (0.0,
      DenseVector([107.0, 0.0, 0.0, 1.0, 0.0, 1.0, 28.0, 201.8, 79.0, 304.9, 128.0, 225.6, 133.0, 11.9, 8.0, 1.0, 10.96])),
     (0.0,
      DenseVector([88.0, 1.0, 0.0, 0.0, 0.0, 1.0, 17.0, 219.5, 78.0, 222.1, 94.0, 188.3, 92.0, 16.1, 5.0, 1.0, 9.26])),
     (0.0,
      DenseVector([111.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 99.3, 112.0, 270.5, 136.0, 225.3, 94.0, 9.0, 6.0, 3.0, 10.96])),
     (0.0,
      DenseVector([77.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 239.2, 114.0, 150.0, 115.0, 160.8, 81.0, 10.3, 2.0, 5.0, 21.21])),
     (0.0,
      DenseVector([67.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 120.9, 58.0, 235.0, 88.0, 95.1, 130.0, 11.4, 11.0, 2.0, 14.1])),
     (0.0,
      DenseVector([102.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 224.7, 81.0, 129.4, 112.0, 167.6, 109.0, 15.8, 6.0, 1.0, 10.0])),
     (0.0,
      DenseVector([146.0, 0.0, 1.0, 0.0, 0.0, 1.0, 19.0, 176.6, 88.0, 162.7, 66.0, 215.5, 98.0, 14.6, 6.0, 1.0, 9.68])),
     (0.0,
      DenseVector([144.0, 1.0, 0.0, 0.0, 0.0, 1.0, 51.0, 283.9, 98.0, 192.0, 109.0, 196.3, 85.0, 10.0, 4.0, 1.0, 12.7])),
     (1.0,
      DenseVector([96.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 180.6, 92.0, 190.9, 114.0, 295.6, 125.0, 10.3, 4.0, 1.0, 8.2])),
     (0.0,
      DenseVector([70.0, 1.0, 0.0, 0.0, 0.0, 1.0, 31.0, 125.9, 101.0, 196.4, 102.0, 252.7, 75.0, 10.3, 4.0, 1.0, 9.68])),
     (0.0,
      DenseVector([149.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 237.6, 79.0, 192.4, 107.0, 207.4, 111.0, 9.1, 9.0, 0.0, 20.97])),
     (0.0,
      DenseVector([129.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 198.4, 91.0, 264.7, 106.0, 111.4, 101.0, 9.2, 2.0, 2.0, 8.62])),
     (0.0,
      DenseVector([166.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 274.3, 110.0, 52.9, 109.0, 246.1, 119.0, 10.9, 5.0, 0.0, 21.21])),
     (1.0,
      DenseVector([136.0, 0.0, 1.0, 0.0, 1.0, 0.0, 0.0, 199.6, 89.0, 211.4, 96.0, 72.4, 84.0, 11.0, 4.0, 3.0, 16.92])),
     (0.0,
      DenseVector([149.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 217.7, 91.0, 273.5, 74.0, 226.9, 99.0, 9.6, 3.0, 3.0, 18.57])),
     (0.0,
      DenseVector([70.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 134.7, 96.0, 235.9, 90.0, 260.2, 113.0, 7.6, 6.0, 3.0, 9.23])),
     (0.0,
      DenseVector([120.0, 1.0, 0.0, 0.0, 0.0, 1.0, 24.0, 212.7, 73.0, 257.5, 103.0, 227.8, 119.0, 9.7, 13.0, 2.0, 11.11])),
     (0.0,
      DenseVector([66.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 256.3, 135.0, 180.2, 106.0, 187.3, 135.0, 6.2, 7.0, 2.0, 6.82])),
     (0.0,
      DenseVector([104.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 183.6, 133.0, 120.7, 98.0, 215.1, 112.0, 12.7, 2.0, 1.0, 11.69])),
     (0.0,
      DenseVector([160.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 176.2, 90.0, 196.0, 115.0, 263.9, 95.0, 9.2, 4.0, 1.0, 21.21])),
     (0.0,
      DenseVector([129.0, 1.0, 0.0, 0.0, 0.0, 1.0, 37.0, 205.0, 94.0, 165.4, 103.0, 185.0, 81.0, 11.7, 8.0, 1.0, 8.97])),
     (1.0,
      DenseVector([93.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 267.9, 114.0, 223.0, 74.0, 262.7, 90.0, 11.3, 3.0, 3.0, 10.0])),
     (0.0,
      DenseVector([169.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 179.2, 111.0, 175.2, 130.0, 228.6, 92.0, 9.9, 6.0, 2.0, 5.66])),
     (0.0,
      DenseVector([58.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 149.4, 145.0, 196.5, 105.0, 209.5, 108.0, 14.9, 3.0, 1.0, 11.11])),
     (0.0,
      DenseVector([75.0, 0.0, 0.0, 1.0, 0.0, 1.0, 38.0, 163.6, 132.0, 146.7, 113.0, 345.8, 115.0, 13.1, 3.0, 3.0, 26.47])),
     (0.0,
      DenseVector([45.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 207.6, 71.0, 152.7, 94.0, 217.8, 125.0, 12.4, 13.0, 1.0, 11.11])),
     (0.0,
      DenseVector([155.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 165.4, 108.0, 183.7, 103.0, 80.2, 108.0, 8.9, 4.0, 3.0, 16.22])),
     (0.0,
      DenseVector([52.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 209.8, 114.0, 171.3, 82.0, 154.6, 119.0, 9.9, 9.0, 4.0, 24.29])),
     (0.0,
      DenseVector([119.0, 1.0, 0.0, 0.0, 0.0, 1.0, 27.0, 220.1, 128.0, 268.2, 133.0, 146.5, 80.0, 11.1, 3.0, 0.0, 12.82])),
     (0.0,
      DenseVector([86.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 141.3, 72.0, 154.3, 95.0, 210.6, 91.0, 8.2, 5.0, 1.0, 21.21])),
     (0.0,
      DenseVector([42.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 196.5, 89.0, 241.3, 123.0, 143.2, 105.0, 4.0, 7.0, 0.0, 24.29])),
     (0.0,
      DenseVector([127.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 180.9, 114.0, 209.5, 118.0, 249.9, 105.0, 7.4, 4.0, 2.0, 8.2])),
     (0.0,
      DenseVector([123.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 105.0, 150.0, 251.6, 90.0, 258.0, 93.0, 14.9, 5.0, 0.0, 12.82])),
     (1.0,
      DenseVector([98.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 271.4, 119.0, 190.4, 102.0, 284.7, 118.0, 11.1, 6.0, 4.0, 16.92])),
     (0.0,
      DenseVector([149.0, 0.0, 0.0, 1.0, 0.0, 1.0, 43.0, 206.7, 79.0, 174.6, 122.0, 241.5, 80.0, 10.9, 3.0, 1.0, 14.75])),
     (0.0,
      DenseVector([160.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 166.8, 109.0, 236.0, 117.0, 307.6, 77.0, 9.3, 1.0, 1.0, 16.92])),
     (0.0,
      DenseVector([103.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 204.9, 107.0, 135.2, 102.0, 208.2, 106.0, 10.4, 3.0, 5.0, 21.21])),
     (0.0,
      DenseVector([132.0, 1.0, 0.0, 0.0, 0.0, 1.0, 15.0, 154.6, 128.0, 245.6, 106.0, 148.6, 90.0, 9.1, 4.0, 1.0, 5.66])),
     (0.0,
      DenseVector([137.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 127.0, 107.0, 323.2, 75.0, 143.9, 127.0, 7.5, 2.0, 1.0, 13.64])),
     (0.0,
      DenseVector([129.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 267.4, 78.0, 204.2, 85.0, 111.7, 146.0, 5.9, 4.0, 1.0, 12.7])),
     (0.0,
      DenseVector([62.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 281.0, 66.0, 160.6, 108.0, 77.9, 74.0, 0.0, 0.0, 1.0, 8.97])),
     (0.0,
      DenseVector([122.0, 0.0, 0.0, 1.0, 0.0, 1.0, 33.0, 270.8, 96.0, 220.4, 110.0, 169.9, 104.0, 11.8, 8.0, 4.0, 12.33])),
     (0.0,
      DenseVector([32.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 171.2, 82.0, 185.6, 102.0, 203.3, 64.0, 10.2, 7.0, 1.0, 11.69])),
     (0.0,
      DenseVector([86.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 124.1, 82.0, 202.6, 120.0, 289.6, 119.0, 6.7, 8.0, 3.0, 14.81])),
     (0.0,
      DenseVector([130.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 162.8, 113.0, 290.3, 111.0, 114.9, 140.0, 7.2, 3.0, 1.0, 12.7])),
     (0.0,
      DenseVector([42.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 146.3, 84.0, 255.9, 113.0, 45.0, 117.0, 8.0, 12.0, 1.0, 11.69])),
     (0.0,
      DenseVector([73.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 254.8, 85.0, 143.4, 80.0, 153.9, 102.0, 15.0, 7.0, 2.0, 14.75])),
     (0.0,
      DenseVector([66.0, 0.0, 1.0, 0.0, 0.0, 1.0, 26.0, 254.9, 108.0, 243.2, 135.0, 190.8, 95.0, 5.4, 3.0, 2.0, 20.97])),
     (0.0,
      DenseVector([103.0, 0.0, 0.0, 1.0, 0.0, 1.0, 31.0, 107.7, 124.0, 188.9, 104.0, 196.2, 98.0, 8.9, 3.0, 0.0, 9.26])),
     (0.0,
      DenseVector([128.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 158.8, 75.0, 264.8, 91.0, 270.0, 77.0, 7.6, 7.0, 1.0, 6.82])),
     (0.0,
      DenseVector([104.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 182.9, 113.0, 239.6, 85.0, 229.8, 104.0, 5.5, 4.0, 2.0, 13.64])),
     (0.0,
      DenseVector([103.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 198.5, 112.0, 42.5, 90.0, 179.2, 124.0, 12.4, 5.0, 0.0, 17.86])),
     (0.0,
      DenseVector([124.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 178.4, 72.0, 233.6, 134.0, 179.4, 91.0, 12.0, 2.0, 0.0, 10.96])),
     (0.0,
      DenseVector([87.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 110.9, 91.0, 158.5, 115.0, 207.5, 131.0, 6.2, 5.0, 1.0, 6.25])),
     (1.0,
      DenseVector([109.0, 1.0, 0.0, 0.0, 0.0, 1.0, 27.0, 166.9, 85.0, 221.2, 92.0, 197.3, 97.0, 12.3, 4.0, 1.0, 7.84])),
     (0.0,
      DenseVector([167.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 244.8, 91.0, 60.8, 105.0, 176.7, 110.0, 10.7, 3.0, 2.0, 11.11])),
     (1.0,
      DenseVector([97.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 120.8, 96.0, 169.8, 101.0, 194.1, 63.0, 11.9, 3.0, 4.0, 20.97])),
     (0.0,
      DenseVector([106.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 165.3, 118.0, 210.0, 101.0, 187.2, 93.0, 8.5, 3.0, 2.0, 24.29])),
     (0.0,
      DenseVector([125.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 126.7, 113.0, 155.5, 131.0, 206.2, 112.0, 14.4, 7.0, 2.0, 10.96])),
     (0.0,
      DenseVector([108.0, 0.0, 1.0, 0.0, 0.0, 1.0, 35.0, 215.9, 106.0, 200.6, 107.0, 195.4, 107.0, 15.5, 7.0, 0.0, 9.26])),
     (0.0,
      DenseVector([125.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 140.1, 132.0, 209.6, 126.0, 264.1, 77.0, 8.0, 2.0, 1.0, 11.69])),
     (0.0,
      DenseVector([89.0, 1.0, 0.0, 0.0, 0.0, 1.0, 32.0, 209.9, 113.0, 249.8, 104.0, 224.2, 92.0, 8.7, 7.0, 1.0, 6.49])),
     (0.0,
      DenseVector([72.0, 0.0, 0.0, 1.0, 1.0, 1.0, 29.0, 139.8, 114.0, 138.2, 91.0, 221.0, 88.0, 5.5, 6.0, 0.0, 6.49])),
     (1.0,
      DenseVector([23.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 321.6, 107.0, 251.6, 115.0, 141.1, 158.0, 11.3, 3.0, 2.0, 16.22])),
     (0.0,
      DenseVector([149.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 166.6, 61.0, 218.8, 107.0, 208.3, 131.0, 8.2, 6.0, 7.0, 5.66])),
     (0.0,
      DenseVector([73.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 214.2, 90.0, 196.8, 78.0, 157.9, 112.0, 5.9, 8.0, 0.0, 8.97])),
     (1.0,
      DenseVector([61.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 260.0, 123.0, 210.5, 127.0, 234.7, 70.0, 9.0, 3.0, 1.0, 24.29])),
     (1.0,
      DenseVector([161.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 191.9, 113.0, 70.9, 87.0, 204.8, 107.0, 13.4, 4.0, 4.0, 9.43])),
     (0.0,
      DenseVector([73.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 213.0, 95.0, 188.8, 104.0, 136.2, 89.0, 13.5, 3.0, 0.0, 10.96])),
     (0.0,
      DenseVector([118.0, 1.0, 0.0, 0.0, 0.0, 1.0, 24.0, 118.1, 83.0, 109.6, 72.0, 245.5, 73.0, 16.9, 2.0, 1.0, 13.89])),
     (0.0,
      DenseVector([23.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 190.2, 89.0, 166.4, 108.0, 219.8, 73.0, 15.0, 4.0, 6.0, 13.64])),
     (0.0,
      DenseVector([127.0, 1.0, 0.0, 0.0, 0.0, 1.0, 25.0, 82.2, 95.0, 163.3, 109.0, 264.9, 104.0, 5.1, 6.0, 0.0, 16.18])),
     (0.0,
      DenseVector([42.0, 1.0, 0.0, 0.0, 0.0, 1.0, 32.0, 163.8, 80.0, 177.8, 123.0, 190.4, 106.0, 8.1, 5.0, 0.0, 26.47])),
     (1.0,
      DenseVector([118.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 267.8, 145.0, 316.4, 121.0, 208.6, 91.0, 14.4, 11.0, 5.0, 20.0])),
     (0.0,
      DenseVector([45.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 159.8, 91.0, 120.4, 86.0, 163.0, 93.0, 10.6, 3.0, 2.0, 6.82])),
     (0.0,
      DenseVector([50.0, 0.0, 1.0, 0.0, 0.0, 1.0, 24.0, 214.3, 129.0, 289.8, 55.0, 312.5, 130.0, 10.6, 4.0, 1.0, 14.81])),
     (1.0,
      DenseVector([179.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 287.3, 123.0, 288.0, 114.0, 266.0, 112.0, 10.5, 4.0, 0.0, 11.11])),
     (0.0,
      DenseVector([152.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 101.2, 122.0, 141.6, 87.0, 198.5, 124.0, 7.5, 3.0, 2.0, 11.11])),
     (0.0,
      DenseVector([105.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 102.8, 74.0, 281.7, 125.0, 228.1, 113.0, 13.2, 5.0, 2.0, 11.69])),
     (0.0,
      DenseVector([72.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 109.1, 97.0, 115.7, 96.0, 295.8, 84.0, 8.3, 6.0, 0.0, 5.66])),
     (0.0,
      DenseVector([52.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 215.9, 67.0, 217.0, 108.0, 342.8, 130.0, 5.2, 2.0, 1.0, 17.78])),
     (0.0,
      DenseVector([125.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 203.4, 110.0, 128.7, 97.0, 190.5, 113.0, 11.0, 4.0, 1.0, 25.0])),
     (0.0,
      DenseVector([143.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 110.1, 113.0, 169.0, 59.0, 166.7, 94.0, 9.2, 2.0, 1.0, 6.49])),
     (0.0,
      DenseVector([65.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 111.0, 51.0, 219.8, 84.0, 202.0, 89.0, 4.4, 14.0, 1.0, 9.23])),
     (0.0,
      DenseVector([80.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 239.9, 121.0, 142.3, 51.0, 364.3, 106.0, 9.3, 5.0, 1.0, 8.97])),
     (0.0,
      DenseVector([1.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 144.8, 107.0, 112.5, 66.0, 218.7, 79.0, 13.8, 3.0, 1.0, 21.54])),
     (0.0,
      DenseVector([60.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 135.4, 134.0, 205.9, 85.0, 204.0, 103.0, 7.9, 4.0, 1.0, 13.56])),
     (0.0,
      DenseVector([43.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 84.2, 134.0, 80.8, 103.0, 196.1, 79.0, 10.8, 2.0, 1.0, 16.18])),
     (0.0,
      DenseVector([143.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 209.1, 127.0, 106.1, 80.0, 179.6, 90.0, 14.0, 6.0, 0.0, 21.21])),
     (0.0,
      DenseVector([81.0, 1.0, 0.0, 0.0, 0.0, 1.0, 24.0, 130.1, 117.0, 196.0, 61.0, 139.3, 123.0, 11.4, 5.0, 0.0, 13.89])),
     (0.0,
      DenseVector([205.0, 0.0, 0.0, 1.0, 0.0, 1.0, 24.0, 175.8, 139.0, 155.0, 98.0, 180.7, 64.0, 7.8, 5.0, 2.0, 20.97])),
     (0.0,
      DenseVector([24.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 241.9, 104.0, 145.2, 112.0, 214.5, 105.0, 6.6, 5.0, 1.0, 5.66])),
     (0.0,
      DenseVector([74.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 136.7, 106.0, 228.6, 105.0, 265.3, 114.0, 9.8, 4.0, 0.0, 12.82])),
     (0.0,
      DenseVector([77.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 67.7, 68.0, 195.7, 86.0, 236.5, 137.0, 12.0, 2.0, 1.0, 9.43])),
     (0.0,
      DenseVector([74.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 200.4, 87.0, 309.2, 105.0, 152.1, 118.0, 10.0, 2.0, 1.0, 14.75])),
     (1.0,
      DenseVector([74.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 125.8, 103.0, 207.7, 96.0, 207.4, 143.0, 14.1, 4.0, 1.0, 13.56])),
     (0.0,
      DenseVector([200.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 128.2, 87.0, 133.2, 105.0, 177.6, 123.0, 11.2, 2.0, 1.0, 10.0])),
     (0.0,
      DenseVector([86.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 226.3, 88.0, 223.0, 107.0, 255.6, 92.0, 13.0, 3.0, 4.0, 24.29])),
     (0.0,
      DenseVector([91.0, 0.0, 0.0, 1.0, 0.0, 1.0, 37.0, 162.3, 107.0, 233.9, 115.0, 277.4, 94.0, 9.2, 4.0, 0.0, 8.2])),
     (0.0,
      DenseVector([76.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 224.4, 121.0, 147.9, 97.0, 183.8, 74.0, 6.7, 2.0, 2.0, 9.26])),
     (0.0,
      DenseVector([130.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 120.5, 127.0, 189.7, 52.0, 270.1, 107.0, 14.3, 2.0, 1.0, 25.0])),
     (0.0,
      DenseVector([56.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 91.1, 90.0, 179.3, 115.0, 300.7, 89.0, 11.9, 8.0, 2.0, 12.82])),
     (0.0,
      DenseVector([117.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 168.8, 137.0, 241.4, 107.0, 204.8, 106.0, 15.5, 4.0, 0.0, 14.75])),
     (0.0,
      DenseVector([63.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 153.5, 81.0, 287.3, 115.0, 230.2, 85.0, 6.5, 5.0, 2.0, 6.82])),
     (0.0,
      DenseVector([126.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 226.2, 88.0, 140.3, 114.0, 208.9, 110.0, 6.4, 2.0, 0.0, 10.96])),
     (0.0,
      DenseVector([132.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 191.9, 107.0, 206.9, 127.0, 272.0, 88.0, 12.6, 2.0, 1.0, 14.1])),
     (1.0,
      DenseVector([81.0, 1.0, 0.0, 0.0, 0.0, 1.0, 28.0, 167.9, 147.0, 190.7, 105.0, 193.0, 103.0, 9.2, 6.0, 4.0, 21.21])),
     (0.0,
      DenseVector([122.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 180.0, 88.0, 145.0, 77.0, 233.7, 120.0, 11.5, 6.0, 2.0, 16.18])),
     (1.0,
      DenseVector([46.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 257.4, 67.0, 261.1, 91.0, 204.4, 107.0, 13.4, 5.0, 2.0, 26.47])),
     (0.0,
      DenseVector([150.0, 1.0, 0.0, 0.0, 0.0, 1.0, 28.0, 174.4, 75.0, 169.9, 80.0, 201.6, 130.0, 11.0, 4.0, 1.0, 17.86])),
     (0.0,
      DenseVector([99.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 159.7, 83.0, 155.4, 121.0, 255.7, 114.0, 8.4, 3.0, 1.0, 12.33])),
     (0.0,
      DenseVector([87.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 237.2, 124.0, 222.6, 87.0, 173.3, 81.0, 11.0, 3.0, 1.0, 10.0])),
     (0.0,
      DenseVector([108.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 103.0, 129.0, 242.3, 103.0, 170.2, 89.0, 7.9, 3.0, 1.0, 5.77])),
     (0.0,
      DenseVector([101.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 153.8, 89.0, 234.0, 89.0, 196.3, 77.0, 11.6, 2.0, 4.0, 10.96])),
     (0.0,
      DenseVector([53.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 205.1, 86.0, 160.5, 95.0, 149.5, 142.0, 10.7, 2.0, 3.0, 17.78])),
     (0.0,
      DenseVector([132.0, 1.0, 0.0, 0.0, 0.0, 1.0, 39.0, 175.7, 93.0, 187.2, 94.0, 225.5, 118.0, 8.6, 3.0, 2.0, 5.77])),
     (0.0,
      DenseVector([158.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 155.9, 123.0, 224.2, 112.0, 221.0, 116.0, 8.6, 8.0, 2.0, 26.47])),
     (0.0,
      DenseVector([114.0, 1.0, 0.0, 0.0, 0.0, 1.0, 34.0, 154.4, 109.0, 221.4, 142.0, 208.5, 103.0, 10.3, 5.0, 0.0, 21.54])),
     (0.0,
      DenseVector([77.0, 1.0, 0.0, 0.0, 0.0, 1.0, 23.0, 209.7, 73.0, 183.6, 63.0, 205.5, 111.0, 7.1, 3.0, 2.0, 20.0])),
     (0.0,
      DenseVector([144.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 150.0, 69.0, 285.9, 73.0, 190.6, 121.0, 9.4, 15.0, 0.0, 21.21])),
     (0.0,
      DenseVector([91.0, 0.0, 0.0, 1.0, 0.0, 1.0, 23.0, 232.4, 97.0, 186.0, 88.0, 190.5, 128.0, 12.3, 3.0, 3.0, 10.0])),
     (0.0,
      DenseVector([58.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 165.4, 100.0, 115.7, 87.0, 193.8, 118.0, 12.8, 5.0, 2.0, 14.81])),
     (0.0,
      DenseVector([5.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 199.2, 106.0, 187.3, 12.0, 214.0, 85.0, 13.3, 3.0, 3.0, 20.0])),
     (0.0,
      DenseVector([97.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 217.6, 81.0, 320.5, 51.0, 150.7, 110.0, 4.2, 3.0, 0.0, 16.92])),
     (0.0,
      DenseVector([107.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 212.1, 95.0, 150.1, 88.0, 219.8, 111.0, 7.7, 2.0, 3.0, 26.47])),
     (0.0,
      DenseVector([142.0, 1.0, 0.0, 0.0, 0.0, 1.0, 30.0, 154.0, 75.0, 165.8, 97.0, 270.0, 83.0, 10.8, 5.0, 1.0, 11.11])),
     (0.0,
      DenseVector([9.0, 0.0, 1.0, 0.0, 0.0, 1.0, 31.0, 193.8, 130.0, 202.6, 98.0, 191.2, 102.0, 13.3, 2.0, 1.0, 18.07])),
     (0.0,
      DenseVector([73.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 175.4, 130.0, 248.1, 105.0, 122.4, 85.0, 12.2, 4.0, 0.0, 6.25])),
     (1.0,
      DenseVector([48.0, 0.0, 0.0, 1.0, 0.0, 1.0, 22.0, 152.0, 63.0, 258.8, 131.0, 263.2, 109.0, 15.7, 5.0, 2.0, 26.47])),
     (0.0,
      DenseVector([43.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 230.2, 147.0, 186.7, 121.0, 128.4, 100.0, 9.2, 3.0, 0.0, 9.43])),
     (1.0,
      DenseVector([122.0, 0.0, 1.0, 0.0, 0.0, 1.0, 33.0, 174.9, 103.0, 248.2, 105.0, 164.6, 116.0, 13.5, 3.0, 1.0, 9.68])),
     (0.0,
      DenseVector([93.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 190.2, 68.0, 262.2, 64.0, 130.0, 92.0, 8.8, 4.0, 0.0, 23.33])),
     (0.0,
      DenseVector([85.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 176.4, 122.0, 224.9, 123.0, 219.6, 50.0, 11.5, 1.0, 1.0, 10.96])),
     (0.0,
      DenseVector([59.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 160.9, 95.0, 251.2, 65.0, 273.4, 97.0, 5.0, 5.0, 3.0, 9.43])),
     (0.0,
      DenseVector([87.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 228.7, 90.0, 163.0, 99.0, 154.1, 90.0, 11.8, 3.0, 1.0, 7.84])),
     (0.0,
      DenseVector([137.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 144.0, 90.0, 181.6, 100.0, 128.1, 93.0, 12.3, 2.0, 0.0, 12.82])),
     (0.0,
      DenseVector([21.0, 0.0, 0.0, 1.0, 0.0, 1.0, 31.0, 135.9, 90.0, 271.0, 84.0, 179.1, 89.0, 9.5, 7.0, 6.0, 14.1])),
     (1.0,
      DenseVector([129.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 334.3, 118.0, 192.1, 104.0, 191.0, 83.0, 10.4, 6.0, 0.0, 14.75])),
     (0.0,
      DenseVector([104.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 130.5, 77.0, 131.2, 117.0, 264.7, 63.0, 13.0, 6.0, 0.0, 13.56])),
     (1.0,
      DenseVector([93.0, 0.0, 1.0, 0.0, 0.0, 1.0, 21.0, 134.2, 105.0, 162.5, 128.0, 186.6, 90.0, 11.8, 2.0, 4.0, 14.81])),
     (1.0,
      DenseVector([63.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 278.0, 102.0, 266.4, 114.0, 224.1, 118.0, 13.1, 4.0, 4.0, 10.96])),
     (0.0,
      DenseVector([161.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 105.4, 70.0, 214.8, 122.0, 223.6, 126.0, 7.8, 5.0, 0.0, 14.1])),
     (0.0,
      DenseVector([50.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 188.9, 94.0, 203.9, 104.0, 151.8, 124.0, 11.6, 8.0, 3.0, 25.0])),
     (0.0,
      DenseVector([103.0, 0.0, 1.0, 0.0, 0.0, 1.0, 24.0, 111.8, 85.0, 239.6, 102.0, 268.3, 81.0, 6.9, 4.0, 1.0, 11.11])),
     (0.0,
      DenseVector([84.0, 1.0, 0.0, 0.0, 0.0, 1.0, 33.0, 159.1, 106.0, 149.8, 101.0, 213.4, 108.0, 13.0, 18.0, 1.0, 9.68])),
     (0.0,
      DenseVector([92.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 212.4, 105.0, 224.6, 118.0, 221.3, 105.0, 9.0, 4.0, 1.0, 17.86])),
     (0.0,
      DenseVector([77.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 142.3, 112.0, 306.3, 111.0, 196.5, 82.0, 9.9, 1.0, 1.0, 21.21])),
     (1.0,
      DenseVector([64.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 346.8, 55.0, 249.5, 79.0, 275.4, 102.0, 13.3, 9.0, 1.0, 18.07])),
     (0.0,
      DenseVector([159.0, 1.0, 0.0, 0.0, 0.0, 1.0, 15.0, 113.9, 102.0, 145.3, 146.0, 195.2, 137.0, 11.8, 9.0, 1.0, 12.7])),
     (1.0,
      DenseVector([110.0, 1.0, 0.0, 0.0, 1.0, 1.0, 27.0, 267.9, 103.0, 263.3, 74.0, 178.1, 106.0, 8.3, 2.0, 1.0, 18.57])),
     (0.0,
      DenseVector([138.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 171.4, 117.0, 115.2, 128.0, 224.5, 115.0, 17.0, 4.0, 3.0, 21.21])),
     (0.0,
      DenseVector([178.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 275.4, 150.0, 187.5, 62.0, 147.1, 126.0, 13.6, 3.0, 1.0, 21.21])),
     (0.0,
      DenseVector([38.0, 1.0, 0.0, 0.0, 0.0, 1.0, 31.0, 197.2, 118.0, 249.9, 70.0, 298.9, 104.0, 3.9, 2.0, 0.0, 23.33])),
     (0.0,
      DenseVector([50.0, 1.0, 0.0, 0.0, 0.0, 1.0, 35.0, 192.6, 97.0, 135.2, 101.0, 216.2, 101.0, 7.9, 2.0, 2.0, 21.92])),
     (0.0,
      DenseVector([45.0, 0.0, 0.0, 1.0, 0.0, 1.0, 26.0, 91.7, 104.0, 150.6, 119.0, 63.3, 103.0, 7.7, 5.0, 1.0, 21.92])),
     (0.0,
      DenseVector([70.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 126.3, 99.0, 141.6, 106.0, 255.9, 96.0, 9.6, 2.0, 0.0, 9.43])),
     (0.0,
      DenseVector([147.0, 0.0, 0.0, 1.0, 0.0, 1.0, 33.0, 251.5, 107.0, 234.1, 110.0, 213.4, 87.0, 10.4, 6.0, 3.0, 18.07])),
     (0.0,
      DenseVector([94.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 190.6, 108.0, 152.3, 95.0, 144.7, 97.0, 7.5, 5.0, 1.0, 21.21])),
     (0.0,
      DenseVector([179.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 116.1, 101.0, 201.8, 99.0, 181.9, 103.0, 11.6, 5.0, 0.0, 8.62])),
     (0.0,
      DenseVector([116.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 217.3, 91.0, 216.1, 95.0, 148.1, 76.0, 11.3, 3.0, 2.0, 21.54])),
     (0.0,
      DenseVector([59.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 179.4, 80.0, 232.5, 99.0, 175.8, 105.0, 14.7, 3.0, 0.0, 9.68])),
     (0.0,
      DenseVector([165.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 207.7, 109.0, 164.8, 94.0, 54.5, 91.0, 7.9, 3.0, 0.0, 16.18])),
     ...]




```python
# Transformando o RDD em um dataframe.
dfTrain = spSession.createDataFrame(dfRDD4, ["label", "features"])
dfTrain.select("features", "label").show(5)
```

    +--------------------+-----+
    |            features|label|
    +--------------------+-----+
    |[128.0,1.0,0.0,0....|  0.0|
    |[107.0,1.0,0.0,0....|  0.0|
    |[137.0,1.0,0.0,0....|  0.0|
    |[84.0,0.0,1.0,0.0...|  0.0|
    |[75.0,1.0,0.0,0.0...|  0.0|
    +--------------------+-----+
    only showing top 5 rows
    



```python
# Gerando a redução de dimensionalidade.
dfPCA = PCA(k = 5, inputCol = "features", outputCol = "pcaFeatures")
pcaModel = dfPCA.fit(dfTrain)
pcaResult = pcaModel.transform(dfTrain).select("label", "pcaFeatures")
pcaResult.show(truncate=False)
```

    +-----+-------------------------------------------------------------------------------------------------+
    |label|pcaFeatures                                                                                      |
    +-----+-------------------------------------------------------------------------------------------------+
    |0.0  |[-283.1321954921104,-8.831255249108578,298.1603313597033,136.05989821984184,84.24310074949314]   |
    |0.0  |[-179.90959353739515,-14.573979225169166,310.1100565320442,116.58991562869525,93.87254186241942] |
    |0.0  |[-256.1565228195028,-15.739649465010036,187.29785476588427,142.7202257867833,85.37303669792381]  |
    |0.0  |[-309.2389414549588,-83.78808829484775,175.0272359666387,87.67354105518473,47.24755694718082]    |
    |0.0  |[-181.07908898716482,-8.61617233534113,228.50052375200076,82.43052298849821,82.70747068922967]   |
    |0.0  |[-241.6493532319047,35.8228556910689,283.45828147360646,125.63380018970287,66.15972569931708]    |
    |0.0  |[-242.71117877989823,129.72898675163805,371.04052684259983,130.59929571083222,58.54596375912775] |
    |0.0  |[-170.038467231184,-58.56306736126223,217.94341028230252,153.2261599574051,51.986990254209054]   |
    |0.0  |[-208.77333348801207,130.69912294622569,377.07345747628506,127.23806721387209,72.53363521381188] |
    |0.0  |[-279.9988380594672,-40.8979241392904,376.93048894084484,150.44912962659225,58.560595058396764]  |
    |1.0  |[-147.43871777054767,40.24767005029255,298.2337719950413,74.70683423563831,104.31397539852156]   |
    |0.0  |[-202.8954325294015,-3.3712644852272495,243.80345680299178,82.55731351709248,107.76266021754387] |
    |0.0  |[-140.55112786481627,-12.139590709097586,165.48787529791022,173.09041628889796,55.14664314658554]|
    |0.0  |[-175.5223920681183,65.66810263990699,295.40261154252494,102.95644679474417,55.342091471716444]  |
    |0.0  |[-142.1656406567889,106.0549520288809,343.67692078083303,71.18656722441791,45.071915290301376]   |
    |1.0  |[-354.8162997925782,136.7655511209752,304.7982480568475,167.21017022258218,33.47048049691777]    |
    |0.0  |[-213.94584162510046,155.02010210600608,234.81679761177543,92.9803760974961,118.83965057983416]  |
    |0.0  |[-206.92352935301207,81.27757574428014,226.60184940314056,100.0743741354304,82.94367131544116]   |
    |0.0  |[-205.8717007607039,55.259605769882384,251.8640960094244,81.93224285611804,35.44045597507703]    |
    |0.0  |[-238.64712463600412,-4.31484043705407,237.22301897186418,79.48054383761966,71.20692027156532]   |
    +-----+-------------------------------------------------------------------------------------------------+
    only showing top 20 rows
    


## Machine Learning

Agora que nosso dataset foi preparado vamos gerar o modelo de machine learning. Neste projeto iremos utilizar a Regressão Logistica como modelo de machine learning, conforme foi definido.

Para o cáculo do erro vamos utilizar a métrica AUC, pois ela contabiliza leva em consideração os falsos positivos, o que é importante para este caso em específico, onde o maior acerto que queremos ter é em relação aos clientes que pretendem sair da empresa, que são a minoria.


```python
# Criando a divisão do dataset entre treino e teste
train, test = pcaResult.randomSplit([0.7, 0.3], seed=12345)
print("O número de linhas de dataset de treino é de {}" .format(train.count()))
print("O número de linhas de dataset de teste é de {}" .format(test.count()))
```

    O número de linhas de dataset de treino é de 2300
    O número de linhas de dataset de teste é de 1033



```python
# Gerando o modelo.
lr = LogisticRegression(labelCol="label", featuresCol="pcaFeatures", maxIter=10)
model=lr.fit(train)
predict_train=model.transform(train)
predict_test=model.transform(test)
predict_test.select("label","prediction", "probability").show(10)
```

    +-----+----------+--------------------+
    |label|prediction|         probability|
    +-----+----------+--------------------+
    |  0.0|       0.0|[0.61255974008626...|
    |  0.0|       0.0|[0.71009560766736...|
    |  0.0|       0.0|[0.76501254613110...|
    |  0.0|       0.0|[0.72097459267070...|
    |  0.0|       0.0|[0.69129181440395...|
    |  0.0|       0.0|[0.71242503502220...|
    |  0.0|       0.0|[0.72918511129230...|
    |  0.0|       0.0|[0.75100364008177...|
    |  0.0|       0.0|[0.79871782886651...|
    |  0.0|       0.0|[0.76853190533380...|
    +-----+----------+--------------------+
    only showing top 10 rows
    



```python
# Medindo o erro do modelo utilizando a métrica AUC.
evaluator=BinaryClassificationEvaluator(rawPredictionCol="rawPrediction",labelCol="label")
predict_test.select("label","rawPrediction","prediction","probability").show(5)

print("The area under ROC for train set is {}".format(evaluator.evaluate(predict_train)))
print("The area under ROC for test set is {}".format(evaluator.evaluate(predict_test)))
```

    +-----+--------------------+----------+--------------------+
    |label|       rawPrediction|prediction|         probability|
    +-----+--------------------+----------+--------------------+
    |  0.0|[0.45808480355365...|       0.0|[0.61255974008626...|
    |  0.0|[0.89584843264415...|       0.0|[0.71009560766736...|
    |  0.0|[1.18036010893639...|       0.0|[0.76501254613110...|
    |  0.0|[0.94930105427821...|       0.0|[0.72097459267070...|
    |  0.0|[0.80616559450467...|       0.0|[0.69129181440395...|
    +-----+--------------------+----------+--------------------+
    only showing top 5 rows
    
    The area under ROC for train set is 0.6362159667743437
    The area under ROC for test set is 0.6689691027926317


Em nosso primeiro teste conseguimos um resultado bom, mas vamos tentar melhorá-lo. Para isso iremos balancear nosso dataset, pois o mesmo possui muitos valores negativos em comparação aos positivos e isso pode causar um overfiting no modelo. Criando os falsos positivos.


```python
dataset_size=float(pcaResult.select("label").count())
numPositives=train.select("label").where('label == 1').count()
per_ones=(float(numPositives)/float(dataset_size))*100
numNegatives=float(dataset_size-numPositives)
print('O número de positivos são {}'.format(numPositives))
print('A porcentagem de positivos são {}'.format(per_ones))
```

    O número de positivos são 330
    A porcentagem de positivos são 9.900990099009901


Como podemos perceber a taxa de números positivos é muito baixa, apenas 10% praticamente. Para nos ajudar, a técnica de Regressão Logística do spark tem uma função que balanceia de forma fácil o nosso dataset. Para isso precisamos apenas calcular o Balancing Ratio, ou taxa de balanceamento e de posivos e negativos e gerar uma coluna no dataset com esses valores.


```python
# Calculando o Balancing Ratio
BalancingRatio= numNegatives/dataset_size
print('BalancingRatio = {}'.format(BalancingRatio))
```

    BalancingRatio = 0.900990099009901



```python
# Criando a nova coluna
train=train.withColumn("classWeights", F.when(train.label == 1,BalancingRatio).otherwise(1-BalancingRatio))
train.select("classWeights").show(5)
```

    +-------------------+
    |       classWeights|
    +-------------------+
    |0.09900990099009899|
    |0.09900990099009899|
    |0.09900990099009899|
    |0.09900990099009899|
    |0.09900990099009899|
    +-------------------+
    only showing top 5 rows
    


Agora que nossa nova coluna foi criada, vamos gerar o mesmo modelo anterior, mas agora com a variável "weightCol", que fará o programa calcular de acordo com esse peso.


```python
# Gerando o modelo
lr = LogisticRegression(labelCol="label", featuresCol="pcaFeatures", weightCol="classWeights", maxIter=10)
model_balanced=lr.fit(train)
predict_train_balanced=model_balanced.transform(train)
predict_test_balanced=model_balanced.transform(test)
predict_test_balanced.select("label","prediction", "probability").show(10)
```

    +-----+----------+--------------------+
    |label|prediction|         probability|
    +-----+----------+--------------------+
    |  0.0|       1.0|[0.20316399920686...|
    |  0.0|       1.0|[0.27835282170878...|
    |  0.0|       1.0|[0.31659705545245...|
    |  0.0|       1.0|[0.26522074687444...|
    |  0.0|       1.0|[0.24725462541386...|
    |  0.0|       1.0|[0.26488013464038...|
    |  0.0|       1.0|[0.27205978985035...|
    |  0.0|       1.0|[0.28025861318636...|
    |  0.0|       1.0|[0.32413841533652...|
    |  0.0|       1.0|[0.32700270595578...|
    +-----+----------+--------------------+
    only showing top 10 rows
    



```python
# Calculando o erro.
evaluator=BinaryClassificationEvaluator(rawPredictionCol="rawPrediction",labelCol="label")
predict_test_balanced.select("label","rawPrediction","prediction","probability").show(5)

print("The area under ROC for train balanced set is {}".format(evaluator.evaluate(predict_train_balanced)))
print("The area under ROC for test balanced set is {}".format(evaluator.evaluate(predict_test_balanced)))
```

    +-----+--------------------+----------+--------------------+
    |label|       rawPrediction|prediction|         probability|
    +-----+--------------------+----------+--------------------+
    |  0.0|[-1.3666353562175...|       1.0|[0.20316399920686...|
    |  0.0|[-0.9526468948795...|       1.0|[0.27835282170878...|
    |  0.0|[-0.7694548014710...|       1.0|[0.31659705545245...|
    |  0.0|[-1.0190076319176...|       1.0|[0.26522074687444...|
    |  0.0|[-1.1133083448899...|       1.0|[0.24725462541386...|
    +-----+--------------------+----------+--------------------+
    only showing top 5 rows
    
    The area under ROC for train balanced set is 0.6369481618212585
    The area under ROC for test balanced set is 0.668768568033274


Com o balanceamento não houve uma melhora significativa no algoritmo, mas apesar do resultado não ser tão satisfatório, ja percebemos as mudanças na previsão, com mais valores positivos.

# Gerando a previsão com o arquvio de teste.

Agora que temos um modelo vamos carregar o arquivo de teste, fazer as modificações necessárias e rodas o modelo preditivo.


```python
# Carregando o dataset
df_teste = pd.read_csv("projeto4_telecom_teste.csv")
# Visualizando as 5 primeras linhas do dataset.
df_teste.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Unnamed: 0</th>
      <th>state</th>
      <th>account_length</th>
      <th>area_code</th>
      <th>international_plan</th>
      <th>voice_mail_plan</th>
      <th>number_vmail_messages</th>
      <th>total_day_minutes</th>
      <th>total_day_calls</th>
      <th>total_day_charge</th>
      <th>total_eve_minutes</th>
      <th>total_eve_calls</th>
      <th>total_eve_charge</th>
      <th>total_night_minutes</th>
      <th>total_night_calls</th>
      <th>total_night_charge</th>
      <th>total_intl_minutes</th>
      <th>total_intl_calls</th>
      <th>total_intl_charge</th>
      <th>number_customer_service_calls</th>
      <th>churn</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>HI</td>
      <td>101</td>
      <td>area_code_510</td>
      <td>no</td>
      <td>no</td>
      <td>0</td>
      <td>70.9</td>
      <td>123</td>
      <td>12.05</td>
      <td>211.9</td>
      <td>73</td>
      <td>18.01</td>
      <td>236.0</td>
      <td>73</td>
      <td>10.62</td>
      <td>10.6</td>
      <td>3</td>
      <td>2.86</td>
      <td>3</td>
      <td>no</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>MT</td>
      <td>137</td>
      <td>area_code_510</td>
      <td>no</td>
      <td>no</td>
      <td>0</td>
      <td>223.6</td>
      <td>86</td>
      <td>38.01</td>
      <td>244.8</td>
      <td>139</td>
      <td>20.81</td>
      <td>94.2</td>
      <td>81</td>
      <td>4.24</td>
      <td>9.5</td>
      <td>7</td>
      <td>2.57</td>
      <td>0</td>
      <td>no</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>OH</td>
      <td>103</td>
      <td>area_code_408</td>
      <td>no</td>
      <td>yes</td>
      <td>29</td>
      <td>294.7</td>
      <td>95</td>
      <td>50.10</td>
      <td>237.3</td>
      <td>105</td>
      <td>20.17</td>
      <td>300.3</td>
      <td>127</td>
      <td>13.51</td>
      <td>13.7</td>
      <td>6</td>
      <td>3.70</td>
      <td>1</td>
      <td>no</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>NM</td>
      <td>99</td>
      <td>area_code_415</td>
      <td>no</td>
      <td>no</td>
      <td>0</td>
      <td>216.8</td>
      <td>123</td>
      <td>36.86</td>
      <td>126.4</td>
      <td>88</td>
      <td>10.74</td>
      <td>220.6</td>
      <td>82</td>
      <td>9.93</td>
      <td>15.7</td>
      <td>2</td>
      <td>4.24</td>
      <td>1</td>
      <td>no</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>SC</td>
      <td>108</td>
      <td>area_code_415</td>
      <td>no</td>
      <td>no</td>
      <td>0</td>
      <td>197.4</td>
      <td>78</td>
      <td>33.56</td>
      <td>124.0</td>
      <td>101</td>
      <td>10.54</td>
      <td>204.5</td>
      <td>107</td>
      <td>9.20</td>
      <td>7.7</td>
      <td>4</td>
      <td>2.08</td>
      <td>2</td>
      <td>no</td>
    </tr>
  </tbody>
</table>
</div>




```python
df_teste.drop("Unnamed: 0", axis=1, inplace=True)
df_teste['Perct_state'] = round(df_teste['state'].map(states),2)
df_teste.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>state</th>
      <th>account_length</th>
      <th>area_code</th>
      <th>international_plan</th>
      <th>voice_mail_plan</th>
      <th>number_vmail_messages</th>
      <th>total_day_minutes</th>
      <th>total_day_calls</th>
      <th>total_day_charge</th>
      <th>total_eve_minutes</th>
      <th>total_eve_calls</th>
      <th>total_eve_charge</th>
      <th>total_night_minutes</th>
      <th>total_night_calls</th>
      <th>total_night_charge</th>
      <th>total_intl_minutes</th>
      <th>total_intl_calls</th>
      <th>total_intl_charge</th>
      <th>number_customer_service_calls</th>
      <th>churn</th>
      <th>Perct_state</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>HI</td>
      <td>101</td>
      <td>area_code_510</td>
      <td>no</td>
      <td>no</td>
      <td>0</td>
      <td>70.9</td>
      <td>123</td>
      <td>12.05</td>
      <td>211.9</td>
      <td>73</td>
      <td>18.01</td>
      <td>236.0</td>
      <td>73</td>
      <td>10.62</td>
      <td>10.6</td>
      <td>3</td>
      <td>2.86</td>
      <td>3</td>
      <td>no</td>
      <td>5.66</td>
    </tr>
    <tr>
      <th>1</th>
      <td>MT</td>
      <td>137</td>
      <td>area_code_510</td>
      <td>no</td>
      <td>no</td>
      <td>0</td>
      <td>223.6</td>
      <td>86</td>
      <td>38.01</td>
      <td>244.8</td>
      <td>139</td>
      <td>20.81</td>
      <td>94.2</td>
      <td>81</td>
      <td>4.24</td>
      <td>9.5</td>
      <td>7</td>
      <td>2.57</td>
      <td>0</td>
      <td>no</td>
      <td>20.59</td>
    </tr>
    <tr>
      <th>2</th>
      <td>OH</td>
      <td>103</td>
      <td>area_code_408</td>
      <td>no</td>
      <td>yes</td>
      <td>29</td>
      <td>294.7</td>
      <td>95</td>
      <td>50.10</td>
      <td>237.3</td>
      <td>105</td>
      <td>20.17</td>
      <td>300.3</td>
      <td>127</td>
      <td>13.51</td>
      <td>13.7</td>
      <td>6</td>
      <td>3.70</td>
      <td>1</td>
      <td>no</td>
      <td>12.82</td>
    </tr>
    <tr>
      <th>3</th>
      <td>NM</td>
      <td>99</td>
      <td>area_code_415</td>
      <td>no</td>
      <td>no</td>
      <td>0</td>
      <td>216.8</td>
      <td>123</td>
      <td>36.86</td>
      <td>126.4</td>
      <td>88</td>
      <td>10.74</td>
      <td>220.6</td>
      <td>82</td>
      <td>9.93</td>
      <td>15.7</td>
      <td>2</td>
      <td>4.24</td>
      <td>1</td>
      <td>no</td>
      <td>9.68</td>
    </tr>
    <tr>
      <th>4</th>
      <td>SC</td>
      <td>108</td>
      <td>area_code_415</td>
      <td>no</td>
      <td>no</td>
      <td>0</td>
      <td>197.4</td>
      <td>78</td>
      <td>33.56</td>
      <td>124.0</td>
      <td>101</td>
      <td>10.54</td>
      <td>204.5</td>
      <td>107</td>
      <td>9.20</td>
      <td>7.7</td>
      <td>4</td>
      <td>2.08</td>
      <td>2</td>
      <td>no</td>
      <td>23.33</td>
    </tr>
  </tbody>
</table>
</div>




```python
df_teste.to_csv('dfTest.csv', header=True, index=False)
```


```python
# Lendo o arquivo em formato RDD
df_teste_RDD = sc.textFile('dfTest.csv')
```


```python
df_teste_RDD.count()
```




    1668




```python
# Lendo as primeiras 5 linhas do dataset
df_teste_RDD.take(5)
```




    ['state,account_length,area_code,international_plan,voice_mail_plan,number_vmail_messages,total_day_minutes,total_day_calls,total_day_charge,total_eve_minutes,total_eve_calls,total_eve_charge,total_night_minutes,total_night_calls,total_night_charge,total_intl_minutes,total_intl_calls,total_intl_charge,number_customer_service_calls,churn,Perct_state',
     'HI,101,area_code_510,no,no,0,70.9,123,12.05,211.9,73,18.01,236.0,73,10.62,10.6,3,2.86,3,no,5.66',
     'MT,137,area_code_510,no,no,0,223.6,86,38.01,244.8,139,20.81,94.2,81,4.24,9.5,7,2.57,0,no,20.59',
     'OH,103,area_code_408,no,yes,29,294.7,95,50.1,237.3,105,20.17,300.3,127,13.51,13.7,6,3.7,1,no,12.82',
     'NM,99,area_code_415,no,no,0,216.8,123,36.86,126.4,88,10.74,220.6,82,9.93,15.7,2,4.24,1,no,9.68']




```python
# Removendo o cabeçalho do arquivo
firstLine = df_teste_RDD.first()
df_teste_RDD2 = df_teste_RDD.filter(lambda x: x != firstLine)
df_teste_RDD2.count()
```




    1667




```python
# Lendo a função TransformaDados em um novo RDD
df_teste_RDD3 = df_teste_RDD2.map(TransformaDados)
df_teste_RDD3.collect()[:5]
```




    [Row(Perct_state='5.66', account_length=101.0, area_code_408=0.0, area_code_415=0.0, area_code_510=1.0, churn=0.0, international_plan=0.0, number_customer_service_calls=3.0, number_vmail_messages=0.0, state='HI', total_day_calls=123.0, total_day_charge='12.05', total_day_minutes='70.9', total_eve_calls=73.0, total_eve_charge='18.01', total_eve_minutes='211.9', total_intl_calls=3.0, total_intl_charge='2.86', total_intl_minutes='10.6', total_night_calls=73.0, total_night_charge='10.62', total_night_minutes='236.0', voice_mail_plan=0.0),
     Row(Perct_state='20.59', account_length=137.0, area_code_408=0.0, area_code_415=0.0, area_code_510=1.0, churn=0.0, international_plan=0.0, number_customer_service_calls=0.0, number_vmail_messages=0.0, state='MT', total_day_calls=86.0, total_day_charge='38.01', total_day_minutes='223.6', total_eve_calls=139.0, total_eve_charge='20.81', total_eve_minutes='244.8', total_intl_calls=7.0, total_intl_charge='2.57', total_intl_minutes='9.5', total_night_calls=81.0, total_night_charge='4.24', total_night_minutes='94.2', voice_mail_plan=0.0),
     Row(Perct_state='12.82', account_length=103.0, area_code_408=1.0, area_code_415=0.0, area_code_510=0.0, churn=0.0, international_plan=0.0, number_customer_service_calls=1.0, number_vmail_messages=29.0, state='OH', total_day_calls=95.0, total_day_charge='50.1', total_day_minutes='294.7', total_eve_calls=105.0, total_eve_charge='20.17', total_eve_minutes='237.3', total_intl_calls=6.0, total_intl_charge='3.7', total_intl_minutes='13.7', total_night_calls=127.0, total_night_charge='13.51', total_night_minutes='300.3', voice_mail_plan=1.0),
     Row(Perct_state='9.68', account_length=99.0, area_code_408=0.0, area_code_415=1.0, area_code_510=0.0, churn=0.0, international_plan=0.0, number_customer_service_calls=1.0, number_vmail_messages=0.0, state='NM', total_day_calls=123.0, total_day_charge='36.86', total_day_minutes='216.8', total_eve_calls=88.0, total_eve_charge='10.74', total_eve_minutes='126.4', total_intl_calls=2.0, total_intl_charge='4.24', total_intl_minutes='15.7', total_night_calls=82.0, total_night_charge='9.93', total_night_minutes='220.6', voice_mail_plan=0.0),
     Row(Perct_state='23.33', account_length=108.0, area_code_408=0.0, area_code_415=1.0, area_code_510=0.0, churn=0.0, international_plan=0.0, number_customer_service_calls=2.0, number_vmail_messages=0.0, state='SC', total_day_calls=78.0, total_day_charge='33.56', total_day_minutes='197.4', total_eve_calls=101.0, total_eve_charge='10.54', total_eve_minutes='124.0', total_intl_calls=4.0, total_intl_charge='2.08', total_intl_minutes='7.7', total_night_calls=107.0, total_night_charge='9.2', total_night_minutes='204.5', voice_mail_plan=0.0)]




```python
df_teste_RDD4 = df_teste_RDD3.map(transformVar)
df_teste_RDD4.collect()
```




    [(0.0,
      DenseVector([101.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 70.9, 123.0, 211.9, 73.0, 236.0, 73.0, 10.6, 3.0, 3.0, 5.66])),
     (0.0,
      DenseVector([137.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 223.6, 86.0, 244.8, 139.0, 94.2, 81.0, 9.5, 7.0, 0.0, 20.59])),
     (0.0,
      DenseVector([103.0, 0.0, 1.0, 0.0, 0.0, 1.0, 29.0, 294.7, 95.0, 237.3, 105.0, 300.3, 127.0, 13.7, 6.0, 1.0, 12.82])),
     (0.0,
      DenseVector([99.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 216.8, 123.0, 126.4, 88.0, 220.6, 82.0, 15.7, 2.0, 1.0, 9.68])),
     (0.0,
      DenseVector([108.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 197.4, 78.0, 124.0, 101.0, 204.5, 107.0, 7.7, 4.0, 2.0, 23.33])),
     (0.0,
      DenseVector([117.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 226.5, 85.0, 141.6, 68.0, 223.0, 90.0, 6.9, 5.0, 1.0, 6.82])),
     (0.0,
      DenseVector([63.0, 1.0, 0.0, 0.0, 0.0, 1.0, 32.0, 218.9, 124.0, 214.3, 125.0, 260.3, 120.0, 12.9, 3.0, 1.0, 9.68])),
     (0.0,
      DenseVector([94.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 157.5, 97.0, 224.5, 112.0, 310.8, 106.0, 11.1, 6.0, 0.0, 7.84])),
     (0.0,
      DenseVector([138.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 89.1, 117.0, 126.8, 46.0, 190.5, 71.0, 9.9, 4.0, 2.0, 11.11])),
     (0.0,
      DenseVector([128.0, 1.0, 0.0, 0.0, 0.0, 1.0, 43.0, 177.8, 100.0, 147.3, 89.0, 194.2, 92.0, 11.9, 1.0, 0.0, 25.0])),
     (0.0,
      DenseVector([113.0, 0.0, 0.0, 1.0, 0.0, 1.0, 39.0, 209.8, 77.0, 164.1, 90.0, 159.7, 100.0, 9.0, 4.0, 1.0, 20.0])),
     (0.0,
      DenseVector([140.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 93.2, 109.0, 197.6, 116.0, 219.8, 94.0, 10.5, 2.0, 1.0, 25.0])),
     (0.0,
      DenseVector([102.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 228.1, 86.0, 156.0, 97.0, 227.9, 124.0, 10.6, 9.0, 1.0, 20.97])),
     (0.0,
      DenseVector([108.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 112.6, 86.0, 114.9, 101.0, 177.8, 119.0, 7.2, 6.0, 3.0, 9.68])),
     (0.0,
      DenseVector([60.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 207.3, 77.0, 207.9, 105.0, 108.2, 89.0, 12.9, 5.0, 1.0, 14.75])),
     (0.0,
      DenseVector([96.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 208.1, 93.0, 189.2, 107.0, 279.6, 90.0, 7.4, 2.0, 1.0, 17.86])),
     (0.0,
      DenseVector([178.0, 1.0, 0.0, 0.0, 0.0, 1.0, 22.0, 112.8, 66.0, 232.6, 100.0, 194.8, 119.0, 14.3, 3.0, 1.0, 18.57])),
     (0.0,
      DenseVector([75.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 225.3, 124.0, 228.0, 81.0, 254.3, 106.0, 11.7, 3.0, 1.0, 17.86])),
     (0.0,
      DenseVector([106.0, 1.0, 0.0, 0.0, 0.0, 1.0, 25.0, 169.4, 105.0, 240.5, 108.0, 159.4, 114.0, 13.9, 5.0, 4.0, 16.18])),
     (0.0,
      DenseVector([158.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 193.3, 121.0, 208.1, 97.0, 228.1, 99.0, 7.1, 9.0, 1.0, 5.66])),
     (0.0,
      DenseVector([111.0, 1.0, 0.0, 0.0, 0.0, 1.0, 35.0, 161.2, 142.0, 159.1, 104.0, 167.9, 98.0, 14.7, 5.0, 1.0, 21.21])),
     (0.0,
      DenseVector([102.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 95.6, 88.0, 167.6, 106.0, 177.3, 95.0, 9.8, 2.0, 3.0, 13.64])),
     (0.0,
      DenseVector([92.0, 0.0, 0.0, 1.0, 0.0, 1.0, 25.0, 79.8, 99.0, 313.6, 120.0, 135.5, 104.0, 9.3, 8.0, 2.0, 9.43])),
     (0.0,
      DenseVector([42.0, 1.0, 0.0, 0.0, 0.0, 1.0, 31.0, 170.8, 101.0, 233.4, 104.0, 174.2, 121.0, 11.0, 3.0, 2.0, 14.75])),
     (0.0,
      DenseVector([69.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 229.2, 111.0, 165.3, 104.0, 235.1, 80.0, 5.2, 5.0, 1.0, 12.82])),
     (0.0,
      DenseVector([117.0, 1.0, 0.0, 0.0, 0.0, 1.0, 38.0, 259.3, 94.0, 245.6, 71.0, 269.3, 125.0, 9.2, 1.0, 3.0, 14.1])),
     (0.0,
      DenseVector([76.0, 1.0, 0.0, 0.0, 0.0, 1.0, 41.0, 212.6, 110.0, 172.7, 97.0, 186.3, 78.0, 10.1, 5.0, 0.0, 8.2])),
     (0.0,
      DenseVector([72.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 101.0, 110.0, 240.0, 70.0, 333.5, 105.0, 11.1, 6.0, 0.0, 12.33])),
     (0.0,
      DenseVector([115.0, 1.0, 0.0, 0.0, 1.0, 1.0, 6.0, 140.1, 125.0, 157.9, 100.0, 249.4, 96.0, 10.1, 3.0, 1.0, 11.69])),
     (0.0,
      DenseVector([68.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 150.4, 106.0, 199.0, 131.0, 109.7, 103.0, 12.3, 3.0, 3.0, 24.29])),
     (0.0,
      DenseVector([60.0, 0.0, 0.0, 1.0, 1.0, 1.0, 37.0, 189.9, 125.0, 110.4, 107.0, 210.7, 115.0, 9.1, 5.0, 3.0, 11.69])),
     (0.0,
      DenseVector([97.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 100.8, 74.0, 233.8, 88.0, 208.7, 79.0, 12.6, 3.0, 2.0, 6.25])),
     (0.0,
      DenseVector([92.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 138.1, 131.0, 175.7, 128.0, 192.7, 119.0, 10.0, 3.0, 3.0, 6.82])),
     (0.0,
      DenseVector([178.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 179.7, 112.0, 276.6, 91.0, 169.7, 101.0, 10.1, 7.0, 3.0, 10.96])),
     (0.0,
      DenseVector([90.0, 1.0, 0.0, 0.0, 0.0, 1.0, 30.0, 191.3, 104.0, 257.1, 109.0, 177.7, 89.0, 11.0, 1.0, 1.0, 18.57])),
     (0.0,
      DenseVector([72.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 168.3, 76.0, 212.4, 126.0, 200.2, 132.0, 12.0, 3.0, 2.0, 21.54])),
     (1.0,
      DenseVector([73.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 137.6, 113.0, 179.7, 75.0, 172.8, 71.0, 10.7, 2.0, 1.0, 26.47])),
     (0.0,
      DenseVector([54.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 255.4, 122.0, 22.3, 87.0, 152.6, 72.0, 11.1, 6.0, 0.0, 12.82])),
     (0.0,
      DenseVector([161.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 166.2, 125.0, 279.4, 78.0, 181.0, 85.0, 13.4, 6.0, 1.0, 8.2])),
     (1.0,
      DenseVector([125.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 158.7, 112.0, 155.7, 126.0, 246.0, 110.0, 8.3, 1.0, 2.0, 26.47])),
     (1.0,
      DenseVector([194.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 258.3, 76.0, 306.0, 88.0, 241.8, 106.0, 13.6, 4.0, 1.0, 14.1])),
     (0.0,
      DenseVector([69.0, 0.0, 0.0, 1.0, 0.0, 1.0, 40.0, 77.0, 106.0, 161.7, 94.0, 188.6, 82.0, 12.5, 6.0, 1.0, 20.59])),
     (0.0,
      DenseVector([141.0, 1.0, 0.0, 0.0, 0.0, 1.0, 33.0, 168.4, 127.0, 185.1, 106.0, 220.0, 78.0, 11.0, 4.0, 2.0, 8.97])),
     (1.0,
      DenseVector([98.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 307.2, 65.0, 138.6, 97.0, 381.6, 99.0, 10.2, 4.0, 2.0, 13.33])),
     (0.0,
      DenseVector([110.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 175.6, 91.0, 236.5, 127.0, 194.9, 114.0, 9.5, 8.0, 0.0, 12.82])),
     (0.0,
      DenseVector([70.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 169.1, 48.0, 219.8, 62.0, 193.5, 105.0, 10.7, 8.0, 3.0, 20.97])),
     (0.0,
      DenseVector([72.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 190.7, 111.0, 241.6, 103.0, 278.1, 95.0, 4.4, 3.0, 1.0, 9.68])),
     (0.0,
      DenseVector([66.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 239.6, 120.0, 230.3, 110.0, 153.0, 82.0, 8.6, 3.0, 1.0, 9.43])),
     (0.0,
      DenseVector([79.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 121.3, 112.0, 282.8, 107.0, 123.1, 83.0, 7.4, 4.0, 1.0, 17.86])),
     (1.0,
      DenseVector([61.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 208.4, 102.0, 326.1, 96.0, 201.9, 73.0, 10.1, 8.0, 1.0, 21.92])),
     (0.0,
      DenseVector([97.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 230.6, 131.0, 261.3, 136.0, 157.1, 96.0, 6.8, 4.0, 0.0, 13.64])),
     (0.0,
      DenseVector([63.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 112.5, 95.0, 211.2, 67.0, 227.5, 96.0, 11.1, 8.0, 0.0, 5.66])),
     (0.0,
      DenseVector([77.0, 0.0, 1.0, 0.0, 0.0, 1.0, 36.0, 172.2, 88.0, 161.6, 112.0, 259.7, 115.0, 11.8, 4.0, 2.0, 10.96])),
     (0.0,
      DenseVector([73.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 195.3, 97.0, 96.8, 89.0, 243.1, 84.0, 16.2, 13.0, 1.0, 25.0])),
     (0.0,
      DenseVector([105.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 167.7, 93.0, 234.1, 103.0, 169.7, 121.0, 6.9, 5.0, 3.0, 10.96])),
     (0.0,
      DenseVector([159.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 180.1, 103.0, 193.4, 148.0, 251.2, 109.0, 13.4, 5.0, 0.0, 14.1])),
     (0.0,
      DenseVector([83.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 129.6, 81.0, 189.7, 117.0, 219.0, 118.0, 9.8, 4.0, 2.0, 9.68])),
     (0.0,
      DenseVector([93.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 215.5, 108.0, 230.9, 70.0, 144.1, 134.0, 13.2, 4.0, 0.0, 13.33])),
     (0.0,
      DenseVector([106.0, 0.0, 1.0, 0.0, 0.0, 1.0, 22.0, 218.6, 83.0, 244.4, 70.0, 132.4, 99.0, 9.1, 6.0, 1.0, 24.29])),
     (0.0,
      DenseVector([98.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 211.9, 65.0, 224.6, 95.0, 233.0, 81.0, 9.9, 3.0, 2.0, 13.89])),
     (0.0,
      DenseVector([163.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 82.0, 95.0, 125.2, 103.0, 171.3, 107.0, 8.0, 4.0, 1.0, 26.47])),
     (0.0,
      DenseVector([169.0, 0.0, 1.0, 0.0, 0.0, 1.0, 22.0, 149.1, 103.0, 207.2, 91.0, 265.4, 92.0, 11.4, 7.0, 2.0, 16.92])),
     (1.0,
      DenseVector([111.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 304.6, 56.0, 157.6, 101.0, 188.2, 116.0, 12.4, 1.0, 1.0, 21.21])),
     (0.0,
      DenseVector([163.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 277.7, 118.0, 115.3, 91.0, 170.3, 90.0, 10.4, 8.0, 0.0, 9.43])),
     (0.0,
      DenseVector([57.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 155.2, 99.0, 214.1, 98.0, 241.0, 64.0, 7.5, 7.0, 1.0, 20.97])),
     (0.0,
      DenseVector([117.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 198.5, 75.0, 179.8, 92.0, 234.1, 119.0, 10.6, 6.0, 0.0, 10.0])),
     (0.0,
      DenseVector([85.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 209.1, 57.0, 110.7, 111.0, 113.3, 113.0, 12.8, 4.0, 1.0, 7.84])),
     (0.0,
      DenseVector([132.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 200.0, 104.0, 149.6, 101.0, 137.1, 110.0, 18.9, 2.0, 1.0, 10.0])),
     (0.0,
      DenseVector([190.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 196.1, 95.0, 228.3, 88.0, 243.6, 95.0, 7.6, 3.0, 3.0, 12.7])),
     (0.0,
      DenseVector([39.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 121.6, 99.0, 267.6, 99.0, 99.7, 88.0, 7.1, 2.0, 1.0, 12.7])),
     (0.0,
      DenseVector([84.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 204.1, 132.0, 164.4, 117.0, 165.1, 123.0, 0.0, 0.0, 1.0, 23.33])),
     (0.0,
      DenseVector([71.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 212.1, 114.0, 235.9, 91.0, 207.7, 135.0, 11.3, 5.0, 4.0, 12.33])),
     (0.0,
      DenseVector([114.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 178.3, 86.0, 176.5, 99.0, 187.1, 83.0, 13.8, 2.0, 1.0, 11.69])),
     (0.0,
      DenseVector([135.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 181.8, 92.0, 159.6, 63.0, 232.4, 78.0, 7.0, 6.0, 0.0, 8.2])),
     (0.0,
      DenseVector([93.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 120.6, 104.0, 205.5, 95.0, 182.5, 107.0, 9.6, 6.0, 2.0, 13.64])),
     (0.0,
      DenseVector([82.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 172.4, 112.0, 131.7, 89.0, 182.8, 78.0, 8.8, 9.0, 1.0, 6.49])),
     (1.0,
      DenseVector([64.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 160.6, 110.0, 171.9, 97.0, 105.2, 56.0, 7.3, 2.0, 0.0, 9.43])),
     (0.0,
      DenseVector([76.0, 0.0, 1.0, 0.0, 0.0, 1.0, 12.0, 98.4, 100.0, 187.5, 119.0, 177.0, 68.0, 15.1, 3.0, 1.0, 6.25])),
     (0.0,
      DenseVector([112.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 109.0, 126.0, 212.7, 106.0, 208.3, 92.0, 7.9, 3.0, 1.0, 18.57])),
     (0.0,
      DenseVector([113.0, 0.0, 1.0, 0.0, 1.0, 0.0, 0.0, 122.4, 98.0, 223.8, 116.0, 267.5, 148.0, 11.6, 5.0, 3.0, 26.47])),
     (1.0,
      DenseVector([137.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 144.5, 119.0, 167.3, 89.0, 175.1, 76.0, 6.1, 4.0, 5.0, 13.33])),
     (0.0,
      DenseVector([128.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 102.5, 87.0, 177.2, 97.0, 226.0, 119.0, 8.7, 5.0, 1.0, 9.68])),
     (0.0,
      DenseVector([84.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 274.1, 119.0, 144.1, 123.0, 268.2, 100.0, 9.9, 5.0, 2.0, 23.33])),
     (0.0,
      DenseVector([140.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 193.1, 82.0, 260.3, 107.0, 241.3, 93.0, 13.3, 5.0, 2.0, 12.33])),
     (0.0,
      DenseVector([106.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 182.8, 96.0, 193.7, 93.0, 219.5, 113.0, 7.7, 2.0, 2.0, 25.0])),
     (0.0,
      DenseVector([166.0, 0.0, 0.0, 1.0, 0.0, 1.0, 32.0, 148.4, 86.0, 170.6, 119.0, 201.5, 124.0, 11.3, 12.0, 1.0, 13.33])),
     (0.0,
      DenseVector([137.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 75.8, 107.0, 256.9, 98.0, 204.4, 118.0, 10.5, 3.0, 2.0, 9.43])),
     (0.0,
      DenseVector([161.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 198.0, 90.0, 186.4, 102.0, 219.2, 90.0, 9.3, 2.0, 1.0, 9.43])),
     (1.0,
      DenseVector([120.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 253.2, 95.0, 210.8, 86.0, 253.9, 81.0, 9.5, 4.0, 5.0, 14.75])),
     (0.0,
      DenseVector([187.0, 0.0, 0.0, 1.0, 0.0, 1.0, 25.0, 199.9, 64.0, 134.6, 110.0, 210.4, 78.0, 11.7, 6.0, 3.0, 9.43])),
     (0.0,
      DenseVector([102.0, 1.0, 0.0, 0.0, 0.0, 1.0, 25.0, 187.5, 105.0, 272.3, 88.0, 216.4, 91.0, 11.7, 2.0, 2.0, 14.1])),
     (0.0,
      DenseVector([82.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 183.6, 111.0, 303.5, 88.0, 189.8, 115.0, 10.3, 6.0, 0.0, 6.82])),
     (0.0,
      DenseVector([100.0, 0.0, 0.0, 1.0, 0.0, 1.0, 21.0, 86.1, 95.0, 189.9, 126.0, 201.3, 113.0, 11.7, 4.0, 0.0, 21.92])),
     (0.0,
      DenseVector([45.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 126.8, 83.0, 157.8, 100.0, 158.3, 97.0, 11.0, 4.0, 3.0, 20.0])),
     (0.0,
      DenseVector([114.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 108.3, 95.0, 168.3, 104.0, 153.1, 109.0, 9.1, 2.0, 1.0, 9.43])),
     (1.0,
      DenseVector([43.0, 0.0, 1.0, 0.0, 1.0, 0.0, 0.0, 160.5, 92.0, 235.8, 121.0, 111.8, 95.0, 13.8, 5.0, 1.0, 9.43])),
     (0.0,
      DenseVector([103.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 147.3, 81.0, 158.4, 88.0, 193.8, 103.0, 11.0, 3.0, 2.0, 8.62])),
     (0.0,
      DenseVector([57.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 165.1, 91.0, 240.8, 56.0, 277.0, 100.0, 9.5, 3.0, 2.0, 14.75])),
     (0.0,
      DenseVector([52.0, 0.0, 1.0, 0.0, 0.0, 1.0, 31.0, 122.2, 88.0, 139.0, 100.0, 141.9, 141.0, 5.7, 10.0, 3.0, 16.18])),
     (0.0,
      DenseVector([74.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 156.3, 102.0, 273.1, 118.0, 215.8, 113.0, 9.0, 4.0, 3.0, 6.82])),
     (0.0,
      DenseVector([74.0, 0.0, 1.0, 0.0, 0.0, 1.0, 46.0, 277.7, 109.0, 270.0, 114.0, 165.3, 82.0, 10.4, 2.0, 1.0, 10.0])),
     (0.0,
      DenseVector([40.0, 1.0, 0.0, 0.0, 1.0, 1.0, 21.0, 147.4, 117.0, 262.8, 110.0, 202.0, 97.0, 10.9, 5.0, 0.0, 5.66])),
     (0.0,
      DenseVector([83.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 228.4, 113.0, 163.3, 103.0, 248.0, 114.0, 7.8, 6.0, 1.0, 17.78])),
     (0.0,
      DenseVector([102.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 184.6, 133.0, 166.8, 94.0, 225.2, 105.0, 6.8, 8.0, 1.0, 9.23])),
     (0.0,
      DenseVector([92.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 87.6, 89.0, 172.3, 133.0, 153.4, 88.0, 8.0, 4.0, 1.0, 7.84])),
     (0.0,
      DenseVector([79.0, 0.0, 1.0, 0.0, 0.0, 1.0, 25.0, 219.0, 95.0, 241.9, 95.0, 203.4, 102.0, 5.6, 5.0, 4.0, 9.26])),
     (1.0,
      DenseVector([110.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 220.9, 100.0, 279.6, 76.0, 239.8, 107.0, 13.5, 9.0, 1.0, 9.43])),
     (0.0,
      DenseVector([69.0, 0.0, 1.0, 0.0, 0.0, 1.0, 27.0, 188.4, 104.0, 320.6, 128.0, 161.7, 120.0, 9.7, 7.0, 1.0, 10.0])),
     (0.0,
      DenseVector([56.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 254.8, 83.0, 194.0, 98.0, 186.2, 118.0, 13.5, 2.0, 4.0, 12.68])),
     (1.0,
      DenseVector([133.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 338.4, 86.0, 234.6, 97.0, 264.8, 139.0, 14.8, 4.0, 3.0, 13.89])),
     (0.0,
      DenseVector([119.0, 1.0, 0.0, 0.0, 0.0, 1.0, 37.0, 168.2, 81.0, 283.3, 105.0, 243.8, 136.0, 10.7, 3.0, 1.0, 12.7])),
     (0.0,
      DenseVector([131.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 143.1, 114.0, 138.1, 119.0, 199.4, 100.0, 8.9, 3.0, 3.0, 6.49])),
     (0.0,
      DenseVector([60.0, 1.0, 0.0, 0.0, 0.0, 1.0, 20.0, 118.3, 113.0, 253.8, 97.0, 172.8, 78.0, 9.4, 4.0, 1.0, 16.22])),
     (0.0,
      DenseVector([118.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 256.5, 115.0, 135.3, 79.0, 208.3, 131.0, 7.5, 5.0, 0.0, 16.18])),
     (0.0,
      DenseVector([92.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 185.9, 111.0, 216.8, 93.0, 231.0, 58.0, 8.5, 2.0, 1.0, 17.78])),
     (0.0,
      DenseVector([52.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 219.0, 100.0, 148.9, 110.0, 151.8, 129.0, 11.9, 8.0, 3.0, 21.92])),
     (0.0,
      DenseVector([135.0, 0.0, 0.0, 1.0, 0.0, 1.0, 18.0, 180.1, 90.0, 152.5, 65.0, 258.2, 85.0, 12.7, 9.0, 1.0, 16.18])),
     (0.0,
      DenseVector([107.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 114.9, 119.0, 239.7, 91.0, 165.3, 112.0, 12.6, 5.0, 2.0, 14.81])),
     (0.0,
      DenseVector([110.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 158.6, 97.0, 204.4, 123.0, 182.5, 123.0, 11.3, 3.0, 2.0, 17.78])),
     (1.0,
      DenseVector([109.0, 1.0, 0.0, 0.0, 1.0, 1.0, 36.0, 244.2, 147.0, 199.7, 65.0, 144.4, 109.0, 15.4, 3.0, 2.0, 14.75])),
     (0.0,
      DenseVector([90.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 180.0, 58.0, 229.4, 88.0, 162.2, 110.0, 13.2, 9.0, 1.0, 18.07])),
     (0.0,
      DenseVector([117.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 240.2, 110.0, 187.9, 65.0, 152.7, 102.0, 7.0, 19.0, 3.0, 16.92])),
     (0.0,
      DenseVector([57.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 234.4, 120.0, 155.3, 120.0, 130.5, 103.0, 9.4, 4.0, 1.0, 20.59])),
     (0.0,
      DenseVector([115.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 135.4, 104.0, 138.8, 91.0, 208.5, 92.0, 5.4, 3.0, 1.0, 8.2])),
     (1.0,
      DenseVector([73.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 272.4, 112.0, 252.2, 91.0, 255.4, 112.0, 10.9, 5.0, 1.0, 20.97])),
     (0.0,
      DenseVector([127.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 262.9, 95.0, 175.9, 126.0, 202.1, 101.0, 7.5, 5.0, 1.0, 26.47])),
     (0.0,
      DenseVector([79.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 66.7, 124.0, 204.0, 91.0, 254.7, 110.0, 15.1, 2.0, 1.0, 14.75])),
     (0.0,
      DenseVector([33.0, 0.0, 1.0, 0.0, 0.0, 1.0, 33.0, 221.4, 110.0, 276.1, 86.0, 222.9, 102.0, 10.1, 4.0, 1.0, 10.96])),
     (1.0,
      DenseVector([110.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 135.1, 99.0, 227.3, 123.0, 176.0, 110.0, 13.2, 4.0, 1.0, 16.18])),
     (0.0,
      DenseVector([133.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 240.8, 72.0, 202.7, 62.0, 212.9, 76.0, 7.7, 5.0, 1.0, 16.22])),
     (0.0,
      DenseVector([145.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 170.5, 101.0, 206.5, 112.0, 194.3, 106.0, 9.8, 1.0, 3.0, 16.07])),
     (0.0,
      DenseVector([47.0, 1.0, 0.0, 0.0, 0.0, 1.0, 40.0, 158.3, 90.0, 213.2, 71.0, 123.0, 132.0, 14.0, 2.0, 2.0, 13.56])),
     (0.0,
      DenseVector([53.0, 0.0, 0.0, 1.0, 0.0, 1.0, 24.0, 201.7, 87.0, 251.7, 97.0, 297.4, 95.0, 7.9, 5.0, 0.0, 13.64])),
     (0.0,
      DenseVector([148.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 168.8, 102.0, 248.1, 125.0, 250.7, 96.0, 10.6, 4.0, 1.0, 16.07])),
     (0.0,
      DenseVector([72.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 141.9, 98.0, 188.1, 63.0, 115.2, 49.0, 12.0, 5.0, 2.0, 16.92])),
     (1.0,
      DenseVector([96.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 182.6, 75.0, 188.6, 67.0, 137.7, 81.0, 9.5, 3.0, 5.0, 7.84])),
     (0.0,
      DenseVector([113.0, 0.0, 0.0, 1.0, 0.0, 1.0, 28.0, 264.4, 93.0, 222.8, 79.0, 213.7, 88.0, 10.0, 4.0, 1.0, 9.43])),
     (0.0,
      DenseVector([55.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 203.7, 80.0, 230.2, 112.0, 120.3, 48.0, 9.4, 1.0, 3.0, 8.62])),
     (0.0,
      DenseVector([143.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 191.6, 53.0, 104.6, 104.0, 195.8, 125.0, 10.7, 9.0, 3.0, 21.92])),
     (0.0,
      DenseVector([60.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 136.4, 147.0, 219.9, 101.0, 99.7, 73.0, 7.0, 3.0, 0.0, 12.68])),
     (0.0,
      DenseVector([90.0, 1.0, 0.0, 0.0, 0.0, 1.0, 30.0, 168.2, 103.0, 220.9, 75.0, 224.3, 105.0, 8.2, 2.0, 0.0, 13.64])),
     (0.0,
      DenseVector([170.0, 0.0, 1.0, 0.0, 0.0, 1.0, 34.0, 125.3, 128.0, 240.6, 104.0, 243.4, 109.0, 7.0, 3.0, 3.0, 9.26])),
     (0.0,
      DenseVector([65.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 217.9, 89.0, 238.8, 101.0, 194.4, 105.0, 6.6, 4.0, 0.0, 25.0])),
     (0.0,
      DenseVector([93.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 198.1, 93.0, 236.1, 95.0, 127.3, 121.0, 12.6, 4.0, 2.0, 6.82])),
     (0.0,
      DenseVector([117.0, 0.0, 1.0, 0.0, 1.0, 1.0, 15.0, 178.8, 81.0, 286.1, 85.0, 229.4, 128.0, 7.1, 5.0, 1.0, 13.89])),
     (0.0,
      DenseVector([96.0, 1.0, 0.0, 0.0, 0.0, 1.0, 18.0, 262.9, 57.0, 190.2, 107.0, 293.5, 103.0, 11.8, 4.0, 3.0, 10.0])),
     (0.0,
      DenseVector([101.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 164.6, 85.0, 204.9, 72.0, 136.4, 104.0, 11.5, 5.0, 0.0, 16.22])),
     (0.0,
      DenseVector([60.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 197.0, 130.0, 219.2, 105.0, 102.8, 113.0, 12.6, 4.0, 1.0, 21.21])),
     (0.0,
      DenseVector([157.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 102.9, 94.0, 229.1, 96.0, 198.7, 129.0, 13.6, 2.0, 1.0, 6.82])),
     (1.0,
      DenseVector([89.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 144.2, 74.0, 164.7, 119.0, 279.2, 121.0, 12.1, 7.0, 4.0, 13.89])),
     (0.0,
      DenseVector([96.0, 0.0, 1.0, 0.0, 0.0, 1.0, 26.0, 126.5, 108.0, 123.8, 114.0, 308.5, 98.0, 13.1, 6.0, 2.0, 14.81])),
     (0.0,
      DenseVector([91.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 139.8, 109.0, 157.0, 91.0, 218.6, 122.0, 13.6, 4.0, 3.0, 17.78])),
     (0.0,
      DenseVector([104.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 139.7, 112.0, 199.8, 127.0, 189.3, 104.0, 10.3, 2.0, 2.0, 6.49])),
     (1.0,
      DenseVector([40.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 309.4, 86.0, 271.3, 98.0, 197.1, 116.0, 11.2, 5.0, 3.0, 23.33])),
     (1.0,
      DenseVector([161.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 159.6, 102.0, 212.6, 119.0, 151.4, 83.0, 12.6, 7.0, 5.0, 9.43])),
     (0.0,
      DenseVector([131.0, 0.0, 1.0, 0.0, 0.0, 1.0, 29.0, 239.7, 102.0, 212.8, 109.0, 187.7, 77.0, 10.5, 2.0, 1.0, 13.56])),
     (0.0,
      DenseVector([131.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 195.6, 93.0, 245.0, 100.0, 104.7, 97.0, 13.2, 6.0, 2.0, 18.57])),
     (0.0,
      DenseVector([79.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 161.1, 110.0, 170.6, 111.0, 274.4, 80.0, 7.6, 2.0, 2.0, 9.26])),
     (0.0,
      DenseVector([130.0, 0.0, 0.0, 1.0, 0.0, 1.0, 22.0, 173.9, 90.0, 234.8, 86.0, 121.8, 79.0, 8.4, 5.0, 1.0, 6.25])),
     (0.0,
      DenseVector([161.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 165.4, 88.0, 209.7, 111.0, 270.7, 129.0, 10.5, 3.0, 4.0, 5.66])),
     (0.0,
      DenseVector([108.0, 1.0, 0.0, 0.0, 0.0, 1.0, 28.0, 221.3, 117.0, 156.0, 102.0, 159.3, 80.0, 8.7, 3.0, 0.0, 21.54])),
     (1.0,
      DenseVector([69.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 176.8, 89.0, 196.0, 89.0, 232.6, 151.0, 13.1, 5.0, 1.0, 9.43])),
     (0.0,
      DenseVector([80.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 196.0, 75.0, 237.8, 112.0, 220.1, 127.0, 13.9, 5.0, 1.0, 16.18])),
     (0.0,
      DenseVector([146.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 174.3, 113.0, 200.7, 107.0, 281.2, 145.0, 11.6, 3.0, 4.0, 20.97])),
     (0.0,
      DenseVector([143.0, 0.0, 1.0, 0.0, 1.0, 0.0, 0.0, 186.9, 98.0, 196.5, 110.0, 215.3, 104.0, 12.3, 3.0, 4.0, 26.47])),
     (1.0,
      DenseVector([114.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 115.6, 93.0, 163.2, 78.0, 251.3, 104.0, 9.7, 1.0, 2.0, 21.21])),
     (0.0,
      DenseVector([113.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 158.4, 107.0, 142.2, 104.0, 174.7, 108.0, 14.9, 2.0, 0.0, 11.69])),
     (0.0,
      DenseVector([29.0, 1.0, 0.0, 0.0, 0.0, 1.0, 38.0, 149.4, 93.0, 189.8, 96.0, 127.4, 71.0, 6.0, 3.0, 1.0, 20.97])),
     (0.0,
      DenseVector([57.0, 1.0, 0.0, 0.0, 0.0, 1.0, 36.0, 244.5, 94.0, 161.2, 113.0, 184.3, 105.0, 9.7, 3.0, 1.0, 21.54])),
     (0.0,
      DenseVector([78.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 145.5, 124.0, 143.4, 71.0, 291.1, 123.0, 9.5, 5.0, 3.0, 13.56])),
     (0.0,
      DenseVector([120.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 147.7, 101.0, 144.1, 71.0, 237.0, 115.0, 11.1, 5.0, 0.0, 14.81])),
     (0.0,
      DenseVector([97.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 164.3, 106.0, 182.3, 97.0, 183.9, 93.0, 12.1, 2.0, 1.0, 10.0])),
     (0.0,
      DenseVector([185.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 89.4, 77.0, 288.9, 128.0, 162.9, 93.0, 10.4, 2.0, 3.0, 12.68])),
     (0.0,
      DenseVector([144.0, 1.0, 0.0, 0.0, 0.0, 1.0, 38.0, 276.3, 149.0, 252.0, 112.0, 132.5, 85.0, 8.7, 3.0, 1.0, 5.77])),
     (0.0,
      DenseVector([97.0, 0.0, 0.0, 1.0, 0.0, 1.0, 32.0, 172.7, 79.0, 217.4, 101.0, 260.7, 88.0, 11.7, 5.0, 1.0, 20.0])),
     (0.0,
      DenseVector([130.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 138.7, 68.0, 205.6, 102.0, 231.2, 108.0, 13.6, 3.0, 2.0, 6.49])),
     (0.0,
      DenseVector([59.0, 1.0, 0.0, 0.0, 0.0, 1.0, 26.0, 205.4, 68.0, 115.0, 115.0, 214.7, 130.0, 9.4, 2.0, 5.0, 9.26])),
     (0.0,
      DenseVector([139.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 187.4, 86.0, 169.3, 116.0, 153.4, 88.0, 11.7, 3.0, 1.0, 12.33])),
     (0.0,
      DenseVector([12.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 206.1, 105.0, 246.6, 104.0, 254.6, 83.0, 12.1, 7.0, 2.0, 14.75])),
     (0.0,
      DenseVector([42.0, 0.0, 1.0, 0.0, 0.0, 1.0, 34.0, 131.5, 143.0, 185.3, 109.0, 177.3, 117.0, 10.9, 5.0, 0.0, 17.86])),
     (0.0,
      DenseVector([63.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 120.1, 103.0, 179.2, 132.0, 127.3, 82.0, 6.3, 4.0, 1.0, 11.69])),
     (0.0,
      DenseVector([143.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 168.6, 85.0, 176.3, 89.0, 282.3, 69.0, 13.3, 10.0, 1.0, 18.07])),
     (0.0,
      DenseVector([150.0, 1.0, 0.0, 0.0, 0.0, 1.0, 29.0, 235.8, 91.0, 202.8, 84.0, 246.3, 81.0, 11.9, 4.0, 1.0, 6.49])),
     (0.0,
      DenseVector([156.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 189.4, 114.0, 145.7, 96.0, 192.8, 121.0, 4.5, 13.0, 0.0, 20.0])),
     (0.0,
      DenseVector([98.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 158.5, 90.0, 254.4, 67.0, 178.4, 104.0, 14.8, 5.0, 2.0, 25.0])),
     (0.0,
      DenseVector([17.0, 0.0, 0.0, 1.0, 0.0, 1.0, 30.0, 280.0, 101.0, 258.9, 85.0, 230.0, 130.0, 10.3, 2.0, 3.0, 9.26])),
     (0.0,
      DenseVector([101.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 229.8, 97.0, 217.8, 133.0, 243.5, 53.0, 8.9, 2.0, 1.0, 18.07])),
     (0.0,
      DenseVector([163.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 241.6, 102.0, 138.9, 100.0, 148.8, 129.0, 9.8, 6.0, 1.0, 12.82])),
     (0.0,
      DenseVector([140.0, 1.0, 0.0, 0.0, 0.0, 1.0, 27.0, 176.0, 141.0, 201.3, 143.0, 167.9, 93.0, 7.1, 5.0, 3.0, 7.84])),
     (0.0,
      DenseVector([176.0, 1.0, 0.0, 0.0, 0.0, 1.0, 31.0, 167.0, 93.0, 174.1, 128.0, 155.0, 124.0, 10.4, 4.0, 3.0, 12.82])),
     (0.0,
      DenseVector([102.0, 0.0, 0.0, 1.0, 0.0, 1.0, 45.0, 129.2, 142.0, 239.4, 114.0, 223.4, 66.0, 8.7, 4.0, 0.0, 13.89])),
     (0.0,
      DenseVector([71.0, 1.0, 0.0, 0.0, 0.0, 1.0, 49.0, 174.0, 122.0, 168.6, 85.0, 132.1, 120.0, 7.8, 4.0, 1.0, 6.49])),
     (1.0,
      DenseVector([104.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 261.2, 81.0, 209.1, 130.0, 281.1, 121.0, 11.3, 1.0, 2.0, 25.0])),
     (0.0,
      DenseVector([96.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 264.6, 117.0, 264.1, 121.0, 77.1, 89.0, 8.1, 4.0, 0.0, 9.43])),
     (0.0,
      DenseVector([90.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 160.9, 97.0, 136.4, 46.0, 209.0, 121.0, 9.1, 4.0, 2.0, 17.78])),
     (0.0,
      DenseVector([106.0, 0.0, 0.0, 1.0, 0.0, 1.0, 36.0, 124.8, 93.0, 241.7, 83.0, 219.0, 110.0, 11.3, 3.0, 1.0, 16.22])),
     (0.0,
      DenseVector([148.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 230.6, 92.0, 185.6, 97.0, 183.2, 79.0, 6.2, 4.0, 2.0, 14.75])),
     (0.0,
      DenseVector([66.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 241.7, 112.0, 251.6, 78.0, 105.0, 82.0, 11.3, 2.0, 2.0, 12.68])),
     (0.0,
      DenseVector([95.0, 0.0, 1.0, 0.0, 0.0, 1.0, 35.0, 229.1, 143.0, 148.2, 104.0, 248.4, 110.0, 3.9, 3.0, 0.0, 13.89])),
     (0.0,
      DenseVector([127.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 212.9, 92.0, 260.1, 124.0, 177.6, 85.0, 8.6, 2.0, 2.0, 14.75])),
     (0.0,
      DenseVector([79.0, 0.0, 1.0, 0.0, 0.0, 1.0, 30.0, 124.8, 99.0, 207.6, 142.0, 175.0, 95.0, 4.6, 3.0, 1.0, 16.07])),
     (0.0,
      DenseVector([83.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 133.9, 82.0, 169.6, 105.0, 222.8, 114.0, 11.7, 4.0, 0.0, 11.11])),
     (0.0,
      DenseVector([83.0, 0.0, 0.0, 1.0, 0.0, 1.0, 31.0, 129.8, 87.0, 183.4, 110.0, 169.4, 40.0, 14.3, 6.0, 1.0, 21.21])),
     (0.0,
      DenseVector([136.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 88.5, 112.0, 279.5, 98.0, 278.0, 69.0, 7.5, 12.0, 1.0, 23.33])),
     (0.0,
      DenseVector([17.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 168.5, 102.0, 297.2, 130.0, 244.7, 117.0, 8.8, 6.0, 3.0, 5.66])),
     (0.0,
      DenseVector([165.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 81.4, 110.0, 263.7, 150.0, 219.7, 107.0, 12.2, 3.0, 1.0, 9.26])),
     (0.0,
      DenseVector([149.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 117.2, 99.0, 227.7, 74.0, 221.7, 141.0, 9.0, 7.0, 1.0, 14.75])),
     (1.0,
      DenseVector([79.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 300.4, 113.0, 210.8, 93.0, 173.4, 121.0, 11.9, 3.0, 2.0, 20.97])),
     (0.0,
      DenseVector([94.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 195.1, 105.0, 272.4, 100.0, 142.7, 102.0, 11.5, 16.0, 1.0, 6.25])),
     (0.0,
      DenseVector([93.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 172.0, 80.0, 219.1, 76.0, 169.8, 108.0, 10.3, 3.0, 0.0, 17.86])),
     (0.0,
      DenseVector([109.0, 1.0, 0.0, 0.0, 1.0, 1.0, 46.0, 165.2, 85.0, 136.0, 92.0, 184.8, 100.0, 11.9, 5.0, 1.0, 11.69])),
     (0.0,
      DenseVector([106.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 149.7, 114.0, 104.5, 72.0, 227.9, 87.0, 9.0, 2.0, 2.0, 21.21])),
     (0.0,
      DenseVector([92.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 185.8, 61.0, 118.1, 131.0, 257.5, 70.0, 11.8, 4.0, 1.0, 10.0])),
     (1.0,
      DenseVector([93.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 214.4, 112.0, 261.5, 97.0, 280.8, 112.0, 14.8, 3.0, 0.0, 21.21])),
     (0.0,
      DenseVector([124.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 244.6, 91.0, 125.1, 70.0, 303.4, 104.0, 12.0, 5.0, 0.0, 9.68])),
     (1.0,
      DenseVector([117.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 302.6, 83.0, 167.1, 106.0, 209.8, 79.0, 10.4, 7.0, 1.0, 16.92])),
     (1.0,
      DenseVector([163.0, 1.0, 0.0, 0.0, 0.0, 1.0, 30.0, 195.2, 123.0, 126.4, 115.0, 221.0, 90.0, 9.0, 5.0, 5.0, 20.97])),
     (0.0,
      DenseVector([87.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 207.8, 113.0, 182.2, 129.0, 163.4, 98.0, 13.0, 3.0, 1.0, 13.33])),
     (1.0,
      DenseVector([59.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 119.0, 119.0, 134.5, 115.0, 298.6, 75.0, 9.0, 6.0, 2.0, 7.84])),
     (0.0,
      DenseVector([103.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 245.8, 116.0, 135.4, 97.0, 190.9, 73.0, 9.9, 7.0, 1.0, 5.66])),
     (0.0,
      DenseVector([101.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 131.0, 148.0, 237.7, 126.0, 281.3, 111.0, 12.6, 4.0, 3.0, 12.7])),
     (1.0,
      DenseVector([72.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 129.8, 106.0, 188.7, 138.0, 212.5, 116.0, 8.3, 4.0, 4.0, 8.97])),
     (1.0,
      DenseVector([108.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 275.9, 84.0, 203.0, 91.0, 211.4, 108.0, 6.7, 4.0, 2.0, 13.33])),
     (0.0,
      DenseVector([80.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 213.3, 124.0, 189.6, 86.0, 193.7, 107.0, 12.1, 2.0, 1.0, 16.07])),
     (0.0,
      DenseVector([117.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 128.6, 92.0, 191.7, 61.0, 121.7, 117.0, 12.4, 9.0, 2.0, 26.47])),
     (0.0,
      DenseVector([51.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 95.3, 103.0, 189.3, 116.0, 238.7, 110.0, 13.6, 4.0, 1.0, 9.68])),
     (0.0,
      DenseVector([126.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 152.4, 94.0, 166.2, 91.0, 199.2, 84.0, 10.2, 1.0, 1.0, 17.86])),
     (0.0,
      DenseVector([149.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 194.9, 108.0, 187.1, 102.0, 174.9, 168.0, 8.2, 4.0, 4.0, 10.96])),
     (0.0,
      DenseVector([158.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 214.7, 112.0, 262.9, 115.0, 228.9, 68.0, 11.6, 2.0, 1.0, 16.92])),
     (0.0,
      DenseVector([66.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 180.4, 99.0, 135.1, 114.0, 206.0, 98.0, 9.5, 6.0, 1.0, 9.43])),
     (0.0,
      DenseVector([82.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 164.1, 84.0, 317.7, 109.0, 241.5, 87.0, 13.3, 4.0, 1.0, 21.92])),
     (0.0,
      DenseVector([127.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 211.1, 62.0, 235.2, 143.0, 227.5, 66.0, 15.6, 3.0, 2.0, 9.23])),
     (0.0,
      DenseVector([108.0, 1.0, 0.0, 0.0, 0.0, 1.0, 19.0, 312.4, 98.0, 271.4, 122.0, 162.6, 91.0, 10.1, 6.0, 0.0, 18.07])),
     (0.0,
      DenseVector([106.0, 0.0, 1.0, 0.0, 0.0, 1.0, 27.0, 223.3, 97.0, 212.5, 131.0, 154.6, 149.0, 11.6, 2.0, 1.0, 13.33])),
     (0.0,
      DenseVector([62.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 166.9, 76.0, 223.6, 113.0, 188.9, 87.0, 14.2, 5.0, 1.0, 21.21])),
     (0.0,
      DenseVector([48.0, 0.0, 1.0, 0.0, 0.0, 1.0, 26.0, 192.9, 60.0, 179.2, 87.0, 185.5, 137.0, 18.7, 7.0, 0.0, 18.57])),
     (0.0,
      DenseVector([58.0, 1.0, 0.0, 0.0, 0.0, 1.0, 28.0, 236.8, 89.0, 158.4, 91.0, 212.3, 97.0, 8.0, 3.0, 2.0, 21.21])),
     (0.0,
      DenseVector([138.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 149.7, 99.0, 160.0, 97.0, 256.5, 161.0, 9.5, 5.0, 1.0, 9.26])),
     (0.0,
      DenseVector([81.0, 1.0, 0.0, 0.0, 0.0, 1.0, 17.0, 268.1, 94.0, 156.1, 124.0, 160.4, 90.0, 13.6, 5.0, 2.0, 20.0])),
     (0.0,
      DenseVector([36.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 245.0, 119.0, 195.5, 91.0, 165.7, 87.0, 7.8, 7.0, 1.0, 13.64])),
     (0.0,
      DenseVector([90.0, 0.0, 0.0, 1.0, 0.0, 1.0, 22.0, 187.9, 121.0, 209.9, 101.0, 256.7, 102.0, 10.4, 5.0, 2.0, 26.47])),
     (0.0,
      DenseVector([177.0, 1.0, 0.0, 0.0, 0.0, 1.0, 21.0, 234.0, 108.0, 238.0, 61.0, 201.9, 125.0, 8.8, 4.0, 0.0, 14.75])),
     (0.0,
      DenseVector([108.0, 0.0, 0.0, 1.0, 1.0, 1.0, 36.0, 153.8, 105.0, 246.2, 62.0, 150.9, 125.0, 11.1, 4.0, 1.0, 9.26])),
     (0.0,
      DenseVector([25.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 211.7, 51.0, 225.2, 74.0, 197.4, 95.0, 12.0, 3.0, 0.0, 6.49])),
     (0.0,
      DenseVector([133.0, 0.0, 1.0, 0.0, 0.0, 1.0, 49.0, 229.4, 78.0, 219.5, 106.0, 172.3, 115.0, 12.0, 4.0, 3.0, 12.7])),
     (0.0,
      DenseVector([90.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 68.8, 124.0, 168.1, 106.0, 281.7, 96.0, 9.8, 5.0, 1.0, 14.81])),
     (0.0,
      DenseVector([94.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 200.2, 113.0, 128.9, 116.0, 278.9, 109.0, 10.4, 5.0, 0.0, 16.22])),
     (0.0,
      DenseVector([68.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 134.6, 73.0, 206.8, 117.0, 138.4, 104.0, 9.4, 2.0, 2.0, 8.62])),
     (0.0,
      DenseVector([171.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 232.4, 101.0, 182.0, 80.0, 271.8, 133.0, 9.5, 3.0, 0.0, 21.21])),
     (0.0,
      DenseVector([122.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 126.0, 89.0, 147.0, 95.0, 280.2, 97.0, 5.6, 4.0, 0.0, 17.86])),
     (0.0,
      DenseVector([94.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 175.9, 121.0, 222.1, 94.0, 191.8, 123.0, 10.9, 5.0, 1.0, 21.21])),
     (0.0,
      DenseVector([25.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 161.7, 99.0, 216.1, 89.0, 176.7, 115.0, 12.4, 6.0, 1.0, 13.89])),
     (1.0,
      DenseVector([120.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 112.6, 80.0, 124.4, 86.0, 234.9, 88.0, 8.1, 3.0, 6.0, 21.54])),
     (0.0,
      DenseVector([107.0, 1.0, 0.0, 0.0, 0.0, 1.0, 26.0, 234.0, 116.0, 223.9, 103.0, 246.5, 105.0, 13.8, 1.0, 1.0, 12.82])),
     (1.0,
      DenseVector([78.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 226.3, 88.0, 306.2, 81.0, 200.9, 120.0, 7.8, 11.0, 1.0, 9.43])),
     (0.0,
      DenseVector([93.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 174.1, 127.0, 176.8, 73.0, 240.0, 111.0, 10.7, 3.0, 0.0, 13.89])),
     (0.0,
      DenseVector([80.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 195.6, 111.0, 221.5, 122.0, 182.0, 89.0, 10.8, 1.0, 1.0, 16.22])),
     (0.0,
      DenseVector([127.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 200.8, 104.0, 181.4, 73.0, 89.7, 49.0, 9.7, 5.0, 0.0, 16.07])),
     (0.0,
      DenseVector([82.0, 1.0, 0.0, 0.0, 1.0, 1.0, 41.0, 116.9, 74.0, 228.2, 115.0, 251.4, 89.0, 9.9, 8.0, 1.0, 14.75])),
     (0.0,
      DenseVector([44.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 225.6, 118.0, 135.4, 122.0, 190.1, 96.0, 12.1, 5.0, 1.0, 14.75])),
     (0.0,
      DenseVector([69.0, 0.0, 1.0, 0.0, 0.0, 1.0, 38.0, 272.9, 122.0, 188.4, 97.0, 132.4, 87.0, 10.5, 3.0, 2.0, 13.64])),
     (0.0,
      DenseVector([89.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 215.9, 106.0, 211.1, 89.0, 252.9, 106.0, 8.8, 4.0, 0.0, 12.7])),
     (1.0,
      DenseVector([69.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 199.7, 121.0, 189.4, 92.0, 264.0, 120.0, 11.1, 4.0, 6.0, 12.33])),
     (0.0,
      DenseVector([64.0, 1.0, 0.0, 0.0, 0.0, 1.0, 31.0, 281.9, 121.0, 240.6, 104.0, 212.2, 120.0, 7.0, 5.0, 3.0, 14.75])),
     (0.0,
      DenseVector([167.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 181.2, 122.0, 262.5, 77.0, 214.0, 88.0, 8.4, 4.0, 0.0, 17.78])),
     (0.0,
      DenseVector([139.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 63.3, 116.0, 177.3, 113.0, 171.6, 82.0, 14.2, 4.0, 1.0, 26.47])),
     (0.0,
      DenseVector([41.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 208.0, 83.0, 199.4, 110.0, 173.5, 91.0, 14.9, 3.0, 1.0, 9.26])),
     (0.0,
      DenseVector([104.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 189.3, 73.0, 156.2, 120.0, 232.2, 76.0, 7.5, 8.0, 3.0, 11.11])),
     (1.0,
      DenseVector([166.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 156.8, 100.0, 171.5, 93.0, 149.1, 97.0, 11.2, 10.0, 1.0, 21.21])),
     (0.0,
      DenseVector([68.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 172.3, 135.0, 190.2, 93.0, 297.6, 84.0, 10.4, 2.0, 1.0, 5.77])),
     (0.0,
      DenseVector([78.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 140.0, 84.0, 216.8, 73.0, 224.5, 102.0, 9.0, 6.0, 2.0, 14.75])),
     (1.0,
      DenseVector([60.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 221.0, 94.0, 308.2, 72.0, 233.2, 94.0, 10.1, 5.0, 1.0, 9.23])),
     (0.0,
      DenseVector([184.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 167.2, 133.0, 162.1, 123.0, 156.9, 81.0, 9.5, 4.0, 1.0, 12.82])),
     (0.0,
      DenseVector([171.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 191.6, 83.0, 181.1, 103.0, 195.3, 73.0, 8.6, 3.0, 3.0, 21.54])),
     (0.0,
      DenseVector([68.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 172.5, 115.0, 221.9, 126.0, 170.9, 98.0, 6.8, 8.0, 4.0, 6.25])),
     (1.0,
      DenseVector([88.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 294.3, 83.0, 172.1, 104.0, 177.3, 105.0, 5.8, 7.0, 2.0, 6.49])),
     (0.0,
      DenseVector([130.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 289.1, 79.0, 150.1, 106.0, 163.4, 98.0, 13.5, 4.0, 1.0, 10.0])),
     (0.0,
      DenseVector([156.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 162.6, 113.0, 260.2, 90.0, 214.7, 93.0, 10.9, 2.0, 0.0, 8.62])),
     (0.0,
      DenseVector([92.0, 0.0, 0.0, 1.0, 0.0, 1.0, 27.0, 233.6, 126.0, 152.9, 94.0, 262.1, 95.0, 12.8, 4.0, 1.0, 21.54])),
     (0.0,
      DenseVector([151.0, 1.0, 0.0, 0.0, 0.0, 1.0, 31.0, 240.7, 102.0, 106.5, 96.0, 195.7, 128.0, 10.6, 2.0, 2.0, 17.86])),
     (1.0,
      DenseVector([78.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 290.5, 119.0, 158.4, 75.0, 255.6, 101.0, 8.6, 6.0, 2.0, 14.75])),
     (1.0,
      DenseVector([102.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 183.2, 112.0, 187.5, 113.0, 232.3, 77.0, 9.2, 5.0, 1.0, 9.43])),
     (0.0,
      DenseVector([131.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 155.6, 78.0, 165.2, 134.0, 184.1, 89.0, 8.7, 2.0, 2.0, 9.43])),
     (1.0,
      DenseVector([127.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 220.7, 100.0, 170.2, 89.0, 278.4, 73.0, 15.0, 4.0, 3.0, 9.43])),
     (1.0,
      DenseVector([100.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 303.0, 93.0, 175.6, 89.0, 135.2, 102.0, 10.4, 5.0, 1.0, 12.7])),
     (0.0,
      DenseVector([94.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 114.4, 115.0, 183.9, 79.0, 205.2, 78.0, 5.9, 2.0, 3.0, 16.07])),
     (0.0,
      DenseVector([81.0, 0.0, 1.0, 0.0, 0.0, 1.0, 36.0, 164.8, 106.0, 210.2, 138.0, 173.6, 78.0, 4.3, 2.0, 2.0, 18.07])),
     (0.0,
      DenseVector([160.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 237.1, 126.0, 238.4, 127.0, 181.3, 100.0, 10.7, 8.0, 2.0, 12.82])),
     (0.0,
      DenseVector([100.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 220.3, 106.0, 126.1, 124.0, 242.0, 67.0, 10.2, 1.0, 3.0, 8.2])),
     (0.0,
      DenseVector([78.0, 0.0, 0.0, 1.0, 0.0, 1.0, 26.0, 189.0, 98.0, 191.7, 67.0, 217.0, 68.0, 11.3, 3.0, 0.0, 9.43])),
     (0.0,
      DenseVector([139.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 172.6, 92.0, 281.7, 123.0, 253.4, 117.0, 10.5, 3.0, 3.0, 21.21])),
     (0.0,
      DenseVector([88.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 168.8, 141.0, 211.5, 129.0, 86.9, 92.0, 10.5, 7.0, 3.0, 16.22])),
     (0.0,
      DenseVector([152.0, 0.0, 1.0, 0.0, 0.0, 1.0, 39.0, 243.1, 137.0, 236.7, 103.0, 191.2, 118.0, 9.9, 3.0, 1.0, 18.57])),
     (0.0,
      DenseVector([144.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 97.9, 119.0, 171.3, 88.0, 169.9, 83.0, 7.4, 4.0, 0.0, 6.49])),
     (0.0,
      DenseVector([45.0, 0.0, 0.0, 1.0, 1.0, 1.0, 40.0, 179.0, 114.0, 272.6, 99.0, 67.1, 80.0, 10.8, 3.0, 3.0, 12.33])),
     (1.0,
      DenseVector([127.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 324.3, 107.0, 141.6, 90.0, 207.6, 90.0, 12.8, 7.0, 1.0, 16.07])),
     (0.0,
      DenseVector([102.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 69.7, 85.0, 226.5, 107.0, 128.5, 104.0, 11.1, 2.0, 2.0, 9.68])),
     (1.0,
      DenseVector([49.0, 0.0, 1.0, 0.0, 1.0, 1.0, 32.0, 176.9, 118.0, 227.0, 121.0, 204.4, 115.0, 15.1, 3.0, 3.0, 20.0])),
     (0.0,
      DenseVector([36.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 140.4, 117.0, 204.5, 121.0, 251.3, 79.0, 8.7, 4.0, 2.0, 16.92])),
     (0.0,
      DenseVector([13.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 167.4, 117.0, 223.5, 102.0, 215.8, 108.0, 9.0, 3.0, 1.0, 8.97])),
     (0.0,
      DenseVector([151.0, 0.0, 1.0, 0.0, 0.0, 1.0, 27.0, 166.4, 112.0, 234.3, 116.0, 161.1, 95.0, 5.5, 2.0, 0.0, 10.0])),
     (0.0,
      DenseVector([111.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 171.5, 108.0, 204.4, 106.0, 238.8, 90.0, 10.7, 4.0, 3.0, 9.43])),
     (0.0,
      DenseVector([135.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 177.2, 151.0, 209.3, 83.0, 236.3, 114.0, 11.1, 5.0, 2.0, 13.64])),
     (0.0,
      DenseVector([114.0, 1.0, 0.0, 0.0, 0.0, 1.0, 34.0, 114.5, 64.0, 229.3, 84.0, 288.8, 68.0, 10.4, 16.0, 0.0, 6.49])),
     (0.0,
      DenseVector([122.0, 1.0, 0.0, 0.0, 0.0, 1.0, 34.0, 205.1, 123.0, 203.3, 123.0, 126.7, 106.0, 10.7, 1.0, 2.0, 16.07])),
     (1.0,
      DenseVector([89.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 208.4, 112.0, 189.5, 116.0, 264.4, 101.0, 16.8, 1.0, 0.0, 9.43])),
     (0.0,
      DenseVector([96.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 125.8, 102.0, 244.4, 111.0, 167.7, 90.0, 9.0, 2.0, 2.0, 13.89])),
     (0.0,
      DenseVector([106.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 186.2, 97.0, 257.7, 89.0, 164.3, 106.0, 11.0, 4.0, 2.0, 16.92])),
     (0.0,
      DenseVector([122.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 128.9, 136.0, 244.4, 133.0, 148.7, 133.0, 10.7, 5.0, 0.0, 13.56])),
     (0.0,
      DenseVector([87.0, 1.0, 0.0, 0.0, 0.0, 1.0, 16.0, 257.4, 91.0, 232.0, 101.0, 143.2, 73.0, 14.7, 2.0, 1.0, 11.69])),
     (0.0,
      DenseVector([145.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 189.3, 113.0, 203.7, 92.0, 218.3, 103.0, 14.2, 3.0, 5.0, 14.75])),
     (0.0,
      DenseVector([136.0, 1.0, 0.0, 0.0, 0.0, 1.0, 24.0, 50.1, 134.0, 295.8, 89.0, 111.8, 115.0, 11.0, 6.0, 0.0, 21.92])),
     (0.0,
      DenseVector([111.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 165.5, 72.0, 228.9, 73.0, 169.7, 103.0, 9.7, 6.0, 2.0, 11.69])),
     (0.0,
      DenseVector([167.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 143.7, 108.0, 75.6, 111.0, 217.9, 99.0, 9.9, 4.0, 0.0, 17.86])),
     (0.0,
      DenseVector([88.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 166.4, 97.0, 186.7, 118.0, 251.6, 89.0, 14.5, 4.0, 0.0, 11.69])),
     (0.0,
      DenseVector([131.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 264.2, 118.0, 180.0, 89.0, 223.9, 104.0, 10.7, 1.0, 2.0, 18.07])),
     (0.0,
      DenseVector([90.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 192.1, 80.0, 269.3, 125.0, 259.6, 97.0, 11.7, 4.0, 2.0, 16.07])),
     (0.0,
      DenseVector([95.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 145.4, 98.0, 334.1, 109.0, 209.8, 72.0, 8.8, 2.0, 1.0, 14.75])),
     (0.0,
      DenseVector([42.0, 0.0, 1.0, 0.0, 0.0, 1.0, 16.0, 240.8, 80.0, 212.6, 121.0, 271.7, 138.0, 9.4, 4.0, 1.0, 26.47])),
     (0.0,
      DenseVector([69.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 170.7, 98.0, 111.6, 109.0, 171.5, 107.0, 4.8, 7.0, 3.0, 6.82])),
     (0.0,
      DenseVector([99.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 221.0, 118.0, 138.8, 104.0, 198.6, 96.0, 12.1, 4.0, 0.0, 14.1])),
     (0.0,
      DenseVector([72.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 180.0, 109.0, 292.7, 96.0, 194.9, 114.0, 10.8, 4.0, 0.0, 17.78])),
     (0.0,
      DenseVector([88.0, 1.0, 0.0, 0.0, 0.0, 1.0, 14.0, 298.8, 92.0, 207.3, 86.0, 257.9, 65.0, 11.9, 4.0, 5.0, 21.92])),
     (1.0,
      DenseVector([113.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 317.1, 97.0, 224.8, 70.0, 215.1, 90.0, 13.6, 2.0, 1.0, 12.7])),
     (0.0,
      DenseVector([142.0, 0.0, 1.0, 0.0, 1.0, 1.0, 23.0, 102.2, 103.0, 229.7, 110.0, 176.3, 100.0, 9.0, 4.0, 1.0, 17.78])),
     (0.0,
      DenseVector([3.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 182.9, 87.0, 172.1, 103.0, 233.1, 89.0, 12.1, 3.0, 4.0, 9.43])),
     (1.0,
      DenseVector([23.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 155.6, 122.0, 223.8, 82.0, 50.9, 130.0, 10.6, 3.0, 4.0, 8.2])),
     (0.0,
      DenseVector([75.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 184.8, 126.0, 198.6, 103.0, 103.0, 90.0, 8.3, 3.0, 1.0, 8.97])),
     (0.0,
      DenseVector([151.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 175.7, 91.0, 152.3, 127.0, 140.0, 88.0, 10.7, 10.0, 1.0, 12.7])),
     (0.0,
      DenseVector([91.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 251.5, 57.0, 179.1, 113.0, 163.2, 72.0, 6.6, 3.0, 1.0, 25.0])),
     (0.0,
      DenseVector([78.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 131.6, 102.0, 290.4, 134.0, 190.5, 95.0, 0.0, 0.0, 3.0, 21.54])),
     (0.0,
      DenseVector([98.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 92.3, 128.0, 250.1, 89.0, 211.9, 110.0, 10.8, 16.0, 3.0, 12.7])),
     (1.0,
      DenseVector([166.0, 0.0, 1.0, 0.0, 0.0, 1.0, 41.0, 196.7, 109.0, 124.3, 107.0, 198.3, 94.0, 11.0, 5.0, 5.0, 9.43])),
     (0.0,
      DenseVector([78.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 131.4, 98.0, 189.9, 95.0, 147.4, 83.0, 11.2, 2.0, 2.0, 23.33])),
     (1.0,
      DenseVector([69.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 215.9, 122.0, 270.3, 114.0, 301.8, 129.0, 6.1, 5.0, 4.0, 14.81])),
     (0.0,
      DenseVector([122.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 158.2, 95.0, 178.5, 113.0, 154.3, 76.0, 8.5, 5.0, 2.0, 14.81])),
     (0.0,
      DenseVector([138.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 216.0, 106.0, 207.6, 110.0, 180.0, 102.0, 11.2, 4.0, 0.0, 20.59])),
     (0.0,
      DenseVector([19.0, 0.0, 0.0, 1.0, 0.0, 1.0, 34.0, 206.4, 111.0, 229.7, 118.0, 156.7, 81.0, 11.8, 10.0, 3.0, 5.66])),
     (0.0,
      DenseVector([141.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 138.2, 94.0, 176.4, 84.0, 182.9, 110.0, 11.1, 6.0, 3.0, 5.66])),
     (0.0,
      DenseVector([79.0, 0.0, 1.0, 0.0, 0.0, 1.0, 27.0, 192.3, 125.0, 147.3, 108.0, 215.0, 97.0, 7.8, 4.0, 1.0, 16.92])),
     (0.0,
      DenseVector([171.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 262.1, 78.0, 171.6, 113.0, 208.7, 100.0, 5.3, 3.0, 0.0, 16.07])),
     (0.0,
      DenseVector([51.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 41.2, 106.0, 210.6, 82.0, 220.1, 115.0, 12.4, 3.0, 0.0, 10.0])),
     (1.0,
      DenseVector([114.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 326.1, 119.0, 161.0, 105.0, 220.1, 129.0, 11.0, 3.0, 1.0, 6.25])),
     (0.0,
      DenseVector([98.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 167.0, 60.0, 125.1, 65.0, 262.5, 100.0, 8.9, 2.0, 1.0, 16.18])),
     (0.0,
      DenseVector([94.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 210.2, 95.0, 191.2, 87.0, 150.8, 111.0, 9.4, 3.0, 2.0, 20.59])),
     (0.0,
      DenseVector([100.0, 1.0, 0.0, 0.0, 0.0, 1.0, 39.0, 204.9, 74.0, 210.2, 80.0, 168.8, 89.0, 11.2, 4.0, 2.0, 21.21])),
     (0.0,
      DenseVector([125.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 184.1, 105.0, 126.7, 123.0, 241.5, 94.0, 4.7, 6.0, 1.0, 23.33])),
     (0.0,
      DenseVector([81.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 263.8, 84.0, 162.8, 107.0, 149.7, 94.0, 8.7, 6.0, 2.0, 6.49])),
     (1.0,
      DenseVector([154.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 283.6, 115.0, 181.3, 103.0, 306.1, 60.0, 8.8, 5.0, 0.0, 20.0])),
     (0.0,
      DenseVector([91.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 167.5, 77.0, 175.2, 113.0, 233.5, 85.0, 16.1, 5.0, 2.0, 9.26])),
     (0.0,
      DenseVector([94.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 142.3, 50.0, 231.5, 142.0, 205.6, 79.0, 5.5, 3.0, 1.0, 9.23])),
     (0.0,
      DenseVector([57.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 167.1, 103.0, 188.4, 112.0, 111.4, 74.0, 9.1, 2.0, 1.0, 9.43])),
     (0.0,
      DenseVector([140.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 148.3, 86.0, 184.2, 151.0, 179.1, 83.0, 10.9, 4.0, 0.0, 20.97])),
     (1.0,
      DenseVector([64.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 297.8, 88.0, 196.0, 89.0, 213.7, 107.0, 12.0, 2.0, 1.0, 10.0])),
     (0.0,
      DenseVector([52.0, 0.0, 1.0, 0.0, 1.0, 0.0, 0.0, 144.5, 60.0, 296.5, 129.0, 194.3, 117.0, 12.4, 3.0, 1.0, 16.18])),
     (1.0,
      DenseVector([117.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 233.4, 71.0, 296.4, 76.0, 276.9, 103.0, 10.3, 1.0, 0.0, 20.59])),
     (0.0,
      DenseVector([119.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 130.5, 98.0, 249.5, 91.0, 219.9, 117.0, 12.7, 1.0, 3.0, 9.43])),
     (0.0,
      DenseVector([85.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 133.3, 107.0, 185.0, 101.0, 289.0, 99.0, 9.1, 4.0, 3.0, 7.84])),
     (0.0,
      DenseVector([92.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 183.7, 103.0, 270.6, 97.0, 132.9, 104.0, 11.2, 2.0, 0.0, 23.33])),
     (1.0,
      DenseVector([116.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 271.0, 114.0, 209.9, 71.0, 193.5, 109.0, 13.7, 2.0, 5.0, 12.68])),
     (0.0,
      DenseVector([163.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 122.5, 70.0, 221.4, 92.0, 187.5, 88.0, 10.4, 6.0, 0.0, 6.82])),
     (0.0,
      DenseVector([88.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 143.3, 97.0, 170.7, 81.0, 195.3, 89.0, 11.4, 5.0, 2.0, 26.47])),
     (0.0,
      DenseVector([83.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 182.5, 85.0, 181.5, 114.0, 199.1, 110.0, 14.7, 6.0, 1.0, 5.77])),
     (0.0,
      DenseVector([117.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 110.4, 122.0, 242.9, 103.0, 170.4, 75.0, 13.9, 5.0, 1.0, 14.81])),
     (0.0,
      DenseVector([90.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 140.8, 109.0, 303.0, 114.0, 267.4, 112.0, 14.1, 1.0, 0.0, 7.84])),
     (0.0,
      DenseVector([137.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 137.5, 102.0, 176.0, 98.0, 210.6, 88.0, 10.4, 6.0, 0.0, 21.92])),
     (0.0,
      DenseVector([112.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 115.9, 81.0, 199.7, 100.0, 249.3, 111.0, 10.0, 4.0, 1.0, 12.7])),
     (0.0,
      DenseVector([52.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 288.2, 108.0, 129.6, 136.0, 229.6, 73.0, 9.6, 5.0, 4.0, 13.89])),
     (0.0,
      DenseVector([173.0, 0.0, 0.0, 1.0, 0.0, 1.0, 20.0, 60.2, 100.0, 118.1, 76.0, 230.1, 131.0, 13.3, 4.0, 1.0, 20.59])),
     (1.0,
      DenseVector([3.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 125.5, 100.0, 266.2, 78.0, 270.4, 119.0, 5.9, 6.0, 3.0, 14.1])),
     (0.0,
      DenseVector([120.0, 1.0, 0.0, 0.0, 0.0, 1.0, 27.0, 179.6, 142.0, 262.8, 103.0, 239.9, 81.0, 11.1, 2.0, 1.0, 10.0])),
     (0.0,
      DenseVector([132.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 99.0, 126.0, 189.4, 76.0, 156.3, 108.0, 8.4, 3.0, 1.0, 20.0])),
     (0.0,
      DenseVector([101.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 155.4, 71.0, 84.0, 122.0, 229.1, 97.0, 5.8, 1.0, 1.0, 24.29])),
     (0.0,
      DenseVector([58.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 177.4, 107.0, 204.7, 104.0, 141.9, 123.0, 15.0, 9.0, 3.0, 9.68])),
     (0.0,
      DenseVector([74.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 233.6, 72.0, 168.0, 103.0, 276.3, 71.0, 6.3, 2.0, 2.0, 16.07])),
     (0.0,
      DenseVector([113.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 210.2, 109.0, 199.4, 81.0, 173.3, 94.0, 9.9, 4.0, 3.0, 12.33])),
     (0.0,
      DenseVector([132.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 126.8, 83.0, 243.2, 97.0, 223.4, 106.0, 7.1, 5.0, 3.0, 21.54])),
     (0.0,
      DenseVector([24.0, 0.0, 0.0, 1.0, 0.0, 1.0, 41.0, 187.1, 80.0, 286.1, 77.0, 188.0, 107.0, 10.4, 2.0, 1.0, 18.57])),
     (0.0,
      DenseVector([127.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 155.8, 91.0, 103.7, 85.0, 117.2, 125.0, 8.6, 3.0, 2.0, 16.22])),
     (0.0,
      DenseVector([85.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 232.1, 130.0, 150.7, 75.0, 253.9, 77.0, 13.0, 3.0, 0.0, 26.47])),
     (0.0,
      DenseVector([59.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 128.7, 85.0, 169.8, 121.0, 207.3, 98.0, 10.5, 4.0, 3.0, 23.33])),
     (0.0,
      DenseVector([15.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 224.3, 85.0, 166.3, 79.0, 187.0, 117.0, 11.7, 3.0, 1.0, 16.07])),
     (0.0,
      DenseVector([113.0, 0.0, 1.0, 0.0, 0.0, 1.0, 30.0, 208.9, 87.0, 145.2, 89.0, 198.2, 77.0, 14.9, 5.0, 2.0, 6.49])),
     (0.0,
      DenseVector([43.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 184.8, 114.0, 130.6, 131.0, 175.0, 69.0, 8.0, 9.0, 0.0, 13.64])),
     (1.0,
      DenseVector([185.0, 0.0, 0.0, 1.0, 0.0, 1.0, 24.0, 121.6, 100.0, 108.3, 77.0, 206.3, 98.0, 12.3, 2.0, 5.0, 12.7])),
     (0.0,
      DenseVector([127.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 145.0, 106.0, 230.3, 99.0, 164.9, 103.0, 12.7, 3.0, 1.0, 6.25])),
     (0.0,
      DenseVector([69.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 243.3, 101.0, 109.2, 99.0, 176.9, 138.0, 9.3, 3.0, 0.0, 14.1])),
     (0.0,
      DenseVector([102.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 214.8, 60.0, 173.8, 70.0, 166.7, 90.0, 10.9, 2.0, 1.0, 25.0])),
     (0.0,
      DenseVector([126.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 184.5, 56.0, 197.7, 91.0, 242.3, 74.0, 11.2, 3.0, 2.0, 24.29])),
     (0.0,
      DenseVector([137.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 84.3, 98.0, 259.5, 115.0, 197.5, 115.0, 10.3, 2.0, 2.0, 9.43])),
     (0.0,
      DenseVector([80.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 142.9, 70.0, 221.6, 83.0, 121.4, 111.0, 15.2, 4.0, 1.0, 14.81])),
     (0.0,
      DenseVector([86.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 120.6, 82.0, 238.3, 96.0, 219.8, 125.0, 11.4, 5.0, 0.0, 6.49])),
     (0.0,
      DenseVector([120.0, 1.0, 0.0, 0.0, 0.0, 1.0, 40.0, 222.2, 80.0, 223.1, 84.0, 164.0, 65.0, 12.1, 3.0, 1.0, 8.2])),
     (0.0,
      DenseVector([85.0, 0.0, 1.0, 0.0, 0.0, 1.0, 21.0, 219.3, 132.0, 218.2, 105.0, 267.6, 95.0, 15.7, 2.0, 2.0, 25.0])),
     (0.0,
      DenseVector([103.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 230.2, 100.0, 174.1, 100.0, 194.1, 112.0, 9.0, 11.0, 2.0, 6.49])),
     (0.0,
      DenseVector([53.0, 1.0, 0.0, 0.0, 0.0, 1.0, 22.0, 76.1, 115.0, 171.2, 119.0, 246.3, 126.0, 6.0, 2.0, 0.0, 9.26])),
     (0.0,
      DenseVector([115.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 147.7, 105.0, 184.1, 68.0, 153.5, 102.0, 12.7, 5.0, 0.0, 8.2])),
     (0.0,
      DenseVector([116.0, 0.0, 1.0, 0.0, 1.0, 0.0, 0.0, 139.4, 102.0, 261.8, 97.0, 167.4, 101.0, 10.5, 4.0, 1.0, 21.54])),
     (0.0,
      DenseVector([93.0, 0.0, 0.0, 1.0, 0.0, 1.0, 23.0, 196.0, 91.0, 156.3, 107.0, 200.5, 68.0, 15.3, 2.0, 1.0, 9.43])),
     (0.0,
      DenseVector([106.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 160.8, 73.0, 170.4, 112.0, 249.2, 131.0, 7.2, 3.0, 1.0, 12.33])),
     (1.0,
      DenseVector([92.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 325.4, 73.0, 127.8, 100.0, 209.9, 98.0, 9.3, 10.0, 1.0, 18.57])),
     (0.0,
      DenseVector([110.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 198.2, 92.0, 147.1, 97.0, 199.6, 76.0, 16.5, 4.0, 4.0, 9.68])),
     (0.0,
      DenseVector([122.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 168.8, 86.0, 237.6, 98.0, 204.3, 94.0, 5.3, 9.0, 1.0, 9.43])),
     (0.0,
      DenseVector([82.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 273.8, 99.0, 123.8, 110.0, 147.7, 61.0, 6.9, 4.0, 2.0, 10.0])),
     (0.0,
      DenseVector([71.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 217.7, 114.0, 182.0, 105.0, 113.5, 87.0, 8.5, 10.0, 4.0, 13.64])),
     (1.0,
      DenseVector([91.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 93.5, 110.0, 213.3, 79.0, 171.7, 83.0, 8.9, 4.0, 4.0, 26.47])),
     (0.0,
      DenseVector([109.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 195.4, 104.0, 204.9, 91.0, 230.2, 95.0, 14.0, 4.0, 0.0, 12.33])),
     (0.0,
      DenseVector([126.0, 0.0, 0.0, 1.0, 0.0, 1.0, 42.0, 247.8, 96.0, 159.1, 98.0, 201.8, 73.0, 10.6, 3.0, 2.0, 21.21])),
     (0.0,
      DenseVector([98.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 162.5, 81.0, 159.2, 114.0, 157.2, 97.0, 9.7, 1.0, 2.0, 9.26])),
     (0.0,
      DenseVector([98.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 86.1, 78.0, 222.7, 130.0, 155.0, 124.0, 9.0, 7.0, 0.0, 25.0])),
     (0.0,
      DenseVector([78.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 89.1, 124.0, 226.6, 121.0, 249.2, 88.0, 6.9, 5.0, 0.0, 5.66])),
     (0.0,
      DenseVector([44.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 189.7, 122.0, 178.9, 72.0, 244.7, 93.0, 9.8, 5.0, 0.0, 7.84])),
     (0.0,
      DenseVector([60.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 190.8, 100.0, 196.0, 85.0, 151.7, 110.0, 9.9, 9.0, 0.0, 24.29])),
     (1.0,
      DenseVector([113.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 198.6, 115.0, 258.0, 106.0, 245.8, 104.0, 10.2, 6.0, 1.0, 17.86])),
     (0.0,
      DenseVector([101.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 176.5, 105.0, 198.6, 136.0, 210.2, 150.0, 7.7, 5.0, 0.0, 9.23])),
     (0.0,
      DenseVector([147.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 188.3, 80.0, 214.7, 123.0, 218.0, 89.0, 10.2, 4.0, 2.0, 17.78])),
     (0.0,
      DenseVector([21.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 197.6, 105.0, 206.4, 88.0, 169.6, 143.0, 9.6, 2.0, 3.0, 21.21])),
     (0.0,
      DenseVector([141.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 208.5, 129.0, 190.3, 86.0, 144.7, 93.0, 12.0, 9.0, 1.0, 9.68])),
     (0.0,
      DenseVector([87.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 261.0, 83.0, 144.2, 95.0, 284.4, 119.0, 16.3, 1.0, 4.0, 11.11])),
     (0.0,
      DenseVector([69.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 209.6, 94.0, 193.1, 106.0, 239.9, 90.0, 12.4, 4.0, 0.0, 12.82])),
     (0.0,
      DenseVector([169.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 157.8, 96.0, 160.0, 120.0, 198.8, 112.0, 13.7, 6.0, 3.0, 13.89])),
     (1.0,
      DenseVector([102.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 245.4, 82.0, 260.3, 124.0, 195.1, 82.0, 10.5, 3.0, 3.0, 14.75])),
     (0.0,
      DenseVector([174.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 72.2, 80.0, 173.4, 81.0, 130.1, 121.0, 11.0, 5.0, 2.0, 16.22])),
     (0.0,
      DenseVector([53.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 100.2, 85.0, 213.8, 90.0, 299.8, 67.0, 7.7, 4.0, 3.0, 9.43])),
     (0.0,
      DenseVector([44.0, 0.0, 0.0, 1.0, 0.0, 1.0, 25.0, 152.9, 106.0, 205.5, 147.0, 247.3, 107.0, 8.0, 5.0, 1.0, 16.07])),
     (0.0,
      DenseVector([89.0, 1.0, 0.0, 0.0, 0.0, 1.0, 15.0, 274.0, 83.0, 192.2, 97.0, 125.9, 141.0, 11.9, 2.0, 0.0, 12.33])),
     (0.0,
      DenseVector([116.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 215.6, 127.0, 105.5, 110.0, 291.7, 80.0, 11.2, 7.0, 3.0, 16.07])),
     (0.0,
      DenseVector([143.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 178.2, 109.0, 254.6, 93.0, 241.3, 139.0, 8.5, 7.0, 0.0, 7.84])),
     (0.0,
      DenseVector([78.0, 1.0, 0.0, 0.0, 1.0, 1.0, 20.0, 252.8, 102.0, 217.6, 105.0, 107.3, 93.0, 11.2, 3.0, 0.0, 6.49])),
     (0.0,
      DenseVector([93.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 216.2, 100.0, 182.8, 136.0, 237.9, 66.0, 7.8, 3.0, 2.0, 12.33])),
     (0.0,
      DenseVector([106.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 165.1, 120.0, 290.2, 79.0, 250.3, 140.0, 8.1, 2.0, 0.0, 6.49])),
     (0.0,
      DenseVector([116.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 133.8, 88.0, 177.1, 103.0, 185.2, 106.0, 12.4, 4.0, 2.0, 10.0])),
     (0.0,
      DenseVector([42.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 217.2, 122.0, 303.2, 58.0, 147.3, 114.0, 14.0, 4.0, 2.0, 10.96])),
     (0.0,
      DenseVector([173.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 82.1, 75.0, 201.2, 95.0, 206.7, 146.0, 9.6, 2.0, 1.0, 21.54])),
     (0.0,
      DenseVector([80.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 157.1, 77.0, 302.0, 69.0, 317.9, 159.0, 15.6, 4.0, 0.0, 5.66])),
     (0.0,
      DenseVector([70.0, 0.0, 0.0, 1.0, 0.0, 1.0, 23.0, 205.5, 95.0, 224.3, 80.0, 196.1, 76.0, 9.7, 4.0, 1.0, 12.82])),
     (1.0,
      DenseVector([162.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 147.6, 126.0, 284.1, 84.0, 170.2, 87.0, 8.1, 7.0, 0.0, 11.11])),
     (0.0,
      DenseVector([112.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 172.1, 73.0, 228.0, 136.0, 269.4, 96.0, 14.1, 3.0, 2.0, 18.07])),
     (0.0,
      DenseVector([186.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 206.8, 104.0, 286.0, 115.0, 153.9, 112.0, 12.4, 6.0, 0.0, 8.97])),
     (0.0,
      DenseVector([99.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 193.3, 51.0, 240.1, 117.0, 145.7, 92.0, 12.2, 4.0, 1.0, 11.11])),
     (0.0,
      DenseVector([56.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 225.9, 52.0, 242.0, 69.0, 144.5, 68.0, 11.7, 3.0, 0.0, 10.0])),
     (0.0,
      DenseVector([135.0, 0.0, 1.0, 0.0, 0.0, 1.0, 38.0, 202.6, 92.0, 169.6, 93.0, 185.8, 108.0, 11.9, 6.0, 4.0, 13.64])),
     (0.0,
      DenseVector([83.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 48.4, 105.0, 139.7, 86.0, 154.1, 80.0, 11.5, 4.0, 2.0, 26.47])),
     (0.0,
      DenseVector([48.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 168.6, 93.0, 246.0, 61.0, 269.9, 102.0, 11.4, 7.0, 1.0, 23.33])),
     (0.0,
      DenseVector([112.0, 1.0, 0.0, 0.0, 0.0, 1.0, 31.0, 142.9, 92.0, 233.8, 107.0, 329.2, 142.0, 10.4, 6.0, 0.0, 5.77])),
     (0.0,
      DenseVector([112.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 85.2, 102.0, 153.2, 118.0, 142.6, 107.0, 8.2, 4.0, 1.0, 5.77])),
     (0.0,
      DenseVector([141.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 77.8, 123.0, 144.4, 119.0, 75.3, 65.0, 5.7, 7.0, 1.0, 17.86])),
     (0.0,
      DenseVector([73.0, 0.0, 1.0, 0.0, 0.0, 1.0, 19.0, 179.4, 108.0, 93.6, 90.0, 234.6, 99.0, 14.9, 4.0, 2.0, 21.92])),
     (0.0,
      DenseVector([81.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 139.8, 98.0, 247.2, 103.0, 241.0, 113.0, 11.7, 3.0, 3.0, 18.57])),
     (0.0,
      DenseVector([165.0, 1.0, 0.0, 0.0, 0.0, 1.0, 17.0, 177.9, 68.0, 153.9, 112.0, 251.2, 121.0, 10.6, 7.0, 1.0, 26.47])),
     (0.0,
      DenseVector([70.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 101.9, 98.0, 193.3, 113.0, 167.5, 126.0, 15.0, 6.0, 1.0, 14.1])),
     (0.0,
      DenseVector([79.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 157.5, 90.0, 223.8, 83.0, 241.1, 98.0, 12.2, 4.0, 1.0, 6.82])),
     (0.0,
      DenseVector([126.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 167.1, 138.0, 154.4, 93.0, 244.5, 148.0, 13.7, 10.0, 3.0, 26.47])),
     (0.0,
      DenseVector([77.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 124.1, 92.0, 214.9, 131.0, 241.3, 132.0, 13.7, 3.0, 0.0, 18.07])),
     (0.0,
      DenseVector([72.0, 0.0, 1.0, 0.0, 0.0, 1.0, 31.0, 221.4, 124.0, 313.4, 96.0, 175.9, 94.0, 11.8, 1.0, 3.0, 17.86])),
     (0.0,
      DenseVector([142.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 245.1, 66.0, 144.6, 109.0, 227.3, 112.0, 3.1, 4.0, 1.0, 21.92])),
     (0.0,
      DenseVector([169.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 135.7, 111.0, 223.0, 124.0, 167.5, 101.0, 9.5, 4.0, 1.0, 8.2])),
     (0.0,
      DenseVector([110.0, 1.0, 0.0, 0.0, 0.0, 1.0, 37.0, 227.9, 80.0, 101.9, 141.0, 188.1, 110.0, 5.1, 6.0, 0.0, 12.82])),
     (0.0,
      DenseVector([88.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 177.8, 112.0, 226.5, 80.0, 221.8, 71.0, 7.9, 4.0, 1.0, 13.33])),
     (1.0,
      DenseVector([80.0, 1.0, 0.0, 0.0, 0.0, 1.0, 33.0, 189.4, 109.0, 148.7, 68.0, 208.9, 119.0, 11.2, 4.0, 1.0, 13.89])),
     (0.0,
      DenseVector([43.0, 0.0, 0.0, 1.0, 0.0, 1.0, 39.0, 188.3, 108.0, 167.6, 116.0, 189.9, 92.0, 15.6, 5.0, 1.0, 12.82])),
     (0.0,
      DenseVector([144.0, 1.0, 0.0, 0.0, 0.0, 1.0, 26.0, 177.1, 94.0, 206.0, 85.0, 254.8, 95.0, 6.6, 2.0, 2.0, 21.54])),
     (0.0,
      DenseVector([142.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 128.5, 95.0, 192.6, 91.0, 79.0, 95.0, 9.2, 6.0, 2.0, 17.78])),
     (0.0,
      DenseVector([158.0, 0.0, 0.0, 1.0, 0.0, 1.0, 22.0, 192.9, 86.0, 219.7, 104.0, 169.3, 92.0, 10.9, 3.0, 2.0, 13.56])),
     (0.0,
      DenseVector([78.0, 0.0, 1.0, 0.0, 0.0, 1.0, 28.0, 130.5, 109.0, 173.3, 99.0, 192.2, 106.0, 10.4, 3.0, 3.0, 16.07])),
     (0.0,
      DenseVector([131.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 201.1, 141.0, 216.5, 93.0, 217.4, 132.0, 11.5, 4.0, 1.0, 11.11])),
     (0.0,
      DenseVector([61.0, 1.0, 0.0, 0.0, 0.0, 1.0, 38.0, 139.8, 108.0, 58.3, 120.0, 203.4, 160.0, 9.7, 1.0, 3.0, 9.43])),
     (0.0,
      DenseVector([73.0, 0.0, 0.0, 1.0, 0.0, 1.0, 6.0, 237.6, 105.0, 244.9, 98.0, 188.3, 107.0, 6.8, 11.0, 4.0, 9.43])),
     (0.0,
      DenseVector([100.0, 1.0, 0.0, 0.0, 0.0, 1.0, 26.0, 197.7, 80.0, 232.6, 83.0, 196.7, 120.0, 12.0, 5.0, 1.0, 10.0])),
     (0.0,
      DenseVector([100.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 222.1, 115.0, 245.0, 130.0, 129.3, 119.0, 8.1, 2.0, 2.0, 7.84])),
     (1.0,
      DenseVector([109.0, 0.0, 1.0, 0.0, 0.0, 1.0, 39.0, 270.0, 58.0, 235.5, 108.0, 194.3, 57.0, 7.8, 6.0, 1.0, 21.21])),
     (0.0,
      DenseVector([103.0, 0.0, 0.0, 1.0, 0.0, 1.0, 29.0, 211.6, 111.0, 237.9, 84.0, 209.4, 93.0, 14.0, 7.0, 2.0, 21.92])),
     (0.0,
      DenseVector([139.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 148.7, 90.0, 204.3, 106.0, 144.1, 105.0, 9.8, 4.0, 2.0, 14.75])),
     (0.0,
      DenseVector([63.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 81.7, 105.0, 203.0, 66.0, 181.2, 109.0, 12.9, 3.0, 2.0, 8.2])),
     (0.0,
      DenseVector([115.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 143.6, 135.0, 278.0, 119.0, 46.7, 84.0, 12.7, 3.0, 0.0, 12.7])),
     (0.0,
      DenseVector([77.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 205.3, 69.0, 171.0, 84.0, 242.4, 96.0, 10.1, 2.0, 3.0, 24.29])),
     (0.0,
      DenseVector([14.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 184.5, 77.0, 272.5, 103.0, 190.3, 129.0, 9.7, 3.0, 4.0, 13.56])),
     (0.0,
      DenseVector([81.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 140.8, 97.0, 211.5, 109.0, 216.9, 53.0, 12.2, 4.0, 0.0, 18.07])),
     (0.0,
      DenseVector([113.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 140.8, 107.0, 252.6, 87.0, 215.4, 136.0, 9.1, 4.0, 3.0, 13.56])),
     (0.0,
      DenseVector([93.0, 1.0, 0.0, 0.0, 1.0, 1.0, 36.0, 169.7, 109.0, 318.6, 88.0, 246.5, 95.0, 9.8, 3.0, 0.0, 21.54])),
     (0.0,
      DenseVector([77.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 168.1, 83.0, 202.0, 91.0, 173.2, 91.0, 10.0, 3.0, 3.0, 8.97])),
     (0.0,
      DenseVector([125.0, 1.0, 0.0, 0.0, 0.0, 1.0, 25.0, 190.1, 133.0, 181.1, 61.0, 104.0, 110.0, 12.5, 3.0, 2.0, 16.07])),
     (0.0,
      DenseVector([88.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 192.6, 96.0, 184.0, 92.0, 173.6, 113.0, 8.8, 6.0, 2.0, 14.1])),
     (0.0,
      DenseVector([89.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 234.8, 95.0, 109.7, 87.0, 180.3, 99.0, 10.4, 2.0, 0.0, 8.97])),
     (0.0,
      DenseVector([40.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 146.4, 105.0, 196.4, 143.0, 235.6, 123.0, 9.4, 3.0, 3.0, 12.33])),
     (0.0,
      DenseVector([124.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 247.2, 84.0, 161.9, 130.0, 170.3, 73.0, 6.1, 6.0, 1.0, 18.57])),
     (0.0,
      DenseVector([117.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 234.8, 118.0, 211.7, 112.0, 147.0, 97.0, 7.5, 3.0, 2.0, 11.69])),
     (0.0,
      DenseVector([83.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 185.9, 115.0, 232.5, 98.0, 205.2, 124.0, 10.2, 2.0, 2.0, 20.59])),
     (0.0,
      DenseVector([7.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 98.5, 85.0, 200.3, 80.0, 252.3, 68.0, 12.0, 7.0, 2.0, 12.33])),
     (1.0,
      DenseVector([104.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 304.0, 141.0, 264.1, 127.0, 138.1, 104.0, 7.2, 4.0, 0.0, 25.0])),
     (0.0,
      DenseVector([128.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 140.3, 108.0, 235.3, 111.0, 200.8, 57.0, 11.8, 6.0, 1.0, 9.43])),
     (0.0,
      DenseVector([76.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 201.5, 127.0, 155.2, 91.0, 249.6, 103.0, 8.6, 1.0, 2.0, 18.07])),
     (0.0,
      DenseVector([135.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 191.7, 125.0, 284.2, 100.0, 211.7, 87.0, 12.5, 2.0, 0.0, 26.47])),
     (0.0,
      DenseVector([59.0, 0.0, 0.0, 1.0, 0.0, 1.0, 45.0, 71.2, 113.0, 233.7, 95.0, 139.3, 129.0, 14.0, 12.0, 1.0, 18.57])),
     (0.0,
      DenseVector([111.0, 0.0, 0.0, 1.0, 0.0, 1.0, 33.0, 168.1, 97.0, 204.0, 88.0, 260.9, 97.0, 15.1, 3.0, 1.0, 6.82])),
     (0.0,
      DenseVector([110.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 178.4, 110.0, 179.1, 116.0, 228.1, 99.0, 6.5, 4.0, 1.0, 21.54])),
     (0.0,
      DenseVector([173.0, 1.0, 0.0, 0.0, 1.0, 1.0, 18.0, 127.2, 89.0, 130.7, 114.0, 229.7, 115.0, 11.2, 5.0, 2.0, 21.92])),
     (0.0,
      DenseVector([60.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 230.7, 66.0, 165.4, 65.0, 309.1, 139.0, 13.3, 10.0, 1.0, 14.75])),
     (0.0,
      DenseVector([117.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 228.3, 84.0, 152.6, 92.0, 309.7, 101.0, 3.7, 3.0, 3.0, 8.62])),
     (0.0,
      DenseVector([54.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 197.7, 84.0, 221.3, 89.0, 189.4, 109.0, 7.8, 3.0, 2.0, 6.49])),
     (0.0,
      DenseVector([76.0, 1.0, 0.0, 0.0, 0.0, 1.0, 35.0, 273.7, 94.0, 153.3, 101.0, 259.0, 109.0, 3.8, 3.0, 0.0, 12.68])),
     (0.0,
      DenseVector([167.0, 0.0, 0.0, 1.0, 0.0, 1.0, 30.0, 195.8, 118.0, 166.4, 109.0, 297.4, 84.0, 11.7, 2.0, 0.0, 20.97])),
     (0.0,
      DenseVector([98.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 190.5, 106.0, 164.2, 92.0, 110.8, 88.0, 11.4, 5.0, 2.0, 9.43])),
     (0.0,
      DenseVector([74.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 176.1, 79.0, 208.3, 105.0, 237.5, 86.0, 16.1, 4.0, 4.0, 9.43])),
     (0.0,
      DenseVector([112.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 210.4, 78.0, 172.4, 75.0, 240.0, 105.0, 11.7, 3.0, 2.0, 25.0])),
     (0.0,
      DenseVector([138.0, 1.0, 0.0, 0.0, 0.0, 1.0, 28.0, 151.0, 59.0, 269.5, 122.0, 179.4, 116.0, 8.9, 9.0, 0.0, 20.59])),
     (0.0,
      DenseVector([65.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 144.3, 109.0, 331.3, 89.0, 155.3, 69.0, 11.8, 15.0, 3.0, 10.0])),
     (0.0,
      DenseVector([194.0, 0.0, 1.0, 0.0, 0.0, 1.0, 42.0, 203.7, 109.0, 192.9, 125.0, 318.0, 81.0, 10.8, 16.0, 3.0, 12.7])),
     (1.0,
      DenseVector([131.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 210.1, 97.0, 177.3, 96.0, 130.2, 136.0, 13.3, 1.0, 0.0, 13.56])),
     (0.0,
      DenseVector([54.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 230.2, 89.0, 148.7, 110.0, 255.6, 139.0, 8.1, 3.0, 4.0, 17.78])),
     (0.0,
      DenseVector([78.0, 1.0, 0.0, 0.0, 0.0, 1.0, 27.0, 121.9, 88.0, 225.4, 75.0, 90.8, 118.0, 13.0, 4.0, 3.0, 12.82])),
     (0.0,
      DenseVector([115.0, 0.0, 1.0, 0.0, 0.0, 1.0, 30.0, 181.2, 65.0, 193.2, 62.0, 153.2, 92.0, 9.9, 6.0, 2.0, 18.07])),
     (0.0,
      DenseVector([99.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 201.5, 103.0, 124.2, 120.0, 239.9, 56.0, 11.5, 3.0, 0.0, 12.33])),
     (0.0,
      DenseVector([120.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 146.0, 58.0, 208.7, 103.0, 215.5, 91.0, 11.9, 2.0, 0.0, 26.47])),
     (1.0,
      DenseVector([112.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 105.3, 101.0, 187.2, 92.0, 206.4, 101.0, 11.1, 2.0, 4.0, 26.47])),
     (0.0,
      DenseVector([1.0, 0.0, 0.0, 1.0, 0.0, 1.0, 26.0, 69.7, 84.0, 250.4, 79.0, 230.2, 122.0, 8.3, 3.0, 2.0, 9.68])),
     (0.0,
      DenseVector([94.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 208.1, 72.0, 188.0, 79.0, 162.0, 96.0, 4.6, 5.0, 2.0, 6.49])),
     (0.0,
      DenseVector([94.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 183.1, 109.0, 199.4, 109.0, 214.1, 71.0, 13.9, 4.0, 0.0, 13.89])),
     (1.0,
      DenseVector([178.0, 0.0, 1.0, 0.0, 0.0, 1.0, 32.0, 148.7, 95.0, 198.6, 55.0, 187.0, 113.0, 10.4, 7.0, 6.0, 11.11])),
     (1.0,
      DenseVector([136.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 240.1, 121.0, 271.2, 123.0, 132.7, 98.0, 17.2, 4.0, 0.0, 9.43])),
     (0.0,
      DenseVector([86.0, 1.0, 0.0, 0.0, 0.0, 1.0, 25.0, 126.4, 97.0, 189.7, 118.0, 233.6, 97.0, 12.0, 2.0, 1.0, 9.68])),
     (1.0,
      DenseVector([87.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 156.0, 70.0, 160.5, 89.0, 206.6, 112.0, 7.1, 3.0, 4.0, 11.11])),
     (0.0,
      DenseVector([18.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 180.2, 115.0, 83.4, 114.0, 245.3, 111.0, 11.9, 6.0, 1.0, 18.57])),
     (0.0,
      DenseVector([49.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 172.0, 142.0, 224.0, 100.0, 177.3, 86.0, 7.4, 1.0, 0.0, 13.64])),
     (0.0,
      DenseVector([20.0, 1.0, 0.0, 0.0, 0.0, 1.0, 27.0, 231.6, 105.0, 223.3, 120.0, 212.0, 99.0, 8.9, 4.0, 1.0, 12.82])),
     (0.0,
      DenseVector([131.0, 1.0, 0.0, 0.0, 0.0, 1.0, 29.0, 42.1, 114.0, 183.3, 70.0, 158.7, 82.0, 10.0, 1.0, 0.0, 12.68])),
     (0.0,
      DenseVector([11.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 136.6, 111.0, 80.6, 78.0, 201.7, 77.0, 9.9, 4.0, 0.0, 8.2])),
     (0.0,
      DenseVector([99.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 155.6, 90.0, 240.1, 117.0, 230.8, 88.0, 10.8, 4.0, 3.0, 14.75])),
     (0.0,
      DenseVector([61.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 234.6, 126.0, 200.7, 76.0, 171.1, 81.0, 8.1, 2.0, 0.0, 12.33])),
     (0.0,
      DenseVector([137.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 115.5, 101.0, 226.2, 81.0, 179.1, 123.0, 13.6, 4.0, 0.0, 14.75])),
     (0.0,
      DenseVector([49.0, 0.0, 0.0, 1.0, 0.0, 1.0, 42.0, 159.0, 67.0, 126.3, 125.0, 206.8, 88.0, 7.4, 3.0, 2.0, 21.21])),
     (0.0,
      DenseVector([83.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 286.6, 114.0, 101.0, 138.0, 199.6, 146.0, 13.2, 4.0, 1.0, 16.92])),
     (0.0,
      DenseVector([192.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 174.5, 83.0, 232.7, 102.0, 101.5, 68.0, 12.4, 4.0, 1.0, 16.92])),
     (0.0,
      DenseVector([105.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 181.9, 70.0, 233.5, 83.0, 194.6, 80.0, 8.1, 9.0, 1.0, 12.7])),
     (0.0,
      DenseVector([124.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 192.6, 108.0, 223.3, 106.0, 187.0, 84.0, 4.2, 2.0, 1.0, 20.0])),
     (1.0,
      DenseVector([90.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 235.0, 77.0, 267.3, 94.0, 222.1, 119.0, 5.3, 4.0, 3.0, 20.0])),
     (0.0,
      DenseVector([111.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 187.8, 107.0, 246.0, 70.0, 175.9, 88.0, 10.5, 7.0, 0.0, 10.0])),
     (0.0,
      DenseVector([135.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 167.1, 105.0, 131.6, 120.0, 242.6, 83.0, 10.4, 2.0, 3.0, 26.47])),
     (0.0,
      DenseVector([121.0, 1.0, 0.0, 0.0, 0.0, 1.0, 22.0, 212.3, 107.0, 238.0, 94.0, 332.8, 114.0, 10.4, 2.0, 1.0, 9.23])),
     (0.0,
      DenseVector([57.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 227.4, 94.0, 146.4, 115.0, 211.4, 92.0, 8.3, 5.0, 1.0, 9.68])),
     (1.0,
      DenseVector([63.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 162.5, 86.0, 165.4, 112.0, 163.7, 67.0, 13.4, 6.0, 4.0, 20.59])),
     (0.0,
      DenseVector([97.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 164.2, 97.0, 232.2, 85.0, 198.8, 118.0, 6.5, 3.0, 1.0, 23.33])),
     (0.0,
      DenseVector([93.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 172.4, 133.0, 205.6, 77.0, 245.8, 126.0, 9.1, 9.0, 1.0, 6.49])),
     (0.0,
      DenseVector([97.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 259.6, 138.0, 233.9, 91.0, 140.6, 119.0, 13.0, 4.0, 1.0, 26.47])),
     (0.0,
      DenseVector([134.0, 0.0, 1.0, 0.0, 0.0, 1.0, 10.0, 159.4, 79.0, 91.0, 94.0, 199.5, 137.0, 10.0, 3.0, 2.0, 14.75])),
     (0.0,
      DenseVector([57.0, 0.0, 0.0, 1.0, 0.0, 1.0, 37.0, 69.5, 94.0, 220.9, 76.0, 218.5, 113.0, 11.9, 3.0, 0.0, 20.97])),
     (0.0,
      DenseVector([109.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 166.5, 101.0, 268.4, 119.0, 126.2, 112.0, 10.2, 3.0, 3.0, 14.75])),
     (0.0,
      DenseVector([66.0, 1.0, 0.0, 0.0, 0.0, 1.0, 21.0, 134.4, 110.0, 136.2, 104.0, 215.6, 105.0, 9.7, 4.0, 3.0, 8.62])),
     (0.0,
      DenseVector([162.0, 0.0, 1.0, 0.0, 0.0, 1.0, 21.0, 205.2, 128.0, 231.7, 128.0, 180.3, 100.0, 10.6, 3.0, 2.0, 14.81])),
     (0.0,
      DenseVector([115.0, 1.0, 0.0, 0.0, 0.0, 1.0, 26.0, 132.3, 142.0, 273.4, 89.0, 195.7, 77.0, 14.8, 7.0, 0.0, 16.18])),
     (0.0,
      DenseVector([132.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 140.3, 144.0, 294.8, 89.0, 153.5, 126.0, 11.7, 4.0, 2.0, 5.66])),
     (0.0,
      DenseVector([152.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 284.6, 113.0, 141.8, 101.0, 127.4, 78.0, 12.6, 5.0, 0.0, 14.75])),
     (0.0,
      DenseVector([86.0, 1.0, 0.0, 0.0, 0.0, 1.0, 29.0, 189.3, 107.0, 221.3, 101.0, 325.6, 137.0, 13.8, 3.0, 1.0, 9.26])),
     (0.0,
      DenseVector([36.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 184.6, 132.0, 193.7, 69.0, 283.5, 83.0, 5.9, 3.0, 1.0, 23.33])),
     (0.0,
      DenseVector([118.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 161.6, 96.0, 223.9, 113.0, 240.6, 137.0, 6.8, 2.0, 0.0, 17.86])),
     (0.0,
      DenseVector([137.0, 0.0, 0.0, 1.0, 0.0, 1.0, 20.0, 268.5, 91.0, 128.8, 121.0, 146.4, 98.0, 12.7, 6.0, 1.0, 5.66])),
     (0.0,
      DenseVector([67.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 178.0, 77.0, 233.6, 88.0, 175.6, 88.0, 6.3, 4.0, 2.0, 26.47])),
     (0.0,
      DenseVector([164.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 215.6, 90.0, 171.6, 99.0, 110.4, 105.0, 11.3, 6.0, 1.0, 21.92])),
     (0.0,
      DenseVector([180.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 253.9, 69.0, 184.2, 138.0, 174.3, 83.0, 0.4, 7.0, 1.0, 13.89])),
     (0.0,
      DenseVector([80.0, 0.0, 1.0, 0.0, 0.0, 1.0, 22.0, 182.8, 142.0, 229.3, 111.0, 313.8, 85.0, 11.3, 2.0, 1.0, 6.82])),
     (0.0,
      DenseVector([126.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 120.0, 82.0, 213.6, 111.0, 312.9, 136.0, 7.5, 6.0, 1.0, 13.64])),
     (0.0,
      DenseVector([138.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 138.7, 124.0, 280.3, 64.0, 206.7, 98.0, 10.4, 4.0, 1.0, 21.54])),
     (0.0,
      DenseVector([101.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 189.0, 122.0, 179.2, 91.0, 186.8, 170.0, 11.1, 6.0, 0.0, 16.92])),
     (0.0,
      DenseVector([147.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 134.6, 91.0, 175.1, 83.0, 201.1, 114.0, 8.9, 3.0, 0.0, 18.07])),
     (0.0,
      DenseVector([58.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 181.4, 106.0, 145.8, 77.0, 200.3, 85.0, 7.9, 3.0, 0.0, 6.49])),
     (0.0,
      DenseVector([35.0, 0.0, 0.0, 1.0, 0.0, 1.0, 23.0, 194.6, 106.0, 199.8, 132.0, 189.6, 88.0, 10.1, 7.0, 4.0, 12.68])),
     (0.0,
      DenseVector([163.0, 1.0, 0.0, 0.0, 0.0, 1.0, 39.0, 268.1, 104.0, 216.6, 65.0, 359.9, 118.0, 11.4, 4.0, 0.0, 16.07])),
     (0.0,
      DenseVector([103.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 205.2, 74.0, 200.8, 97.0, 251.1, 94.0, 11.2, 1.0, 1.0, 8.62])),
     (1.0,
      DenseVector([152.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 302.8, 143.0, 189.8, 69.0, 214.9, 114.0, 10.9, 4.0, 2.0, 5.77])),
     (0.0,
      DenseVector([44.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 213.2, 85.0, 224.0, 128.0, 137.8, 74.0, 12.5, 5.0, 1.0, 9.43])),
     (0.0,
      DenseVector([75.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 192.0, 106.0, 140.4, 97.0, 190.1, 94.0, 7.6, 5.0, 2.0, 16.07])),
     (0.0,
      DenseVector([103.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 107.9, 111.0, 166.9, 129.0, 208.5, 78.0, 9.7, 3.0, 1.0, 9.68])),
     (0.0,
      DenseVector([126.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 181.7, 102.0, 216.3, 114.0, 317.9, 83.0, 12.4, 6.0, 1.0, 9.23])),
     (0.0,
      DenseVector([104.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 191.8, 105.0, 148.7, 96.0, 234.8, 123.0, 19.3, 4.0, 1.0, 18.57])),
     (0.0,
      DenseVector([99.0, 0.0, 1.0, 0.0, 0.0, 1.0, 21.0, 180.2, 97.0, 167.7, 47.0, 203.1, 84.0, 14.6, 5.0, 2.0, 20.0])),
     (0.0,
      DenseVector([119.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 215.1, 105.0, 249.4, 104.0, 249.9, 83.0, 10.8, 8.0, 2.0, 16.07])),
     (1.0,
      DenseVector([155.0, 0.0, 0.0, 1.0, 1.0, 1.0, 38.0, 234.6, 84.0, 214.9, 119.0, 196.7, 69.0, 8.0, 2.0, 1.0, 13.56])),
     (0.0,
      DenseVector([51.0, 1.0, 0.0, 0.0, 0.0, 1.0, 43.0, 158.2, 94.0, 213.2, 105.0, 190.4, 91.0, 8.5, 2.0, 2.0, 18.57])),
     (0.0,
      DenseVector([142.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 204.2, 98.0, 188.9, 85.0, 231.2, 136.0, 6.5, 6.0, 1.0, 23.33])),
     (0.0,
      DenseVector([108.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 221.8, 105.0, 108.2, 107.0, 207.1, 87.0, 12.1, 3.0, 2.0, 12.33])),
     (0.0,
      DenseVector([154.0, 1.0, 0.0, 0.0, 0.0, 1.0, 14.0, 183.0, 113.0, 187.0, 144.0, 181.1, 112.0, 10.2, 3.0, 1.0, 12.82])),
     (0.0,
      DenseVector([171.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 221.5, 103.0, 109.5, 58.0, 134.6, 115.0, 11.6, 4.0, 1.0, 10.0])),
     (0.0,
      DenseVector([33.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 228.1, 77.0, 176.3, 102.0, 224.5, 119.0, 7.5, 1.0, 0.0, 18.57])),
     (1.0,
      DenseVector([68.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 197.7, 99.0, 188.8, 106.0, 254.6, 80.0, 19.2, 4.0, 3.0, 16.22])),
     (0.0,
      DenseVector([65.0, 1.0, 0.0, 0.0, 0.0, 1.0, 21.0, 135.1, 120.0, 238.4, 90.0, 286.4, 93.0, 11.0, 9.0, 4.0, 11.69])),
     (0.0,
      DenseVector([95.0, 1.0, 0.0, 0.0, 0.0, 1.0, 35.0, 238.3, 86.0, 258.1, 132.0, 194.9, 72.0, 13.1, 4.0, 5.0, 21.21])),
     (0.0,
      DenseVector([47.0, 1.0, 0.0, 0.0, 0.0, 1.0, 28.0, 196.2, 88.0, 194.8, 106.0, 243.0, 103.0, 10.5, 10.0, 0.0, 12.33])),
     (0.0,
      DenseVector([49.0, 1.0, 0.0, 0.0, 0.0, 1.0, 20.0, 205.9, 109.0, 298.4, 105.0, 142.1, 91.0, 12.3, 3.0, 4.0, 12.68])),
     (0.0,
      DenseVector([104.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 174.1, 97.0, 174.6, 104.0, 182.7, 158.0, 9.3, 4.0, 3.0, 5.77])),
     (0.0,
      DenseVector([173.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 154.6, 81.0, 147.3, 100.0, 132.9, 99.0, 6.9, 5.0, 0.0, 20.0])),
     (0.0,
      DenseVector([98.0, 1.0, 0.0, 0.0, 1.0, 1.0, 20.0, 186.2, 112.0, 145.3, 65.0, 142.8, 104.0, 10.9, 4.0, 3.0, 8.97])),
     (0.0,
      DenseVector([81.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 176.1, 113.0, 162.0, 80.0, 160.9, 83.0, 11.9, 4.0, 1.0, 12.68])),
     (0.0,
      DenseVector([70.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 156.6, 82.0, 193.1, 112.0, 179.8, 108.0, 12.6, 1.0, 1.0, 13.56])),
     (0.0,
      DenseVector([63.0, 1.0, 0.0, 0.0, 0.0, 1.0, 19.0, 163.0, 75.0, 238.4, 103.0, 171.8, 100.0, 11.3, 3.0, 3.0, 6.25])),
     (0.0,
      DenseVector([32.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 103.6, 78.0, 238.5, 115.0, 186.9, 70.0, 11.0, 4.0, 2.0, 12.68])),
     (0.0,
      DenseVector([111.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 162.1, 98.0, 217.6, 87.0, 264.1, 107.0, 7.7, 1.0, 0.0, 16.18])),
     (0.0,
      DenseVector([96.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 149.2, 125.0, 210.6, 119.0, 290.5, 102.0, 9.4, 3.0, 4.0, 24.29])),
     (0.0,
      DenseVector([79.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 187.3, 108.0, 177.1, 72.0, 269.2, 135.0, 9.5, 3.0, 3.0, 9.68])),
     (1.0,
      DenseVector([123.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 222.4, 90.0, 286.3, 104.0, 300.8, 95.0, 9.9, 3.0, 2.0, 12.82])),
     (1.0,
      DenseVector([88.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 82.7, 94.0, 157.5, 130.0, 205.9, 81.0, 10.6, 4.0, 0.0, 12.68])),
     (0.0,
      DenseVector([182.0, 0.0, 1.0, 0.0, 0.0, 1.0, 24.0, 215.2, 73.0, 133.1, 85.0, 205.1, 79.0, 10.9, 7.0, 0.0, 6.49])),
     (0.0,
      DenseVector([84.0, 0.0, 1.0, 0.0, 1.0, 1.0, 18.0, 136.8, 114.0, 200.3, 110.0, 199.0, 86.0, 8.0, 4.0, 3.0, 14.81])),
     (0.0,
      DenseVector([109.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 168.0, 103.0, 190.8, 100.0, 196.9, 65.0, 7.1, 4.0, 0.0, 10.0])),
     (0.0,
      DenseVector([79.0, 1.0, 0.0, 0.0, 0.0, 1.0, 12.0, 62.4, 119.0, 237.6, 96.0, 193.4, 116.0, 7.8, 2.0, 0.0, 6.82])),
     (0.0,
      DenseVector([68.0, 1.0, 0.0, 0.0, 0.0, 1.0, 41.0, 226.0, 113.0, 149.8, 115.0, 184.9, 88.0, 11.5, 2.0, 2.0, 6.49])),
     (0.0,
      DenseVector([129.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 52.2, 93.0, 128.8, 117.0, 141.7, 63.0, 9.4, 12.0, 1.0, 13.33])),
     (0.0,
      DenseVector([70.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 205.7, 80.0, 221.4, 103.0, 206.2, 117.0, 14.2, 5.0, 2.0, 21.21])),
     (0.0,
      DenseVector([44.0, 1.0, 0.0, 0.0, 0.0, 1.0, 20.0, 184.8, 105.0, 195.0, 169.0, 130.1, 121.0, 11.6, 3.0, 1.0, 11.69])),
     (0.0,
      DenseVector([113.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 193.1, 93.0, 206.4, 85.0, 215.9, 102.0, 11.1, 2.0, 1.0, 16.92])),
     (0.0,
      DenseVector([38.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 228.3, 129.0, 141.9, 83.0, 268.6, 116.0, 9.7, 3.0, 2.0, 26.47])),
     (0.0,
      DenseVector([86.0, 0.0, 0.0, 1.0, 1.0, 1.0, 41.0, 225.5, 95.0, 229.3, 101.0, 134.0, 82.0, 11.6, 10.0, 2.0, 13.89])),
     (0.0,
      DenseVector([170.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 215.4, 94.0, 138.2, 97.0, 237.1, 117.0, 12.5, 5.0, 1.0, 9.68])),
     (0.0,
      DenseVector([59.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 163.5, 107.0, 187.1, 91.0, 179.1, 100.0, 10.3, 1.0, 2.0, 16.07])),
     (0.0,
      DenseVector([112.0, 0.0, 0.0, 1.0, 0.0, 1.0, 22.0, 244.7, 104.0, 203.1, 88.0, 222.0, 84.0, 7.4, 6.0, 0.0, 26.47])),
     (0.0,
      DenseVector([152.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 216.7, 70.0, 152.7, 102.0, 181.2, 99.0, 11.6, 6.0, 1.0, 16.92])),
     (0.0,
      DenseVector([108.0, 1.0, 0.0, 0.0, 0.0, 1.0, 15.0, 196.8, 79.0, 173.5, 99.0, 274.7, 108.0, 14.6, 5.0, 1.0, 14.81])),
     (0.0,
      DenseVector([91.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 197.3, 120.0, 202.4, 89.0, 290.5, 128.0, 15.3, 7.0, 1.0, 5.77])),
     (0.0,
      DenseVector([57.0, 1.0, 0.0, 0.0, 0.0, 1.0, 23.0, 208.7, 57.0, 233.4, 132.0, 108.8, 117.0, 9.1, 5.0, 1.0, 20.0])),
     (0.0,
      DenseVector([112.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 168.4, 53.0, 187.5, 68.0, 204.5, 125.0, 9.8, 3.0, 1.0, 9.43])),
     (0.0,
      DenseVector([100.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 129.4, 69.0, 251.3, 73.0, 157.1, 92.0, 10.4, 3.0, 1.0, 14.1])),
     (0.0,
      DenseVector([42.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 260.9, 94.0, 152.8, 123.0, 212.6, 94.0, 11.1, 4.0, 1.0, 12.33])),
     (1.0,
      DenseVector([68.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 267.9, 80.0, 145.4, 143.0, 273.3, 119.0, 14.9, 1.0, 3.0, 18.07])),
     (0.0,
      DenseVector([132.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 172.7, 90.0, 344.0, 62.0, 301.6, 113.0, 5.5, 4.0, 0.0, 13.56])),
     (0.0,
      DenseVector([137.0, 0.0, 1.0, 0.0, 1.0, 0.0, 0.0, 159.1, 80.0, 239.8, 81.0, 249.0, 96.0, 8.5, 5.0, 0.0, 9.23])),
     (0.0,
      DenseVector([42.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 166.8, 138.0, 212.4, 78.0, 194.7, 70.0, 12.4, 9.0, 2.0, 12.33])),
     (0.0,
      DenseVector([81.0, 1.0, 0.0, 0.0, 0.0, 1.0, 17.0, 145.7, 89.0, 202.9, 70.0, 212.4, 92.0, 9.5, 5.0, 1.0, 16.07])),
     (0.0,
      DenseVector([135.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 129.4, 104.0, 199.0, 96.0, 142.4, 81.0, 12.3, 4.0, 1.0, 13.64])),
     (0.0,
      DenseVector([102.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 171.5, 77.0, 261.0, 107.0, 328.8, 72.0, 10.2, 1.0, 0.0, 6.82])),
     (1.0,
      DenseVector([117.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 180.6, 134.0, 216.6, 96.0, 101.0, 122.0, 10.6, 6.0, 4.0, 20.59])),
     (0.0,
      DenseVector([37.0, 0.0, 0.0, 1.0, 0.0, 1.0, 33.0, 225.7, 133.0, 223.9, 115.0, 192.7, 131.0, 11.2, 5.0, 3.0, 11.69])),
     (0.0,
      DenseVector([72.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 153.8, 64.0, 304.4, 106.0, 185.0, 125.0, 8.4, 5.0, 1.0, 5.66])),
     (0.0,
      DenseVector([48.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 176.9, 110.0, 274.3, 114.0, 115.3, 125.0, 11.0, 2.0, 1.0, 8.97])),
     (0.0,
      DenseVector([155.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 250.5, 89.0, 146.1, 84.0, 164.2, 60.0, 8.4, 3.0, 1.0, 14.75])),
     (1.0,
      DenseVector([124.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 282.8, 108.0, 246.3, 89.0, 256.4, 96.0, 10.3, 1.0, 3.0, 21.21])),
     (0.0,
      DenseVector([91.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 142.6, 90.0, 286.8, 102.0, 222.2, 113.0, 14.8, 3.0, 0.0, 13.89])),
     (0.0,
      DenseVector([169.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 276.1, 120.0, 123.5, 88.0, 261.9, 116.0, 11.9, 5.0, 1.0, 13.56])),
     (0.0,
      DenseVector([74.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 154.3, 71.0, 262.3, 105.0, 235.3, 99.0, 13.9, 4.0, 0.0, 16.07])),
     (0.0,
      DenseVector([101.0, 0.0, 1.0, 0.0, 1.0, 1.0, 35.0, 181.0, 88.0, 222.5, 113.0, 217.8, 103.0, 11.1, 3.0, 1.0, 9.23])),
     (0.0,
      DenseVector([142.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 108.1, 81.0, 146.9, 115.0, 197.6, 84.0, 9.0, 7.0, 1.0, 26.47])),
     (0.0,
      DenseVector([61.0, 1.0, 0.0, 0.0, 0.0, 1.0, 31.0, 252.1, 98.0, 215.7, 149.0, 135.7, 117.0, 5.1, 4.0, 2.0, 17.78])),
     (1.0,
      DenseVector([50.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 160.1, 64.0, 208.7, 123.0, 199.7, 84.0, 7.6, 12.0, 5.0, 16.92])),
     (0.0,
      DenseVector([82.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 217.6, 82.0, 169.4, 101.0, 248.9, 124.0, 12.7, 3.0, 0.0, 8.97])),
     (0.0,
      DenseVector([142.0, 1.0, 0.0, 0.0, 0.0, 1.0, 22.0, 198.3, 144.0, 157.2, 96.0, 180.9, 114.0, 11.1, 4.0, 2.0, 20.59])),
     (0.0,
      DenseVector([27.0, 1.0, 0.0, 0.0, 0.0, 1.0, 32.0, 190.3, 97.0, 279.0, 77.0, 133.8, 117.0, 8.4, 3.0, 2.0, 14.81])),
     (0.0,
      DenseVector([165.0, 0.0, 1.0, 0.0, 0.0, 1.0, 23.0, 184.7, 63.0, 168.0, 62.0, 176.1, 125.0, 8.7, 5.0, 1.0, 6.25])),
     (1.0,
      DenseVector([111.0, 0.0, 1.0, 0.0, 1.0, 0.0, 0.0, 159.4, 47.0, 269.3, 53.0, 179.8, 103.0, 13.7, 2.0, 2.0, 14.81])),
     (0.0,
      DenseVector([33.0, 0.0, 0.0, 1.0, 0.0, 1.0, 16.0, 125.1, 56.0, 246.6, 140.0, 235.4, 109.0, 12.1, 2.0, 0.0, 20.97])),
     (0.0,
      DenseVector([55.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 181.3, 100.0, 200.5, 122.0, 226.9, 99.0, 10.9, 2.0, 1.0, 20.97])),
     (0.0,
      DenseVector([96.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 99.8, 101.0, 253.6, 83.0, 112.5, 75.0, 7.5, 6.0, 0.0, 13.89])),
     (0.0,
      DenseVector([115.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 254.8, 97.0, 153.5, 110.0, 217.6, 117.0, 9.0, 4.0, 1.0, 24.29])),
     (1.0,
      DenseVector([65.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 244.5, 72.0, 206.2, 88.0, 299.8, 110.0, 7.6, 1.0, 5.0, 9.26])),
     (0.0,
      DenseVector([50.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 167.7, 74.0, 179.9, 98.0, 166.5, 73.0, 10.9, 2.0, 1.0, 12.82])),
     (0.0,
      DenseVector([137.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 79.5, 121.0, 164.1, 86.0, 121.8, 108.0, 11.7, 7.0, 2.0, 11.11])),
     (1.0,
      DenseVector([95.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 133.0, 94.0, 196.6, 126.0, 181.0, 128.0, 9.9, 3.0, 4.0, 12.33])),
     (1.0,
      DenseVector([118.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 297.2, 113.0, 188.8, 114.0, 188.9, 100.0, 12.4, 6.0, 1.0, 21.21])),
     (0.0,
      DenseVector([84.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 214.9, 72.0, 197.7, 92.0, 153.1, 79.0, 11.9, 2.0, 2.0, 18.07])),
     (0.0,
      DenseVector([42.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 269.9, 99.0, 37.8, 76.0, 166.6, 139.0, 13.8, 1.0, 1.0, 26.47])),
     (0.0,
      DenseVector([53.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 143.8, 102.0, 199.6, 124.0, 177.8, 65.0, 7.8, 4.0, 1.0, 9.68])),
     (1.0,
      DenseVector([123.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 132.2, 122.0, 169.9, 101.0, 150.1, 123.0, 12.9, 11.0, 3.0, 12.33])),
     (0.0,
      DenseVector([60.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 162.8, 117.0, 163.2, 121.0, 242.4, 140.0, 14.2, 2.0, 1.0, 11.11])),
     (1.0,
      DenseVector([112.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 351.5, 95.0, 206.8, 108.0, 275.8, 146.0, 11.9, 4.0, 1.0, 26.47])),
     (0.0,
      DenseVector([43.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 165.8, 96.0, 132.0, 105.0, 323.2, 62.0, 10.7, 6.0, 0.0, 16.18])),
     (0.0,
      DenseVector([150.0, 1.0, 0.0, 0.0, 0.0, 1.0, 24.0, 195.7, 108.0, 208.8, 110.0, 177.0, 84.0, 11.7, 2.0, 2.0, 13.56])),
     (0.0,
      DenseVector([92.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 250.6, 100.0, 41.7, 94.0, 186.2, 100.0, 9.7, 6.0, 2.0, 16.22])),
     (0.0,
      DenseVector([68.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 141.9, 112.0, 166.2, 94.0, 223.5, 77.0, 9.2, 6.0, 1.0, 18.07])),
     (0.0,
      DenseVector([159.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 168.5, 80.0, 203.9, 119.0, 199.1, 126.0, 8.1, 2.0, 1.0, 12.82])),
     (0.0,
      DenseVector([158.0, 1.0, 0.0, 0.0, 0.0, 1.0, 19.0, 127.6, 88.0, 235.2, 82.0, 195.0, 87.0, 10.1, 3.0, 0.0, 8.62])),
     (0.0,
      DenseVector([119.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 197.2, 111.0, 168.1, 78.0, 209.8, 84.0, 4.2, 2.0, 1.0, 8.62])),
     (0.0,
      DenseVector([54.0, 1.0, 0.0, 0.0, 0.0, 1.0, 28.0, 217.4, 98.0, 246.2, 120.0, 127.0, 92.0, 7.5, 8.0, 1.0, 21.21])),
     (1.0,
      DenseVector([60.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 97.2, 91.0, 243.7, 93.0, 235.0, 116.0, 10.1, 2.0, 1.0, 26.47])),
     (0.0,
      DenseVector([111.0, 1.0, 0.0, 0.0, 0.0, 1.0, 23.0, 87.7, 91.0, 218.3, 118.0, 173.1, 87.0, 12.3, 4.0, 1.0, 8.97])),
     (0.0,
      DenseVector([119.0, 1.0, 0.0, 0.0, 0.0, 1.0, 32.0, 186.4, 75.0, 206.9, 92.0, 150.6, 80.0, 12.1, 2.0, 1.0, 9.43])),
     (0.0,
      DenseVector([74.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 180.0, 110.0, 212.9, 100.0, 182.3, 95.0, 11.8, 10.0, 0.0, 10.0])),
     (0.0,
      DenseVector([118.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 232.4, 92.0, 191.0, 68.0, 213.0, 138.0, 7.7, 4.0, 1.0, 11.69])),
     (0.0,
      DenseVector([124.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 151.6, 119.0, 198.1, 93.0, 173.4, 91.0, 11.6, 3.0, 3.0, 20.97])),
     (0.0,
      DenseVector([133.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 172.2, 81.0, 214.9, 123.0, 184.3, 90.0, 11.1, 6.0, 3.0, 7.84])),
     (0.0,
      DenseVector([49.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 260.9, 88.0, 174.2, 103.0, 262.8, 98.0, 10.6, 4.0, 1.0, 8.97])),
     (0.0,
      DenseVector([112.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 124.4, 109.0, 196.1, 119.0, 166.1, 89.0, 10.2, 4.0, 2.0, 16.07])),
     (0.0,
      DenseVector([130.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 126.8, 101.0, 144.1, 105.0, 249.6, 117.0, 11.4, 8.0, 2.0, 5.66])),
     (0.0,
      DenseVector([130.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 110.0, 107.0, 181.9, 85.0, 208.0, 94.0, 11.7, 3.0, 1.0, 9.43])),
     (0.0,
      DenseVector([188.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 231.1, 92.0, 182.4, 93.0, 227.2, 89.0, 9.8, 5.0, 4.0, 13.89])),
     (0.0,
      DenseVector([174.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 187.4, 74.0, 144.6, 142.0, 235.7, 102.0, 10.2, 1.0, 1.0, 5.66])),
     (0.0,
      DenseVector([57.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 166.4, 114.0, 76.8, 119.0, 260.1, 77.0, 10.9, 3.0, 3.0, 10.96])),
     (0.0,
      DenseVector([98.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 172.5, 118.0, 198.5, 107.0, 230.2, 105.0, 12.6, 3.0, 1.0, 25.0])),
     (0.0,
      DenseVector([105.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 141.8, 85.0, 191.3, 101.0, 133.6, 126.0, 10.9, 3.0, 1.0, 24.29])),
     (1.0,
      DenseVector([175.0, 0.0, 1.0, 0.0, 1.0, 1.0, 35.0, 159.5, 95.0, 140.7, 100.0, 176.3, 99.0, 5.4, 4.0, 5.0, 9.43])),
     (0.0,
      DenseVector([100.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 218.9, 124.0, 237.5, 99.0, 189.7, 52.0, 11.0, 6.0, 2.0, 14.81])),
     (0.0,
      DenseVector([132.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 138.7, 101.0, 257.5, 85.0, 165.6, 92.0, 7.8, 1.0, 2.0, 16.92])),
     (1.0,
      DenseVector([117.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 158.1, 101.0, 315.1, 96.0, 176.1, 108.0, 13.9, 2.0, 3.0, 9.68])),
     (0.0,
      DenseVector([65.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 97.9, 138.0, 173.0, 102.0, 262.2, 92.0, 9.2, 4.0, 1.0, 26.47])),
     (1.0,
      DenseVector([131.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 103.5, 105.0, 174.1, 103.0, 248.2, 95.0, 11.7, 2.0, 5.0, 16.07])),
     (0.0,
      DenseVector([114.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 206.3, 113.0, 223.5, 97.0, 234.3, 119.0, 8.1, 6.0, 2.0, 26.47])),
     (0.0,
      DenseVector([127.0, 1.0, 0.0, 0.0, 0.0, 1.0, 24.0, 211.5, 121.0, 183.9, 115.0, 186.2, 103.0, 13.5, 1.0, 3.0, 12.7])),
     (0.0,
      DenseVector([94.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 229.2, 103.0, 163.9, 133.0, 190.1, 67.0, 7.4, 6.0, 3.0, 23.33])),
     (1.0,
      DenseVector([67.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 125.0, 96.0, 294.5, 114.0, 205.7, 75.0, 10.2, 4.0, 1.0, 11.69])),
     (0.0,
      DenseVector([123.0, 0.0, 1.0, 0.0, 0.0, 1.0, 41.0, 141.3, 127.0, 152.0, 96.0, 218.8, 114.0, 8.1, 3.0, 3.0, 25.0])),
     (0.0,
      DenseVector([116.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 119.0, 94.0, 144.3, 110.0, 199.7, 65.0, 8.8, 5.0, 2.0, 16.92])),
     (1.0,
      DenseVector([107.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 260.6, 81.0, 246.1, 116.0, 243.3, 117.0, 12.8, 9.0, 0.0, 12.33])),
     (0.0,
      DenseVector([110.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 149.2, 128.0, 112.8, 101.0, 156.3, 111.0, 12.2, 4.0, 1.0, 25.0])),
     (0.0,
      DenseVector([29.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 163.7, 109.0, 207.3, 88.0, 266.2, 101.0, 10.4, 2.0, 1.0, 14.75])),
     (0.0,
      DenseVector([76.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 223.0, 109.0, 218.7, 97.0, 138.6, 126.0, 11.6, 8.0, 2.0, 18.07])),
     (0.0,
      DenseVector([115.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 200.2, 115.0, 268.8, 109.0, 185.4, 102.0, 12.8, 2.0, 3.0, 20.97])),
     (0.0,
      DenseVector([68.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 248.6, 59.0, 199.1, 126.0, 112.5, 117.0, 6.5, 3.0, 2.0, 9.68])),
     (0.0,
      DenseVector([63.0, 1.0, 0.0, 0.0, 0.0, 1.0, 26.0, 214.2, 80.0, 253.0, 152.0, 156.4, 109.0, 10.3, 2.0, 2.0, 8.62])),
     (0.0,
      DenseVector([69.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 245.6, 128.0, 173.0, 101.0, 111.8, 118.0, 14.2, 7.0, 2.0, 7.84])),
     (0.0,
      DenseVector([97.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 238.6, 85.0, 203.3, 75.0, 180.5, 127.0, 16.2, 8.0, 0.0, 9.43])),
     (0.0,
      DenseVector([108.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 86.1, 97.0, 227.9, 101.0, 225.8, 102.0, 9.1, 3.0, 3.0, 21.92])),
     (0.0,
      DenseVector([79.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 161.6, 94.0, 187.5, 110.0, 220.8, 71.0, 10.3, 7.0, 2.0, 21.54])),
     (1.0,
      DenseVector([52.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 257.9, 80.0, 220.7, 82.0, 225.4, 70.0, 16.8, 3.0, 2.0, 14.75])),
     (0.0,
      DenseVector([114.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 209.2, 127.0, 165.3, 93.0, 196.8, 98.0, 10.6, 8.0, 3.0, 18.57])),
     (0.0,
      DenseVector([90.0, 0.0, 0.0, 1.0, 0.0, 1.0, 19.0, 214.0, 57.0, 245.3, 105.0, 205.6, 68.0, 10.3, 2.0, 1.0, 9.23])),
     (0.0,
      DenseVector([82.0, 1.0, 0.0, 0.0, 0.0, 1.0, 33.0, 247.4, 106.0, 167.1, 110.0, 207.6, 113.0, 6.8, 1.0, 5.0, 26.47])),
     (0.0,
      DenseVector([122.0, 1.0, 0.0, 0.0, 0.0, 1.0, 21.0, 196.0, 104.0, 194.7, 73.0, 150.8, 92.0, 9.6, 4.0, 0.0, 8.97])),
     (0.0,
      DenseVector([73.0, 1.0, 0.0, 0.0, 0.0, 1.0, 33.0, 129.8, 112.0, 225.3, 96.0, 262.5, 104.0, 8.6, 3.0, 1.0, 20.59])),
     (0.0,
      DenseVector([100.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 239.1, 96.0, 180.1, 110.0, 259.2, 112.0, 14.0, 2.0, 0.0, 9.43])),
     (1.0,
      DenseVector([124.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 133.2, 80.0, 144.2, 98.0, 261.0, 102.0, 12.0, 3.0, 3.0, 8.62])),
     (0.0,
      DenseVector([112.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 135.7, 34.0, 243.0, 99.0, 209.3, 115.0, 12.2, 8.0, 1.0, 14.1])),
     (0.0,
      DenseVector([120.0, 1.0, 0.0, 0.0, 0.0, 1.0, 31.0, 211.0, 117.0, 238.7, 77.0, 184.5, 99.0, 14.0, 8.0, 1.0, 17.86])),
     (0.0,
      DenseVector([95.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 135.2, 93.0, 285.9, 123.0, 228.1, 112.0, 10.0, 2.0, 3.0, 25.0])),
     (0.0,
      DenseVector([102.0, 0.0, 0.0, 1.0, 0.0, 1.0, 29.0, 158.2, 132.0, 250.6, 47.0, 219.3, 86.0, 15.1, 4.0, 1.0, 6.25])),
     (1.0,
      DenseVector([61.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 140.8, 126.0, 212.5, 115.0, 141.7, 79.0, 6.4, 5.0, 4.0, 7.84])),
     (0.0,
      DenseVector([42.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 160.2, 103.0, 159.8, 116.0, 255.7, 87.0, 12.9, 3.0, 1.0, 10.0])),
     (0.0,
      DenseVector([104.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 153.7, 86.0, 160.3, 78.0, 176.6, 140.0, 9.6, 8.0, 1.0, 24.29])),
     (0.0,
      DenseVector([184.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 237.6, 86.0, 157.0, 105.0, 163.6, 103.0, 14.2, 2.0, 2.0, 9.68])),
     (0.0,
      DenseVector([29.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 182.1, 92.0, 180.4, 91.0, 206.4, 128.0, 7.2, 2.0, 2.0, 9.26])),
     (0.0,
      DenseVector([5.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 198.7, 108.0, 146.7, 86.0, 216.8, 95.0, 9.5, 3.0, 2.0, 24.29])),
     (0.0,
      DenseVector([58.0, 0.0, 1.0, 0.0, 0.0, 1.0, 47.0, 151.1, 117.0, 137.4, 103.0, 172.6, 99.0, 9.2, 8.0, 1.0, 16.92])),
     (0.0,
      DenseVector([103.0, 1.0, 0.0, 0.0, 0.0, 1.0, 22.0, 219.0, 126.0, 311.9, 126.0, 197.0, 112.0, 9.0, 8.0, 3.0, 20.0])),
     (0.0,
      DenseVector([165.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 195.2, 84.0, 229.5, 116.0, 185.3, 147.0, 11.9, 3.0, 0.0, 9.26])),
     (1.0,
      DenseVector([77.0, 0.0, 1.0, 0.0, 1.0, 1.0, 34.0, 209.9, 79.0, 226.1, 88.0, 101.8, 103.0, 11.4, 2.0, 2.0, 26.47])),
     (0.0,
      DenseVector([156.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 81.7, 133.0, 242.5, 117.0, 203.8, 112.0, 12.3, 6.0, 3.0, 24.29])),
     (0.0,
      DenseVector([132.0, 1.0, 0.0, 0.0, 1.0, 1.0, 18.0, 130.7, 86.0, 186.1, 98.0, 204.8, 103.0, 11.9, 6.0, 0.0, 9.23])),
     (0.0,
      DenseVector([115.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 265.9, 112.0, 132.9, 94.0, 185.2, 106.0, 14.6, 7.0, 1.0, 10.0])),
     (0.0,
      DenseVector([109.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 186.5, 106.0, 190.3, 103.0, 168.5, 128.0, 10.5, 6.0, 2.0, 21.54])),
     (1.0,
      DenseVector([117.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 185.6, 115.0, 174.8, 83.0, 188.2, 122.0, 6.7, 4.0, 4.0, 26.47])),
     (0.0,
      DenseVector([148.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 249.7, 91.0, 185.1, 90.0, 234.2, 88.0, 10.5, 1.0, 1.0, 25.0])),
     (0.0,
      DenseVector([135.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 204.1, 116.0, 150.2, 74.0, 108.1, 154.0, 8.1, 1.0, 2.0, 21.54])),
     (0.0,
      DenseVector([87.0, 0.0, 0.0, 1.0, 0.0, 1.0, 24.0, 135.8, 108.0, 251.4, 62.0, 181.5, 91.0, 6.4, 4.0, 0.0, 8.97])),
     (0.0,
      DenseVector([26.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 83.2, 93.0, 199.5, 64.0, 242.6, 112.0, 10.7, 4.0, 1.0, 14.81])),
     (0.0,
      DenseVector([78.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 211.4, 105.0, 249.9, 77.0, 199.6, 129.0, 9.3, 2.0, 2.0, 20.97])),
     (0.0,
      DenseVector([78.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 185.8, 102.0, 251.6, 88.0, 202.8, 86.0, 13.3, 6.0, 2.0, 20.59])),
     (0.0,
      DenseVector([121.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 123.6, 113.0, 229.4, 85.0, 242.9, 97.0, 10.3, 4.0, 0.0, 20.0])),
     (0.0,
      DenseVector([101.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 202.5, 91.0, 142.5, 134.0, 219.6, 102.0, 10.1, 4.0, 3.0, 12.33])),
     (0.0,
      DenseVector([90.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 169.4, 127.0, 157.2, 111.0, 172.4, 103.0, 10.7, 2.0, 1.0, 9.68])),
     (0.0,
      DenseVector([83.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 135.2, 76.0, 161.0, 98.0, 129.3, 105.0, 9.0, 6.0, 0.0, 6.25])),
     (0.0,
      DenseVector([81.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 125.1, 103.0, 209.6, 103.0, 186.2, 78.0, 11.8, 1.0, 1.0, 26.47])),
     (1.0,
      DenseVector([119.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 287.1, 83.0, 218.4, 124.0, 151.5, 42.0, 11.0, 4.0, 1.0, 26.47])),
     (0.0,
      DenseVector([134.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 199.7, 107.0, 205.8, 86.0, 198.8, 125.0, 8.3, 5.0, 0.0, 21.21])),
     (0.0,
      DenseVector([48.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 247.6, 114.0, 172.3, 110.0, 264.4, 107.0, 7.1, 3.0, 1.0, 21.21])),
     (0.0,
      DenseVector([185.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 158.5, 93.0, 294.7, 69.0, 146.2, 116.0, 16.6, 3.0, 0.0, 10.0])),
     (0.0,
      DenseVector([91.0, 0.0, 0.0, 1.0, 0.0, 1.0, 39.0, 160.8, 97.0, 119.3, 54.0, 147.9, 101.0, 10.3, 3.0, 3.0, 10.0])),
     (0.0,
      DenseVector([90.0, 0.0, 1.0, 0.0, 0.0, 1.0, 31.0, 118.5, 92.0, 257.9, 76.0, 186.2, 96.0, 9.0, 6.0, 1.0, 23.33])),
     (0.0,
      DenseVector([159.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 181.0, 107.0, 188.4, 114.0, 175.8, 41.0, 9.9, 4.0, 7.0, 9.43])),
     (0.0,
      DenseVector([103.0, 0.0, 0.0, 1.0, 0.0, 1.0, 40.0, 140.3, 52.0, 245.6, 114.0, 111.7, 86.0, 11.1, 6.0, 1.0, 13.64])),
     (1.0,
      DenseVector([142.0, 0.0, 1.0, 0.0, 1.0, 0.0, 0.0, 160.6, 101.0, 202.0, 125.0, 221.7, 100.0, 8.8, 2.0, 1.0, 12.82])),
     (0.0,
      DenseVector([104.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 111.9, 105.0, 197.7, 121.0, 302.8, 108.0, 10.1, 5.0, 1.0, 25.0])),
     (0.0,
      DenseVector([82.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 120.5, 107.0, 176.3, 124.0, 258.0, 121.0, 10.5, 5.0, 0.0, 20.97])),
     (0.0,
      DenseVector([76.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 156.3, 117.0, 115.7, 53.0, 316.4, 121.0, 6.8, 4.0, 1.0, 7.84])),
     (0.0,
      DenseVector([88.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 242.4, 89.0, 161.4, 89.0, 142.4, 95.0, 5.4, 2.0, 1.0, 20.59])),
     (0.0,
      DenseVector([118.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 178.4, 81.0, 182.7, 119.0, 251.4, 109.0, 10.8, 2.0, 1.0, 26.47])),
     (1.0,
      DenseVector([139.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 258.7, 94.0, 190.3, 132.0, 247.2, 73.0, 11.4, 2.0, 1.0, 13.89])),
     (0.0,
      DenseVector([87.0, 1.0, 0.0, 0.0, 0.0, 1.0, 18.0, 124.3, 104.0, 196.1, 80.0, 290.0, 110.0, 7.2, 3.0, 1.0, 9.26])),
     (0.0,
      DenseVector([167.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 94.9, 114.0, 315.8, 105.0, 181.0, 71.0, 10.8, 6.0, 0.0, 13.89])),
     (0.0,
      DenseVector([138.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 182.0, 101.0, 200.3, 119.0, 208.9, 131.0, 13.4, 3.0, 4.0, 13.64])),
     (0.0,
      DenseVector([130.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 162.0, 62.0, 278.1, 108.0, 197.5, 82.0, 9.1, 4.0, 1.0, 16.92])),
     (0.0,
      DenseVector([87.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 176.6, 83.0, 221.6, 101.0, 185.3, 89.0, 9.4, 1.0, 0.0, 12.7])),
     (0.0,
      DenseVector([69.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 198.8, 141.0, 203.7, 110.0, 177.1, 109.0, 15.2, 2.0, 4.0, 9.23])),
     (0.0,
      DenseVector([164.0, 0.0, 1.0, 0.0, 1.0, 1.0, 28.0, 156.1, 98.0, 209.9, 140.0, 190.0, 69.0, 11.4, 4.0, 0.0, 12.33])),
     (0.0,
      DenseVector([109.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 150.6, 106.0, 147.4, 108.0, 173.2, 109.0, 9.5, 5.0, 1.0, 13.64])),
     (0.0,
      DenseVector([53.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 198.3, 100.0, 304.2, 93.0, 131.5, 85.0, 9.2, 5.0, 2.0, 21.54])),
     (0.0,
      DenseVector([59.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 189.1, 141.0, 198.2, 113.0, 280.2, 103.0, 2.2, 7.0, 1.0, 17.78])),
     (0.0,
      DenseVector([124.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 156.9, 74.0, 195.8, 82.0, 181.0, 99.0, 8.8, 2.0, 1.0, 13.56])),
     (0.0,
      DenseVector([73.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 67.0, 76.0, 223.0, 154.0, 236.5, 151.0, 11.2, 2.0, 1.0, 12.33])),
     (0.0,
      DenseVector([94.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 261.3, 89.0, 152.4, 139.0, 120.2, 124.0, 10.5, 2.0, 0.0, 5.77])),
     (0.0,
      DenseVector([80.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 135.1, 92.0, 185.0, 103.0, 147.1, 74.0, 14.7, 5.0, 3.0, 10.0])),
     (0.0,
      DenseVector([101.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 92.1, 109.0, 163.0, 83.0, 133.3, 102.0, 9.7, 11.0, 2.0, 23.33])),
     (0.0,
      DenseVector([115.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 173.5, 121.0, 168.3, 100.0, 138.3, 105.0, 7.8, 5.0, 1.0, 17.86])),
     (0.0,
      DenseVector([100.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 156.3, 82.0, 259.4, 91.0, 143.7, 93.0, 8.3, 7.0, 5.0, 18.57])),
     (0.0,
      DenseVector([85.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 274.7, 79.0, 119.4, 122.0, 296.9, 74.0, 9.4, 10.0, 1.0, 16.92])),
     (1.0,
      DenseVector([110.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 244.7, 84.0, 106.4, 95.0, 194.9, 61.0, 10.5, 1.0, 2.0, 13.56])),
     (0.0,
      DenseVector([92.0, 0.0, 1.0, 0.0, 1.0, 0.0, 0.0, 183.6, 96.0, 149.7, 55.0, 135.2, 71.0, 11.8, 4.0, 0.0, 8.97])),
     (0.0,
      DenseVector([102.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 167.1, 80.0, 222.9, 94.0, 179.9, 103.0, 11.6, 3.0, 5.0, 21.92])),
     (0.0,
      DenseVector([134.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 177.1, 120.0, 218.1, 123.0, 183.8, 93.0, 12.7, 5.0, 3.0, 21.21])),
     (0.0,
      DenseVector([96.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 217.8, 120.0, 139.6, 109.0, 227.7, 111.0, 11.6, 3.0, 0.0, 9.26])),
     (0.0,
      DenseVector([42.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 143.7, 96.0, 221.0, 98.0, 108.9, 94.0, 8.6, 1.0, 2.0, 8.2])),
     (0.0,
      DenseVector([175.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 185.0, 104.0, 325.8, 86.0, 85.4, 105.0, 14.6, 2.0, 1.0, 14.1])),
     (0.0,
      DenseVector([136.0, 1.0, 0.0, 0.0, 0.0, 1.0, 23.0, 235.6, 65.0, 177.7, 81.0, 138.7, 105.0, 11.3, 5.0, 1.0, 14.1])),
     (1.0,
      DenseVector([32.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 265.5, 75.0, 119.7, 77.0, 169.9, 128.0, 14.8, 6.0, 2.0, 20.0])),
     (1.0,
      DenseVector([101.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 332.1, 91.0, 151.0, 123.0, 277.8, 99.0, 11.3, 3.0, 1.0, 13.89])),
     (0.0,
      DenseVector([132.0, 0.0, 1.0, 0.0, 1.0, 1.0, 21.0, 223.4, 89.0, 193.2, 105.0, 226.2, 85.0, 7.7, 5.0, 3.0, 6.25])),
     (0.0,
      DenseVector([120.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 163.6, 109.0, 237.3, 95.0, 186.2, 141.0, 12.3, 5.0, 0.0, 20.97])),
     (0.0,
      DenseVector([122.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 190.0, 125.0, 141.5, 77.0, 216.8, 68.0, 15.9, 3.0, 3.0, 20.59])),
     (0.0,
      DenseVector([52.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 153.7, 100.0, 174.2, 139.0, 242.3, 53.0, 14.1, 3.0, 1.0, 14.75])),
     (0.0,
      DenseVector([182.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 160.3, 87.0, 229.9, 101.0, 185.3, 96.0, 8.7, 11.0, 2.0, 10.96])),
     (0.0,
      DenseVector([138.0, 0.0, 1.0, 0.0, 0.0, 1.0, 12.0, 114.5, 100.0, 175.1, 130.0, 112.7, 85.0, 7.8, 5.0, 0.0, 9.68])),
     (0.0,
      DenseVector([130.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 140.9, 68.0, 217.5, 109.0, 123.6, 96.0, 13.6, 4.0, 3.0, 12.33])),
     (0.0,
      DenseVector([106.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 168.4, 85.0, 123.3, 95.0, 162.5, 83.0, 4.6, 4.0, 3.0, 23.33])),
     (1.0,
      DenseVector([150.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 150.9, 92.0, 191.0, 106.0, 223.6, 107.0, 8.3, 4.0, 5.0, 16.92])),
     (1.0,
      DenseVector([94.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 255.1, 96.0, 276.9, 67.0, 226.5, 74.0, 10.6, 2.0, 0.0, 14.75])),
     (0.0,
      DenseVector([116.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 154.0, 127.0, 280.4, 68.0, 213.0, 56.0, 8.6, 2.0, 6.0, 26.47])),
     (0.0,
      DenseVector([89.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 94.5, 107.0, 191.8, 85.0, 196.2, 131.0, 15.0, 5.0, 1.0, 13.64])),
     (0.0,
      DenseVector([45.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 135.0, 116.0, 159.0, 108.0, 170.3, 130.0, 11.1, 15.0, 1.0, 21.21])),
     (1.0,
      DenseVector([74.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 286.9, 113.0, 260.8, 121.0, 137.1, 94.0, 12.6, 6.0, 0.0, 18.57])),
     (0.0,
      DenseVector([158.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 165.4, 70.0, 280.1, 104.0, 121.8, 106.0, 10.4, 9.0, 1.0, 14.81])),
     (1.0,
      DenseVector([76.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 222.0, 99.0, 215.9, 122.0, 204.1, 131.0, 8.2, 5.0, 3.0, 10.0])),
     (0.0,
      DenseVector([102.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 209.8, 85.0, 165.7, 100.0, 230.1, 92.0, 8.7, 6.0, 0.0, 13.56])),
     (0.0,
      DenseVector([4.0, 0.0, 1.0, 0.0, 0.0, 1.0, 31.0, 158.3, 99.0, 164.2, 98.0, 192.7, 108.0, 12.5, 3.0, 2.0, 7.84])),
     (1.0,
      DenseVector([161.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 252.3, 120.0, 243.8, 98.0, 284.4, 96.0, 9.2, 2.0, 1.0, 17.86])),
     (1.0,
      DenseVector([116.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 255.5, 93.0, 224.4, 122.0, 317.3, 85.0, 12.7, 4.0, 1.0, 9.43])),
     (0.0,
      DenseVector([49.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 101.7, 71.0, 223.2, 102.0, 182.1, 112.0, 7.3, 3.0, 3.0, 12.33])),
     (0.0,
      DenseVector([86.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 227.9, 105.0, 218.7, 118.0, 223.2, 120.0, 14.2, 5.0, 3.0, 11.69])),
     (0.0,
      DenseVector([39.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 185.0, 113.0, 106.5, 125.0, 167.3, 78.0, 12.0, 2.0, 0.0, 8.2])),
     (0.0,
      DenseVector([118.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 142.2, 96.0, 164.9, 107.0, 183.7, 92.0, 7.1, 4.0, 1.0, 8.97])),
     (0.0,
      DenseVector([57.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 140.0, 100.0, 174.7, 102.0, 258.5, 111.0, 13.3, 4.0, 1.0, 21.92])),
     (0.0,
      DenseVector([138.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 157.5, 96.0, 271.7, 107.0, 138.2, 84.0, 10.7, 6.0, 2.0, 16.07])),
     (0.0,
      DenseVector([98.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 237.5, 130.0, 213.2, 85.0, 194.1, 107.0, 10.1, 3.0, 0.0, 5.77])),
     (0.0,
      DenseVector([67.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 164.5, 79.0, 110.3, 108.0, 203.9, 102.0, 9.8, 2.0, 1.0, 14.75])),
     (0.0,
      DenseVector([145.0, 1.0, 0.0, 0.0, 0.0, 1.0, 10.0, 190.7, 100.0, 177.7, 114.0, 167.3, 100.0, 12.8, 2.0, 0.0, 6.25])),
     (0.0,
      DenseVector([77.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 215.5, 81.0, 170.9, 100.0, 170.3, 59.0, 10.3, 3.0, 1.0, 21.21])),
     (0.0,
      DenseVector([99.0, 0.0, 1.0, 0.0, 0.0, 1.0, 12.0, 119.9, 83.0, 237.7, 98.0, 187.6, 159.0, 16.6, 2.0, 1.0, 9.43])),
     (1.0,
      DenseVector([106.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 196.7, 100.0, 210.2, 109.0, 173.8, 83.0, 7.8, 1.0, 4.0, 10.96])),
     (0.0,
      DenseVector([32.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 230.7, 122.0, 204.7, 134.0, 142.0, 86.0, 8.7, 3.0, 2.0, 14.75])),
     (0.0,
      DenseVector([131.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 131.3, 39.0, 242.9, 101.0, 278.8, 100.0, 8.5, 2.0, 2.0, 9.23])),
     (0.0,
      DenseVector([100.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 167.2, 100.0, 141.2, 87.0, 251.3, 76.0, 6.1, 6.0, 3.0, 16.92])),
     (1.0,
      DenseVector([174.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 222.3, 102.0, 173.8, 123.0, 297.2, 97.0, 5.7, 2.0, 0.0, 14.1])),
     (1.0,
      DenseVector([98.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 198.6, 92.0, 349.4, 99.0, 221.7, 83.0, 7.3, 6.0, 2.0, 18.07])),
     (0.0,
      DenseVector([42.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 139.4, 109.0, 223.8, 121.0, 244.9, 105.0, 4.6, 3.0, 1.0, 20.97])),
     (0.0,
      DenseVector([99.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 130.4, 121.0, 219.9, 103.0, 84.1, 85.0, 9.8, 3.0, 2.0, 6.49])),
     (0.0,
      DenseVector([122.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 184.6, 88.0, 148.8, 98.0, 309.7, 58.0, 14.3, 2.0, 3.0, 14.75])),
     (1.0,
      DenseVector([4.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 169.7, 96.0, 131.8, 94.0, 245.1, 86.0, 14.2, 4.0, 0.0, 12.82])),
     (0.0,
      DenseVector([119.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 153.2, 131.0, 116.6, 85.0, 218.5, 74.0, 10.1, 5.0, 1.0, 18.07])),
     (0.0,
      DenseVector([31.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 224.5, 96.0, 191.2, 72.0, 214.5, 100.0, 9.1, 1.0, 2.0, 17.86])),
     (1.0,
      DenseVector([114.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 247.8, 95.0, 255.1, 98.0, 224.1, 105.0, 9.5, 6.0, 1.0, 9.43])),
     (0.0,
      DenseVector([47.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 177.1, 104.0, 217.4, 97.0, 191.8, 131.0, 9.7, 5.0, 2.0, 24.29])),
     (0.0,
      DenseVector([77.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 134.5, 119.0, 119.7, 104.0, 250.7, 112.0, 9.3, 5.0, 0.0, 14.81])),
     (0.0,
      DenseVector([39.0, 0.0, 0.0, 1.0, 0.0, 1.0, 38.0, 201.8, 66.0, 200.1, 87.0, 173.7, 112.0, 9.5, 3.0, 0.0, 11.69])),
     (0.0,
      DenseVector([193.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 180.5, 114.0, 117.9, 80.0, 192.9, 97.0, 10.2, 4.0, 2.0, 9.43])),
     (0.0,
      DenseVector([103.0, 1.0, 0.0, 0.0, 0.0, 1.0, 40.0, 164.5, 95.0, 228.7, 82.0, 194.7, 97.0, 7.4, 1.0, 4.0, 12.82])),
     (1.0,
      DenseVector([36.0, 1.0, 0.0, 0.0, 1.0, 1.0, 19.0, 171.9, 96.0, 198.4, 111.0, 321.7, 76.0, 10.5, 1.0, 1.0, 5.77])),
     (0.0,
      DenseVector([149.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 244.2, 83.0, 262.0, 72.0, 144.5, 113.0, 7.0, 4.0, 1.0, 20.59])),
     (0.0,
      DenseVector([87.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 221.4, 69.0, 195.2, 53.0, 120.6, 91.0, 13.7, 4.0, 1.0, 8.62])),
     (0.0,
      DenseVector([1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 241.5, 114.0, 195.2, 94.0, 201.6, 93.0, 14.1, 3.0, 3.0, 9.23])),
     (0.0,
      DenseVector([62.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 116.5, 77.0, 186.1, 111.0, 180.6, 72.0, 16.1, 2.0, 3.0, 11.69])),
     (0.0,
      DenseVector([112.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 158.1, 107.0, 181.5, 101.0, 200.3, 126.0, 8.3, 7.0, 1.0, 21.21])),
     (0.0,
      DenseVector([82.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 121.6, 81.0, 191.3, 109.0, 231.4, 81.0, 8.6, 5.0, 3.0, 13.64])),
     (0.0,
      DenseVector([86.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 178.3, 94.0, 264.4, 74.0, 284.6, 109.0, 15.0, 2.0, 0.0, 9.26])),
     (0.0,
      DenseVector([141.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 215.8, 79.0, 112.7, 85.0, 221.8, 109.0, 5.6, 4.0, 2.0, 12.82])),
     (0.0,
      DenseVector([55.0, 1.0, 0.0, 0.0, 0.0, 1.0, 28.0, 222.5, 134.0, 177.8, 106.0, 194.6, 90.0, 9.7, 4.0, 2.0, 10.0])),
     (0.0,
      DenseVector([127.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 264.5, 75.0, 179.2, 91.0, 141.9, 93.0, 8.0, 1.0, 1.0, 13.56])),
     (0.0,
      DenseVector([82.0, 0.0, 0.0, 1.0, 0.0, 1.0, 23.0, 123.7, 93.0, 139.1, 74.0, 174.5, 118.0, 10.6, 2.0, 1.0, 6.82])),
     (0.0,
      DenseVector([69.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 244.8, 76.0, 77.2, 100.0, 222.8, 102.0, 10.0, 3.0, 0.0, 12.82])),
     (0.0,
      DenseVector([131.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 212.8, 108.0, 212.0, 115.0, 235.9, 96.0, 9.8, 11.0, 0.0, 24.29])),
     (1.0,
      DenseVector([87.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 297.4, 63.0, 118.4, 108.0, 242.3, 72.0, 11.0, 2.0, 2.0, 5.66])),
     (0.0,
      DenseVector([63.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 147.8, 95.0, 201.4, 72.0, 154.9, 118.0, 8.6, 8.0, 1.0, 9.43])),
     (0.0,
      DenseVector([124.0, 0.0, 1.0, 0.0, 1.0, 1.0, 29.0, 187.1, 75.0, 148.5, 142.0, 214.4, 124.0, 5.5, 7.0, 2.0, 13.33])),
     (0.0,
      DenseVector([148.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 236.5, 84.0, 236.0, 95.0, 228.6, 124.0, 8.3, 6.0, 2.0, 18.07])),
     (0.0,
      DenseVector([68.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 186.5, 108.0, 190.2, 107.0, 187.8, 103.0, 11.0, 5.0, 1.0, 12.33])),
     (1.0,
      DenseVector([72.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 286.1, 101.0, 306.1, 94.0, 232.8, 89.0, 10.3, 1.0, 4.0, 25.0])),
     (0.0,
      DenseVector([119.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 107.2, 100.0, 221.1, 100.0, 212.6, 96.0, 8.3, 3.0, 2.0, 25.0])),
     (0.0,
      DenseVector([43.0, 1.0, 0.0, 0.0, 0.0, 1.0, 36.0, 93.1, 68.0, 246.1, 83.0, 94.9, 109.0, 8.7, 8.0, 1.0, 12.33])),
     (0.0,
      DenseVector([136.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 132.4, 110.0, 254.1, 81.0, 176.5, 100.0, 13.0, 6.0, 2.0, 26.47])),
     (0.0,
      DenseVector([97.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 80.7, 99.0, 262.3, 93.0, 214.6, 130.0, 8.8, 3.0, 0.0, 11.11])),
     (0.0,
      DenseVector([149.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 114.5, 103.0, 228.8, 100.0, 135.8, 74.0, 10.7, 3.0, 0.0, 14.1])),
     (1.0,
      DenseVector([59.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 155.5, 92.0, 192.3, 104.0, 123.3, 106.0, 10.5, 3.0, 4.0, 13.56])),
     (0.0,
      DenseVector([77.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 184.7, 130.0, 194.0, 105.0, 249.1, 91.0, 9.0, 4.0, 1.0, 18.07])),
     (0.0,
      DenseVector([110.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 94.1, 127.0, 283.0, 97.0, 168.7, 69.0, 10.3, 6.0, 2.0, 21.92])),
     (0.0,
      DenseVector([70.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 229.3, 110.0, 118.2, 89.0, 155.0, 105.0, 7.9, 4.0, 1.0, 21.21])),
     (0.0,
      DenseVector([155.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 223.8, 109.0, 233.4, 68.0, 201.6, 111.0, 8.8, 8.0, 1.0, 10.96])),
     (0.0,
      DenseVector([151.0, 1.0, 0.0, 0.0, 0.0, 1.0, 27.0, 154.4, 141.0, 210.6, 109.0, 167.9, 109.0, 7.8, 3.0, 3.0, 9.26])),
     (1.0,
      DenseVector([125.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 190.2, 107.0, 209.2, 75.0, 102.1, 136.0, 14.2, 5.0, 4.0, 25.0])),
     (0.0,
      DenseVector([83.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 167.0, 120.0, 209.1, 102.0, 131.0, 107.0, 8.6, 2.0, 1.0, 26.47])),
     (0.0,
      DenseVector([94.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 154.8, 113.0, 261.8, 121.0, 213.0, 81.0, 11.6, 5.0, 0.0, 9.68])),
     (0.0,
      DenseVector([11.0, 1.0, 0.0, 0.0, 0.0, 1.0, 36.0, 293.4, 122.0, 174.1, 91.0, 212.0, 50.0, 6.9, 6.0, 1.0, 13.64])),
     (0.0,
      DenseVector([92.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 160.5, 113.0, 287.3, 134.0, 212.6, 93.0, 8.2, 3.0, 3.0, 13.64])),
     (0.0,
      DenseVector([70.0, 1.0, 0.0, 0.0, 0.0, 1.0, 20.0, 230.2, 110.0, 177.5, 111.0, 206.3, 38.0, 11.9, 2.0, 0.0, 14.81])),
     (0.0,
      DenseVector([82.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 191.9, 95.0, 116.7, 105.0, 202.6, 113.0, 8.2, 5.0, 0.0, 13.89])),
     (0.0,
      DenseVector([65.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 131.5, 119.0, 222.3, 119.0, 155.3, 83.0, 11.1, 7.0, 1.0, 13.64])),
     (1.0,
      DenseVector([102.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 171.6, 116.0, 219.4, 111.0, 242.1, 85.0, 12.1, 4.0, 0.0, 6.25])),
     (1.0,
      DenseVector([112.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 242.1, 69.0, 235.2, 150.0, 237.7, 106.0, 10.0, 5.0, 3.0, 25.0])),
     (0.0,
      DenseVector([78.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 157.3, 61.0, 242.0, 119.0, 196.0, 107.0, 10.6, 1.0, 1.0, 16.22])),
     (0.0,
      DenseVector([77.0, 0.0, 1.0, 0.0, 1.0, 0.0, 0.0, 192.7, 82.0, 198.0, 91.0, 204.2, 95.0, 9.0, 6.0, 1.0, 9.68])),
     (0.0,
      DenseVector([126.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 175.6, 91.0, 151.1, 100.0, 201.5, 100.0, 9.9, 2.0, 1.0, 21.92])),
     (0.0,
      DenseVector([58.0, 1.0, 0.0, 0.0, 0.0, 1.0, 29.0, 239.6, 84.0, 182.1, 80.0, 118.3, 100.0, 11.0, 3.0, 2.0, 20.97])),
     (0.0,
      DenseVector([38.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 96.9, 110.0, 241.4, 75.0, 141.0, 69.0, 10.8, 3.0, 0.0, 16.22])),
     (0.0,
      DenseVector([87.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 129.5, 104.0, 194.0, 133.0, 287.9, 65.0, 7.2, 7.0, 2.0, 11.11])),
     (0.0,
      DenseVector([72.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 274.0, 75.0, 162.8, 110.0, 110.0, 121.0, 7.7, 3.0, 4.0, 11.69])),
     (0.0,
      DenseVector([69.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 111.7, 86.0, 187.5, 68.0, 194.8, 128.0, 11.6, 10.0, 2.0, 9.26])),
     (0.0,
      DenseVector([85.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 142.7, 82.0, 179.1, 106.0, 141.4, 88.0, 5.0, 8.0, 3.0, 9.23])),
     (0.0,
      DenseVector([176.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 216.7, 91.0, 233.9, 104.0, 231.1, 110.0, 9.0, 4.0, 1.0, 6.49])),
     (0.0,
      DenseVector([133.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 207.1, 106.0, 283.5, 124.0, 65.2, 107.0, 10.5, 2.0, 3.0, 16.92])),
     (0.0,
      DenseVector([146.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 214.8, 86.0, 230.9, 94.0, 223.8, 100.0, 5.8, 3.0, 3.0, 20.59])),
     (0.0,
      DenseVector([31.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 142.9, 67.0, 109.7, 72.0, 293.6, 112.0, 11.5, 6.0, 0.0, 21.21])),
     (1.0,
      DenseVector([78.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 71.5, 126.0, 166.8, 127.0, 211.6, 86.0, 8.3, 2.0, 3.0, 9.43])),
     (0.0,
      DenseVector([118.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 77.6, 113.0, 126.9, 75.0, 191.0, 79.0, 14.9, 4.0, 1.0, 9.68])),
     (0.0,
      DenseVector([119.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 73.7, 66.0, 160.5, 132.0, 214.2, 67.0, 13.0, 6.0, 3.0, 20.97])),
     (1.0,
      DenseVector([141.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 264.3, 90.0, 216.4, 101.0, 208.8, 72.0, 9.6, 3.0, 0.0, 13.64])),
     (1.0,
      DenseVector([91.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 103.2, 100.0, 124.8, 154.0, 336.1, 111.0, 14.0, 4.0, 2.0, 12.68])),
     (1.0,
      DenseVector([150.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 114.8, 156.0, 135.3, 78.0, 218.4, 98.0, 15.6, 4.0, 1.0, 9.26])),
     (0.0,
      DenseVector([76.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 212.5, 124.0, 215.5, 110.0, 128.0, 94.0, 5.2, 9.0, 3.0, 13.89])),
     (0.0,
      DenseVector([159.0, 0.0, 0.0, 1.0, 0.0, 1.0, 36.0, 288.2, 120.0, 227.1, 105.0, 198.2, 113.0, 13.3, 7.0, 1.0, 12.33])),
     (0.0,
      DenseVector([66.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 192.4, 98.0, 187.0, 93.0, 132.9, 82.0, 11.2, 1.0, 0.0, 13.89])),
     (0.0,
      DenseVector([139.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 102.2, 76.0, 230.0, 106.0, 183.4, 136.0, 10.8, 4.0, 1.0, 24.29])),
     (0.0,
      DenseVector([122.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 49.5, 114.0, 214.1, 98.0, 154.9, 120.0, 10.0, 4.0, 0.0, 10.96])),
     (1.0,
      DenseVector([91.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 153.2, 70.0, 231.7, 91.0, 115.1, 80.0, 9.8, 3.0, 4.0, 12.7])),
     (0.0,
      DenseVector([131.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 55.5, 79.0, 163.5, 104.0, 128.1, 59.0, 10.1, 4.0, 1.0, 16.07])),
     (0.0,
      DenseVector([135.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 247.2, 61.0, 168.7, 122.0, 169.8, 100.0, 6.6, 1.0, 0.0, 8.62])),
     (0.0,
      DenseVector([109.0, 0.0, 1.0, 0.0, 0.0, 1.0, 40.0, 99.7, 116.0, 140.3, 117.0, 221.3, 76.0, 13.9, 5.0, 3.0, 9.68])),
     (0.0,
      DenseVector([129.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 220.2, 117.0, 261.1, 102.0, 165.9, 132.0, 10.8, 1.0, 2.0, 18.57])),
     (0.0,
      DenseVector([85.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 116.9, 75.0, 183.3, 109.0, 307.4, 119.0, 10.0, 8.0, 2.0, 9.43])),
     (0.0,
      DenseVector([125.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 175.9, 98.0, 192.8, 148.0, 278.2, 110.0, 10.6, 5.0, 0.0, 12.33])),
     (0.0,
      DenseVector([67.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 208.0, 95.0, 234.8, 81.0, 266.0, 80.0, 11.7, 6.0, 3.0, 20.97])),
     (0.0,
      DenseVector([61.0, 1.0, 0.0, 0.0, 0.0, 1.0, 28.0, 239.9, 110.0, 218.9, 87.0, 166.0, 46.0, 12.1, 4.0, 1.0, 16.92])),
     (0.0,
      DenseVector([77.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 136.5, 95.0, 182.6, 102.0, 233.6, 93.0, 9.9, 10.0, 1.0, 25.0])),
     (0.0,
      DenseVector([135.0, 0.0, 1.0, 0.0, 1.0, 0.0, 0.0, 174.8, 112.0, 218.0, 125.0, 180.1, 102.0, 12.7, 6.0, 3.0, 14.1])),
     (0.0,
      DenseVector([43.0, 0.0, 0.0, 1.0, 0.0, 1.0, 26.0, 141.5, 94.0, 196.4, 123.0, 172.4, 100.0, 12.9, 1.0, 3.0, 9.43])),
     (0.0,
      DenseVector([120.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 79.2, 123.0, 212.1, 106.0, 224.6, 104.0, 10.2, 4.0, 3.0, 11.11])),
     (0.0,
      DenseVector([62.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 238.9, 102.0, 161.4, 115.0, 248.5, 103.0, 13.4, 4.0, 0.0, 11.69])),
     (0.0,
      DenseVector([160.0, 0.0, 1.0, 0.0, 0.0, 1.0, 37.0, 179.8, 104.0, 144.8, 115.0, 209.1, 89.0, 11.1, 8.0, 3.0, 9.43])),
     (1.0,
      DenseVector([67.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 225.9, 78.0, 243.1, 83.0, 84.1, 94.0, 13.5, 2.0, 2.0, 13.89])),
     (0.0,
      DenseVector([98.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 189.8, 113.0, 271.5, 111.0, 218.9, 96.0, 13.0, 5.0, 1.0, 13.56])),
     (1.0,
      DenseVector([103.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 267.0, 97.0, 204.3, 151.0, 246.9, 67.0, 11.3, 7.0, 1.0, 14.75])),
     (0.0,
      DenseVector([121.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 217.3, 79.0, 218.0, 106.0, 166.5, 69.0, 11.5, 7.0, 1.0, 9.43])),
     (0.0,
      DenseVector([111.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 129.1, 92.0, 148.9, 132.0, 264.0, 103.0, 16.8, 7.0, 2.0, 14.81])),
     (0.0,
      DenseVector([127.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 135.5, 110.0, 146.0, 116.0, 208.0, 88.0, 10.7, 4.0, 2.0, 8.2])),
     (1.0,
      DenseVector([65.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 183.4, 63.0, 183.6, 80.0, 153.5, 83.0, 10.7, 4.0, 4.0, 21.21])),
     (1.0,
      DenseVector([86.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 223.0, 109.0, 286.2, 76.0, 257.8, 132.0, 7.5, 7.0, 3.0, 11.11])),
     (0.0,
      DenseVector([133.0, 0.0, 0.0, 1.0, 0.0, 1.0, 24.0, 125.5, 106.0, 161.2, 95.0, 209.1, 112.0, 15.0, 4.0, 2.0, 9.43])),
     (0.0,
      DenseVector([238.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 202.0, 60.0, 213.9, 105.0, 266.4, 90.0, 12.0, 1.0, 2.0, 21.21])),
     (0.0,
      DenseVector([119.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 140.0, 135.0, 138.9, 103.0, 230.2, 121.0, 6.1, 6.0, 0.0, 20.97])),
     (0.0,
      DenseVector([113.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 126.0, 90.0, 205.8, 92.0, 217.0, 121.0, 12.8, 1.0, 0.0, 8.97])),
     (0.0,
      DenseVector([126.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 228.2, 68.0, 170.2, 102.0, 168.9, 72.0, 10.2, 6.0, 1.0, 16.18])),
     (1.0,
      DenseVector([103.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 278.4, 121.0, 252.2, 139.0, 202.3, 96.0, 13.7, 1.0, 1.0, 14.75])),
     (0.0,
      DenseVector([104.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 285.3, 75.0, 130.8, 111.0, 181.8, 99.0, 10.5, 2.0, 2.0, 16.92])),
     (0.0,
      DenseVector([56.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 222.9, 100.0, 145.2, 110.0, 170.6, 129.0, 5.6, 8.0, 1.0, 17.86])),
     (0.0,
      DenseVector([56.0, 0.0, 0.0, 1.0, 0.0, 1.0, 37.0, 153.7, 97.0, 271.4, 91.0, 179.0, 121.0, 8.3, 2.0, 1.0, 9.68])),
     (1.0,
      DenseVector([155.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 176.1, 88.0, 244.5, 84.0, 189.9, 99.0, 11.2, 1.0, 2.0, 6.25])),
     (0.0,
      DenseVector([100.0, 0.0, 1.0, 0.0, 0.0, 1.0, 41.0, 306.9, 71.0, 243.3, 72.0, 125.9, 114.0, 10.5, 4.0, 2.0, 23.33])),
     (0.0,
      DenseVector([113.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 203.4, 85.0, 219.8, 140.0, 240.0, 107.0, 8.3, 5.0, 1.0, 20.97])),
     (0.0,
      DenseVector([103.0, 1.0, 0.0, 0.0, 0.0, 1.0, 37.0, 230.7, 112.0, 319.4, 116.0, 299.4, 77.0, 7.9, 3.0, 2.0, 11.11])),
     (0.0,
      DenseVector([160.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 118.9, 106.0, 163.5, 75.0, 195.0, 131.0, 13.1, 4.0, 0.0, 26.47])),
     (0.0,
      DenseVector([142.0, 0.0, 1.0, 0.0, 0.0, 1.0, 34.0, 219.5, 85.0, 144.0, 120.0, 258.2, 84.0, 7.0, 3.0, 1.0, 11.69])),
     (0.0,
      DenseVector([73.0, 1.0, 0.0, 0.0, 1.0, 1.0, 34.0, 252.9, 103.0, 220.1, 79.0, 144.9, 65.0, 11.4, 3.0, 0.0, 14.75])),
     (0.0,
      DenseVector([135.0, 1.0, 0.0, 0.0, 0.0, 1.0, 46.0, 172.6, 77.0, 203.4, 102.0, 264.2, 108.0, 10.4, 2.0, 1.0, 17.78])),
     (0.0,
      DenseVector([120.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 174.1, 108.0, 209.6, 96.0, 267.5, 99.0, 11.6, 2.0, 0.0, 9.68])),
     (0.0,
      DenseVector([29.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 262.0, 112.0, 82.7, 60.0, 195.1, 120.0, 10.7, 5.0, 1.0, 9.68])),
     (0.0,
      DenseVector([158.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 123.3, 115.0, 130.4, 91.0, 243.7, 119.0, 11.2, 3.0, 3.0, 12.7])),
     (0.0,
      DenseVector([132.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 213.6, 125.0, 133.9, 54.0, 332.2, 104.0, 7.5, 2.0, 4.0, 11.11])),
     (1.0,
      DenseVector([104.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 210.9, 90.0, 272.2, 101.0, 304.3, 86.0, 10.6, 5.0, 3.0, 21.21])),
     (0.0,
      DenseVector([74.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 143.7, 97.0, 229.6, 110.0, 98.8, 100.0, 11.8, 5.0, 1.0, 13.33])),
     (0.0,
      DenseVector([28.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 171.2, 90.0, 173.9, 65.0, 210.5, 139.0, 14.0, 4.0, 2.0, 14.1])),
     (1.0,
      DenseVector([45.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 189.6, 96.0, 171.7, 69.0, 222.0, 102.0, 13.9, 7.0, 1.0, 9.43])),
     (0.0,
      DenseVector([60.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 229.5, 91.0, 157.7, 67.0, 162.4, 76.0, 13.2, 3.0, 2.0, 6.49])),
     (1.0,
      DenseVector([173.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 128.6, 108.0, 248.3, 92.0, 130.1, 115.0, 10.4, 2.0, 4.0, 20.97])),
     (0.0,
      DenseVector([152.0, 0.0, 1.0, 0.0, 0.0, 1.0, 26.0, 214.8, 82.0, 226.7, 135.0, 206.7, 97.0, 7.6, 11.0, 1.0, 12.7])),
     (0.0,
      DenseVector([140.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 187.1, 110.0, 158.0, 70.0, 211.4, 123.0, 8.5, 2.0, 2.0, 9.23])),
     (0.0,
      DenseVector([94.0, 1.0, 0.0, 0.0, 0.0, 1.0, 31.0, 209.3, 90.0, 281.7, 111.0, 190.1, 66.0, 7.9, 5.0, 0.0, 9.26])),
     (0.0,
      DenseVector([88.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 141.7, 102.0, 226.2, 125.0, 100.1, 93.0, 11.9, 8.0, 0.0, 20.0])),
     (0.0,
      DenseVector([170.0, 0.0, 1.0, 0.0, 0.0, 1.0, 22.0, 173.6, 66.0, 142.2, 83.0, 156.5, 93.0, 5.2, 3.0, 1.0, 14.1])),
     (0.0,
      DenseVector([57.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 176.9, 75.0, 204.2, 86.0, 254.1, 90.0, 6.1, 4.0, 1.0, 13.33])),
     (1.0,
      DenseVector([78.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 198.9, 93.0, 210.6, 86.0, 252.1, 99.0, 14.4, 12.0, 0.0, 21.54])),
     (0.0,
      DenseVector([102.0, 0.0, 1.0, 0.0, 0.0, 1.0, 27.0, 109.8, 66.0, 207.3, 76.0, 236.9, 101.0, 10.3, 7.0, 0.0, 16.18])),
     (1.0,
      DenseVector([49.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 202.3, 105.0, 195.4, 131.0, 190.1, 88.0, 9.0, 2.0, 3.0, 12.82])),
     (0.0,
      DenseVector([195.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 221.6, 109.0, 126.2, 94.0, 212.2, 98.0, 6.4, 2.0, 0.0, 12.68])),
     (1.0,
      DenseVector([64.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 131.3, 105.0, 109.4, 114.0, 246.2, 125.0, 12.7, 4.0, 4.0, 20.59])),
     (1.0,
      DenseVector([105.0, 0.0, 1.0, 0.0, 1.0, 0.0, 0.0, 274.0, 156.0, 263.0, 69.0, 195.9, 93.0, 11.3, 3.0, 3.0, 23.33])),
     (0.0,
      DenseVector([81.0, 1.0, 0.0, 0.0, 0.0, 1.0, 33.0, 200.3, 103.0, 216.9, 96.0, 249.8, 100.0, 15.5, 4.0, 1.0, 14.1])),
     (0.0,
      DenseVector([123.0, 1.0, 0.0, 0.0, 0.0, 1.0, 38.0, 202.3, 104.0, 292.4, 105.0, 239.3, 63.0, 11.2, 1.0, 3.0, 14.1])),
     (0.0,
      DenseVector([63.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 204.0, 119.0, 161.7, 90.0, 202.6, 113.0, 9.4, 4.0, 3.0, 14.81])),
     (0.0,
      DenseVector([126.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 193.1, 114.0, 75.5, 61.0, 186.2, 73.0, 11.1, 1.0, 3.0, 21.92])),
     (0.0,
      DenseVector([153.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 202.2, 132.0, 158.9, 78.0, 111.4, 98.0, 8.6, 4.0, 2.0, 14.1])),
     (0.0,
      DenseVector([67.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 245.1, 118.0, 185.2, 94.0, 193.6, 112.0, 8.1, 3.0, 3.0, 14.1])),
     (0.0,
      DenseVector([65.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 158.3, 71.0, 168.3, 119.0, 139.9, 119.0, 6.6, 6.0, 2.0, 16.92])),
     (0.0,
      DenseVector([21.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 138.1, 79.0, 186.9, 75.0, 194.2, 105.0, 12.1, 1.0, 0.0, 6.82])),
     (0.0,
      DenseVector([72.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 212.7, 92.0, 306.4, 100.0, 140.3, 90.0, 12.9, 6.0, 0.0, 6.49])),
     (1.0,
      DenseVector([176.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 304.2, 111.0, 306.6, 64.0, 207.9, 98.0, 9.5, 2.0, 7.0, 9.43])),
     (0.0,
      DenseVector([150.0, 0.0, 1.0, 0.0, 0.0, 1.0, 24.0, 212.4, 157.0, 168.7, 91.0, 199.4, 112.0, 9.7, 1.0, 2.0, 25.0])),
     (1.0,
      DenseVector([108.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 137.3, 68.0, 143.8, 94.0, 238.7, 101.0, 8.9, 1.0, 1.0, 8.62])),
     (0.0,
      DenseVector([129.0, 0.0, 0.0, 1.0, 0.0, 1.0, 10.0, 161.4, 91.0, 183.1, 102.0, 240.3, 65.0, 12.9, 3.0, 1.0, 9.23])),
     (0.0,
      DenseVector([73.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 202.9, 108.0, 285.9, 121.0, 219.0, 102.0, 12.8, 5.0, 1.0, 21.21])),
     (0.0,
      DenseVector([90.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 272.2, 75.0, 122.9, 96.0, 252.6, 100.0, 9.2, 2.0, 0.0, 14.75])),
     (0.0,
      DenseVector([105.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 98.2, 103.0, 140.3, 91.0, 140.2, 106.0, 13.9, 1.0, 1.0, 21.21])),
     (0.0,
      DenseVector([115.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 80.8, 81.0, 195.7, 91.0, 190.1, 66.0, 11.7, 6.0, 1.0, 11.11])),
     (0.0,
      DenseVector([82.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 207.2, 114.0, 163.7, 97.0, 192.8, 79.0, 9.3, 4.0, 7.0, 12.82])),
     (0.0,
      DenseVector([131.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 227.7, 134.0, 162.7, 88.0, 279.4, 90.0, 8.9, 7.0, 4.0, 16.07])),
     (0.0,
      DenseVector([77.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 208.8, 117.0, 175.4, 81.0, 171.5, 105.0, 13.4, 3.0, 0.0, 20.97])),
     (0.0,
      DenseVector([201.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 159.9, 61.0, 237.3, 124.0, 290.2, 99.0, 13.4, 4.0, 3.0, 16.07])),
     (0.0,
      DenseVector([139.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 185.1, 101.0, 266.7, 75.0, 207.9, 95.0, 9.0, 3.0, 3.0, 16.18])),
     (0.0,
      DenseVector([94.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 139.1, 93.0, 237.9, 100.0, 227.1, 59.0, 11.4, 3.0, 1.0, 13.56])),
     (0.0,
      DenseVector([95.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 155.5, 99.0, 167.0, 75.0, 190.7, 125.0, 13.2, 4.0, 1.0, 13.89])),
     (0.0,
      DenseVector([138.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 180.5, 82.0, 242.4, 103.0, 244.4, 89.0, 10.3, 7.0, 2.0, 21.21])),
     (0.0,
      DenseVector([105.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 204.6, 111.0, 134.4, 109.0, 260.8, 69.0, 5.7, 7.0, 1.0, 10.96])),
     (1.0,
      DenseVector([81.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 220.4, 76.0, 276.4, 122.0, 239.5, 112.0, 11.4, 5.0, 1.0, 20.97])),
     (0.0,
      DenseVector([127.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 223.2, 137.0, 144.7, 119.0, 96.0, 109.0, 8.6, 4.0, 2.0, 20.97])),
     (0.0,
      DenseVector([84.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 109.1, 114.0, 231.4, 113.0, 274.0, 112.0, 9.6, 4.0, 0.0, 17.78])),
     (0.0,
      DenseVector([47.0, 0.0, 0.0, 1.0, 0.0, 1.0, 30.0, 136.5, 101.0, 174.9, 74.0, 146.4, 91.0, 10.9, 3.0, 2.0, 21.92])),
     (1.0,
      DenseVector([128.0, 0.0, 0.0, 1.0, 1.0, 0.0, 0.0, 192.8, 96.0, 136.6, 86.0, 151.8, 75.0, 13.5, 4.0, 1.0, 17.86])),
     (0.0,
      DenseVector([39.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 179.0, 88.0, 148.2, 124.0, 146.8, 116.0, 8.8, 4.0, 2.0, 13.33])),
     (1.0,
      DenseVector([76.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 293.8, 94.0, 169.6, 62.0, 160.4, 103.0, 13.7, 9.0, 0.0, 13.56])),
     (0.0,
      DenseVector([95.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 226.6, 98.0, 88.5, 107.0, 166.2, 105.0, 11.9, 3.0, 2.0, 23.33])),
     (1.0,
      DenseVector([82.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 313.6, 110.0, 263.2, 104.0, 139.0, 93.0, 7.5, 5.0, 1.0, 21.21])),
     ...]




```python
# Transformando o RDD em um dataframe.
dfTeste = spSession.createDataFrame(df_teste_RDD4, ["label", "features"])
dfTeste.select("features", "label").show(5)
```

    +--------------------+-----+
    |            features|label|
    +--------------------+-----+
    |[101.0,0.0,0.0,1....|  0.0|
    |[137.0,0.0,0.0,1....|  0.0|
    |[103.0,0.0,1.0,0....|  0.0|
    |[99.0,1.0,0.0,0.0...|  0.0|
    |[108.0,1.0,0.0,0....|  0.0|
    +--------------------+-----+
    only showing top 5 rows
    



```python
# Aplicando a redução de dimensionalidade.
pcaResultTeste = pcaModel.transform(dfTeste).select("label", "pcaFeatures")
pcaResultTeste.show(truncate=False)
```

    +-----+-------------------------------------------------------------------------------------------------+
    |label|pcaFeatures                                                                                      |
    +-----+-------------------------------------------------------------------------------------------------+
    |0.0  |[-89.06525925476696,11.62585881657066,310.60049108578994,111.37360211138329,100.16454819793907]  |
    |0.0  |[-240.27201756684255,123.94081894963233,212.85321165238037,143.401749253455,71.88851942743605]   |
    |0.0  |[-316.04447171672814,-13.454903116255084,365.7070728373371,111.82004072546961,60.43957322053354] |
    |0.0  |[-230.55718879238782,-47.87754439974771,237.71088573059646,106.37206095131314,98.52051773845284] |
    |0.0  |[-211.01338622383972,-38.56861966799496,224.484796704194,113.96763892205483,49.911451139407106]  |
    |0.0  |[-241.00760310953808,-37.196089150058874,248.32189393810802,123.2319638046336,57.26581116793162] |
    |0.0  |[-238.04943797487945,-4.980457091441139,324.84050747468746,72.59073111770601,93.76307025738882]  |
    |0.0  |[-178.45778236561168,-27.37646076354952,372.3915099788798,104.61182936730175,70.25407848835923]  |
    |0.0  |[-102.27460572025515,-25.74304880673113,220.04879266433724,145.5008090164181,89.9476551109852]   |
    |0.0  |[-192.30928440240513,-13.767914509620482,231.86952984580458,134.7961572747316,72.85437143182212] |
    |0.0  |[-224.03326014431445,20.705157709047782,214.5288198295455,118.45812142166481,49.82653952642783]  |
    |0.0  |[-111.13438847841475,10.395976977935472,286.90204140466,149.53948839848513,84.884552670601]      |
    |0.0  |[-243.84391909178171,-29.283570720255515,261.54481663528435,108.61574241963004,52.00163192899574]|
    |0.0  |[-125.32441028246649,-27.201522774151933,202.2797459312445,114.33487988964863,53.95619085078746] |
    |0.0  |[-221.7446777231579,87.06683333516426,203.32855983016753,65.60486270149981,58.23852684654037]    |
    |0.0  |[-226.41292564390508,-35.93473730475091,323.40245696992116,104.90262873188355,70.51451237824013] |
    |0.0  |[-132.04126559221368,53.62436491790955,287.9593122372125,185.8260088767455,33.8025648472777]     |
    |0.0  |[-244.63428332467592,9.399230906845132,328.18896910635755,84.21850676491377,92.60719300385449]   |
    |0.0  |[-187.4829346441786,80.38149813090855,264.53571110391795,113.9257791978343,75.51197750153568]    |
    |0.0  |[-211.82121189536232,11.145648443635741,294.74538301869524,166.8959119685182,91.69555426938149]  |
    +-----+-------------------------------------------------------------------------------------------------+
    only showing top 20 rows
    



```python
# Aplicando o modelo.
predictTeste=model_balanced.transform(pcaResultTeste)
predictTeste.select("label","prediction", "probability").show(10)
```

    +-----+----------+--------------------+
    |label|prediction|         probability|
    +-----+----------+--------------------+
    |  0.0|       0.0|[0.60002173144674...|
    |  0.0|       1.0|[0.32008207913131...|
    |  0.0|       1.0|[0.16962995012075...|
    |  0.0|       1.0|[0.40429521695323...|
    |  0.0|       1.0|[0.43458814717464...|
    |  0.0|       1.0|[0.35585009095895...|
    |  0.0|       1.0|[0.32318195932051...|
    |  0.0|       1.0|[0.38610428465828...|
    |  0.0|       0.0|[0.64084406519937...|
    |  0.0|       1.0|[0.45145301761493...|
    +-----+----------+--------------------+
    only showing top 10 rows
    



```python
# Calculando o erro.
evaluator=BinaryClassificationEvaluator(rawPredictionCol="rawPrediction",labelCol="label")
predictTeste.select("label","rawPrediction","prediction","probability").show(5)

print("The area under ROC for train balanced set is {}".format(evaluator.evaluate(predictTeste)))
```

    +-----+--------------------+----------+--------------------+
    |label|       rawPrediction|prediction|         probability|
    +-----+--------------------+----------+--------------------+
    |  0.0|[0.40555565662291...|       0.0|[0.60002173144674...|
    |  0.0|[-0.7533946260905...|       1.0|[0.32008207913131...|
    |  0.0|[-1.5882521441998...|       1.0|[0.16962995012075...|
    |  0.0|[-0.3875998677531...|       1.0|[0.40429521695323...|
    |  0.0|[-0.2631556141527...|       1.0|[0.43458814717464...|
    +-----+--------------------+----------+--------------------+
    only showing top 5 rows
    
    The area under ROC for train balanced set is 0.6499542124542117


O desempenho do modelo no arquivo de teste foi muito próximo ao arquivo de treino, o que é um bom sinal, pois o mostra que o mesmo é bem está estável.


```python

```
