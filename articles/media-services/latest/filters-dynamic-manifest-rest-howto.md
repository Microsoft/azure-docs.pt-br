---
title: Criando Filtros com a API REST dos Serviços de Mídia do Azure | Microsoft Docs
description: Este tópico descreve como criar filtros para que seu cliente possa usá-los na transmissão de seções específicas de um fluxo. Os Serviços de Mídia criam manifestos dinâmicos para atingir esse streaming seletivo.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: 76e6e1595cb8bf49dbbc82c3cae5de80ea718aeb
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786446"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Criando filtros com a API REST de Serviços de Mídia do Microsoft Azure

Ao entregar seu conteúdo aos clientes (streaming de eventos ao vivo ou vídeo por demanda), seu cliente pode precisar de mais flexibilidade do que o descrito no arquivo de manifesto do ativo padrão. Os Serviços de Mídia do Azure permitem definir filtros de conta e filtros de recursos para o seu conteúdo. 

Para obter uma descrição detalhada desse recurso e os cenários em que ele é usado, consulte [manifestos dinâmicos](filters-dynamic-manifest-overview.md) e [filtros](filters-concept.md).

Este tópico mostra como definir um filtro para um ativo vídeo por demanda e usar APIs REST para criar [Filtros de Conta](https://docs.microsoft.com/rest/api/media/accountfilters) e [Filtros de Ativos](https://docs.microsoft.com/rest/api/media/assetfilters). 

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

Na coleção do Postman que você fez o download, selecione **Account Filters**-> **Crie ou atualize um Filtro de Conta**.

O método de solicitação HTTP **PUT** é semelhante a:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/accountFilters/{filterName}?api-version=2018-07-01
```

Selecione a guia **Body** e cole o código json [definido anteriormente](#define-a-filter).

Selecione **Enviar**. 

O filtro foi criado.

Para mais informações, consulte [Criar ou atualizar](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate). Além disso, veja [exemplos de JSON para filtros](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create-an-account-filter).

## <a name="create-asset-filters"></a>Crie filtros de ativos  

Na coleção do Postman dos "Serviços de Mídia v3" que você baixou, selecione **Ativos**->**Criar ou atualizar Filtro de Ativos**.

O método de solicitação HTTP **PUT** é semelhante a:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

Selecione a guia **Body** e cole o código json [definido anteriormente](#define-a-filter).

Selecione **Enviar**. 

O filtro de ativos foi criado.

Para detalhes sobre como criar ou atualizar filtros de recursos, consulte [Criar ou atualizar](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate). Além disso, veja [exemplos de JSON para filtros](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create-an-asset-filter). 

## <a name="associate-filters-with-streaming-locator"></a>Associar filtros de localizador de Streaming

Você pode especificar uma lista de filtros de ativo ou conta, que se aplica a localizador de Streaming. O [Dynamic Packager (ponto de extremidade de Streaming)](dynamic-packaging-overview.md) se aplica a esta lista de filtros junto com aqueles seu cliente especifica a URL. Essa combinação gera uma [manifesto dinâmico](filters-dynamic-manifest-overview.md), que se baseia nos filtros na URL + filtros que você especificar no localizador de Streaming. É recomendável que você use esse recurso se você deseja aplicar filtros, mas não quiser expor os nomes de filtro na URL.

Para criar e associar filtros com um localizador de Streaming usando o REST, use o [localizadores de Streaming - crie](https://docs.microsoft.com/rest/api/media/streaminglocators/create) API e especificar `properties.filters` no [corpo da solicitação](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body).
                                
## <a name="stream-using-filters"></a>Stream usando filtros

Depois que você definir filtros, seus clientes poderão usá-los na URL de streaming. Os filtros podem ser aplicados a protocolos de streaming de taxa de bits adaptável: Apple HTTP Live Streaming (HLS), MPEG-DASH e Smooth Streaming.

A tabela a seguir mostra alguns exemplos de URLs com filtros:

|Protocol|Exemplo|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Próximas etapas

[Vídeos do Stream](stream-files-tutorial-with-rest.md) 
