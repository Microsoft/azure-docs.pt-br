---
title: Redefinir suas credenciais de conta – CLI
description: Use o script da CLI do Azure para redefinir as credenciais de sua conta e recuperar as configurações do app.config.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: cc605a08147da1d076b302e515a4ebe8d411a782
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101091849"
---
# <a name="azure-cli-example-reset-the-account-credentials"></a>Exemplo da CLI do Azure: Redefinir as credenciais da conta

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

O script da CLI do Azure neste artigo mostra como redefinir as credenciais da sua conta e recuperar as configurações de app.config.

## <a name="prerequisites"></a>Pré-requisitos

[Crie uma conta de Serviços de Mídia](./create-account-howto.md).

## <a name="example-script"></a>Script de exemplo

```azurecli-interactive
# Update the following variables for your own settings:
resourceGroup=amsResourceGroup
amsAccountName=amsmediaaccountname

az ams account sp reset-credentials \
  --account-name $amsAccountName \
  --resource-group $resourceGroup
 ```

## <a name="next-steps"></a>Próximas etapas

* [az ams](/cli/azure/ams)
* [Redefinir credenciais](/cli/azure/ams/account/sp#az-ams-account-sp-reset-credentials)
