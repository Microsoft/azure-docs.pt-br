---
title: Montagem do compartilhamento de arquivos do Azure no SMB com macOS | Microsoft Docs
description: Saiba como montar um compartilhamento de arquivos do Azure no SMB com macOS.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7f2abbb355513c175329d01b9d5ed8884f2c27ef
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763542"
---
# <a name="mount-azure-file-share-over-smb-with-macos"></a>Montagem do compartilhamento de arquivos do Azure no SMB com macOS
[Arquivos do Azure](storage-files-introduction.md) é o sistema de arquivos de nuvem fácil de usar da Microsoft. Os compartilhamentos de arquivos do Azure podem ser montados com o protocolo SMB 3 padrão do setor do macOS El Capitan 10.11 e versões posteriores. Este artigo mostra duas maneiras diferentes para montar um compartilhamento de arquivos do Azure no macOS: com a interface do usuário do Finder e usando o Terminal.

> [!Note]  
> Antes de montar um compartilhamento de arquivos do Azure no SMB, é recomendável desabilitar a assinatura de pacote SMB. Isso pode resultar em baixo desempenho ao acessar o compartilhamento de arquivos do Azure no macOS. Sua conexão SMB será criptografada para que isso não afete a segurança de sua conexão. No terminal, os comandos a seguir desabilitarão a assinatura de pacotes SMB, conforme descrito por este [Artigo do suporte da Apple sobre como desabilitar a assinatura de pacote SMB](https://support.apple.com/HT205926):  
>    ```
>    sudo -s
>    echo "[default]" >> /etc/nsmb.conf
>    echo "signing_required=no" >> /etc/nsmb.conf
>    exit
>    ```

## <a name="prerequisites-for-mounting-an-azure-file-share-on-macos"></a>Pré-requisitos para montar um compartilhamento de arquivos do Azure no macOS
* **Nome da conta de armazenamento**: Para montar um compartilhamento de arquivos do Azure, você precisará do nome da conta de armazenamento.

* **Chave de conta de armazenamento**: Para montar um compartilhamento de arquivos do Azure, você precisará da chave de armazenamento primária (ou secundária). Atualmente, as chaves SAS não têm suporte para montagem.

* **Verifique se a porta 445 está aberta**: SMB se comunica pela porta TCP 445. No computador cliente (Mac), verifique se o firewall não está bloqueando a porta TCP 445.

## <a name="mount-an-azure-file-share-via-finder"></a>Montar um compartilhamento de arquivos do Azure por meio do Localizador
1. **Abrir o Localizador**: o Localizador é aberto no macOS por padrão, mas você pode garantir que ele seja o aplicativo selecionado clicando no "ícone de rosto do macOS" no encaixe:  
    ![O ícone de rosto do macOS](./media/storage-how-to-use-files-mac/mount-via-finder-1.png)

2. **Selecione "Conectar ao Servidor" no Menu "Go"**: Usando o caminho UNC dos pré-requisitos, converta a barra invertida dupla de início (`\\`) em `smb://` e todas as outras barras invertidas (`\`) em barras "/" (`/`). Seu link deverá ter a seguinte aparência: ![A caixa de diálogo "Conectar ao Servidor"](./media/storage-how-to-use-files-mac/mount-via-finder-2.png)

3. **Use o nome da conta de armazenamento e a chave de conta de armazenamento quando for solicitado a fornecer um nome de usuário e senha**: ao clicar em "Conectar" na caixa de diálogo "Conectar ao Servidor", você será solicitado a fornecer o nome de usuário e senha (ele será preenchido automaticamente com seu nome de usuário do macOS). Você tem a opção de colocar a chave da conta de armazenamento/nome da conta de armazenamento em seu conjunto de chaves do macOS.

4. **Use o compartilhamento de arquivos do Azure conforme desejado**: depois de substituir o nome de compartilhamento e a chave da conta de armazenamento pelo nome de usuário e senha, o compartilhamento será montado. Você pode usar isso como você normalmente usa um compartilhamento de arquivo/pasta local, incluindo a opção de arrastar e soltar arquivos no compartilhamento de arquivos:

    ![Um instantâneo de um compartilhamento de arquivos montado do Azure](./media/storage-how-to-use-files-mac/mount-via-finder-3.png)

## <a name="mount-an-azure-file-share-via-terminal"></a>Montar um compartilhamento de arquivos do Azure por meio do Terminal
1. Substitua  `<storage-account-name>`  pelo nome de sua conta de armazenamento. Quando solicitado, forneça a chave da conta de armazenamento como a senha. 

    ```
    mount_smbfs //<storage-account-name>@<storage-account-name>.file.core.windows.net/<share-name> <desired-mount-point>
    ```

2. **Use o compartilhamento de arquivos do Azure conforme desejado**: o compartilhamento de arquivos do Azure será montado no ponto de montagem especificado pelo comando anterior.  

    ![Um instantâneo do compartilhamento de arquivos montado do Azure](./media/storage-how-to-use-files-mac/mount-via-terminal-1.png)

## <a name="next-steps"></a>Próximas etapas
Veja estes links para obter mais informações sobre o Arquivos do Azure.

* [Artigo de suporte da Apple - Como conectar-se com o compartilhamento de arquivos em seu Mac](https://support.apple.com/HT204445)
* [Perguntas frequentes](../storage-files-faq.md)
* [Solução de problemas no Windows](storage-troubleshoot-windows-file-connection-problems.md)      
* [Solução de problemas no Linux](storage-troubleshoot-linux-file-connection-problems.md)    
