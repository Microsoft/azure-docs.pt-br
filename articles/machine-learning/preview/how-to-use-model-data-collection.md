---
title: Como usar o recurso de Coleta de Dados de Gerenciamento de Modelos no Azure Machine Learning Workbench| Microsoft Docs
description: Este documento aborda como usar o recurso de Coleta de Dados de Gerenciamento de Modelos no Azure Machine Learning Workbench
services: machine-learning
author: aashishb
ms.author: aashishb
manager: neerajkh
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: e751e2f0ec812de43a03749e04ff165fa62ec649
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-collect-model-data-using-data-collection"></a>Como coletar dados de modelo usando a coleta de dados

O recurso de coleta de dados de modelo permite arquivar entradas e previsões do modelo de um serviço Web de aprendizado de máquina. Este documento discute os seguintes aspectos da coleta de dados de modelo:

- Como instalar o pacote de coleta de dados
- Como usar a coleta de dados
- Como exibir e consumir os dados coletados

## <a name="how-to-install-the-data-collection-package"></a>Como instalar o pacote de coleta de dados
A biblioteca de coleta de dados pode ser instalada nativamente no Linux e no Windows.

### <a name="windows"></a>Windows
No Windows, o módulo do coletor de dados pode ser instalado com o seguinte comando:

    pip install azureml.datacollector

### <a name="linux"></a>Linux
No Linux, a biblioteca libxml++ deve ser instalada primeiro. Execute o comando a seguir, que deve ser emitido em sudo:

    sudo apt-get install libxml++2.6-2v5

Então emita o seguinte comando:

    pip install azureml.datacollector
## <a name="how-to-use-data-collection"></a>Como usar a coleta de dados

Para usar a coleta de dados de modelo, você precisa fazer as seguintes alterações ao seu arquivo de pontuação:

1. Adicione o seguinte código na parte superior do arquivo
   
    ```python
    from azureml.datacollector import ModelDataCollector
    ```

2. Adicione as seguintes linhas de código à função `init()`,
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
    ```

3. Adicione as seguintes linhas de código à função `run(input_df)`,
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc.collect(input_df)
    prediction_dc.collect(pred)
    ```

    Verifique se as variáveis `input_df` e `pred` (valor de previsão de `model.predict()`) estão inicializadas antes de chamar a função `collect()` nelas.

4. Use o comando `az ml service create realtime` com a opção `--collect-model-data true` para criar um serviço Web em tempo real. Isso garante que os dados de modelo sejam coletados quando o serviço está sendo executado.

     ```batch
    c:\temp\myIris> az ml service create realtime -f iris_score.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true 
    ```
    
5. Para testar a coleta de dados, execute o `az ml service run realtime`,

    ```
    C:\Temp\myIris> az ml service run realtime -i irisapp -d "ADD YOUR INPUT DATA HERE!!" 
    ``` 
    
## <a name="how-to-view-and-consume-the-collected-data"></a>Como exibir e consumir os dados coletados
Para exibir os dados coletados no armazenamento de blobs:

1. Faça logon no [Portal do Azure](https://portal.azure.com).
2. Clique em **Mais Serviços**.
3. Na caixa de pesquisa, digite `Storage accounts` e pressione **Enter**
4. Na folha de pesquisa **Contas de armazenamento**, selecione o recurso **Conta de armazenamento**. Para determinar sua conta de armazenamento, use as etapas a seguir,

    a. Vá para o Azure ML Workbench, selecione o projeto em que você está trabalhando e abra o prompt de linha de comando do menu **Arquivo**
    
    b. Digite `az ml env show -v` e verifique o valor *storage_account*. Esse é o nome da sua conta de armazenamento

5. Clique em **Contêineres** no menu da folha de recursos e, em seguida, no contêiner chamado **modeldata**. Poderá ser necessário aguardar até 10 minutos após a primeira solicitação de serviço Web para ver o início da propagação dos dados para a conta de armazenamento. Os dados fluem para blobs com o seguinte caminho de contêiner:

    `/modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv`

Os dados podem ser consumidos de blobs do Azure de diversas maneiras, tanto usando ferramentas de software livre quanto software da Microsoft. Estes são alguns exemplos:
 - Azure ML Workbench – abra o arquivo csv no Azure ML Workbench adicionando o arquivo csv como uma fonte de dados
 - Excel – abra os arquivos csv diários como uma planilha
 - [Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-azure-and-power-bi/) – crie gráficos usando os dados obtidos dos dados csv em blobs
 - [Spark](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-overview) – crie um dataframe com uma grande porção de dados csv
    ```python
    var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
    ```
- [Hive](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started) – carregue dados csv em uma tabela de Hive e execute consultas SQL diretamente no blob

