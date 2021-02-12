---
title: Mostrar dados de tráfego em mapas do Android | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá como exibir dados de tráfego em um mapa usando a SDK do Android de mapas de Microsoft Azure.
author: rbrundritt
ms.author: richbrun
ms.date: 12/04/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 113f39ac2976b870c9e07851cdd0919e2578940f
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680450"
---
# <a name="show-traffic-data-on-the-map-android-sdk"></a>Mostrar dados de tráfego no mapa (SDK do Android)

Dados de fluxo e dados de incidentes são os dois tipos de dados de tráfego que podem ser exibidos no mapa. Este guia mostra como exibir os dois tipos de dados de tráfego. Os dados de incidentes consistem em dados de ponto e de linha para coisas como construções, fechamentos de estrada e acidentes. Os dados de fluxo mostram métricas sobre o fluxo de tráfego em trânsito.

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de concluir as etapas no documento [início rápido: criar um aplicativo Android](quick-android-map.md) . Os blocos de código neste artigo podem ser inseridos no `onReady` manipulador de eventos Maps.

## <a name="show-traffic-on-the-map"></a>Mostrar tráfego no mapa

Há dois tipos de dados de tráfego disponíveis no Azure Mapas:

- Dados de incidentes – consistem em dados baseados em ponto e em linha para coisas como obras, fechamentos de estrada e acidentes.
- Dados de fluxo – fornecem métricas sobre o fluxo do tráfego nas estradas. Os dados de fluxo de tráfego são frequentemente usados para colorir as estradas. As cores são baseadas no quanto o tráfego está diminuindo o fluxo, em relação ao limite de velocidade ou a outra métrica. Há quatro valores que podem ser passados para a `flow` opção de tráfego do mapa.

    |Valor do fluxo | Descrição|
    | :-- | :-- |
    | Do trafficflow. NONE | Não exibe dados de tráfego no mapa |
    | Do trafficflow. RELATIVE | Mostra os dados de tráfego relativos à velocidade de fluxo livre da estrada |
    | TrafficFlow.RELATIVE_DELAY | Exibe áreas que são mais lentas do que o atraso médio esperado |
    | Do trafficflow. ABSOLUTE | Mostra a velocidade absoluta de todos os veículos em trânsito |

O código a seguir mostra como exibir dados de tráfego no mapa.

```java
//Show traffic on the map using the traffic options.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
);
```

A captura de tela a seguir mostra o código acima renderização informações de tráfego em tempo real no mapa.

![Mapa mostrando informações de tráfego em tempo real](media/how-to-show-traffic-android/android-show-traffic.png)

## <a name="get-traffic-incident-details"></a>Obter detalhes do incidente de tráfego

Os detalhes sobre um incidente de tráfego estão disponíveis nas propriedades do recurso usado para exibir o incidente no mapa. Os incidentes de tráfego são adicionados ao mapa usando o serviço de bloco de vetor de incidente de tráfego do Azure Maps. O formato dos dados nesses blocos de vetor se [documentado aqui](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles). O código a seguir adiciona um evento de clique ao mapa e recupera o recurso de incidente de tráfego que foi clicado e exibe uma mensagem do sistema com alguns dos detalhes.

```java
//Show traffic information on the map.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
);

//Add a click event to the map.
map.events.add((OnFeatureClick) (features) -> {

    if (features != null && features.size() > 0) {
        Feature incident = features.get(0);

        //Ensure that the clicked feature is an traffic incident feature.
        if (incident.properties() != null && incident.hasProperty("incidentType")) {

            StringBuilder sb = new StringBuilder();
            String incidentType = incident.getStringProperty("incidentType");

            if (incidentType != null) {
                sb.append(incidentType);
            }

            if (sb.length() > 0) {
                sb.append("\n");
            }

            //If the road is closed, find out where it is closed from.
            if ("Road Closed".equals(incidentType)) {
                String from = incident.getStringProperty("from");

                if (from != null) {
                    sb.append(from);
                }
            } else {
                //Get the description of the traffic incident.
                String description = incident.getStringProperty("description");

                if (description != null) {
                    sb.append(description);
                }
            }

            String message = sb.toString();

            if (message.length() > 0) {
                Toast.makeText(this, message, Toast.LENGTH_LONG).show();
            }
        }
    }
});
```

A captura de tela a seguir mostra o código acima renderização informações de tráfego em tempo real no mapa com uma mensagem do sistema exibindo detalhes do incidente.

![Mapa mostrando informações de tráfego em tempo real com uma mensagem do sistema exibindo detalhes do incidente](media/how-to-show-traffic-android/android-traffic-details.png)

## <a name="next-steps"></a>Próximas etapas

Exiba os guias a seguir para saber como adicionar mais dados ao mapa:

> [!div class="nextstepaction"]
> [Adicionar uma camada de bloco](how-to-add-tile-layer-android-map.md)
