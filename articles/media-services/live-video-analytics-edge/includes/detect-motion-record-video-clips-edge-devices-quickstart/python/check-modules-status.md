---
ms.openlocfilehash: 3f92bae608284c8b619be34a0e08f15e831bf88e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101751137"
---
Na etapa [Gerar e implantar o manifesto de implantação do IoT Edge](../../../detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest), no Visual Studio Code, expanda o nó **lva-sample-device** em **HUB IOT DO AZURE** (na seção inferior esquerda). Você deverá ver os seguintes módulos implantados:

* O módulo da Análise Dinâmica de Vídeo, chamado `lvaEdge`
* Um módulo chamado `rtspsim`, que simula um servidor RTSP que atua como a origem de um feed de vídeo ao vivo

  ![Módulos](../../../media/quickstarts/lva-sample-device-node.png)

> [!NOTE]
> As etapas acima pressupõem que você esteja usando a máquina virtual criada pelo script de instalação. Se estiver usando um dispositivo de borda próprio, acesse o dispositivo de borda e execute os seguintes comandos com **direitos de administrador** para efetuar pull do arquivo de vídeo de exemplo usado neste guia de início rápido e armazená-lo:  

```
mkdir /home/lvaedgeuser/samples
mkdir /home/lvaedgeuser/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaedgeuser/samples/input/camera-300s.mkv  
chown -R lvalvaedgeuser:localusergroup /home/lvaedgeuser/samples/  
```
