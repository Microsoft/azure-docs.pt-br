---
title: Comparação do Windows do Azure Kinect DK
description: Diferenças de hardware e software entre o Azure Kinect DK e o Kinect para Windows v2
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: article
keywords: Kinect, Windows, v2, Kinect do Azure, comparação, SDK, diferenças, hardware, software
ms.openlocfilehash: 0a8d399370f354524858bdd658ffd65c0494dd4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87031565"
---
# <a name="azure-kinect-and-kinect-windows-v2-comparison"></a>Comparação entre o Azure Kinect e o Kinect Windows v2

Os kits de desenvolvimento de hardware e software do Azure Kinect DK têm diferenças do Kinect para Windows v2. Qualquer Kinect existente para aplicativos do Windows v2 não funcionará diretamente com o Azure Kinect DK e exigirá a portabilidade para o novo SDK.  

## <a name="hardware"></a>Hardware

As diferenças de alto nível entre o kit de desenvolvimento do Azure Kinect e o Kinect para Windows v2 estão listadas na tabela a seguir.

| Recurso | Type | Azure Kinect DK | Kinect para Windows v2 |
| ------- | ---- | --------------- | --------------------- |
| **Áudio** | Detalhes  | matriz circular de 7-MIC | matriz de fase linear de 4-MIC |
| **Sensor de movimento** | Detalhes | acelerômetro de 3 eixos de giro de 3 eixos | acelerômetro de 3 eixos |
| **Câmera RGB**    | Detalhes | 3840 x 2160 PX @30 fps | 1920 x 1080 PX @30 fps |
| **Câmera de profundidade**  | Método   | Tempo de vôo | Tempo de vôo |
|                   | Resolução | 640 x 576 PX @30 fps | 512 x 424 PX @ 30 fps |
|                   |            | 512 x 512 PX @30 fps |                       |
|                   |            | 1024x1024 PX @15 fps |                       |
| **Conectividade** | Dados | USB 3.1 Gen 1 com tipo USB-C  | USB 3,1 Gen 1|
|  | Energia | PSU externo ou USB-C | PSU externo |
|  | Sincronização | RGB & profundidade interna, dispositivo externo para dispositivo| RGB & profundidade somente interna |
| **Apoio** | Dimensões | 103 x 39 x 126 mm | 249 x 66 x 67 mm |
|  | Em massa | 440 g | 970 g |
| | Montar | Um UNC 1/4-20. Quatro pontos de parafuso internos | Um UNC 1/4-20 |

Encontre detalhes adicionais no documento de [hardware do Azure Kinect DK](hardware-specification.md) .

## <a name="sensor-access"></a>Acesso ao sensor

A tabela a seguir fornece comparação de recursos de acesso de sensor de dispositivo de nível baixo.

| **Funcionalidade**| **Azure Kinect** | **Kinect para Windows** | **Observações** |
|---------|---------|------------|---------|
| **Profundidade** | ✔️ | ✔️ |    |   |
| **IR** | ✔️ | ✔️ |  |
| **Cor** | ✔️ | ✔️ | O formato de cor dá suporte a diferenças, o Azure Kinect DK dá suporte a esses controles de câmera: exposição, equilíbrio de branco, brilho, contraste, saturação, nitidez e controle de conquista |
| **Áudio** | ✔️ | ✔️ | O Azure Kinect DK mics é acessado por meio do SDK de fala ou da API nativa do Windows |
| **IMU** | ✔️ |  | O Azure Kinect DK tem um IMU completo de 6 eixos e Kinect para Windows fornece apenas um eixo |
| **Dados de calibragem** | ✔️ | ✔️ | Calibragem do modelo de câmera compatível com OpenCV |
| **Profundidade – sincronização interna RGB** | ✔️ | ✔️ |  |
| **Sincronização externa**| ✔️|  | O Azure Kinect DK permite atraso programável para sincronização externa |
| **Compartilhar acesso com vários clientes** | | ✔️ | O SDK do sensor de Kinect do Azure se baseia no WinUSB/libUSB para acessar o dispositivo e não tem um serviço implementado para habilitar o compartilhamento do acesso ao dispositivo com vários processos |
| **Ferramenta gravar registro/reprodução** | ✔️ | ✔️ | O Azure Kinect DK usa uma implementação baseada em contêiner Matroska de código aberto |

## <a name="features"></a>Recursos

O conjunto de recursos do SDK Kinect do Azure é diferente do Kinect para Windows v2, conforme detalhado abaixo:

| **Recurso Kinect v2** | **Tipo de dados Kinect v2** | **SDK/serviço do Azure Kinect** |
|--------|--------|------|
| Acesso a dados do sensor |DepthFrame| [SDK do sensor-recuperar imagens](retrieve-images.md) 
| |InfraredFrame | [SDK do sensor-recuperar imagens](retrieve-images.md) 
| | ColorFrame | [SDK do sensor-recuperar imagens](retrieve-images.md) | 
| | AudioBeamFrame |Sem suporte no momento 
| Acompanhamento de corpo | BodyFrame | SDK de acompanhamento de corpo |
| | BodyIndexFrame | SDK de acompanhamento de corpo  |
| Mapeamento de coordenadas|CoordinateMapper| [SDK do sensor-transformações de imagem](use-image-transformation.md) |
|Acompanhamento facial | FaceFrame | [Serviços cognitivas: face](https://azure.microsoft.com/services/cognitive-services/face/)       |
|    Reconhecimento de fala    |    N/D                      |    [Serviços cognitivas: fala](https://azure.microsoft.com/services/cognitive-services/directory/speech/)     |

## <a name="next-steps"></a>Próximas etapas

[Kinect para páginas de desenvolvedor do Windows](https://developer.microsoft.com/windows/kinect)
