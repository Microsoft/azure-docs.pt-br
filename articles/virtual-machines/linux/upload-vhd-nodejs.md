---
title: Carregar uma imagem personalizada do Linux com a CLI 1.0 do Azure | Microsoft Docs
description: "Crie e carregue um VHD (disco rígido virtual) no Azure com uma imagem personalizada do Linux usando o modelo de implantação do Resource Manager e a CLI 1.0 do Azure."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: iainfou
ms.openlocfilehash: ca4c6cb9296028275b2b032af0c94baabeec1223
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-image-by-using-the-azure-cli-10"></a>Carregar e criar uma VM Linux com base em uma imagem de disco personalizada usando a CLI 1.0 do Azure
Este artigo mostra como carregar um VHD (disco rígido virtual) no Azure usando o modelo de implantação do Resource Manager e criar VMs Linux com base nessa imagem personalizada. Essa funcionalidade permite que você instale e configure uma distribuição do Linux segundo suas necessidades e use esse VHD para criar rapidamente máquinas virtuais (VMs) do Azure.


## <a name="cli-versions-to-complete-the-task"></a>Versões da CLI para concluir a tarefa
Você pode concluir a tarefa usando uma das seguintes versões da CLI:

- [CLI 1.0 do Azure](#quick-commands) – nossa CLI para os modelos de implantação clássico e de gerenciamento de recursos (este artigo)
- [CLI 2.0 do Azure](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – nossa última geração de CLI para o modelo de implantação de gerenciamento de recursos


## <a name="quick-commands"></a>Comandos rápidos
Se você precisar realizar rapidamente a tarefa, a seção a seguir detalha a base de dados de comandos para carregar uma VM no Azure. Mais informações detalhadas e contexto para cada etapa podem ser encontrados no restante do documento, [começando aqui](#requirements).

Verifique se [a CLI 1.0 do Azure](../../cli-install-nodejs.md) está conectada e usando o modo Resource Manager:

```azurecli
azure config mode arm
```

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Os nomes de parâmetro de exemplo incluíram `myResourceGroup`, `mystorageaccount` e `myimages`.

Em primeiro lugar, crie um grupo de recursos. O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` no local `WestUs`:

```azurecli
azure group create myResourceGroup --location "WestUS"
```

Crie uma conta de armazenamento para manter seus discos virtuais. O exemplo a seguir cria uma conta de armazenamento chamada `mystorageaccount`:

```azurecli
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

Lista as chaves de acesso para a sua conta de armazenamento. Anote `key1`:

```azurecli
azure storage account keys list mystorageaccount --resource-group myResourceGroup
```

Crie um contêiner dentro de sua conta de armazenamento usando a chave de armazenamento obtida. O exemplo a seguir cria um contêiner denominado `myimages` usando o valor de chave de armazenamento de `key1`:

```azurecli
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

Por fim, carregue o VHD no contêiner que você criou. Especifique o caminho local para o VHD em `/path/to/disk/mydisk.vhd`:

```azurecli
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

Agora você pode criar uma VM do disco virtual carregado [usando um modelo do Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd). Você também pode usar a CLI, especificando o URI para o seu disco (`--image-urn`). O exemplo a seguir cria uma VM denominada `myVM` usando o disco virtual carregado anteriormente:

```azurecli
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
```

A conta de armazenamento de destino deve ser a mesma em que você carregou o disco virtual. Você também precisará especificar todos os parâmetros adicionais necessários ou responder a prompts deles pelo comando `azure vm create` , como rede virtual, endereço IP público, nome de usuário e chaves SSH. Leia mais sobre os [parâmetros do Resource Manager na CLI disponíveis](../azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

## <a name="requirements"></a>Requisitos
Para concluir as etapas a seguir, você precisa:

* **Sistema operacional Linux instalado em um arquivo .vhd** - Instale uma [distribuição Linux endossada pelo Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (ou confira as [informações para as distribuições não endossadas](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) em um disco virtual no formato VHD. Existem várias ferramentas para criar uma VM e o VHD:
  * Instalar e configurar o [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) ou o [KVM](http://www.linux-kvm.org/page/RunningKVM), tomando o cuidado de usar o VHD como seu formato de imagem. Se necessário, você pode [converter uma imagem](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) usando `qemu-img convert`.
  * Também pode usar o Hyper-V [no Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) ou [no Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Não há suporte para o formato VHDX mais recente no Azure. Ao criar uma VM, especifique VHD como o formato. Se necessário, será possível converter discos VHDX para VHD usando o cmdlet [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) ou [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) do PowerShell. Além disso, o Azure não dá suporte ao carregamento de VHDs dinâmicos, por isso você precisa converter esses discos para VHDs estáticos antes de carregar. Você pode usar ferramentas como o [Azure VHD Utilities for GO](https://github.com/Microsoft/azure-vhd-utils-for-go) para converter os discos dinâmicos durante o processo de carregamento no Azure.
> 
> 

* As VMs criadas com base em sua imagem personalizada devem residir na mesma conta de armazenamento que a imagem
  * Criar uma conta de armazenamento e um contêiner para manter a imagem personalizada e as VMs criadas
  * Depois de criar todas as VMs, você poderá excluir a imagem com segurança

Verifique se [a CLI 1.0 do Azure](../../cli-install-nodejs.md) está conectada e usando o modo Resource Manager:

```azurecli
azure config mode arm
```

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Os nomes de parâmetro de exemplo incluíram `myResourceGroup`, `mystorageaccount` e `myimages`.

<a id="prepimage"> </a>

## <a name="prepare-the-image-to-be-uploaded"></a>Preparar a imagem a ser carregada
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


## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Os grupos de recursos reúnem logicamente todos os recursos do Azure para dar suporte às suas máquinas virtuais, como a rede virtual e o armazenamento. Leia mais sobre os [grupos de recursos do Azure aqui](../../azure-resource-manager/resource-group-overview.md). Antes de carregar sua imagem de disco personalizada e criar VMs, primeiro você precisa criar um grupo de recursos. 

O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` no local `WestUS`:

```azurecli
azure group create myResourceGroup --location "WestUS"
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
As VMs são armazenadas como blobs de páginas em uma conta de armazenamento. Leia mais sobre o [armazenamento de blobs do Azure aqui](../../storage/common/storage-introduction.md#blob-storage). Você cria uma conta de armazenamento para suas VMs e imagem de disco personalizadas. Todas as VMs criadas com base na imagem de disco personalizada precisam estar na mesma conta de armazenamento que a imagem.

O exemplo a seguir cria uma conta de armazenamento denominada `mystorageaccount` no grupo de recursos criado anteriormente:

```azurecli
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

## <a name="list-storage-account-keys"></a>Listar chaves da conta de armazenamento
O Azure gera duas chaves de acesso de 512 bits para cada conta de armazenamento. Essas chaves de acesso são usadas durante a autenticação na conta de armazenamento, por exemplo, para executar operações de gravação. Leia mais sobre [como gerenciar o acesso ao armazenamento aqui](../../storage/common/storage-create-storage-account.md#manage-your-storage-account). É possível exibir as chaves de acesso com o comando `azure storage account keys list` .

Veja as chaves de acesso da conta de armazenamento que você criou:

```azurecli
azure storage account keys list mystorageaccount --resource-group myResourceGroup
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
Da mesma forma que você cria diferentes diretórios para organizar logicamente seu sistema de arquivos local, crie contêineres em uma conta de armazenamento para organizar suas imagens e discos virtuais. Uma conta de armazenamento pode conter qualquer quantidade de contêineres. 

O exemplo a seguir cria contêiner chamado `myimages`, especificando a chave de acesso obtida na etapa anterior (`key1`):

```azurecli
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

## <a name="upload-vhd"></a>Carregar o VHD
Agora, você pode efetivamente carregar sua imagem de disco personalizada. Assim como ocorre com todos os discos virtuais usados pelas VMs, você carrega e armazenará sua imagem de disco personalizada como um blob de páginas.

Especifique sua chave de acesso, o contêiner que você criou na etapa anterior e o caminho para a imagem de disco personalizada no seu computador local:

```azurecli
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

## <a name="create-vm-from-custom-image"></a>Criar uma VM com base em uma imagem personalizada
Quando você cria VMs da sua imagem do disco personalizada, especifique o URI para a imagem de disco. Verifique se a conta de armazenamento de destino corresponde ao local em que a imagem de disco personalizada está armazenada. Você pode criar sua VM usando a CLI do Azure ou o modelo JSON do Resource Manager.

### <a name="create-a-vm-using-the-azure-cli"></a>Criar uma VM usando a CLI do Azure
Especifique o parâmetro `--image-urn` com o comando `azure vm create` para apontar para a imagem de disco personalizada. Verifique se `--storage-account-name` corresponde à conta de armazenamento em que a imagem de disco personalizada está armazenada. Você não precisa usar o mesmo contêiner como a imagem de disco personalizada para armazenar suas VMs. Certifique-se de criar recipientes adicionais da mesma forma como as etapas anteriores antes de carregar as imagens de disco personalizadas.

O exemplo a seguir cria uma VM denominada `myVM` da imagem do disco personalizada:

```azurecli
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
    --storage-account-name mystorageaccount
```

Você ainda precisará especificar todos os parâmetros adicionais necessários ou responder a prompts deles pelo comando `azure vm create` , como rede virtual, endereço IP público, nome de usuário e chaves SSH. Leia mais sobre os [parâmetros do Resource Manager na CLI disponíveis](../azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

### <a name="create-a-vm-using-a-json-template"></a>Criar uma VM usando um modelo JSON
Os modelos do Azure Resource Manager são arquivos JSON (JavaScript Object Notation) que definem o ambiente que você deseja criar. Os modelos são divididos em diferentes provedores de recursos, como computação ou rede. Você pode usar os modelos existentes ou escrever seus próprios. Saiba mais sobre como [usar o Resource Manager e os modelos](../../azure-resource-manager/resource-group-overview.md).

No provedor `Microsoft.Compute/virtualMachines` do seu modelo, haverá um nó `storageProfile` que contém os detalhes de configuração da sua VM. Os dois parâmetros principais a serem editados são os URIs `image` e `vhd` que apontam para a imagem de disco personalizada e o disco virtual da nova VM. A seguir está um exemplo do JSON para o uso de uma imagem de disco personalizada:

```json
"storageProfile": {
          "osDisk": {
            "name": "myVM",
            "osType": "Linux",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "image": {
              "uri": "https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd"
            },
            "vhd": {
              "uri": "https://mystorageaccount.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

Você pode usar [este modelo existente para criar uma VM de uma imagem personalizada](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) ou ler sobre como [criar seus próprios modelos do Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md). 

Depois de configurar um modelo, crie suas VMs usando o comando `azure group deployment create` . Especifique o URI do modelo JSON com o parâmetro `--template-uri` :

```azurecli
azure group deployment create --resource-group myResourceGroup
    --template-uri https://uri.to.template/mytemplate.json
```

Se você tiver um arquivo JSON armazenado localmente no computador, será possível usar o parâmetro `--template-file` :

```azurecli
azure group deployment create --resource-group myResourceGroup
    --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Próximas etapas
Depois de preparar e carregar seu disco virtual personalizado, leia mais sobre como [usar o Resource Manager e os modelos](../../azure-resource-manager/resource-group-overview.md). É recomendável [adicionar um disco de dados](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) às novas VMs. Se você tiver aplicativos em execução nas VMs que precisa acessar, não se esqueça de [abrir as portas e os pontos de extremidade](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

