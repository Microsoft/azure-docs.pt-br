---
title: Criar um serviço de vínculo privado do Azure usando CLI do Azure
description: Saiba como criar um serviço de vínculo privado do Azure usando o CLI do Azure
services: private-link
author: asudbring
ms.service: private-link
ms.topic: how-to
ms.date: 01/22/2021
ms.author: allensu
ms.openlocfilehash: 567ed736c52e8b3cbb03edeb19b3c0e2364e4112
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98757332"
---
# <a name="create-a-private-link-service-using-azure-cli"></a>Criar um serviço de vínculo privado usando CLI do Azure

Introdução à criação de um serviço de Link Privado referente ao seu serviço.  Conceda acesso do Link Privado ao serviço ou recurso implantado por trás de um Standard Load Balancer do Azure.  Os usuários do seu serviço têm acesso privado da rede virtual deles.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

- Este guia de início rápido requer a versão 2.0.28 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos com [az group create](/cli/azure/group#az_group_create):

* Chamado **CreatePrivLinkService-RG**. 
* Na localização **eastus**.

```azurecli-interactive
  az group create \
    --name CreatePrivLinkService-rg \
    --location eastus2

```

## <a name="create-an-internal-load-balancer"></a>Criar um balanceador de carga interno

Nesta seção, você criará uma rede virtual e um Azure Load Balancer interno.

### <a name="virtual-network"></a>Rede virtual

Nesta seção, você criará uma rede virtual e uma sub-rede para hospedar o balanceador de carga que acessa o serviço de Link Privado.

Crie uma rede virtual usando [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create):

* Chamada **myVNet**.
* Prefixo de endereço **10.1.0.0/16**.
* Sub-rede chamada **mysubnet**.
* Prefixo de sub-rede **10.1.0.0/24**.
* No grupo de recursos **CreatePrivLinkService-RG** .
* Local do **eastus2**.
* Desabilite a política de rede para o serviço de vínculo privado na sub-rede.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePrivLinkService-rg \
    --location eastus2 \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefixes 10.1.0.0/24

```

Para atualizar a sub-rede para desabilitar as políticas de rede do serviço de vínculo privado, use [AZ Network vnet subnet Update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
    --name mySubnet \
    --resource-group CreatePrivLinkService-rg \
    --vnet-name myVNet \
    --disable-private-link-service-network-policies true
```

### <a name="create-standard-load-balancer"></a>Criar Standard Load Balancer

Esta seção fornece detalhes sobre como criar e configurar os componentes do balanceador de carga abaixo:

  * Um pool de IPs de front-end que recebe o tráfego de rede de entrada no balanceador de carga.
  * Um pool de IPs de back-end ao qual o pool de front-end envia o tráfego de rede com a carga balanceada.
  * Uma investigação de integridade que determina a integridade das instâncias de VM de back-end.
  * Uma regra de balanceador de carga que define como o tráfego é distribuído para as VMs.

### <a name="create-the-load-balancer-resource"></a>Criar o recurso do balanceador de carga

Crie um balanceador de carga público com [az network lb create](/cli/azure/network/lb#az-network-lb-create):

* Chamado **myLoadBalancer**.
* Um pool de front-end chamado **myFrontEnd**.
* Um pool de back-end chamado **myBackEndPool**.
* Associado à rede virtual **myVNet**.
* Associado à sub- **rede** de back-end da sub-rede.

```azurecli-interactive
  az network lb create \
    --resource-group CreatePrivLinkService-rg \
    --name myLoadBalancer \
    --sku Standard \
    --vnet-name myVnet \
    --subnet mySubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool
```

### <a name="create-the-health-probe"></a>Criar a investigação de integridade

Uma investigação de integridade verifica todas as instâncias da máquina virtual para garantir que elas possam enviar tráfego de rede. 

Uma máquina virtual com uma verificação de investigação com falha é removida do balanceador de carga. A máquina virtual será adicionada novamente ao balanceador de carga quando a falha for resolvida.

Crie uma investigação de integridade com [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create):

* Monitora a integridade das máquinas virtuais.
* Chamado **myHealthProbe**.
* Protocolo **TCP**.
* Monitorando a **Porta 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePrivLinkService-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-the-load-balancer-rule"></a>Criar a regra de balanceador de carga

Uma regra de balanceador de carga define:

* A configuração do IP de front-end para o tráfego de entrada.
* O pool de IPs de back-end para receber o tráfego.
* As portas de origem e de destino necessárias. 

Crie uma regra de balanceador de carga com [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create):

* Chamada **myHTTPRule**
* Escutando na **Porta 80** no pool de front-end **myFrontEnd**.
* Enviando tráfego de rede com balanceamento de carga para o pool de endereços de back-end **myBackEndPool** usando a **Porta 80**. 
* Usando a investigação de integridade **myHealthProbe**.
* Protocolo **TCP**.
* Tempo limite de ociosidade de **15 minutos**.
* Habilitar redefinição de TCP.

```azurecli-interactive
  az network lb rule create \
    --resource-group CreatePrivLinkService-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15 \
    --enable-tcp-reset true
```

## <a name="create-a-private-link-service"></a>Criar um serviço de Link Privado

Nesta seção, crie um serviço de vínculo privado que usa o Azure Load Balancer criado na etapa anterior.

Crie um serviço de vínculo privado usando uma configuração de IP de front-end do balanceador de carga padrão com [AZ Network Private-link-Service Create](/cli/azure/network/private-link-service#az-network-private-link-service-create):

* Chamado **myPrivateLinkService**.
* Na rede virtual **myVNet**.
* Associado à configuração padrão do Load Balancer **myLoadBalancer** e FrontEnd **myfrontend**.
* No local **eastus2** .
 
```azurecli-interactive
az network private-link-service create \
    --resource-group CreatePrivLinkService-rg \
    --name myPrivateLinkService \
    --vnet-name myVNet \
    --subnet mySubnet \
    --lb-name myLoadBalancer \
    --lb-frontend-ip-configs myFrontEnd \
    --location eastus2
```

Seu serviço de vínculo privado é criado e pode receber tráfego. Se você quiser ver os fluxos de tráfego, configure seu aplicativo por trás do balanceador de carga padrão.


## <a name="create-private-endpoint"></a>Criar um ponto de extremidade privado

Nesta seção, você mapeará o serviço de vínculo privado para um ponto de extremidade privado. Uma rede virtual contém o ponto de extremidade privado para o serviço de vínculo privado. Essa rede virtual contém os recursos que acessarão o serviço de vínculo privado.

### <a name="create-private-endpoint-virtual-network"></a>Criar rede virtual de ponto de extremidade privado

Crie uma rede virtual usando [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create):

* Chamado **myVNetPE**.
* Prefixo de endereço de **11.1.0.0/16**.
* Sub-rede chamada **mySubnetPE**.
* Prefixo de sub-rede de **11.1.0.0/24**.
* No grupo de recursos **CreatePrivLinkService-RG** .
* Local do **eastus2**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePrivLinkService-rg \
    --location eastus2 \
    --name myVNetPE \
    --address-prefixes 11.1.0.0/16 \
    --subnet-name mySubnetPE \
    --subnet-prefixes 11.1.0.0/24
```

Para atualizar a sub-rede para desabilitar as políticas de rede de ponto de extremidade particulares, use [AZ Network vnet subnet Update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
    --name mySubnetPE \
    --resource-group CreatePrivLinkService-rg \
    --vnet-name myVNetPE \
    --disable-private-endpoint-network-policies true
```

### <a name="create-endpoint-and-connection"></a>Criar ponto de extremidade e conexão

* Use [AZ Network Private-link-Service show](/cli/azure/network/private-link-service#az_network_private_link_service_show) para obter a ID de recurso do serviço de vínculo privado. O comando coloca a ID do recurso em uma variável para uso posterior.

* Use [AZ Network Private-Endpoint Create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) para criar o ponto de extremidade privado na rede virtual que você criou anteriormente.

* Chamado **MyPrivateEndpoint**.
* No grupo de recursos **CreatePrivLinkService-RG** .
* Nome da conexão **myPEconnectiontoPLS**.
* Local do **eastus2**.
* Em rede virtual **myVNetPE** e sub-rede **mySubnetPE**.

```azurecli-interactive
  export resourceid=$(az network private-link-service show \
    --name myPrivateLinkService \
    --resource-group CreatePrivLinkService-rg \
    --query id \
    --output tsv)

  az network private-endpoint create \
    --connection-name myPEconnectiontoPLS \
    --name myPrivateEndpoint \
    --private-connection-resource-id $resourceid \
    --resource-group CreatePrivLinkService-rg \
    --subnet mySubnetPE \
    --manual-request false \
    --vnet-name myVNetPE 

```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, use o comando [AZ Group Delete](/cli/azure/group#az-group-delete) para remover o grupo de recursos, o serviço de vínculo privado, o balanceador de carga e todos os recursos relacionados.

```azurecli-interactive
  az group delete \
    --name CreatePrivLinkService-rg 
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você:

* criou uma rede virtual e um Azure Load Balancer interno.
* criou um serviço de link privado

Para saber mais sobre o ponto de extremidade privado do Azure, acesse:
> [!div class="nextstepaction"]
> [Início Rápido: criar um Ponto de Extremidade Privado usando a CLI do Azure](create-private-endpoint-cli.md)
