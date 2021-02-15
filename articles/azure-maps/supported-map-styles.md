---
title: Estilos de mapa do Azure Maps internos com suporte
description: Saiba mais sobre os estilos de mapa internos que o Azure Maps dá suporte, como estrada, blank_accessible, satélite, satellite_road_labels, road_shaded_relief e noite.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: b3404c02dc159309ed1e9fcd6f9f6bb593fc7552
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896932"
---
# <a name="azure-maps-supported-built-in-map-styles"></a>Estilos de mapa internos com suporte do Azure Maps

O Azure Mapas dá suporte a vários estilos de mapa internos diferentes, como descrito abaixo.

## <a name="road"></a>rodoviário

Um mapa de **estrada** é um mapa padrão que exibe estradas. Ele também exibe recursos naturais e artificiais e os rótulos para esses recursos.

![estilo de mapa de estrada](./media/supported-map-styles/road.png)

**APIs aplicáveis:**

* [Imagem do mapa](/rest/api/maps/render/getmapimage)
* [Bloco de mapa](/rest/api/maps/render/getmaptile)
* Controle de mapa do SDK da Web
* Controle de mapa do Android
* Visual do Power BI

## <a name="blank-and-blank_accessible"></a>em branco e blank_accessible

Os estilos de mapa **em branco** e de **blank_accessible** fornecem uma tela em branco para visualização de dados. O estilo de **blank_accessible** continuará a fornecer atualizações de leitor de tela com detalhes de local do mapa, mesmo que o mapa base não seja exibido.

> [!Note]
> No SDK da Web, você pode alterar a cor do plano de fundo do mapa definindo o `background-color` estilo CSS do elemento do mapa div.

**APIs aplicáveis:**

* Controle de mapa do SDK da Web

## <a name="satellite"></a>satellite

O estilo **satélite** é uma combinação de imagens de satélite e aéreas.

![estilo de mapa de bloco satélite](./media/supported-map-styles/satellite.png)

**APIs aplicáveis:**

* [Peça de satélite](/rest/api/maps/render/getmapimagerytilepreview)
* Controle de mapa do SDK da Web
* Controle de mapa do Android
* Visual do Power BI

## <a name="satellite_road_labels"></a>satellite_road_labels

Este estilo de mapa é um híbrido de estradas e rótulos sobrepostos sobre imagens aéreas e de satélite.

![satellite_road_labels estilo de mapa](./media/supported-map-styles/satellite-road-labels.png)

**APIs aplicáveis:**

* Controle de mapa do SDK da Web
* Controle de mapa do Android
* Visual do Power BI

## <a name="grayscale_dark"></a>grayscale_dark

**Escala de cinza escuro** é uma versão escura do estilo de mapa rodoviário.

![gray_scale estilo de mapa](./media/supported-map-styles/grayscale-dark.png)

**APIs aplicáveis:**

* [Imagem do mapa](/rest/api/maps/render/getmapimage)
* [Bloco de mapa](/rest/api/maps/render/getmaptile)
* Controle de mapa do SDK da Web
* Controle de mapa do Android
* Visual do Power BI

## <a name="grayscale_light"></a>grayscale_light

a **luz de escala de cinza** é uma versão leve do estilo de mapa rodoviário.

![estilo de mapa da luz em tons de cinza](./media/supported-map-styles/grayscale-light.png)

**APIs aplicáveis:**
* Controle de mapa do SDK da Web
* Controle de mapa do Android
* Visual do Power BI

## <a name="night"></a>noite

**noite** é uma versão escura do estilo de mapa rodoviário com estradas e símbolos coloridos.

![estilo de mapa noturno](./media/supported-map-styles/night.png)

**APIs aplicáveis:**

* Controle de mapa do SDK da Web
* Controle de mapa do Android
* Visual do Power BI

## <a name="road_shaded_relief"></a>road_shaded_relief

**road shaded relief** é o estilo principal do Azure Mapas concluído com as delimitações da Terra.

![estilo de mapa de alívio sombreado](./media/supported-map-styles/shaded-relief.png)

**APIs aplicáveis:**

* [Bloco de mapa](/rest/api/maps/render/getmaptile)
* Controle de mapa do SDK da Web
* Controle de mapa do Android
* Visual do Power BI

## <a name="high_contrast_dark"></a>high_contrast_dark

**high_contrast_dark** é um estilo de mapa escuro com um contraste maior do que os outros estilos.

![estilo de mapa escuro de alto contraste](./media/supported-map-styles/high-contrast-dark.png)

**APIs aplicáveis:**

* Controle de mapa do SDK da Web
* Visual do Power BI

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como definir um estilo de mapa no Azure Maps:

[Escolher um estilo de mapa](./choose-map-style.md)