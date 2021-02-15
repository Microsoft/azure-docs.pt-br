---
title: Conectar-se a um serviço do serviço de recurso da Web (WFS) | Mapas do Microsoft Azure
description: Saiba como se conectar a um serviço WFS e consulte o serviço WFS usando o SDK da Web do Azure Maps e o módulo de e/s espacial.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 75a7da41e4f0d5356ca135eb3cb4cbe5f29e8116
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92891441"
---
# <a name="connect-to-a-wfs-service"></a>Conectar-se a um serviço WFS

Um serviço de recurso da Web (WFS) é um serviço Web para consultar dados espaciais que têm uma API padronizada que é definida pelo Open Geospatial Consortium (OGC). A `WfsClient` classe no módulo de e/s espacial permite que os desenvolvedores se conectem a um serviço WFS e consultem dados do serviço.

Os recursos a seguir têm suporte na `WfsClient` classe:

- Versões com suporte: `1.0.0` , `1.1.0` e `2.0.0`
- Operadores de filtro com suporte: comparações binárias, lógica, matemática, valor e `bbox` .
- As solicitações são feitas usando `HTTP GET` apenas.
- Operações com suporte:

    | Operação | Descrição |
    | :-- | :-- |
    | GetCapabilities | Gera um documento de metadados com operações e parâmetros WFS válidos |
    | GetFeature | Retorna uma seleção de recursos de uma fonte de dados |
    | DescribeFeatureType | Retorna os tipos de recurso com suporte |

## <a name="using-the-wfs-client"></a>Usando o cliente WFS

A `atlas.io.ogc.WfsClient` classe no módulo de e/s espacial torna mais fácil consultar um serviço WFS e converter as respostas em objetos geojson. Esse objeto geojson pode ser usado para outras finalidades de mapeamento.

O código a seguir consulta um serviço WFS e renderiza os recursos retornados no mapa.

<br/>

<iframe height='700' scrolling='no' title='Exemplo de WFS simples' src='//codepen.io/azuremaps/embed/MWwvVYY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte o <a href='https://codepen.io/azuremaps/pen/MWwvVYY/'>exemplo de WFS simples</a> de caneta por mapas do Azure ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="supported-filters"></a>Filtros com suporte

A especificação para o padrão WFS usa filtros OGC. Os filtros a seguir têm suporte pelo cliente WFS, supondo que o serviço que está sendo chamado também ofereça suporte a esses filtros. Cadeias de caracteres de filtro personalizadas podem ser passadas para a `CustomFilter` classe.

**Operadores lógicos**

- `And`
- `Or`
- `Not`

**Operadores de valor**

- `GmlObjectId`
- `ResourceId`

**Operadores matemáticos**

- `Add`
- `Sub`
- `Mul`
- `Div`

**Operadores de comparação**

- `PropertyIsEqualTo`
- `PropertyIsNotEqualTo`
- `PropertyIsLessThan`
- `PropertyIsGreaterThan`
- `PropertyIsLessThanOrEqualTo`
- `PropertyIsGreaterThanOrEqualTo`
- `PropertyIsLike`
- `PropertyIsNull`
- `PropertyIsNil`
- `PropertyIsBetween`

O código a seguir demonstra o uso de diferentes filtros com o cliente WFS.

<br/>

<iframe height='500' scrolling='no' title= 'Exemplos de filtros WFS' src='//codepen.io/azuremaps/embed/NWqvYrV/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte os <a href='https://codepen.io/azuremaps/pen/NWqvYrV/'>exemplos de filtro WFS</a> de caneta por mapas do Azure ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="wfs-service-explorer"></a>Gerenciador de serviços do WFS

O código a seguir usa o cliente WFS para explorar os serviços do WFS. Selecione uma camada de tipo de propriedade dentro do serviço e veja a legenda associada.

<br/>

<iframe height='700' style='width: 100%;' scrolling='no' title= 'Gerenciador de serviços do WFS' src='//codepen.io/azuremaps/embed/bGdrvmG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte o <a href='https://codepen.io/azuremaps/pen/bGdrvmG/'>Gerenciador de serviços de WFS</a> de caneta pelo Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

Para acessar os serviços do WFS hospedados em pontos de extremidade não habilitados para CORS, um serviço proxy habilitado para CORS pode ser passado para a `proxyService` opção do cliente WFS, conforme mostrado abaixo. 

```JavaScript
//Create the WFS client to access the service and use the proxy service settings
client = new atlas.io.ogc.WfsClient({
    url: url,
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
});
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [WfsClient](/JavaScript/api/azure-maps-spatial-io/atlas.io.ogc.wfsclient)

> [!div class="nextstepaction"]
> [WfsServiceOptions](/JavaScript/api/azure-maps-spatial-io/atlas.wfsserviceoptions)

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Aproveitar as principais operações](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Detalhes do formato de dados com suporte](spatial-io-supported-data-format-details.md)