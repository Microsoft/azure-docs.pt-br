---
title: Gerenciar IoT Central de CLI do Azure | Microsoft Docs
description: Este artigo descreve como criar e gerenciar seu aplicativo IoT Central usando a CLI. Você pode exibir, modificar e remover o aplicativo usando a CLI.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
ms.custom: devx-track-azurecli
manager: philmea
ms.openlocfilehash: 6496e944d30724fe9e8db7168f9c9cb1552dcd1b
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2021
ms.locfileid: "98203314"
---
# <a name="manage-iot-central-from-azure-cli"></a>Gerenciar IoT Central de CLI do Azure

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Em vez de criar e gerenciar IoT Central aplicativos no site [do Azure IOT central Application Manager](https://aka.ms/iotcentral) , você pode usar [CLI do Azure](/cli/azure/) para gerenciar seus aplicativos.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Se você precisar executar os comandos da CLI em uma assinatura do Azure diferente, consulte [alterar a assinatura ativa](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#change-the-active-subscription&preserve-view=true).

## <a name="create-an-application"></a>Criar um aplicativo

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

Use o comando [AZ IOT central app Create](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-create&preserve-view=true) para criar um aplicativo IOT central em sua assinatura do Azure. Por exemplo:

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iot central app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku ST1 --template "iotc-pnp-preview" \
  --display-name "My Custom Display Name"
```

Esses comandos primeiro criam um grupo de recursos na região leste dos EUA para o aplicativo. A tabela a seguir descreve os parâmetros usados com o comando **AZ IOT central app Create** :

| Parâmetro         | Descrição |
| ----------------- | ----------- |
| resource-group    | O grupo de recursos que contém o aplicativo. Esse grupo de recursos já precisa existir na sua assinatura. |
| local          | Por padrão, esse comando usa o local do grupo de recursos. No momento, você pode criar um aplicativo IoT Central nas regiões da **Austrália**, **Pacífico Asiático**, **Europa**, **Estados Unidos**, **Reino Unido** e **Japão** . |
| name              | Digite o nome do aplicativo no portal do Azure. |
| subdomain         | O subdomínio na URL do aplicativo. No exemplo, a URL do aplicativo é `https://mysubdomain.azureiotcentral.com`. |
| sku               | No momento, você pode usar **ST1** ou **ST2**. Confira [Preço do Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
| template          | O modelo de aplicativo a usar. Para obter mais informações, confira a tabela a seguir. |
| nome de exibição      | O nome do aplicativo, conforme exibido na interface do usuário. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-applications"></a>Exibir seus aplicativos

Use o comando [AZ IOT central app List](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-list&preserve-view=true) para listar seus aplicativos IOT central e exibir metadados.

## <a name="modify-an-application"></a>Modificar um aplicativo

Use o comando [AZ IOT central app Update](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-update&preserve-view=true) para atualizar os metadados de um aplicativo IOT central. Por exemplo, para alterar o nome de exibição do aplicativo:

```azurecli-interactive
az iot central app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Remover um aplicativo

Use o comando [AZ IOT central app Delete](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-delete&preserve-view=true) para excluir um aplicativo IOT central. Por exemplo:

```azurecli-interactive
az iot central app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a gerenciar os aplicativos do Azure IoT Central do CLI do Azure, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Administrar seu aplicativo](howto-administer.md)
