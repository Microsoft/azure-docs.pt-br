---
title: Pesquisar um local usando os serviços de pesquisa do Azure Maps
description: Saiba mais sobre o serviço de pesquisa do Azure Maps. Veja como usar esse conjunto de APIs para geocodificação, geocodificação geográfica, pesquisas difusas e pesquisas de várias ruas invertidas.
author: anastasia-ms
ms.author: v-stharr
ms.date: 01/19/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: dddf56edf2037d87a28589a59834db32f8d04a4c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98598359"
---
# <a name="search-for-a-location-using-azure-maps-search-services"></a>Pesquisar um local usando os serviços de pesquisa do Azure Maps

O [Azure Maps serviço de pesquisa](/rest/api/maps/search) é um conjunto de APIs RESTful projetado para ajudar os desenvolvedores a pesquisar endereços, locais e listagens de negócios por nome, categoria e outras informações geográficas. Além de oferecer suporte à geocodificação tradicional, os serviços também podem reverter endereços geocódigo e entre ruas com base em latitudes e longitudes. Os valores de latitude e longitude retornados pela pesquisa podem ser usados como parâmetros em outros serviços do Azure Maps, como serviços de [rota](/rest/api/maps/route) e [clima](/rest/api/maps/weather) .


Neste artigo, você aprenderá a:

* Solicitar coordenadas de latitude e longitude para um endereço (local de endereço geocódigo) usando a [API de endereço de pesquisa]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddress).
* Procure um endereço ou ponto de interesse (POI) usando a [API de pesquisa difusa](/rest/api/maps/search/getsearchfuzzy).
* Faça uma [pesquisa de endereço reverso](/rest/api/maps/search/getsearchaddressreverse) para converter o local da coordenada em endereço.
* Traduza o local da coordenada em uma entre as ruas compreensíveis humanas usando a API de localização [cruzada de endereços](/rest/api/maps/search/getsearchaddressreversecrossstreet).  Geralmente, isso é necessário no rastreamento de aplicativos que recebem um feed GPS de um dispositivo ou ativo, e você deseja saber onde a coordenada está localizada.

## <a name="prerequisites"></a>Pré-requisitos

1. [Fazer uma conta do Azure Mapas](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Obtenha uma chave de assinatura primária](quick-demo-map-app.md#get-the-primary-key-for-your-account), também conhecida como a chave primária ou a chave de assinatura.

Este tutorial usa o aplicativo [Postman](https://www.postman.com/), mas você pode escolher um ambiente de desenvolvimento de API diferente.

## <a name="request-latitude-and-longitude-for-an-address-geocoding"></a>Solicitar latitude e longitude para um endereço (geocodificação)

Neste exemplo, usaremos a [API obter endereço de pesquisa](/rest/api/maps/search/getsearchaddress) do Azure Maps para converter um endereço em coordenadas de latitude e longitude. Esse processo também é chamado de *geocodificação*. Além de retornar as coordenadas, a resposta também retornará Propriedades de endereço detalhadas, como rua, código postal, município e informações de país/região.

>[!TIP]
>Se você tiver um conjunto de endereços para geocódigo, poderá usar a [API do lote de endereço de pesquisa de postagem](/rest/api/maps/search/postsearchaddressbatch) para enviar um lote de consultas em uma única chamada à API.

1. Abra o aplicativo Postman. Próximo à parte superior do aplicativo Postman, selecione **Novo**. Na janela **Criar**, selecione **Coleção**.  Nomeie a coleção e selecione o botão **Criar**. Você usará essa coleção para o restante dos exemplos neste documento.

2. Para criar a solicitação, selecione **Novo** outra vez. Na janela **Criar**, selecione **Solicitação**. Insira um **Nome de solicitação** para a solicitação. Selecione a coleção que você criou na etapa anterior e clique em **Salvar**.

3. Selecione o método http **Get** na guia Construtor e insira a URL a seguir. Nesta solicitação, Estamos pesquisando um endereço específico: `400 Braod St, Seattle, WA 98109` . Para essa solicitação e outras solicitações mencionadas neste artigo, substitua `{Azure-Maps-Primary-Subscription-key}` pela sua chave de assinatura primária.

    ```http
    https://atlas.microsoft.com/search/address/json?&subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&language=en-US&query=400 Broad St, Seattle, WA 98109
    ```

4. Clique no botão azul **Send** . O corpo da resposta conterá dados para um único local.

5. Agora, Pesquisaremos um endereço com mais de um possível local. Na seção **params** , altere a `query` chave para `400 Broad, Seattle` . Clique no botão azul **Send** .

    :::image type="content" source="./media/how-to-search-for-address/search-address.png" alt-text="Pesquisar endereço":::

6. Em seguida, tente definir a `query` chave como `400 Broa` .

7. Clique no botão **Enviar**. Agora você pode ver que a resposta inclui respostas de vários países. Para resultados geopolares para a área relevante para seus usuários, sempre adicione o máximo possível de detalhes de local à solicitação.

## <a name="using-fuzzy-search-api"></a>Usando a API de pesquisa difusa

A API de [pesquisa difusa](/rest/api/maps/search/getsearchfuzzy) do Azure Maps dá suporte a pesquisas de linha única e de forma livre padrão. Recomendamos que você use a API difusa da pesquisa do Azure Maps quando não souber o tipo de entrada do usuário para uma solicitação de pesquisa.  A entrada da consulta pode ser um endereço completo ou parcial. Ele também pode ser um token de POI (ponto de interesse), como um nome de POI, POI categoria ou nome de marca. Além disso, para melhorar a relevância dos resultados da pesquisa, os resultados da consulta podem ser restritos por um local de coordenadas e RADIUS, ou definindo uma caixa delimitadora.

>[!TIP]
>A maioria das consultas de pesquisa padrão é maxFuzzyLevel = 1 para obter o desempenho e reduzir resultados incomuns. Você pode ajustar os níveis de fuzzing usando `maxFuzzyLevel` os `minFuzzyLevel` parâmetros ou. Para obter mais informações sobre `maxFuzzyLevel` o e uma lista completa de todos os parâmetros opcionais, consulte [parâmetros de URI de pesquisa difusa](/rest/api/maps/search/getsearchfuzzy#uri-parameters)

### <a name="search-for-an-address-using-fuzzy-search"></a>Procurar um endereço usando a Pesquisa Difusa

Neste exemplo, usaremos a pesquisa difusa para pesquisar o mundo inteiro `pizza` .  Em seguida, mostraremos como pesquisar o escopo de um país específico. Por fim, mostraremos como usar um local de coordenadas e o raio para definir o escopo de uma pesquisa em uma área específica e limitar o número de resultados retornados.

>[!IMPORTANT]
>Para resultados geopolares para a área relevante para seus usuários, sempre adicione o máximo possível de detalhes de local. Para saber mais, veja [práticas recomendadas para pesquisa](how-to-use-best-practices-for-search.md#geobiased-search-results).

1. Abra o aplicativo do postmaster, clique em **novo** e selecione **solicitação**. Insira um **Nome de solicitação** para a solicitação. Selecione a coleção que você criou na seção anterior ou criou uma nova e, em seguida, selecione **salvar**.

2. Selecione o método http **Get** na guia Construtor e insira a URL a seguir. Para essa solicitação e outras solicitações mencionadas neste artigo, substitua `{Azure-Maps-Primary-Subscription-key}` pela sua chave de assinatura primária.

    ```http
   https://atlas.microsoft.com/search/fuzzy/json?&api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=pizza
    ```

    >[!NOTE]
    >O atributo _json_ no caminho da URL determina o formato da resposta. Este artigo usa o JSON para facilitar o uso e a legibilidade. Para localizar outros formatos de resposta com suporte, consulte a `format` definição de parâmetro na [documentação de referência do parâmetro Uri](/rest/api/maps/search/getsearchfuzzy#uri-parameters).

3. Clique em **Enviar** e analise o corpo da resposta.

    A cadeia de caracteres de consulta ambígua para "pizza" retornou 10 [resultados de POI (ponto de interesse](/rest/api/maps/search/getsearchpoi#searchpoiresponse) ) nas categorias "pizza" e "restaurante". Cada resultado inclui detalhes como endereço, valores de latitude e longitude, porta de exibição e pontos de entrada para o local. Os resultados agora são variados para essa consulta e não estão vinculados a nenhum local de referência.
  
    Na próxima etapa, usaremos o `countrySet` parâmetro para especificar apenas os países/regiões para os quais seu aplicativo precisa de cobertura. Para obter uma lista completa de países/regiões com suporte, consulte [cobertura de pesquisa](./geocoding-coverage.md).

4. O comportamento padrão é Pesquisar o mundo inteiro, potencialmente retornando resultados desnecessários. Em seguida, Pesquisaremos apenas a Estados Unidos de pizza. Adicione a `countrySet` chave à seção **params** e defina seu valor como `US` . Definir a `countrySet` chave como `US` vinculará os resultados ao Estados Unidos.

    :::image type="content" source="./media/how-to-search-for-address/search-fuzzy-country.png" alt-text="Pesquisar pizza na Estados Unidos":::

    Os resultados agora são limitados pelo código do país e a consulta retorna as pizzarias no Brasil.

5. Para obter uma pesquisa ainda mais direcionada, você pode pesquisar o escopo de um lat./Lon. par de coordenadas. Neste exemplo, usaremos o lat./Lon. da agulha do espaço de Seattle. Como queremos apenas retornar resultados dentro de um raio de 400-metros, adicionaremos o `radius` parâmetro. Além disso, adicionaremos o `limit` parâmetro para limitar os resultados aos cinco lugares de pizza mais próximos.

    Na seção **params** , adicione os seguintes pares de chave/valor:

     | Chave | Valor |
    |-----|------------|
    | lat | 47.620525 |
    | lon | -122.349274 |
    | raio | 400 |
    | limite | 5|

6. Clique em **Enviar**. A resposta inclui resultados para o restaurantes de pizza próximo à agulha de espaço de Seattle.

## <a name="search-for-a-street-address-using-reverse-address-search"></a>Procurar um endereço usando a Pesquisa Invertida de Endereço

A API de [obtenção de endereços de pesquisa inversa]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) do Azure Maps converte coordenadas em endereços de rua legíveis. Essa API é geralmente usada para aplicativos que consomem feeds GPS e desejam descobrir endereços em pontos de coordenadas específicos.

>[!IMPORTANT]
>Para resultados geopolares para a área relevante para seus usuários, sempre adicione o máximo possível de detalhes de local. Para saber mais, veja [práticas recomendadas para pesquisa](how-to-use-best-practices-for-search.md#geobiased-search-results).

>[!TIP]
>Se você tiver um conjunto de locais de coordenadas para reverter o código, você poderá usar a [API de postagem inversa do endereço de pesquisa](/rest/api/maps/search/postsearchaddressreversebatch) para enviar um lote de consultas em uma única chamada à API.

Neste exemplo, vamos fazer pesquisas inversas usando alguns dos parâmetros opcionais disponíveis. Para obter a lista completa de parâmetros opcionais, consulte [parâmetros de pesquisa inversa](/rest/api/maps/search/getsearchaddressreverse#uri-parameters).

1. No aplicativo de postmaster, clique em **novo** e selecione **solicitação**. Insira um **Nome de solicitação** para a solicitação. Selecione a coleção que você criou na primeira seção ou criou uma nova e, em seguida, selecione **salvar**.

2. Selecione o método http **Get** na guia Construtor e insira a URL a seguir. Para essa solicitação e outras solicitações mencionadas neste artigo, substitua `{Azure-Maps-Primary-Subscription-key}` pela sua chave de assinatura primária. A solicitação deve ser semelhante à seguinte URL:

    ```http
    https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=47.591180,-122.332700&number=1
    ```

3. Clique em **Enviar** e examine o corpo da resposta. Você deverá ver um resultado da consulta. A resposta inclui informações importantes sobre o endereço do Safeco Field.
  
4. Agora, adicionaremos os seguintes pares de chave/valor à seção **params** :

    | Chave | Valor | Retornos
    |-----|------------|------|
    | número | 1 |A resposta pode incluir o lado da rua (esquerda/direita) e também uma posição de deslocamento para o número.|
    | returnSpeedLimit | true | Retorna o limite de velocidade no endereço.|
    | returnRoadUse | true | Retorna os tipos de uso de estrada no endereço. Para todos os tipos de uso de estrada possíveis, consulte [tipos de uso de estrada](/rest/api/maps/search/getsearchaddressreverse#uri-parameters).|
    | returnMatchType | true| Retorna o tipo de correspondência. Para todos os valores possíveis, consulte [resultados da pesquisa de endereço reverso](/rest/api/maps/search/getsearchaddressreverse#searchaddressreverseresult)

   :::image type="content" source="./media/how-to-search-for-address/search-reverse.png" alt-text="Pesquisar inversa.":::

5. Clique em **Enviar** e examine o corpo da resposta.

6. Em seguida, adicionaremos a `entityType` chave e definiremos seu valor como `Municipality` . A `entityType` chave substituirá a `returnMatchType` chave na etapa anterior. Também precisaremos remover `returnSpeedLimit` e `returnRoadUse` , como estamos solicitando informações sobre o município.  Para todos os tipos de entidade possíveis, consulte [tipos de entidade](/rest/api/maps/search/getsearchaddressreverse#entitytype).

    :::image type="content" source="./media/how-to-search-for-address/search-reverse-entity-type.png" alt-text="Pesquisar entityType inverso.":::

7. Clique em **Enviar**. Compare os resultados com os resultados retornados na etapa 5.  Como o tipo de entidade solicitado agora é `municipality` , a resposta não inclui informações de endereço de rua. Além disso, o retornado `geometryId` pode ser usado para solicitar o polígono de limite por meio do Azure Maps obter [API de polígono de pesquisa](/rest/api/maps/search/getsearchpolygon).

>[!TIP]
>Para obter mais informações sobre esses parâmetros, bem como para saber mais sobre outros, consulte a [seção parâmetros de pesquisa inversa](/rest/api/maps/search/getsearchaddressreverse#uri-parameters).

## <a name="search-for-cross-street-using-reverse-address-cross-street-search"></a>Pesquisar entre as ruas usando a pesquisa de postagem de endereço reverso

Neste exemplo, Pesquisaremos uma cruz com base nas coordenadas de um endereço.

1. No aplicativo de postmaster, clique em **novo** e selecione **solicitação**. Insira um **Nome de solicitação** para a solicitação. Selecione a coleção que você criou na primeira seção ou criou uma nova e, em seguida, selecione **salvar**.

2. Selecione o método http **Get** na guia Construtor e insira a URL a seguir. Para essa solicitação e outras solicitações mencionadas neste artigo, substitua `{Azure-Maps-Primary-Subscription-key}` pela sua chave de assinatura primária. A solicitação deve ser semelhante à seguinte URL:
  
    ```http
   https://atlas.microsoft.com/search/address/reverse/crossstreet/json?&api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=47.591180,-122.332700
    ```

    :::image type="content" source="./media/how-to-search-for-address/search-address-cross.png" alt-text="Pesquisar entre as ruas.":::
  
3. Clique em **Enviar** e examine o corpo da resposta. Você observará que a resposta contém um `crossStreet` valor de `South Atlantic Street` .

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [API REST do Azure Maps Serviço de Pesquisa](/rest/api/maps/search)

> [!div class="nextstepaction"]
> [Práticas recomendadas do Azure Maps Serviço de Pesquisa](how-to-use-best-practices-for-search.md)