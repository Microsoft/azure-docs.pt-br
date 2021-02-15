---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: 9f079be76d4d75f05b4a8e132ac425255eed8558
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88605968"
---
<!--Create a media services asset CLI-->

O comando da CLI do Azure a seguir cria um ativo dos Serviços de Mídia. Substitua os valores `assetName`, `amsAccountName` e `resourceGroup` pelos valores com os quais você está trabalhando no momento.

```azurecli
az ams asset create -n assetName -a amsAccountName -g resourceGroup
```
