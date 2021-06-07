---
title: 'Tutorial: Usar seus próprios dados'
titleSuffix: Azure Machine Learning
description: A Parte 4 da série de introdução do Azure Machine Learning mostra como usar seus dados em uma execução de treinamento remoto.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 02/11/2021
ms.custom: tracking-python, contperf-fy21q3
ms.openlocfilehash: 503d1d1220cd4704a6e70d0b7e575a70275e5e4d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105936828"
---
# <a name="tutorial-use-your-own-data-part-4-of-4"></a>Tutorial: Usar seus dados (parte 4 de 4)

Este tutorial mostra como carregar e usar seus próprios dados para treinar modelos de machine learning no Azure Machine Learning.

Este tutorial é a *parte 4 de uma série de tutoriais de quatro partes* em que você aprenderá os conceitos básicos do Azure Machine Learning e realizará tarefas de machine learning baseadas em trabalhos no Azure. Este tutorial se baseia no trabalho que você concluiu na [Parte 1: Configurar](tutorial-1st-experiment-sdk-setup-local.md), [Parte 2: Executar "Olá, Mundo!"](tutorial-1st-experiment-hello-world.md) e [Parte 3: Treine um modelo](tutorial-1st-experiment-sdk-train.md).

Na [Parte 3: Treinar um modelo](tutorial-1st-experiment-sdk-train.md), os dados foram baixados por meio do método `torchvision.datasets.CIFAR10` embutido na API do PyTorch. No entanto, em muitos casos, o ideal é usar seus dados em uma execução de treinamento remoto. Este artigo mostra o fluxo de trabalho que você pode usar para trabalhar com seus dados no Azure Machine Learning.

Neste tutorial, você:

> [!div class="checklist"]
> * Configura o script de treinamento para usar dados em um diretório local.
> * Testa o script de treinamento localmente.
> * Carrega dados no Azure.
> * Cria um script de controle.
> * Entende os novos conceitos do Azure Machine Learning (passagem de parâmetros, conjuntos de dados, armazenamentos de dados).
> * Envia e executa seu script de treinamento.
> * Exibe a saída do código na nuvem.

## <a name="prerequisites"></a>Pré-requisitos

Você precisará dos dados e de uma versão atualizada do ambiente do PyTorch criado no tutorial anterior.  Lembre-se de concluir estas etapas:

1. [Criar o script de treinamento](tutorial-1st-experiment-sdk-train.md#create-training-scripts)
1. [Criar um ambiente do Python](tutorial-1st-experiment-sdk-train.md#environment)
1. [Testá-lo localmente](tutorial-1st-experiment-sdk-train.md#test-local)
1. [Atualizar o arquivo do ambiente Conda](tutorial-1st-experiment-sdk-train.md#update-the-conda-environment-file)

## <a name="adjust-the-training-script"></a>Ajustar o script de treinamento

A essa altura, você tem o script de treinamento (tutorial/src/train.py) em execução no Azure Machine Learning e pode monitorar o desempenho do modelo. Vamos parametrizar o script de treinamento introduzindo argumentos. O uso de argumentos permitirá que você compare com facilidade diferentes hiperparâmetros.

Agora, nosso script de treinamento está definido para baixar o conjunto de dados CIFAR10 em cada execução. O código Python a seguir foi ajustado para ler os dados de um diretório.

>[!NOTE] 
> O uso de `argparse` parametriza o script.

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/code/pytorch-cifar10-your-data/train.py":::

### <a name="understanding-the-code-changes"></a>Compreender as alterações de código

O código em `train.py` usou a biblioteca `argparse` para configurar `data_path`, `learning_rate` e `momentum`.

```python
# .... other code
parser = argparse.ArgumentParser()
parser.add_argument('--data_path', type=str, help='Path to the training data')
parser.add_argument('--learning_rate', type=float, default=0.001, help='Learning rate for SGD')
parser.add_argument('--momentum', type=float, default=0.9, help='Momentum for SGD')
args = parser.parse_args()
# ... other code
```

Além disso, o script `train.py` foi adaptado para atualizar o otimizador de modo que ele use os parâmetros definidos pelo usuário:

```python
optimizer = optim.SGD(
    net.parameters(),
    lr=args.learning_rate,     # get learning rate from command-line argument
    momentum=args.momentum,    # get momentum from command-line argument
)
```

> [!div class="nextstepaction"]
> [Ajustei o script de treinamento](?success=adjust-training-script#test-locally) [Encontrei um problema](https://www.research.net/r/7C6W7BQ?issue=adjust-training-script)

## <a name="test-the-script-locally"></a><a name="test-locally"></a> Testar o script localmente

Seu script agora aceita _caminho de dados_ como um argumento. Para começar, teste-o localmente. Adicione à estrutura do diretório do tutorial uma pasta chamada `data`. A estrutura do diretório deve ser semelhante a:

:::image type="content" source="media/tutorial-1st-experiment-bring-data/directory-structure.png" alt-text="A estrutura de diretório mostra subdiretórios .azureml, data e src":::

1. Saia do ambiente atual.

    ```bash
    conda deactivate

1. Now create and activate the new environment.  This will rebuild the pytorch-aml-env with the [updated environment file](tutorial-1st-experiment-sdk-train.md#update-the-conda-environment-file)


    ```bash
    conda env create -f .azureml/pytorch-env.yml    # create the new conda environment with updated dependencies
    ```

    ```bash
    conda activate pytorch-aml-env          # activate new conda environment
    ```

1. Por fim, execute o script de treinamento modificado localmente.

    ```bash
    python src/train.py --data_path ./data --learning_rate 0.003 --momentum 0.92
    ```

Você evita precisar baixar o conjunto de dados CIFAR10 passando um caminho local para os dados. Experimente também valores diferentes para os hiperparâmetros _taxa de aprendizado_ e _dinâmica_ sem precisar embuti-los em código no script de treinamento.

> [!div class="nextstepaction"]
> [Testei o script localmente](?success=test-locally#upload) [Encontrei um problema](https://www.research.net/r/7C6W7BQ?issue=test-locally)

## <a name="upload-the-data-to-azure"></a><a name="upload"></a> Carregar os dados para o Azure

Para executar esse script no Azure Machine Learning, você precisará disponibilizar seus dados de treinamento no Azure. Seu Workspace do Azure Machine Learning vem equipado com um armazenamento de dados _padrão_. Essa é uma conta de Armazenamento de Blobs do Azure na qual você pode armazenar seus dados de treinamento.

>[!NOTE] 
> O Azure Machine Learning permite que você se conecte a outros armazenamentos de dados baseados em nuvem que armazenam seus dados. Para obter mais detalhes, confira a [documentação de armazenamentos de dados](./concept-data.md).  

Crie um script de controle do Python chamado `05-upload-data.py` no diretório `tutorial`:

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/05-upload-data.py":::

O valor `target_path` especifica o caminho no armazenamento de dados no qual os dados do CIFAR10 serão carregados.

>[!TIP] 
> Embora esteja usando o Azure Machine Learning para carregar os dados, você pode usar o [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para carregar arquivos ad hoc. Caso precise de uma ferramenta ETL, use o [Azure Data Factory](../data-factory/introduction.md) para ingerir seus dados no Azure.

Na janela que tem o ambiente do Conda *tutorial1* ativado, execute o arquivo do Python para carregar os dados. (O upload deve ser rápido, em menos de 60 segundos.)

```bash
python 05-upload-data.py
```

Você deverá ver a seguinte saída padrão:

```txt
Uploading ./data\cifar-10-batches-py\data_batch_2
Uploaded ./data\cifar-10-batches-py\data_batch_2, 4 files out of an estimated total of 9
.
.
Uploading ./data\cifar-10-batches-py\data_batch_5
Uploaded ./data\cifar-10-batches-py\data_batch_5, 9 files out of an estimated total of 9
Uploaded 9 files
```

> [!div class="nextstepaction"]
> [Carreguei os dados](?success=upload-data#control-script) [Encontrei um problema](https://www.research.net/r/7C6W7BQ?issue=upload-data)

## <a name="create-a-control-script"></a><a name="control-script"></a> Criar um script de controle

Como você fez anteriormente, crie um script de controle Python chamado `06-run-pytorch-data.py`:

```python
# 06-run-pytorch-data.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig
from azureml.core import Dataset

if __name__ == "__main__":
    ws = Workspace.from_config()
    datastore = ws.get_default_datastore()
    dataset = Dataset.File.from_files(path=(datastore, 'datasets/cifar10'))

    experiment = Experiment(workspace=ws, name='day1-experiment-data')

    config = ScriptRunConfig(
        source_directory='./src',
        script='train.py',
        compute_target='cpu-cluster',
        arguments=[
            '--data_path', dataset.as_named_input('input').as_mount(),
            '--learning_rate', 0.003,
            '--momentum', 0.92],
    )
    # set up pytorch environment
    env = Environment.from_conda_specification(
        name='pytorch-env',
        file_path='./.azureml/pytorch-env.yml'
    )
    config.run_config.environment = env

    run = experiment.submit(config)
    aml_url = run.get_portal_url()
    print("Submitted to compute cluster. Click link below")
    print("")
    print(aml_url)
```

### <a name="understand-the-code-changes"></a>Entender as alterações de código

O script de controle é semelhante àquele da [parte 3 desta série](tutorial-1st-experiment-sdk-train.md), com as seguintes novas linhas:

:::row:::
   :::column span="":::
      `dataset = Dataset.File.from_files( ... )`
   :::column-end:::
   :::column span="2":::
      Um [conjunto de dados](/python/api/azureml-core/azureml.core.dataset.dataset) é usado para referenciar os dados carregados no Armazenamento de Blobs do Azure. Os conjuntos de dados são uma camada de abstração sobre seus dados projetada para melhorar a confiabilidade e a confiança.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig(...)`
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) é modificado para incluir uma lista de argumentos que serão passados para `train.py`. O argumento `dataset.as_named_input('input').as_mount()` significa que o diretório especificado será _montado_ no destino de computação.
   :::column-end:::
:::row-end:::

> [!div class="nextstepaction"]
> [Criei o script de controle](?success=control-script#submit-to-cloud) [Encontrei um problema](https://www.research.net/r/7C6W7BQ?issue=control-script)

## <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit-to-cloud"></a> Enviar a execução para o Azure Machine Learning

Agora, reenvie a execução para usar a nova configuração:

```bash
python 06-run-pytorch-data.py
```

Esse código imprimirá uma URL para o experimento no Azure Machine Learning Studio. Se você acessar esse link, poderá ver o código em execução.

> [!div class="nextstepaction"]
> [Reenviei a execução](?success=submit-to-cloud#inspect-log) [Encontrei um problema](https://www.research.net/r/7C6W7BQ?issue=submit-to-cloud)

### <a name="inspect-the-log-file"></a><a name="inspect-log"></a> Inspecionar o arquivo de log

No estúdio, acesse a execução do experimento (selecionando a saída da URL anterior) seguida de **Saídas + logs**. Selecione o arquivo `70_driver_log.txt`. A seguinte saída deve ser exibida:

```txt
Processing 'input'.
Processing dataset FileDataset
{
  "source": [
    "('workspaceblobstore', 'datasets/cifar10')"
  ],
  "definition": [
    "GetDatastoreFiles"
  ],
  "registration": {
    "id": "XXXXX",
    "name": null,
    "version": null,
    "workspace": "Workspace.create(name='XXXX', subscription_id='XXXX', resource_group='X')"
  }
}
Mounting input to /tmp/tmp9kituvp3.
Mounted input to /tmp/tmp9kituvp3 as folder.
Exit __enter__ of DatasetContextManager
Entering Run History Context Manager.
Current directory:  /mnt/batch/tasks/shared/LS_root/jobs/dsvm-aml/azureml/tutorial-session-3_1600171983_763c5381/mounts/workspaceblobstore/azureml/tutorial-session-3_1600171983_763c5381
Preparing to call script [ train.py ] with arguments: ['--data_path', '$input', '--learning_rate', '0.003', '--momentum', '0.92']
After variable expansion, calling script [ train.py ] with arguments: ['--data_path', '/tmp/tmp9kituvp3', '--learning_rate', '0.003', '--momentum', '0.92']

Script type = None
===== DATA =====
DATA PATH: /tmp/tmp9kituvp3
LIST FILES IN DATA PATH...
['cifar-10-batches-py', 'cifar-10-python.tar.gz']
```

Aviso:

- O Azure Machine Learning montou o Armazenamento de Blobs no cluster de cálculo automaticamente para você.
- O ``dataset.as_named_input('input').as_mount()`` usado no script de controle é resolvido para o ponto de montagem.

> [!div class="nextstepaction"]
> [Inspecionei o arquivo de log](?success=inspect-log#clean-up-resources) [Encontrei um problema](https://www.research.net/r/7C6W7BQ?issue=inspect-log)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Você também pode manter o grupo de recursos, mas excluir um único workspace. Exiba as propriedades do workspace e, em seguida, selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, vimos como carregar dados no Azure usando `Datastore`. O armazenamento de dados atuou como armazenamento em nuvem para seu workspace, fornecendo um local persistente e flexível para manter seus dados.

Você viu como modificar seu script de treinamento para aceitar um caminho de dados por meio da linha de comando. Usando `Dataset`, você conseguiu montar um diretório na execução remota. 

Agora que você tem um modelo, saiba:

* Como [implantar modelos com o Azure Machine Learning](how-to-deploy-and-where.md).
