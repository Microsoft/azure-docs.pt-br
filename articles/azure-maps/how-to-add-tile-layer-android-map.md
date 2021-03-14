---
title: Adicionar uma camada de bloco ao Android Maps | Mapas do Microsoft Azure
description: Saiba como adicionar uma camada de bloco a um mapa. Veja um exemplo que usa o Azure Maps SDK do Android para adicionar uma sobreposição de radar meteorológico a um mapa.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 6a920dc222cae4aedd77b667644de317637bbb69
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102047495"
---
# <a name="add-a-tile-layer-to-a-map-android-sdk"></a>Adicionar uma camada de bloco a um mapa (SDK do Android)

Este artigo mostra como renderizar uma camada de bloco em um mapa usando o SDK do Android do Azure Maps. Camadas lado a lado permitem que você sobreponha imagens sobre peças de mapa base do Azure Mapas. Obtenha mais informações sobre o sistema de mosaico do Azure Mapas na documentação [Níveis de Zoom e grade lado a lado](zoom-levels-and-tile-grid.md).

Uma camada de bloco é carregada em blocos de um servidor. Essas imagens podem ser previamente renderizadas e armazenadas como qualquer outra imagem em um servidor, usando uma Convenção de nomenclatura que a camada de bloco compreende. Ou, essas imagens podem ser renderizadas com um serviço dinâmico que gera as imagens quase em tempo real. Há três convenções de nomenclatura de serviço de bloco diferentes com suporte pela classe TileLayer do Azure Maps:

* X, Y, notação de Zoom - com base no nível de zoom, x é a coluna e y é a posição da linha do bloco na grade lado a lado.
* Notação Quadkey - combinação x, y, informações de zoom em um valor de cadeia de caracteres única que é um identificador exclusivo para um bloco.
* A caixa delimitadora – as coordenadas da caixa delimitadora podem ser usadas para especificar uma imagem no formato `{west},{south},{east},{north}` , que é normalmente usado pelos [serviços de mapeamento da Web (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> Um TileLayer é uma ótima maneira para visualizar grandes conjuntos de dados no mapa. Não apenas uma camada de bloco pode ser gerada de uma imagem, mas os dados de vetor podem também ser renderizados como uma camada de peça. Ao renderizar dados de vetor como uma camada de bloco, o controle de mapa só precisa carregar os blocos, o que pode ser muito menor no tamanho do arquivo do que os dados de vetor que eles representam. Essa técnica é usada por muitas pessoas que precisam renderizar milhões de linhas de dados no mapa.

A URL do bloco passada para uma camada de peça deve ser uma URL http/https para um recurso de TileJSON ou um modelo de URL de bloco que usa os seguintes parâmetros: 

* `{x}` - posição X do bloco. Também precisa `{y}` e `{z}`.
* `{y}` - posição Y do bloco. Também precisa `{x}` e `{z}`.
* `{z}` -Nível de zoom do bloco. Também precisa `{x}` e `{y}`.
* `{quadkey}` - identificador quadkey de bloco baseado a convenção de nomenclatura do sistema de blocos Bing Maps.
* `{bbox-epsg-3857}` -Uma cadeia de caracteres de caixa delimitadora com o formato `{west},{south},{east},{north}` no sistema de referência espacial do EPSG 3857.
* `{subdomain}` -Um espaço reservado para os valores de subdomínio, se o valor de subdomínio for especificado.
* `azmapsdomain.invalid` -Um espaço reservado para alinhar o domínio e a autenticação de solicitações de bloco com os mesmos valores usados pelo mapa. Use isso ao chamar um serviço de bloco hospedado pelo Azure Maps.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir o processo neste artigo, você precisa instalar o [Azure Maps SDK do Android](how-to-use-android-map-control-library.md) para carregar um mapa.

## <a name="add-a-tile-layer-to-the-map"></a>Adicionar uma camada de bloco ao mapa

Este exemplo mostra como criar uma camada de bloco que aponta para um conjunto de blocos. Este exemplo usa o sistema de divisão "x, y," zoom ". A origem dessa camada do bloco é o [projeto OpenSeaMap](https://openseamap.org/index.php), que contém gráficos náuticas originados de um público. Geralmente, ao exibir camadas de bloco, é desejável poder ver claramente os rótulos de cidades no mapa. Esse comportamento pode ser obtido inserindo-se a camada lado abaixo das camadas do rótulo do mapa.

::: zone pivot="programming-language-java-android"

```java
TileLayer layer = new TileLayer(
    tileUrl("https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png"),
    opacity(0.8f),
    tileSize(256),
    minSourceZoom(7),
    maxSourceZoom(17)
);

map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = TileLayer(
    tileUrl("https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png"),
    opacity(0.8f),
    tileSize(256),
    minSourceZoom(7),
    maxSourceZoom(17)
)

map.layers.add(layer, "labels")
```

::: zone-end

A captura de tela a seguir mostra o código acima exibindo uma camada de bloco de informações náuticas em um mapa que tem um estilo de tons de cinza escuro.

![Mapa do Android exibindo camada do bloco](media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)

## <a name="next-steps"></a>Próximas etapas

Consulte o artigo a seguir para saber mais sobre maneiras de sobrepor imagens em um mapa.

> [!div class="nextstepaction"]
> [Camada de imagem](map-add-image-layer-android.md)
