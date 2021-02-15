---
title: Zona de análise espacial e posicionamento de linha
titleSuffix: Azure Cognitive Services
description: Saiba como configurar zonas e linhas com análise espacial
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: aahi
ms.openlocfilehash: 7e2a64c14d7d7a1d20b64b746969aca1e60ab218
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90933265"
---
# <a name="zone-and-line-placement-guide"></a>Guia de posicionamento de zona e linha

Este artigo fornece diretrizes sobre como definir zonas e linhas para operações de análise espacial para obter uma análise precisa de movimentos de pessoas em um espaço. Isso se aplica a todas as operações. 

Zonas e linhas são definidas usando o parâmetro SPACEANALYSIS_CONFIG JSON. Consulte o artigo [operações de análise espacial](spatial-analysis-operations.md) para obter mais informações.

## <a name="guidelines-for-drawing-zones"></a>Diretrizes para zonas de desenho

Lembre-se de que todos os espaços são diferentes; Você precisará atualizar a posição ou o tamanho dependendo de suas necessidades.

Se você quiser ver uma seção específica de sua exibição de câmera, crie a maior zona que você pode, cobrindo a área de piso específica em que você está interessado, mas não incluindo outras áreas nas quais você não está interessado. Isso aumenta a precisão dos dados coletados e impede falsos positivos de áreas que você não deseja controlar. Tenha cuidado ao colocar os cantos do seu polígono e verifique se eles não estão fora da área que você deseja acompanhar.  

### <a name="example-of-a-well-shaped-zone"></a>Exemplo de uma zona de bom formato

A zona deve ser grande o suficiente para acomodar três pessoas posicionadas em cada borda e focadas na área de interesse. A análise espacial identificará as pessoas cujos pés são colocados na zona, portanto, ao desenhar zonas na imagem 2D, imagine a zona como um carpete no chão.

![Zona de bom formato](./media/spatial-analysis/zone-good-example.png)

### <a name="examples-of-zones-that-arent-well-shaped"></a>Exemplos de zonas que não estão com bom formato

Os exemplos a seguir mostram zonas com formato ruim. Nesses exemplos, a área de interesse é o espaço na frente da tela de *tempo do jogo* .

**A zona não está no chão.**

![Zona com formato ruim](./media/spatial-analysis/zone-not-on-floor.png) 

**A zona é muito pequena.**

![a zona é muito pequena](./media/spatial-analysis/zone-too-small.png)

**A zona não captura totalmente a área em volta da tela.**

![a zona não captura totalmente a área ao contrário da extremidade final](./media/spatial-analysis/zone-bad-capture.png)

**A zona está muito próxima da borda da imagem da câmera e não captura a exibição correta.**

![a zona está muito próxima da borda da imagem da câmera e não captura a exibição correta](./media/spatial-analysis/zone-edge.png)

**A zona é parcialmente bloqueada pela prateleira, por isso as pessoas e o andar não estão totalmente visíveis.**

![a zona está parcialmente bloqueada, portanto as pessoas não estão totalmente visíveis](./media/spatial-analysis/zone-partially-blocked.png)

### <a name="example-of-a-well-shaped-line"></a>Exemplo de uma linha em formato

A linha deve ser longa o suficiente para acomodar toda a entrada. A análise espacial identificará as pessoas cujos pés atravessam a linha, portanto, ao desenhar linhas na imagem 2D imagine que você as desenhará como se estivesse no chão. 

Se possível, estenda a linha mais larga do que a entrada real. Se isso não resultar em cruzamentos extras (como na imagem abaixo, quando a linha estiver em uma parede), estenda-o.

![Linha com formato de caixa](./media/spatial-analysis/zone-line-good-example.png)

### <a name="examples-of-lines-that-arent-well-shaped"></a>Exemplos de linhas que não estão com bom formato

Os exemplos a seguir mostram linhas mal definidas.

**A linha não cobre toda a forma de entrada no chão.**

![A linha não cobre toda a forma de entrada no chão](./media/spatial-analysis/zone-line-bad-coverage.png)

**A linha é muito alta e não cobre todo o valor da porta.**

![A linha é muito alta e não cobre todo o valor da porta](./media/spatial-analysis/zone-line-too-high.png)

## <a name="next-steps"></a>Próximas etapas

* [Implantar um aplicativo Web de contagem de pessoas](spatial-analysis-web-app.md)
* [Configurar operações de análise espacial](./spatial-analysis-operations.md)
* [Registro em log e solução de problemas](spatial-analysis-logging.md)
* [Guia de posicionamento da câmera](spatial-analysis-camera-placement.md)
