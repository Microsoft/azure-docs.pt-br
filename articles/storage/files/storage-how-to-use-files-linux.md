---
title: Usar o Arquivos do Azure com o Linux | Microsoft Docs
description: Saiba como montar um Compartilhamento de Arquivos do Azure com SMB no Linux.
services: storage
documentationcenter: na
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 6edc37ce-698f-4d50-8fc1-591ad456175d
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: renash
ms.openlocfilehash: 192680efe07368666c5a9d037549c7686189d0b0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-files-with-linux"></a>Usar o Arquivos do Azure com o Linux
[Arquivos do Azure](storage-files-introduction.md) é o sistema de arquivos de nuvem de fácil acesso da Microsoft. Compartilhamentos de Arquivos do Azure podem ser montados em distribuições do Linux usando o [pacote cifs-utils](https://wiki.samba.org/index.php/LinuxCIFS_utils) do [projeto Samba](https://www.samba.org/). Este artigo mostra duas maneiras de montar um Compartilhamento de Arquivos do Azure: sob demanda com o comando `mount` e na inicialização criando uma entrada em `/etc/fstab`.

> [!NOTE]  
> Para montar um Compartilhamento de Arquivos do Azure fora da região do Azure no qual ele está hospedado, como local ou em uma região diferente do Azure, o sistema operacional deve dar suporte à funcionalidade de criptografia do SMB 3.0. O recurso de criptografia do SMB 3.0 para Linux foi introduzido no kernel 4.11. Este recurso permite a montagem do Compartilhamento de Arquivos do Azure do local ou de uma região diferente do Azure. No momento da publicação deste artigo, essa funcionalidade foi retrocompatibilizada para o Ubuntu 16.04 e superior.


## <a name="prerequisities-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>Pré-requisitos para montar um Compartilhamento de Arquivos do Azure com o Linux e o pacote cifs-utils
* **Escolha uma distribuição do Linux que pode ter o pacote cifs-utils instalado**: a Microsoft recomenda as seguintes distribuições do Linux na Galeria de imagens do Azure:

    * Ubuntu Server 14.04+
    * RHEL 7+
    * CentOS 7+
    * Debian 8
    * openSUSE 13.2+
    * SUSE Linux Enterprise Server 12

* <a id="install-cifs-utils"></a>**O pacote cifs-utils é instalado**: o cifs-utils pode ser instalado usando o gerenciador de pacotes na distribuição do Linux escolhida. 

    Em distribuições **Ubuntu** e **Debian**, use o gerenciador de pacotes do `apt-get`:

    ```
    sudo apt-get update
    sudo apt-get install cifs-utils
    ```

    No **RHEL** e **CentOS**, use o gerenciador de pacotes do `yum`:

    ```
    sudo yum install samba-client samba-common cifs-utils
    ```

    No **openSUSE**, use o gerenciador de pacotes do `zypper`:

    ```
    sudo zypper install samba*
    ```

    Em outras distribuições, use o gerenciador de pacotes apropriado ou [compile do código-fonte](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download).

* **Decida as permissões de diretório/arquivo do compartilhamento montado**: nos exemplos abaixo, usamos 0777 para conceder as permissões de leitura, gravação e execução para todos os usuários. Você pode substituí-las por outras [permissões chmod](https://en.wikipedia.org/wiki/Chmod) se desejar. 

* **Nome da conta de armazenamento**: para montar um compartilhamento de arquivos do Azure, você precisará do nome da conta de armazenamento.

* **Chave de conta de armazenamento**: para montar um compartilhamento de arquivos do Azure, você precisará da chave de armazenamento primária (ou secundária). Atualmente, as chaves SAS não têm suporte para montagem.

* **Verifique se a porta 445 está aberta**: o SMB se comunica pela porta TCP 445, por isso confira se o firewall não está bloqueando as portas TCP 445 do computador cliente.

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Montar o Compartilhamento de Arquivos do Azure sob demanda com `mount`
1. **[Instale o pacote cifs-utils para sua distribuição Linux](#install-cifs-utils)**.

2. **Crie uma pasta para o ponto de montagem**: isso pode ser feito em qualquer lugar no sistema de arquivos.

    ```
    mkdir mymountpoint
    ```

3. **Use o comando de montagem para montar o Compartilhamento de Arquivos do Azure**: lembre-se de substituir `<storage-account-name>`, `<share-name>` e `<storage-account-key>` pelas informações apropriadas.

    ```
    sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> ./mymountpoint -o vers=2.1,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> Quando tiver terminado de usar o Compartilhamento de Arquivos do Azure, você pode usar `sudo umount ./mymountpoint` para desmontar o compartilhamento.

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Criar um ponto de montagem persistente para o Compartilhamento de Arquivos do Azure com `/etc/fstab`
1. **[Instale o pacote cifs-utils para sua distribuição Linux](#install-cifs-utils)**.

2. **Crie uma pasta para o ponto de montagem**: isso pode ser feito em qualquer lugar no sistema de arquivos, mas você precisa observar o caminho absoluto da pasta. O exemplo a seguir cria uma pasta na raiz.

    ```
    sudo mkdir /mymountpoint
    ```

3. **Use este comando para acrescentar a linha a seguir a `/etc/fstab`**: lembre-se de substituir `<storage-account-name>`, `<share-name>` e `<storage-account-key>` pelas informações apropriadas.

    ```
    sudo bash -c 'echo "//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs vers=2.1,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'
    ```

> [!Note]  
> Você pode usar `sudo mount -a` para montar o Compartilhamento de Arquivos do Azure após a edição de `/etc/fstab` em vez de reinicializar.

## <a name="feedback"></a>Comentários
Usuários do Linux, queremos ouvir sua opinião!

O grupo do Arquivos do Azure para usuários do Linux oferece um fórum para que você possa compartilhar comentários à medida que você avalia e adota o Armazenamento de Arquivos no Linux. Envie um email para [Usuários do Linux do Arquivos do Azure](mailto:azurefileslinuxusers@microsoft.com) para ingressar no grupo de usuários.

## <a name="next-steps"></a>Próximas etapas
Veja estes links para obter mais informações sobre o Arquivos do Azure.
* [Referência à API REST do serviço de arquivos](http://msdn.microsoft.com/library/azure/dn167006.aspx)
* [Como usar o AzCopy com o Armazenamento do Microsoft Azure](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Usando a CLI do Azure com o Armazenamento do Azure](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-and-manage-file-shares)
* [Perguntas frequentes](../storage-files-faq.md)
* [Solução de problemas](storage-troubleshoot-linux-file-connection-problems.md)
