---
title: Criando filtros com a API REST dos serviços de mídia do Azure v3
description: Este tópico descreve como criar filtros para que seu cliente possa usá-los na transmissão de seções específicas de um fluxo. A API REST dos serviços de mídia v3 cria manifestos dinâmicos para atingir esse streaming seletivo.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: c1d7bf933b487c40d571f1912341b5ef771e4e67
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90527319"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Criando filtros com a API REST de Serviços de Mídia do Microsoft Azure

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ao entregar seu conteúdo aos clientes (streaming de eventos ao vivo ou Video por Demanda), seu cliente pode precisar de mais flexibilidade do que o descrito no arquivo de manifesto do ativo padrão. Os Serviços de Mídia do Azure permitem definir filtros de conta e filtros de recursos para o seu conteúdo. 

Para obter uma descrição detalhada desse recurso e dos cenários em que ele é usado, consulte manifestos e [filtros](filters-concept.md) [dinâmicos](filters-dynamic-manifest-overview.md) .

Este tópico mostra como definir um filtro para um ativo vídeo por demanda e usar APIs REST para criar [Filtros de Conta](/rest/api/media/accountfilters) e [Filtros de Ativos](/rest/api/media/assetfilters). 

> [!NOTE]
> Certifique-se de examinar [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Pré-requisitos 

Para concluir as etapas descritas neste tópico, você precisa:

- Analise [Filtros e manifestos dinâmicos](filters-dynamic-manifest-overview.md).
- [Configurar o Postman para chamadas à API REST de Serviços de Mídia do Azure](media-rest-apis-with-postman.md).

    Certifique-se de seguir a última etapa no tópico [Obter token do Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-filter"></a>Definir um filtro  

A seguir, o exemplo **Request body**, que define as condições de seleção de faixas adicionadas ao manifesto. Esse filtro inclui quaisquer faixas de áudio que sejam EC-3 e quaisquer faixas de vídeo com taxa de bits na faixa de 0 a 1000000.

```json
{
    "properties": {
        "tracks": [
          {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Audio",
                        "operation": "Equal"
                    },
                    {
                        "property": "FourCC",
                        "value": "EC-3",
                        "operation": "Equal"
                    }
                ]
            },
            {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Video",
                        "operation": "Equal"
                    },
                    {
                        "property": "Bitrate",
                        "value": "0-1000000",
                        "operation": "Equal"
                    }
                ]
            }
        ]
     }
}
```

## <a name="create-account-filters"></a>Crie filtros de conta

Na coleção do postmaster que você baixou, selecione **filtros de conta** -> **criar ou atualizar um filtro de conta**.

O método de solicitação HTTP **PUT** é semelhante a:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/accountFilters/{filterName}?api-version=2018-07-01
```

Selecione a guia **Body** e cole o código json [definido anteriormente](#define-a-filter).

Selecione **Enviar**. 

O filtro foi criado.

Para mais informações, consulte [Criar ou atualizar](/rest/api/media/accountfilters/createorupdate). Além disso, veja [exemplos de JSON para filtros](/rest/api/media/accountfilters/createorupdate#create-an-account-filter).

## <a name="create-asset-filters"></a>Crie filtros de ativos  

Na coleção do "Media Services V3" do postmaster que você baixou, selecione **ativos** -> **criar ou atualizar o filtro de ativo**.

O método de solicitação HTTP **PUT** é semelhante a:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

Selecione a guia **Body** e cole o código json [definido anteriormente](#define-a-filter).

Selecione **Enviar**. 

O filtro de ativos foi criado.

Para detalhes sobre como criar ou atualizar filtros de recursos, consulte [Criar ou atualizar](/rest/api/media/assetfilters/createorupdate). Além disso, veja [exemplos de JSON para filtros](/rest/api/media/assetfilters/createorupdate#create-an-asset-filter). 

## <a name="associate-filters-with-streaming-locator"></a>Associar filtros ao localizador de streaming

Você pode especificar uma lista de ativos ou filtros de conta, que se aplicariam ao seu localizador de streaming. O [Gerenciador dinâmico (ponto de extremidade de streaming)](dynamic-packaging-overview.md) aplica essa lista de filtros junto com aqueles que seu cliente especifica na URL. Essa combinação gera um [manifesto dinâmico](filters-dynamic-manifest-overview.md), que é baseado em filtros na URL + filtros que você especificar no localizador de streaming. Recomendamos que você use esse recurso se desejar aplicar filtros, mas não quiser expor os nomes de filtro na URL.

Para criar e associar filtros a um localizador de streaming usando REST, use os [localizadores de streaming – criar](/rest/api/media/streaminglocators/create) API e especifique `properties.filters` no [corpo da solicitação](/rest/api/media/streaminglocators/create#request-body).
                                
## <a name="stream-using-filters"></a>Fluxo usando filtros

Depois que você definir filtros, seus clientes poderão usá-los na URL de streaming. Os filtros podem ser aplicados a protocolos de streaming de taxa de bits adaptáveis: Apple HTTP Live Streaming (HLS), MPEG-DASH e Smooth Streaming.

A tabela a seguir mostra alguns exemplos de URLs com filtros:

|Protocolo|Exemplo|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Próximas etapas

[Vídeos do Stream](stream-files-tutorial-with-rest.md) 
