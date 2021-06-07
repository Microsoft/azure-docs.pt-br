---
title: 'Tutorial: Redirecionamento com base no caminho da URL usando a CLI'
titleSuffix: Azure Application Gateway
description: Neste tutorial, vai aprender como criar um gateway de aplicativo com o tráfego redirecionado baseado em caminhos de URL, usando a CLI do Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 03/05/2021
ms.author: victorh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 042cb42d16fdc5394940b62709b5d25187775014
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772623"
---
# <a name="tutorial-create-an-application-gateway-with-url-path-based-redirection-using-the-azure-cli"></a>Tutorial: Criar um gateway de aplicativo com o redirecionamento baseado em caminhos de URL, usando a CLI do Azure

Você pode usar a CLI do Azure para configurar as [Regras de roteamento baseadas em caminhos de URL](tutorial-url-route-cli.md) quando você cria um [gateway de aplicativo](./overview.md). Neste tutorial, você criará pools de back-end usando [conjuntos de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/overview.md). Em seguida, criará regras de roteamento de URL que garantem que o tráfego da Web seja redirecionado para o pool de back-end apropriado.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar a rede
> * Criar um Gateway de Aplicativo
> * Adicionar ouvintes e regras de roteamento
> * Criar conjuntos de dimensionamento de máquinas virtuais para pools de back-end

O exemplo a seguir mostra o tráfego do site proveniente de ambas as portas 8080 e 8081 e sendo direcionado para os mesmos pools de back-end:

![Exemplo de roteamento de URL](./media/tutorial-url-redirect-cli/scenario.png)

Se preferir, você pode concluir este tutorial usando o [Azure PowerShell](tutorial-url-redirect-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Este tutorial requer a versão 2.0.4 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Criar um grupo de recursos usando [az group create](/cli/azure/group).

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

Use [az network application-gateway create](/cli/azure/network/application-gateway) para criar o gateway do aplicativo denominado myAppGateway. Quando você cria um gateway de aplicativo usando a CLI do Azure, você pode especificar informações de configuração, como configurações de HTTP, sku e capacidade. O gateway de aplicativo é atribuído a *myAGSubnet* e *myPublicIPAddress* que você criou anteriormente.

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

 O gateway de aplicativo pode demorar vários minutos para ser criado. Depois de criar o gateway de aplicativo, você pode ver estes novos recursos:

- *appGatewayBackendPool* - Um gateway de aplicativo deve ter pelo menos um pool de endereços de back-end.
- *appGatewayBackendHttpSettings* - Especifica que a porta 80 e um protocolo HTTP são usados para comunicação.
- *appGatewayHttpListener* - O ouvinte padrão associado ao *appGatewayBackendPool*.
- *appGatewayFrontendIP* - Atribui *myAGPublicIPAddress* ao *appGatewayHttpListener*.
- *rule1* - A regra padrão de roteamento que está associada ao *appGatewayHttpListener*.


### <a name="add-backend-pools-and-ports"></a>Adicionar pools e portas de back-end

Você pode adicionar os pools de endereços de back-end denominados *imagesBackendPool* e *videoBackendPool* para o seu gateway de aplicativo usando [az network application-gateway address-pool create](/cli/azure/network/application-gateway/address-pool). Adicionar as portas de front-end para os pools usando [az network application-gateway frontend-port create](/cli/azure/network/application-gateway/frontend-port). 

```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name imagesBackendPool

az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name videoBackendPool

az network application-gateway frontend-port create \
  --port 8080 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name bport

az network application-gateway frontend-port create \
  --port 8081 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name rport
```

## <a name="add-listeners-and-rules"></a>Adicionar ouvintes e regras

### <a name="add-listeners"></a>Adicionar ouvintes

Adicione os ouvintes de back-end chamados *backendListener* e *redirectedListener* necessários para rotear o tráfego usando [az network application-gateway http-listener create](/cli/azure/network/application-gateway/http-listener).


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port bport \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway

az network application-gateway http-listener create \
  --name redirectedListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port rport \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-the-default-url-path-map"></a>Adicionar o mapa de caminho de URL padrão

Os mapas de caminho de URL garantem que as URLs específicas sejam roteadas para pools de back-end específicos. Você pode criar mapas de caminho de URL chamados *imagePathRule* e *videoPathRule* usando [az network application-gateway url-path-map create](/cli/azure/network/application-gateway/url-path-map) e [az network application-gateway url-path-map rule create](/cli/azure/network/application-gateway/url-path-map/rule)

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name urlpathmap \
  --paths /images/* \
  --resource-group myResourceGroupAG \
  --address-pool imagesBackendPool \
  --default-address-pool appGatewayBackendPool \
  --default-http-settings appGatewayBackendHttpSettings \
  --http-settings appGatewayBackendHttpSettings \
  --rule-name imagePathRule

az network application-gateway url-path-map rule create \
  --gateway-name myAppGateway \
  --name videoPathRule \
  --resource-group myResourceGroupAG \
  --path-map-name urlpathmap \
  --paths /video/* \
  --address-pool videoBackendPool
```

### <a name="add-redirection-configuration"></a>Adicionar a configuração de redirecionamento

Você pode configurar o redirecionamento para o ouvinte usando [az network application-gateway redirect-config create](/cli/azure/network/application-gateway/redirect-config).

```azurecli-interactive
az network application-gateway redirect-config create \
  --gateway-name myAppGateway \
  --name redirectConfig \
  --resource-group myResourceGroupAG \
  --type Found \
  --include-path true \
  --include-query-string true \
  --target-listener backendListener
```

### <a name="add-the-redirection-url-path-map"></a>Adicionar o mapa de caminho de URL de redirecionamento

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name redirectpathmap \
  --paths /images/* \
  --resource-group myResourceGroupAG \
  --redirect-config redirectConfig \
  --rule-name redirectPathRule
```

### <a name="add-routing-rules"></a>Adicionar regras de redirecionamento

As regras de redirecionamento associam os mapa de URL ao ouvintes que você criou. É possível adicionar as regras chamadas *defaultRule* e *redirectedRule* usando [az network application-gateway rule create](/cli/azure/network/application-gateway/rule).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name defaultRule \
  --resource-group myResourceGroupAG \
  --http-listener backendListener \
  --rule-type PathBasedRouting \
  --url-path-map urlpathmap \
  --address-pool appGatewayBackendPool

az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name redirectedRule \
  --resource-group myResourceGroupAG \
  --http-listener redirectedListener \
  --rule-type PathBasedRouting \
  --url-path-map redirectpathmap \
  --address-pool appGatewayBackendPool
```

## <a name="create-virtual-machine-scale-sets"></a>Criar conjuntos de dimensionamento de máquinas virtuais

Neste exemplo, você cria três conjuntos de dimensionamento de máquinas virtuais que oferecem suporte a três pools de back-end que você criou. Os conjuntos de dimensionamento que você cria são denominados *myvmss1*, *myvmss2*, e *myvmss3*. Cada conjunto de dimensionamento contém duas instâncias de máquina virtual nas quais você instala o NGINX.

Substitua \<azure-user> e \<password> por um nome de usuário e uma senha de sua escolha.

```azurecli
for i in `seq 1 3`; do
  if [ $i -eq 1 ]
  then
    poolName="appGatewayBackendPool" 
  fi
  if [ $i -eq 2 ]
  then
    poolName="imagesBackendPool"
  fi
  if [ $i -eq 3 ]
  then
    poolName="videoBackendPool"
  fi

  az vmss create \
    --name myvmss$i \
    --resource-group myResourceGroupAG \
    --image UbuntuLTS \
    --admin-username <azure-user> \
    --admin-password <password> \
    --instance-count 2 \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --vm-sku Standard_DS2 \
    --upgrade-policy-mode Automatic \
    --app-gateway myAppGateway \
    --backend-pool-name $poolName
done
```

### <a name="install-nginx"></a>Instalar o NGINX

```azurecli-interactive
for i in `seq 1 3`; do
  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"], "commandToExecute": "./install_nginx.sh" }'

done
```

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicativo

Para obter o endereço IP público do gateway de aplicativo, use [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). Copie o endereço IP público e cole-o na barra de endereços do seu navegador. Como `http://40.121.222.19`, `http://40.121.222.19:8080/images/test.htm`, `http://40.121.222.19:8080/video/test.htm`, ou `http://40.121.222.19:8081/images/test.htm`.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Testar a URL de base no gateway de aplicativo](./media/tutorial-url-redirect-cli/application-gateway-nginx.png)

Altere a URL para http://&lt;ip-address&gt;:8080/images/test.html, substituindo o endereço IP por &lt;ip-address&gt; e você deverá ver algo semelhante ao exemplo a seguir:

![Testar a URL de imagens no gateway de aplicativo](./media/tutorial-url-redirect-cli/application-gateway-nginx-images.png)

Altere a URL para http://&lt;ip-address&gt;:8080/video/test.html, substituindo o endereço IP por &lt;ip-address&gt;, e você deverá ver algo semelhante ao exemplo a seguir:

![Testar a URL de vídeo no gateway de aplicativo](./media/tutorial-url-redirect-cli/application-gateway-nginx-video.png)

Agora, altera a URL para http://&lt;ip-address&gt;:8081/images/test.htm, substituindo o endereço IP para &lt;ip-address&gt;, e você deverá ver o tráfego redirecionado de volta para o pool de back-end de imagens no http://&lt;ip-address&gt;:8080/images.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, remova o grupo de recursos, o gateway de aplicativo e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroupAG
```
## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre o que você pode fazer com o gateway de aplicativo](./overview.md)