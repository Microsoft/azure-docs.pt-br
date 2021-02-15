---
ms.openlocfilehash: 53052097fa6616f889b710c58488a9f7a616168d
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956218"
---
Na etapa [Gerar e implantar o manifesto de implantação do IoT Edge](../../../detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest), no Visual Studio Code, expanda o nó **lva-sample-device** em **HUB IOT DO AZURE** (na seção inferior esquerda). Você deverá ver os seguintes módulos implantados:

* O módulo da Análise Dinâmica de Vídeo, chamado `lvaEdge`
* Um módulo chamado `rtspsim`, que simula um servidor RTSP que atua como a origem de um feed de vídeo ao vivo

  ![Módulos](../../../media/quickstarts/lva-sample-device-node.png)

> [!NOTE]
> As etapas acima pressupõem que você esteja usando a máquina virtual criada pelo script de instalação. Se estiver usando um dispositivo de borda próprio, acesse o dispositivo de borda e execute os seguintes comandos com **direitos de administrador** para efetuar pull do arquivo de vídeo de exemplo usado neste guia de início rápido e armazená-lo:  

```
mkdir /home/lvaadmin/samples
mkdir /home/lvaadmin/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
chown -R lvaadmin /home/lvaadmin/samples/  
```
