---
title: Como acessar um terminal de instância de computação em seu espaço de trabalho
titleSuffix: Azure Machine Learning
description: Use o terminal em uma instância de computação para operações git, para instalar pacotes e adicionar kernels.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 02/05/2021
ms.openlocfilehash: e42d6d53e4d06e5ec7a33ea4b8ca18233dcf1dd7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100101155"
---
# <a name="access-a-compute-instance-terminal-in-your-workspace"></a>Acessar um terminal de instância de computação em seu espaço de trabalho

Acesse o terminal de uma instância de computação em seu espaço de trabalho para:

* Use arquivos de git e arquivos de versão. Esses arquivos são armazenados no sistema de arquivos do espaço de trabalho, não restritos a uma única instância de computação.
* Instale pacotes na instância de computação.
* Crie kernels extras na instância de computação.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree) antes de começar.
* Um Workspace do Machine Learning. Confira [Criar um Workspace do Azure Machine Learning](how-to-manage-workspace.md).

## <a name="access-a-terminal"></a>Acessar um terminal

Para acessar o terminal:

1. Abra seu workspace no [Azure Machine Learning Studio](https://ml.azure.com).
1. No lado esquerdo, selecione **Notebooks**.
1. Selecione a imagem **aberta do terminal** .

    :::image type="content" source="media/how-to-use-terminal/open-terminal-window.png" alt-text="Abrir janela do terminal":::

1. Quando uma instância de computação estiver em execução, a janela do terminal dessa instância de computação será exibida.
1. Quando nenhuma instância de computação estiver em execução, use a seção **computação** à direita para iniciar ou criar uma instância de computação.
    :::image type="content" source="media/how-to-use-terminal/start-or-create-compute.png" alt-text="Iniciar ou criar uma instância de computação":::

Além das etapas acima, você também pode acessar o terminal de:

* RStudio: Selecione a guia **Terminal** no canto superior esquerdo.
* Jupyter Lab:  selecione o bloco **Terminal** no cabeçalho **Outro** na guia Iniciador.
* Jupyter:  Selecione **Novo > Terminal** no canto superior direito na guia Arquivos.
* SSH para o computador, se você tiver habilitado o acesso SSH quando a instância de computação foi criada.

## <a name="copy-and-paste-in-the-terminal"></a>Copiar e colar no terminal

> * Windows: `Ctrl-Insert` para copiar e usar `Ctrl-Shift-v` ou `Shift-Insert` para colar.
> * Mac OS: `Cmd-c` para copiar e `Cmd-v` para colar.
> * Talvez o FireFox/IE não dê suporte apropriado a permissões de área de transferência.

## <a name="use-files-from-git-and-version-files"></a><a name=git></a> Usar arquivos de git e arquivos de versão

Acesse todas as operações git do terminal. Todos os arquivos e pastas do Git serão armazenados no sistema de arquivos do workspace. Esse armazenamento permite que você use esses arquivos de qualquer instância de computação em seu espaço de trabalho.

> [!NOTE]
> Adicione seus arquivos e pastas em qualquer lugar na pasta **~/cloudfiles/code/Users** para que eles fiquem visíveis em todos os seus ambientes do Jupyter.

Saiba mais sobre [clonar repositórios Git em seu sistema de arquivos do workspace](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system).

## <a name="install-packages"></a>Instalar Pacotes

 Instale pacotes de uma janela de terminal. Instale os pacotes do Python no ambiente **python 3,6-AzureML** .  Instale os pacotes do R no ambiente do **R**.

Ou você pode instalar pacotes diretamente em Jupyter Notebook ou RStudio:

* RStudio Use a guia **Pacotes** no canto inferior direito ou a guia **Console** no canto superior esquerdo.  
* Python: Adicione o código de instalação e execute em uma célula Jupyter Notebook.

> [!NOTE]
> Para o gerenciamento de pacotes em um notebook, use as funções mágicas **% Pip** ou **% Conda** para instalar pacotes automaticamente no **kernel em execução no momento**, em vez de **! Pip** ou **! Conda** que se refere a todos os pacotes (incluindo pacotes fora do kernel em execução no momento)

## <a name="add-new-kernels"></a>Adicionar novos kernels

> [!WARNING]
>  Ao personalizar a instância de computação, certifique-se de não excluir o ambiente de **azureml_py36** Conda ou o kernel **Python 3,6-azureml** . Isso é necessário para a funcionalidade Jupyter/JupyterLab

Para adicionar um novo kernel Jupyter à instância de computação:

1. Use a janela do terminal para criar um ambiente.  Por exemplo, o código a seguir cria `newenv` :

    ```shell
    conda create --name newenv
    ```

1. Ative o ambiente.  Por exemplo, depois de criar `newenv`:

    ```shell
    conda activate newenv
    ```

1. Instalar o pacote Pip e ipykernel no novo ambiente e criar um kernel para esse Conda env

    ```shell
    conda install pip
    conda install ipykernel
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

Qualquer um dos [Kernels do Jupyter disponíveis](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels) pode ser instalado.

## <a name="manage-terminal-sessions"></a>Gerenciar sessões de terminal

 Selecione **Exibir sessões ativas** na barra de ferramentas do terminal para ver uma lista de todas as sessões de terminal ativas. Quando não houver nenhuma sessão ativa, essa guia será desabilitada.

Feche todas as sessões não utilizadas para preservar os recursos da instância de computação.