
# Projeto Prático

Neste último capítulo, vamos desenvolver um projeto de _feature engineering_ utilizando o que aprendemos ao longo deste livro. Usaremos um conjunto de dados sintéticos para aplicar nossas habilidades conquistadas até aqui.


## Conjunto de Dados

Para atingir os objetivos deste capítulo, utilizamos um conjunto de dados sintéticos de um hospital que está disponível neste link: assim como o o _notebook_ deste capítulo. A escolha desse conjunto serve para destacar a importância de _feature engineering_.

## Estrutura do DataFrame

A seguir, está a estrutura do DataFrame que utilizaremos ao longo deste capítulo para nosso estudo.

|index|id|data\_admissao|idade|peso|altura|indice\_massa\_corporal|sexo|tipo\_sangue|doenca\_cronica|hipertensao|diabetes|cirurgia|uti|dias\_internado|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|0|1|2015-06-18|62|58\.05|NaN|18\.34|Masculino|O+|true|false|false|false|false|10|
|1|2|2023-10-15|16|79\.91|1\.66|28\.97|Masculino|B+|true|false|false|false|false|6|
|2|3|2020-08-13|72|77\.13|1\.55|31\.98|Masculino|A+|true|false|false|false|false|6|
|3|4|2018-09-11|32|89\.78|1\.61|34\.63|Feminino|O+|false|false|false|false|false|4|

*Descrição das Colunas*

* id: Identificador único de cada paciente
* data admissão: Data de admissão do paciente no hospital
* idade: Idade do paciente
* peso: Peso do paciente em quilogramas
* altura: Altura do paciente em metros
* Indice de Massa Corporal: Índice de massa corporal (IMC) do paciente
* Sexo: Sexo do paciente: (Masculino/ Feminino)
* Tipo sangue: Tipo sanguuíneo do paciente
* Doença cronica: Indica se o paciente tem uma doença crônica (True/False)
* hipertensão: Indica se o paciente tem hipertensão (True/False)
* diabetes: Indica se o paciente tem diabetes (True/False)
* cirurgia: Indica se o paciente passou por cirurgia (True/False)
* uti: Indica se o paciente foi internado na UTI (True/False)
* Dias Internado: Número de dias que o paciente ficou internado


## Objetivo do Projeto

Antes de prosseguirmos com nosso estudo de _feature engineering_, é fundamental definir o objetivo do projeto: o que estamos tentando prever com esse conjunto de dados? Neste trabalho, queremos prever o número de dias que um paciente ficará internado. Assim, temos as variáveis previsoreas e a nossa variável alvo descritas na tabela a seguir:


|Variáveis previsoras    | Variável Alvo|
|---                     |---|
|data admissão           |dias internados|
|idade                   |
|peso                    |
|altura                  |
|indice de massa corporal|
|sexo                    |
|tipo sangue             |
|doença cronica          |
|hipertensão             |
|diabetes                |
|cirurgia                |
|uti                     |


## Check-List de Tarefas

Para organizarmos nosso trabalho, vamos seguir check-list detalhado:

1. Análise de Valores Ausentes

* Identificar colunas com valores ausentes
* Calcular a porcentagem de valores ausentes em cada coluna
* Decidir a estratégia para lidar com valores ausentes (remoção, imputação, etc.)

2. Análise de Outliers

* Identificar outliers em cada feature
* Decidir a estrtégia para lidar com outliers

3. Engenharia de features temporais

* Extrair dia da semana da data de admissão
* Extrair mês da data de admissão
* Criar uma feature indicando se é período de férias

4. Criação de novas features

* Criar a feature `idade_avancada` (`True` se `idade`> `65`, caso contrário `False`)
* Criar a feature `imc_categoria` (Categoria de IMC: abaixo do peso, normal, sobrepeso, obeso)
* Criar a feature `internacao_fase_ferias` (`True` se `data_admissao` for junho ou julho)

5. Análise de Raridade das Features

* Identificar categorias raras em features categóricas (ex.: `tipo_sangue`)
* Decidir a estratégia para lidar com categorias raras (agregação, remoção, etc.)

6. Normalização e Padronização

* Normalizar ou padronizar features numéricas (ex.: `peso`, `altura`, `indice_massa_corporal`)

7. Codificação de Features Categóricas

* Codificação features categóricas (ex.: one-hot-encoding)

Seguindo esse check-list, poderemos e avaliar nossa progressão de forma eficiente. Vamos então, colocar em prática todas as etapas descritas e verificar como a _feature engineering_ pode melhorar a análise dos nossos dados.

## 1 Análise de Valores ausentes

Começaremos explorando se há dados ausentes e qual é a porcentagem de dados ausentes em nosso DataFrame. Com essas informações, poderemos criar um plano de ação que envolve:


* Excluir linhas com dados ausentes
* Substituir valores ausentes por otros valores

Para isso, vamos utilizar o código a seguir:

```Python
# Calcular o número de valores ausentes e a porcentagem
num_valores_ausentes = pacientes.isnull().sum()
ausentes_porcentagem = (num_valores_ausentes / len(pacientes)) * 100

# Criar a lista de tuplas com o nome da coluna, número de dados ausentes e porcentagem
missing_info = [(col, num_valores_ausentes[col], ausentes_porcentagem[col]) for col in pacientes.columns if num_valores_ausentes[col] > 0]

# Imprimir a informação
for col, num_valores_ausentes, ausentes_porcentagem in missing_info:
    print(f"Coluna: {col}, Dados ausentes: {num_valores_ausentes}, Porcentagem: {ausentes_porcentagem:.2f}%")
```
Esse código nos mostrará os dados ausentes em cada coluna, salvando o nome das colunas, o número de dados faltantes e a porcentagem de dados ausentes em nossa tabela em uma lista, e em seguida, imprimirá as informações mostrado a seguir:

```Python
Coluna: peso, Dados ausentes: 60, Porcentagem: 12.00%
Coluna: altura, Dados ausentes: 25, Porcentagem: 5.00%
Coluna: indice_massa_corporal, Dados ausentes: 139, Porcentagem: 27.80%
Coluna: sexo, Dados ausentes: 125, Porcentagem: 25.00%
Coluna: tipo_sangue, Dados ausentes: 25, Porcentagem: 5.00%
Coluna: hipertensao, Dados ausentes: 32, Porcentagem: 6.40%
Coluna: diabetes, Dados ausentes: 67, Porcentagem: 13.40%
```

### Imputação vs. Exclusão de Dados Ausentes

Imputação e exclusão de dados ausentes são abordagens comuns na manipulação de dados faltantes. Ambas têm prós e contras, especialmente em conjuntos de dados relacionados à saúde. Aqui estão os principais pontos a serem considerados:

*Prós da Imputação*

1. Preservação do conjunto de dados completo: A imputação permite manter todos os registros no conjunto de dados, o que pode ser crucial para análises estatísticas e modelos de aprendizado de máquina, que geralmente requerem um grande número de amostras.

2. Redução de viés: Se os dados ausentes não são aleatórios, a exclusão pode introduzir um viés. A imputação pode ajudar a mitigar esse problema ao forncer estimativas para valores ausentes

3. Melhoria da Precisão do Modelo: Modelos de aprendizado de máquina podem ter um desempenho melhor com conjuntos de dados completos. A imputação pode levar a previsões mais precisas se for bem executada.

4. Utilização de informação disponível: Métodos avançados de imputação (como KNN, regressão, ou métodos baseados em aprendizado de máquina) podem usar a informação de outras variáveis para prever os valores ausentes, o que pode ser mais representativo do que simplesmente remover os dados faltantes.


*Contras da Imputação*

1. Introdução de Ruído: A imputação pode introduzir ruído adicional no conjunto de dados se os valores imputados não forem precisos, o que pode afetar negativamente a análise subsequente.

2. Dependência de suposições: A imputação frequentemente depende de suposições sobre os dados ausentes, que podem não ser verdadeiras. Por exemplo, a imputação com a média assume que os dados são normalmente distribuídos


3. Complexiade Adicional: Métodos avançados de imputação podem ser complexos e exigirem mais tempo e recursos computacionais. Além disso, podem ser difícies de implementar e interpretar 

### Exclusão de Dados Ausentes

*Prós da Exclusão de Dados Ausentes*

1. Simplicidade e Facilidade de Implementação: A exclusão de dados ausentes é simples e direta. Pode ser realizada rapidamente sem a necessidade de cálculos complexos.

2. Eliminação de Ruído: A exclusão pode ajudar a eliminar ruído se os dados ausentes forem suspeitos de serem imprecisos ou inconsistentes.

3. Mantém a integridade dos dados restantes: Não há risco de introduizr suposições ou estimativas erradas nos dados, preservando a integridade dos dados restantes.

*Contras da Exclusão de Dados Ausentes* 

1. Perda de informação: A exclusão de registros com dados ausentes pode levar à perda de informações valiosas, o que é especialmente crítico em conjuntos de dados de saúde, onde cada registro pode conter informações cruciais sobre pacientes

2. Redução do Tamanho do Conjunto de dados: A exclusão pode resultar em um conjunto de dados significativamente menor, o que pode prejudicar a robustez das análises estatísticas e performance dos modelos de aprendizado de máquina.

3. Introdução de viés: Se os dados ausentes não sao aleatórios, a exclusão pode introduzir viés no conjunto de dados. Por exemplo, pacientes mais graves podem ter mais dados faltantes, e a exclusão desses registros pode levar a conclusões incorretas sobre a saúde da população.

Em dados de saúde, cada ponto de dados pode ser crítico para o diagnóstico e tratamento dos pacientes. Imputar dados ausentes com precisão pode ser preferível neste caso, mas faremos isso com cuidado para não distorcer as conclusões.

Com essa visualização dos prós e contras, a escolha para o tratamento dos dados ausentes será a imputação com categoria personalizada e da coluna `sexo`. A justificativa é que, ao tratar valores ausentes como uma categoria separada (`Missing`), evitamos suposições sobre os dados ausentes que poderiam introduzir viés. A categoria `Missing` representa explicitamente a falta de dados, o que pode ser relevante para análises. Já na coluna `sexo`, temos que há diferenças fisiológicas significativas que podem influenciar suas métricas de saúde. Por exemplo, fatores como massa muscular, distribuição de gordura corporal e resposta a certos tratamentos médicos podem variar entre os sexos. Em contextos clínico, o sexo de um paciente pode influenciar decisões de tratamento e prognóstico.

Vamos começar o código importando as bibliotecas necessárias e, em seguida, importando a base de dados;

```Python
import pandas as pd
from sklearn.model_selection import train_test_split
from  feature_engine.imputation import CategoricalImputer

pacientes = pd.read_csv("pacientes.csv")
```

Com isso, temos nossa base de dados carregada. Agora, iremos fazer a divisão em dados de treino e dados de teste, igual ao projeto prático da capítulo de dados ausentes:

```Python
treino_df, teste_df = train_test_split(pacientes, teste_size=0.3, random_state=42)
```

Vamos criar os imputadores para cada coluna com dados ausentes da seguinte forma:

```Python
mean_imputer = MeanMedianImputer(imputation_method='mean', variables=['peso', 'altura'])
median_imputer = MeanMedianImputer(imputation_method='median', variables=['indice_massa_corporal'])
categorical_imputer = CategoricalImputer(imputation_method='frequent', variables=['tipo_sangue', 'hipertensao', 'diabetes'])

pacientes = mean_imputer.fit_transform(pacientes)
pacientes = median_imputer.fit_transform(pacientes)
pacientes = categorical_imputer.fit_transform(pacientes)

pacientes.head()
```

Desta forma, fizemos as imputações necessárias e agora, se fizermos a contagem de dados ausentes novamente, não aparecerá nada ao executarmos o código a seguir:

```Python
num_valores_ausentes = pacientes.isnull().sum()
ausentes_porcentagem = (num_valores_ausentes / len(pacientes)) * 100


missing_info = [(col, num_valores_ausentes[col], ausentes_porcentagem[col]) for col in pacientes.columns if num_valores_ausentes[col] > 0]

for col, num_valores_ausentes, ausentes_porcentagem in missing_info:
    print(f"Coluna: {col}, Dados ausentes: {num_valores_ausentes}, Porcentagem: {ausentes_porcentagem:.2f}%")
```

Mostrando que não temos mais valores ausentes em nosso DataFrame, agora, podemos ir para o próximo tópico do nosso checklist.

## Análise de Outliers

A análise de outliers é uma etapa essencial no processamento de dados, especialmente em conjuntos de dados como o de pacientes que estamos lidando. Outliers são observações que se desviam significativamente dos padrões normais dos dados. Identificar e tratar outliers é crucial para garantir a qualidade e a precisão das análises subsequentes, bem como para melhorar a eficácia dos modelos preditivos. Entretanto, o tratamento de outliers possui seus prós e contras que veremos a seguir:



Prós:

1. Melhoria na performance dos modelos: Tratar outliers pode melhorar a acurácia e a robustez do modelos preditivos. Modelos menor influenciados por valores extremos tendem a generalizar melhor para novos dados.

2. Aumento da precisão Estatística: A remoção ou ajuste de outliers pode levar a estimativas mais precisas das estatísticas descritivas, como média e variância, resultando em análises mais confiáveis

3. Correção de erros: Indetificar e corrigir outliers pode ajudar a resolver erros de entrada  de dados, garantindo que a análise se baseie em informações corretas e relevantes


Contras

1. Perda de informação: Remover outliers pode levar à perda de dados valiosos. Em alguns casos, outliers podem representar variações importantes ou condições raras que são relevantes para a análise.

2. Decisões Arbitrárias: O tratamento de outliers pode envolver decisões subjetivas sobre o que constitui um outlier e o que fazer com esses dados. Isso pode introduzir viés ou alterar a interpretação dos resultados.

3. Impacto na representatividade dos dados: Alterar ou remover outliers pode afetar a representatividade dos dados, especialmente se esses valores extremos estiverem associados a grupos específicos ou condições únicas que são importantes para o resultado.

Então, antes de prosseguirmos, teremos que averiguar se temos algum outlier em nosso dataframe. Para isso, vamos analisar o nosso dataframe que recordaremos a seguir:


### Tratamento da Coluna de Pressão Sanguínea


Antes de prosseguirmos, vamos tratar a coluna de pressão sanguínea. Observamos que temos os valores de mínimo e máximo separadados com um hífen. Com a variável dessa forma, não consiguiremos analisar com precisão os outliers. Então, antes, temos que transfromaá-la em duas outras colunas, uma para os valores mínimos e outra para os valores máximos, e depois apagar a coluna de pressão sanguínea. Para isso, usaremos o seguinte código:

```Python
pacientes[['pressao_min', 'pressao_max']] = pacientes['pressao'].str.split('-', expand=True).astype(float)

pacientes.drop(columns=['pressao'],inplace= True)
```

com isso temos o novo dataframe:


### Identificação de Outliers

Agora, vamos criar um código para calcular os outliers do nosso dataframe:

```Python
def identify_outliers(df, columns):
    outliers = {}
    for col in columns:
        Q1 = df[col].quantile(0.25)
        Q3 = df[col].quantile(0.75)
        IQR = Q3 - Q1
        lower_bound = Q1 - 1.5 * IQR
        upper_bound = Q3 + 1.5 * IQR
        outlier_indices = df[(df[col] < lower_bound) | (df[col] > upper_bound)].index
        outliers[col] = len(outlier_indices)
    return outliers
```

A função que fizemos irá calcular os possíveis outliers das colunas. Vamos executar a função com o seguinte código:

```Python
outliers = identify_outliers(pacientes, num_cols)
print("Número de outliers por coluna:")
for col, count in outliers.items():
    print(f"{col}: {count} outliers")
```
Com o código executado, temos os seguintes resultados:

```Python
Número de outliers por coluna:
id: 0 outliers
idade: 0 outliers
peso: 9 outliers
altura: 3 outliers
indice_massa_corporal: 33 outliers
dias_internado: 2 outliers
pressao_min: 5 outliers
pressao_max: 0 outliers
```
Agora que sabemos quais colunas possuem outilers, vamos tratá-los.

### Tratamento de Outliers com Winsorização

Para tratarmos esses outliers escolhemos a Winsorização. 

A winsorização é uma técnica de tratamento de outliers que substitui os valores extremos em uma variável por valores mais próximo aos percentis extremos definidos, geralmente o 1º percentil e o 99º percentil. Em dados de saúde, como pressão sanguínea, essa abordagem pode ser particularmente útil. 

*Prós da Winsorização*

A Winsorização é uma técnica de tratamento de outliers que substitui os valores extremos em uma variável por valores mais próximos aos percentis extremos definidos, geralmente o percentil 1 e o percentil 99. Em dados de saúde, como pressão sanguínea, essa abordagem pode ser particularmente útil, porém esse tratamento possuí seus pros e contras que veremos a seguir:

Prós da Winsorização

1. Redução da influência dos outliers: A winsorização ajuda a mitigar o impacto dos valores extremos nos modelos estatísticos e análises. Isso é crucial em dados de saúde, onde outliers podem distorcer a interpretação dos dados e levar a concusões incorretas sobre padrões de saúde.

2. Preservação da estrutura dos dados: Ao invés de simplesmente remover os outliers, a winsorização mantém todos os dados no conjunto, mas ajusta os extremos. isso preserva a estrutura dos dados e evita a perda potencial de informações valiosas.

3. Facilita modelagem estatística: Muitos algoritmos de machine learning e técnicas estatísicas podem ser sensíveis a outliers. A winsorização pode melhorar a performance de modelos ao reduzir o impacto desproporcional de valores extremos, resultando em modelos mais robustos.


*Contras da Winsorização*

1. Perda de informações extremas: Ao substituir valores extremos por valores truncados, pode-se perder informações importantes sobre variabilidades raras e extremas, que podem ser clinicamente relevantes para a saúde.

2. Não resolve todos os problemas: Embora a winsorização seja eficaz para lidar com outliers, ela não resolve todos os problemas relacionadaos a dados extremos. Algumas variáveis podem ainda apresentar distorções que necessitam de outras formas de tratamento.

3. Pode ocultar padrões reais: Em algumas situações, os outliers podem representar padrões ou condições raras que são significativas para compreensão da saúde. 


Para fazer a winsorização iremos utilizar a biblioteca `feature_engine` e para isso você deve ter ela instalada `pip install feature-engine` ou se você estiver usando o google colab o código é `!pip install feature-engine`. Após isso vamos importar a biblioteca com o código a seguir:

```Python
from feature_engine.outlier_removers import Winsorizer
```

Importamos a biblioteca e agora vamos instânciar nossa classe `Winsorizer`:

```Python
winsorizador = Winsorizer(capping_method="quantiles",tail='both',missing_values = 'ignore', variables=['peso','altura','indice_massa_corporal','dias_internado','pressao_min'])
```

Com isso instanciamos nosso Winsorizador para levar em consideração como outliers os valores acima dos 95% e abaixo dos 5% do nosso conjunto de valores. Vamos treinar nosso algoritmo:

```Python
winsorizador.fit(pacientes)
```

E vamos transformar os nossos dados:

```Python
pacientes_sem_outliers = winsorizador.transform(pacientes)
```

usando o código a seguir para procurar outliers vemos que não temos mais outliers em nosso conjunto de dados, veja:

```Python
num_cols = pacientes_sem_outliers.select_dtypes(include=['float64', 'int64']).columns


outliers_sem = identify_outliers(pacientes_sem_outliers, num_cols)


print("Número de outliers por coluna:")
for col, count in outliers_sem.items():
    print(f"{col}: {count} outliers")
```

e o resultado é o seguinte:

```Python
Número de outliers por coluna:
id: 0 outliers
idade: 0 outliers
peso: 0 outliers
altura: 0 outliers
indice_massa_corporal: 0 outliers
dias_internado: 0 outliers
pressao_min: 0 outliers
pressao_max: 0 outliers
```

e podemos ir para o próximo tópico do nosso checklist.

## Engenharia de features temporais

A engenharia de features temporais envolve a criação das colunas de dia, mês e ano e a exclusão da coluna data. Dessa forma, conseguimos analisar, por exemplo, quais os dias da semana têm mais entradas e de quais tipos de pacientes. Com a coluna mês, identificamos os meses de pico de entradas, o que ajuda a planejar o quadro de funcionários e evitar quedas no número de médicos durante períodos críticos. Porém, como em todos os casos a criação de novas _features_ tem seus prós e contras que veremos a seguir:

*Prós*

1. Melhoria na análise de dados: Permite uma análise mais detalhada e precisa dos dados temporais, facilitando a identificação de padrões sazonais.

2. Planejamento Estratégico: Auxilia no planejamento estratégico da empresa, como a criação de cronogramas de férias para médicos.

*Contras*

1. Complexidade Adicional: A criação de múltiplas novas colunas pode aumentar a complexidade do dataframe, tornando-o mais difícl de manusear.

2. Risco de Erros: Ao transformar e manipular dados, há sempre um risco de introduzir erros, especialmente se os dados originais contêm incosistências.

3. Espaço em disco: O aumento no número de colunas pode levar a um maior consumo de espaço em disco, especialmente em dataframes muito grandes.

Para fazermos essa procedimento temos que que verificar os tipos das nossas variáveis e para isso vamos utilizar o código a seguir:


```Python
tipos = pacientes.dtypes.reset_index()
tipos.columns = ['coluna','tipo']
```

Isso nos dá o seguinte dataframe:

|index|coluna|tipo|
|---|---|---|
|0|id|int64|
|1|data_admissao|object|
|2|idade|int64|
|3|peso|float64|
|4|altura|float64|
|5|indice_massa_corporal|float64|
|6|sexo|object|
|7|tipo_sangue|object|
|8|doenca_cronica|bool|
|9|hipertensao|object|
|10|diabetes|object|
|11|cirurgia|bool|
|12|uti|bool|
|13|pressao_sanguinea|object|
|14|dias_internado|int64|

A coluna `data_admissao` é do tipo `object`, sendo assim, precisamos transformá-la para o timpo `datetime` com o seguinte código:

```Python
pacientes['data_admissao'] = pd.to_datetime(pacientes['data_admissao'], format='%Y-%m-%d')
```
Após a transformação, os tipo ficam assim:

|index|coluna|tipo|
|---|---|---|
|0|id|int64|
|1|data_admissao|datetime64[ns]|
|2|idade|int64|
|3|peso|float64|
|4|altura|float64|
|5|indice_massa_corporal|float64|
|6|sexo|object|
|7|tipo_sangue|object|
|8|doenca_cronica|bool|
|9|hipertensao|object|
|10|diabetes|object|
|11|cirurgia|bool|
|12|uti|bool|
|13|pressao_sanguinea|object|
|14|dias_internado|int64|

Agora, podemos criar as colunas de ano, mês e dia com o código a seguir:

```Python
pacientes['Ano'] = pacientes['data_admissao'].dt.year
pacientes['Mês'] = pacientes['data_admissao'].dt.month
pacientes['Dia'] = pacientes['data_admissao'].dt.day_name()
```

Olhando para o nosso `dataframe` vemos que os dados estão em inglês. 

|index|id|data\_admissao|idade|peso|altura|indice\_massa\_corporal|sexo|tipo\_sangue|doenca\_cronica|hipertensao|diabetes|cirurgia|uti|pressao\_sanguinea|dias\_internado|Ano|Mês|Dia|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|0|1|2015-06-20 00:00:00|62|NaN|1\.78|18\.34|NaN|O+|true|false|false|false|false|13-16|10|2015|6|Saturday|
|1|2|2023-10-16 00:00:00|16|79\.91|1\.66|28\.97|Masculino|B+|true|false|false|false|false|14-19|6|2023|10|Monday|
|2|3|2020-08-16 00:00:00|72|77\.13|1\.55|NaN|Masculino|A+|true|false|false|false|false|3-7|6|2020|8|Sunday|
|3|4|2018-09-08 00:00:00|32|89\.78|1\.61|34\.63|NaN|O+|false|false|false|false|false|14-16|4|2018|9|Saturday|
|4|5|2012-04-07 00:00:00|83|66\.85|1\.57|NaN|Feminino|B+|false|true|true|false|false|14-17|7|2012|4|Saturday|

Para traduzirmos para português, usamos um dicionário:

```Python

dias_semana = {
    'Monday': 'Segunda-feira',
    'Tuesday': 'Terça-feira',
    'Wednesday': 'Quarta-feira',
    'Thursday': 'Quinta-feira',
    'Friday': 'Sexta-feira',
    'Saturday': 'Sábado',
    'Sunday': 'Domingo'
}

df['Dia'] = df['Dia'].map(dias_semana)
```

Olhando novamente o dataframe temos o seguinte:

|index|id|data\_admissao|idade|peso|altura|indice\_massa\_corporal|sexo|tipo\_sangue|doenca\_cronica|hipertensao|diabetes|cirurgia|uti|pressao\_sanguinea|dias\_internado|Ano|Mês|Dia|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|0|1|2015-06-20 00:00:00|62|NaN|1\.78|18\.34|NaN|O+|true|false|false|false|false|13-16|10|2015|6|Sábado|
|1|2|2023-10-16 00:00:00|16|79\.91|1\.66|28\.97|Masculino|B+|true|false|false|false|false|14-19|6|2023|10|Segunda-feira|
|2|3|2020-08-16 00:00:00|72|77\.13|1\.55|NaN|Masculino|A+|true|false|false|false|false|3-7|6|2020|8|Domingo|
|3|4|2018-09-08 00:00:00|32|89\.78|1\.61|34\.63|NaN|O+|false|false|false|false|false|14-16|4|2018|9|Sábado|
|4|5|2012-04-07 00:00:00|83|66\.85|1\.57|NaN|Feminino|B+|false|true|true|false|false|14-17|7|2012|4|Sábado|


Agora, temos os nossos dados em português facilitando assim para quem for analisar os dados, desta forma podemos excluir a coluna `data_admissao` com o seguinte código:

```Python
pacientes.drop(columns=['data_admissao'],inplace=True)
```

Com isso conseguimos terminar nossa outro checklist e podemos prosseguir para o proximo tópico.


## Criação de novas features

Nesse novo tópico vamos criar novas colunas para conseguirmos criar outras analises. Vamos criar as seguintes colunas:

* `idade avançada` (`True` se `idade`>65, caso contrário `False`)
* `imc_categoria` (Categoria de IMC: abaixo do peso, normal, sobrepeso, obeso)
* `admissao_fim_semana` ( True se admissão na sexta-feira ou sábado)
* `internacao_fase_ferias` (`True` se `data_admissão` for junho ou julho)


Porém esse tipo de transformação possui seus prós e contras que veremos a seguir:

*Prós*

1. Melhoria na análise de dados: A criação de novas features permite análises mais detalhadas e insights profundos sobre os dados.

2. Planejamento estratégico: Auxilia na identificação de padrões que podem ser usados para planejamento e tomada de decisão, como a alocação de recursos durante picos de internação.

3. Flexibilidade: Porporciona a flexibilidade para realizar diferentes tipos de análise, como estudar a correlação entre diferentes variáveis.

*Contras*

1. Complexidade Adicional: Adicionar muitas colunas pode tornar o dataframe mais complexo e difícil de gerenciar.

2. Risco de erros; Durante a transformação e manipulação dos dados, há risco de introduzir erros, especialmente se houver incosistências nos dados originais.

3. Espaço em Disco: O aumento no número de colunas pode levar a um maior consumo de espaço em disco, especialmente em dataframes grandes.


## Criação das novas colunas

1. Coluna `idade_avancada`: Primeiro, vamos criar a coluna `idade_avancada`. Com ela, podemos analisar a quantidade de pessoas com mais de 65 anos por dia, até a quantidade de pessoas por sexo com idade avançada que entram por dia. Para isso, usamos o seguinte código:

```Python
pacientes['idade_avancada'] = pacientes['idade'] > 65
```
Assim, a coluna `idade_avancada` terá `True` para idades maiores que 65 e `False` para idades menores ou iguais a 65 anos.

2. Coluna `imc_categoria`: Outra coluna importante é a `imc_categoria`, que classifica o IMC das pessoas em categorias: `Desconhecido`, `Abaixo do peso`, `Normal`, `Sobrepeso`, `Obeso`. Isso nos permite observar a relação entre o IMC das pessoas e o tempo de internamento. Para isso, criamos uma função para fazer um mapeamento e gerar uma nova coluna:

>Desconhecido
> Iremos inserir uma categória `desconhecido` para caso tenha algum valor faltante no futuro iremos conseguir 
> capturar os dados faltantes com uma categória faltante e desta forma conseguiremos tratar de forma mais rápida

```Python
def categoria_imc(imc):
    if pd.isna(imc):
        return 'Desconhecido'
    elif imc < 18.5:
        return 'Abaixo do peso'
    elif 18.5 <= imc < 25:
        return 'Normal'
    elif 25 <= imc < 30:
        return 'Sobrepeso'
    else:
        return 'Obeso'
```

Aplicamos essa função ao datarame com o código:

```Python
pacientes['imc_categoria'] = pacientes['indice_massa_corporal'].apply(categoria_imc)
```

3. Colunas `admissao_fim_semana` e `internacao_fase_ferias`: As colunas `admissao_fim_semana` e `internacao_fase_ferias` são criadas ao mesmo tempo, pois ambas têm a finalidade de visualizar a distribuição das internações pelo dia da semana e se ocorrem mais na época das férias. Para isso, usamos o seguinte código:

```Python
pacientes['admissao_fim_semana'] = pacientes['Dia'].isin(['Sexta-feira', 'Sábado'])
pacientes['internacao_fase_ferias'] = pacientes['Mês'].isin([6, 7])
```
E com isso temos o seguinte dataframe:


Note que não excluímos a coluna com os valores do índice de massa corporal. isso oorre porque com os valores numéricos podemos eventualmente criar um gráfico ou um algoritmo de regressão linear para identificar a relação entre os dias internados e o índice de massa corporal. Nem sempre é pertinente excluir colunas quando conseguimos extrair suas características em novas colunas. 

> Como saber se devemos excluir uma coluna?
> Um método para sabermos se devemos excluir uma 
> coluna ou não é utilizar o PCA para analisar a  
> importância de cada coluna para explicar o
> nosso conjunto de dados, desta forma você
> conseguirá fundamentar com base em dados a 
> exclusão ou não as suas colunas.

## Análise de Raridade das Features

Nessa parte vamos analisar as chamadas categorias raras, ou seja, aquelas que aparecem poucas vezes no dataframe, definidas como aquelas que correspondem a 5% ou menso dos dados. Vamos focar nas seguintes colunas:

* Tipo Sangue
* Dia
* IMC categoria

Para isso usaremos o código a seguir:

```Python
def categoria_rara(df, columns, threshold=0.05):
    rare_categories = {}
    
    for col in columns:
        freq = df[col].value_counts(normalize=True)
        
        rare = freq[freq < threshold].index
        
        if not rare.empty:
            rare_categories[col] = rare.tolist()
    
    return rare_categories

rare_categories = categoria_rara(pacientes,['tipo_sangue','imc_categoria','Dia'] , threshold=0.05)

print("Categorias raras em cada coluna:")
for col, categories in rare_categories.items():
    print(f"{col}: {categories}")
```

E com esse código temos o seguinte retorno:

```Python
Categorias raras em cada coluna:
tipo_sangue: ['AB+', 'AB-', 'B-']
```

Ou seja, a única coluna que temos categorias raras é a coluna `tipo_sangue`, com as seguintes categórias:

* AB+
* AB-
* B-

### Desafios das categorias Raras

Quando lidamos com dados de saúde, a presença de categorias raras pode introduzir desafios significativos, especialmente em relação ao equilíbrio do dataset e à qualidade das análises e modelos preditivos. Para lidar com esses dasafios, as técnicas de `upsampling` e `downsampling` são frequentemente empregadas, cada uma com seus prós e contras.


*Upsampling*

O upsampling aumenta a quantidade de amostras em categorias sub-representadas dentro de um dataset. isso pode ser feito replicando as existentes ou gerando novas amostras sintéticas.


*Prós*

1. Melhoria na representatividade: Aumenta a representatividade do modelo para todas as classes, melhorando a generalização e a precisão das predições.

2. Redução do viés: Reduz o viés do modelo, que pode ocorrer quando categorias raras são sub-representadas, melhorando a capacidade do modelo de aprender padrões relevantes

3. Maior detalhamento: Em áreas de saúda, ajuda a garantir que o modelo não ignore condições raras, cruciais para detecção precoce.


*Contras*

1. Sobrecarga computacional: Aumentar o número de amostras pode resultar em tempos de treinamento mais longos e maior uso de recursos.

2. Risco de overfitting: A replicação de instâncias ou geração de amostras sintéticas pode levar ao overfitting

3. Qualidade das amostras sintéticas: Se não feito corretamente, pode gerar amostras que não refletem a variabilidade real dos dados.

*Downsampling*

O downsampling reduz a quantidade de amostras em categorias majoritárias para equilibrar o dataset, elimando amostras da categoria majoritária.

*Prós*

1. Equilíbrio do Dataset: Ajuda a equilibras o número de amostras entre categorias, melhorando a capacidade do modelo de aprender padrões relevantes.

2. Redução da complexidade: Pode diminuir a complexidade do treinamento, reduzindo o tempo e os requisitos computacionais.

3. Menor risco de overfitting: reduz o risco de overfitting relacionado à classe majoritária.


*Contras*


1. Perda de informação: Eliminar amostras pode resultar na perda de informações valiosas.

2. Redução da variabilidade: Pode diminuir a variabilidade do dataset, resultando em um modelo menos robusto.

3. Viés introduzido: Pode introduzir um viés adicional, tornando o modelo menos representativo da variabilidade real dos dados.

Para nosso caso, usaremos a técnica de `upsampling`, pois temos poucos dados e a redução poderia levar à perda significativa de informação:

```Python
import pandas as pd
from sklearn.utils import resample
```
Definimos as categorias raras e a contagem de amostras:

```Python

categorias_raras = ['AB+', 'AB-', 'B-']

contagem = pacientes['tipo_sangue'].value_counts()
max_count = contagem.max()
```

Agora, duplicamos o dataframe e fazemos a contagem das categorias raras:
```Python
for categoria in categorias_raras:
    df_categoria = pacientes_upsampled[pacientes_upsampled['tipo_sangue'] == categoria]
```

Duplicar a contagem das categorias raras é uma técnica utilizada para balancear o dataset, garantido que todas as categorias tenham representatividade suficiente.

Por fim usamos o `resample` para replicar essas amostras até que o número de amostras seja igual ao `max_cunt` (número de amostras da categoria mais frequente)
```Python
  df_categoria_upsampled = resample(df_categoria, 
                                      replace=True,
                                      n_samples=max_count,
                                      random_state=42) 
    

    pacientes_upsampled = pd.concat([pacientes_upsampled, df_categoria_upsampled])

```
Então, o `resample` é comumente utilizado para replicar amostras 

## Normalização e Padronização

Nesta seção, realizaremos a normalização de nossos dados.

A padronização das variáveis numéricas é essencial em diversas análises de dados e algoritmos de machine learning. Esse processo transforma os dados para que tenham uma média de zero e um desvio padrão de um. Os principais motivos para realizar a padronização incluem:

1. Escalas diferentes: Em um conjunto de dados, diferentes variáveis podem ter escalas distintas. Por exemplo, a idade pode variar de 0 a 100 anos, enquanto o salário pode variar de centenas a milhares de unidades monetárias. A padronização coloca todas as vairáveis na mesma escala, o que é crucial para muitos algoritmos de machine learning que são sensíveis à escla das variáveis.

2. Melhor Desempenho de Algoritmos: Algoritmos como regressão linear, _k-means clustering_, e redes neurais são sensíveis às escalas das variáveis. A padronização pode melhorar a convergência e a precisão desses algoritmos.

3. Comparabilidade: Padronizar as variáveis facilita a comparação entre diferentes métricas e variáveis, permitindo uma interpretação mais intuitiva dos resultados


*Vantagens da Padronização*

1. melhoria no desempenho dos modelos: Muitos algoritmos de machine learning, como regressão logística e SVM, funcionam melhor e mais rapidamente quando as variáveis são padronizadas.

2. Prevenção de problemas de escala: Variáveis em escalas diferentes podem influenciar desproporcionalmente os resultados dos modelos. A padronização resolve esse problema, garantindo que todas as variáveis contribuam igualmente.

3. Facilita a análise de componentes principais (PCA): PCA e outras técnicas de redução de dimensionalidade funcionam melhor com dados padronizados, pois essas técnicas são baseadas na variância das variáveis.

4. Melhor convergência em algoritmos iterativos:Em algoritmos iterativos como o gradiente descendente, a padronização pode ajudar a alcançar uma convergência mais rápida.

*Contras da padronização*

1. Perda de interpretação direta: Os valores padronização não têm uma interpretação direta como os valores originais, o que pode ser um desafio para a comunicação dos resultados a partes interessadas não técnicas.

2. requer passos adicionais: A padronização adiciona uma etapa extra no pré-processamento dos dados, o que pode aumentar a complexidade do pipeline de dados.

3. Não adequado para todos os modelos: Nem todos os algoritmos de machine learning requerem padronização. Por exemplo, as árvores de decisão e algoritmos baseados em árvore (como random forest) não são afetados pela escla das variáveis.

4. Afeta Outliers: A presença de outliers pode influenciar a média e o desvio padrão, e, consequentemente, a padronização pode não ser eficaz se os outliers não forem tratados previamente.

Para esse dataframe escolhemos fazer o `StandarScale` com o `sckit-learning` para as seguintes variáveis:

* Idade
* Peso
* Altura
* Indice de massa corporal
* Pressão mínima
* Pressão Máxima

Vamos começar importando a biblioteca que utilizaremos:

```Python
import pandas as pd
from sklearn.preprocessing import StandardScaler
```

Vamos criar uma lista com as vaariáveis que desejamos padronizar:

```Python
# Definir as variáveis numéricas
variaveis_numericas = ['idade', 'peso', 'altura', 'indice_massa_corporal', 'pressao_min', 'pressao_max']
```

Vamos instanciar nosso `StandarScaler` e aplicar o scaler às variáveis númericas:

```Python
# Criar o objeto StandardScaler
standard_scaler = StandardScaler()

# Aplicar o scaler às variáveis numéricas
pacientes[variaveis_numericas] = standard_scaler.fit_transform(pacientes[variaveis_numericas])
```

E por fim vamos exibir os dados:

```Python
pacientes.head()
```

e o resultado pode ser visto a seguir:

|index|id|idade|peso|altura|indice\_massa\_corporal|sexo|tipo\_sangue|doenca\_cronica|hipertensao|diabetes|cirurgia|uti|dias\_internado|pressao\_min|pressao\_max|Ano|Mês|Dia|idade\_avancada|imc\_categoria|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|0|1|0\.4767879250004813|0\.002662386323167059|0\.8966102016787333|-1\.417327980467162|Masculino|O+|true|false|false|false|false|10|-0\.16279871661545897|-0\.6323352615458208|2015|6|Sábado|false|Abaixo do peso|
|1|2|-1\.0938397282523218|0\.623843474756021|-0\.4632591909031931|0\.9391732617969516|Masculino|B+|true|false|false|false|false|6|-1\.1245526815832645|-1\.0742187427099166|2023|10|Segunda-feira|false|Sobrepeso|
|2|3|0\.8182287191858733|0\.4218392722886035|-1\.7098061341032897|-0\.05397119402084998|Masculino|A+|true|false|false|false|false|6|-0\.774823967049517|-1\.7738675878864012|2020|8|Domingo|true|Normal|
|3|4|-0\.5475344575556946|1\.3410310568975354|-1\.0298714378123266|2\.061005297213467|Masculino|O+|false|false|false|false|false|4|1\.4984126774198414|1\.024727792819538|2018|9|Sábado|false|Obeso|
|4|5|1\.1938135927898044|-0\.3251402965477457|-1\.4831612353396353|-0\.05397119402084998|Feminino|B+|false|true|true|false|false|7|0\.27436217655172535|0\.4355498179340771|2012|4|Sábado|true|Normal|

Ao padronizarmos os dados, perdemos a referência original, mas podemos reverter a padronização com o seguinte código:

```Python
pacientes_revertidos = pacientes.copy()
pacientes_revertidos[variaveis_numericas] = standard_scaler.inverse_transform(pacientes[variaveis_numericas])

pacientes_revertidos.head()
```

Isso nos permite restaurar os dados para a escala original.

## Codificação de Features Categóricas

Para concluir nosso projeto de engenharia de recursos, faremos a codificação One-Hot encoded em nossa base de dados.

O One-Hot encoding é uma técnica amplamente utilizada para transformar variáveis categóricas em dados numéricas para modelos de aprendizado de máquina. Em nossa base de dados, aplicamos o One-Hot encoding para converter variáveis categóricas, como `sexo`, `tipo_sangue` e `doeca_cronica`, em presentações binárias que indicam a presença ou ausência de cada categoria.


*Prós do One-Hot Encoding*

1. Compatibilidade com Modelos de ML: Muitos algoritmos de aprendizado de máquina, como regressão logística e redes neurais, exigem entradas numéricas. O One-Hot encoding transforma variáveis categóricas em um formato que esses modelos podem processar diretamente.

2. Evita Assunções Errôneas: Ao representar cada categorica com uma coluna separada, o One-Hot encoding evita que o modelo interprete os valores categóricos como ordinais, o que poderia introduzir viés.

3. Interpretação Intuitiva: Cada nova coluna gerada pelo One-Hot encoding representa uma categoria específica, facilitando a interpretação dos dados e a visaulização das relações entre categorias e variáveis-alvo


*Contras do One-hot Encoding*

1. Aumento da Dimensionalidade: A principal desvantagem do One-hot encoding é o aumento significativo do número de colunas no dataset, especialmente quando a variável categórica possuí muitas categorias. Isso pode levar a problemas de escalabilidade e a um aumento no tempo de treinamento do modelo.

2. Esparsidade dos dados: A técnica gera matrizes esparsas, onde a maioria dos valores é zero. isso pode resultar em maior uso de memória e em eficência reduzida na manipulação dos dados

3. Perda de informação: Ao criar variáveis binárias, o One-hot encoding pode resultar na perda de informações sobre a frequência ou a ordem das categorias, o que ser relevante em alguns contextos. 

Para esse código iremos comesçar importando a biblioteca necessária:

```Python
from sklearn.preprocessing import OneHotEncoder
```

Em seguida instanciaremos o nosso `OneHotEncoder` da seguinte forma:

```Python
encoder = OneHotEncoder(sparse_output=False, drop='first')
```
Definimos `sparse_output=False` para que o código retorne codificada como uma matriz densa (dense matrix). Isso significa que todos os valores são armazenados explicitamente, o que pode consumir mais memória, mas facilita a manipulação dos dados, especialmente em conjuntos de dados menores. Também usamos `drop='first'` para excluir uma coluna do nosso `OneHotEncoder`.

Agora, vamos aplicar o `OneHotEncoder` em nossas variáveis categóricas e nas variáveis booleanas:

```Python
df_categoricas = pacientes[variaveis_categoricas]
df_booleanas = pacientes[variaveis_booleanas]
```

Por fim vamos transformar os dados:

```Python

df_encoded_categoricas = encoder.fit_transform(df_categoricas)


df_encoded_categoricas = pd.DataFrame(df_encoded_categoricas, columns=encoder.get_feature_names_out(variaveis_categoricas))


df_final = pd.concat([df_encoded_categoricas, df_booleanas.reset_index(drop=True)], axis=1)

print("Dados após One-Hot Encoding:")
df_final.head()
```

Nosso dataframe final ficou com 25 colunas após a transformação, por isso não o colocaremos aqui devido à limitação de espaço.


# Caros Leitores

Com a conclusão desta seção sobre feature engineering, chegamos ao fim de uma etapa fundamental em nosso projeto. Depois dessas transformações, avançaremos para outras fases igualmente importantes, como a visualização de dados e a aplicação de algoritmos de machine learning. Nosso objetivo é pegar o dataframe salvo e inseri-lo em modelos de aprendizado de máquina que poderão trazer insights valiosos e predições mais confiáveis.

Espero que tenham achado esta jornada tão enriquecedora quanto eu. A verdade é que a jornada de vocês está apenas começando. No mundo da ciência de dados e aprendizado de máquina , novas técnicas e algoritmos estão sempre surgindo, oferencendo oportunidades para continuar explorando e aprendendo.

Que este livro sirva como ponto de partida para uma carreira cheia de dscobertas e inovações. O conhecimento é um caminho sem fim, e cada passo que damos nos aproxima mais da próxima grande descoberta.

Até a próxima!

Atenciosamente,

Thiago Luiz Benevides