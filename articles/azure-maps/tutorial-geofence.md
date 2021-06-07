---
title: 'Tutorial: Criar uma cerca geográfica e controlar dispositivos no Microsoft Azure Mapas'
description: Tutorial sobre como configurar uma cerca geográfica. Veja como controlar dispositivos em relação à cerca geográfica usando o serviço espacial dos Azure Mapas
author: anastasia-ms
ms.author: v-stharr
ms.date: 8/20/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 759adea3cf34b79c76b6facec3bd4626ca54107e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98625025"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Tutorial: Configurar um limite geográfico usando o Azure Mapas

Este tutorial ensina as noções básicas de criação e uso dos serviços de cerca geográfica dos Azure Mapas. Você fará isso no contexto do seguinte cenário:

*Um gerente de canteiro de obras precisa acompanhar os equipamentos conforme eles entram e saem dos perímetros de uma área de construção. Sempre que um equipamento sai ou entra nesses perímetros, é enviada uma notificação por email ao gerente de operações.*

O Azure Mapas fornece vários serviços para dar suporte ao acompanhamento de equipamentos que entram e saem da área de construção. Neste tutorial, você:

> [!div class="checklist"]
> * Carregará os [dados GeoJSON de delimitação geográfica](geofence-geojson.md), que definem as áreas do canteiro de obras que você deseja monitorar. Você usará a [API de Upload de Dados](/rest/api/maps/data/uploadpreview) para carregar as cercas geográficas como coordenadas em polígono na sua conta dos Azure Mapas.
> * Configurará dois [aplicativos lógicos](../event-grid/handler-webhooks.md#logic-apps) que, quando disparados, enviam notificações por email para o gerente de operações do canteiro de obras quando o equipamento entra e sai da área de cerca geográfica.
> * Usará a [Grade de Eventos do Azure](../event-grid/overview.md) para assinar eventos de entrada e saída da sua cerca geográfica dos Azure Mapas. Você configurará duas assinaturas de evento de webhook que chamam os pontos de extremidade HTTP definidos nos dois aplicativos lógicos. Em seguida, os aplicativos lógicos enviarão as notificações por email apropriadas de equipamentos que se movem além da cerca geográfica ou que entram nela.
> * Use a [Pesquisa de API de GET de Cerca Geográfica](/rest/api/maps/spatial/getgeofence) para receber notificações quando uma parte do equipamento sair e entrar em áreas de cerca geográfica.

## <a name="prerequisites"></a>Pré-requisitos

1. [Crie uma conta do Azure Mapas](quick-demo-map-app.md#create-an-azure-maps-account).
2. [Obtenha uma chave de assinatura primária](quick-demo-map-app.md#get-the-primary-key-for-your-account), também conhecida como a chave primária ou a chave de assinatura.

Este tutorial usa o aplicativo [Postman](https://www.postman.com/), mas você pode escolher um ambiente de desenvolvimento de API diferente.

## <a name="upload-geofencing-geojson-data"></a>Carregar dados GeoJSON de delimitação geográfica

Neste tutorial, você carregará dados GeoJSON de delimitação geográfica que contenham `FeatureCollection`. O `FeatureCollection` contém duas cercas geográficas que definem áreas poligonais no site de construção. A primeira cerca geográfica não tem restrições ou hora de término. A segunda só pode ser consultada durante o horário comercial (9h às 17h no fuso horário do Pacífico) e não será mais válida após 1º de janeiro de 2022. Para obter mais informações sobre o formato GeoJSON, confira [Dados de GeoJSON de delimitação geográfica](geofence-geojson.md).

>[!TIP]
>Você pode atualizar os dados de delimitação geográfica a qualquer momento. Para saber mais, confira [API de Upload de dados](/rest/api/maps/data/uploadpreview).

1. Abra o aplicativo Postman. Próximo à parte superior, selecione **Novo**. Na janela **Criar**, selecione **Coleção**. Nomeie a coleção e selecione **Criar**.

2. Para criar a solicitação, selecione **Novo** outra vez. Na janela **Criar**, selecione **Solicitação**. Insira um **Nome de solicitação** para a solicitação. Selecione a coleção que você criou na etapa anterior e clique em **Salvar**.

3. Selecione o método HTTP **POST** na guia do construtor e insira a URL a seguir para carregar os dados de delimitação geográfica no Azure Mapas. Para essa solicitação e outras solicitações mencionadas neste artigo, substitua `{Azure-Maps-Primary-Subscription-key}` pela sua chave de assinatura primária.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    O parâmetro `geojson` no caminho da URL representa o formato dos dados que estão sendo carregados.

4. Selecione a guia **Corpo**. Selecione **bruto** e escolha **JSON** como o formato de entrada. Copie e cole os seguintes dados GeoJSON na área de texto **Corpo**:

   ```JSON
   {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13393688201903,
                  47.63829579223815
                ],
                [
                  -122.13389128446579,
                  47.63782047131512
                ],
                [
                  -122.13240802288054,
                  47.63783312249837
                ],
                [
                  -122.13238388299942,
                  47.63829037035086
                ],
                [
                  -122.13393688201903,
                  47.63829579223815
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "1"
          }
        },
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13374376296996,
                  47.63784758098976
                ],
                [
                  -122.13277012109755,
                  47.63784577367854
                ],
                [
                  -122.13314831256866,
                  47.6382813338708
                ],
                [
                  -122.1334782242775,
                  47.63827591198201
                ],
                [
                  -122.13374376296996,
                  47.63784758098976
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "2",
            "validityTime": {
            "expiredTime": "2022-01-01T00:00:00",
            "validityPeriod": [
                {
                  "startTime": "2020-07-15T16:00:00",
                  "endTime": "2020-07-15T24:00:00",
                  "recurrenceType": "Daily",
                  "recurrenceFrequency": 1,
                  "businessDayOnly": true
                }
              ]
            }
          }
        }
      ]
   }
   ```

5. Selecione **Enviar** e aguarde a solicitação ser processada. Depois que a solicitação for concluída, vá para a guia **Cabeçalhos** da resposta. Copie o valor da chave **Local**, que é `status URL`.

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. Para verificar o status da chamada à API, crie uma solicitação HTTP **GET** em `status URL`. Você precisará acrescentar sua chave de assinatura primária à URL para autenticação. A solicitação **GET** deve ser semelhante à seguinte URL:

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Quando a solicitação HTTP **GET** for concluída com êxito, ela retornará um `resourceLocation`. O `resourceLocation` contém o `udid` exclusivo para o conteúdo carregado. Salve esse `udid` para consultar a API de Obtenção de Cerca Geográfica na última seção deste tutorial. Opcionalmente, você poderá usar a URL do `resourceLocation` para recuperar metadados desse recurso na próxima etapa.

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

8. Para recuperar metadados de conteúdo, crie uma solicitação HTTP **GET** na URL do `resourceLocation` que foi recuperada na etapa 7. Certifique-se de acrescentar a sua chave de assinatura primária à URL para autenticação. A solicitação **GET** deve ser semelhante à seguinte URL:

    ```http
   https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

9. Quando a solicitação HTTP **GET** for concluída com êxito, o corpo da resposta conterá o `udid` especificado no `resourceLocation` da etapa 7. Ele também conterá o local para acessar e baixar o conteúdo no futuro e outros metadados sobre o conteúdo. Um exemplo da resposta geral é:

    ```json
    {
        "udid": "{udid}",
        "location": "https://atlas.microsoft.com/mapData/{udid}?api-version=1.0",
        "created": "7/15/2020 6:11:43 PM +00:00",
        "updated": "7/15/2020 6:11:45 PM +00:00",
        "sizeInBytes": 1962,
        "uploadStatus": "Completed"
    }
    ```

## <a name="create-workflows-in-azure-logic-apps"></a>Criar fluxos de trabalho em Aplicativos Lógicos do Azure

Em seguida, você criará dois pontos de extremidade de [aplicativo lógico](../event-grid/handler-webhooks.md#logic-apps) que dispararão uma notificação por email. Confira como criar o primeiro:

1. Entre no [portal do Azure](https://portal.azure.com).

2. No canto superior esquerdo do Portal do Azure, selecione **Criar um recurso**.

3. Na caixa **Pesquisar no Marketplace**, digite **Aplicativo Lógico**.

4. Nos resultados, selecione **Aplicativo Lógico** > **Criar**.

5. Na página **Aplicativo Lógico**, insira os seguintes valores:
    * A **Assinatura** que você deseja usar para esse aplicativo lógico.
    * O nome do **Grupo de recursos** para esse aplicativo lógico. Você pode escolher **Criar** ou **Usar existente** em relação ao grupo de recursos.
    * O **nome do seu Aplicativo Lógico**. Nesse caso, use `Equipment-Enter` como o nome.

    Para este tutorial, mantenha todos os outros valores nas configurações padrão.

    :::image type="content" source="./media/tutorial-geofence/logic-app-create.png" alt-text="Captura de tela da criação de um aplicativo lógico.":::

6. Selecione **Examinar + criar**. Examine as configurações e selecione **Criar** para enviar a implantação. Quando a implantação for concluída com êxito, selecione **Ir para o recurso**. Você será levado para o **Designer de Aplicativo Lógico**.

7. Selecione um tipo de gatilho. Role para baixo até a seção **Iniciar com um gatilho comum**. Selecione **Quando uma solicitação HTTP é recebida**.

     :::image type="content" source="./media/tutorial-geofence/logic-app-trigger.png" alt-text="Captura de tela da criação de um gatilho HTTP do aplicativo lógico.":::

8. No canto superior direito do Designer do Aplicativo Lógico, selecione **Salvar**. A **URL HTTP POST** é gerada automaticamente. Salve a URL. Você precisará dela na próxima seção para criar um ponto de extremidade de evento.

    :::image type="content" source="./media/tutorial-geofence/logic-app-httprequest.png" alt-text="Captura de tela de JSON e URL de solicitação HTTP do Aplicativo Lógico.":::

9. Selecione **+ Nova Etapa**. Agora, escolha uma ação. Digite `outlook.com email` na caixa de pesquisa. Na lista **Ações**, role para baixo e selecione **Enviar um email (V2)** .
  
    :::image type="content" source="./media/tutorial-geofence/logic-app-designer.png" alt-text="Captura de tela da criação de um designer de aplicativo lógico.":::

10. Entre na sua conta do Outlook. Selecione **Sim** para permitir que o aplicativo lógico acesse a conta. Preencha os campos para enviar um email.

    :::image type="content" source="./media/tutorial-geofence/logic-app-email.png" alt-text="Captura de tela da criação de uma etapa enviar email do aplicativo lógico.":::

    >[!TIP]
    > Você pode recuperar os dados de resposta GeoJSON, como `geometryId` ou `deviceId`, em suas notificações de email. Você pode configurar Aplicativos Lógicos para ler os dados enviados pela Grade de Eventos. Para obter informações sobre como configurar os Aplicativos Lógicos para consumir e transmitir dados de evento em notificações por email, confira [Tutorial: enviar notificações por email sobre os eventos do Hub IoT do Azure usando os Aplicativos Lógicos e a Grade de Eventos](../event-grid/publish-iot-hub-events-to-logic-apps.md).

11. No canto superior esquerdo do Designer de Aplicativo Lógico, selecione **Salvar**.

Para criar um segundo Aplicativo Lógico que notifique o gerente quando o equipamento sair do canteiro de obras, repita as etapas de 3 a 11. Dê ao Aplicativo Lógico o nome `Equipment-Exit`.

## <a name="create-azure-maps-events-subscriptions"></a>Criar assinaturas de eventos dos Azure Mapas

O Azure Mapas dá suporte a [três tipos de eventos](../event-grid/event-schema-azure-maps.md). Aqui, você precisa criar duas assinaturas de evento diferentes: uma para os eventos de entrada na cerca geográfica e outra para os eventos de saída.

As etapas a seguir mostram como criar uma assinatura para eventos de entrada na cerca geográfica. Você pode assinar eventos de saída de cerca geográfica repetindo as etapas de maneira semelhante.

1. Acesse sua conta dos Azure Mapas. No painel, selecione **Assinaturas**. Selecione o nome da sua assinatura e **Eventos** no menu de configurações.

    :::image type="content" source="./media/tutorial-geofence/events-tab.png" alt-text="Captura de tela do acesso aos eventos da conta dos Azure Mapas":::

2. Para criar uma assinatura de evento, selecione **+ Assinatura de Evento** na página de eventos.

    :::image type="content" source="./media/tutorial-geofence/create-event-subscription.png" alt-text="Captura de tela da criação de uma assinatura de evento dos Azure Mapas.":::

3. Na página **Criar Assinatura de Eventos**, insira os seguintes valores:
    * O **Nome** da assinatura de evento.
    * O **Esquema de Evento** deve ser *Esquema de Grade de Eventos*.
    * O **Nome do Tópico do Sistema** dessa assinatura de evento, que nesse caso é `Contoso-Construction`.
    * Para **Filtrar os Tipos de Evento**, escolha `Geofence Entered` como o tipo de evento.
    * Para o **Tipo de Ponto de Extremidade**, escolha `Web Hook`.
    * Para **Ponto de extremidade**, copie a URL HTTP POST para o ponto de extremidade de entrada do aplicativo lógico que você criou na seção anterior. Se você se esqueceu de salvá-lo, basta voltar ao Designer de Aplicativo Lógico e copiá-lo na etapa do gatilho HTTP.

    :::image type="content" source="./media/tutorial-geofence/events-subscription.png" alt-text="Captura de tela de detalhes da assinatura de eventos dos Azure Mapas":::

4. Selecione **Criar**.

Repita as etapas 1 a 4 para o ponto de extremidade de saída do aplicativo lógico criado na seção anterior. Na etapa 3, certifique-se de escolher `Geofence Exited` como o tipo de evento.

## <a name="use-spatial-geofence-get-api"></a>Usar a API de Obtenção de Cerca Geográfica Espacial

Use a [API de Obtenção de Cerca Geográfica Espacial](/rest/api/maps/spatial/getgeofence) para enviar notificações por email ao gerene de operações quando um equipamento entrar ou sair das cercas geográficas.

Cada equipamento tem uma `deviceId`. Neste tutorial, você acompanhará um único equipamento, com uma ID exclusiva `device_1`.

O diagrama a seguir mostra as cinco localizações do equipamento ao longo do tempo, começando pela localização de *Início*, que está em algum lugar do lado de fora das cercas geográficas. Neste tutorial, a localização de *Início* é indefinida, já que não consultamos o dispositivo nessa localização.

Se consultarmos a [API de Obtenção de Cerca Geográfica Espacial](/rest/api/maps/spatial/getgeofence) com uma localização de equipamento que indica a entrada inicial em uma cerca geográfica ou a saída dela, a Grade de Eventos chamará o ponto de extremidade do aplicativo lógico apropriado para enviar uma notificação por email ao gerente de operações.

Cada uma das sessões a seguir faz as solicitações de API usando as cinco diferentes coordenadas de localização dos equipamentos.

![Diagrama de mapa de cerca geográfica nos Azure Mapas](./media/tutorial-geofence/geofence.png)

### <a name="equipment-location-1-47638237-122132483"></a>Localização do equipamento 1 (47.638237,-122.132483)

1. Próximo à parte superior do aplicativo Postman, selecione **Novo**. Na janela **Criar**, selecione **Solicitação**. Insira um **Nome de solicitação** para a solicitação. Defina como *Local 1*. Selecione a coleção que você criou na seção [Carregar dados GeoJSON de delimitação geográfica](#upload-geofencing-geojson-data) e escolha **Salvar**.

2. Selecione o método HTTP **GET** na guia do construtor e insira URL a seguir. Substitua `{Azure-Maps-Primary-Subscription-key}` pela chave de assinatura primária e `{udid}` pelo `udid` salvo na seção [Carregar dados GeoJSON de delimitação geográfica](#upload-geofencing-geojson-data).

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

3. Selecione **Enviar**. O GeoJSON a seguir aparecerá na janela de resposta.

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_1",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.638291,
          "nearestLon": -122.132483
        },
        {
          "deviceId": "device_1",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": 999.0,
          "nearestLat": 47.638053,
          "nearestLon": -122.13295
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ```

Na resposta GeoJSON acima, a distância negativa da cerca geográfica do local principal significa que os equipamentos estão dentro da cerca. A distância positiva da cerca geográfica do sublocal indica que os equipamentos estão fora da cerca do sublocal. Como essa é a primeira vez que o dispositivo foi localizado dentro da cerca geográfica do local principal, o parâmetro `isEventPublished` é definido como `true`. O gerente de operações recebe uma notificação por email de que o equipamento entrou na cerca geográfica.

### <a name="location-2-4763800-122132531"></a>Localização 2 (47,63800,-122,132531)

1. Próximo à parte superior do aplicativo Postman, selecione **Novo**. Na janela **Criar**, selecione **Solicitação**. Insira um **Nome de solicitação** para a solicitação. Defina como *Local 2*. Selecione a coleção que você criou na seção [Carregar dados GeoJSON de delimitação geográfica](#upload-geofencing-geojson-data) e escolha **Salvar**.

2. Selecione o método HTTP **GET** na guia do construtor e insira URL a seguir. Substitua `{Azure-Maps-Primary-Subscription-key}` pela chave de assinatura primária e `{udid}` pelo `udid` salvo na seção [Carregar dados GeoJSON de delimitação geográfica](#upload-geofencing-geojson-data).

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

3. Selecione **Enviar**. O seguinte GeoJSON aparecerá na janela de resposta:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.637997,
          "nearestLon": -122.132399
        },
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": 999.0,
          "nearestLat": 47.63789,
          "nearestLon": -122.132809
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": false
    }
    ````

Na resposta GeoJSON acima, o equipamento permaneceu na cerca geográfica do local principal e não entrou na cerca do sublocal. Como resultado, o parâmetro `isEventPublished` é definido como `false`, e o gerente de operações não receberá notificações por email.

### <a name="location-3-4763810783315048-12213336020708084"></a>Localização 3 (47,63810783315048,-122,13336020708084)

1. Próximo à parte superior do aplicativo Postman, selecione **Novo**. Na janela **Criar**, selecione **Solicitação**. Insira um **Nome de solicitação** para a solicitação. Defina como *Local 3*. Selecione a coleção que você criou na seção [Carregar dados GeoJSON de delimitação geográfica](#upload-geofencing-geojson-data) e escolha **Salvar**.

2. Selecione o método HTTP **GET** na guia do construtor e insira URL a seguir. Substitua `{Azure-Maps-Primary-Subscription-key}` pela chave de assinatura primária e `{udid}` pelo `udid` salvo na seção [Carregar dados GeoJSON de delimitação geográfica](#upload-geofencing-geojson-data).

    ```HTTP
      https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
      ```

3. Selecione **Enviar**. O seguinte GeoJSON aparecerá na janela de resposta:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.638294,
          "nearestLon": -122.133359
        },
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": -999.0,
          "nearestLat": 47.638161,
          "nearestLon": -122.133549
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ````

Na resposta GeoJSON acima, o equipamento permaneceu na cerca geográfica do local principal, mas entrou na cerca geográfica do sublocal. Como resultado, o parâmetro `isEventPublished` é definido como `true`. O gerente de operações recebe uma notificação por email indicando que o equipamento entrou na cerca geográfica.

>[!NOTE]
>Se o equipamento tivesse sido transferido para o sublocal após o horário comercial, nenhum evento seria publicado, e o gerente de operações não receberia nenhuma notificação.  

### <a name="location-4-47637988-1221338344"></a>Localização 4 (47,637988,-122,1338344)

1. Próximo à parte superior do aplicativo Postman, selecione **Novo**. Na janela **Criar**, selecione **Solicitação**. Insira um **Nome de solicitação** para a solicitação. Defina como *Local 4*. Selecione a coleção que você criou na seção [Carregar dados GeoJSON de delimitação geográfica](#upload-geofencing-geojson-data) e escolha **Salvar**.

2. Selecione o método HTTP **GET** na guia do construtor e insira URL a seguir. Substitua `{Azure-Maps-Primary-Subscription-key}` pela chave de assinatura primária e `{udid}` pelo `udid` salvo na seção [Carregar dados GeoJSON de delimitação geográfica](#upload-geofencing-geojson-data).

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.637988&userTime=2023-01-16&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

3. Selecione **Enviar**. O seguinte GeoJSON aparecerá na janela de resposta:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.637985,
          "nearestLon": -122.133907
        }
      ],
      "expiredGeofenceGeometryId": [
        "2"
      ],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": false
    }
    ````

Na resposta GeoJSON acima, o equipamento permaneceu na cerca geográfica do local principal, mas saiu da cerca do sublocal. Observe, no entanto, que o valor de `userTime` vem após o `expiredTime`, conforme definido nos dados de cerca geográfica. Como resultado, o parâmetro `isEventPublished` é definido como `false`, e o gerente de operações não receberá uma notificação por email.

### <a name="location-5-4763799--122134505"></a>Localização 5 (47,63799, -122,134505)

1. Próximo à parte superior do aplicativo Postman, selecione **Novo**. Na janela **Criar**, selecione **Solicitação**. Insira um **Nome de solicitação** para a solicitação. Defina como *Local 5*. Selecione a coleção que você criou na seção [Carregar dados GeoJSON de delimitação geográfica](#upload-geofencing-geojson-data) e escolha **Salvar**.

2. Selecione o método HTTP **GET** na guia do construtor e insira URL a seguir. Substitua `{Azure-Maps-Primary-Subscription-key}` pela chave de assinatura primária e `{udid}` pelo `udid` salvo na seção [Carregar dados GeoJSON de delimitação geográfica](#upload-geofencing-geojson-data).

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.63799&lon=-122.134505&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

3. Selecione **Enviar**. O seguinte GeoJSON aparecerá na janela de resposta:

    ```json
    {
      "geometries": [
      {
        "deviceId": "device_01",
        "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
        "geometryId": "1",
        "distance": -999.0,
        "nearestLat": 47.637985,
        "nearestLon": -122.133907
      },
      {
        "deviceId": "device_01",
        "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
        "geometryId": "2",
        "distance": 999.0,
        "nearestLat": 47.637945,
        "nearestLon": -122.133683
      }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ````

Na resposta GeoJSON anterior, o equipamento saiu da cerca geográfica do local principal. Como resultado, o parâmetro `isEventPublished` é definido como `true`, e o gerente de operações receberá uma notificação por email indicando que o equipamento saiu de uma cerca geográfica.


Você também pode [Enviar notificações por email usando a Grade de Eventos e os Aplicativos Lógicos](../event-grid/publish-iot-hub-events-to-logic-apps.md) e verificar [Manipuladores de Eventos com Suporte na Grade de Eventos](../event-grid/event-handlers.md) usando o Azure Mapas.

## <a name="clean-up-resources"></a>Limpar os recursos

Não há recursos que exijam limpeza.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Manipular tipos de conteúdo em Aplicativos Lógicos do Azure](../logic-apps/logic-apps-content-type.md)