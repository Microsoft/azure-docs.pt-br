---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 04/03/2020
ms.author: crtreasu
ms.openlocfilehash: 43c8c578587c65b6e0317caf77ff2d0604cb4fa3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "102044522"
---
### <a name="build-and-deploy-the-app"></a>Compilar e implantar o aplicativo

Abra o arquivo `.sln` gerado pelo Unity. Altere a configuração de compilação para o seguinte.

:::image type="content" source="./media/object-anchors-quickstarts-unity/aoa-unity-vs-build-config.png" alt-text="configuração de compilação":::

Em seguida, você precisará configurar o **Endereço IP do Computador Remoto** para implantar e depurar o aplicativo.

Clique com o botão direito do mouse no projeto do aplicativo e selecione **Propriedades**. Na página de propriedades, selecione **Propriedades de Configuração -> Depuração**. Altere o valor do **Nome da Máquina** para o endereço IP do seu dispositivo HoloLens e clique em **Aplicar**.

:::image type="content" source="./media/object-anchors-quickstarts-unity/aoa-unity-vs-remote-debug.png" alt-text="depuração remota":::

Feche a página de propriedades. Clique em **Computador Remoto**. O aplicativo deve começar a compilação e implantação em seu dispositivo remoto. Verifique se o dispositivo está ativo.
