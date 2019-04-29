---
title: Como pesquisar com eficiência usando o serviço de pesquisa de mapas do Azure | Microsoft Docs
description: Saiba como usar as práticas recomendadas para a pesquisa usando o serviço de pesquisa de mapas do Azure
ms.author: v-musehg
ms.date: 04/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: e60b202a32745062d2078d9721dfeba7aba69bda
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763895"
---
# <a name="best-practices-to-use-azure-maps-search-service"></a>Práticas recomendadas para usar o serviço de pesquisa do Azure mapas

Mapas do Azure [serviço de pesquisa](https://docs.microsoft.com/rest/api/maps/search) inclui APIs com vários recursos, por exemplo, de pesquisa invertida de endereço para a pesquisa de dados de ponto de interesse POI () em torno de um local específico. Neste artigo, Compartilharemos as práticas recomendadas para chamar os dados por meio de serviços de pesquisa de mapas do Azure. Você saberá como:

* Criar consultas para retornar as correspondências relevantes
* Limitar resultados da pesquisa
* Aprenda a diferença entre vários tipos de resultado
* Ler a estrutura de resposta de pesquisa de endereço


## <a name="prerequisites"></a>Pré-requisitos

Para fazer todas as chamadas para as APIs do serviço de mapas, você precisa de uma conta de mapas e chave. Para obter mais informações sobre como criar uma conta e recuperar uma chave, consulte [Como gerenciar as chaves e a conta dos Mapas do Azure](how-to-manage-account-keys.md).

> [!Tip]
> Para consultar o serviço de pesquisa, você pode usar o [aplicativo Postman](https://www.getpostman.com/apps) compilar REST chamadas ou você pode usar qualquer ambiente de desenvolvimento de API que você preferir.


## <a name="best-practices-for-geocoding"></a>Práticas recomendadas para geocodificação

Quando você procura um endereço completo ou parcial, usando o serviço de pesquisa de mapas do Azure, ele usa o termo de pesquisa e retorna as coordenadas de longitude e latitude do endereço. Esse processo é chamado de geocodificação. A capacidade de geocodificação em um país é dependente de cobertura de dados de estrada e a precisão da codificação geográfica do serviço de geocodificação.

Ver [cobertura de geocodificação](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage) para saber mais sobre os recursos de geocodificação de mapas do Azure por país/região.

### <a name="limit-search-results"></a>Limitar resultados da pesquisa

   Nesta seção, você aprenderá como usar APIs de pesquisa de mapas do Azure para limitar os resultados da pesquisa. 

   > [!Note]
   > Pesquisa de nem todos os APIs totalmente dão suporte a parâmetros listados abaixo

   **Resultados da pesquisa de desvio de geográfica**

   A tendência de geográfica seus resultados para a área relevante para o usuário, você deve sempre adicionar o máximo possível detalhado entrada local. Para restringir os resultados da pesquisa, considere adicionar os seguintes tipos de entrada:

   1. Defina o `countrySet` parâmetro, por exemplo "US, FR". O comportamento de pesquisa padrão é pesquisar no mundo inteiro, retornando possivelmente resultados desnecessários. Se sua consulta não incluir `countrySet` parâmetro, a pesquisa pode retornar resultados imprecisos. Por exemplo, procure uma cidade denominada **Bellevue** retornará resultados dos EUA e da França, uma vez que há cidades denominadas **Bellevue** na França e nos EUA.

   2. Você pode usar o `btmRight` e `topleft` parâmetros para definir o limite da caixa para restringir a pesquisa a uma área específica do mapa.

   3. Para influenciar a área de relevância para os resultados, você pode definir as `lat`e `lon` coordenar os parâmetros e defina o raio da área de pesquisa usando o `radius` parâmetro.


   **Parâmetros de pesquisa difusa**

   1. O `minFuzzyLevel` e `maxFuzzyLevel`, ajudar a retornar correspondências relevantes, mesmo quando os parâmetros de consulta não exatamente correspondam às informações desejadas. A maioria das consultas de pesquisa como padrão `minFuzzyLevel=1` e `maxFuzzyLevel=2` para obter um melhor desempenho e reduzir os resultados incomuns. Veja um exemplo de um termo de pesquisa "restrant", ele corresponde a "restaurante" quando o `maxFuzzyLevel` é definido como 2. Os níveis de difusa padrão podem ser substituídos, de acordo com as necessidades de solicitação. 

   2. Você também pode especificar o conjunto exato de tipos de resultado a ser retornado usando o `idxSet` parâmetro. Para essa finalidade você pode enviar uma lista separada por vírgulas de índices, não importa a ordem dos itens. Estes são os índices com suporte:

       * `Addr` - **Intervalos de endereços**: Para algumas ruas, há pontos de endereço que são interpolados do início e término da rua; Esses pontos são representados como intervalos de endereços.
       * `Geo` - **Regiões geográficas**: Áreas em um mapa que representam a Divisão administrativa de uma Terra, ou seja, o país, o estado, a cidade.
       * `PAD` - **Endereço do ponto de**:  Pontos em um mapa onde endereço específico com um nome da rua e o número pode ser encontrado em um índice, por exemplo, Soquel 2501 de recuperação de desastre. É o nível mais alto de precisão para endereços disponível.  
       * `POI` - **Pontos de interesse**: Pontos em um mapa que merecem atenção e possam ser interessantes.  [Obter o endereço de pesquisa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) não retornará POIs.  
       * `Str` - **Ruas**: Representação de ruas no mapa.
       * `XStr` - **Cross/interseções de ruas**:  Representação de junções; coloca a interseção de dois ruas.


       **Exemplos de uso**:

       * idxSet = POI (pesquisa de pontos de interesse apenas) 

       * idxSet = TECLADO, Addr (pesquisa somente endereços PAD = endereço de ponto, Addr = intervalo de endereços)

### <a name="reverse-geocode-and-geography-entity-type-filter"></a>Filtro de tipo de entidade inverso de geocode e Geografia

Ao executar uma pesquisa inversa geocode com [reverso API de pesquisa de endereço](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse), o serviço tem a capacidade de retornar polígonos para as áreas administrativas. Fornecendo o parâmetro `entityType` na solicitação, você pode restringir a pesquisa para tipos de entidade de Geografia especificada. A resposta resultante conterá a ID da geografia, bem como o tipo de entidade correspondida. Se você fornecer mais de uma entidade, ponto de extremidade retornará a **menor entidade disponível**. Retornou a ID de geometria pode ser usada para obter a geometria da região geográfica por meio [serviço de polígono obter](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon).

**Solicitação de exemplo:**

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

### <a name="search-results-language"></a>Linguagem de resultados de pesquisa

O `language` parâmetro permite que você defina os resultados no qual a pesquisa de idioma devem ser retornados. Se o idioma não for definido na solicitação, o serviço de pesquisa é automaticamente padronizada para a linguagem mais comum no país/região. Além disso, quando os dados no idioma especificado não estiverem disponíveis, o idioma padrão é usado. Ver [idiomas com suporte](https://docs.microsoft.com/azure/azure-maps/supported-languages) para obter uma lista dos idiomas com suporte em relação aos serviços de mapas do Azure por país/região.


### <a name="predictive-mode-auto-suggest"></a>Modo de previsão (sugestão automática)

Para encontrar mais correspondências parciais em consultas, `typeahead` parâmetro deve ser definido como 'true'. A consulta será interpretada como uma entrada parcial e a pesquisa entrará no modo de previsão. Caso contrário, o serviço assumirá que todas as informações relevantes foi passadas.

No exemplo de consulta abaixo, você pode ver que a pesquisa do serviço de endereço é consultada para "Microso" com o `typeahead` parâmetro definido como **verdadeiro**. Se você observar a resposta, você pode ver que o serviço de pesquisa interpretadas a consulta como consulta parcial e a resposta contém os resultados de consulta sugeridos automaticamente.

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


### <a name="uri-encoding-to-handle-special-characters"></a>URI de codificação para lidar com caracteres especiais 

Para localizar entre endereços, ou seja, "1º Avenida & união Street, Seattle", caractere especial '&' precisa ser codificada antes de enviar a solicitação. É recomendável a codificação de dados de caractere em um URI, onde todos os caracteres são codificados usando um caractere '% s'e um valor hexadecimal de dois caracteres correspondentes a seus caracteres UTF-8.

**Exemplos de uso**:

Obter o endereço de pesquisa:

```
query=1st Avenue & E 111th St, New York
```

 deve ser codificada como:

```
query"=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```


Aqui estão os diferentes métodos para usar para diferentes idiomas: 

JavaScript/TypeScript:
```Javascript
encodeURIComponent(query)
```

C#/VB:
```C#
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

Ruby:
```Ruby
CGI::escape(query) 
```

Swift:
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

Acesse:
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-search"></a>Práticas recomendadas para pesquisa POI

Pontos de interesse (POI) pesquisa permite que você solicite POI resultados por nome, por exemplo, a empresa pesquisa por nome. É altamente recomendável que você use o `countrySet` parâmetro para especificar os países em que seu aplicativo precisa de cobertura, pois o comportamento padrão será a pesquisar no mundo inteiro, retornando possivelmente resultados desnecessários e/ou resultar em tempos de pesquisa.

### <a name="brand-search"></a>Pesquisa de marca

Para melhorar a relevância dos resultados e as informações na resposta, a resposta da pesquisa do ponto de interesse (POI) inclui as informações de marca que podem ser usadas para analisar a resposta.

Vamos fazer uma [pesquisa de categoria de POI](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) solicitação para postos de gasolina quase campus da Microsoft (Redmond, WA). Se você observar a resposta, você pode ver informações sobre a marca para cada POI retornado.

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


### <a name="airport-search"></a>Pesquisa do aeroporto

Pesquisa POI dá suporte à pesquisa aeroportos usando os códigos de aeroporto oficiais. Por exemplo, **SEA** (aeroporto internacional de Seattle Tacoma). 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>Pesquisa próxima

Para recuperar apenas os resultados POI em torno de um local específico, o [nas proximidades de API de pesquisa](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) pode ser a escolha certa. Esse ponto de extremidade retornará apenas resultados POI e não utilize um parâmetro de consulta de pesquisa. Para limitar os resultados, é recomendável definir o raio.

## <a name="understanding-the-responses"></a>Noções básicas sobre as respostas

Vamos fazer uma solicitação de pesquisa de endereço para o Azure mapas [serviço de pesquisa](https://docs.microsoft.com/rest/api/maps/search) um endereço em Seattle. Se você examinar cuidadosamente a URL de solicitação abaixo, definimos a `countrySet` parâmetro para **EUA** para pesquisar o endereço nos Estados Unidos da América.

**Consulta de exemplo:**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

Além disso, vamos dar uma olhada na estrutura de resposta abaixo. Os tipos de resultado de objetos de resultado na resposta são diferentes. Se você observar com atenção que você pode ver, que temos três tipos diferentes de objetos de resultado, que são "Endereço de ponto", "Street" e "Entre Street". Observe que a pesquisa endereço POIs não retorna. O `Score` parâmetro para cada objeto de resposta indica a pontuação de correspondência relativa para pontuações de outros objetos na mesma resposta. Ver [obter pesquisar endereço](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) para saber mais sobre os parâmetros de objeto de resposta.

**Tipos de resultado com suporte:**

* **Endereço de ponto:** Pontos em um mapa com um endereço específico com um nome da rua e um número. O nível mais alto de precisão para endereços disponível. 

* **Intervalo de endereços:**  Para algumas ruas, há pontos de endereço que são interpolados do início e término da rua; Esses pontos são representados como intervalos de endereços. 

* **Geografia:** Áreas em um mapa que representam a Divisão administrativa de uma Terra, ou seja, o país, o estado, a cidade. 

* **POI - (pontos de interesse):** Pontos em um mapa que merecem atenção e possam ser interessantes.

* **Rua:** Representação de ruas no mapa. Os endereços são resolvidos para a coordenada de latitude/longitude da rua que contém o endereço. O número da casa não pode ser processado. 

* **Cruzamento:** Interseções. Representações de junções; coloca a interseção de dois ruas.

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

Quando o tipo de resposta é **geometria**, ele pode incluir a ID de geometria que é retornada na **fontes de dados** objeto sob "geometria" e "id". Por exemplo, [serviço de polígono obter](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) permite solicitar os dados de geometria no formato GeoJSON, como uma cidade ou aeroporto da estrutura de tópicos para um conjunto de entidades. Você pode usar esses dados de limite para [Geofencing](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) ou [POIs pesquisa dentro da geometria](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry).


[Pesquisar endereço](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) ou [pesquisa difusa](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) respostas de API podem incluir o **geometria ID** que é retornado no objeto de fontes de dados em "geometria" e "id".


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>Próximas etapas

* Saiba mais [como criar solicitações de serviço de pesquisa do Azure mapas](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address).
* Explore o mapas do Azure [documentação da API de serviço de pesquisa](https://docs.microsoft.com/rest/api/maps/search). 