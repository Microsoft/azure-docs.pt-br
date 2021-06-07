---
title: Treinar com conjuntos de computadores de aprendizado de máquina
titleSuffix: Azure Machine Learning
description: Aprenda a disponibilizar seus dados para sua computação local ou remota para treinamento de modelo com conjuntos de dados Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: 8b984a17c8c10c3dff7c57b7d0223ba8b4197012
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640113"
---
# <a name="train-models-with-azure-machine-learning-datasets"></a>Treinar modelos com conjuntos de Azure Machine Learning 

Neste artigo, você aprenderá a trabalhar com [conjuntos de Azure Machine Learning](/python/api/azureml-core/azureml.core.dataset%28class%29) de trabalho para treinar modelos de aprendizado de máquina.  Você pode usar conjuntos de dados em seu destino de computação local ou remoto sem se preocupar com cadeias de conexão ou caminhos de dado. 

Azure Machine Learning conjuntos de valores fornecem uma integração direta com Azure Machine Learning funcionalidade de treinamento como [pipelines](./how-to-create-machine-learning-pipelines.md)de [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig), [hyperdrive](/python/api/azureml-train-core/azureml.train.hyperdrive) e Azure Machine Learning.

Se você não estiver pronto para disponibilizar seus dados para treinamento de modelo, mas quiser carregar seus dados para o seu bloco de anotações para exploração de dados, consulte como [explorar os dados em seu DataSet](how-to-create-register-datasets.md#explore-data). 

## <a name="prerequisites"></a>Pré-requisitos

Para criar e treinar com conjuntos de os, você precisa:

* Uma assinatura do Azure. Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente hoje mesmo a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

* Um [espaço de trabalho Azure Machine Learning](how-to-manage-workspace.md).

* O [SDK do Azure Machine Learning para Python instalado](/python/api/overview/azure/ml/install) (>= 1.13.0), que inclui o `azureml-datasets` pacote.

> [!Note]
> Algumas classes de conjunto de objetos têm dependências no pacote [azureml-dataprep](https://pypi.org/project/azureml-dataprep/) . Para usuários do Linux, essas classes têm suporte apenas nas seguintes distribuições: Red Hat Enterprise Linux, Ubuntu, Fedora e CentOS.

## <a name="consume-datasets-in-machine-learning-training-scripts"></a>Consumir conjuntos de informações em scripts de treinamento do Machine Learning

Se você tiver dados estruturados que ainda não estão registrados como um DataSet, crie um TabularDataset e use-o diretamente no script de treinamento para seu experimento local ou remoto.

Neste exemplo, você cria um [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) não registrado e o especifica como um argumento de script no objeto ScriptRunConfig para treinamento. Se você quiser reutilizar esse TabularDataset com outros experimentos em seu espaço de trabalho, consulte [como registrar conjuntos de registros em seu espaço de trabalho](how-to-create-register-datasets.md#register-datasets).

### <a name="create-a-tabulardataset"></a>Criar um TabularDataset

O código a seguir cria um TabularDataset não registrado de uma URL da Web.  

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

Os objetos TabularDataset fornecem a capacidade de carregar os dados em seu TabularDataset em um data frame do pandas ou Spark para que você possa trabalhar com bibliotecas de treinamento e preparação de dados familiares sem precisar sair do bloco de anotações.

### <a name="access-dataset-in-training-script"></a>Acessar o conjunto de acesso no script de treinamento

O código a seguir configura um argumento de script `--input-data` que será especificado quando você configurar a execução de treinamento (consulte a próxima seção). Quando o conjunto de valores de tabela é passado como o valor do argumento, o Azure ML resolverá isso para a ID do conjunto de um, que você pode usar para acessar o conjunto de um script de treinamento (sem precisar codificar o nome ou a ID do conjunto de valores no script). Em seguida, ele usa o [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) método para carregar o conjunto de dados em um dataframe do pandas para exploração e preparação de outros e futuramente antes do treinamento.

> [!Note]
> Se a fonte de dados original contiver NaN, cadeias de caracteres vazias ou valores em branco, quando você usar `to_pandas_dataframe()` , esses valores serão substituídos como um valor *nulo* .

Se você precisar carregar os dados preparados em um novo conjunto de dado a partir de um data frame do pandas na memória, grave os dados em um arquivo local, como um parquet, e crie um novo DataSet a partir desse arquivo. Saiba mais sobre [como criar conjuntos de](how-to-create-register-datasets.md)os.

```Python
%%writefile $script_folder/train_titanic.py

import argparse
from azureml.core import Dataset, Run

parser = argparse.ArgumentParser()
parser.add_argument("--input-data", type=str)
args = parser.parse_args()

run = Run.get_context()
ws = run.experiment.workspace

# get the input dataset by ID
dataset = Dataset.get_by_id(ws, id=args.input_data)

# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-training-run"></a>Configurar a execução de treinamento

Um objeto [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrun) é usado para configurar e enviar a execução de treinamento.

Esse código cria um objeto ScriptRunConfig, `src` , que especifica

* Um diretório de script para seus scripts. Todos os arquivos neste diretório são carregados nos nós do cluster para execução.
* O script de treinamento, *train_titanic. py*.
* O conjunto de dados de entrada para treinamento, `titanic_ds` , como um argumento de script. O Azure ML resolverá isso para a ID correspondente do conjunto de um quando for passado para o script.
* O destino de computação para a execução.
* O ambiente para a execução.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=script_folder,
                      script='train_titanic.py',
                      # pass dataset as an input with friendly name 'titanic'
                      arguments=['--input-data', titanic_ds.as_named_input('titanic')],
                      compute_target=compute_target,
                      environment=myenv)
                             
# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)                             
```

## <a name="mount-files-to-remote-compute-targets"></a>Montar arquivos para destinos de computação remota

Se você tiver dados não estruturados, crie um [Filedataset](/python/api/azureml-core/azureml.data.filedataset) e monte ou baixe seus arquivos de dados para disponibilizá-los para o destino de computação remota para treinamento. Saiba mais sobre quando usar o [Mount vs. download](#mount-vs-download) para seus experimentos de treinamento remoto. 

O exemplo a seguir cria um filedataset e monta o conjunto de dados para o destino de computação, passando-o como um argumento para o script de treinamento. 

> [!Note]
> Se você estiver usando uma imagem de base do Docker personalizada, será necessário instalar o fusível por meio `apt-get install -y fuse` do como uma dependência para a montagem do conjunto de dados funcionar. Saiba como [criar uma imagem de compilação personalizada](how-to-deploy-custom-docker-image.md#build-a-custom-base-image).

### <a name="create-a-filedataset"></a>Criar um FileDataset

O exemplo a seguir cria um filedataset não registrado a partir de URLs da Web. Saiba mais sobre [como criar conjuntos](how-to-create-register-datasets.md) de informações de outras fontes.

```Python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
mnist_ds = Dataset.File.from_files(path = web_paths)
```

### <a name="configure-the-training-run"></a>Configurar a execução de treinamento

É recomendável passar o DataSet como um argumento ao montar por meio do `arguments` parâmetro do `ScriptRunConfig` Construtor. Ao fazer isso, você obterá o caminho de dados (ponto de montagem) em seu script de treinamento por meio de argumentos. Dessa forma, você poderá usar o mesmo script de treinamento para depuração local e treinamento remoto em qualquer plataforma de nuvem.

O exemplo a seguir cria um ScriptRunConfig que passa no filedataset via `arguments` . Depois que você enviar a execução, os arquivos de dados referenciados pelo DataSet `mnist_ds` serão montados no destino de computação.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=script_folder,
                      script='train_mnist.py',
                      # the dataset will be mounted on the remote compute and the mounted path passed as an argument to the script
                      arguments=['--data-folder', mnist_ds.as_mount(), '--regularization', 0.5],
                      compute_target=compute_target,
                      environment=myenv)

# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="retrieve-data-in-your-training-script"></a>Recuperar dados em seu script de treinamento

O código a seguir mostra como recuperar os dados em seu script.

```Python
%%writefile $script_folder/train_mnist.py

import argparse
import os
import numpy as np
import glob

from utils import load_data

# retrieve the 2 arguments configured through `arguments` in the ScriptRunConfig
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# get the file paths on the compute
X_train_path = glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0]
X_test_path = glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0]
y_train_path = glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0]
y_test = glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0]

# load train and test set into numpy arrays
X_train = load_data(X_train_path, False) / 255.0
X_test = load_data(X_test_path, False) / 255.0
y_train = load_data(y_train_path, True).reshape(-1)
y_test = load_data(y_test, True).reshape(-1)
```

## <a name="mount-vs-download"></a>Montagem vs download

Há suporte para a montagem ou o download de arquivos de qualquer formato para conjuntos de dados criados a partir do armazenamento de BLOBs do Azure, arquivos do Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2 do Azure SQL e banco de dados do Azure para PostgreSQL. 

Ao **montar** um conjunto de um DataSet, você anexa os arquivos referenciados pelo conjunto de um diretório (ponto de montagem) e os disponibiliza no destino de computação. A montagem tem suporte para computações baseadas em Linux, incluindo Azure Machine Learning computação, máquinas virtuais e HDInsight. 

Quando você **baixa** um conjunto de um DataSet, todos os arquivos referenciados pelo conjunto de um serão baixados para o destino de computação. O download tem suporte para todos os tipos de computação. 

Se o seu script processa todos os arquivos referenciados pelo conjunto de dados e seu disco de computação pode se ajustar a seu conjunto de dados completo, o download é recomendado para evitar a sobrecarga de streaming de serviços de armazenamento. Se o tamanho dos dados exceder o tamanho do disco de computação, o download não será possível. Para este cenário, recomendamos a montagem, pois apenas os arquivos de dados usados pelo script são carregados no momento do processamento.

O código a seguir é montado no `dataset` diretório Temp em `mounted_path`

```python
import tempfile
mounted_path = tempfile.mkdtemp()

# mount dataset onto the mounted_path of a Linux-based compute
mount_context = dataset.mount(mounted_path)

mount_context.start()

import os
print(os.listdir(mounted_path))
print (mounted_path)
```

## <a name="get-datasets-in-machine-learning-scripts"></a>Obter conjuntos de informações em scripts do Machine Learning

Os conjuntos de itens registrados são acessíveis localmente e remotamente em clusters de computação, como a Azure Machine Learning computação. Para acessar seu conjunto de seus conjuntos de testes entre experimentos, use o código a seguir para acessar seu espaço de trabalho e obter o conjunto de um que foi usado em sua execução enviada anteriormente. Por padrão, o [`get_by_name()`](/python/api/azureml-core/azureml.core.dataset.dataset#get-by-name-workspace--name--version--latest--) método na `Dataset` classe retorna a versão mais recente do conjunto de informações registrado com o espaço de trabalho.

```Python
%%writefile $script_folder/train.py

from azureml.core import Dataset, Run

run = Run.get_context()
workspace = run.experiment.workspace

dataset_name = 'titanic_ds'

# Get a dataset by name
titanic_ds = Dataset.get_by_name(workspace=workspace, name=dataset_name)

# Load a TabularDataset into pandas DataFrame
df = titanic_ds.to_pandas_dataframe()
```

## <a name="access-source-code-during-training"></a>Acessar o código-fonte durante o treinamento

O Armazenamento de Blobs do Azure tem velocidades de taxa de transferência mais altas que um compartilhamento de arquivo do Azure e será dimensionado para um grande número de trabalhos iniciados em paralelo. Por esse motivo, recomendamos configurar suas execuções para usar o Armazenamento de Blobs na transferência de arquivos de código-fonte.

O exemplo de código a seguir especifica na configuração de execução qual armazenamento de dados de blob usar em transferências de código-fonte.

```python 
# workspaceblobstore is the default blob storage
src.run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="notebook-examples"></a>Exemplos de notebook

+ Os [notebooks de conjunto de anotações](https://aka.ms/dataset-tutorial) demonstram e se expandem sobre os conceitos deste artigo.
+ Veja como [parametrize conjuntos de valores em seus pipelines de ml](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-dataset-and-pipelineparameter.ipynb).

## <a name="troubleshooting"></a>Solução de problemas

* **Falha na inicialização do conjunto de pontos: aguardando o ponto de montagem estar pronto** expirado: 
  * Se você não tiver regras de [grupo de segurança de rede](../virtual-network/network-security-groups-overview.md) de saída e estiver usando `azureml-sdk>=1.12.0` , atualize `azureml-dataset-runtime` e suas dependências para serem as mais recentes para a versão secundária específica ou se estiver usando-a em uma execução, recrie seu ambiente para que ele possa ter o patch mais recente com a correção. 
  * Se você estiver usando `azureml-sdk<1.12.0` o, atualize para a versão mais recente.
  * Se você tiver regras de NSG de saída, verifique se há uma regra de saída que permita todo o tráfego para a marca de serviço `AzureResourceMonitor` .

### <a name="overloaded-azurefile-storage"></a>Armazenamento do Azurefile sobrecarregado

Se você receber um erro `Unable to upload project files to working directory in AzureFile because the storage is overloaded` , aplique as seguintes soluções alternativas.

Se você estiver usando o compartilhamento de arquivos para outras cargas de trabalho, como a transferência de dados, a recomendação será usar BLOBs para que o compartilhamento de arquivos esteja livre para ser usado para o envio de execuções. Você também pode dividir a carga de trabalho entre dois espaços diferentes.

### <a name="passing-data-as-input"></a>Passando dados como entrada

*  **TypeError: fileNotFound: não há tal arquivo ou diretório**: esse erro ocorrerá se o caminho do arquivo fornecido não for onde o arquivo está localizado. Você precisa certificar-se de que a maneira como você se refere ao arquivo é consistente com o local em que você montou seu conjunto de seu destino de computação. Para garantir um estado determinístico, é recomendável usar o caminho abstrato ao montar um conjunto de um DataSet para um destino de computação. Por exemplo, no código a seguir, montamos o DataSet na raiz do sistema de arquivos do destino de computação, `/tmp` . 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    Se você não incluir a barra "/" à esquerda, será necessário prefixar o diretório de trabalho, por exemplo, `/mnt/batch/.../tmp/dataset` no destino de computação para indicar onde você deseja que o conjunto de os seja montado.


## <a name="next-steps"></a>Próximas etapas

* [Treine automaticamente os modelos de aprendizado de máquina](how-to-auto-train-remote.md) com o TabularDatasets.

* [Treine modelos de classificação de imagem](https://aka.ms/filedataset-samplenotebook) com DataSets.

* [Treine com conjuntos de valores usando pipelines](./how-to-create-machine-learning-pipelines.md).
