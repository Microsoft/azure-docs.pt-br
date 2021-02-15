---
title: arquivo de inclusão
description: arquivo de inclusão
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 6/2/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 195caa6f8d7c00c741741fbf80a77bd7fe5579b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84464925"
---
O comando da CLI a seguir negará todo o tráfego para o ponto de extremidade público da conta de armazenamento. Observe que esse comando tem o parâmetro `-bypass` definido como `AzureServices`. Isso permitirá que serviços próprios confiáveis, como a Sincronização de Arquivos do Azure, acessem a conta de armazenamento por meio do ponto de extremidade público.

```bash
# This assumes $storageAccountResourceGroupName and $storageAccountName 
# are still defined from the beginning of this guide.
az storage account update \
    --resource-group $storageAccountResourceGroupName \
    --name $storageAccountName \
    --bypass "AzureServices" \
    --default-action "Deny" \
    --output none
```