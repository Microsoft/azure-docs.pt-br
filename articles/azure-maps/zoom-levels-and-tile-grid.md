---
title: Níveis de zoom e grade lado a lado nos Mapas do Azure | Microsoft Docs
description: Saiba mais sobre os níveis de zoom e grade lado a lado nos Mapas do Azure
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: e7dcdb960fbd9196aca8b667269a4c6e5a1fb8f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60795054"
---
# <a name="zoom-levels-and-tile-grid"></a>Níveis de zoom e grade lado a lado
Mapas do Azure usam o sistema de coordenadas de projeção do Spherical Mercator (EPSG: 3857).

O mundo é dividido em blocos quadrados. Mapas do Azure fornece os blocos de varredura e vetoriais para 23 níveis de zoom, numerados de 0 a 22. No nível de zoom 0, o mundo inteiro se ajusta em um único bloco:

![Bloco de mundo](./media/zoom-levels-and-tile-grid/world0.png)

Nível de zoom 1 usa quatro blocos para renderizar o mundo: um quadrado de 2 x 2

![Superior esquerda do bloco de mundo](media/zoom-levels-and-tile-grid/world1a.png)     ![Superior direita do bloco de mundo](media/zoom-levels-and-tile-grid/world1c.png) 

![Inferior esquerda do bloco de mundo](media/zoom-levels-and-tile-grid/world1b.png)     ![Inferior direita do bloco de mundo](media/zoom-levels-and-tile-grid/world1d.png) 

Cada nível de zoom adicionais divide em quatro blocos do anterior, criando uma grade de 2<sup>zoom</sup> x 2<sup>zoom</sup>. Nível de zoom 22 é uma grade 2<sup>22</sup> x 2<sup>22</sup>, ou peças 4,194,304 x 4,194,304 (17,592,186,044,416 peças no total).

Os controles de mapa interativo de mapas do Azure para a web e suporte do Android zoom níveis 25 os níveis de zoom, numerados de 0 a 24. Embora os dados de estrada só estarão disponíveis nos níveis de zoom em quando os blocos estão disponíveis.

A tabela a seguir fornece os valores da lista completa para os níveis de zoom:

|Nível de zoom|Medidores/pixel|Medidores/lado a lado|
|--- |--- |--- |
|0|156543|40075008|
|1|78271.5|20037504|
|2|39135.8|10018764.8|
|3|19567.9|5009382.4|
|4|9783.9|2504678.4|
|5|4892|1252352|
|6|2446|626176|
|7|1223|313088|
|8|611.5|156544|
|9|305.7|78259.2|
|10|152.9|39142.4|
|11|76.4|19558.4|
|12|38.2|9779.2|
|13|19.1|4889.6|
|14|9.6.|2457.6|
|15|4.8|1228.8|
|16|2.4|614.4|
|17|1.2|307.2|
|18|0.6|152.8|
|19|0.3|76.4|
|20|0.15|38.2|
|21|0.075|19.1|
|22|0.0375|9.55|
|23|0.01875|4.775|
|24|0.009375|2.3875|

Blocos são chamados por nível de zoom e as coordenadas x e y correspondentes à posição do bloco da grade para esse nível de zoom.

Ao determinar qual nível de zoom usar, lembre-se de que cada local está em uma posição fixa no seu bloco. Isso significa que o número de blocos necessário para exibir uma determinada extensão de território depende do posicionamento específico da grade de zoom no mundo. Por exemplo, se houver dois pontos de 900 metros de distância, ele *pode* levar apenas três blocos para exibir uma rota entre elas no nível de zoom 17. No entanto, se o ponto ocidental está à direita do seu bloco e o ponto oriental à esquerda do bloco, pode ter quatro blocos:

![Escala de demonstração de zoom](media/zoom-levels-and-tile-grid/zoomdemo_scaled.png) 

Uma vez determinado o nível de zoom, os valores x e y podem ser calculados. O bloco superior esquerdo em cada grade de zoom é x = 0, y = 0; no bloco do canto inferior direito está em x = 2<sup>zoom -1</sup>, y = 2<sup>zoom 1</sup>.

Aqui está a grade de zoom para o nível de zoom 1:

![Grade de zoom para o nível de zoom 1](media/zoom-levels-and-tile-grid/api_x_y.png)
