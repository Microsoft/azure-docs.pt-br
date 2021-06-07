---
title: Criar um Hub IoT usando a CLI do Azure | Microsoft Docs
description: Saiba como usar os comandos de CLI do Azure para criar um grupo de recursos e, em seguida, criar um hub IoT no grupo de recursos. Saiba também como remover o Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: e9757b94330f889869080a0e961ab2728cee86f6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94659906"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Criar um Hub IoT usando a CLI do Azure

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Este artigo mostra como criar um Hub IoT usando a CLI do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-an-iot-hub"></a>Crie um Hub IoT

Use a CLI do Azure para criar um grupo de recursos e, em seguida, adicione um Hub IoT.

1. Quando cria um Hub IoT, você deve criá-lo em um grupo de recursos. Use um grupo de recursos existente ou execute o seguinte [comando para criar um grupo de recursos](/cli/azure/resource):
    
   ```azurecli-interactive
   az group create --name {your resource group name} --location westus
   ```

   > [!TIP]
   > O exemplo anterior cria o grupo de recursos localizado no Oeste dos EUA. Você pode exibir uma lista dos locais disponíveis executando o comando: 
   >
   > ```azurecli-interactive
   > az account list-locations -o table
   > ```
   >

2. Execute o seguinte [comando para criar um Hub IoT](/cli/azure/iot/hub#az-iot-hub-create) no seu grupo de recursos, usando um nome globalmente exclusivo para o Hub IoT:
    
   ```azurecli-interactive
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


O comando anterior cria um Hub IoT com tipo de preço S1, pelo qual você será cobrado. Para saber mais, confira [Preço do Hub IoT do Azure](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="remove-an-iot-hub"></a>Remover um Hub IoT

Você pode usar a CLI do Azure para [excluir um recurso individual](/cli/azure/resource), tal como um Hub IoT, ou excluir um grupo de recursos e todos os seus recursos, incluindo quaisquer Hubs IoT.

Para [excluir um hub IOT](/cli/azure/iot/hub#az-iot-hub-delete), execute o seguinte comando:

```azurecli-interactive
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

Para [excluir um grupo de recursos](/cli/azure/group#az-group-delete) e todos os seus recursos, execute o seguinte comando:

```azurecli-interactive
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar um Hub IoT, veja os seguintes artigos:

* [Guia do desenvolvedor do Hub IoT](iot-hub-devguide.md)
* [Usar o portal do Azure para gerenciar o Hub IoT](iot-hub-create-through-portal.md)