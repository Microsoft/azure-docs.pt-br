---
title: 'Tutorial: Rotear veículos elétricos usando Azure Notebooks (Python) com o Microsoft Azure Mapas'
description: Tutorial sobre como rotear veículos elétricos usando as APIs de roteiros dos Microsoft Azure Mapas e do Azure Notebooks
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 7341d1f07e8814edcad7b84f6b3b46c7bece3159
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98680325"
---
# <a name="tutorial-route-electric-vehicles-by-using-azure-notebooks-python"></a>Tutorial: Rotear veículos elétricos usando o Azure Notebooks (Python)

Os Azure Mapas são um portfólio de APIs de serviço geoespacial que são nativamente integradas ao Azure. Essas APIs permitem que desenvolvedores, empresas e ISVs desenvolvam aplicativos com reconhecimento de localização e soluções de IoT, mobilidade, logística e acompanhamento de ativos. 

As APIs REST dos Azure Mapas podem ser chamadas em linguagens como Python e R para habilitar a análise de dados geoespaciais e cenários de machine learning. Os Azure Mapas oferecem um conjunto robusto de [APIs de roteamento](/rest/api/maps/route) que permitem aos usuários calcular rotas entre vários pontos de dados. Os cálculos se baseiam em várias condições, como tipo de veículo ou área acessível. 

Neste tutorial, você ajudará um motorista cuja bateria do veículo elétrico está acabando. O motorista precisa encontrar a estação de carga mais próxima possível da localização do veículo.

Neste tutorial, você irá:

> [!div class="checklist"]
> * Criar e executar um arquivo do Jupyter Notebook no [Azure Notebooks](https://notebooks.azure.com) na nuvem.
> * Chamar as APIs REST dos Azure Mapas no Python.
> * Pesquisar por um intervalo alcançável com base no modelo de consumo do veículo elétrico.
> * Pesquisar postos de recarga de veículos elétricos dentro do intervalo acessível ou isócrono.
> * Renderizar o limite do intervalo alcançável e as estações de carregamento em um mapa.
> * Localizar e visualizar uma rota para o posto de recarga de veículos elétricos mais próximo de acordo com o tempo de condução.


## <a name="prerequisites"></a>Pré-requisitos 

Para concluir este tutorial, primeiro, é necessário criar uma conta dos Azure Mapas e obter a chave primária (chave de assinatura). 

Para criar a assinatura de uma conta do Azure Mapas, siga as instruções em [Criar uma conta](quick-demo-map-app.md#create-an-azure-maps-account). Você precisa ter uma assinatura de conta do Azure Mapas da faixa de preço S1. 

Para obter a chave de assinatura primária da sua conta, siga as instruções em [Obter chave primária](quick-demo-map-app.md#get-the-primary-key-for-your-account).

Para obter mais informações sobre a autenticação nos Azure Mapas, confira [Gerenciar a autenticação nos Azure Mapas](./how-to-manage-authentication.md).

## <a name="create-an-azure-notebooks-project"></a>Criar um projeto do Azure Notebooks

Para acompanhar este tutorial, você precisará criar um projeto do Azure Notebooks e baixar e executar o arquivo do Jupyter Notebook. O arquivo do Jupyter Notebook contém o código Python, que implementa o cenário neste tutorial. Para criar um projeto do Azure Notebooks e carregar o documento do Jupyter Notebook nele, siga estas etapas:

1. Vá até [Azure Notebooks](https://notebooks.azure.com) e entre. Para saber mais, confira [Início Rápido: Entrar e definir uma ID de usuário](https://notebooks.azure.com).
1. Na parte superior da página de seu perfil público, selecione **Meus Projetos**.

    ![O botão Meus Projetos](./media/tutorial-ev-routing/myproject.png)

1. Na página **Meus Projetos**, selecione **Novo Projeto**.
 
   ![O botão Novo Projeto](./media/tutorial-ev-routing/create-project.png)

1. No painel **Criar Projeto**, insira um nome e uma ID de projeto.
 
    ![O painel Criar Projeto](./media/tutorial-ev-routing/create-project-window.png)

1. Selecione **Criar**.

1. Após a criação do projeto, baixe este [arquivo de documento do Jupyter Notebook](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/EVrouting.ipynb) do [repositório do Jupyter Notebook dos Azure Mapas](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook).

1. Na lista de projetos da página **Meus Projetos**, escolha seu projeto e selecione **Carregar** para carregar o arquivo de documento do Jupyter Notebook. 

    ![carregar o Jupyter Notebook](./media/tutorial-ev-routing/upload-notebook.png)

1. Faça upload do arquivo do computador e, em seguida, selecione **Concluído**.

1. Depois que o upload for concluído com êxito, o arquivo será exibido na página do projeto. Clique duas vezes no arquivo para abri-lo como um Jupyter Notebook.

Tente entender a funcionalidade implementada no arquivo do Jupyter Notebook. Execute o código, no arquivo do Jupyter Notebook, uma célula por vez. Execute o código em cada célula selecionando o botão **Executar** na parte superior do aplicativo do Jupyter Notebook.

  ![O botão Executar](./media/tutorial-ev-routing/run.png)

## <a name="install-project-level-packages"></a>Instalar pacotes do nível do projeto

Para executar o código no Jupyter Notebook, instale os pacotes no nível do projeto seguindo estas etapas:

1. Baixe o arquivo [*requirements.txt*](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/requirements.txt) do [repositório do Jupyter Notebook dos Azure Mapas](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook) e carregue-o no projeto.
1. No painel do projeto, selecione **Configurações do Projeto**. 
1. No painel **Configurações do Projeto**, selecione a guia **Ambiente** e, em seguida, selecione **Adicionar**.
1. Em **Etapas de Instalação do Ambiente**, faça o seguinte:   
    a. Na primeira lista suspensa, selecione **Requirements.txt**.  
    b. Na segunda lista suspensa, selecione o arquivo *requirements.txt*.  
    c. Na terceira lista suspensa, selecione **Python Versão 3.6** como a versão.
1. Clique em **Salvar**.

    ![Instalar Pacotes](./media/tutorial-ev-routing/install-packages.png)

## <a name="load-the-required-modules-and-frameworks"></a>Carregar as estruturas e os módulos necessários

Para carregar todas as estruturas e todos os módulos necessários, execute o script a seguir.

```Python
import time
import aiohttp
import urllib.parse
from IPython.display import Image, display
```

## <a name="request-the-reachable-range-boundary"></a>Solicitar o limite do intervalo acessível

Uma empresa de entrega de pacotes tem alguns veículos elétricos na frota. Durante o dia, os veículos elétricos precisam ser recarregados sem a necessidade de retornar ao depósito. Sempre que a carga restante cai para menos de uma hora, você pesquisa por um conjunto de estações de carga que estão em um intervalo acessível. Essencialmente, você pesquisa por uma estação de carga quando a bateria está com pouca carga. Além disso, você obtém as informações de limite referentes a esse intervalo de estações de carga. 

Como a empresa prefere usar rotas que exigem um equilíbrio entre economia e velocidade, o routeType solicitado é *eco*. O script a seguir chama a [API Obter Intervalo de Rotas](/rest/api/maps/route/getrouterange) do serviço de roteiros do Azure Mapas. Ele usa parâmetros do modelo de consumo do veículo. Em seguida, o script analisa a resposta para criar um objeto de polígono do formato GeoJSON, que representa o intervalo máximo acessível do carro.

Para determinar os limites do intervalo acessível do veículo elétrico, execute o script na seguinte célula:

```python
subscriptionKey = "Your Azure Maps key"
currentLocation = [34.028115,-118.5184279]
session = aiohttp.ClientSession()

# Parameters for the vehicle consumption model 
travelMode = "car"
vehicleEngineType = "electric"
currentChargeInkWh=45
maxChargeInkWh=80
timeBudgetInSec=550
routeType="eco"
constantSpeedConsumptionInkWhPerHundredkm="50,8.2:130,21.3"


# Get boundaries for the electric vehicle's reachable range.
routeRangeResponse = await (await session.get("https://atlas.microsoft.com/route/range/json?subscription-key={}&api-version=1.0&query={}&travelMode={}&vehicleEngineType={}&currentChargeInkWh={}&maxChargeInkWh={}&timeBudgetInSec={}&routeType={}&constantSpeedConsumptionInkWhPerHundredkm={}"
                                              .format(subscriptionKey,str(currentLocation[0])+","+str(currentLocation[1]),travelMode, vehicleEngineType, currentChargeInkWh, maxChargeInkWh, timeBudgetInSec, routeType, constantSpeedConsumptionInkWhPerHundredkm))).json()

polyBounds = routeRangeResponse["reachableRange"]["boundary"]

for i in range(len(polyBounds)):
    coordList = list(polyBounds[i].values())
    coordList[0], coordList[1] = coordList[1], coordList[0]
    polyBounds[i] = coordList

polyBounds.pop()
polyBounds.append(polyBounds[0])

boundsData = {
               "geometry": {
                 "type": "Polygon",
                 "coordinates": 
                   [
                      polyBounds
                   ]
                }
             }
```

## <a name="search-for-electric-vehicle-charging-stations-within-the-reachable-range"></a>Pesquisar postos de recarga de veículos elétricos dentro do intervalo acessível

Depois de determinar o intervalo acessível (isócrono) para o veículo elétrico, pesquise os postos de recarga nesse intervalo. 

O script a seguir chama a [API Pós-pesquisa dentro da Geometria](/rest/api/maps/search/postsearchinsidegeometry) dos Azure Mapas. Ele pesquisa estações de recarga para o veículo elétrico, dentro dos limites do intervalo máximo alcançável do carro. Em seguida, o script analisa a resposta em uma matriz de localizações acessíveis.

Para pesquisar os postos de recarga de veículos elétricos dentro do intervalo acessível, execute o seguinte script:

```python
# Search for electric vehicle stations within reachable range.
searchPolyResponse = await (await session.post(url = "https://atlas.microsoft.com/search/geometry/json?subscription-key={}&api-version=1.0&query=electric vehicle station&idxSet=POI&limit=50".format(subscriptionKey), json = boundsData)).json() 

reachableLocations = []
for loc in range(len(searchPolyResponse["results"])):
                location = list(searchPolyResponse["results"][loc]["position"].values())
                location[0], location[1] = location[1], location[0]
                reachableLocations.append(location)
```

## <a name="upload-the-reachable-range-and-charging-points-to-azure-maps-data-service-preview"></a>Carregar o intervalo acessível e os pontos de recarga no serviço Dados dos Azure Mapas (versão prévia)

Em um mapa, o ideal é visualizar as estações de carga e os limites do intervalo máximo acessível para o veículo elétrico. Para fazer isso, carregue os dados de limite e os dados das estações de carga como objetos geojson no serviço Dados dos Azure Mapas (versão prévia). Use a [API de Upload de Dados](/rest/api/maps/data/uploadpreview). 

Para carregar os dados de pontos de recarga e de limite no serviço Dados dos Azure Mapas, execute as duas seguintes células:

```python
rangeData = {
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "properties": {},
      "geometry": {
        "type": "Polygon",
        "coordinates": [
          polyBounds
        ]
      }
    }
  ]
}

# Upload the range data to Azure Maps Data service (Preview).
uploadRangeResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = rangeData)

rangeUdidRequest = uploadRangeResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getRangeUdid = await (await session.get(rangeUdidRequest)).json()
    if 'udid' in getRangeUdid:
        break
    else:
        time.sleep(0.2)
rangeUdid = getRangeUdid["udid"]
```

```python
poiData = {
    "type": "FeatureCollection",
    "features": [
      {
        "type": "Feature",
        "properties": {},
        "geometry": {
            "type": "MultiPoint",
            "coordinates": reachableLocations
        }
    }
  ]
}

# Upload the electric vehicle charging station data to Azure Maps Data service (Preview).
uploadPOIsResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = poiData)

poiUdidRequest = uploadPOIsResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getPoiUdid = await (await session.get(poiUdidRequest)).json()
    if 'udid' in getPoiUdid:
        break
    else:
        time.sleep(0.2)
poiUdid = getPoiUdid["udid"]
```

## <a name="render-the-charging-stations-and-reachable-range-on-a-map"></a>Renderizar os postos de recarga e o intervalo acessível em um mapa

Depois de fazer upload dos dados para o serviço de dados, chame o [Serviço Obter Imagem de Mapa](/rest/api/maps/render/getmapimage) dos Azure Mapas. Esse serviço é usado para renderizar os pontos de carga e o limite máximo alcançável na imagem do mapa estático executando o seguinte script:

```python
# Get boundaries for the bounding box.
def getBounds(polyBounds):
    maxLon = max(map(lambda x: x[0], polyBounds))
    minLon = min(map(lambda x: x[0], polyBounds))

    maxLat = max(map(lambda x: x[1], polyBounds))
    minLat = min(map(lambda x: x[1], polyBounds))
    
    # Buffer the bounding box by 10 percent to account for the pixel size of pins at the ends of the route.
    lonBuffer = (maxLon-minLon)*0.1
    minLon -= lonBuffer
    maxLon += lonBuffer

    latBuffer = (maxLat-minLat)*0.1
    minLat -= latBuffer
    maxLat += latBuffer
    
    return [minLon, maxLon, minLat, maxLat]

minLon, maxLon, minLat, maxLat = getBounds(polyBounds)

path = "lcff3333|lw3|la0.80|fa0.35||udid-{}".format(rangeUdid)
pins = "custom|an15 53||udid-{}||https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/images/icons/ev_pin.png".format(poiUdid)

encodedPins = urllib.parse.quote(pins, safe='')

# Render the range and electric vehicle charging points on the map.
staticMapResponse =  await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&pins={}&path={}&bbox={}&zoom=12".format(subscriptionKey,encodedPins,path,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

poiRangeMap = await staticMapResponse.content.read()

display(Image(poiRangeMap))
```

![Um mapa que mostra o intervalo de localização](./media/tutorial-ev-routing/location-range.png)


## <a name="find-the-optimal-charging-station"></a>Encontrar o posto de recarga ideal

Primeiro, determine todas as estações de carga potenciais dentro do intervalo acessível. Em seguida, descubra quais delas podem ser alcançadas em um período mínimo. 

O script a seguir chama a [API de Roteiros de Matriz](/rest/api/maps/route/postroutematrix) do Azure Mapas. Ele retorna a localização do veículo especificado, o tempo de viagem e a distância de cada estação carga. O script na próxima célula analisa a resposta para localizar o posto de recarga acessível mais próximo com relação ao tempo.

Para encontrar o posto de recarga acessível mais próximo ao qual se pode chegar no menor período de tempo, execute o script na seguinte célula:

```python
locationData = {
            "origins": {
              "type": "MultiPoint",
              "coordinates": [[currentLocation[1],currentLocation[0]]]
            },
            "destinations": {
              "type": "MultiPoint",
              "coordinates": reachableLocations
            }
         }

# Get the travel time and distance to each specified charging station.
searchPolyRes = await (await session.post(url = "https://atlas.microsoft.com/route/matrix/json?subscription-key={}&api-version=1.0&routeType=shortest&waitForResults=true".format(subscriptionKey), json = locationData)).json()

distances = []
for dist in range(len(reachableLocations)):
    distances.append(searchPolyRes["matrix"][0][dist]["response"]["routeSummary"]["travelTimeInSeconds"])

minDistLoc = []
minDistIndex = distances.index(min(distances))
minDistLoc.extend([reachableLocations[minDistIndex][1], reachableLocations[minDistIndex][0]])
closestChargeLoc = ",".join(str(i) for i in minDistLoc)
```

## <a name="calculate-the-route-to-the-closest-charging-station"></a>Calcular a rota para o posto de recarga mais próximo

Agora que você encontrou o posto de recarga mais próximo, chame a [API Obter Trajetos de Rota](/rest/api/maps/route/getroutedirections) para solicitar a rota detalhada partindo da localização atual do veículo elétrico ao posto de recarga.

Para obter a rota para o posto de recarga e analisar a resposta a fim de criar um objeto GeoJSON que representa a rota, execute o script na seguinte célula:

```python
# Get the route from the electric vehicle's current location to the closest charging station. 
routeResponse = await (await session.get("https://atlas.microsoft.com/route/directions/json?subscription-key={}&api-version=1.0&query={}:{}".format(subscriptionKey, str(currentLocation[0])+","+str(currentLocation[1]), closestChargeLoc))).json()

route = []
for loc in range(len(routeResponse["routes"][0]["legs"][0]["points"])):
                location = list(routeResponse["routes"][0]["legs"][0]["points"][loc].values())
                location[0], location[1] = location[1], location[0]
                route.append(location)

routeData = {
         "type": "LineString",
         "coordinates": route
     }
```

## <a name="visualize-the-route"></a>Visualizar a rota

Para ajudar a visualizar a rota, primeiro carregue os dados de rota como um objeto geojson no serviço Dados dos Azure Mapas (versão prévia). Para fazer isso, use a [API de Upload de Dados](/rest/api/maps/data/uploadpreview) dos Azure Mapas. Em seguida, chame o serviço de renderização, a [API Obter Imagem do Mapa](/rest/api/maps/render/getmapimage), para renderizar a rota no mapa e visualizá-la.

Para obter uma imagem para a rota renderizada no mapa, execute o seguinte script:

```python
# Upload the route data to Azure Maps Data service (Preview).
routeUploadRequest = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = routeData)

udidRequestURI = routeUploadRequest.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    udidRequest = await (await session.get(udidRequestURI)).json()
    if 'udid' in udidRequest:
        break
    else:
        time.sleep(0.2)

udid = udidRequest["udid"]

destination = route[-1]

destination[1], destination[0] = destination[0], destination[1]

path = "lc0f6dd9|lw6||udid-{}".format(udid)
pins = "default|codb1818||{} {}|{} {}".format(str(currentLocation[1]),str(currentLocation[0]),destination[1],destination[0])


# Get boundaries for the bounding box.
minLat, maxLat = (float(destination[0]),currentLocation[0]) if float(destination[0])<currentLocation[0] else (currentLocation[0], float(destination[0]))
minLon, maxLon = (float(destination[1]),currentLocation[1]) if float(destination[1])<currentLocation[1] else (currentLocation[1], float(destination[1]))

# Buffer the bounding box by 10 percent to account for the pixel size of pins at the ends of the route.
lonBuffer = (maxLon-minLon)*0.1
minLon -= lonBuffer
maxLon += lonBuffer

latBuffer = (maxLat-minLat)*0.1
minLat -= latBuffer
maxLat += latBuffer

# Render the route on the map.
staticMapResponse = await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&&path={}&pins={}&bbox={}&zoom=16".format(subscriptionKey,path,pins,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

staticMapImage = await staticMapResponse.content.read()

await session.close()
display(Image(staticMapImage))
```

![Um mapa que mostra a rota](./media/tutorial-ev-routing/route.png)

Neste tutorial, você aprendeu a chamar as APIs REST dos Azure Mapas diretamente e a visualizar os dados dos Azure Mapas usando o Python.

Para explorar as APIs dos Azure Mapas que são usadas neste tutorial, confira:

* [Obter intervalo de rotas](/rest/api/maps/route/getrouterange)
* [Pesquisa POST dentro da geometria](/rest/api/maps/search/postsearchinsidegeometry)
* [Upload de dados](/rest/api/maps/data/uploadpreview)
* [Renderizar – obter imagem do mapa](/rest/api/maps/render/getmapimage)
* [Matrix de rota POST](/rest/api/maps/route/postroutematrix)
* [Obter Trajeto de Rota](/rest/api/maps/route/getroutedirections)
* [APIs REST do Azure Mapas](./consumption-model.md)

## <a name="clean-up-resources"></a>Limpar os recursos

Não há recursos que exijam limpeza.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Azure Notebooks, confira

> [!div class="nextstepaction"]
> [Azure Notebooks](https://notebooks.azure.com)