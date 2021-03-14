---
title: Wrangling de dados com pools de Apache Spark (versão prévia)
titleSuffix: Azure Machine Learning
description: Saiba como anexar e iniciar pools de Apache Spark para Wrangling de dados com o Azure Synapse Analytics e o Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/02/2021
ms.custom: how-to, devx-track-python, data4ml, synapse-azureml
ms.openlocfilehash: acd8df620e23ee4ebc103d8910c6443f47ffa141
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503820"
---
# <a name="attach-apache-spark-pools-powered-by-azure-synapse-analytics-for-data-wrangling-preview"></a>Anexar pools de Apache Spark (da plataforma Azure Synapse Analytics) for Data Wrangling (versão prévia)

Neste artigo, você aprende a anexar e a iniciar um pool de Apache Spark da plataforma [Azure Synapse Analytics](/synapse-analytics/overview-what-is.md) for Data Wrangling em escala. 

Este artigo contém diretrizes para executar tarefas de Wrangling de dados interativamente em uma sessão Synapse dedicada em um notebook Jupyter. Se você preferir usar pipelines Azure Machine Learning, consulte [como usar Apache Spark (da plataforma Azure Synapse Analytics) em seu pipeline do Machine Learning (versão prévia)](how-to-use-synapsesparkstep.md).

>[!IMPORTANT]
> A integração do Azure Machine Learning e do Azure Synapse Analytics está em versão prévia. Os recursos apresentados neste artigo empregam o `azureml-synapse` pacote que contém recursos de visualização [experimental](/python/api/overview/azure/ml/#stable-vs-experimental) que podem ser alterados a qualquer momento.

## <a name="azure-machine-learning-and-azure-synapse-analytics-integration-preview"></a>Azure Machine Learning e integração do Azure Synapse Analytics (versão prévia)

A integração do Azure Synapse Analytics com o Azure Machine Learning (versão prévia) permite anexar um pool de Apache Spark apoiado pelo Azure Synapse para exploração e preparação de dados interativas. Com essa integração, você pode ter uma computação dedicada para Wrangling de dados em escala, tudo no mesmo bloco de anotações do Python que você usa para treinar seus modelos de aprendizado de máquina.

## <a name="prerequisites"></a>Pré-requisitos

* [Criar um workspace do Azure Machine Learning](how-to-manage-workspace.md?tabs=python).

* [Crie um espaço de trabalho do Azure Synapse Analytics no portal do Azure](../synapse-analytics/quickstart-create-workspace.md).

* [Criar Apache Spark pool usando portal do Azure, ferramentas da Web ou Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)

* [Configure seu ambiente de desenvolvimento](how-to-configure-environment.md) para instalar o sdk do Azure Machine Learning ou use uma [instância de computação Azure Machine Learning](concept-compute-instance.md#create) com o SDK já instalado. 

* Instale o `azureml-synapse` pacote (versão prévia) com o seguinte código:

  ```python
  pip install azureml-synapse
  ```

* [Link Azure Machine Learning espaço de trabalho e espaço de trabalho do Azure Synapse Analytics](how-to-link-synapse-ml-workspaces.md).

## <a name="get-an-existing-linked-service"></a>Obter um serviço vinculado existente
Antes de poder anexar uma computação dedicada para Wrangling de dados, você deve ter um espaço de trabalho am vinculado a um espaço de trabalho do Azure Synapse Analytics, que é conhecido como um serviço vinculado. 

Para recuperar e usar um serviço vinculado existente, é necessário ter permissões de **usuário ou colaborador** para o espaço de trabalho do Azure Synapse Analytics.

Exiba todos os serviços vinculados associados ao seu espaço de trabalho do Machine Learning. 

```python
LinkedService.list(ws)
```

Este exemplo recupera um serviço vinculado existente, `synapselink1` , do espaço de trabalho, `ws` , com o [`get()`](/python/api/azureml-core/azureml.core.linkedservice#get-workspace--name-) método.
```python
linked_service = LinkedService.get(ws, 'synapselink1')
```
 
## <a name="attach-synapse-spark-pool-as-a-compute"></a>Anexar pool do Spark Synapse como uma computação

Depois de recuperar o serviço vinculado, anexe um pool de Apache Spark de Synapse como um recurso de computação dedicado para suas tarefas de Wrangling de dados. 

Você pode anexar pools de Apache Spark via,
* Azure Machine Learning Studio
* [Modelos do Azure Resource Manager (ARM)](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)
* O SDK do Python 

### <a name="attach-a-pool-via-the-studio"></a>Anexar um pool por meio do estúdio
Siga estas etapas: 

1. Entre no [Azure Machine Learning Studio](https://ml.azure.com/).
1. Selecione **Serviços vinculados** na seção **gerenciar** do painel esquerdo.
1. Selecione seu espaço de trabalho do Synapse.
1. Selecione **pools do Spark anexados** na parte superior esquerda. 
1. Selecionar **Anexar**. 
1. Selecione o pool de Apache Spark na lista e forneça um nome.  
    1. Essa lista identifica os pools do Spark Synapse disponíveis que podem ser anexados à sua computação. 
    1. Para criar um novo pool do Spark Synapse, confira [criar pool de Apache Spark com o Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)
1. Selecione **anexar selecionado**. 

### <a name="attach-a-pool-with-the-python-sdk"></a>Anexar um pool com o SDK do Python

Você também pode empregar o **SDK do Python** para anexar um pool de Apache Spark. 

O código a seguir, 
1. Configura o SynapseCompute com,

   1. O LinkedService, `linked_service` que você criou ou recuperou na etapa anterior. 
   1. O tipo de destino de computação que você deseja anexar, `SynapseSpark`
   1. O nome do pool de Apache Spark. Isso deve corresponder a um pool de Apache Spark existente que está em seu espaço de trabalho do Azure Synapse Analytics.
   
1. Cria um ComputeTarget de Machine Learning passando por 
   1. O espaço de trabalho do Machine Learning que você deseja usar, `ws`
   1. O nome que você deseja consultar na computação no espaço de trabalho Azure Machine Learning. 
   1. O attach_configuration que você especificou ao configurar sua computação Synapse.
       1. A chamada para ComputeTarget. Attach () é assíncrona, portanto, o exemplo é bloqueado até que a chamada seja concluída.

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(linked_service, #Linked synapse workspace alias
                                                    type='SynapseSpark', #Type of assets to attach
                                                    pool_name="<Synapse Spark pool name>") #Name of Synapse spark pool 

synapse_compute = ComputeTarget.attach(workspace= ws,                
                                       name="<Synapse Spark pool alias in Azure ML>", 
                                       attach_configuration=attach_config
                                      )

synapse_compute.wait_for_completion()
```

Verifique se o pool de Apache Spark está anexado.

```python
ws.compute_targets['Synapse Spark pool alias']
```

## <a name="launch-synapse-spark-pool-for-data-preparation-tasks"></a>Iniciar o pool do Spark Synapse para tarefas de preparação de dados

Você pode especificar um [ambiente de Azure Machine Learning](concept-environments.md) a ser usado durante a sessão de Apache Spark. Somente as dependências Conda especificadas no ambiente entrarão em vigor. Não há suporte para a imagem do Docker.

>[!WARNING]
>  Não há suporte para dependências do Python especificadas nas dependências de Conda de ambiente em pools de Apache Spark. Atualmente, há suporte apenas para versões fixas do Python. Verifique sua versão do Python incluindo  `sys.version_info` em seu script.

O código a seguir, cria o ambiente, `myenv` , que instala a `azureml-core` versão 1.20.0 e a `numpy` versão 1.17.0 antes do início da sessão. Em seguida, você pode incluir esse ambiente em sua instrução de sessão de Apache Spark `start` .

```python

from azureml.core import Workspace, Environment

# creates environment with numpy and azureml-core dependencies
ws = Workspace.from_config()
env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core==1.20.0")
env.python.conda_dependencies.add_conda_package("numpy==1.17.0")
env.register(workspace=ws)
```

Para iniciar a preparação de dados com o pool de Apache Spark, especifique o nome do pool de Apache Spark e forneça sua ID de assinatura, o grupo de recursos do espaço de trabalho do Machine Learning, o nome do espaço de trabalho do Machine Learning e o ambiente a ser usado durante a sessão de Apache Spark. 

> [!IMPORTANT]
> Para continuar usando o pool de Apache Spark, você deve indicar qual recurso de computação usar em suas tarefas de Wrangling de dados com `%synapse` para linhas de código únicas e `%%synapse` para várias linhas. 

```python
%synapse start -c SynapseSparkPoolAlias -s AzureMLworkspaceSubscriptionID -r AzureMLworkspaceResourceGroupName -w AzureMLworkspaceName -e myenv
```

Depois que a sessão for iniciada, você poderá verificar os metadados da sessão.

```python
%synapse meta
```

## <a name="load-data-from-storage"></a>Carregar dados do armazenamento

Quando a sessão de Apache Spark for iniciada, leia os dados que você deseja preparar. Há suporte para o carregamento de dados no armazenamento de BLOBs do Azure e Azure Data Lake Storage as gerações 1 e 2.

Há duas maneiras de carregar dados desses serviços de armazenamento: 

* Carregue dados diretamente do armazenamento usando seu caminho do sistema de arquivos distribuído Hadoop (HDFS).

* Ler dados de um conjunto de [Azure Machine Learning](how-to-create-register-datasets.md)existente.

Para acessar esses serviços de armazenamento, você precisa de permissões de **leitor de dados de armazenamento de BLOBs** . Se você planeja gravar dados de volta para esses serviços de armazenamento, precisará de permissões de **colaborador de dados de blob de armazenamento** . [Saiba mais sobre permissões e funções de armazenamento](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues).

### <a name="load-data-with-hadoop-distributed-files-system-hdfs-path"></a>Carregar dados com o caminho HDFS (sistema de arquivos distribuído Hadoop)

Para carregar e ler dados no armazenamento com o caminho HDFS correspondente, você precisa ter suas credenciais de autenticação de acesso a dados prontamente disponíveis. Essas credenciais são diferentes dependendo do tipo de armazenamento.  

O código a seguir demonstra como ler dados de um **armazenamento de BLOBs do Azure** em um dataframe do Spark com seu token de assinatura de acesso compartilhado (SAS) ou chave de acesso. 

```python
%%synapse

# setup access key or SAS token
sc._jsc.hadoopConfiguration().set("fs.azure.account.key.<storage account name>.blob.core.windows.net", "<access key>")
sc._jsc.hadoopConfiguration().set("fs.azure.sas.<container name>.<storage account name>.blob.core.windows.net", "<sas token>")

# read from blob 
df = spark.read.option("header", "true").csv("wasbs://demo@dprepdata.blob.core.windows.net/Titanic.csv")
```

O código a seguir demonstra como ler dados de **Azure data Lake Storage geração 1 (ADLS Gen 1)** com suas credenciais de entidade de serviço. 

```python

# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.access.token.provider.type","ClientCredential")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.client.id", "<client id>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.credential", "<client secret>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.refresh.url",
https://login.microsoftonline.com/<tenant id>/oauth2/token)

df = spark.read.csv("adl://<storage account name>.azuredatalakestore.net/<path>")

```

O código a seguir demonstra como ler dados no de **Azure data Lake Storage geração 2 (ADLS Gen 2)** com suas credenciais de entidade de serviço. 

```python
# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.azure.account.auth.type.<storage account name>.dfs.core.windows.net","OAuth")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth.provider.type.<storage account name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.id.<storage account name>.dfs.core.windows.net", "<client id>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.secret.<storage account name>.dfs.core.windows.net", "<client secret>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.endpoint.<storage account name>.dfs.core.windows.net",
https://login.microsoftonline.com/<tenant id>/oauth2/token)


df = spark.read.csv("abfss://<container name>@<storage account>.dfs.core.windows.net/<path>")

```

### <a name="read-in-data-from-registered-datasets"></a>Ler dados de conjuntos de dados registrados

Você também pode obter um conjunto de dados registrado existente em seu espaço de trabalho e executar a preparação de dado nele convertendo-o em um dataframe do Spark.  

O exemplo a seguir obtém um TabularDataset registrado, `blob_dset` , que faz referência a arquivos no armazenamento de BLOBs e os converte em um dataframe do Spark. Quando você converte seus conjuntos de dados em um dataframe do Spark, você pode aproveitar as `pyspark` bibliotecas de exploração e de preparação do dado.  

``` python

%%synapse
from azureml.core import Workspace, Dataset

dset = Dataset.get_by_name(ws, "blob_dset")
spark_df = dset.to_spark_dataframe()
```

## <a name="perform-data-wrangling-tasks"></a>Executar tarefas de Wrangling de dados

Depois de recuperar e explorar seus dados, você pode executar tarefas de Wrangling de dados.

O código a seguir, expande o exemplo do HDFS na seção anterior e filtra os dados no dataframe do Spark, `df` , com base na coluna **sobrevivente** e agrupa a lista por **idade**

```python
%%synapse
from pyspark.sql.functions import col, desc

df.filter(col('Survived') == 1).groupBy('Age').count().orderBy(desc('count')).show(10)

df.show()

```

## <a name="save-data-to-storage-and-stop-spark-session"></a>Salvar dados no armazenamento e parar a sessão do Spark

Depois que a exploração e a preparação dos dados forem concluídas, armazene seus dados preparados para uso posterior em sua conta de armazenamento no Azure.

No exemplo a seguir, os dados preparados são gravados no armazenamento de BLOBs do Azure e substitui o `Titanic.csv` arquivo original no `training_data` diretório. Para fazer write-back no armazenamento, você precisa de permissões de **colaborador de dados de blob de armazenamento** . [Saiba mais sobre permissões e funções de armazenamento](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues).

```python
%% synapse

df.write.format("csv").mode("overwrite").save("wasbs://demo@dprepdata.blob.core.windows.net/training_data/Titanic.csv")
```

Quando você concluir a preparação de dados e salvar os dados preparados no armazenamento, pare de usar o pool de Apache Spark com o comando a seguir.

```python
%synapse stop
```

## <a name="create-dataset-to-represent-prepared-data"></a>Crie um conjunto de dados para representar dado preparado

Quando você estiver pronto para consumir seus dados preparados para treinamento de modelo, conecte-se ao armazenamento com um [Azure Machine Learning datastore](how-to-access-data.md)e especifique quais arquivos deseja usar com um conjunto de dados [Azure Machine Learning](how-to-create-register-datasets.md).

O exemplo de código a seguir,

* Pressupõe que você já criou um armazenamento de dados que se conecta ao serviço de armazenamento em que você salvou os seus dados preparados.  
* Obtém o repositório de armazenamento existente, `mydatastore` , no espaço de trabalho, `ws` com o método Get ().
* Cria um [Filedataset](how-to-create-register-datasets.md#filedataset), `train_ds` , que faz referência aos arquivos de dados preparados localizados no `training_data` diretório no `mydatastore` .  
* Cria a variável `input1` , que pode ser usada mais tarde para disponibilizar os arquivos de dados do `train_ds` DataSet para um destino de computação.

```python
from azureml.core import Datastore, Dataset

datastore = Datastore.get(ws, datastore_name='mydatastore')

datastore_paths = [(datastore, '/training_data/')]
train_ds = Dataset.File.from_files(path=datastore_paths, validate=True)
input1 = train_ds.as_mount()

```

## <a name="next-steps"></a>Próximas etapas

* [Treinar um modelo](how-to-set-up-training-targets.md).
* [Treinar com conjunto de Azure Machine Learning](how-to-train-with-datasets.md)
