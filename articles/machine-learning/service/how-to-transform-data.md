---
title: 'Transformações: SDK do Python de preparação de dados'
titleSuffix: Azure Machine Learning service
description: Saiba mais sobre a transformação e a limpeza de dados com o SDK de preparação de dados do Azure Machine Learning. Use métodos de transformação para adicionar colunas, filtrar linhas ou colunas indesejadas e acrescentar valores ausentes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: jmartens
ms.date: 07/16/2019
ms.custom: seodec18
ms.openlocfilehash: 08cf646d63e1a295a1bc2ff28180983cc462f084
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360921"
---
# <a name="transform-data-with-the-azure-machine-learning-data-prep-sdk"></a>Transformar dados com o SDK de preparação de dados do Azure Machine Learning

Neste artigo, você aprenderá métodos diferentes para transformar dados usando o `azureml-dataprep` pacote. O pacote oferece funções que tornam simples a adição de colunas, o filtro de linhas ou colunas indesejadas e a imputar de valores ausentes. Consulte a documentação de referência completa para o [pacote azureml-dataprep](https://aka.ms/data-prep-sdk).

> [!Important]
> Se você estiver criando uma nova solução, experimente os [conjuntos](how-to-explore-prepare-data.md) de dados Azure Machine Learning (versão prévia) para transformar seus dados, fazer instantâneos e armazenar as definições de conjunto de dados com controle de versão. Conjuntos de dados é a próxima versão do data Prep SDK, oferecendo funcionalidade expandida para o gerenciamento de conjuntos de dados em soluções de ia. Se você usar o `azureml-dataprep` pacote para criar um fluxo de os com suas transformações em vez `azureml-datasets` de usar o pacote para criar um conjunto de um, não será possível usar instantâneos ou conjuntos de valores com controle de versão mais tarde.

Este "como" mostra exemplos para as seguintes tarefas:

- Adicionar coluna usando uma expressão
- [Acrescentar valores ausentes](#impute-missing-values)
- [Derivar colunas por exemplo](#derive-column-by-example)
- [Filtragem](#filtering)
- [Transformações personalizadas de Python](#custom-python-transforms)

## <a name="add-column-using-an-expression"></a>Adicionar coluna usando uma expressão

O SDK de preparação de dados do Azure Machine Learning inclui expressões de `substring` que você pode usar para calcular um valor de colunas existentes e, em seguida, colocar esse valor em uma nova coluna. Neste exemplo, você carrega dados e tenta adicionar colunas a esses dados de entrada.

```Python
import azureml.dataprep as dprep

# loading data
dflow = dprep.read_csv(path=r'data\crime0-10.csv')
dflow.head(3)
```

||ID|Número do Caso|Date|Bloquear|IUCR|Texto Primário|Descrição|Descrição do Local|Detenção|Nacional|...|Ward|Área da Comunidade|Código do FBI|Coordenada X|Coordenada Y|Ano|Atualizado Em|Latitude|Longitude|Location|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|
|0|10140490|HY329907|05/07/2015 11:50:00 pm|050XX N NEWLAND AVE|0820|ROUBO|US $500 E ABAIXO DE|RUA|false|false|...|41|10|06|1129230|1933315|2015|12/07/2015 12:42:46 pm|41,973309466|-87,800174996|(41,973309466, -87,800174996)|
|1|10139776|HY329265|05/07/2015 11:30:00 pm|011XX W MORSE AVE|0460|BATERIA|SIMPLES|RUA|false|true|...|49|1|08B|1167370|1946271|2015|12/07/2015 12:42:46 pm|42,008124017|-87,65955018|(42,008124017, -87,65955018)|
|2|10140270|HY329253|05/07/2015 11:20:00 pm|121XX S FRONT ALVAR|0486|BATERIA|BATERIA DOMÉSTICA SIMPLE|RUA|false|true|...|9|53|08B|||2015|12/07/2015 12:42:46 pm|


Use a expressão `substring(start, length)` para extrair o prefixo da coluna Número do caso e colocar essa sequência em uma nova coluna, `Case Category`. Passar a variável `substring_expression` para o parâmetro `expression` cria uma nova coluna calculada que executa a expressão em cada registro.

```Python
substring_expression = dprep.col('Case Number').substring(0, 2)
case_category = dflow.add_column(new_column_name='Case Category',
                                    prior_column='Case Number',
                                    expression=substring_expression)
case_category.head(3)
```

||ID|Número do Caso|Categoria do Caso|Date|Bloquear|IUCR|Texto Primário|Descrição|Descrição do Local|Detenção|Nacional|...|Ward|Área da Comunidade|Código do FBI|Coordenada X|Coordenada Y|Ano|Atualizado Em|Latitude|Longitude|Location|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|------|
|0|10140490|HY329907|HY|05/07/2015 11:50:00 pm|050XX N NEWLAND AVE|0820|ROUBO|US $500 E ABAIXO DE|RUA|false|false|...|41|10|06|1129230|1933315|2015|12/07/2015 12:42:46 pm|41,973309466|-87,800174996|(41,973309466, -87,800174996)|
|1|10139776|HY329265|HY|05/07/2015 11:30:00 pm|011XX W MORSE AVE|0460|BATERIA|SIMPLES|RUA|false|true|...|49|1|08B|1167370|1946271|2015|12/07/2015 12:42:46 pm|42,008124017|-87,65955018|(42,008124017, -87,65955018)|
|2|10140270|HY329253|HY|05/07/2015 11:20:00 pm|121XX S FRONT ALVAR|0486|BATERIA|BATERIA DOMÉSTICA SIMPLE|RUA|false|true|...|9|53|08B|||2015|12/07/2015 12:42:46 pm|


Use a `substring(start)` expressão para extrair apenas o número da coluna de número de casos e criar uma nova coluna. Convertê-lo em um tipo de dados numéricos usando a `to_number()` função e passar o nome da coluna de cadeia de caracteres como um parâmetro.

```Python
substring_expression2 = dprep.col('Case Number').substring(2)
case_id = dflow.add_column(new_column_name='Case Id',
                              prior_column='Case Number',
                              expression=substring_expression2)
case_id = case_id.to_number('Case Id')
```

## <a name="impute-missing-values"></a>Acrescentar valores ausentes

O SDK pode imputar valores ausentes em colunas especificadas. Neste exemplo, você carrega valores de latitude e longitude e, em seguida, tenta imputar valores ausentes nos dados de entrada.

```python
import azureml.dataprep as dprep

# loading input data
dflow = dprep.read_csv(r'data\crime0-10.csv')
dflow = dflow.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
dflow = dflow.to_number(['Latitude', 'Longitude'])
dflow.head(3)
```

||ID|Detenção|Latitude|Longitude|
|-----|------|-----|------|-----|
|0|10140490|false|41,973309|-87,800175|
|1|10139776|false|42,008124|-87,659550|
|2|10140270|false|NaN|NaN|

O terceiro registro não tem valores de latitude e longitude. Para imputar esses valores ausentes, use [`ImputeMissingValuesBuilder`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py) para aprender uma expressão fixa. Ele pode imputar as colunas com um valor calculado de `MIN`, `MAX`, `MEAN` ou um valor `CUSTOM`. Quando `group_by_columns` for especificado, serão inseridos valores ausentes pelo grupo com `MIN`, `MAX` e `MEAN` calculados por grupo.

Verifique o `MEAN` valor da coluna latitude usando a [`summarize()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#summarize-summary-columns--typing-union-typing-list-azureml-dataprep-api-dataflow-summarycolumnsvalue---nonetype----none--group-by-columns--typing-union-typing-list-str---nonetype----none--join-back--bool---false--join-back-columns-prefix--typing-union-str--nonetype----none-----azureml-dataprep-api-dataflow-dataflow) função. Essa função aceita uma matriz de colunas no parâmetro `group_by_columns` para especificar o nível de agregação. O parâmetro `summary_columns` aceita uma chamada `SummaryColumnsValue`. Essa chamada de função especifica o nome da coluna atual, o novo nome de campo calculado e o `SummaryFunction` a ser executado.

```python
dflow_mean = dflow.summarize(group_by_columns=['Arrest'],
                             summary_columns=[dprep.SummaryColumnsValue(column_id='Latitude',
                                                                        summary_column_name='Latitude_MEAN',
                                                                        summary_function=dprep.SummaryFunction.MEAN)])
dflow_mean = dflow_mean.filter(dprep.col('Arrest') == 'false')
dflow_mean.head(1)
```

||Detenção|Latitude_MEAN|
|-----|-----|----|
|0|false|41,878961|

O valor de `MEAN` das latitudes parece preciso, use a função `ImputeColumnArguments` para imputá-lo. Esta função aceita uma cadeia de caracteres `column_id` e uma `ReplaceValueFunction` para especificar o tipo de impute. Para o valor de longitude faltando, imputa-lo com 42 base no conhecimento externo.

Etapas de imputação podem ser encadeadas em um objeto `ImputeMissingValuesBuilder`, usando a função de construtor `impute_missing_values()`. O parâmetro `impute_columns` aceita uma matriz de `ImputeColumnArguments` objetos. Chame a função `learn()` para armazenar as etapas de impute e, em seguida, aplique a um objeto de fluxo de dados usando `to_dataflow()`.

```python
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id='Latitude',
                                          impute_function=dprep.ReplaceValueFunction.MEAN)
# impute with custom value 42
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=42)
# get instance of ImputeMissingValuesBuilder
impute_builder = dflow.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                      group_by_columns=['Arrest'])

impute_builder.learn()
dflow_imputed = impute_builder.to_dataflow()
dflow_imputed.head(3)
```

||ID|Detenção|Latitude|Longitude|
|-----|------|-----|------|-----|
|0|10140490|false|41,973309|-87,800175|
|1|10139776|false|42,008124|-87,659550|
|2|10140270|false|41,878961|42,000000|

Conforme mostrado no resultado acima, a latitude ausente foi inserida com o valor `MEAN` do grupo `Arrest=='false'`. A longitude ausente foi acrescentada com 42.

```python
imputed_longitude = dflow_imputed.to_pandas_dataframe()['Longitude'][2]
assert imputed_longitude == 42
```

## <a name="derive-column-by-example"></a>Derivar colunas por exemplo

Uma das ferramentas mais avançadas no SDK de preparação de dados de aprendizado de máquina do Azure é a capacidade de derivar colunas usando exemplos de resultados desejados. Isso permite que você dê um exemplo ao SDK para gerar código para alcançar a transformação pretendida.

```python
import azureml.dataprep as dprep
dflow = dprep.read_csv(
    path='https://dpreptestfiles.blob.core.windows.net/testfiles/BostonWeather.csv')
dflow.head(4)
```

||DATA|REPORTTPYE|HOURLYDRYBULBTEMPF|HOURLYRelativeHumidity|HOURLYWindSpeed|
|----|----|----|----|----|----|
|0|1/1/2015 0:54|FM-15|22|50|10|
|1|1/1/2015 1:00|FM-12|22|50|10|
|2|1/1/2015 1:54|FM-15|22|50|10|
|3|1/1/2015 2:54|FM-15|22|50|11|

Suponha que você precise unir esse arquivo com um conjunto de dados em que a data e a hora estejam em um formato '10 de março de 2018|2 AM-4AM'.

```python
builder = dflow.builders.derive_column_by_example(
    source_columns=['DATE'], new_column_name='date_timerange')
builder.add_example(
    source_data=dflow.iloc[1], example_value='Jan 1, 2015 12AM-2AM')
builder.preview(count=5)
```

||DATA|date_timerange|
|----|----|----|
|0|1/1/2015 0:54|1 de janeiro de 2015 0:00 - 2:00|
|1|1/1/2015 1:00|1 de janeiro de 2015 0:00 - 2:00|
|2|1/1/2015 1:54|1 de janeiro de 2015 0:00 - 2:00|
|3|1/1/2015 2:54|1 de janeiro de 2015 2:00 - 4:00|
|4|1/1/2015 3:54|1 de janeiro de 2015 2:00 - 4:00|

O código anterior primeiro cria um construtor para a coluna derivada. Você fornece uma matriz de colunas de origem a serem consideradas (`DATE`) e um nome para a nova coluna a ser adicionada. Como o primeiro exemplo, você passa na segunda linha (índice 1) e fornece um valor esperado para a coluna derivada.

Finalmente, você chama `builder.preview(skip=30, count=5)` e pode ver a coluna derivada ao lado da coluna de origem. O formato parece correto, mas você só vê valores para a mesma data "1º de janeiro de 2015".

Agora, passe o número de linhas que você deseja `skip` da parte superior para ver as linhas mais abaixo.

> [!NOTE]
> A função Preview () ignora as linhas, mas não renumera o índice de saída. No exemplo a seguir, o índice 0 na tabela corresponde ao índice 30 no fluxo de os.

```python
builder.preview(skip=30, count=5)
```

||DATA|date_timerange|
|-----|-----|-----|
|0|1/1/2015 22:54|1 de janeiro de 2015 22:00 - 00:00|
|1|1/1/2015 23:54|1 de janeiro de 2015 22:00 - 00:00|
|2|1/1/2015 23:59|1 de janeiro de 2015 22:00 - 00:00|
|3|2/1/2015 0:54|1 de fevereiro de 2015 0:00 - 2:00|
|4|2/1/2015 1:00|1 de fevereiro de 2015 0:00 - 2:00|

Aqui você vê um problema com o programa gerado. Baseado apenas no exemplo fornecido acima, o programa derive escolheu analisar a data como "Dia/Mês/Ano", o que não é o que você deseja neste caso. Para corrigir esse problema, direcione um índice de registro específico e forneça outro exemplo usando `add_example()` a função `builder` na variável.

```python
builder.add_example(
    source_data=dflow.iloc[3], example_value='Jan 2, 2015 12AM-2AM')
builder.preview(skip=30, count=5)
```

||DATA|date_timerange|
|-----|-----|-----|
|0|1/1/2015 22:54|1 de janeiro de 2015 22:00 - 00:00|
|1|1/1/2015 23:54|1 de janeiro de 2015 22:00 - 00:00|
|2|1/1/2015 23:59|1 de janeiro de 2015 22:00 - 00:00|
|3|2/1/2015 0:54|2 de janeiro de 2015 0:00 - 2:00|
|4|2/1/2015 1:00|2 de janeiro de 2015 0:00 - 2:00|

Agora, as linhas lidam corretamente com ' 1/2/2015 ' como ' 2 de janeiro de 2015 ', mas se você olhar além do índice 76 da coluna derivada, verá que os valores no final não têm nada na coluna derivada.

```python
builder.preview(skip=75, count=5)
```


||DATA|date_timerange|
|-----|-----|-----|
|0|1/3/2015 7:00|3 de janeiro de 2015 6h – 8:00|
|1|1/3/2015 7:54|3 de janeiro de 2015 6h – 8:00|
|2|1/29/2015 6:54|Nenhum|
|3|1/29/2015 7:00|Nenhum|
|4|1/29/2015 7:54|Nenhum|

```python
builder.add_example(
    source_data=dflow.iloc[77], example_value='Jan 29, 2015 6AM-8AM')
builder.preview(skip=75, count=5)
```

||DATA|date_timerange|
|-----|-----|-----|
|0|1/3/2015 7:00|3 de janeiro de 2015 6h – 8:00|
|1|1/3/2015 7:54|3 de janeiro de 2015 6h – 8:00|
|2|1/29/2015 6:54|29 de janeiro de 2015 6h – 8:00|
|3|1/29/2015 7:00|29 de janeiro de 2015 6h – 8:00|
|4|1/29/2015 7:54|29 de janeiro de 2015 6h – 8:00|

 Para ver uma lista da chamada `list_examples()` de derivações de exemplo atual no objeto Builder.

```python
examples = builder.list_examples()
```

| |DATA|exemplo|example_id|
| -------- | -------- | -------- | -------- |
|0|1/1/2015 1:00|1 de janeiro de 2015 0:00 - 2:00|-1|
|1|2/1/2015 0:54|2 de janeiro de 2015 0:00 - 2:00|-2|
|2|29/11/2015 20:54|29 de janeiro de 2015 20:00 - 22:00|-3|


Em determinados casos, se você quiser excluir exemplos incorretos, poderá passar `example_row` do valor ou `example_id` do dataframe do pandas. Por exemplo, se você executar `builder.delete_example(example_id=-1)`, ele excluirá o primeiro exemplo de transformação.


Chame `to_dataflow()` no construtor, que retorna um fluxo de dados com as colunas derivadas desejadas adicionadas.

```python
dflow = builder.to_dataflow()
df = dflow.to_pandas_dataframe()
```

## <a name="filtering"></a>Filtrando

O SDK inclui os métodos [`drop_columns()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#drop-columns-columns--multicolumnselection-----azureml-dataprep-api-dataflow-dataflow) e [`filter()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py) para permitir que você filtre colunas ou linhas.

### <a name="initial-setup"></a>Configuração inicial

> [!Note]
> A URL neste mesmo exemplo não é uma URL completa. Em vez disso, ela se refere à pasta demo no BLOB. A URL completa para os dados é https://dprepdata.blob.core.windows.net/demo/green-small/green_tripdata_2013-08.csv

O que estamos fazendo no tutorial é carregar todos os arquivos dentro da pasta e agregar o resultado em green_df_raw e yellow_df_raw.

```python
import azureml.dataprep as dprep
from datetime import datetime
dflow = dprep.read_csv(
    path='https://dprepdata.blob.core.windows.net/demo/green-small/*')
dflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Store_and_fwd_flag|RateCodeID|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_longitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|
|1|01-08-2013 08:14:37|01-08-2013 09:09:06|N|1|0|0|0|0|1|.00|0|0|21.25|
|2|01-08-2013 09:13:00|01-08-2013 11:38:00|N|1|0|0|0|0|2|.00|0|0|75|
|3|01-08-2013 09:48:00|01-08-2013 09:49:00|N|5|0|0|0|0|1|.00|0|1|2,1|
|4|01-08-2013 10:38:35|01-08-2013 10:38:51|N|1|0|0|0|0|1|.00|0|0|3.25|

### <a name="filtering-columns"></a>Filtrando colunas

Para filtrar colunas, use `drop_columns()`. Esse método usa uma lista de colunas a serem descartadas ou um argumento [`ColumnSelector`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.columnselector?view=azure-dataprep-py)mais complexo chamado.

#### <a name="filtering-columns-with-list-of-strings"></a>Filtrando colunas com lista de cadeias de caracteres

Neste exemplo, `drop_columns()` usa uma lista de cadeias de caracteres. Cada cadeia de caracteres deve corresponder exatamente à coluna desejada para soltar.

```python
dflow = dflow.drop_columns(['Store_and_fwd_flag', 'RateCodeID'])
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_longitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|
|1|01-08-2013 08:14:37|01-08-2013 09:09:06|0|0|0|0|1|.00|0|0|21.25|

#### <a name="filtering-columns-with-regex"></a>Filtrando colunas com regex

Como alternativa, use a expressão `ColumnSelector` para descartar colunas que correspondam a uma expressão regex. Neste exemplo, você solta todas as colunas que correspondem à expressão `Column*|.*longitude|.*latitude`.

```python
dflow = dflow.drop_columns(dprep.ColumnSelector(
    'Column*|.*longitud|.*latitude', True, True))
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|
|1|01-08-2013 08:14:37|01-08-2013 09:09:06|1|.00|0|0|21.25|

## <a name="filtering-rows"></a>Filtrando dados

Para filtrar linhas, use `filter()`. Esse método usa uma expressão do SDK de preparação de dados do Azure Machine Learning como um argumento e retorna um novo fluxo de dados com as linhas que a expressão avalia como True. As expressões são criadas usando construtores de expressões (`col`, `f_not`, `f_and`, `f_or`) e operadores regulares (>, <, >=, <=, ==, !=).

### <a name="filtering-rows-with-simple-expressions"></a>Filtrando linhas com expressões simples

Use o construtor de expressões `col`, especifique o nome da coluna como um argumento de cadeia de caracteres `col('column_name')`. Use essa expressão em combinação com um dos seguintes operadores padrão>, <,> =, <=, ==,! = Para criar uma expressão como `col('Tip_amount') > 0`. Por fim, passe a expressão criada na função `filter()`.

Neste exemplo, `dflow.filter(col('Tip_amount') > 0)` retorna um novo fluxo de dados com as linhas em que o valor de `Tip_amount` é maior que 0.

> [!NOTE] 
> `Tip_amount` é primeiro convertido em números, o que nos permite criar uma expressão comparando-o a outros valores numéricos.

```python
dflow = dflow.to_number(['Tip_amount'])
dflow = dflow.filter(dprep.col('Tip_amount') > 0)
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|01-08-2013 19:33:28|01-08-2013 19:33:21|5|.00|0.08|0|4.58|
|1|05-08-2013 13:16:38|05-08-2013 13:18:24|1|.00|0.30|0|3.8|

### <a name="filtering-rows-with-complex-expressions"></a>Filtrar linhas com expressões complexas

Para filtrar usando expressões complexas, combine uma ou mais expressões simples com os construtores de expressões `f_not`, `f_and` ou `f_or`.

Neste exemplo, `dflow.filter()` retorna um novo fluxo de dados com as linhas em que o valor de `'Passenger_count'` é maior que 5 e `'Tolls_amount'` é maior que 0.

```python
dflow = dflow.to_number(['Passenger_count', 'Tolls_amount'])
dflow = dflow.filter(dprep.f_and(
    dprep.col('Passenger_count') < 5, dprep.col('Tolls_amount') > 0))
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|08-08-2013 12:16:00|08-08-2013 12:16:00|1.0|.00|2.25|5.00|19.75|
|1|12-08-2013 14:43:53|12-08-2013 15:04:50|1.0|5.28|6.46|5.33|32.29|

Também é possível filtrar linhas combinando mais de um construtor de expressões para criar uma expressão aninhada.

> [!NOTE]
> `lpep_pickup_datetime` e `Lpep_dropoff_datetime` são primeiro convertidos em números, o que nos permite criar uma expressão comparando-o a outros valores de data/hora.

```python
dflow = dflow.to_datetime(
    ['lpep_pickup_datetime', 'Lpep_dropoff_datetime'], ['%Y-%m-%d %H:%M:%S'])
dflow = dflow.to_number(['Total_amount', 'Trip_distance'])
mid_2013 = datetime(2013, 7, 1)
dflow = dflow.filter(
    dprep.f_and(
        dprep.f_or(
            dprep.col('lpep_pickup_datetime') > mid_2013,
            dprep.col('Lpep_dropoff_datetime') > mid_2013),
        dprep.f_and(
            dprep.col('Total_amount') > 40,
            dprep.col('Trip_distance') < 10)))
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|13-08-2013 06:11:06+00:00|13-08-2013 06:30:28+00:00|1.0|9.57|7.47|5.33|44.80|
|1|23-08-2013 06:30:28+00:00|23-08-2013 12:50:28+00:00|2.0|8.22|8.08|5.33|40.41|

## <a name="custom-python-transforms"></a>Transformações personalizadas de Python

Sempre haverá cenários em que a opção mais fácil para fazer uma transformação é escrever seu próprio script. O SDK fornece três pontos de extensão que você pode usar para scripts Python personalizados.

- Nova coluna de script
- Novo filtro de script
- Transformar partição

Cada uma das extensões é compatível com o runtime de ampliação e expansão. A principal vantagem de usar esses pontos de extensão é que você não precisa efetuar pull de todos os dados para criar um quadro de dados. Seu código Python será executado assim como como outras transformações, em escala, por partição e, geralmente, em paralelo.

### <a name="initial-data-preparation"></a>Preparação de dados inicial

Comece a carregar alguns dados do Blob do Azure.

```python
import azureml.dataprep as dprep
col = dprep.col

dflow = dprep.read_csv(
    path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv', skip_rows=1)
dflow.head(2)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|
|0|ALABAMA|1|101710|Condado de Hale|10171002158| |
|1|ALABAMA|1|101710|Condado de Hale|10171002162| |

Reduza o conjunto de dados e faça algumas transformações básicas, incluindo a remoção de colunas, a substituição de valores e a conversão de tipos.

```python
dflow = dflow.keep_columns(
    ['stnam', 'leanm10', 'ncessch', 'MAM_MTH00numvalid_1011'])
dflow = dflow.replace_na(
    columns=['leanm10', 'MAM_MTH00numvalid_1011'], custom_na_list='.')
dflow = dflow.to_number(['ncessch', 'MAM_MTH00numvalid_1011'])
dflow.head(2)
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|
|0|ALABAMA|Condado de Hale|1.017100e+10|Nenhum|
|1|ALABAMA|Condado de Hale|1.017100e+10|Nenhum|

Procure valores null com o filtro a seguir.

```python
dflow.filter(col('MAM_MTH00numvalid_1011').is_null()).head(2)
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|
|0|ALABAMA|Condado de Hale|1.017100e+10|Nenhum|
|1|ALABAMA|Condado de Hale|1.017100e+10|Nenhum|

### <a name="transform-partition"></a>Transformar partição

Use [`transform_partition()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#transform-partition-script--str-----azureml-dataprep-api-dataflow-dataflow) para substituir todos os valores nulos por 0. Este código será executado por partição, não em todo o conjunto de dados de uma só vez. Isso significa que, em um grande conjunto de dados, esse código pode ser executado em paralelo à medida que o tempo de execução processa os dados, partição por partição.

O script Python deve definir uma função chamada `transform()` que recebe dois argumentos, `df` e `index`. O argumento `df` será um dataframe do pandas que contém os dados para a partição e o argumento `index` é um identificador exclusivo da partição. A função de transformação pode editar completamente o dataframe transmitido, mas deve retornar um dataframe. Quaisquer bibliotecas importadas pelo script Python devem existir no ambiente em que o fluxo de dados é executado.

```python
df = df.transform_partition("""
def transform(df, index):
    df['MAM_MTH00numvalid_1011'].fillna(0,inplace=True)
    return df
""")
df.head(2)
```

||stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|
|0|ALABAMA|Condado de Hale|1.017100e+10|0,0|
|1|ALABAMA|Condado de Hale|1.017100e+10|0,0|

### <a name="new-script-column"></a>Nova coluna de script

Você pode usar um script Python para criar uma nova coluna que tem o nome do município e o nome do estado, e também para colocar em maiúscula o nome do estado. Para fazer isso, use o [`new_script_column()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#new-script-column-new-column-name--str--insert-after--str--script--str-----azureml-dataprep-api-dataflow-dataflow) método no fluxo de dados.

O script Python deve definir uma função chamada `newvalue()` que usa um único argumento `row`. O argumento `row` é um dict (`key`: nome da coluna, `val`: valor atual) e será passado para esta função para cada linha no conjunto de dados. Esta função deve retornar um valor a ser usado na nova coluna. Quaisquer bibliotecas importadas pelo script Python devem existir no ambiente em que o fluxo de dados é executado.

```python
dflow = dflow.new_script_column(new_column_name='county_state', insert_after='leanm10', script="""
def newvalue(row):
    return row['leanm10'] + ', ' + row['stnam'].title()
""")
dflow.head(2)
```

||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Condado de Hale|Hale County, Alabama|1.017100e+10|0,0|
|1|ALABAMA|Condado de Hale|Hale County, Alabama|1.017100e+10|0,0|

### <a name="new-script-filter"></a>Novo Filtro de Script

Crie uma expressão Python usando [`new_script_filter()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#new-script-filter-script--str-----azureml-dataprep-api-dataflow-dataflow) para filtrar o conjunto de dados somente para linhas em que ' Hale ' não esteja na `county_state` nova coluna. A expressão retorna `True` se quisermos manter a linha, e `False` para removê-la.

```python
dflow = dflow.new_script_filter("""
def includerow(row):
    val = row['county_state']
    return 'Hale' not in val
""")
dflow.head(2)
```

||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Jefferson County|Jefferson County, Alabama|1.019200e+10|1.0|
|1|ALABAMA|Jefferson County|Jefferson County, Alabama|1.019200e+10|0,0|

## <a name="next-steps"></a>Próximas etapas

* Consulte o [tutorial](tutorial-data-prep.md) Azure Machine Learning data Prep SDK para obter um exemplo de como resolver um cenário específico
