---
title: Implantar em um IoT Edge para Linux no Windows – Azure
description: Este artigo fornece orientação sobre como implantar o em um IoT Edge para Linux no dispositivo Windows.
ms.topic: how-to
ms.date: 02/18/2021
ms.openlocfilehash: 9ec28c62ca804137ede3cd60d1980e55fbaa2807
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102618127"
---
# <a name="deploy-to-an-iot-edge-for-linux-on-windows-eflow-device"></a>Implantar em um dispositivo IoT Edge para Linux no Windows (EFLOW)

Neste artigo, você aprenderá a implantar a análise de vídeo ao vivo em um dispositivo de borda que tem [IOT Edge para Linux no Windows (EFLOW)](https://docs.microsoft.com/azure/iot-edge/iot-edge-for-linux-on-windows). Depois de concluir as etapas deste documento, você poderá executar um [grafo de mídia](media-graph-concept.md) que detecta o movimento em um vídeo e emite esses eventos para o Hub IOT na nuvem. Em seguida, você pode desativar o grafo de mídia para cenários avançados e trazer o poder da análise de vídeo ao vivo para seu dispositivo de IoT Edge baseado em Windows.

## <a name="prerequisites"></a>Pré-requisitos 

* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) se não tiver uma.

    > [!NOTE]
    > Você precisará ter uma assinatura do Azure com permissões para criar entidades de serviço (a **função de proprietário** fornece isso). Se você não tiver as permissões corretas, entre em contato com o administrador da conta para lhe conceder as permissões corretas.
* O [Visual Studio Code](https://code.visualstudio.com/) em seu computador de desenvolvimento. Verifique se você tem a [Extensão do Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Leia [o que é EFLOW](https://aka.ms/AzEFLOW-docs).

## <a name="deployment-steps"></a>Etapas de implantação.

A seguir, há uma descrição do fluxo geral do documento e de cinco etapas simples que você deve estar configurado para executar a análise de vídeo ao vivo em um dispositivo Windows que tenha EFLOW:

:::image type="content" source="./media/deploy-iot-edge-linux-on-windows/eflow.png" alt-text="Diagrama do IoT Edge para Linux no Windows (EFLOW)":::

1. [Instale o EFLOW](https://aka.ms/AzEFLOW-install) em seu dispositivo Windows. 

    1. Se você estiver usando seu computador Windows, na página inicial do [centro de administração do Windows](https://docs.microsoft.com/windows-server/manage/windows-admin-center/overview) , na lista de conexões, você verá uma conexão de host local que representa o PC em que você está executando o centro de administração do Windows. 
    1. Quaisquer servidores, computadores ou clusters adicionais que você gerenciar também aparecerão aqui.
    1. Você pode usar o centro de administração do Windows para instalar e gerenciar o Azure EFLOW em seu dispositivo local ou em dispositivos remotos gerenciados. Neste guia, a conexão de host local servia como o dispositivo de destino para a implantação do Azure IoT Edge para Linux no Windows. Portanto, você verá o localhost também listado como um dispositivo IoT Edge.

    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/windows-admin-center.png" alt-text="Etapas de implantações-centro de administração do Windows":::
1. Clique no dispositivo IoT Edge para se conectar a ele e você deverá ver uma visão geral e a guia Shell de comando. A guia Shell de comando é onde você pode emitir comandos para o dispositivo de borda.
 
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-iot-edge-manager.png" alt-text="Etapas de implantações-Gerenciador de Azure IoT Edge":::
1. Vá para o Shell de comando e digite o seguinte comando:
    
    ```bash
    bash -c "$(curl -sL https://aka.ms/lva-edge/prep_device)"
    ```

    O módulo análise de vídeo ao vivo é executado no dispositivo de borda com [contas de usuário locais](deploy-iot-edge-device.md#create-and-use-local-user-account-for-deployment)sem privilégios. Além disso, ele [precisa de certas pastas locais](deploy-iot-edge-device.md#granting-permissions-to-device-storage) para armazenar dados de configuração do aplicativo. Por fim, para este guia de instruções, estamos aproveitando um [simulador RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) que retransmite um feed de vídeo em tempo real para o módulo LVA para análise. Esse simulador usa como entrada arquivos de vídeo previamente gravados de um diretório de entrada. 
    
    O script Prep-Device usado acima automatiza essas tarefas para que você possa executar um comando e ter todas as pastas de entrada e configuração relevantes, arquivos de entrada de vídeo e contas de usuário com privilégios criados diretamente. Depois que o comando for concluído com êxito, você deverá ver as seguintes pastas criadas no seu dispositivo de borda. 
    
    * `/home/lvaedgeuser/samples`
    * `/home/lvaedgeuser/samples/input`
    * `/var/lib/azuremediaservices`
    * `/var/media`
    
    Observe os arquivos de vídeo (*. mkv) na pasta/home/lvaedgeuser/Samples/Input, que serve como arquivos de entrada a serem analisados. 
    
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-iot-edge-manager-analysis.png" alt-text="Analisa":::
1. Agora que você tem o dispositivo de borda configurado, registrado no Hub e executado com êxito com as estruturas de pasta corretas criadas, a próxima etapa é configurar os seguintes recursos adicionais do Azure e implantar o módulo LVA. 

    * Conta de armazenamento
    * Conta dos Serviços de Mídia do Azure

    Para isso, recomendamos que você use o [script de instalação de recursos de análise de vídeo ao vivo](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) para implantar os recursos necessários em sua assinatura do Azure. Para fazer isso, siga estas etapas:

    1. Abra o [Azure Cloud Shell](https://ms.portal.azure.com/#cloudshell/).

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-resources-cloud-shell.png" alt-text="Cloud Shell":::
    1. Se estiver usando Cloud Shell pela primeira vez, você receberá um prompt para selecionar uma assinatura a fim de criar uma conta de armazenamento e um compartilhamento de Arquivos do Microsoft Azure. Selecione **Criar armazenamento** para criar uma conta de armazenamento para suas informações de sessão do Cloud Shell. Essa conta de armazenamento é separada da conta que o script criará para usar com sua conta dos Serviços de Mídia do Azure.
    1. No menu suspenso no lado esquerdo da janela do Cloud Shell, selecione Bash como seu ambiente.

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/bash.png" alt-text="Ambiente de bash":::
    1. Execute o comando a seguir.

        ```bash
        bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
        ```
        
        Certifique-se de escolher **Y** quando for solicitado a escolher seu próprio dispositivo de borda como um dispositivo IOT Edge, pois você criou o dispositivo e o Hub IOT anteriormente. Você também será solicitado a fornecer o nome do seu hub IoT e a ID do dispositivo IoT Edge. Você pode obter ambos fazendo logon no portal do Azure e clicando em seu hub IoT e, em seguida, acessando a opção IoT Edge na folha do portal do Hub IoT.

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/iot-edge-devices.png" alt-text="Confira dispositivos IoT Edge":::

    Depois de concluído, você pode fazer logon novamente no Shell de comando do dispositivo IoT Edge e executar o comando a seguir.
    
    `sudo iotedge list`
    
    Você deve ver os quatro módulos a seguir implantados e em execução no seu dispositivo de borda. Observe que o script de criação de recursos implanta o módulo LVA junto com os módulos de IoT Edge (edgeAgent e edgeHub) e um módulo simulador RTSP para fornecer o feed de vídeo RTSP simulado.
    
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/running.png" alt-text="Ver Status":::
1. Com os módulos implantados e configurados, você está pronto para executar seu primeiro grafo de mídia LVA em EFLOW. Você pode executar um grafo de detecção de movimento simples como abaixo e visualizar os resultados executando as seguintes etapas:

    :::image type="content" source="./media/analyze-live-video/motion-detection.svg" alt-text="Grafo de detecção de movimento":::

    1. [Configure](get-started-detect-motion-emit-events-quickstart.md#configure-the-azure-iot-tools-extension) a extensão de ferramentas de IOT do Azure.
    
        > [!Note]
        > Use o seguinte caminho: `~/clouddrive/lva_byod/appsettings.json. - instead of ~/clouddrive/lva-sample/appsettings.json` .
    1. Defina a topologia, crie uma instância de um grafo e ative-a por meio dessas [chamadas de método diretas](get-started-detect-motion-emit-events-quickstart.md#use-direct-method-calls).
    1. [Observe os resultados](get-started-detect-motion-emit-events-quickstart.md#observe-results) no Hub.
    1. Invocar [métodos de limpeza](get-started-detect-motion-emit-events-quickstart.md#invoke-graphinstancedeactivate).
    1. Exclua seus recursos se não forem mais necessários.

        > [!IMPORTANT]
        > Os recursos não excluídos ainda podem estar ativos e incorrer em custos do Azure.
    
## <a name="next-steps"></a>Próximas etapas

* Experimente a detecção de movimento juntamente com a gravação de vídeos relevantes na nuvem. Siga as etapas do guia [detectar movimento, gravar clipes de vídeo no guia de início rápido dos serviços de mídia](detect-motion-record-video-clips-media-services-quickstart.md#review-the-sample-video) .
* Executar o [ia em vídeo ao vivo](use-your-model-quickstart.md#overview) (você pode ignorar a configuração de pré-requisito, pois ela já foi feita acima)
* Use nossa [extensão de vs Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.live-video-analytics-edge) para exibir gráficos de mídia adicionais.
* Use uma [câmera IP](https://en.wikipedia.org/wiki/IP_camera)  que ofereça suporte a RTSP em vez de usar o simulador RTSP. Veja as câmeras IP compatíveis com RTSP na página [Produtos em conformidade com ONVIF](https://www.onvif.org/conformant-products/). Procure dispositivos em conformidade com os perfis G, S ou T.

