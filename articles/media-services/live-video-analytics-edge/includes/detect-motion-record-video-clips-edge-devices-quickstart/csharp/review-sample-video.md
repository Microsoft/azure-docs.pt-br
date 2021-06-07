---
ms.openlocfilehash: df6137e4e00ebd089ef7fb5efa163a513fb30728
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98061038"
---
Quando você configura os recursos do Azure para este início rápido, um vídeo curto de um estacionamento é copiado para a VM Linux no Azure usada como o dispositivo do IoT Edge. Esse arquivo de vídeo será usado para simular uma transmissão ao vivo para este tutorial.

Abra um aplicativo como o [VLC media player](https://www.videolan.org/vlc/), selecione Ctrl+N e cole [este link](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) para o vídeo de estacionamento começar a ser reproduzido. Na marca de aproximadamente 5 segundos, um carro branco percorre o estacionamento.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LUbN]

Conclua as etapas abaixo para usar a Análise Dinâmica de Vídeo no IoT Edge para detectar o movimento do carro e gravar um clipe de vídeo começando na marca de cerca de 5 segundos.
