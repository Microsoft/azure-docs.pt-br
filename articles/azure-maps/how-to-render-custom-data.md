---
title: Como renderizar dados personalizados em um mapa de varredura em mapas do Azure | Microsoft Docs
description: Processar dados personalizados em um mapa de varredura em mapas do Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 46f08aaa33563f620e7a011620730249e903f7b7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60794646"
---
# <a name="render-custom-data-on-a-raster-map"></a>Processar dados personalizados em um mapa de varredura

Este artigo explica como usar o [serviço de imagem estática](https://docs.microsoft.com/rest/api/maps/render/getmapimage) com funcionalidade de composição de imagem para permitir que as sobreposições na parte superior de um mapa de varredura. Composição de imagem inclui a capacidade de obter um bloco de varredura, com dados adicionais, como anotações personalizadas, rótulos e sobreposições de geometria.

Para renderizar os pinos personalizados, rótulos e sobreposições de geometria, você pode usar o aplicativo Postman. Você pode usar Azure mapas [APIs de serviços de dados](https://docs.microsoft.com/rest/api/maps/data) armazene e processe as sobreposições.


## <a name="prerequisites"></a>Pré-requisitos

### <a name="create-an-azure-maps-account"></a>Criar uma conta dos Mapas do Azure

Para concluir os procedimentos neste artigo, primeiro você precisa [criar uma conta do Azure mapas](how-to-manage-account-keys.md) no tipo de preço S1.

## <a name="render-pushpins-with-labels-and-a-custom-image"></a>Renderizar anotações com rótulos e uma imagem personalizada

> [!Note]
> O procedimento nesta seção requer uma conta do Azure mapas na camada de preços S0 ou S1.

Os mapas do Azure conta oferece suporte de camada S0 apenas uma única instância de `pins` parâmetro. Ele permite renderizar até cinco pinos, especificados na solicitação de URL, com uma imagem personalizada.

Para renderizar os pinos com rótulos e uma imagem personalizada, conclua estas etapas:

1. Crie uma coleção para armazenar as solicitações. No aplicativo Postman, selecione **New**. No **criar novo** janela, selecione **coleção**. Nome da coleção e selecione o **criar** botão. 

2. Para criar a solicitação, selecione **New** novamente. No **criar novo** janela, selecione **solicitar**. Insira um **nome da solicitação** para os pinos, selecione a coleção que você criou na etapa anterior como o local no qual salvar a solicitação e, em seguida, selecione **salvar**.
    
    ![Criar uma solicitação no Postman](./media/tutorial-geofence/postman-new.png)

3. Selecione o método HTTP GET na guia criador e insira a URL a seguir para criar uma solicitação GET.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttp%3A%2F%2Fazuremapscodesamples.azurewebsites.net%2FCommon%2Fimages%2Fpushpins%2Fylw-pushpin.png
    ```
    Aqui está a imagem resultante:

    ![Um pino personalizado com um rótulo](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Obter dados do armazenamento de dados do Azure Mapas

> [!Note]
> O procedimento nesta seção requer uma conta de mapas do Azure no tipo de preço S1.

Você também pode obter as informações de local do caminho e o pin, usando o [API de carregar dados](https://docs.microsoft.com/rest/api/maps/data/uploadpreview). Siga as etapas abaixo para carregar o caminho e os dados dos marcadores.

1. No aplicativo do Postman, abra uma nova guia na coleção que você criou na seção anterior. Selecione o método HTTP POST na guia criador e insira a URL a seguir para fazer uma solicitação POST:

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. Sobre o **Params** , insira os seguintes pares chave/valor, que são usados para a URL da solicitação POST. Substitua o `subscription-key` valor com sua chave de assinatura de mapas do Azure.
    
    ![Parâmetros de chave/valor no Postman](./media/how-to-render-custom-data/postman-key-vals.png)

3. Sobre o **corpo** , selecione o formato de entrada bruto e escolha o JSON como formato de entrada na lista suspensa. Fornece este JSON como dados a serem carregados:
    
    ```JSON
    {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -73.98235,
                  40.76799
                ],
                [
                  -73.95785,
                  40.80044
                ],
                [
                  -73.94928,
                  40.7968
                ],
                [
                  -73.97317,
                  40.76437
                ],
                [
                  -73.98235,
                  40.76799
                ]
              ]
            ]
          }
        },
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "LineString",
            "coordinates": [
              [
                -73.97624731063843,
                40.76560773817073
              ],
              [
                -73.97914409637451,
                40.766826609362575
              ],
              [
                -73.98513078689575,
                40.7585866048861
              ]
            ]
          }
        }
      ]
    }
    ```

4. Selecione **enviar** e examine o cabeçalho de resposta. O cabeçalho de local contém o URI usado para acessar ou baixar os dados para uso futuro. Também contém um `udId` exclusivo para os dados carregados.  

   ```HTTP
   https://atlas.microsoft.com/mapData/{udId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

5. Use o `udId` valor recebida da API de carregar dados para renderizar os recursos no mapa. Para fazer isso, abra uma nova guia na coleção que você criou na seção anterior. Selecione o método HTTP GET na guia criador e inserir esta URL para fazer uma solicitação GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

6. Aqui está a imagem resultante:

    ![Obter dados do armazenamento de dados do Azure Mapas](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-a-polygon-with-color-and-opacity"></a>Renderizar um polígono com cor e opacidade

> [!Note]
> O procedimento nesta seção requer uma conta de mapas do Azure no tipo de preço S1.


É possível modificar a aparência de um polígono, usando modificadores de estilo com o [parâmetro de caminho](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).

1. No aplicativo do Postman, abra uma nova guia na coleção que você criou anteriormente. Selecione o método HTTP GET na guia criador e insira a URL a seguir para configurar uma solicitação GET para renderizar um polígono com cor e opacidade:
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription--key}
    ```

Aqui está a imagem resultante:

![Renderizar um polígono opaco](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>Renderizar um círculo e anotações com rótulos personalizados

> [!Note]
> O procedimento nesta seção requer uma conta de mapas do Azure no tipo de preço S1.


Você pode fazer anotações e seus rótulos maiores ou menores usando o `sc` modificador de estilo de escala. Esse modificador assume um valor maior que zero. Um valor de 1 é a escala padrão. Valores maiores que 1 tornarão os marcadores maiores e valores menores que 1 os tornarão menores. Para obter mais informações sobre os modificadores de estilo, consulte [parâmetros de caminho do serviço de imagem estática](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).


Siga estas etapas para renderizar um círculo e anotações com rótulos personalizados:

1. No aplicativo do Postman, abra uma nova guia na coleção que você criou anteriormente. Selecione o método HTTP GET na guia criador e inserir esta URL para fazer uma solicitação GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

Aqui está a imagem resultante:

![Renderizar um círculo com anotações personalizados](./media/how-to-render-custom-data/circle-custom-pins.png)

## <a name="next-steps"></a>Próximas etapas


* Explore a documentação [Obter API de Imagem de Mapa do Azure Mapas](https://docs.microsoft.com/rest/api/maps/render/getmapimage).
* Para saber mais sobre o serviço de dados de mapas do Azure, consulte a [documentação de serviço](https://docs.microsoft.com/rest/api/maps/data).

