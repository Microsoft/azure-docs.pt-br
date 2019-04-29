---
title: Criar uma VM do Linux no Azure com várias NICs | Microsoft Docs
description: Saiba como criar uma VM Linux com várias NICs anexadas usando a CLI do Azure ou modelos do Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: 5d2d04d0-fc62-45fa-88b1-61808a2bc691
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/07/2018
ms.author: cynthn
ms.openlocfilehash: b77ed879375cff8d45f7d532283647e70252bdab
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60772426"
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Como criar uma máquina virtual Linux no Azure com várias placas de adaptador de rede


Este artigo fornece detalhes sobre como criar uma VM com várias NICs com a CLI do Azure.

## <a name="create-supporting-resources"></a>Criar recursos de suporte
Instale a [CLI do Azure](/cli/azure/install-az-cli2) mais recente do Azure e faça logon em uma conta do Azure usando [az login](/cli/azure/reference-index).

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Os nomes de parâmetro de exemplo incluem *myResourceGroup*, *mystorageaccount* e *myVM*.

Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Crie a rede virtual com [az network vnet create](/cli/azure/network/vnet). O exemplo a seguir cria uma rede virtual chamada *myVnet* e uma sub-rede chamada *mySubnetFrontEnd*:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 10.0.1.0/24
```

Crie uma sub-rede para o tráfego de back-end com [az network vnet subnet create](/cli/azure/network/vnet/subnet). O exemplo a seguir cria uma sub-rede chamada *mySubnetBackEnd*:

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 10.0.2.0/24
```

Crie um grupo de segurança de rede com [az network nsg create](/cli/azure/network/nsg). O exemplo a seguir cria um grupo de segurança de rede denominado *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>Criar e configurar várias NICs
Crie duas NICs com [az network nic create](/cli/azure/network/nic). O exemplo a seguir cria duas NICs, chamadas *myNic1* e *myNic2*, conectadas ao Grupo de Segurança de Rede, com uma NIC se conectando a cada sub-rede:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic1 \
    --vnet-name myVnet \
    --subnet mySubnetFrontEnd \
    --network-security-group myNetworkSecurityGroup
az network nic create \
    --resource-group myResourceGroup \
    --name myNic2 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>Criar uma VM e conectar as NICs
Quando você criar a VM, especifica as NICs que criou com `--nics`. Você também precisa tomar cuidado ao selecionar o tamanho da VM. Há limites para o número total de NICs que podem ser adicionados a uma VM. Leia mais sobre [Tamanhos de VM Linux](sizes.md).

Crie uma VM com [az vm create](/cli/azure/vm). O exemplo a seguir cria uma VM chamada *myVM*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS3_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic1 myNic2
```

Adicione tabelas de roteamento ao SO convidado concluindo as etapas em [Configure o SO convidado para várias NICs](#configure-guest-os-for- multiple-nics).

## <a name="add-a-nic-to-a-vm"></a>Adicionar uma NIC a uma VM
As etapas anteriores criaram uma VM com várias NICs. Também é possível adicionar NICs a uma VM existente com a CLI do Azure. Diferentes [tamanhos de VM](sizes.md) dão suporte a um número variável de NICs, sendo assim, dimensione sua VM adequadamente. Se necessário, é possível [redimensionar uma VM](change-vm-size.md).

Crie outra NIC com [az network nic create](/cli/azure/network/nic). O exemplo a seguir cria uma NIC chamada *myNic3* conectada à sub-rede de back-end e ao Grupo de Segurança de Rede criado nas etapas anteriores:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

Para adicionar uma NIC a uma VM existente, primeiro desaloque a VM com [az vm deallocate](/cli/azure/vm). O exemplo a seguir desaloca a VM chamada *myVM*:


```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Adicione a NIC com [az vm nic add](/cli/azure/vm/nic). O exemplo a seguir adiciona *myNic3* à *myVM*:

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Inicie a VM com [az vm start](/cli/azure/vm):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

Adicione tabelas de roteamento ao SO convidado concluindo as etapas em [Configure o SO convidado para várias NICs](#configure-guest-os-for- multiple-nics).

## <a name="remove-a-nic-from-a-vm"></a>Remover uma NIC de uma VM
Para remover uma NIC de uma VM existente, primeiro desaloque a VM com [az vm deallocate](/cli/azure/vm). O exemplo a seguir desaloca a VM chamada *myVM*:

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Remova a NIC com [az vm nic remove](/cli/azure/vm/nic). O exemplo a seguir remove *myNic3* de *myVM*:

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Inicie a VM com [az vm start](/cli/azure/vm):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```


## <a name="create-multiple-nics-using-resource-manager-templates"></a>Criar várias NICs usando modelos do Resource Manager
Os modelos do Azure Resource Manager usam arquivos JSON declarativos para definir o seu ambiente. Você pode ler uma [visão geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). Os modelos do Gerenciador de Recursos oferecem uma maneira de criar várias instâncias de um recurso durante a implantação, como a criação de várias NICs. Você usa *copiar* para especificar o número de instâncias a serem criadas:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Leia mais sobre a [criação de várias instâncias usando *copiar*](../../resource-group-create-multiple.md). 

Você também pode usar um `copyIndex()` para acrescentar um número a um nome de recurso, o que lhe permite criar `myNic1`, `myNic2` etc. Veja a seguir um exemplo de como acrescentar o valor de índice:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Você pode ler um exemplo completo em [Criando várias NICs usando modelos do Gerenciador de Recursos](../../virtual-network/template-samples.md).

Adicione tabelas de roteamento ao SO convidado concluindo as etapas em [Configure o SO convidado para várias NICs](#configure-guest-os-for- multiple-nics).

## <a name="configure-guest-os-for-multiple-nics"></a>Configurar o SO convidado para várias NICs

As etapas anteriores criaram uma rede virtual e uma sub-rede, conectaram adaptadores de rede e, em seguida, criaram uma VM. Um endereço IP público e regras de grupo de segurança de rede que permitem tráfego SSH não foram criados. Para configurar o SO convidado para vários adaptadores de rede, é necessário permitir conexões remotas e executar comandos localmente na VM.

Para permitir tráfego SSH, crie uma regra de grupo de segurança de rede com [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create), conforme a seguir:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name allow_ssh \
    --priority 101 \
    --destination-port-ranges 22
```

Crie um endereço IP público com [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) e atribua-o ao primeiro adaptador de rede com [az network nic ip-config update](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-update):

```azurecli
az network public-ip create --resource-group myResourceGroup --name myPublicIP

az network nic ip-config update \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --name ipconfig1 \
    --public-ip myPublicIP
```

Para exibir o endereço IP público da VM, use [az vm show](/cli/azure/vm#az-vm-show), conforme a seguir:

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Exemplo de SSH para o endereço IP público da VM. O nome de usuário padrão fornecido em uma etapa anterior era *azureuser*. Forneça seu próprio nome de usuário e endereço IP público:

```bash
ssh azureuser@137.117.58.232
```

Para enviar para ou de um adaptador de rede secundário, é necessário adicionar manualmente rotas persistentes ao sistema operacional para cada adaptador de rede secundário. Neste artigo, *eth1* é o adaptador de rede secundário. As instruções para adicionar rotas persistentes ao sistema operacional variam de acordo com a distribuição. Consulte a documentação da sua distribuição para obter mais instruções.

Ao adicionar a rota ao sistema operacional, o endereço do gateway será *.1* para qualquer sub-rede na qual o adaptador de rede está. Por exemplo, se o adaptador de rede tiver o endereço *10.0.2.4*, o gateway especificado para a rota será *10.0.2.1*. É possível definir uma rede específica para o destino da rota ou especificar um destino *0.0.0.0*, se você quiser que todo o tráfego do adaptador passe pelo gateway especificado. O gateway de cada sub-rede é gerenciado pela rede virtual.

Após adicionar a rota para um adaptador de rede secundário, verifique se a rota está na tabela de rotas com `route -n`. A saída de exemplo a seguir é para a tabela de rotas que tem as dois adaptadores de rede adicionadas à VM neste artigo:

```bash
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         10.0.1.1        0.0.0.0         UG    0      0        0 eth0
0.0.0.0         10.0.2.1        0.0.0.0         UG    0      0        0 eth1
10.0.1.0        0.0.0.0         255.255.255.0   U     0      0        0 eth0
10.0.2.0        0.0.0.0         255.255.255.0   U     0      0        0 eth1
168.63.129.16   10.0.1.1        255.255.255.255 UGH   0      0        0 eth0
169.254.169.254 10.0.1.1        255.255.255.255 UGH   0      0        0 eth0
```

Confirme se a rota que você adicionou persistiu entre os reinícios, verificando a tabela de rotas novamente após reiniciar. Para testar a conectividade, é possível inserir o comando a seguir, por exemplo, em que *eth1* é o nome de um adaptador de rede secundário:

```bash
ping bing.com -c 4 -I eth1
```

## <a name="next-steps"></a>Próximas etapas
Examine [Tamanhos de VM Linux](sizes.md) ao tentar criar uma VM com várias NICs. Preste atenção ao número máximo de NICs a que cada VM dá suporte.

Para proteger ainda mais as VMs, use acesso just-in-time à VM. Esse recurso abre regras de grupo de segurança de rede para tráfego SSH quando necessário e por um período de tempo definido. Para saber mais, confira [Gerenciar o acesso à máquina virtual usando o just in time](../../security-center/security-center-just-in-time.md).
