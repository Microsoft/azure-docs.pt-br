---
title: 'Tutorial: Usar o Criador do Microsoft Azure Mapas (versão prévia) para criar mapas do interior'
description: Tutorial sobre como usar o Criador do Microsoft Azure Mapas (versão prévia) para criar mapas do interior
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 9ac53dab29feddd36a95b8b2b041caaf5c3598d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101720130"
---
# <a name="tutorial-use-creator-preview-to-create-indoor-maps"></a>Tutorial: Usar o Criador (versão prévia) para criar mapas do interior

> [!IMPORTANT]
> Os serviços do Criador do Azure Mapas estão em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este tutorial mostra como criar mapas internos. Neste tutorial, você aprenderá como usar a API para:

> [!div class="checklist"]
> * Carregar seu pacote do Drawing de mapa interno
> * Converter seu pacote do Drawing em dados do mapa
> * Criar um conjunto de dados de seus dados de mapa
> * Criar um conjunto de peças dos dados em seu conjunto de dados
> * Consultar a API do WFS (Web Feature Service) do Azure Mapas para saber mais sobre seus recursos de mapa
> * Criar um conjunto de estados do recurso usando seus recursos de mapa e os dados em seu conjunto de dados
> * Atualizar seu conjunto de estados do recurso

## <a name="prerequisites"></a>Pré-requisitos

Para criar mapas internos:

1. [Faça uma conta do Azure Mapas](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Obtenha uma chave de assinatura primária](quick-demo-map-app.md#get-the-primary-key-for-your-account), também conhecida como a chave primária ou a chave de assinatura.
3. [Criar um recurso de Criador (versão prévia)](how-to-manage-creator.md)
4. Baixe o [pacote de exemplo do Drawing](https://github.com/Azure-Samples/am-creator-indoor-data-examples/blob/master/Sample%20-%20Contoso%20Drawing%20Package.zip).

Este tutorial usa o aplicativo [Postman](https://www.postman.com/), mas você pode escolher um ambiente de desenvolvimento de API diferente.

>[!IMPORTANT]
> As URLs da API neste documento talvez tenham que ser ajustadas de acordo com a localização do recurso do Criador. Para obter mais detalhes, confira o [Acesso aos Serviços do Criador](how-to-manage-creator.md#access-to-creator-services).

## <a name="upload-a-drawing-package"></a>Carregar um pacote do Drawing

Use a [API de upload de dados](/rest/api/maps/data/uploadpreview) para carregar o pacote do Drawing nos recursos do Azure Mapas.

A API de upload de dados é uma transação de execução prolongada que implementa o padrão definido aqui. Quando a operação for concluída, usaremos o `udid` para acessar o pacote carregado para convertê-lo. Siga as etapas abaixo para obter `udid`.

1. Abra o aplicativo Postman. Próximo à parte superior do aplicativo Postman, selecione **Novo**. Na janela **Criar**, selecione **Coleção**.  Nomeie a coleção e selecione o botão **Criar**.

2. Para criar a solicitação, selecione **Novo** outra vez. Na janela **Criar**, selecione **Solicitação**. Insira um **Nome de solicitação** para a solicitação. Selecione a coleção que você criou na etapa anterior e clique em **Salvar**.

3. Selecione o método HTTP **POST** na guia do construtor e insira a URL a seguir para carregar o pacote do Drawing no serviço do Azure Mapas. Para essa solicitação e outras solicitações mencionadas neste artigo, substitua `{Azure-Maps-Primary-Subscription-key}` pela sua chave de assinatura primária.

    ```http
    https://atlas.microsoft.com/mapData/upload?api-version=1.0&dataFormat=zip&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

4. Na guia **Cabeçalhos**, especifique um valor para a chave `Content-Type`. O pacote do Drawing é uma pasta compactada, portanto, use o valor `application/octet-stream`. Na guia **Corpo**, selecione **binário**. Clique em **Selecionar Arquivo** e escolha um pacote do Drawing.

     ![data-management](./media/tutorial-creator-indoor-maps/enter-content-type-dialog.png)

5. Clique no botão azul **Enviar** e aguarde a solicitação ser processada. Depois que a solicitação for concluída, vá para a guia **Cabeçalhos** da resposta. Copie o valor da chave **Local**, que é `status URL`.

6. Para verificar o status da chamada à API, crie uma solicitação HTTP **GET** em `status URL`. Você precisará acrescentar sua chave de assinatura primária à URL para autenticação. A solicitação **GET** deve ser semelhante à seguinte URL:

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

7. Quando a solicitação HTTP **GET** for concluída com êxito, ela retornará um `resourceLocation`. O `resourceLocation` contém o `udid` exclusivo para o conteúdo carregado. Opcionalmente, você poderá usar a URL do `resourceLocation` para recuperar metadados desse recurso na próxima etapa.

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

9. Quando o a solicitação HTTP **GET** for concluída com êxito, o corpo da resposta conterá o `udid` especificado no `resourceLocation` da etapa 7, a localização para acessar/baixar o conteúdo no futuro e outros metadados sobre o conteúdo, como data de criação/atualização, tamanho e assim por diante. Um exemplo da resposta geral é:

    ```json
    {
        "udid": "{udid}",
        "location": "https://atlas.microsoft.com/mapData/{udid}?api-version=1.0",
        "created": "2020-02-03T02:32:25.0509366+00:00",
        "updated": "2020-02-11T06:12:13.0309351+00:00",
        "sizeInBytes": 766,
        "uploadStatus": "Completed"
    }
    ```

## <a name="convert-a-drawing-package"></a>Converter um pacote do Drawing

 Agora que o pacote do Drawing foi carregado, usaremos `udid` para o pacote carregado converter o pacote em dados do mapa. A API de conversão usa uma transação de execução prolongada que implementa o padrão definido [aqui](creator-long-running-operation.md). Quando a operação for concluída, usaremos o `conversionId` para acessar os dados convertidos. Siga as etapas abaixo para obter `conversionId`.

1. Selecione **Novo**. Na janela **Criar**, selecione **Solicitação**. Insira o **Nome da solicitação** e selecione uma coleção. Clique em **Save** (Salvar).

2. Selecione o método HTTP **POST** na guia do construtor e insira a URL a seguir para converter o pacote do Drawing carregado nos dados do mapa. Use o `udid` para o pacote carregado.

    ```http
    https://atlas.microsoft.com/conversion/convert?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&udid={udid}&inputType=DWG
    ```

    >[!IMPORTANT]
    > As URLs da API neste documento talvez tenham que ser ajustadas de acordo com a localização do recurso do Criador. Para obter mais detalhes, confira [Acesso aos serviços do Criador (versão prévia)](how-to-manage-creator.md#access-to-creator-services).
    > Se receber um erro com o código `"RequiresCreatorResource"`, certifique-se de ter [provisionado um recurso do Criador do Azure Mapas](how-to-manage-creator.md) em sua conta do Azure Mapas.

3. Clique no botão **Enviar** e aguarde a solicitação ser processada. Depois que a solicitação for concluída, vá para a guia **Cabeçalhos** da resposta e procure pela chave **Local**. Copie o valor da chave **Local**, que é `status URL`, para a solicitação de conversão. Você usará isso na próxima etapa.

    :::image type="content" source="./media/tutorial-creator-indoor-maps/copy-location-uri-dialog.png" border="true" alt-text="Copie o valor da chave local":::

4. Inicie um novo método HTTP **GET** na guia do construtor. Acrescente sua chave de assinatura primária do Azure Mapas ao `status URL`. Faça uma solicitação **GET** no `status URL` que você copiou na etapa 3. O `status URL` se parece com a seguinte URL:

    ```http
    https://atlas.microsoft.com/conversion/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

    Se o processo de conversão ainda não tiver sido concluído, você poderá ver algo semelhante à seguinte resposta JSON:

    ```json
    {
        "operationId": "<operationId>",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Running"
    }
    ```

5. Depois que a solicitação for concluída com êxito, você verá uma mensagem de status de êxito no corpo da resposta.  Copie `conversionId` da URL `resourceLocation` para o pacote convertido. O `conversionId` é usado por outra API para acessar os dados de mapa convertidos.

    ```json
   {
        "operationId": "<operationId>",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
        "properties": {}
    }
    ```

>[!NOTE]
>O aplicativo Postman não dá suporte nativo a solicitações HTTP de execução prolongada. Como resultado, você poderá notar um longo atraso ao fazer uma solicitação **GET** na URL de status.  Aguarde cerca de trinta segundos e tente clicar no botão **Enviar** novamente até que a resposta mostre êxito ou falha.

O pacote de exemplo do Drawing deve ser convertido sem erros ou avisos. No entanto, se você receber erros ou avisos de seu próprio pacote do Drawing, a resposta JSON fornecerá um link para o [visualizador de erros do Drawing](drawing-error-visualizer.md). O visualizador de erros do Drawing permite inspecionar os detalhes de erros e avisos. Para receber recomendações sobre como resolver erros e avisos de conversão, consulte [Erros e avisos de conversão do Drawing](drawing-conversion-error-codes.md).

```json
{
    "operationId": "<operationId>",
    "created": "2020-04-22T19:39:54.9518496+00:00",
    "status": "Failed",
    "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
    "properties": {
        "diagnosticPackageLocation": "https://atlas.microsoft.com/mapData/ce61c3c1-faa8-75b7-349f-d863f6523748?api-version=1.0"
    }
}
```

## <a name="create-a-dataset"></a>Criar um conjunto de dados

O conjunto de dados é uma coleção de recursos de mapa, como edifícios, níveis e salas. Para criar um conjunto de dados, use a [API de criação de conjunto de dados](/rest/api/maps/dataset/createpreview). A API de criação de conjunto de dados pega `conversionId` para o pacote do Drawing convertido e retorna um `datasetId` do conjunto de dados criado. As etapas abaixo mostram como criar um conjunto de dados.

1. No aplicativo Postman, selecione **Novo**. Na janela **Criar**, selecione **Solicitação**. Insira o **Nome da solicitação** e selecione uma coleção. Clique em **Salvar**

2. Faça uma solicitação **POST** à [API de criação de conjunto de dados](/rest/api/maps/dataset/createpreview) para criar um conjunto de dados. Antes de enviar a solicitação, acrescente sua chave de assinatura e o `conversionId` com o `conversionId` obtido durante o processo de conversão na etapa 5.  A solicitação deve ser semelhante à seguinte URL:

    ```http
    https://atlas.microsoft.com/dataset/create?api-version=1.0&conversionID={conversionId}&type=facility&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Obtenha o `statusURL` na chave **Local** dos **Cabeçalhos** de resposta.

4. Faça uma solicitação **GET** no `statusURL` para obter o `datasetId`. Acrescente sua chave de assinatura primária do Azure Mapas para autenticação. A solicitação deve ser semelhante à seguinte URL:

    ```http
    https://atlas.microsoft.com/dataset/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

5. Quando a solicitação HTTP **GET** for concluída com êxito, o cabeçalho de resposta conterá o `datasetId` para o conjunto de dados criado. Copie `datasetId`. Você precisará usar o `datasetId` para criar um conjunto de peças.

    ```json
    {
        "operationId": "<operationId>",
        "created": "2020-04-22T19:52:38.9352189+00:00",
        "status": "Succeeded",
        "resourceLocation": "https://azure.microsoft.com/dataset/{datasetiId}?api-version=1.0"
     }
    ```

## <a name="create-a-tileset"></a>Criar um conjunto de peças

Um conjunto de peças é um conjunto de peças de vetor que são renderizadas no mapa. Os conjuntos de peças são criados usando conjuntos de dados existentes. No entanto, um conjunto de peças é independente do conjunto de dados do qual foi originado. Se o conjunto de dados for excluído, o conjunto de peças continuará a existir. Para criar um conjunto de peças, siga as etapas abaixo:

1. No aplicativo Postman, selecione **Novo**. Na janela **Criar**, selecione **Solicitação**. Insira o **Nome da solicitação** e selecione uma coleção. Clique em **Salvar**

2. Faça uma solicitação **POST** na guia do construtor. A URL de solicitação deve ser semelhante à seguinte URL:

    ```http
    https://atlas.microsoft.com/tileset/create/vector?api-version=1.0&datasetID={datasetId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Faça uma solicitação **GET** no `statusURL` para o conjunto de peças. Acrescente sua chave de assinatura primária do Azure Mapas para autenticação. A solicitação deve ser semelhante à seguinte URL:

   ```http
    https://atlas.microsoft.com/tileset/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

4. Quando a solicitação HTTP **GET** for concluída com êxito, o cabeçalho de resposta conterá o `tilesetId` para o conjunto de peças criado. Copie `tilesetId`.

    ```json
    {
        "operationId": "<operationId>",
        "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/tileset/{tilesetId}?api-version=1.0"
    }
    ```

## <a name="query-datasets-with-wfs-api"></a>Consultar conjuntos de dados com a API do WFS

 Os conjuntos de dados podem ser consultados usando a [API do WFS](/rest/api/maps/wfs). Com a API do WFS, você pode consultar coleções de recursos, uma coleção específica ou um recurso específico com um recurso **ID**. O recurso **ID** identifica exclusivamente o recurso no conjunto de dados. Ele é usado, por exemplo, para identificar qual estado do recurso deve ser atualizado em um determinado conjunto de dados.

1. No aplicativo Postman, selecione **Novo**. Na janela **Criar**, selecione **Solicitação**. Insira o **Nome da solicitação** e selecione uma coleção. Clique em **Salvar**

2. Faça uma solicitação **GET** para exibir uma lista das coleções no conjunto de dados. Substitua `<dataset-id>` por seu `datasetId`. Use sua chave primária do Azure Mapas em vez do espaço reservado. A solicitação deve ser semelhante à seguinte URL:

    ```http
    https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

3. O corpo da resposta será entregue no formato GeoJSON e conterá todas as coleções no conjunto de dados. Para simplificar, o exemplo aqui mostra apenas a coleção `unit`. Para ver um exemplo que contém todas as coleções, consulte [WFS: API de descrição de coleções](/rest/api/maps/wfs/collectiondescriptionpreview). Para saber mais sobre coleções, você pode clicar em uma das URLs dentro do elemento `link`.

    ```json
    {
    "collections": [
        {
            "name": "unit",
            "description": "A physical and non-overlapping area which might be occupied and traversed by a navigating agent. Can be a hallway, a room, a courtyard, etc. It is surrounded by physical obstruction (wall), unless the is_open_area attribute is equal to true, and one must add openings where the obstruction shouldn't be there. If is_open_area attribute is equal to true, all the sides are assumed open to the surroundings and walls are to be added where needed. Walls for open areas are represented as a line_element or area_element with is_obstruction equal to true.",
            "links": [
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/definition?api-version=1.0",
                    "rel": "describedBy",
                    "title": "Metadata catalogue for unit"
                },
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?api-version=1.0",
                    "rel": "data",
                    "title": "unit"
                }
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit?api-version=1.0",
                    "rel": "self",
                    "title": "Metadata catalogue for unit"
                }
            ]
        },
    ```

4. Faça uma solicitação **GET** para as coleções de recursos do `unit`.  Substitua `{datasetId}` por seu `datasetId`. Use sua chave primária do Azure Mapas em vez do espaço reservado. O corpo da resposta conterá todos os recursos da coleção `unit`. A solicitação deve ser semelhante à seguinte URL:

    ```http
    https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

5. Copie o recurso `id` para um recurso de unidade que tenha propriedades de estilo que possam ser modificadas dinamicamente.  Como o status de ocupação da unidade e a temperatura podem ser atualizados dinamicamente, usaremos esse recurso `id` na próxima seção. No exemplo a seguir, o recurso `id` é "UNIT26". Vamos nos referir às propriedades de estilo desse recurso como estados e usaremos o recurso para fazer um conjunto de estados.

     ```json
    {
        "type": "FeatureCollection",
        "features": [
            {
                "type": "Feature",
                "geometry": {
                    "type": "Polygon",
                    "coordinates": ["..."]
                },
                "properties": {
                    "original_id": "b7410920-8cb0-490b-ab23-b489fd35aed0",
                    "category_id": "CTG8",
                    "is_open_area": true,
                    "navigable_by": [
                        "pedestrian"
                    ],
                    "route_through_behavior": "allowed",
                    "level_id": "LVL14",
                    "occupants": [],
                    "address_id": "DIR1",
                    "name": "157"
                },
                "id": "UNIT26",
                "featureType": ""
            }, {"..."}
        ]
    }
    ```

## <a name="create-a-feature-stateset"></a>Criar um conjunto de estados do recurso

1. No aplicativo Postman, selecione **Novo**. Na janela **Criar**, selecione **Solicitação**. Insira o **Nome da solicitação** e selecione uma coleção. Clique em **Salvar**

2. Faça uma solicitação **POST** para a [API de criação de conjunto de estados](/rest/api/maps/featurestate/createstatesetpreview). Use o `datasetId` do conjunto de dados que contém o estado que você deseja modificar. A solicitação deve ser semelhante à seguinte URL:

    ```http
    https://atlas.microsoft.com/featureState/stateset?api-version=1.0&datasetId={datasetId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Nos **Cabeçalhos** da solicitação **POST**, defina `Content-Type` como `application/json`. No **Corpo**, forneça os estilos JSON brutos abaixo para refletir as alterações feitas nos *estados* `occupied` e `temperature`. Ao terminar, clique em **Enviar**.

    ```json
    {
       "styles":[
          {
             "keyname":"occupied",
             "type":"boolean",
             "rules":[
                {
                   "true":"#FF0000",
                   "false":"#00FF00"
                }
             ]
          },
          {
             "keyname":"temperature",
             "type":"number",
             "rules":[
                {
                   "range":{
                      "exclusiveMaximum":66
                   },
                   "color":"#00204e"
                },
                {
                   "range":{
                      "minimum":66,
                      "exclusiveMaximum":70
                   },
                   "color":"#0278da"
                },
                {
                   "range":{
                      "minimum":70,
                      "exclusiveMaximum":74
                   },
                   "color":"#187d1d"
                },
                {
                   "range":{
                      "minimum":74,
                      "exclusiveMaximum":78
                   },
                   "color":"#fef200"
                },
                {
                   "range":{
                      "minimum":78,
                      "exclusiveMaximum":82
                   },
                   "color":"#fe8c01"
                },
                {
                   "range":{
                      "minimum":82
                   },
                   "color":"#e71123"
                }
             ]
          }
       ]
    }
    ```

4. Copie `statesetId` do corpo da resposta.

5. Crie uma solicitação **POST** para atualizar o estado: Passe o statesetId e o recurso `ID` com sua chave de assinatura do Azure Mapas. A solicitação deve ser semelhante à seguinte URL:

    ```http
    https://atlas.microsoft.com/featureState/state?api-version=1.0&statesetID={statesetId}&featureID={featureId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. Nos **Cabeçalhos** da solicitação **POST**, defina `Content-Type` como `application/json`. No **CORPO** da solicitação **POST**, copie e cole o JSON no exemplo abaixo.

    ```json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": true,
                "eventTimestamp": "2019-11-14T17:10:20"
            }
        ]
    }
    ```

    >[!NOTE]
    > A atualização só será salva se o carimbo de data/hora postado for posterior ao carimbo de data/hora da solicitação anterior. Podemos passar qualquer keyname que configuramos anteriormente durante a criação.

7. Após uma atualização bem-sucedida, você receberá um código de status HTTP `200 OK`. Se você tiver o [estilo dinâmico implementado](indoor-map-dynamic-styling.md) para um mapa interno, a atualização será exibida no mapa renderizado no carimbo de data/hora especificado.

A [API de obtenção de estados do recurso](/rest/api/maps/featurestate/getstatespreview) permite que você recupere o estado de um recurso usando seu recurso `ID`. Você também pode excluir o conjunto de estados e seus recursos usando a [API de exclusão de estado do recurso](/rest/api/maps/featurestate/deletestatesetpreview).

Para saber mais sobre os diferentes serviços do Criador do Azure Mapas (versão prévia) discutidos neste artigo, confira [Criador de Mapas do Interior](creator-indoor-maps.md).

## <a name="clean-up-resources"></a>Limpar os recursos

Não há recursos que exijam limpeza.

## <a name="next-steps"></a>Próximas etapas

Para saber como usar o módulo do Indoor Maps, confira

> [!div class="nextstepaction"]
> [Usar o módulo do Azure Mapas Interno](how-to-use-indoor-module.md)