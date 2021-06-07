---
title: Solicitar dados de elevação usando o serviço de elevação do Azure Maps (versão prévia)
description: Saiba como solicitar dados de elevação usando o serviço de elevação do Azure Maps (versão prévia).
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: d14eda84144105bf2e04f1238284bc58a91c4c03
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98684048"
---
# <a name="request-elevation-data-using-the-azure-maps-elevation-service-preview"></a>Solicitar dados de elevação usando o serviço de elevação do Azure Maps (versão prévia)

> [!IMPORTANT]
> O serviço de elevação do Azure Maps está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O serviço de [elevação](/rest/api/maps/elevation) do Azure Maps fornece APIs para consultar dados de elevação em qualquer lugar na superfície da terra. Você pode solicitar dados de elevação de amostra ao longo de caminhos, dentro de uma caixa delimitadora definida ou em coordenadas específicas. Além disso, você pode usar a [API do bloco render v2-obter mapa](/rest/api/maps/renderv2) para recuperar dados de elevação no formato de bloco. Os blocos são entregues no formato de varredura GeoTIFF. Este artigo mostra como usar o serviço de elevação do mapas do Azure e a API do bloco obter mapa para solicitar dados de elevação. Os dados de elevação podem ser solicitados nos formatos geojson e GeoTIFF.

## <a name="prerequisites"></a>Pré-requisitos

1. [Criar uma conta do Azure Maps no tipo de preço S1](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Obtenha uma chave de assinatura primária](quick-demo-map-app.md#get-the-primary-key-for-your-account), também conhecida como a chave primária ou a chave de assinatura.

Para obter mais informações sobre autenticação no Azure Maps, [gerencie a autenticação no Azure Maps](how-to-manage-authentication.md).

Este artigo usa o aplicativo do [postmaster](https://www.postman.com/) , mas você pode escolher um ambiente de desenvolvimento de API diferente.

## <a name="request-elevation-data-in-raster-tiled-format"></a>Solicitar dados de elevação em formato de xadrez de varredura

Para solicitar dados de elevação no formato de bloco rasterizado, use a [API de bloco render v2-obter mapa](/rest/api/maps/renderv2). Se o bloco puder ser encontrado, a API retornará o bloco como um GeoTIFF. Caso contrário, a API retornará 0. Todos os blocos de DEM de rasterização estão usando o modo terra de GeoId (nível do mar). Neste exemplo, solicitaremos dados de elevação para Mt. Everest.

>[!TIP]
>Para recuperar um bloco em uma área específica no mapa mundial, você precisará localizar o bloco correto no nível de zoom apropriado. Observe também que, WorldDEM cobre toda a landmass global, mas não cobre os oceanos.  Para obter mais informações, consulte [Níveis de Zoom e grade de blocos](zoom-levels-and-tile-grid.md).

1. Abra o aplicativo Postman. Próximo à parte superior do aplicativo Postman, selecione **Novo**. Na janela **Criar**, selecione **Coleção**.  Nomeie a coleção e selecione o botão **Criar**.

2. Para criar a solicitação, selecione **Novo** outra vez. Na janela **Criar**, selecione **Solicitação**. Insira um **Nome de solicitação** para a solicitação. Selecione a coleção que você criou na etapa anterior e clique em **Salvar**.

3. Selecione o método http **Get** na guia Construtor e insira a URL a seguir para solicitar o bloco rasterizado. Para essa solicitação e outras solicitações mencionadas neste artigo, substitua `{Azure-Maps-Primary-Subscription-key}` pela sua chave de assinatura primária.

    ```http
    https://atlas.microsoft.com/map/tile?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=2.0&tilesetId=microsoft.dem&zoom=13&x=6074&y=3432
    ```

4. Clique no botão **Enviar**. Você deve receber o bloco de varredura que contém os dados de elevação no formato GeoTIFF. Cada pixel dentro dos dados brutos do bloco de varredura é do tipo `float` . O valor de cada pixel representa a altura de elevação em metros.

## <a name="request-elevation-data-in-geojson-format"></a>Solicitar dados de elevação no formato geojson

Use as APIs do serviço de elevação (versão prévia) para solicitar dados de elevação no formato geojson. Esta seção lhe mostrará cada uma das três APIs:

* [Obter dados para pontos](/rest/api/maps/elevation/getdataforpoints)
* [Postar dados para pontos](/rest/api/maps/elevation/postdataforpoints)
* [Obter dados para polilinha](/rest/api/maps/elevation/getdataforpolyline)
* [Postar dados para a polilinha](/rest/api/maps/elevation/postdataforpolyline)
* [Obter dados para a caixa delimitadora](/rest/api/maps/elevation/getdataforboundingbox)

>[!IMPORTANT]
> Quando nenhum dado pode ser retornado, todas as APIs retornam `0` .

### <a name="request-elevation-data-for-points"></a>Solicitar dados de elevação para pontos

Neste exemplo, usaremos a [API obter dados para pontos](/rest/api/maps/elevation/getdataforpoints) para solicitar dados de elevação em Mt. Everest e Chamlang montanhas. Em seguida, usaremos a [API post data for Points](/rest/api/maps/elevation/postdataforpoints) para solicitar dados de elevação usando os mesmos dois pontos. As latitudes e longitudes na URL devem estar no grau decimal WGS84 (World geodésico System).

 >[!IMPORTANT]
 >Devido ao limite de comprimento de caracteres de URL de 2048, não é possível passar mais de 100 coordenadas como uma cadeia de caracteres delimitada por pipeline em uma solicitação GET de URL. Se você pretende passar mais de 100 coordenadas como uma cadeia de caracteres delimitada por pipeline, use os dados de POSTAgem para pontos.

1. Para criar a solicitação, selecione **Novo** outra vez. Na janela **Criar**, selecione **Solicitação**. Insira um **Nome de solicitação** para a solicitação. Selecione a coleção que você criou na etapa anterior e clique em **Salvar**.

2. Selecione o método http **Get** na guia Construtor e insira a URL a seguir. Para essa solicitação e outras solicitações mencionadas neste artigo, substitua `{Azure-Maps-Primary-Subscription-key}` pela sua chave de assinatura primária.

    ```http
    https://atlas.microsoft.com/elevation/point/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&points=-73.998672,40.714728|150.644,-34.397
    ```

3. Clique no botão **Enviar**.  Você receberá a seguinte resposta JSON:

    ```json
    {
    "data": [
        {
            "coordinate": {
                "latitude": 40.714728,
                "longitude": -73.998672
            },
            "elevationInMeter": 12.142355447638208
        },
        {
            "coordinate": {
                "latitude": -34.397,
                "longitude": 150.644
            },
            "elevationInMeter": 384.47041445517846
        }
    ]
    }
    ```

4. Agora, chamaremos os [dados de post para a API Points](/rest/api/maps/elevation/postdataforpoints) para obter dados de elevação para os mesmos dois pontos. Selecione o método http **post** na guia Construtor e insira a URL a seguir. Para essa solicitação e outras solicitações mencionadas neste artigo, substitua `{Azure-Maps-Primary-Subscription-key}` pela sua chave de assinatura primária.

    ```http
    https://atlas.microsoft.com/elevation/point/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

5. Nos **Cabeçalhos** da solicitação **POST**, defina `Content-Type` como `application/json`. No **corpo**, forneça as informações do ponto de coordenadas abaixo. Ao terminar, clique em **Enviar**.

     ```json
    [
        {
            "lon": -73.998672,
            "lat": 40.714728
        },
        {
            "lon": 150.644,
            "lat": -34.397
        }
    ]
    ```

### <a name="request-elevation-data-samples-along-a-polyline"></a>Exemplos de dados de elevação de solicitação ao longo de uma polilinha

Neste exemplo, usaremos os [dados get para Polyline](/rest/api/maps/elevation/getdataforpolyline) para solicitar cinco exemplos de dados de elevação espaçados igualmente em uma linha reta entre as coordenadas em Mt. Everest e Chamlang montanhas. Ambas as coordenadas devem ser definidas no formato Long/Lat. Se você não especificar um valor para o `samples` parâmetro, o número de amostras padrão será 10. O número máximo de amostras é 2.000.

Em seguida, usaremos a linha obter dados para a polilinha para solicitar três amostras espaçadas igualmente dos dados de elevação ao longo de um caminho. Vamos definir o local preciso para os exemplos passando três pares de coordenadas de longa/Lat.

Por fim, usaremos os [dados post para a API Polyline](/rest/api/maps/elevation/postdataforpolyline) para solicitar dados de elevação com os mesmos três exemplos igualmente espaçados.

As latitudes e longitudes na URL devem estar no grau decimal WGS84 (World geodésico System).

 >[!IMPORTANT]
 >Devido ao limite de comprimento de caracteres de URL de 2048, não é possível passar mais de 100 coordenadas como uma cadeia de caracteres delimitada por pipeline em uma solicitação GET de URL. Se você pretende passar mais de 100 coordenadas como uma cadeia de caracteres delimitada por pipeline, use os dados de POSTAgem para pontos.

1. Selecione **Novo**. Na janela **Criar**, selecione **Solicitação**. Insira o **Nome da solicitação** e selecione uma coleção. Clique em **Save** (Salvar).

2. Selecione o método http **Get** na guia Construtor e insira a URL a seguir. Para essa solicitação e outras solicitações mencionadas neste artigo, substitua `{Azure-Maps-Primary-Subscription-key}` pela sua chave de assinatura primária.

   ```http
    https://atlas.microsoft.com/elevation/line/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&lines=-73.998672,40.714728|150.644,-34.397&samples=5
    ```

3. Clique no botão **Enviar**.  Você receberá a seguinte resposta JSON:

    ```JSON
    {
        "data": [
            {
                "coordinate": {
                    "latitude": 40.714728,
                    "longitude": -73.998672
                },
                "elevationInMeter": 12.14236
            },
            {
                "coordinate": {
                    "latitude": 21.936796000000001,
                    "longitude": -17.838003999999998
                },
                "elevationInMeter": 0.0
            },
            {
                "coordinate": {
                    "latitude": 3.1588640000000012,
                    "longitude": 38.322664000000003
                },
                "elevationInMeter": 598.66943
            },
            {
                "coordinate": {
                    "latitude": -15.619067999999999,
                    "longitude": 94.483332000000019
                },
                "elevationInMeter": 0.0
            },
            {
                "coordinate": {
                    "latitude": -34.397,
                    "longitude": 150.644
                },
                "elevationInMeter": 384.47041
            }
        ]
    }
    ```

4. Agora, solicitaremos três exemplos de dados de elevação ao longo de um caminho entre as coordenadas na montagem Everest, Chamlang e Jannu montanhas. Na seção **params** , copie a seguinte matriz de coordenadas para o valor da `lines` chave de consulta.

    ```html
        86.9797222, 27.775|86.9252778, 27.9880556 | 88.0444444, 27.6822222
    ```

5. Altere o `samples` valor da chave de consulta para `3` .  A imagem abaixo mostra os novos valores.

     :::image type="content" source="./media/how-to-request-elevation-data/get-elevation-samples.png" alt-text="Recuperar três exemplos de dados de elevação.":::

6. Clique no botão azul **Send** . Você receberá a seguinte resposta JSON:

    ```json
    {
        "data": [
            {
                "coordinate": {
                    "latitude": 27.775,
                    "longitude": 86.9797222
                },
                "elevationInMeter": 7116.0348851572589
            },
            {
                "coordinate": {
                    "latitude": 27.737403546316028,
                    "longitude": 87.411180791156454
                },
                "elevationInMeter": 1798.6945512521534
            },
            {
                "coordinate": {
                    "latitude": 27.682222199999998,
                    "longitude": 88.0444444
                },
                "elevationInMeter": 7016.9372013588072
            }
        ]
    }
    ```

7. Agora, chamaremos os [dados de postagem para a API de polilinha](/rest/api/maps/elevation/postdataforpolyline) para obter dados de elevação para os mesmos três pontos. Selecione o método http **post** na guia Construtor e insira a URL a seguir. Para essa solicitação e outras solicitações mencionadas neste artigo, substitua `{Azure-Maps-Primary-Subscription-key}` pela sua chave de assinatura primária.

    ```http
    https://atlas.microsoft.com/elevation/line/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&samples=5
    ```

8. Nos **Cabeçalhos** da solicitação **POST**, defina `Content-Type` como `application/json`. No **corpo**, forneça as informações do ponto de coordenadas abaixo. Ao terminar, clique em **Enviar**.

     ```json
    [
        {
            "lon": 86.9797222,
            "lat": 27.775
        },
        {
            "lon": 86.9252778,
            "lat": 27.9880556
        },
        {
            "lon": 88.0444444,
            "lat": 27.6822222
        }
    ]
    ```

### <a name="request-elevation-data-by-bounding-box"></a>Solicitar dados de elevação por caixa delimitadora

Agora, usaremos a [caixa obter dados para delimitador](/rest/api/maps/elevation/getdataforboundingbox) para solicitar dados de elevação perto de Mt. Rainier, WA. Os dados de elevação serão retornados em locais igualmente espaçados dentro de uma caixa delimitadora. A área delimitadora definida por (2) conjuntos de coordenadas de lat/long (Latitude do Sul, longitude ocidental | latitude do Norte, longitude Oriental) é dividida em linhas e colunas. As bordas da conta da caixa delimitadora são duas (2) das linhas e duas (2) das colunas. As elevações são retornadas para os vértices de grade criados nas interseções de linha e coluna. Até 2000 elevações podem ser retornadas em uma única solicitação.

Neste exemplo, especificaremos Rows = 3 e Columns = 6. 18 valores de elevação são retornados na resposta. No diagrama a seguir, os valores de elevação são ordenados a partir do canto do sudoeste e, em seguida, continua oeste para leste e Sul para norte.  Os pontos de elevação são numerados na ordem em que são retornados.

:::image type="content" source="./media/how-to-request-elevation-data/bounding-box.png" border="false" alt-text="As coordenadas da caixa delimitadora nos cantos de NE e SE.":::

1. Selecione **Novo**. Na janela **Criar**, selecione **Solicitação**. Insira o **Nome da solicitação** e selecione uma coleção. Clique em **Save** (Salvar).

2. Selecione o método http **Get** na guia Construtor e insira a URL a seguir. Para essa solicitação e outras solicitações mencionadas neste artigo, substitua `{Azure-Maps-Primary-Subscription-key}` pela sua chave de assinatura primária.

    ```http
    https://atlas.microsoft.com/elevation/lattice/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&bounds=-121.66853362143818, 46.84646479863713,-121.65853362143818, 46.85646479863713&rows=2&columns=3
    ```

3. Clique no botão azul **Send** . 18 exemplos de dados de elevação, um para cada vértice da grade, são retornados na resposta.

    ```json
    {
    "data": [
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2298.6581875651746
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2306.3980756609963
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2279.3385479564113
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2233.1549264690366
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2196.4485923541492
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2133.1756767157253
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2345.3227848228803
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2292.2449195443587
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2270.5867788258074
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2296.8311427390604
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2266.0729430891065
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2242.216346631234
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2378.460838833359
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2327.6761137260387
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2208.3782743402949
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2106.9526472760981
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2054.3270174034078
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2030.6438331110671
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2318.753153399402
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2253.88875188271
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2136.6145845357587
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2073.6734467948486
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2042.994055784251
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 1988.3631481900356
        }
    ]
    }
    ```

## <a name="samples-use-elevation-service-preview-apis-in-azure-maps-control"></a>Exemplos: usar as APIs do serviço de elevação (versão prévia) no controle de mapas do Azure

### <a name="get-elevation-data-by-coordinate-position"></a>Obter dados de elevação por posição de coordenada

A página da Web de exemplo a seguir mostra como usar o controle de mapa para exibir dados de elevação em um ponto de coordenadas. Quando o usuário arrastar o marcador, o mapa exibirá os dados de elevação em um pop-up.

<br/>

<iframe height="500" style="width:100%;" scrolling="no" title="Obter elevação na posição" src="https://codepen.io/azuremaps/embed/c840b510e113ba7cb32809591d5f96a2?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte a caneta <a href='https://codepen.io/azuremaps/pen/c840b510e113ba7cb32809591d5f96a2'>obter elevação na posição</a> pelo Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="get-elevation-data-by-bounding-box"></a>Obter dados de elevação por caixa delimitadora

A página da Web de exemplo a seguir mostra como usar o controle de mapa para exibir dados de elevação contidos em uma caixa delimitadora. O usuário define a caixa delimitadora clicando no `square` ícone no canto superior esquerdo e desenhando o quadrado em qualquer lugar do mapa. Em seguida, o controle de mapa renderizará os dados de elevação de acordo com as cores especificadas na chave localizada no canto superior direito.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Elevações por caixa delimitadora" src="https://codepen.io/azuremaps/embed/619c888c70089c3350a3e95d499f3e48?height=500&theme-id=default&default-tab=js,result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte as <a href='https://codepen.io/azuremaps/pen/619c888c70089c3350a3e95d499f3e48'>elevações de caneta por caixa delimitadora</a> pelo Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="get-elevation-data-by-polyline-path"></a>Obter dados de elevação por caminho de polilinha

A página da Web de exemplo a seguir mostra como usar o controle de mapa para exibir dados de elevação ao longo de um caminho. O usuário define o caminho clicando no `Polyline` ícone no canto superior esquerdo e desenhando a polilinha no mapa. Em seguida, o controle de mapa renderiza os dados de elevação em cores que são especificadas na chave localizada no canto superior direito.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Gradiente de caminho de elevação" src="https://codepen.io/azuremaps/embed/7bee08e5cb13d05cb0a11636b60f14ca?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/7bee08e5cb13d05cb0a11636b60f14ca'>gradiente de caminho de elevação</a> de caneta pelo Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) em <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Próximas etapas

Para explorar ainda mais as APIs de elevação do Azure Maps (versão prévia), consulte:

> [!div class="nextstepaction"]
> [Elevação (visualização) – obter dados de coordenadas longas de lat](/rest/api/maps/elevation/getdataforpoints)

> [!div class="nextstepaction"]
> [Elevação (visualização) – obter dados para a caixa delimitadora](/rest/api/maps/elevation/getdataforboundingbox)

> [!div class="nextstepaction"]
> [Elevação (visualização) – obter dados para a polilinha](/rest/api/maps/elevation/getdataforpolyline)

> [!div class="nextstepaction"]
> [Renderizar v2 – bloco obter mapa](/rest/api/maps/renderv2)

Para obter uma lista completa das APIs REST do Azure Mapas, confira:

> [!div class="nextstepaction"]
> [APIs REST do Azure Mapas](/rest/api/maps/)