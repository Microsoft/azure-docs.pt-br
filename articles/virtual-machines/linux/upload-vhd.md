---
title: Carregar ou copiar uma VM personalizada do Linux com a CLI do Azure
description: Carregue ou copie uma máquina virtual personalizada usando o modelo de implantação do Gerenciador de Recursos e a CLI do Azure
services: virtual-machines
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: how-to
ms.date: 10/10/2019
ms.author: cynthn
ms.openlocfilehash: ecad48592ecfb6723548a27997cfe1b81545b24a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759591"
---
# <a name="create-a-linux-vm-from-a-custom-disk-with-the-azure-cli"></a>Criar uma VM Linux usando um disco personalizado com a CLI do Azure

<!-- rename to create-vm-specialized -->

Este artigo mostra como carregar um disco rígido virtual (VHD) personalizado e como copiar um VHD existente no Azure. O VHD recém-criado, em seguida, é usado para criar novas máquinas de virtuais do Linux (VMs). Você pode instalar e configurar uma distribuição do Linux conforme suas necessidades e então usar o VHD para criar rapidamente uma nova máquina virtual do Azure.

Para criar várias VMs usando seu disco personalizado, primeiro crie uma imagem da VM ou VHD. Para obter mais informações, consulte [Criar uma imagem personalizada de uma VM do Azure usando a CLI](tutorial-custom-images.md).

Você tem duas opções para criar um disco personalizado:
* Carregar um VHD
* Copiar uma VM existente do Azure


## <a name="requirements"></a>Requisitos
Para concluir as etapas a seguir, você precisará de:

- Uma máquina virtual Linux que foi preparada para uso no Azure. A seção [Preparar a VM](#prepare-the-vm) deste artigo aborda como encontrar informações específicas da distribuição sobre como instalar o agente de Linux do Azure (waagent), que é necessário para que você possa se conectar a uma VM com SSH.
- O arquivo VHD de uma [distribuição do Linux aprovada pelo Azure](endorsed-distros.md) já existente (ou consulte [informações para distribuições não aprovadas](create-upload-generic.md)) em um disco virtual no formato VHD. Existem várias ferramentas para criar uma VM e o VHD:
  - Instalar e configurar o [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) ou o [KVM](https://www.linux-kvm.org/page/RunningKVM), tomando o cuidado de usar o VHD como seu formato de imagem. Se necessário, você pode [converter uma imagem](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) com `qemu-img convert`.
  - Também pode usar o Hyper-V [no Windows 10](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) ou [no Windows Server 2012/2012 R2](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

> [!NOTE]
> Não há suporte para o formato VHDX mais recente no Azure. Ao criar uma VM, especifique VHD como o formato. Se necessário, você pode converter discos VHDX em VHD com o [converter qemu-img](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) ou o [Convert-VHD](/powershell/module/hyper-v/convert-vhd) cmdlet do PowerShell. O Azure não dá suporte ao carregamento de VHDs dinâmicos, por isso você precisa converter esses discos para VHDs estáticos antes de carregar. Você pode usar ferramentas como [Azure VHD Utilities for GO](https://github.com/Microsoft/azure-vhd-utils-for-go) para converter discos dinâmicos durante o processo de carregamento no Azure.
> 
> 


- Certifique-se de que você tenha a versão mais recente [CLI do Azure](/cli/azure/install-az-cli2) instalado e você está conectado a uma conta do Azure com [login az](/cli/azure/reference-index#az_login).

Nos exemplos a seguir, substitua os nomes de parâmetros de exemplo por valores próprios, como `myResourceGroup`, `mystorageaccount` e `mydisks`.

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>Preparar a VM

O Azure dá suporte a várias distribuições do Linux (consulte [Distribuições endossadas](endorsed-distros.md)). Os artigos a seguir descrevem como preparar as diversas distribuições do Linux que têm suporte no Azure:

* [Distribuições com base em CentOS](create-upload-centos.md)
* [Debian Linux](debian-create-upload-vhd.md)
* [Oracle Linux](oracle-create-upload-vhd.md)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
* [SLES e openSUSE](suse-create-upload-vhd.md)
* [Ubuntu](create-upload-ubuntu.md)
* [Outros: Distribuições não endossadas](create-upload-generic.md)

Confira também as [Observações de instalação do Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais dicas gerais sobre como preparar as imagens do Linux para o Azure.

> [!NOTE]
> O [SLA da plataforma Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) se aplica às VMs que executam o Linux somente quando uma das distribuições endossadas é usada com os detalhes da configuração, conforme especificado na seção “Versões com suporte” em [Linux em distribuições endossadas pelo Azure](endorsed-distros.md).
> 
> 

## <a name="option-1-upload-a-vhd"></a>Opção 1: Carregar um VHD

Agora você pode carregar um VHD diretamente em um disco gerenciado. Para obter instruções, confira [Carregar um VHD no Azure usando a CLI do Azure](disks-upload-vhd-to-managed-disk-cli.md).

## <a name="option-2-copy-an-existing-vm"></a>Opção 2: Copiar uma VM existente

Você pode também criar uma VM personalizada no Azure e, em seguida, copie o disco do sistema operacional e anexe-o a uma nova VM para criar outra cópia. Isso é bom para teste, mas se você quiser usar uma VM do Azure existente como modelo para várias novas VMs, crie uma *imagem* em vez disso. Para obter mais informações sobre como criar uma imagem de uma VM do Azure existente, consulte [Criar uma imagem personalizada de uma VM do Azure usando a CLI](tutorial-custom-images.md).

Caso deseje copiar uma VM existente para outra região, use o AzCopy para [criar uma cópia de um disco em outra região](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk). 

Caso contrário, você deverá tirar um instantâneo da VM e criar um VHD do sistema operacional com base no instantâneo.

### <a name="create-a-snapshot"></a>Criar um instantâneo

Este exemplo cria um instantâneo de uma VM denominada *myVM* no grupo de recursos *myResourceGroup* e cria um instantâneo denominado *osDiskSnapshot*.

```azurecli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>Criar o disco gerenciado

Crie um novo disco gerenciado com base no instantâneo.

Obtenha a ID do instantâneo. Neste exemplo, o instantâneo é denominado *osDiskSnapshot* e está no grupo de recursos *myResourceGroup*.

```azurecli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

Criar o disco gerenciado. Neste exemplo, vamos criar um disco gerenciado chamado *myManagedDisk* em nosso instantâneo, onde o disco está no armazenamento standard e dimensionados em 128 GB.

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>Criar a VM

Crie a VM com [az vm create](/cli/azure/vm#az_vm_create) e anexe (--attach-os-disk) o disco gerenciado como o disco do sistema operacional. O exemplo a seguir cria uma VM denominada *myNewVM* usando o disco gerenciado criado com base no seu VHD carregado:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

Você deve ser capaz de fazer SSH para a VM usando as credenciais da VM de origem. 

## <a name="next-steps"></a>Próximas etapas
Depois de preparar e carregar seu disco virtual personalizado, leia mais sobre como [usar o Resource Manager e os modelos](../../azure-resource-manager/management/overview.md). É recomendável [adicionar um disco de dados](add-disk.md) às novas VMs. Se você tiver aplicativos em execução nas VMs que precisa acessar, não se esqueça de [abrir as portas e os pontos de extremidade](nsg-quickstart.md).
