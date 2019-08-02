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
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 6753be5613b10b64936cddaafbb9859aad837b02
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358630"
---
# <a name="write-and-configure-data--with-the-azure-machine-learning-data-prep-sdk"></a>Gravar e configurar dados com o SDK de preparação de dados Azure Machine Learning

Neste artigo, você aprende diferentes métodos para gravar dados usando o [SDK do python Azure Machine Learning data Prep](https://aka.ms/data-prep-sdk) e como configurar esses dados para experimentação com o [SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).  Os dados de saída podem ser gravados em qualquer ponto em um fluxo de dados. As gravações são adicionadas como etapas para o fluxo de dados resultante e essas etapas são executadas toda vez que o fluxo de dados é executado. Os dados são gravados em vários arquivos de partição para permitir gravações paralelas.

> [!Important]
> Se você estiver criando uma nova solução, experimente os [conjuntos](how-to-explore-prepare-data.md) de dados Azure Machine Learning (versão prévia) para transformar seus dados, fazer instantâneos e armazenar as definições de conjunto de dados com controle de versão. Conjuntos de dados é a próxima versão do data Prep SDK, oferecendo funcionalidade expandida para o gerenciamento de conjuntos de dados em soluções de ia.
> Se você usar o `azureml-dataprep` pacote para criar um fluxo de os com suas transformações em vez `azureml-datasets` de usar o pacote para criar um conjunto de um, não será possível usar instantâneos ou conjuntos de valores com controle de versão mais tarde.

Como não há nenhuma limitação para a quantidade de etapas de gravação em um pipeline, você pode facilmente adicionar mais etapas de gravação para obter resultados intermediários para solução de problemas ou para outros pipelines.

Sempre que você executar uma etapa de gravação, ocorrerá um pull completo dos dados no fluxo de dados. Por exemplo, um fluxo de dados com três etapas de gravação lerá e processar todos os registros no conjunto de dados três vezes.

## <a name="supported-data-types-and-location"></a>Tipos de dados e locais com suporte

Os formatos de arquivo a seguir têm suporte
-   Arquivos delimitados (CSV, TSV, etc.)
-   Arquivos Parquet

Usando o SDK do Python do Azure Machine Learning data Prep, você pode gravar dados em:
+ um sistema de arquivos local
+ Armazenamento de Blob do Azure
+ Azure Data Lake Storage

## <a name="spark-considerations"></a>Considerações sobre o Spark

Ao executar um fluxo de dados no Spark, você precisa gravar em uma pasta vazia. Tentar executar uma gravação em uma pasta existente causará uma falha. Verifique se a pasta de destino está vazia ou use um local de destino diferente para cada execução, caso contrário, a gravação falhará.

## <a name="monitoring-write-operations"></a>Monitoramento de operações de gravação

Para sua conveniência, um arquivo de sentinela chamado SUCCESS é gerado quando a gravação é concluída. Sua presença ajuda a identificar quando uma gravação intermediária foi concluída sem precisar esperar a conclusão do pipeline inteiro.

## <a name="example-write-code"></a>Exemplo de código de gravação

Para este exemplo, comece carregando dados em um fluxo de dados usando `auto_read_file()`. Você reutilizar esses dados com diferentes formatos.

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

O código a seguir usa [`write_to_csv()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow#write-to-csv-directory-path--datadestination--separator--str--------na--str----na---error--str----error------azureml-dataprep-api-dataflow-dataflow) a função para gravar dados em um arquivo delimitado.

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
|0| 10000.0 | 99999.0 | ERRO | NÃO | NÃO | ENRS | NaN    | NaN | NaN |   
|1| 10003.0 | 99999.0 | ERRO | NÃO | NÃO | ENSO |    NaN | NaN | NaN |   
|2| 10010.0 | 99999.0 | ERRO | NÃO | JN | ENJA |    70933.0 | -8667.0 | 90.0 |
|3| 10013.0 | 99999.0 | ERRO | NÃO | NÃO |     | NaN | NaN | NaN |
|4| 10014.0 | 99999.0 | ERRO | NÃO | NÃO | ENSO |    59783.0 | 5350.0 |  500.0|

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

Semelhante a `write_to_csv()`, a [`write_to_parquet()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow#write-to-parquet-file-path--typing-union--datadestination--nonetype----none--directory-path--typing-union--datadestination--nonetype----none--single-file--bool---false--error--str----error---row-groups--int---0-----azureml-dataprep-api-dataflow-dataflow) função retorna um novo fluxo de dados com uma etapa de gravação parquet que é executada quando o fluxo de dados é executado.

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

## <a name="configure-data-for-automated-machine-learning-training"></a>Configurar dados para treinamento automatizado do Machine Learning

Passe seu arquivo de dados recentemente gravado em [`AutoMLConfig`](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#automlconfig) um objeto em preparação para o treinamento automatizado do Machine Learning. 

O exemplo de código a seguir ilustra como converter o fluxo de transmissão em um dataframe do pandas e, posteriormente, dividi-lo em conjuntos de testes de treinamento e teste para treinamento automatizado do Machine Learning.

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

Se você não precisar de nenhuma etapa intermediária de preparação de dados, como no exemplo anterior, você pode passar seu Dataflow diretamente para `AutoMLConfig`o.

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
* Consulte a [visão geral](https://aka.ms/data-prep-sdk) do SDK para padrões de design e exemplos de uso 
* Consulte o [tutorial](tutorial-auto-train-models.md) de aprendizado de máquina automatizado para um exemplo de modelo de regressão
