---
title: Posicionamento de zona e de linha na Análise Espacial
titleSuffix: Azure Cognitive Services
description: Saiba como configurar zonas e linhas com a Análise Espacial
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: aahi
ms.openlocfilehash: cfd3bc406407298c6daf7723cb684911d7c9a9cf
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284661"
---
# <a name="zone-and-line-placement-guide"></a>Guia de posicionamento de zona e de linha

Este artigo fornece diretrizes sobre como definir zonas e linhas para operações de Análise Espacial para obter uma análise precisa dos movimentos das pessoas em um espaço. Aplica-se a todas as operações. 

Zonas e linhas são definidas usando o parâmetro JSON SPACEANALYSIS_CONFIG. Confira o artigo [Operações de Análise Espacial](spatial-analysis-operations.md) para obter mais informações.

## <a name="guidelines-for-drawing-zones"></a>Diretrizes para desenhar zonas

Lembre-se de que todo espaço é diferente. Você precisará atualizar a posição ou o tamanho dependendo de suas necessidades.

Se quiser ver uma seção específica da visão da sua câmera, crie a maior zona possível, cobrindo a área do solo específica que deseja, mas sem incluir outras áreas que não são de seu interesse. Isso aumenta a precisão dos dados coletados e impede falsos positivos de áreas que você não deseja acompanhar. Tenha cuidado ao colocar os cantos do polígono e verifique se eles não estão fora da área que você deseja acompanhar.  

### <a name="example-of-a-well-shaped-zone"></a>Exemplo de uma zona bem delineada

A zona deve ser grande o suficiente para acomodar três pessoas posicionadas em cada borda e focada na área de interesse. A Análise Espacial identificará as pessoas cujos pés estão posicionados na zona, portanto, ao desenhar zonas na imagem 2D, imagine a zona como um tapete no chão.

![Zona bem delineada](./media/spatial-analysis/zone-good-example.png)

### <a name="examples-of-zones-that-arent-well-shaped"></a>Exemplos de zonas que não estão bem delineadas

Os exemplos a seguir mostram zonas com formato ruim. Nesses exemplos, a área de interesse é o espaço em frente à placa com o texto *It's Game Time*.

**A zona não está no chão.**

![Zona com formato ruim](./media/spatial-analysis/zone-not-on-floor.png) 

**A zona é muito pequena.**

![a zona é muito pequena](./media/spatial-analysis/zone-too-small.png)

**A zona não captura totalmente a área em volta da placa.**

![a zona não captura totalmente a área em volta do arremate de extremidade](./media/spatial-analysis/zone-bad-capture.png)

**A zona está muito próxima da borda da imagem da câmera e não captura a exibição correta.**

![a zona está muito próxima da borda da imagem da câmera e não captura a exibição correta](./media/spatial-analysis/zone-edge.png)

**A zona está parcialmente bloqueada pela prateleira, de modo que as pessoas e o solo não estão totalmente visíveis.**

![a zona está parcialmente bloqueada, de modo que as pessoas não estão totalmente visíveis](./media/spatial-analysis/zone-partially-blocked.png)

### <a name="example-of-a-well-shaped-line"></a>Exemplo de uma linha bem delineada

A linha deve ser longa o suficiente para acomodar toda a entrada. A Análise Espacial identificará pessoas cujos pés cruzarem a linha. Portanto, ao desenhar linhas na imagem 2D, imagine que você as desenhará como se estivesse no chão. 

Se possível, estenda a linha além da entrada real. Se isso não gerar cruzamentos extra (como na imagem abaixo, quando a linha está em uma parede), estenda a linha.

![Linha bem delineada](./media/spatial-analysis/zone-line-good-example.png)

### <a name="examples-of-lines-that-arent-well-shaped"></a>Exemplos de linhas que não estão bem delineadas

Os exemplos a seguir mostram linhas mal definidas.

**A linha não cobre toda a entrada no solo.**

![A linha não cobre toda a entrada no solo](./media/spatial-analysis/zone-line-bad-coverage.png)

**A linha está muito alta e não cobre toda a porta.**

![A linha está muito alta e não cobre toda a porta](./media/spatial-analysis/zone-line-too-high.png)

## <a name="next-steps"></a>Próximas etapas

* [Implantar um aplicativo Web de Contagem de Pessoas](spatial-analysis-web-app.md)
* [Configurar operações de análise espacial](./spatial-analysis-operations.md)
* [Registro em log e solução de problemas](spatial-analysis-logging.md)
* [Guia de posicionamento da câmera](spatial-analysis-camera-placement.md)
