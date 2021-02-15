---
title: Rotear o tráfego da Web baseado na URL – CLI do Azure
description: Neste artigo, aprenda a rotear o tráfego da Web baseado na URL para especificar pools dimensionáveis de servidores usando a CLI do Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 08/01/2019
ms.author: victorh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 8e8fed99fe0b1de52d2e2d0018dfd8867b54b63b
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566513"
---
# <a name="route-web-traffic-based-on-the-url-using-the-azure-cli"></a>Rotear o tráfego da Web baseado na URL usando a CLI do Azure

Como um administrador de TI que gerencia tráfego da Web, você deseja ajudar seus clientes ou usuários a obter as informações necessárias o mais rápido possível. Uma maneira de otimizar essa experiência é roteando diferentes tipos de tráfego da Web para recursos de servidor diferentes. Este artigo mostra como usar a CLI do Azure para instalar e configurar o roteamento do Gateway de Aplicativo para vários tipos de tráfego do seu aplicativo. O roteamento, em seguida, direciona o tráfego para pools de servidor diferentes com base na URL.

![Exemplo de roteamento de URL](./media/tutorial-url-route-cli/scenario.png)

Neste artigo, você aprenderá como:

* Criar um grupo de recursos para os recursos de rede necessários
* Criar os recursos de rede
* Criar um gateway de aplicativo para o tráfego proveniente de seu aplicativo
* Especificar pools de servidores e regras de roteamento para vários tipos de tráfego
* Criar um conjunto de dimensionamento para cada pool para que o pool possa fazer o dimensionamento automaticamente
* Executar um teste para que você possa verificar se os diferentes tipos de tráfego estão indo para os pools corretos

Se preferir, você pode concluir este procedimento usando o [Azure PowerShell](tutorial-url-route-powershell.md) ou o [portal do Azure](create-url-route-portal.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Este tutorial requer a versão 2.0.4 ou posterior do CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contêiner lógico nos quais os recursos do Azure são implantados e gerenciados. Crie um grupos de recursos usando `az group create`.

O exemplo a seguir cria um grupo de recursos denominado *myResourceGroupAG* no local *eastus*.

```azurecli-interactive
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Criar recursos da rede

Criar a rede virtual denominada *myVNet* e a sub-rede denominada *myAGSubnet* usando `az network vnet create`. Em seguida, adicione uma sub-rede denominada *myBackendSubnet* , requerida pelos servidores de back-end, usando `az network vnet subnet create`. Crie o endereço IP público denominado *myAGPublicIPAddress* usando `az network public-ip create`.

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

## <a name="create-the-app-gateway-with-a-url-map"></a>Criar o gateway de aplicativo com um mapa de URL

Use `az network application-gateway create` para criar um gateway de aplicativo denominado *myAppGateway*. Quando você cria um gateway de aplicativo usando a CLI do Azure, você pode especificar informações de configuração, como configurações de HTTP, sku e capacidade. O gateway de aplicativo é atribuído a *myAGSubnet* e *myAGPublicIPAddress*.

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

 Pode levar vários minutos para criar o gateway de aplicativo. Depois de criar o gateway de aplicativo, você pode ver estes novos recursos:


|Recurso  |Descrição  |
|---------|---------|
|appGatewayBackendPool     |Um gateway de aplicativo deve ter pelo menos um pool de endereços de back-end.|
|appGatewayBackendHttpSettings     |Especifica que a porta 80 e um protocolo HTTP são usados para comunicação.|
|appGatewayHttpListener     |O ouvinte padrão associado ao appGatewayBackendPool|
|appGatewayFrontendIP     |Atribui myAGPublicIPAddress ao appGatewayHttpListener.|
|rule1     |A regra padrão de roteamento que está associada ao appGatewayHttpListener.|

### <a name="add-image-and-video-backend-pools-and-a-port"></a>Adicionar pools de back-end de imagem e vídeo e uma porta

Adicione os pools de back-end denominados *imagesBackendPool* e *videoBackendPool* ao gateway de aplicativo usando `az network application-gateway address-pool create`. Você adiciona a porta de front-end para os pools usando `az network application-gateway frontend-port create`.

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
  --name port8080
```

### <a name="add-a-backend-listener"></a>Adicionar um ouvinte de back-end

Adicione o ouvinte de back-end denominado *backendListener* , que é necessário para rotear o tráfego usando `az network application-gateway http-listener create`.


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port port8080 \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-a-url-path-map"></a>Adicionar um mapa de caminho da URL

Os mapas de caminho de URL certificam que as URLs específicas sejam roteadas para pools de back-end específicos. Crie mapas de caminho da URL chamados *imagePathRule* e *videoPathRule* usando `az network application-gateway url-path-map create` e `az network application-gateway url-path-map rule create`.

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name myPathMap \
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
  --path-map-name myPathMap \
  --paths /video/* \
  --address-pool videoBackendPool
```

### <a name="add-a-routing-rule"></a>Adicionar uma regra de roteamento

A regra de roteamento associa o mapa de URL ao listener que você criou. Adicione uma regra denominada *rule2* usando `az network application-gateway rule create`.

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name rule2 \
  --resource-group myResourceGroupAG \
  --http-listener backendListener \
  --rule-type PathBasedRouting \
  --url-path-map myPathMap \
  --address-pool appGatewayBackendPool
```

## <a name="create-virtual-machine-scale-sets"></a>Criar conjuntos de dimensionamento de máquinas virtuais

Neste artigo, você cria três conjuntos de dimensionamento de máquinas virtuais que oferecem suporte a três pools de back-end que você criou. Os conjuntos de dimensionamento que você cria são denominados *myvmss1* , *myvmss2* , e *myvmss3*. Cada conjunto de dimensionamento contém duas instâncias de máquina virtual nas quais você instala o NGINX.

```azurecli-interactive
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
    --admin-username azureuser \
    --admin-password Azure123456! \
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

Para obter o endereço IP público do gateway de aplicativo, use az network public-ip show. Copie o endereço IP público e cole-o na barra de endereços do seu navegador. Como `http://40.121.222.19`, `http://40.121.222.19:8080/images/test.htm` ou `http://40.121.222.19:8080/video/test.htm`.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Testar a URL de base no gateway de aplicativo](./media/tutorial-url-route-cli/application-gateway-nginx.png)

Altere a URL para http://&lt;ip-address&gt;:8080/images/test.html, substituindo o endereço IP por &lt;ip-address&gt; e você deverá ver algo semelhante ao exemplo a seguir:

![Testar a URL de imagens no gateway de aplicativo](./media/tutorial-url-route-cli/application-gateway-nginx-images.png)

Altere a URL para http:// &lt; IP-address &gt; : 8080/vídeo/test.html, substituindo seu endereço IP para &lt; endereço IP &gt; e você deverá ver algo semelhante ao exemplo a seguir.

![Testar a URL de vídeo no gateway de aplicativo](./media/tutorial-url-route-cli/application-gateway-nginx-video.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando não forem mais necessários, remova o grupo de recursos, o gateway de aplicativo e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>Próximas etapas

[Criar um gateway de aplicativo com o redirecionamento baseado em caminhos de URL](./tutorial-url-redirect-cli.md)
