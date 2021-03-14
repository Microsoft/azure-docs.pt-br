---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 03/02/2021
ms.author: crtreasu
ms.openlocfilehash: d8dfc3d4b7a8447250481b98c1adadc865a29da1
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102532559"
---
### <a name="upload-your-model"></a>Carregar seu modelo

Caso ainda não tenha um modelo de Âncoras de Objeto, siga as instruções em [Criar um modelo](/azure/object-anchors/quickstarts/get-started-model-conversion) para criar um. Depois, retorne aqui.

Com seu HoloLens conectado ao Portal de Dispositivos do Windows, siga estas etapas para carregar um modelo para o aplicativo usar:

1. No Portal de Dispositivos do Windows, vá para **Sistema > Explorador de Arquivos > LocalAppData**. Nesse local, seu aplicativo deve aparecer na lista de aplicativos.

    :::image type="content" source="./media/object-anchors-quickstarts-unity/portal-localappdata.png" alt-text="explorador de arquivos":::

2. Abra seu aplicativo e clique na pasta `LocalState`.

    :::image type="content" source="./media/object-anchors-quickstarts-unity/portal-localstate.png" alt-text="Abra a pasta LocalState":::

3. Carregue o arquivo de modelo na pasta `LocalState`.

    :::image type="content" source="./media/object-anchors-quickstarts/portal-upload-model.png" alt-text="carregar o modelo no portal":::

    Inicie o aplicativo a partir do HoloLens novamente. Agora você pode detectar objetos que correspondem ao modelo.
