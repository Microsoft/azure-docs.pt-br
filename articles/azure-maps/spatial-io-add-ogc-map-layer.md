---
title: Adicionar uma camada de mapa de Open Geospatial Consortium (OGC) | Mapas do Microsoft Azure
description: Saiba como sobrepor uma camada do mapa do OGC no mapa e como usar as diferentes opções na classe OgcMapLayer.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 7d94a681fa987a4b23dbcda744c2f8516da4437a
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92891658"
---
# <a name="add-a-map-layer-from-the-open-geospatial-consortium-ogc"></a>Adicionar uma camada do mapa do Open Geospatial Consortium (OGC)

A `atlas.layer.OgcMapLayer` classe pode sobrepor imagens do WMS (serviços de mapa da Web) e imagens WMTS (serviços de blocos de mapa da Web) no mapa. O WMS é um protocolo padrão desenvolvido pelo OGC para servir imagens de mapa georeferenciadas pela Internet. A Image Georeference é o processo de associar uma imagem a uma localização geográfica. WMTS também é um protocolo padrão desenvolvido pelo OGC. Ele foi projetado para fornecer blocos de mapa previamente renderizados e georeferenciados.

As seções a seguir descrevem os recursos do serviço de mapa da Web que são suportados pela `OgcMapLayer` classe.

**Serviço de mapa da Web (WMS)**

- Versões com suporte: `1.0.0` , `1.1.0` , `1.1.1` e `1.3.0`
- O serviço deve dar suporte ao `EPSG:3857` sistema de projeção ou lidar com as reprojeções.
- O GetFeatureInfo exige que o serviço ofereça suporte `EPSG:4326` ou lide com as reprojeções. 
- Operações com suporte:

    | Operação | Descrição |
    | :-- | :-- |
    | GetCapabilities | Recupera metadados sobre o serviço com os recursos com suporte |
    | GetMap | Recupera uma imagem de mapa para uma região especificada |
    | GetFeatureInfo | Recupera `feature_info` , que contém dados subjacentes sobre o recurso |

**Serviço de bloco do mapa da Web (WMTS)**

- Versões com suporte: `1.0.0`
- Os blocos devem ser quadrados, de modo que `TileWidth == TileHeight` .
- CRS com suporte: `EPSG:3857` ou `GoogleMapsCompatible` 
- O identificador TileMatrix deve ser um valor inteiro que corresponda a um nível de zoom no mapa. Em um mapa do Azure, o nível de zoom é um valor entre `"0"` e `"22"` . Portanto, o tem `"0"` suporte, mas `"00"` não tem suporte.
- Operações com suporte:

    | Operação | Descrição |
    | :-- | :-- |
    | GetCapabilities | Recupera as operações e os recursos com suporte |
    | GetTile | Recupera imagens para um bloco específico |

## <a name="overlay-an-ogc-map-layer"></a>Sobrepor uma camada do mapa do OGC

O `url` pode ser a URL base para o serviço ou uma URL completa com a consulta para obter os recursos do serviço. Dependendo dos detalhes fornecidos, o cliente WFS pode tentar vários formatos de URL padrão para determinar como acessar inicialmente o serviço.

O código a seguir mostra como sobrepor uma camada do mapa do OGC no mapa.

<br/>

<iframe height='700' scrolling='no' title='Exemplo de camada do mapa OGC' src='//codepen.io/azuremaps/embed/xxGLZWB/?height=700&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte o <a href='https://codepen.io/azuremaps/pen/xxGLZWB/'>exemplo de camada do mapa de OGC</a> de caneta pelo Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-map-layer-options"></a>Opções de camada do mapa OGC

O exemplo abaixo demonstra as diferentes opções de camada do mapa OGC. Você pode clicar no botão de caneta de código no canto superior direito para editar a caneta de código.

<br/>

<iframe height='700' scrolling='no' title='Opções de camada do mapa OGC' src='//codepen.io/azuremaps/embed/abOyEVQ/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as <a href='https://codepen.io/azuremaps/pen/abOyEVQ/'>Opções da camada do mapa de OGC</a> de caneta pelo Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-web-map-service-explorer"></a>Gerenciador de serviços do mapa da Web do OGC

A ferramenta a seguir sobrepõe as imagens dos serviços do mapa da Web (WMS) e dos serviços de bloco do mapa da Web (WMTS) como camadas. Você pode selecionar quais camadas no serviço são renderizadas no mapa. Você também pode exibir as legendas associadas para essas camadas.

<br/>

<iframe height='750' style='width: 100%;' scrolling='no' title='Gerenciador de serviços do mapa da Web do OGC' src='//codepen.io/azuremaps/embed/YzXxYdX/?height=750&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Confira a caneta <a href='https://codepen.io/azuremaps/pen/YzXxYdX/'>OGC Web Map Service Explorer</a> pelo Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

Você também pode especificar as configurações de mapa para usar um serviço de proxy. O serviço de proxy permite que você carregue recursos hospedados em domínios que não têm o CORS habilitado.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [OgcMapLayer](/javascript/api/azure-maps-spatial-io/atlas.layer.ogcmaplayer)

> [!div class="nextstepaction"]
> [OgcMapLayerOptions](/javascript/api/azure-maps-spatial-io/atlas.ogcmaplayeroptions)

Consulte os artigos a seguir, que contêm exemplos de código que você pode adicionar a seus mapas:

> [!div class="nextstepaction"]
> [Conectar-se a um serviço WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Aproveitar as principais operações](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Detalhes do formato de dados com suporte](spatial-io-supported-data-format-details.md)