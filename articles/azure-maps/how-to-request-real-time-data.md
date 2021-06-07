---
title: Solicitar dados de trânsito públicos em tempo real com os serviços de mobilidade de mapas de Microsoft Azure (versão prévia)
description: Saiba como solicitar dados de trânsito públicos em tempo real, como entradas em uma parada de trânsito. Consulte como usar os serviços de mobilidade do Azure Maps (versão prévia) para essa finalidade.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: d3e3dc4b0e3bc64a38856da8344583b744ea62b6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96906039"
---
# <a name="request-real-time-public-transit-data-using-the-azure-maps-mobility-services-preview"></a>Solicitar dados de trânsito públicos em tempo real usando os serviços de mobilidade do Azure Maps (versão prévia) 

> [!IMPORTANT]
> Os serviços de mobilidade do Azure Maps estão atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Este artigo mostra como usar os [serviços de mobilidade](/rest/api/maps/mobility) do Azure Maps para solicitar dados de trânsito públicos em tempo real.

Neste artigo, você aprenderá a solicitar as próximas entradas em tempo real para todas as linhas que chegam em uma determinada parada

## <a name="prerequisites"></a>Pré-requisitos

Primeiro, você precisa ter uma conta do Azure Maps e uma chave de assinatura para fazer chamadas para as APIs de trânsito público do Azure Maps. Para obter informações, siga as instruções em [criar uma conta](quick-demo-map-app.md#create-an-azure-maps-account) para criar uma conta do Azure Maps. Siga as etapas em [obter chave primária](quick-demo-map-app.md#get-the-primary-key-for-your-account) para obter a chave primária para sua conta. Para obter mais informações sobre a autenticação nos Azure Mapas, confira [Gerenciar a autenticação nos Azure Mapas](./how-to-manage-authentication.md).

Este artigo usa o [aplicativo Postman](https://www.getpostman.com/apps) para criar chamadas REST. Você pode usar qualquer ambiente de desenvolvimento de API que você preferir.

## <a name="request-real-time-arrivals-for-a-stop"></a>Solicitar entradas em tempo real para uma parada

Para solicitar dados de chegada em tempo real de uma parada de trânsito público em particular, você precisará fazer a solicitação para a [API de entradas em tempo real](/rest/api/maps/mobility/getrealtimearrivalspreview) do serviço de mobilidade do Azure Maps [(versão prévia)](/rest/api/maps/mobility). Você precisará de **metroID** e **StopId** para concluir a solicitação. Para saber mais sobre como solicitar esses parâmetros, consulte nosso guia sobre como [solicitar rotas de trânsito públicas](./how-to-request-transit-data.md).

Vamos usar "522" como nossa ID metro, que é a ID do metro para a área "Seattle – Tacoma – Bellevue, WA". Use "522---2060603" como a ID de parada, essa parada de barramento está em "ne 24 St & 162nd ave ne, Bellevue WA". Para solicitar os próximos cinco dados de chegada em tempo real, para todas as próximas entradas ao vivo neste ponto de interrupção, conclua as seguintes etapas:

1. Abra o aplicativo do postmaster e vamos criar uma coleção para armazenar as solicitações. Próximo à parte superior do aplicativo Postman, selecione **Novo**. Na janela **Criar**, selecione **Coleção**.  Nomeie a coleção e selecione o botão **Criar**.

2. Para criar a solicitação, selecione **Novo** outra vez. Na janela **Criar**, selecione **Solicitação**. Insira um **Nome de solicitação** para a solicitação. Selecione a coleção que você criou na etapa anterior, como o local no qual salvar a solicitação. Em seguida, selecione **Salvar**.

    ![Criar uma solicitação no postmaster](./media/how-to-request-transit-data/postman-new.png)

3. Selecione o método http **Get** na guia Construtor e insira a URL a seguir para criar uma solicitação get. Substitua `{subscription-key}` , por sua chave primária do Azure Maps.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

4. Após uma solicitação bem-sucedida, você receberá a resposta a seguir.  Observe que o parâmetro ' ScheduleType ' define se o tempo de chegada estimado é baseado em dados em tempo real ou estáticos.

    ```JSON
    {
        "results": [
            {
                "arrivalMinutes": 8,
                "scheduleType": "realTime",
                "patternId": "522---4143196",
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            },
            {
                "arrivalMinutes": 25,
                "scheduleType": "realTime",
                "patternId": "522---3510227",
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>Próximas etapas

Saiba como solicitar dados de trânsito usando os serviços de mobilidade (versão prévia):

> [!div class="nextstepaction"]
> [Como solicitar dados de trânsito](how-to-request-transit-data.md)

Explore a documentação da API dos serviços de mobilidade do Azure Maps (versão prévia):

> [!div class="nextstepaction"]
> [Documentação da API dos serviços de mobilidade](/rest/api/maps/mobility)