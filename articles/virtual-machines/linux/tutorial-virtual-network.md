---
title: "Redes Virtuais do Azure e Máquinas Virtuais do Linux | Microsoft Docs"
description: "Tutorial – Gerenciar redes virtuais do Azure e Máquinas Virtuais do Linux com a CLI do Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 10dda8b93e003c35c2a97a0aa7ca74d04249e52f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-virtual-networks-and-linux-virtual-machines-with-the-azure-cli"></a>Gerenciar redes virtuais do Azure e Máquinas Virtuais do Linux com a CLI do Azure

As máquinas virtuais do Azure usam a rede do Azure para comunicação de rede interna e externa. Este tutorial explica como implantar duas máquinas virtuais e configurar a rede do Azure para essas VMs. Os exemplos neste tutorial supõem que as VMs estão hospedando um aplicativo Web com um back-end de banco de dados. No entanto, não há implantação de aplicativo no tutorial. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Implantar uma rede virtual
> * Criar uma sub-rede em uma rede virtual
> * Anexar máquinas virtuais a uma sub-rede
> * Gerenciar endereços IP públicos de máquina virtual
> * Proteger tráfego da internet de entrada
> * Garantir VM para tráfego de VM


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá que você execute a CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

## <a name="vm-networking-overview"></a>Visão Geral da VM

As redes virtuais do Azure habilitam conexões de rede seguras entre máquinas virtuais, Internet e outros serviços do Azure, como banco de dados SQL do Azure. As redes virtuais são divididas em segmentos lógicos chamados sub-redes. As sub-redes são usadas para controlar o fluxo de rede e como um limite de segurança. Ao implantar uma máquina virtual, ela geralmente inclui um adaptador de rede virtual, que é anexado a uma sub-rede.

## <a name="deploy-virtual-network"></a>Implantar rede virtual

Para este tutorial, uma única rede virtual é criada com duas sub-redes. Uma sub-rede de front-end para hospedar um aplicativo Web e uma sub-rede de back-end para hospedar um servidor de banco de dados.

Antes de criar uma máquina virtual, crie um grupo de recursos com o [az group create](/cli/azure/group#create). O exemplo abaixo cria um grupo de recursos denominado *myRGNetwork* no local eastus.

```azurecli-interactive 
az group create --name myRGNetwork --location eastus
```

### <a name="create-virtual-network"></a>Criar rede virtual

Use o comando [az network vnet create](/cli/azure/network/vnet#create) para criar uma rede virtual. Neste exemplo, a rede é denominada *mvVnet* e recebe um prefixo de endereço *10.0.0.0/16*. Uma sub-rede também é criada com o nome *mySubnetFrontEnd* e um prefixo *10.0.1.0/24*. Mais tarde neste tutorial, uma VM de front-end será conectada a essa sub-rede. 

```azurecli-interactive 
az network vnet create \
  --resource-group myRGNetwork \
  --name myVnet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name mySubnetFrontEnd \
  --subnet-prefix 10.0.1.0/24
```

### <a name="create-subnet"></a>Criar sub-rede

Uma nova sub-rede é adicionada à rede virtual usando o comando [az network vnet subnet create](/cli/azure/network/vnet/subnet#create). Neste exemplo, a sub-rede é denominada *mySubnetBackEnd* e recebe um prefixo de endereço *10.0.2.0/24*. Essa sub-rede é usada com todos os serviços de back-end.

```azurecli-interactive 
az network vnet subnet create \
  --resource-group myRGNetwork \
  --vnet-name myVnet \
  --name mySubnetBackEnd \
  --address-prefix 10.0.2.0/24
```

Neste ponto, uma rede foi criada e segmentada em duas sub-redes, uma para os serviços de front-end e outra para serviços de back-end. Na próxima seção, as máquinas virtuais serão criadas e conectadas a essas sub-redes.

## <a name="understand-public-ip-address"></a>Compreender o endereço IP público

Um endereço IP público permite que os recursos do Azure sejam acessados pela Internet. Nesta seção do tutorial, uma VM é criada para demonstrar como trabalhar com endereços IP públicos.

### <a name="allocation-method"></a>Método de alocação

Um endereço IP público pode ser alocado como dinâmico ou estático. Por padrão, o endereço IP público é alocado dinamicamente. Os endereços IP dinâmicos são liberados quando uma VM é desalocada. Esse comportamento faz com que o endereço IP se altere durante operações que incluam uma desalocação de VM.

O método de alocação pode ser definido como estático, o que garante que o endereço IP permaneça atribuídos a uma VM mesmo durante um estado desalocado. Ao usar um endereço IP alocado estaticamente, o próprio endereço IP não poderá ser especificado. Em vez disso, ele é alocado de um pool de endereços disponíveis.

### <a name="dynamic-allocation"></a>Alocação dinâmica

Ao criar uma máquina virtual com o comando [az vm create](/cli/azure/vm#create), o método de alocação do endereço IP público padrão será dinâmico. No exemplo a seguir, uma máquina virtual é criada com um endereço IP dinâmico. 

```azurecli-interactive 
az vm create \
  --resource-group myRGNetwork \
  --name myFrontEndVM \
  --vnet-name myVnet \
  --subnet mySubnetFrontEnd \
  --nsg myNSGFrontEnd \
  --public-ip-address myFrontEndIP \
  --image UbuntuLTS \
  --generate-ssh-keys
```

### <a name="static-allocation"></a>Alocação estática

Ao criar uma máquina virtual usando o comando [az vm create](/cli/azure/vm#create), inclua o argumento `--public-ip-address-allocation static` para atribuir um endereço IP público estático. Essa operação não é demonstrada neste tutorial, mas, na próxima seção, um endereço IP alocado dinamicamente é alterado para um endereço alocado estaticamente. 

### <a name="change-allocation-method"></a>Alterar método de alocação

O método de alocação de endereço IP pode ser alterado usando o comando [az network public-ip update](/cli/azure/network/public-ip#update). Neste exemplo, o método de alocação do endereço IP da máquina virtual front-end é alterado para estático.

Primeiro, desaloque a VM.

```azurecli-interactive 
az vm deallocate --resource-group myRGNetwork --name myFrontEndVM
```

Use o comando [az network public-ip update](/cli/azure/network/public-ip#update) para atualizar o método de alocação. Nesse caso, o `--allocation-method` está sendo definido como *estático*.

```azurecli-interactive 
az network public-ip update --resource-group myRGNetwork --name myFrontEndIP --allocation-method static
```

Inicie a VM.

```azurecli-interactive 
az vm start --resource-group myRGNetwork --name myFrontEndVM --no-wait
```

### <a name="no-public-ip-address"></a>Sem endereço IP público

Geralmente, uma VM não precisa ficar acessível pela Internet. Para criar uma máquina virtual sem um endereço IP público, use o argumento `--public-ip-address ""` com um conjunto vazio de aspas duplas. Essa configuração é demonstrada mais tarde neste tutorial

## <a name="secure-network-traffic"></a>Protegem o tráfego de rede

Um NSG (grupo de segurança de rede) contém uma lista de regras de segurança que permitem ou negam o tráfego de rede para recursos conectados a VNets (redes virtuais) do Azure. Os NSGs podem ser associados a sub-redes ou adaptadores de rede individuais. Quando um NSG está associado um adaptador de rede, ele se aplica somente à VM associada. Quando um NSG está associado a uma sub-rede, as regras se aplicam a todos os recursos conectados à sub-rede. 

### <a name="network-security-group-rules"></a>Regras de grupo de segurança de rede

As regras NSG definem portas de rede pelas quais o tráfego é permitido ou negado. As regras podem incluir intervalos de endereços IP de origem e de destino, para que o tráfego seja controlado entre sistemas ou sub-redes específicos. As regras NSG também incluem uma prioridade (entre 1 e 4096). As regras são avaliadas na ordem de prioridade. Uma regra com uma prioridade 100 é avaliada antes de uma regra com prioridade 200.

Todos os NSGs contêm um conjunto de regras padrão. As regras padrão não podem ser excluídas, mas como recebem a prioridade mais baixa, elas podem ser substituídas pelas regras que você criar.

- **Rede virtual:** o tráfego que começa e termina em uma rede virtual é permitido nas direções de entrada e saída.
- **Internet:** o tráfego de saída é permitido, mas o tráfego de entrada é bloqueado.
- **Balanceador de carga:** o balanceador de carga do Azure permite investigar a integridade de suas VMs e instâncias de função. Se não estiver usando um conjunto de balanceamento de carga, você poderá substituir essa regra.

### <a name="create-network-security-groups"></a>Criar grupos de segurança de rede

Um grupo de segurança de rede pode ser criado ao mesmo tempo que uma VM usando o comando [az vm create](/cli/azure/vm#create). Ao fazer isso, o NSG é associado ao adaptador de rede das VMs e uma regra NSG é criada automaticamente para permitir o tráfego na porta *22* de qualquer origem. No início deste tutorial, o NSG de front-end foi criado automaticamente com a máquina virtual de front-end. Uma regra NSG também foi criada para a porta 22 automaticamente. 

Em alguns casos, pode ser útil criar um NSG previamente, como quando as regras SSH padrão não devem ser criadas ou quando o NSG deve ser conectado a uma sub-rede. 

Use o comando [az network nsg create](/cli/azure/network/nsg#create) para criar um grupo de segurança de rede.

```azurecli-interactive 
az network nsg create --resource-group myRGNetwork --name myNSGBackEnd
```

Em vez de associar o NSG a um adaptador de rede, ele é associado a uma sub-rede. Nessa configuração, todas as VMs conectadas à sub-rede herdam as regras NSG.

Atualize a sub-rede existente denominada *mySubnetBackEnd* com o novo NSG.

```azurecli-interactive 
az network vnet subnet update \
  --resource-group myRGNetwork \
  --vnet-name myVnet \
  --name mySubnetBackEnd \
  --network-security-group myNSGBackEnd
```

Cri uma máquina virtual, que é anexada ao *mySubnetBackEnd*. Observe que o argumento `--nsg` possui um valor vazio com aspas duplas. Um NSG não precisa ser criado com a máquina virtual. A máquina virtual está conectada à sub-rede de back-end, que é protegida com o NSG de back-end criado previamente. Esse NSG aplica-se à VM. Além disso, observe aqui que o argumento `--public-ip-address` possui um valor vazios com aspas duplas. Essa configuração cria uma VM sem um endereço IP público. 

```azurecli-interactive 
az vm create \
  --resource-group myRGNetwork \
  --name myBackEndVM \
  --vnet-name myVnet \
  --subnet mySubnetBackEnd \
  --public-ip-address "" \
  --nsg "" \
  --image UbuntuLTS \
  --generate-ssh-keys
```

### <a name="secure-incoming-traffic"></a>Proteger o tráfego de entrada

Quando a máquina virtual front-end foi criada, uma regra NSG foi criada para permitir o tráfego de entrada na porta 22. Essa regra permite conexões de SSH para a máquina virtual. Para este exemplo, o tráfego também deve ser permitido na porta *80*. Essa configuração permite que um aplicativo Web seja acessado na VM.

Use o comando [az network nsg rule create](/cli/azure/network/nsg/rule#create) para criar uma regra de porta *80*.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myNSGFrontEnd \
  --name http \
  --access allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 200 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 80
```

A VM de front-end agora só está acessível nas portas *22* e *80*. Todos os outros tráfegos de entrada são bloqueados no grupo de segurança de rede. Ele pode ser útil para visualizar as configurações de regra do NSG. Retorne a configuração da regra do NSG como comando [az network rule list](/cli/azure/network/nsg/rule#list). 

```azurecli-interactive 
az network nsg rule list --resource-group myRGNetwork --nsg-name myNSGFrontEnd --output table
```

Saída:

```azurecli-interactive 
Access    DestinationAddressPrefix      DestinationPortRange  Direction    Name                 Priority  Protocol    ProvisioningState    ResourceGroup    SourceAddressPrefix    SourcePortRange
--------  --------------------------  ----------------------  -----------  -----------------  ----------  ----------  -------------------  ---------------  ---------------------  -----------------
Allow     *                                               22  Inbound      default-allow-ssh        1000  Tcp         Succeeded            myRGNetwork      *                      *
Allow     *                                               80  Inbound      http                      200  Tcp         Succeeded            myRGNetwork      *                      *
```

### <a name="secure-vm-to-vm-traffic"></a>Garantir VM para tráfego de VM

As regras de grupo de segurança de rede também podem se aplicar entre VMs. Neste exemplo, a VM de front-end precisa se comunicar com a VM de back-end nas portas *22* e *3306*. Essa configuração permite conexões de SSH da VM front-end e também que um aplicativo na VM de front-end se comunique com um banco de dados MySQL de back-end. Todo o tráfego deve ser bloqueado entre as máquinas virtuais de front-end e back-end.

Use o comando [az network nsg rule create](/cli/azure/network/nsg/rule#create) para criar uma regra de porta 22. Observe que o argumento `--source-address-prefix` especifica um valor de *10.0.1.0/24*. Essa configuração garante que apenas o tráfego da sub-rede de front-end seja permitido pelo NSG.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myNSGBackEnd \
  --name SSH \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix 10.0.1.0/24 \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "22"
```

Agora, adicione uma regra para o tráfego MySQL na porta 3306.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myNSGBackEnd \
  --name MySQL \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 200 \
  --source-address-prefix 10.0.1.0/24 \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "3306"
```

Por fim, como os NSGs têm uma regra padrão permitindo todo o tráfego entre as VMs na mesma rede virtual, uma regra pode ser criada para os NSGs de back-end bloquearem todo o tráfego. Observe aqui que a `--priority` recebe um valor de *300*, que é menor que as regras NSG e MySQL. Essa configuração garante que os tráfegos SSH e MySQL ainda sejam permitidos pelo NSG.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myNSGBackEnd \
  --name denyAll \
  --access Deny \
  --protocol Tcp \
  --direction Inbound \
  --priority 300 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "*"
```

A VM de back-end agora só está acessível nas portas *22* e *3306* da sub-rede de front-end. Todos os outros tráfegos de entrada são bloqueados no grupo de segurança de rede. Ele pode ser útil para visualizar as configurações de regra do NSG. Retorne a configuração da regra do NSG como comando [az network rule list](/cli/azure/network/nsg/rule#list). 

```azurecli-interactive 
az network nsg rule list --resource-group myRGNetwork --nsg-name myNSGBackEnd --output table
```

Saída:

```azurecli-interactive 
Access    DestinationAddressPrefix    DestinationPortRange    Direction    Name       Priority  Protocol    ProvisioningState    ResourceGroup    SourceAddressPrefix    SourcePortRange
--------  --------------------------  ----------------------  -----------  -------  ----------  ----------  -------------------  ---------------  ---------------------  -----------------
Allow     *                           22                      Inbound      SSH             100  Tcp         Succeeded            myRGNetwork      10.0.1.0/24            *
Allow     *                           3306                    Inbound      MySQL           200  Tcp         Succeeded            myRGNetwork      10.0.1.0/24            *
Deny      *                           *                       Inbound      denyAll         300  Tcp         Succeeded            myRGNetwork      *                      *
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou e protegeu redes do Azure em relação às máquinas virtuais. Você aprendeu como:

> [!div class="checklist"]
> * Implantar uma rede virtual
> * Criar uma sub-rede em uma rede virtual
> * Anexar máquinas virtuais a uma sub-rede
> * Gerenciar endereços IP públicos de máquina virtual
> * Proteger tráfego da internet de entrada
> * Garantir VM para tráfego de VM

Avance para o próximo tutorial a fim de aprender sobre como proteger dados em máquinas virtuais usando o backup do Azure. 

> [!div class="nextstepaction"]
> [Fazer backup de máquinas virtuais do Linux no Azure](./tutorial-backup-vms.md)
