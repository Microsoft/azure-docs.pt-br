---
title: Como Pesquisar com eficiência usando o serviço de pesquisa do Azure Maps | Microsoft Docs
description: Saiba como usar as práticas recomendadas para pesquisa usando o serviço de pesquisa do Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 04/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 996a084fd653b2100d94313e8801d915b4bf2cf3
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348187"
---
# <a name="best-practices-to-use-azure-maps-search-service"></a>Práticas recomendadas para usar o Azure Maps Serviço de Pesquisa

O Azure Maps [serviço de pesquisa](https://docs.microsoft.com/rest/api/maps/search) inclui APIs com vários recursos, por exemplo, de pesquisa de endereço para pesquisar dados de POI (ponto de interesse) em um local específico. Neste artigo, você compartilhará as práticas recomendadas para chamar dados por meio dos serviços de pesquisa do Azure Maps. Você saberá como:

* Criar consultas para retornar correspondências relevantes
* Limitar os resultados da pesquisa
* Aprenda a diferença entre vários tipos de resultados
* Ler a estrutura de resposta de pesquisa de endereço


## <a name="prerequisites"></a>Pré-requisitos

Para fazer todas as chamadas para as APIs do serviço de mapas, você precisa de uma conta de mapas e chave. Para obter mais informações sobre como criar uma conta e recuperar uma chave, consulte [Como gerenciar as chaves e a conta dos Mapas do Azure](how-to-manage-account-keys.md).

> [!Tip]
> Para consultar o serviço de pesquisa, você pode usar o [aplicativo de postmaster](https://www.getpostman.com/apps) para criar chamadas REST ou pode usar qualquer ambiente de desenvolvimento de API que preferir.


## <a name="best-practices-for-geocoding"></a>Práticas recomendadas para geocodificação

Quando você procura um endereço completo ou parcial usando o Azure Maps Serviço de Pesquisa, ele usa o termo de pesquisa e retorna as coordenadas de longitude e latitude do endereço. Esse processo é chamado de geocodificação. A capacidade de geocodificação em um país é dependente de cobertura de dados de estrada e a precisão da codificação geográfica do serviço de geocodificação.

Consulte [cobertura](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage) de geocodificação para saber mais sobre os recursos de geocodificação do Azure Maps por país/região.

### <a name="limit-search-results"></a>Limitar os resultados da pesquisa

   Nesta seção, você aprenderá a usar as APIs de pesquisa do Azure Maps para limitar os resultados da pesquisa. 

   > [!Note]
   > Nem todas as APIs de pesquisa dão suporte total a parâmetros listados abaixo

   **Resultados da pesquisa de tendência geográfica**

   Para fazer a tendência geográfica dos resultados para a área relevante para o usuário, você sempre deve adicionar a entrada de local detalhada máxima possível. Para restringir os resultados da pesquisa, considere adicionar os seguintes tipos de entrada:

   1. Defina o `countrySet` parâmetro, por exemplo, "US, fr". O comportamento de pesquisa padrão é Pesquisar o mundo inteiro, potencialmente retornando resultados desnecessários. Se a consulta não incluir `countrySet` o parâmetro, a pesquisa poderá retornar resultados imprecisos. Por exemplo, procurar uma cidade chamada **Bellevue** retornará resultados dos EUA e da França, já que há cidades chamadas **Bellevue** na França e nos EUA.

   2. Você pode usar os `btmRight` parâmetros `topleft` e para definir a caixa delimitadora para restringir a pesquisa a uma área específica no mapa.

   3. Para influenciar a área de relevância dos resultados, você pode definir os `lat`parâmetros de coordenada e `lon` definir o raio da área de pesquisa usando o `radius` parâmetro.


   **Parâmetros de pesquisa difusa**

   1. O `minFuzzyLevel` e`maxFuzzyLevel`o ajudam a retornar correspondências relevantes mesmo quando os parâmetros de consulta não correspondem exatamente às informações desejadas. A maioria das consultas de `minFuzzyLevel=1` pesquisa `maxFuzzyLevel=2` usa como padrão e para obter o desempenho e reduzir resultados incomuns. Veja um exemplo de termo de pesquisa "restrant", que é correspondido a "restaurante" quando o `maxFuzzyLevel` é definido como 2. Os níveis de fuzzing padrão podem ser substituídos de acordo com as necessidades de solicitação. 

   2. Você também pode especificar o conjunto exato de tipos de resultados a serem retornados usando o `idxSet` parâmetro. Para essa finalidade, você pode enviar uma lista de índices separados por vírgulas, a ordem do item não importa. Estes são os índices com suporte:

       * `Addr` - **Intervalos de endereços**: Para algumas ruas, há pontos de endereço que são interpolados do início e do fim da rua; esses pontos são representados como intervalos de endereços.
       * `Geo` - **Geografias**: Áreas em um mapa que representam a divisão administrativa de um terreno, ou seja, país, estado, cidade.
       * `PAD` - **Endereço do ponto**:  Pontos em um mapa em que um endereço específico com um nome de rua e número podem ser encontrados em um índice, por exemplo, Soquel Dr 2501. É o nível mais alto de precisão disponível para endereços.  
       * `POI` - **Pontos de interesse**: Pontos em um mapa que vale a pena dar atenção e podem ser interessantes.  [Obter o endereço de pesquisa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) não retornará depois.  
       * `Str` - **Ruas**: Representação de ruas no mapa.
       * `XStr` - **Ruas cruzadas/interseções**:  Representação de junções; locais onde duas ruas se cruzam.


       **Exemplos de uso**:

       * idxSet = POI (somente pontos de pesquisa de interesse) 

       * idxSet = PAD, addr (somente endereços de pesquisa, PAD = endereço de ponto, addr = intervalo de endereços)

### <a name="reverse-geocode-and-geography-entity-type-filter"></a>Reverter o filtro de tipo de entidade geográfica e geography

Ao executar uma pesquisa de geocódigo inversa com a [API reversa de endereço de pesquisa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse), o serviço tem capacidade para retornar polígonos para as áreas administrativas. Ao fornecer o parâmetro `entityType` na solicitação, você pode restringir a pesquisa para tipos de entidade geography especificados. A resposta resultante conterá a ID de Geografia, bem como o tipo de entidade correspondente. Se você fornecer mais de uma entidade, Endpoint retornará a **menor entidade disponível**. A ID geometry retornada pode ser usada para obter a geometria dessa geografia por meio do [serviço de polígono](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon).

**Exemplo de solicitação:**

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

**Resposta:**

```JSON
{
    "summary": {
        "queryTime": 8,
        "numResults": 1
    },
    "addresses": [
        {
            "address": {
                "routeNumbers": [],
                "countryCode": "US",
                "countrySubdivision": "WA",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "municipality": "Redmond",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Redmond, WA",
                "boundingBox": {
                    "northEast": "47.717105,-122.034537",
                    "southWest": "47.627016,-122.164998",
                    "entity": "position"
                },
                "countrySubdivisionName": "Washington"
            },
            "position": "47.639454,-122.130455",
            "dataSources": {
                "geometry": {
                    "id": "00005557-4100-3c00-0000-0000596ae571"
                }
            },
            "entityType": "Municipality"
        }
    ]
}
```

### <a name="search-results-language"></a>Idioma dos resultados da pesquisa

O `language` parâmetro permite que você defina em quais resultados de pesquisa de idioma devem ser retornados. Se o idioma não estiver definido na solicitação, o serviço de pesquisa padroniza automaticamente para a linguagem mais comum no país/região. Além disso, quando os dados no idioma especificado não estão disponíveis, o idioma padrão é usado. Consulte [idiomas com suporte](https://docs.microsoft.com/azure/azure-maps/supported-languages) para obter uma lista de idiomas com suporte em relação aos serviços do Azure Maps por país/região.


### <a name="predictive-mode-auto-suggest"></a>Modo de previsão (sugestão automática)

Para encontrar mais correspondências para consultas parciais, `typeahead` o parâmetro deve ser definido como ' true '. A consulta será interpretada como uma entrada parcial e a pesquisa entrará no modo de previsão. Caso contrário, o serviço assumirá que todas as informações relevantes foram passadas.

Na consulta de exemplo abaixo, você pode ver que o serviço de endereço de pesquisa é consultado por "Microsoft `typeahead` " com o parâmetro definido como **true**. Se você observar a resposta, poderá ver que o serviço de pesquisa interpretou a consulta como consulta parcial e a resposta contém resultados para consulta sugerida automaticamente.

**Consulta de exemplo:**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

**Resposta:**

```JSON
{
    "summary": {
        "query": "microsoft",
        "queryType": "NON_NEAR",
        "queryTime": 25,
        "numResults": 6,
        "offset": 0,
        "totalResults": 6,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6370891183,
            "lon": -122.123736172
        }
    },
    "results": [
        {
            "type": "Street",
            "id": "US/STR/p0/10294417",
            "score": 2.594,
            "dist": 327.546040632591,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98052",
                "extendedPostalCode": "980526399,980528300",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Redmond, WA 98052",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.63989,
                "lon": -122.12509
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63748,
                    "lon": -122.12309
                },
                "btmRightPoint": {
                    "lat": 47.64223,
                    "lon": -122.13061
                }
            }
        },
        ...,
        ...,
        ...,
        ...,
        {
            "type": "Street",
            "id": "US/STR/p0/9063400",
            "score": 2.075,
            "dist": 3655467.6406921702,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Yorkmount, Charlotte",
                "municipality": "Charlotte",
                "countrySecondarySubdivision": "Mecklenburg",
                "countryTertiarySubdivision": "Township 1 Charlotte",
                "countrySubdivision": "NC",
                "postalCode": "28217",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Charlotte, NC 28217",
                "countrySubdivisionName": "North Carolina"
            },
            "position": {
                "lat": 35.14279,
                "lon": -80.91814
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14267,
                    "lon": -80.91814
                },
                "btmRightPoint": {
                    "lat": 35.14279,
                    "lon": -80.91824
                }
            }
        }
    ]
}
```


### <a name="uri-encoding-to-handle-special-characters"></a>Codificação de URI para manipular caracteres especiais 

Para localizar endereços cruzados, ou seja, "1º Avenida & rua da União, Seattle", o caractere especial ' & ' precisa ser codificado antes de enviar a solicitação. É recomendável codificar dados de caractere em um URI, onde todos os caracteres são codificados usando um caractere '% ' e um valor hexadecimal de dois caracteres correspondente ao seu caractere UTF-8.

**Exemplos de uso**:

Obter endereço de pesquisa:

```
query=1st Avenue & E 111th St, New York
```

 deve ser codificado como:

```
query"=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```


Aqui estão os diferentes métodos a serem usados para diferentes idiomas: 

JavaScript/TypeScript:
```Javascript
encodeURIComponent(query)
```

C#/VB:
```csharp
Uri.EscapeDataString(query)
```

Java:
```Java
URLEncoder.encode(query, "UTF-8") 
```

Python:
```Python
import urllib.parse 
urllib.parse.quote(query)
```

C++:
```C++
#include <curl/curl.h>
curl_easy_escape(query)
```

PHP:
```PHP
urlencode(query)
```

Rubi
```Ruby
CGI::escape(query) 
```

Swift
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

Vá
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-search"></a>Práticas recomendadas para pesquisa do POI

A pesquisa de POI (pontos de interesse) permite solicitar resultados de POI por nome, por exemplo, Pesquisar negócios por nome. Recomendamos que você use o `countrySet` parâmetro para especificar os países em que seu aplicativo precisa de cobertura, pois o comportamento padrão será Pesquisar todo o mundo, potencialmente retornando resultados desnecessários e/ou resultará em tempos de pesquisa mais longos.

### <a name="brand-search"></a>Pesquisa de marca

Para melhorar a relevância dos resultados e as informações na resposta, a resposta de pesquisa de POI (ponto de interesse) inclui as informações de marca que podem ser usadas ainda mais para analisar a resposta.

Vamos fazer uma solicitação de [pesquisa de categoria POI](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) para estações de gás próximas à Microsoft campus (Redmond, WA). Se você observar a resposta, poderá ver informações de marca para cada POI retornado.

**Consulta de exemplo:**

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas%20station&limit=3&lat=47.6413362&lon=-122.1327968
```

**Resposta:**

```JSON
{
    "summary": {
        "query": "gas station",
        "queryType": "NON_NEAR",
        "queryTime": 206,
        "numResults": 3,
        "offset": 0,
        "totalResults": 742169,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6413362,
            "lon": -122.1327968
        }
    },
    "results": [
        {
            "type": "POI",
            "id": "US/POI/p0/245813",
            "score": 5.663,
            "dist": 1037.0280221303253,
            "info": "search:ta:840531000004190-US",
            "poi": {
                "name": "Chevron",
                "phone": "+(1)-(425)-6532200",
                "brands": [
                    {
                        "name": "Chevron"
                    }
                ],
                "url": "www.chevron.com",
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "2444",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Northeast Bellevue, Bellevue",
                "municipality": "Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "2444 Bel Red Rd, Bellevue, WA 98007",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.63201,
                "lon": -122.13281
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63291,
                    "lon": -122.13414
                },
                "btmRightPoint": {
                    "lat": 47.63111,
                    "lon": -122.13148
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63223,
                        "lon": -122.13311
                    }
                }
            ]
        },
        ...,
        {
            "type": "POI",
            "id": "US/POI/p0/7727106",
            "score": 5.662,
            "dist": 1458.645407416307,
            "info": "search:ta:840539000488527-US",
            "poi": {
                "name": "BROWN BEAR CAR WASH",
                "phone": "+(1)-(425)-6442868",
                "brands": [
                    {
                        "name": "Texaco"
                    }
                ],
                "url": "www.texaco.com/",
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "15248",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98052",
                "extendedPostalCode": "980525511",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "15248 Bel Red Rd, Redmond, WA 98052",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62843,
                "lon": -122.13628
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62933,
                    "lon": -122.13761
                },
                "btmRightPoint": {
                    "lat": 47.62753,
                    "lon": -122.13495
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.62826,
                        "lon": -122.13626
                    }
                }
            ]
        }
    ]
}
```


### <a name="airport-search"></a>Pesquisa de aeroportos

A pesquisa do POI dá suporte à pesquisa de aeroportos usando os códigos oficiais do aeroporto. Por exemplo, **mar** (Aeroporto Internacional de Seattle-Tacoma). 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>Pesquisa próxima

Para recuperar apenas os resultados de POI em um local específico, a [API de pesquisa próxima](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) pode ser a escolha certa. Esse ponto de extremidade retornará apenas resultados de POI e não usará um parâmetro de consulta de pesquisa. Para limitar os resultados, é recomendável definir o raio.

## <a name="understanding-the-responses"></a>Noções básicas sobre as respostas

Vamos fazer uma solicitação de pesquisa de endereço para o [serviço de pesquisa](https://docs.microsoft.com/rest/api/maps/search) do Azure Maps para um endereço em Seattle. Se você olhar atentamente a URL de solicitação abaixo, definimos o `countrySet` parâmetro como **nós** para pesquisar o endereço no Estados Unidos da América.

**Consulta de exemplo:**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

Vamos dar uma olhada na estrutura de resposta abaixo. Os tipos de resultado dos objetos de resultado na resposta são diferentes. Se você observar com cuidado, pode ver que temos três tipos diferentes de objetos de resultado, que são "endereço de ponto", "Street" e "Cross Street". Observe que a pesquisa de endereço não retorna depois. O `Score` parâmetro para cada objeto de resposta indica a pontuação de correspondência relativa para pontuações de outros objetos na mesma resposta. Consulte [obter o endereço de pesquisa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) para saber mais sobre os parâmetros do objeto de resposta.

**Tipos de resultado com suporte:**

* **Endereço do ponto:** Pontos em um mapa com endereço específico com um nome e número de rua. O nível mais alto de precisão disponível para endereços. 

* **Intervalo de endereços:**  Para algumas ruas, há pontos de endereço que são interpolados do início e do fim da rua; esses pontos são representados como intervalos de endereços. 

* **Gráfico** Áreas em um mapa que representam a divisão administrativa de um terreno, ou seja, país, estado, cidade. 

* **POI-(pontos de interesse):** Pontos em um mapa que vale a pena dar atenção e podem ser interessantes.

* **Endereço** Representação de ruas no mapa. Os endereços são resolvidos para a coordenada de latitude/longitude da rua que contém o endereço. O número da casa não pode ser processado. 

* **Entre as ruas:** Interseções. Representações de junções; locais onde duas ruas se cruzam.

**Resposta:**

```JSON
{
    "summary": {
        "query": "400 broad street seattle wa",
        "queryType": "NON_NEAR",
        "queryTime": 129,
        "numResults": 6,
        "offset": 0,
        "totalResults": 6,
        "fuzzyLevel": 1
    },
    "results": [
        {
            "type": "Point Address",
            "id": "US/PAD/p0/43076024",
            "score": 9.894,
            "address": {
                "streetNumber": "400",
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, South Lake Union, Lower Queen Anne",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "400 Broad Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62039,
                "lon": -122.34928
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62129,
                    "lon": -122.35061
                },
                "btmRightPoint": {
                    "lat": 47.61949,
                    "lon": -122.34795
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.61982,
                        "lon": -122.34886
                    }
                }
            ]
        },
        {
            "type": "Street",
            "id": "US/STR/p0/2440854",
            "score": 8.129,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Westlake, South Lake Union",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "extendedPostalCode": "981094347,981094700,981094701,981094702",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62553,
                "lon": -122.33936
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62545,
                    "lon": -122.33861
                },
                "btmRightPoint": {
                    "lat": 47.62574,
                    "lon": -122.33974
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/8450985",
            "score": 8.129,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Belltown",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109,98121",
                "extendedPostalCode": "981094991,981211117,981211237,981213206",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.61691,
                "lon": -122.35251
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61502,
                    "lon": -122.35041
                },
                "btmRightPoint": {
                    "lat": 47.61857,
                    "lon": -122.35484
                }
            }
        },
        ...,
        ...,
        {
            "type": "Cross Street",
            "id": "US/XSTR/p1/3816818",
            "score": 6.759,
            "address": {
                "streetName": "Broad Street & Valley Street",
                "municipalitySubdivision": "South Lake Union, Seattle",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street & Valley Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62574,
                "lon": -122.33861
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62664,
                    "lon": -122.33994
                },
                "btmRightPoint": {
                    "lat": 47.62484,
                    "lon": -122.33728
                }
            }
        }
    ]
}
```

### <a name="geometry"></a>Geometria

Quando o tipo de resposta é **Geometry**, ele pode incluir a ID geometry retornada no objeto DataSources em "Geometry" e "ID". Por exemplo, [obter serviço de polígono](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) permite solicitar os dados de geometria no formato geojson, como um esboço de cidade ou aeroporto para um conjunto de entidades. Você pode usar esses dados de limite para o [isolamento geográfico](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) ou [Pesquisar por dentro da geometria](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry).


As respostas de [endereço de pesquisa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) ou de pesquisa de API [difusa](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) podem incluir a **ID de geometria** retornada no objeto de fontes de origem em "Geometry" e "ID".


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>Próximas etapas

* Saiba [como criar solicitações de serviço de pesquisa do Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address).
* Explore a documentação da [API do serviço de pesquisa](https://docs.microsoft.com/rest/api/maps/search)do Azure Maps. 
