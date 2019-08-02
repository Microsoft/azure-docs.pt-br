---
title: Usar a CLI para criar filtros com os Serviços de Mídia do Azure | Microsoft Docs
description: Este tópico mostra como usar a CLI para criar filtros com os Serviços de Mídia.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 26350479f0f066f45c143e1a35061b3a409de309
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786486"
---
# <a name="creating-filters-with-cli"></a>Criando filtros com a CLI 

Ao fornecer conteúdo aos clientes (eventos de transmissão ao vivo ou vídeo sob demanda), seu cliente pode precisar de mais flexibilidade que o que é descrito no arquivo de manifesto do ativo padrão. Os Serviços de Mídia do Azure permitem definir filtros de conta e filtros de recursos para o seu conteúdo. 

Para obter uma descrição detalhada desse recurso e os cenários em que ele é usado, consulte [manifestos dinâmicos](filters-dynamic-manifest-overview.md) e [filtros](filters-concept.md).

Este tópico mostra como configurar um filtro para um ativo de vídeo sob demanda e usar a CLI para os Serviços de Mídia do Microsoft Azure v3 criem [filtros de conta](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) e [filtros de ativo](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

> [!NOTE]
> Certifique-se de examinar [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Pré-requisitos 

- [Crie uma conta de Serviços de Mídia](create-account-cli-how-to.md). Lembre-se de lembrar o nome do grupo de recursos e o nome da conta do Media Services. 

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-filter"></a>Definir um filtro 

O exemplo a seguir define as condições de seleção de faixas adicionadas ao manifesto final. Esse filtro inclui quaisquer faixas de áudio que sejam EC-3 e quaisquer faixas de vídeo com taxa de bits na faixa de 0 a 1000000.

> [!TIP]
> Se você planeja definir **filtros** no REST, observe que você precisa incluir o objeto JSON de wrapper de "Propriedades".  

```json
[
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
                "operation": "NotEqual"
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
```

## <a name="create-account-filters"></a>Crie filtros de conta

O comando [az ams account-filter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) a seguir cria um filtro de conta com seleções de trilha de filtro que foram [definidas anteriormente](#define-a-filter). 

O comando permite que passar um parâmetro opcional `--tracks` que contém o JSON que representa as seleções de faixa.  Use @{file} para carregar o JSON de um arquivo. Se estiver usando a CLI do Azure localmente, especifique o caminho do arquivo inteiro:

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

Além disso, veja [exemplos de JSON para filtros](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create-an-account-filter).

## <a name="create-asset-filters"></a>Criar filtros de ativo

O comando [az-s-asset-filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest) a seguir cria um filtro de ativos com seleções de trilha de filtro [definidas anteriormente](#define-a-filter). 

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

Além disso, veja [exemplos de JSON para filtros](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create-an-asset-filter).

## <a name="associate-filters-with-streaming-locator"></a>Associar filtros de localizador de Streaming

Você pode especificar uma lista de filtros de ativo ou conta, que se aplica a localizador de Streaming. O [Dynamic Packager (ponto de extremidade de Streaming)](dynamic-packaging-overview.md) se aplica a esta lista de filtros junto com aqueles seu cliente especifica a URL. Essa combinação gera uma [manifesto dinâmico](filters-dynamic-manifest-overview.md), que se baseia nos filtros na URL + filtros que você especificar no localizador de Streaming. É recomendável que você use esse recurso se você deseja aplicar filtros, mas não quiser expor os nomes de filtro na URL.

O código CLI a seguir mostra como criar um localizador de Streaming e especificar `filters`. Isso é uma propriedade opcional que utiliza uma lista separada por espaço de nomes de recursos de filtro e/ou nomes de conta de filtro.

```azurecli
az ams streaming-locator create -a amsAccount -g resourceGroup -n streamingLocatorName \
                                --asset-name assetName \                               
                                --streaming-policy-name policyName \
                                --filters filterName1 filterName2
                                
```

## <a name="stream-using-filters"></a>Stream usando filtros

Depois que você definir filtros, seus clientes poderão usá-los na URL de streaming. Os filtros podem ser aplicados a protocolos de streaming de taxa de bits adaptável: Apple HTTP Live Streaming (HLS), MPEG-DASH e Smooth Streaming.

A tabela a seguir mostra alguns exemplos de URLs com filtros:

|Protocol|Exemplo|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-step"></a>Próxima etapa

[Vídeos do Stream](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>Consulte também

[CLI do Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
