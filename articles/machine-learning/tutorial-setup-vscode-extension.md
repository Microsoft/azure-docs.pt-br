---
title: 'Tutorial: Configurar a extensão do Visual Studio Code (versão prévia)'
titleSuffix: Azure Machine Learning
description: Saiba como instalar e executar scripts de treinamento usando a extensão do Azure Machine Learning para Visual Studio Code.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 11/16/2020
ms.openlocfilehash: f485c12bf82dde798a77175a3fab4d76488150c3
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701194"
---
# <a name="set-up-azure-machine-learning-visual-studio-code-extension-preview"></a>Configurar a extensão do Azure Machine Learning para Visual Studio Code (versão prévia)

Saiba como instalar e executar scripts usando a extensão do Azure Machine Learning para Visual Studio Code.

Neste tutorial, você aprende as seguintes tarefas:

> [!div class="checklist"]
> * Instalar a extensão do Azure Machine Learning para Visual Studio Code
> * Entrar em sua conta do Azure por meio do Visual Studio Code
> * Usar a extensão do Azure Machine Learning para executar um script de exemplo

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure. Caso não tenha uma, inscreva-se para experimentar a [versão paga ou gratuita do Azure Machine Learning](https://aka.ms/AMLFree).
- Visual Studio Code. Se você não tiver, [instale-o](https://code.visualstudio.com/docs/setup/setup-overview).
- [Python 3](https://www.python.org/downloads/)

## <a name="install-the-extension"></a>Instalar a extensão

1. Abra o Visual Studio Code.
1. Selecione o ícone **Extensões** na **Barra de Atividade** para abrir a exibição Extensões.
1. Na exibição Extensões, pesquise "Azure Machine Learning".
1. Selecione **Instalar**.

    > [!div class="mx-imgBorder"]
    > ![Instalar a extensão VS Code do Azure Machine Learning](./media/tutorial-setup-vscode-extension/install-aml-vscode-extension.PNG)

> [!NOTE]
> Como alternativa, você pode instalar a extensão do Azure Machine Learning por meio do Visual Studio Marketplace [baixando o instalador diretamente](https://aka.ms/vscodetoolsforai). 

O restante das etapas deste tutorial foi testado com a **versão 0.6.8** da extensão.

## <a name="sign-in-to-your-azure-account"></a>Entrar em sua conta do Azure

Para provisionar recursos e executar cargas de trabalho no Azure, você precisa entrar com suas credenciais de conta do Azure. Para auxiliar no gerenciamento de conta, o Azure Machine Learning instala automaticamente a extensão Conta do Azure. Visite o site a seguir para [saber mais sobre a extensão Conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

1. Abra a paleta de comandos selecionando **Exibição > Paleta de Comandos** na barra de menus. 
1. Insira o comando "Azure: Entrar" na paleta de comandos para iniciar o processo de entrada.

## <a name="run-a-machine-learning-model-training-script-in-azure"></a>Executar um script de treinamento de modelo de machine learning no Azure

Agora que você entrou no Azure com suas credenciais de conta, use as etapas desta seção para saber como usar a extensão para treinar um modelo de machine learning.

1. Baixe e descompacte o [repositório do VS Code Tools for AI](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip) em qualquer lugar no computador.
1. Abra o diretório `mnist-vscode-docs-sample` no Visual Studio Code.
1. Selecione o ícone **Azure** na barra de atividade.
1. Selecione o ícone **Executar Experimento** na parte superior da Exibição do Azure Machine Learning.

    > [!div class="mx-imgBorder"]
    > ![Executar o experimento](./media/tutorial-setup-vscode-extension/run-experiment.PNG)

1. Quando a paleta de comandos for expandida, siga os avisos.

    > [!NOTE]
    > Se você já tiver recursos existentes do Azure Machine Learning provisionados, confira [Como executar experimentos no guia do VS Code](./how-to-manage-resources-vscode.md#run-experiment).

    1. Selecione sua assinatura do Azure.
    1. Na lista de ambientes, selecione **Arquivo de dependências do Conda**.
    1. Pressione **Enter** para procurar o arquivo de dependências do Conda. Esse arquivo contém as dependências necessárias para executar seu script. Nesse caso, o arquivo de dependências é o arquivo `env.yml` dentro do diretório `mnist-vscode-docs-sample`.
    1. Pressione **Enter** para procurar o arquivo de script de treinamento. Esse é o arquivo que contém o código para um modelo de machine learning que categoriza imagens de dígitos manuscritos. Nesse caso, o script para treinar o modelo é o arquivo `train.py` dentro do diretório `mnist-vscode-docs-sample`.

1. Neste ponto, um arquivo de configuração semelhante ao mostrado abaixo é exibido no editor de texto. A configuração contém as informações necessárias para executar o trabalho de treinamento, como o arquivo que contém o código para treinar o modelo e as dependências do Python especificadas na etapa anterior.

    ```json
    {
        "workspace": "WS06271500",
        "resourceGroup": "WS06271500-rg2",
        "location": "South Central US",
        "experiment": "WS06271500-exp1",
        "compute": {
            "name": "WS06271500-com1",
            "vmSize": "Standard_D1_v2, Cores: 1; RAM: 3.5GB;"
        },
        "runConfiguration": {
            "filename": "WS06271500-com1-rc1",
            "environment": {
                "name": "WS06271500-env1",
                "conda_dependencies": [
                    "python=3.6.2",
                    "tensorflow=1.15.0",
                    "pip"
                ],
                "pip_dependencies": [
                    "azureml-defaults"
                ],
                "environment_variables": {}
            }
        }
    }
    ```

1. Quando estiver satisfeito com a configuração, envie seu experimento abrindo a paleta de comandos e inserindo o seguinte comando:

    ```text
    Azure ML: Submit Experiment
    ```

    Isso envia o `train.py` e o arquivo de configuração para o Workspace do Azure Machine Learning. O trabalho de treinamento é então iniciado em um recurso de computação no Azure.

### <a name="track-the-progress-of-the-training-script"></a>Acompanhar o progresso do script de treinamento

A execução do seu script pode levar vários minutos. Para acompanhar o progresso dele:

1. Selecione o ícone **Azure** na barra de atividades.
1. Expanda o nó da assinatura.
1. Expanda o nó do seu experimento em execução no momento. Ele está localizado dentro do nó `{workspace}/Experiments/{experiment}` em que os valores do workspace e do experimento são os mesmos que as propriedades definidas no arquivo de configuração.
1. Todas as execuções do experimento são listadas, bem como o status delas. Para obter o status mais recente, clique no ícone de atualização na parte superior da exibição do Azure Machine Learning.

    > [!div class="mx-imgBorder"]
    > ![Acompanhar o progresso do experimento](./media/tutorial-setup-vscode-extension/track-experiment-progress.PNG)

### <a name="download-the-trained-model"></a>Baixar o modelo treinado

Quando a execução do experimento for concluída, a saída será um modelo treinado. Para baixar as saídas localmente:

1. Clique com o botão direito do mouse na execução mais recente e selecione **Baixar Saídas**.

    > [!div class="mx-imgBorder"]
    > ![Baixar Modelo Treinado](./media/tutorial-setup-vscode-extension/download-trained-model.PNG)

1. Selecione uma localização para salvar as saídas.
1. Uma pasta com o nome da sua execução é baixada localmente. Navegue até ela.
1. Os arquivos de modelo estão dentro do diretório `outputs/outputs/model`.

## <a name="next-steps"></a>Próximas etapas

* [Tutorial: Treinar e implantar um modelo do TensorFlow de classificação de imagem usando a extensão do Azure Machine Learning para Visual Studio Code](tutorial-train-deploy-image-classification-model-vscode.md).
