---
title: Implantar o aplicativo sem monitoração de kubernetes em Azure Stack GPU pro Edge por meio do módulo IoT Edge | Microsoft Docs
description: Descreve como implantar um aplicativo sem monitoração de estado do kubernetes em seu dispositivo de GPU pro Azure Stack Edge usando um módulo IoT Edge que é acessado por meio de um IP externo.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 4eda6eaf69fad6f8600651660ae4ac6223fe8f8c
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102438056"
---
# <a name="use-iot-edge-module-to-run-a-kubernetes-stateless-application-on-your-azure-stack-edge-pro-gpu-device"></a>Usar IoT Edge módulo para executar um aplicativo sem monitoração de estado kubernetes em seu dispositivo de GPU pro Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este artigo descreve como você pode usar um módulo IoT Edge para implantar um aplicativo sem estado em seu dispositivo Azure Stack Edge pro.

Para implantar o aplicativo sem estado, você executará as seguintes etapas:

- Verifique se os pré-requisitos foram concluídos antes de implantar um módulo IoT Edge.
- Adicione um módulo IoT Edge para acessar a rede de computação em seu Azure Stack Edge pro.
- Verifique se o módulo pode acessar a interface de rede habilitada.

Neste artigo de instruções, você usará um módulo do aplicativo WebServer para demonstrar o cenário.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você precisará de:

- Um dispositivo Azure Stack Edge pro. Certifique-se de que:

    - As configurações de rede de computação são configuradas no dispositivo.
    - O dispositivo é ativado de acordo com as etapas no [tutorial: ativar seu dispositivo](azure-stack-edge-gpu-deploy-activate.md).
- Você concluiu a etapa **Configurar computação** de acordo com o [tutorial: configurar a computação em seu dispositivo Azure Stack Edge pro](azure-stack-edge-gpu-deploy-configure-compute.md) em seu dispositivo. Seu dispositivo deve ter um recurso de Hub IoT associado, um dispositivo IoT e um dispositivo IoT Edge.


## <a name="add-webserver-app-module"></a>Adicionar módulo de aplicativo do webserver

Execute as etapas a seguir para adicionar um módulo de aplicativo do servidor WebServer em seu dispositivo Azure Stack Edge pro.

1. No recurso do Hub IoT associado ao seu dispositivo, acesse **gerenciamento automático de dispositivo > IOT Edge**.
1. Selecione e clique no dispositivo IoT Edge associado ao dispositivo Azure Stack Edge pro. 

    ![Selecionar dispositivo IoT Edge](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/select-iot-edge-device-1.png)  

1. Selecione **Definir módulos**. Em **definir módulos no dispositivo**, selecione **+ Adicionar** e, em seguida, selecione **IOT Edge módulo**.

    ![Selecionar IoT Edge módulo](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/select-iot-edge-module-1.png)

1. No **módulo adicionar IOT Edge**:

    1. Especifique um **nome** para o módulo de aplicativo do servidor da WebServer que você deseja implantar.
    2. Na guia **configurações do módulo** , forneça um URI de **imagem** para a imagem do módulo. Um módulo que corresponde ao nome e às marcas fornecidas é recuperado. Nesse caso, `nginx:stable` o receberá uma imagem Nginx estável (marcada como estável) do repositório público do [Docker](https://hub.docker.com/_/nginx/).

        ![Adicionar IoT Edge módulo](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/set-module-settings-1.png)    

    3. Na guia **Opções de criação do contêiner** , Cole o seguinte código de exemplo:  

        ```
        {
            "HostConfig": {
                "PortBindings": {
                    "80/tcp": [
                        {
                            "HostPort": "8080"
                        }
                    ]
                }
            }
        }
        ```

        Essa configuração permite que você acesse o módulo usando o IP de rede de computação sobre *http* na porta TCP 8080 (com a porta padrão do webserver sendo 80). Selecione **Adicionar**.

        ![Especificar informações de porta na folha IoT Edge módulo personalizado](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-module-status-1.png)

    4. Selecione **Examinar + criar**. Examine os detalhes do módulo e selecione **criar**.

## <a name="verify-module-access"></a>Verificar o acesso ao módulo

1. Verifique se o módulo foi implantado com êxito e se está em execução. Na guia **módulos** , o status de tempo de execução do módulo deve estar **em execução**.  

    ![Verificar se o status do módulo está em execução](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-module-status-1.png)

1. Para obter o ponto de extremidade externo do aplicativo WebServer, [acesse o painel do kubernetes](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#access-dashboard). 
1. No painel esquerdo do painel, filtre por namespace **iotedge** . Vá para **descoberta e balanceamento de carga > serviços**. Na lista de serviços listados, localize o ponto de extremidade externo para o módulo de aplicativo do servidor da WebServer. 

    ![Conectar-se ao aplicativo WebServer no ponto de extremidade externo](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/connect-external-endpoint-1.png)

1. Selecione o ponto de extremidade externo para abrir uma nova janela do navegador.

    Você deve ver que o aplicativo WebServer está em execução.

    ![Verificar conexão com o módulo pela porta especificada](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-webserver-app-1.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba como expor aplicativos com estado por meio de um módulo IoT Edge<!--insert link-->.
