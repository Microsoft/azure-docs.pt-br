---
title: Como solicitar dados em tempo real nos mapas do Azure | Microsoft Docs
description: Solicite dados em tempo real usando o serviço de mobilidade de mapas do Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: aaab5ef4d8fc3d60a12f9e9f85f2846695fd1ab4
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329667"
---
# <a name="request-real-time-data-using-the-azure-maps-mobility-service"></a>Solicitar dados em tempo real usando o serviço de mobilidade do Azure mapas

Este artigo mostra como usar o Azure mapas [o serviço de mobilidade](https://aka.ms/AzureMapsMobilityService) para solicitar dados de trânsito em tempo real.

Neste artigo, você aprenderá como:


 * Solicitar a próxima entrada em tempo real para todas as linhas que chegam na parada de determinado
 * Solicite informações em tempo real para uma estação de encaixe de bicicleta determinado.


## <a name="prerequisites"></a>Pré-requisitos

Para fazer todas as chamadas para o trânsito de mapas do Azure pública APIs, você precisa de uma conta de mapas e uma chave. Para obter mais informações sobre como criar uma conta e recuperar uma chave, consulte [Como gerenciar as chaves e a conta dos Mapas do Azure](how-to-manage-account-keys.md).

Este artigo usa o [aplicativo Postman](https://www.getpostman.com/apps) para criar chamadas REST. Você pode usar qualquer ambiente de desenvolvimento de API que você preferir.


## <a name="request-real-time-arrivals-for-a-stop"></a>Chegadas de solicitações em tempo real para uma parada

Para solicitar dados de entrada em tempo real para uma parada de trânsito público específico, você precisará fazer uma solicitação para o [API de entradas em tempo real](https://aka.ms/AzureMapsMobilityRealTimeArrivals) do Azure mapas [o serviço de mobilidade](https://aka.ms/AzureMapsMobilityService). Será necessário o **metroID** e **stopID** para concluir a solicitação. Para saber mais sobre como solicitar esses parâmetros, consulte nossas instruções guia [solicitar as rotas de trânsito público](https://aka.ms/AMapsHowToGuidePublicTransitRouting). 

Vamos usar "522" como nosso metro ID, que é o metro ID para a área de "Seattle – Tacoma – Bellevue, WA" e use a ID de parada "2060603", que é um barramento parar em "Ne 24 e & 162nd Ave Ne, Bellevue WA". Para solicitar dados de entrada em tempo real próximas cinco para todas as chegadas em tempo real próxima nessa parada, conclua as seguintes etapas:

1. Crie uma coleção para armazenar as solicitações. No aplicativo Postman, selecione **New**. No **criar novo** janela, selecione **coleção**. Nome da coleção e selecione o **criar** botão.

2. Para criar a solicitação, selecione **New** novamente. No **criar novo** janela, selecione **solicitar**. Insira um **nome da solicitação** para a solicitação, selecione a coleção que você criou na etapa anterior como o local no qual salvar a solicitação e, em seguida, selecione **salvar**.

    ![Criar uma solicitação no Postman](./media/how-to-request-transit-data/postman-new.png)

3. Selecione o método HTTP GET na guia criador e insira a URL a seguir para criar uma solicitação GET.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=2060603&transitType=bus
    ```

4. Depois de uma solicitação bem-sucedida, você receberá a seguinte resposta.  Observe que 'scheduleType' do parâmetro define se a hora de chegada estimado é baseada em dados em tempo real ou estáticos.

    ```JSON
    {
        "results": [
            {
                "arrivalMinutes": 4,
                "scheduleType": "realTime",
                "patternId": 3860436,
                "line": {
                    "lineId": 2756599,
                    "lineGroupId": 666063,
                    "direction": "forward",
                    "agencyId": 5872,
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": 2060603,
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                }
            },
            {
                "arrivalMinutes": 30,
                "scheduleType": "scheduledTime",
                "patternId": 3860436,
                "line": {
                    "lineId": 2756599,
                    "lineGroupId": 666063,
                    "direction": "forward",
                    "agencyId": 5872,
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": 2060603,
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                }
            }
        ]
    }
    ```


## <a name="real-time-data-for-bike-docking-station"></a>Dados em tempo real para a estação de encaixe de bicicleta

O [API de informações de encaixe de trânsito obter](https://aka.ms/AzureMapsMobilityTransitDock) do serviço de mobilidade do Azure mapas, permite solicitar informações estáticas e em tempo real como disponibilidade e vaga para uma determinada estação de encaixe bicicleta ou scooter. Faremos uma solicitação para obter os dados em tempo real para uma estação de encaixe de bicicletas.

Para fazer uma solicitação para a API de informações de encaixe de trânsito obter, será necessário o **dockId** para essa estação. Você pode obter a ID de encaixe, fazendo uma solicitação de pesquisa para o [obter a API de trânsito próximos](https://aka.ms/AzureMapsMobilityNearbyTransit) e definindo o **objectType** parâmetro para "bikeDock". Siga as etapas abaixo para obter os dados em tempo real de uma estação de encaixe de bicicletas.


### <a name="get-dock-id"></a>Obter a identificação de encaixe

Para obter **dockID**, siga as etapas abaixo para fazer uma solicitação para a API de trânsito próximos obter:

1. No Postman, clique em **nova solicitação** | **solicitação GET** e nomeie-a **Get encaixar ID**.

2.  Na guia criador, selecione a **Obtenha** método HTTP, insira a URL de solicitação a seguir e clique em **enviar**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=121&query=40.7663753,-73.9627498&radius=100&objectType=bikeDock
    ```

3. Depois de uma solicitação bem-sucedida, você receberá a seguinte resposta. Observe que agora temos as **id** na resposta, que pode ser usado posteriormente como um parâmetro de consulta na solicitação para a API de informações de encaixe de trânsito obter.

    ```JSON
    {
        "results": [
            {
                "id": "121---4640799",
                "type": "bikeDock",
                "objectDetails": {
                    "availableVehicles": 0,
                    "vacantLocations": 30,
                    "lastUpdated": "2019-05-21T20:06:59-04:00",
                    "operatorInfo": {
                        "id": "80",
                        "name": "Citi Bike"
                    }
                },
                "position": {
                    "latitude": 40.767128,
                    "longitude": -73.962243
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 40.768039,
                        "longitude": -73.963413
                    },
                    "btmRightPoint": {
                        "latitude": 40.766216,
                        "longitude": -73.961072
                    }
                }
            }
        ]
    }
    ```


### <a name="get-real-time-bike-dock-status"></a>Obter o status de encaixe de bicicleta em tempo real

Siga as etapas abaixo para fazer uma solicitação para o trânsito encaixar Info API obter para obter os dados em tempo real para encaixe selecionado.

1. No Postman, clique em **nova solicitação** | **solicitação GET** e nomeie-a **obter dados em tempo real de encaixe**.

2.  Na guia criador, selecione a **Obtenha** método HTTP, insira a URL de solicitação a seguir e clique em **enviar**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/dock/json?subscription-key={subscription-key}&api-version=1.0&query=121---4640799
    ```

3. Após uma solicitação bem-sucedida, você receberá uma resposta da seguinte estrutura:

    ```JSON
    {
        "availableVehicles": 1,
        "vacantLocations": 29,
        "position": {
            "latitude": 40.767128,
            "longitude": -73.962246
        },
        "lastUpdated": "2019-05-21T20:26:47-04:00",
        "operatorInfo": {
            "id": "80",
            "name": "Citi Bike"
        }
    }
    ```


## <a name="next-steps"></a>Próximas etapas

Saiba como solicitar dados de trânsito usando o serviço de mobilidade:

> [!div class="nextstepaction"]
> [Como solicitar dados de trânsito](how-to-request-transit-data.md)

Explore a documentação da API do serviço de mobilidade do Azure mapas:

> [!div class="nextstepaction"]
> [Documentação da API do serviço de mobilidade](https://aka.ms/AzureMapsMobilityService)
