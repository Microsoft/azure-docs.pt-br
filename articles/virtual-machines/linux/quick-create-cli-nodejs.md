---
title: Criar uma VM Linux usando a CLI clássica do Azure | Microsoft Docs
description: Criar uma VM Linux no Azure usando a CLI clássica do Azure
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: gwallace
editor: ''
ms.assetid: facb1115-2b4e-4ef3-9905-330e42beb686
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2016
ms.author: gwallace
ms.openlocfilehash: b36f6e2ace02324a63616f31fcdccfa666856699
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875228"
---
# <a name="create-a-linux-vm-using-the-azure-classic-cli"></a>Criar uma VM Linux usando a CLI do Azure clássica

Este artigo mostra como implantar rapidamente uma nova VM (máquina virtual) no Azure usando o comando `azure vm quick-create` na CLI (interface de linha de comando) do Azure. O comando `quick-create` implanta uma VM em uma infraestrutura básica e protegida que você pode usar para criar um protótipo ou testar um conceito rapidamente.

> [!NOTE]
> Para criar uma VM usando a CLI do Azure, consulte [Criar uma VM com a CLI do Azure](../windows/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Você também pode implantar rapidamente uma VM do Linux usando o [portal do Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

O artigo exige [arquivos de chave SSH pública e privada](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="quick-commands"></a>Comandos rápidos

O exemplo a seguir mostra como implantar uma VM CoreOS e anexar sua chave SSH (Secure Shell) (seus argumentos podem ser diferentes):

```azurecli
azure vm quick-create -M ~/.ssh/id_rsa.pub -Q CoreOS
```

## <a name="detailed-walkthrough"></a>Passo a passo detalhado

A instrução a seguir tem uma VM UbuntuLTS sendo implantada, passo a passo, com explicações do que cada etapa está realizando.

## <a name="vm-quick-create-aliases"></a>Aliases de criação rápida de VM

Uma maneira rápida de escolher uma distribuição é usar os aliases da CLI do Azure mapeados para as distribuições do SO mais comuns. A tabela a seguir lista os aliases (a partir da CLI do Azure versão 0.10). Todas as implantações que usam `quick-create` são por padrão VMs com suporte de armazenamento SSD (unidade de estado sólido), que oferecem provisionamento mais rápido e acesso a disco de alto desempenho. (Esses aliases do representam uma pequena parte das distribuições disponíveis no Azure. Veja mais imagens no Azure Marketplace [pesquisando uma imagem no PowerShell](../windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), [na Web](https://azure.microsoft.com/marketplace/virtual-machines/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) ou [carregando sua própria imagem personalizada](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).)

| Alias | Publicador | Oferta | SKU | Versão |
|:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |CentOS |7,2 |latest |
| CoreOS |CoreOS |CoreOS |Estável |latest |
| Debian |credativ |Debian |8 |latest |
| openSUSE |SUSE |openSUSE |13.2 |latest |
| RHEL |Red Hat |RHEL |7,2 |latest |
| UbuntuLTS |Canônico |Ubuntu Server |14.04.4-LTS |latest |

As seções a seguir usam o alias `UbuntuLTS` para a opção **ImageURN ()** (`-Q`) a fim de implantar um servidor Ubuntu LTS 14.04.4.

O exemplo de `quick-create` anterior somente chamou o sinalizador `-M` para identificar a chave pública SSH a ser carregada durante a desativação de senhas de SSH e, portanto, você receberá uma solicitação para fornecer os seguintes argumentos:

* nome do grupo de recursos (qualquer cadeia de caracteres costuma funcionar bem para o primeiro grupo de recursos do Azure)
* Nome da VM
* local (`westus` ou `westeurope` são bons padrões)
* Linux (para permitir que o Azure saiba qual é o sistema operacional desejado)
* Nome de Usuário

O exemplo a seguir especifica todos os valores para que nenhuma outra notificação seja necessária. Enquanto houver um `~/.ssh/id_rsa.pub` como arquivo de chave pública de formato ssh-rsa, ele funcionará como está:

```azurecli
azure vm quick-create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --admin-username myAdminUser \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --image-urn UbuntuLTS
```

A saída deve ser semelhante ao bloco de saída a seguir:

```azurecli
info:    Executing command vm quick-create
+ Listing virtual machine sizes available in the location "westus"
+ Looking up the VM "myVM"
info:    Verifying the public key SSH file: /Users/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli16330708391032639673
+ Looking up the NIC "examp-westu-1633070839-nic"
info:    An nic with given name "examp-westu-1633070839-nic" not found, creating a new one
+ Looking up the virtual network "examp-westu-1633070839-vnet"
info:    Preparing to create new virtual network and subnet
/ Creating a new virtual network "examp-westu-1633070839-vnet" [address prefix: "10.0.0.0/16"] with subnet "examp-westu-1633070839-snet" [address prefix: "10.+.1.0/24"]
+ Looking up the virtual network "examp-westu-1633070839-vnet"
+ Looking up the subnet "examp-westu-1633070839-snet" under the virtual network "examp-westu-1633070839-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "examp-westu-1633070839-pip"
info:    PublicIP with given name "examp-westu-1633070839-pip" not found, creating a new one
+ Creating public ip "examp-westu-1633070839-pip"
+ Looking up the public ip "examp-westu-1633070839-pip"
+ Creating NIC "examp-westu-1633070839-nic"
+ Looking up the NIC "examp-westu-1633070839-nic"
+ Looking up the storage account clisto1710997031examplev
+ Creating VM "myVM"
+ Looking up the VM "myVM"
+ Looking up the NIC "examp-westu-1633070839-nic"
+ Looking up the public ip "examp-westu-1633070839-pip"
data:    Id                              :/subscriptions/2<--snip-->d/resourceGroups/exampleResourceGroup/providers/Microsoft.Compute/virtualMachines/exampleVMName
data:    ProvisioningState               :Succeeded
data:    Name                            :exampleVMName
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :Canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :14.04.4-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clic7fadb847357e9cf-os-1473374894359
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli16330708391032639673.blob.core.windows.net/vhds/clic7fadb847357e9cf-os-1473374894359.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM
data:      User Name                     :myAdminUser
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-42-FB
data:          Provisioning State        :Succeeded
data:          Name                      :examp-westu-1633070839-nic
data:          Location                  :westus
data:            Public IP address       :138.91.247.29
data:            FQDN                    :examp-westu-1633070839-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://clisto1710997031examplev.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

## <a name="log-in-to-the-new-vm"></a>Faça logon na nova VM
Entre na VM usando o endereço IP público listado na saída. Você também pode usar o FQDN (nome de domínio totalmente qualificado) que está listado:

```bash
ssh -i ~/.ssh/id_rsa.pub ahmet@138.91.247.29
```

O processo de logon deve ter uma aparência semelhante ao seguinte bloco de saída:

```bash
Warning: Permanently added '138.91.247.29' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 14.04.4 LTS (GNU/Linux 3.19.0-65-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Thu Sep  8 22:50:57 UTC 2016

  System load: 0.63              Memory usage: 2%   Processes:       81
  Usage of /:  39.6% of 1.94GB   Swap usage:   0%   Users logged in: 0

  Graph this data and manage this system at:
    https://landscape.canonical.com/

  Get cloud support with Ubuntu Advantage Cloud Guest:
    https://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

myAdminUser@myVM:~$
```

## <a name="next-steps"></a>Próximas etapas
O comando `azure vm quick-create` é a maneira de implantar rapidamente uma VM para que você possa fazer logon em um shell bash e começar a trabalhar. No entanto, o uso de `vm quick-create` não lhe dá amplo controle e não permite que você crie um ambiente mais complexo.  Para implantar uma VM do Linux personalizada para sua infraestrutura, você pode seguir qualquer um destes artigos:

* [Criar seu próprio ambiente personalizado para uma VM do Linux usando os comandos da CLI do Azure diretamente](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma VM do Linux Protegida por SSH no Azure usando modelos](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Você também pode [usar o driver do Azure `docker-machine` com vários comandos para criar rapidamente uma VM do Linux como um host do docker](docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
