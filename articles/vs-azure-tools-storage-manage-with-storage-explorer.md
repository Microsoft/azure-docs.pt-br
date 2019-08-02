---
title: Introdução ao Gerenciador de Armazenamento | Microsoft Docs
description: Gerenciar recursos do armazenamento do Azure com o Gerenciador de Armazenamento
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 04/22/2019
ms.author: cawa
ms.openlocfilehash: f7dd6d3d30f34ba2c69b40111bb28d484ce572e7
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508744"
---
# <a name="get-started-with-storage-explorer"></a>Introdução ao Gerenciador de Armazenamento

## <a name="overview"></a>Visão geral

O Gerenciador de Armazenamento do Azure é um aplicativo autônomo que permite que você trabalhe facilmente com dados do Armazenamento do Azure no Windows, no macOS e no Linux. Neste artigo, você aprende diversas maneiras de se conectar e gerenciar suas contas de armazenamento do Azure.

![Gerenciador do Armazenamento do Microsoft Azure][0]

## <a name="prerequisites"></a>Pré-requisitos

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Gerenciador de Armazenamento do Azure tem suporte nas seguintes versões do Windows:

* Windows 10 (recomendado)
* Windows 8
* Windows 7

Para todas as versões do Windows, .NET Framework 4.6.2 ou posterior é necessário.

[Baixe e instale o Gerenciador de Armazenamento](https://www.storageexplorer.com)

# <a name="macostabmacos"></a>[macOS](#tab/macos)

Gerenciador de Armazenamento do Azure tem suporte nas seguintes versões do macOS:

* macOS 10.12 “Sierra” e versões posteriores

[Baixe e instale o Gerenciador de Armazenamento](https://www.storageexplorer.com)

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Gerenciador de Armazenamento do Azure tem suporte nas seguintes distribuições do Linux:

* Ubuntu 18.04 x64
* Ubuntu 16.04 x64
* Ubuntu 14.04 x64

O Gerenciador de Armazenamento do Azure pode funcionar em outras distribuições, mas somente aquelas listadas acima são suportadas oficialmente.

Para obter mais ajuda para instalar o Gerenciador de armazenamento no Linux, consulte o [guia de solução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies).

As [Notas de Versão](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409) do Gerenciador de Armazenamento do Azure contém etapas específicas para algumas distribuições.

[Baixe e instale o Gerenciador de Armazenamento](https://www.storageexplorer.com)

---

## <a name="connect-to-a-storage-account-or-service"></a>Conectar-se a uma conta de armazenamento ou serviço

O Gerenciador de Armazenamento fornece várias maneiras de se conectar às contas de armazenamento. Em geral você pode:

* [Entrar no Azure para acessar suas assinaturas e seus recursos](#sign-in-to-azure)
* [Anexar a um recurso específico de armazenamento ou o CosmosDB](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>Entrar no Azure

> [!NOTE]
> Para acessar os recursos totalmente depois de entrar, o Gerenciador de armazenamento requer gerenciamento (ARM) e permissões de camada de dados. Isso significa que você precisa de permissões do AD do Azure que dão acesso à sua conta de armazenamento, os contêineres na conta e os dados em contêineres. Se você tiver apenas permissões da camada de dados, considere o uso de [anexar com o Azure AD](#add-a-resource-via-azure-ad). Para obter mais informações sobre as permissões exatas que exige que o Gerenciador de armazenamento, consulte o [guia de solução](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#role-based-access-control-permission-issues).
>
>

1. No Gerenciador de Armazenamento, selecione **Gerenciar Contas** para acessar o **Painel de Gerenciamento de Conta**.

    ![Gerenciar Contas][1]

2. Agora o painel à esquerda exibe todas as contas do Azure em que você se conectou. Para conectar-se a outra conta, selecione **Adicionar uma conta**

3. Se você quiser entrar em uma nuvem nacional ou no Azure Stack, clique na lista suspensa **Ambiente do Azure** para selecionar qual nuvem do Azure você deseja usar. Depois de escolher o ambiente, clique no botão **Entrar...** . Se você estiver entrando no Azure Stack, consulte [Conectar o Gerenciador de Armazenamento a uma assinatura do Azure Stack](/azure-stack/user/azure-stack-storage-connect-se) para obter mais informações.

    ![Opção de conexão][2]

4. Depois de entrar com êxito usando uma conta do Azure, a conta e as assinaturas do Azure associadas com essa conta serão adicionadas ao painel à esquerda. Selecione as assinaturas do Azure com as quais você deseja trabalhar e, em seguida, selecione **Aplicar** (selecionar **Todas as assinaturas:** alterna entre a seleção de todas ou de nenhuma das assinaturas do Azure listadas).

    ![Selecionar assinaturas do Azure][3]

    O painel esquerdo exibe as contas de armazenamento associadas às assinaturas do Azure selecionadas.

    ![Assinaturas do Azure selecionadas][4]

### <a name="attach-a-specific-resource"></a>Anexar a um recurso específico
    
Há uma variedade de opções para anexação de um recurso de Gerenciador de armazenamento. Você pode:

* [Adicionar um recurso por meio do Azure AD](#add-a-resource-via-azure-ad): Se você tiver apenas permissões da camada de dados, você pode usar essa opção para adicionar um contêiner de Blob ou um contêiner de Blob do ADLS Gen2.
* [Use uma cadeia de caracteres de conexão](#use-a-connection-string): Se você tiver uma cadeia de caracteres de conexão para uma conta de armazenamento. O Gerenciador de armazenamento dá suporte a ambas as chave e [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) cadeias de caracteres de conexão.
* [Use um URI de SAS](#use-a-sas-uri): Se você tiver um [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) URI para um contêiner de Blob, compartilhamento de arquivo, fila ou tabela. Para obter um URI de SAS, você pode usar [Gerenciador de armazenamento](#generate-a-sas-in-storage-explorer) ou o [portal do Azure](https://portal.azure.com).
* [Use um nome e chave](#use-a-name-and-key): Se você souber qualquer uma das chaves de conta para sua conta de armazenamento, você pode usar essa opção para conectar-se rapidamente. As chaves para sua conta de armazenamento estão localizadas na conta de armazenamento **chaves de acesso** folha com as [portal do Azure](https://portal.azure.com).
* [Anexar a um emulador local](#attach-to-a-local-emulator): Se você estiver usando um dos emuladores do armazenamento do Azure disponíveis, use esta opção para conectar-se facilmente ao emulador.
* [Conectar-se a uma conta do Azure Cosmos DB usando uma cadeia de conexão](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string): Se você tiver uma cadeia de caracteres de conexão para uma instância do CosmosDB.
* [Conectar-se ao Azure Data Lake Store por URI](#connect-to-azure-data-lake-store-by-uri): Se você tiver um URI para uma Store do Azure Data Lake.

#### <a name="add-a-resource-via-azure-ad"></a>Adicionar um recurso por meio do Azure AD

1. Abra o **caixa de diálogo Conectar** clicando na **botão conectar** no lado esquerdo, a barra de ferramentas vertical.

    ![Opção Conectar ao Armazenamento do Azure][9]

2. Se você ainda não fez isso, use o **adicionar uma conta do Azure** opção para entrar na conta do Azure que tem acesso ao recurso. Depois de entrar em retorno de **caixa de diálogo Conectar**.

3. Selecione o **adicionar um recurso por meio do Azure Active Directory (Azure AD)** opção e clique em **próxima**.

4. Selecione a conta do Azure que tem acesso ao recurso de armazenamento que você deseja anexar e a assinatura que o recurso está em e, em seguida, clique em **próxima**.

5. Escolha o tipo de recurso que você deseja anexar e, em seguida, insira as informações necessárias para se conectar. As entradas nesta página serão alterado dependendo do tipo de recurso que você está adicionando. Certifique-se de escolher o tipo correto de recurso. Depois que você tiver preenchido o informações necessárias, clique **próxima**.

6. Examine o resumo de conexão e verifique se todas as informações estão corretas. Se todas as informações de estiver correto, em seguida, clique em **Connect**, caso contrário, retornar às páginas anteriores com o **volta** botão para corrigir quaisquer informações erradas.

Depois que a conexão for adicionado com êxito, a árvore de recursos será automaticamente navegar até o nó que representa a conexão. Se por algum motivo, ele não abrir, procure em **Local e anexados** → **contas de armazenamento** → **(contêineres anexado)** → **contêineres de Blob** . Se o Gerenciador de armazenamento não foi possível adicionar sua conexão, ou se você não pode acessar seus dados depois de adicionar com êxito a conexão, consulte a [guia de solução](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) para obter ajuda.

#### <a name="use-a-connection-string"></a>Use uma cadeia de caracteres de conexão

1. Abra o **caixa de diálogo Conectar** clicando na **botão conectar** no lado esquerdo, a barra de ferramentas vertical.

    ![Opção Conectar ao Armazenamento do Azure][9]

2. Selecione o **usar uma cadeia de caracteres de conexão** opção e clique em **próxima**.

3. Escolha um nome de exibição para sua conexão e inserir em sua cadeia de conexão. Em seguida, clique em **Próximo**.

4. Examine o resumo de conexão e verifique se todas as informações estão corretas. Se todas as informações de estiver correto, em seguida, clique em **Connect**, caso contrário, retornar às páginas anteriores com o **volta** botão para corrigir quaisquer informações erradas.

Depois que a conexão for adicionado com êxito, a árvore de recursos será automaticamente navegar até o nó que representa a conexão. Se por algum motivo, ele não abrir, procure em **Local e anexados** → **contas de armazenamento**. Se o Gerenciador de armazenamento não foi possível adicionar sua conexão, ou se você não pode acessar seus dados depois de adicionar com êxito a conexão, consulte a [guia de solução](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) para obter ajuda.

#### <a name="use-a-sas-uri"></a>Use um URI de SAS

1. Abra o **caixa de diálogo Conectar** clicando na **botão conectar** no lado esquerdo, a barra de ferramentas vertical.

    ![Opção Conectar ao Armazenamento do Azure][9]

2. Selecione o **usar uma assinatura de acesso compartilhado (SAS) URI** opção e clique em **próxima**.

3. Escolha um nome de exibição para sua conexão e insira o URI de SAS. Preenchimento automático deve ser o ponto de extremidade de serviço para o tipo de recurso que você está anexando. Se você estiver usando um ponto de extremidade personalizado, em seguida, é possível não pode. Clique em **Avançar**.

4. Examine o resumo de conexão e verifique se todas as informações estão corretas. Se todas as informações de estiver correto, em seguida, clique em **Connect**, caso contrário, retornar às páginas anteriores com o **volta** botão para corrigir quaisquer informações erradas.

Depois que a conexão for adicionado com êxito, a árvore de recursos será automaticamente navegar até o nó que representa a conexão. Se por algum motivo, ele não abrir, procure em **Local e anexados** → **contas de armazenamento** → **(contêineres anexado)** → **o nó do serviço para o tipo de contêiner que você anexou**. Se o Gerenciador de armazenamento não foi possível adicionar sua conexão, ou se você não pode acessar seus dados depois de adicionar com êxito a conexão, consulte a [guia de solução](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) para obter ajuda.

#### <a name="use-a-name-and-key"></a>Use um nome e chave

1. Abra o **caixa de diálogo Conectar** clicando na **botão conectar** no lado esquerdo, a barra de ferramentas vertical.

    ![Opção Conectar ao Armazenamento do Azure][9]

2. Selecione o **usar um nome de conta de armazenamento e a chave** opção e clique em **próxima**.

3. Escolha um nome de exibição para sua conexão.

4. Insira o nome da sua conta de armazenamento e qualquer uma das suas chaves de acesso.

5. Escolha o **domínio de armazenamento** e, em seguida, clique em **próxima**.

4. Examine o resumo de conexão e verifique se todas as informações estão corretas. Se todas as informações de estiver correto, em seguida, clique em **Connect**, caso contrário, retornar às páginas anteriores com o **volta** botão para corrigir quaisquer informações erradas.

Depois que a conexão for adicionado com êxito, a árvore de recursos será automaticamente navegar até o nó que representa a conexão. Se por algum motivo, ele não abrir, procure em **Local e anexados** → **contas de armazenamento**. Se o Gerenciador de armazenamento não foi possível adicionar sua conexão, ou se você não pode acessar seus dados depois de adicionar com êxito a conexão, consulte a [guia de solução](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) para obter ajuda.

#### <a name="attach-to-a-local-emulator"></a>Anexar a um emulador local

O Gerenciador de armazenamento dá suporte a emuladores em todas as plataformas. Os dois atualmente oficiais emuladores disponíveis são:
* [Emulador de armazenamento do Azure](storage/common/storage-use-emulator.md) (somente Windows)
* [Azurite](https://github.com/azure/azurite) (Windows, macOS ou Linux)

Se o emulador estiver em execução nas portas padrão que você pode usar o **emulador - portas padrão** nó, que sempre pode ser encontrado em **Local e anexados** → **contas de armazenamento** , para acessar rapidamente o emulador. Se você deseja usar um nome diferente para sua conexão ou se o emulador não está em execução nas portas padrão, em seguida, siga as etapas a seguir.

1. Inicie o emulador. Quando você fizer, anote quais portas o emulador está escutando para cada tipo de serviço. Você precisará conhecer essas informações posteriormente.

   > [!IMPORTANT]
   > O Gerenciador de Armazenamento do Microsoft Azure não inicia automaticamente o emulador. Você deve iniciá-lo.

2. Abra o **caixa de diálogo Conectar** clicando na **botão conectar** no lado esquerdo, a barra de ferramentas vertical.

    ![Opção Conectar ao Armazenamento do Azure][9]

3. Selecione o **anexar a um emulador local** opção e clique em **próxima**.

4. Escolha um nome de exibição para sua conexão e insira nas portas que o emulador está escutando para cada tipo de serviço. Por padrão, as caixas de texto conterá os valores de porta padrão para a maioria dos emuladores. O **arquivos de porta** também for deixado em branco por padrão como nenhum dos emuladores do oficiais de suporte no momento, o serviço de arquivos. Se você estiver usando o emulador oferece suporte a ele no entanto, você pode inserir na porta que está sendo usada. Clique em **Avançar**.

5. Examine o resumo de conexão e verifique se todas as informações estão corretas. Se todas as informações de estiver correto, em seguida, clique em **Connect**, caso contrário, retornar às páginas anteriores com o **volta** botão para corrigir quaisquer informações erradas.

Depois que a conexão for adicionado com êxito, a árvore de recursos será automaticamente navegar até o nó que representa a conexão. Se por algum motivo, ele não abrir, procure em **Local e anexados** → **contas de armazenamento**. Se o Gerenciador de armazenamento não foi possível adicionar sua conexão, ou se você não pode acessar seus dados depois de adicionar com êxito a conexão, consulte a [guia de solução](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) para obter ajuda.

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Conectar-se ao Azure Cosmos DB usando uma cadeia de conexão

Além de gerenciar contas do Azure Cosmos DB por meio de uma assinatura do Azure, um modo alternativo de se conectar a um Azure Cosmos DB é usar uma cadeia de conexão. Siga as etapas abaixo para se conectar usando uma cadeia de conexão.

1. Encontre a opção **Local e Anexo** na árvore à esquerda, clique com o botão direito do mouse em **Contas do Azure Cosmos DB** e escolha **Conectar-se ao Azure Cosmos DB...**

    ![conectar-se ao Azure Cosmos DB por uma cadeia de conexão][21]

2. Escolha a API do Azure Cosmos DB, cole a **Cadeia de Conexão** e, em seguida, clique em **OK** para se conectar à conta do Azure Cosmos DB. Para obter informações sobre como recuperar a cadeia de conexão, consulte [Obtenha a cadeia de conexão](https://docs.microsoft.com/azure/cosmos-db/manage-account).

    ![connection-string][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Conectar-se ao Azure Data Lake Store por URI

Se você deseja obter acesso aos recursos que não existem na sua assinatura. Mas outras pessoas concedem a você para obter o URI para os recursos. Nesse caso, você pode se conectar ao Data Lake Store usando o URI depois de se conectar. Consulte as etapas a seguir.

1. Abra o Explorer do Armazenamento.
2. No painel esquerdo, expanda **Local e Conectado**.
3. Clique com o botão direito do mouse em **Data Lake Store**e, no menu de contexto, selecione **Conectar-se ao Data Lake Store...** .

    ![Menu de contexto Conectar-se ao Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Digite o URI e a ferramenta navegará até o local da URL inserida.

    ![Diálogo Conectar-se ao Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![Resultado Conectar-se ao Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)


## <a name="generate-a-sas-in-storage-explorer"></a>Gerar uma SAS no Gerenciador de armazenamento

### <a name="account-level-sas"></a>SAS de nível de conta

1. Com a conta de armazenamento que você deseja compartilhar e, em seguida, selecione o botão direito **obter assinatura de acesso compartilhado...** .

    ![Opção do menu de contexto Obter SAS][14]

2. Na caixa de diálogo **Gerar Assinatura de Acesso Compartilhado**, especifique o período e as permissões que você deseja para a conta e, em seguida, clique no botão **Criar**.

    ![Caixa de diálogo Obter SAS][15]

3. Agora você pode copiar o **cadeia de caracteres de Conexão** ou o raw **cadeia de caracteres de consulta** na área de transferência.

### <a name="service-level-sas"></a>SAS de nível de serviço

[Como obter uma SAS para um contêiner de blob no Gerenciador de armazenamento](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

## <a name="search-for-storage-accounts"></a>Pesquisar nas contas de armazenamento

Se você precisa encontrar um recurso de armazenamento e não sabe onde ele está, use a caixa de pesquisa na parte superior do painel à esquerda para pesquisar o recurso.

À medida que você digita na caixa de pesquisa, o painel esquerdo exibe todos os recursos que correspondem ao valor de pesquisa inserido até esse ponto. Por exemplo, veja uma pesquisa por **pontos de extremidade** na captura de tela a seguir:

![Pesquisa na conta de armazenamento][23]

> [!NOTE]
> Use o **Painel de Gerenciamento de Conta** para cancelar a seleção das assinaturas que não contêm o item que você está procurando, a fim de melhorar o tempo de execução da pesquisa. Você também pode clicar com o botão direito do mouse em um nó e escolher **Pesquisar Aqui** para iniciar a pesquisa em um nó específico.
>
>

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar recursos de Armazenamento de Blobs com o Gerenciador de Armazenamento](vs-azure-tools-storage-explorer-blobs.md)
* [Gerenciar o Azure Cosmos DB no Gerenciador de Armazenamento do Azure (Versão Prévia)](./cosmos-db/storage-explorer.md)
* [Gerenciar os recursos do Azure Data Lake Store com o Gerenciador de Armazenamento](./data-lake-store/data-lake-store-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-SignInSelected.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccountPanel.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/PortalAccessKeys.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccessKeys.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-AddWithKeySelected.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-NameAndKeyPage.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount-Detach.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/GetSharedAccessSignature.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SharedAccessSignatureDialog.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-WithConnStringOrSASSelected.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-1.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithSASAccount.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-2.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ServiceAttachedWithSAS.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Search.png
