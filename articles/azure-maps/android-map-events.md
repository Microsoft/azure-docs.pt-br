---
title: Manipular eventos de mapa no Android Maps | Mapas do Microsoft Azure
description: Saiba quais eventos são acionados quando os usuários interagem com mapas. Exiba uma lista de todos os eventos de mapa com suporte. Consulte como usar o SDK do Android do Azure Maps para manipular eventos.
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 818d33947fa079a130c3009a34dcb9b72ad52f91
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681403"
---
# <a name="interact-with-the-map-android-sdk"></a>Interagir com o mapa (SDK do Android)

Este artigo mostra como usar o Gerenciador de eventos do Maps.

## <a name="interact-with-the-map"></a>Interagir com o mapa

O mapa gerencia todos os eventos por meio de sua `events` propriedade. A tabela a seguir lista todos os eventos de mapa com suporte.

| Evento                  | Formato do manipulador de eventos | Descrição |
|------------------------|----------------------|-------------|
| `OnCameraIdle`         | `()`                 | <p>Acionado após o último quadro renderizado antes do mapa entrar em um estado "ocioso":<ul><li>Nenhuma transição de câmera está em andamento.</li><li>Todos os blocos atualmente solicitados foram carregados.</li><li>Todas as animações de esmaecimento/transição foram concluídas.</li></ul></p> |
| `OnCameraMove`         | `()`                 | Acionado repetidamente durante uma transição animada de uma exibição para outra, como resultado de métodos ou de interação do usuário. |
| `OnCameraMoveCanceled` | `()`                 | Acionado quando uma solicitação de movimentação para a câmera é cancelada. |
| `OnCameraMoveStarted`  | `(int reason)`       | Acionado imediatamente antes de o mapa começar uma transição de uma exibição para outra, como resultado de métodos ou de interação do usuário. O `reason` argumento do ouvinte de eventos retorna um valor inteiro que fornece detalhes de como o movimento da câmera foi iniciado. A seguir está a lista de possíveis motivos:<ul><li>1: gesto</li><li>2: animação do desenvolvedor</li><li>3: animação de API</li></ul>   |
| `OnClick`              | `(double lat, double lon)` | Acionado quando o mapa é pressionado e liberado no mesmo ponto no mapa. |
| `OnFeatureClick`       | `(List<Feature>)`    | Acionado quando o mapa é pressionado e liberado no mesmo ponto em um recurso.  |
| `OnLongClick`          | `(double lat, double lon)` | Acionado quando o mapa é pressionado, mantido por um momento e, em seguida, liberado no mesmo ponto no mapa. |
| `OnLongFeatureClick `  | `(List<Feature>)`    | Acionado quando o mapa é pressionado, mantido por um momento e, em seguida, liberado no mesmo ponto em um recurso. |
| `OnReady`              | `(AzureMap map)`     | Acionado quando o mapa é carregado inicialmente ou quando a orientação do aplicativo é alterada e os recursos de mapa mínimos necessários são carregados e o mapa está pronto para ser programaticamente interagindo com o. |

O código a seguir mostra como adicionar os `OnClick` `OnFeatureClick` eventos, e `OnCameraMove` ao mapa.

```java
map.events.add((OnClick) (lat, lon) -> {
    //Map clicked.
});

map.events.add((OnFeatureClick) (features) -> {
    //Feature clicked.
});

map.events.add((OnCameraMove) () -> {
    //Map camera moved.
});
```

Para obter mais informações, consulte [navegando na documentação do mapa](how-to-use-android-map-control-library.md#navigating-the-map) sobre como interagir com os eventos Map e Trigger.

## <a name="scope-feature-events-to-layer"></a>Eventos de recurso de escopo para camada

Ao adicionar os `OnFeatureClick` `OnLongFeatureClick` eventos ou ao mapa, uma ID de camada pode ser passada como um segundo parâmetro. Quando uma ID de camada é passada, o evento só será acionado se o evento ocorrer nessa camada. Os eventos com escopo de camadas têm suporte nas camadas símbolo, bolha, linha e polígono.

```java
//Create a data source.
DataSource source = new DataSource();
map.sources.add(source);

//Add data to the data source.
source.add(Point.fromLngLat(0, 0));

//Create a layer and add it to the map.
BubbleLayer layer = new BubbleLayer(source);
map.layers.add(layer);

//Add a feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnFeatureClick) (features) -> {
    //One or more features clicked.
}, layer.getId());

//Add a long feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnLongFeatureClick) (features) -> {
    //One or more features long clicked.
}, layer.getId());
```

## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes artigos para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Navegar no mapa](how-to-use-android-map-control-library.md#navigating-the-map)

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolhas](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de linha](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígono](how-to-add-shapes-to-android-map.md)
