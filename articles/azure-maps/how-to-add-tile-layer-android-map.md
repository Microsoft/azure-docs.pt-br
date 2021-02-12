---
title: Adicionar uma camada de bloco ao Android Maps | Mapas do Microsoft Azure
description: Saiba como adicionar uma camada de bloco a um mapa. Veja um exemplo que usa o Azure Maps SDK do Android para adicionar uma sobreposição de radar meteorológico a um mapa.
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 8ea6f44c47c5cd4d223b053640f65827f46db482
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679312"
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

## <a name="prerequisites"></a>Pré-requisitos

Para concluir o processo neste artigo, você precisa instalar o [Azure Maps SDK do Android](how-to-use-android-map-control-library.md) para carregar um mapa.

## <a name="add-a-tile-layer-to-the-map"></a>Adicionar uma camada de bloco ao mapa

Este exemplo mostra como criar uma camada de bloco que aponta para um conjunto de blocos. Este exemplo usa o sistema de divisão "x, y," zoom ". A origem dessa camada do bloco é o [projeto OpenSeaMap](https://openseamap.org/index.php), que contém gráficos náuticas originados de um público. Geralmente, ao exibir camadas de bloco, é desejável poder ver claramente os rótulos de cidades no mapa. Esse comportamento pode ser obtido inserindo-se a camada lado abaixo das camadas do rótulo do mapa.

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

A captura de tela a seguir mostra o código acima exibindo uma camada de bloco de informações náuticas em um mapa que tem um estilo de tons de cinza escuro.

![Mapa do Android exibindo camada do bloco](media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)

## <a name="next-steps"></a>Próximas etapas

Consulte o artigo a seguir para saber mais sobre maneiras de definir estilos de mapa

> [!div class="nextstepaction"]
> [Alterar o estilo do mapa](set-android-map-styles.md)

> [!div class="nextstepaction"]
> [Adicionar um mapa de calor](map-add-heat-map-layer-android.md)
