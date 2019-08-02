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
ms.date: 05/24/2019
ms.custom: seodec18
ms.openlocfilehash: 97a4bc20394553b97211763cedaa76c3711306f2
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68319321"
---
# <a name="access-data-from-your-datastores"></a>Acessar dados de seus armazenamentos

 No Azure Machine Learning Service, os repositórios de armazenamento são mecanismos independentes de localização de computação para acessar o armazenamento sem a necessidade de alterações no código-fonte. Se você escrever código de treinamento para pegar um caminho como parâmetro ou fornecer um repositório de armazenamento diretamente para um estimador, Azure Machine Learning fluxos de trabalho garantem que seus locais de armazenamento de datafiquem acessíveis e disponibilizados para o seu contexto de computação.

Este "como" mostra exemplos das seguintes tarefas:
* [Escolher um repositório de armazenamento](#access)
* [Obter dados](#get)
* [Carregar e baixar dados em repositórios](#up-and-down)
* [Acessar o repositório de armazenamento durante o treinamento](#train)

## <a name="prerequisites"></a>Pré-requisitos

Para usar datastores, primeiro você precisa de uma [área de trabalho](concept-workspace.md).

Início das [criando um novo espaço de trabalho](setup-create-workspace.md#sdk) ou recuperar um existente:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

<a name="access"></a>

## <a name="choose-a-datastore"></a>Escolher um repositório de armazenamento

Você pode usar o repositório de armazenamento padrão ou trazer seu próprio.

### <a name="use-the-default-datastore-in-your-workspace"></a>Usar o repositório de armazenamento padrão em seu espaço de trabalho

 Cada espaço de trabalho tem um repositório de armazenamento padrão registrado que você pode usar imediatamente.

Para obter acesso ao repositório de dados padrão do workspace:

```Python
ds = ws.get_default_datastore()
```

### <a name="register-your-own-datastore-with-the-workspace"></a>Registrar seu próprio repositório de armazenamento com o espaço de trabalho

Se você tiver o Armazenamento do Microsoft Azure existente, poderá registrá-lo como um armazenamento de dados em sua área de trabalho. 

<a name="store"></a>

####  <a name="storage-guidance"></a>Orientação de armazenamento

Recomendamos o armazenamento de BLOBs e os armazenamentos de BLOBs. Tanto o armazenamento Standard quanto o Premium estão disponíveis para BLOBs. Embora sejam mais caros, sugerimos o armazenamento Premium devido a velocidades de taxa de transferência mais rápidas que podem melhorar a velocidade de suas execuções de treinamento, especialmente se você treinar em um grande conjunto de dados. Consulte a [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service) para obter informações de custo da conta de armazenamento.

>[!NOTE]
> Azure Machine Learning serviço dá suporte a outros tipos de repositórios de armazenamento, que podem ser úteis para cenários específicos. Por exemplo, se você precisar treinar o uso de dados armazenados em um banco de dado, poderá usar AzureSQLDatabaseDatastore ou AzurePostgreSqlDatastore. Consulte [esta tabela](#matrix) para os tipos de repositório de armazenamento disponíveis.

#### <a name="register-your-datastore"></a>Registrar seu repositório de armazenamento
Todos os métodos de registro estão na [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) classe e têm o formato register_azure_ *.

Os exemplos a seguir mostram que você deve registrar um contêiner de blob do Azure ou um compartilhamento de arquivos do Azure como um repositório de armazenamento.

+ Para um **armazenamento de contêiner de blob do Azure**, use[`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)

  ```Python
  ds = Datastore.register_azure_blob_container(workspace=ws, 
                                               datastore_name='your datastore name', 
                                               container_name='your azure blob container name',
                                               account_name='your storage account name', 
                                               account_key='your storage account key',
                                               create_if_not_exists=True)
  ```

+ Para um **repositório de armazenamento de arquivos do Azure**, use [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). Por exemplo: 
  ```Python
  ds = Datastore.register_azure_file_share(workspace=ws, 
                                           datastore_name='your datastore name', 
                                           file_share_name='your file share name',
                                           account_name='your storage account name', 
                                           account_key='your storage account key',
                                           create_if_not_exists=True)
  ```

<a name="get"></a>

## <a name="find--define-datastores"></a>Localizar & definir repositórios de armazenamento

Para obter um repositório de armazenamento especificado registrado no espaço de trabalho atual [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) , use:

```Python
#get named datastore from current workspace
ds = Datastore.get(ws, datastore_name='your datastore name')
```

Para obter uma lista de todos os repositórios de dados em um determinado espaço de trabalho, use este código:

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, ds in datastores.items():
    print(name, ds.datastore_type)
```

Para definir um repositório de armazenamento padrão diferente para o espaço de trabalho [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-)atual, use:

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>Carregar & Baixar dados
Os [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) métodos [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) e descritos nos exemplos a seguir são específicos para e operam de forma idêntica para as classes [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) e [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) .

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
## <a name="access-datastores-during-training"></a>Acessar repositórios de armazenamento durante o treinamento

Depois de disponibilizar o armazenamento de dados no destino de computação de treinamento, você pode acessá-lo durante as execuções de treinamento (por exemplo, dados de treinamento ou de validação) simplesmente passando o caminho para ele como um parâmetro no script de treinamento.

A tabela a seguir lista [`DataReference`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) os métodos que dizem ao destino de computação como usar o repositório de armazenamento durante execuções.

Aparência|Método|DESCRIÇÃO|
----|-----|--------
Montar| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)| Use para montar o repositório de armazenamento no destino de computação.
Baixar|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)|Use para baixar o conteúdo do seu repositório de armazenamento para o local especificado `path_on_compute`por. <br> Para o contexto de execução de treinamento, esse download ocorre antes da execução.
Carregar|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)| Use para carregar um arquivo do local especificado pelo `path_on_compute` para seu repositório de armazenamento. <br> Para o contexto de execução de treinamento, esse upload ocorre após a execução.

 ```Python
import azureml.data
from azureml.data.data_reference import DataReference

ds.as_mount()
ds.as_download(path_on_compute='your path on compute')
ds.as_upload(path_on_compute='yourfilename')
```  

Para fazer referência a uma pasta ou arquivo específico em seu repositório de armazenamento e torná-lo disponível no destino de computação, use [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#path-path-none--data-reference-name-none-) a função do repositório de armazenamento.

```Python
#download the contents of the `./bar` directory in ds to the compute target
ds.path('./bar').as_download()
```

> [!NOTE]
> Qualquer `ds` objeto `ds.path` ou é resolvido para um nome de variável de ambiente do formato `"$AZUREML_DATAREFERENCE_XXXX"` cujo valor representa o caminho de montagem/download na computação de destino. O caminho do repositório de armazenamento na computação de destino pode não ser o mesmo que o caminho de execução para o script de treinamento.

<a name="matrix"></a>
### <a name="training-compute-and-datastore-matrix"></a>Computação de treinamento e matriz de repositório de armazenamento

A matriz a seguir exibe as funcionalidades de acesso a dados disponíveis para os diferentes destinos de computação de treinamento e cenários de armazenamento de Datastore. Saiba mais sobre os [destinos de computação de treinamento para Azure Machine Learning](how-to-set-up-training-targets.md#compute-targets-for-training).

|Computação|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| Local|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|N/D         |N/D                                                                         |
| Computação do Azure Machine Learning |[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;pipelines](concept-ml-pipelines.md)|[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;pipelines](concept-ml-pipelines.md)|N/D         |N/D                                                                         |
| Máquinas virtuais               |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/D         |N/D                                                                         |
| HDInsight                      |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/D         |N/D                                                                         |
| Transferência de dados                  |[Pipelines de ml&nbsp;](concept-ml-pipelines.md)                                               |N/D                                           |[Pipelines de ml&nbsp;](concept-ml-pipelines.md)            |[Pipelines de ml&nbsp;](concept-ml-pipelines.md)                                                                            |
| Databricks                     |[Pipelines de ml&nbsp;](concept-ml-pipelines.md)                                              |N/D                                           |[Pipelines de ml&nbsp;](concept-ml-pipelines.md)             |N/D                                                                         |
| Lote do Azure                    |[Pipelines de ml&nbsp;](concept-ml-pipelines.md)                                               |N/D                                           |N/D         |N/D                                                                         |
| Azure datalake Analytics       |N/D                                           |N/D                                           |[Pipelines de ml&nbsp;](concept-ml-pipelines.md)             |N/D                                                                         |

> [!NOTE]
> Pode haver cenários em que processos altamente iterativos de dados grandes são executados mais `as_download()` rapidamente usando `as_mount()`em vez de; isso pode ser validado experimentalmente.

### <a name="examples"></a>Exemplos 

Os exemplos de código a seguir são específicos [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) para a classe para acessar seu repositório de armazenamento durante o treinamento.

Esse código cria um estimador usando o script de `train.py`treinamento,, do diretório de origem indicado usando os parâmetros `script_params`definidos em, tudo no destino de computação de treinamento especificado.

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

Você também pode passar uma lista de repositórios de armazenamento para o parâmetro do `inputs` Construtor estimador para montar ou copiar de/para seus repositórios de armazenamento. Este exemplo de código:
* Baixa todo o conteúdo no repositório de `ds1` armazenamento para o destino de computação antes de `train.py` o script de treinamento ser executado
* Baixa a pasta `'./foo'` no repositório de `ds2` armazenamento para o destino de `train.py` computação antes da execução
* Carrega o arquivo `'./bar.pkl'` do destino de computação até o repositório de armazenamento `ds3` após a execução do script

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```

## <a name="access-datastores-during-for-scoring"></a>Acessar repositórios de armazenamento durante a Pontuação

O serviço de Azure Machine Learning fornece várias maneiras de usar seus modelos para pontuação. Alguns desses métodos não fornecem acesso a repositórios de armazenamento. Use a tabela a seguir para entender quais métodos permitem que você acesse os repositórios de armazenamento durante a Pontuação:

| Método | Acesso ao repositório de armazenamento | DESCRIÇÃO |
| ----- | :-----: | ----- |
| [Previsão de lote](how-to-run-batch-predictions.md) | ✔ | Faça previsões em grandes quantidades de dados de forma assíncrona. |
| [Serviço Web](how-to-deploy-and-where.md) | &nbsp; | Implantar modelo (s) como um serviço Web. |
| [Módulo do IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Implantar modelo (s) para IoT Edge dispositivos. |

Para situações em que o SDK não fornece acesso a repositórios de dados, você poderá criar um código personalizado usando o SDK do Azure relevante para acessar o dado. Por exemplo, usando o [SDK de armazenamento do Azure para Python](https://github.com/Azure/azure-storage-python) para acessar dados armazenados em BLOBs.

## <a name="next-steps"></a>Próximas etapas

* [Treinar um modelo](how-to-train-ml-models.md)

* [Implantar um modelo](how-to-deploy-and-where.md)
