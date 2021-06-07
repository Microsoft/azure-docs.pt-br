---
title: Visão geral das Âncoras de Objeto do Azure
description: Saiba como as Âncoras de Objeto do Azure ajudam a detectar objetos no mundo físico.
author: craigktreasure
manager: vriveras
ms.author: crtreasu
ms.date: 03/02/2021
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: 90b718e34a12fe71eec4e972bbf8de42d8b52007
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063756"
---
# <a name="azure-object-anchors-overview"></a>Visão geral das Âncoras de Objeto do Azure

As Âncoras de Objeto do Azure permitem que um aplicativo detecte um objeto no mundo físico usando um modelo 3D e estime sua pose com 6DoF. A pose com 6DoF (6 graus de liberdade) é definida como uma rotação e uma translação entre um modelo 3D e a respectiva contraparte física, o objeto real.

As Âncoras de Objeto do Azure são compostas por um serviço para conversão de modelo e um SDK de cliente de runtime para o HoloLens. O serviço aceita um modelo de objeto 3D e gera como saída um modelo de Âncoras de Objeto do Azure. O modelo de Âncoras de Objeto do Azure é usado juntamente com o SDK de runtime a fim de permitir que um aplicativo HoloLens carregue um modelo de objeto e detecte e rastreie as instâncias desse modelo no mundo físico.

:::image type="content" source="./media/aoa-overview.jpg" alt-text="Âncoras de Objeto do Azure em ação":::

## <a name="examples"></a>Exemplos

Alguns casos de uso de exemplo habilitados pelas Âncoras de Objeto do Azure incluem:

- **Treinamento**. Crie experiências de treinamento de Realidade Misturada para seus funcionários sem a necessidade de inserir marcadores nem gastar tempo ajustando manualmente o alinhamento do holograma. Se quiser aprimorar suas experiências de treinamento de Realidade Misturada com detecção e rastreamento automatizados, faça a ingestão do modelo no serviço de Âncoras de Objeto do Azure e você estará um passo mais próximo de uma experiência sem marcador.

- **Diretrizes de tarefa**. Orientar os funcionários na realização de um conjunto de tarefas pode se tornar um esforço bem mais simples usando a Realidade Misturada. A sobreposição de instruções digitais e melhores práticas, aplicadas no objeto físico – seja a peça de uma máquina no chão de fábrica ou a cafeteira na cozinha da equipe – pode reduzir bastante a dificuldade para concluir um conjunto de tarefas. Para disparar essas experiências, normalmente é necessária alguma forma de marcador ou alinhamento manual. No entanto, com as Âncoras de Objeto do Azure, você pode criar uma experiência que detecta automaticamente o objeto relacionado à tarefa em questão. Então, prosseguir por meio de diretrizes de Realidade Misturada sem marcadores ou alinhamento manual.

- **Localização de ativos**. Se você já tiver um modelo 3D de algum objeto no espaço físico, as Âncoras de Objeto do Azure poderão permitir que você localize e rastreie instâncias desse objeto no ambiente físico.

## <a name="next-steps"></a>Próximas etapas

As seções a seguir fornecem informações sobre como começar a criar e usar aplicativos com as Âncoras de Objeto do Azure.

> [!div class="nextstepaction"]
> [Ingestão de modelo](quickstarts/get-started-model-conversion.md)

> [!div class="nextstepaction"]
> [Unity HoloLens](quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity HoloLens com MRTK](quickstarts/get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [HoloLens DirectX](quickstarts/get-started-hololens-directx.md)
