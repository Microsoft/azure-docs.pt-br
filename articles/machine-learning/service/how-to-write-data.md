---
title: 'Gravar: SDK do Python de preparação de dados'
titleSuffix: Azure Machine Learning service
description: Saiba mais sobre a gravação de dados com o SDK de preparação de dados do Azure Machine Learning. Você pode gravar dados em qualquer ponto de um fluxo de dados e em arquivos em qualquer local com suporte (sistema de arquivos local, Armazenamento de Blobs do Azure e Azure Data Lake Storage).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: jmartens
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 92f04d80ea956f3036d7778a5d6de62e53b969ad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60817365"
---
# <a name="write-and-configure-data-using-azure-machine-learning"></a>Escrever e configurar os dados usando o Azure Machine Learning

Neste artigo, você aprenderá os diferentes métodos para gravar dados usando o [SDK do Python do Azure Machine Learning dados Prep](https://aka.ms/data-prep-sdk) e como configurar esses dados para experimentação com a [SDK de aprendizado de máquina do Azure para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).  Dados de saída podem ser gravados em qualquer ponto em um fluxo de dados. As gravações são adicionadas como etapas ao fluxo de dados resultante e essas etapas são executados sempre que o fluxo de dados é executado. Os dados são gravados em vários arquivos de partição para permitir gravações paralelas.

Como não há nenhuma limitação para a quantidade de etapas de gravação em um pipeline, você pode facilmente adicionar mais etapas de gravação para obter resultados intermediários para solução de problemas ou para outros pipelines.

Sempre que você executar uma etapa de gravação, ocorrerá um pull completo dos dados no fluxo de dados. Por exemplo, um fluxo de dados com três etapas de gravação lerá e processar todos os registros no conjunto de dados três vezes.

## <a name="supported-data-types-and-location"></a>Tipos de dados e locais com suporte

Os formatos de arquivo a seguir têm suporte
-   Arquivos delimitados (CSV, TSV, etc.)
-   Arquivos Parquet

Usando o SDK do Azure Machine Learning Data Prep Python, você pode gravar dados para:
+ um sistema de arquivos local
+ Armazenamento do Blobs do Azure
+ Armazenamento do Azure Data Lake

## <a name="spark-considerations"></a>Considerações sobre o Spark

Ao executar um fluxo de dados no Spark, você precisa gravar em uma pasta vazia. Tentar executar uma gravação em uma pasta existente causará uma falha. Verifique se a pasta de destino está vazia ou use um local de destino diferente para cada execução, caso contrário, a gravação falhará.

## <a name="monitoring-write-operations"></a>Monitoramento de operações de gravação

Para sua conveniência, um arquivo de sentinela chamado SUCCESS é gerado quando a gravação é concluída. Sua presença ajuda a identificar quando uma gravação intermediária foi concluída sem precisar esperar a conclusão do pipeline inteiro.

## <a name="example-write-code"></a>Exemplo de código de gravação

Neste exemplo, comece Carregando dados em um fluxo de dados usando `auto_read_file()`. Você reutilizar esses dados com diferentes formatos.

```python
import azureml.dataprep as dprep
t = dprep.auto_read_file('./data/fixed_width_file.txt')
t = t.to_number('Column3')
t.head(5)
```

Saída de exemplo:

| | Coluna1 | Coluna2 | Coluna3 | Coluna4 | Coluna5 | Coluna6 | Coluna7 | Coluna8 | Coluna9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000.0 | 99999.0 | Nenhum | NÃO | NÃO | ENRS | NaN | NaN | NaN |   
|1| 10003.0 | 99999.0 | Nenhum | NÃO | NÃO | ENSO | NaN | NaN | NaN |   
|2| 10010.0 | 99999.0 | Nenhum | NÃO | JN | ENJA | 70933.0 | -8667.0 | 90.0 |
|3| 10013.0 | 99999.0 | Nenhum | NÃO | NÃO |      | NaN | NaN | NaN |
|4| 10014.0 | 99999.0 | Nenhum | NÃO | NÃO | ENSO | 59783.0 | 5350.0 |  500.0|

### <a name="delimited-file-example"></a>Exemplo de arquivo delimitado

O código a seguir usa o [ `write_to_csv()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#write-to-csv-directory-path--destinationpath--separator--str--------na--str----na---error--str----error------azureml-dataprep-api-dataflow-dataflow) função para gravar dados em um arquivo delimitado.

```python
# Create a new data flow using `write_to_csv` 
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'))

# Run the data flow to begin the write operation.
write_t.run_local()

written_files = dprep.read_csv('./test_out/part-*')
written_files.head(5)
```

Saída de exemplo:

| | Coluna1 | Coluna2 | Coluna3 | Coluna4 | Coluna5 | Coluna6 | Coluna7 | Coluna8 | Coluna9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000.0 | 99999.0 | ERROR | NÃO | NÃO | ENRS | NaN    | NaN | NaN |   
|1| 10003.0 | 99999.0 | ERROR | NÃO | NÃO | ENSO |    NaN | NaN | NaN |   
|2| 10010.0 | 99999.0 | ERROR | NÃO | JN | ENJA |    70933.0 | -8667.0 | 90.0 |
|3| 10013.0 | 99999.0 | ERROR | NÃO | NÃO |     | NaN | NaN | NaN |
|4| 10014.0 | 99999.0 | ERROR | NÃO | NÃO | ENSO |    59783.0 | 5350.0 |  500.0|

Na saída anterior, vários erros aparecem nas colunas numéricas por causa de números que não foram analisados corretamente. Quando gravados em CSV, valores nulos são substituídos com a cadeia de caracteres "ERROR", por padrão.

Adicione parâmetros como parte de sua gravação chamar e especificam uma cadeia de caracteres a ser usado para representar valores nulos.

```python
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'), 
                         error='BadData',
                         na='NA')
write_t.run_local()
written_files = dprep.read_csv('./test_out/part-*')
written_files.head(5)
```

O código anterior produz esta saída:

| | Coluna1 | Coluna2 | Coluna3 | Coluna4 | Coluna5 | Coluna6 | Coluna7 | Coluna8 | Coluna9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000.0 | 99999.0 | BadData | NÃO | NÃO | ENRS | NaN  | NaN | NaN |   
|1| 10003.0 | 99999.0 | BadData | NÃO | NÃO | ENSO |  NaN | NaN | NaN |   
|2| 10010.0 | 99999.0 | BadData | NÃO | JN | ENJA |  70933.0 | -8667.0 | 90.0 |
|3| 10013.0 | 99999.0 | BadData | NÃO | NÃO |   | NaN | NaN | NaN |
|4| 10014.0 | 99999.0 | BadData | NÃO | NÃO | ENSO |  59783.0 | 5350.0 |  500.0|

### <a name="parquet-file-example"></a>Exemplo de arquivo parquet

Semelhante ao `write_to_csv()`, o [ `write_to_parquet()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#write-to-parquet-file-path--typing-union--destinationpath--nonetype----none--directory-path--typing-union--destinationpath--nonetype----none--single-file--bool---false--error--str----error---row-groups--int---0-----azureml-dataprep-api-dataflow-dataflow) função retorna um novo fluxo de dados com uma gravação de etapa de Parquet que é executada quando o fluxo de dados é executado.

```python
write_parquet_t = t.write_to_parquet(directory_path=dprep.LocalFileOutput('./test_parquet_out/'),
error='MiscreantData')
```

Execute o fluxo de dados para iniciar a operação de gravação.

```python
write_parquet_t.run_local()

written_parquet_files = dprep.read_parquet_file('./test_parquet_out/part-*')
written_parquet_files.head(5)
```

O código anterior produz esta saída:

|   | Coluna1 | Coluna2 | Coluna3 | Coluna4 | Coluna5 | Coluna6 | Coluna7 | Coluna8 | Coluna9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |-------- |
|0| 10000.0 | 99999.0 | MiscreantData | NÃO | NÃO | ENRS | MiscreantData | MiscreantData | MiscreantData |
|1| 10003.0 | 99999.0 | MiscreantData | NÃO | NÃO | ENSO | MiscreantData | MiscreantData | MiscreantData |   
|2| 10010.0 | 99999.0 | MiscreantData | NÃO| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0 | 99999.0 | MiscreantData | NÃO| NÃO| |   MiscreantData|    MiscreantData|    MiscreantData|
|4| 10014.0 | 99999.0 | MiscreantData | NÃO| NÃO| ENSO|   59783.0|    5350.0| 500.0|

## <a name="configure-data-for-automated-machine-learning-training"></a>Configurar os dados para treinamento de aprendizado de máquina automatizado

Passe o arquivo de dados recém-criados para um [ `AutoMLConfig` ](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#automlconfig) objeto em preparação para o treinamento de aprendizado de máquina automatizado. 

O exemplo de código a seguir ilustra como converter seu fluxo de dados a um dataframe Pandas e subsequentemente, dividi-la em conjuntos de dados de treinamento e teste de treinamento de aprendizado de máquina automatizado.

```Python
from azureml.train.automl import AutoMLConfig
from sklearn.model_selection import train_test_split

dflow = dprep.auto_read_file(path="")
X_dflow = dflow.keep_columns([feature_1,feature_2, feature_3])
y_dflow = dflow.keep_columns("target")

X_df = X_dflow.to_pandas_dataframe()
y_df = y_dflow.to_pandas_dataframe()

X_train, X_test, y_train, y_test = train_test_split(X_df, y_df, test_size=0.2, random_state=223)

# flatten y_train to 1d array
y_train.values.flatten()

#configure 
automated_ml_config = AutoMLConfig(task = 'regression',
                               X = X_train.values,  
                   y = y_train.values.flatten(),
                   iterations = 30,
                       Primary_metric = "AUC_weighted",
                       n_cross_validation = 5
                       )

```

Se você não precisar de quaisquer etapas de preparação de dados intermediário, como no exemplo anterior, você pode passar seu fluxo de dados diretamente em `AutoMLConfig`.

```Python
automated_ml_config = AutoMLConfig(task = 'regression', 
                   X = X_dflow,   
                   y = y_dflow, 
                   iterations = 30, 
                   Primary_metric = "AUC_weighted",
                   n_cross_validation = 5
                   )
```

## <a name="next-steps"></a>Próximas etapas
* Consulte o SDK [visão geral](https://aka.ms/data-prep-sdk) para padrões de design e exemplos de uso 
* Consulte o aprendizado de máquina automatizado [tutorial](tutorial-auto-train-models.md) para obter um exemplo de modelo de regressão