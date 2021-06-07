---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 03/17/2020
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: ea5d4ef26fb14e22b871bb4bfa1054cb749d38e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97673011"
---
## <a name="prepare-an-iot-hub"></a>Preparar um hub IoT

É necessário ter um Hub IoT do Azure na assinatura do Microsoft Azure para concluir as etapas deste artigo. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Se estiver usando a CLI do Azure localmente, primeiro entre na assinatura do Azure usando `az login`. Se estiver executando esses comandos no Azure Cloud Shell, você entrará automaticamente.

Se estiver usando a CLI do Azure no local, a versão do `az` deverá ser **2.8.0** ou posterior; o Azure Cloud Shell usa a versão mais recente. Use o comando `az --version` para verificar a versão instalada no computador.

Execute o seguinte comando para adicionar a Extensão de IoT do Microsoft Azure para a CLI do Azure à instância:

```azurecli-interactive
az extension add --name azure-iot
```

Se você ainda não tiver um hub IoT que possa usar, execute os comandos a seguir para criar um grupo de recursos e um Hub IoT de camada gratuita em sua assinatura. Substitua `<YourIoTHubName>` por um nome de hub de sua escolha:

```azurecli-interactive
az group create --name my-pnp-resourcegroup \
    --location centralus
az iot hub create --name <YourIoTHubName> \
    --resource-group my-pnp-resourcegroup --sku F1
```

Use o comando a seguir para criar uma identidade do dispositivo no Hub IoT. Substitua os espaços reservados `<YourIoTHubName>` e `<YourDeviceID>` pelo seu _nome do Hub IoT_ e uma _ID do dispositivo_ de sua escolha.

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```
