---
ms.openlocfilehash: eaa44ae9d8bf4d723944da695d9a063937b7e020
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98061080"
---
Quando você configura os recursos do Azure, um vídeo curto do tráfego em uma rodovia é copiado para a VM Linux no Azure que você está usando como o dispositivo do IoT Edge. Este guia de início rápido usa o arquivo de vídeo para simular uma transmissão ao vivo.

Abra um aplicativo como o [VLC Media Player](https://www.videolan.org/vlc/). Selecione CTRL + N e cole um link para [o vídeo de exemplo de cruzamento de rodovias](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv) para iniciar a reprodução. Você vê uma gravação de muitos veículos que se movimentam no tráfego de uma rodovia.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4]

Neste início rápido, você usará a Análise Dinâmica de Vídeo no IoT Edge para detectar objetos, como veículos e pessoas. Você publicará os eventos de inferência associados no Hub do IoT Edge.
