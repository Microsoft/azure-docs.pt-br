---
title: Compartilhar e receber dados do Banco de Dados SQL do Azure e do Azure Synapse Analytics
description: Saiba como compartilhar e receber dados do banco de dados SQL do Azure e do Azure Synapse Analytics
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 02/24/2021
ms.openlocfilehash: ef8c1a50cd3568c6cec9bdb053b02e6e14741eb0
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644684"
---
# <a name="share-and-receive-data-from-azure-sql-database-and-azure-synapse-analytics"></a>Compartilhar e receber dados do Banco de Dados SQL do Azure e do Azure Synapse Analytics

[!INCLUDE[appliesto-sql](includes/appliesto-sql.md)]

O compartilhamento de dados do Azure dá suporte ao compartilhamento baseado em instantâneo do Azure SQL Database e ao Azure Synapse Analytics. Este artigo explica como compartilhar e receber dados dessas fontes.

O compartilhamento de dados do Azure dá suporte ao compartilhamento de tabelas e exibições do banco de dados SQL do Azure e do Azure Synapse Analytics (anteriormente conhecido como Azure SQL DW) e compartilhamento de tabelas do pool SQL dedicado do Azure Synapse Analytics (espaço de trabalho). O compartilhamento do pool SQL sem servidor do Azure Synapse Analytics (espaço de trabalho) não tem suporte no momento. Os consumidores de dados podem optar por aceitar os dados no Azure Data Lake Storage Gen2 ou no armazenamento de BLOBs do Azure como arquivos CSV ou parquet, bem como no banco de dados SQL do Azure e no Azure Synapse Analytics como tabelas.

Ao aceitar dados no Azure Data Lake Store Gen2 ou no armazenamento de BLOBs do Azure, instantâneos completos substituem o conteúdo do arquivo de destino, se já existir.
Quando os dados são recebidos na tabela SQL e, se a tabela de destino ainda não existir, o compartilhamento de dados do Azure criará a tabela SQL com o esquema de origem. Se uma tabela de destino já existir com o mesmo nome, ela será descartada e substituída pelo instantâneo completo mais recente. Não há suporte para instantâneos incrementais no momento.

## <a name="share-data"></a>Compartilhar dados

### <a name="prerequisites-to-share-data"></a>Pré-requisitos para compartilhar dados

* Assinatura do Azure: Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* O endereço de email de logon do Azure de seus destinatários (usar o alias de email não funcionará).
* Se o armazenamento de dados do Azure de origem estiver em uma assinatura do Azure diferente daquela que você usará para criar o recurso do Data Share, registre o [provedor de recursos Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) na assinatura em que o armazenamento de dados do Azure está localizado. 

### <a name="prerequisites-for-sql-source"></a>Pré-requisitos para a origem do SQL
Veja abaixo a lista de pré-requisitos para o compartilhamento de dados de uma fonte SQL. 

#### <a name="prerequisites-for-sharing-from-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Pré-requisitos para o compartilhamento por meio do Banco de Dados SQL do Azure ou do Azure Synapse Analytics (antigo SQL DW do Azure)


Para compartilhar dados usando a autenticação Azure Active Directory, aqui está uma lista de pré-requisitos:

* Um Banco de Dados SQL do Azure ou o Azure Synapse Analytics (antigo SQL DW do Azure) com tabelas e exibições que você deseja compartilhar.
* Permissão para gravar em bancos de dados no SQL Server, que está presente em *Microsoft.Sql/servers/databases/write*. Essa permissão existe na função **Colaborador**.
* SQL Server **Azure Active Directory administrador**
* Acesso ao firewall do SQL Server. Isso pode ser feito executando as seguintes etapas: 
    1. No portal do Azure, navegue até o SQL Server. Selecione *Firewalls e redes virtuais* no painel de navegação à esquerda.
    1. Clique em **Sim** na opção *Permitir que serviços e recursos do Azure acessem este servidor*.
    1. Clique em **+Adicionar IP do cliente**. O endereço IP do cliente está sujeito a alterações. Esse processo talvez precise ser repetido na próxima vez em que você estiver compartilhando dados SQL do portal do Azure. Você também pode adicionar um intervalo de IP.
    1. Clique em **Save** (Salvar). 

Para compartilhar dados usando a autenticação do SQL, veja abaixo uma lista de pré-requisitos. Siga a [demonstração passo a passo](https://youtu.be/hIE-TjJD8Dc) para configurar os pré-requisitos.

* Um Banco de Dados SQL do Azure ou o Azure Synapse Analytics (antigo SQL DW do Azure) com tabelas e exibições que você deseja compartilhar.
* Permissão para gravar em bancos de dados no SQL Server, que está presente em *Microsoft.Sql/servers/databases/write*. Essa permissão existe na função **Colaborador**.
* Permissão para que a identidade gerenciada do recurso do Data Share acesse o banco de dados. Isso pode ser feito executando as seguintes etapas: 
    1. No portal do Azure, navegue até o SQL Server e defina você mesmo como o **Administrador do Azure Active Directory**.
    1. Conecte-se ao Data Warehouse/Banco de Dados SQL do Azure usando o [Editor de Consultas](../azure-sql/database/connect-query-portal.md#connect-using-azure-active-directory) ou o SQL Server Management Studio com a autenticação do Azure Active Directory. 
    1. Execute o script a seguir para adicionar a identidade gerenciada do recurso do Data Share como um db_datareader. Você deve se conectar usando o Active Directory e não a autenticação do SQL Server. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       Observe que *<share_acc_name>* é o nome do seu recurso do Data Share. Se você ainda não tiver criado um recurso do Data Share, poderá voltar para esse pré-requisito mais tarde.  

* Um Usuário do Banco de Dados SQL do Azure com o acesso **'db_datareader'** para procurar e selecionar as tabelas e/ou as exibições que você deseja compartilhar. 

* Acesso ao firewall do SQL Server. Isso pode ser feito executando as seguintes etapas: 
    1. No portal do Azure, navegue até o SQL Server. Selecione *Firewalls e redes virtuais* no painel de navegação à esquerda.
    1. Clique em **Sim** na opção *Permitir que serviços e recursos do Azure acessem este servidor*.
    1. Clique em **+Adicionar IP do cliente**. O endereço IP do cliente está sujeito a alterações. Esse processo talvez precise ser repetido na próxima vez em que você estiver compartilhando dados SQL do portal do Azure. Você também pode adicionar um intervalo de IP.
    1. Clique em **Save** (Salvar). 

#### <a name="prerequisites-for-sharing-from-azure-synapse-analytics-workspace-sql-pool"></a>Pré-requisitos para o compartilhamento do pool de SQL do Azure Synapse Analytics (workspace)

* Um pool de SQL dedicado do Azure Synapse Analytics (workspace) com as tabelas que você deseja compartilhar. Atualmente, não há suporte para o compartilhamento de exibição. No momento, também não há suporte para o compartilhamento por meio do pool de SQL sem servidor.
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

### <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

### <a name="create-a-data-share-account"></a>Criar uma conta do Data Share

Crie um recurso do Azure Data Share em um grupo de recursos do Azure.

1. Clique no botão de menu no canto superior esquerdo do portal e selecione **Criar um recurso** (+).

1. Pesquise *Data Share*.

1. Selecione Data Share (versão prévia) e selecione **Criar**.

1. Preencha os detalhes básicos de seu recurso do Azure Data Share com as informações a seguir. 

     **Configuração** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Subscription | Sua assinatura | Selecione a assinatura do Azure que deseja usar para sua conta de compartilhamento de dados.|
    | Grupo de recursos | *test-resource-group* | Use um grupo de recursos existente ou crie um novo. |
    | Localização | *Leste dos EUA 2* | Selecione uma região para sua conta de compartilhamento de dados.
    | Nome | *datashareaccount* | Especifique um nome para sua conta de compartilhamento de dados. |
    | | |

1. Selecione **Examinar + criar** e **Criar** para provisionar sua conta de compartilhamento de dados. O provisionamento de uma nova conta de compartilhamento de dados normalmente leva cerca de 2 minutos ou menos. 

1. Depois que a implantação estiver concluída, selecione **Ir para o recurso**.

### <a name="create-a-share"></a>Criar um compartilhamento

1. Navegue até sua página de Visão Geral do Data Share.

    ![Compartilhar seus dados](./media/share-receive-data.png "Compartilhar seus dados") 

1. Selecione **Começar a compartilhar seus dados**.

1. Selecione **Criar**.   

1. Preencha os detalhes do seu compartilhamento. Especifique um nome, um tipo de compartilhamento, uma descrição do conteúdo de compartilhamento e os termos de uso (opcional). 

    ![EnterShareDetails](./media/enter-share-details.png "Inserir detalhes do Compartilhamento") 

1. Selecione **Continuar**.

1. Para adicionar conjuntos de dados ao compartilhamento, selecione **Adicionar Conjuntos de Dados**. 

    ![Adicionar conjuntos de dados ao compartilhamento](./media/datasets.png "Conjunto de dados")

1. Selecione o tipo de conjunto de dados que você deseja adicionar. Você verá uma lista diferente de tipos de conjuntos de dados, dependendo do tipo de compartilhamento (instantâneo ou in-loco) que você selecionou na etapa anterior. 

    ![AddDatasets](./media/add-datasets.png "Adicionar conjuntos de dados")    

1. Selecione seu espaço de trabalho do SQL Server ou do Synapse. Se você estiver usando a autenticação do AAD e a caixa de seleção **permitir que o compartilhamento de dados execute o script SQL ' criar usuário ' acima em meu nome** aparecer, marque a caixa de seleção. Se você estiver usando a autenticação do SQL, forneça credenciais e siga as etapas em pré-requisitos para executar o script aparecendo na tela. Isso dá permissão ao recurso de compartilhamento de dados para ler a partir do seu BD SQL. 

   Selecione **Avançar** para navegar até o objeto que deseja compartilhar e selecione "Adicionar conjuntos de os". Você pode selecionar tabelas e exibições do banco de dados SQL do Azure e do Azure Synapse Analytics (anteriormente conhecido como Azure SQL DW) ou tabelas do pool SQL dedicado do Azure Synapse Analytics (espaço de trabalho). 

    ![SelectDatasets](./media/select-datasets-sql.png "Selecionar conjuntos de dados")    

1. Na guia Destinatários, insira os endereços de email de seu Consumidor de Dados selecionando "+ Adicionar Destinatário". O endereço de email precisa ser um email de logon do Azure do destinatário.

    ![AddRecipients](./media/add-recipient.png "Adicionar destinatários") 

1. Selecione **Continuar**.

1. Se você tiver selecionado o tipo de compartilhamento de instantâneo, poderá configurar a agenda de instantâneo para fornecer atualizações de seus dados para o consumidor de dados. 

    ![EnableSnapshots](./media/enable-snapshots.png "Habilitar instantâneos") 

1. Selecione uma hora de início e um intervalo de recorrência. 

1. Selecione **Continuar**.

1. Na guia Examinar + Criar, examine o Conteúdo do Pacote, Configurações, Destinatários e Configurações de Sincronização. Selecione **Criar**.

O Azure Data Share agora foi criado e o destinatário de seu Compartilhamento de Dados agora está pronto para aceitar seu convite. 

## <a name="receive-data"></a>Receber dados

### <a name="prerequisites-to-receive-data"></a>Pré-requisitos para receber dados
Antes de aceitar um convite de compartilhamento de dados, é preciso provisionar vários recursos do Azure, que estão listados abaixo. 

Cumpra todos os pré-requisitos antes de aceitar um convite de compartilhamento de dados. 

* Assinatura do Azure: Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Um convite do Azure Data Share: Um convite do Microsoft Azure com o assunto intitulado "Convite do Azure Data Share de **<yourdataprovider@domain.com>** ".
* Registre o [provedor de recursos Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) na assinatura do Azure, na qual você criará um recurso do Data Share e a assinatura do Azure em que os armazenamentos de dados do Azure de destino estão localizados.

### <a name="prerequisites-for-target-storage-account"></a>Pré-requisitos para a conta de armazenamento de destino
Se você optar por receber dados no armazenamento do Azure, abaixo está a lista de pré-requisitos.

* Uma conta de Armazenamento do Azure: Se você não tiver uma, poderá criar uma [conta de Armazenamento do Azure](../storage/common/storage-account-create.md). 
* Permissão para gravar na conta de armazenamento, que está presente em *Microsoft.Storage/storageAccounts/write*. Essa permissão existe na função **Colaborador**. 
* Permissão para adicionar a atribuição de função da identidade gerenciada do recurso de compartilhamento de dados à conta de armazenamento, que está presente no *Microsoft. Authorization/atribuições/gravação de função*. Essa permissão existe na função **Proprietário**.  

### <a name="prerequisites-for-sql-target"></a>Pré-requisitos para o destino do SQL
Se você optar por receber os dados no Banco de Dados SQL do Azure e no Azure Synapse Analytics, veja abaixo a lista de pré-requisitos. 

#### <a name="prerequisites-for-receiving-data-into-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Pré-requisitos para o recebimento de dados no Banco de Dados SQL do Azure ou no Azure Synapse Analytics (antigo SQL DW do Azure)

Para receber dados em um SQL Server em que você é o **administrador do Azure Active Directory** do SQL Server, aqui está uma lista de pré-requisitos:

* Um Banco de Dados SQL do Azure ou o Azure Synapse Analytics (antigo SQL DW do Azure).
* Permissão para gravar em bancos de dados no SQL Server, que está presente em *Microsoft.Sql/servers/databases/write*. Essa permissão existe na função **Colaborador**.
* Acesso ao firewall do SQL Server. Isso pode ser feito executando as seguintes etapas: 
    1. No portal do Azure, navegue até o SQL Server. Selecione *Firewalls e redes virtuais* no painel de navegação à esquerda.
    1. Clique em **Sim** na opção *Permitir que serviços e recursos do Azure acessem este servidor*.
    1. Clique em **+Adicionar IP do cliente**. O endereço IP do cliente está sujeito a alterações. Esse processo talvez precise ser repetido na próxima vez em que você estiver compartilhando dados SQL do portal do Azure. Você também pode adicionar um intervalo de IP.
    1. Clique em **Save** (Salvar). 
    
Para receber dados em um SQL Server em que você não é o **administrador do Azure Active Directory**, abaixo está uma lista de pré-requisitos. Siga a [demonstração passo a passo](https://youtu.be/aeGISgK1xro) para configurar os pré-requisitos.

* Um Banco de Dados SQL do Azure ou o Azure Synapse Analytics (antigo SQL DW do Azure).
* Permissão para gravar nos bancos de dados no SQL Server, que está presente em *Microsoft.Sql/servers/databases/write*. Essa permissão existe na função **Colaborador**. 
* Permissão para que a identidade gerenciada do recurso do Data Share acesse o Banco de Dados SQL do Azure ou o Azure Synapse Analytics. Isso pode ser feito executando as seguintes etapas: 
    1. No portal do Azure, navegue até o SQL Server e defina você mesmo como o **Administrador do Azure Active Directory**.
    1. Conecte-se ao Data Warehouse/Banco de Dados SQL do Azure usando o [Editor de Consultas](../azure-sql/database/connect-query-portal.md#connect-using-azure-active-directory) ou o SQL Server Management Studio com a autenticação do Azure Active Directory. 
    1. Execute o script a seguir para adicionar a identidade gerenciada do Data Share como um 'db_datareader, db_datawriter, db_ddladmin'. Você deve se conectar usando o Active Directory e não a autenticação do SQL Server. 

        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```      
        Observe que *<share_acc_name>* é o nome do seu recurso do Data Share. Se você ainda não tiver criado um recurso do Data Share, poderá voltar para esse pré-requisito mais tarde.         

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

### <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

### <a name="open-invitation"></a>Convite aberto

1. Você pode abrir o convite do email ou diretamente do portal do Azure. 

   Para abrir um convite do email, confira sua caixa de entrada para um convite de seu provedor de dados. O convite é do Microsoft Azure, intitulado **Convite do Azure Data Share de <yourdataprovider@domain.com>** . Clique em **Exibir convite** para ver seu convite no Azure. 

   Para abrir o convite diretamente do portal do Azure, pesquise por **Convites do Data Share** no portal do Azure. Isso levará você à lista de convites do Data Share.

   ![Lista de convites](./media/invitations.png "Lista de convites") 

1. Selecione o compartilhamento que você deseja exibir. 

### <a name="accept-invitation"></a>Aceitar o convite
1. Examine todos os campos, incluindo os **Termos de uso**. Se você concordar com os termos de uso, será solicitado a marcar a caixa para indicar sua concordância. 

   ![Termos de uso](./media/terms-of-use.png "Termos de uso") 

1. Em *Conta do Data Share de Destino*, selecione a assinatura e o Grupo de Recursos em que você vai implantar seu Data Share. 

   Para o campo **Conta do Data Share**, selecione **Criar nova** se você não tiver uma conta do Data Share. Caso contrário, selecione uma conta do Data Share em que você gostaria de aceitar o compartilhamento de dados. 

   Para o campo **Nome do Compartilhamento Recebido**, deixe o padrão especificado pelo provedor de dados ou especifique um novo nome para o compartilhamento recebido. 

   Depois de concordar com os termos de uso e especificar uma conta do Data Share para gerenciar seu compartilhamento recebido, selecione **Aceitar e configurar**. Uma assinatura de compartilhamento será criada. 

   ![Aceitar opções](./media/accept-options.png "Aceitar opções") 

   Isso levará você para o compartilhamento recebido em sua conta de compartilhamento de dados. 

   Se você não quiser aceitar o convite, selecione *Rejeitar*. 

### <a name="configure-received-share"></a>Configurar o compartilhamento recebido
Siga as etapas abaixo para configurar o local em que deseja receber os dados.

1. Selecione a guia **Conjuntos de Dados**. Marque a caixa ao lado do conjunto de dados ao qual deseja atribuir um destino. Selecione **+ Mapear para o destino** para escolher um armazenamento de dados de destino. 

   ![Mapear para o destino](./media/dataset-map-target.png "Mapear para o destino") 

1. Selecione um armazenamento de dados de destino no qual você deseja que os dados entrem. Todos os arquivos de dados ou tabelas no armazenamento de dados de destino com o mesmo caminho e nome serão substituídos. Se você estiver recebendo dados para o destino do SQL e a caixa de seleção **permitir que o compartilhamento de dados seja executado acima, selecione o script SQL ' criar usuário ' em meu nome** aparecer, marque a caixa de seleção. Caso contrário, siga as instruções em pré-requisitos para executar o script aparecendo na tela. Isso fornecerá permissão de gravação de recurso de compartilhamento de dados para seu banco de dado SQL de destino.

   ![Conta de armazenamento de destino](./media/dataset-map-target-sql.png "Armazenamento de dados de destino") 

1. Para o compartilhamento baseado em instantâneo, se o provedor de dados tiver criado um agendamento de instantâneo para fornecer atualização regular aos dados, você também poderá habilitar o agendamento de instantâneo selecionando a guia **Agendamento de Instantâneos**. Marque a caixa ao lado do agendamento de instantâneo e selecione **+ Habilitar**. Observe que o primeiro instantâneo agendado começará dentro de um minuto após o tempo de agendamento e os instantâneos subsequentes serão iniciados em segundos da hora agendada.

   ![Habilitar o agendamento de instantâneo](./media/enable-snapshot-schedule.png "Habilitar o agendamento de instantâneo")

### <a name="trigger-a-snapshot"></a>Disparar um instantâneo
Essas etapas se aplicam somente ao compartilhamento baseado em instantâneo.

1. Você pode disparar um instantâneo selecionando a guia **Detalhes** seguido de **Disparar instantâneo**. Aqui, você pode disparar um instantâneo completo ou incremental de seus dados. Se esta for a primeira vez que você está recebendo dados de seu provedor de dados, selecione a cópia completa. Para fontes SQL, somente o instantâneo completo tem suporte. Quando um instantâneo estiver em execução, os instantâneos subsequentes não serão iniciados até que o anterior seja concluído.

   ![Disparar instantâneo](./media/trigger-snapshot.png "Disparar instantâneo") 

1. Quando o status da última execução for *bem-sucedido*, acesse o armazenamento de dados de destino para exibir os dados recebidos. Selecione **Conjuntos de dados** e clique no link no Caminho de Destino. 

   ![Conjuntos de dados do consumidor](./media/consumer-datasets.png "Mapeamento de conjunto de dados do consumidor") 

### <a name="view-history"></a>Exibir histórico
Esta etapa só se aplica ao compartilhamento baseado em instantâneo. Para ver o histórico dos seus instantâneos, selecione a guia **Histórico**. Aqui, você encontrará o histórico de todos os instantâneos gerados nos últimos 30 dias. 

## <a name="supported-data-types"></a>Tipos de dados com suporte
Quando você compartilha dados da fonte SQL, o mapeamento a seguir é usado de SQL Server tipos de dados para tipos de dados provisórios do compartilhamento de dados do Azure durante o processo de instantâneo. 

| Tipo de dados do SQL Server | Tipo de dados provisório do compartilhamento de dados do Azure |
|:--- |:--- |
| BIGINT |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |Datetime |
| Datetime |Datetime |
| datetime2 |Datetime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| INT |Int32 |
| money |Decimal |
| NCHAR |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| NVARCHAR |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |Datetime |
| SMALLINT |Int16 |
| SMALLMONEY |Decimal |
| sql_variant |Objeto |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| TINYINT |Int16 |
| UNIQUEIDENTIFIER |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |String |

>[!NOTE]
> 1. Para tipos de dados que são mapeados para o tipo provisório decimal, o instantâneo atualmente dá suporte à precisão de até 28. Se você tiver dados que exijam precisão maior que 28, considere converter para uma cadeia de caracteres. 
> 1.  Se você estiver compartilhando dados de um banco de dados SQL do Azure para o Azure Synapse Analytics, nem todos os tipos de dado têm suporte. Consulte [tipos de dados de tabela no pool de SQL dedicado](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-data-types.md) para obter detalhes. 

## <a name="sql-always-encrypted-or-dynamic-data-masking"></a>SQL Always Encrypted ou Máscara de Dados Dinâmicos
Atualmente, o compartilhamento de dados do Azure não oferece suporte a bancos de dados SQL do Azure com Always Encrypted configurado. 

Para tabelas SQL de origem com máscara de dados dinâmicos, os dados aparecerão mascarados no lado do destinatário.

## <a name="sql-snapshot-performance"></a>Desempenho de instantâneo do SQL
O desempenho do instantâneo do SQL é afetado por vários fatores. É sempre recomendável realizar seus próprios testes de desempenho. Veja abaixo alguns fatores de exemplo que afetam o desempenho.

* Configuração de hardware (por exemplo, vCores, Memory, DWU) do armazenamento de dados SQL de origem e de destino. 
* Acesso simultâneo aos armazenamentos de dados de origem e de destino. Se você estiver compartilhando várias tabelas e exibições do mesmo armazenamento de dados SQL, ou receber várias tabelas e exibições no mesmo armazenamento de dados SQL, o desempenho será afetado.   
* Local dos armazenamentos de dados de origem e de destino. 

## <a name="troubleshoot-sql-snapshot-failure"></a>Solucionar falhas de instantâneo do SQL
A causa mais comum de falha de instantâneo é que o compartilhamento de dados não tem permissão para o armazenamento de dados de origem ou de destino. Para conceder permissão de compartilhamento de dados para a origem ou o destino do Azure SQL Database ou o Azure Synapse Analytics (anteriormente conhecido como Azure SQL DW), você deve executar o script SQL fornecido ao se conectar ao banco de dados SQL usando a autenticação Azure Active Directory. Para solucionar falhas adicionais de instantâneo de SQL, consulte [solucionar problemas de instantâneo](data-share-troubleshoot.md#snapshots).

## <a name="next-steps"></a>Próximas etapas
Você aprendeu a compartilhar e receber dados de fontes SQL usando o serviço de compartilhamento de dados do Azure. Para saber mais sobre o compartilhamento de outras fontes de dados, continue com [os armazenamentos de dados com suporte](supported-data-stores.md).