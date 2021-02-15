---
title: Usar o Gerenciador de Armazenamento com o Armazenamento de Arquivos do Azure | Microsoft Docs
description: Aprenda a usar o Gerenciador de Armazenamento para trabalhar com compartilhamento de arquivos e arquivos.
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: ''
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/09/2017
ms.author: cawa
ms.openlocfilehash: 84f6473c25a5be11eeda7cd2b311d93a7226a78c
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96488384"
---
# <a name="using-storage-explorer-with-azure-file-storage"></a>Uso do Gerenciador de Armazenamento com o armazenamento de arquivos do Azure

O armazenamento de Arquivos do Azure é um serviço que oferece compartilhamentos de arquivos na nuvem usando o Protocolo SMB (Server Message Block) padrão. Há suporte ao SMB 2.1 e ao 3.0 SMB. Com o armazenamento de Arquivos do Azure, você pode migrar aplicativos herdados que dependem de compartilhamentos de arquivos para o Azure rapidamente e sem regravações caras. Você pode usar o Armazenamento de Arquivos para expor dados publicamente para o mundo ou para armazenar dados do aplicativo de forma privada. Neste artigo, você aprenderá a usar o Gerenciador de Armazenamento para trabalhar com compartilhamento de arquivos e arquivos.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte:

- [Baixe e instale o Gerenciador de Armazenamento](https://www.storageexplorer.com/)

- [Conectar-se a uma conta de armazenamento do Azure ou serviço](./vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-file-share"></a>Criar um compartilhamento de arquivos

Todos os arquivos devem residir em um compartilhamento de arquivos, que é simplesmente um agrupamento lógico de arquivos. Uma conta pode conter um número ilimitado de compartilhamentos de arquivo, e cada compartilhamento pode armazenar um número ilimitado de arquivos.

As etapas a seguir ilustram como criar um compartilhamento de arquivos no Gerenciador de Armazenamento.

1. Abra o Explorer do Armazenamento.

1. No painel esquerdo, expanda a conta de armazenamento na qual você deseja criar o compartilhamento de arquivos

1. Clique com botão direito do mouse em **Compartilhamentos de Arquivos** e, no menu de contexto, selecione **Criar Compartilhamento de Arquivos**.

    ![Criar o compartilhamento de arquivos](media/vs-azure-tools-storage-explorer-files/image1.png)

1. Uma caixa de texto será exibida abaixo da pasta **Compartilhamentos de Arquivos** . Insira o nome do compartilhamento de arquivo. Confira a seção [Regras de nomenclatura do compartilhamento](./storage/blobs/storage-quickstart-blobs-dotnet.md) para obter uma lista de regras e restrições sobre como nomear os compartilhamentos de arquivos.

    ![Nomeação do compartilhamento](media/vs-azure-tools-storage-explorer-files/image2.png)

1. Pressione **Enter** quanto terminar de criar o compartilhamento de arquivos ou **Esc** para cancelar. Após a criação do compartilhamento de arquivos, ele será exibido na pasta **Compartilhamentos de Arquivos** da conta de armazenamento selecionada.

    ![O novo compartilhamento](media/vs-azure-tools-storage-explorer-files/image3.png)

## <a name="view-a-file-shares-contents"></a>Exibir o conteúdo de um compartilhamento de arquivos

Compartilhamentos de arquivos contêm arquivos e pastas (que também podem conter arquivos).

As etapas a seguir ilustram como exibir o conteúdo de um compartilhamento de arquivos no Gerenciador de Armazenamento:+

1. Abra o Explorer do Armazenamento.

1. No painel esquerdo, expanda a conta de armazenamento que contém o compartilhamento de arquivos que você deseja exibir.

1. Expanda os **Compartilhamentos de arquivos** da conta de armazenamento.

1. Clique com o botão direito do mouse no compartilhamento de arquivos que você deseja exibir e, no menu de contexto, escolha **Abrir**. Você também pode clicar duas vezes no compartilhamento de arquivos que deseja exibir.

    ![Abrir compartilhamento](media/vs-azure-tools-storage-explorer-files/image4.png)

1. O painel principal exibirá o conteúdo do compartilhamento de arquivos.
    
    ![Captura de tela do painel principal para um compartilhamento de arquivos no Gerenciador de Armazenamento mostrando o conteúdo do compartilhamento.](media/vs-azure-tools-storage-explorer-files/image5.png)

## <a name="delete-a-file-share"></a>Excluir um compartilhamento de arquivos

Os compartilhamentos de arquivos podem ser criados e excluídos facilmente conforme a necessidade. (Para saber como excluir arquivos individuais, consulte a seção [Gerenciar arquivos em um compartilhamento de arquivo](./vs-azure-tools-storage-explorer-blobs.md#managing-blobs-in-a-blob-container).)

As etapas a seguir ilustram como excluir um compartilhamento de arquivos no Gerenciador de Armazenamento:

1. Abra o Explorer do Armazenamento.

1. No painel esquerdo, expanda a conta de armazenamento que contém o compartilhamento de arquivos que você deseja exibir.

1. Expanda os **Compartilhamentos de arquivos** da conta de armazenamento.

1. Clique com o botão direito do mouse no compartilhamento de arquivos que você deseja excluir e, no menu de contexto, escolha **Excluir**. Você também pode pressionar **Excluir** para excluir o compartilhamento de arquivos selecionado atualmente.

    ![Excluir](media/vs-azure-tools-storage-explorer-files/image6.png)

1. Escolha **Sim** na caixa de diálogo de confirmação.
    
    ![Diálogo de confirmação](media/vs-azure-tools-storage-explorer-files/image7.png)

## <a name="copy-a-file-share"></a>Copiar um compartilhamento de arquivos

O Gerenciador de Armazenamento permite que você copie um compartilhamento de arquivos na área de transferência e cole esse compartilhamento de arquivos em outra conta de armazenamento. (Para saber como copiar arquivos individuais, consulte a seção [Gerenciar arquivos em um compartilhamento de arquivo](./vs-azure-tools-storage-explorer-blobs.md#managing-blobs-in-a-blob-container).)

As etapas a seguir ilustram como copiar um compartilhamento de arquivos de uma conta de armazenamento para outra.

1. Abra o Explorer do Armazenamento.

1. No painel esquerdo, expanda a conta de armazenamento contendo o compartilhamento de arquivos que você deseja copiar.

1. Expanda os **Compartilhamentos de arquivos** da conta de armazenamento.

1. Clique com o botão direito do mouse no compartilhamento de arquivos que você deseja copiar e, no menu de contexto, escolha **Copiar Compartilhamento de Arquivos**.

    ![Copiar o compartilhamento de arquivos](media/vs-azure-tools-storage-explorer-files/image8.png)

1. Clique com botão direito do mouse na conta de armazenamento de "destino" na qual você deseja colar o compartilhamento de arquivos e, no menu de contexto, escolha **Colar Compartilhamento de Arquivos**.

    ![Colar o compartilhamento de arquivos](media/vs-azure-tools-storage-explorer-files/image9.png)

## <a name="get-the-sas-for-a-file-share"></a>Obter a SAS para um compartilhamento de arquivos

Uma [SAS (assinatura de acesso compartilhado)](./storage/common/storage-sas-overview.md) fornece acesso delegado aos recursos em sua conta de armazenamento. Isso significa que você pode conceder a um cliente permissões limitadas a objetos na sua conta de armazenamento por um período especificado e com um conjunto determinado de permissões, sem precisar compartilhar suas chaves de acesso de conta.

As etapas a seguir ilustram como criar uma SAS para um compartilhamento de arquivos:+

1. Abra o Explorer do Armazenamento.

1. No painel esquerdo, expanda a conta de armazenamento que contém o compartilhamento de arquivos para o qual você deseja obter uma SAS.

1. Expanda os **Compartilhamentos de arquivos** da conta de armazenamento.

1. Clique com o botão direito do mouse no compartilhamento de arquivos desejado e, no menu de contexto, escolha **Obter Assinatura de Acesso Compartilhado**.

    ![Obter Assinatura de Acesso Compartilhado](media/vs-azure-tools-storage-explorer-files/image10.png)

1. Na caixa de diálogo **Assinatura de Acesso Compartilhado** , especifique a política, as datas de início e de vencimento, o fuso horário e os níveis de acesso que você deseja para o recurso.

    ![Diálogo Obter SAS](media/vs-azure-tools-storage-explorer-files/image11.png)

1. Ao terminar de especificar as opções de SAS, escolha **Criar**.

1. Uma segunda caixa de diálogo de **Assinatura de Acesso Compartilhado** será exibida listando o compartilhamento de arquivos juntamente com a URL e as QueryStrings que você pode usar para acessar o recurso de armazenamento. Escolha **Copiar** próximo à URL que você deseja copiar na área de transferência.
    
    ![Segundo diálogo SAS](media/vs-azure-tools-storage-explorer-files/image12.png)

1. Ao terminar, escolha **Fechar**.

## <a name="manage-access-policies-for-a-file-share"></a>Gerenciar políticas de acesso para um compartilhamento de arquivos

As etapas abaixo ilustram como gerenciar (adicionar e remover) políticas de acesso de um compartilhamento de arquivos:+. As políticas de acesso são usadas para criar URLs de SAS que podem ser usadas para acessar o recurso Arquivo de Armazenamento durante um período de tempo definido.

1. Abra o Explorer do Armazenamento.

1. No painel esquerdo, expanda a conta de armazenamento que contém o compartilhamento de arquivos cujas políticas de acesso você deseja gerenciar.

1. Expanda os **Compartilhamentos de arquivos** da conta de armazenamento.

1. Escolha o compartilhamento de arquivos desejado e, no menu de contexto, escolha **Gerenciar Políticas de Acesso**.

    ![Menu de contexto Gerenciar políticas de acesso](media/vs-azure-tools-storage-explorer-files/image13.png)

1. A caixa de diálogo **Políticas de Acesso** listará todas as políticas de acesso que já foram criadas para o compartilhamento de arquivos selecionado.
    
    ![Políticas de acesso](media/vs-azure-tools-storage-explorer-files/image14.png)

1. Execute estas etapas, dependendo da tarefa de gerenciamento de política de acesso:
    
    - **Adicionar uma nova política de acesso** — escolha **Adicionar**. Uma vez gerada, a caixa de diálogo **Políticas de Acesso** exibirá a política de acesso recém-adicionada (com configurações padrão).

    - **Editar uma política de acesso** -faça as edições desejadas e selecione **salvar**.

    - **Remover uma política de acesso** — escolha **Remover** ao lado da política de acesso que você deseja remover.

1. Crie uma nova URL de SAS usando a política de acesso que você criou anteriormente:
    
    ![Obter SAS](media/vs-azure-tools-storage-explorer-files/image15.png)
    
    ![Nome e propriedades SAS](media/vs-azure-tools-storage-explorer-files/image16.png)

## <a name="managing-files-in-a-file-share"></a>Gerenciando arquivos em um compartilhamento de arquivos

Depois de criar um compartilhamento de arquivos, você pode carregar um arquivo nesse compartilhamento de arquivos, baixar um arquivo em seu computador local, abrir um arquivo em seu computador local e muito mais.

As etapas a seguir ilustram como gerenciar os arquivos (e pastas) dentro de um compartilhamento de arquivos.

1.  Abra o Explorer do Armazenamento.

1.  No painel esquerdo, expanda a conta de armazenamento que contém o compartilhamento de arquivos que você deseja gerenciar.

1.  Expanda os **Compartilhamentos de arquivos** da conta de armazenamento.

1.  Clique duas vezes no compartilhamento de arquivos que você deseja exibir.

1.  O painel principal exibirá o conteúdo do compartilhamento de arquivos.

    ![Captura de tela do painel principal do compartilhamento de arquivos myazurefileshare em Gerenciador de Armazenamento, mostrando o conteúdo do compartilhamento com a primeira pasta selecionada.](media/vs-azure-tools-storage-explorer-files/image17.png)

1.  O painel principal exibirá o conteúdo do compartilhamento de arquivos.

1.  Execute estas etapas, dependendo da tarefa que deseja executar:

    - **Carregar arquivos em um compartilhamento de arquivos**

        a.  Na barra de ferramentas do painel principal, selecione **carregar** e, em seguida, **carregar arquivos** no menu suspenso.

        ![Carregar arquivos](media/vs-azure-tools-storage-explorer-files/image18.png)
        
        b. Na caixa de diálogo **Carregar arquivos**, escolha o botão de reticências (**...**) no lado direito da caixa de texto **Arquivos** para selecionar os arquivos que você deseja carregar.

        ![Adicionando arquivos](media/vs-azure-tools-storage-explorer-files/image19.png)

        c. Escolha **Carregar**.

    - **Carregar uma pasta em um compartilhamento de arquivos**
        
        a. Na barra de ferramentas do painel principal, selecione **carregar** e, em seguida, **carregar pasta** no menu suspenso.

        ![Menu Carregar pasta](media/vs-azure-tools-storage-explorer-files/image20.png)

        b. Na caixa de diálogo **Carregar pasta**, escolha o botão de reticências (**...**) no lado direito da caixa de texto **Pasta** para selecionar a pasta cujo conteúdo você deseja carregar.

        c. Como opção, especifique uma pasta de destino na qual o conteúdo da pasta selecionada será carregado. Se a pasta de destino não existir, ela será criada.

        d. Escolha **Carregar**.

    - **Baixar um arquivo em seu computador local**
        
        a. Selecione o arquivo que você deseja baixar.
        
        b. Na barra de ferramentas do painel principal, escolha **Baixar**.
        
        c. Na caixa de diálogo **Especifique onde salvar o arquivo baixado**, especifique o local onde você deseja baixar o arquivo e o nome que deseja dar a ele.

        d. Clique em **Salvar**.

    - **Abrir um arquivo em seu computador local**
        
        a.  Selecione o arquivo que você deseja abrir.
        
        b.  Na barra de ferramentas do painel principal, escolha **Abrir**.
        
        c.  O arquivo será baixado e aberto usando o aplicativo associado ao tipo de arquivo subjacente.

    - **Copiar um arquivo para a área de transferência**

        a. Escolha o arquivo que você deseja copiar.

        b. Na barra de ferramentas do painel principal, escolha **Copiar**.

        c. No painel esquerdo, navegue até outro compartilhamento de arquivos e clique duas vezes nele para exibi-lo no painel principal.

        d. Na barra de ferramentas do painel principal, escolha **Colar** para criar uma cópia do arquivo.

    - **Excluir um arquivo**

        a. Selecione o arquivo que você deseja excluir.

        b. Na barra de ferramentas do painel principal, escolha **Excluir**.

        c. Escolha **Sim** na caixa de diálogo de confirmação.

## <a name="next-steps"></a>Próximas etapas

- Exibir as [notas de versão e os vídeos mais recentes do Gerenciador de Armazenamento](https://www.storageexplorer.com/).

- Saiba como [criar aplicativos usando os blobs, tabelas, filas e arquivos do Azure](https://azure.microsoft.com/documentation/services/storage/).