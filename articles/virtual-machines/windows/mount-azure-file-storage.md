---
title: Montar armazenamento de arquivos do Azure com base em uma VM do Windows Azure | Microsoft Docs
description: "Armazene o arquivo na nuvem com o armazenamento de arquivos do Azure e monte o compartilhamento de arquivos de nuvem de uma VM (máquina virtual) do Azure."
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: 
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 06/15/2017
ms.author: cynthn
ms.openlocfilehash: 6ffb2d2da1e2439df6f5da543411e3c2c68d3435
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-file-shares-with-windows-vms"></a>Usar compartilhamentos de arquivos do Azure com VMs do Windows 

Você pode usar compartilhamentos de arquivos do Azure como uma maneira de armazenar e acessar arquivos na VM. Por exemplo, você pode armazenar um script ou um arquivo de configuração de aplicativo que deseja que todas as VMs compartilhem. Neste tópico, mostramos como criar e montar um compartilhamento de arquivos do Azure e como carregar e baixar arquivos.

## <a name="connect-to-a-file-share-from-a-vm"></a>Conectar a um compartilhamento de arquivo por meio de uma VM

Esta seção pressupõe que você já tenha um compartilhamento de arquivos ao qual deseja se conectar. Se você precisar criar um, confira [Criar um compartilhamento de arquivos](#create-a-file-share) mais adiante neste tópico.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu à esquerda, clique em **Contas de armazenamento**.
3. Escolha sua conta de armazenamento.
4. Na página **Visão geral**, em **Serviços**, selecione **Arquivos**.
5. Selecione um compartilhamento de arquivos.
6. Clique em **Conectar** para abrir uma página que mostra a sintaxe de linha de comando para montar o compartilhamento de arquivos do Windows ou Linux.
7. Realce a sintaxe do comando e cole-a no Bloco de Notas ou em outro lugar em que você possa acessá-la facilmente. 
8. Edite a sintaxe para remover o **> ** inicial e substitua *[letra da unidade]* pela letra da unidade (por exemplo, **y:**) em que você deseja montar o compartilhamento de arquivos.
8. Conecte-se à VM e abra um prompt de comando.
9. Cole a sintaxe de conexão editado e pressione **Enter**.
10. Quando a conexão tiver sido criada, você receberá a mensagem **O comando foi concluído com êxito.**
11. Verifique a conexão digitando a letra da unidade para alternar para a unidade e digite **dir** para ver o conteúdo do compartilhamento de arquivo.



## <a name="create-a-file-share"></a>Criar um compartilhamento de arquivos 
1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu à esquerda, clique em **Contas de armazenamento**.
3. Escolha sua conta de armazenamento.
4. Na página **Visão geral**, em **Serviços**, selecione **Arquivos**.
5. Na página de Serviço de Arquivo, clique em **+Compartilhamento de arquivos** para criar o primeiro compartilhamento de arquivos.\
6. Preencha o nome do compartilhamento de arquivo. Nomes de compartilhamento de arquivos podem usar letras minúsculas, números e hifens únicos. O nome não pode começar com um hífen e não é possível usar várias hifens consecutivos. 
7. Preencha o limite de tamanho do arquivo, até 5120 GB.
8. Clique em **OK** para implantar o compartilhamento de arquivos.
   
## <a name="upload-files"></a>Carregar arquivos
1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu à esquerda, clique em **Contas de armazenamento**.
3. Escolha sua conta de armazenamento.
4. Na página **Visão geral**, em **Serviços**, selecione **Arquivos**.
5. Selecione um compartilhamento de arquivos.
6. Clique em **Carregar** para abrir a página **Carregar arquivos**.
7. Clique no ícone de pasta para procurar no sistema de arquivos local um arquivo para carregar.   
8. Clique em **Carregar** para carregar o arquivo para o compartilhamento de arquivos.

## <a name="download-files"></a>Baixar arquivos
1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu à esquerda, clique em **Contas de armazenamento**.
3. Escolha sua conta de armazenamento.
4. Na página **Visão geral**, em **Serviços**, selecione **Arquivos**.
5. Selecione um compartilhamento de arquivos.
6. Clique com o botão direito do mouse no arquivo e escolha **Baixar** para baixá-lo no computador local.
   

## <a name="next-steps"></a>Próximas etapas

Você também pode criar e gerenciar compartilhamentos de arquivos usando o PowerShell. Para obter mais informações, confira [Introdução ao Armazenamento de Arquivos do Azure no Windows](../../storage/files/storage-dotnet-how-to-use-files.md).
