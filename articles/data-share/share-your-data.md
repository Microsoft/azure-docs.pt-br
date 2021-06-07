---
title: 'Tutorial: Compartilhar fora de sua organização – Azure Data Share'
description: Tutorial – compartilhar dados com clientes e parceiros usando o Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 03/24/2021
ms.openlocfilehash: 8e149270d8f98cbf72d3864d238a3d8ddfd61c67
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105639535"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>Tutorial: Compartilhar dados usando o Azure Data Share  

Neste tutorial, você aprenderá a configurar um novo Azure Data Share e começará a compartilhar seus dados com clientes e parceiros fora da organização do Azure. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um Data Share.
> * Adicionar conjuntos de dados a seu Data Share.
> * Habilitar uma agenda de instantâneo para seu Data Share. 
> * Adicionar destinatários a seu Data Share. 

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure: Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* O endereço de email de logon do Azure de seus destinatários (usar o alias de email não funcionará).
* Se o armazenamento de dados do Azure de origem estiver em uma assinatura do Azure diferente daquela que você usará para criar o recurso do Data Share, registre o [provedor de recursos Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) na assinatura em que o armazenamento de dados do Azure está localizado. 

### <a name="share-from-a-storage-account"></a>Compartilhamento por meio de uma conta de armazenamento

* Uma conta de Armazenamento do Azure: Se você não tiver uma, poderá criar uma [conta de Armazenamento do Azure](../storage/common/storage-account-create.md)
* Permissão para gravar na conta de armazenamento, que está presente em *Microsoft.Storage/storageAccounts/write*. Essa permissão existe na função **Colaborador**.
* Permissão para adicionar uma atribuição de função à conta de armazenamento, que está presente em *Microsoft.Authorization/role assignments/write*. Essa permissão existe na função **Proprietário**. 


### <a name="share-from-a-sql-based-source"></a>Compartilhamento por meio de uma fonte baseada em SQL
Veja abaixo a lista de pré-requisitos para o compartilhamento de dados de uma fonte SQL. 

#### <a name="prerequisites-for-sharing-from-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Pré-requisitos para o compartilhamento por meio do Banco de Dados SQL do Azure ou do Azure Synapse Analytics (antigo SQL DW do Azure)

* Um Banco de Dados SQL do Azure ou o Azure Synapse Analytics (antigo SQL DW do Azure) com tabelas e exibições que você deseja compartilhar.
* Permissão para gravar em bancos de dados no SQL Server, que está presente em *Microsoft.Sql/servers/databases/write*. Essa permissão existe na função **Colaborador**.
* **Administrador do Azure Active Directory** do SQL Server
* Acesso ao firewall do SQL Server. Isso pode ser feito executando as seguintes etapas: 
    1. No portal do Azure, navegue até o SQL Server. Selecione *Firewalls e redes virtuais* no painel de navegação à esquerda.
    1. Clique em **Sim** na opção *Permitir que serviços e recursos do Azure acessem este servidor*.
    1. Clique em **+Adicionar IP do cliente**. O endereço IP do cliente está sujeito a alterações. Esse processo talvez precise ser repetido na próxima vez em que você estiver compartilhando dados SQL do portal do Azure. Você também pode adicionar um intervalo de IP.
    1. Clique em **Save** (Salvar). 

#### <a name="prerequisites-for-sharing-from-azure-synapse-analytics-workspace-sql-pool"></a>Pré-requisitos para o compartilhamento do pool de SQL do Azure Synapse Analytics (workspace)

* * Um pool de SQL dedicado do Azure Synapse Analytics (workspace) com as tabelas que você deseja compartilhar. Atualmente, não há suporte para o compartilhamento de exibição. No momento, também não há suporte para o compartilhamento por meio do pool de SQL sem servidor.
* Permissão de gravação no pool de SQL no workspace do Azure Synapse, que está presente em *Microsoft.Synapse/workspaces/sqlPools/write*. Essa permissão existe na função **Colaborador**.
* Permissão para que a identidade gerenciada do recurso do Data Share acesse o pool de SQL do workspace do Azure Synapse. Isso pode ser feito executando as seguintes etapas: 
    1. No portal do Azure, navegue até o workspace do Azure Synapse. Selecione o administrador do SQL Active Directory no painel de navegação à esquerda e defina você mesmo como o **administrador do Azure Active Directory**.
    1. Abra o Synapse Studio e selecione *Gerenciar* no painel de navegação à esquerda. Escolha *Controle de acesso* em Segurança. Atribua a você mesmo a função **Administrador do SQL** ou **Administrador do workspace**.
    1. No Synapse Studio, selecione *Desenvolver* no painel de navegação à esquerda. Execute o script a seguir no pool de SQL para adicionar a identidade gerenciada do recurso do Data Share como um db_datareader. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       Observe que *<share_acc_name>* é o nome do seu recurso do Data Share. Se você ainda não tiver criado um recurso do Data Share, poderá voltar para esse pré-requisito mais tarde.  

* Acesso ao Firewall do workspace do Azure Synapse. Isso pode ser feito executando as seguintes etapas: 
    1. No portal do Azure, navegue até o workspace do Azure Synapse. Selecione *Firewalls* no painel de navegação à esquerda.
    1. Clique em **ATIVADO** na opção *Permitir que serviços e recursos do Azure acessem este workspace*.
    1. Clique em **+Adicionar IP do cliente**. O endereço IP do cliente está sujeito a alterações. Esse processo talvez precise ser repetido na próxima vez em que você estiver compartilhando dados SQL do portal do Azure. Você também pode adicionar um intervalo de IP.
    1. Clique em **Save** (Salvar). 


### <a name="share-from-azure-data-explorer"></a>Compartilhar do Azure Data Explorer
* Um cluster do Azure Data Explorer com bancos de dados que você deseja compartilhar.
* Permissão para gravar no cluster do Azure Data Explorer, que está presente em *Microsoft.Kusto/clusters/write*. Essa permissão existe na função **Colaborador**.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-data-share-account"></a>Criar uma conta do Data Share

### <a name="portal"></a>[Portal](#tab/azure-portal)

Crie um recurso do Azure Data Share em um grupo de recursos do Azure.

1. Clique no botão de menu no canto superior esquerdo do portal e selecione **Criar um recurso** (+).

1. Pesquise *Data Share*.

1. Selecione Data Share (versão prévia) e selecione **Criar**.

1. Preencha os detalhes básicos de seu recurso do Azure Data Share com as informações a seguir. 

     **Configuração** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Subscription | Sua assinatura | Selecione a assinatura do Azure que deseja usar para sua conta de compartilhamento de dados.|
    | Grupo de recursos | *testresourcegroup* | Use um grupo de recursos existente ou crie um novo. |
    | Localização | *Leste dos EUA 2* | Selecione uma região para sua conta de compartilhamento de dados.
    | Nome | *datashareaccount* | Especifique um nome para sua conta de compartilhamento de dados. |
    | | |

1. Selecione **Examinar + criar** e **Criar** para provisionar sua conta de compartilhamento de dados. O provisionamento de uma nova conta de compartilhamento de dados normalmente leva cerca de 2 minutos ou menos. 

1. Depois que a implantação estiver concluída, selecione **Ir para o recurso**.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Crie um recurso do Azure Data Share em um grupo de recursos do Azure.

Comece preparando seu ambiente para a CLI do Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Use estes comandos para criar o recurso:

1. Use o comando [az account set](/cli/azure/account#az_account_set) para definir sua assinatura como a assinatura padrão atual:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Execute o comando [az provider register](/cli/azure/provider#az_provider_register) para registrar o provedor de recursos:

   ```azurecli
   az provider register --name "Microsoft.DataShare"
   ```

1. Execute o comando [az group create](/cli/azure/group#az_group_create) para criar um grupo de recursos ou usar um grupo de recursos existente:

   ```azurecli
   az group create --name testresourcegroup --location "East US 2"
   ```

1. Execute o comando [az datashare account create](/cli/azure/ext/datashare/datashare/account#ext_datashare_az_datashare_account_create) para criar uma conta do Data Share:

   ```azurecli
   az datashare account create --resource-group testresourcegroup --name datashareaccount --location "East US 2" 
   ```

   Execute o comando [az datashare account list](/cli/azure/ext/datashare/datashare/account#ext_datashare_az_datashare_account_list) para ver suas contas do Data Share:

   ```azurecli
   az datashare account list --resource-group testresourcegroup
   ```

---

## <a name="create-a-share"></a>Criar um compartilhamento

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navegue até sua página de Visão Geral do Data Share.

    ![Compartilhar seus dados](./media/share-receive-data.png "Compartilhar seus dados") 

1. Selecione **Começar a compartilhar seus dados**.

1. Selecione **Criar**.   

1. Preencha os detalhes do seu compartilhamento. Especifique um nome, um tipo de compartilhamento, uma descrição do conteúdo de compartilhamento e os termos de uso (opcional). 

    ![EnterShareDetails](./media/enter-share-details.png "Inserir detalhes do Compartilhamento") 

1. Selecione **Continuar**.

1. Para adicionar conjuntos de dados ao compartilhamento, selecione **Adicionar Conjuntos de Dados**. 

    ![Adicionar conjuntos de dados ao compartilhamento](./media/datasets.png "Conjunto de dados")

1. Selecione o tipo de conjunto de dados que você deseja adicionar. Você verá uma lista diferente de tipos de conjuntos de dados, dependendo do tipo de compartilhamento (instantâneo ou in-loco) que você selecionou na etapa anterior. Se você estiver compartilhando dados em um Banco de Dados SQL do Azure ou no Azure Synapse Analytics (antigo SQL DW do Azure), precisará fornecer o método de autenticação para listar tabelas. Selecione autenticação do AAD e marque a caixa de seleção **Permitir que o Data Share execute o script 'create user' acima em meu nome**. 

    ![AddDatasets](./media/add-datasets.png "Adicionar conjuntos de dados")    

1. Navegue até o objeto que você gostaria de compartilhar e selecione "Adicionar Conjuntos de Dados". 

    ![SelectDatasets](./media/select-datasets.png "Selecionar conjuntos de dados")    

1. Na guia Destinatários, insira os endereços de email de seu Consumidor de Dados selecionando "+ Adicionar Destinatário". 

    ![AddRecipients](./media/add-recipient.png "Adicionar destinatários") 

1. Selecione **Continuar**.

1. Se você tiver selecionado o tipo de compartilhamento de instantâneo, poderá configurar a agenda de instantâneo para fornecer atualizações de seus dados para o consumidor de dados. 

    ![EnableSnapshots](./media/enable-snapshots.png "Habilitar instantâneos") 

1. Selecione uma hora de início e um intervalo de recorrência. 

1. Selecione **Continuar**.

1. Na guia Examinar + Criar, examine o Conteúdo do Pacote, Configurações, Destinatários e Configurações de Sincronização. Selecione **Criar**.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Execute o comando [az storage account create](/cli/azure/storage/account#az_storage_account_create) para criar um Data Share:

   ```azurecli
   az storage account create --resource-group testresourcegroup --name ContosoMarketplaceAccount
   ```

1. Use o comando [az storage container create](/cli/azure/storage/container#az_storage_container_create) para criar um contêiner para o compartilhamento do comando anterior:

   ```azurecli
   az storage container create --name ContosoMarketplaceContainer --account-name ContosoMarketplaceAccount
   ```

1. Execute o comando [az datashare create](/cli/azure/ext/datashare/datashare#ext_datashare_az_datashare_create) para criar seu Data Share:

   ```azurecli
   az datashare create --resource-group testresourcegroup \
     --name ContosoMarketplaceDataShare --account-name ContosoMarketplaceAccount \
     --description "Data Share" --share-kind "CopyBased" --terms "Confidential"
   ```

1. Use o comando [az datashare invitation create](/cli/azure/ext/datashare/datashare/invitation#ext_datashare_az_datashare_invitation_create) para criar o convite para o endereço especificado:

   ```azurecli
   az datashare invitation create --resource-group testresourcegroup \
     --name DataShareInvite --share-name ContosoMarketplaceDataShare \
     --account-name ContosoMarketplaceAccount --target-email "jacob@fabrikam"
   ```

---

O Azure Data Share agora foi criado e o destinatário de seu Compartilhamento de Dados agora está pronto para aceitar seu convite.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando o recurso não for mais necessário, acesse a página **Visão Geral do Data Share** e selecione **Excluir** para removê-lo.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a criar um Azure Data Share e convidar destinatários. Para saber mais sobre como um Consumidor de Dados pode aceitar e receber um compartilhamento de dados, prossiga para o tutorial Aceitar e receber dados.

> [!div class="nextstepaction"]
> [Tutorial: Aceitar e receber dados usando o Azure Data Share](subscribe-to-data-share.md)
