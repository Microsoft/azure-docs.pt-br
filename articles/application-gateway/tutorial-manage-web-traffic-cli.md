---
title: Gerenciar tráfego da Web - CLI do Azure
description: Saiba como criar um gateway de aplicativo com um conjunto de dimensionamento de máquinas virtuais para gerenciar tráfego de web usando a CLI do Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 07/20/2019
ms.author: victorh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 89ba84be61469ff07eff55bb9cd114fe124b3ec2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94566598"
---
# <a name="manage-web-traffic-with-an-application-gateway-using-the-azure-cli"></a>Gerenciar o tráfego da Web com um gateway de aplicativo usando a CLI do Azure

Gateway de aplicativo é usado para gerenciar e proteger o tráfego da web em servidores que você mantém. Você pode usar a CLI do Azure para criar um [gateway de aplicativo](overview.md) que usa um [conjunto de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/overview.md) para servidores de back-end. Neste exemplo, o conjunto de dimensionamento contém duas instâncias de máquina virtual. O conjunto de dimensionamento é adicionado ao pool de back-end padrão do gateway de aplicativo.

Neste artigo, você aprenderá como:

* Configurar a rede
* Criar um Gateway de Aplicativo
* Criar um conjunto de dimensionamento de máquinas virtuais com o pool de back-end padrão

Se preferir, você poderá concluir este procedimento usando o [Azure PowerShell](tutorial-manage-web-traffic-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Este tutorial requer a versão 2.0.4 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Criar um grupo de recursos usando [az group create](/cli/azure/group#az-group-create).

O exemplo a seguir cria um grupo de recursos denominado *myResourceGroupAG* no local *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Criar recursos da rede 

Criar a rede virtual denominada *myVNet* e a sub-rede denominada *myAGSubnet* usando [az network vnet create](/cli/azure/network/vnet). Você pode adicionar a sub-rede denominada *myBackendSubnet* que é necessária para os servidores de back-end usando [az network vnet subnet create](/cli/azure/network/vnet/subnet). Crie o endereço IP público denominado *myAGPublicIPAddress* usando [az network public-ip create](/cli/azure/network/public-ip).

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24

az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24

az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-an-application-gateway"></a>Criar um Gateway de Aplicativo

Use [AZ Network Application-Gateway Create](/cli/azure/network/application-gateway) para criar o gateway de aplicativo chamado *myAppGateway*. Quando você cria um gateway de aplicativo usando a CLI do Azure, você pode especificar informações de configuração, como configurações de HTTP, sku e capacidade. O gateway de aplicativo é atribuído a *myAGSubnet* e *myPublicIPAddress* que você criou anteriormente. 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

 O gateway de aplicativo pode demorar vários minutos para ser criado. Depois de criar o gateway de aplicativo, você verá estes novos recursos:

- *appGatewayBackendPool* - Um gateway de aplicativo deve ter pelo menos um pool de endereços de back-end.
- *appGatewayBackendHttpSettings* - Especifica que a porta 80 e um protocolo HTTP são usados para comunicação.
- *appGatewayHttpListener* - O ouvinte padrão associado ao *appGatewayBackendPool*.
- *appGatewayFrontendIP* - Atribui *myAGPublicIPAddress* ao *appGatewayHttpListener*.
- *rule1* - A regra padrão de roteamento que está associada ao *appGatewayHttpListener*.

## <a name="create-a-virtual-machine-scale-set"></a>Crie um conjunto de dimensionamento de máquinas virtuais

Neste exemplo, você criará um conjunto de dimensionamento de máquinas virtuais que fornece servidores para o pool de back-end no gateway de aplicativo. As máquinas virtuais no conjunto de dimensionamento são associadas com *myBackendSubnet* e *appGatewayBackendPool*. Para criar um conjunto de dimensionamento, use [az vmss create](/cli/azure/vmss#az-vmss-create).

```azurecli-interactive
az vmss create \
  --name myvmss \
  --resource-group myResourceGroupAG \
  --image UbuntuLTS \
  --admin-username azureuser \
  --admin-password Azure123456! \
  --instance-count 2 \
  --vnet-name myVNet \
  --subnet myBackendSubnet \
  --vm-sku Standard_DS2 \
  --upgrade-policy-mode Automatic \
  --app-gateway myAppGateway \
  --backend-pool-name appGatewayBackendPool
```

### <a name="install-nginx"></a>Instalar o NGINX

Agora você pode instalar NGINX no conjunto de dimensionamento de máquinas virtuais para testar a conectividade HTTP para o pool de back-end.

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroupAG \
  --vmss-name myvmss \
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"], "commandToExecute": "./install_nginx.sh" }'
```

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicativo

Para obter o endereço IP público do gateway de aplicativo, use [az network public-ip show](/cli/azure/network/public-ip). Copie o endereço IP público e cole-o na barra de endereços do seu navegador.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Testar a URL de base no gateway de aplicativo](./media/tutorial-manage-web-traffic-cli/tutorial-nginxtest.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, remova o grupo de recursos, o gateway de aplicativo e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroupAG --location eastus
```

## <a name="next-steps"></a>Próximas etapas

[Restringir o tráfego da web com um firewall do aplicativo Web](../web-application-firewall/ag/tutorial-restrict-web-traffic-cli.md)