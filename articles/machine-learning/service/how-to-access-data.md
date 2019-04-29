---
title: Acessar dados em armazenamentos de dados/blobs para treinamento
titleSuffix: Azure Machine Learning service
description: Saiba como usar os armazenamentos de dados para acessar o armazenamento de dados de blobs durante o treinamento com o Serviço do Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 02/25/2019
ms.custom: seodec18
ms.openlocfilehash: 0ab01187b03b3d658b171029003667588382bd7f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60820288"
---
# <a name="access-data-from-your-datastores"></a>Acessar dados de seus repositórios de dados

 No serviço do Azure Machine Learning, armazenamentos de dados são de computação mecanismos independente de local para acessar o armazenamento sem a necessidade de alterações em seu código-fonte. Se você escrever o código de treinamento para levar um caminho como um parâmetro ou fornece um repositório de dados diretamente para um estimador, certifique-se de fluxos de trabalho do Azure Machine Learning seus locais de repositório de dados estão acessíveis e disponíveis para seu contexto de computação.

Estas instruções mostram exemplos das seguintes tarefas:
* [Escolha um repositório de dados](#access)
* [Obter dados](#get)
* [Carregar e baixar dados para armazenamentos de dados](#up-and-down)
* [Acesso de armazenamento de dados durante o treinamento](#train)

## <a name="prerequisites"></a>Pré-requisitos

Para usar datastores, primeiro você precisa de uma [área de trabalho](concept-azure-machine-learning-architecture.md#workspace).

Início das [criando um novo espaço de trabalho](setup-create-workspace.md#sdk) ou recuperar um existente:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

<a name="access"></a>

## <a name="choose-a-datastore"></a>Escolha um repositório de dados

Você pode usar o armazenamento de dados padrão ou traga seu próprio.

### <a name="use-the-default-datastore-in-your-workspace"></a>Usar o armazenamento de dados padrão em seu espaço de trabalho

 Cada espaço de trabalho tem um armazenamento de dados padrão registrado, você pode usar imediatamente.

Para obter acesso ao repositório de dados padrão do workspace:

```Python
ds = ws.get_default_datastore()
```

### <a name="register-your-own-datastore-with-the-workspace"></a>Registrar seu próprio repositório de dados com o espaço de trabalho

Se você tiver o Armazenamento do Microsoft Azure existente, poderá registrá-lo como um armazenamento de dados em sua área de trabalho.   Todos os métodos de registro são sobre o [ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) de classe e ter o formulário register_azure_ *. 

Os exemplos a seguir mostram a você registrar um contêiner de Blob do Azure ou um compartilhamento de arquivos do Azure como um repositório de dados.

+ Para um **repositório de dados de contêiner de Blob do Azure**, use [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)

  ```Python
  ds = Datastore.register_azure_blob_container(workspace=ws, 
                                               datastore_name='your datastore name', 
                                               container_name='your azure blob container name',
                                               account_name='your storage account name', 
                                               account_key='your storage account key',
                                               create_if_not_exists=True)
  ```

+ Para um **repositório de dados de compartilhamento de arquivos do Azure**, use [ `register_azure_file_share()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). Por exemplo:  
  ```Python
  ds = Datastore.register_azure_file_share(workspace=ws, 
                                           datastore_name='your datastore name', 
                                           file_share_name='your file share name',
                                           account_name='your storage account name', 
                                           account_key='your storage account key',
                                           create_if_not_exists=True)
  ```

<a name="get"></a>

## <a name="find--define-datastores"></a>Localizar & Definir armazenamentos de dados

Para obter um repositório de dados especificado registrado no espaço de trabalho atual, use [ `get()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) :

```Python
#get named datastore from current workspace
ds = Datastore.get(ws, datastore_name='your datastore name')
```

Para obter uma lista de todos os armazenamentos de dados em um determinado espaço de trabalho, use este código:

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, ds in datastores.items():
    print(name, ds.datastore_type)
```

Para definir um repositório de dados padrão diferente para o espaço de trabalho atual, use [ `set_default_datastore()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-):

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>Carregar e baixar dados
O [ `upload()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) e [ `download()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) métodos descritos nos exemplos a seguir são específicos e operar identicamente para o [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) e [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) classes.

### <a name="upload"></a>Carregar

 Carregue um diretório ou arquivos individuais para o armazenamento de dados usando o SDK do Python.

Para carregar um diretório em um repositório de dados `ds`:

```Python
import azureml.data
from azureml.data.azure_storage_datastore import AzureFileDatastore, AzureBlobDatastore

ds.upload(src_dir='your source directory',
          target_path='your target path',
          overwrite=True,
          show_progress=True)
```

`target_path` especifica o local no compartilhamento de arquivos (ou contêiner de blob) para o carregamento. O padrão é `None`, nesse caso, os dados são carregados para a raiz. `overwrite=True` substituirá quaisquer dados existentes em `target_path`.

Ou faça o upload de uma lista de arquivos individuais para o armazenamento de dados por meio do método `upload_files()` do armazenamento de dados.

### <a name="download"></a>Baixar
Da mesma forma, faça o download dos dados de um armazenamento de dados para o sistema de arquivos local.

```Python
ds.download(target_path='your target path',
            prefix='your prefix',
            show_progress=True)
```

`target_path` é a localização do diretório local para o qual os dados serão baixados. Para especificar um caminho para a pasta no compartilhamento de arquivos (ou contêiner de blob) para baixar, forneça esse caminho para `prefix`. Se `prefix` for `None`, todo o conteúdo do compartilhamento de arquivos (ou contêiner de blob) será baixado.

<a name="train"></a>
## <a name="access-datastores-during-training"></a>Acesso a armazenamentos de dados durante o treinamento

Depois que você disponibiliza seu armazenamento de dados no destino de computação, você pode acessá-lo durante execuções de treinamento (por exemplo, dados de treinamento ou validação), simplesmente passando o caminho para ele como um parâmetro em seu script de treinamento.

A seguinte tabela lista os [ `DataReference` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) métodos que informam ao destino de computação como usar o armazenamento de dados durante execuções.

forma|Método|DESCRIÇÃO|
----|-----|--------
Montar| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)| Use para montar o armazenamento de dados no destino de computação.
Baixar|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)|Usar para baixar o conteúdo de seu armazenamento de dados no local especificado por `path_on_compute`. <br> Para o contexto de execução de treinamento, esse download ocorre antes da execução.
Carregar|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)| Use para carregar um arquivo do local especificado por `path_on_compute` para seu repositório de dados. <br> Para o contexto de execução de treinamento, esse carregamento ocorre após sua execução.

 ```Python
import azureml.data
from azureml.data.data_reference import DataReference

ds.as_mount()
ds.as_download(path_on_compute='your path on compute')
ds.as_upload(path_on_compute='yourfilename')
```  

Para fazer referência a uma pasta ou arquivo específico em seu repositório de dados e torná-lo disponível no destino de computação, use o armazenamento de dados [ `path()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#path-path-none--data-reference-name-none-) função.

```Python
#download the contents of the `./bar` directory in ds to the compute target
ds.path('./bar').as_download()
```

> [!NOTE]
> Qualquer `ds` ou `ds.path` objeto é resolvido para um nome de variável de ambiente do formato `"$AZUREML_DATAREFERENCE_XXXX"` cujo valor representa o caminho de montagem/download na computação de destino. O caminho do repositório de dados na computação de destino não pode ser o mesmo que o caminho de execução do script de treinamento.

### <a name="compute-context-and-datastore-type-matrix"></a>Matriz de tipo de repositório de dados e o contexto de computação

A matriz a seguir exibe as funcionalidades de acesso de dados disponíveis para os cenários de armazenamento de dados e de contexto de computação diferentes. O termo "Pipeline" nesta matriz se refere à capacidade de usar armazenamentos de dados como uma entrada ou saída em [Pipelines de Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/concept-ml-pipelines).

||Computação local|Computação do Azure Machine Learning|Transferência de dados|Databricks|HDInsight|Lote do Azure|Azure DataLake Analytics|Máquinas Virtuais|
-|--|-----------|----------|---------|-----|--------------|---------|---------|
|AzureBlobDatastore|[`as_download()`] [`as_upload()`]|[`as_mount()`]<br> [`as_download()`] [`as_upload()`] <br> Pipeline|Pipeline|Pipeline|[`as_download()`] <br> [`as_upload()`]|Pipeline||[`as_download()`] <br> [`as_upload()`]|
|AzureFileDatastore|[`as_download()`] [`as_upload()`]|[`as_mount()`]<br> [`as_download()`] [`as_upload()`] Pipeline |||[`as_download()`] [`as_upload()`]|||[`as_download()`] [`as_upload()`]|
|AzureDataLakeDatastore|||Pipeline|Pipeline|||Pipeline||
|AzureDataLakeGen2Datastore|||Pipeline||||||
|AzureDataPostgresSqlDatastore|||Pipeline||||||
|AzureSqlDatabaseDataDatastore|||Pipeline||||||


> [!NOTE]
> Pode haver situações em que altamente iterativo, dados grandes processos são executados mais rapidamente usando [`as_download()`], em vez de [`as_mount()`]; isso pode ser validado Experimentalmente.

### <a name="examples"></a>Exemplos 

Os exemplos de código a seguir são específicos para o [ `Estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) classe para acessar seu repositório de dados durante o treinamento.

Esse código cria um estimador usando o script de treinamento `train.py`, do diretório de origem indicado, usando os parâmetros definidos no `script_params`, tudo no destino de computação especificado.

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': ds.as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

Você também pode passar em uma lista de armazenamentos de dados para o construtor do estimador `inputs` parâmetro montar ou copiar bidirecionalmente em seus armazenamentos de dados. Este exemplo de código:
* Baixa todo o conteúdo no armazenamento de dados `ds1` para o destino de computação antes de seu script de treinamento `train.py` é executado
* A pasta de downloads `'./foo'` no repositório de dados `ds2` para o destino de computação antes de `train.py` é executado
* Carrega o arquivo `'./bar.pkl'` de destino de computação até o repositório de dados `ds3` depois que o script foi executado

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```

## <a name="next-steps"></a>Próximas etapas

* [Treinar um modelo](how-to-train-ml-models.md)

* [Implantar um modelo](how-to-deploy-and-where.md)
