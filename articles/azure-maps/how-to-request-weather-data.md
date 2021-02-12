---
title: Solicitar dados meteorológicos em tempo real e previsão usando os serviços meteorológicos do Azure Maps (versão prévia)
description: Saiba como solicitar dados meteorológicos em tempo real (atuais) e previstos (minuto, por hora, diariamente) usando Microsoft Azure mapear os serviços meteorológicos (versão prévia)
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: fe1b337fe3e1dcf499f9a7428f66543108d0c050
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680410"
---
# <a name="request-real-time-and-forecasted-weather-data-using-azure-maps-weather-services-preview"></a>Solicitar dados meteorológicos em tempo real e previsão usando os serviços meteorológicos do Azure Maps (versão prévia) 

> [!IMPORTANT]
> O serviço Clima do Azure Mapas está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Os [serviços meteorológicos](/rest/api/maps/weather) do Azure Maps são um conjunto de APIs RESTful que permite aos desenvolvedores integrar dados de clima históricos, em tempo real e previstos e visualizações em suas soluções. Neste artigo, mostraremos como solicitar dados meteorológicos em tempo real e previsão.

Neste artigo, você aprenderá a:

* Solicite dados meteorológicos (atuais) em tempo real usando a [API obter condições atuais](/rest/api/maps/weather/getcurrentconditionspreview).
* Solicite alertas de clima severos usando a [API obter alertas meteorológicos severas](/rest/api/maps/weather/getsevereweatheralertspreview).
* Solicite previsões diárias usando a [API obter previsão diária](/rest/api/maps/weather/getdailyforecastpreview).
* Solicitar previsões por hora usando a [API obter previsão por hora](/rest/api/maps/weather/gethourlyforecastpreview).
* Solicite previsões de minuto por minuto usando a [API de previsão de obter minuto](/rest/api/maps/weather/getminuteforecastpreview).

Este vídeo fornece exemplos para fazer chamadas REST para os serviços meteorológicos do Azure Maps.

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps-Weather-services-for-developers/player?format=ny]

## <a name="prerequisites"></a>Pré-requisitos

1. [Fazer uma conta do Azure Mapas](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Obtenha uma chave de assinatura primária](quick-demo-map-app.md#get-the-primary-key-for-your-account), também conhecida como a chave primária ou a chave de assinatura. Para obter mais informações sobre a autenticação nos Azure Mapas, confira [Gerenciar a autenticação nos Azure Mapas](./how-to-manage-authentication.md).

    >[!IMPORTANT]
    >A [API obter previsão de minutos](/rest/api/maps/weather/getminuteforecastpreview) requer uma chave de tipo de preço S1. Todas as outras APIs exigem uma chave de tipo de preço S0.

Este tutorial usa o aplicativo [Postman](https://www.postman.com/), mas você pode escolher um ambiente de desenvolvimento de API diferente.

## <a name="request-real-time-weather-data"></a>Solicitar dados meteorológicos em tempo real

A [API obter condições atuais](/rest/api/maps/weather/getcurrentconditionspreview) retorna condições de clima detalhadas, como precipitação, temperatura e vento para um determinado local de coordenadas. Além disso, as observações das últimas 6 ou 24 horas para um local específico podem ser recuperadas. A resposta inclui detalhes como data e hora de observação, breve descrição das condições de clima, ícone de clima, sinalizadores de indicador precipitação e temperatura. O índice RealFeel™ temperatura e Ultraviolet (UV) também são retornados.

Neste exemplo, você usará a [API obter condições atuais](/rest/api/maps/weather/getcurrentconditionspreview) para recuperar as condições do clima atual em coordenadas localizadas em Seattle, WA.

1. Abra o aplicativo Postman. Próximo à parte superior do aplicativo Postman, selecione **Novo**. Na janela **Criar**, selecione **Coleção**.  Nomeie a coleção e selecione o botão **Criar**. Você usará essa coleção para o restante dos exemplos neste documento.

2. Para criar a solicitação, selecione **Novo** outra vez. Na janela **Criar**, selecione **Solicitação**. Insira um **Nome de solicitação** para a solicitação. Selecione a coleção que você criou na etapa anterior e clique em **Salvar**.

3. Selecione o método http **Get** na guia Construtor e insira a URL a seguir. Para essa solicitação e outras solicitações mencionadas neste artigo, substitua `{Azure-Maps-Primary-Subscription-key}` pela sua chave de assinatura primária.

    ```http
    https://atlas.microsoft.com/weather/currentConditions/json?api-version=1.0&query=47.60357,-122.32945&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

4. Clique no botão azul **Send** . O corpo da resposta contém informações sobre o clima atual.

    ```json
    {
    "results": [
        {
            "dateTime": "2020-10-19T20:39:00+00:00",
            "phrase": "Cloudy",
            "iconCode": 7,
            "hasPrecipitation": false,
            "isDayTime": true,
            "temperature": {
                "value": 12.4,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperature": {
                "value": 13.7,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperatureShade": {
                "value": 13.7,
                "unit": "C",
                "unitType": 17
            },
            "relativeHumidity": 87,
            "dewPoint": {
                "value": 10.3,
                "unit": "C",
                "unitType": 17
            },
            "wind": {
                "direction": {
                    "degrees": 23.0,
                    "localizedDescription": "NNE"
                },
                "speed": {
                    "value": 4.5,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "windGust": {
                "speed": {
                    "value": 9.0,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "uvIndex": 1,
            "uvIndexPhrase": "Low",
            "visibility": {
                "value": 9.7,
                "unit": "km",
                "unitType": 6
            },
            "obstructionsToVisibility": "",
            "cloudCover": 100,
            "ceiling": {
                "value": 1494.0,
                "unit": "m",
                "unitType": 5
            },
            "pressure": {
                "value": 1021.2,
                "unit": "mb",
                "unitType": 14
            },
            "pressureTendency": {
                "localizedDescription": "Steady",
                "code": "S"
            },
            "past24HourTemperatureDeparture": {
                "value": -2.1,
                "unit": "C",
                "unitType": 17
            },
            "apparentTemperature": {
                "value": 15.0,
                "unit": "C",
                "unitType": 17
            },
            "windChillTemperature": {
                "value": 12.2,
                "unit": "C",
                "unitType": 17
            },
            "wetBulbTemperature": {
                "value": 11.3,
                "unit": "C",
                "unitType": 17
            },
            "precipitationSummary": {
                "pastHour": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past3Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past6Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past9Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past12Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past18Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past24Hours": {
                    "value": 0.4,
                    "unit": "mm",
                    "unitType": 3
                }
            },
            "temperatureSummary": {
                "past6Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 14.0,
                        "unit": "C",
                        "unitType": 17
                    }
                },
                "past12Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 14.0,
                        "unit": "C",
                        "unitType": 17
                    }
                },
                "past24Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 15.6,
                        "unit": "C",
                        "unitType": 17
                    }
                }
            }
        }
    ]
    }
    ```

## <a name="request-severe-weather-alerts"></a>Solicitar alertas de clima severos

A [API de obtenção de alertas meteorológicos do Azure Maps](/rest/api/maps/weather/getsevereweatheralertspreview) retorna os alertas de clima severas que estão disponíveis em todo o mundo de órgãos de aeródromo governamentais oficiais e provedores de alerta de clima global a regional. O serviço pode retornar detalhes como tipo de alerta, categoria, nível e descrições detalhadas sobre os alertas graves ativos para o local solicitado, como furacões, thunderstorms, relâmpago, ondas de calor ou incêndios de florestas. Por exemplo, gerentes de logística podem visualizar severas condições de clima em um mapa, juntamente com locais de negócios e rotas planejadas, além de coordenar ainda mais com os drivers e os funcionários locais.

Neste exemplo, você usará a [API obter alertas meteorológicos severas](/rest/api/maps/weather/getsevereweatheralertspreview) para recuperar as condições do clima atual em coordenadas localizadas em Cheyenne, WY.

>[!NOTE]
>Este exemplo recupera alertas de clima severos no momento da redação deste artigo. É provável que não haja mais nenhum alerta de clima severo no local solicitado. Para recuperar dados de alerta graves reais ao executar este exemplo, você precisará recuperar dados em um local de coordenadas diferente.

1. Abra o aplicativo do postmaster, clique em **novo** e selecione **solicitação**. Insira um **Nome de solicitação** para a solicitação. Selecione a coleção que você criou na seção anterior ou criou uma nova e, em seguida, selecione **salvar**.

2. Selecione o método http **Get** na guia Construtor e insira a URL a seguir. Para essa solicitação e outras solicitações mencionadas neste artigo, substitua `{Azure-Maps-Primary-Subscription-key}` pela sua chave de assinatura primária.

    ```http
    https://atlas.microsoft.com/weather/severe/alerts/json?api-version=1.0&query=41.161079,-104.805450&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Clique no botão azul **Send** . Se não houver nenhum alerta de clima severo, o corpo da resposta conterá uma `results[]` matriz vazia. Se houver alertas de clima graves, o corpo da resposta conterá algo semelhante à resposta JSON a seguir:

    ```json
    {
    "results": [
        {
            "countryCode": "US",
            "alertId": 2194734,
            "description": {
                "localized": "Red Flag Warning",
                "english": "Red Flag Warning"
            },
            "category": "FIRE",
            "priority": 54,
            "source": "U.S. National Weather Service",
            "sourceId": 2,
            "alertAreas": [
                {
                    "name": "Platte/Goshen/Central and Eastern Laramie",
                    "summary": "Red Flag Warning in effect until 7:00 PM MDT.  Source: U.S. National Weather Service",
                    "startTime": "2020-10-05T15:00:00+00:00",
                    "endTime": "2020-10-06T01:00:00+00:00",
                    "latestStatus": {
                        "localized": "Continue",
                        "english": "Continue"
                    },
                    "alertDetails": "...RED FLAG WARNING REMAINS IN EFFECT FROM 9 AM THIS MORNING TO\n7 PM MDT THIS EVENING FOR STRONG GUSTY WINDS AND LOW HUMIDITY...\n\n* WHERE...Fire weather zones 303, 304, 305, 306, 307, 308, 309,\n  and 310 in southeast Wyoming. Fire weather zone 313 in Nebraska.\n\n* WIND...West to northwest 15 to 30 MPH with gusts around 40 MPH.\n\n* HUMIDITY...10 to 15 percent.\n\n* IMPACTS...Any fires that develop will likely spread rapidly.\n  Outdoor burning is not recommended.\n\nPRECAUTIONARY/PREPAREDNESS ACTIONS...\n\nA Red Flag Warning means that critical fire weather conditions\nare either occurring now...or will shortly. A combination of\nstrong winds...low relative humidity...and warm temperatures can\ncontribute to extreme fire behavior.\n\n&&",
                    "alertDetailsLanguageCode": "en"
                }
            ]
            },...
        ]
    }
    ```

## <a name="request-daily-weather-forecast-data"></a>Solicitar dados de previsão do clima diário

A [API obter previsão diária](/rest/api/maps/weather/getdailyforecastpreview) retorna uma previsão do tempo diário detalhada, como temperatura e vento. A solicitação pode especificar quantos dias para retornar: 1, 5, 10, 15, 25 ou 45 dias para um determinado local de coordenadas. A resposta inclui detalhes como temperatura, vento, precipitação, qualidade do ar e índice UV.  Neste exemplo, solicitamos cinco dias por configuração `duration=5` .

>[!IMPORTANT]
>No tipo de preço S0, você pode solicitar uma previsão diária para os próximos 1, 5, 10 e 15 dias. No tipo de preço S1, você também pode solicitar uma previsão diária para os próximos 25 dias e 45 dias.

Neste exemplo, você usará a [API obter previsão diária](/rest/api/maps/weather/getdailyforecastpreview) para recuperar a previsão do tempo de cinco dias para as coordenadas localizadas em Seattle, WA.

1. Abra o aplicativo do postmaster, clique em **novo** e selecione **solicitação**. Insira um **Nome de solicitação** para a solicitação. Selecione a coleção que você criou na seção anterior ou criou uma nova e, em seguida, selecione **salvar**.

2. Selecione o método http **Get** na guia Construtor e insira a URL a seguir. Para essa solicitação e outras solicitações mencionadas neste artigo, substitua `{Azure-Maps-Primary-Subscription-key}` pela sua chave de assinatura primária.

    ```http
    https://atlas.microsoft.com/weather/forecast/daily/json?api-version=1.0&query=47.60357,-122.32945&duration=5&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Clique no botão azul **Send** . O corpo da resposta contém os dados de previsão do tempo de cinco dias. Para fins de brevidade, a resposta JSON abaixo mostra a previsão para o primeiro dia.
    ```json
    {
    "summary": {
        "startDate": "2020-10-18T17:00:00+00:00",
        "endDate": "2020-10-19T23:00:00+00:00",
        "severity": 2,
        "phrase": "Snow, mixed with rain at times continuing through Monday evening and a storm total of 3-6 cm",
        "category": "snow/rain"
    },
    "forecasts": [
        {
            "date": "2020-10-19T04:00:00+00:00",
            "temperature": {
                "minimum": {
                    "value": -1.1,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 1.3,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "realFeelTemperature": {
                "minimum": {
                    "value": -6.0,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 0.5,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "realFeelTemperatureShade": {
                "minimum": {
                    "value": -6.0,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 0.7,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "hoursOfSun": 1.8,
            "degreeDaySummary": {
                "heating": {
                    "value": 18.0,
                    "unit": "C",
                    "unitType": 17
                },
                "cooling": {
                    "value": 0.0,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "airAndPollen": [
                {
                    "name": "AirQuality",
                    "value": 23,
                    "category": "Good",
                    "categoryValue": 1,
                    "type": "Ozone"
                },
                {
                    "name": "Grass",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Mold",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Ragweed",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Tree",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "UVIndex",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                }
            ],
            "day": {
                "iconCode": 22,
                "iconPhrase": "Snow",
                "hasPrecipitation": true,
                "precipitationType": "Mixed",
                "precipitationIntensity": "Light",
                "shortPhrase": "Chilly with snow, 2-4 cm",
                "longPhrase": "Chilly with snow, accumulating an additional 2-4 cm",
                "precipitationProbability": 90,
                "thunderstormProbability": 0,
                "rainProbability": 54,
                "snowProbability": 85,
                "iceProbability": 8,
                "wind": {
                    "direction": {
                        "degrees": 36.0,
                        "localizedDescription": "NE"
                    },
                    "speed": {
                        "value": 9.3,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "windGust": {
                    "direction": {
                        "degrees": 70.0,
                        "localizedDescription": "ENE"
                    },
                    "speed": {
                        "value": 25.9,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "totalLiquid": {
                    "value": 4.3,
                    "unit": "mm",
                    "unitType": 3
                },
                "rain": {
                    "value": 0.5,
                    "unit": "mm",
                    "unitType": 3
                },
                "snow": {
                    "value": 2.72,
                    "unit": "cm",
                    "unitType": 4
                },
                "ice": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "hoursOfPrecipitation": 9.0,
                "hoursOfRain": 1.0,
                "hoursOfSnow": 9.0,
                "hoursOfIce": 0.0,
                "cloudCover": 96
            },
            "night": {
                "iconCode": 29,
                "iconPhrase": "Rain and snow",
                "hasPrecipitation": true,
                "precipitationType": "Mixed",
                "precipitationIntensity": "Light",
                "shortPhrase": "Showers of rain and snow",
                "longPhrase": "A couple of showers of rain or snow this evening; otherwise, cloudy; storm total snowfall 1-3 cm",
                "precipitationProbability": 65,
                "thunderstormProbability": 0,
                "rainProbability": 60,
                "snowProbability": 54,
                "iceProbability": 4,
                "wind": {
                    "direction": {
                        "degrees": 16.0,
                        "localizedDescription": "NNE"
                    },
                    "speed": {
                        "value": 16.7,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "windGust": {
                    "direction": {
                        "degrees": 1.0,
                        "localizedDescription": "N"
                    },
                    "speed": {
                        "value": 35.2,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "totalLiquid": {
                    "value": 4.3,
                    "unit": "mm",
                    "unitType": 3
                },
                "rain": {
                    "value": 3.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "snow": {
                    "value": 0.79,
                    "unit": "cm",
                    "unitType": 4
                },
                "ice": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "hoursOfPrecipitation": 4.0,
                "hoursOfRain": 1.0,
                "hoursOfSnow": 3.0,
                "hoursOfIce": 0.0,
                "cloudCover": 94
            },
            "sources": [
                "AccuWeather"
            ]
        },...
    ]
    }
    ```

## <a name="request-hourly-weather-forecast-data"></a>Solicitar dados de previsão do clima por hora

A [API obter previsão por hora](/rest/api/maps/weather/gethourlyforecastpreview) retorna a previsão do tempo detalhada por hora para os próximos 1, 12, 24 (1 dia), 72 (3 dias), 120 (5 dias) e 240 horas (10 dias) para o local de coordenadas fornecido. A API retorna detalhes como temperatura, umidade, vento, precipitação e índice UV.

>[!IMPORTANT]
>No tipo de preço S0, você pode solicitar uma previsão por hora para as próximas 1, 12, 24 horas (1 dia) e 72 horas (3 dias). No tipo de preço S1, você também pode solicitar uma previsão por hora para os próximos 120 (5 dias) e 240 horas (10 dias).

Neste exemplo, você usará a [API obter previsão por hora](/rest/api/maps/weather/gethourlyforecastpreview) para recuperar a previsão do tempo por hora das próximas 12 horas nas coordenadas localizadas em Seattle, WA.

1. Abra o aplicativo do postmaster, clique em **novo** e selecione **solicitação**. Insira um **Nome de solicitação** para a solicitação. Selecione a coleção que você criou na seção anterior ou criou uma nova e, em seguida, selecione **salvar**.

2. Selecione o método http **Get** na guia Construtor e insira a URL a seguir. Para essa solicitação e outras solicitações mencionadas neste artigo, substitua `{Azure-Maps-Primary-Subscription-key}` pela sua chave de assinatura primária.

    ```http
    https://atlas.microsoft.com/weather/forecast/hourly/json?api-version=1.0&query=47.60357,-122.32945&duration=12&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Clique no botão azul **Send** . O corpo da resposta contém dados de previsão do tempo para as próximas 12 horas. Para fins de brevidade, a resposta JSON abaixo mostra a previsão para a primeira hora.

    ```json
    {
    "forecasts": [
        {
            "date": "2020-10-19T21:00:00+00:00",
            "iconCode": 12,
            "iconPhrase": "Showers",
            "hasPrecipitation": true,
            "precipitationType": "Rain",
            "precipitationIntensity": "Light",
            "isDaylight": true,
            "temperature": {
                "value": 14.7,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperature": {
                "value": 13.3,
                "unit": "C",
                "unitType": 17
            },
            "wetBulbTemperature": {
                "value": 12.0,
                "unit": "C",
                "unitType": 17
            },
            "dewPoint": {
                "value": 9.5,
                "unit": "C",
                "unitType": 17
            },
            "wind": {
                "direction": {
                    "degrees": 242.0,
                    "localizedDescription": "WSW"
                },
                "speed": {
                    "value": 9.3,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "windGust": {
                "speed": {
                    "value": 14.8,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "relativeHumidity": 71,
            "visibility": {
                "value": 9.7,
                "unit": "km",
                "unitType": 6
            },
            "cloudCover": 100,
            "ceiling": {
                "value": 1128.0,
                "unit": "m",
                "unitType": 5
            },
            "uvIndex": 1,
            "uvIndexPhrase": "Low",
            "precipitationProbability": 51,
            "rainProbability": 51,
            "snowProbability": 0,
            "iceProbability": 0,
            "totalLiquid": {
                "value": 0.3,
                "unit": "mm",
                "unitType": 3
            },
            "rain": {
                "value": 0.3,
                "unit": "mm",
                "unitType": 3
            },
            "snow": {
                "value": 0.0,
                "unit": "cm",
                "unitType": 4
            },
            "ice": {
                "value": 0.0,
                "unit": "mm",
                "unitType": 3
            }
        }...
    ]
    }
    ```
## <a name="request-minute-by-minute-weather-forecast-data"></a>Solicitar dados de previsão do tempo de minuto a minuto

 A [API obter previsão de minutos](/rest/api/maps/weather/getminuteforecastpreview) retorna previsões de minuto a minuto para um determinado local nos próximos 120 minutos. Os usuários podem solicitar previsões do tempo em intervalos de 1, 5 e 15 minutos. A resposta inclui detalhes como o tipo de precipitação (incluindo chuva, neve ou uma mistura de ambos), hora de início e valor de intensidade de precipitação (dBZ).

Neste exemplo, você usará a [API obter previsão de minutos](/rest/api/maps/weather/getminuteforecastpreview) para recuperar a previsão do tempo por minuto nas coordenadas localizadas em Seattle, WA. A previsão do tempo é dada para os próximos 120 minutos. Nossa consulta solicita que a previsão seja fornecida em intervalos de 15 minutos, mas você pode ajustar o parâmetro para ser 1 ou 5 minutos.

1. Abra o aplicativo do postmaster, clique em **novo** e selecione **solicitação**. Insira um **Nome de solicitação** para a solicitação. Selecione a coleção que você criou na seção anterior ou criou uma nova e, em seguida, selecione **salvar**.

2. Selecione o método http **Get** na guia Construtor e insira a URL a seguir. Para essa solicitação e outras solicitações mencionadas neste artigo, substitua `{Azure-Maps-Primary-Subscription-key}` pela sua chave de assinatura primária.

    ```http
    https://atlas.microsoft.com/weather/forecast/minute/json?api-version=1.0&query=47.60357,-122.32945&interval=15&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Clique no botão azul **Send** . O corpo da resposta contém dados de previsão do tempo para os próximos 120 minutos, em intervalos de 15 minutos.

    ```json
    {
    "summary": {
        "briefPhrase60": "No precipitation for at least 60 min",
        "shortPhrase": "No precip for 120 min",
        "briefPhrase": "No precipitation for at least 120 min",
        "longPhrase": "No precipitation for at least 120 min",
        "iconCode": 7
    },
    "intervalSummaries": [
        {
            "startMinute": 0,
            "endMinute": 119,
            "totalMinutes": 120,
            "shortPhrase": "No precip for %MINUTE_VALUE min",
            "briefPhrase": "No precipitation for at least %MINUTE_VALUE min",
            "longPhrase": "No precipitation for at least %MINUTE_VALUE min",
            "iconCode": 7
        }
    ],
    "intervals": [
        {
            "startTime": "2020-10-19T20:51:00+00:00",
            "minute": 0,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:06:00+00:00",
            "minute": 15,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:21:00+00:00",
            "minute": 30,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:36:00+00:00",
            "minute": 45,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:51:00+00:00",
            "minute": 60,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:06:00+00:00",
            "minute": 75,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:21:00+00:00",
            "minute": 90,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:36:00+00:00",
            "minute": 105,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        }
        ]
    }
    ```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Conceitos dos serviços meteorológicos do Azure Maps (versão prévia)](./weather-services-concepts.md)

> [!div class="nextstepaction"]
> [API REST dos serviços meteorológicos do Azure Maps (versão prévia)](/rest/api/maps/weather)