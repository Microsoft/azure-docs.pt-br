---
title: 'Início rápido: criar e configurar o Servidor de Rota usando a CLI do Azure'
description: Neste início rápido, você aprenderá a criar e configurar um Servidor de Rota usando a CLI do Azure.
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: e9c583db7493afc04b2c66553801f62d364b0a80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103419601"
---
# <a name="quickstart-create-and-configure-route-server-using-azure-cli"></a>Início rápido: criar e configurar o Servidor de Rota usando a CLI do Azure 

Este artigo ajuda a configurar o Servidor de Rota do Azure para ser emparelhado com uma NVA (solução de virtualização de rede) na rede virtual usando a CLI do Azure. O Servidor de Rota do Azure vai aprender e programar as rotas da NVA para as máquinas virtuais da rede virtual. Também vai anunciar as rotas de rede virtual para a NVA. Para obter mais informações, leia [Servidor de Rota do Azure](overview.md).

> [!IMPORTANT]
> No momento, o Servidor de Rota do Azure (versão prévia) está na versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

##  <a name="prerequisites"></a>Pré-requisitos 

* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
* Verifique se você tem os módulos mais recentes da CLI do Azure ou se pode usar o Azure Cloud Shell no portal. 
* Examine os [limites de serviço do Servidor de Rota do Azure](route-server-faq.md#limitations). 

##  <a name="create-a-route-server"></a>Criar um Servidor de Rota 

###  <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Entre na sua conta do Azure e selecione sua assinatura. 

Para iniciar sua configuração, entrar na sua conta do Azure. Se usar o "Experimente" do Cloud Shell, você entrará automaticamente. Use o exemplo a seguir para ajudar a conectar:

```azurecli-interactive
az login
```

Verificar as assinaturas da conta.

```azurecli-interactive
az account list
```

Selecione a assinatura para a qual você deseja criar um circuito do ExpressRoute.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

### <a name="create-a-resource-group-and-virtual-network"></a>Criar um grupo de recursos e uma rede virtual 

Para criar um Servidor de Rota do Azure, você precisa de uma rede virtual para hospedar a implantação. Use o comando a seguir para criar um grupo de recursos e uma rede virtual. Se você já tiver uma rede virtual, pode pular para a próxima seção.

```azurecli-interactive
az group create -n "RouteServerRG" -l "westus" 
az network vnet create -g "RouteServerRG" -n "myVirtualNetwork" --address-prefix "10.0.0.0/16" 
``` 

### <a name="add-a-subnet"></a>Adicionar uma sub-rede 

1. Adicione uma sub-rede chamada *RouteServerSubnet* para implantar o Servidor de Rota do Azure. Essa é uma sub-rede dedicada somente para o Servidor de Rota do Azure. O RouteServerSubnet deve ser /27 ou um prefixo mais curto (como /26, /25), caso contrário, você receberá uma mensagem de erro ao adicionar o Servidor de Rota do Azure.

    ```azurecli-interactive 
    az network vnet subnet create -g "RouteServerRG" --vnet-name "myVirtualNetwork" --name "RouteServerSubnet" --address-prefix "10.0.0.0/24"  
    ``` 

1. Obtenha a ID de RouteServerSubnet. Para ver a ID de recurso de todas as sub-redes na rede virtual, use este comando: 

    ```azurecli-interactive 
    $subnet_id = $(az network vnet subnet show -n "RouteServerSubnet" --vnet-name "myVirtualNetwork" -g "RouteServerRG" --query id -o tsv) 
    ``` 

A ID de RouteServerSubnet é parecida com a seguinte: 

`/subscriptions/<subscriptionID>/resourceGroups/RouteServerRG/providers/Microsoft.Network/virtualNetworks/myVirtualNetwork/subnets/RouteServerSubnet`

## <a name="create-the-route-server"></a>Crie o Servidor de Rota 

Crie o Servidor de Rota usando este comando: 

```azurecli-interactive
az network routeserver create -n "myRouteServer" -g "RouteServerRG" --hosted-subnet $subnet_id  
``` 

O local precisa corresponder ao local da rede virtual. O HostedSubnet é a ID de RouteServerSubnet que você obteve na seção anterior. 

## <a name="create-peering-with-an-nva"></a>Criar o emparelhamento com uma NVA 

Use o seguinte comando para estabelecer o emparelhamento do Servidor de Rota para a NVA: 

```azurecli-interactive 

az network routeserver peering create --routeserver-name "myRouteServer" -g "RouteServerRG" --peer-ip "nva_ip" --peer-asn "nva_asn" -n "NVA1_name" 

``` 

O "nva_ip" é o IP da rede virtual atribuído à NVA. O "nva_asn" é o ASN (Número do Sistema Autônomo) configurado na NVA. O ASN pode ser qualquer número de 16 bits que não esteja no intervalo de 65515-65520. Esse intervalo de ASNs é reservado pela Microsoft. 

Para configurar o emparelhamento com uma NVA diferente ou outra instância da mesma NVA para redundância, use este comando:

```azurecli-interactive 

az network routeserver peering create --routeserver-name "myRouteServer" -g "RouteServerRG" --peer-ip "nva_ip" --peer-asn "nva_asn" -n "NVA2_name" 
``` 

## <a name="complete-the-configuration-on-the-nva"></a>Conclua a configuração na NVA 

Para concluir a configuração na NVA e habilitar as sessões do protocolo BGP, você precisa do IP e do ASN do Servidor de Rota do Azure. Você pode obter essas informações usando este comando: 

```azurecli-interactive 
az network routeserver show -g "RouteServerRG" -n "myRouteServer" 
``` 

A saída tem as seguintes informações. 

```azurecli-interactive 
RouteServerAsn  : 65515 

RouteServerIps  : {10.5.10.4, 10.5.10.5}  "virtualRouterAsn": 65515, 

  "virtualRouterIps": [ 

    "10.0.0.4", 

    "10.0.0.5" 

  ], 

``` 

## <a name="configure-route-exchange"></a>Configurar troca de rota 

Se você tiver um gateway de ExpressRoute e um gateway de VPN do Azure na mesma VNet e quiser que troquem rotas, poderá habilitar a troca de rota no Servidor de Rota do Azure.

> [!IMPORTANT]
> Para implantações de Greenfield, certifique-se de criar o gateway de VPN do Azure antes de criar o Servidor de Rota do Azure; caso contrário, a implantação do Gateway de VPN do Azure falhará.
> 

1. Habilite a troca de rota entre o Servidor de Rota do Azure e os gateways usando este comando:

```azurecli-interactive 
az network routeserver update -g "RouteServerRG" -n "myRouteServer" --allow-b2b-traffic true 

``` 

2. Desabilite a troca de rota entre o Servidor de Rota do Azure e os gateways usando este comando:

```azurecli-interactive
az network routeserver update -g "RouteServerRG" -n "myRouteServer" --allow-b2b-traffic false 
``` 

## <a name="troubleshooting"></a>Solução de problemas 

Você pode exibir as rotas anunciadas e recebidas pelo Servidor de Rota do Azure usando este comando:

```azurecli-interactive 
az network routeserver peering list-advertised-routes -g RouteServerRG --vrouter-name myRouteServer -n NVA1_name 
az network routeserver peering list-learned-routes -g RouteServerRG --vrouter-name myRouteServer -n NVA1_name 
``` 

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não precisar mais do Servidor de Rota do Azure, use estes comandos para remover o emparelhamento via BGP e, em seguida, remova o Servidor de Rota. 

1. Remova o emparelhamento via protocolo BGP entre o Servidor de Rota do Azure e uma NVA usando este comando:

```azurecli-interactive
az network routeserver peering delete --routeserver-name "myRouteServer" -g "RouteServerRG" -n "NVA2_name" 
``` 

2. Remova o Servidor de Rota usando este comando: 

```azurecli-interactive 
az network routeserver delete -n "myRouteServer" -g "RouteServerRG" 
``` 

## <a name="next-steps"></a>Próximas etapas

Depois de criar o Servidor de Rota do Azure, continue a aprender como o Servidor de Rota do Azure interage com os gateways de VPN e ExpressRoute: 

> [!div class="nextstepaction"]
> [Suporte à VPN do Azure e ao Azure ExpressRoute](expressroute-vpn-support.md)
 
