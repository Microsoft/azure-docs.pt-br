---
title: 'Início Rápido: Criar um balanceador de carga público – CLI do Azure'
titleSuffix: Azure Load Balancer
description: O início rápido mostra como criar um balanceador de carga público usando a CLI do Azure
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
tags: azure-resource-manager
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 9b332b18930d58ebb1d155c35a74eed69a90ce73
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788755"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-azure-cli"></a>Início Rápido: Criar um balanceador de carga público para balancear cargas de VMs usando a CLI do Azure

Veja uma introdução ao Azure Load Balancer usando a CLI do Azure para criar um balanceador de carga público e três máquinas virtuais.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este guia de início rápido requer a versão 2.0.28 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos com [az group create](/cli/azure/group#az_group_create):

* Chamado **CreatePubLBQS-RG**. 
* Na localização **eastus**.

```azurecli-interactive
  az group create \
    --name CreatePubLBQS-rg \
    --location eastus
```
---

# <a name="standard-sku"></a>[**SKU Standard**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>O balanceador de carga de SKU Standard é recomendado para cargas de trabalho de produção. Para obter mais informações sobre SKUs, confira **[SKUs do Azure Load Balancer](skus.md)** .

:::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/resources-diagram.png" alt-text="Recursos do Standard Load Balancer criados para o guia de início rápido." border="false":::

## <a name="configure-virtual-network---standard"></a>Configurar a rede virtual – Standard

Antes de implantar VMs e testar o balanceador de carga, crie os recursos da rede virtual de suporte.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Crie uma rede virtual usando [az network vnet create](/cli/azure/network/vnet#az_network_vnet_createt):

* Chamada **myVNet**.
* Prefixo de endereço **10.1.0.0/16**.
* Sub-rede chamada **myBackendSubnet**.
* Prefixo de sub-rede **10.1.0.0/24**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Localização de **eastus**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePubLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```
### <a name="create-a-public-ip-address"></a>Criar um endereço IP público

Use [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) para criar um endereço IP público para o bastion host:

* Crie um endereço IP público com redundância de zona padrão chamado **myBastionIP**.
* Em **CCreatePubLBQS-rg**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>Criar uma sub-rede do bastion

Use o comando [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) para criar uma sub-rede do bastion:

* Chamada **AzureBastionSubnet**.
* Prefixo de endereço **10.1.1.0/24**.
* Na rede virtual **myVNet**.
* No grupo de recursos **CreateIntLBQS-rg**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePubLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>Criar um bastion host

Use [az network bastion create](/cli/azure/network/bastion#az_network_bastion_create) para criar um bastion host:

* chamado **myBastionHost**.
* Em **CreatePubLBQS-rg**.
* Associado ao IP público **myBastionIP**.
* Associado à rede virtual **myVNet**.
* Na localização **eastus**.

```azurecli-interactive
az network bastion create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Levará alguns minutos para que o host do Azure Bastion seja implantado.

### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Para um Standard Load Balancer, as VMs no endereço de back-end precisam ter adaptadores de rede que pertençam a um grupo de segurança de rede. 

Crie um grupo de segurança de rede usando [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create):

* Chamado **myNSG**.
* No grupo de recursos **CreateIntLBQS-rg**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreatePubLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Criar uma regra de grupo de segurança de rede

Crie uma regra de grupo de segurança de rede usando [az network nsg create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create):

* Chamada **myNSGRuleHTTP**.
* No grupo de segurança de rede criado na etapa anterior, **myNSG**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Protocolo **(*)** .
* Direção de **Entrada**.
* Origem **(*)** .
* Destino **(*)** .
* Porta de destino **Porta 80**.
* Acesso **Permitir**.
* Prioridade **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreatePubLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

## <a name="create-backend-servers---standard"></a>Criar servidores back-end – Standard

Nesta seção, você cria:

* Três adaptadores de rede para as máquinas virtuais.
* Três máquinas virtuais a serem usadas como servidores de back-end para o balanceador de carga.

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Criar interfaces de rede para as máquinas virtuais

Crie três interfaces de rede com [az network nic create](/cli/azure/network/nic#az_network_nic_create):

* Chamados **myNicVM1**, **myNicVM2** e **myNicVM3**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Na rede virtual **myVNet**.
* Na sub-rede **myBackendSubnet**.
* No grupo de segurança de rede **myNSG**.

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreatePubLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie as máquinas virtuais com [az vm create](/cli/azure/vm#az_vm_create):

### <a name="vm1"></a>VM1
* Chamada **myVM1**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Anexada ao adaptador de rede **myNicVM1**.
* Imagem da máquina virtual **win2019datacenter**.
* Na **Zona 1**.

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone 1 \
    --no-wait
```
#### <a name="vm2"></a>VM2
* Chamada **myVM2**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Anexada ao adaptador de rede **myNicVM2**.
* Imagem da máquina virtual **win2019datacenter**.
* Na **Zona 2**.

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone 2 \
    --no-wait
```

#### <a name="vm3"></a>VM3
* Chamada **myVM3**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Anexada ao adaptador de rede **myNicVM3**.
* Imagem da máquina virtual **win2019datacenter**.
* Na **Zona 3**.

```azurecli-interactive
   az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM3 \
    --nics myNicVM3 \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone 3 \
    --no-wait
```
Pode levar alguns minutos para que as VMs sejam implantadas.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-a-public-ip-address---standard"></a>Criar um endereço IP público – Standard

Para acessar seu aplicativo Web na Internet, você precisará de um endereço IP público para o balanceador de carga. 

Use [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) para:

* Criar um endereço IP público com redundância de zona padrão chamado **myPublicIP**.
* Em **CreatePubLBQS-rg**.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Standard
```

Para criar um endereço IP público com redundância zonal na Zona 1:

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Standard \
    --zone 1
```

## <a name="create-standard-load-balancer"></a>Criar Standard Load Balancer

Esta seção fornece detalhes sobre como criar e configurar os componentes do balanceador de carga abaixo:

  * Um pool de IPs de front-end que recebe o tráfego de rede de entrada no balanceador de carga.
  * Um pool de IPs de back-end ao qual o pool de front-end envia o tráfego de rede com a carga balanceada.
  * Uma investigação de integridade que determina a integridade das instâncias de VM de back-end.
  * Uma regra de balanceador de carga que define como o tráfego é distribuído para as VMs.

### <a name="create-the-load-balancer-resource"></a>Criar o recurso do balanceador de carga

Crie um balanceador de carga público com [az network lb create](/cli/azure/network/lb#az_network_lb_create):

* Chamado **myLoadBalancer**.
* Um pool de front-end chamado **myFrontEnd**.
* Um pool de back-end chamado **myBackEndPool**.
* Associado ao endereço IP público **myPublicIP** criado na etapa anterior. 

```azurecli-interactive
  az network lb create \
    --resource-group CreatePubLBQS-rg \
    --name myLoadBalancer \
    --sku Standard \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Criar a investigação de integridade

Uma investigação de integridade verifica todas as instâncias da máquina virtual para garantir que elas possam enviar tráfego de rede. 

Uma máquina virtual com uma verificação de investigação com falha é removida do balanceador de carga. A máquina virtual será adicionada novamente ao balanceador de carga quando a falha for resolvida.

Crie uma investigação de integridade com [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create):

* Monitora a integridade das máquinas virtuais.
* Chamado **myHealthProbe**.
* Protocolo **TCP**.
* Monitorando a **Porta 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePubLBQS-rg \
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

Crie uma regra de balanceador de carga com [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create):

* Chamada **myHTTPRule**
* Escutando na **Porta 80** no pool de front-end **myFrontEnd**.
* Enviando tráfego de rede com balanceamento de carga para o pool de endereços de back-end **myBackEndPool** usando a **Porta 80**. 
* Usando a investigação de integridade **myHealthProbe**.
* Protocolo **TCP**.
* Tempo limite de ociosidade de **15 minutos**.
* Habilitar redefinição de TCP.


```azurecli-interactive
  az network lb rule create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --disable-outbound-snat true \
    --idle-timeout 15 \
    --enable-tcp-reset true

```
### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Adicionar máquinas virtuais ao pool de back-end do balanceador de carga

Adicione as máquinas virtuais ao pool de back-end com [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add):

* No pool de endereços de back-end **myBackEndPool**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Associada ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic ip-config address-pool add \
     --address-pool myBackendPool \
     --ip-config-name ipconfig1 \
     --nic-name $vmnic \
     --resource-group CreatePubLBQS-rg \
     --lb-name myLoadBalancer
  done
```

## <a name="create-outbound-rule-configuration"></a>Criar configurações de regra de saída
As regras de saída do balanceador de carga configuram o SNAT de saída para as VMs no pool de back-end. 

Para saber mais sobre as conexões de saída, confira [Conexões de saída no Azure](load-balancer-outbound-connections.md).

Um IP público ou um prefixo pode ser usado para a configuração de saída.

### <a name="public-ip"></a>IP público

Use [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) para criar um IP para a conectividade de saída.  

* Chamado **myPublicIPOutbound**.
* Em **CreatePubLBQS-rg**.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPOutbound \
    --sku Standard
```

Para criar um endereço IP público com redundância zonal na Zona 1:

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPOutbound \
    --sku Standard \
    --zone 1
```

### <a name="public-ip-prefix"></a>Prefixo IP público

Use [az network public-ip prefix create](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_create) para criar um prefixo de IP público para a conectividade de saída.

* Chamado **myPublicIPPrefixOutbound**.
* Em **CreatePubLBQS-rg**.
* Comprimento do prefixo de **28**.

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPPrefixOutbound \
    --length 28
```
Para criar um prefixo de IP público com redundância zonal na Zona 1:

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPPrefixOutbound \
    --length 28 \
    --zone 1
```

Para saber mais sobre como dimensionar o NAT de saída e a conectividade de saída, confira [Dimensionar o NAT de saída com vários endereços IP](load-balancer-outbound-connections.md).

### <a name="create-outbound-frontend-ip-configuration"></a>Criar configuração de IP de front-end de saída

Crie uma configuração de IP de front-end com [az network lb frontend-ip create ](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_create):

Selecione os comandos do IP público ou do prefixo de IP público com base na decisão na etapa anterior.

#### <a name="public-ip"></a>IP público

* Chamada **myFrontEndOutbound**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Associada ao endereço IP público **myPublicIPOutbound**.
* Associada ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-address myPublicIPOutbound 
```

#### <a name="public-ip-prefix"></a>Prefixo de IP público

* Chamada **myFrontEndOutbound**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Associado ao prefixo do IP público **myPublicIPPrefixOutbound**.
* Associada ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-prefix myPublicIPPrefixOutbound 
```

### <a name="create-outbound-pool"></a>Criar pool de saída

Crie um pool de saída com [az network lb address-pool create](/cli/azure/network/lb/address-pool#az_network_lb_address_pool_create):

* Chamado **myBackEndPoolOutbound**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Associada ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  az network lb address-pool create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myBackendPoolOutbound
```
### <a name="create-outbound-rule"></a>Criar uma regra de saída

Crie uma regra de saída para o pool de back-end de saída com [az network lb outbound-rule create](/cli/azure/network/lb/outbound-rule#az_network_lb_outbound_rule_create):

* Chamada **myOutboundRule**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Associada ao balanceador de carga **myLoadBalancer**
* Associada ao front-end **myFrontEndOutbound**.
* Protocolo **Todos**.
* Tempo limite de ociosidade de **15**.
* **10000** portas de saída.
* Associada ao pool de back-end **myBackEndPoolOutbound**.

```azurecli-interactive
  az network lb outbound-rule create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myOutboundRule \
    --frontend-ip-configs myFrontEndOutbound \
    --protocol All \
    --idle-timeout 15 \
    --outbound-ports 10000 \
    --address-pool myBackEndPoolOutbound
```
### <a name="add-virtual-machines-to-outbound-pool"></a>Adicionar máquinas virtuais a um pool de saída

Adicione as máquinas virtuais ao pool de saída com [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add):


* No pool de endereços de back-end **myBackEndPoolOutbound**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Associada ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic ip-config address-pool add \
     --address-pool myBackendPoolOutbound \
     --ip-config-name ipconfig1 \
     --nic-name $vmnic \
     --resource-group CreatePubLBQS-rg \
     --lb-name myLoadBalancer
  done
```

# <a name="basic-sku"></a>[**SKU Básico**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>O balanceador de carga de SKU Standard é recomendado para cargas de trabalho de produção. Para obter mais informações sobre SKUs, confira **[SKUs do Azure Load Balancer](skus.md)** .

:::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/resources-diagram-basic.png" alt-text="Recursos do balanceador de carga básico criados no guia de início rápido." border="false":::

## <a name="configure-virtual-network---basic"></a>Configurar a rede virtual – Básico

Antes de implantar VMs e testar o balanceador de carga, crie os recursos da rede virtual de suporte.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Crie uma rede virtual usando [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create):

* Chamada **myVNet**.
* Prefixo de endereço **10.1.0.0/16**.
* Sub-rede chamada **myBackendSubnet**.
* Prefixo de sub-rede **10.1.0.0/24**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Localização de **eastus**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePubLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-public-ip-address"></a>Criar um endereço IP público

Use [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) para criar um endereço IP público para o bastion host:

* Crie um endereço IP público com redundância de zona padrão chamado **myBastionIP**.
* Em **CreatePubLBQS-rg**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>Criar uma sub-rede do bastion

Use o comando [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) para criar uma sub-rede do bastion:

* Chamada **AzureBastionSubnet**.
* Prefixo de endereço **10.1.1.0/24**.
* Na rede virtual **myVNet**.
* No grupo de recursos **CreateIntLBQS-rg**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePubLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>Criar um bastion host

Use [az network bastion create](/cli/azure/network/bastion#az_network_bastion_create) para criar um bastion host:

* chamado **myBastionHost**.
* Em **CreatePubLBQS-rg**.
* Associado ao IP público **myBastionIP**.
* Associado à rede virtual **myVNet**.
* Na localização **eastus**.

```azurecli-interactive
az network bastion create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Levará alguns minutos para que o host do Azure Bastion seja implantado.

### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Para um Standard Load Balancer, as VMs no endereço de back-end precisam ter adaptadores de rede que pertençam a um grupo de segurança de rede. 

Crie um grupo de segurança de rede usando [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create):

* Chamado **myNSG**.
* No grupo de recursos **CreateIntLBQS-rg**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreatePubLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Criar uma regra de grupo de segurança de rede

Crie uma regra de grupo de segurança de rede usando [az network nsg create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create):

* Chamada **myNSGRuleHTTP**.
* No grupo de segurança de rede criado na etapa anterior, **myNSG**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Protocolo **(*)** .
* Direção de **Entrada**.
* Origem **(*)** .
* Destino **(*)** .
* Porta de destino **Porta 80**.
* Acesso **Permitir**.
* Prioridade **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreatePubLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

## <a name="create-backend-servers---basic"></a>Criar servidores back-end – Básico

Nesta seção, você cria:

* Três adaptadores de rede para as máquinas virtuais.
* Conjunto de disponibilidade para as máquinas virtuais
* Três máquinas virtuais a serem usadas como servidores de back-end para o balanceador de carga.


### <a name="create-network-interfaces-for-the-virtual-machines"></a>Criar interfaces de rede para as máquinas virtuais

Crie três interfaces de rede com [az network nic create](/cli/azure/network/nic#az_network_nic_create):


* Chamados **myNicVM1**, **myNicVM2** e **myNicVM3**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Na rede virtual **myVNet**.
* Na sub-rede **myBackendSubnet**.
* No grupo de segurança de rede **myNSG**.

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreatePubLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```
### <a name="create-availability-set-for-virtual-machines"></a>Criar conjunto de disponibilidade para máquinas virtuais

Crie a conjunto de disponibilidade com [az vm availability-set create](/cli/azure/vm/availability-set#az_vm_availability_set_create):

* Chamado **myAvSet**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Localização **eastus**.

```azurecli-interactive
  az vm availability-set create \
    --name myAvSet \
    --resource-group CreatePubLBQS-rg \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie as máquinas virtuais com [az vm create](/cli/azure/vm#az_vm_create):

### <a name="vm1"></a>VM1
* Chamada **myVM1**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Anexada ao adaptador de rede **myNicVM1**.
* Imagem da máquina virtual **win2019datacenter**.
* Na **Zona 1**.

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvSet \
    --no-wait
```
#### <a name="vm2"></a>VM2
* Chamada **myVM2**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Anexada ao adaptador de rede **myNicVM2**.
* Imagem da máquina virtual **win2019datacenter**.
* Na **Zona 2**.

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvSet \
    --no-wait
```

#### <a name="vm3"></a>VM3
* Chamada **myVM3**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Anexada ao adaptador de rede **myNicVM3**.
* Imagem da máquina virtual **win2019datacenter**.
* Na **Zona 3**.

```azurecli-interactive
   az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM3 \
    --nics myNicVM3 \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvSet \
    --no-wait
```
Pode levar alguns minutos para que as VMs sejam implantadas.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-a-public-ip-address---basic"></a>Criar um endereço IP público – Básico

Para acessar seu aplicativo Web na Internet, você precisará de um endereço IP público para o balanceador de carga. 

Use [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) para:

* Criar um endereço IP público com redundância de zona padrão chamado **myPublicIP**.
* Em **CreatePubLBQS-rg**.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Basic
```

## <a name="create-basic-load-balancer"></a>Criar o balanceador de carga básico

Esta seção fornece detalhes sobre como criar e configurar os componentes do balanceador de carga abaixo:

  * Um pool de IPs de front-end que recebe o tráfego de rede de entrada no balanceador de carga.
  * Um pool de IPs de back-end ao qual o pool de front-end envia o tráfego de rede com a carga balanceada.
  * Uma investigação de integridade que determina a integridade das instâncias de VM de back-end.
  * Uma regra de balanceador de carga que define como o tráfego é distribuído para as VMs.

### <a name="create-the-load-balancer-resource"></a>Criar o recurso do balanceador de carga

Crie um balanceador de carga público com [az network lb create](/cli/azure/network/lb#az_network_lb_create):

* Chamado **myLoadBalancer**.
* Um pool de front-end chamado **myFrontEnd**.
* Um pool de back-end chamado **myBackEndPool**.
* Associado ao endereço IP público **myPublicIP** criado na etapa anterior. 

```azurecli-interactive
  az network lb create \
    --resource-group CreatePubLBQS-rg \
    --name myLoadBalancer \
    --sku Basic \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Criar a investigação de integridade

Uma investigação de integridade verifica todas as instâncias da máquina virtual para garantir que elas possam enviar tráfego de rede. 

Uma máquina virtual com uma verificação de investigação com falha é removida do balanceador de carga. A máquina virtual será adicionada novamente ao balanceador de carga quando a falha for resolvida.

Crie uma investigação de integridade com [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create):

* Monitora a integridade das máquinas virtuais.
* Chamado **myHealthProbe**.
* Protocolo **TCP**.
* Monitorando a **Porta 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePubLBQS-rg \
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

Crie uma regra de balanceador de carga com [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create):

* Chamada **myHTTPRule**
* Escutando na **Porta 80** no pool de front-end **myFrontEnd**.
* Enviando tráfego de rede com balanceamento de carga para o pool de endereços de back-end **myBackEndPool** usando a **Porta 80**. 
* Usando a investigação de integridade **myHealthProbe**.
* Protocolo **TCP**.
* Tempo limite de ociosidade de **15 minutos**.

```azurecli-interactive
  az network lb rule create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15
```

### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Adicionar máquinas virtuais ao pool de back-end do balanceador de carga

Adicione as máquinas virtuais ao pool de back-end com [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add):

* No pool de endereços de back-end **myBackEndPool**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Associada ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic ip-config address-pool add \
     --address-pool myBackendPool \
     --ip-config-name ipconfig1 \
     --nic-name $vmnic \
     --resource-group CreatePubLBQS-rg \
     --lb-name myLoadBalancer
  done
```

---

## <a name="install-iis"></a>Instalar o IIS

Use [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set) para instalar o IIS nas máquinas virtuais e definir o site padrão como o nome do computador.

```azurecli-interactive
  array=(myVM1 myVM2 myVM3)
    for vm in "${array[@]}"
    do
     az vm extension set \
       --publisher Microsoft.Compute \
       --version 1.8 \
       --name CustomScriptExtension \
       --vm-name $vm \
       --resource-group CreatePubLBQS-rg \
       --settings '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
  done

```

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

Para obter o endereço IP público do balanceador de carga, use [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). 

Copie o endereço IP público e cole-o na barra de endereços do seu navegador.

```azurecli-interactive
  az network public-ip show \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --query ipAddress \
    --output tsv
```
:::image type="content" source="./media/load-balancer-standard-public-cli/running-nodejs-app.png" alt-text="Testar o balanceador de carga" border="true":::

## <a name="clean-up-resources"></a>Limpar os recursos

Use o comando [az group delete](/cli/azure/group#az_group_delete) para remover o grupo de recursos, o balanceador de carga e todos os recursos relacionados quando deixarem de ser necessários.

```azurecli-interactive
  az group delete \
    --name CreatePubLBQS-rg
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido

* Você criou um balanceador de carga público padrão ou público
* Anexou máquinas virtuais. 
* Configurou a regra de tráfego e a investigação de integridade do balanceador de carga.
* Testou o balanceador de carga.

Para saber mais sobre o Azure Load Balancer, vá para:
> [!div class="nextstepaction"]
> [O que é o Azure Load Balancer?](load-balancer-overview.md)