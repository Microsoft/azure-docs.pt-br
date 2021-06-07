---
title: Detalhes de formato de dados com suporte | Mapas do Microsoft Azure
description: Saiba como os dados espaciais delimitados são analisados no módulo de e/s espacial.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 7227813f607ca18ee50f503a30b290414f333e21
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91310162"
---
# <a name="supported-data-format-details"></a>Detalhes do formato de dados com suporte

Este artigo fornece informações específicas sobre o suporte de leitura e gravação para todas as marcas XML e Well-Known tipos de geometria de texto. Ele também detalha como os dados espaciais delimitados são analisados no módulo de e/s espacial.

## <a name="supported-xml-namespaces"></a>Namespaces XML com suporte

O módulo de e/s espacial dá suporte a marcas XML dos namespaces a seguir.

| Prefixo do namespace | URI de namespace   | Observações                                                                    |
|:------------------|:-----------------|:----------------------------------------|
| `atom`           | `http://www.w3.org/2005/Atom`   |                                         |
| `geo`            | `http://www.w3.org/2003/01/geo/wgs84_pos#`  | Suporte somente leitura em arquivos GeoRSS.           |
| `georss`         | `http://www.georss.org/georss`  |                                                |
| `geourl`         | `http://geourl.org/rss/module/` | Suporte somente leitura em arquivos GeoRSS.                       |
| `gml`            | `http://www.opengis.net/gml`    |                                                        |
| `gpx`            | `http://www.topografix.com/GPX/1/1` |                                                   |
| `gpxx`           | `http://www.garmin.com/xmlschemas/GpxExtensions/v3` | Suporte somente leitura em arquivos GPX. Analisa e usa DisplayColor. Todas as outras propriedades adicionadas aos metadados de forma. |
| `gpx_style`      | `http://www.topografix.com/GPX/gpx_style/0/2`      | Com suporte em arquivos GPX. Usa a cor da linha. |
| `gx`             | `http://www.google.com/kml/ext/2.2` |                                                      |
| `kml`            | `http://www.opengis.net/kml/2.2`    |                                                      |
| `rss`            |                                 | Somente leitura. GeoRSS grava usando o formato Atom.              |

## <a name="supported-xml-elements"></a>Elementos XML com suporte

O módulo de e/s espacial dá suporte aos seguintes elementos XML. Todas as marcas XML que não têm suporte serão convertidas em um objeto JSON. Em seguida, cada marca será adicionada como uma propriedade no `properties` campo da forma ou camada pai.

### <a name="kml-elements"></a>Elementos KML

O módulo de e/s espacial dá suporte aos seguintes elementos KML.

| Nome do elemento         | Ler    | Gravar   | Observações                                                                                                                      |
|----------------------|---------|---------|----------------------------------------------------------------------------------------------------------------------------|
| `address`            | partial | sim     | O objeto é analisado, mas não é usado para posicionar a forma.                                                                    |
| `AddressDetails`     | partial | não      | O objeto é analisado, mas não é usado para posicionar a forma.                                                                    |
| `atom:author`        | sim     | sim     |                                                                                                                            |
| `atom:link`          | sim     | sim     |                                                                                                                            |
| `atom:name`          | sim     | sim     |                                                                                                                            |
| `BalloonStyle`       | partial | partial | Não há suporte para `displayMode`. Convertido em um `PopupTemplate` . Para gravar, adicione uma `popupTemplate` propriedade como uma propriedade do recurso para a qual você deseja escrevê-la. |
| `begin`              | sim     | sim     |                                                                                                                            |
| `color`              | sim     | sim     | Inclui `#AABBGGRR` e `#BBGGRR` . Analisado em uma cadeia de caracteres de cor CSS                                                           |
| `colorMode`          | sim     | não      |                                                                                                                            |
| `coordinates`        | sim     | sim     |                                                                                                                            |
| `Data`               | sim     | sim     |                                                                                                                            |
| `description`        | sim     | sim     |                                                                                                                            |
| `displayName`        | sim     | sim     |                                                                                                                            |
| `Document`           | sim     | sim     |                                                                                                                            |
| `drawOrder`          | partial | não      | Leia as sobreposições de aterramento e usadas para classificá-las. 
| `east`               | sim     | sim     |                                                                                                                            |
| `end`                | sim     | sim     |                                                                                                                            |
| `ExtendedData`       | sim     | sim     | Dá suporte `Data` a substituições não tipadas, `SimpleData` ou `Schema` e de entidade do formulário `$[dataName]` .                      |
| `extrude`            | partial | partial | Com suporte apenas para polígonos. A multigeometry com polígonos de diferentes alturas será dividida em recursos individuais. Não há suporte para estilos de linha. Polígonos com uma altitude de 0 serão renderizados como um polígono simples. Durante a leitura, a altitude da primeira coordenada no anel exterior será adicionada como uma propriedade de altura do polígono. Em seguida, a altitude da primeira coordenada será usada para renderizar o polígono no mapa. |
| `fill`               | sim     | sim     |                                                                                                                            |
| `Folder`             | sim     | sim     |                                                                                                                            |
| `GroundOverlay`      | sim     | sim     | `color` Não tem suporte                                                                                                   |
| `heading`            | partial | não      | Analisado, mas não renderizado pelo `SimpleDataLayer` . Só grava se os dados são armazenados na propriedade da forma.                 |
| `hotSpot`            | sim     | partial | Só grava se os dados são armazenados na propriedade da forma. As unidades são emitidas apenas como "pixels".                         |
| `href`               | sim     | sim     |                                                                                                                            |
| `Icon`               | partial | partial | Analisado, mas não renderizado pelo `SimpleDataLayer` . Somente gravará a Propriedade Icon da forma se ela contiver dados URI. Apenas `href` tem suporte. |
| `IconStyle`          | partial | partial | `icon``heading` `colorMode` os valores,, e `hotspots` são analisados, mas não são renderizados por`SimpleDataLayer`         |
| `innerBoundaryIs`    | sim     | sim     |                                                                                                                            |
| `kml`                | sim     | sim     |                                                                                                                            |
| `LabelStyle`         | não      | não      |                                                                                                                            |
| `LatLonBox`          | sim     | sim     |                                                                                                                            |
| `gx:LatLonQuad`      | sim     | sim     |                                                                                                                            |
| `LinearRing`         | sim     | sim     |                                                                                                                            |
| `LineString`         | sim     | sim     |                                                                                                                            |
| `LineStyle`          | sim     | sim     | Não há suporte para `colorMode`.                                                                                         |
| `Link`               | sim     | não      | Somente a `href` propriedade tem suporte para links de rede.                                                                   |
| `MultiGeometry`      | partial | partial | Podem ser divididos em recursos individuais quando lidos.                                                                     |
| `name`               | sim     | sim     |                                                                                                                            |
| `NetworkLink`        | sim     | não      | Os links precisam estar no mesmo domínio que o documento.                                                                  |
| `NetworkLinkControl` | não      | não      |                                                                                                                            |
| `north`              | sim     | sim     |                                                                                                                            |
| `open`               | sim     | sim     |                                                                                                                            |
| `outerBoundaryIs`    | sim     | sim     |                                                                                                                            |
| `outline`            | sim     | sim     |                                                                                                                            |
| `overlayXY`          | não      | não      |                                                                                                                            |
| `Pair`               | partial | não      | Somente o `normal` estilo em um `StyleMap` tem suporte. Não há suporte para `highlight`.                                   |
| `phoneNumber`        | sim     | sim     |                                                                                                                            |
| `PhotoOverlay`       | não      | não      |                                                                                                                            |
| `Placemark`          | sim     | sim     |                                                                                                                            |
| `Point`              | sim     | sim     |                                                                                                                            |
| `Polygon`            | sim     | sim     |                                                                                                                            |
| `PolyStyle`          | sim     | sim     |                                                                                                                            |
| `Region`             | partial | partial | `LatLongBox` tem suporte no nível do documento.                                                                      |
| `rotation`           | não      | não      |                                                                                                                            |
| `rotationXY`         | não      | não      |                                                                                                                            |
| `scale`              | não      | não      |                                                                                                                            |
| `Schema`             | sim     | sim     |                                                                                                                            |
| `SchemaData`         | sim     | sim     |                                                                                                                            |
| `schemaUrl`          | partial | sim     | Não dá suporte ao carregamento de estilos de documentos externos que não estão incluídos em um KMZ.                             |
| `ScreenOverlay`      | não      | não      |                                                                                                                            |
| `screenXY`           | não      | não      |                                                                                                                            |
| `SimpleData`         | sim     | sim     |                                                                                                                            |
| `SimpleField`        | sim     | sim     |                                                                                                                            |
| `size`               | não      | não      |                                                                                                                            |
| `Snippet`            | partial | partial | `maxLines` o atributo é ignorado.                                                                                  |
| `south`              | sim     | sim     |                                                                                                                            |
| `Style`              | sim     | sim     |                                                                                                                            |
| `StyleMap`           | partial | não      | Há suporte apenas para o estilo normal em um `StyleMap` .                                                                        |
| `styleUrl`           | partial | sim     | Não há suporte para URLs de estilo externo.                                                                         |
| `text`               | sim     | sim     | `$[geDirections]`Não há suporte para a substituição                                                                          |
| `textColor`          | sim     | sim     |                                                                                                                            |
| `TimeSpan`           | sim     | sim     |                                                                                                                            |
| `TimeStamp`          | sim     | sim     |                                                                                                                            |
| `value`              | sim     | sim     |                                                                                                                            |
| `viewRefreshMode`    | partial | não      |  Se estiver apontando para um serviço do WMS, somente o `onStop` terá suporte para sobreposições de aterramento. Acrescentará `BBOX=[bboxWest],[bboxSouth],[bboxEast],[bboxNorth]` à URL e atualizará à medida que o mapa for movido.  |
| `visibility`         | sim     | sim     |                                                                                                                            |
| `west`               | sim     | sim     |                                                                                                                            |
| `when`               | sim     | sim     |                                                                                                                            |
| `width`              | sim     | sim     |                                                                                                                            |

### <a name="georss-elements"></a>Elementos GeoRSS

O módulo de e/s espacial dá suporte aos seguintes elementos GeoRSS.

| Nome do elemento             | Ler    | Gravar | Observações                                                                                          |
|--------------------------|---------|-------|------------------------------------------------------------------------------------------------|
| `atom:author`            | sim     | sim   |                                                                                                |
| `atom:category`          | sim     | sim   |                                                                                                |
| `atom:content`           | sim     | sim   |                                                                                                |
| `atom:contributor`       | sim     | sim   |                                                                                                |
| `atom:email`             | sim     | sim   |                                                                                                |
| `atom:entry`             | sim     | sim   |                                                                                                |
| `atom:feed`              | sim     | sim   |                                                                                                |
| `atom:icon`              | sim     | sim   |                                                                                                |
| `atom:id`                | sim     | sim   |                                                                                                |
| `atom:link`              | sim     | sim   |                                                                                                |
| `atom:logo`              | sim     | sim   |                                                                                                |
| `atom:name`              | sim     | sim   |                                                                                                |
| `atom:published`         | sim     | sim   |                                                                                                |
| `atom:rights`            | sim     | sim   |                                                                                                |
| `atom:source`            | sim     | sim   |                                                                                                |
| `atom:subtitle`          | sim     | sim   |                                                                                                |
| `atom:summary`           | sim     | sim   |                                                                                                |
| `atom:title`             | sim     | sim   |                                                                                                |
| `atom:updated`           | sim     | sim   |                                                                                                |
| `atom:uri`               | sim     | sim   |                                                                                                |
| `geo:lat`                | sim     | não    | Gravado como um `georss:point` .                                                                   |
| `geo:lon`                | sim     | não    | Gravado como um `georss:point` .                                                                   |
| `geo:long`               | sim     | não    | Gravado como um `georss:point` .                                                                   |
| `georss:box`             | sim     | não    | Ler como um polígono e receber uma `subType` propriedade de "Rectangle"                                |
| `georss:circle`          | sim     | sim   |                                                                                                |
| `georss:elev`            | sim     | sim   |                                                                                                |
| `georss:featurename`     | sim     | sim   |                                                                                                |
| `georss:featuretypetag`  | sim     | sim   |                                                                                                |
| `georss:floor`           | sim     | sim   |                                                                                                |
| `georss:line`            | sim     | sim   |                                                                                                |
| `georss:point`           | sim     | sim   |                                                                                                |
| `georss:polygon`         | sim     | sim   |                                                                                                |
| `georss:radius`          | sim     | sim   |                                                                                                |
| `georss:relationshiptag` | sim     | sim   |                                                                                                |
| `georss:where`           | sim     | sim   |                                                                                                |
| `geourl:latitude`        | sim     | não    | Gravado como um `georss:point` .                                                                   |
| `geourl:longitude`       | sim     | não    | Gravado como um `georss:point` .                                                                   |
| `position`               | sim     | não    | Alguns feeds XML encapsularão GML com uma marca de posição em vez de encapsulá-lo com uma `georss:where` marca. Lerá essa marca, mas escreverá usando uma `georss:where` marca. |
| `rss`                    | sim     | não    | GeoRSS gravado no formato ATOM.                                                                 |
| `rss:author`             | sim     | partial | Gravado como um `atom:author` .                                                                 |
| `rss:category`           | sim     | partial | Gravado como um `atom:category` .                                                               |
| `rss:channel`            | sim     | não    |                                                                                                |
| `rss:cloud`              | sim     | não    |                                                                                                |
| `rss:comments`           | sim     | não    |                                                                                                |
| `rss:copyright`          | sim     | partial | Escrito como uma `atom:rights` forma if ainda não tem uma `rights` `properties` propriedade.       |
| `rss:description`        | sim     | partial | Escrito como uma `atom:content` forma if ainda não tem uma `content` `properties` propriedade.      |
| `rss:docs`               | sim     | não    |                                                                                                |
| `rss:enclosure`          | sim     | não    |                                                                                                |
| `rss:generator`          | sim     | não    |                                                                                                |
| `rss:guid`               | sim     | partial | Escrito como uma `atom:id` forma if ainda não tem uma `id` `properties` propriedade.         |
| `rss:image`              | sim     | partial | Escrito como uma `atom:logo` forma if ainda não tem uma `logo` `properties` propriedade.      |
| `rss:item`               | sim     | partial | Gravado como um `atom:entry` .                                                                  |
| `rss:language`           | sim     | não    |                                                                                                |
| `rss:lastBuildDate`      | sim     | partial | Escrito como uma `atom:updated` forma if ainda não tem uma `updated` `properties` propriedade.     |
| `rss:link`               | sim     | partial | Gravado como um `atom:link` .                                                                   |
| `rss:managingEditor`     | sim     | partial | Gravado como um `atom:contributor` .                                                            |
| `rss:pubDate`            | sim     | partial | Escrito como uma `atom:published` forma if ainda não tem uma `published` `properties` propriedade.  |
| `rss:rating`             | sim     | não    |                                                                                                |
| `rss:skipDays`           | sim     | não    |                                                                                                |
| `rss:skipHours`          | sim     | não    |                                                                                                |
| `rss:source`             | sim     | partial | Escrito como um `atom:source` que contém um `atom:link` .                                       |
| `rss:textInput`          | sim     | não    |                                                                                                |
| `rss:title`              | sim     | partial | Gravado como um `atom:title` .                                                                  |
| `rss:ttl`                | sim     | não    |                                                                                                |
| `rss:webMaster`          | sim     | não    |                                                                                                |

### <a name="gml-elements"></a>Elementos GML

O módulo de e/s espacial dá suporte aos seguintes elementos GML. 

| Nome do elemento            | Ler | Gravar | Observações                                                                                  |
|-------------------------|------|-------|----------------------------------------------------------------------------------------|
| `gml:coordinates`       | sim  | não    | Escrito como `gml:posList` .                                                              |
| `gml:curveMember`       | sim  | não    |                                                                                        |
| `gml:curveMembers`      | sim  | não    |                                                                                        |
| `gml:Box`               | sim  | não    | Escrito como `gml:Envelope` .                                                             |
| `gml:description`       | sim  | sim   |                                                                                        |
| `gml:Envelope`          | sim  | sim   |                                                                                        |
| `gml:exterior`          | sim  | sim   |                                                                                        |
| `gml:Feature`           | sim  | não    | Escrito como uma forma.                                                                    |
| `gml:FeatureCollection` | sim  | não    | Escrito como uma coleção Geometry.                                                      |
| `gml:featureMember`     | sim  | não    | Escrito como uma coleção Geometry.                                                      |
| `gml:geometry`          | sim  | não    | Escrito como uma forma.                                                                    |
| `gml:geometryMember`    | sim  | sim   |                                                                                        |
| `gml:geometryMembers`   | sim  | sim   |                                                                                        |
| `gml:identifier`        | sim  | sim   |                                                                                        |
| `gml:innerBoundaryIs`   | sim  | não    | Escrito usando `gml.interior` .                                                          |
| `gml:interior`          | sim  | sim   |                                                                                        |
| `gml:LinearRing`        | sim  | sim   |                                                                                        |
| `gml:LineString`        | sim  | sim   |                                                                                        |
| `gml:lineStringMember`  | sim  | sim   |                                                                                        |
| `gml:lineStringMembers` | sim  | não    |                                                                                        |
| `gml:MultiCurve`        | sim  | não    | Somente lê `gml:LineString` Membros. Escrito como `gml.MultiLineString`                  |
| `gml:MultiGeometry`     | partial  | partial   | Somente leitura como uma Featurecollection.                                              |
| `gml:MultiLineString`   | sim  | sim   |                                                                                        |
| `gml:MultiPoint`        | sim  | sim   |                                                                                        |
| `gml:MultiPolygon`      | sim  | sim   |                                                                                        |
| `gml:MultiSurface`      | sim  | não    | Somente lê `gml:Polygon` Membros. Escrito como `gml.MultiPolygon`                        |
| `gml:name`              | sim  | sim   |                                                                                        |
| `gml:outerBoundaryIs`   | sim  | não    | Escrito usando `gml.exterior` .                                                          |
| `gml:Point`             | sim  | sim   |                                                                                        |
| `gml:pointMember`       | sim  | sim   |                                                                                        |
| `gml:pointMembers`      | sim  | não    |                                                                                        |
| `gml:Polygon`           | sim  | sim   |                                                                                        |
| `gml:polygonMember`     | sim  | sim   |                                                                                        |
| `gml:polygonMembers`    | sim  | não    |                                                                                        |
| `gml:pos`               | sim  | sim   |                                                                                        |
| `gml:posList`           | sim  | sim   |                                                                                        |
| `gml:surfaceMember`     | sim  | sim   |                                                                                        |

#### <a name="additional-notes"></a>observações adicionais

- Os elementos de membro serão pesquisados em busca de uma geometria que pode ser incluída nos elementos filho. Essa operação de pesquisa é necessária, pois muitos formatos XML que se estendem de GML podem não posicionar uma geometria como um filho direto de um elemento de membro.
- `srsName` Há suporte parcial para coordenadas WGS84 e os seguintes códigos:[EPSG: 4326](https://epsg.io/4326)) e Web Mercator ([EPSG: 3857](https://epsg.io/3857) ou um de seus códigos alternativos. Qualquer outro sistema de coordenadas será analisado como WGS84 como está.
- A menos que especificado durante a leitura de um feed XML, a ordem do eixo é determinada com base nas dicas no feed XML. Uma preferência é fornecida para a ordem do eixo "latitude, longitude".
- A menos que um namespace GML personalizado seja especificado para as propriedades durante a gravação em um arquivo GML, informações de propriedade adicionais não serão adicionadas.

### <a name="gpx-elements"></a>Elementos GPX

O módulo de e/s espacial dá suporte aos seguintes elementos GPX.

| Nome do elemento             | Ler    | Gravar   | Observações                                                                                       |
|--------------------------|---------|---------|---------------------------------------------------------------------------------------------|
| `gpx:ageofdgpsdata`      | sim     | sim     |                                                                                             |
| `gpx:author`             | sim     | sim     |                                                                                             |
| `gpx:bounds`             | sim     | sim     | Convertido em um LocationRect quando lido.                                                    |
| `gpx:cmt`                | sim     | sim     |                                                                                             |
| `gpx:copyright`          | sim     | sim     |                                                                                             |
| `gpx:desc`               | sim     | sim     | Copiado para uma propriedade de descrição quando Read para alinhar com outros formatos XML.               |
| `gpx:dgpsid`             | sim     | sim     |                                                                                             |
| `gpx:ele`                | sim     | sim     |                                                                                             |
| `gpx:extensions`         | partial | partial | Quando lida, as informações de estilo são extraídas. Todas as outras extensões serão mescladas em um objeto JSON simples. Somente as informações de estilo de forma são gravadas. |
| `gpx:geoidheight`        | sim     | sim     |                                                                                             |
| `gpx:gpx`                | sim     | sim     |                                                                                             |
| `gpx:hdop`               | sim     | sim     |                                                                                             |
| `gpx:link`               | sim     | sim     |                                                                                             |
| `gpx:magvar`             | sim     | sim     |                                                                                             |
| `gpx:metadata`           | sim     | sim     |                                                                                             |
| `gpx:name`               | sim     | sim     |                                                                                             |
| `gpx:pdop`               | sim     | sim     |                                                                                             |
| `gpx:rte`                | sim     | sim     |                                                                                             |
| `gpx:rtept`              | sim     | sim     |                                                                                             |
| `gpx:sat`                | sim     | sim     |                                                                                             |
| `gpx:src`                | sim     | sim     |                                                                                             |
| `gpx:sym`                | sim     | sim     | O valor é capturado, mas não é usado para alterar o ícone de pino.                               |
| `gpx:text`               | sim     | sim     |                                                                                             |
| `gpx:time`               | sim     | sim     |                                                                                             |
| `gpx:trk`                | sim     | sim     |                                                                                             |
| `gpx:trkpt`              | sim     | sim     |                                                                                             |
| `gpx:trkseg`             | sim     | sim     |                                                                                             |
| `gpx:type`               | sim     | sim     |                                                                                             |
| `gpx:vdop`               | sim     | sim     |                                                                                             |
| `gpx:wpt`                | sim     | sim     |                                                                                             |
| `gpx_style:color`        | sim     | sim     |                                                                                             |
| `gpx_style:line`         | partial | partial | `color`, `opacity` , `width` , `lineCap` têm suporte.                                           |
| `gpx_style:opacity`      | sim     | sim     |                                                                                             |
| `gpx_style:width`        | sim     | sim     |                                                                                             |
| `gpxx:DisplayColor`      | sim     | não      | Usado para especificar a cor de uma forma. Ao escrever, a `gpx_style:line` cor será usada em seu lugar.  |
| `gpxx:RouteExtension`    | partial | não      | Todas as propriedades são lidas em `properties` . Somente `DisplayColor` é usado.                     |
| `gpxx:TrackExtension`    | partial | não      | Todas as propriedades são lidas em `properties` . Somente `DisplayColor` é usado.                     |
| `gpxx:WaypointExtension` | partial | não      | Todas as propriedades são lidas em `properties` . Somente `DisplayColor` é usado.                     |
| `gpx:keywords`           | sim     | sim     |                                                                                             |
| `gpx:fix`                | sim     | sim     |                                                                                             |

#### <a name="additional-notes"></a>observações adicionais

Ao escrever;

- Os multipontos serão divididos em Marcos individuais.
- Polígonos e subpolígonos serão gravados como faixas. 
  
## <a name="supported-well-known-text-geometry-types"></a>Tipos de geometria de texto Well-Known com suporte

| Tipo de geometria | Ler | Gravar |
|--------------|:----:|:-----:|
| EMPRESAS | x | x |
| PONTO Z | x | x | 
| PONTO M | x | x<sup>[2]</sup> |
| ZM DO PONTO | x<sup>[1]</sup><sup>[2]</sup> | | 
| LINESTRING | x | x |
| LINESTRING Z | x | x | 
| LINESTRING M | x | x<sup>[2]</sup> |
| LINESTRING ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| POLYGON | x | x |
| POLÍGONO Z | x | x |
| POLÍGONO M | x | x<sup>[2]</sup> |
| ZM DE POLÍGONO | x<sup>[1]</sup><sup>[2]</sup> | | 
| MULTIPOINT | x | x |
| MULTIPOINT Z | x | x | 
| MULTIPOINT M | x | x<sup>[2]</sup> |
| POMULTIPOINTINT ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| MULTILINESTRING | x | x |
| MULTILINESTRING Z | x | x | 
| MULTILINESTRING M | x | x<sup>[2]</sup> |
| MULTILINESTRING ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| MULTIPOLYGON | x | x |
| MULTIPOLYGON Z | x | x | 
| MULTIPOLYGON M | x | x<sup>[2]</sup> |
| MULTIPOLYGON ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| GEOMETRYCOLLECTION | x | x |
| GEOMETRYCOLLECTION Z | x | x | 
| GEOMETRYCOLLECTION M | x | x<sup>[2]</sup> | 
| ZM GEOMETRYCOLLECTION | x<sup>[1]</sup><sup>[2]</sup> | x | 

\[1 \] somente o parâmetro Z é capturado e adicionado como um terceiro valor no valor da posição.

\[2 \] M parâmetro não é capturado.

## <a name="delimited-spatial-data-support"></a>Suporte a dados espaciais delimitados

Os dados espaciais delimitados, como CSV (arquivos de valores separados por vírgula), geralmente têm colunas que contêm dados espaciais. Por exemplo, pode haver colunas que contêm informações de latitude e longitude. No formato de texto Well-Known, pode haver uma coluna que contém dados de geometria espacial.

### <a name="spatial-data-column-detection"></a>Detecção de coluna de dados espaciais

Ao ler um arquivo delimitado que contém dados espaciais, o cabeçalho será analisado para determinar quais colunas contêm campos de localização. Se o cabeçalho contiver informações de tipo, ele será usado para converter os valores de célula para o tipo apropriado. Se nenhum cabeçalho for especificado, a primeira linha será analisada e usada para gerar um cabeçalho. Ao analisar a primeira linha, uma verificação é executada para corresponder nomes de coluna com os nomes a seguir de forma não diferencia maiúsculas de minúsculas. A ordem dos nomes é a prioridade, caso dois ou mais nomes existam em um arquivo.

#### <a name="latitude"></a>Latitude

- `latitude`
- `lat`
- `latdd`
- `lat_dd`
- `latitude83`
- `latdecdeg`
- `y`
- `ycenter`
- `point-y`

#### <a name="longitude"></a>Longitude

- `longitude`
- `lon`
- `lng`
- `long`
- `longdd`
- `long_dd`
- `longitude83`
- `longdecdeg`
- `x`
- `xcenter`
- `point-x`

#### <a name="elevation"></a>Elevação

- `elevation`
- `elv`
- `altitude`
- `alt`
- `z`

#### <a name="geography"></a>painel Geografia do app&#39;s selecionado

A primeira linha de dados será verificada em busca de cadeias de caracteres que estejam no formato de texto Well-Known. 

### <a name="delimited-data-column-types"></a>Tipos de coluna de dados delimitados

Ao verificar a linha de cabeçalho, qualquer informação de tipo que esteja no nome da coluna será extraída e usada para converter as células nessa coluna. Aqui está um exemplo de um nome de coluna que tem um valor de tipo: "ColumnName (typeName)". Os seguintes nomes de tipos que não diferenciam maiúsculas de minúsculas têm suporte:

#### <a name="numbers"></a>Números

- EDM. Int64
- INT
- long
- EDM. Double
- FLOAT
- double
- número

#### <a name="booleans"></a>Boolianos

- EDM. booliano
- bool
- boolean

#### <a name="dates"></a>Datas

- EDM. DateTime
- date
- DATETIME

#### <a name="geography"></a>painel Geografia do app&#39;s selecionado

- EDM. geography
- geografia

#### <a name="strings"></a>Cadeias de caracteres

- EDM. String
- varchar
- text
- string

Se nenhuma informação de tipo puder ser extraída do cabeçalho e a opção de digitação dinâmica estiver habilitada durante a leitura, cada célula será analisada individualmente para determinar a qual tipo de dados é mais adequado ser convertido como.

## <a name="next-steps"></a>Próximas etapas

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

[Ler e gravar dados espaciais](spatial-io-read-write-spatial-data.md)
