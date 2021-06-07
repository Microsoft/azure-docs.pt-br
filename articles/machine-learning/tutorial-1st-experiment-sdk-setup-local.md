---
title: 'Tutorial: Introdução ao machine learning – Python'
titleSuffix: Azure Machine Learning
description: Neste tutorial, você começará a usar o SDK do Azure Machine Learning para Python em execução no seu ambiente de desenvolvimento pessoal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 02/11/2021
ms.custom: devx-track-python, contperf-fy21q3
adobe-target: true
ms.openlocfilehash: 81b9b5d53e1b61322d25d5dead85429474c1272c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105935542"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-your-development-environment-part-1-of-4"></a>Tutorial: Introdução ao Azure Machine Learning no seu ambiente de desenvolvimento (parte 1 de 4)

Nesta *série de tutoriais de quatro partes*, você aprenderá os conceitos básicos do Azure Machine Learning e concluirá as tarefas de machine learning do Python baseadas em trabalhos na plataforma de nuvem do Azure. 

Na Parte 1 desta série de tutoriais, você vai:

> [!div class="checklist"]
> * Instalar o SDK do Azure Machine Learning.
> * Configurar a estrutura de diretório para o código.
> * Criar um Workspace de Azure Machine Learning.
> * Configurar seu ambiente de desenvolvimento local.
> * Configurar um cluster de cálculo.

> [!NOTE]
> Esta série de tutoriais enfoca os conceitos de Azure Machine Learning necessários para enviar **trabalhos em lotes** – é aqui que o código é enviado para a nuvem para ser executado em segundo plano sem qualquer interação do usuário. Isso é útil para scripts ou códigos acabados que você deseja executar repetidamente ou para tarefas de machine learning com uso intenso de computação. Se você estiver mais interessado em um fluxo de trabalho exploratório, poderá usar o [Jupyter ou RStudio em uma instância de computação do Azure Machine Learning](tutorial-1st-experiment-sdk-setup.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente o [Azure Machine Learning](https://aka.ms/AMLFree).
- [Anaconda](https://www.anaconda.com/download/) ou [Miniconda](https://www.anaconda.com/download/) para gerenciar os ambientes virtuais Python e instalar pacotes.  
- Caso não esteja familiarizado com o uso do Conda, confira [Introdução ao Conda](https://conda.io/projects/conda/en/latest/user-guide/getting-started.html).

## <a name="install-the-azure-machine-learning-sdk"></a>Instalar o SDK do Azure Machine Learning

Neste tutorial, você usará o SDK do Python do Azure Machine Learning para Python. Para evitar problemas de dependência do Python, você criará um ambiente isolado. Esta série de tutoriais usa o Conda para criar esse ambiente. Se você preferir usar outras soluções, como `venv`, `virtualenv` ou Docker, use uma versão do Python >=3.5 e <3.9.

Verifique se você tem o Conda instalado no sistema:
    
```bash
conda --version
```
    
Se esse comando retornar um erro `conda not found`, [baixe e instale o Miniconda](https://docs.conda.io/en/latest/miniconda.html). 

Depois de instalar o Conda, use uma janela de prompt de terminal ou o Anaconda para criar um ambiente:

```bash
conda create -n tutorial python=3.8
```

Em seguida, instale o SDK do Azure Machine Learning no ambiente do Conda que você criou:

```bash
conda activate tutorial
pip install azureml-core
```
    
> [!NOTE]
> Leva aproximadamente 2 minutos para a instalação do SKD do Azure Machine Learning ser concluída.
>
> Se você receber um erro de tempo limite, tente usar `pip install --default-timeout=100 azureml-core`.


> [!div class="nextstepaction"]
> [Instalei o SDK](?success=install-sdk#dir) [Encontrei um problema](https://www.research.net/r/7C8Z3DN?issue=install-sdk)

## <a name="create-a-directory-structure-for-code"></a><a name="dir"></a>Criar uma estrutura de diretório para o código

Recomendamos que você configure a seguinte estrutura de diretório simples para este tutorial:

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/directory-structure-1.png" alt-text="estrutura de diretórios: nível superior do tutorial com o subdiretório .azureml":::


- `tutorial`: diretório de nível superior do projeto.
- `.azureml`: subdiretório oculto usado para armazenar arquivos de configuração do Azure Machine Learning.

Por exemplo, para criar isso em uma janela Bash:

```bash
mkdir tutorial
cd tutorial
mkdir .azureml
```

> [!TIP]
> Para criar ou exibir a estrutura em uma janela gráfica, primeiro habilite a capacidade de ver e criar pastas e arquivos ocultos:
>
> * Em uma janela do Finder do Mac, use **Comando + Shift + .** para alternar a exibição de arquivos/pastas ocultas.  
> * Em um Explorador de Arquivos do Windows 10, confira [como exibir arquivos e pastas ocultas](https://support.microsoft.com/en-us/windows/view-hidden-files-and-folders-in-windows-10-97fbc472-c603-9d90-91d0-1166d1d9f4b5). 
> * Na Interface Gráfica do Linux, use **CTRL + h** ou o menu **Exibir** e marque a caixa **Mostrar arquivos ocultos**.




> [!div class="nextstepaction"]
> [Criei um diretório](?success=create-dir#workspace) [Encontrei um problema](https://www.research.net/r/7C8Z3DN?issue=create-dir)

## <a name="create-an-azure-machine-learning-workspace"></a><a name="workspace"></a>Criar um workspace do Azure Machine Learning

Um workspace é um recurso de nível superior para Azure Machine Learning e é um local centralizado para:

- Gerenciar recursos como computação.
- Armazenar ativos como notebooks, ambientes, conjuntos de dados, pipelines, modelos e pontos de extremidade.
- Colaborar com outros membros da equipe.

No diretório de nível superior, `tutorial`, adicione um novo arquivo do Python chamado `01-create-workspace.py` usando o código a seguir. Adapte os parâmetros (nome, ID da assinatura, grupo de recursos e [localização](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)) conforme suas preferências.

Execute o código em uma sessão interativa ou como um arquivo do Python.

>[!NOTE]
> Quando estiver usando um ambiente de desenvolvimento local (por exemplo, seu computador), você precisará se autenticar no seu workspace usando um *código de dispositivo* na primeira vez que executar o código a seguir. Siga as instruções na tela.

```python
# tutorial/01-create-workspace.py
from azureml.core import Workspace

ws = Workspace.create(name='<my_workspace_name>', # provide a name for your workspace
                      subscription_id='<azure-subscription-id>', # provide your subscription ID
                      resource_group='<myresourcegroup>', # provide a resource group name
                      create_resource_group=True,
                      location='<NAME_OF_REGION>') # For example: 'westeurope' or 'eastus2' or 'westus2' or 'southeastasia'.

# write out the workspace details to a configuration file: .azureml/config.json
ws.write_config(path='.azureml')
```

Na janela que tem o ambiente do Conda *tutorial1* ativado, execute este código no diretório `tutorial`.

```bash
cd <path/to/tutorial>
python ./01-create-workspace.py
```

> [!TIP]
> Se a execução desse código fornecer um erro informando que você não tem acesso à assinatura, confira [Criar um workspace](how-to-manage-workspace.md?tab=python#create-multi-tenant) para obter informações sobre as opções de autenticação.


Depois de executar com êxito *01-create-workspace.py*, sua estrutura de pastas terá a seguinte aparência:

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/directory-structure-2.png" alt-text="O arquivo config.json é exibido no subdiretório .azureml após a execução de 01-create-workspace.py":::

O arquivo `.azureml/config.json` contém os metadados necessários para se conectar ao seu Workspace do Azure Machine Learning. Ou seja, ele contém a ID da assinatura, o grupo de recursos e o nome do workspace. 

> [!NOTE]
> O conteúdo de `config.json` não são segredos. Não há problemas em compartilhar esses detalhes.
>
> A autenticação ainda é necessária para interagir com seu workspace do Azure Machine Learning.

> [!div class="nextstepaction"]
> [Criei um workspace](?success=create-workspace#cluster) [Encontrei um problema](https://www.research.net/r/7C8Z3DN?issue=create-workspace)

## <a name="create-an-azure-machine-learning-compute-cluster"></a><a name="cluster"></a> Criar um cluster de cálculo do Azure Machine Learning

Crie um script Python no diretório de nível superior `tutorial` chamado `02-create-compute.py`. Preencha-o com o seguinte código para criar um cluster de cálculo do Azure Machine Learning que fará o dimensionamento automático entre zero e quatro nós:

```python
# tutorial/02-create-compute.py
from azureml.core import Workspace
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

ws = Workspace.from_config() # This automatically looks for a directory .azureml

# Choose a name for your CPU cluster
cpu_cluster_name = "cpu-cluster"

# Verify that the cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           idle_seconds_before_scaledown=2400,
                                                           min_nodes=0,
                                                           max_nodes=4)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)
```

Na janela que tem o ambiente do Conda *tutorial1* ativado, execute o arquivo do Python:

```bash
python ./02-create-compute.py
```


> [!NOTE]
> Quando o cluster for criado, ele não terá nenhum nó provisionado. O cluster *não* gera custos até que você envie um trabalho. Esse cluster será reduzido quando ficar ocioso por 2.400 segundos (40 minutos).

Sua estrutura de pastas agora terá a seguinte aparência:

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/directory-structure-3.png" alt-text="Adicionar 02-create-compute.py ao diretório do tutorial":::

> [!div class="nextstepaction"]
> [Criei um cluster de cálculo](?success=create-compute-cluster#next-steps) [Encontrei um problema](https://www.research.net/r/7C8Z3DN?issue=create-compute-cluster)

## <a name="view-in-the-studio"></a>Exibir no estúdio

Entre no [Estúdio do Azure Machine Learning](https://ml.azure.com) para ver o workspace e a instância de computação que você criou.

1. Selecione a **Assinatura** usada para criar o workspace.
1. Selecione o **Workspace do Machine Learning** criado, *tutorial-ws*.
1. Depois que o workspace for carregado, no lado esquerdo, selecione **Computação**.
1. Na parte superior, selecione a guia **Clusters de computação**.

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/compute-instance-in-studio.png" alt-text="Captura de tela: Veja a instância de computação em seu workspace.":::

Essa exibição mostra o cluster de computação provisionado, juntamente com o número de nós ociosos, nós ocupados e nós não provisionados.  Como você ainda não usou o cluster, todos os nós estão desprovisionados.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial de configuração, você:

- Criou um Workspace do Azure Machine Learning.
- Configurou seu ambiente de desenvolvimento local.
- Criou um cluster de computação do Azure Machine Learning.

Nas outras partes deste tutorial você aprenderá a:

* Parte 2. Executar o código na nuvem usando o SDK do Azure Machine Learning para Python.
* Parte 3. Gerenciar o ambiente do Python que você usa para treinamento de modelo.
* Parte 4. Carregar dados no Azure e consumi-los no treinamento.

Passe para o próximo tutorial para aprender a enviar um script para o cluster de Computação do Azure Machine Learning.

> [!div class="nextstepaction"]
> [Tutorial: Executar um aplicativo "Olá, Mundo!" Script Python no Azure](tutorial-1st-experiment-hello-world.md)
