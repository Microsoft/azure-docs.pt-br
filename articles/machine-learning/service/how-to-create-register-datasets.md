---
title: Criar conjuntos de dados para acessar os dados com o azureml-DataSets
titleSuffix: Azure Machine Learning service
description: Saiba como criar conjuntos de itens de várias fontes e registrar conjuntos de registros com seu espaço de trabalho
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/21/2019
ms.openlocfilehash: 765ec8291ba873c6b200cf330d82e6e2ab53357d
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423118"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>Criar e acessar conjuntos de os (visualização) no Azure Machine Learning

Neste artigo, você aprenderá a criar conjuntos de dados do Azure Machine Learning (versão prévia) e a acessar os dados de experimentos locais ou remotos.

Com os conjuntos de Azure Machine Learning, você pode: 

* **Manter uma única cópia de dados em seu armazenamento** referenciado por conjuntos de dados

* **Analisar dados** por meio da análise de dados exploratórios 

* **Acesse dados facilmente durante o treinamento do modelo** sem se preocupar com a cadeia de conexão ou o caminho de dados

* **Compartilhar dados & colaborar** com outros usuários

## <a name="prerequisites"></a>Pré-requisitos

Para criar e trabalhar com conjuntos de os, você precisa de:

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Serviço do Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

* Um [espaço de trabalho de serviço do Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace)

* O [SDK do Azure Machine Learning para Python instalado](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), que inclui o pacote de conjuntos de linhas do azureml.

> [!Note]
> Algumas classes de DataSet (versão prévia) têm dependências no pacote [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) (GA). Para usuários do Linux, essas classes têm suporte apenas nas seguintes distribuições:  Red Hat Enterprise Linux, Ubuntu, Fedora e CentOS.

## <a name="data-formats"></a>Formatos de dados

Você pode criar um conjunto de Azure Machine Learning a partir dos seguintes formatos:
+ [delimitados](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset#from-delimited-files-path--separator------header--promoteheadersbehavior-all-files-have-same-headers--3---encoding--fileencoding-utf8--0---quoting-false--infer-column-types-true--skip-rows-0--skip-mode--skiplinesbehavior-no-rows--0---comment-none--include-path-false--archive-options-none-)
+ [json](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-json-files-path--encoding--fileencoding-utf8--0---flatten-nested-arrays-false--include-path-false-)
+ [Excel](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-excel-files-path--sheet-name-none--use-column-headers-false--skip-rows-0--include-path-false--infer-column-types-true-)
+ [Parquet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-parquet-files-path--include-path-false-)
+ [dataframe do pandas](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-pandas-dataframe-dataframe--path-none--in-memory-false-)
+ [SQL query](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-sql-query-data-source--query-)
+ [binary](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-binary-files-path-)

## <a name="create-datasets"></a>Criar conjuntos de dados 

Ao criar um conjunto de dados, você cria uma referência para o local da fonte de dado, juntamente com uma cópia de seus metadados. Os dados permanecem em seu local existente, portanto, nenhum custo de armazenamento extra é incorrido.

### <a name="create-from-local-files"></a>Criar a partir de arquivos locais

Carregue arquivos do computador local especificando o caminho do arquivo ou da pasta com o [`auto_read_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#auto-read-files-path--include-path-false-) método `Dataset` da classe.  Esse método executa as etapas a seguir sem exigir que você especifique o tipo de arquivo ou os argumentos de análise:

* Inferindo e definindo o delimitador.
* Ignorando registros vazios na parte superior do arquivo.
* Inferindo e definindo a linha de cabeçalho.
* Inferindo e convertendo tipos de dados de coluna.

```Python
from azureml.core.dataset import Dataset

dataset = Dataset.auto_read_files('./data/crime.csv')
```

Como alternativa, use as funções específicas de arquivo para controlar explicitamente a análise do arquivo. 


### <a name="create-from-azure-datastores"></a>Criar de repositórios de armazenamento do Azure

Para criar conjuntos de valores de um [repositório de armazenamento do Azure](how-to-access-data.md):

* Verifique se você `contributor` tem `owner` ou tem acesso ao repositório de armazenamento do Azure registrado.

* Criar o conjunto de os fazendo referência a um caminho no repositório de armazenamento 

```Python
from azureml.core.workspace import Workspace
from azureml.core.datastore import Datastore
from azureml.core.dataset import Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()

# retrieve an existing datastore in the workspace by name
dstore = Datastore.get(workspace, datastore_name)
```

Use o `from_delimited_files()` método para ler arquivos delimitados de uma [referência](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py)de datareferente e criar um conjunto de um DataSet não registrado.

```Python
# create an in-memory Dataset on your local machine
dataset = Dataset.from_delimited_files(dstore.path('data/src/crime.csv'))

# returns the first 5 rows of the Dataset as a pandas Dataframe.
dataset.head(5)
```

## <a name="register-datasets"></a>Registrar conjuntos de os

Para concluir o processo de criação, registre seus conjuntos de registros com o espaço de trabalho:

Use o [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) método para registrar conjuntos de registros em seu espaço de trabalho para que eles possam ser compartilhados com outras pessoas e reutilizados em vários experimentos.

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',
                           description = 'Training data',
                           exist_ok = False
                           )
```

>[!NOTE]
> Se `exist_ok = False` (padrão) e você tentar registrar um conjunto de registros com o mesmo nome que o outro, ocorrerá um erro. Defina como `True` para substituir existente.

## <a name="access-data-in-datasets"></a>Acessar dados em conjuntos de dados

Os conjuntos de itens registrados são acessíveis localmente e remotamente em clusters de computação, como a Azure Machine Learning computação. Para acessar seu conjunto de seus conjuntos de testes entre experimentos, use o código a seguir para obter seu espaço de trabalho e o conjunto de um registrado pelo nome.

```Python
workspace = Workspace.from_config()

# See list of datasets registered in workspace.
print(Dataset.list(workspace))

# Get dataset by name
dataset = Dataset.get(workspace, 'dataset_crime')

# Load data into pandas DataFrame
dataset.to_pandas_dataframe()
```

## <a name="next-steps"></a>Próximas etapas

* [Explore e prepare conjuntos de valores](how-to-explore-prepare-data.md).
* Para obter um exemplo de como usar conjuntos de valores, consulte os [blocos de anotações de exemplo](https://aka.ms/dataset-tutorial).
