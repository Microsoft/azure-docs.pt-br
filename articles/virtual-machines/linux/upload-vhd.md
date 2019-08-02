---
title: Carregar ou copiar uma VM do Linux personalizada com a CLI do Azure | Microsoft Docs
description: Carregue ou copie uma máquina virtual personalizada usando o modelo de implantação do Gerenciador de Recursos e a CLI do Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/17/2018
ms.author: cynthn
ms.openlocfilehash: 026cab6a5749f556d6f748c80e492d1c920767d1
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708402"
---
# <a name="create-a-linux-vm-from-a-custom-disk-with-the-azure-cli"></a>Criar uma VM Linux usando um disco personalizado com a CLI do Azure

<!-- rename to create-vm-specialized -->

Este artigo mostra como carregar um disco rígido virtual (VHD) personalizado e como copiar um VHD existente no Azure. O VHD recém-criado, em seguida, é usado para criar novas máquinas de virtuais do Linux (VMs). Você pode instalar e configurar uma distribuição do Linux conforme suas necessidades e então usar o VHD para criar rapidamente uma nova máquina virtual do Azure.

Para criar várias VMs usando seu disco personalizado, primeiro crie uma imagem da VM ou VHD. Para obter mais informações, consulte [Criar uma imagem personalizada de uma VM do Azure usando a CLI](tutorial-custom-images.md).

Você tem duas opções para criar um disco personalizado:
* Carregar um VHD
* Copiar uma VM existente do Azure

## <a name="quick-commands"></a>Comandos rápidos

Ao criar uma nova VM com [criar vm az](/cli/azure/vm#az-vm-create) de um disco especializado ou personalizado, você **anexa** o disco (-attach-os-disk) em vez de especificar uma imagem do marketplace ou personalizada (-imagem). O exemplo a seguir cria uma VM denominada *myVM* usando o disco gerenciado denominado *myManagedDisk* criado com base no seu VHD personalizado:

```azurecli
az vm create --resource-group myResourceGroup --location eastus --name myVM \
   --os-type linux --attach-os-disk myManagedDisk
```

## <a name="requirements"></a>Requisitos
Para concluir as etapas a seguir, você precisará de:

* Uma máquina virtual Linux que foi preparada para uso no Azure. A seção [Preparar a VM](#prepare-the-vm) deste artigo aborda como encontrar informações específicas da distribuição sobre como instalar o agente de Linux do Azure (waagent), que é necessário para que você possa se conectar a uma VM com SSH.
* O arquivo VHD de uma [distribuição do Linux aprovada pelo Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) já existente (ou consulte [informações para distribuições não aprovadas](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) em um disco virtual no formato VHD. Existem várias ferramentas para criar uma VM e o VHD:
  * Instalar e configurar o [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) ou o [KVM](https://www.linux-kvm.org/page/RunningKVM), tomando o cuidado de usar o VHD como seu formato de imagem. Se necessário, você pode [converter uma imagem](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) com `qemu-img convert`.
  * Também pode usar o Hyper-V [no Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) ou [no Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Não há suporte para o formato VHDX mais recente no Azure. Ao criar uma VM, especifique VHD como o formato. Se necessário, você pode converter discos VHDX em VHD com o [converter qemu-img](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) ou o [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) cmdlet do PowerShell. O Azure não dá suporte ao carregamento de VHDs dinâmicos, por isso você precisa converter esses discos para VHDs estáticos antes de carregar. Você pode usar ferramentas como [Azure VHD Utilities for GO](https://github.com/Microsoft/azure-vhd-utils-for-go) para converter discos dinâmicos durante o processo de carregamento no Azure.
> 
> 


* Certifique-se de que você tenha a versão mais recente [CLI do Azure](/cli/azure/install-az-cli2) instalado e você está conectado a uma conta do Azure com [login az](/cli/azure/reference-index#az-login).

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores, tais como *myResourceGroup*, *mystorageaccount*, e *mydisks*.

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>Preparar a VM

O Azure dá suporte a várias distribuições do Linux (consulte [Distribuições endossadas](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Os artigos a seguir descrevem como preparar as diversas distribuições do Linux que têm suporte no Azure:

* [Distribuições com base em CentOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SLES e openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Outros: Distribuições não endossadas](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Confira também as [Observações de instalação do Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais dicas gerais sobre como preparar as imagens do Linux para o Azure.

> [!NOTE]
> O [SLA da plataforma Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) se aplica às VMs que executam o Linux somente quando uma das distribuições endossadas é usada com os detalhes da configuração, conforme especificado na seção “Versões com suporte” em [Linux em distribuições endossadas pelo Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="option-1-upload-a-vhd"></a>Opção 1: Carregar um VHD

Você pode carregar um VHD personalizado que esteja em execução em um computador local ou que você tenha exportado de outra nuvem. Para usar o VHD para criar uma nova VM do Azure, você precisa carregar o VHD para uma conta de armazenamento e criar um disco gerenciado com base no VHD. Para saber mais, veja [Visão geral dos Azure Managed Disks](../windows/managed-disks-overview.md).

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de carregar seu disco personalizado e criar VMs, você precisa criar um grupo de recursos com [az group create](/cli/azure/group#az-group-create).

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```azurecli
az group create \
    --name myResourceGroup \
    --location eastus
```

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Criar uma conta de armazenamento para seu disco personalizado e VMs com [az storage account create](/cli/azure/storage/account). O exemplo a seguir cria uma conta de armazenamento denominada *mystorageaccount* no grupo de recursos criado anteriormente:

```azurecli
az storage account create \
    --resource-group myResourceGroup \
    --location eastus \
    --name mystorageaccount \
    --kind Storage \
    --sku Standard_LRS
```

### <a name="list-storage-account-keys"></a>Listar chaves da conta de armazenamento
O Azure gera duas chaves de acesso de 512 bits para cada conta de armazenamento. Essas chaves de acesso são usadas ao autenticar a conta de armazenamento, como quando realização de operações de gravação. Para obter mais informações, consulte [Gerenciando o acesso a armazenamento](../../storage/common/storage-account-manage.md#access-keys). 

Exibir as chaves de acesso com [lista de chaves de conta de armazenamento az](/cli/azure/storage/account/keys#az-storage-account-keys-list). Por exemplo, para exibir as chaves de acesso para o armazenamento de conta que você criou:

```azurecli
az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount
```

A saída deverá ser semelhante a:

```azurecli
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK
```
Tome nota da **key1**, pois você a usará para interagir com sua conta de armazenamento nas próximas etapas.

### <a name="create-a-storage-container"></a>Criar um contêiner de armazenamento
Da mesma forma que você cria diferentes diretórios para organizar logicamente seu sistema de arquivos local, crie contêineres em uma conta de armazenamento para organizar seus discos. Uma conta de armazenamento pode conter vários contêineres. Crie um contêiner com [az storage container create](/cli/azure/storage/container#az-storage-container-create).

O seguinte exemplo cria um contêiner chamado *mydisks*:

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

### <a name="upload-the-vhd"></a>Carregar o VHD
Carregue seu disco personalizado com [az storage blob upload](/cli/azure/storage/blob#az-storage-blob-upload). Carregue e armazene seu disco personalizado como um blob de página.

Especifique sua chave de acesso, o contêiner que você criou na etapa anterior e o caminho para o disco personalizado no seu computador local:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 \
    --container-name mydisks \
    --type page \
    --file /path/to/disk/mydisk.vhd \
    --name myDisk.vhd
```
Carregar o VHD pode demorar um pouco.

### <a name="create-a-managed-disk"></a>Criar um disco gerenciado


Criar um disco gerenciado do VHD com [az disk create](/cli/azure/disk#az-disk-create). O exemplo a seguir cria um disco gerenciado chamado *myManagedDisk* usando o VHD carregado para o contêiner e a conta de armazenamento nomeados:

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
  --source https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd
```
## <a name="option-2-copy-an-existing-vm"></a>Opção 2: Copiar uma VM existente

Você pode também criar uma VM personalizada no Azure e, em seguida, copie o disco do sistema operacional e anexe-o a uma nova VM para criar outra cópia. Isso é bom para teste, mas se você quiser usar uma VM do Azure existente como modelo para várias novas VMs, crie uma *imagem* em vez disso. Para obter mais informações sobre como criar uma imagem de uma VM do Azure existente, consulte [Criar uma imagem personalizada de uma VM do Azure usando a CLI](tutorial-custom-images.md).

### <a name="create-a-snapshot"></a>Criar um instantâneo

Este exemplo cria um instantâneo de uma VM denominada *myVM* no grupo de recursos *myResourceGroup* e cria um instantâneo denominado *osDiskSnapshot*.

```azure-cli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>Criar o disco gerenciado

Crie um novo disco gerenciado com base no instantâneo.

Obtenha a ID do instantâneo. Neste exemplo, o instantâneo é denominado *osDiskSnapshot* e está no grupo de recursos *myResourceGroup*.

```azure-cli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

Criar o disco gerenciado. Neste exemplo, vamos criar um disco gerenciado chamado *myManagedDisk* em nosso instantâneo, onde o disco está no armazenamento standard e dimensionados em 128 GB.

```azure-cli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>Criar a VM

Crie a VM com [az vm create](/cli/azure/vm#az-vm-create) e anexe (--attach-os-disk) o disco gerenciado como o disco do sistema operacional. O exemplo a seguir cria uma VM denominada *myNewVM* usando o disco gerenciado criado com base no seu VHD carregado:

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
Depois de preparar e carregar seu disco virtual personalizado, leia mais sobre como [usar o Resource Manager e os modelos](../../azure-resource-manager/resource-group-overview.md). É recomendável [adicionar um disco de dados](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) às novas VMs. Se você tiver aplicativos em execução nas VMs que precisa acessar, não se esqueça de [abrir as portas e os pontos de extremidade](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
