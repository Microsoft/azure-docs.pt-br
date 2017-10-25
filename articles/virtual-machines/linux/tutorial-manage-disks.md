---
title: Gerenciar discos do Azure com o Azure CLI | Microsoft Docs
description: "Tutorial – Gerenciar discos do Azure com o Azure CLI"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 882446ba32252490e27056c7c5c9a8f755e26ee6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-disks-with-the-azure-cli"></a>Gerenciar discos do Azure com o Azure CLI

Máquinas virtuais do Azure usam discos para armazenar o sistema operacional de VMs, aplicativos e dados. Ao criar uma VM, é importante escolher um tamanho de disco e a configuração apropriada para a carga de trabalho esperada. Este tutorial aborda a implantação e gerenciamento de discos de VM. Você saberá mais sobre:

> [!div class="checklist"]
> * Discos de sistema operacional e discos temporários
> * Discos de dados
> * Discos Standard e Premium
> * Desempenho do disco
> * Anexar e preparar os discos de dados
> * Redimensionamento de discos
> * Instantâneos de disco


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá que você execute a CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

## <a name="default-azure-disks"></a>Discos padrão do Azure

Quando uma máquina virtual do Azure é criada, dois discos são automaticamente anexados à máquina virtual. 

**Disco do sistema operacional**: os discos do sistema operacional podem ser dimensionados para até 1 terabyte e hospedar o sistema operacional das máquinas virtuais. O disco do sistema operacional é rotulado */dev/sda* por padrão. A configuração de cache do disco do SO é otimizada para desempenho do SO. Devido a essa configuração, o disco do sistema operacional **não deve** hospedar aplicativos ou dados. Para aplicativos e dados, utilize um disco de dados, que é detalhado posteriormente neste artigo. 

**Disco temporário** - Discos temporários utilizam uma unidade de estado sólido localizada no mesmo host do Azure que a máquina virtual. Os discos temporários são altamente eficazes e podem ser usados para operações como o processamento de dados temporário. No entanto, se a VM for movida para um novo host, todos os dados armazenados em um disco temporário serão removidos. O tamanho do disco temporário é determinado pelo tamanho da máquina virtual. Os discos temporários são rotulados */dev/sdb* e têm um ponto de montagem de */mnt*.

### <a name="temporary-disk-sizes"></a>Tamanhos do disco temporário

| Tipo | Tamanho da VM | Tamanho máximo do disco temporário (GB) |
|----|----|----|
| [Propósito geral](sizes-general.md) | Série A e D | 800 |
| [Computação otimizada](sizes-compute.md) | Série F | 800 |
| [Memória otimizada](../virtual-machines-windows-sizes-memory.md) | Série D e G | 6144 |
| [Armazenamento otimizado](../virtual-machines-windows-sizes-storage.md) | Série L | 5630 |
| [GPU](sizes-gpu.md) | Série N | 1440 |
| [Alto desempenho](sizes-hpc.md) | Séries A e H | 2000 |

## <a name="azure-data-disks"></a>Discos de dados do Azure

Os discos de dados extras podem ser adicionados para instalação de aplicativos e armazenamento de dados. Os discos de dados devem ser usados em qualquer situação onde o armazenamento de dados durável e responsivo é desejado. Cada disco de dados tem uma capacidade máxima de 1 terabyte. O tamanho da máquina virtual determina quantos discos de dados podem ser anexados a uma VM. Para cada núcleo da VM, podem ser anexados dois discos de dados. 

### <a name="max-data-disks-per-vm"></a>Máximo de discos de dados por VM

| Tipo | Tamanho da VM | Máximo de discos de dados por VM |
|----|----|----|
| [Propósito geral](sizes-general.md) | Série A e D | 32 |
| [Computação otimizada](sizes-compute.md) | Série F | 32 |
| [Memória otimizada](../virtual-machines-windows-sizes-memory.md) | Série D e G | 64 |
| [Armazenamento otimizado](../virtual-machines-windows-sizes-storage.md) | Série L | 64 |
| [GPU](sizes-gpu.md) | Série N | 48 |
| [Alto desempenho](sizes-hpc.md) | Série A e H | 32 |

## <a name="vm-disk-types"></a>Tipos de disco da máquina virtual

O Azure fornece dois tipos de disco.

### <a name="standard-disk"></a>Disco Standard

Armazenamento padrão é apoiado por HDDs e oferece armazenamento econômico e eficaz. Os discos Standard são ideais para uma carga de trabalho econômica de desenvolvimento e teste.

### <a name="premium-disk"></a>Disco Premium

Os discos Premium são apoiados por disco de baixa latência e alto desempenho baseado em SSD. Perfeitos para VMs que executam carga de trabalho de produção. O Armazenamento Premium dá suporte às VMs das séries DS, DSv2, GS e FS. Os discos Premium são apresentados em três tipos (P10, P20 e P30), o tamanho do disco determina o tipo de disco. Na escolha do tamanho de um disco, o valor é arredondado para o próximo tipo. Por exemplo, se o tamanho do disco for menor que 128 GB, o tipo de disco é P10. Se o tamanho do disco está entre 129 e 512 GB, o tamanho é P20. Não mais que 512 GB, o tamanho é um P30.

### <a name="premium-disk-performance"></a>Desempenho do disco Premium

|Tipo de disco de armazenamento Premium | P10 | P20 | P30 |
| --- | --- | --- | --- |
| Tamanho do disco (arredondado) | 128 GB | 512 GB | 1.024 GB (1 TB) |
| IOPS máxima por disco | 500 | 2.300 | 5.000 |
Taxa de transferência por disco | 100 MB/s | 150 MB/s | 200 MB/s |

Embora a tabela acima identifique a IOPS máxima por disco, um nível mais alto de desempenho pode ser obtido com a distribuição de vários discos de dados. Por exemplo, uma VM Standard_GS5 pode atingir o máximo de 80.000 IOPS. Para obter informações detalhadas sobre o máximo de IOPS por VM, veja [Tamanhos da VM Linux](sizes.md).

## <a name="create-and-attach-disks"></a>Criar e anexar discos

Discos de dados podem ser criados e anexados no momento da criação de VM ou a uma VM existente.

### <a name="attach-disk-at-vm-creation"></a>Anexar disco na criação da VM

Crie um grupo de recursos com o comando [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create). 

```azurecli-interactive 
az group create --name myResourceGroupDisk --location eastus
```

Crie uma máquina virtual com o comando [az vm create]( /cli/azure/vm#create). O `--datadisk-sizes-gb` argumento é utilizado para especificar que um disco adicional deve ser criado e anexado à máquina virtual. Para criar e anexar mais de um disco, utilize uma lista delimitada por espaço dos valores de tamanho de disco. No exemplo a seguir, uma VM é criada com dois discos de dados, ambos os 128 GB. Como os tamanhos de disco são 128 GB, esses discos são configurados como P10, que fornecem o máximo de 500 IOPS por disco.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroupDisk \
  --name myVM \
  --image UbuntuLTS \
  --size Standard_DS2_v2 \
  --data-disk-sizes-gb 128 128 \
  --generate-ssh-keys
```

### <a name="attach-disk-to-existing-vm"></a>Anexar disco à máquina virtual existente

Para criar e anexar um novo disco a uma máquina virtual existente, utilize o comando [az vm disk attach](/cli/azure/vm/disk#attach). O exemplo a seguir cria um disco premium de 128 gigabytes de tamanho e anexa-o à VM criada na última etapa.

```azurecli-interactive 
az vm disk attach --vm-name myVM --resource-group myResourceGroupDisk --disk myDataDisk --size-gb 128 --sku Premium_LRS --new 
```

## <a name="prepare-data-disks"></a>Preparar discos de dados

Depois que um disco é anexado à máquina virtual, o sistema operacional precisa ser configurado para usar o disco. O exemplo a seguir mostra como configurar um disco manualmente. Esse processo também pode ser automatizado utilizando a inicialização por nuvem, que é abordada em um [tutorial posterior](./tutorial-automate-vm-deployment.md).

### <a name="manual-configuration"></a>Configuração manual

Crie uma conexão SSH com a máquina virtual. Substitua o endereço IP de exemplo pelo endereço IP público de sua máquina virtual.

```azurecli-interactive 
ssh 52.174.34.95
```

Particione o disco com `fdisk`.

```bash
(echo n; echo p; echo 1; echo ; echo ; echo w) | sudo fdisk /dev/sdc
```

Grave um sistema de arquivos na partição utilizando o comando `mkfs`.

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Monte o novo disco para que ele seja acessível no sistema operacional.

```bash
sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
```

O disco agora pode ser acessado por meio do ponto de montagem *datadrive*, que pode ser verificado ao executar o comando `df -h`. 

```bash
df -h
```

A saída mostra a nova unidade montada em */datadrive*.

```bash
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        30G  1.4G   28G   5% /
/dev/sdb1       6.8G   16M  6.4G   1% /mnt
/dev/sdc1        50G   52M   47G   1% /datadrive
```

Para garantir que a unidade seja remontada após uma reinicialização, ela deve ser adicionada ao arquivo */etc/fstab*. Para fazer isso, obtenha o UUID do disco com o `blkid` utilitário.

```bash
sudo -i blkid
```

A saída exibe o UUID da unidade, `/dev/sdc1` nesse caso.

```bash
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

Adicione uma linha semelhante à seguinte para o arquivo */etc/fstab*. Observe também que as barreiras de gravação podem ser desabilitadas utilizando *barrier=0*, essa configuração pode melhorar o desempenho do disco. 

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive  ext4    defaults,nofail,barrier=0   1  2
```

Agora que o disco foi configurado, feche a sessão SSH.

```bash
exit
```

## <a name="resize-vm-disk"></a>Redimensionar o disco da máquina virtual

Após a implantação de uma máquina virtual, o disco do sistema operacional ou quaisquer discos de dados anexados podem aumentar de tamanho. Aumentar o tamanho de um disco é útil quando é necessário mais espaço de armazenamento ou um nível mais alto de desempenho (P10, P20 e P30). Observe que discos não podem ser reduzidos.

Antes de aumentar o tamanho de um disco, é necessário ter a ID ou o nome do disco. Utilize o comando [az disk list](/cli/azure/disk#az_disk_list) para retornar todos os discos em um grupo de recursos. Anote o nome do disco que você deseja redimensionar.

```azurecli-interactive 
az disk list -g myResourceGroupDisk --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' --output table
```

A máquina vitual também deve ser desalocada. Utilize o comando [az vm deallocate]( /cli/azure/vm#deallocate) para parar e desalocar a máquina virtual.

```azurecli-interactive 
az vm deallocate --resource-group myResourceGroupDisk --name myVM
```

Utilize o comando [az disk update](/cli/azure/vm/disk#update) para redimensionar o disco. Este exemplo redimensiona um disco chamado *myDataDisk* para 1 terabyte.

```azurecli-interactive 
az disk update --name myDataDisk --resource-group myResourceGroupDisk --size-gb 1023
```

Concluída a operação de redimensionamento, inicie a máquina virtual.

```azurecli-interactive 
az vm start --resource-group myResourceGroupDisk --name myVM
```

Se o disco do sistema operacional foi redimensionado, a partição é expandida automaticamente. Se você tiver redimensionado um disco de dados, as partições atuais precisam ser expandidas no sistema operacional das máquinas virtuais.

## <a name="snapshot-azure-disks"></a>Discos padrão do Azure

Criar um instantâneo cria uma cópia, de apenas leitura, de um ponto no tempo do disco. Instantâneos de máquina virtual do Azure são úteis para salvar rapidamente o estado de uma máquina virtual antes de fazer alterações de configuração. No caso das alterações de configuração se mostrarem indesejadas, o estado da máquina virtual pode ser restaurado usando o instantâneo. Quando uma máquina virtual tem mais de um disco, um instantâneo é tirado de cada disco independentemente dos outros. Para fazer backups consistentes de aplicativos, considere parar a máquina virtual antes de tirar instantâneos de disco. Como alternativa, utilize o [serviço de Backup do Azure](/azure/backup/), que permite realizar backups automáticos, enquanto a máquina virtual está em execução.

### <a name="create-snapshot"></a>Como criar um instantâneo

Antes de criar um instantâneo de disco da máquina vitual, você deve ter a ID ou o nome do disco. Utilize o comando [az vm show](https://docs.microsoft.com/en-us/cli/azure/vm#az_vm_show) para obter a Id do disco. Neste exemplo, a Id do disco é armazenada em uma variável e utilizada em uma etapa posterior.

```azurecli-interactive 
osdiskid=$(az vm show -g myResourceGroupDisk -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
```

Agora que você tem a ID do disco da máquina virtual, o comando a seguir criará o instantâneo.

```azurcli
az snapshot create -g myResourceGroupDisk --source "$osdiskid" --name osDisk-backup
```

### <a name="create-disk-from-snapshot"></a>Como criar o disco a partir de um instantâneo

Esse instantâneo pode ser convertido em um disco, que pode ser usado para recriar a máquina virtual.

```azurecli-interactive 
az disk create --resource-group myResourceGroupDisk --name mySnapshotDisk --source osDisk-backup
```

### <a name="restore-virtual-machine-from-snapshot"></a>Como restaurar a máquina virtual a partir de um instantâneo

Para demonstrar a recuperação da máquina virtual, exclua a máquina virtual existente. 

```azurecli-interactive 
az vm delete --resource-group myResourceGroupDisk --name myVM
```

Crie uma nova máquina virtual a partir do disco de instantâneo.

```azurecli-interactive 
az vm create --resource-group myResourceGroupDisk --name myVM --attach-os-disk mySnapshotDisk --os-type linux
```

### <a name="reattach-data-disk"></a>Reanexar um disco de dados

Todos os discos de dados precisam ser anexados novamente à máquina virtual.

Primeiro, utilize o comando [az disk list](https://docs.microsoft.com/cli/azure/disk#az_disk_list) para encontrar o nome do disco de dados. Este exemplo coloca o nome do disco em uma variável chamada *datadisk*, que será usada na próxima etapa.

```azurecli-interactive 
datadisk=$(az disk list -g myResourceGroupDisk --query "[?contains(name,'myVM')].[name]" -o tsv)
```

Use o comando [az vm disk attach](https://docs.microsoft.com/cli/azure/vm/disk#az_vm_disk_attach) para anexar o disco.

```azurecli-interactive 
az vm disk attach –g myResourceGroupDisk –-vm-name myVM –-disk $datadisk
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos de discos da VM como:

> [!div class="checklist"]
> * Discos de sistema operacional e discos temporários
> * Discos de dados
> * Discos Standard e Premium
> * Desempenho do disco
> * Anexar e preparar os discos de dados
> * Redimensionamento de discos
> * Instantâneos de disco

Vá para o próximo tutorial para saber como automatizar a configuração da máquina virtual.

> [!div class="nextstepaction"]
> [Automatizar a configuração da VM](./tutorial-automate-vm-deployment.md)
