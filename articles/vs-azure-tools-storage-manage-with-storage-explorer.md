---
title: Introdução ao Gerenciador de Armazenamento | Microsoft Docs
description: Comece a gerenciar os recursos de armazenamento do Azure com o Gerenciador de Armazenamento. Baixe e instale o Gerenciador de Armazenamento do Azure, conecte-se a uma conta de armazenamento ou serviço e muito mais.
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2019
ms.author: cawa
ms.openlocfilehash: be9b2d9a31d4affc9615f5d2f4b2585b7533a0f6
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95545900"
---
# <a name="get-started-with-storage-explorer"></a>Introdução ao Gerenciador de Armazenamento

## <a name="overview"></a>Visão geral

O Gerenciador de Armazenamento do Microsoft Azure é um aplicativo autônomo que facilita o trabalho com dados do Armazenamento do Azure no Windows, macOS e Linux. Neste artigo, você aprenderá várias maneiras de se conectar e gerenciar suas contas de armazenamento do Azure.

![Gerenciador do Armazenamento do Microsoft Azure][0]

## <a name="prerequisites"></a>Pré-requisitos

# <a name="windows"></a>[Windows](#tab/windows)

As seguintes versões do Windows dão suporte ao Gerenciador de Armazenamento:

* Windows 10 (recomendado)
* Windows 8
* Windows 7

Para todas as versões do Windows, Gerenciador de Armazenamento requer .NET Framework 4.7.2 no mínimo.

# <a name="macos"></a>[macOS](#tab/macos)

As seguintes versões do suporte para macOS Gerenciador de Armazenamento:

* macOS 10,12 Sierra e versões posteriores

# <a name="linux"></a>[Linux](#tab/linux)

Gerenciador de Armazenamento está disponível no [repositório de snap](https://snapcraft.io/storage-explorer) para as distribuições mais comuns do Linux. Recomendamos o repositório de snap para esta instalação. O snap Gerenciador de Armazenamento instala todas as suas dependências e atualizações quando novas versões são publicadas no repositório de instantâneos.

Para obter as distribuições com suporte, consulte a [página de instalação encaixada](https://snapcraft.io/docs/installing-snapd).

Gerenciador de Armazenamento requer o uso de um Gerenciador de senhas. Talvez você precise se conectar manualmente a um Gerenciador de senhas. Você pode se conectar Gerenciador de Armazenamento ao Gerenciador de senhas do sistema executando o seguinte comando:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Gerenciador de Armazenamento também está disponível como um download *. tar. gz* . Você precisa instalar dependências manualmente. As seguintes distribuições da instalação do Linux Support *. tar. gz* :

* Ubuntu 20, 4 x64
* Ubuntu 18, 4 x64
* Ubuntu 16, 4 x64

A instalação do *. tar. gz* pode funcionar em outras distribuições, mas somente essas listadas são oficialmente suportadas.

Para obter mais ajuda para instalar o Gerenciador de Armazenamento no Linux, consulte [dependências do Linux](./storage/common/storage-explorer-troubleshooting.md#linux-dependencies) no guia de solução de problemas do Gerenciador de armazenamento do Azure.

---

## <a name="download-and-install"></a>Baixar e instalar

Para baixar e instalar o Gerenciador de Armazenamento, consulte [Gerenciador de armazenamento do Azure](https://www.storageexplorer.com).

## <a name="connect-to-a-storage-account-or-service"></a>Conectar-se a uma conta de armazenamento ou serviço

O Gerenciador de Armazenamento fornece várias maneiras de se conectar às contas de armazenamento. Em geral, você pode:

* [Entre no Azure para acessar suas assinaturas e seus recursos](#sign-in-to-azure)
* [Anexar um recurso de armazenamento ou CosmosDB específico](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>Entrar no Azure

> [!NOTE]
> Para acessar totalmente os recursos depois de entrar, Gerenciador de Armazenamento requer as permissões de gerenciamento (Azure Resource Manager) e de camada de dados. Isso significa que você precisa de permissões Azure Active Directory (Azure AD), que lhe dão acesso à sua conta de armazenamento, aos contêineres na conta e aos dados nos contêineres. Se você tiver permissões somente na camada de dados, considere [Adicionar um recurso por meio do Azure ad](#add-a-resource-via-azure-ad). Para obter mais informações sobre as permissões específicas Gerenciador de Armazenamento requer, consulte o [Guia de solução de problemas do Gerenciador de armazenamento do Azure](./storage/common/storage-explorer-troubleshooting.md#azure-rbac-permissions-issues).

1. Em Gerenciador de armazenamento, selecione **Exibir**  >  **Gerenciamento de conta** ou selecione o botão **gerenciar contas** .

    ![Gerenciar Contas][1]

1. O **Gerenciamento de conta** agora exibe todas as contas do Azure às quais você se conectou. Para se conectar a outra conta, selecione **Adicionar uma conta**.

1. Em **conectar ao armazenamento do Azure**, selecione uma nuvem do Azure do **ambiente do Azure** para entrar em uma nuvem nacional ou um Azure Stack. Depois de escolher o ambiente, selecione **Avançar**.

    ![Opção para entrar][2]

    Gerenciador de Armazenamento abre uma página para você entrar. Para obter mais informações, consulte [conectar o Gerenciador de armazenamento a uma assinatura Azure Stack ou conta de armazenamento](/azure-stack/user/azure-stack-storage-connect-se).

1. Depois de entrar com êxito com uma conta do Azure, a conta e as assinaturas do Azure associadas a essa conta aparecerão em **Gerenciamento de conta**. Selecione **todas as assinaturas** para alternar sua seleção entre todas ou nenhuma das assinaturas do Azure listadas. Selecione as assinaturas do Azure com as quais você deseja trabalhar e selecione **Aplicar**.

    ![Selecionar assinaturas do Azure][3]

    O **Explorer** exibe as contas de armazenamento associadas às assinaturas do Azure selecionadas.

    ![Assinaturas do Azure selecionadas][4]

### <a name="attach-a-specific-resource"></a>Anexar um recurso específico

Há várias maneiras de anexar a um recurso no Gerenciador de Armazenamento:

* [Adicione um recurso por meio do Azure ad](#add-a-resource-via-azure-ad). Se você tiver permissões somente na camada de dados, use esta opção para adicionar um contêiner de BLOBs ou um contêiner de armazenamento de BLOBs Azure Data Lake Storage Gen2.
* [Use uma cadeia de conexão](#use-a-connection-string). Use esta opção se você tiver uma cadeia de conexão para uma conta de armazenamento. Gerenciador de Armazenamento dá suporte às cadeias de conexão de assinatura de chave e de [acesso compartilhado](./storage/common/storage-sas-overview.md) .
* [Use um URI de assinatura de acesso compartilhado](#use-a-shared-access-signature-uri). Se você tiver um [URI de assinatura de acesso compartilhado](./storage/common/storage-sas-overview.md) para um contêiner de BLOB, compartilhamento de arquivos, fila ou tabela, use-o para anexar ao recurso. Para obter um URI de assinatura de acesso compartilhado, você pode usar [Gerenciador de armazenamento](#generate-a-sas-in-storage-explorer) ou o [portal do Azure](https://portal.azure.com).
* [Use um nome e uma chave](#use-a-name-and-key). Se você souber uma das chaves de conta para sua conta de armazenamento, poderá usar essa opção para se conectar rapidamente. Localize suas chaves na página da conta de armazenamento selecionando **configurações**  >  **chaves de acesso** no [portal do Azure](https://portal.azure.com).
* [Anexe a um emulador local](#attach-to-a-local-emulator). Se você estiver usando um dos emuladores de armazenamento do Azure disponíveis, use esta opção para se conectar facilmente ao seu emulador.
* [Conecte-se a uma conta de Azure Cosmos DB usando uma cadeia de conexão](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string). Use esta opção se você tiver uma cadeia de conexão para uma instância do CosmosDB.
* [Conecte-se a Azure data Lake Store por URI](#connect-to-azure-data-lake-store-by-uri). Use esta opção se você tiver um URI para Azure Data Lake Store.

#### <a name="add-a-resource-via-azure-ad"></a>Adicionar um recurso por meio do Azure AD

1. Selecione o símbolo de **conexão** para abrir **conectar ao armazenamento do Azure**.

    ![Opção Conectar ao Armazenamento do Azure][9]

1. Se você ainda não tiver feito isso, use a opção **Adicionar uma conta do Azure** para entrar na conta do Azure que tem acesso ao recurso. Depois de entrar, retorne para **se conectar ao armazenamento do Azure**.

1. Selecione **Adicionar um recurso via Azure Active Directory (Azure AD)** e, em seguida, selecione **Avançar**.

1. Selecione uma conta do Azure e um locatário. Esses valores devem ter acesso ao recurso de armazenamento ao qual você deseja anexar. Selecione **Avançar**.

1. Escolha o tipo de recurso que você deseja anexar. Insira as informações necessárias para se conectar. 

   As informações inseridas nesta página dependem do tipo de recurso que você está adicionando. Certifique-se de escolher o tipo correto de recurso. Depois de inserir as informações necessárias, selecione **Avançar**.

1. Examine o **Resumo da conexão** para verificar se todas as informações estão corretas. Se for, selecione **conectar**. Caso contrário, selecione **voltar** para retornar às páginas anteriores para corrigir todas as informações incorretas.

Depois que a conexão for adicionada com êxito, a árvore de recursos vai para o nó que representa a conexão. O recurso aparece em **local &**  >  **Storage Accounts**  >  contêineres de blob de contas de armazenamento anexadas **(contêineres anexados)**  >  **Blob Containers**. Se Gerenciador de Armazenamento não foi possível adicionar a conexão ou se você não puder acessar seus dados depois de adicionar a conexão com êxito, consulte o [Guia de solução de problemas Gerenciador de armazenamento do Azure](./storage/common/storage-explorer-troubleshooting.md).

#### <a name="use-a-connection-string"></a>Usar uma cadeia de conexão

1. Selecione o símbolo de **conexão** para abrir **conectar ao armazenamento do Azure**.

    ![Opção Conectar ao Armazenamento do Azure][9]

1. Selecione **usar uma cadeia de conexão** e, em seguida, selecione **Avançar**.

1. Escolha um nome de exibição para a conexão e insira sua cadeia de conexão. Em seguida, selecione **Avançar**.

1. Examine o **Resumo da conexão** para verificar se todas as informações estão corretas. Se for, selecione **conectar**. Caso contrário, selecione **voltar** para retornar às páginas anteriores para corrigir todas as informações incorretas.

Depois que a conexão for adicionada com êxito, a árvore de recursos vai para o nó que representa a conexão. O recurso aparece no **local &**  >  **contas de armazenamento** anexadas. Se Gerenciador de Armazenamento não foi possível adicionar a conexão ou se você não puder acessar seus dados depois de adicionar a conexão com êxito, consulte o [Guia de solução de problemas Gerenciador de armazenamento do Azure](./storage/common/storage-explorer-troubleshooting.md).

#### <a name="use-a-shared-access-signature-uri"></a>Usar um URI de assinatura de acesso compartilhado

1. Selecione o símbolo de **conexão** para abrir **conectar ao armazenamento do Azure**.

    ![Opção Conectar ao Armazenamento do Azure][9]

1. Selecione **usar um URI de SAS (assinatura de acesso compartilhado)** e, em seguida, selecione **Avançar**.

1. Escolha um nome de exibição para a conexão e insira seu URI de assinatura de acesso compartilhado. O ponto de extremidade de serviço para o tipo de recurso que você está anexando deve fazer preenchimento automático. Se você estiver usando um ponto de extremidade personalizado, é possível que ele não possa. Selecione **Avançar**.

1. Examine o **Resumo da conexão** para verificar se todas as informações estão corretas. Se for, selecione **conectar**. Caso contrário, selecione **voltar** para retornar às páginas anteriores para corrigir todas as informações incorretas.

Depois que a conexão for adicionada com êxito, a árvore de recursos vai para o nó que representa a conexão. O recurso aparece em **local &**  >  **contas de armazenamento** anexadas  >  **(contêineres anexados)**  >  *o nó de serviço para o tipo de contêiner que você anexou*. Se Gerenciador de Armazenamento não foi possível adicionar a conexão, consulte o [Guia de solução de problemas Gerenciador de armazenamento do Azure](./storage/common/storage-explorer-troubleshooting.md). Consulte o guia de solução de problemas se você não puder acessar seus dados depois de adicionar a conexão com êxito.

#### <a name="use-a-name-and-key"></a>Usar um nome e uma chave

1. Selecione o símbolo de **conexão** para abrir **conectar ao armazenamento do Azure**.

    ![Opção Conectar ao Armazenamento do Azure][9]

1. Selecione **Usar um nome e uma chave da conta de armazenamento** e selecione **Próximo**.

1. Escolha um nome de exibição para a conexão.

1. Insira o nome da conta de armazenamento e qualquer uma de suas chaves de acesso.

1. Escolha o **domínio de armazenamento** a ser usado e, em seguida, selecione **Avançar**.

1. Examine o **Resumo da conexão** para verificar se todas as informações estão corretas. Se for, selecione **conectar**. Caso contrário, selecione **voltar** para retornar às páginas anteriores para corrigir todas as informações incorretas.

Depois que a conexão for adicionada com êxito, a árvore de recursos vai para o nó que representa a conexão. O recurso aparece no **local &**  >  **contas de armazenamento** anexadas. Se Gerenciador de Armazenamento não foi possível adicionar a conexão ou se você não puder acessar seus dados depois de adicionar a conexão com êxito, consulte o [Guia de solução de problemas Gerenciador de armazenamento do Azure](./storage/common/storage-explorer-troubleshooting.md).

#### <a name="attach-to-a-local-emulator"></a>Anexar a um emulador local

O Gerenciador de Armazenamento atualmente dá suporte a dois emuladores de armazenamento oficiais:

* [Emulador de armazenamento do Azure](storage/common/storage-use-emulator.md) (somente Windows)
* [Azurite](https://github.com/azure/azurite) (Windows, MacOS ou Linux)

Se o emulador estiver escutando nas portas padrão, você poderá usar o nó **emulador-portas padrão** para acessar seu emulador. Procure **portas padrão do emulador** em contas de armazenamento **& conectadas locais**  >  **Storage Accounts**.

Se você quiser usar um nome diferente para a conexão ou se o emulador não estiver em execução nas portas padrão, siga estas etapas:

1. Inicie o emulador. Insira o comando `AzureStorageEmulator.exe status` para exibir as portas para cada tipo de serviço.

   > [!IMPORTANT]
   > Gerenciador de Armazenamento não inicia automaticamente seu emulador. Você deve iniciá-lo manualmente.

1. Selecione o símbolo de **conexão** para abrir **conectar ao armazenamento do Azure**.

    ![Opção Conectar ao Armazenamento do Azure][9]

1. Selecione **anexar a um emulador local** e, em seguida, selecione **Avançar**.

1. Escolha um nome de exibição para a conexão e insira as portas em que o emulador está escutando para cada tipo de serviço. **Anexar a um emulador local** sugere os valores de porta padrão para a maioria dos emuladores. A **porta de arquivos** está em branco, pois nenhum dos emuladores oficiais dão suporte atualmente ao serviço de arquivos. Se o emulador que você está usando oferece suporte a arquivos, você pode inserir a porta a ser usada. Em seguida, selecione **Avançar**.

1. Examine o **Resumo da conexão** e verifique se todas as informações estão corretas. Se for, selecione **conectar**. Caso contrário, selecione **voltar** para retornar às páginas anteriores para corrigir todas as informações incorretas.

Depois que a conexão for adicionada com êxito, a árvore de recursos vai para o nó que representa a conexão. O nó deve aparecer no **local &**  >  **contas de armazenamento** anexadas. Se Gerenciador de Armazenamento não foi possível adicionar a conexão ou se você não puder acessar seus dados depois de adicionar a conexão com êxito, consulte o [Guia de solução de problemas Gerenciador de armazenamento do Azure](./storage/common/storage-explorer-troubleshooting.md).

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Conectar-se ao Azure Cosmos DB usando uma cadeia de conexão

Em vez de gerenciar contas de Azure Cosmos DB por meio de uma assinatura do Azure, você pode se conectar ao Azure Cosmos DB usando uma cadeia de conexão. Para se conectar, siga essas etapas:

1. Em **Gerenciador**, expanda **local & anexado**, clique com o botão direito do mouse em **contas de Cosmos DB** e selecione **conectar-se a Azure Cosmos DB**.

    ![Conectar-se ao Azure Cosmos DB por uma cadeia de conexão][21]

1. Selecione a API Azure Cosmos DB, insira os dados da **cadeia de conexão** e, em seguida, selecione **OK** para conectar a conta de Azure Cosmos DB. Para obter informações sobre como recuperar a cadeia de conexão, consulte [gerenciar uma conta do Azure Cosmos](./cosmos-db/how-to-manage-database-account.md).

    ![Cadeia de conexão][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Conectar-se ao Azure Data Lake Store por URI

Você pode acessar um recurso que não está em sua assinatura. Você precisa de alguém que tenha acesso a esse recurso para fornecer o URI do recurso. Depois de entrar, conecte-se ao Data Lake Store usando o URI. Para se conectar, siga essas etapas:

1. Em **Gerenciador**, expanda **local & anexado**.

1. Clique com o botão direito do mouse em **Data Lake Storage Gen1** e selecione **conectar-se a data Lake Storage Gen1**.

    ![Conectar ao menu de contexto de Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-connect-data-lake-storage.png)

1. Insira o URI e, em seguida, selecione **OK**. Sua Data Lake Store aparece em **Data Lake Storage**.

    ![Conectar-se a Data Lake Store resultado](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-attach-data-lake-finished.png)

Este exemplo usa Data Lake Storage Gen1. Azure Data Lake Storage Gen2 agora está disponível. Para obter mais informações, consulte [o que é Azure data Lake Storage Gen1](./data-lake-store/data-lake-store-overview.md).

## <a name="generate-a-shared-access-signature-in-storage-explorer"></a>Gerar uma assinatura de acesso compartilhado no Gerenciador de Armazenamento<a name="generate-a-sas-in-storage-explorer"></a>

### <a name="account-level-shared-access-signature"></a>Assinatura de acesso compartilhado no nível da conta

1. Clique com o botão direito do mouse na conta de armazenamento que você deseja compartilhar e selecione **obter assinatura de acesso compartilhado**.

    ![Opção de menu de contexto obter assinatura de acesso compartilhado][14]

1. Em **assinatura de acesso compartilhado**, especifique o intervalo de tempo e as permissões desejadas para a conta e, em seguida, selecione **criar**.

    ![Obter uma assinatura de acesso compartilhado][15]

1. Copie a **cadeia de conexão** ou a cadeia de **caracteres de consulta** bruta para a área de transferência.

### <a name="service-level-shared-access-signature"></a>Assinatura de acesso compartilhado de nível de serviço

Você pode obter uma assinatura de acesso compartilhado no nível de serviço. Para obter mais informações, consulte [obter a SAS para um contêiner de blob](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container).

## <a name="search-for-storage-accounts"></a>Pesquisar nas contas de armazenamento

Para localizar um recurso de armazenamento, você pode pesquisar no painel do **Explorer** .

À medida que você insere o texto na caixa de pesquisa, Gerenciador de Armazenamento exibe todos os recursos que correspondem ao valor de pesquisa que você inseriu até esse ponto. Este exemplo mostra uma pesquisa de **pontos de extremidade**:

![Pesquisa na conta de armazenamento][23]

> [!NOTE]
> Para acelerar sua pesquisa, use o **Gerenciamento de conta** para desmarcar todas as assinaturas que não contenham o item que você está procurando. Você também pode clicar com o botão direito do mouse em um nó e selecionar **Pesquisar aqui** para iniciar a pesquisa de um nó específico.
>
>

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar recursos de armazenamento de BLOBs do Azure com Gerenciador de Armazenamento](vs-azure-tools-storage-explorer-blobs.md)
* [Trabalhar com os dados usando o Gerenciador de Armazenamento do Azure](./cosmos-db/storage-explorer.md)
* [Gerenciar os recursos do Azure Data Lake Store com o Gerenciador de Armazenamento](./data-lake-store/data-lake-store-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-azure-environment.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/account-panel-subscriptions-apply.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/PortalAccessKeys.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccessKeys.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-AddWithKeySelected.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-NameAndKeyPage.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount-Detach.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-shared-access-signature-for-storage-explorer.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/create-shared-access-signature-for-storage-explorer.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-WithConnStringOrSASSelected.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-1.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithSASAccount.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-2.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ServiceAttachedWithSAS.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-cosmos-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string-for-cosmos-db.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-search-for-resource.png