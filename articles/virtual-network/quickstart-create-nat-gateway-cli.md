---
title: Criar um gateway da NAT – CLI do Azure
titlesuffix: Azure Virtual Network NAT
description: O início rápido mostra como criar um gateway da NAT usando a CLI do Azure
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8d14b8b83fd784956091e738a38d6851d5edacd9
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98927146"
---
# <a name="create-a-nat-gateway-using-azure-cli"></a>Criar um gateway da NAT usando a CLI do Azure

Este tutorial mostra como usar o serviço NAT da Rede Virtual do Azure. Você criará um gateway da NAT para fornecer conectividade de saída para uma máquina virtual no Azure. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo exige a versão 2.0.71 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

O seguinte exemplo cria um grupo de recursos chamado **myResourceGroupNAT** na localização **eastus2**:

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>Criar o Gateway da NAT

### <a name="create-a-public-ip-address"></a>Criar um endereço IP público

Para acessar a Internet pública, você precisa de um ou mais endereços IP públicos para o gateway da NAT. Use [az network public-ip create](/cli/azure/network/public-ip) para criar um recurso de endereço IP público chamado **myPublicIP** em **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard
```

### <a name="create-a-public-ip-prefix"></a>Criar um prefixo IP público

Você pode usar um ou mais recursos de endereço IP público, prefixos IP público ou ambos com o gateway da NAT. Adicionaremos um recurso de prefixo IP público a esse cenário para demonstrar.   Use [az network public-ip prefix create](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create) para criar um recurso de prefixo IP público chamado **myPublicIPprefix** em **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPprefix \
    --length 31
```

### <a name="create-a-nat-gateway-resource"></a>Criar um recurso de gateway da NAT

Esta seção fornece detalhes sobre como criar e configurar os seguintes componentes do serviço NAT usando o recurso de gateway da NAT:
  - Um pool de IPs públicos e o prefixo IP público a serem usados para fluxos de saída convertidos pelo recurso de Gateway da NAT.
  - Altere o tempo limite de ociosidade do padrão de 4 minutos para 10 minutos.

Crie um gateway da NAT global do Azure com [az network nat gateway create](/cli/azure/network/nat) chamado **myNATgateway**. O comando usa o endereço IP público **myPublicIP** e o prefixo IP público **myPublicIPprefix**. O comando altera o tempo limite de ociosidade para **10** minutos.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --public-ip-prefixes myPublicIPprefix \
    --idle-timeout 10       
  ```

Neste momento, o gateway da NAT está funcional e tudo o que está faltando é configurar quais sub-redes de uma rede virtual devem usá-lo.

## <a name="configure-virtual-network"></a>Configurar rede virtual

Antes de implantar uma VM e poder usar o gateway da NAT, precisamos criar a rede virtual.

Crie uma rede virtual chamada **myVnet** com uma sub-rede nomeada **mySubnet** no **myResourceGroupNAT** usando [az network vnet create](/cli/azure/network/vnet).  O espaço de endereços IP da rede virtual é **192.168.0.0/16**. A sub-rede dentro da rede virtual é **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.0.0/24
```

### <a name="configure-nat-service-for-source-subnet"></a>Configurar o serviço NAT da sub-rede de origem

Configuraremos a sub-rede de origem **mySubnet** na rede virtual **myVnet** para usar um recurso de gateway da NAT específico **myNATgateway** com [az network vnet subnet update](/cli/azure/network/vnet/subnet).  Esse comando ativará o serviço NAT na sub-rede especificada.

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

Todo o tráfego de saída para os destinos da Internet agora está usando o gateway da NAT.  Não é necessário configurar uma UDR.

## <a name="create-a-vm-to-use-the-nat-service"></a>Criar uma VM para usar o serviço NAT

Agora criaremos uma VM para usar o serviço NAT.  Essa VM tem um IP público para ser usado como um IP público em nível de instância para que você possa acessar a VM.  O serviço NAT reconhece a direção do fluxo e substituirá o destino da Internet padrão em sua sub-rede. O endereço IP público da VM não será usado para conexões de saída.

### <a name="create-public-ip-for-source-vm"></a>Criar IP público para a VM de origem

Criamos um IP público para ser usado para acessar a VM.  Use [az network public-ip create](/cli/azure/network/public-ip) para criar um recurso de endereço IP público chamado **myPublicIPVM** em **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --sku standard
```

### <a name="create-an-nsg-for-vm"></a>Criar um NSG para a VM

Como os endereços IP Públicos Standard são "seguros por padrão", precisamos criar um NSG para permitir o acesso de entrada para acesso SSH. Use [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) para criar um recurso NSG chamado **myNSG** em **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSG 
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>Expor o ponto de extremidade SSH na VM de origem

Criamos uma regra no NSG para acesso SSH à VM de origem. Use [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) para criar uma regra NSG chamada **ssh** no NSG chamado **myNSG** em **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSG \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="create-nic-for-vm"></a>Criar NIC para a VM

Crie um adaptador de rede com [az network nic create](/cli/azure/network/nic#az-network-nic-create) e associe ao endereço IP Público e o grupo de segurança de rede. 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --public-ip-address myPublicIPVM \
    --network-security-group myNSG
```

### <a name="create-vm"></a>Criar VM

Crie a máquina virtual com [az vm create](/cli/azure/vm#az-vm-create).  Geramos chaves SSH para essa VM e armazenamos a chave privada para uso posterior.

 ```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --generate-ssh-keys
```

Aguarde até que a VM seja implantada e continue com o restante das etapas.

## <a name="discover-the-ip-address-of-the-vm"></a>Descobrir o endereço IP da VM

Primeiro precisamos descobrir o endereço IP da VM criada. Para recuperar o endereço IP público da VM, use [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Copie o endereço IP público e cole-o em um bloco de notas para poder usá-lo a fim de acessar a VM.

### <a name="sign-in-to-vm"></a>Entrar na VM

As credenciais do SSH devem ser armazenadas no Cloud Shell da operação anterior.  Abra um [Azure Cloud Shell](https://shell.azure.com) no navegador. Use o endereço IP recuperado na etapa anterior para se conectar por SSH à máquina virtual.

```bash
ssh <ip-address-destination>
```

Agora você está pronto para usar o serviço NAT.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, você poderá usar o comando [az group delete](/cli/azure/group#az-group-delete) para remover o grupo de recursos e todos os recursos contidos nele.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um gateway da NAT e uma VM para usá-lo. 

Examine as métricas no Azure Monitor para verificar a operação do serviço NAT. Diagnostique problemas como o esgotamento de recursos das portas SNAT disponíveis.  A exaustão de recursos de portas SNAT é resolvida adicionando mais recursos de endereço IP público, de prefixo IP público ou ambos.


- Saiba mais sobre a [NAT de Rede Virtual do Azure](./nat-overview.md)
- Saiba mais sobre o [recurso de gateway da NAT](./nat-gateway-resource.md).
- Início Rápido para implantar o [recurso de gateway da NAT usando a CLI do Azure](./quickstart-create-nat-gateway-cli.md).
- Início Rápido para implantar o [recurso de gateway da NAT usando o Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Início Rápido para implantar o [recurso de gateway da NAT usando o portal do Azure](./quickstart-create-nat-gateway-portal.md).
> [!div class="nextstepaction"]
