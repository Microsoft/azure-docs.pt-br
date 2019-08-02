---
title: Explorar e transformar os dados (classe de conjunto de dados)
titleSuffix: Azure Machine Learning service
description: Explorar dados usando as estatísticas de resumo e preparar dados por meio de limpeza de dados, transformação e engenharia de recursos
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/23/2019
ms.openlocfilehash: 80137c7f1ecebab4d2da0c4b7ba0ca9292dad22e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443962"
---
# <a name="explore-and-prepare-data-with-the-dataset-class-preview"></a>Explorar e preparar dados com a classe de conjunto de dados (visualização)

Aprenda a explorar e preparar dados com o pacote azureml conjuntos de dados na [SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). O [conjunto de dados](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) classe (visualização) permite que você explore e preparar seus dados, fornecendo funções, como: amostragem, as estatísticas de resumo e transformações inteligentes. Etapas de transformação são salvos no [definições de conjunto de dados](how-to-manage-dataset-definitions.md) com a capacidade de lidar com vários arquivos grandes de esquemas diferentes de uma maneira altamente escalonável.

> [!Important]
> Algumas classes de conjunto de dados (visualização) têm dependências no [azureml dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) (GA) do pacote. Embora as funções de transformação podem ser feitas diretamente com o GA'ed [funções de preparação de dados](how-to-transform-data.md), recomendamos que os wrappers de pacote do conjunto de dados descritos neste artigo se você estiver criando uma nova solução. Azure Machine Learning conjuntos de dados (versão prévia) permitem que você não só transformar seus dados, mas também [dados de instantâneo](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py) e armazenar [definições de conjunto de dados com controle de versão](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py). Conjuntos de dados é a próxima versão do SDK de preparação de dados, oferecendo funcionalidade expandida para gerenciar conjuntos de dados em soluções de inteligência Artificial.

## <a name="prerequisites"></a>Pré-requisitos

Para explorar e preparar seus dados, você precisará de:

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Serviço do Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

* Um workspace de serviço do Azure Machine Learning. Ver [criar um espaço de trabalho do serviço de Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace).

* O SDK de aprendizado de máquina do Azure para Python (versão 1.0.21 ou posterior), que inclui o pacote azureml conjuntos de dados. Para instalar ou atualizar para a versão mais recente do SDK, consulte [instalar ou atualizar o SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* A Azure Machine Learning a preparação de dados do SDK. Para instalar ou atualizar para a versão mais recente, consulte [instalar ou atualizar o SDK de preparação de dados](https://docs.microsoft.com/python/api/overview/azure/dataprep/intro?view=azure-dataprep-py#install).

* Baixar os arquivos de exemplo para acompanhar os exemplos: [crime.csv](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv) e [city.json](https://dprepdata.blob.core.windows.net/dataset-sample-files/city.json).

## <a name="sampling"></a>amostragem

Levar uma amostra dos seus dados para obter uma compreensão inicial de sua arquitetura de dados e conteúdo. Neste momento, o [ `sample()` ](https://docs.microsoft.com//python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#sample-sample-strategy--arguments-) método da classe de conjunto de dados é compatível com estratégias de amostragem Top N aleatória simples e Stratified.

```Python
from azureml.core.dataset import Dataset
import random

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')

# create seed for Simple Random and Stratified sampling
seed = random.randint(0, 4294967295)
```

### <a name="top-n-sample"></a>Exemplo de N maiores

Para amostragem de N superior, os primeiros n registros do conjunto de dados são o seu exemplo. Isso é útil se você estiver apenas tentando obter uma ideia do que a aparência de registros de dados, como o ou veja quais são os campos em seus dados.

```Python
top_n_sample_dataset = dataset.sample('top_n', {'n': 5})
top_n_sample_dataset.to_pandas_dataframe()
```

||id|Número do Caso|Data|Bloco|IUCR|Texto Primário|...|
-|--|-----------|----|-----|----|------------|---
0|10498554|HZ239907|4/4/2016 23:56|007XX E 111TH ST|1153|PRÁTICA ENGANOSA|...
1|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|ROUBO|...
2|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO AVE|1154|PRÁTICA ENGANOSA|...
3|10519591|HZ261534|4/15/2016 9:00|113XX S PRAIRIE AVE|1120|PRÁTICA ENGANOSA|...
4|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|ROUBO|...

### <a name="simple-random-sample"></a>Simple exemplo aleatório

A amostragem aleatória simples, cada membro da população de dados tem a mesma chance de serem selecionados como parte do exemplo. No `simple_random` estratégia de exemplo, os registros do conjunto de dados são selecionados com base na probabilidade especificada e retorna um conjunto de dados modificado. O parâmetro de semente é opcional.

```Python
simple_random_sample_dataset = dataset.sample('simple_random', {'probability':0.3, 'seed': seed})
simple_random_sample_dataset.to_pandas_dataframe()
```

||id|Número do Caso|Data|Bloco|IUCR|Texto Primário|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|ROUBO|...
1|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO AVE|1154|PRÁTICA ENGANOSA|...
2|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|ROUBO|...

### <a name="stratified-sample"></a>Amostra estratificada

Exemplos estratificados Certifique-se de que certos grupos de uma população são representados no exemplo. No `stratified` estratégia de exemplo, a população é dividida em strata ou subgrupos, com base em semelhanças, e registros serão selecionados aleatoriamente de cada strata de acordo com os pesos de segmentos indicados pelo `fractions` parâmetro.

No exemplo a seguir, podemos agrupar cada registro pelas colunas especificadas e incluir esse registro com base nas informações strata X peso de `fractions`. Se não for especificado um strata ou o registro não pode ser agrupado, o peso padrão, a amostra é 0.

```Python
# take 50% of records with `Primary Type` as `THEFT` and 20% of records with `Primary Type` as `DECEPTIVE PRACTICE` into sample Dataset
fractions = {}
fractions[('THEFT',)] = 0.5
fractions[('DECEPTIVE PRACTICE',)] = 0.2

sample_dataset = dataset.sample('stratified', {'columns': ['Primary Type'], 'fractions': fractions, 'seed': seed})

sample_dataset.to_pandas_dataframe()
```

||id|Número do Caso|Data|Bloco|IUCR|Texto Primário|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|ROUBO|...
1|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|ROUBO|...
2|10535059|HZ278872|4/15/2016 4:30|004XX S KILBOURN AVE|810|ROUBO|...

## <a name="explore-with-summary-statistics"></a>Explore com estatísticas de resumo

 Detectar anomalias, valores, ausentes ou contagens de erro com o [ `get_profile()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-profile-arguments-none--generate-if-not-exist-true--workspace-none--compute-target-none-) método. Essa função obtém o perfil e as estatísticas de resumo de seus dados, que por sua vez ajuda a determinam as operações de preparação de dados necessários para aplicar.

```Python
dataset.get_profile()
```

||Type|Min|max|Contagem|Contagem faltando|Sem contagem faltando|Percentual faltando|Contagem de erros|Contagem vazia|0,1% quantil|1% quantil|5% quantil|25% quantil|50% quantil|75% quantil|95% quantil|99% quantil|99,9% quantil|Média|Desvio padrão|Variação|Distorção|Curtose
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
id|FieldType.INTEGER|1.04986e + 07|1.05351e + 07|10.0|0,0|10.0|0,0|0,0|0,0|1.04986e + 07|1.04992e + 07|1.04986e + 07|1.05166e + 07|1.05209e + 07|1.05259e + 07|1.05351e + 07|1.05351e + 07|1.05351e + 07|1.05195e + 07|12302.7|1.51358e + 08|-0.495701|-1.02814
Número do Caso|FieldType.STRING|HZ239907|HZ278872|10.0|0,0|10.0|0,0|0,0|0,0||||||||||||||
Data|FieldType.DATE|2016-04-04 23:56:00+00:00|2016-04-15 17:00:00+00:00|10.0|0,0|10.0|0,0|0,0|0,0||||||||||||||
Bloco|FieldType.STRING|004XX S KILBOURN AVE|113XX S PRAIRIE AVE|10.0|0,0|10.0|0,0|0,0|0,0||||||||||||||
IUCR|FieldType.INTEGER|810|1154|10.0|0,0|10.0|0,0|0,0|0,0|810|850|810|890|1136|1153|1154|1154|1154|1058.5|137.285|18847.2|-0.785501|-1.3543
Texto Primário|FieldType.STRING|PRÁTICA ENGANOSA|ROUBO|10.0|0,0|10.0|0,0|0,0|0,0||||||||||||||
DESCRIÇÃO|FieldType.STRING|VERIFICAÇÃO DE FALSA|AO LONGO DE US $500|10.0|0,0|10.0|0,0|0,0|0,0||||||||||||||
Descrição do Local|FieldType.STRING||ESCOLA, PÚBLICA, CRIANDO|10.0|0,0|10.0|0,0|0,0|1.0||||||||||||||
Detenção|FieldType.BOOLEAN|Falso|Falso|10.0|0,0|10.0|0,0|0,0|0,0||||||||||||||
Nacional|FieldType.BOOLEAN|Falso|Falso|10.0|0,0|10.0|0,0|0,0|0,0||||||||||||||
Superar|FieldType.INTEGER|531|2433|10.0|0,0|10.0|0,0|0,0|0,0|531|531|531|614|1318.5|1911|2433|2433|2433|1371.1|692.094|478994|0.105418|-1.60684
Distrito|FieldType.INTEGER|5|24|10.0|0,0|10.0|0,0|0,0|0,0|5|5|5|6|13|19|24|24|24|13,5|6.94822|48.2778|0.0930109|-1.62325
Ward|FieldType.INTEGER|1|48|10.0|0,0|10.0|0,0|0,0|0,0|1|5|1|9|22,5|40|48|48|48|24,5|16.2635|264.5|0.173723|-1.51271
Área da Comunidade|FieldType.INTEGER|4|77|10.0|0,0|10.0|0,0|0,0|0,0|4|8.5|4|24|37.5|71|77|77|77|41.2|26.6366|709.511|0.112157|-1.73379
Código do FBI|FieldType.INTEGER|6|11|10.0|0,0|10.0|0,0|0,0|0,0|6|6|6|6|11|11|11|11|11|9.4|2.36643|5.6|-0.702685|-1.59582
Coordenada X|FieldType.INTEGER|1.16309e + 06|1.18336e + 06|10.0|7.0|3.0|0.7|0,0|0,0|1.16309e + 06|1.16309e + 06|1.16309e + 06|1.16401e + 06|1.16678e + 06|1.17921e + 06|1.18336e + 06|1.18336e + 06|1.18336e + 06|1.17108e + 06|10793.5|1.165e + 08|0.335126|-2.33333
Coordenada Y|FieldType.INTEGER|1.8315e + 06|1.908e + 06|10.0|7.0|3.0|0.7|0,0|0,0|1.8315e + 06|1.8315e + 06|1.8315e + 06|1.83614e + 06|1.85005e + 06|1.89352e + 06|1.908e + 06|1.908e + 06|1.908e + 06|1.86319e + 06|39905.2|1.59243e + 09|0.293465|-2.33333
Ano|FieldType.INTEGER|2016|2016|10.0|0,0|10.0|0,0|0,0|0,0|2016|2016|2016|2016|2016|2016|2016|2016|2016|2016|0|0|NaN|NaN
Atualizado Em|FieldType.DATE|2016-05-11 15:48:00+00:00|2016-05-27 15:45:00+00:00|10.0|0,0|10.0|0,0|0,0|0,0||||||||||||||
Latitude|FieldType.DECIMAL|41.6928|41.9032|10.0|7.0|3.0|0.7|0,0|0,0|41.6928|41.6928|41.6928|41.7057|41.7441|41.8634|41.9032|41.9032|41.9032|41.78|0.109695|0.012033|0.292478|-2.33333
Longitude|FieldType.DECIMAL|-87.6764|-87.6043|10.0|7.0|3.0|0.7|0,0|0,0|-87.6764|-87.6764|-87.6764|-87.6734|-87.6645|-87.6194|-87.6043|-87.6043|-87.6043|-87.6484|0.0386264|0.001492|0.344429|-2.33333
Location|FieldType.STRING||(41.903206037, -87.676361925)|10.0|0,0|10.0|0,0|0,0|7.0||||||||||||||

## <a name="impute-missing-values"></a>Acrescentar valores ausentes

Conjuntos de dados, valores nulos, do NaN e que não contêm nenhum conteúdo são considerados valores ausentes. Eles podem afetar o desempenho de seu modelos de aprendizado de máquina ou levar a conclusões inválidas. Imputação é uma abordagem comum para tratar valores ausentes e é útil quando você tem uma alta porcentagem de perder os registros que você não pode simplesmente excluir de valor.

O perfil de conjunto de dados gerado na seção anterior, podemos ver que `Latitude` e `Longitude` colunas têm uma alta porcentagem de valores ausentes. Neste exemplo, podemos calcular a média e imputar valores ausentes para essas duas colunas.

Primeiro, obtenha a definição mais recente do conjunto de dados com [ `get_definition()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-definition-version-id-none-) e reduzir os dados com [ `keep_columns()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow#keep-columns-columns--multicolumnselection--validate-column-exists--bool---false-----azureml-dataprep-api-dataflow-dataflow), portanto, podemos exibir apenas as colunas que desejamos endereço.

```Python
from azureml.core.dataset import Dataset
import azureml.dataprep as dprep

# get the latest definition of Dataset
ds_def = dataset.get_definition()

# keep useful columns for this example
ds_def = ds_def.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
ds_def.head(3)
```

||id|Detenção| Latitude|Longitude|
-|---------|-----|---------|----------|
|0|10498554|Falso|41.692834|-87.604319|
|1|10516598|Falso| 41.744107 |-87.664494|
|2|10519196|Falso| NaN|NaN|

Em seguida, verifique as `MEAN` o valor da coluna de latitude usando o [ `summarize()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#summarize-summary-columns--typing-union-typing-list-azureml-dataprep-api-dataflow-summarycolumnsvalue---nonetype----none--group-by-columns--typing-union-typing-list-str---nonetype----none--join-back--bool---false--join-back-columns-prefix--typing-union-str--nonetype----none-----azureml-dataprep-api-dataflow-dataflow) função. Essa função aceita uma matriz de colunas no parâmetro `group_by_columns` para especificar o nível de agregação. O `summary_columns` parâmetro aceita a `SummaryColumnsValue` função, que especifica o nome da coluna atual, o nome do novo campo calculado, e o `SummaryFunction` para executar.

```Python
lat_mean = ds_def.summarize(group_by_columns = ['Arrest'],
                            summary_columns = [dprep.SummaryColumnsValue(column_id = 'Latitude',
                                                                         summary_column_name = 'Latitude_MEAN',
                                                                         summary_function = dprep.SummaryFunction.MEAN)])
lat_mean = lat_mean.filter(lat_mean['Arrest'] == False)
lat_mean.head(1)
```

||Detenção|Latitude_MEAN|
--|-----|--------|
|0|Falso|41.780049|

Assim que pudermos verificar os valores a serem imputar, use [ `ImputeMissingValuesBuilder` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py) aprender uma expressão fixa que imputes as colunas com qualquer um calculado `MIN`, `MAX`, `MEAN` valor, ou um `CUSTOM` valor. Quando `group_by_columns` for especificado, serão inseridos valores ausentes pelo grupo com `MIN`, `MAX` e `MEAN` calculados por grupo.

O [ `ImputeColumnArguments` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputecolumnarguments?view=azure-dataprep-pyfunction) aceita uma cadeia de caracteres do column_id e um `ReplaceValueFunction` para especificar o tipo de impute. Para o valor de longitude ausente, imputá-lo com-87 com base no conhecimento externo.

```Python
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id = 'Latitude',
                                          impute_function = dprep.ReplaceValueFunction.MEAN)

# impute with custom value -87
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=-87)
```

Imputar etapas podem ser encadeadas em um `ImputeMissingValuesBuilder` do objeto usando o [ `Builders` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py) classe função [ `impute_missing_values()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py#impute-missing-values-impute-columns--typing-list-azureml-dataprep-api-builders-imputecolumnarguments----none--group-by-columns--typing-union-typing-list-str---nonetype----none-----azureml-dataprep-api-builders-imputemissingvaluesbuilder). O parâmetro `impute_columns` aceita uma matriz de `ImputeColumnArguments` objetos.

```Python
# get instance of ImputeMissingValuesBuilder
impute_builder = ds_def.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                   group_by_columns=['Arrest'])
```

Chame o [ `learn()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#learn------none) funcionar para armazenar as etapas de impute e aplicá-las a um objeto de fluxo de dados usando [ `to_dataflow()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#to-dataflow------azureml-dataprep-api-dataflow-dataflow).

```Python
impute_builder.learn()
ds_def = impute_builder.to_dataflow()
ds_def.head(3)
```

Conforme mostrado na tabela de saída a seguir, a latitude ausente foi inserir com o `MEAN` valor de `Arrest==False` grupo e a longitude ausente foi inserir com-87.

||id|Detenção|Latitude|Longitude
-|---------|-----|---------|----------
0|10498554|Falso|41.692834|-87.604319
1|10516598|Falso|41.744107|-87.664494
2|10519196|Falso|41.780049|-87.000000

Atualizar a definição de conjunto de dados, [ `update_definition()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#update-definition-definition--definition-update-message-) para manter as etapas de transformação executada.

```Python
dataset = dataset.update_definition(ds_def, 'Impute Missing')
dataset.head(3)
```

||id|Detenção|Latitude|Longitude
-|---------|-----|---------|----------
0|10498554|Falso|41.692834|-87.604319
1|10516598|Falso|41.744107|-87.664494
2|10519196|Falso|41.780049|-87.000000

## <a name="create-assertion-rules"></a>Criar regras de declaração

Frequentemente, os dados que nós trabalhamos com durante a limpeza e preparação de dados é apenas um subconjunto do total de dados que precisamos para produção. Como resultado, algumas das pressuposições, que podemos fazer como parte de nosso processo de limpeza podem se tornar falsa. Por exemplo, em um conjunto de dados que atualiza continuamente, uma coluna que originalmente continha somente números em um determinado intervalo pode conter uma variedade maior de valores em execuções posteriores. Esses erros geralmente resultam em pipelines interrompidas ou dados corrompidos.

Suporte a conjuntos de dados criando asserções em dados, que são avaliados como o pipeline é executado. Essas declarações permitem verificar se nossos suposições sobre os dados continuam sejam precisas e, quando não, para lidar com falhas adequadamente.

Por exemplo, se você quiser restringir `Latitude` e `Longitude` valores no conjunto de dados a intervalos numéricos específicos, o [ `assert_value()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#assert-value-columns--multicolumnselection--expression--azureml-dataprep-api-expressions-expression--policy--azureml-dataprep-api-engineapi-typedefinitions-assertpolicy----assertpolicy-errorvalue--1---error-code--str----assertionfailed------azureml-dataprep-api-dataflow-dataflow) método garante que isso é sempre o caso.

```Python
from azureml.dataprep import value
from azureml.core.dataset import Dataset

# get the latest definition of the Dataset
ds_def = dataset.get_definition()

# set assertion rules for `Latitude` and `Longitude` columns
ds_def = ds_def.assert_value('Latitude', (value <= 90) & (value >= -90), error_code='InvalidLatitude')
ds_def = ds_def.assert_value('Longitude', (value <= 180) & (value >= -87), error_code='InvalidLongitude')

ds_def.get_profile()
```

||Type|Min|max|Count|Contagem faltando|Sem contagem faltando|Percentual faltando|Contagem de erros|Contagem vazia|0,1% quantil|1% quantil|5% quantil|25% quantil|50% quantil|75% quantil|95% quantil|99% quantil|99,9% quantil|Média|Desvio padrão|Variação|Distorção|Curtose
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
id|FieldType.INTEGER|1.04986e + 07|1.05351e + 07|10.0|0,0|10.0|0,0|0,0|0,0|1.04986e + 07|1.04992e + 07|1.04986e + 07|1.05166e + 07|1.05209e + 07|1.05259e + 07|1.05351e + 07|1.05351e + 07|1.05351e + 07|1.05195e + 07|12302.7|1.51358e + 08|-0.495701|-1.02814
Detenção|FieldType.BOOLEAN|Falso|Falso|10.0|0,0|10.0|0,0|0,0|0,0||||||||||||||
Latitude|FieldType.DECIMAL|41.6928|41.9032|10.0|0,0|10.0|0,0|0,0|0,0|41.6928|41.7185|41.6928|41.78|41.78|41.78|41.9032|41.9032|41.9032|41.78|0.0517107|0.002674|0.837593|1.05
Longitude|FieldType.INTEGER|-87|-87|10.0|0,0|10.0|0,0|3.0|0,0|-87|-87|-87|-87|-87|-87|-87|-87|-87|-87|0|0|NaN|NaN

O perfil, você vê que o `Error Count` para o `Longitude` coluna é 3. O código a seguir filtra o conjunto de dados, recupera o erro e vê que valor faz com que a asserção falha. A partir daqui, ajuste o seu código e limpar seus dados adequadamente.

```Python
from azureml.dataprep import col

ds_error = ds_def.filter(col('Longitude').is_error())
error = ds_error.head(10)['Longitude'][0]

print(error.originalValue)
```

    -87.60431945

## <a name="derive-columns-by-example"></a>Derivar colunas por exemplo

Uma das ferramentas mais avançadas para conjuntos de dados é a capacidade de derivar colunas usando os exemplos de resultados desejados. Isso permite dar a um exemplo, o SDK para que ele pode gerar o código para obter as transformações pretendidas.

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')
dataset.head(3)
```

||id|Número do Caso|Data|Bloco|...|
-|---------|-----|---------|----|---
0|10498554|HZ239907|2016-04-04 23:56:00|007XX E 111TH ST|...
1|10516598|HZ258664|2016-04-15 17:00:00|082XX S MARSHFIELD AVE|...
2|10519196|HZ261252|2016-04-15 10:00:00|104XX S SACRAMENTO AVE|...

Digamos que você precisa transformar o formato de data e hora para 2016-04-04 10 e 23h às 00H '. No [ `derive_column_by_example()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#derive-column-by-example-source-columns--sourcecolumns--new-column-name--str--example-data--exampledata-----azureml-dataprep-api-dataflow-dataflow) argumento, fornecem exemplos de sua saída desejada na `example_data` parâmetro neste formato: *(saída original, a saída desejada)* .

O código a seguir fornece dois exemplos de saída desejado, ("2016-04-04 23:56:00", "2016-04-04 10 PM-12 AM") e ("2016-04-15 17:00:00", "2016-04-15 às 16H - 6 PM")

```Python
ds_def = dataset.get_definition()
ds_def = ds_def.derive_column_by_example(
        source_columns = "Date",
        new_column_name = "Date_Time_Range",
        example_data = [("2016-04-04 23:56:00", "2016-04-04 10PM-12AM"), ("2016-04-15 17:00:00", "2016-04-15 4PM-6PM")]
    )
ds_def.keep_columns(['ID','Date','Date_Time_Range']).head(3)
```

Na tabela a seguir, observe que uma nova coluna, Date_Time_Range contém registros no formato especificado.

||id|Data|Date_Time_Range
-|--------|-----|----
0|10498554|2016-04-04 23:56:00|2016-04-04 10 PM-12 AM
1|10516598|2016-04-15 17:00:00|2016-04-15 ÀS 16H - 6 PM
2|10519196|2016-04-15 10:00:00|2016-04-15 10H -12H00MIN.

```Python
# update Dataset definition to keep the transformation steps performed.
dataset = dataset.update_definition(ds_def, 'Derive Date_Time_Range')
```

## <a name="fuzzy-groupings"></a>Agrupamentos difusos

Quando você coletar dados de fontes diferentes, você pode encontrar variações de ortografia, maiusculas e minúsculas ou abreviações das mesmas entidades. Automaticamente padronizar e reconciliar essas variantes com a funcionalidade de agrupamento difuso ou clustering de texto, do SDK.

Por exemplo, a coluna `inspections.business.city` contém várias formas da cidade nome "San Francisco".

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local json file
dataset = Dataset.auto_read_files('./data/city.json')
dataset.head(5)
```

||inspections.business.business_id|inspections.business_name|inspections.business.address|inspections.business.city|...|
-|-----|-------------------------|------------|--|---
0|16162|Quick-N-Ezee alimentos indianos|3861 24 St|SF|...
1|67565|Rei de Cafe macarrão tailandês|1541 TARAVAL St|SAN FRANCISCO|...
2|67565|Rei de Cafe macarrão tailandês|1541 TARAVAL St|SAN FRANCISCO|...
3|68701|Grindz|St clement 832|SF|...
4|69186|Premier buffet & eventos, Inc.|1255 22 St|S.F.|...

Vamos usar o [ `fuzzy_group_column()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#fuzzy-group-column-source-column--str--new-column-name--str--similarity-threshold--float---0-8--similarity-score-column-name--str---none-----azureml-dataprep-api-dataflow-dataflow) método para adicionar uma coluna com a forma canônica detectada automaticamente de "San Francisco". Os argumentos `source_column` e `new_column_name` são necessários. Você também tem a opção de:

* Criar uma nova coluna, `similarity_score_column_name`, que mostra a pontuação de similaridade entre cada par de valores originais e canônicos.

* Forneça um `similarity_threshold`, que é a pontuação de similaridade mínima para os valores a serem agrupados.

```Python
# get the latest Dataset definition
ds_def = dataset.get_definition()
ds_def = ds_def.fuzzy_group_column(source_column='inspections.business.city',
                                       new_column_name='city_grouped',
                                       similarity_threshold=0.8,
                                       similarity_score_column_name='similarity_score')
ds_def.head(5)
```

||inspections.business.business_id|inspections.business_name|inspections.business.address|inspections.business.city|city_grouped|similarity_score|...|
-|-----|-------------------------|------------|--|---|---|---
0|16162|Quick-N-Ezee alimentos indianos|3861 24 St|SF|São Francisco|0.814806|...
1|67565|Rei de Cafe macarrão tailandês|1541 TARAVAL St|SAN FRANCISCO|São Francisco|1.000000|...
2|67565|Rei de Cafe macarrão tailandês|1541 TARAVAL St|SAN FRANCISCO|São Francisco|1.000000|...
3|68701|Grindz|St clement 832|SF|São Francisco|0.814806|...
4|69186|Premier buffet & eventos, Inc.|1255 22 St|S.F.|São Francisco|0.814806|...

Na definição de conjunto de dados resultante, todas as variações diferentes de "San Francisco" que representa os dados foram normalizadas para a mesma cadeia de caracteres, "San Francisco".

Salvar esta etapa de agrupamento difuso para a definição de conjunto de dados mais recente com `update_definition()`.

```Python
dataset = dataset.update_definition(ds_def, 'fuzzy grouping')
```

## <a name="next-steps"></a>Próximas etapas

* Consulte o aprendizado de máquina automatizado [tutorial](tutorial-auto-train-models.md) para obter um exemplo de modelo de regressão.

* Consulte o SDK [visão geral](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) para padrões de design e exemplos de uso.

* Para obter um exemplo de como usar conjuntos de dados, consulte o [notebooks de exemplo](https://aka.ms/dataset-tutorial).
