---
title: Montagem do compartilhamento de arquivos do Azure no SMB com macOS | Microsoft Docs
description: Saiba como montar um compartilhamento de arquivos do Azure via SMB com macOS usando o Finder ou o terminal. Arquivos do Azure é o sistema de arquivos de nuvem fácil de usar da Microsoft.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/23/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 119f4c0ea434bc431b40c905d9142e187b7d9474
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91326058"
---
# <a name="mount-azure-file-share-over-smb-with-macos"></a>Montagem do compartilhamento de arquivos do Azure no SMB com macOS
[Arquivos do Azure](storage-files-introduction.md) é o sistema de arquivos de nuvem fácil de usar da Microsoft. Os compartilhamentos de arquivos do Azure podem ser montados com o protocolo SMB 3 padrão do setor por macOS High Sierra 10.13 +. Este artigo mostra duas maneiras diferentes para montar um compartilhamento de arquivos do Azure no macOS: com a interface do usuário do Finder e usando o Terminal.

## <a name="prerequisites-for-mounting-an-azure-file-share-on-macos"></a>Pré-requisitos para montar um compartilhamento de arquivos do Azure no macOS
* **Nome da conta de armazenamento**: Para montar um compartilhamento de arquivos do Azure, você precisará do nome da conta de armazenamento.

* **Chave de conta de armazenamento**: Para montar um compartilhamento de arquivos do Azure, você precisará da chave de armazenamento primária (ou secundária). Atualmente, as chaves SAS não têm suporte para montagem.

* **Certifique-se de que a porta 445 está aberta**: SMB se comunica pela porta TCP 445. No computador cliente (Mac), verifique se o firewall não está bloqueando a porta TCP 445.

## <a name="mount-an-azure-file-share-via-finder"></a>Montar um compartilhamento de arquivos do Azure por meio do Localizador
1. **Abrir o Localizador**: o Localizador é aberto no macOS por padrão, mas você pode garantir que ele é o aplicativo selecionado clicando no "ícone de rosto do macOS" no encaixe:  
    ![O ícone de rosto do macOS](./media/storage-how-to-use-files-mac/mount-via-finder-1.png)

2. **Selecione "conectar ao servidor" no menu "ir"**: usando o caminho UNC dos pré-requisitos, converta a barra invertida dupla inicial ( `\\` ) para `smb://` e todas as barras invertidas ( `\` ) para encaminhar barras () `/` . O link deve ser semelhante ao seguinte: ![a caixa de diálogo "Conectar ao servidor"](./media/storage-how-to-use-files-mac/mount-via-finder-2.png)

3. **Use o nome da conta de armazenamento e a chave da conta de armazenamento quando solicitado a fornecer um nome de usuário e senha**: ao clicar em "Conectar" na caixa de diálogo "Conectar ao servidor", você será solicitado a fornecer o nome de usuário e senha (Isso será preenchido automaticamente com seu nome de usuário macOS). Você tem a opção de colocar a chave da conta de armazenamento/nome da conta de armazenamento em seu conjunto de chaves do macOS.

4. **Use o compartilhamento de arquivos do Azure conforme desejado**: depois de substituir o nome do compartilhamento e a chave da conta de armazenamento em para o nome de usuário e a senha, o compartilhamento será montado. Você pode usar isso como você normalmente usa um compartilhamento de arquivo/pasta local, incluindo a opção de arrastar e soltar arquivos no compartilhamento de arquivos:

    ![Um instantâneo de um compartilhamento de arquivos montado do Azure](./media/storage-how-to-use-files-mac/mount-via-finder-3.png)

## <a name="mount-an-azure-file-share-via-terminal"></a>Montar um compartilhamento de arquivos do Azure por meio do Terminal
1. Substitua  `<storage-account-name>` , `<storage-account-key>` e `<share-name>`   pelos valores apropriados para o seu ambiente.

    ```
    open smb://<storage-account-name>:<storage-account-key>@<storage-account-name>.file.core.windows.net/<share-name>
    ```

2. **Use o compartilhamento de arquivos do Azure conforme desejado**: o compartilhamento de arquivos do Azure será montado no ponto de montagem especificado pelo comando anterior.  

    ![Um instantâneo do compartilhamento de arquivos montado do Azure](./media/storage-how-to-use-files-mac/mount-via-terminal-1.png)

## <a name="next-steps"></a>Próximas etapas
* [Conectar seu Mac a computadores e servidores compartilhados-suporte da Apple](https://support.apple.com/guide/mac-help/connect-mac-shared-computers-servers-mchlp1140/mac)