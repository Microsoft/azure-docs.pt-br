---
title: Sobre o Azure Kinect DK
description: Visão geral das ferramentas do DK (kit do desenvolvedor) do Azure Kinect e serviços integrados.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.topic: overview
ms.date: 06/26/2019
keywords: azure, kinect, overview, dev kit, DK, device, depth, body tracking, speech, cognitive services, SDKs, SDK, firmware
ms.openlocfilehash: d188d37c0247aebb16f51b4404da81d469136468
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359646"
---
# <a name="about-azure-kinect-dk"></a>Sobre o Azure Kinect DK

 ![Azure Kinect DK](./media/index/device-image.jpg)

O Azure Kinect DK é um kit de desenvolvedor com sensores de IA avançados que fornecem modelos de fala e pesquisa visual computacional sofisticados.  O Kinect contém um sensor de profundidade, uma matriz de microfone espacial com uma câmera de vídeo e um sensor de orientação integrados em um dispositivo pequeno, com vários modos, opções e SDKs (Software Development Kits). Ele está disponível para compra na [loja online da Microsoft](https://www.microsoft.com/p/azure-kinect-dk/8pp5vxmd9nhq).

O ambiente de desenvolvimento do Azure Kinect DK é composto pelos seguintes SDKs:

- SDK do Sensor para acesso ao dispositivo e ao sensor de nível inferior.
- SDK de Acompanhamento do Corpo para acompanhamento de corpos em 3D.
- SDK dos Serviços Cognitivos de Fala para habilitar o acesso ao microfone e os serviços de fala baseados em nuvem do Azure.

Além disso, os serviços de Visão Cognitiva podem ser usados com a câmera RGB do dispositivo.

   ![Diagrama de SDKs do Azure Kinect](./media/quickstarts/sdk-diagram.jpg)

## <a name="azure-kinect-sensor-sdk"></a>SDK do Sensor do Azure Kinect

O SDK do Sensor do Azure Kinect fornece acesso ao sensor de nível inferior para sensores de hardware e a configuração de dispositivo do Azure Kinect DK.

Para saber mais sobre o SDK do Sensor do Azure Kinect, confira [Usar o SDK do Sensor](about-sensor-sdk.md).

### <a name="azure-kinect-sensor-sdk-features"></a>Recursos do SDK do Sensor do Azure Kinect

O SDK do Sensor tem os seguintes recursos que funcionam depois de instalados e executados no Azure Kinect DK:

- Controle de acesso e modo de câmera de profundidade (um modo de IR passivo, além de modos de profundidade de campo de exibição largos e estreitos) 
- Acesso à câmera RGB e controle da câmera RGB (por exemplo, exposição e proporção de branco) 
- Acesso ao sensor de movimento (giroscópio e acelerômetro) 
- Profundidade Sincronizada – Streaming da câmera RGB com atraso configurável entre câmeras 
- Controle de sincronização de dispositivo externo com deslocamento de atraso configurável entre dispositivos 
- Acesso a metadados de quadro de câmera para resolução de imagem, carimbo de data/hora etc. 
- Acesso a dados de calibragem do dispositivo 

### <a name="azure-kinect-sensor-sdk-tools"></a>Ferramentas do SDK do Sensor do Azure Kinect

As seguintes ferramentas estão disponíveis no SDK do Sensor:

- Uma ferramenta de visualizador para monitorar fluxos de dados do dispositivo e configurar modos diferentes.
- Uma ferramenta de gravação de sensor e uma API de leitor de reprodução que usa o formato de contêiner Matroska.
- Uma ferramenta de atualização de firmware do Azure Kinect DK.

## <a name="azure-kinect-body-tracking-sdk"></a>SDK de rastreamento de corpo do Azure Kinect

O SDK de Acompanhamento do Corpo inclui um runtime e uma biblioteca do Windows para rastrear corpos em 3D quando usado com o hardware do Azure Kinect DK.

### <a name="azure-kinect-body-tracking-features"></a>Recursos de acompanhamento do Corpo do Azure Kinect

Os seguintes recursos de acompanhamento do corpo estão disponíveis no SDK fornecido:

- Fornece segmentação do corpo.
- Contém um esqueleto anatomicamente correto para cada corpo parcial ou completo em FOV.
- Oferece uma identidade única para cada corpo.
- Pode rastrear os corpos ao longo do tempo.

### <a name="azure-kinect-body-tracking-tools"></a>Ferramentas de Acompanhamento do Corpo do Azure Kinect

- O Rastreador de Corpo tem uma ferramenta de visualizador para rastrear os corpos em 3D.

## <a name="speech-cognitive-services-sdk"></a>SDK dos Serviços Cognitivos de Fala

O SDK de Fala habilita serviços de fala conectados ao Azure.

### <a name="speech-services"></a>Serviços de Fala

- Conversão de fala em texto
- Tradução de fala
- Conversão de texto em fala

>[!NOTE]
>O Azure Kinect DK não tem alto-falantes.

Para obter mais detalhes e informações, acesse a [documentação do Serviço de Fala](../cognitive-services/speech-service/index.yml).

## <a name="vision-services"></a>Serviços de visão

Os [Serviços de Visão Cognitiva do Azure](https://azure.microsoft.com/services/cognitive-services/directory/vision/) a seguir fornecem serviços do Azure que podem identificar e analisar conteúdo dentro de imagens e vídeos.

- [Visual computacional](https://azure.microsoft.com/services/cognitive-services/computer-vision/)
- [Detecção Facial](https://azure.microsoft.com/services/cognitive-services/face/)
- [Video Indexer](https://azure.microsoft.com/services/media-services/video-indexer/)
- [Moderador de conteúdo](https://azure.microsoft.com/services/cognitive-services/content-moderator/)
- [Visão Personalizada](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)

Os serviços evoluem e se aprimoram constantemente, então lembre-se de verificar regularmente se há [Serviços cognitivos](https://azure.microsoft.com/services/cognitive-services/) novos ou adicionais para aprimorar seu aplicativo. Para ter um olhar inicial sobre novos serviços emergentes, confira os [Laboratórios dos Serviços Cognitivos](https://labs.cognitive.microsoft.com/).

## <a name="azure-kinect-hardware-requirements"></a>Requisitos de hardware do Azure Kinect

O Azure Kinect DK integra a tecnologia mais recente de sensor da Microsoft a um só acessório conectado por USB. Para obter mais informações, confira [Especificação de Hardware do Azure Kinect DK](hardware-specification.md).

## <a name="next-steps"></a>Próximas etapas

Agora você tem uma visão geral do Azure Kinect DK. A próxima etapa é mergulhar de cabeça e configurá-lo!

> [!div class="nextstepaction"]
>[Início Rápido: Configurar o Azure Kinect DK](set-up-azure-kinect-dk.md)