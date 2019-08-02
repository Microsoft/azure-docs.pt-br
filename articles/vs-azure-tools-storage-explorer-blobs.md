---
title: Gerenciar recursos do Armazenamento de Blobs com o Gerenciador de Armazenamento do Microsoft Azure | Microsoft Docs
description: Gerenciar Contêineres de Blob do Azure com o Gerenciador de Armazenamento do Azure
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: 2f09e545-ec94-4d89-b96c-14783cc9d7a9
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2019
ms.author: cawa
ms.openlocfilehash: 56c20c995a95058b5039b7268c7b7b1426e900fa
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442975"
---
# <a name="manage-azure-blob-storage-resources-with-storage-explorer"></a>Gerenciar recursos de Armazenamento de Blobs com o Gerenciador de Armazenamento do Azure

## <a name="overview"></a>Visão geral

O [Armazenamento de Blobs do Azure](storage/blobs/storage-dotnet-how-to-use-blobs.md) é um serviço para armazenar grandes quantidades de dados não estruturados, como texto ou dados binários, que podem ser acessados de qualquer lugar do mundo por meio de HTTP ou HTTPS.
Você pode usar o armazenamento de Blob para expor dados publicamente para o mundo ou para armazenar dados do aplicativo de forma privada. Neste artigo, você aprenderá a usar o Gerenciador de Armazenamento do Azure para trabalhar com blobs e contêineres de blobs.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte:

* [Baixe e instale o Gerenciador de Armazenamento](https://www.storageexplorer.com)
* [Conectar-se a uma conta de armazenamento do Azure ou serviço](vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-blob-container"></a>Criar um contêiner de blob

Todos os blobs devem residir em um contêiner de blob, que é simplesmente um agrupamento lógico de blobs. Uma conta pode conter um número ilimitado de contêineres, e cada contêiner pode armazenar um número ilimitado de blobs.

As etapas a seguir ilustram como criar um contêiner de blobs no Gerenciador de Armazenamento.

1. Abra o Explorer do Armazenamento.
2. No painel esquerdo, expanda a conta de armazenamento na qual você deseja criar o contêiner de blobs.
3. Clique com botão direito em **Contêineres de Blob** e, no menu de contexto, selecione **Criar Contêiner de Blobs**.

   ![Menu de contexto Criar contêineres de blob][0]
4. Uma caixa de texto será exibida abaixo da pasta **Contêineres de Blob** . Insira o nome de seu contêiner de blobs. Ver [criar um contêiner](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container) para obter informações sobre regras e restrições sobre como nomear os contêineres de blob.

   ![Caixa de texto Criar Contêineres de Blob][1]
5. Pressione **Enter** quanto terminar de criar o contêiner de blobs ou **Esc** para cancelar. Após a criação do contêiner de blobs, ele será exibido na pasta **Contêineres de Blob** da conta de armazenamento selecionada.

   ![Contêiner de blob criado][2]

## <a name="view-a-blob-containers-contents"></a>Exibir o conteúdo de um contêiner de blobs

Os contêineres de blob contêm blobs e pastas (que também podem conter blobs).

As etapas a seguir ilustram como exibir o conteúdo de um contêiner de blobs no Gerenciador de Armazenamento:

1. Abra o Explorer do Armazenamento.
2. No painel esquerdo, expanda a conta de armazenamento que contém o contêiner de blobs que você deseja exibir.
3. Expanda os **Contêineres de Blob**da conta de armazenamento.
4. Clique com o botão direito no contêiner de blob que você deseja exibir e, no menu de contexto, selecione **Abrir Editor do Contêiner de Blobs**.
   Você também pode clicar duas vezes no contêiner de blobs que deseja exibir.

   ![Menu de contexto Abrir editor do contêiner de blobs][19]
5. O painel principal exibirá o conteúdo do contêiner de blobs.

   ![Editor do Contêiner de blobs][3]

## <a name="delete-a-blob-container"></a>Excluir um contêiner de blob

Os contêineres de blob podem ser facilmente criados e excluídos conforme o necessário. (Para saber como excluir blobs individuais, confira a seção [Gerenciando blobs em um contêiner de blobs](#managing-blobs-in-a-blob-container).)

As etapas a seguir ilustram como excluir um contêiner de blobs no Gerenciador de Armazenamento:

1. Abra o Explorer do Armazenamento.
2. No painel esquerdo, expanda a conta de armazenamento que contém o contêiner de blobs que você deseja exibir.
3. Expanda os **Contêineres de Blob**da conta de armazenamento.
4. Clique com o botão direito no contêiner de blobs que você deseja excluir e, no menu de contexto, escolha **Excluir**.
   Você também pode pressionar **Excluir** para excluir o contêiner de blobs selecionado atualmente.

   ![Menu de contexto Excluir contêiner de blobs][4]
5. Escolha **Sim** na caixa de diálogo de confirmação.

   ![Confirmação de exclusão do contêiner de blobs][5]

## <a name="copy-a-blob-container"></a>Copiar um contêiner de blobs

O Gerenciador de Armazenamento permite que você copie um contêiner de blobs na área de transferência e cole esse contêiner de blobs em outra conta de armazenamento. (Para saber como copiar blobs individuais, confira a seção [Gerenciando blobs em um contêiner de blobs](#managing-blobs-in-a-blob-container).)

As etapas a seguir ilustram como copiar um contêiner de blobs de uma conta de armazenamento para outra.

1. Abra o Explorer do Armazenamento.
2. No painel esquerdo, expanda a conta de armazenamento que contém o contêiner de blobs que você deseja copiar.
3. Expanda os **Contêineres de Blob**da conta de armazenamento.
4. Clique com o botão direito no contêiner de blobs que você deseja copiar e, no menu de contexto, escolha **Copiar Contêiner de Blobs**.

   ![Menu de contexto Copiar contêiner de blobs][6]
5. Clique com botão direito na conta de armazenamento de "destino" na qual você deseja colar o contêiner de blobs e, no menu de contexto, escolha **Colar Contêiner de Blobs**.

   ![Menu de contexto Colar contêiner de blobs][7]

## <a name="get-the-sas-for-a-blob-container"></a>Obter a SAS para um contêiner de blob

Uma [SAS (Assinatura de Acesso Compartilhado)](storage/common/storage-dotnet-shared-access-signature-part-1.md) fornece acesso delegado aos recursos da sua conta de armazenamento.
Isso significa que você pode conceder a um cliente permissões limitadas a objetos na sua conta de armazenamento por um período especificado e com um conjunto determinado de permissões, sem precisar compartilhar suas chaves de acesso de conta.

As etapas a seguir ilustram como criar uma SAS para um contêiner de blobs:

1. Abra o Explorer do Armazenamento.
2. No painel esquerdo, expanda a conta de armazenamento que contém o contêiner de blobs para o qual você deseja obter uma SAS.
3. Expanda os **Contêineres de Blob**da conta de armazenamento.
4. Clique com o botão direito no contêiner de blobs desejado e, no menu de contexto, escolha **Obter Assinatura de Acesso Compartilhado**.

   ![Menu de contexto Obter SAS][8]
5. Na caixa de diálogo **Assinatura de Acesso Compartilhado** , especifique a política, as datas de início e de vencimento, o fuso horário e os níveis de acesso que você deseja para o recurso.

   ![Opções de Obter SAS][9]
6. Ao terminar de especificar as opções de SAS, escolha **Criar**.
7. Uma segunda caixa de diálogo de **Assinatura de Acesso Compartilhado** será exibida listando o contêiner de blobs juntamente com a URL e as QueryStrings que você pode usar para acessar o recurso de armazenamento.
   Escolha **Copiar** próximo à URL que você deseja copiar na área de transferência.

   ![Copiar URLs de SAS][10]
8. Ao terminar, escolha **Fechar**.

## <a name="manage-access-policies-for-a-blob-container"></a>Gerenciar políticas de acesso para um contêiner de blobs

As etapas a seguir ilustram como gerenciar (adicionar e remover) políticas de acesso para um contêiner de blobs:

1. Abra o Explorer do Armazenamento.
2. No painel esquerdo, expanda a conta de armazenamento que contém o contêiner de blobs cujas políticas de acesso você deseja gerenciar.
3. Expanda os **Contêineres de Blob**da conta de armazenamento.
4. Escolha o contêiner de blobs desejado e, no menu de contexto, escolha **Gerenciar Políticas de Acesso**.

   ![Menu de contexto Gerenciar políticas de acesso][11]
5. A caixa de diálogo **Políticas de Acesso** listará todas as políticas de acesso que já foram criadas para o contêiner de blobs selecionado.

   ![Opções de Política de Acesso][12]
6. Execute estas etapas, dependendo da tarefa de gerenciamento de política de acesso:

   * **Adicionar uma nova política de acesso** — escolha **Adicionar**. Uma vez gerada, a caixa de diálogo **Políticas de Acesso** exibirá a política de acesso recém-adicionada (com configurações padrão).
   * **Editar uma política de acesso** — faça as edições desejadas e escolha **Salvar**.
   * **Remover uma política de acesso** — escolha **Remover** ao lado da política de acesso que você deseja remover.

## <a name="set-the-public-access-level-for-a-blob-container"></a>Definir o nível de acesso público para um contêiner de blobs

Por padrão, cada contêiner de blobs é definido como "Sem acesso público".

As etapas a seguir ilustram como especificar um nível de acesso público para um contêiner de blobs.

1. Abra o Explorer do Armazenamento.
2. No painel esquerdo, expanda a conta de armazenamento que contém o contêiner de blobs cujas políticas de acesso você deseja gerenciar.
3. Expanda os **Contêineres de Blob**da conta de armazenamento.
4. Escolha o contêiner de blobs desejado e, no menu de contexto, escolha **Definir o Nível de Acesso Público**.

   ![Menu de contexto Definir nível de acesso público][13]
5. Na caixa de diálogo **Definir o Nível de Acesso Público do Contêiner** , especifique o nível de acesso desejado.

   ![Opções de Definir nível de acesso público][14]
6. Escolha **Aplicar**.

## <a name="managing-blobs-in-a-blob-container"></a>Gerenciamento de blobs em um contêiner de blobs

Depois de criar um contêiner de blob, você pode carregar um blob nesse contêiner de blob, baixar um blob em seu computador local, abrir um blob em seu computador local e muito mais.

As etapas a seguir ilustram como gerenciar os blobs (e pastas) dentro de um contêiner de blobs.

1. Abra o Explorer do Armazenamento.
2. No painel esquerdo, expanda a conta de armazenamento que contém o contêiner de blobs que você deseja gerenciar.
3. Expanda os **Contêineres de Blob**da conta de armazenamento.
4. Clique duas vezes no contêiner de blobs que você deseja exibir.
5. O painel principal exibirá o conteúdo do contêiner de blobs.

   ![Exibir contêiner de blobs][3]
6. O painel principal exibirá o conteúdo do contêiner de blobs.
7. Execute estas etapas, dependendo da tarefa que deseja executar:

   * **Carregar arquivos em um contêiner de blobs**

     1. Na barra de ferramentas do painel principal, escolha **Carregar** e **Carregar Arquivos** no menu suspenso.

        ![Menu Carregar arquivos][15]
     2. Na caixa de diálogo **Carregar arquivos**, escolha o botão de reticências ( **...** ) no lado direito da caixa de texto **Arquivos** para selecionar os arquivos que você deseja carregar.

        ![Opções de Carregar arquivos][16]
     3. Especifique o tipo de **Tipo de blob**. Ver [criar um contêiner](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container) para obter mais informações.
     4. Como opção, especifique uma pasta de destino na qual os arquivos selecionados serão carregados. Se a pasta de destino não existir, ela será criada.
     5. Escolha **Carregar**.
   * **Carregar uma pasta em um contêiner de blobs**

     1. Na barra de ferramentas do painel principal, escolha **Carregar** e **Carregar Pasta** no menu suspenso.

        ![Menu Carregar pasta][17]
     2. Na caixa de diálogo **Carregar pasta**, escolha o botão de reticências ( **...** ) no lado direito da caixa de texto **Pasta** para selecionar a pasta cujo conteúdo você deseja carregar.

        ![Opções de Carregar pasta][18]
     3. Especifique o tipo de **Tipo de blob**. Ver [criar um contêiner](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container) para obter mais informações.
     4. Como opção, especifique uma pasta de destino na qual o conteúdo da pasta selecionada será carregado. Se a pasta de destino não existir, ela será criada.
     5. Escolha **Carregar**.
   * **Baixar um blob em seu computador local**

     1. Selecione o blob que você deseja baixar.
     2. Na barra de ferramentas do painel principal, escolha **Baixar**.
     3. Na caixa de diálogo **Especifique onde salvar o blob baixado** , especifique o local onde você deseja baixar o blob, e o nome que deseja dar a ele.  
     4. Clique em **Salvar**.
   * **Abrir um blob em seu computador local**

     1. Selecione o blob que você deseja abrir.
     2. Na barra de ferramentas do painel principal, escolha **Abrir**.
     3. O blob será baixado e aberto usando o aplicativo associado ao tipo de arquivo subjacente do blob.
   * **Copiar um blob na área de transferência**

     1. Escolha o blob que você deseja copiar.
     2. Na barra de ferramentas do painel principal, escolha **Copiar**.
     3. No painel esquerdo, navegue até outro contêiner de blobs e clique duas vezes nele para exibi-lo no painel principal.
     4. Na barra de ferramentas do painel principal, escolha **Colar** para criar uma cópia do blob.
   * **Excluir um blob**

     1. Selecione o blob que você deseja excluir.
     2. Na barra de ferramentas do painel principal, escolha **Excluir**.
     3. Escolha **Sim** na caixa de diálogo de confirmação.

## <a name="next-steps"></a>Próximas etapas

* Exibir as [notas de versão e os vídeos mais recentes do Gerenciador de Armazenamento](https://www.storageexplorer.com).
* Saiba como [criar aplicativos usando os blobs, tabelas, filas e arquivos do Azure](https://azure.microsoft.com/documentation/services/storage/).

[0]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-create-context-menu.png
[1]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create.png
[2]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create-done.png
[3]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-editor.png
[4]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-context-menu.png
[5]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-confirmation.png
[6]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-copy-context-menu.png
[7]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-paste-context-menu.png
[8]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-context-menu.png
[9]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-options.png
[10]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-urls.png
[11]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-context-menu.png
[12]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-options.png
[13]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-context-menu.png
[14]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-options.png
[15]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-menu.png
[16]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-options.png
[17]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-menu.png
[18]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-options.png
[19]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-open-editor-context-menu.png
