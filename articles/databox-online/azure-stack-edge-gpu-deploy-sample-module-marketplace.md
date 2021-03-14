---
title: Implantar o módulo GPU em seu dispositivo Microsoft Azure Stack Edge pro do Azure Marketplace | Microsoft Docs
description: Descreve como implantar o módulo IoT habilitado para GPU em seu dispositivo Azure Stack Edge pro GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: e6a58a1f404228564e4e1ac6d77051a747debaa7
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102438142"
---
# <a name="deploy-a-gpu-enabled-iot-module-from-azure-marketplace-on-azure-stack-edge-pro-gpu-device"></a>Implantar um módulo IoT habilitado para GPU do Azure Marketplace no dispositivo Azure Stack Edge pro GPU

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Este artigo descreve como implantar uma GPU (unidade de processamento gráfico) habilitada IoT Edge módulo do Azure Marketplace em seu dispositivo Azure Stack Edge pro. 

Neste artigo, você aprenderá como:
  - Prepare Azure Stack o Edge pro para executar um módulo de GPU.
  - Baixe e implante o módulo IoT habilitado para GPU do Azure Marketplace.
  - Monitore a saída do módulo.

## <a name="about-sample-module"></a>Sobre o módulo de exemplo

O módulo exemplo de GPU neste artigo inclui o exemplo de código de parâmetro de comparação PyTorch e TensorFlow para CPU em relação à GPU.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você tem:

- Você tem acesso a um dispositivo de borda com Azure Stack de 1 nó habilitado por GPU. Esse dispositivo foi ativado com um recurso no Azure. 
- Você configurou a computação neste dispositivo. Siga as etapas em [tutorial: configurar a computação em seu dispositivo de Azure Stack Edge](azure-stack-edge-gpu-deploy-configure-compute.md).
- Os seguintes recursos de desenvolvimento em um cliente do Windows:
    - [Visual Studio Code](https://code.visualstudio.com/)  
    - [Extensão de Azure IOT Edge para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).   


## <a name="get-module-from-azure-marketplace"></a>Obter o módulo do Azure Marketplace

1. Procurar todos os [aplicativos no Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps).

    ![Procurar aplicativos no Azure Marketplace](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/browse-apps-marketplace-1.png)

2. Procure **introdução às GPUs**.

    ![Pesquisar módulo de exemplo de GPU](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/search-gpu-sample-module-1.png)

3. Selecione **Obter agora**.

    ![Obter módulo de exemplo](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/get-sample-module-1.png)

4. Selecione **continuar** para confirmar os termos de uso e a política de privacidade do provedor. 

    ![Obter módulo de exemplo 2](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/terms-of-use-1.png)

5. Escolha a assinatura que você usou para implantar seu dispositivo Azure Stack Edge pro.

    ![Selecionar uma assinatura](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/select-subscription-1.png)

6. Insira o nome do serviço do Hub IoT que você criou quando configurou o dispositivo Azure Stack Edge pro. Para localizar esse nome de serviço do Hub IoT, vá para o Azure Stack recurso de borda associado ao seu dispositivo no portal do Azure. 

    1. Nas opções de menu do painel esquerdo, acesse **serviços de borda > IOT Edge**. 

        ![Exibir configuração de computação](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/view-config-1.png)

    1. Vá para **Propriedades**. 

        1. Anote o serviço do Hub IoT que foi criado quando você configurou a computação em seu dispositivo Azure Stack Edge pro.
        2. Observe o nome do dispositivo IoT Edge que foi criado quando você configurou a computação. Você usará esse nome na etapa subsequente.

        ![Configuração de computação de borda](media/azure-stack-edge-gpu-deploy-sample-module/view-compute-config-1.png)

10. Selecione **Implantar em um dispositivo**.

11. Insira o nome do dispositivo IoT Edge ou selecione **Localizar dispositivo** para procurar entre os dispositivos registrados com o Hub.

    ![Localizar dispositivo](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/find-device-1.png)

12. Selecione **criar** para continuar o processo padrão de configuração de um manifesto de implantação, incluindo a adição de outros módulos, se desejado. Os detalhes para o novo módulo são predefinidos, como a imagem do URI, as opções de criação e as propriedades desejadas, mas podem ser alterados.

    ![Selecione criar](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/target-devices-iot-edge-module-1.png)


13. Verifique se o módulo está implantado em seu hub IoT no portal do Azure. Selecione seu dispositivo, selecione **definir módulos** e o módulo deve estar listado na seção **módulos de IOT Edge** .

    ![Selecione criar 2](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/running-module-iotres-1.png)

## <a name="monitor-the-module"></a>Monitorar o módulo  

1. Na paleta de comandos do VS Code, execute **Hub IoT do Azure: selecionar Hub IoT**.

2. Escolha a assinatura e o hub IoT que contêm o dispositivo do IoT Edge que você deseja configurar. Nesse caso, selecione a assinatura usada para implantar o Azure Stack dispositivo pro Edge e selecione o dispositivo IoT Edge criado para seu dispositivo do Azure Stack Edge pro. Isso ocorre quando você configura a computação por meio do portal do Azure nas etapas anteriores.

3. No VS Code Explorer, expanda a seção Hub IoT do Azure. Em **dispositivos**, você deve ver o dispositivo IOT Edge correspondente ao dispositivo do Azure Stack Edge pro. 

    1. Selecione esse dispositivo, clique com o botão direito do mouse e selecione **Iniciar Monitoramento de ponto de extremidade de evento interno**.
  
        ![Começar a monitorar](media/azure-stack-edge-gpu-deploy-sample-module/monitor-builtin-event-endpoint-1.png)  

    2. Vá para **dispositivos > módulos** e você deverá ver o **módulo de GPU** em execução.

    3. O terminal de VS Code também deve mostrar os eventos do Hub IoT como a saída de monitoramento para seu dispositivo do Azure Stack Edge pro.

        ![Saída de monitoramento](media/azure-stack-edge-gpu-deploy-sample-module/monitor-events-output-1.png) 

        Você pode ver que o tempo necessário para executar o mesmo conjunto de operações (5000 iterações de transformação de forma) pela GPU é muito menor do que para a CPU.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como [Configurar a GPU para usar um módulo](azure-stack-edge-j-series-configure-gpu-modules.md).
