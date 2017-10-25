---
title: Montar o Armazenamento de arquivos do Azure em VMs Linux ao usar SMB com CLI 1.0 do Azure | Microsoft Docs
description: Como montar o Armazenamento de arquivos do Azure em VMs Linux usando o SMB
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
ms.date: 12/07/2016
ms.author: v-livech
ms.openlocfilehash: 4951860630f0aad107d0846d52ebe4423ee0b91c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="mount-azure-file-storage-on-linux-vms-by-using-smb-with-azure-cli-10"></a>Montar o Armazenamento de arquivos do Azure em VMs Linux ao usar SMB com CLI 1.0 do Azure

Este artigo mostra como montar o Armazenamento de arquivos do Azure em uma VM Linux ao usar o protocolo SMB. O armazenamento de arquivos oferece compartilhamentos de arquivos na nuvem por meio do protocolo SMB padrão. Esses requisitos são:

* Uma [conta do Azure](https://azure.microsoft.com/pricing/free-trial/)
* [Arquivos de chave Secure Shell (SSH) pública e privada](mac-create-ssh-keys.md)

## <a name="cli-versions-to-use"></a>Versões CLI para utilizar
É possível concluir a tarefa usando uma das seguintes versões da interface de linha de comando (CLI):

- [CLI 1.0 do Azure](#quick-commands) – nossa CLI para os modelos de implantação clássico e de gerenciamento de recursos (este artigo)
- [CLI do Azure 2.0](mount-azure-file-storage-on-linux-using-smb-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – nossa próxima geração de CLI para o modelo de implantação do gerenciamento de recursos


## <a name="quick-commands"></a>Comandos rápidos
Para executar a tarefa rapidamente, siga as etapas nesta seção. Para obter informações e contexto detalhados, comece na seção ["Passo a passo detalhado"](mount-azure-file-storage-on-linux-using-smb.md#detailed-walkthrough).

### <a name="prerequisites"></a>Pré-requisitos
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
Adicione a seguinte linha ao `/etc/fstab`:

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="detailed-walkthrough"></a>Passo a passo detalhado

O armazenamento de arquivos oferece compartilhamentos de arquivos na nuvem que usam o protocolo SMB padrão. Com a última versão do Armazenamento de arquivos, também é possível montar um compartilhamento de arquivos em qualquer sistema operacional que dá suporte ao SMB 3.0. Ao usar uma montagem SMB no Linux, você obtém backups fáceis em uma localização robusta e permanente de armazenamento de arquivamento com suporte em um SLA.

Mover arquivos de uma VM para uma montagem SMB hospedada no Armazenamento de arquivos é uma ótima maneira de depurar logs. Isso ocorre porque o mesmo compartilhamento SMB pode ser montado localmente em sua estação de trabalho Mac, Linux ou Windows. O SMB não é a melhor solução para transmitir logs do Linux ou de aplicativo em tempo real, pois o protocolo SMB não foi desenvolvido para lidar com tarefas de log tão grandes. Uma ferramenta de camada de log unificada dedicada, como o Fluentd, poderá ser uma escolha melhor em relação ao SMB para coletar a saída de log do Linux e de aplicativo.

Para este passo a passo detalhado, criamos os pré-requisitos necessários para primeiro criar o compartilhamento do Armazenamento de arquivos e, depois, montá-lo pelo SMB em uma VM Linux.

1. Crie uma conta de armazenamento do Azure usando o código a seguir:

    ```azurecli
    azure storage account create myStorageAccount \
    --sku-name lrs \
    --kind storage \
    -l westus \
    -g myResourceGroup
    ```

2. Mostrar as chaves da conta de armazenamento.

    Ao criar uma conta de armazenamento, as chaves da conta são criadas em pares, para que possam ser giradas sem nenhuma interrupção de serviço. Depois de mudar as chaves para a segunda chave do par, você cria um novo par de chaves. Novas chaves da conta de armazenamento são sempre criadas em pares, garantindo que você sempre tenha, pelo menos, uma chave de armazenamento não utilizada pronta para ser mudada. Para mostrar as chaves da conta de armazenamento, use o seguinte código:

    ```azurecli
    azure storage account keys list myStorageAccount \
    --resource-group myResourceGroup
    ```
3. Criar o compartilhamento do Armazenamento de arquivos.

    O compartilhamento do Armazenamento de arquivos contém o compartilhamento SMB. A cota é sempre expressa em gigabytes (GB). Para criar o compartilhamento do Armazenamento de arquivos, use o seguinte código:

    ```azurecli
    azure storage share create mystorageshare \
    --quota 10 \
    --account-name myStorageAccount \
    --account-key nPOgPR<--snip-->4Q==
    ```

4. Criar o diretório de ponto de montagem.

    Você deve criar um diretório local no sistema de arquivos do Linux no qual o compartilhamento SMB será montado. Tudo que for gravado ou lido no diretório de montagem local será encaminhado para o compartilhamento SMB que está hospedado no Armazenamento de arquivos. Para criar o diretório, use o seguinte código:

    ```bash
    sudo mkdir -p /mnt/mymountdirectory
    ```

5. Monte o compartilhamento SMB usando o código a seguir:

    ```azurecli
    sudo mount -t cifs //myStorageAccount.file.core.windows.net/mystorageshare /mnt/mymountdirectory -o vers=3.0,username=myStorageAccount,password=myStorageAccountkey,dir_mode=0777,file_mode=0777
    ```

6. Persistir na montagem SMB por meio de reinicializações.

    Ao reinicializar a VM Linux, o compartilhamento SMB montado é desmontado durante o desligamento. Para montar novamente o compartilhamento SMB durante a inicialização, é necessário adicionar uma linha ao /etc/fstab do Linux. O Linux usa o arquivo fstab para listar os sistemas de arquivos que precisa montar durante o processo de inicialização. A adição do compartilhamento SMB garante que o compartilhamento do Armazenamento de arquivos seja um sistema de arquivos montado permanentemente para a VM Linux. É possível adicionar o compartilhamento SMB do Armazenamento de arquivos a uma nova VM ao usar a inicialização de nuvem.

    ```bash
    //myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    ```

## <a name="next-steps"></a>Próximas etapas

- [Uso de cloud-init para personalizar uma VM do Linux durante a criação](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Adicionar um disco a uma VM do Linux](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Criptografar discos em uma VM do Linux usando a CLI do Azure](encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
