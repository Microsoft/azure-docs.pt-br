---
title: 'Tutorial: Aceitar e receber dados – Azure Data Share'
description: Tutorial - Aceitar e receber dados usando o Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 03/24/2021
ms.openlocfilehash: ccfda4975b6453ed67edc2640520bc0a76df5709
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105644876"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>Tutorial: Aceitar e receber dados usando o Azure Data Share  

Neste tutorial, você aprenderá a aceitar um convite de compartilhamento de dados usando o Azure Data Share. Você aprenderá a receber dados que estão sendo compartilhados com você, bem como habilitar um intervalo de atualização regular para sempre ter o instantâneo mais recente dos dados que estão sendo compartilhados com você. 

> [!div class="checklist"]
> * Como aceitar um convite do Azure Data Share
> * Criar um Azure Data Share
> * Especificar um destino para seus dados
> * Criar uma assinatura para seu compartilhamento de dados para a atualização agendada

## <a name="prerequisites"></a>Pré-requisitos
Antes de aceitar um convite de compartilhamento de dados, é preciso provisionar vários recursos do Azure, que estão listados abaixo. 

Cumpra todos os pré-requisitos antes de aceitar um convite de compartilhamento de dados. 

* Assinatura do Azure: Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Um convite do Azure Data Share: Um convite do Microsoft Azure com o assunto intitulado "Convite do Azure Data Share de **<yourdataprovider@domain.com>** ".
* Registre o [provedor de recursos Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) na assinatura do Azure, na qual você criará um recurso do Data Share e a assinatura do Azure em que os armazenamentos de dados do Azure de destino estão localizados.

### <a name="receive-data-into-a-storage-account"></a>Receber dados em uma conta de armazenamento

* Uma conta de Armazenamento do Azure: Se você não tiver uma, poderá criar uma [conta de Armazenamento do Azure](../storage/common/storage-account-create.md). 
* Permissão para gravar na conta de armazenamento, que está presente em *Microsoft.Storage/storageAccounts/write*. Essa permissão existe na função Colaborador. 
* Permissão para adicionar uma atribuição de função à conta de armazenamento, que está presente em *Microsoft.Authorization/role assignments/write*. Essa permissão existe na função Proprietário.  

### <a name="receive-data-into-a-sql-based-target"></a>Receber dados em um destino baseado em SQL
Se você optar por receber os dados no Banco de Dados SQL do Azure e no Azure Synapse Analytics, veja abaixo a lista de pré-requisitos. 

#### <a name="prerequisites-for-receiving-data-into-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Pré-requisitos para o recebimento de dados no Banco de Dados SQL do Azure ou no Azure Synapse Analytics (antigo SQL DW do Azure)

* Um Banco de Dados SQL do Azure ou o Azure Synapse Analytics (antigo SQL DW do Azure).
* Permissão para gravar nos bancos de dados no SQL Server, que está presente em *Microsoft.Sql/servers/databases/write*. Essa permissão existe na função **Colaborador**. 
* **Administrador do Azure Active Directory** do SQL Server
* Acesso ao firewall do SQL Server. Isso pode ser feito executando as seguintes etapas: 
    1. No SQL Server, no portal do Azure, navegue até *Firewalls e redes virtuais*
    1. Clique em **Sim** na opção *Permitir que serviços e recursos do Azure acessem este servidor*.
    1. Clique em **+Adicionar IP do cliente**. O endereço IP do cliente está sujeito a alterações. Esse processo talvez precise ser repetido na próxima vez em que você estiver compartilhando dados SQL do portal do Azure. Você também pode adicionar um intervalo de IP.
    1. Clique em **Save** (Salvar). 
 
#### <a name="prerequisites-for-receiving-data-into-azure-synapse-analytics-workspace-sql-pool"></a>Pré-requisitos para o recebimento de dados no pool de SQL do Azure Synapse Analytics (workspace)

* Um pool de SQL dedicado do Azure Synapse Analytics (workspace). No momento, não há suporte para o recebimento de dados no pool de SQL sem servidor.
* Permissão de gravação no pool de SQL no workspace do Azure Synapse, que está presente em *Microsoft.Synapse/workspaces/sqlPools/write*. Essa permissão existe na função **Colaborador**.
* Permissão para que a identidade gerenciada do recurso do Data Share acesse o pool de SQL do workspace do Azure Synapse. Isso pode ser feito executando as seguintes etapas: 
    1. No portal do Azure, navegue até o workspace do Azure Synapse. Selecione o administrador do SQL Active Directory no painel de navegação à esquerda e defina você mesmo como o **administrador do Azure Active Directory**.
    1. Abra o Synapse Studio e selecione *Gerenciar* no painel de navegação à esquerda. Escolha *Controle de acesso* em Segurança. Atribua a você mesmo a função **Administrador do SQL** ou **Administrador do workspace**.
    1. No Synapse Studio, selecione *Desenvolver* no painel de navegação à esquerda. Execute o script a seguir no pool de SQL para adicionar a identidade gerenciada do recurso do Data Share como um 'db_datareader, db_datawriter, db_ddladmin'. 
    
        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```                   
       Observe que *<share_acc_name>* é o nome do seu recurso do Data Share. Se você ainda não tiver criado um recurso do Data Share, poderá voltar para esse pré-requisito mais tarde.  

* Acesso ao Firewall do workspace do Azure Synapse. Isso pode ser feito executando as seguintes etapas: 
    1. No portal do Azure, navegue até o workspace do Azure Synapse. Selecione *Firewalls* no painel de navegação à esquerda.
    1. Clique em **ATIVADO** na opção *Permitir que serviços e recursos do Azure acessem este workspace*.
    1. Clique em **+Adicionar IP do cliente**. O endereço IP do cliente está sujeito a alterações. Esse processo talvez precise ser repetido na próxima vez em que você estiver compartilhando dados SQL do portal do Azure. Você também pode adicionar um intervalo de IP.
    1. Clique em **Save** (Salvar). 

### <a name="receive-data-into-an-azure-data-explorer-cluster"></a>Receba os dados em um cluster do Azure Data Explorer: 

* Um cluster do Azure Data Explorer no mesmo data center do Azure que o cluster do Data Explorer do provedor de dados: Se você não tiver, poderá criar um [cluster do Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal). Se você não conhecer o data center do Azure do cluster do provedor de dados, poderá criá-lo posteriormente no processo.
* Permissão para gravar no cluster do Azure Data Explorer, que está presente em *Microsoft.Kusto/clusters/write*. Essa permissão existe na função Colaborador. 

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="open-invitation"></a>Convite aberto

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Você pode abrir o convite do email ou diretamente do portal do Azure. 

   Para abrir um convite do email, confira sua caixa de entrada para um convite de seu provedor de dados. O convite é do Microsoft Azure, intitulado **Convite do Azure Data Share de <yourdataprovider@domain.com>** . Clique em **Exibir convite** para ver seu convite no Azure. 

   Para abrir o convite diretamente do portal do Azure, pesquise por **Convites do Data Share** no portal do Azure. Essa ação levará você à lista de convites do Data Share.

   ![Lista de convites](./media/invitations.png "Lista de convites") 

1. Selecione o compartilhamento que você deseja exibir. 

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Prepare seu ambiente da CLI do Azure e veja seus convites.

Comece preparando seu ambiente para a CLI do Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Execute o comando [az datashare consumer invitation list](/cli/azure/ext/datashare/datashare/consumer/invitation#ext_datashare_az_datashare_consumer_invitation_list) para ver seus convites atuais:

```azurecli
az datashare consumer invitation list --subscription 11111111-1111-1111-1111-111111111111
```

Copie sua ID de convite para uso na próxima seção.

---

## <a name="accept-invitation"></a>Aceitar o convite

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Examine todos os campos, incluindo os **Termos de uso**. Se você concordar com os termos de uso, será solicitado a marcar a caixa para indicar sua concordância. 

   ![Termos de uso](./media/terms-of-use.png "Termos de uso") 

1. Em *Conta do Data Share de Destino*, selecione a assinatura e o Grupo de Recursos em que você vai implantar seu Data Share. 

   Para o campo **Conta do Data Share**, selecione **Criar nova** se você não tiver uma conta do Data Share. Caso contrário, selecione uma conta do Data Share em que você gostaria de aceitar o compartilhamento de dados. 

   Para o campo **Nome do Compartilhamento Recebido**, deixe o padrão especificado pelo provedor de dados ou especifique um novo nome para o compartilhamento recebido. 

   Depois de concordar com os termos de uso e especificar uma conta do Data Share para gerenciar seu compartilhamento recebido, selecione **Aceitar e configurar**. Uma assinatura de compartilhamento será criada. 

   ![Aceitar opções](./media/accept-options.png "Aceitar opções") 

   Essa ação levará você para o compartilhamento recebido na sua conta do Data Share. 

   Se você não quiser aceitar o convite, selecione *Rejeitar*. 

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Use o comando [az datashare consumer share-subscription create](/cli/azure/ext/datashare/datashare/consumer/share-subscription#ext_datashare_az_datashare_consumer_share_subscription_create) para criar o Data Share.

```azurecli
az datashare consumer share-subscription create --resource-group share-rg \
  --name "Fabrikam Solutions" --account-name FabrikamDataShareAccount \
  --invitation-id 89abcdef-0123-4567-89ab-cdef01234567 \
  --source-share-location "East US 2" --subscription 11111111-1111-1111-1111-111111111111
```

---

## <a name="configure-received-share"></a>Configurar o compartilhamento recebido

### <a name="portal"></a>[Portal](#tab/azure-portal)

Siga as etapas abaixo para configurar o local em que deseja receber os dados.

1. Selecione a guia **Conjuntos de Dados**. Marque a caixa ao lado do conjunto de dados ao qual deseja atribuir um destino. Selecione **+ Mapear para o destino** para escolher um armazenamento de dados de destino. 

   ![Mapear para o destino](./media/dataset-map-target.png "Mapear para o destino") 

1. Selecione um tipo de armazenamento de dados de destino no qual deseja que os dados sejam inseridos. Todos os arquivos de dados ou tabelas no armazenamento de dados de destino com o mesmo caminho e nome serão substituídos. Se você estiver recebendo dados para o Banco de Dados SQL do Azure ou o Azure Synapse Analytics (chamado anteriormente de Azure SQL DW), marque a caixa de seleção **Permitir que o compartilhamento de dados execute o script 'create user' acima em meu nome**.

   Para o compartilhamento in-loco, selecione um armazenamento de dados na Localização especificada. A Localização é o data center do Azure no qual o armazenamento de dados de origem do provedor de dados está localizado. Depois que o conjunto de dados for mapeado, siga o link no Caminho de Destino para acessar os dados.

   ![Conta de armazenamento de destino](./media/dataset-map-target-sql.png "Armazenamento de destino") 

1. Para o compartilhamento baseado em instantâneo, se o provedor de dados tiver criado um agendamento de instantâneo para fornecer atualização regular aos dados, você também poderá habilitar o agendamento de instantâneo selecionando a guia **Agendamento de Instantâneos**. Marque a caixa ao lado do agendamento de instantâneo e selecione **+ Habilitar**. Observe que o primeiro instantâneo agendado começará dentro de um minuto após a hora agendada e os instantâneos posteriores começarão em segundos da hora agendada.

   ![Habilitar o agendamento de instantâneo](./media/enable-snapshot-schedule.png "Habilitar o agendamento de instantâneo")

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Use estes comandos para configurar a localização em que deseja receber os dados.

1. Execute o comando [az datashare consumer share-subscription list-source-dataset](/cli/azure/ext/datashare/datashare/consumer/share-subscription#ext_datashare_az_datashare_consumer_share_subscription_list_source_dataset) para obter a ID do conjunto de dados:

   ```azurecli
   az datashare consumer share-subscription list-source-dataset \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "[0].dataSetId"
   ```

1. Execute o comando [az storage account create](/cli/azure/storage/account#az_storage_account_create) para criar uma conta de armazenamento para este Data Share:

   ```azurecli
   az storage account create --resource-group "share-rg" --name "FabrikamDataShareAccount" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. Use o comando [az storage account show](/cli/azure/storage/account#az_storage_account_show) para obter a ID da conta de armazenamento:

   ```azurecli
   az storage account show --resource-group "share-rg" --name "FabrikamDataShareAccount" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "id"
   ```

1. Use o seguinte comando para obter a ID da entidade de segurança da conta:

   ```azurecli
   az datashare account show --resource-group "share-rg" --name "cli_test_consumer_account" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "identity.principalId"
   ```

1. Use o comando [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) para criar uma atribuição de função para a entidade de segurança da conta:

   ```azurecli
   az role assignment create --role "01234567-89ab-cdef-0123-456789abcdef" \
     --assignee-object-id 6789abcd-ef01-2345-6789-abcdef012345 
     --assignee-principal-type ServicePrincipal --scope 456789ab-cdef-0123-4567-89abcdef0123 \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. Crie uma variável para o mapeamento com base na ID do conjunto de dados:

   ```azurecli
   $mapping='{\"data_set_id\":\"' + $dataset_id + '\",\"container_name\":\"newcontainer\",
     \"storage_account_name\":\"datashareconsumersa\",\"kind\":\"BlobFolder\",\"prefix\":\"consumer\"}'
   ```

1. Use o comando [az datashare consumer dataset-mapping create](/cli/azure/ext/datashare/datashare/consumer/dataset-mapping#ext_datashare_az_datashare_consumer_dataset_mapping_create) para criar o mapeamento do conjunto de dados:

   ```azurecli
   az datashare consumer dataset-mapping create --resource-group "share-rg" \
     --name "consumer-data-set-mapping" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" --mapping $mapping \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. Execute o comando [az datashare consumer share-subscription synchronization start](/cli/azure/ext/datashare/datashare/consumer/share-subscription/synchronization#ext_datashare_az_datashare_consumer_share_subscription_synchronization_start) para iniciar a sincronização do conjunto de dados.

   ```azurecli
   az datashare consumer share-subscription synchronization start \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount"  \
     --share-subscription-name "Fabrikam Solutions" --synchronization-mode "Incremental" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

   Execute o comando [az datashare consumer share-subscription synchronization list](/cli/azure/ext/datashare/datashare/consumer/share-subscription/synchronization#ext_datashare_az_datashare_consumer_share_subscription_synchronization_list) para ver uma lista das sincronizações:

   ```azurecli
   az datashare consumer share-subscription synchronization list \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

   Use o comando [az datashare consumer share-subscription list-source-share-synchronization-setting](/cli/azure/ext/datashare/datashare/consumer/share-subscription#ext_datashare_az_datashare_consumer_share_subscription_list_source_share_synchronization_setting) para ver as configurações de sincronização definidas no seu compartilhamento.

   ```azurecli
   az datashare consumer share-subscription list-source-share-synchronization-setting \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" --subscription 11111111-1111-1111-1111-111111111111
   ```

---

## <a name="trigger-a-snapshot"></a>Disparar um instantâneo

### <a name="portal"></a>[Portal](#tab/azure-portal)

Essas etapas se aplicam somente ao compartilhamento baseado em instantâneo.

1. Você pode disparar um instantâneo selecionando a guia **Detalhes** seguido de **Disparar instantâneo**. Aqui, você pode disparar um instantâneo completo ou incremental de seus dados. Se esta for a primeira vez que você está recebendo dados de seu provedor de dados, selecione a cópia completa. 

   ![Disparar instantâneo](./media/trigger-snapshot.png "Disparar instantâneo") 

1. Quando o status da última execução for *bem-sucedido*, acesse o armazenamento de dados de destino para exibir os dados recebidos. Selecione **Conjuntos de dados** e clique no link no Caminho de Destino. 

   ![Conjuntos de dados do consumidor](./media/consumer-datasets.png "Mapeamento de conjunto de dados do consumidor") 

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Execute o comando [az datashare consumer trigger create](/cli/azure/ext/datashare/datashare/consumer/trigger#ext_datashare_az_datashare_consumer_trigger_create) para disparar um instantâneo:

```azurecli
az datashare consumer trigger create --resource-group "share-rg" \
  --name "share_test_trigger" --account-name "FabrikamDataShareAccount" \
  --share-subscription-name "Fabrikam Solutions" --recurrence-interval "Day" \
  --synchronization-time "2020-04-23 18:00:00 +00:00" --kind ScheduleBased \
  --subscription 11111111-1111-1111-1111-111111111111
```

> [!NOTE]
> Use esse comando somente para o compartilhamento baseado em instantâneo.

---

## <a name="view-history"></a>Exibir histórico
Esta etapa só se aplica ao compartilhamento baseado em instantâneo. Para ver o histórico dos seus instantâneos, selecione a guia **Histórico**. Aqui, você encontrará o histórico de todos os instantâneos gerados nos últimos 30 dias.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando o recurso não for mais necessário, acesse a página **Visão Geral do Data Share** e selecione **Excluir** para removê-lo.

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu a aceitar e receber um Azure Data Share. Para saber mais sobre os conceitos do Azure Data Share, prossiga para a Terminologia do Azure Data Share.

> [!div class="nextstepaction"]
> [Conceitos do Azure Data Share](terminology.md)