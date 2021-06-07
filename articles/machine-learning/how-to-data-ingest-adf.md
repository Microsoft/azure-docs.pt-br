---
title: Ingestão de dados com o Azure Data Factory
titleSuffix: Azure Machine Learning
description: Conheça as opções disponíveis para criar um pipeline de ingestão de dados com Azure Data Factory e os benefícios de cada um.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 01/26/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: b842934ea6bb458a59a53ea7068aa9ece98aacf1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98796155"
---
# <a name="data-ingestion-with-azure-data-factory"></a>Ingestão de dados com o Azure Data Factory

Neste artigo, você aprenderá sobre as opções disponíveis para criar um pipeline de ingestão de dados com [Azure data Factory](../data-factory/introduction.md). Este Azure Data Factory pipeline é usado para ingerir dados para uso com [Azure Machine Learning](overview-what-is-azure-ml.md). Data Factory permite que você extraia, transforme e carregue dados (ETL) facilmente. Depois que os dados tiverem sido transformados e carregados no armazenamento, eles poderão ser usados para treinar seus modelos de aprendizado de máquina no Azure Machine Learning.

A transformação simples de dados pode ser tratada com atividades Data Factory nativas e instrumentos como o [fluxo de dados](../data-factory/control-flow-execute-data-flow-activity.md). Quando se trata de cenários mais complicados, os dados podem ser processados com algum código personalizado. Por exemplo, Python ou código R.

## <a name="compare-azure-data-factory-data-ingestion-pipelines"></a>Comparar Azure Data Factory pipelines de ingestão de dados 
Há várias técnicas comuns de usar Data Factory para transformar dados durante a ingestão. Cada técnica tem vantagens e desvantagens que ajudam a determinar se é uma boa opção para um caso de uso específico:

| Técnica | Vantagens | Desvantagens |
| ----- | ----- | ----- |
| Data Factory + Azure Functions | <li> Computação sem servidor de baixa latência<li>Funções com estado<li>Funções reutilizáveis | Só bom para processamento de execução curta |
| Data Factory + componente personalizado | <li>Computação paralela em larga escala<li>Adequado para algoritmos pesados | <li>Requer código de encapsulamento em um executável<li>Complexidade de lidar com dependências e e/s |
| Data Factory + Azure Databricks Notebook |<li> Apache Spark<li>Ambiente de Python nativo |<li>Pode ser caro<li>A criação de clusters inicialmente leva tempo e adiciona latência

## <a name="azure-data-factory-with-azure-functions"></a>Azure Data Factory com o Azure Functions

Azure Functions permite que você execute pequenas partes de código (funções) sem se preocupar com a infraestrutura do aplicativo. Nessa opção, os dados são processados com o código Python personalizado encapsulado em uma função do Azure. 

A função é invocada com o [Azure data Factory atividade da função do Azure](../data-factory/control-flow-azure-function-activity.md). Essa abordagem é uma boa opção para transformações de dados leves. 

![O diagrama mostra um pipeline Azure Data Factory, com o Azure Function e executa o pipeline ML, e um pipeline de Azure Machine Learning, com o modelo de treinamento e como eles interagem com dados brutos e dados preparados.](media/how-to-data-ingest-adf/adf-function.png)



* Vantagens:
    * Os dados são processados em uma computação sem servidor com uma latência relativamente baixa
    * Data Factory pipeline pode invocar uma [função durável do Azure](../azure-functions/durable/durable-functions-overview.md) que pode implementar um fluxo de transformação de dados sofisticado 
    * Os detalhes da transformação de dados são dissociados para fora da função do Azure que podem ser reutilizados e invocados de outros locais
* Desvantagens
    * O Azure Functions deve ser criado antes do uso com o ADF
    * Azure Functions é bom somente para processamento de dados de execução curta

## <a name="azure-data-factory-with-custom-component-activity"></a>Azure Data Factory com atividade de componente Personalizada

Nessa opção, os dados são processados com o código Python personalizado encapsulado em um executável. Ele é invocado com uma [ Azure data Factory atividade de componente Personalizada](../data-factory/transform-data-using-dotnet-custom-activity.md). Essa abordagem é uma melhor opção para dados grandes do que a técnica anterior.

![O diagrama mostra um pipeline de Azure Data Factory, com um componente personalizado e executa o pipeline L d e um pipeline de Azure Machine Learning, com o modelo de treinamento e como eles interagem com dados brutos e dados preparados.](media/how-to-data-ingest-adf/adf-customcomponent.png)

* Vantagens:
    * Os dados são processados no pool [do lote do Azure](../batch/batch-technical-overview.md) , que fornece computação em larga escala paralela e de alto desempenho
    * Pode ser usado para executar algoritmos pesados e processar quantidades significativas de dados
* As desvantagens:
    * O pool do lote do Azure deve ser criado antes do uso com Data Factory
    * Sobre a engenharia relacionada à disposição do código Python em um executável. Complexidade de tratamento de dependências e parâmetros de entrada/saída

## <a name="azure-data-factory-with-azure-databricks-python-notebook"></a>Azure Data Factory com Azure Databricks Notebook Python

[Azure Databricks](https://azure.microsoft.com/services/databricks/) é uma plataforma de análise baseada em Apache Spark no Microsoft Cloud.

Nessa técnica, a transformação de dados é executada por um [Notebook Python](../data-factory/transform-data-using-databricks-notebook.md), em execução em um cluster Azure Databricks. Isso é provavelmente, a abordagem mais comum que aproveita todo o poder de um serviço de Azure Databricks. Ele foi projetado para processamento de dados distribuídos em escala.

![O diagrama mostra um pipeline Azure Data Factory, com Azure Databricks Python e executa o pipeline L M e um pipeline Azure Machine Learning, com o modelo de treinamento e como eles interagem com dados brutos e dados preparados.](media/how-to-data-ingest-adf/adf-databricks.png)

* Vantagens:
    * Os dados são transformados no serviço de processamento de dados mais potente do Azure, cujo backup é feito por Apache Spark ambiente
    * Suporte nativo do Python juntamente com estruturas e bibliotecas de ciência de dados, incluindo TensorFlow, PyTorch e scikit-Learn
    * Não é necessário encapsular o código Python em funções ou módulos executáveis. O código funciona como está.
* As desvantagens:
    * Azure Databricks infraestrutura deve ser criada antes do uso com Data Factory
    * Pode ser caro dependendo da configuração do Azure Databricks
    * A rotação de clusters de computação do modo "frio" leva algum tempo que leva alta latência para a solução 
    

## <a name="consume-data-in-azure-machine-learning"></a>Consumir dados no Azure Machine Learning 

O pipeline de Data Factory salva os dados preparados em seu armazenamento em nuvem (como o blob do Azure ou o Azure datalake). <br>
Consuma seus dados preparados em Azure Machine Learning pelo, 

* Invocar um pipeline de Azure Machine Learning de seu pipeline de Data Factory.<br>**OR**
* Criar um [repositório de armazenamento de Azure Machine Learning](how-to-access-data.md#create-and-register-datastores) e [Azure Machine Learning conjunto](how-to-create-register-datasets.md) de data para uso posterior.

### <a name="invoke-azure-machine-learning-pipeline-from-data-factory"></a>Invocar Azure Machine Learning pipeline de Data Factory

Esse método é recomendado para [fluxos de trabalho de MLOps (operações de Machine Learning)](concept-model-management-and-deployment.md#what-is-mlops). Se você não quiser configurar um pipeline de Azure Machine Learning, consulte [ler dados diretamente do armazenamento](#read-data-directly-from-storage).

Cada vez que o pipeline de Data Factory é executado, 

1. Os dados são salvos em um local diferente no armazenamento. 
1. Para passar o local para Azure Machine Learning, o pipeline Data Factory chama um [pipeline Azure Machine Learning](concept-ml-pipelines.md). Ao chamar o pipeline de ML, o local de dados e a ID de execução são enviados como parâmetros. 
1. O pipeline ML pode, então, criar um armazenamento de dados Azure Machine Learning e um DataSet com o local do dado. Saiba mais em [executar pipelines de Azure Machine Learning no data Factory](../data-factory/transform-data-machine-learning-service.md).

![O diagrama mostra um pipeline de Azure Data Factory e um pipeline de Azure Machine Learning e como eles interagem com dados brutos e dados preparados. O pipeline Data Factory feeds de dados para o banco de dados preparado, que alimenta um armazenamento de dados, que alimenta conjuntos de dados no espaço de trabalho Machine Learning.](media/how-to-data-ingest-adf/aml-dataset.png)

> [!TIP]
> Os conjuntos de dados [dão suporte ao controle de versão](./how-to-version-track-datasets.md), de modo que o pipeline ml pode registrar uma nova versão do conjunto de dados que aponta para o dado mais recente do pipeline do ADF.

Depois que os dados estiverem acessíveis por meio de um datastore ou DataSet, você poderá usá-los para treinar um modelo de ML. O processo de treinamento pode fazer parte do mesmo pipeline ML chamado do ADF. Ou pode ser um processo separado, como experimentação em um notebook Jupyter.

Como os conjuntos de dados dão suporte ao controle de versão e cada execução do pipeline cria uma nova versão, é fácil entender qual versão dos dados foi usada para treinar um modelo.

### <a name="read-data-directly-from-storage"></a>Ler dados diretamente do armazenamento

Se você não quiser criar um pipeline de ML, poderá acessar os dados diretamente da conta de armazenamento em que seus dados preparados são salvos com um armazenamento Azure Machine Learning Data Store e DataSet. 

O código Python a seguir demonstra como criar um repositório de armazenamento que se conecta ao armazenamento do Azure datalake geração 2. [Saiba mais sobre repositórios de armazenamento e onde encontrar permissões de entidade de serviço](how-to-access-data.md#create-and-register-datastores).

```python
ws = Workspace.from_config()
adlsgen2_datastore_name = '<ADLS gen2 storage account alias>'  #set ADLS Gen2 storage account alias in AML

subscription_id=os.getenv("ADL_SUBSCRIPTION", "<ADLS account subscription ID>") # subscription id of ADLS account
resource_group=os.getenv("ADL_RESOURCE_GROUP", "<ADLS account resource group>") # resource group of ADLS account

account_name=os.getenv("ADLSGEN2_ACCOUNTNAME", "<ADLS account name>") # ADLS Gen2 account name
tenant_id=os.getenv("ADLSGEN2_TENANT", "<tenant id of service principal>") # tenant id of service principal
client_id=os.getenv("ADLSGEN2_CLIENTID", "<client id of service principal>") # client id of service principal
client_secret=os.getenv("ADLSGEN2_CLIENT_SECRET", "<secret of service principal>") # the secret of service principal

adlsgen2_datastore = Datastore.register_azure_data_lake_gen2(
    workspace=ws,
    datastore_name=adlsgen2_datastore_name,
    account_name=account_name, # ADLS Gen2 account name
    filesystem='<filesystem name>', # ADLS Gen2 filesystem
    tenant_id=tenant_id, # tenant id of service principal
    client_id=client_id, # client id of service principal
```

Em seguida, crie um conjunto de informações para referenciar os arquivos que você deseja usar em sua tarefa de aprendizado de máquina. 

O código a seguir cria um TabularDataset de um arquivo CSV, `prepared-data.csv` . Saiba mais sobre [tipos de conjunto de informações e formatos de arquivo aceitos](how-to-create-register-datasets.md#dataset-types). 

```python
from azureml.core import Workspace, Datastore, Dataset
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig

# retrieve data via AML datastore
datastore = Datastore.get(ws, adlsgen2_datastore)
datastore_path = [(datastore, '/data/prepared-data.csv')]
        
prepared_dataset = Dataset.Tabular.from_delimited_files(path=datastore_path)
```

A partir daqui, use `prepared_dataset` para referenciar seus dados preparados, como em seus scripts de treinamento. Saiba como [treinar modelos com conjuntos de os em Azure Machine Learning](./how-to-train-with-datasets.md).

## <a name="next-steps"></a>Próximas etapas

* [Executar um bloco de anotações do databricks no Azure Data Factory](../data-factory/transform-data-using-databricks-notebook.md)
* [Acessar dados nos serviços de armazenamento do Azure](./how-to-access-data.md#create-and-register-datastores)
* [Treine modelos com conjuntos de os Azure Machine Learning](./how-to-train-with-datasets.md).
* [DevOps para um pipeline de ingestão de dados](./how-to-cicd-data-ingestion.md)