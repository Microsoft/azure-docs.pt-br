---
title: Montar o armazenamento de Arquivos do Azure em VMs Linux usando SMB | Microsoft Docs
description: Como montar o armazenamento de Arquivos do Azure em VMs Linux usando SMB com a CLI 2.0 do Azure
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/13/2017
ms.author: v-livech
ms.openlocfilehash: 9eae17b304f8a987b44ebed8906dabd8ff3a36a8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>Montar o Armazenamento de Arquivos do Azure em VMs Linux usando SMB

Este artigo mostra como utilizar o serviço armazenamento de Arquivos do Azure em uma VM Linux usando uma montagem SMB com a CLI 2.0 do Azure. O armazenamento de arquivos do Azure oferece compartilhamentos de arquivos na nuvem usando o protocolo SMB padrão. Você também pode executar essas etapas com a [CLI do Azure 1.0](mount-azure-file-storage-on-linux-using-smb-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Esses requisitos são:

- [uma conta do Azure](https://azure.microsoft.com/pricing/free-trial/)
- [arquivos de chave SSH pública e privada](mac-create-ssh-keys.md)

## <a name="quick-commands"></a>Comandos rápidos

* Um grupo de recursos
* Uma Rede virtual do Azure
* Um grupo de segurança de rede com uma entrada SSH
* Uma sub-rede
* Uma conta de armazenamento do Azure
* Chaves de conta de armazenamento do Azure
* Um compartilhamento do Armazenamento de arquivos do Azure
* Uma VM do Linux

Substitua os exemplos por suas próprias configurações.

### <a name="create-a-directory-for-the-local-mount"></a>Criar um diretório para a montagem local

```bash
mkdir -p /mnt/mymountpoint
```

### <a name="mount-the-file-storage-smb-share-to-the-mount-point"></a>Montar o compartilhamento SMB do Armazenamento de arquivos no ponto de montagem

```bash
sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

### <a name="persist-the-mount-after-a-reboot"></a>Persista na montagem após uma reinicialização
Para fazer isso, adicione a seguinte linha ao arquivo `/etc/fstab`:

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="detailed-walkthrough"></a>Passo a passo detalhado

O armazenamento de arquivos oferece compartilhamentos de arquivos na nuvem que usam o protocolo SMB padrão. Com a última versão do Armazenamento de arquivos, também é possível montar um compartilhamento de arquivos em qualquer sistema operacional que dá suporte ao SMB 3.0. Ao usar uma montagem SMB no Linux, você obtém backups fáceis em uma localização robusta e permanente de armazenamento de arquivamento com suporte em um SLA.

Mover arquivos de uma VM para uma montagem SMB hospedada no Armazenamento de arquivos é uma ótima maneira de depurar logs. Isso ocorre porque o mesmo compartilhamento SMB pode ser montado localmente em sua estação de trabalho Mac, Linux ou Windows. O SMB não é a melhor solução para transmitir logs do Linux ou de aplicativo em tempo real, pois o protocolo SMB não foi desenvolvido para lidar com tarefas de log tão grandes. Uma ferramenta de camada de log unificada dedicada, como o Fluentd, poderá ser uma escolha melhor em relação ao SMB para coletar a saída de log do Linux e de aplicativo.

Para este passo a passo detalhado, criamos os pré-requisitos necessários para primeiro criar o compartilhamento do Armazenamento de arquivos e, depois, montá-lo pelo SMB em uma VM Linux.

1. Crie um grupo de recursos com [az group create](/cli/azure/group#create) para armazenar o compartilhamento de arquivo.

    Para criar um grupo de recursos denominado `myResourceGroup` na localização Oeste dos EUA, use o seguinte exemplo:

    ```azurecli
    az group create --name myResourceGroup --location westus
    ```

2. Crie uma conta de armazenamento do Azure com [az storage account create](/cli/azure/storage/account#create) para armazenar os arquivos reais.

    Para criar uma conta de armazenamento denominada mystorageaccount usando o SKU de armazenamento Standard_LRS, use o exemplo a seguir:

    ```azurecli
    az storage account create --resource-group myResourceGroup \
        --name mystorageaccount \
        --location westus \
        --sku Standard_LRS
    ```

3. Mostrar as chaves da conta de armazenamento.

    Ao criar uma conta de armazenamento, as chaves da conta são criadas em pares, para que possam ser giradas sem nenhuma interrupção de serviço. Depois de mudar as chaves para a segunda chave do par, você cria um novo par de chaves. Novas chaves de conta de armazenamento são sempre criadas em pares, garantindo que você sempre tenha, pelo menos, uma chave de conta de armazenamento não utilizada pronta para ser mudada.

    Exiba as chaves da conta de armazenamento com a [lista de chaves de conta de armazenamento az](/cli/azure/storage/account/keys#list). As chaves da conta de armazenamento para o `mystorageaccount` denominado estão listadas no seguinte exemplo:

    ```azurecli
    az storage account keys list --resource-group myResourceGroup \
        --account-name mystorageaccount
    ```

    Para extrair uma única chave, use o sinalizador `--query`. O exemplo a seguir extrai a primeira chave (`[0]`):

    ```azurecli
    az storage account keys list --resource-group myResourceGroup \
        --account-name mystorageaccount \
        --query '[0].{Key:value}' --output tsv
    ```

4. Criar o compartilhamento do Armazenamento de arquivos.

    O compartilhamento de Armazenamento de arquivos contém o compartilhamento SMB com [az storage share create](/cli/azure/storage/share#create). A cota é sempre expressa em gigabytes (GB). Passe em uma das chaves do comando `az storage account keys list` anterior. Crie um compartilhamento chamado mystorageshare com uma cota de 10 GB usando o exemplo a seguir:

    ```azurecli
    az storage share create --name mystorageshare \
        --quota 10 \
        --account-name mystorageaccount \
        --account-key nPOgPR<--snip-->4Q==
    ```

5. Crie um diretório de ponto de montagem.

    Crie um diretório local no sistema de arquivos do Linux no qual o compartilhamento SMB será montado. Tudo que for gravado ou lido no diretório de montagem local será encaminhado para o compartilhamento SMB que está hospedado no Armazenamento de arquivos. Para criar um diretório local no /mnt/mymountdirectory, use o exemplo a seguir:

    ```bash
    sudo mkdir -p /mnt/mymountdirectory
    ```

6. Monte o compartilhamento SMB para o diretório local.

    Forneça seu próprio nome de usuário de conta de armazenamento e chave de conta de armazenamento para as credenciais de montagem, da seguinte maneira:

    ```azurecli
    sudo mount -t cifs //myStorageAccount.file.core.windows.net/mystorageshare /mnt/mymountdirectory -o vers=3.0,username=mystorageaccount,password=mystorageaccountkey,dir_mode=0777,file_mode=0777
    ```

7. Persistir na montagem SMB por meio de reinicializações.

    Ao reinicializar a VM Linux, o compartilhamento SMB montado é desmontado durante o desligamento. Para montar novamente o compartilhamento SMB durante a inicialização, adicione uma linha ao /etc/fstab do Linux. O Linux usa o arquivo fstab para listar os sistemas de arquivos que precisa montar durante o processo de inicialização. A adição do compartilhamento SMB garante que o compartilhamento do Armazenamento de arquivos seja um sistema de arquivos montado permanentemente para a VM Linux. É possível adicionar o compartilhamento SMB do Armazenamento de arquivos a uma nova VM ao usar a inicialização de nuvem.

    ```bash
    //myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    ```

## <a name="next-steps"></a>Próximas etapas

- [Uso de cloud-init para personalizar uma VM do Linux durante a criação](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Adicionar um disco a uma VM do Linux](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Criptografar discos em uma VM do Linux usando a CLI do Azure](encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
