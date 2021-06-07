---
title: Gerenciar IoT Central do portal do Azure | Microsoft Docs
description: Este artigo descreve como criar e gerenciar seus aplicativos de IoT Central do portal do Azure.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: 2af97206db00d683ab409710bc71a3b5048bf6ae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104658458"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Gerenciar IoT Central do portal do Azure

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Em vez de criar e gerenciar aplicativos IoT Central no site [do Azure IOT central Application Manager](https://aka.ms/iotcentral) , você pode usar o [portal do Azure](https://portal.azure.com) para gerenciar seus aplicativos.

## <a name="create-iot-central-applications"></a>Criar aplicativos do IoT Central

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]


Para criar um aplicativo, navegue até a [portal do Azure](https://ms.portal.azure.com) e selecione **criar um recurso**.

Em **Pesquisar na barra do Marketplace** , digite *IOT central*:

![Portal de gerenciamento: pesquisar](media/howto-manage-iot-central-from-portal/image0a1.png)

Selecione o bloco **IOT central aplicativo** nos resultados da pesquisa:

![Portal de gerenciamento: resultados da pesquisa](media/howto-manage-iot-central-from-portal/image0b1.png)

Agora, selecione **criar**:

![Portal de gerenciamento: recurso do IoT Central](media/howto-manage-iot-central-from-portal/image0c1.png)

Preencha todos os campos no formulário. Esse formulário é semelhante ao formulário que você preenche para criar aplicativos no site [do Azure IOT central Application Manager](https://aka.ms/iotcentral) . Para obter mais informações, confira o guias de início rápido [Criar um aplicativo do IoT Central](quick-deploy-iot-central.md).

![Criar formulário de IoT Central](media/howto-manage-iot-central-from-portal/image6a.png)

**Localização** é a [geografia](https://azure.microsoft.com/global-infrastructure/geographies/) em que você deseja criar seu aplicativo. Normalmente, você deve escolher a localização fisicamente mais próxima de seus dispositivos para obter um desempenho ideal. No momento, o Azure IoT Central está disponível nas regiões da **Austrália**, **Pacífico Asiático**, **Europa**, **Estados Unidos**, **Reino Unido** e **Japão** . Depois de escolher uma localização, você não poderá mover o aplicativo posteriormente para outra localização.

Depois de preencher todos os campos, selecione **criar**.

## <a name="manage-existing-iot-central-applications"></a>Gerenciar aplicativos do IoT Central existentes

Se você já tiver um aplicativo do Azure IoT Central, poderá excluí-lo ou movê-lo para uma assinatura ou grupo de recursos diferente no portal do Azure.

> [!NOTE]
> Os aplicativos criados por meio do plano *gratuito* não exigem assinaturas do Azure e, portanto, você não os encontrará na sua assinatura do Azure no portal do Azure. Você só poderá ver e gerenciar aplicativos gratuitos no portal do IoT Central.

Para começar, selecione **todos os recursos** no Portal. Selecione **Mostrar tipos ocultos** e comece a digitar o nome do seu aplicativo em **Filtrar por nome** para localizá-lo. Em seguida, selecione o aplicativo IoT Central que você gostaria de gerenciar.

Para navegar até o aplicativo, selecione a **URL do aplicativo IOT central**:

![Captura de tela que mostra a página "visão geral" com a "URL do aplicativo IoT Central" realçada.](media/howto-manage-iot-central-from-portal/image3.png)

Para mover o aplicativo para um grupo de recursos diferente, selecione **alterar** ao lado do grupo de recursos. Na página **mover recursos** , escolha o grupo de recursos para o qual você deseja mover este aplicativo:

![Captura de tela que mostra a página "visão geral" com o "grupo de recursos (alterar)" realçado.](media/howto-manage-iot-central-from-portal/image4a.png)

Para mover o aplicativo para uma assinatura diferente, selecione  **alterar** ao lado da assinatura. Na página **mover recursos** , escolha a assinatura para a qual você gostaria de mover este aplicativo:

![Portal de gerenciamento: gerenciamento de recursos](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a gerenciar os aplicativos do Azure IoT Central no portal do Azure, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Administrar seu aplicativo](howto-administer.md)