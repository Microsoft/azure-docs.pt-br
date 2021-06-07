---
title: 'Tutorial: Criar um gateway da NAT – CLI do Azure'
titlesuffix: Azure Virtual Network NAT
description: Comece a criar um gateway da NAT usando a CLI do Azure.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/10/2021
ms.custom: template-tutorial, devx-track-azurecli
ms.openlocfilehash: 60436b8d4a0f338f4ece59ad4cd11c14c9e4c352
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762633"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-cli"></a>Tutorial: criar um gateway da NAT usando a CLI do Azure

Este tutorial mostra como usar o serviço NAT da Rede Virtual do Azure. Você criará um gateway da NAT para fornecer conectividade de saída para uma máquina virtual no Azure. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Crie uma rede virtual.
> * Crie uma máquina virtual.
> * Crie um gateway da NAT e o associe à rede virtual.
> * Conecte-se à máquina virtual e verifique o endereço IP de NAT.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este guia de início rápido requer a versão 2.0.28 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com [az group create](/cli/azure/group#az_group_create). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

O seguinte exemplo cria um grupo de recursos chamado **myResourceGroupNAT** na localização **eastus2**:

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>Criar o gateway da NAT

Nesta seção, criamos o gateway da NAT e os recursos de suporte.

### <a name="create-public-ip-address"></a>Criar um endereço IP público

Para acessar a Internet, você precisa de um ou mais endereços IP públicos para o gateway da NAT. Use [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) para criar um recurso de endereço IP público chamado **myPublicIP** em **myResourceGroupNAT**. 

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard \
    --allocation static
```

### <a name="create-nat-gateway-resource"></a>Criar um recurso de gateway da NAT

Crie um gateway da NAT global do Azure com [az network nat gateway create](/cli/azure/network/nat#az_network_nat_gateway_create). O resultado desse comando criará um recurso de gateway chamado **myNATgateway**, que usa o endereço IP público **myPublicIP**. O tempo limite de ociosidade está definido como 10 minutos.  

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --idle-timeout 10       
  ```

### <a name="create-virtual-network"></a>Criar rede virtual

Crie uma rede virtual nomeada **myVnet** com uma sub-rede nomeada **mySubnet** com [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) no grupo de recursos **myResourceGroup**. O espaço de endereços IP da rede virtual é **10.1.0.0/16**. A sub-rede dentro da rede virtual é **10.1.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnet \
    --address-prefix 10.1.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.1.0.0/24
```

### <a name="create-bastion-host"></a>Criar um bastion host

Crie um host do Azure Bastion chamado **myBastionHost** para acessar a máquina virtual. 

Use o comando [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) para criar uma sub-rede do Azure Bastion.

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroupNAT \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

Crie um endereço IP público para o bastion host com [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create). 

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myBastionIP \
    --sku Standard
```

Use [az network bastion create](/cli/azure/network/bastion#az_network_bastion_create) para criar o bastion host. 

```azurecli-interactive
az network bastion create \
    --resource-group myResourceGroupNAT \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus2
```

### <a name="configure-nat-service-for-source-subnet"></a>Configurar o serviço NAT da sub-rede de origem

Configuraremos a sub-rede de origem **mySubnet** na rede virtual **myVnet** para usar um recurso de gateway da NAT específico **myNATgateway** com [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update). Esse comando ativará o serviço NAT na sub-rede especificada.

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

Todo o tráfego de saída para os destinos da Internet agora está usando o gateway da NAT.  Não é necessário configurar uma UDR.


## <a name="virtual-machine"></a>Máquina virtual

Nesta seção, você criará uma máquina virtual para testar um gateway da NAT a fim de verificar o endereço IP público da conexão de saída.

Crie a máquina virtual com [az vm create](/cli/azure/vm#az_vm_create).

```azurecli-interactive
az vm create \
    --name myVM \
    --resource-group myResourceGroupNAT  \
    --admin-username azureuser \
    --image win2019datacenter \
    --public-ip-address "" \
    --subnet mySubnet \
    --vnet-name myVNet
```

Aguarde a conclusão da criação da máquina virtual antes de passar para a próxima seção.

## <a name="test-nat-gateway"></a>Testar um gateway da NAT

Nesta seção, testaremos um gateway da NAT. Primeiro, descobriremos o IP público do gateway da NAT. Depois nos conectaremos à máquina virtual de teste e verificaremos a conexão de saída por meio do gateway da NAT.
    
1. Entre no [Portal do Azure](https://portal.azure.com)

1. Localize o endereço IP público do gateway da NAT na tela **Visão Geral**. Clique em **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e depois selecione **myPublicIP**.

2. Anote o endereço IP público:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="Descobrir o endereço IP público do gateway da NAT" border="true":::

3. Selecione **Todos os serviços** no menu esquerdo, depois **Todos os recursos**. Além disso, na lista de recursos, selecione a opção **myVM** que está localizada no grupo de recursos **myResourceGroupNAT**.

4. Na página **Visão Geral**, selecione **Conectar** e **Bastion**.

5. Selecione o botão azul **Usar Bastion**.

6. Insira o nome de usuário e a senha fornecidos durante a criação da VM.

7. Abra o **Internet Explorer** em **myTestVM**.

8. Digite **https://whatsmyip.com** na barra de endereços.

9. Verifique se o endereço IP exibido corresponde ao endereço do gateway da NAT anotado na etapa anterior:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="O Internet Explorer mostrando um IP de saída externo" border="true":::

## <a name="clean-up-resources"></a>Limpar os recursos

Caso não pretenda continuar usando esse aplicativo, exclua a rede virtual, a máquina virtual e o gateway da NAT usando as seguintes etapas:

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a NAT da Rede Virtual do Azure, confira:
> [!div class="nextstepaction"]
> [Visão geral da NAT da Rede Virtual](nat-overview.md)
