---
title: Criar uma máquina virtual do Azure com Rede Acelerada | Microsoft Docs
description: Saiba como criar uma máquina virtual Linux com Rede Acelerada habilitada.
services: virtual-network
documentationcenter: na
author: gsilva5
manager: gedegrac
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/10/2019
ms.author: gsilva
ms.custom: ''
ms.openlocfilehash: 8ea17e5615c0256c084b0745a392fb49f8873f99
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60713718"
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking"></a>Criar uma máquina virtual Linux com Rede Acelerada

Neste tutorial, você aprenderá a criar uma VM (máquina virtual) do Linux com Rede Acelerada. Para criar uma VM Windows com Rede Acelerada, consulte [Criar uma VM Windows com Rede Acelerada](create-vm-accelerated-networking-powershell.md). Rede acelerada permite SR-IOV (virtualização de E/S de raiz única) para uma VM, melhorando muito seu desempenho de rede. Esse caminho de alto desempenho ignora o host do caminho de dados, reduzindo a latência, a tremulação e a utilização da CPU para uso com as cargas de trabalho de rede mais exigentes nos tipos de VM compatíveis. A figura abaixo mostra a comunicação entre duas VMs com e sem rede acelerada:

![Comparação](./media/create-vm-accelerated-networking/accelerated-networking.png)

Sem rede acelerada, todo o tráfego de rede que entra e sai da VM deve cruzar o host e o comutador virtual. O comutador virtual fornece toda a imposição de política, como grupos de segurança de rede, listas de controle de acesso, isolamento e outros serviços virtualizados de rede para tráfego de rede. Para saber mais sobre comutadores virtuais, leia o artigo [Virtualização de rede Hyper-V e comutador virtual](https://technet.microsoft.com/library/jj945275.aspx).

Com Rede Acelerada, o tráfego de rede chega ao NIC (adaptador de rede) da máquina virtual e então é encaminhado para a VM. Todas as políticas de rede que o comutador virtual aplica agora são descarregadas e aplicadas em hardware. Aplicar a política de hardware permite que a NIC encaminhe tráfego de rede diretamente à VM, ignorando o host e o comutador virtual, mantendo toda a política que aplicou no host.

Os benefícios da rede acelerada aplicam-se somente à VM em que ela está habilitada. Para obter melhores resultados, é ideal habilitar esse recurso em pelo menos duas VMs conectadas à mesma VNet (rede virtual) do Azure. Ao se comunicar entre VNets ou fazer conexão local, esse recurso tem impacto mínimo sobre a latência geral.

## <a name="benefits"></a>Benefícios
* **Latência menor/mais pps (pacotes por segundo):** remover o comutador virtual do caminho de dados elimina o tempo que os pacotes gastam no host para processamento da política e aumenta o número de pacotes que podem ser processados dentro da VM.
* **Tremulação reduzida:** o processamento de comutador virtual depende da quantidade de política que precisa ser aplicada e da carga de trabalho da CPU que está fazendo o processamento. O descarregamento da imposição de política para o hardware remove essa variabilidade ao entregar pacotes diretamente à VM, removendo a comunicação do host para a VM e todas as interrupções e mudanças de contexto de software.
* **Utilização de CPU reduzida:** ignorar o comutador virtual no host resulta em menor utilização da CPU para processar o tráfego de rede.

## <a name="supported-operating-systems"></a>Sistemas operacionais com suporte
As seguintes distribuições têm suporte imediato da Galeria do Azure: 
* **Ubuntu 14.04 com o kernel do linux azure**
* **Ubuntu 16.04 ou posterior** 
* **SLES12 SP3 ou posterior** 
* **RHEL 7.4 ou posterior**
* **CentOS 7.4 ou posteriores**
* **CoreOS Linux**
* **Debian "Stretch" com kernel de portas traseiras**
* **Oracle Linux 7.4 e posterior com o Red Hat compatível com Kernel (rhck c)**
* **Oracle Linux 7.5 e versões posterior com UEK versão 5**
* **FreeBSD 10.4, 11.1 & 12.0**

## <a name="limitations-and-constraints"></a>Limitações e Restrições

### <a name="supported-vm-instances"></a>Instâncias de VM compatíveis
A Rede Acelerada é compatível com os tamanhos de instância de uso geral e de computação otimizada com 2 ou mais vCPUs.  Essas séries com suporte são: D/DSv2 e F/Fs

Em instâncias que são compatíveis com hyperthreading, a Rede Acelerada é compatível com instâncias de VM com 4 ou mais vCPUs. Essas séries com suporte são: D/DSV3, e/esv3, Fsv2, Lsv2, Ms/Mms e Ms/Mmsv2.

Para obter mais informações sobre instâncias de VM, consulte [Tamanhos de VM do Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="regions"></a>Regiões
Disponível em todas as regiões do Azure públicas e também na Nuvem do Azure Governamental.

<!-- ### Network interface creation 
Accelerated networking can only be enabled for a new NIC. It cannot be enabled for an existing NIC.
removed per issue https://github.com/MicrosoftDocs/azure-docs/issues/9772 -->
### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Habilitando Rede Acelerada em uma VM em execução
Um tamanho de VM com suporte sem rede acelerada habilitada só pode ter o recurso habilitado quando ele for interrompido e desalocado.  
### <a name="deployment-through-azure-resource-manager"></a>Implantação por meio do Azure Resource Manager
Máquinas virtuais (clássicas) não podem ser implantadas com Rede Acelerada.

## <a name="create-a-linux-vm-with-azure-accelerated-networking"></a>Criar uma VM do Linux com Rede Acelerada do Azure
## <a name="portal-creation"></a>Criação de portal
Embora este artigo forneça etapas para criar uma máquina virtual com a rede acelerada usando a CLI do Azure, você também pode [criar uma máquina virtual com a rede acelerada usando o portal do Azure](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ao criar uma máquina virtual no portal, nos **criar uma máquina virtual** folha, escolha o **Networking** guia.  Nessa guia, há uma opção para **rede acelerada**.  Se você tiver escolhido um [sistema operacional com suporte](#supported-operating-systems) e [tamanho da VM](#supported-vm-instances), essa opção será preenchido automaticamente para "Ativado".  Caso contrário, ele irá preencher a opção "Desativado" para a rede acelerada e dar ao usuário um motivo por que ele não ainda ser habilitado.   

* *Observação:* Apenas sistemas operacionais com suporte pode ser habilitados por meio do portal.  Se você estiver usando uma imagem personalizada e sua imagem oferece suporte a rede acelerada, crie sua VM usando a CLI ou Powershell. 

Depois que a máquina virtual é criada, você pode confirmar a rede acelerada está habilitada, seguindo as instruções de [confirme se a rede acelerada está habilitada](#confirm-that-accelerated-networking-is-enabled).

## <a name="cli-creation"></a>Criação de CLI
### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Instale a [CLI do Azure](/cli/azure/install-azure-cli) mais recente do Azure e faça logon em uma conta do Azure usando [az login](/cli/azure/reference-index). Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Os nomes de parâmetro de exemplo incluem *myResourceGroup*, *myNic* e *myVm*.

Crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *centralus*:

```azurecli
az group create --name myResourceGroup --location centralus
```

Selecione uma região do Linux compatível listada em [Rede acelerada Linux](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview).

Crie a rede virtual com [az network vnet create](/cli/azure/network/vnet). O exemplo a seguir cria uma rede virtual chamada *myVnet* com uma sub-rede:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede
Crie um grupo de segurança de rede com [az network nsg create](/cli/azure/network/nsg). O exemplo a seguir cria um grupo de segurança de rede denominado *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

O grupo de segurança de rede contém várias regras padrão, uma das quais desabilita todo o acesso de entrada proveniente da Internet. Abra uma porta para permitir o acesso SSH à máquina virtual com [az network nsg rule create](/cli/azure/network/nsg/rule):

```azurecli
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup \
  --name Allow-SSH-Internet \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 22
```

### <a name="create-a-network-interface-with-accelerated-networking"></a>Criar um adaptador de rede com rede acelerada

Crie um endereço IP público com [az network public-ip create](/cli/azure/network/public-ip). Você não precisa de um endereço IP público se não planeja acessar a máquina virtual por meio da Internet, mas precisa para concluir as etapas deste artigo.

```azurecli
az network public-ip create \
    --name myPublicIp \
    --resource-group myResourceGroup
```

Crie um adaptador de rede com [az network nic create](/cli/azure/network/nic) com a rede acelerada habilitada. O exemplo a seguir cria um adaptador de rede denominado *myNic* na sub-rede *mySubnet* da rede virtual *myVnet* e associa o grupo de segurança de rede *myNetworkSecurityGroup* ao adaptador de rede:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --accelerated-networking true \
    --public-ip-address myPublicIp \
    --network-security-group myNetworkSecurityGroup
```

### <a name="create-a-vm-and-attach-the-nic"></a>Criar uma VM e anexar o NIC
Ao criar a VM, especifique o NIC que criou com `--nics`. Selecione um tamanho e uma distribuição listados em [Rede acelerada Linux](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview). 

Crie uma VM com [az vm create](/cli/azure/vm). O exemplo a seguir cria uma VM denominada *myVM* com a imagem UbuntuLTS e um tamanho que é compatível com a Rede Acelerada (*Standard_DS4_v2*):

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS4_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic
```

Para obter uma lista de todos os tamanhos e características de VM, consulte [Tamanhos de VM do Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Depois que a VM é criada, é retornada uma saída semelhante ao exemplo a seguir. Anote o **publicIpAddress**. Esse endereço é usado para acessar a VM em etapas subsequentes.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "centralus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "192.168.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

### <a name="confirm-that-accelerated-networking-is-enabled"></a>Confirmar se a rede acelerada está habilitada

Use o comando a seguir para criar uma sessão SSH com a VM. Substitua `<your-public-ip-address>` pelo endereço IP público atribuído à máquina virtual que você criou e substitua *azureuser* se você usou um valor diferente para `--admin-username` ao criar a VM.

```bash
ssh azureuser@<your-public-ip-address>
```

No shell Bash, insira `uname -r` e confirme se a versão do kernel é uma das seguintes versões, ou superior:

* **Ubuntu 16.04**: 4.11.0-1013
* **SLES SP3**: 4.4.92-6.18
* **RHEL**: 7.4.2017120423
* **CentOS**: 7.4.20171206


Confirme se o dispositivo Mellanox VF está exposto à VM com o comando `lspci`. A saída retornada é semelhante à seguinte saída:

```bash
0000:00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (AGP disabled) (rev 03)
0000:00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 01)
0000:00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
0000:00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 02)
0000:00:08.0 VGA compatible controller: Microsoft Corporation Hyper-V virtual VGA
0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
```

Verifique se há atividade na VF (função virtual) com o comando `ethtool -S eth0 | grep vf_`. Se você receber uma saída semelhante ao seguinte exemplo de saída, a rede acelerada estará habilitada e funcionando.

```bash
vf_rx_packets: 992956
vf_rx_bytes: 2749784180
vf_tx_packets: 2656684
vf_tx_bytes: 1099443970
vf_tx_dropped: 0
```
A Rede Acelerada agora está habilitada para sua VM.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Habilitar Rede Acelerada em VMs existentes
Se você tiver criado uma VM sem Rede Acelerada, será possível habilitar esse recurso em uma VM existente.  A VM deve dar suporte à Rede Acelerada atendendo aos pré-requisitos a seguir que também estão descritos acima:

* A VM deve ter um tamanho com suporte para Rede Acelerada
* A VM deve ser uma imagem da Galeria do Azure com suporte (e a versão de kernel do Linux)
* Todas as VMs em um conjunto de disponibilidade ou VMSS devem ser interrompidas/desalocadas antes de se habilitar a Rede Acelerada em uma NIC

### <a name="individual-vms--vms-in-an-availability-set"></a>VMs individuais e VMs em um conjunto de disponibilidade
Primeiro interrompa/desaloque a VM ou, se for um Conjunto de Disponibilidade, todas as VMs no conjunto:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

Importante: observe que se sua VM tiver sido criada individualmente, sem um conjunto de disponibilidade, você só precisará interromper/desalocar a VM individual para habilitar a Rede Acelerada.  Se sua VM tiver sido criada com um conjunto de disponibilidade, todas as VMs contidas no conjunto de disponibilidade precisarão ser interrompidas/desalocadas antes de habilitar a Rede Acelerada em qualquer uma das NICs. 

Uma vez interrompida, habilite a Rede Acelerada na NIC de sua VM:

```azurecli
az network nic update \
    --name myNic \
    --resource-group myResourceGroup \
    --accelerated-networking true
```

Reinicie a VM ou, se em um Conjunto de Disponibilidade, todas as VMs no conjunto, e confirme se a Rede Acelerada está habilitada: 

```azurecli
az vm start --resource-group myResourceGroup \
    --name myVM
```

### <a name="vmss"></a>VMSS
O VMSS é ligeiramente diferente, mas segue o mesmo fluxo de trabalho.  Em primeiro lugar, interrompa as VMs:

```azurecli
az vmss deallocate \
    --name myvmss \
    --resource-group myrg
```

Depois que as VMs forem interrompidas, atualize a propriedade de Rede Acelerada na interface de rede:

```azurecli
az vmss update --name myvmss \
    --resource-group myrg \
    --set virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].enableAcceleratedNetworking=true
```

Observe que um VMSS tem upgrades de VM que aplicam atualizações usando três diferentes configurações: automática, sem interrupção e manual.  Nessas instruções, a política é definida como automática para que o VMSS acompanhe as alterações imediatamente após a reinicialização.  Para defini-la como automática para que as alterações sejam aplicadas imediatamente: 

```azurecli
az vmss update \
    --name myvmss \
    --resource-group myrg \
    --set upgradePolicy.mode="automatic"
```

Finalmente, reinicie o VMSS:

```azurecli
az vmss start \
    --name myvmss \
    --resource-group myrg
```

Após você reiniciar, aguarde o término dos upgrades. Após a conclusão, o VF será exibido dentro da VM.  (Verifique se você está usando um tamanho de VM e um sistema operacional com suporte.)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Redimensionar VMs existentes com Rede Acelerada

VMs com Rede Acelerada habilitada só podem ser redimensionadas para VMs com suporte para Rede Acelerada.  

Uma VM com Rede Acelerada habilitada não pode ser redimensionada para uma instância de VM que não oferece suporte a Rede Acelerada usando a operação de redimensionamento.  Em vez disso, para redimensionar uma dessas VMs: 

* Interrompa/desaloque a VM ou, em um conjunto de disponibilidade/VMSS, interrompa/desaloque todas as VMs no conjunto/VMSS.
* A Rede Acelerada deve ser desabilitada na NIC da VM ou, se em um conjunto de disponibilidade/VMSS, todas as VMs no conjunto/VMSS.
* Quando a Rede Acelerada é desabilitada, a VM/o conjunto de disponibilidade/o VMSS podem ser movidos para um novo tamanho que não oferece suporte para Rede Acelerada e reiniciados.  

