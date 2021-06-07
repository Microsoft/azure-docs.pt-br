---
title: Executar Azure Stream Analytics em Azure Stack
description: Crie um trabalho de borda Azure Stream Analytics e implante-o no Hub de Azure Stack por meio do tempo de execução de IoT Edge.
ms.service: stream-analytics
author: an-emma
ms.author: raan
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18
ms.openlocfilehash: 315b2efa042721a19fa779794c4921f9ced1fc83
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589796"
---
# <a name="run-azure-stream-analytics-on-azure-stack"></a>Executar Azure Stream Analytics em Azure Stack 

Você pode executar Azure Stream Analytics no Hub Azure Stack como um módulo IoT Edge. Foram adicionadas configurações ao módulo IoT Edge, que permite interagir com o armazenamento de BLOBs, hubs de eventos e hubs IoT em execução em uma assinatura de Hub de Azure Stack, permitindo que URLs personalizadas sejam encontradas em cada trabalho de Hub de Azure Stack.

Com Stream Analytics em Azure Stack, você pode criar arquiteturas realmente híbridas para processamento de fluxo em sua própria nuvem privada e autônoma, que pode ser conectada ou desconectada com aplicativos nativos de nuvem usando serviços do Azure consistentes locais. 

Este artigo mostra como transmitir dados do Hub IoT ou Hub de eventos para outro hub de eventos ou armazenamento de BLOBs em uma assinatura de Hub de Azure Stack e processá-lo com Stream Analytics.

## <a name="set-up-environments"></a>Configurar ambientes

Azure Stream Analytics é um serviço híbrido no Hub Azure Stack. É um módulo IoT Edge que é configurado no Azure, mas pode ser executado no Hub Azure Stack.  

Se você for novo no Azure Stack Hub ou IoT Edge, siga as instruções abaixo para configurar ambientes.

### <a name="prepare-the-azure-stack-hub-environment"></a>Preparar o ambiente do hub de Azure Stack

Crie uma assinatura de Hub de Azure Stack. Para obter mais informações, consulte o [tutorial para criar uma assinatura de Hub de Azure Stack.](/azure-stack/user/azure-stack-subscribe-services/)

Se você quiser avaliar Azure Stack Hub em seu próprio servidor, poderá usar o Kit de Desenvolvimento do Azure Stack (ASDK).  Para obter mais informações sobre o ASDK, consulte a [visão geral do ASDK](/azure-stack/asdk/).

### <a name="install-the-iot-edge-runtime"></a>Instalar o runtime do Azure IoT Edge

Para executar Azure Stream Analytics no Hub Azure Stack, o dispositivo deve ter o tempo de execução do IoT Edge e deve ter conectividade de rede com o Hub Azure Stack ou ser uma VM em execução no Hub Azure Stack. O tempo de execução de IoT Edge permite que Stream Analytics trabalhos do Edge se integrem ao armazenamento do Azure e aos hubs de eventos do Azure que estão em execução no Hub de Azure Stack. Para obter mais informações, consulte [Azure Stream Analytics no IOT Edge](stream-analytics-edge.md) 

Além de ter acesso à rede para os recursos do Hub Azure Stack, o dispositivo IoT Edge (ou VM) precisa acessar o Hub IoT do Azure na nuvem pública do Azure para configurar o módulo Stream Analytics. 

Os guias a seguir mostram como configurar o tempo de execução de IoT Edge em seu dispositivo ou VM:

* [Instalar o runtime do Azure IoT Edge no Windows](../iot-edge/how-to-install-iot-edge.md)
* [Instalar o runtime do Azure IoT Edge em sistemas Linux baseados em Debian](../iot-edge/how-to-install-iot-edge.md)


## <a name="create-an-azure-stream-analytics-edge-job"></a>Criar um trabalho de borda Azure Stream Analytics

Trabalhos ASA Edge são executados em contêineres implantados nos dispositivos do Azure IoT Edge. Eles são compostos de duas partes:
* Uma parte de nuvem que é responsável pela definição de trabalho: os usuários definem entradas, saída, consulta e outras configurações (eventos fora de ordem etc.) na nuvem.
* Um módulo em execução em seus dispositivos IoT. Ele contém o mecanismo do ASA e recebe a definição de trabalho da nuvem.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Quando você cria um trabalho do Azure Stream Analytics para execução em um dispositivo IoT Edge, ele precisa ser armazenado de uma forma que possa ser chamado a partir do dispositivo. Você pode usar uma conta de armazenamento do Azure existente ou criar uma nova.
1. Na portal do Azure, vá para **criar um recurso > armazenamento > conta de armazenamento-BLOB, arquivo, tabela, fila**.
2. Forneça os seguintes valores para criar sua conta de armazenamento:

   | Campo | Valor |
   | --- | --- |
   | Nome | Forneça um nome exclusivo para sua conta de armazenamento. |
   | Location | Escolha um local perto de você.|
   | Subscription | Escolha a mesma assinatura que o Hub IoT.|
   | Grupo de recursos | Recomendamos que você use o mesmo grupo de recursos para todos os recursos de teste criados durante os [guias de início rápido](../iot-edge/quickstart.md) e tutoriais do IOT Edge. Por exemplo, **IoTEdgeResources**. |

3. Use os valores padrão para os outros campos e selecione **Criar**.


### <a name="create-a-new-job"></a>Criar um novo trabalho

1. Na portal do Azure, vá para **criar um recurso > Internet das Coisas > trabalho de Stream Analytics**.
2. Forneça os seguintes valores para criar sua conta de armazenamento:

   | Campo | Valor |
   | --- | --- |
   | Nome do Trabalho | Forneça um nome para o seu trabalho. Por exemplo, **IoTEdgeJob** |
   | Subscription | Escolha a mesma assinatura que o Hub IoT.|
   | Grupo de recursos | Recomendamos que você use o mesmo grupo de recursos para todos os recursos de teste criados durante os [guias de início rápido](../iot-edge/quickstart.md) e tutoriais do IOT Edge. Por exemplo, **IoTEdgeResources**. |
   | Location | Escolha um local perto de você. |
   | Ambiente de hospedagem | Selecione **Edge**. |

3. Selecione **Criar**.

### <a name="configure-your-job"></a>Configurar seu trabalho

Depois que o trabalho do Stream Analytics for criado no portal do Azure, você pode configurá-lo com uma entrada, uma saída e uma consulta para execução nos dados que passam por ele. Você pode especificar manualmente as entradas de um hub IoT ou um hub de eventos em uma assinatura de Hub de Azure Stack.

1. Navegue até o seu trabalho do Stream Analytics no portal do Azure.
2. Em **Configurar**, selecione **configurações da conta de armazenamento** e escolha a conta de armazenamento que você criou na etapa anterior.
   > [!div class="mx-imgBorder"]
   > [Configuração de conta de armazenamento de ![ ](media/on-azure-stack/storage-account-settings.png) trabalho ](media/on-azure-stack/storage-account-settings.png#lightbox)
3. Em **topologia do trabalho**, selecione **entradas** e, em seguida, **Adicionar entrada de fluxo.**
4. Escolha **Hub IOT**, **Hub de eventos** ou **Hub do Edge** na lista suspensa. 
5. Se a entrada for um hub de eventos ou Hub IoT em uma assinatura de Hub de Azure Stack, forneça informações manualmente, conforme mostrado abaixo.

   #### <a name="event-hub"></a>Hub de evento

   | Campo | Valor |
   | --- | --- |
   | Alias de entrada | Um nome amigável que você usa na consulta do trabalho para fazer referência a essa entrada. |
   | Namespace do Barramento de Serviço | O namespace é um contêiner para um conjunto de entidades de mensagens. Ao criar um novo hub de eventos, você também cria o namespace. (Exemplo: *SB:// <Event Hub Name> . eventhub.Shanghai.azurestack.Corp.Microsoft.com*) |
   | Nome do Hub de Eventos | O nome do Hub de Eventos para usar como entrada. |
   | Nome da política do Hub de Eventos | A política de acesso compartilhado que fornece acesso ao Hub de Eventos. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. Essa opção é preenchida automaticamente, a menos que você selecione a opção de fornecer as configurações do Hub de Eventos manualmente. |
   | Chave de política do Hub de Eventos | A chave de acesso compartilhado usada para autorizar o acesso ao Hub de eventos. Essa opção é preenchida automaticamente, a menos que você selecione a opção de fornecer as configurações do Hub de Eventos manualmente. Você pode encontrá-lo nas configurações do hub de eventos. |
   | Grupo de consumidores do hub de eventos (opcional) | É altamente recomendável usar um grupo de consumidores distinto para cada trabalho do Stream Analytics. Esta cadeia de caracteres identifica o grupo de consumidores a ser usado para ingerir dados do hub de eventos. Se nenhum grupo de consumidores for especificado, o trabalho do Stream Analytics usará o grupo de consumidores $Default. |
   | Contagem de partições | Contagem de partições é o número de partições em um hub de eventos. |

   > [!div class="mx-imgBorder"]
   > [Entrada do hub de ![ ](media/on-azure-stack/event-hub-input.png) eventos ](media/on-azure-stack/event-hub-input.png#lightbox)

   #### <a name="iot-hub"></a>Hub IoT

   | Campo | Valor |
   | --- | --- |
   | Alias de entrada | Um nome amigável que você usa na consulta do trabalho para fazer referência a essa entrada. |
   | Hub IoT | O nome do Hub IoT para usar como entrada. (Exemplo:*<IoT Hub Name> . Shanghai.azurestack.Corp.Microsoft.com*) |
   | Nome da política de acesso compartilhado | A política de acesso compartilhado que fornece acesso ao Hub IoT. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. |
   | Chave da política de acesso compartilhado | A chave de acesso compartilhado usada para autorizar o acesso ao Hub IoT. Essa opção é preenchida automaticamente, a menos que você selecione a opção de fornecer as configurações do Hub IoT manualmente. |
   | Grupo de consumidores (opcional) | É altamente recomendável usar um grupo de consumidores distinto para cada trabalho do Stream Analytics. O grupo de consumidores é usado para ingerir dados do Hub IoT. O Stream Analytics usa o grupo de consumidores $Default, a menos que você especifique o contrário. |
   | Contagem de partições | Contagem de partições é o número de partições em um hub de eventos. |

   > [!div class="mx-imgBorder"]
   > [![Entrada ](media/on-azure-stack/iot-hub-input.png) do Hub IOT](media/on-azure-stack/iot-hub-input.png#lightbox)

6. Mantenha os valores padrão para os outros campos e selecione Salvar.
7. Em Topologia do Trabalho, abra Saídas e, em seguida, selecione Adicionar.
8. Escolha armazenamento de BLOBs, Hub de eventos ou Hub do Edge na lista suspensa.
9. Se a saída for um hub de eventos ou um armazenamento de BLOBs em uma assinatura de Hub de Azure Stack, forneça informações manualmente, conforme mostrado abaixo.

   #### <a name="event-hub"></a>Hub de evento

   | Campo | Valor |
   | --- | --- |
   | Alias de saída | Um nome amigável utilizado em consultas para direcionar a saída da consulta para esse hub de eventos. |
   | Namespace do Barramento de Serviço | Um contêiner para um conjunto de entidades de mensagens. Ao criar um novo hub de eventos, você também criou um namespace do barramento de serviço. (Exemplo: *SB:// <Event Hub Name> . eventhub.Shanghai.azurestack.Corp.Microsoft.com*) |
   | Nome do Hub de Eventos | O nome da sua saída de hub de eventos. |
   | Nome da política do Hub de Eventos | A política de acesso compartilhado, que você pode criar na guia Configurar do hub de eventos. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. |
   | Chave de política do Hub de Eventos | A chave de acesso compartilhado que é usada para autenticar o acesso ao namespace do hub de eventos. |

   > [!div class="mx-imgBorder"]
   > [Saída do hub de ![ ](media/on-azure-stack/event-hub-output.png) eventos ](media/on-azure-stack/event-hub-output.png#lightbox)

   #### <a name="blob-storage"></a>Armazenamento de Blobs 

   | Campo | Valor |
   | --- | --- |
   | Alias de saída | Um nome amigável utilizado em consultas para direcionar a saída da consulta para esse armazenamento de blob. |
   | Conta de armazenamento | O nome da conta de armazenamento em que você está enviando a saída. (Exemplo: *<Storage Account Name> . blob.Shanghai.azurestack.Corp.Microsoft.com*) |
   | Chave da conta de armazenamento | A chave secreta associada à conta de armazenamento. Essa opção é preenchida automaticamente, a menos que você selecione a opção de fornecer as configurações do Armazenamento de Blobs manualmente. |

> [!NOTE]
> Não há suporte para o formato parquet para trabalhos de borda no Hub Azure Stack. Para linhas mínimas e o tempo máximo, use 0 ou deixe-as em branco.


## <a name="deploy-stream-analytics-on-a-vm-or-device-connected-to-azure-stack"></a>Implantar Stream Analytics em uma VM ou dispositivo conectado a Azure Stack

1. No portal do Azure, abra o Hub IoT. Navegue até **IOT Edge** e clique no dispositivo (VM) que você deseja direcionar para essa implantação.
2. Selecione **Definir módulos**. Em seguida, selecione **+ Adicionar** e escolha **Azure Stream Analytics módulo**. 
3. Selecione a assinatura e o trabalho de borda da análise de fluxo que você criou. Clique em **salvar** e selecione **Avançar: rotas**.

   > [!div class="mx-imgBorder"]
   > [![Adicionar módulos ](media/on-azure-stack/edge-modules.png)](media/on-azure-stack/edge-modules.png#lightbox)

4. Clique em **revisar + criar >**.
5. Na etapa **revisar + criar** , selecione **criar**. 
   > [!div class="mx-imgBorder"]
   > [![Manifesto ](media/on-azure-stack/module-content.png) do](media/on-azure-stack/module-content.png#lightbox)
6. Confirme se o módulo foi adicionado à lista.
   > [!div class="mx-imgBorder"]
   > [![Página ](media/on-azure-stack/edge-deployment.png) de implantação](media/on-azure-stack/edge-deployment.png#lightbox)

## <a name="next-steps"></a>Próximas etapas
- [O Stream Analytics do Azure no IoT Edge](./stream-analytics-edge.md)
- [Desenvolver Stream Analytics trabalhos do Edge](/stream-analytics-query/stream-analytics-query-language-reference)
