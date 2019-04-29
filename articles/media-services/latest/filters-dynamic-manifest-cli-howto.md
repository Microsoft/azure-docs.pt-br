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
ms.date: 11/26/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 2ba3de32f4ec3b9f6faf1d5a51da9c1c91e4a2e4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60732426"
---
# <a name="creating-filters-with-cli"></a>Criando filtros com a CLI 

Ao fornecer conteúdo aos clientes (eventos de transmissão ao vivo ou vídeo sob demanda), seu cliente pode precisar de mais flexibilidade que o que é descrito no arquivo de manifesto do ativo padrão. Os Serviços de Mídia do Azure permitem definir filtros de conta e filtros de recursos para o seu conteúdo. Para obter mais informações, consulte [Filtros e manifestos dinâmicos](filters-dynamic-manifest-overview.md).

Este tópico mostra como configurar um filtro para um ativo de vídeo sob demanda e usar a CLI para os Serviços de Mídia do Microsoft Azure v3 criem [filtros de conta](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) e [filtros de ativo](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

## <a name="prerequisites"></a>Pré-requisitos 

- [Crie uma conta de Serviços de Mídia](create-account-cli-how-to.md). Lembre-se de lembrar o nome do grupo de recursos e o nome da conta do Media Services. 
- Analise [Filtros e manifestos dinâmicos](filters-dynamic-manifest-overview.md).

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-filter"></a>Definir um filtro 

O exemplo a seguir define as condições de seleção de faixas adicionadas ao manifesto final. Esse filtro inclui quaisquer faixas de áudio que sejam EC-3 e quaisquer faixas de vídeo com taxa de bits na faixa de 0 a 1000000.

Os filtros definidos no REST incluem o objeto JSON do wrapper "Propriedades".  

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

## <a name="create-account-filters"></a>Criar filtros de conta

O comando [az ams account-filter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) a seguir cria um filtro de conta com seleções de trilha de filtro que foram [definidas anteriormente](#define-a-filter). 

O comando permite que passar um parâmetro opcional `--tracks` que contém o JSON que representa as seleções de faixa.  Use @{file} para carregar o JSON de um arquivo. Se estiver usando a CLI do Azure localmente, especifique o caminho do arquivo inteiro:

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

Além disso, veja [exemplos de JSON para filtros](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create_an_account_filter).

## <a name="create-asset-filters"></a>Criar filtros de ativo

O comando [az-s-asset-filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest) a seguir cria um filtro de ativos com seleções de trilha de filtro [definidas anteriormente](#define-a-filter). 

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

Além disso, veja [exemplos de JSON para filtros](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create_an_asset_filter).

## <a name="next-step"></a>Próxima etapa

[Vídeos do Stream](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>Consulte também

[CLI do Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
