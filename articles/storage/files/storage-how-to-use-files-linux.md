---
title: Usar o Arquivos do Azure com o Linux | Microsoft Docs
description: Saiba como montar um compartilhamento de arquivos do Azure com SMB no Linux.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 03/29/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 75987c7838846aacb099b725e2a222967b32fe64
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763819"
---
# <a name="use-azure-files-with-linux"></a>Usar o Arquivos do Azure com o Linux

[Arquivos do Azure](storage-files-introduction.md) é o sistema de arquivos de nuvem de fácil acesso da Microsoft. Os compartilhamentos de arquivos do Azure podem ser montados em distribuições do Linux usando o [cliente de kernel SMB](https://wiki.samba.org/index.php/LinuxCIFS). Este artigo mostra duas maneiras de montar um compartilhamento de arquivos do Azure: sob demanda com o comando `mount` e na inicialização criando uma entrada em `/etc/fstab`.

> [!NOTE]  
> Para montar um compartilhamento de arquivos do Azure fora da região do Azure no qual ele está hospedado, como local ou em uma região diferente do Azure, o sistema operacional deve dar suporte à funcionalidade de criptografia do SMB 3.0.

## <a name="prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>Pré-requisitos para montar um compartilhamento de arquivos do Azure com o Linux e o pacote cifs-utils
<a id="smb-client-reqs"></a>

* **Um compartilhamento de arquivo e conta de armazenamento do Azure existente**: Para concluir este artigo, você precisa ter uma conta de armazenamento e compartilhamento de arquivos. Se você ainda não criou um, consulte um dos nossos guias de início rápido sobre o assunto: [Criar compartilhamentos de arquivos - CLI](storage-how-to-use-files-cli.md).

* **O nome de conta de armazenamento e chave** será necessário o nome da conta de armazenamento e a chave para concluir este artigo. Se você criou um usando o início rápido da CLI que você já deve tê-las, caso contrário, consulte o guia de início rápido da CLI foi vinculado anteriormente, para saber como recuperar sua chave de conta de armazenamento.

* **Escolha uma distribuição do Linux para atender às suas necessidades de montagem.**  
      O Arquivos do Azure podem ser montados por meio do SMB 2.1 e do SMB 3.0. Para conexões de clientes no local ou em outras regiões do Azure, é necessário usar SMB 3.0; os Arquivos do Azure rejeitarão SMB 2.1 (ou SMB 3.0 sem criptografia). Se você estiver acessando o compartilhamento de arquivos do Azure de uma VM na mesma região do Azure, poderá acessar o compartilhamento de arquivos usando SMB 2.1, se e somente se a *transferência segura obrigatória* estiver desabilitada para a conta de armazenamento que hospeda o compartilhamento de arquivos do Azure. Recomendamos sempre exigir transferência segura e usar apenas SMB 3.0 com criptografia.

    Suporte a criptografia SMB 3.0 foi introduzido na versão 4.11 do kernel do Linux e foi backported para versões mais antigas do kernel para distribuições populares do Linux. No momento da publicação deste documento, as seguintes distribuições da galeria do Azure são compatíveis com a opção de montagem especificada nos cabeçalhos de tabela. 

* **Versões mínimas recomendadas com recursos de montagem correspondentes (SMB versão 2.1 vs SMB versão 3.0)**    

    |   | SMB 2.1 <br>(Montagens em VMs na mesma região do Azure) | SMB 3.0 <br>(Montagens de região cruzada e locais) |
    | --- | :---: | :---: |
    | Ubuntu Server | 14.04+ | 16.04+ |
    | RHEL | 7+ | 7.5+ |
    | CentOS | 7+ |  7.5+ |
    | Debian | 8+ |   |
    | openSUSE | 13.2+ | 42.3+ |
    | SUSE Linux Enterprise Server | 12 | 12 SP3+ |

    Se a distribuição de Linux não estiver listada aqui, você pode verificar a versão do kernel do Linux com o seguinte comando:

   ```bash
   uname -r
   ```

* <a id="install-cifs-utils"></a>**O pacote de utilitários cifs está instalado.**  
    O pacote cifs-utils pode ser instalado usando o gerenciador de pacotes na distribuição do Linux escolhida. 

    Em distribuições **Ubuntu** e **Debian**, use o gerenciador de pacotes do `apt-get`:

    ```bash
    sudo apt-get update
    sudo apt-get install cifs-utils
    ```

    No **RHEL** e **CentOS**, use o gerenciador de pacotes do `yum`:

    ```bash
    sudo yum install cifs-utils
    ```

    No **openSUSE**, use o gerenciador de pacotes do `zypper`:

    ```bash
    sudo zypper install cifs-utils
    ```

    Em outras distribuições, use o gerenciador de pacotes apropriado ou [compile do código-fonte](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download)

* **Decida as permissões de diretório/arquivo do compartilhamento montado**: Nos exemplos abaixo, a permissão `0777` é usada para dar permissões de leitura, gravação e execução a todos os usuários. Você pode substituí-las por outras [permissões chmod](https://en.wikipedia.org/wiki/Chmod) se desejar.

* **Verifique se a porta 445 está aberta**: O SMB se comunica pela porta TCP 445: confira se o firewall não está bloqueando as portas TCP 445 do computador cliente.

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Montar o compartilhamento de arquivos do Azure sob demanda com `mount`

1. **[Instale o pacote cifs-utils para sua distribuição Linux](#install-cifs-utils)**.

1. **Crie uma pasta para um ponto de montagem**: Uma pasta para um ponto de montagem pode ser criada em qualquer lugar no sistema de arquivos, mas é comum criá-la na pasta `/mnt`. Por exemplo: 

    ```bash
    mkdir /mnt/MyAzureFileShare
    ```

1. **Use o comando de montagem para montar o compartilhamento de arquivos do Azure**: Lembre-se de substituir `<storage-account-name>`, `<share-name>`, `<smb-version>`, `<storage-account-key>` e `<mount-point>` pelas informações apropriadas para o seu ambiente. Se a sua distribuição de Linux oferece suporte a SMB 3.0 com criptografia (consulte [Entender os requisitos do cliente SMB](#smb-client-reqs) para obter mais informações), use `3.0` para `<smb-version>`. Para distribuições do Linux que não têm suporte a SMB 3.0 com criptografia, use `2.1` para `<smb-version>`. Um compartilhamento de arquivos do Azure só pode ser montado fora de uma região do Azure (incluindo local ou em outra região do Azure) com SMB 3.0. 

    ```bash
    sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> Quando tiver terminado de usar o compartilhamento de arquivos do Azure, você pode usar `sudo umount <mount-point>` para desmontar o compartilhamento.

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Criar um ponto de montagem persistente para o compartilhamento de arquivos do Azure com `/etc/fstab`

1. **[Instale o pacote cifs-utils para sua distribuição Linux](#install-cifs-utils)**.

1. **Crie uma pasta para um ponto de montagem**: Uma pasta para um ponto de montagem pode ser criada em qualquer lugar no sistema de arquivos, mas é comum criá-la na pasta `/mnt`. Sempre que você criá-lo, observe o caminho absoluto da pasta. Por exemplo, o comando a seguir cria uma nova pasta em `/mnt` (o caminho é um caminho absoluto).

    ```bash
    sudo mkdir /mnt/MyAzureFileShare
    ```

1. **Crie um arquivo de credencial para armazenar o nome de usuário (o nome de conta de armazenamento) e a senha (a chave de conta de armazenamento) para o compartilhamento de arquivos.** Lembre-se de substituir `<storage-account-name>` e `<storage-account-key>` pelas informações apropriadas para o seu ambiente. 

    ```bash
    if [ ! -d "/etc/smbcredentials" ]; then
        sudo mkdir /etc/smbcredentials
    fi

    if [ ! -f "/etc/smbcredentials/<storage-account-name>.cred" ]; then
        sudo bash -c 'echo "username=<storage-account-name>" >> /etc/smbcredentials/<storage-account-name>.cred'
        sudo bash -c 'echo "password=<storage-account-key>" >> /etc/smbcredentials/<storage-account-name>.cred'
    fi
    ```

1. **Alterar as permissões no arquivo de credenciais para que raiz possa ler ou modificar o arquivo de senha.** Como a chave da conta de armazenamento é essencialmente uma senha de super administrador para a conta de armazenamento, é importante definir as permissões no arquivo de modo que somente o usuário root possa acessar, para que usuários com privilégios mais baixos não possam recuperar a chave da conta de armazenamento.   

    ```bash
    sudo chmod 600 /etc/smbcredentials/<storage-account-name>.cred
    ```

1. **Use o seguinte comando para acrescentar a linha abaixo a `/etc/fstab`**: Lembre-se de substituir `<storage-account-name>`, `<share-name>`, `<smb-version>` e `<mount-point>` pelas informações apropriadas para o seu ambiente. Se a sua distribuição de Linux oferece suporte a SMB 3.0 com criptografia (consulte [Entender os requisitos do cliente SMB](#smb-client-reqs) para obter mais informações), use `3.0` para `<smb-version>`. Para distribuições do Linux que não têm suporte a SMB 3.0 com criptografia, use `2.1` para `<smb-version>`. Um compartilhamento de arquivos do Azure só pode ser montado fora de uma região do Azure (incluindo local ou em outra região do Azure) com SMB 3.0. 

    ```bash
    sudo bash -c 'echo "//<storage-account-name>.file.core.windows.net/<share-name> <mount-point> cifs nofail,vers=<smb-version>,credentials=/etc/smbcredentials/<storage-account-name>.cred,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'
    ```

> [!Note]  
> Você pode usar `sudo mount -a` para montar o compartilhamento de arquivos do Azure após a edição de `/etc/fstab` em vez de reinicializar.

## <a name="feedback"></a>Comentários

Usuários do Linux, queremos ouvir sua opinião!

O grupo do Arquivos do Azure para usuários do Linux oferece um fórum para que você possa compartilhar comentários à medida que você avalia e adota o Armazenamento de Arquivos no Linux. Envie um email para [Usuários do Linux do Arquivos do Azure](mailto:azurefileslinuxusers@microsoft.com) para ingressar no grupo de usuários.

## <a name="next-steps"></a>Próximos passos

Veja estes links para obter mais informações sobre o Arquivos do Azure:

* [Planejando uma implantação de Arquivos do Azure](storage-files-planning.md)
* [Perguntas frequentes](../storage-files-faq.md)
* [Solução de problemas](storage-troubleshoot-linux-file-connection-problems.md)
