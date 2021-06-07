---
title: 'Tutorial: Unir dados de sensor e dados de previsão do tempo usando Azure Notebooks (Python) com o Microsoft Azure Mapas'
description: Tutorial sobre como unir dados de sensor e dados de previsão do tempo do serviço Clima dos Microsoft Azure Mapas usando Azure Notebooks (Python).
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 276dd5b7eba33081c5131eba722df91d8685adff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98678156"
---
# <a name="tutorial-join-sensor-data-with-weather-forecast-data-by-using-azure-notebooks-python"></a>Tutorial: Unir dados de sensor com os dados de previsão do tempo usando o Azure Notebooks (Python)

> [!IMPORTANT]
> O serviço Clima do Azure Mapas está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A energia eólica é uma fonte de energia alternativa aos combustíveis fósseis para combater a alteração climática. Como o vento não é consistente por natureza, os operadores de energia eólica precisam criar modelos de ML (machine learning) para prever a capacidade de energia eólica. Essa previsão é necessária para atender à demanda de eletricidade e garantir a estabilidade da grade. Neste tutorial, explicaremos como os dados de previsão do tempo dos Azure Mapas são combinados com os dados de demonstração de leituras do clima. Os dados de previsão do tempo são solicitados chamando o serviço Clima do Azure Mapas (versão prévia).

Neste tutorial, você irá:

> [!div class="checklist"]
> * Trabalhar com arquivos de dados no [Azure Notebooks](https://notebooks.azure.com) na nuvem.
> * Carregar dados de demonstração do arquivo.
> * Chamar as APIs REST dos Azure Mapas no Python.
> * Renderizar dados de localização no mapa.
> * Enriquecer os dados de demonstração com os dados meteorológicos de [previsão diária](/rest/api/maps/weather/getdailyforecastpreview) do Azure Mapas.
> * Plotar dados de previsão em gráficos.


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, primeiro você precisará:

1. Criar uma assinatura da conta dos Azure Mapas no tipo de preço S0 seguindo as instruções em [Criar uma conta](quick-demo-map-app.md#create-an-azure-maps-account).
2. Obter a chave de assinatura primária da sua conta seguindo as instruções em [Obter chave primária](quick-demo-map-app.md#get-the-primary-key-for-your-account).


Para obter mais informações sobre a autenticação nos Azure Mapas, confira [Gerenciar a autenticação nos Azure Mapas](./how-to-manage-authentication.md).

Para se familiarizar com o Azure Notebooks e saber como começar, siga as instruções [Criar um Azure Notebook](./tutorial-ev-routing.md#create-an-azure-notebooks-project).

> [!Note]
> O arquivo do Jupyter Notebook para este projeto pode ser baixado do [Repositório de Jupyter Notebooks de Mapas Meteorológicos](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data).

## <a name="load-the-required-modules-and-frameworks"></a>Carregar as estruturas e os módulos necessários

Para carregar todas as estruturas e todos os módulos necessários, execute o seguinte script:

```python
import pandas as pd
import datetime
from IPython.display import Image, display
!pip install aiohttp
import aiohttp
```

## <a name="import-weather-data"></a>Importar dados meteorológicos

Para este tutorial, usaremos as leituras de dados meteorológicos de sensores instalados em quatro turbinas eólicas diferentes. Os dados de exemplo consistem em 30 dias de leituras do clima. Essas leituras são coletadas dos data centers meteorológicos próximos a cada localização da turbina. Os dados de demonstração contêm leituras de temperatura e velocidade e direção do vento. Você pode baixar os dados de demonstração [aqui](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data/data). O script abaixo importa dados de demonstração para o Azure Notebook.

```python
df = pd.read_csv("./data/weather_dataset_demo.csv")
```

## <a name="request-daily-forecast-data"></a>Solicitar dados de previsão diária

Em nosso cenário, desejamos solicitar uma previsão diária para cada localização de sensor. O script a seguir chama a [API de Previsão Diária](/rest/api/maps/weather/getdailyforecastpreview) do serviço Clima dos Azure Mapas (versão prévia). Essa API retorna a previsão do tempo para cada turbina eólica, para os próximos 15 dias a contar da data atual.


```python
subscription_key = "Your Azure Maps key"

# Get a lists of unique station IDs and their coordinates 
station_ids = pd.unique(df[['StationID']].values.ravel())
coords = pd.unique(df[['latitude','longitude']].values.ravel())

years,months,days = [],[],[]
dates_check=set()
wind_speeds, wind_direction = [], []

# Call azure maps Weather services (Preview) to get daily forecast data for 15 days from current date
session = aiohttp.ClientSession()
j=-1
for i in range(0, len(coords), 2):
    wind_speeds.append([])
    wind_direction.append([])
    
    query = str(coords[i])+', '+str(coords[i+1])
    forecast_response = await(await session.get("https://atlas.microsoft.com/weather/forecast/daily/json?query={}&api-version=1.0&subscription-key={}&duration=15".format(query, subscription_key))).json()
    j+=1
    for day in range(len(forecast_response['forecasts'])):
            date = forecast_response['forecasts'][day]['date'][:10]
            wind_speeds[j].append(forecast_response['forecasts'][day]['day']['wind']['speed']['value'])
            wind_direction[j].append(forecast_response['forecasts'][day]['day']['windGust']['direction']['degrees'])
            
            if date not in dates_check:
                year,month,day= date.split('-')
                years.append(year)
                months.append(month)
                days.append(day)
                dates_check.add(date)
            
await session.close()
```

O script a seguir renderiza os locais de turbina no mapa chamando o [serviço Obter Imagem do Mapa](/rest/api/maps/render/getmapimage) do Azure Mapas.

```python
# Render the turbine locations on the map by calling the Azure Maps Get Map Image service
session = aiohttp.ClientSession()

pins="default|la-25+60|ls12|lc003C62|co9B2F15||'Location A'{} {}|'Location B'{} {}|'Location C'{} {}|'Location D'{} {}".format(coords[1],coords[0],coords[3],coords[2],coords[5],coords[4], coords[7],coords[6])

image_response = "https://atlas.microsoft.com/map/static/png?subscription-key={}&api-version=1.0&layer=basic&style=main&zoom=6&center={},{}&pins={}".format(subscription_key,coords[7],coords[6],pins)

static_map_response = await session.get(image_response)

poi_range_map = await static_map_response.content.read()

await session.close()

display(Image(poi_range_map))
```

![Locais de turbina](./media/weather-service-tutorial/location-map.png)


Agruparemos os dados de previsão com os dados de demonstração de acordo com a ID da estação. A ID da estação destina-se ao data center meteorológico. Esse agrupamento aumenta os dados de demonstração com os dados de previsão.

```python
# Group forecasted data for all locations
df = df.reset_index(drop=True)
forecast_data = pd.DataFrame(columns=['StationID','latitude','longitude','Year','Month','Day','DryBulbCelsius','WetBulbFarenheit','WetBulbCelsius','DewPointFarenheit','DewPointCelsius','RelativeHumidity','WindSpeed','WindDirection'])

for i in range(len(station_ids)):
    loc_forecast = pd.DataFrame({'StationID':station_ids[i], 'latitude':coords[0], 'longitude':coords[1], 'Year':years, 'Month':months, 'Day':days, 'WindSpeed':wind_speeds[i], 'WindDirection':wind_direction[i]})
    forecast_data = pd.concat([forecast_data,loc_forecast], axis=0, sort=False)
    
combined_weather_data = pd.concat([df,forecast_data])
grouped_weather_data = combined_weather_data.groupby(['StationID'])
```

A tabela a seguir exibe os dados históricos e de previsão combinados para um dos locais de turbina.

```python
# Display data for first location
grouped_weather_data.get_group(station_ids[0]).reset_index()
```

<center>

![Dados agrupados](./media/weather-service-tutorial/grouped-data.png)</center>

## <a name="plot-forecast-data"></a>Plotar dados de previsão

Plotaremos os valores previstos em relação aos dias para os quais eles são previstos. Essa plotagem nos permite ver as alterações de velocidade e direção do vento nos próximos 15 dias.

```python
# Plot wind speed
curr_date = datetime.datetime.now().date()
windsPlot_df = pd.DataFrame({ 'Location A': wind_speeds[0], 'Location B': wind_speeds[1], 'Location C': wind_speeds[2], 'Location D': wind_speeds[3]}, index=pd.date_range(curr_date,periods=15))
windsPlot = windsPlot_df.plot.line()
windsPlot.set_xlabel("Date")
windsPlot.set_ylabel("Wind speed")
```

```python
#Plot wind direction 
windsPlot_df = pd.DataFrame({ 'Location A': wind_direction[0], 'Location B': wind_direction[1], 'Location C': wind_direction[2], 'Location D': wind_direction[3]}, index=pd.date_range(curr_date,periods=15))
windsPlot = windsPlot_df.plot.line()
windsPlot.set_xlabel("Date")
windsPlot.set_ylabel("Wind direction")
```

Os grafos abaixo visualizam os dados de previsão. Para a alteração da velocidade do vento, confira o grafo à esquerda. Para a alteração da direção do vento, confira o gráfico à direita. Esses dados são a previsão para os próximos 15 dias, a partir do dia em que os dados são solicitados.

<center>

![Gráfico da velocidade do vento](./media/weather-service-tutorial/speed-date-plot.png) ![Gráfico da direção do vento](./media/weather-service-tutorial/direction-date-plot.png)</center>

Neste tutorial, você aprendeu a chamar as APIs REST dos Azure Mapas para obter dados de previsão do tempo. Você também aprendeu a visualizar os dados em grafos.

Para saber mais sobre como chamar as APIs REST do Azure Mapas dentro de Azure Notebooks, consulte [Roteamento de EV usando Azure Notebooks](./tutorial-ev-routing.md).

Para explorar as APIs dos Azure Mapas que são usadas neste tutorial, confira:

* [Previsão diária](/rest/api/maps/weather/getdailyforecastpreview)
* [Renderizar – obter imagem do mapa](/rest/api/maps/render/getmapimage)

Para obter uma lista completa das APIs REST dos Azure Mapas, confira [APIs REST dos Azure Mapas](./consumption-model.md).

## <a name="clean-up-resources"></a>Limpar os recursos

Não há recursos que exijam limpeza.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Azure Notebooks, confira

> [!div class="nextstepaction"]
> [Azure Notebooks](https://notebooks.azure.com)