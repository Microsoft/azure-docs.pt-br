---
title: Usar o Gerenciador de Armazenamento do Azure com o Azure Data Lake Storage Gen2
description: Use o Gerenciador de Armazenamento do Azure para gerenciar diretórios e ACLs (listas de controle de acesso) de arquivos e diretórios nas contas de armazenamento que têm um HNS (namespace hierárquico) habilitado.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e6147918e7cd56aed5b5b333a8e9825a34d60fd4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100652268"
---
# <a name="use-azure-storage-explorer-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Usar o Gerenciador de Armazenamento do Azure para gerenciar diretórios e arquivos no Azure Data Lake Storage Gen2

Este artigo mostra como usar o [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para criar e gerenciar diretórios e arquivos nas contas de armazenamento que têm um HNS (namespace hierárquico) habilitado.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

- Uma conta de armazenamento precisa ter o HNS (namespace hierárquico) habilitado. Siga [estas](../common/storage-account-create.md) instruções para criar um.

- Gerenciador de Armazenamento do Azure instalado no seu computador local. Para instalar o Gerenciador de Armazenamento do Azure para Windows, Macintosh ou Linux, confira o [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/).

> [!NOTE]
> O Gerenciador de Armazenamento usa os [pontos de extremidade](../common/storage-private-endpoints.md#private-endpoints-for-azure-storage) do Blob e do DFS (Data Lake Storage Gen2) ao trabalhar com o Azure Data Lake Storage Gen2. Se o acesso ao Azure Data Lake Storage Gen2 for configurado usando pontos de extremidade privados, verifique se dois pontos de extremidade privados foram criados para a conta de armazenamento: um com o sub-recurso de destino `blob` e o outro com o sub-recurso de destino `dfs`.

## <a name="sign-in-to-storage-explorer"></a>Conectar-se ao Gerenciador de Armazenamento

Quando você iniciar o Gerenciador de Armazenamento pela primeira vez, a janela **Gerenciador de Armazenamento do Microsoft Azure - Conectar** será exibida. Embora o Gerenciador de Armazenamento forneça várias maneiras de se conectar às contas de armazenamento, apenas uma maneira tem suporte atualmente para gerenciar ACLs.

|Tarefa|Finalidade|
|---|---|
|Adicionar uma conta do Azure | Redireciona você para a página de entrada de sua organização para autenticá-lo no Azure. Atualmente, esse será o método de autenticação com suporte apenas se você quiser gerenciar e definir ACLs.|
|Usar uma cadeia de conexão ou um URI de assinatura de acesso compartilhado | Podem ser usados para acessar diretamente um contêiner ou uma conta de armazenamento com um token SAS ou uma cadeia de conexão compartilhada. |
|Usar um nome e uma chave da conta de armazenamento| Use o nome e a chave da sua conta de armazenamento para se conectar ao armazenamento do Azure.|

Selecione **Adicionar uma Conta do Azure** e clique em **Entrar...** . Siga os avisos da tela para entrar na sua conta do Azure.

![Captura de tela que mostra o Gerenciador de Armazenamento do Microsoft Azure e realça a opção Adicionar uma Conta do Azure e o botão Entrar.](media/storage-quickstart-blobs-storage-explorer/connect.png)

Ao concluir a conexão, o Gerenciador de Armazenamento do Azure carrega exibindo a guia **Explorer**. Essa exibição lhe dá informações de todas suas contas de armazenamento do Azure e do armazenamento local, configuradas por meio do [emulador de armazenamento do Azurite](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), das contas do [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ou dos ambientes do [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

![Gerenciador de Armazenamento do Microsoft Azure – Janela Conexão](media/storage-quickstart-blobs-storage-explorer/mainpage.png)

## <a name="create-a-container"></a>Criar um contêiner

Um contêiner armazena diretórios e arquivos. Para criar um, expanda a conta de armazenamento criada por você na etapa a seguir. Selecione **Contêineres de Blob**, clique com o botão direito do mouse e selecione **Criar Contêiner de Blob**. Insira o nome do seu contêiner. Confira a seção [Criar um contêiner](storage-quickstart-blobs-dotnet.md#create-a-container) para obter uma lista de regras e restrições sobre como dar nome aos contêineres. Ao concluir, pressione **Enter** para criar o contêiner. Após a criação com sucesso do contêiner, ele será exibido na pasta **Contêineres de Blob** da conta de armazenamento selecionada.

![Gerenciador de Armazenamento do Microsoft Azure – Criando um contêiner](media/data-lake-storage-explorer/creating-a-filesystem.png)

## <a name="create-a-directory"></a>Criar um diretório

Para criar um diretório, selecione na etapa a seguir o contêiner que você criou. Na faixa de opções do contêiner, escolha o botão **Nova pasta**. Insira o nome do seu diretório. Ao concluir, pressione **Enter** para criar o diretório. Depois que o diretório tiver sido criado com sucesso, ele aparecerá na janela do editor.

![Gerenciador de Armazenamento do Microsoft Azure – Criando um diretório](media/data-lake-storage-explorer/creating-a-directory.png)

## <a name="upload-blobs-to-the-directory"></a>Carregar blobs para o diretório

Na faixa de opções do diretório, escolha o botão **Carregar**. Essa operação lhe dá a opção de carregar um arquivo ou uma pasta.

Escolha os arquivos ou pastas a serem carregados.

![Gerenciador de Armazenamento do Microsoft Azure – carregar um blob](media/data-lake-storage-explorer/upload-file.png)

Ao selecionar **OK**, os arquivos selecionados são colocados na fila para carregamento e cada arquivo é carregado. Quando o carregamento for concluído, os resultados são mostrados na janela **Atividades**.

## <a name="view-blobs-in-a-directory"></a>Exibir blobs em um diretório

No aplicativo **Gerenciador de Armazenamento do Azure**, selecione um diretório em uma conta de armazenamento. O painel principal mostra uma lista dos blobs no diretório selecionado.

![Gerenciador de Armazenamento do Microsoft Azure – listar blobs em um diretório](media/data-lake-storage-explorer/list-files.png)

## <a name="download-blobs"></a>Baixar blobs

Para baixar arquivos usando o **Gerenciador de Armazenamento do Azure**, com um arquivo selecionado, escolha **Baixar** na faixa de opções. Uma caixa de diálogo é aberta, na qual é possível inserir um nome de arquivo. Selecione **Salvar** para iniciar o download do arquivo na unidade local.

## <a name="next-steps"></a>Próximas etapas

Saiba como gerenciar a permissão de arquivos e diretórios definindo ACLs (listas de controle de acesso)

> [!div class="nextstepaction"]
> [Usar o Gerenciador de Armazenamento do Azure para gerenciar o acesso no Azure Data Lake Storage Gen2](./data-lake-storage-explorer-acl.md)
