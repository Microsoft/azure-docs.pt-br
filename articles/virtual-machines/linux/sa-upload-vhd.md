---
title: Carregar um disco Linux personalizado com CLI do Azure | Microsoft Docs
description: Criar e carregar um VHD (disco rígido virtual) no Azure usando o modelo de implantação do Resource Manager e a CLI do Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 07/10/2017
ms.author: cynthn
ms.openlocfilehash: 368fec658dd1f063c45f3d00d42a4549ca9dfd83
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771151"
---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-with-the-azure-cli"></a>Carregar e criar uma VM do Linux usando disco personalizado com a CLI do Azure

Este artigo mostra como carregar um VHD (disco rígido virtual) em uma conta de armazenamento do Azure com a CLI do Azure e como criar VMs do Linux com base nesse disco personalizado. Essa funcionalidade permite que você instale e configure uma distribuição do Linux segundo suas necessidades e use esse VHD para criar rapidamente máquinas virtuais (VMs) do Azure.

Este tópico usa contas de armazenamento para os VHDs finais, mas você também pode realizar essas etapas usando [Managed Disks](upload-vhd.md). 

## <a name="quick-commands"></a>Comandos rápidos
Caso você precise realizar rapidamente a tarefa, a seção a seguir detalha os comandos básicos para carregar uma VHD no Azure. Mais informações detalhadas e contexto para cada etapa podem ser encontrados no restante do documento, [começando aqui](#requirements).

Certifique-se de que você tenha instalado a versão mais recente da [CLI do Azure](/cli/azure/install-az-cli2) e entrado em uma conta do Azure usando [az login](/cli/azure/reference-index).

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Os nomes de parâmetro de exemplo incluíram `myResourceGroup`, `mystorageaccount` e `mydisks`.

Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` no local `WestUs`:

```azurecli
az group create --name myResourceGroup --location westus
```

Criar uma conta de armazenamento para armazenar seus discos virtuais com [criar conta de armazenamento az](/cli/azure/storage/account). O exemplo a seguir cria uma conta de armazenamento chamada `mystorageaccount`:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

Lista as chaves de acesso para sua conta de armazenamento com [lista de chaves de conta de armazenamento az](/cli/azure/storage/account/keys). Anote `key1`:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

Crie um contêiner dentro de sua conta de armazenamento usando a chave de armazenamento obtida com [az storage container create](/cli/azure/storage/container). O exemplo a seguir cria um contêiner denominado `mydisks` usando o valor de chave de armazenamento de `key1`:

```azurecli
az storage container create --account-name mystorageaccount \
    --account-key key1 --name mydisks
```

Por fim, carregue o VHD no contêiner criado com [carregamento de blob de armazenamento az](/cli/azure/storage/blob). Especifique o caminho local para o VHD em `/path/to/disk/mydisk.vhd`:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

Especifique o URI para o disco (`--image`) com [az vm create](/cli/azure/vm). O exemplo a seguir cria uma VM denominada `myVM` usando o disco virtual carregado anteriormente:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisk/myDisks.vhd \
    --use-unmanaged-disk
```

A conta de armazenamento de destino deve ser a mesma em que você carregou o disco virtual. Você também precisará especificar todos os parâmetros adicionais necessários ou responder a prompts deles pelo comando **az vm create**, como rede virtual, endereço IP público, nome de usuário e chaves SSH. Leia mais sobre os [parâmetros do Resource Manager na CLI disponíveis](../azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

## <a name="requirements"></a>Requisitos
Para concluir as etapas a seguir, você precisa:

* **Sistema operacional Linux instalado em um arquivo .vhd** - Instale uma [distribuição Linux endossada pelo Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (ou confira as [informações para as distribuições não endossadas](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) em um disco virtual no formato VHD. Existem várias ferramentas para criar uma VM e o VHD:
  * Instalar e configurar o [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) ou o [KVM](https://www.linux-kvm.org/page/RunningKVM), tomando o cuidado de usar o VHD como seu formato de imagem. Se necessário, você pode [converter uma imagem](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) usando `qemu-img convert`.
  * Também pode usar o Hyper-V [no Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) ou [no Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Não há suporte para o formato VHDX mais recente no Azure. Ao criar uma VM, especifique VHD como o formato. Se necessário, será possível converter discos VHDX para VHD usando o cmdlet [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) ou [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) do PowerShell. Além disso, o Azure não dá suporte ao carregamento de VHDs dinâmicos, por isso você precisa converter esses discos para VHDs estáticos antes de carregar. Você pode usar ferramentas como o [Azure VHD Utilities for GO](https://github.com/Microsoft/azure-vhd-utils-for-go) para converter os discos dinâmicos durante o processo de carregamento no Azure.
> 
> 

* As VMs criadas com base em seu disco personalizado devem residir na mesma conta de armazenamento que o disco
  * Criar uma conta de armazenamento e um contêiner para manter o disco personalizado e as VMs criadas
  * Depois de criar todas as VMs, você poderá excluir o disco com segurança

Certifique-se de que você tenha instalado a versão mais recente da [CLI do Azure](/cli/azure/install-az-cli2) e entrado em uma conta do Azure usando [az login](/cli/azure/reference-index).

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Os nomes de parâmetro de exemplo incluíram `myResourceGroup`, `mystorageaccount` e `mydisks`.

<a id="prepimage"> </a>

## <a name="prepare-the-disk-to-be-uploaded"></a>Preparar o disco a ser carregado
O Azure dá suporte a várias distribuições do Linux (consulte [Distribuições endossadas](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Os seguintes artigos explicam como preparar as diversas distribuições Linux com suporte no Azure:

* **[Distribuições com base em CentOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Outros — Distribuições não endossadas](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Veja também as **[Observações de instalação do Linux](create-upload-generic.md#general-linux-installation-notes)** para obter mais dicas gerais sobre como preparar as imagens do Linux para o Azure.

> [!NOTE]
> O [SLA da plataforma Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) se aplica às VMs que executam o Linux somente quando uma das distribuições endossadas é usada com os detalhes da configuração, conforme especificado na seção “Versões com suporte” em [Linux em distribuições endossadas pelo Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Os grupos de recursos reúnem logicamente todos os recursos do Azure para dar suporte às suas máquinas virtuais, como a rede virtual e o armazenamento. Mais informações sobre grupos de recursos, veja [visão geral de grupos de recurso](../../azure-resource-manager/resource-group-overview.md). Antes de carregar seu disco personalizado e criar VMs, primeiro você precisa criar um grupo de recursos com [az group create](/cli/azure/group).

O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` no local `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Criar uma conta de armazenamento para seu disco personalizado e VMs com [az storage account create](/cli/azure/storage/account). Todas as VMs com discos não gerenciados criadas com base no disco personalizado precisam estar na mesma conta de armazenamento que o disco. 

O exemplo a seguir cria uma conta de armazenamento denominada `mystorageaccount` no grupo de recursos criado anteriormente:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

## <a name="list-storage-account-keys"></a>Listar chaves da conta de armazenamento
O Azure gera duas chaves de acesso de 512 bits para cada conta de armazenamento. Essas chaves de acesso são usadas durante a autenticação na conta de armazenamento, por exemplo, para executar operações de gravação. Leia mais sobre [como gerenciar o acesso ao armazenamento aqui](../../storage/common/storage-account-manage.md#access-keys). Exibir as chaves de acesso com [lista de chaves de conta de armazenamento az](/cli/azure/storage/account/keys).

Veja as chaves de acesso da conta de armazenamento que você criou:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
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
Anote `key1` , pois você usará isso para interagir com sua conta de armazenamento nas próximas etapas.

## <a name="create-a-storage-container"></a>Criar um contêiner de armazenamento
Da mesma forma que você cria diferentes diretórios para organizar logicamente seu sistema de arquivos local, crie contêineres em uma conta de armazenamento para organizar seus discos. Uma conta de armazenamento pode conter qualquer quantidade de contêineres. Crie um contêiner com [az storage container create](/cli/azure/storage/container).

O seguinte exemplo cria um contêiner chamado `mydisks`:

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

## <a name="upload-vhd"></a>Carregar o VHD
Agora, carregue seu disco personalizado com [az storage blob upload](/cli/azure/storage/blob). Carregue e armazene seu disco personalizado como um blob de páginas.

Especifique sua chave de acesso, o contêiner que você criou na etapa anterior e o caminho para o disco personalizado no seu computador local:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

## <a name="create-the-vm"></a>Criar a VM
Para criar uma VM com discos não gerenciados, especifique o URI para o disco (`--image`) com [az vm create](/cli/azure/vm). O exemplo a seguir cria uma VM denominada `myVM` usando o disco virtual carregado anteriormente:

Especifique o parâmetro `--image` com [az vm create](/cli/azure/vm) para apontar para o disco personalizado. Verifique se `--storage-account` corresponde à conta de armazenamento em que o disco personalizado está armazenado. Você não precisa usar o mesmo contêiner que o disco personalizado para armazenar suas VMs. Certifique-se de criar recipientes adicionais da mesma forma como as etapas anteriores antes de carregar o disco personalizado.

O exemplo a seguir cria uma VM denominada `myVM` do disco personalizado:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd \
    --use-unmanaged-disk
```

Você ainda precisará especificar todos os parâmetros adicionais necessários ou responder a prompts deles pelo comando **az vm create**, como nome de usuário e chaves SSH.


## <a name="resource-manager-template"></a>Modelo do Resource Manager
Os modelos do Azure Resource Manager são arquivos JSON (JavaScript Object Notation) que definem o ambiente que você deseja criar. Os modelos são divididos em diferentes provedores de recursos, como computação ou rede. Você pode usar os modelos existentes ou escrever seus próprios. Saiba mais sobre como [usar o Resource Manager e os modelos](../../azure-resource-manager/resource-group-overview.md).

No provedor `Microsoft.Compute/virtualMachines` do seu modelo, haverá um nó `storageProfile` que contém os detalhes de configuração da sua VM. Os dois parâmetros principais a serem editados são os URIs `image` e `vhd` que apontam para o disco personalizado e o disco virtual da nova VM. A seguir está um exemplo do JSON para o uso de um disco personalizado:

```json
"storageProfile": {
          "osDisk": {
            "name": "myVM",
            "osType": "Linux",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "image": {
              "uri": "https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd"
            },
            "vhd": {
              "uri": "https://mystorageaccount.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

Você pode usar [este modelo existente para criar uma VM de uma imagem personalizada](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) ou ler sobre como [criar seus próprios modelos do Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md). 

Quando você tiver um modelo configurado, use [criar implantação de grupo az](/cli/azure/group/deployment) para criar suas VMs. Especifique o URI do modelo JSON com o parâmetro `--template-uri` :

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-uri https://uri.to.template/mytemplate.json
```

Se você tiver um arquivo JSON armazenado localmente no computador, será possível usar o parâmetro `--template-file` :

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Próximas etapas
Depois de preparar e carregar seu disco virtual personalizado, leia mais sobre como [usar o Resource Manager e os modelos](../../azure-resource-manager/resource-group-overview.md). É recomendável [adicionar um disco de dados](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) às novas VMs. Se você tiver aplicativos em execução nas VMs que precisa acessar, não se esqueça de [abrir as portas e os pontos de extremidade](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

