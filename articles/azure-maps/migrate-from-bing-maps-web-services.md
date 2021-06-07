---
title: 'Tutorial: Migrar serviços Web do Bing Mapas | Microsoft Azure Mapas'
description: Tutorial sobre como migrar serviços Web do Bing Maps para o Microsoft Azure Mapas.
author: rbrundritt
ms.author: richbrun
ms.date: 12/07/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 6024aae68183fbe02125ef4207e9fbce8abd6a2b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97679069"
---
# <a name="tutorial-migrate-web-service-from-bing-maps"></a>Tutorial: Migrar serviço Web do Bing Mapas

O Azure Mapas e o Bing Mapas dão acesso a APIs espaciais por meio de serviços Web REST. As interfaces de API para essas plataformas executam funcionalidades semelhantes, mas usam diferentes convenções de nomenclatura e objetos de resposta. Neste tutorial, você aprenderá a:

> * Geocódigo direto e reverso
> * Pesquisar pontos de interesse
> * Calcular rotas e instruções
> * Recuperar uma imagem do mapa
> * Calcular uma matriz de distância
> * Obter detalhes do fuso horário

A tabela a seguir apresenta as APIs de serviço do Azure Mapas que fornecem funcionalidade semelhante às APIs de serviço do Bing Mapas listadas.

| API de serviço do Bing Mapas                 | API de serviço do Azure Mapas      |
|---------------------------------------|-----------------------------|
| Sugestão automática                           | [Pesquisa](/rest/api/maps/search)     |
| Trajetos (incluindo caminhões)          | [Trajeto da rota](/rest/api/maps/route/getroutedirections)                          |
| Matriz de Distância                       | [Matrix de Rota](/rest/api/maps/route/postroutematrixpreview)                          |
| Imagens – Mapa estático                  | [Render](/rest/api/maps/render/getmapimage)                                   |
| Isócronos                            | [Intervalo de rotas](/rest/api/maps/route/getrouterange)                                    |
| Percepções Locais                        | [Pesquisar](/rest/api/maps/search) + [Intervalo de rotas](/rest/api/maps/route/getrouterange)    |
| Pesquisa local                          | [Pesquisa](/rest/api/maps/search)     |
| Reconhecimento de localização (POIs)           | [Pesquisa](/rest/api/maps/search)     |
| Localizações (geocódigo direto/reverso) | [Pesquisar](/rest/api/maps/search)                                               |
| Ajustar à estrada                          | [Trajeto da rota POST](/rest/api/maps/route/postroutedirections)                         |
| SDS (Serviços de Dados Espaciais)           | [Pesquisar](/rest/api/maps/search) + [Rota](/rest/api/maps/route) + outros Serviços do Azure |
| Fuso horário                             | [Fuso horário](/rest/api/maps/timezone)  |
| Incidentes de tráfego                     | [Detalhes do incidente de tráfego](/rest/api/maps/traffic/gettrafficincidentdetail)                     |
| Elevação                             | [Elevação (versão prévia)](/rest/api/maps/elevation)

As seguintes APIs de serviço não estão disponíveis atualmente no Azure Mapas:

-   Rotas de itinerário otimizadas – planejado. A API de Rota do Azure Mapas dá suporte à otimização de caixeiro viajante para apenas um veículo.
-   Metadados de imagens – usados principalmente para obter URLs de peça no Bing Mapas. O Azure Mapas tem um serviço autônomo para acessar diretamente as peças de mapa.

O Azure Mapas tem vários serviços Web REST adicionais que podem ser interessantes;

-   [Criador do Azure Mapas (versão prévia)](./creator-indoor-maps.md) – crie um gêmeo digital privado personalizado de edifícios e espaços.
-   [Operações espaciais](/rest/api/maps/spatial) – descarregue operações e cálculos espaciais complexos, como a delimitação geográfica, para um serviço.
-   [Peças de Mapa](/rest/api/maps/render/getmaptile) – acesse peças de estradas e de imagens do Azure Mapas como peças de varredura e de vetor.
-   [Roteamento em lote](/rest/api/maps/route/postroutedirectionsbatchpreview) – permite que até 1.000 solicitações de rota sejam feitas em um só lote em um determinado período. As rotas são calculadas em paralelo no servidor para um processamento mais rápido.
-   [Fluxo de Tráfego](/rest/api/maps/traffic) – acesse dados de fluxo de tráfego em tempo real como peças de varredura e de vetor.
-   [API de Geolocalização (versão prévia)](/rest/api/maps/geolocation/getiptolocationpreview) – obtenha a localização de um endereço IP.
-   [Serviços de Clima](/rest/api/maps/weather) – tenha acesso a dados meteorológicos e de previsão em tempo real.

Confira também os seguintes guias de melhores práticas:

-   [Melhores práticas para pesquisar](./how-to-use-best-practices-for-search.md)
-   [Melhores práticas para roteamento](./how-to-use-best-practices-for-routing.md)

## <a name="prerequisites"></a>Pré-requisitos

1. Entre no [portal do Azure](https://portal.azure.com). Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
2. [Faça uma conta do Azure Mapas](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Obtenha uma chave de assinatura primária](quick-demo-map-app.md#get-the-primary-key-for-your-account), também conhecida como a chave primária ou a chave de assinatura. Para obter mais informações sobre a autenticação nos Azure Mapas, confira [Gerenciar a autenticação nos Azure Mapas](how-to-manage-authentication.md).

## <a name="geocoding-addresses"></a>Como geocodificar endereços

A geocodificação é o processo de converter um endereço (como `"1 Microsoft way, Redmond, WA"`) em uma coordenada (como longitude: -122.1298, latitude: 47,64005). As coordenadas então costumam ser usadas para posicionar um marcador em um mapa ou centralizar o mapa.

O Azure Mapas fornece vários métodos de geocodificação de endereços;

-   [Geocódigo de endereço em forma livre](/rest/api/maps/search/getsearchaddress): especifique uma cadeia de caracteres de endereço individual (como `"1 Microsoft way, Redmond, WA"`) e processe a solicitação imediatamente. Esse serviço é recomendado se você precisa geocodificar endereços individuais rapidamente.
-   [Geocódigo de endereço estruturado](/rest/api/maps/search/getsearchaddressstructured): Especifique as partes de um endereço (como o nome da rua, a cidade, o país e o CEP) e processe a solicitação imediatamente. Esse serviço é recomendado se você precisa geocodificar endereços individuais rapidamente e os dados já foram analisados nas respectivas partes de endereço individuais.
-   [Geocódigo de endereço de lote](/rest/api/maps/search/postsearchaddressbatchpreview): Crie uma solicitação contendo até 10 mil endereços e faça com que elas sejam processadas durante um período de tempo. Todos os endereços serão geocodificados em paralelo no servidor e, quando concluído, o conjunto de resultados completo poderá ser baixado. Esse serviço é recomendado para a geocodificação de conjuntos de dados grandes.
-   [Pesquisa difusa](/rest/api/maps/search/getsearchfuzzy): Essa API combina geocódigo com pesquisa de ponto de interesse. Essa API usa uma cadeia de caracteres com forma livre que pode ser um endereço, um local, um ponto de referência, um ponto de interesse ou uma categoria de ponto de interesse e processa a solicitação imediatamente. Essa API é recomendada para aplicativos em que os usuários podem pesquisar endereços ou pontos de interesse usando a mesma caixa de texto.
-   [Pesquisa de lote difusa](/rest/api/maps/search/postsearchfuzzybatchpreview): Crie uma solicitação contendo até 10 mil endereços, locais, pontos de referência ou pontos de interesse e faça com que eles sejam processados durante um período. Todos os dados serão processados em paralelo no servidor e, quando concluído, o conjunto de resultados completo poderá ser baixado.

As tabelas a seguir fazem referência cruzada dos parâmetros da API do Bing Mapas com os parâmetros da API comparável no Azure Mapas para a geocodificação de endereços estruturada e em forma livre.

**Localização por endereço (endereço estruturado)**

| Parâmetro da API do Bing Mapas              | Parâmetro comparável da API do Azure Mapas                 |
|--------------------------------------|-----------------------------------------------------|
| `addressLine`                      | `streetNumber`, `streetName` ou `crossStreet` |
| `adminDistrict`                    | `countrySubdivision`                              |
| `countryRegion`                    | `country` e `countryCode`                     |
| `locality`                         | `municipality` ou `municipalitySubdivision`     |
| `postalCode`                       | `postalCode`                                      |
| `maxResults` (`maxRes`)          | `limit`                                           |
| `includeNeighborhood` (`inclnb`) | N/D – sempre retornado pelo Azure Mapas, se disponível.   |
| `include` (`incl`)               | N/D – Código de País ISO2 sempre retornado pelo Azure Mapas. |
| `key`                              | `subscription-key` – confira também a documentação [Autenticação com o Azure Mapas](./azure-maps-authentication.md). |
| `culture` (`c`)                  | `language` – confira a documentação de [idiomas compatíveis](./supported-languages.md). |
| `userRegion` (`ur`)              | `view` – confira a documentação das [exibições com suporte](./supported-languages.md#azure-maps-supported-views). |

O Azure Mapas também dá suporte a:

* `countrySecondarySubdivision` – condado, distritos
* `countryTertiarySubdivision` – áreas nomeadas; burgos, cantões, comunas
* `ofs` – percorra os resultados em combinação com o parâmetro `maxResults`.

**Localização por consulta (cadeia de caracteres de endereço em forma livre)**

| Parâmetro da API do Bing Mapas              | Parâmetro comparável da API do Azure Mapas      |
|--------------------------------------|------------------------------------------|
| `query`                            | `query`                                |
| `maxResults` (`maxRes`)          | `limit`                                |
| `includeNeighborhood` (`inclnb`) | N/D – sempre retornado pelo Azure Mapas, se disponível.  |
| `include` (`incl`)               | N/D – Código de País ISO2 sempre retornado pelo Azure Mapas.  |
| `key`                              | `subscription-key` – confira também a documentação [Autenticação com o Azure Mapas](./azure-maps-authentication.md). |
| `culture` (`c`)                  | `language` – confira a documentação de [idiomas compatíveis](./supported-languages.md).  |
| `userRegion` (`ur`)              | `view` – confira a documentação das [exibições com suporte](./supported-languages.md#azure-maps-supported-views). |

O Azure Mapas também dá suporte a;

* `typeahead` – especifica se a consulta será interpretada como uma entrada parcial e se a pesquisa entrará no modo preditivo (preenchimento/sugestão automática).
* `countrySet` – uma lista separada por vírgulas de códigos de país ISO2 à qual limitar a pesquisa.
* `lat`/`lon`, `topLeft`/`btmRight`, `radius` – especifique o local e a área do usuário para tornar os resultados mais relevantes localmente.
* `ofs` – percorra os resultados em combinação com o parâmetro `maxResults`.

Um exemplo de como usar o serviço de pesquisa está documentado [aqui](./how-to-search-for-address.md). Examine a documentação de [melhores práticas para pesquisar](./how-to-use-best-practices-for-search.md).

## <a name="reverse-geocode-a-coordinate-find-a-location-by-point"></a>Reverter o código geográfico em uma coordenada (encontrar um local por ponto)

O geocódigo inverso é o processo de conversão de coordenadas geográficas (como longitude: -122,1298, latitude: 47.64005) em seu endereço aproximado (por exemplo, `"1 Microsoft way, Redmond, WA"`).

O Azure Mapas fornece vários métodos de geocódigo inverso;

-   [Geocodificador inverso de endereço](/rest/api/maps/search/getsearchaddressreverse): Especifique uma coordenada geográfica para obter seu endereço aproximado e processar a solicitação imediatamente.
-   [Geocodificador inverso de cruzamentos](/rest/api/maps/search/getsearchaddressreversecrossstreet): Especifique uma coordenada geográfica para obter informações de rua cruzada próximas (por exemplo, 1ª e principal) e processar a solicitação imediatamente.
-   [Geocodificador inverso de endereço de lote](/rest/api/maps/search/postsearchaddressreversebatchpreview): Crie uma solicitação contendo até 10 mil coordenadas e faça com que elas sejam processadas durante um período de tempo. Todos os dados serão processados em paralelo no servidor e, quando concluído, o conjunto de resultados completo poderá ser baixado.

A tabela a seguir faz referência cruzada dos parâmetros da API do Bing Mapas com os parâmetros comparáveis da API do Azure Mapas.

| Parâmetro da API do Bing Mapas              | Parâmetro comparável da API do Azure Mapas       |
|--------------------------------------|-------------------------------------------|
| `point`                              | `query`                                   |
| `includeEntityTypes`                 | `entityType` – confira a tabela de comparação de tipos de entidade abaixo.    |
| `includeNeighborhood` (`inclnb`)     | N/D – sempre retornado pelo Azure Mapas, se disponível.         |
| `include` (`incl`)                   | N/D – Código de País ISO2 sempre retornado pelo Azure Mapas.    |
| `key`                                | `subscription-key` – confira também a documentação [Autenticação com o Azure Mapas](./azure-maps-authentication.md). |
| `culture` (`c`)                      | `language` – confira a documentação de [idiomas compatíveis](./supported-languages.md).   |
| `userRegion` (`ur`)                  | `view` – confira a documentação das [exibições com suporte](./supported-languages.md#azure-maps-supported-views). |

Examine a documentação de [melhores práticas para pesquisar](./how-to-use-best-practices-for-search.md).

A API de geocódigo inverso do Azure Mapas tem alguns recursos adicionais não disponíveis no Bing Mapas que podem ser úteis integrar ao migrar seu aplicativo:

* Recupere dados de limite de velocidade.
* Recupere informações de uso de estrada: estrada local, arterial, acesso limitado, com rampa etc.
* O lado da rua em que a coordenada cai.

**Tabela de comparação de tipos de entidade**

A tabela a seguir faz a referência cruzada dos tipos de entidade do Bing Mapas com os nomes das propriedades equivalentes no Azure Mapas.

| Tipo de entidade do Bing Mapas | Tipo de entidade comparável do Azure Mapas               | Descrição                                |
|-----------------------|-------------------------------------------------|--------------------------------------------|
| `Address`             |                                                 | *Endereço*                                  |
| `Neighborhood`        | `Neighbourhood`                                 | *Vizinhança*                             |
| `PopulatedPlace`      | `Municipality` ou `MunicipalitySubdivision`     | *Cidade*, *Município ou subterritório* ou *Supercidade*     |
| `Postcode1`           | `PostalCodeArea`                                | *Código postal* ou *CEP*                |
| `AdminDivision1`      | `CountrySubdivision`                            | *Estado* ou *Província*                      |
| `AdminDivision2`      | `CountrySecondarySubdivison`                    | *Condado* ou *distritos*                    |
| `CountryRegion`       | `Country`                                       | *Nome do país*                             |
|                       | `CountryTertiarySubdivision`                    | *Burgos*, *Cantões*, *Comunas*          |

## <a name="get-location-suggestions-autosuggest"></a>Obter sugestões de localização (Sugestão Automática)

Várias das APIs de pesquisa do Azure Mapas dão suporte ao modo preditivo, que pode ser usado para cenários de sugestão automática. A API de [pesquisa difusa](/rest/api/maps/search/getsearchfuzzy) do Azure Mapas é a mais parecida com a API de Sugestão Automática do Bing Mapas. As seguintes APIs também dão suporte ao modo preditivo, adicione `&typeahead=true` à consulta;

-   [Geocódigo de endereço em forma livre](/rest/api/maps/search/getsearchaddress): especifique uma cadeia de caracteres de endereço individual (como `"1 Microsoft way, Redmond, WA"`) e processe a solicitação imediatamente. Esse serviço é recomendado se você precisa geocodificar endereços individuais rapidamente.
-   [Pesquisa difusa](/rest/api/maps/search/getsearchfuzzy): Essa API combina geocódigo com pesquisa de ponto de interesse. Essa API usa uma cadeia de caracteres com forma livre que pode ser um endereço, um local, um ponto de referência, um ponto de interesse ou uma categoria de ponto de interesse e processa a solicitação imediatamente. Essa API é recomendada para aplicativos em que os usuários podem pesquisar endereços ou pontos de interesse usando a mesma caixa de texto.
-   [Pesquisa de POI](/rest/api/maps/search/getsearchpoi): Pesquise pontos de interesse pelo nome. Por exemplo, `"starbucks"`.
-   [Pesquisa de categoria de POI](/rest/api/maps/search/getsearchpoicategory): Pesquise pontos de interesse pela categoria. Por exemplo, "restaurante".

## <a name="calculate-routes-and-directions"></a>Calcular rotas e instruções

O Azure Mapas pode ser usado para calcular rotas e direções. O Azure Mapas tem muitas das mesmas funcionalidades que o serviço de roteamento do Bing Mapas, como:

* horas de chegada e partida
* rotas de tráfego preditivas e em tempo real
* diferentes meios de transporte, como automóvel, a pé, de caminhão
* otimização de ordem do localizador (caixeiro viajante)

> [!NOTE]
> O Azure Mapas exige que todos os marcos sejam coordenadas. Os endereços precisarão ser geocodificados primeiro.

O serviço de roteamento do Azure Mapas fornece as seguintes APIs para calcular rotas:

-   [Calcular rota](/rest/api/maps/route/getroutedirections): Calcule uma rota e tenha a solicitação seja processada imediatamente. Essa API é compatível com solicitações GET e POST. Solicitações POST são recomendadas ao especificar um grande número de marcos ou ao usar muitas das opções de rota para garantir que a solicitação de URL não se torne longa demais e cause problemas.
-   [Rota de lote](/rest/api/maps/route/postroutedirectionsbatchpreview): Crie uma solicitação contendo até 1.000 solicitação de rota e faça com que elas sejam processadas durante um período de tempo. Todos os dados serão processados em paralelo no servidor e, quando concluído, o conjunto de resultados completo poderá ser baixado.
-   [Serviços Mobilidade (versão prévia) ](/rest/api/maps/mobility): Calcule rotas e instruções usando o trânsito público.

A tabela a seguir faz referência cruzada dos parâmetros da API do Bing Mapas com os parâmetros comparáveis da API do Azure Mapas.

| Parâmetro da API do Bing Mapas                                    | Parâmetro comparável da API do Azure Mapas               |
|------------------------------------------------------------|---------------------------------------------------|
| `avoid`                                                    | `avoid`                                           |
| `dateTime` (`dt`)                                          | `departAt` ou `arriveAt`                          |
| `distanceBeforeFirstTurn` (`dbft`)                         | N/D                                               |
| `distanceUnit` (`du`)                                      | N/A – o Azure Mapas usa apenas o sistema métrico.     |
| `heading` (`hd`)                                           | `vehicleHeading`                                  |
| `maxSolutions` (`maxSolns`)                                | `maxAlternatives`, `alternativeType`, `minDeviationDistance` e `minDeviationTime`  |
| `optimize` (`optwz`)                                       | `routeType` e `traffic`                         |
| `optimizeWaypoints` (`optWp`)                              | `computeBestOrder`                                |
| `routeAttributes` (`ra`)                                   | `instructionsType`                                |
| `routePathOutput` (`rpo`)                                  | `routeRepresentation`                             |
| `timeType` (`tt`)                                          | `departAt` ou `arriveAt`                          |
| `tolerances` (`tl`)                                        | N/D                                               |
| `travelMode`                                               | `travelMode`                                      |
| `waypoint.n` (`wp.n`) ou `viaWaypoint.n` (`vwp.n`)         | `query` – coordenadas no formato `lat0,lon0:lat1,lon1….`   |
| `key`                                                      | `subscription-key` – confira também a documentação [Autenticação com o Azure Mapas](./azure-maps-authentication.md). |
| `culture` (`c`)                                            | `language` – confira a documentação de [idiomas compatíveis](./supported-languages.md). |
| `userRegion` (`ur`)                                        | `view` – confira a documentação das [exibições com suporte](./supported-languages.md#azure-maps-supported-views). |

A API de roteamento do Azure Mapas também dá suporte ao roteamento de caminhão dentro da mesma API. A tabela a seguir faz referência cruzada dos parâmetros adicionais de roteamento de caminhão do Bing Mapas com os parâmetros comparáveis da API do Azure Mapas.

| Parâmetro da API do Bing Mapas                  | Parâmetro comparável da API do Azure Mapas        |
|------------------------------------------|--------------------------------------------|
| `dimensionUnit` (`dims`)                 | N/D – suporte apenas para dimensões em metros. |
| `weightUnit` (`wu`)                      | N/D – suporte apenas para pesos em quilogramas. |
| `vehicleHeight` (`height`)               | `vehicleHeight`                            |
| `vehicleWidth` (`width`)                 | `vehicleWidth`                             |
| `vehicleLength` (`vl`)                   | `vehicleLength`                            |
| `vehicleWeight` (`weight`)               | `vehicleWeight`                            |
| `vehicleAxles` (`axles`)                 | `vehicleAxelWeight`                        |
| `vehicleTrailers` (`vt`)                 | **N/A**                                    |
| `vehicleSemi` (`semi`)                   | `vehicleCommercial`                        |
| `vehicleMaxGradient` (`vmg`)             | **N/A**                                    |
| `vehicleMinTurnRadius` (`vmtr`)          | **N/A**                                    |
| `vehicleAvoidCrossWind` (`vacw`)         | **N/A**                                    |
| `vehicleAvoidGroundingRisk` (`vagr`)     | **N/A**                                    |
| `vehicleHazardousMaterials` (`vhm`)      | `vehicleLoadType`                          |
| `vehicleHazardousPermits` (`vhp`)        | `vehicleLoadType`                          |

> [!TIP]
> Por padrão, a API de rotas do Azure Mapas retorna apenas um resumo (distância e tempos) e as coordenadas para o caminho da rota. Use o parâmetro `instructionsType` para recuperar as instruções curva a curva. O parâmetro `routeRepresentation` pode ser usado para filtrar o resumo e o caminho da rota.

Confira a documentação de [Melhores práticas de roteamento](./how-to-use-best-practices-for-routing.md).

A API de roteamento do Azure Mapas tem muitos recursos adicionais não disponíveis no Bing Mapas que podem ser úteis integrar ao migrar seu aplicativo:

* Suporte para tipo de rota: mais curta, mais rápida, emocionante e mais econômica no consumo de combustível.
* Suporte para modos de viagem adicionais: bicicleta, ônibus, motocicleta, táxi, caminhão e van.
* Suporte para 150 marcos.
* Calcular vários tempos de viagem em uma solicitação; tráfego histórico, tráfego ao vivo, sem tráfego.
* Evite tipos de estrada adicionais: estradas com pista para veículos de alta ocupação, estradas não pavimentadas e estradas já usadas.
* Roteiros baseados na especificação do mecanismo. Calcule as rotas para veículos elétricos ou de combustão com base nas especificações de combustível/carga restantes e motor.
* Especifique a velocidade máxima do veículo.

## <a name="snap-coordinates-to-road"></a>Ajustar coordenadas à estrada

Há várias maneiras de ajustar as coordenadas às estradas no Azure Mapas.

* Use a API de direções de rota para ajustar as coordenadas a uma rota lógica ao longo da rede de estradas.
* Use o SDK Web do Azure Mapas para ajustar coordenadas individuais à estrada mais próxima nas peças de vetor.
* Use as peças de vetor do Azure Mapas diretamente para ajustar coordenadas individuais.

**Usando a API de direção de rota para ajustar coordenadas**

O Azure Mapas pode ajustar coordenadas a estradas usando a API de [direções de rota](/rest/api/maps/route/postroutedirections). Esse serviço pode ser usado para reconstruir uma rota lógica entre um conjunto de coordenadas e é comparável à API de Ajuste à Estrada do Bing Mapas.

Há duas maneiras diferentes de usar a API de direções de rota para ajustar coordenadas a estradas.

* Se houver 150 coordenadas ou menos, elas poderão ser passadas como localizadores na API de direções de rota GET. Com essa abordagem, dois tipos diferentes de dados ajustados podem ser recuperados: as instruções de rota vão conter os localizadores ajustados individuais, enquanto o caminho da rota terá um conjunto interpolado de coordenadas que preenchem o caminho completo entre as coordenadas.
* Se houver mais de 150 coordenadas, a API de direções de rota POST poderá ser usada. As coordenadas de início e de término devem ser passadas para o parâmetro de consulta, mas todas as coordenadas podem ser passadas para o parâmetro `supportingPoints` no corpo da solicitação POST e formatadas como uma coleção de pontos de geometria GeoJSON. Os únicos dados ajustados disponíveis usando essa abordagem serão o caminho de rota, que é um conjunto interpolado de coordenadas que preenche o caminho completo entre as coordenadas. [Veja um exemplo](https://azuremapscodesamples.azurewebsites.net/?sample=Snap%20points%20to%20logical%20route%20path) dessa abordagem usando o módulo de serviços no SDK Web do Azure Mapas.

A tabela a seguir faz referência cruzada dos parâmetros da API do Bing Mapas com os parâmetros comparáveis da API do Azure Mapas.

| Parâmetro da API do Bing Mapas    | Parâmetro comparável da API do Azure Mapas                                 |
|----------------------------|---------------------------------------------------------------------|
| `points`                   | `supportingPoints` – passe esses pontos para corpo da solicitação POST  |
| `interpolate`              | N/D                                                                 |
| `includeSpeedLimit`        | N/D                                                                 |
| `includeTruckSpeedLimit`   | N/D                                                                 |
| `speedUnit`                | N/D                                                                 |
| `travelMode`               | `travelMode`                                                        |
| `key`                      | `subscription-key` – confira também a documentação [Autenticação com o Azure Mapas](./azure-maps-authentication.md). |
| `culture` (`c`)            | `language` – confira a documentação de [idiomas compatíveis](./supported-languages.md).   |
| `userRegion` (`ur`)        | `view` – confira a documentação das [exibições com suporte](./supported-languages.md#azure-maps-supported-views).   |

A API de roteamento do Azure Mapas também dá suporte ao parâmetro de roteamento de caminhão na mesma API, a fim de garantir que caminhos lógicos sejam calculados. A tabela a seguir faz referência cruzada dos parâmetros adicionais de roteamento de caminhão do Bing Mapas com os parâmetros comparáveis da API do Azure Mapas.

| Parâmetro da API do Bing Mapas                 | Parâmetro comparável da API do Azure Mapas        |
|-----------------------------------------|--------------------------------------------|
| `dimensionUnit` (`dims`)                | N/D – suporte apenas para dimensões em metros. |
| `weightUnit` (`wu`)                     | N/D – suporte apenas para pesos em quilogramas. |
| `vehicleHeight` (`height`)              | `vehicleHeight`                            |
| `vehicleWidth` (`width`)                | `vehicleWidth`                             |
| `vehicleLength` (`vl`)                  | `vehicleLength`                            |
| `vehicleWeight` (`weight`)              | `vehicleWeight`                            |
| `vehicleAxles` (`axles`)                | `vehicleAxelWeight`                        |
| `vehicleTrailers` (`vt`)                | **N/A**                                    |
| `vehicleSemi` (`semi`)                  | `vehicleCommercial`                        |
| `vehicleMaxGradient` (`vmg`)            | **N/A**                                    |
| `vehicleMinTurnRadius` (`vmtr`)         | **N/A**                                    |
| `vehicleAvoidCrossWind` (`vacw`)        | **N/A**                                    |
| `vehicleAvoidGroundingRisk` (`vagr`)    | **N/A**                                    |
| `vehicleHazardousMaterials` (`vhm`)     | `vehicleLoadType`                          |
| `vehicleHazardousPermits` (`vhp`)       | `vehicleLoadType`                          |

Como essa abordagem usa a API de direções de rota, o conjunto completo de opções nesse serviço pode ser usado para personalizar a lógica usada para ajustar a coordenada às estradas. Por exemplo, especificar um horário de partida faria com que dados de tráfego históricos fossem levados em consideração.

Atualmente, a API de direções de rota do Azure Mapas não retorna dados de limite de velocidade, mas esses dados podem ser recuperados usando a API de geocodificação inversa do Azure Mapas.

**Usando o SDK Web para ajustar coordenadas**

O SDK Web do Azure Mapas usa peças de vetor para renderizar os mapas. Essas peças de vetor contêm as informações brutas de geometria da estrada e podem ser usados para calcular a estrada mais próxima a uma coordenada para fazer o ajuste simples de coordenadas individuais. Isso é útil quando você deseja que as coordenadas apareçam visualmente sobre as estradas e já está usando o SDK Web do Azure Mapas para visualizar os dados.

No entanto, essa abordagem só fará o ajuste aos segmentos de estrada carregados na exibição do mapa. Ao reduzir para o nível do país, pode não haver dados de estrada, portanto, o encaixe não pode ser feito. No entanto, nesse nível de zoom, um só pixel pode representar a área de vários blocos da cidade e, portanto, o encaixe não é necessário. Para resolver isso, a lógica de ajuste pode ser aplicada toda vez que o mapa terminar de ser movido. [Veja um exemplo de código](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Basic%20snap%20to%20road%20logic) que demonstra isso.

**Usando as peças de vetor do Azure Mapas diretamente para ajustar coordenadas**

As peças de vetor do Azure Mapas contêm as informações brutas de geometria da estrada, que podem ser usados para calcular o ponto mais próximo em uma estrada a uma coordenada, para fazer o ajuste simples de coordenadas individuais. Todos os segmentos de estrada aparecem nos setores no nível de zoom 15, portanto, você desejará recuperar as peças começando nesse nível. Em seguida, você pode usar a [matemática da pirâmide de peças quadtree](./zoom-levels-and-tile-grid.md) para determinar quais peças são necessárias e converter as peças em geometrias. Em seguida, uma biblioteca de matemática espacial, como [turf js](http://turfjs.org/) ou [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite), pode ser usada para calcular os segmentos de linha mais próximos.

## <a name="retrieve-a-map-image-static-map"></a>Recuperar uma imagem do mapa (Mapa Estático)

O Azure Mapas fornece uma API para renderizar as imagens de mapa estático contendo dados sobrepostos. A API [Renderização de imagem do mapa](/rest/api/maps/render/getmapimagerytile) do Azure Mapas é comparável à API de mapa estático no Bing Mapas.

> [!NOTE]
> O Azure Mapas exige que o centro, todos os marcadores e locais de caminho sejam coordenadas no formato `longitude,latitude`, enquanto o Bing Mapas usa o formato `latitude,longitude`.</p>
<p>Os endereços precisarão ser geocodificados primeiro.

A tabela a seguir faz referência cruzada dos parâmetros da API do Bing Mapas com os parâmetros comparáveis da API do Azure Mapas.

| Parâmetro da API do Bing Mapas  | Parâmetro comparável da API do Azure Mapas            |
|--------------------------|------------------------------------------------|
| `centerPoint`            | `center`                                       |
| `format`                 | `format` – especificado como parte do caminho da URL. No momento, há suporte apenas para PNG.  |
| `heading`                | N/D – não há suporte para Streetside.                |
| `imagerySet`             | `layer` e `style` – confira a documentação de [Estilos de mapa compatíveis](./supported-map-styles.md).   |
| `mapArea` (`ma`)         | `bbox`                                         |
| `mapLayer` (`ml`)        | N/D                                            |
| `mapSize` (`ms`)         | `width` e `height` – pode ter um tamanho de até 8192x8192. |
| `declutterPins` (`dcl`)  | N/D                                            |
| `dpi`                    | N/D                                            |
| `drawCurve`              | `path`                                         |
| `mapMetadata`            | N/D                                            |
| `pitch`                  | N/D – não há suporte para Streetside.                |
| `pushpin` (`pp`)         | `pins`                                         |
| `zoomLevel`              | `zoom`                                         |
| `query`                  | N/A – o centro ou a caixa delimitadora deve ser usada.     |
| `highlightEntity` (`he`) | N/D                                            |
| `style`                  | N/D                                            |
| parâmetros de rota         | N/D                                            |
| `key`                    | `subscription-key` – confira também a documentação [Autenticação com o Azure Mapas](./azure-maps-authentication.md). |
| `culture` (`c`)          | `language` – confira a documentação de [idiomas compatíveis](./supported-languages.md).   |
| `userRegion` (`ur`)      | `view` – confira a documentação das [exibições com suporte](./supported-languages.md#azure-maps-supported-views). |

> [!NOTE]
> O Azure Mapas usa um sistema com peças que são duas vezes o tamanho das peças de mapa usadas no Bing Mapas. Assim, o valor do nível de zoom no Azure Mapas aparecerá um nível de zoom mais próximo no Azure Mapas em comparação ao Bing Mapas. Reduza o nível de zoom em um nas solicitações que você está migrando para compensar isso.

Confira o [Guia de instruções sobre a API de renderização de imagem do mapa](./how-to-render-custom-data.md) para obter mais informações.

Além de poder gerar uma imagem de mapa estático, o serviço de renderização do Azure Mapas também permite acessar diretamente os blocos de mapa em formato de varredura (PNG) e vetor:

-   [Peça de mapa](/rest/api/maps/render/getmaptile) – recupere peças de varredura (PNG) e de vetor para os mapas base (estradas, limites, segundo plano).
-   [Peça de imagem de mapa](/rest/api/maps/render/getmapimagerytile) – recupere peças de imagem aérea e de satélite.

### <a name="pushpin-url-parameter-format-comparison"></a>Comparação de formato de parâmetro de URL do marcador

**Antes: Bing Mapas**

No Bing Mapas, é possível adicionar marcadores a uma imagem de mapa estático usando o parâmetro `pushpin` na URL. O parâmetro `pushpin` usa um local no formato `latitude,longitude`, um estilo de ícone e um rótulo de texto (até três caracteres), conforme mostrado abaixo:

> `&pushpin=latitude,longitude;iconStyle;label`

Marcadores adicionais podem ser usados adicionando parâmetros `pushpin` extras à URL com um conjunto de valores diferentes. Os estilos de ícone de marcador são limitados a um dos estilos predefinidos disponíveis na API do Bing Mapas.

Por exemplo, no Bing Mapas, um marcador vermelho com o rótulo "AB" pode ser adicionado ao mapa nas coordenadas (longitude:-110, latitude: 45) com o seguinte parâmetro de URL:

> `&pushpin=45,-110;7;AB`

![Marcador de mapa estático do Bing Mapas](media/migrate-bing-maps-web-service/bing-maps-static-map-pin.jpg)

**Depois: Azure Mapas**

No Azure Mapas, os marcadores também podem ser adicionados a uma imagem de mapa estático especificando o parâmetro `pins` na URL. Os marcadores no Azure Mapas são definidos especificando um estilo de ícone e uma lista de locais que usam esse estilo de ícone. Essas informações são passadas para o parâmetro `pins`, pode ser especificado várias vezes para dar suporte a marcadores com estilos diferentes.

> `&pins=iconType|pinStyles||pinLocation1|pinLocation2|...`

Estilos adicionais podem ser usados adicionando parâmetros `pins` extras à URL com um estilo e um conjunto de locais diferentes.

Quando se trata de localizações de marcador, o Azure Mapas exige que as coordenadas estejam no formato `longitude latitude`, enquanto o Bing Mapas usa o formato `latitude,longitude`. Observe também que **há um espaço, não uma vírgula**, separando a longitude e a latitude no Azure Mapas.

O valor `iconType` especifica o tipo de marcador a ser criado e pode ter os seguintes valores:

* `default` – o ícone de marcação padrão.
* `none` – nenhum ícone é exibido, somente os rótulos serão renderizados.
* `custom` – especifica que um ícone personalizado deve ser usado. Uma URL que aponta para a imagem do ícone pode ser adicionada ao final do parâmetro `pins` após as informações de localização do marcador.
* `{udid}` – uma UDID (ID de dados exclusiva) para um ícone armazenado na plataforma de armazenamento de dados do Azure Mapas.

Os estilos de marcador no Azure Mapas são adicionados com o formato `optionNameValue`, com vários estilos separados por caracteres de barra vertical (`|`) como este `iconType|optionName1Value1|optionName2Value2`. Observe que os nomes e os valores de opção não são separados. Os seguintes nomes de opção de estilo podem ser usados para os estilos de marcador no Azure Mapas:

* `al` – especifica a opacidade (alfa) dos marcadores. Pode ser um número entre 0 e 1.
* `an` – especifica a âncora do marcador. Valores de pixel x e y especificados no formato `x y`.
* `co` – a cor do marcador. Deve ser uma cor hexadecimal de 24 bits: `000000` a `FFFFFF`.
* `la` – especifica a âncora do rótulo. Valores de pixel x e y especificados no formato `x y`.
* `lc` – a cor do rótulo. Deve ser uma cor hexadecimal de 24 bits: `000000` a `FFFFFF`.
* `ls` – o tamanho do rótulo em pixels. Pode ser um número maior que 0.
* `ro` – um valor em graus para girar o ícone. Pode ser um número entre -360 e 360.
* `sc` – um valor de escala para o ícone de marcador. Pode ser um número maior que 0.

Os valores de rótulo são especificados para cada localização do marcador, em vez de ter um valor de rótulo que se aplica a todos os marcadores na lista de locais. O valor do rótulo pode ser uma cadeia de vários caracteres e ser encapsulado entre aspas simples para garantir que não seja confundido como um valor de estilo ou localização.

Por exemplo, no Azure Mapas, adicionar um ícone padrão (`FF0000`) vermelho com o rótulo "Agulha de Espaço" posicionado abaixo (15 50) do ícone em coordenadas (longitude:-122,349300, latitude: 47,620180) pode ser feito com o seguinte parâmetro de URL:

> `&pins=default|coFF0000|la15 50||'Space Needle'-122.349300 47.620180`

![Marcador de mapa estático do Azure Mapas](media/migrate-bing-maps-web-service/azure-maps-static-map-pin.jpg)

O exemplo a seguir adiciona três alfinetes com os valores de rótulo '1', '2' e '3':

> `&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12`

![Mapa estático do Azure Mapas com vários marcadores](media/migrate-bing-maps-web-service/azure-maps-static-map-multiple-pins.jpg)

### <a name="draw-curve-url-parameter-format-comparison"></a>Comparação de formato de parâmetro de URL para desenhar curva

**Antes: Bing Mapas**

No Bing Mapas, é possível adicionar linhas e polígonos a uma imagem de mapa estático usando o parâmetro `drawCurve` na URL. O parâmetro `drawCurve` usa um tipo de forma, um tipo de estilo e uma lista de localizações a serem renderizados no mapa, conforme mostrado abaixo:

> `&drawCurve=shapeType,styleType,location1,location2...`

Estilos adicionais podem ser usados adicionando parâmetros `drawCurve` extras à URL com um estilo e um conjunto de locais diferentes.

Os locais no Bing Mapas são especificados com o formato `latitude1,longitude1_latitude2,longitude2_…`. Os locais também podem ser codificados.

Os tipos de forma no Bing Mapas incluem linhas, polígonos, círculo e curva. Os tipos de estilo incluem cor da linha, espessura da linha, cor da estrutura de tópicos, cor de preenchimento, espessura do contorno e raio circular.

Por exemplo, no Bing Mapas, uma linha azul com opacidade de 50% e uma espessura de quatro pixels pode ser adicionada ao mapa entre coordenadas (longitude: -110, latitude: 45 e longitude: -100, latitude: 50) com o seguinte parâmetro de URL:

`&drawCurve=l,FF000088,4;45,-110_50,-100`

![Linha de mapa estático do Bing Mapas](media/migrate-bing-maps-web-service/bing-maps-static-map-line.jpg)

**Depois: Azure Mapas**

No Azure Mapas, linhas e polígonos também podem ser adicionados a uma imagem de mapa estático especificando o parâmetro *path* na URL. Assim como o Bing Mapas, um estilo e uma lista de locais podem ser especificados nesse parâmetro, sendo que o parâmetro *path* pode ser especificado várias vezes para renderizar vários círculos, linhas e polígonos com estilos diferentes.

> `&path=pathStyles||pathLocation1|pathLocation2|...`

Quando se trata de localizações de caminho, o Azure Mapas exige que as coordenadas estejam no formato `longitude latitude`, enquanto o Bing Mapas usa o formato `latitude,longitude`. Observe também que **há um espaço, não uma vírgula**, separando a longitude e a latitude no Azure Mapas. Atualmente, o Azure Mapas não dá suporte a caminhos codificados. Conjuntos de dados maiores podem ser carregados como preenchimentos GeoJSON na API de Armazenamento de Dados do Azure Mapas, conforme documentado [aqui](./how-to-render-custom-data.md#get-data-from-azure-maps-data-storage).

Os estilos de caminho no Azure Mapas são adicionados com o formato `optionNameValue`, com vários estilos separados por caracteres de barra vertical (`|`) como este `optionName1Value1|optionName2Value2`. Observe que os nomes e os valores de opção não são separados. Os seguintes nomes de opção de estilo podem ser usados para caminhos de estilo no Azure Mapas:

* `fa` – a opacidade da cor de preenchimento (alfa) usada ao renderizar polígonos. Pode ser um número entre 0 e 1.
* `fc` – a cor de preenchimento usada para renderizar a área de um polígono.
* `la` – a linha de opacidade (alfa) usada ao renderizar linhas e o contorno de polígonos. Pode ser um número entre 0 e 1.
* `lc` – a cor da linha usada para renderizar linhas e o contorno de polígonos.
* `lw` – a largura da linha em pixels.
* `ra` – especifica um raio de círculos em metros.

Por exemplo, no Azure Mapas, uma linha azul com opacidade de 50% e uma espessura de quatro pixels pode ser adicionada ao mapa entre coordenadas (longitude: -110, latitude: 45 e longitude: -100, latitude: 50) com o seguinte parâmetro de URL:

> `&path=lc0000FF|la.5|lw4||-110 45|-100 50`

![Linha de mapa estático do Azure Mapas](media/migrate-bing-maps-web-service/azure-maps-static-map-line.jpg)

## <a name="calculate-a-distance-matrix"></a>Calcular uma matriz de distância

O Azure Mapas fornece uma API para calcular os tempos de viagem e as distâncias entre um conjunto de locais como uma matriz de distância. A API de matriz de distância do Azure Mapas é comparável à API de matriz de distância no Bing Mapas.

-   [Matriz de rota](/rest/api/maps/route/postroutematrixpreview): calcula de maneira assíncrona os tempos de viagem e as distâncias de um conjunto de origens e destinos. Há suporte para até 700 células por solicitação (o número de origens multiplicado pelo número de destinos). Com essa restrição em mente, exemplos de possíveis dimensões de matriz são: `700x1`, `50x10`, `10x10`, `28x25`, `10x70`.

> [!NOTE]
> Uma solicitação para a API de matriz de distância só pode ser feita usando uma solicitação POST com as informações de origem e destino no corpo da solicitação.</p>
<p>Além disso, o Azure Mapas requer que todas as origens e destinos sejam coordenadas. Os endereços precisarão ser geocodificados primeiro.

A tabela a seguir faz referência cruzada dos parâmetros da API do Bing Mapas com os parâmetros comparáveis da API do Azure Mapas.

| Parâmetro da API do Bing Mapas | Parâmetro comparável da API do Azure Mapas                         |
|-------------------------|-------------------------------------------------------------|
| `origins`               | `origins` – especifique no corpo da solicitação POST como GeoJSON.    |
| `destinations`          | `destination` – especifique no corpo da solicitação POST como GeoJSON. |
| `endTime`               | `arriveAt`                                                  |
| `startTime`             | `departAt`                                                  |
| `travelMode`            | `travelMode`                                                |
| `resolution`            | N/D                                                         |
| `distanceUnit`          | N/A – todas as distâncias em metros.                              |
| `timeUnit`              | N/A – todos os tempos em segundos.                                 |
| `key`                   | `subscription-key` – confira também a documentação [Autenticação com o Azure Mapas](./azure-maps-authentication.md). |
| `culture` (`c`)         | `language` – confira a documentação de [idiomas compatíveis](./supported-languages.md).  |
| `userRegion` (`ur`)     | `view` – confira a documentação das [exibições com suporte](./supported-languages.md#azure-maps-supported-views).     |

> [!TIP]
> Todas as opções de roteiros avançadas disponíveis na API de roteiros do Azure Mapas (roteiros de caminhão, especificações do motor, evitar etc.) são compatíveis com a API da matriz de distância do Azure Mapas.

## <a name="calculate-an-isochrone"></a>Calcular um isócrono

O Azure Mapas fornece uma API para calcular um isócrono, um polígono que cobre uma área que pode ser percorrida em qualquer direção, saindo de um ponto de origem, em um período especificado ou com uma quantidade de combustível/carga especificada. A API de intervalo de rotas do Azure Mapas é comparável à API de isócrono no Bing Mapas;

-   [Intervalo](/rest/api/maps/route/getrouterange) de rotas**: calcule um polígono que cobre uma área que pode ser percorrida em qualquer direção, saindo de um ponto de origem, em um período especificado, com uma distância especificada ou com uma quantidade de combustível/carga especificada disponível.

> [!NOTE]
> O Azure Mapas exige que a origem da consulta seja uma coordenada. Os endereços precisarão ser geocodificados primeiro.</p>
<p>Além disso, o Bing Mapas pode calcular isócronos com base no tempo ou na distância, enquanto o Azure Mapas pode calcular isócronos com base no tempo, na distância ou na quantidade de combustível/carga disponível.

A tabela a seguir faz referência cruzada dos parâmetros da API do Bing Mapas com os parâmetros comparáveis da API do Azure Mapas.

| Parâmetro da API do Bing Mapas      | Parâmetro comparável da API do Azure Mapas            |
|------------------------------|------------------------------------------------|
| `waypoint` (`wp`)            | `query`                                        |
| `dateTime` (`dt`)            | `departAt`                                     |
| `maxTime`                    | `timeBudgetInSec`                              |
| `timeUnit` (`tu`)            | N/A – todos os tempos em segundos.                    |
| `travelMode` (`mode`)        | `travelMode`                                   |
| `maxDistance` (`maxDis`)     | `distanceBudgetInMeters`                       |
| `distanceUnit` (`du`)        | N/A – todas as distâncias em metros.                 |
| `optimize` (`optmz`)         | `routeType`                                    |
| `key`                        | `subscription-key` – confira também a documentação [Autenticação com o Azure Mapas](./azure-maps-authentication.md). |
| `culture` (`c`)              | `language` – confira a documentação de [idiomas compatíveis](./supported-languages.md).  |
| `userRegion` (`ur`)          | `view` – confira a documentação das [exibições com suporte](./supported-languages.md#azure-maps-supported-views). |

> [!TIP]
> Todas as opções de roteiros avançadas disponíveis na API de roteiros do Azure Mapas (roteiros de caminhão, especificações do motor, evitar etc.) têm suporte da API de isócronos do Azure Mapas.

## <a name="search-for-points-of-interest"></a>Pesquisar pontos de interesse

Dados de ponto de interesse podem ser pesquisados no Bing Mapas usando as seguintes APIs:

-   **Pesquisa local:** pesquisa pontos de interesse próximos (pesquisa radial), por nome ou por tipo de entidade (categoria). As APIs de [Pesquisa de POI](/rest/api/maps/search/getsearchpoi) e de [Pesquisa de categoria de POI](/rest/api/maps/search/getsearchpoicategory) do Azure Mapas são as mais semelhantes a essa API.
-   **Reconhecimento de localização**: Pesquisa pontos de interesse dentro de uma determinada distância de uma localização. A API de [Pesquisa nas redondezas](/rest/api/maps/search/getsearchnearby) do Azure Mapas é a mais semelhante a essa API.
-   **Insights locais:** pesquisa pontos de interesse dentro de uma distância máxima ou de um tempo máximo de condução especificado com relação a uma coordenada específica. Para isso, o Azure Mapas calcula primeiro um isócrono e, em seguida, passa-o para a API [Pesquisar dentro da geometria](/rest/api/maps/search/postsearchinsidegeometry).

O Azure Mapas fornece várias APIs de pesquisa para pontos de interesse:

-   [Pesquisa de POI](/rest/api/maps/search/getsearchpoi): Pesquise pontos de interesse pelo nome. Por exemplo, `"starbucks"`.
-   [Pesquisa de categoria de POI](/rest/api/maps/search/getsearchpoicategory): Pesquise pontos de interesse pela categoria. Por exemplo, "restaurante".
-   [Pesquisa de proximidade](/rest/api/maps/search/getsearchnearby): Pesquisa pontos de interesse dentro de uma determinada distância de uma localização.
-   [Pesquisa difusa](/rest/api/maps/search/getsearchfuzzy): Essa API combina geocódigo com pesquisa de ponto de interesse. Essa API usa uma cadeia de caracteres com forma livre que pode ser um endereço, um local, um ponto de referência, um ponto de interesse ou uma categoria de ponto de interesse e processa a solicitação imediatamente. Essa API é recomendada para aplicativos em que os usuários podem pesquisar endereços ou pontos de interesse usando a mesma caixa de texto.
-   [Pesquisar dentro da geometria](/rest/api/maps/search/postsearchinsidegeometry): Pesquisar pontos de interesse dentro de uma geometria (polígono) especificada.
-   [Pesquisar ao longo da rota](/rest/api/maps/search/postsearchalongroute): Pesquise pontos de interesse que estejam ao longo de um caminho de rota especificado.
-   [Pesquisa de lote difusa](/rest/api/maps/search/postsearchfuzzybatchpreview): Crie uma solicitação contendo até 10 mil endereços, locais, pontos de referência ou pontos de interesse e faça com que eles sejam processados durante um período. Todos os dados serão processados em paralelo no servidor e, quando concluído, o conjunto de resultados completo poderá ser baixado.

Examine a documentação de [melhores práticas para pesquisar](./how-to-use-best-practices-for-search.md).

## <a name="get-traffic-incidents"></a>Obter incidentes de tráfego

O Azure Mapas fornece várias APIs para recuperar dados de tráfego. Há dois tipos de dados de tráfego disponíveis:

* **Dados de fluxo** – fornecem métricas sobre o fluxo do tráfego nas seções das estradas. Geralmente é usado para codificar as estradas por cor. Esses dados são atualizados a cada dois minutos.
* **Dados de incidentes** – fornecem dados sobre construções, fechamentos de estradas, acidentes e outros incidentes que podem afetar o tráfego. Esses dados são atualizados a cada minuto.

O Bing Mapas fornece dados de fluxo e de incidentes de tráfego em seus controles de mapa interativos e também disponibiliza dados de incidentes como um serviço.

Os dados de tráfego também são integrados aos controles de mapa interativos do Azure Mapas. O Azure Mapas também fornece as seguintes APIs de serviços de tráfego:

-   [Segmentos de fluxo de tráfego](/rest/api/maps/traffic/gettrafficflowsegment): fornece informações sobre as velocidades e os tempos de percurso do fragmento de estrada mais próximo às coordenadas fornecidas.
-   [Peças de fluxo de tráfego](/rest/api/maps/traffic/gettrafficflowtile): fornece peças de vetor e de varredura que contêm dados de fluxo de tráfego. Elas podem ser usadas com os controles do Azure Mapas ou em controles de mapa de terceiros, como o Leaflet. As peças de vetor também podem ser usadas para análise de dados avançada.
-   [Detalhes do incidente de tráfego](/rest/api/maps/traffic/gettrafficincidentdetail): fornece detalhes sobre os incidentes de tráfego que estão dentro de uma caixa delimitadora, de um nível de zoom e de um modelo de tráfego.
-   [Peças do incidente de tráfego](/rest/api/maps/traffic/gettrafficincidenttile): fornece peças de vetor e de varredura que contêm dados de incidentes de tráfego.
-   [Visor do incidente de tráfego](/rest/api/maps/traffic/gettrafficincidentviewport): recupera as informações legais e técnicas sobre o visor descrito na solicitação, como a ID do modelo de tráfego.

A tabela a seguir faz referência cruzada dos parâmetros da API de tráfego do Bing Mapas com os parâmetros comparáveis da API de detalhes de incidente de tráfego do Azure Mapas.

| Parâmetro da API do Bing Mapas  | Parâmetro comparável da API do Azure Mapas   |
|--------------------------|---------------------------------------|
| `mapArea`                | `boundingBox` e `boundingZoom`      |
| `includeLocationCodes`   | N/D                                   |
| `severity` (`s`)         | N/A – todos os dados retornados               |
| `type` (`t`)             | N/A – todos os dados retornados               |
| `key`                    | `subscription-key` – confira também a documentação [Autenticação com o Azure Mapas](./azure-maps-authentication.md). |
| `culture` (`c`)          | `language` – confira a documentação de [idiomas compatíveis](./supported-languages.md). |
| `userRegion` (`ur`)      | `view` – confira a documentação das [exibições com suporte](./supported-languages.md#azure-maps-supported-views). |

## <a name="get-a-time-zone"></a>Obter um fuso horário

O Azure Mapas fornece uma API para recuperar o fuso horário em que uma coordenada se encontra. A API de fuso horário do Azure Mapas é comparável à API de fuso horário no Bing Mapas:

-   [Fuso horário por coordenada](/rest/api/maps/timezone/gettimezonebycoordinates): Especifique uma coordenada e obtenha os detalhes do fuso horário em que ela está.

A tabela a seguir faz referência cruzada dos parâmetros da API do Bing Mapas com os parâmetros comparáveis da API do Azure Mapas.

| Parâmetro da API do Bing Mapas | Parâmetro comparável da API do Azure Mapas          |
|-------------------------|----------------------------------------------|
| `point`                 | `query`                                      |
| `query`                 | N/A – os locais precisam ser geocodificados primeiro.      |
| `dateTime`              | `timeStamp`                                  |
| `includeDstRules`       | N/A – sempre incluído na resposta do Azure Mapas. |
| `key`                   | `subscription-key` – confira também a documentação [Autenticação com o Azure Mapas](./azure-maps-authentication.md). |
| `culture` (`c`)         | `language` – confira a documentação de [idiomas compatíveis](./supported-languages.md).  |
| `userRegion` (`ur`)     | `view` – confira a documentação das [exibições com suporte](./supported-languages.md#azure-maps-supported-views).  |

Além disso, a plataforma Azure Mapas também fornece várias APIs de fuso horário adicionais para ajudar com conversões com nomes e IDs de fuso horário;

-   [Fuso horário por ID](/rest/api/maps/timezone/gettimezonebyid): Retorna informações de fuso horário atuais, históricas e futuras para a ID de fuso horário do IANA especificada.
-   [IANA de enumeração de fuso horário](/rest/api/maps/timezone/gettimezoneenumiana): Retorna uma lista completa de IDs de fuso horário do IANA. As atualizações ao serviço IANA são refletidas no sistema dentro de um dia. 
-   [Enumeração de fuso horário no Windows](/rest/api/maps/timezone/gettimezoneenumwindows): Retorna uma lista completa de IDs de fuso horário do Windows.
-   [Fuso horário versão IANA](/rest/api/maps/timezone/gettimezoneianaversion): Retorna o número de versão atual do IANA usado pelo Azure Mapas. 
-   [Fuso horário Windows para IANA](/rest/api/maps/timezone/gettimezonewindowstoiana): Retorna uma ID do IANA correspondente, dada uma ID de fuso horário do Windows válida. Várias IDs do IANA podem ser retornadas para uma ID do Windows individual.

## <a name="spatial-data-services-sds"></a>SDS (Serviços de Dados Espaciais)

Os serviços de dados espaciais no Bing Mapas fornecem três funcionalidades principais:

* Geocodificação em lote – processe um grande lote de geocódigos de endereços com apenas uma solicitação.
* Recuperar dados de limites administrativos – use uma coordenada e obtenha um limite de interseção para um tipo de entidade especificado.
* Hospedar e consultar dados espaciais comerciais – carregue uma simples tabela de dados 2D e acesse-a usando algumas consultas espaciais simples.

### <a name="batch-geocode-data"></a>Geocodificar dados em lote

A geocodificação em lote é o processo de pegar um grande número de endereços ou locais, passar todos eles em apenas uma solicitação para um serviço e fazer com que todos sejam geocodificados em paralelo e que os resultados sejam retornados em uma resposta.

O Bing Mapas permite que até 200.000 endereços sejam passados em uma solicitação de geocodificação em lote. Essa solicitação entra em uma fila e, geralmente, é processada por algum tempo, que pode ir de alguns minutos a algumas horas dependendo do tamanho do conjunto de dados e da carga no serviço. Cada endereço na solicitação gera uma transação.

O Azure Mapas tem um serviço de geocodificação em lote, mas ele permite que até 10.000 endereços sejam passados em uma solicitação e sejam processados em segundos a alguns minutos, dependendo do tamanho do conjunto de dados e da carga no serviço. Cada endereço na solicitação gera uma transação. No Azure Mapas, o serviço de geocodificação em lote está disponível apenas na camada S1.

Outra opção para geocodificar um grande número de endereços com o Azure Mapas é fazer solicitações paralelas às APIs de pesquisa padrão. Esses serviços aceitam apenas um endereço por solicitação, mas podem ser usados com o nível S0, que também fornece limites de uso gratuitos. O nível S0 permite fazer até 50 solicitações por segundo à plataforma do Azure Mapas usando apenas uma conta. Portanto, se o seu processamento ficar dentro desse limite, será possível geocodificar mais de 180.000 endereços por hora. O nível S1 não tem um limite documentado para o número de consultas por segundo que podem ser feitas de uma conta; portanto, muito mais dados podem ser processados mais rapidamente ao usar esse tipo de preço. No entanto, o uso do serviço de geocodificação em lote ajuda a reduzir a quantidade total de dados transferidos e a reduzir drasticamente o tráfego de rede.

-   [Geocódigo de endereço em forma livre](/rest/api/maps/search/getsearchaddress): especifique uma cadeia de caracteres de endereço individual (como `"1 Microsoft way, Redmond, WA"`) e processe a solicitação imediatamente. Esse serviço é recomendado se você precisa geocodificar endereços individuais rapidamente.
-   [Geocódigo de endereço estruturado](/rest/api/maps/search/getsearchaddressstructured): Especifique as partes de um endereço (como o nome da rua, a cidade, o país e o CEP) e processe a solicitação imediatamente. Esse serviço é recomendado se você precisa geocodificar endereços individuais rapidamente e os dados já foram analisados nas respectivas partes de endereço individuais.
-   [Geocódigo de endereço de lote](/rest/api/maps/search/postsearchaddressbatchpreview): Crie uma solicitação contendo até 10 mil endereços e faça com que elas sejam processadas durante um período de tempo. Todos os endereços serão geocodificados em paralelo no servidor e, quando concluído, o conjunto de resultados completo poderá ser baixado. Esse serviço é recomendado para a geocodificação de conjuntos de dados grandes.
-   [Pesquisa difusa](/rest/api/maps/search/getsearchfuzzy): Essa API combina geocódigo com pesquisa de ponto de interesse. Essa API usa uma cadeia de caracteres com forma livre que pode ser um endereço, um local, um ponto de referência, um ponto de interesse ou uma categoria de ponto de interesse e processa a solicitação imediatamente. Essa API é recomendada para aplicativos em que os usuários podem pesquisar endereços ou pontos de interesse usando a mesma caixa de texto.
-   [Pesquisa de lote difusa](/rest/api/maps/search/postsearchfuzzybatchpreview): Crie uma solicitação contendo até 10 mil endereços, locais, pontos de referência ou pontos de interesse e faça com que eles sejam processados durante um período. Todos os dados serão processados em paralelo no servidor e, quando concluído, o conjunto de resultados completo poderá ser baixado.

### <a name="get-administrative-boundary-data"></a>Obter dados de limites administrativos

No Bing Mapas, os limites administrativos de países, estados, condados, cidades e códigos postais são disponibilizados por meio da API Geodata. Essa API usa uma coordenada ou uma consulta para a geocodificação. Se uma consulta for passada, ela será geocodificada e as coordenadas do primeiro resultado serão usadas. Essa API usa as coordenadas e recupera o limite do tipo de entidade especificado que forma uma intersecção com a coordenada. Observe que a API não retorna necessariamente o limite da consulta que foi passada. Se uma consulta por `"Seattle, WA"` for passada, mas o valor do tipo de entidade estiver definido como uma região do país, o limite dos EUA será retornado.

O Azure Mapas também fornece acesso a limites administrativos (países, estados, condados, cidades e códigos postais). Para recuperar um limite, você precisa consultar uma das APIs de pesquisa para obter o limite desejado (ou seja, `Seattle, WA`). Se o resultado da pesquisa tiver um limite associado, uma ID de geometria será fornecida na resposta do resultado. A API de polígono de pesquisa pode ser usada para recuperar os limites exatos referentes a uma ou mais IDs de geometria. Isso é um pouco diferente do Bing Mapas, pois o Azure Mapas retorna o limite referente ao que foi pesquisado, enquanto o Bing Mapas retorna um limite referente a um tipo de entidade especificado em uma coordenada especificada. Além disso, os dados de limite são retornados pelo Azure Mapas no formato GeoJSON.

Recapitulando:

1.  Passe uma consulta do limite que você deseja receber para uma das APIs de pesquisa a seguir.
    -   [Geocódigo de endereço em forma livre](/rest/api/maps/search/getsearchaddress)
    -   [Geocódigo de endereço estruturado](/rest/api/maps/search/getsearchaddressstructured)
    -   [Geocódigo de endereço em lote](/rest/api/maps/search/postsearchaddressbatchpreview)
    -   [Pesquisa difusa](/rest/api/maps/search/getsearchfuzzy)
    -   [Pesquisa em lote difusa](/rest/api/maps/search/postsearchfuzzybatchpreview)
2.  Se o resultado desejado tiver uma ID de geometria, passe-a para a [API de Polígono de Pesquisa](/rest/api/maps/search/getsearchpolygon).

### <a name="host-and-query-spatial-business-data"></a>Hospedar e consultar dados espaciais comerciais

Os serviços de dados espaciais no Bing Mapas fornecem uma solução de armazenamento de dados espaciais simples para hospedar dados comerciais e expor esses dados como um serviço REST espacial. Esse serviço fornece quatro consultas principais: localizar por propriedade, localizar próximo, localizar na caixa delimitadora e localizar dentro de uma milha de rota. É comum que muitas empresas que usam esse serviço já tenham seus dados armazenados em um banco de dados em algum lugar e estejam carregando um pequeno subconjunto deles para esse serviço, a fim de alimentar aplicativos como localizadores de loja. Como a autenticação baseada em chave fornece segurança básica, é recomendável que o serviço seja usado apenas com dados voltados para o público.

A maioria dos dados de localização comercial começa em um banco de dados. Sendo assim, é recomendável usar soluções de armazenamento do Azure existentes, como o SQL Azure ou o Azure PostgreSQL (com o plug-in PostGIS). As duas soluções de armazenamento dão suporte a dados espaciais e fornecem um conjunto avançado de recursos de consulta espacial. Quando os dados estiverem em uma solução de armazenamento adequada, eles poderão ser integrados ao seu aplicativo criando um serviço Web personalizado ou usando uma estrutura como o ASP.NET ou o Entity Framework. Essa abordagem fornece mais recursos de consulta e muito mais opções de segurança.

O Azure Cosmos DB também fornece um conjunto limitado de recursos espaciais que, dependendo do cenário, pode ser suficiente.

Veja alguns recursos úteis relacionados à hospedagem e à consulta de dados espaciais no Azure.

-   [Visão geral de tipos de dados espaciais no SQL Azure](/sql/relational-databases/spatial/spatial-data-types-overview)
-   [SQL Azure espacial – consultar vizinho mais próximo](/sql/relational-databases/spatial/query-spatial-data-for-nearest-neighbor)
-   [Visão geral dos recursos geoespaciais do Azure Cosmos DB](../cosmos-db/sql-query-geospatial-intro.md)

## <a name="client-libraries"></a>Bibliotecas de cliente

O Azure Mapas fornece bibliotecas de cliente para as seguintes linguagens de programação:

-   JavaScript, TypeScript, Node.js – [documentação](./how-to-use-services-module.md) \| [pacote NPM](https://www.npmjs.com/package/azure-maps-rest)

Bibliotecas de cliente de software livre para outras linguagens de programação:

* .NET Standard 2.0 – [projeto GitHub](https://github.com/perfahlen/AzureMapsRestServices) \| [pacote NuGet](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="clean-up-resources"></a>Limpar recursos

Não há recursos a serem limpos.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os serviços REST do Azure Mapas.

> [!div class="nextstepaction"]
> [Melhores práticas para usar o serviço de pesquisa](how-to-use-best-practices-for-search.md)
