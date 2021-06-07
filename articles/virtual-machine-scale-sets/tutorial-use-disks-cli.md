---
title: Tutorial – Criar e usar discos para conjuntos de dimensionamento com a CLI do Azure
description: Aprenda a usar a CLI do Azure para criar e usar Discos gerenciados com o conjunto de dimensionamento de máquinas virtuais, incluindo como adicionar, preparar, listar e desanexar discos.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: adaa7d1c2cf4a78a680ef4fbbec06975ceda812b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96433490"
---
# <a name="tutorial-create-and-use-disks-with-virtual-machine-scale-set-with-the-azure-cli"></a>Tutorial: Criar e usar discos com conjunto de dimensionamento de máquinas virtuais com a CLI do Azure
Conjuntos de dimensionamento de máquinas virtuais usam discos para armazenar o sistema operacional da instância de VM, aplicativos e dados. Ao criar e gerenciar um conjunto de dimensionamento, é importante escolher um tamanho e uma configuração de disco apropriados para a carga de trabalho esperada. Este tutorial aborda como criar e gerenciar os discos de VM. Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Discos de sistema operacional e discos temporários
> * Discos de dados
> * Discos Standard e Premium
> * Desempenho do disco
> * Anexar e preparar discos de dados

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo exige a versão 2.0.29 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="default-azure-disks"></a>Discos padrão do Azure
Quando um conjunto de dimensionamento é criado ou dimensionado, dois discos são automaticamente anexados a cada instância de VM.

**Disco do sistema operacional** – os discos do sistema operacional podem ser dimensionados para até 2 TB e hospedar o sistema operacional da instância de VM. O disco do sistema operacional é rotulado */dev/sda* por padrão. A configuração de cache do disco do SO é otimizada para desempenho do SO. Devido a essa configuração, o disco do sistema operacional **não deve** hospedar aplicativos ou dados. Para aplicativos e dados, utilize um disco de dados, que é detalhado posteriormente neste artigo.

**Disco temporário** – discos temporários utilizam uma unidade de estado sólido localizada no mesmo host do Azure que a instância da VM. Tratam-se de discos de alto desempenho que podem ser usados para operações como o processamento de dados temporário. No entanto, se a instância de VM for movida para um novo host, todos os dados armazenados em um disco temporário serão removidos. O tamanho do disco temporário é determinado pelo tamanho da instância de VM. Os discos temporários são rotulados */dev/sdb* e têm um ponto de montagem de */mnt*.

### <a name="temporary-disk-sizes"></a>Tamanhos do disco temporário
| Type | Tamanhos comuns | Tamanho máximo do disco temporário (GiB) |
|----|----|----|
| [Propósito geral](../virtual-machines/sizes-general.md) | Série A, B e D | 1600 |
| [Computação otimizada](../virtual-machines/sizes-compute.md) | Série F | 576 |
| [Memória otimizada](../virtual-machines/sizes-memory.md) | Série D, E, G e M | 6144 |
| [Armazenamento otimizado](../virtual-machines/sizes-storage.md) | Série L | 5630 |
| [GPU](../virtual-machines/sizes-gpu.md) | Série N | 1440 |
| [Alto desempenho](../virtual-machines/sizes-hpc.md) | Série A e H | 2000 |


## <a name="azure-data-disks"></a>Discos de dados do Azure
Outros discos de dados podem ser adicionados caso você precise instalar aplicativos e armazenar dados. Os discos de dados devem ser usados em qualquer situação onde o armazenamento de dados durável e responsivo é desejado. Cada disco de dados tem uma capacidade máxima de 4 TB. O tamanho da instância de VM determina quantos discos de dados podem ser anexados. Para cada vCPU de VM, dois discos de dados podem ser anexados a um máximo absoluto de 64 discos por máquina virtual.

## <a name="vm-disk-types"></a>Tipos de disco da máquina virtual
O Azure fornece dois tipos de disco.

### <a name="standard-disk"></a>Disco Standard
O armazenamento Standard é apoiado por HDDs e oferece armazenamento e desempenho econômicos. Os discos Standard são ideais para uma carga de trabalho econômica de desenvolvimento e teste.

### <a name="premium-disk"></a>Disco Premium
Os discos Premium são apoiados por disco de baixa latência e alto desempenho baseado em SSD. Esses discos são recomendados para as VMs que executam cargas de trabalho de produção. O Armazenamento Premium dá suporte às VMs das séries DS, DSv2, GS e FS. Ao escolher o tamanho de um disco, o valor é arredondado para o tipo seguinte. Por exemplo, se o tamanho do disco for menor que 128 GB, o tipo de disco é P10. Se o tamanho do disco está entre 129 e 512 GB, o tamanho é P20. Acima de 512 GB, o tamanho é um P30.

### <a name="premium-disk-performance"></a>Desempenho do disco Premium
|Tipo de disco de armazenamento Premium | P4 | P6 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Tamanho do disco (arredondado) | 32 GB | 64 GB | 128 GB | 512 GB | 1\.024 GB (1 TB) | 2,048 GB (2 TB) | 4,095 GB (4 TB) |
| IOPS máxima por disco | 120 | 240 | 500 | 2\.300 | 5\.000 | 7\.500 | 7\.500 |
Taxa de transferência por disco | 25 MB/s | 50 MB/s | 100 MB/s | 150 MB/s | 200 MB/s | 250 MB/s | 250 MB/s |

Embora a tabela acima identifique a IOPS máxima por disco, um nível mais alto de desempenho pode ser obtido com a distribuição de vários discos de dados. Por exemplo, uma VM Standard_GS5 pode atingir o máximo de 80.000 IOPS. Para obter informações detalhadas sobre o máximo de IOPS por VM, veja [Tamanhos da VM Linux](../virtual-machines/sizes.md).


## <a name="create-and-attach-disks"></a>Criar e anexar discos
Você pode criar e anexar discos ao criar um conjunto de dimensionamento ou com um conjunto de dimensionamento existente.

Desde a versão `2019-07-01` da API, é possível definir o tamanho do disco do sistema operacional em um conjunto de dimensionamento de máquinas virtuais com a propriedade [storageProfile.osDisk.diskSizeGb](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosdisk). Após o provisionamento, talvez seja necessário expandir ou reparticionar o disco para uso do espaço inteiro. Saiba mais sobre a [expansão de disco aqui](../virtual-machines/windows/expand-os-disk.md#expand-the-volume-within-the-os).

### <a name="attach-disks-at-scale-set-creation"></a>Anexar discos na criação do conjunto de dimensionamento
Primeiro, crie um grupo de recursos com o comando [az group create](/cli/azure/group). Neste exemplo, um grupo de recursos denominado *myResourceGroup* é criado na região *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Crie um conjunto de dimensionamento de máquinas virtuais com o comando [az vmss create](/cli/azure/vmss). O exemplo a seguir criará um conjunto de dimensionamento chamado *myScaleSet* e gerará chaves SSH se elas não existirem. Dois discos são criados com o parâmetro `--data-disk-sizes-gb`. É o primeiro disco tem *64* GB de tamanho e o segundo, *128* GB:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy automatic \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 64 128
```

Leva alguns minutos para criar e configurar todos os recursos e as instâncias de VM do conjunto de dimensionamento.

### <a name="attach-a-disk-to-existing-scale-set"></a>Anexar um disco ao conjunto de dimensionamento existente
Você também pode anexar discos a um conjunto de dimensionamento existente. Use o conjunto de dimensionamento criado na etapa anterior para adicionar outro disco com [az vmss disk attach](/cli/azure/vmss/disk). O exemplo abaixo anexa um disco de *128* GB adicional:

```azurecli-interactive
az vmss disk attach \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --size-gb 128
```


## <a name="prepare-the-data-disks"></a>Preparar os discos de dados
Os discos que são criados e anexados às suas instâncias de VM do conjunto de dimensionamento são discos brutos. Antes que você possa usá-los com seus dados e aplicativos, os discos devem ser preparados. Para preparar os discos, crie uma partição, crie um sistema de arquivos e monte-os.

Para automatizar o processo em várias instâncias de VM em um conjunto de dimensionamento, você pode usar a Extensão de Script Personalizado do Azure. Esta extensão pode executar scripts localmente em cada instância de VM, como preparar discos de dados anexados. Para obter mais informações, consulte a [Visão geral da Extensão de Script Personalizado](../virtual-machines/extensions/custom-script-linux.md).

O exemplo a seguir executa um script de um repositório GitHub de exemplo em cada instância de VM com [az vmss extension set](/cli/azure/vmss/extension), que prepara todos os discos de dados brutos anexados:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"],"commandToExecute":"./prepare_vm_disks.sh"}'
```

Para confirmar que os discos foram preparados corretamente, faça o SSH para uma das instâncias de VM. Liste as informações de conexão para o conjunto de dimensionamento com [az vmss list-instance-connection-info](/cli/azure/vmss):

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```

Use seu próprio número de porta e endereço IP público para se conectar com a primeira instância de VM, conforme mostrado no exemplo abaixo:

```console
ssh azureuser@52.226.67.166 -p 50001
```

Examine as partições na instância de VM da seguinte maneira:

```bash
sudo fdisk -l
```

A saída de exemplo a seguir mostra que três discos estão anexados à instância de V: */dev/sdc*, */dev/sdd*, e */dev/sde*. Cada um desses discos tem uma única partição que usa todo o espaço disponível:

```bash
Disk /dev/sdc: 64 GiB, 68719476736 bytes, 134217728 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xa47874cb

Device     Boot Start       End   Sectors Size Id Type
/dev/sdc1        2048 134217727 134215680  64G 83 Linux

Disk /dev/sdd: 128 GiB, 137438953472 bytes, 268435456 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xd5c95ca0

Device     Boot Start       End   Sectors  Size Id Type
/dev/sdd1        2048 268435455 268433408  128G 83 Linux

Disk /dev/sde: 128 GiB, 137438953472 bytes, 268435456 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x9312fdf5

Device     Boot Start       End   Sectors  Size Id Type
/dev/sde1        2048 268435455 268433408  128G 83 Linux
```

Examine os sistemas de arquivos e os pontos de montagem na instância de VM da seguinte maneira:

```bash
sudo df -h
```

A saída de exemplo abaixo mostra que os três discos têm seus sistemas de arquivos montados corretamente em */datadisks*:

```output
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        30G  1.3G   28G   5% /
/dev/sdb1        50G   52M   47G   1% /mnt
/dev/sdc1        63G   52M   60G   1% /datadisks/disk1
/dev/sdd1       126G   60M  120G   1% /datadisks/disk2
/dev/sde1       126G   60M  120G   1% /datadisks/disk3
```

Os discos em cada instância de VM no dimensionamento são automaticamente preparados da mesma maneira. À medida que seu conjunto de dimensionamento escala verticalmente, os discos de dados necessários são anexados às novas instâncias de VM. A Extensão de Script Personalizado também é executada para preparar os discos automaticamente.

Feche a conexão SSH para a instância de VM:

```bash
exit
```


## <a name="list-attached-disks"></a>Listar discos anexados
Para exibir informações sobre os discos anexados a um conjunto de dimensionamento, use [az vmss show](/cli/azure/vmss) e consulte *virtualMachineProfile.storageProfile.dataDisks*:

```azurecli-interactive
az vmss show \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --query virtualMachineProfile.storageProfile.dataDisks
```

São exibidas informações sobre o tamanho do disco, a camada de armazenamento e o LUN (número de unidade lógica). A saída de exemplo abaixo detalha os três discos de dados anexados ao conjunto de dimensionamento:

```json
[
  {
    "additionalProperties": {},
    "caching": "None",
    "createOption": "Empty",
    "diskSizeGb": 64,
    "lun": 0,
    "managedDisk": {
      "additionalProperties": {},
      "storageAccountType": "Standard_LRS"
    },
    "name": null
  },
  {
    "additionalProperties": {},
    "caching": "None",
    "createOption": "Empty",
    "diskSizeGb": 128,
    "lun": 1,
    "managedDisk": {
      "additionalProperties": {},
      "storageAccountType": "Standard_LRS"
    },
    "name": null
  },
  {
    "additionalProperties": {},
    "caching": "None",
    "createOption": "Empty",
    "diskSizeGb": 128,
    "lun": 2,
    "managedDisk": {
      "additionalProperties": {},
      "storageAccountType": "Standard_LRS"
    },
    "name": null
  }
]
```


## <a name="detach-a-disk"></a>Desanexar um disco
Quando não precisar mais de determinado disco, você poderá desanexá-lo do conjunto de dimensionamento. O disco é removido de todas as instâncias de VM no conjunto de dimensionamento. Para desanexar um disco de um conjunto de dimensionamento, use [az vmss disk detach](/cli/azure/vmss/disk) e especifique o LUN do disco. Os LUNs são mostrados na saída de [az vmss show](/cli/azure/vmss) na seção anterior. O exemplo a seguir desanexa o LUN *2* do conjunto de dimensionamento:

```azurecli-interactive
az vmss disk detach \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --lun 2
```


## <a name="clean-up-resources"></a>Limpar os recursos
Para remover o conjunto de dimensionamento e os discos, exclua o grupo de recursos e todos os seus recursos com [az group delete](/cli/azure/group). O parâmetro `--no-wait` retorna o controle ao prompt sem aguardar a conclusão da operação. O parâmetro `--yes` confirma que você deseja excluir os recursos sem um prompt adicional para fazer isso.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como criar e usar discos com conjuntos de dimensionamento com a CLI do Azure:

> [!div class="checklist"]
> * Discos de sistema operacional e discos temporários
> * Discos de dados
> * Discos Standard e Premium
> * Desempenho do disco
> * Anexar e preparar discos de dados

Siga para o próximo tutorial para aprender a usar uma imagem personalizada para suas instâncias de VM do conjunto de dimensionamento.

> [!div class="nextstepaction"]
> [Usar uma imagem personalizada para instâncias de VM do conjunto de dimensionamento](tutorial-use-custom-image-cli.md)