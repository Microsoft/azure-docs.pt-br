---
title: Criar grupo de dispositivos na atualização do dispositivo para o Hub IoT do Azure | Microsoft Docs
description: Criar um grupo de dispositivos na atualização do dispositivo para o Hub IoT do Azure
author: vimeht
ms.author: vimeht
ms.date: 2/17/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: a0894047db1ed7687a1a0f5f87fc4020ddf7c694
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101678971"
---
# <a name="create-device-groups-in-device-update-for-iot-hub"></a>Criar grupos de dispositivos na atualização do dispositivo para o Hub IoT
A atualização de dispositivo para o Hub IoT permite implantar uma atualização em um grupo de dispositivos IoT.

## <a name="prerequisites"></a>Pré-requisitos

* [Acesso a um hub IOT com a atualização de dispositivo para o Hub IOT habilitado](create-device-update-account.md). É recomendável que você use uma camada S1 (padrão) ou superior para o Hub IoT. 
* Um dispositivo IoT (ou simulador) provisionado para atualização de dispositivo no Hub IoT.
* [Pelo menos uma atualização foi importada com êxito para o dispositivo provisionado.](import-update.md)

## <a name="add-a-tag-to-your-devices"></a>Adicionar uma marca a seus dispositivos  

A atualização de dispositivo para o Hub IoT permite implantar uma atualização em um grupo de dispositivos IoT. Para criar um grupo, a primeira etapa é adicionar uma marca ao conjunto de destino de dispositivos no Hub IoT. As marcas só podem ser adicionadas com êxito ao seu dispositivo depois que ele estiver conectado à atualização do dispositivo.

A documentação abaixo descreve como adicionar e atualizar uma marca.

### <a name="programmatically-update-device-twin"></a>Atualizar o dispositivo de atualização por meio de programação

Você pode atualizar o dispositivo de atualização de imagens com a marca apropriada usando Registrymanager após registrar o dispositivo com a atualização do dispositivo. 
[Saiba como adicionar marcas usando um aplicativo .NET de exemplo.](../iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)  
[Saiba mais sobre as propriedades de marca](../iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format).

#### <a name="device-update-tag-format"></a>Formato de marca de atualização do dispositivo

```markdown
     "tags": {
              "ADUGroup": "<CustomTagValue>"
             }
```

### <a name="using-jobs"></a>Usando trabalhos

É possível agendar um trabalho em vários dispositivos para adicionar ou atualizar uma marca de atualização de dispositivo seguindo [estes](../iot-hub/iot-hub-devguide-jobs.md) exemplos. [Saiba mais](../iot-hub/iot-hub-csharp-csharp-schedule-jobs.md).

  > [!NOTE] 
  > Essa ação vai para a cota de mensagens do Hub IOT atual e é recomendável alterar apenas até 50.000 marcas de dispositivo de uma vez, caso contrário, talvez seja necessário comprar mais unidades do Hub IoT se você exceder a cota de mensagens do Hub IoT diária. Os detalhes podem ser encontrados em [cotas e limitação](../iot-hub/iot-hub-devguide-quotas-throttling.md#quotas-and-throttling).

### <a name="direct-twin-updates"></a>Atualizações do Direct up

As marcas também podem ser adicionadas ou atualizadas diretamente no dispositivo.

1. Faça logon em [portal do Azure](https://portal.azure.com) e navegue até o Hub IOT.

2. Em "Dispositivos IoT" ou "IoT Edge" no painel de navegação esquerdo, encontre seu dispositivo IoT e navegue até Dispositivo gêmeo.

3. Em Dispositivo gêmeo, exclua todos os valores de marca existentes da Atualização de dispositivo definindo-os como null.

4. Adicione um novo valor de marca da Atualização de dispositivo, conforme mostrado abaixo. [Documento JSON de dispositivo de exemplo com marcas.](../iot-hub/iot-hub-devguide-device-twins.md#device-twins)

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

### <a name="limitations"></a>Limitações

* Você pode adicionar qualquer valor à sua marca, exceto para ' não Categorizado ', que é um valor reservado.
* O valor da marca não pode exceder 255 caracteres.
* O valor da marca pode conter caracteres alfanuméricos e os seguintes caracteres especiais ".", "-", "_", "~".
* Nomes de marcas e grupos diferenciam maiúsculas de minúsculas.
* Um dispositivo só pode ter uma marca com o nome ADUGroup, quaisquer adições subsequentes de uma marca com esse nome substituirão o valor existente para o nome da marca ADUGroup.
* Um dispositivo só pode pertencer a um grupo.

## <a name="create-a-device-group-by-selecting-an-existing-iot-hub-tag"></a>Criar um grupo de dispositivos selecionando uma marca de Hub IoT existente

1. Vá para o [Portal do Azure](https://portal.azure.com).

2. Selecione o Hub IoT que você conectou anteriormente à sua instância de atualização de dispositivo.

3. Selecione a opção Atualizações de dispositivo em Gerenciamento de dispositivo automático na barra de navegação à esquerda.

4. Selecione a guia Grupos na parte superior da página. Você poderá ver o número de dispositivos conectados à atualização de dispositivo que ainda não estão agrupados.
   :::image type="content" source="media/create-update-group/ungrouped-devices.png" alt-text="Captura de tela de dispositivos desagrupados." lightbox="media/create-update-group/ungrouped-devices.png":::

5. Clique no botão Adicionar para criar um novo grupo.
   :::image type="content" source="media/create-update-group/add-group.png" alt-text="Captura de tela da adição do grupo de dispositivos." lightbox="media/create-update-group/add-group.png":::

6. Selecione uma marca do Hub IoT na lista e, em seguida, selecione Criar grupo de atualização.
   :::image type="content" source="media/create-update-group/select-tag.png" alt-text="Captura de tela da seleção de marca." lightbox="media/create-update-group/select-tag.png":::

7. Depois que o grupo for criado, você verá que a lista de gráficos e grupos de conformidade de atualização será atualizada.  O gráfico de conformidade da atualização mostra a contagem de dispositivos em vários Estados de conformidade: na atualização mais recente, novas atualizações disponíveis, atualizações em andamento e dispositivos ainda não agrupados. [Saiba mais sobre a conformidade da atualização.](device-update-compliance.md) 
    :::image type="content" source="media/create-update-group/updated-view.png" alt-text="Captura de tela da exibição de conformidade da atualização." lightbox="media/create-update-group/updated-view.png":::

8. Você deve ver seu grupo recém-criado e todas as atualizações disponíveis para os dispositivos no novo grupo. Você pode implantar a atualização para o novo grupo por meio desse modo de exibição clicando no nome da atualização. Consulte a próxima etapa: implantar atualização para obter mais detalhes.

## <a name="view-device-details-for-the-group-you-created"></a>Exibir detalhes do dispositivo para o grupo que você criou

1. Navegue até o grupo recém-criado e clique no nome do grupo.

2. Uma lista de dispositivos que fazem parte do grupo será mostrada junto com suas propriedades de atualização de dispositivo. Nessa exibição, você também pode ver as informações de conformidade de atualização para todos os dispositivos que são membros do grupo. O gráfico de conformidade da atualização mostra a contagem de dispositivos em vários Estados de conformidade: na atualização mais recente, novas atualizações disponíveis e atualizações em andamento.
   :::image type="content" source="media/create-update-group/group-details.png" alt-text="Captura de tela da exibição de detalhes do grupo de dispositivos." lightbox="media/create-update-group/group-details.png":::

3. Você também pode clicar em cada dispositivo individual dentro de um grupo para ser redirecionado para a página de detalhes do dispositivo no Hub IoT.
   :::image type="content" source="media/create-update-group/device-details.png" alt-text="Captura de tela da exibição de detalhes do dispositivo." lightbox="media/create-update-group/device-details.png":::

## <a name="next-steps"></a>Próximas etapas 

[Implantar atualização](deploy-update.md)

[Saiba mais sobre grupos de dispositivos](device-update-groups.md)

[Saiba mais sobre a conformidade da atualização.](device-update-compliance.md)
