---
title: Como gerenciar os Arquivos do Azure no portal do Azure | Microsoft Docs
description: Saiba como usar o portal do Azure para gerenciar os Arquivos do Azure.
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: renash
ms.openlocfilehash: e56f8bf1057a8bc2cfcde841f69022104bafff27
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-azure-files-from-the-azure-portal"></a>Como usar os Arquivos do Azure desde o Portal do Azure
O [portal do Azure](https://portal.azure.com) fornece uma interface do usuário para gerenciar os Arquivos do Azure. Você pode executar as seguintes ações em seu navegador da Web:

* Criar um compartilhamento de arquivos
* Carregar e baixar arquivos de e para o compartilhamento de arquivos.
* Monitorar o uso real de cada compartilhamento de arquivos.
* Ajustar a cota de tamanho do compartilhamento de arquivos.
* Copie os comandos de montagem para montar seu compartilhamento de arquivos de um cliente Windows ou Linux.

## <a name="create-file-share"></a>Criar o compartilhamento de arquivos
1. Entre no Portal do Azure.
2. No menu de navegação, clique em **Contas de armazenamento** ou em **Contas de armazenamento (clássico)**.
    
    ![Captura de tela que mostra como criar o compartilhamento de arquivos no portal](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share1.png)

3. Escolha sua conta de armazenamento.

    ![Captura de tela que mostra como criar o compartilhamento de arquivos no portal](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share2.png)

4. Escolha o serviço "Arquivos".

    ![Captura de tela que mostra como criar o compartilhamento de arquivos no portal](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share3.png)

5. Clique em "Compartilhamentos de arquivos" e siga o link para criar o primeiro compartilhamento de arquivos.

    ![Captura de tela que mostra como criar o compartilhamento de arquivos no portal](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share4.png)

6. Preencha o nome e o tamanho do compartilhamento de arquivos (até 5120 GB) para criar o primeiro compartilhamento de arquivos. Quando o compartilhamento de arquivos tiver sido criado, você poderá montá-lo em qualquer sistema de arquivos que dê suporte a SMB 2.1 ou SMB 3.0. Você pode clicar em **Cota** no compartilhamento de arquivos para alterar o tamanho do arquivo até 5120 GB. Consulte [Calculadora de Preços do Azure](https://azure.microsoft.com/pricing/calculator/) para estimar o custo de armazenamento ao usar os Arquivos do Azure.

    ![Captura de tela que mostra como criar o compartilhamento de arquivos no portal](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share5.png)

## <a name="upload-and-download-files"></a>Carregar e baixar arquivos
1. Escolha um compartilhamento de arquivos que já tenha sido criado.

    ![Captura de tela que mostra como carregar e baixar arquivos do portal](./media/storage-how-to-use-files-portal/use-files-portal-upload-file1.png)

2. Clique em **Carregar** para abrir a interface do usuário para o carregamento de arquivos.

    ![Captura de tela que mostra como carregar arquivos do portal](./media/storage-how-to-use-files-portal/use-files-portal-upload-file2.png)

## <a name="connect-to-file-share"></a>Conectar-se ao compartilhamento de arquivos
-  Clique em **Conectar** para obter a linha de comando para montar o compartilhamento de arquivos do Windows e do Linux. Para os usuários do Linux, você também pode consultar [Como usar os Arquivos do Azure com Linux](../storage-how-to-use-files-linux.md) para obter mais instruções de montagem para outras distribuições do Linux.

    ![Captura de tela que mostra como montar o compartilhamento de arquivos](./media/storage-how-to-use-files-portal/use-files-portal-connect.png)
-  Você pode copiar os comandos para montagem do compartilhamento de arquivos no Windows ou Linux e executá-los da VM do Azure ou do computador local.

    ![Captura de tela que mostra os comandos de montagem para Windows e Linux](./media/storage-how-to-use-files-portal/use-files-portal-show-mount-commands.png)

**Dica:**  
Para localizar a chave de acesso de conta de armazenamento para montagem, clique em **Exibir as teclas de acesso para esta conta de armazenamento** na parte inferior da página de conexão.

## <a name="see-also"></a>Consulte também
Veja estes links para obter mais informações sobre o Arquivos do Azure.

* [Perguntas frequentes](../storage-files-faq.md)
* [Solução de problemas no Windows](storage-troubleshoot-windows-file-connection-problems.md)      
* [Solução de problemas no Linux](storage-troubleshoot-linux-file-connection-problems.md)    
