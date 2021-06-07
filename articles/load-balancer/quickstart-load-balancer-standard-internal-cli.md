---
title: 'Início Rápido: Criar um balanceador de carga interno – CLI do Azure'
titleSuffix: Azure Load Balancer
description: Este início rápido mostra como criar um balanceador de carga interno usando a CLI do Azure.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/19/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 2db30024b26352bcc038d606bcdc760b6350d413
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788827"
---
# <a name="quickstart-create-an-internal-load-balancer-by-using-the-azure-cli"></a>Início Rápido: Criar um balanceador de carga interno usando a CLI do Azure

Veja uma introdução ao Azure Load Balancer usando a CLI do Azure para criar um balanceador de carga interno e três máquinas virtuais.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

Este guia de início rápido requer a versão 2.0.28 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a última versão já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contêiner lógico no qual você implanta e gerencia os recursos do Azure.

Crie um grupo de recursos com [az group create](/cli/azure/group#az_group_create). Dê ao grupo de recursos o nome **CreateIntLBQS-rg** e especifique o local como **eastus**.

```azurecli-interactive
  az group create \
    --name CreateIntLBQS-rg \
    --location eastus

```

---
# <a name="standard-sku"></a>[**SKU Standard**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>O balanceador de carga de SKU Standard é recomendado para cargas de trabalho de produção. Para obter mais informações sobre SKUs, confira **[SKUs do Azure Load Balancer](skus.md)** .

Nesta seção, você criará um balanceador de carga que faz o balanceamento da carga de máquinas virtuais. Quando você cria um balanceador de carga interno, uma rede virtual é configurada como a rede para o balanceador de carga. O seguinte diagrama mostra os recursos criados neste guia de início rápido:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal.png" alt-text="Recursos do Standard Load Balancer criados para o guia de início rápido." border="false":::

### <a name="configure-the-virtual-network"></a>Configurar a rede virtual

Antes de implantar as VMs e o balanceador de carga, crie os recursos da rede virtual de suporte.

#### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Crie uma rede virtual usando [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). Especifique o seguinte:

* Chamada **myVNet**
* Prefixo de endereço **10.1.0.0/16**
* Sub-rede chamada **myBackendSubnet**
* Prefixo de sub-rede **10.1.0.0/24**
* No grupo de recursos **CreateIntLBQS-rg**
* Localização de **eastus**

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

#### <a name="create-a-public-ip-address"></a>Criar um endereço IP público

Use [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) para criar um endereço IP público para o Azure Bastion host. Especifique o seguinte:

* Crie um endereço IP público com redundância de zona padrão chamado **myBastionIP**
* Em **CreateIntLBQS-rg**

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionIP \
    --sku Standard
```
#### <a name="create-an-azure-bastion-subnet"></a>Criar uma sub-rede do Azure Bastion

Use [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) para criar uma sub-rede. Especifique o seguinte:

* Chamada **AzureBastionSubnet**
* Prefixo de endereço **10.1.1.0/24**
* Na rede virtual **myVNet**
* No grupo de recursos **CreateIntLBQS-rg**

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg  \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

#### <a name="create-an-azure-bastion-host"></a>Criar um Azure Bastion host

Use [az network bastion create](/cli/azure/network/bastion#az_network_bastion_create) para criar um host. Especifique o seguinte:

* Chamado **myBastionHost**
* Em **CreateIntLBQS-rg**
* Associado ao IP público **myBastionIP**
* Associado à rede virtual **myVNet**
* Na localização **eastus**

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Levará alguns minutos para que o host do Azure Bastion seja implantado.

#### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Para um balanceador de carga padrão, certifique-se de que as VMs tenham adaptadores de rede que pertençam a um grupo de segurança de rede. Crie um grupo de segurança de rede usando [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create). Especifique o seguinte:

* Chamado **myNSG**
* No grupo de recursos **CreateIntLBQS-rg**

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

#### <a name="create-a-network-security-group-rule"></a>Criar uma regra de grupo de segurança de rede

Crie uma regra de grupo de segurança de rede usando [az network nsg create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create). Especifique o seguinte:

* Chamada **myNSGRuleHTTP**
* No grupo de segurança de rede criado na etapa anterior, **myNSG**
* No grupo de recursos **CreateIntLBQS-rg**
* Protocolo **(*)**
* Direção de **Entrada**
* Origem **(*)**
* Destino **(*)**
* Porta de destino **Porta 80**
* Acesso **Permitir**
* Prioridade **200**

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
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

### <a name="create-back-end-servers"></a>Criar servidores back-end

Nesta seção, você cria:

* Três adaptadores de rede para as máquinas virtuais.
* Três máquinas virtuais a serem usadas como servidores para o balanceador de carga.

#### <a name="create-network-interfaces-for-the-virtual-machines"></a>Criar interfaces de rede para as máquinas virtuais

Crie três interfaces de rede com [az network nic create](/cli/azure/network/nic#az_network_nic_create). Especifique o seguinte:

* Chamados **myNicVM1**, **myNicVM2** e **myNicVM3**
* No grupo de recursos **CreateIntLBQS-rg**
* Na rede virtual **myVNet**
* Na sub-rede **myBackendSubnet**
* No grupo de segurança de rede **myNSG**

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreateIntLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

#### <a name="create-the-virtual-machines"></a>Criar as máquinas virtuais

Crie as máquinas virtuais com [az vm create](/cli/azure/vm#az_vm_create). Especifique o seguinte:

* Chamada **myVM1**, **myVM2** e **myVM3**
* No grupo de recursos **CreateIntLBQS-rg**
* Anexada ao adaptador de rede **myNicVM1**, **myNicVM2** e **myNicVM3**
* Imagem da máquina virtual **win2019datacenter**
* Em **Zona 1**, **Zona 2** e **Zona 3**

```azurecli-interactive
  array=(1 2 3)
  for n in "${array[@]}"
  do
    az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM$n \
    --nics myNicVM$n \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone $n \
    --no-wait
  done
```

Pode levar alguns minutos para que as VMs sejam implantadas.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]


### <a name="create-the-load-balancer"></a>Criar o balanceador de carga

Esta seção fornece detalhes sobre como criar e configurar os componentes do balanceador de carga abaixo:

* Um pool de IPs que recebe o tráfego de rede de entrada no balanceador de carga.
* Um segundo pool de IPs para o qual o pool de front-end envia o tráfego de rede com a carga balanceada.
* Uma investigação de integridade que determina a integridade das instâncias de VM.
* Uma regra de balanceador de carga que define como o tráfego é distribuído para as VMs.

#### <a name="create-the-load-balancer-resource"></a>Criar o recurso do balanceador de carga

Crie um balanceador de carga público com [az network lb create](/cli/azure/network/lb#az_network_lb_create). Especifique o seguinte:

* Chamado **myLoadBalancer**
* Um pool chamado **myFrontEnd**
* Um pool chamado **myBackEndPool**
* Associado à rede virtual **myVNet**
* Associado à sub-rede **myBackendSubnet**

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Standard \
    --vnet-name myVnet \
    --subnet myBackendSubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool
```

#### <a name="create-the-health-probe"></a>Criar a investigação de integridade

Uma investigação de integridade verifica todas as instâncias da máquina virtual para garantir que elas possam enviar tráfego de rede. Uma máquina virtual com uma verificação de investigação com falha é removida do balanceador de carga. A máquina virtual será adicionada novamente ao balanceador de carga quando a falha for resolvida.

Crie uma investigação de integridade com [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create). Especifique o seguinte:

* Monitora a integridade das máquinas virtuais
* Chamado **myHealthProbe**
* Protocolo **TCP**
* Monitorando a **Porta 80**

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

#### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

Uma regra de balanceador de carga define:

* A configuração do IP para o tráfego de entrada.
* O pool de IPs para receber o tráfego.
* As portas de origem e de destino necessárias. 

Crie uma regra de balanceador de carga com [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create). Especifique o seguinte:

* Chamada **myHTTPRule**
* Escutando na **Porta 80** no pool **myFrontEnd**
* Enviando tráfego de rede com balanceamento de carga para o pool de endereços **myBackEndPool** usando a **Porta 80** 
* Usando a investigação de integridade **myHealthProbe**
* Protocolo **TCP**
* Tempo limite ocioso de **15 minutos**
* Habilitar redefinição de TCP

```azurecli-interactive
  az network lb rule create \
    --resource-group CreateIntLBQS-rg \
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

#### <a name="add-vms-to-the-load-balancer-pool"></a>Adicionar VMs ao pool do balanceador de carga

Adicione as máquinas virtuais ao pool de back-end com [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add). Especifique o seguinte:

* No pool de endereços **myBackEndPool**
* No grupo de recursos **CreateIntLBQS-rg**
* Associada ao adaptador de rede **myNicVM1**, **myNicVM2** e **myNicVM3**
* Associada ao balanceador de carga **myLoadBalancer**

```azurecli-interactive
  array=(VM1 VM2 VM3)
  for vm in "${array[@]}"
  do
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNic$vm \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
  done

```

# <a name="basic-sku"></a>[**SKU Básico**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>O balanceador de carga de SKU Standard é recomendado para cargas de trabalho de produção. Para obter mais informações sobre SKUs, confira **[SKUs do Azure Load Balancer](skus.md)** .

Nesta seção, você criará um balanceador de carga que faz o balanceamento da carga de máquinas virtuais. Quando você cria um balanceador de carga interno, uma rede virtual é configurada como a rede para o balanceador de carga. O seguinte diagrama mostra os recursos criados neste guia de início rápido:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal-basic.png" alt-text="Recursos do balanceador de carga básico criados para o guia de início rápido." border="false":::

### <a name="configure-the-virtual-network"></a>Configurar a rede virtual

Antes de implantar as VMs e o balanceador de carga, crie os recursos da rede virtual de suporte.

#### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Crie uma rede virtual usando [az network vnet create](/cli/azure/network/vnet#az_network_vnet_createt). Especifique o seguinte:

* Chamada **myVNet**
* Prefixo de endereço **10.1.0.0/16**
* Sub-rede chamada **myBackendSubnet**
* Prefixo de sub-rede **10.1.0.0/24**
* No grupo de recursos **CreateIntLBQS-rg**
* Localização de **eastus**

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

#### <a name="create-a-public-ip-address"></a>Criar um endereço IP público

Use [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) para criar um endereço IP público para o Azure Bastion host. Especifique o seguinte:

* Crie um endereço IP público com redundância de zona padrão chamado **myBastionIP**
* Em **CreateIntLBQS-rg**

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
#### <a name="create-an-azure-bastion-subnet"></a>Criar uma sub-rede do Azure Bastion

Use [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) para criar uma sub-rede. Especifique o seguinte:

* Chamada **AzureBastionSubnet**
* Prefixo de endereço **10.1.1.0/24**
* Na rede virtual **myVNet**
* No grupo de recursos **CreateIntLBQS-rg**

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

#### <a name="create-an-azure-bastion-host"></a>Criar um Azure Bastion host

Use [az network bastion create](/cli/azure/network/bastion#az_network_bastion_create) para criar um host. Especifique o seguinte:

* Chamado **myBastionHost**
* Em **CreateIntLBQS-rg**
* Associado ao IP público **myBastionIP**
* Associado à rede virtual **myVNet**
* Na localização **eastus**

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Levará alguns minutos para que o host do Azure Bastion seja implantado.

#### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Para um balanceador de carga padrão, certifique-se de que as VMs tenham adaptadores de rede que pertençam a um grupo de segurança de rede. Crie um grupo de segurança de rede usando [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create). Especifique o seguinte:

* Chamado **myNSG**
* No grupo de recursos **CreateIntLBQS-rg**

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

#### <a name="create-a-network-security-group-rule"></a>Criar uma regra de grupo de segurança de rede

Crie uma regra de grupo de segurança de rede usando [az network nsg create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create). Especifique o seguinte:

* Chamada **myNSGRuleHTTP**
* No grupo de segurança de rede criado na etapa anterior, **myNSG**
* No grupo de recursos **CreateIntLBQS-rg**
* Protocolo **(*)**
* Direção de **Entrada**
* Origem **(*)**
* Destino **(*)**
* Porta de destino **Porta 80**
* Acesso **Permitir**
* Prioridade **200**

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
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

### <a name="create-back-end-servers"></a>Criar servidores back-end

Nesta seção, você cria:

* Três adaptadores de rede para as máquinas virtuais.
* O conjunto de disponibilidade para as máquinas virtuais.
* Três máquinas virtuais a serem usadas como servidores para o balanceador de carga.

#### <a name="create-network-interfaces-for-the-virtual-machines"></a>Criar interfaces de rede para as máquinas virtuais

Crie três interfaces de rede com [az network nic create](/cli/azure/network/nic#az_network_nic_create). Especifique o seguinte:

* Chamados **myNicVM1**, **myNicVM2** e **myNicVM3**
* No grupo de recursos **CreateIntLBQS-rg**
* Na rede virtual **myVNet**
* Na sub-rede **myBackendSubnet**
* No grupo de segurança de rede **myNSG**

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreateIntLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

#### <a name="create-the-availability-set-for-the-virtual-machines"></a>Criar o conjunto de disponibilidade para as máquinas virtuais

Crie a conjunto de disponibilidade com [az vm availability-set create](/cli/azure/vm/availability-set#az_vm_availability_set_create). Especifique o seguinte:

* Chamada **myAvailabilitySet**
* No grupo de recursos **CreateIntLBQS-rg**
* Localização **eastus**

```azurecli-interactive
  az vm availability-set create \
    --name myAvailabilitySet \
    --resource-group CreateIntLBQS-rg \
    --location eastus 
    
```

#### <a name="create-the-virtual-machines"></a>Criar as máquinas virtuais

Crie as máquinas virtuais com [az vm create](/cli/azure/vm#az_vm_create). Especifique o seguinte:

* Chamada **myVM1**, **myVM2** e **myVM3**
* No grupo de recursos **CreateIntLBQS-rg**
* Anexada ao adaptador de rede **myNicVM1**, **myNicVM2** e **myNicVM3**
* Imagem da máquina virtual **win2019datacenter**
* Em **myAvailabilitySet**


```azurecli-interactive
  array=(1 2 3)
  for n in "${array[@]}"
  do
    az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM$n \
    --nics myNicVM$n \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvailabilitySet \
    --no-wait
  done
```
Pode levar alguns minutos para que as VMs sejam implantadas.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]


### <a name="create-the-load-balancer"></a>Criar o balanceador de carga

Esta seção fornece detalhes sobre como criar e configurar os componentes do balanceador de carga abaixo:

* Um pool de IPs que recebe o tráfego de rede de entrada no balanceador de carga.
* Um segundo pool de IPs para o qual o pool de front-end envia o tráfego de rede com a carga balanceada.
* Uma investigação de integridade que determina a integridade das instâncias de VM.
* Uma regra de balanceador de carga que define como o tráfego é distribuído para as VMs.

#### <a name="create-the-load-balancer-resource"></a>Criar o recurso do balanceador de carga

Crie um balanceador de carga público com [az network lb create](/cli/azure/network/lb#az_network_lb_create). Especifique o seguinte:

* Chamado **myLoadBalancer**
* Um pool chamado **myFrontEnd**
* Um pool chamado **myBackEndPool**
* Associado à rede virtual **myVNet**
* Associado à sub-rede **myBackendSubnet**

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Basic \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool
```

#### <a name="create-the-health-probe"></a>Criar a investigação de integridade

Uma investigação de integridade verifica todas as instâncias da máquina virtual para garantir que elas possam enviar tráfego de rede. Uma máquina virtual com uma verificação de investigação com falha é removida do balanceador de carga. A máquina virtual será adicionada novamente ao balanceador de carga quando a falha for resolvida.

Crie uma investigação de integridade com [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create). Especifique o seguinte:

* Monitora a integridade das máquinas virtuais
* Chamado **myHealthProbe**
* Protocolo **TCP**
* Monitorando a **Porta 80**

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

#### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

Uma regra de balanceador de carga define:

* A configuração do IP para o tráfego de entrada.
* O pool de IPs para receber o tráfego.
* As portas de origem e de destino necessárias. 

Crie uma regra de balanceador de carga com [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create). Especifique o seguinte:

* Chamada **myHTTPRule**
* Escutando na **Porta 80** no pool **myFrontEnd**
* Enviando tráfego de rede com balanceamento de carga para o pool de endereços **myBackEndPool** usando a **Porta 80** 
* Usando a investigação de integridade **myHealthProbe**
* Protocolo **TCP**
* Tempo limite ocioso de **15 minutos**

```azurecli-interactive
  az network lb rule create \
    --resource-group CreateIntLBQS-rg \
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
#### <a name="add-vms-to-the-load-balancer-pool"></a>Adicionar VMs ao pool do balanceador de carga

Adicione as máquinas virtuais ao pool de back-end com [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add). Especifique o seguinte:

* No pool de endereços **myBackEndPool**
* No grupo de recursos **CreateIntLBQS-rg**
* Associada ao adaptador de rede **myNicVM1**, **myNicVM2** e **myNicVM3**
* Associada ao balanceador de carga **myLoadBalancer**

```azurecli-interactive
  array=(VM1 VM2 VM3)
  for vm in "${array[@]}"
  do
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNic$vm \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
  done

```
---
## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

Crie os adaptadores de rede com [az network nic create](/cli/azure/network/nic#az_network_nic_create). Especifique o seguinte:

* Chamado **myNicTestVM**
* No grupo de recursos **CreateIntLBQS-rg**
* Na rede virtual **myVNet**
* Na sub-rede **myBackendSubnet**
* No grupo de segurança de rede **myNSG**

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicTestVM \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
Crie a máquina virtual com [az vm create](/cli/azure/vm#az_vm_create). Especifique o seguinte:

* Chamada **myTestVM**
* No grupo de recursos **CreateIntLBQS-rg**
* Anexada ao adaptador de rede **myNicTestVM**
* Imagem da máquina virtual **Win2019Datacenter**

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myTestVM \
    --nics myNicTestVM \
    --image Win2019Datacenter \
    --admin-username azureuser \
    --no-wait
```
Talvez seja necessário esperar alguns minutos para que a máquina virtual seja implantada.

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
       --resource-group CreateIntLBQS-rg \
       --settings '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
  done

```

### <a name="test"></a>Teste

1. [Entre](https://portal.azure.com) no portal do Azure.

2. Na página de **Visão geral**, encontre o endereço IP privado para o balanceador de carga. No menu à esquerda, selecione **Todos os serviços** > **Todos os recursos** > **myLoadBalancer**.

3. Na visão geral de **myLoadBalancer**, copie o endereço ao lado de **Endereço IP Privado**.

4. No menu à esquerda, selecione **Todos os serviços** > **Todos os recursos**. Na lista de recursos, no grupo de recursos **CreateIntLBQS-rg**, selecione **myTestVM**.

5. Na página **Visão Geral**, selecione **Conectar** > **Bastion**.

6. Insira o nome de usuário e a senha que você inseriu quando criou a VM.

7. Em **myTestVM**, abra o **Internet Explorer**.

8. Insira o endereço IP da etapa anterior na barra de endereços do navegador. A página padrão do servidor Web do IIS é mostrada no navegador.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Captura de tela do endereço IP na barra de endereços do navegador." border="true":::
   
Para ver o balanceador de carga distribuir o tráfego entre as três VMs, personalize a página padrão do servidor Web do IIS de cada VM. Em seguida, atualize manualmente o navegador da Web do computador cliente.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando os recursos deixarem de ser necessários, use o comando [az group delete](/cli/azure/group#az_group_delete) para remover o grupo de recursos, o balanceador de carga e todos os recursos relacionados.

```azurecli-interactive
  az group delete \
    --name CreateIntLBQS-rg
```

## <a name="next-steps"></a>Próximas etapas

 Obtenha uma visão geral  do Balanceador de carga do Azure.
> [!div class="nextstepaction"]
> [O que é o Azure Load Balancer?](load-balancer-overview.md)
