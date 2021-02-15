---
title: Sistemas de coordenadas do Azure Kinect DK
description: Descrição dos sistemas de coordenadas do Azure Kinect DK associados aos sensores do Azure DK
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, sensor, SDK, câmera de profundidade, TOF, princípios, desempenho, invalidação
ms.openlocfilehash: 4bb1c3b79862b918870cff786042d9b4c66270d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85276326"
---
# <a name="azure-kinect-dk-coordinate-systems"></a>Sistemas de coordenadas do Azure Kinect DK

Neste artigo, descrevemos as convenções usadas para sistemas de coordenadas 2D e 3D.  Há sistemas de coordenadas separados associados ao dispositivo de cada sensor e às [funções de calibração](use-calibration-functions.md) permitidas para transformar pontos entre eles. As [funções de transformação transformam](use-image-transformation.md) imagens inteiras entre sistemas de coordenadas.  

## <a name="2d-coordinate-systems"></a>sistemas de coordenadas 2D

 As câmeras de profundidade e cor são associadas a um sistema de coordenadas 2D independente. Uma coordenada [x, y] é representada em unidades de pixels em que os intervalos *x* de 0 até largura-1 e *y* variam de 0 a altura-1. A largura e a altura dependem do modo escolhido no qual as câmeras de profundidade e cor são operadas. A coordenada de pixel `[0,0]` corresponde ao pixel superior esquerdo da imagem. As coordenadas de pixel podem ser fracionárias representando coordenadas de subpixel.

O sistema de coordenadas 2D é centralizado em 0, ou seja, a coordenada de subpixel `[0.0, 0.0]` representa o centro e `[0.5,0.5]` o canto inferior direito do pixel, conforme mostrado abaixo.

   ![sistema de coordenadas 2D](./media/concepts/concepts-coordinate-systems/coordinate-systems-sdk-2d-system.png)

## <a name="3d-coordinate-systems"></a>sistemas de coordenadas 3D

Cada câmera, o acelerômetro e o giroscópio estão associados a um sistema independente de espaço de coordenadas 3D.

Os pontos nos sistemas de coordenadas 3D são representados como métrica [X, Y, Z] – coordenar tercetos com unidades em milímetros.

### <a name="depth-and-color-camera"></a>Câmera de profundidade e cor

A origem `[0,0,0]` está localizada no ponto focal da câmera. O sistema de coordenadas é orientado de forma que os pontos positivos do eixo X sejam corretos, os pontos positivos do eixo Y e os pontos positivos do eixo Z sejam encaminhados.

A câmera de profundidade está inclinada 6 graus para baixo da câmera colorida, conforme mostrado abaixo. 

Há dois iluminadores usados pela câmera de profundidade. O iluminador usado em modos NFOV (campo de exibição estreito) é alinhado com o caso da câmera de profundidade, portanto, o iluminador não está inclinado. O iluminador usado em modos de WFOV (campo de exibição amplo) é inclinado 1,3 graus para baixo em relação à câmera de profundidade.

![convenções de coordenadas 3D](./media/concepts/concepts-coordinate-systems/coordinate-systems-camera-features.png)

### <a name="gyroscope-and-accelerometer"></a>Giroscópio e acelerômetro

A origem do giroscópio `[0,0,0]` é idêntica à origem da câmera de profundidade. A origem do acelerômetro coincide com seu local físico. Os sistemas de coordenadas acelerômetro e giroscópio são destros. O eixo X positivo do sistema de coordenadas volta para trás, os pontos positivos do eixo Y à esquerda e os pontos positivos do eixo Z, conforme mostrado abaixo.

![Sistema de coordenadas IMU](./media/concepts/concepts-coordinate-systems/coordinate-systems-gyroscope.png)

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre o SDK do sensor de Kinect do Azure](about-sensor-sdk.md)