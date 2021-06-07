---
title: Sobre o SDK do sensor de Kinect do Azure
description: Visão geral do Software Development Kit do sensor do Azure Kinect (SDK), seus recursos e ferramentas.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: article
keywords: Azure, Kinect, RGB, IR, gravação, sensor, SDK, acesso, profundidade, vídeo, câmera, IMU, movimento, sensor, áudio, microfone, Matroska, SDK do sensor, download
ms.openlocfilehash: 17c1b33120eacb5d0c6d3c02e692d1488ef474e6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "85276357"
---
# <a name="about-azure-kinect-sensor-sdk"></a>Sobre o SDK do sensor de Kinect do Azure

Este artigo fornece uma visão geral do Software Development Kit do sensor do Azure Kinect (SDK), seus recursos e ferramentas.

## <a name="features"></a>Recursos

O SDK do sensor de Kinect do Azure fornece acesso de nível cruzado entre plataformas para fluxos de configuração de dispositivo do Azure Kinect e sensores de hardware, incluindo:

- Controle de acesso e modo de câmera de profundidade (um modo de IR passivo, além de modos de profundidade de campo de exibição largos e estreitos) 
- Acesso à câmera RGB e controle da câmera RGB (por exemplo, exposição e proporção de branco) 
- Acesso ao sensor de movimento (giroscópio e acelerômetro) 
- Profundidade Sincronizada – Streaming da câmera RGB com atraso configurável entre câmeras 
- Controle de sincronização de dispositivo externo com deslocamento de atraso configurável entre dispositivos 
- Acesso a metadados de quadro de câmera para resolução de imagem, carimbo de data/hora etc. 
- Acesso a dados de calibragem do dispositivo 

## <a name="tools"></a>Ferramentas

- Um [Azure Kinect Viewer](azure-kinect-viewer.md) para monitorar fluxos de dados de dispositivo e configurar modos diferentes.
- Um [gravador de Kinect do Azure](azure-kinect-recorder.md) e uma API de leitor de reprodução que usa o [formato de contêiner Matroska](record-file-format.md).
- Uma [ferramenta de atualização de firmware](azure-kinect-firmware-tool.md)do Azure Kinect DK.

## <a name="sensor-sdk"></a>SDK do sensor

- [Baixe o SDK do sensor](sensor-sdk-download.md).
- O SDK do sensor está disponível em [código-fonte aberto no GitHub](https://github.com/microsoft/Azure-Kinect-Sensor-SDK).
- Para obter mais informações sobre o uso, consulte [documentação da API do sensor SDK](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/index.html).

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre o SDK do sensor de Kinect do Azure, também pode:
>[!div class="nextstepaction"]
>[Baixar o código SDK do sensor](sensor-sdk-download.md)

>[!div class="nextstepaction"]
>[Localizar e abrir dispositivo](find-then-open-device.md)
