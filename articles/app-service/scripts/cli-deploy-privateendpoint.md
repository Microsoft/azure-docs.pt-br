---
title: 'CLI: Implantar o ponto de extremidade privado para o Aplicativo Web com a CLI do Azure'
description: Saiba como usar a CLI do Azure para implantar o ponto de extremidade privado para seu Aplicativo Web
author: ericgre
ms.assetid: a56faf72-7237-41e7-85ce-da8346f2bcaa
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/06/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 21f937d95c6cd0dafa27daae50d7a74648af7bdc
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97006099"
---
# <a name="create-an-app-service-app-and-deploy-private-endpoint-using-azure-cli"></a>Criar um aplicativo do Serviço de Aplicativo e implantar um ponto de extremidade privado usando a CLI do Azure

Este script de exemplo cria um aplicativo no Serviço de Aplicativo com os recursos relacionados e implanta um ponto de extremidade privado.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Este tutorial exige a versão 2.0.28 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Para criar qualquer recurso, você precisa criar um grupo de recursos para hospedar o Aplicativo Web, a Rede Virtual e outros componentes de rede. Crie um grupo de recursos com [az group create](/cli/azure/group). Este exemplo cria um grupo de recursos chamado *myResourceGroup* na localização *francecentral*:

```azurecli-interactive
az group create --name myResourceGroup --location francecentral 
```

## <a name="create-an-app-service-plan"></a>Criar um Plano do Serviço de Aplicativo

Você precisará criar um Plano do Serviço de Aplicativo para hospedar seu Aplicativo Web.
Crie um Plano do Serviço de Aplicativo com [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create).
Este exemplo cria o Plano do Serviço de Aplicativo chamado *myAppServicePlan* na localização *francecentral* com o SKU *P1V2* e apenas um trabalho: 

```azurecli-interactive
az appservice plan create \
--name myAppServicePlan \
--resource-group myResourceGroup \
--location francecentral \
--sku P1V2 \
--number-of-workers 1
```

## <a name="create-a-web-app"></a>Criar um aplicativo Web

Agora que você tem um Plano do Serviço de Aplicativo, implante um Aplicativo Web.
Criar um Aplicativo Web com [az appservice plan create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create.
Este exemplo cria um Aplicativo Web chamado *mySiteName* no plano chamado *myAppServicePlan*

```azurecli-interactive
az webapp create \
--name mySiteName \
--resource-group myResourceGroup \
--plan myAppServicePlan
```

## <a name="create-a-vnet"></a>Criar uma VNET

Crie uma Rede Virtual com [az network vnet create](/cli/azure/network/vnet). O exemplo cria uma Rede Virtual padrão chamada *myVNet* com uma sub-rede chamada *mySubnet*:

```azurecli-interactive
az network vnet create \
--name myVNet \
--resource-group myResourceGroup \
--location francecentral \
--address-prefixes 10.8.0.0/16 \
--subnet-name mySubnet \
--subnet-prefixes 10.8.100.0/24
```

## <a name="configure-the-subnet"></a>Configurar a sub-rede 

Você precisará atualizar a sub-rede para desabilitar as políticas de rede do ponto de extremidade privado. Atualize uma configuração de sub-rede denominada *mySubnet* com [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
--name mySubnet \
--resource-group myResourceGroup \
--vnet-name myVNet \
--disable-private-endpoint-network-policies true
```

## <a name="create-the-private-endpoint"></a>Criar um Ponto de Extremidade Privado

Crie o ponto de extremidade privado para seu Aplicativo Web com [az network private-endpoint create](/cli/azure/network/private-endpoint). Este exemplo cria um ponto de extremidade privado chamado *myPrivateEndpoint* na VNet *myVNet* na sub-rede *mySubnet* com uma conexão chamada *myConnectionName* para a ID do recurso do meu Aplicativo Web /subscriptions/SubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/myWebApp, o parâmetro de grupo é *sites* para o tipo de Aplicativo Web. 

```azurecli-interactive
az network private-endpoint create \
--name myPrivateEndpoint \
--resource-group myResourceGroup \
--vnet-name myVNet \
--subnet mySubnet \
--connection-name myConnectionName \
--private-connection-resource-id /subscriptions/SubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/myWebApp \
--group-id sites
```

## <a name="configure-the-private-zone"></a>Configurar a zona privada

No final, você precisará criar uma zona DNS privada chamada *privatelink.azurewebsites.net* vinculada à VNet para resolver o nome DNS do Aplicativo Web.


```azurecli-interactive
az network private-dns zone create \
--name privatelink.azurewebsites.net \
--resource-group myResourceGroup

az network private-dns link vnet create \
--name myDNSLink \
--resource-group myResourceGroup \
--registration-enabled false \
--virtual-network myVNet \
--zone-name privatelink.azurewebsites.net

az network private-endpoint dns-zone-group create \
--name myZoneGroup \
--resource-group myResourceGroup \
--endpoint-name myPrivateEndpoint \
--private-dns-zone privatelink.azurewebsites.net \
--zone-name privatelink.azurewebsites.net
```






[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]


## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).
- Os exemplos de script da CLI do Serviço de Aplicativo adicionais podem ser encontrados na [documentação do Serviço de Aplicativo do Azure](../samples-cli.md).
