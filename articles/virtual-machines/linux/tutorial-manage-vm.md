---
title: Tutorial – Criar e gerenciar VMs Linux com a CLI do Azure | Microsoft Docs
description: Neste tutorial, você aprenderá a usar a CLI do Azure para criar e gerenciar VMs Linux no Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/23/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 1de2d40e107c03db8c0e406a7bb1a12c15d5c736
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708507"
---
# <a name="tutorial-create-and-manage-linux-vms-with-the-azure-cli"></a>Tutorial: Criar e gerenciar VMs do Linux com a CLI do Azure

Máquinas virtuais do Azure fornecem um ambiente de computação totalmente configurável e flexível. Este tutorial aborda itens básicos de implantação de máquina virtual do Azure, como a seleção de um tamanho de VM, seleção de uma imagem de VM e implantação de uma VM. Você aprenderá como:

> [!div class="checklist"]
> * Criar e conectar-se a uma VM
> * Selecionar e usar imagens de VM
> * Exibir e usar tamanhos específicos de VM
> * Redimensionar uma VM
> * Exibir e compreender o estado da VM

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá que você execute a CLI do Azure versão 2.0.30 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos com o comando [az group create](https://docs.microsoft.com/cli/azure/group). 

Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Você deve criar um grupo de recursos antes de criar uma máquina virtual. Neste exemplo, criaremos um grupo de recursos chamado *myResourceGroupVM* na região *eastus*. 

```azurecli-interactive 
az group create --name myResourceGroupVM --location eastus
```

O grupo de recursos é especificado ao criar ou modificar uma VM, que pode ser visto durante este tutorial.

## <a name="create-virtual-machine"></a>Criar máquina virtual

Crie uma máquina virtual com o comando [az vm create](https://docs.microsoft.com/cli/azure/vm). 

Há várias opções disponíveis ao criar uma máquina virtual, como a imagem do sistema operacional, as credenciais administrativas e o dimensionamento do disco. O exemplo a seguir cria uma VM chamada *myVM* que executa o Servidor Ubuntu. Uma conta de usuário chamada *azureuser* é criada na VM, e as chaves SSH são geradas, se ainda não existirem no local de chave padrão ( *~/.ssh*):

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupVM \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

A criação da VM pode levar alguns minutos. Depois que a VM tiver sido criada, a CLI do Azure envia informações sobre a VM. Anote o `publicIpAddress`, esse endereço pode ser usado para acessar a máquina virtual... 

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroupVM"
}
```

## <a name="connect-to-vm"></a>Conectar-se a uma VM

Agora você pode se conectar à VM com o SSH no Azure Cloud Shell ou do computador local. Substitua o endereço IP de exemplo com o `publicIpAddress` observado na etapa anterior.

```bash
ssh azureuser@52.174.34.95
```

Depois de conectado à VM, você pode instalar e configurar aplicativos. Quando tiver terminado, você fechará a sessão SSH normalmente:

```bash
exit
```

## <a name="understand-vm-images"></a>Entender as imagens de VM

O Azure marketplace inclui muitas imagens que podem ser usadas para criar VMs. Nas etapas anteriores, uma máquina virtual foi criada usando uma imagem do Ubuntu. Nesta etapa, a CLI do Azure é usada para pesquisar no marketplace para uma imagem CentOS, que é usado para implantar uma segunda máquina virtual. 

Para ver uma lista dos mais usados imagens, use o comando [lista de imagens de vm az](/cli/azure/vm/image).

```azurecli-interactive 
az vm image list --output table
```

A saída do comando retorna as imagens da VM mais populares no Azure.

```bash
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
```

Uma lista completa pode ser vista, adicionando o `--all` argumento. A lista de imagens também pode ser filtrada por `--publisher` ou `–-offer`. Neste exemplo, a lista está filtrada para todas as imagens com uma oferta que corresponde a *CentOS*. 

```azurecli-interactive 
az vm image list --offer CentOS --all --output table
```

Resultado parcial:

```azurecli-interactive 
Offer             Publisher         Sku   Urn                                     Version
----------------  ----------------  ----  --------------------------------------  -----------
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201501         6.5.201501
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201503         6.5.201503
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201506         6.5.201506
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20150904       6.5.20150904
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20160309       6.5.20160309
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20170207       6.5.20170207
```

Para implantar uma VM usando uma imagem específica, anote o valor da coluna *Urn*, o qual consiste no publicador, oferta, SKU e, opcionalmente, um número de versão para [identificar](cli-ps-findimage.md#terminology) a imagem. Ao especificar a imagem, o número de versão da imagem pode ser substituído por "mais recente", que seleciona a versão mais recente da distribuição. Neste exemplo, o `--image` argumento é usado para especificar a versão mais recente de uma imagem do CentOS 6.5.  

```azurecli-interactive 
az vm create --resource-group myResourceGroupVM --name myVM2 --image OpenLogic:CentOS:6.5:latest --generate-ssh-keys
```

## <a name="understand-vm-sizes"></a>Entender os tamanhos de VM

Um tamanho de máquina virtual determina a quantidade de recursos de computação, como memória, CPU e GPU que estão disponíveis para a máquina virtual. Máquinas virtuais precisam ser dimensionada adequadamente para a carga de trabalho esperada. Se a carga de trabalho aumentar, uma máquina virtual existente pode ser redimensionada.

### <a name="vm-sizes"></a>Tamanhos de VM

A tabela a seguir categoriza tamanhos em casos de uso.  

| Type                     | Tamanhos comuns           |    DESCRIÇÃO       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Propósito geral](sizes-general.md)         |B, Dsv3, Dv3, DSv2, Dv2, Av2, DC| CPU/memória equilibrados. Ideal para desenvolvimento/teste e para aplicativos de pequeno a médio porte e soluções de dados.  |
| [Computação otimizada](sizes-compute.md)   | Fsv2          | Relação de CPU/memória alta. Boa para aplicativos de tráfego médio, dispositivos de rede e processos em lote.        |
| [Memória otimizada](sizes-memory.md)    | Esv3, Ev3, M, DSv2, Dv2  | Relação de memória/núcleo alta. Ótima para banco de dados relacionais, caches médios a grandes e análises na memória.                 |
| [Armazenamento otimizado](sizes-storage.md)      | Lsv2, Ls              | Alta taxa de transferência de disco e de E/S. Ideal para Big Data, SQL e bancos de dados NoSQL.                                                         |
| [GPU](sizes-gpu.md)          | NV, NVv2, NC, NCv2, NCv3, ND            | VMs especializadas, destinadas para renderização gráfica e edição de vídeo pesadas.       |
| [Alto desempenho](sizes-hpc.md) | H        | Nossas VMs de CPU mais potentes com adaptadores de rede de alto rendimento (RDMA) opcionais. |


### <a name="find-available-vm-sizes"></a>Encontrar tamanhos de VM disponíveis

Para ver uma lista de tamanhos de VM disponíveis em uma região específica, use o comando [lista-tamanhos de vm az](/cli/azure/vm). 

```azurecli-interactive 
az vm list-sizes --location eastus --output table
```

Resultado parcial:

```azurecli-interactive 
  MaxDataDiskCount    MemoryInMb  Name                      NumberOfCores    OsDiskSizeInMb    ResourceDiskSizeInMb
------------------  ------------  ----------------------  ---------------  ----------------  ----------------------
                 2          3584  Standard_DS1                          1           1047552                    7168
                 4          7168  Standard_DS2                          2           1047552                   14336
                 8         14336  Standard_DS3                          4           1047552                   28672
                16         28672  Standard_DS4                          8           1047552                   57344
                 4         14336  Standard_DS11                         2           1047552                   28672
                 8         28672  Standard_DS12                         4           1047552                   57344
                16         57344  Standard_DS13                         8           1047552                  114688
                32        114688  Standard_DS14                        16           1047552                  229376
                 1           768  Standard_A0                           1           1047552                   20480
                 2          1792  Standard_A1                           1           1047552                   71680
                 4          3584  Standard_A2                           2           1047552                  138240
                 8          7168  Standard_A3                           4           1047552                  291840
                 4         14336  Standard_A5                           2           1047552                  138240
                16         14336  Standard_A4                           8           1047552                  619520
                 8         28672  Standard_A6                           4           1047552                  291840
                16         57344  Standard_A7                           8           1047552                  619520
```

### <a name="create-vm-with-specific-size"></a>Criar VM com um tamanho específico

No exemplo de criação de VM anterior, um tamanho não foi fornecido, que resulta em um tamanho padrão. Um tamanho de VM pode ser selecionado no momento da criação usando [criar vm az](/cli/azure/vm) e `--size` argumento. 

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroupVM \
    --name myVM3 \
    --image UbuntuLTS \
    --size Standard_F4s \
    --generate-ssh-keys
```

### <a name="resize-a-vm"></a>Redimensionar uma VM

Após a implantação de uma VM, ela pode ser redimensionada para aumentar ou diminuir a alocação de recursos. Você pode exibir atual do tamanho de uma VM com [az vm show](/cli/azure/vm):

```azurecli-interactive
az vm show --resource-group myResourceGroupVM --name myVM --query hardwareProfile.vmSize
```

Antes de redimensionar uma VM, verifique se o tamanho desejado está disponível no cluster da VM atual. O comando [az vm lista-vm--opções de redimensionamento](/cli/azure/vm) retorna a lista de tamanhos. 

```azurecli-interactive 
az vm list-vm-resize-options --resource-group myResourceGroupVM --name myVM --query [].name
```
Se o tamanho desejado estiver disponível, a VM poderá ser redimensionada a partir de um estado ligado. No entanto, ela é reinicializada durante a operação. Use o [az vm redimensionar]( /cli/azure/vm) comando para executar o redimensionamento.

```azurecli-interactive 
az vm resize --resource-group myResourceGroupVM --name myVM --size Standard_DS4_v2
```

Se o tamanho desejado não estiver no cluster atual, a VM precisará ser desalocada antes que a operação de redimensionamento ocorra. Utilize o comando [az vm deallocate]( /cli/azure/vm) para parar e desalocar a máquina virtual. Observe que quando a VM é ligada novamente, todos os dados no disco temporário podem ser removidos. O endereço IP público também altera a menos que um endereço IP estático está sendo usado. 

```azurecli-interactive 
az vm deallocate --resource-group myResourceGroupVM --name myVM
```

Quando desalocados, o redimensionamento pode ocorrer. 

```azurecli-interactive 
az vm resize --resource-group myResourceGroupVM --name myVM --size Standard_GS1
```

Após o redimensionamento, a VM pode ser iniciada.

```azurecli-interactive 
az vm start --resource-group myResourceGroupVM --name myVM
```

## <a name="vm-power-states"></a>Estados de energia da VM

Uma VM do Azure pode ter um dentre vários estados de energia. Esse estado representa o estado atual da VM do ponto de vista do hipervisor. 

### <a name="power-states"></a>Estados de energia

| Estado de energia | DESCRIÇÃO
|----|----|
| Iniciando | Indica que a máquina virtual está sendo iniciada. |
| Executando | Indica que a máquina virtual está em execução. |
| Parando | Indica que a máquina virtual está sendo interrompida. | 
| Parado | Indica que a máquina virtual foi parada. Máquinas virtuais no estado interrompido ainda incorrerá em encargos de computação.  |
| Desalocando | Indica que a máquina virtual está sendo desalocada. |
| Desalocada | Indica que a máquina virtual é removido do hipervisor, mas ainda estão disponíveis no plano de controle. Máquinas virtuais no estado Desalocado não incorrem em encargos de computação. |
| - | Indica que o estado de energia da máquina virtual é desconhecido. |

### <a name="find-the-power-state"></a>Localizar o estado de energia

Para recuperar o estado de uma VM específica, use o comando [az vm get-instance-view](/cli/azure/vm). Especifique um nome válido para uma máquina virtual e grupo de recursos. 

```azurecli-interactive 
az vm get-instance-view \
    --name myVM \
    --resource-group myResourceGroupVM \
    --query instanceView.statuses[1] --output table
```

Saída:

```azurecli-interactive 
ode                DisplayStatus    Level
------------------  ---------------  -------
PowerState/running  VM running       Info
```

## <a name="management-tasks"></a>Tarefas de gerenciamento

Durante o ciclo de vida de uma máquina virtual, você talvez queira executar tarefas de gerenciamento, como iniciar, interromper ou excluir uma máquina virtual. Além disso, é possível que você queira criar scripts para automatizar tarefas repetitivas ou complexas. Usando a CLI do Azure, muitas tarefas comuns de gerenciamento podem ser executadas em linha de comando ou em scripts. 

### <a name="get-ip-address"></a>Como obter o endereço IP

Esse comando retorna os endereços IP públicos e privados de uma máquina virtual.  

```azurecli-interactive 
az vm list-ip-addresses --resource-group myResourceGroupVM --name myVM --output table
```

### <a name="stop-virtual-machine"></a>Como interromper uma máquina virtual

```azurecli-interactive 
az vm stop --resource-group myResourceGroupVM --name myVM
```

### <a name="start-virtual-machine"></a>Como iniciar uma máquina virtual

```azurecli-interactive 
az vm start --resource-group myResourceGroupVM --name myVM
```

### <a name="delete-resource-group"></a>Excluir grupo de recursos

Excluir um grupo de recursos exclui todos os recursos contidos nele, tais como a VM, rede virtual e disco. O parâmetro `--no-wait` retorna o controle ao prompt sem aguardar a conclusão da operação. O parâmetro `--yes` confirma que você deseja excluir os recursos sem um prompt adicional para fazer isso.

```azurecli-interactive 
az group delete --name myResourceGroupVM --no-wait --yes
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre a criação e o gerenciamento básico de VM e como:

> [!div class="checklist"]
> * Criar e conectar-se a uma VM
> * Selecionar e usar imagens de VM
> * Exibir e usar tamanhos específicos de VM
> * Redimensionar uma VM
> * Exibir e compreender o estado da VM

Avança para o próximo tutorial para saber mais sobre os discos de VM.  

> [!div class="nextstepaction"]
> [Criar e gerenciar discos de VM](./tutorial-manage-disks.md)
