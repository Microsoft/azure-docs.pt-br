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
ms.openlocfilehash: 3a8fe3ded6608059cc6ad50901ffe6df5dcf1b08
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102441526"
---
# <a name="get-started-with-storage-explorer"></a>Introdução ao Gerenciador de Armazenamento

## <a name="overview"></a>Visão geral

O Gerenciador de Armazenamento do Microsoft Azure é um aplicativo autônomo que facilita o trabalho com dados do Armazenamento do Azure no Windows, macOS e Linux.

Neste artigo, você aprenderá várias maneiras de se conectar e gerenciar suas contas de armazenamento do Azure.

:::image type="content" alt-text="Gerenciador do Armazenamento do Microsoft Azure" source="./vs-storage-explorer-overview.png":::

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

Para obter as distribuições com suporte, consulte a [ `snapd` página instalação](https://snapcraft.io/docs/installing-snapd).

Gerenciador de Armazenamento requer o uso de um Gerenciador de senhas. Talvez você precise se conectar manualmente a um Gerenciador de senhas. Você pode se conectar Gerenciador de Armazenamento ao Gerenciador de senhas do sistema executando o seguinte comando:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Gerenciador de Armazenamento também está disponível como um download *. tar. gz* . Se você usar o *. tar. gz*, deverá instalar dependências manualmente. As seguintes distribuições da instalação do Linux Support *. tar. gz* :

* Ubuntu 20, 4 x64
* Ubuntu 18, 4 x64
* Ubuntu 16, 4 x64

A instalação do *. tar. gz* pode funcionar em outras distribuições, mas somente essas listadas são oficialmente suportadas.

Para obter mais ajuda para instalar o Gerenciador de Armazenamento no Linux, consulte [dependências do Linux](./storage/common/storage-explorer-troubleshooting.md#linux-dependencies) no guia de solução de problemas do Gerenciador de armazenamento do Azure.

---

## <a name="download-and-install"></a>Baixar e instalar

Para baixar e instalar o Gerenciador de Armazenamento, consulte [Gerenciador de armazenamento do Azure](https://www.storageexplorer.com).

## <a name="connect-to-a-storage-account-or-service"></a>Conectar-se a uma conta de armazenamento ou serviço

Gerenciador de Armazenamento fornece várias maneiras de se conectar aos recursos do Azure:

* [Entre no Azure para acessar suas assinaturas e seus recursos](#sign-in-to-azure)
* [Anexar a um recurso de armazenamento individual do Azure](#attach-to-an-individual-resource)
* [Anexar a um recurso CosmosDB](#connect-to-azure-cosmos-db)

### <a name="sign-in-to-azure"></a>Entrar no Azure

> [!NOTE]
> Para acessar totalmente os recursos depois de entrar, Gerenciador de Armazenamento requer as permissões de gerenciamento (Azure Resource Manager) e de camada de dados. Isso significa que você precisa de permissões de Azure Active Directory (Azure AD) para acessar sua conta de armazenamento, os contêineres na conta e os dados nos contêineres. Se você tiver permissões somente na camada de dados, considere a possibilidade de escolher a opção **entrar usando Azure Active Directory (Azure AD)** ao anexar a um recurso. Para obter mais informações sobre as permissões específicas Gerenciador de Armazenamento requer, consulte o [Guia de solução de problemas do Gerenciador de armazenamento do Azure](./storage/common/storage-explorer-troubleshooting.md#azure-rbac-permissions-issues).

1. Em Gerenciador de armazenamento, selecione **Exibir**  >  **Gerenciamento de conta** ou selecione o botão **gerenciar contas** .

    :::image type="content" alt-text="Gerenciar contas" source ="./vs-storage-explorer-manage-accounts.png":::

1. O **Gerenciamento de conta** agora exibe todas as contas do Azure às quais você está conectado. Para se conectar a outra conta, selecione **Adicionar uma conta...**.

1. A caixa de diálogo **conectar ao armazenamento do Azure** é aberta. No painel **selecionar recurso** , selecione **assinatura**.

    :::image type="content" alt-text="Caixa de diálogo conectar" source="./vs-storage-explorer-connect-dialog.png":::

1. No painel **selecionar ambiente do Azure** , selecione um ambiente do Azure para entrar. Você pode entrar no Azure global, em uma nuvem nacional ou em uma instância de Azure Stack. Em seguida, selecione **Avançar**.

    :::image type="content" alt-text="Opção para entrar" source="./vs-storage-explorer-connect-environment.png":::

    > [!TIP]
    > Para obter mais informações sobre Azure Stack, consulte [conectar Gerenciador de armazenamento a uma assinatura Azure Stack ou conta de armazenamento](/azure-stack/user/azure-stack-storage-connect-se).

1. Gerenciador de Armazenamento abrirá uma página da Web para que você entre.

1. Depois de entrar com êxito com uma conta do Azure, a conta e as assinaturas do Azure associadas a essa conta aparecerão em **Gerenciamento de conta**. Selecione as assinaturas do Azure com as quais você deseja trabalhar e selecione **Aplicar**.

    :::image type="content" alt-text="Selecionar assinaturas do Azure" source="./vs-storage-explorer-account-panel.png":::

1. O **Explorer** exibe as contas de armazenamento associadas às assinaturas do Azure selecionadas.

    :::image type="content" alt-text="Assinaturas do Azure selecionadas" source="./vs-storage-explorer-subscription-node.png":::

### <a name="attach-to-an-individual-resource"></a>Anexar a um recurso individual

Gerenciador de Armazenamento permite que você se conecte a recursos individuais, como um contêiner de Azure Data Lake Storage Gen2, usando vários métodos de autenticação. Alguns métodos de autenticação têm suporte apenas para determinados tipos de recursos.

| Tipo de recurso    | Azure AD | Nome da conta e chave | SAS (Assinatura de Acesso Compartilhado)  | Público (anônimo) |
|------------------|----------|----------------------|--------------------------------|--------------------|
| Contas de armazenamento | Sim      | Sim                  | Sim (cadeia de conexão ou URL) | Não                 |
| Contêineres de blobs  | Sim      | Não                   | Sim (URL)                      | Sim                |
| Contêineres de Gen2  | Sim      | Não                   | Sim (URL)                      | Sim                |
| Diretórios Gen2 | Sim      | Não                   | Sim (URL)                      | Sim                |
| Compartilhamentos de arquivos      | Não       | Não                   | Sim (URL)                      | Não                 |
| Filas           | Sim      | Não                   | Sim (URL)                      | Não                 |
| Tabelas           | Não       | Não                   | Sim (URL)                      | Não                 |
 
Gerenciador de Armazenamento também pode se conectar a um [emulador de armazenamento local](#local-storage-emulator) usando as portas configuradas do emulador.

Para se conectar a um recurso individual, selecione o botão **conectar** na barra de ferramentas do lado esquerdo. Em seguida, siga as instruções para o tipo de recurso ao qual você deseja se conectar.

:::image type="content" alt-text="Opção Conectar ao Armazenamento do Azure" source="./vs-storage-explorer-connect-button.png":::

Quando uma conexão com uma conta de armazenamento é adicionada com êxito, um novo nó de árvore aparecerá em contas de armazenamento **& anexadas local**  >  .

Para outros tipos de recursos, um novo nó é adicionado em contas de armazenamento **locais & anexadas**  >    >  **(contêineres anexados)**. O nó aparecerá sob um nó de grupo correspondente ao seu tipo. Por exemplo, uma nova conexão com um contêiner Azure Data Lake Storage Gen2 aparecerá em **contêineres de blob**.

Se Gerenciador de Armazenamento não foi possível adicionar a conexão ou se você não puder acessar seus dados depois de adicionar a conexão com êxito, consulte o [Guia de solução de problemas Gerenciador de armazenamento do Azure](./storage/common/storage-explorer-troubleshooting.md).

As seções a seguir descrevem os diferentes métodos de autenticação que você pode usar para se conectar a recursos individuais.

#### <a name="azure-ad"></a>Azure AD

Gerenciador de Armazenamento pode usar sua conta do Azure para se conectar aos seguintes tipos de recursos:
* Contêineres de blobs
* Contêineres de Azure Data Lake Storage Gen2
* Diretórios Azure Data Lake Storage Gen2
* Filas
 
O Azure AD é a opção preferida se você tiver acesso à camada de dados para seu recurso, mas sem acesso à camada de gerenciamento.

1. Entre em pelo menos uma conta do Azure usando as [etapas descritas acima](#sign-in-to-azure).
1. No painel **selecionar recurso** da caixa de diálogo **conectar ao armazenamento do Azure** , selecione **contêiner de blob**, contêiner de **ADLS Gen2** ou **fila**.
1. Selecione **entrar usando Azure Active Directory (Azure AD)** e selecione **Avançar**.
1. Selecione uma conta do Azure e um locatário. A conta e o locatário devem ter acesso ao recurso de armazenamento ao qual você deseja anexar. Selecione **Avançar**.
1. Insira um nome de exibição para a conexão e a URL do recurso. Selecione **Avançar**.
1. Examine as informações de conexão no painel de **Resumo** . Se as informações de conexão estiverem corretas, selecione **conectar**.

#### <a name="account-name-and-key"></a>Nome da conta e chave

Gerenciador de Armazenamento pode se conectar a uma conta de armazenamento usando o nome e a chave da conta de armazenamento.

Você pode encontrar suas chaves de conta no [portal do Azure](https://portal.azure.com). Abra a página conta de armazenamento e selecione **configurações**  >  **chaves de acesso**.

1. No painel **selecionar recurso** da caixa de diálogo **conectar ao armazenamento do Azure** , selecione **conta de armazenamento**.
1. Selecione **nome da conta e chave** e selecione **Avançar**.
1. Insira um nome de exibição para sua conexão, o nome da conta e uma das chaves de conta. Selecione o ambiente do Azure apropriado. Selecione **Avançar**.
1. Examine as informações de conexão no painel de **Resumo** . Se as informações de conexão estiverem corretas, selecione **conectar**.

#### <a name="shared-access-signature-sas-connection-string"></a>Cadeia de conexão de SAS (assinatura de acesso compartilhado)

Gerenciador de Armazenamento pode se conectar a uma conta de armazenamento usando uma cadeia de conexão com uma SAS (assinatura de acesso compartilhado). Uma cadeia de conexão SAS tem esta aparência:

```text
SharedAccessSignature=sv=2020-04-08&ss=btqf&srt=sco&st=2021-03-02T00%3A22%3A19Z&se=2020-03-03T00%3A22%3A19Z&sp=rl&sig=fFFpX%2F5tzqmmFFaL0wRffHlhfFFLn6zJuylT6yhOo%2FY%3F;
BlobEndpoint=https://contoso.blob.core.windows.net/;
FileEndpoint=https://contoso.file.core.windows.net/;
QueueEndpoint=https://contoso.queue.core.windows.net/;
TableEndpoint=https://contoso.table.core.windows.net/;
```

1. No painel **selecionar recurso** da caixa de diálogo **conectar ao armazenamento do Azure** , selecione **conta de armazenamento**.
1. Selecione **SAS (assinatura de acesso compartilhado)** e selecione **Avançar**.
1. Insira um nome de exibição para a conexão e a cadeia de conexão SAS para a conta de armazenamento. Selecione **Avançar**.
1. Examine as informações de conexão no painel de **Resumo** . Se as informações de conexão estiverem corretas, selecione **conectar**.

#### <a name="shared-access-signature-sas-url"></a>URL de assinatura de acesso compartilhado (SAS)

Gerenciador de Armazenamento pode se conectar aos seguintes tipos de recursos usando um URI de SAS:
* Contêiner de blob
* Azure Data Lake Storage Gen2 contêiner ou diretório
* Compartilhamento de arquivo
* Fila
* Tabela

Um URI de SAS tem esta aparência:

```text
https://contoso.blob.core.windows.net/container01?sv=2020-04-08&st=2021-03-02T00%3A30%3A33Z&se=2020-03-03T00%3A30%3A33Z&sr=c&sp=rl&sig=z9VFdWffrV6FXU51T8b8HVfipZPOpYOFLXuQw6wfkFY%3F
```

1. No painel **selecionar recurso** da caixa de diálogo **conectar ao armazenamento do Azure** , selecione o recurso ao qual você deseja se conectar.
1. Selecione **SAS (assinatura de acesso compartilhado)** e selecione **Avançar**.
1. Insira um nome de exibição para a conexão e o URI de SAS para o recurso. Selecione **Avançar**.
1. Examine as informações de conexão no painel de **Resumo** . Se as informações de conexão estiverem corretas, selecione **conectar**.

#### <a name="local-storage-emulator"></a>Emulador de armazenamento local

Gerenciador de Armazenamento pode se conectar a um emulador de armazenamento do Azure. Atualmente, há dois emuladores com suporte:

* [Emulador de armazenamento do Azure](storage/common/storage-use-emulator.md) (somente Windows)
* [Azurite](https://github.com/azure/azurite) (Windows, MacOS ou Linux)

Se o emulador estiver escutando nas portas padrão, você poderá usar o nó **local &** o  >  emulador de **contas de armazenamento** conectado  >  **-portas padrão** para acessar seu emulador.

Se você quiser usar um nome diferente para sua conexão, ou se o emulador não estiver em execução nas portas padrão:

1. Inicie o emulador.

   > [!IMPORTANT]
   > Gerenciador de Armazenamento não inicia automaticamente seu emulador. Você deve iniciá-lo manualmente.

1. No painel **selecionar recurso** da caixa de diálogo **conectar ao armazenamento do Azure** , selecione **emulador de armazenamento local**.
1. Insira um nome de exibição para a conexão e o número da porta para cada serviço emulado que você deseja usar. Se você não quiser usar o para um serviço, deixe a porta correspondente em branco. Selecione **Avançar**.
1. Examine as informações de conexão no painel de **Resumo** . Se as informações de conexão estiverem corretas, selecione **conectar**.

### <a name="connect-to-azure-cosmos-db"></a>Conectar-se ao Azure Cosmos DB

O Gerenciador de Armazenamento também dá suporte à conexão com Azure Cosmos DB recursos.

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

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar recursos de armazenamento de BLOBs do Azure com Gerenciador de Armazenamento](vs-azure-tools-storage-explorer-blobs.md)
* [Trabalhar com os dados usando o Gerenciador de Armazenamento do Azure](./cosmos-db/storage-explorer.md)
* [Gerenciar os recursos do Azure Data Lake Store com o Gerenciador de Armazenamento](./data-lake-store/data-lake-store-in-storage-explorer.md)

[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-shared-access-signature-for-storage-explorer.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/create-shared-access-signature-for-storage-explorer.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-cosmos-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string-for-cosmos-db.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-search-for-resource.png
