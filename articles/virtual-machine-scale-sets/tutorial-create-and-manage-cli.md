---
title: 'Tutorial: Criar e gerenciar um conjunto de dimensionamento de máquinas virtuais – CLI do Azure'
description: Aprenda a usar a CLI do Azure para criar um conjunto de dimensionamento de máquina virtual, juntamente com algumas tarefas comuns de gerenciamento, como iniciar e parar uma instância ou alterar a capacidade do conjunto de dimensionamento.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 0f94823b958ae5f95789dd4ef9a62057bdf764a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94517454"
---
# <a name="tutorial-create-and-manage-a-virtual-machine-scale-set-with-the-azure-cli"></a>Tutorial: Criar e gerenciar um conjunto de dimensionamento de máquinas virtuais definido com a CLI do Azure
Um conjunto de dimensionamento de máquinas virtuais permite implantar e gerenciar um conjunto de máquinas virtuais idênticas de dimensionamento automático. Durante todo o ciclo de vida do conjunto de dimensionamento de uma máquina virtual, você poderá precisar executar uma ou mais tarefas de gerenciamento. Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Criar e se conectar a um conjunto de dimensionamento de máquinas virtuais
> * Selecionar e usar imagens de VM
> * Exibir e usar tamanhos específicos de instância de VM
> * Dimensionar manualmente um conjunto de dimensionamento
> * Executar tarefas comuns de gerenciamento de conjunto de dimensionamento

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo exige a versão 2.0.29 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada. 


## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Você deve criar um grupo de recursos antes de criar um conjunto de dimensionamento de máquinas virtuais. Crie um grupo de recursos com o comando [az group create](/cli/azure/group). Neste exemplo, um grupo de recursos denominado *myResourceGroup* é criado na região *eastus*. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

O nome do grupo de recursos é especificado quando você cria ou modifica um conjunto de dimensionamento ao longo deste tutorial.


## <a name="create-a-scale-set"></a>Criar um conjunto de escala
Crie um conjunto de dimensionamento de máquinas virtuais com o comando [az vmss create](/cli/azure/vmss). O exemplo a seguir criará um conjunto de dimensionamento chamado *myScaleSet* e gerará chaves SSH se elas não existirem:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Leva alguns minutos para criar e configurar todos os recursos e as instâncias de VM do conjunto de dimensionamento. Para distribuir o tráfego para as instâncias de VM individuais, um balanceador de carga também é criado.


## <a name="view-the-vm-instances-in-a-scale-set"></a>Exibir as instâncias de VM em um conjunto de dimensionamento
Para exibir uma lista de instância de VM em um conjunto de dimensionamento, use [az vmss list-instances](/cli/azure/vmss) da seguinte forma:

```azurecli-interactive
az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```

A saída de exemplo abaixo mostra duas instâncias de VM no conjunto de dimensionamento:

```output
  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup    VmId
------------  --------------------  ----------  ------------  -------------------  ---------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUP  c059be0c-37a2-497a-b111-41272641533c
           3  True                  eastus      myScaleSet_3  Succeeded            MYRESOURCEGROUP  ec19e7a7-a4cd-4b24-9670-438f4876c1f9
```


A primeira coluna na saída mostra um *InstanceId*. Para exibir informações adicionais sobre uma instância específica de VM, adicione o parâmetro `--instance-id` a [az vmss get-instance-view](/cli/azure/vmss). O exemplo abaixo exibe informações sobre a instância de VM *1*:

```azurecli-interactive
az vmss get-instance-view \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --instance-id 1
```


## <a name="list-connection-information"></a>Listar informações de conexão
Um endereço IP público é atribuído ao balanceador de carga que direciona o tráfego para as instâncias de VM individuais. Por padrão, as regras de Conversão de endereços de rede (NAT) são adicionadas ao balanceador de carga do Azure, que encaminha o tráfego de conexão remota para cada VM em determinada porta. Para conectar as instâncias de VM em um conjunto de dimensionamento, crie uma conexão remota para um endereço IP público e número de porta atribuídos.

Para listar os endereços e as portas para se conectar a instâncias de VM em um conjunto de dimensionamento, use [az vmss list-instance-connection-info](/cli/azure/vmss):

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```

A saída de exemplo a seguir mostra o nome da instância, o endereço IP público do balanceador de carga e o número de porta para onde as regras de NAT encaminham o tráfego:

```output
{
  "instance 1": "13.92.224.66:50001",
  "instance 3": "13.92.224.66:50003"
}
```

SSH para a primeira instância de VM. Especifique o endereço IP público e o número da porta com o parâmetro `-p`, conforme mostrado no comando anterior:

```console
ssh azureuser@13.92.224.66 -p 50001
```

Depois de conectado à instância de VM, você poderá executar algumas alterações de configuração manualmente, caso seja necessário. Por enquanto, feche a sessão de SSH normalmente:

```console
exit
```


## <a name="understand-vm-instance-images"></a>Entender imagens de instância de VM
Quando você criou um conjunto de dimensionamento definido no início do tutorial, uma `--image` de *UbuntuLTS* foi especificada para as instâncias de VM. O Azure Marketplace inclui muitas imagens que podem ser usadas para criar instâncias de VM. Para ver uma lista dos mais usados imagens, use o comando [lista de imagens de vm az](/cli/azure/vm/image).

```azurecli-interactive
az vm image list --output table
```

A saída de exemplo a seguir mostra as imagens de VM mais comuns no Azure. O *UrnAlias* pode ser usado para especificar uma dessas imagens comuns ao criar um conjunto de dimensionamento.

```output
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
```

Para exibir uma lista completa, adicione o argumento `--all`. A lista de imagens também pode ser filtrada por `--publisher` ou `–-offer`. No exemplo abaixo, a lista está filtrada para todas as imagens com uma oferta que corresponde a *CentOS*:

```azurecli-interactive
az vm image list --offer CentOS --all --output table
```

A seguinte saída condensada mostra algumas das imagens de CentOS 7.3 disponíveis:

```output
Offer    Publisher   Sku   Urn                                 Version
-------  ----------  ----  ----------------------------------  -------------
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20161221   7.3.20161221
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170421   7.3.20170421
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170517   7.3.20170517
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170612   7.3.20170612
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170707   7.3.20170707
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170925   7.3.20170925
```

Para implantar um conjunto de dimensionamento que usa uma imagem específica, use o valor na coluna *Urn*. Na hora de especificar a imagem, o número de versão da imagem pode ser substituído por *mais recente*, que seleciona a versão mais recente da distribuição. No exemplo a seguir, o `--image` argumento é usado para especificar a versão mais recente de uma imagem do CentOS 7.3.

> [!IMPORTANT]
> É recomendável usar a versão *mais recente* da imagem. Especifique 'mais recente' para usar a versão mais recente de uma imagem disponível no momento da implantação. Observe que, mesmo que você use 'mais recente', a imagem da VM não será atualizada automaticamente após o tempo de implantação, mesmo se uma nova versão ficar disponível.

Como demora alguns minutos para criar e configurar todos os recursos do conjunto de dimensionamento e as instâncias de VM, você não precisa implantar o seguinte conjunto de dimensionamento:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSetCentOS \
  --image OpenLogic:CentOS:7.3:latest \
  --admin-user azureuser \
  --generate-ssh-keys
```


## <a name="understand-vm-instance-sizes"></a>Entender tamanhos de instância de VM
Um tamanho de instância de VM, ou *SKU*, determina a quantidade de recursos de computação, como memória, CPU e GPU que estão disponíveis para a instância de VM. As instâncias de VM em um conjunto de dimensionamento precisam ser dimensionadas apropriadamente para a carga de trabalho esperada.

### <a name="vm-instance-sizes"></a>Tamanhos de instância de VM
A tabela a seguir categoriza tamanhos de VMs comuns para determinados casos de uso.

| Type                     | Tamanhos comuns           |    Descrição       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Propósito geral](../virtual-machines/sizes-general.md)         |Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0-7| CPU/memória equilibrados. Ideal para desenvolvimento/teste e para aplicativos de pequeno a médio porte e soluções de dados.  |
| [Computação otimizada](../virtual-machines/sizes-compute.md)   | Fs, F             | Relação de CPU/memória alta. Boa para aplicativos de tráfego médio, dispositivos de rede e processos em lote.        |
| [Memória otimizada](../virtual-machines/sizes-memory.md)    | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | Relação de memória/núcleo alta. Ótima para banco de dados relacionais, caches médios a grandes e análises na memória.                 |
| [Armazenamento otimizado](../virtual-machines/sizes-storage.md)      | Ls                | Alta taxa de transferência de disco e de E/S. Ideal para Big Data, SQL e bancos de dados NoSQL.                                                         |
| [GPU](../virtual-machines/sizes-gpu.md)          | NV, NC            | VMs especializadas, destinadas para renderização gráfica e edição de vídeo pesadas.       |
| [Alto desempenho](../virtual-machines/sizes-hpc.md) | H, A8-11          | Nossas VMs de CPU mais potentes com adaptadores de rede de alto rendimento (RDMA) opcionais. 

### <a name="find-available-vm-instance-sizes"></a>Localizar tamanhos de instância de VM disponíveis
Para ver uma lista de tamanhos de instância de VM disponíveis em uma região específica, use o comando [az vm list-sizes](/cli/azure/vm).

```azurecli-interactive
az vm list-sizes --location eastus --output table
```

O resultado é semelhante ao exemplo condensado abaixo, que mostra os recursos atribuídos a cada tamanho de VM:

```output
  MaxDataDiskCount    MemoryInMb  Name                      NumberOfCores    OsDiskSizeInMb    ResourceDiskSizeInMb
------------------  ------------  ----------------------  ---------------  ----------------  ----------------------
                 4          3584  Standard_DS1_v2                       1           1047552                    7168
                 8          7168  Standard_DS2_v2                       2           1047552                   14336
[...]
                 1           768  Standard_A0                           1           1047552                   20480
                 2          1792  Standard_A1                           1           1047552                   71680
[...]
                 4          2048  Standard_F1                           1           1047552                   16384
                 8          4096  Standard_F2                           2           1047552                   32768
[...]
                24         57344  Standard_NV6                          6           1047552                   38912
                48        114688  Standard_NV12                        12           1047552                  696320
```

### <a name="create-a-scale-set-with-a-specific-vm-instance-size"></a>Criar um conjunto de dimensionamento com um tamanho de instância de VM específico
Quando você criou um conjunto de dimensionamento no início do tutorial, um SKU de VM padrão *Standard_D1_v2* foi fornecido para as instâncias de VM. Você pode especificar um tamanho de instância de VM diferente com base na saída de [az vm list-sizes](/cli/azure/vm). O exemplo a seguir cria um conjunto de dimensionamento com o parâmetro `--vm-sku` para especificar um tamanho de instância de VM *Standard_F1*. Como demora alguns minutos para criar e configurar todos os recursos do conjunto de dimensionamento e as instâncias de VM, você não precisa implantar o seguinte conjunto de dimensionamento:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSetF1Sku \
  --image UbuntuLTS \
  --vm-sku Standard_F1 \
  --admin-user azureuser \
  --generate-ssh-keys
```


## <a name="change-the-capacity-of-a-scale-set"></a>Alterar a capacidade de um conjunto de dimensionamento
Quando você criou um conjunto de dimensionamento definido no início do tutorial, duas instâncias de VM foram implantadas por padrão. Você pode especificar o parâmetro `--instance-count` com [az vmss create](/cli/azure/vmss) para alterar o número de instâncias criadas com um conjunto de dimensionamento. Para aumentar ou diminuir o número de instâncias de VM no conjunto de dimensionamento existente, você pode alterar a capacidade manualmente. O conjunto de dimensionamento cria ou remove o número necessário de instâncias de VM e configura o balanceador de carga para distribuir o tráfego.

Para aumentar ou diminuir o número de instâncias de VM no conjunto de dimensionamento manualmente, use [az vmss scale](/cli/azure/vmss). O seguinte exemplo aumenta o número de instâncias de VM no conjunto de dimensionamento para *3*:

```azurecli-interactive
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 3
```

Demora alguns minutos para atualizar a capacidade do seu conjunto de dimensionamento. Para ver o número de instâncias atualmente em um conjunto de dimensionamento, use [az vmss show](/cli/azure/vmss) e consulte *sku.capacity*:

```azurecli-interactive
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```


## <a name="common-management-tasks"></a>Tarefas comuns de gerenciamento
Agora você pode criar um conjunto de dimensionamento, listar as informações de conexão e se conectar a instâncias de VM. Você aprendeu como pode usar uma imagem de sistema operacional diferente para suas instâncias de VM, selecionar um tamanho de VM diferente ou dimensionar o número de instâncias manualmente. Como parte do gerenciamento diário, talvez seja necessário parar, iniciar ou reiniciar as instâncias de VM em seu conjunto de dimensionamento.

### <a name="stop-and-deallocate-vm-instances-in-a-scale-set"></a>Parar e desalocar instâncias de VM em um conjunto de dimensionamento
Para parar uma ou mais instâncias de VM em um conjunto de dimensionamento, use [az vmss stop](/cli/azure/vmss). O parâmetro `--instance-ids` permite que você especifique uma ou mais instâncias de VM a serem paradas. Se você não especificar uma ID de instância, todas as instâncias de VM no conjunto de dimensionamento serão paradas. O exemplo abaixo interrompe a instância *1*:

```azurecli-interactive
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```

As instâncias de VM paradas permanecem alocadas e continuam a incorrer em encargos de computação. Se, em vez disso, você deseja que as instâncias de VM sejam desalocadas e incorram somente em encargos de armazenamento, use [az vmss deallocate](/cli/azure/vmss). O exemplo abaixo interrompe e desaloca a instância *1*:

```azurecli-interactive
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```

### <a name="start-vm-instances-in-a-scale-set"></a>Iniciar instâncias de VM em um conjunto de dimensionamento
Para iniciar uma ou mais instâncias de VM em um conjunto de dimensionamento, use [az vmss start](/cli/azure/vmss). O parâmetro `--instance-ids` permite que você especifique uma ou mais instâncias de VM a serem iniciadas. Se você não especificar uma ID de instância, todas as instâncias de VM no conjunto de dimensionamento serão iniciadas. O exemplo abaixo inicia a instância *1*:

```azurecli-interactive
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```

### <a name="restart-vm-instances-in-a-scale-set"></a>Reiniciar instâncias de VM em um conjunto de dimensionamento
Para reiniciar uma ou mais instâncias de VM em um conjunto de dimensionamento, use [az vmss restart](/cli/azure/vmss). O parâmetro `--instance-ids` permite que você especifique uma ou mais instâncias de VM a serem reiniciadas. Se você não especificar uma ID de instância, todas as instâncias de VM no conjunto de dimensionamento serão reiniciadas. O exemplo abaixo reinicia a instância *1*:

```azurecli-interactive
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```


## <a name="clean-up-resources"></a>Limpar os recursos
Quando você excluir um grupo de recursos, todos os recursos contidos, como as instâncias de VM, as rede virtuais e os discos, também serão excluídos. O parâmetro `--no-wait` retorna o controle ao prompt sem aguardar a conclusão da operação. O parâmetro `--yes` confirma que você deseja excluir os recursos sem um prompt adicional para fazer isso.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu a executar algumas tarefas básicas de criação e gerenciamento de conjuntos de escala com a CLI do Azure:

> [!div class="checklist"]
> * Criar e se conectar a um conjunto de dimensionamento de máquinas virtuais
> * Selecionar e usar imagens de VM
> * Exibir e usar tamanhos específicos de VM
> * Dimensionar manualmente um conjunto de dimensionamento
> * Executar tarefas comuns de gerenciamento de conjunto de dimensionamento

Avance para o próximo tutorial a fim de saber mais sobre discos de conjunto de dimensionamento.

> [!div class="nextstepaction"]
> [Usar discos de dados com conjuntos de dimensionamento](tutorial-use-disks-cli.md)
