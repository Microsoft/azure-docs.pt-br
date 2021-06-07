---
title: Tutorial – Gerenciar discos do Azure com o Azure CLI
description: Neste tutorial, você aprenderá a usar a CLI do Azure para criar e gerenciar discos do Azure para máquinas virtuais
author: cynthn
ms.service: virtual-machines
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 08/20/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.subservice: disks
ms.openlocfilehash: 56e804bc0d479f09ef2900c42361fbd24eed1d98
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765945"
---
# <a name="tutorial---manage-azure-disks-with-the-azure-cli"></a>Tutorial – Gerenciar discos do Azure com o Azure CLI

Máquinas virtuais (VMs) do Azure usam discos para armazenar o sistema operacional, aplicativos e dados. Ao criar uma VM, é importante escolher um tamanho de disco e a configuração apropriada para a carga de trabalho esperada. Este tutorial mostra como implantar e gerenciar os discos de VM. Você saberá mais sobre:

> [!div class="checklist"]
> * Discos de sistema operacional e discos temporários
> * Discos de dados
> * Discos Standard e Premium
> * Desempenho do disco
> * Anexar e preparar os discos de dados
> * Instantâneos de disco


## <a name="default-azure-disks"></a>Discos padrão do Azure

Quando uma máquina virtual do Azure é criada, dois discos são automaticamente anexados à máquina virtual.

**Disco do sistema operacional** - Os discos do sistema operacional podem ser dimensionados para até 2 TB e hospedar o sistema operacional das máquinas virtuais. O disco do sistema operacional é rotulado */dev/sda* por padrão. A configuração de cache do disco do SO é otimizada para desempenho do SO. Devido a essa configuração, o disco do sistema operacional **não deve** ser usado para aplicativos ou dados. Para aplicativos e dados, utilize um disco de dados, que é detalhado posteriormente neste tutorial.

**Disco temporário** - Discos temporários utilizam uma unidade de estado sólido localizada no mesmo host do Azure que a máquina virtual. Os discos temporários são altamente eficazes e podem ser usados para operações como o processamento de dados temporário. No entanto, se a VM for movida para um novo host, todos os dados armazenados em um disco temporário serão removidos. O tamanho do disco temporário é determinado pelo tamanho da máquina virtual. Os discos temporários são rotulados */dev/sdb* e têm um ponto de montagem de */mnt*.

## <a name="azure-data-disks"></a>Discos de dados do Azure

Para instalar aplicativos e dados de armazenamento, outros discos de dados podem ser adicionados. Os discos de dados devem ser usados em qualquer situação onde o armazenamento de dados durável e responsivo é desejado. O tamanho da máquina virtual determina quantos discos de dados podem ser anexados a uma VM.

## <a name="vm-disk-types"></a>Tipos de disco da máquina virtual

O Azure fornece dois tipos de discos.

**Discos Standard** - apoiados por HDDs e oferecem armazenamento econômico e eficaz. Os discos Standard são ideais para uma carga de trabalho econômica de desenvolvimento e teste.

**Discos Premium** – apoiados por disco de baixa latência e alto desempenho baseado em SSD. Perfeitos para VMs que executam carga de trabalho de produção. Os tamanhos de VM com um **S** no [nome do tamanho](../vm-naming-conventions.md) normalmente são compatíveis com o Armazenamento Premium. Por exemplo, as VMs das séries DS, DSv2, GS e FS são compatíveis com o Armazenamento Premium. Ao escolher o tamanho de um disco, o valor é arredondado para o tipo seguinte. Por exemplo, se o tamanho do disco for maior que 64 GB, mas menor que 128 GB, o tipo de disco será P10. 

<br>


[!INCLUDE [disk-storage-premium-ssd-sizes](../../../includes/disk-storage-premium-ssd-sizes.md)]

Quando você provisiona um disco de armazenamento premium, ao contrário do armazenamento padrão, a capacidade, IOPS e taxa de transferência de disco são garantidos. Por exemplo, se você criar um disco P50, o Azure provisionará uma capacidade de armazenamento de 4.095 GB, 7.500 IOPS e uma taxa de transferência de 250 MB/s para o disco. O aplicativo pode usar a capacidade e o desempenho no todo ou em parte. Os discos SSD Premium são projetados para fornecer as latências baixas de milissegundos de dígito único e a IOPS de destino e a taxa de transferência descritas na tabela anterior 99,9% do tempo.

Embora a tabela acima identifique a IOPS máxima por disco, um nível mais alto de desempenho pode ser obtido com a distribuição de vários discos de dados. Por exemplo, 64 discos de dados podem ser anexados à VM Standard_GS5. Se cada um desses discos for dimensionado como um P30, será possível chegar a um máximo de 80.000 IOPS. Para obter informações detalhadas sobre o máximo de IOPS por VM, veja [Tipos e tamanhos de VM](../sizes.md).

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta.

Para abrir o Cloud Shell, selecione **Experimentar** no canto superior direito de um bloco de código. Você também pode iniciar o Cloud Shell em uma guia separada do navegador indo até [https://shell.azure.com/powershell](https://shell.azure.com/bash). Selecione **Copiar** para copiar os blocos de código, cole o código no Cloud Shell e depois pressione Enter para executá-lo.

## <a name="create-and-attach-disks"></a>Criar e anexar discos

Discos de dados podem ser criados e anexados no momento da criação de VM ou a uma VM existente.

### <a name="attach-disk-at-vm-creation"></a>Anexar disco na criação da VM

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az_group_create).

```azurecli-interactive
az group create --name myResourceGroupDisk --location eastus
```

Crie uma máquina virtual com o comando [az vm create](/cli/azure/vm#az_vm_create). O exemplo a seguir cria uma VM chamada *myVM*, adiciona uma conta de usuário chamada *azureuser* e gera as chaves SSH, caso ainda não existam. O `--datadisk-sizes-gb` argumento é utilizado para especificar que um disco adicional deve ser criado e anexado à máquina virtual. Para criar e anexar mais de um disco, utilize uma lista delimitada por espaço dos valores de tamanho de disco. No exemplo a seguir, uma VM é criada com dois discos de dados, ambos os 128 GB. Como os tamanhos de disco são 128 GB, esses discos são configurados como P10, que fornecem o máximo de 500 IOPS por disco.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupDisk \
  --name myVM \
  --image UbuntuLTS \
  --size Standard_DS2_v2 \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 128 128
```

### <a name="attach-disk-to-existing-vm"></a>Anexar disco à máquina virtual existente

Para criar e anexar um novo disco a uma máquina virtual existente, utilize o comando [az vm disk attach](/cli/azure/vm/disk#az_vm_disk_attach). O exemplo a seguir cria um disco premium de 128 gigabytes de tamanho e anexa-o à VM criada na última etapa.

```azurecli-interactive
az vm disk attach \
    --resource-group myResourceGroupDisk \
    --vm-name myVM \
    --name myDataDisk \
    --size-gb 128 \
    --sku Premium_LRS \
    --new
```

## <a name="prepare-data-disks"></a>Preparar discos de dados

Depois que um disco é anexado à máquina virtual, o sistema operacional precisa ser configurado para usar o disco. O exemplo a seguir mostra como configurar um disco manualmente. Esse processo também pode ser automatizado utilizando a inicialização por nuvem, que é abordada em um [tutorial posterior](./tutorial-automate-vm-deployment.md).


Crie uma conexão SSH com a máquina virtual. Substitua o endereço IP de exemplo pelo endereço IP público de sua máquina virtual.

```console
ssh 10.101.10.10
```

Particione o disco com `parted`.

```bash
sudo parted /dev/sdc --script mklabel gpt mkpart xfspart xfs 0% 100%
```

Grave um sistema de arquivos na partição utilizando o comando `mkfs`. Use `partprobe` para tornar o sistema operacional ciente da alteração.

```bash
sudo mkfs.xfs /dev/sdc1
sudo partprobe /dev/sdc1
```

Monte o novo disco para que ele seja acessível no sistema operacional.

```bash
sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
```

O disco agora pode ser acessado por meio do ponto de montagem `/datadrive`, que pode ser verificado ao executar o comando `df -h`.

```bash
df -h | grep -i "sd"
```

A saída mostra a nova unidade montada em `/datadrive`.

```bash
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        29G  2.0G   27G   7% /
/dev/sda15      105M  3.6M  101M   4% /boot/efi
/dev/sdb1        14G   41M   13G   1% /mnt
/dev/sdc1        50G   52M   47G   1% /datadrive
```

Para garantir que a unidade seja remontada após uma reinicialização, ela deve ser adicionada ao arquivo */etc/fstab*. Para fazer isso, obtenha o UUID do disco com o `blkid` utilitário.

```bash
sudo -i blkid
```

A saída exibe o UUID da unidade, `/dev/sdc1` nesse caso.

```bash
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="xfs"
```

> [!NOTE]
> A edição inadequada do arquivo **/etc/fstab** pode resultar em um sistema não inicializável. Se não tiver certeza, consulte a documentação de distribuição para obter informações sobre como editá-lo corretamente. Também é recomendável que um backup do arquivo /etc/fstab seja criado antes da edição.

Abra o arquivo `/etc/fstab` em um editor de texto conforme a seguir:

```bash
sudo nano /etc/fstab
```

Adicione uma linha semelhante a esta ao arquivo */etc/fstab*, substituindo o valor UUID pelo seu.

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive  xfs    defaults,nofail   1  2
```

Quando terminar de editar o arquivo, use `Ctrl+O` para gravar o arquivo e `Ctrl+X` para sair do editor.

Agora que o disco foi configurado, feche a sessão SSH.

```bash
exit
```

## <a name="take-a-disk-snapshot"></a>Tirar um instantâneo do disco

Ao criar um instantâneo do disco, o Azure cria uma cópia do disco de apenas leitura, de um ponto no tempo. Instantâneos de máquina virtual do Azure são úteis para salvar rapidamente o estado de uma máquina virtual antes de fazer alterações de configuração. No caso de um problema ou erro, a VM pode ser restaurada usando um instantâneo. Quando uma máquina virtual tem mais de um disco, um instantâneo é tirado de cada disco independentemente dos outros. Para fazer backups consistentes de aplicativos, considere parar a máquina virtual antes de tirar instantâneos de disco. Como alternativa, utilize o [serviço de Backup do Azure](../../backup/index.yml), que permite realizar backups automáticos, enquanto a máquina virtual está em execução.

### <a name="create-snapshot"></a>Create snapshot

Antes de criar um instantâneo, você precisará da ID ou do nome do disco. Use [az vm show](/cli/azure/vm#az_vm_show) para capturar a ID do disco. Neste exemplo, a ID do disco é armazenada em uma variável e utilizada em uma etapa posterior.

```azurecli-interactive
osdiskid=$(az vm show \
   -g myResourceGroupDisk \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Agora que você tem a ID, use [az snapshot create](/cli/azure/snapshot#az_snapshot_create) para criar um instantâneo do disco.

```azurecli-interactive
az snapshot create \
    --resource-group myResourceGroupDisk \
    --source "$osdiskid" \
    --name osDisk-backup
```

### <a name="create-disk-from-snapshot"></a>Como criar o disco a partir de um instantâneo

Esse instantâneo pode ser convertido em um disco usando [az disk create](/cli/azure/disk#az_disk_create), que pode ser usado para recriar a máquina virtual.

```azurecli-interactive
az disk create \
   --resource-group myResourceGroupDisk \
   --name mySnapshotDisk \
   --source osDisk-backup
```

### <a name="restore-virtual-machine-from-snapshot"></a>Como restaurar a máquina virtual a partir de um instantâneo

Para demonstrar a recuperação da máquina virtual, exclua a máquina virtual existente usando [az vm delete](/cli/azure/vm#az_vm_delete).

```azurecli-interactive
az vm delete \
--resource-group myResourceGroupDisk \
--name myVM
```

Crie uma nova máquina virtual a partir do disco de instantâneo.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupDisk \
    --name myVM \
    --attach-os-disk mySnapshotDisk \
    --os-type linux
```

### <a name="reattach-data-disk"></a>Reanexar um disco de dados

Todos os discos de dados precisam ser anexados novamente à máquina virtual.

Use o comando [az disk list](/cli/azure/disk#az_disk_list) para localizar o nome do disco de dados. Este exemplo coloca o nome do disco em uma variável chamada `datadisk`, que será usada na próxima etapa.

```azurecli-interactive
datadisk=$(az disk list \
   -g myResourceGroupDisk \
   --query "[?contains(name,'myVM')].[id]" \
   -o tsv)
```

Use o comando [az vm disk attach](/cli/azure/vm/disk#az_vm_disk_attach) para anexar o disco.

```azurecli-interactive
az vm disk attach \
   –g myResourceGroupDisk \
   --vm-name myVM \
   --name $datadisk
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos de discos da VM como:

> [!div class="checklist"]
> * Discos de sistema operacional e discos temporários
> * Discos de dados
> * Discos Standard e Premium
> * Desempenho do disco
> * Anexar e preparar os discos de dados
> * Instantâneos de disco

Vá para o próximo tutorial para saber como automatizar a configuração da máquina virtual.

> [!div class="nextstepaction"]
> [Automatizar a configuração da VM](./tutorial-automate-vm-deployment.md)
