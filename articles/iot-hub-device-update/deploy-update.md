---
title: Implantar uma atualização usando a atualização do dispositivo para o Hub IoT do Azure | Microsoft Docs
description: Implante uma atualização usando a atualização de dispositivo para o Hub IoT do Azure.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 0a11c8e8946229941c1fe60f7f2ce84d9fadb2ed
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101678970"
---
# <a name="deploy-an-update-using-device-update-for-iot-hub"></a>Implantar uma atualização usando a atualização de dispositivo para o Hub IoT

Saiba como implantar uma atualização em um dispositivo IoT usando a atualização de dispositivo para o Hub IoT.

## <a name="prerequisites"></a>Pré-requisitos

* [Acesso a um hub IOT com a atualização de dispositivo para o Hub IOT habilitado](create-device-update-account.md). É recomendável que você use uma camada S1 (padrão) ou superior para o Hub IoT. 
* [Pelo menos uma atualização foi importada com êxito para o dispositivo provisionado.](import-update.md) 
* Um dispositivo IoT (ou simulador) provisionado para atualização de dispositivo no Hub IoT.
* [Uma marca foi atribuída ao dispositivo IoT que você está tentando atualizar. O dispositivo faz parte de pelo menos um grupo de atualização.](create-update-group.md)
* Navegadores compatíveis:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

## <a name="deploy-an-update"></a>Implantar uma atualização

1. Ir para [portal do Azure](https://portal.azure.com)

2. Navegue até a folha de atualização de dispositivo do Hub IoT.

  :::image type="content" source="media/deploy-update/device-update-iot-hub.png" alt-text="Hub IoT" lightbox="media/deploy-update/device-update-iot-hub.png":::

3. Selecione a guia grupos na parte superior da página. [Saiba mais](device-update-groups.md) sobre grupos de dispositivos. 

  :::image type="content" source="media/deploy-update/updated-view.png" alt-text="Guia Grupos" lightbox="media/deploy-update/updated-view.png":::

4. Exiba a lista de gráficos e grupos de conformidade de atualização. Você deverá ver uma nova atualização disponível para seu grupo de dispositivos, com um link para a atualização em atualizações pendentes (talvez seja necessário atualizar uma vez). [Saiba mais sobre a conformidade da atualização.](device-update-compliance.md) 

5. Selecione a atualização disponível.

6. Confirme se o grupo correto está selecionado como o grupo de destino. Agende a implantação e, em seguida, selecione implantar atualização.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Selecionar atualização" lightbox="media/deploy-update/select-update.png":::

7. Exiba o gráfico de conformidade. Você deve ver que a atualização está em andamento. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Atualização em andamento" lightbox="media/deploy-update/update-in-progress.png":::

8. Depois que o dispositivo for atualizado com êxito, você deverá ver o gráfico de conformidade e a atualização dos detalhes da implantação para refletir o mesmo. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Atualização bem-sucedida" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Monitorar uma implantação de atualização

1. Selecione a guia implantações na parte superior da página.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Guia implantações" lightbox="media/deploy-update/deployments-tab.png":::

2. Selecione a implantação que você criou para exibir os detalhes da implantação.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Detalhes de implantação" lightbox="media/deploy-update/deployment-details.png":::

3. Selecione atualizar para exibir os detalhes de status mais recentes. Continue esse processo até que o status seja alterado para êxito.


## <a name="retry-an-update-deployment"></a>Repetir uma implantação de atualização

Se a implantação falhar por algum motivo, você poderá repetir a implantação para dispositivos com falha. 

1. Vá para a guia implantações e selecione a implantação que falhou. 

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Detalhes de implantação" lightbox="media/deploy-update/deployment-details.png":::

2. Clique no status do dispositivo "falha" no painel de informações Detalhado da implantação.

3. Clique em "repetir dispositivos com falha" e confirme a notificação de confirmação. 

## <a name="next-steps"></a>Próximas etapas

[Solução de problemas comuns](troubleshoot-device-update.md)
