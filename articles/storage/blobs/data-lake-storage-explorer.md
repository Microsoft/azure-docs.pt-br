---
title: Usar Gerenciador de Armazenamento do Azure com Azure Data Lake Storage Gen2
description: Use o Gerenciador de Armazenamento do Azure para gerenciar diretórios e listas de controle de acesso (ACL) de arquivos e diretórios em contas de armazenamento que têm o namespace hierárquico (HNS) habilitado.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e6147918e7cd56aed5b5b333a8e9825a34d60fd4
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652268"
---
# <a name="use-azure-storage-explorer-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Use Gerenciador de Armazenamento do Azure para gerenciar diretórios e arquivos no Azure Data Lake Storage Gen2

Este artigo mostra como usar [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para criar e gerenciar diretórios e arquivos em contas de armazenamento que têm o namespace HIERÁRQUICO (HNS) habilitado.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

- Uma conta de armazenamento precisa ter o HNS (namespace hierárquico) habilitado. Siga [estas](../common/storage-account-create.md) instruções para criar um.

- Gerenciador de Armazenamento do Azure instalado no computador local. Para instalar o Gerenciador de Armazenamento do Azure para Windows, Macintosh ou Linux, confira o [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/).

> [!NOTE]
> Gerenciador de Armazenamento usa os [pontos de extremidade](../common/storage-private-endpoints.md#private-endpoints-for-azure-storage) de BLOB (blob) & data Lake Storage Gen2 (DFS) ao trabalhar com Azure data Lake Storage Gen2. Se o acesso ao Azure Data Lake Storage Gen2 for configurado usando pontos de extremidade privados, certifique-se de que dois pontos de extremidade privados sejam criados para a conta de armazenamento: um com o subrecurso de destino `blob` e o outro com o subrecurso de destino `dfs` .

## <a name="sign-in-to-storage-explorer"></a>Conectar-se ao Gerenciador de Armazenamento

Quando você iniciar o Gerenciador de Armazenamento pela primeira vez, a janela **Gerenciador de Armazenamento do Microsoft Azure - Conectar** será exibida. Embora o Gerenciador de Armazenamento forneça várias maneiras de se conectar às contas de armazenamento, apenas uma maneira tem suporte atualmente para gerenciar ACLs.

|Tarefa|Finalidade|
|---|---|
|Adicionar uma conta do Azure | Redireciona você para a página de entrada de sua organização para autenticá-lo no Azure. Atualmente, esse será o método de autenticação com suporte apenas se você quiser gerenciar e definir ACLs.|
|Usar uma cadeia de conexão ou um URI de assinatura de acesso compartilhado | Podem ser usados para acessar diretamente um contêiner ou uma conta de armazenamento com um token SAS ou uma cadeia de conexão compartilhada. |
|Usar um nome e uma chave da conta de armazenamento| Use o nome e a chave da sua conta de armazenamento para se conectar ao armazenamento do Azure.|

Selecione **Adicionar uma Conta do Azure** e clique em **Entrar...** . Siga os avisos da tela para entrar na sua conta do Azure.

![Captura de tela que mostra Gerenciador de Armazenamento do Microsoft Azure e realça a opção Adicionar uma conta do Azure e o botão entrar.](media/storage-quickstart-blobs-storage-explorer/connect.png)

Ao concluir a conexão, o Gerenciador de Armazenamento do Azure carrega exibindo a guia **Explorer**. Essa exibição lhe dá informações de todas suas contas de armazenamento do Azure e do armazenamento local, configuradas por meio do [emulador de armazenamento do Azurite](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), das contas do [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ou dos ambientes do [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

![Gerenciador de Armazenamento do Microsoft Azure – Janela Conexão](media/storage-quickstart-blobs-storage-explorer/mainpage.png)

## <a name="create-a-container"></a>Criar um contêiner

Um contêiner contém diretórios e arquivos. Para criar um, expanda a conta de armazenamento que você criou na etapa de continuação. Selecione **contêineres de blob**, clique com o botão direito do mouse e selecione **criar contêiner de blob**. Insira o nome do seu contêiner. Consulte a seção [criar um contêiner](storage-quickstart-blobs-dotnet.md#create-a-container) para obter uma lista de regras e restrições sobre os contêineres de nomenclatura. Ao concluir, pressione **Enter** para criar o contêiner. Depois que o contêiner tiver sido criado com êxito, ele será exibido na pasta **contêineres de blob** para a conta de armazenamento selecionada.

![Gerenciador de Armazenamento do Microsoft Azure-criando um contêiner](media/data-lake-storage-explorer/creating-a-filesystem.png)

## <a name="create-a-directory"></a>Criar um diretório

Para criar um diretório, selecione o contêiner que você criou na etapa de continuação. Na faixa de seleção do contêiner, escolha o botão **nova pasta** . Insira o nome do seu diretório. Ao concluir, pressione **Enter** para criar o diretório. Depois que o diretório tiver sido criado com êxito, ele aparecerá na janela do editor.

![Gerenciador de Armazenamento do Microsoft Azure-criando um diretório](media/data-lake-storage-explorer/creating-a-directory.png)

## <a name="upload-blobs-to-the-directory"></a>Carregar blobs para o diretório

Na faixa de seleção do diretório, escolha o botão **carregar** . Essa operação lhe dá a opção de carregar um arquivo ou uma pasta.

Escolha os arquivos ou pastas a serem carregados.

![Gerenciador de Armazenamento do Microsoft Azure – carregar um blob](media/data-lake-storage-explorer/upload-file.png)

Ao selecionar **OK**, os arquivos selecionados são colocados na fila para carregamento e cada arquivo é carregado. Quando o carregamento for concluído, os resultados são mostrados na janela **Atividades**.

## <a name="view-blobs-in-a-directory"></a>Exibir blobs em um diretório

No aplicativo **Gerenciador de Armazenamento do Azure**, selecione um diretório em uma conta de armazenamento. O painel principal mostra uma lista dos blobs no diretório selecionado.

![Gerenciador de Armazenamento do Microsoft Azure – listar blobs em um diretório](media/data-lake-storage-explorer/list-files.png)

## <a name="download-blobs"></a>Baixar blobs

Para baixar arquivos usando **Gerenciador de armazenamento do Azure**, com um arquivo selecionado, selecione **baixar** na faixa de opções. Uma caixa de diálogo é aberta, na qual é possível inserir um nome de arquivo. Selecione **salvar** para iniciar o download de um arquivo para o local local.

## <a name="next-steps"></a>Próximas etapas

Saiba como gerenciar a permissão de arquivo e diretório definindo ACLs (listas de controle de acesso)

> [!div class="nextstepaction"]
> [Usar Gerenciador de Armazenamento do Azure para gerenciar ACLs no Azure Data Lake Storage Gen2](./data-lake-storage-explorer-acl.md)
