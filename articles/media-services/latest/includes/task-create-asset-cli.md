---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: e16e3dc6788fb30a87a78fd6d6077087f461cfa7
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107512563"
---
<!--Create a media services asset CLI-->

O comando da CLI do Azure a seguir cria um ativo dos Serviços de Mídia. Substitua os valores `assetName`, `amsAccountName` e `resourceGroup` pelos valores com os quais você está trabalhando no momento.

```azurecli
az ams asset create -n assetName -a amsAccountName -g resourceGroup
```
