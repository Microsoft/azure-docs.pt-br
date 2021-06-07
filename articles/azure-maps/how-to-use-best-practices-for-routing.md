---
title: Práticas recomendadas para o Azure Maps Serviço de Roteiros no Microsoft Azure Maps
description: Saiba como encaminhar veículos usando Serviço de Roteiros de mapas Microsoft Azure.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8174529def5e3924086e49f36c225f07a4da2648
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99051644"
---
# <a name="best-practices-for-azure-maps-route-service"></a>Práticas recomendadas para o serviço de rota do Azure Maps

As APIs de direção de rota e matriz de rota no Azure Maps [serviço de roteiros](/rest/api/maps/route) podem ser usadas para calcular os tempos de chegada estimados (ETAs) para cada rota solicitada. APIs de rota consideram fatores como informações de tráfego em tempo real e dados de tráfego históricos, como as velocidades de estrada típicas no dia solicitado da semana e hora do dia. As APIs retornam as rotas mais curtas ou mais rápidas disponíveis para vários destinos de cada vez em sequência ou em ordem otimizada com base em tempo ou distância. Os usuários também podem solicitar roteiros e detalhes especializados para os apresentadores, ciclistas e veículos comerciais como caminhões. Neste artigo, vamos compartilhar as práticas recomendadas para chamar o Azure Maps [serviço de roteiros](/rest/api/maps/route)e você aprenderá a:

 * Escolha entre as APIs de direção de rota e a API de roteamento de matriz
 * Solicitar tempos de viagem históricos e previstos, com base em dados de tráfego históricos e em tempo real
 * Detalhes da rota de solicitação, como tempo e distância, para toda a rota e cada trecho da rota
 * Solicitar rota para um veículo comercial, como um caminhão
 * Solicitar informações de tráfego ao longo de uma rota, como informações de emperramento e de Tarifa
 * Solicitar uma rota que consiste em uma ou mais paradas (Marcos)
 * Otimizar uma rota de uma ou mais interrupções para obter a melhor ordem para visitar cada parada (waypoint)
 * Otimizar rotas alternativas usando pontos de suporte. Por exemplo, ofereça rotas alternativas que passem uma estação de cobrança de veículo elétrico.
 * Usar o [serviço de roteiros](/rest/api/maps/route) com o SDK da Web do Azure Maps

## <a name="prerequisites"></a>Pré-requisitos

1. [Fazer uma conta do Azure Mapas](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Obtenha uma chave de assinatura primária](quick-demo-map-app.md#get-the-primary-key-for-your-account), também conhecida como a chave primária ou a chave de assinatura.

Para obter mais informações sobre a cobertura do Serviço de Roteiros, consulte a [cobertura de roteamento](routing-coverage.md).

Este artigo usa o [aplicativo de postmaster](https://www.postman.com/downloads/) para criar chamadas REST, mas você pode escolher qualquer ambiente de desenvolvimento de API.

## <a name="choose-between-route-directions-and-matrix-routing"></a>Escolha entre direções de rota e roteamento de matriz

As APIs de direção de rota retornam instruções, incluindo o tempo de viagem e as coordenadas de um caminho de rota. A API de matriz de rota permite que você calcule o tempo de viagem e as distâncias de um conjunto de rotas que são definidas por locais de origem e de destino. Para cada origem determinada, a API de matriz calcula o custo (tempo de viagem e distância) do roteamento dessa origem para cada destino especificado. Todas essas APIs permitem que você especifique parâmetros como a hora de partida desejada, os tempos de chegada e o tipo de veículo, como carro ou caminhão. Todos eles usam dados de tráfego de previsão ou em tempo real de forma adequada para retornar as rotas ideais.

Considere chamar APIs de direções de rota se o seu cenário for:

* Solicite a rota de condução mais curta ou mais rápida entre dois ou mais locais conhecidos, para obter tempos de chegada precisos para seus veículos de entrega.
* Solicitar orientação de rota detalhada, incluindo a geometria de rota, para visualizar rotas no mapa
* Dada uma lista de locais de clientes, calcule a rota mais curta possível para visitar cada local do cliente e retornar à origem. Esse cenário é normalmente conhecido como o problema de vendas de viagens. Você pode passar até 150 marcos (paradas) em uma solicitação.
* Envie lotes de consultas para a API do lote de direções de rota usando apenas uma única chamada à API.

Considere chamar a API de roteamento de matriz se seu cenário for:

* Calcule o tempo de viagem ou a distância entre um conjunto de origens e destinos. Por exemplo, você tem 12 drivers e precisa encontrar o driver disponível mais próximo para pegar a entrega de alimentos do restaurante.
* Classificar possíveis rotas por sua distância ou tempo real de viagem. A API de matriz retorna apenas os tempos de viagem e as distâncias para cada combinação de origem e destino.
* Dados de cluster com base no tempo de viagem ou em distâncias. Por exemplo, sua empresa tem 50 funcionários, encontre todos os funcionários que residem dentro de 20 minutos de tempo da unidade do seu escritório.

Aqui está uma comparação para mostrar alguns recursos das direções de rota e APIs de matriz:

| API do Azure Maps | Número máximo de consultas na solicitação | Evitar áreas | Roteamento de caminhão e de veículo elétrico | Marcos e otimização de vendas de viagens | Pontos de suporte |
| :--------------: |  :--------------: |  :--------------: | :--------------: | :--------------: | :--------------: |
| Obter Trajeto de Rota | 1 | | ✔ | ✔ | |
| Pós-direções de rota | 1 | ✔ | ✔ | ✔ | ✔ |
| Postar o lote de direções do roteiro | 700 | | ✔ | ✔ | |
| Matrix de rota POST | 700 | | ✔ | | |

Para saber mais sobre os recursos de roteamento de veículo elétrico, confira nosso tutorial sobre como [rotear veículos elétricos usando o Azure notebooks com Python](tutorial-ev-routing.md).

## <a name="request-historic-and-real-time-data"></a>Solicitar dados históricos e em tempo real

Por padrão, o serviço de rota assume que o modo de viagem é um carro e o horário de partida agora é. Ele retorna a rota com base em condições de tráfego em tempo real, a menos que uma solicitação de cálculo de rota especifique o contrário. Restrições de tráfego fixas dependentes de tempo, como ' Left não são permitidas entre 4:00 PM a 6:00 PM ' são capturadas e serão consideradas pelo mecanismo de roteamento. Os fechamentos de estrada, como roadworks, serão considerados, a menos que você solicite especificamente uma rota que ignore o tráfego ativo atual. Para ignorar o tráfego atual, defina `traffic` como `false` em sua solicitação de API.

O valor de **travelTimeInSeconds** de cálculo de rota inclui o atraso devido ao tráfego. Ele é gerado aproveitando os dados atuais e históricos do horário de viagem, quando a hora de partida é definida como Now. Se o horário de partida for definido no futuro, as APIs retornarão tempos de viagem previstos com base em dados históricos.

Se você incluir o parâmetro **computeTravelTimeFor = All** em sua solicitação, o elemento Summary na resposta terá os seguintes campos adicionais, incluindo condições de tráfego históricas:

| Elemento | Descrição|
| :--- | :--- |
| noTrafficTravelTimeInSeconds | Tempo estimado de viagem calculado como se não houvesse atrasos na rota devido a condições de tráfego, por exemplo, devido ao congestionamento |
| historicTrafficTravelTimeInSeconds | Tempo estimado de viagem calculado usando dados de tráfego históricos dependentes de tempo |
| liveTrafficIncidentsTravelTimeInSeconds | Tempo estimado de viagem calculado usando dados de velocidade em tempo real |

As próximas seções demonstram como fazer chamadas para as APIs de rota usando os parâmetros abordados.

### <a name="sample-query"></a>Exemplo de consulta

No primeiro exemplo abaixo, a hora de partida é definida para o futuro, no momento da gravação.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=51.368752,-0.118332:51.385426,-0.128929&travelMode=car&traffic=true&departAt=2025-03-29T08:00:20&computeTravelTimeFor=all
```

A resposta contém um elemento Summary, como o mostrado abaixo. Como a hora de partida é definida para o futuro, o valor de **trafficDelayInSeconds** é zero. O valor de **travelTimeInSeconds** é calculado usando dados de tráfego históricos dependentes de tempo. Portanto, nesse caso, o valor de **travelTimeInSeconds** é igual ao valor de **historicTrafficTravelTimeInSeconds** .

```json
"summary": {
    "lengthInMeters": 2131,
    "travelTimeInSeconds": 248,
    "trafficDelayInSeconds": 0,
    "departureTime": "2025-03-29T08:00:20Z",
    "arrivalTime": "2025-03-29T08:04:28Z",
    "noTrafficTravelTimeInSeconds": 225,
    "historicTrafficTravelTimeInSeconds": 248,
    "liveTrafficIncidentsTravelTimeInSeconds": 248
},
```

### <a name="sample-query"></a>Exemplo de consulta

No segundo exemplo abaixo, temos uma solicitação de roteamento em tempo real, em que o tempo de partida agora é. Ele não é especificado explicitamente na URL porque é o valor padrão.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=47.6422356,-122.1389797:47.6641142,-122.3011268&travelMode=car&traffic=true&computeTravelTimeFor=all
```

A resposta contém um resumo, conforme mostrado abaixo. Devido a congestionamentos, o valor de **trafficDelaysInSeconds** é maior que zero. Ele também é maior que **historicTrafficTravelTimeInSeconds**.

```json
"summary": {
    "lengthInMeters": 16637, 
    "travelTimeInSeconds": 2905, 
    "trafficDelayInSeconds": 1604, 
    "departureTime": "2020-02-28T01:00:20+00:00",
    "arrivalTime": "2020-02-28T01:48:45+00:00", 
    "noTrafficTravelTimeInSeconds": 872, 
    "historicTrafficTravelTimeInSeconds": 1976, 
    "liveTrafficIncidentsTravelTimeInSeconds": 2905 
},
```

## <a name="request-route-and-leg-details"></a>Detalhes da rota e do segmento da solicitação

Por padrão, o serviço de rota retornará uma matriz de coordenadas. A resposta conterá as coordenadas que compõem o caminho em uma lista chamada `points` . A resposta de rota também inclui a distância desde o início da rota e o tempo decorrido estimado. Esses valores podem ser usados para calcular a velocidade média de toda a rota.

A imagem a seguir mostra o `points` elemento.

![Elemento Points](media/how-to-use-best-practices-for-routing/points-list-is-hidden-img.png)

Expanda o `point` elemento para ver a lista de coordenadas para o caminho:

![Elemento de pontos expandidos](media/how-to-use-best-practices-for-routing/points-list-img.png)

As APIs de direção de rota dão suporte a formatos diferentes de instruções que podem ser usadas especificando o parâmetro de **instruções** . Para formatar instruções para facilitar o processamento do computador, use o **instruçõestype = codificado**. Use o **instruçõestype = marcado** para exibir instruções como texto para o usuário. Além disso, as instruções podem ser formatadas como texto em que alguns elementos das instruções são marcados e a instrução é apresentada com formatação especial. Para obter mais informações, consulte a [lista de tipos de instrução com suporte](/rest/api/maps/route/postroutedirections#routeinstructionstype).

Quando as instruções são solicitadas, a resposta retorna um novo elemento chamado `guidance` . O `guidance` elemento contém duas informações: instruções ativas e instruções resumidas.

![Tipo de instruções](media/how-to-use-best-practices-for-routing/instructions-type-img.png)

O `instructions` elemento contém orientações viradas para a viagem e o `instructionGroups` tem instruções resumidas. Cada Resumo de instrução abrange um segmento da corrida que pode abranger várias estradas. As APIs podem retornar detalhes para as seções de uma rota. como, o intervalo de coordenadas de uma obstrução de tráfego ou a velocidade atual do tráfego.

![Ativar/desativar instruções](media/how-to-use-best-practices-for-routing/instructions-turn-by-turn-img.png)

![Instruções resumidas](media/how-to-use-best-practices-for-routing/instructions-summary-img.png)

## <a name="request-a-route-for-a-commercial-vehicle"></a>Solicitar uma rota para um veículo comercial

As APIs de roteamento do Azure Maps dão suporte ao roteamento comercial de veículo, cobrindo o roteamento de caminhões comercial. As APIs consideram os limites especificados. Como, a altura e o peso do veículo, e se o veículo estiver realizando uma carga perigosa. Por exemplo, se um veículo estiver carregando flammable, o mecanismo de roteamento evitará determinados túneis próximos a áreas residenciais.

### <a name="sample-query"></a>Exemplo de consulta

A solicitação de exemplo abaixo consulta uma rota para um caminhão comercial. O caminhão está carregando a classe 1 material de desperdício perigoso.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass1&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

A API de rota retorna direções que acomodam as dimensões do caminhão e o desperdício perigoso. Você pode ler as instruções de rota expandindo o `guidance` elemento.

![Caminhão com classe 1 hazwaste](media/how-to-use-best-practices-for-routing/truck-with-hazwaste-img.png)

### <a name="sample-query"></a>Exemplo de consulta

Alterar a classe US Hazmat, da consulta acima, resultará em uma rota diferente para acomodar essa alteração.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass9&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

A resposta abaixo é para um caminhão que tem um material perigoso de classe 9, que é menos perigoso do que um material perigoso de classe 1. Quando você expandir o `guidance` elemento para ler as direções, observará que as direções não são as mesmas. Há mais instruções de rota para o material perigoso da classe de transporte de um caminhão.



![Caminhão com classe 9 hazwaste](media/how-to-use-best-practices-for-routing/truck-with-hazwaste9-img.png)



## <a name="request-traffic-information-along-a-route"></a>Solicitar informações de tráfego ao longo de uma rota

Com as APIs de direção de rota do Azure Maps, os desenvolvedores podem solicitar detalhes para cada tipo de seção, incluindo o `sectionType` parâmetro na solicitação. Por exemplo, você pode solicitar as informações de velocidade para cada segmento de obstrução de tráfego. Consulte a [lista de valores para a chave de sectiontype](/rest/api/maps/route/getroutedirections#sectiontype) para saber mais sobre os vários detalhes que você pode solicitar.

### <a name="sample-query"></a>Exemplo de consulta

A consulta a seguir define o `sectionType` para `traffic` . Ele solicita as seções que contêm informações de tráfego de Seattle a San Diego.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&sectionType=traffic&query=47.6062,-122.3321:32.7157,-117.1611
```

A resposta contém as seções que são adequadas para o tráfego durante as coordenadas fornecidas.

![Seções de tráfego](media/how-to-use-best-practices-for-routing/traffic-section-type-img.png)

Essa opção pode ser usada para colorir as seções ao renderizar o mapa, como na imagem abaixo: 

![Seções coloridas renderizadas no mapa](media/how-to-use-best-practices-for-routing/show-traffic-sections-img.png)

## <a name="calculate-and-optimize-a-multi-stop-route"></a>Calcular e otimizar uma rota de parada múltipla

Atualmente, o Azure Maps fornece duas formas de otimizações de rota:

* Otimizações baseadas no tipo de rota solicitado, sem alterar a ordem de Marcos. Você pode encontrar os [tipos de rota com suporte aqui](/rest/api/maps/route/postroutedirections#routetype)

* Otimização de vendas de viagens, que altera a ordem do Marcos para obter a melhor ordem para visitar cada parada

Para o roteamento de várias interrupções, até 150 marcos pode ser especificado em uma única solicitação de rota. Os locais de coordenadas inicial e final podem ser os mesmos, como seria o caso com uma viagem de ida e volta. Mas você precisa fornecer pelo menos um waypoint adicional para fazer o cálculo da rota. Marcos pode ser adicionado à consulta entre as coordenadas de origem e destino.

Se você quiser otimizar a melhor ordem para visitar o Marcos fornecido, será necessário especificar **computeBestOrder = true**. Esse cenário também é conhecido como o problema de otimização do vendedor de viagem.

### <a name="sample-query"></a>Exemplo de consulta

A consulta a seguir solicita o caminho para seis Marcos, com o `computeBestOrder` parâmetro definido como `false` . Também é o valor padrão para o `computeBestOrder` parâmetro.

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=false&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

A resposta descreve o comprimento do caminho para 140.851 metros e que levaria 9.991 segundos para viajar esse caminho.

![Resposta não otimizada](media/how-to-use-best-practices-for-routing/non-optimized-response-img.png)

A imagem abaixo ilustra o caminho resultante desta consulta. Esse caminho é uma rota possível. Não é o caminho ideal com base no tempo ou na distância.

![Imagem não otimizada](media/how-to-use-best-practices-for-routing/non-optimized-image-img.png)



Esta ordem de waypoint de rota é: 0, 1, 2, 3, 4, 5 e 6.

### <a name="sample-query"></a>Exemplo de consulta

A consulta a seguir solicita o caminho para os mesmos seis Marcos, como no exemplo acima. Desta vez, o `computeBestOrder` parâmetro definido como `true` (a otimização do vendedor de viagem).

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=true&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

A resposta descreve o comprimento do caminho para 91.814 metros e que levaria 7.797 segundos para viajar esse caminho. A distância de viagem e o tempo de viagem são inferiores aqui porque a API retornou a rota otimizada.

![Resposta otimizada](media/how-to-use-best-practices-for-routing/optimized-response-img.png)

A imagem abaixo ilustra o caminho resultante desta consulta.

![Imagem otimizada](media/how-to-use-best-practices-for-routing/optimized-image-img.png)

A rota ideal tem a seguinte ordem de waypoint: 0, 5, 1, 2, 4, 3 e 6.

>[!TIP]
> As informações do pedido waypoint otimizado do serviço de roteamento fornecem um conjunto de índices. Eles excluem os índices de origem e de destino. Você precisa incrementar esses valores em 1 para considerar a origem. Em seguida, adicione seu destino ao final para obter a lista completa de waypoint ordenada.

## <a name="calculate-and-bias-alternative-routes-using-supporting-points"></a>Calcular e diferenciar rotas alternativas usando pontos de suporte

Você pode ter situações em que deseja reconstruir uma rota para calcular zero ou mais rotas alternativas para uma rota de referência. Por exemplo, talvez você queira mostrar rotas alternativas de clientes que passam pela sua loja de varejo. Nesse caso, você precisará inclinar um local usando pontos de suporte. Estas são as etapas para diferenciar um local:

1. Calcular uma rota como está e obter o caminho da resposta da rota
2. Use o caminho da rota para localizar os locais desejados ao longo ou próximo ao caminho da rota. Por exemplo, você pode usar a [API de ponto de interesse](/rest/api/maps/search/getsearchpoi) do Azure Maps ou consultar seus próprios dados no banco de dado.  
3. Ordenar os locais com base na distância desde o início da rota
4. Adicione esses locais como pontos de suporte em uma nova solicitação de rota para a [API de direções de rota post](/rest/api/maps/route/postroutedirections). Para saber mais sobre os pontos de suporte, consulte a [documentação da API postar direções de rota](/rest/api/maps/route/postroutedirections#supportingpoints). 

Ao chamar a [API de direções de rota post](/rest/api/maps/route/postroutedirections), você pode definir o tempo mínimo de desvio ou as restrições de distância, juntamente com os pontos de suporte. Use esses parâmetros se desejar oferecer rotas alternativas, mas você também deseja limitar o tempo de viagem. Quando essas restrições forem usadas, as rotas alternativas seguirão a rota de referência do ponto de origem para o tempo ou a distância especificada. Em outras palavras, as outras rotas divergem da rota de referência de acordo com as restrições determinadas.

A imagem abaixo é um exemplo de processamento de rotas alternativas com limites de desvios especificados para a hora e a distância.

![Rotas alternativas](media/how-to-use-best-practices-for-routing/alternative-routes-img.png)

## <a name="use-the-routing-service-in-a-web-app"></a>Usar o serviço de roteamento em um aplicativo Web

O SDK da Web do Azure Maps fornece um [módulo de serviço](/javascript/api/azure-maps-rest/). Esse módulo é uma biblioteca auxiliar que torna mais fácil usar as APIs REST do Azure Maps em aplicativos Web ou Node.js, usando JavaScript ou TypeScript. O módulo de serviço pode ser usado para renderizar as rotas retornadas no mapa. O módulo determina automaticamente qual API usar com solicitações GET e POST.

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte:

> [!div class="nextstepaction"]
> [Serviço Roteiros do Azure Mapas](/rest/api/maps/route)

> [!div class="nextstepaction"]
> [Como usar o módulo de serviço](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Mostrar rota no mapa](./map-route.md)

> [!div class="nextstepaction"]
> [Pacote NPM do Azure Maps](https://www.npmjs.com/package/azure-maps-rest  )
