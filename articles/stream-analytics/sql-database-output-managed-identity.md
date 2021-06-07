---
title: Usar identidades gerenciadas para acessar o banco de dados SQL do Azure ou o Azure Synapse Analytics-Azure Stream Analytics
description: Este artigo descreve como usar identidades gerenciadas para autenticar seu trabalho de Azure Stream Analytics no banco de dados SQL do Azure ou na saída do Azure Synapse Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: e491c421f4af256b2e74fa61eb442d269bdb9e34
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102487909"
---
# <a name="use-managed-identities-to-access-azure-sql-database-or-azure-synapse-analytics-from-an-azure-stream-analytics-job-preview"></a>Usar identidades gerenciadas para acessar o banco de dados SQL do Azure ou o Azure Synapse Analytics de um trabalho Azure Stream Analytics (versão prévia)

Azure Stream Analytics dá suporte à [autenticação de identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) para coletores de saída do banco de dados SQL do Azure e do Azure Synapse Analytics. Identidades gerenciadas eliminam as limitações dos métodos de autenticação baseados em usuário, como a necessidade de autenticar novamente devido a alterações de senha ou expirações de token de usuário que ocorrem a cada 90 dias. Quando você remove a necessidade de autenticar manualmente, suas implantações do Stream Analytics podem ser totalmente automatizadas.

Uma identidade gerenciada é um aplicativo gerenciado registrado no Azure Active Directory que representa um determinado trabalho do Stream Analytics. O aplicativo gerenciado é usado para autenticar um recurso de destino. Este artigo mostra como habilitar a identidade gerenciada para um banco de dados SQL do Azure ou uma saída do Azure Synapse Analytics de um trabalho Stream Analytics por meio do portal do Azure.

## <a name="overview"></a>Visão geral

Este artigo mostra as etapas necessárias para conectar seu trabalho do Stream Analytics ao seu banco de dados SQL do Azure ou ao pool do SQL do Azure Synapse Analytics usando o modo de autenticação de identidade gerenciada. 

- Primeiro, você cria uma identidade gerenciada atribuída pelo sistema para seu trabalho de Stream Analytics. Essa é a identidade do seu trabalho no Azure Active Directory.  

- Adicione um administrador de Active Directory ao seu espaço de trabalho do SQL Server ou do Synapse, que habilita a autenticação do Azure AD (identidade gerenciada) para esse recurso.

- Em seguida, crie um usuário independente representando a identidade do trabalho Stream Analytics no banco de dados. Sempre que o trabalho de Stream Analytics interage com o recurso SQL DB ou Synapse SQL DB, essa é a identidade à qual ele fará referência para verificar quais permissões seu trabalho Stream Analytics tem.

- Conceda permissões ao seu trabalho de Stream Analytics para acessar o banco de dados SQL ou os pools SQL do Synapse.

- Por fim, adicione seu banco de dados SQL do Azure/análise de Synapse do Azure como saída no trabalho de Stream Analytics.

## <a name="prerequisites"></a>Pré-requisitos

#### <a name="azure-sql-database"></a>[Banco de Dados SQL do Azure](#tab/azure-sql)

Os itens a seguir são necessários para usar esse recurso:

- Um trabalho do Azure Stream Analytics.

- Um recurso do Banco de Dados SQL do Azure.

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

Os itens a seguir são necessários para usar esse recurso:

- Um trabalho do Azure Stream Analytics.

- Um pool do SQL do Azure Synapse Analytics.

- Uma conta de armazenamento do Azure que é [configurada para seu trabalho de Stream Analytics](azure-synapse-analytics-output.md).

- Observação: Stream Analytics a MSI de armazenamento de conta integrada com Synapse SQL MSI não está disponível no momento.

---

## <a name="create-a-managed-identity"></a>Criar uma identidade gerenciada

Primeiro, você cria uma identidade gerenciada para seu trabalho do Azure Stream Analytics.

1. No [portal do Azure](https://portal.azure.com), abra seu trabalho do Azure Stream Analytics.

1. No menu de navegação à esquerda, selecione **Identidade Gerenciada** localizada em **Configurar**. Em seguida, marque a caixa ao lado de **Usar a Identidade Gerenciada atribuída ao sistema** e selecione **Salvar**.

   ![Selecione a identidade gerenciada atribuída ao sistema](./media/sql-db-output-managed-identity/system-assigned-managed-identity.png)

   Uma entidade de serviço para a identidade do trabalho do Stream Analytics no Azure Active Directory. O ciclo de vida da identidade que acaba de ser criada é gerenciado pelo Azure. Quando o trabalho do Stream Analytics é excluído, a identidade associada (ou seja, a entidade de serviço) é excluída automaticamente pelo Azure.

1. Ao salvar a configuração, a OID (ID do objeto) da entidade de serviço é listada como a ID da Entidade de Segurança conforme mostrado abaixo: 

   ![ID do objeto mostrada como ID da entidade](./media/sql-db-output-managed-identity/principal-id.png)

   A entidade de serviço tem o mesmo nome que o trabalho do Stream Analytics. Por exemplo, se o nome do seu trabalho for *MyASAJob*, o nome da entidade de serviço também será *MyASAJob*.

## <a name="select-an-active-directory-admin"></a>Selecione um administrador do Active Directory

Depois de criar uma identidade gerenciada, selecione um administrador do Active Directory.

1. Navegue até o recurso de pool de SQL do banco de dados SQL do Azure ou Azure Synapse Analytics e selecione o espaço de trabalho SQL Server ou Synapse no qual o recurso está, respectivamente. Você pode encontrar o link para eles na página Visão geral do recurso ao lado de *nome do servidor* ou *nome do espaço de trabalho*.

1. Selecione **Active Directory admin** ou o **administrador do SQL Active Directory** em **configurações**, para SQL Server e Synapse espaço de trabalho, respectivamente. Em seguida, selecione **Definir administrador**.

   ![Página de administração do Active Directory](./media/sql-db-output-managed-identity/active-directory-admin-page.png)

1. Na página Administrador do Active Directory, procure um usuário ou grupo para ser um administrador do SQL Server e clique em **Selecionar**. Esse será o usuário que poderá criar o usuário de banco de **dados independente** na próxima seção.

   ![Adicione o administrador do Active Directory](./media/sql-db-output-managed-identity/add-admin.png)

   A página de administração do Active Directory mostra todos os membros e grupos do Active Directory. Não é possível selecionar usuários ou grupos acinzentados, pois eles não têm suporte como administradores de Azure Active Directory. Consulte a lista de administradores com suporte na seção **recursos e limitações do Azure Active Directory**   de [usar a autenticação Azure Active Directory para autenticação com o banco de dados SQL ou o Azure Synapse](../azure-sql/database/authentication-aad-overview.md#azure-ad-features-and-limitations).

1. Selecione **Salvar** na página **Administrador do Active Directory**. O processo para alterar o administrador leva alguns minutos.

## <a name="create-a-contained-database-user"></a>Criação de um usuário de banco de dados independente

Em seguida, você cria um usuário de banco de dados independente em seu banco de dados SQL do Azure ou Azure Synapse que é mapeado para a identidade de Azure Active Directory. O usuário de banco de dados independente não tem um logon para o banco de dados primário, mas ele mapeia para uma identidade no diretório que está associado ao banco de dados. A identidade do Azure Active Directory pode ser uma conta de usuário individual ou um grupo. Nesse caso, crie um usuário de banco de dados independente para seu trabalho do Stream Analytics. 

Para obter mais informações, consulte o seguinte artigo para saber mais sobre a integração do Azure AD: [autenticação universal com Banco de dados SQL e Azure Synapse Analytics (suporte do SSMS para MFA)](../azure-sql/database/authentication-mfa-ssms-overview.md)

1. Conecte-se ao Azure SQL ou ao banco de dados Synapse do Azure usando SQL Server Management Studio. O **Nome de usuário** é um usuário do Azure Active Directory com a permissão **ALTER ANY USER**. O administrador definido no SQL Server é um exemplo. Use **Azure Active Directory – Universal com autenticação multifator (MFA)** . 

   ![Conecte-se ao SQL Server](./media/sql-db-output-managed-identity/connect-sql-server.png)

   O nome do servidor `<SQL Server name>.database.windows.net` pode ser diferente em regiões diferentes. Por exemplo, a região da China deve usar `<SQL Server name>.database.chinacloudapi.cn`.
 
   Você pode especificar um Azure SQL ou um banco de dados Synapse do Azure específico acessando **opções > Propriedades de conexão > se conectar ao banco de dados**.  

   ![Propriedades de conexão do SQL Server](./media/sql-db-output-managed-identity/sql-server-connection-properties.png)

1. Ao se conectar pela primeira vez, você poderá encontrar a seguinte janela:

   ![Janela de nova regra de firewall](./media/sql-db-output-managed-identity/new-firewall-rule.png)

   1. Nesse caso, vá para o recurso de espaço de trabalho SQL Server/Synapse no portal do Azure. Na seção **segurança** , abra a página **firewalls e rede virtual/firewalls** . 
   1. Adicione uma nova regra com qualquer nome de regra.
   1. Use o endereço IP *De* na janela **Nova Regra de Firewall** para a *IP inicial*.
   1. Use o endereço IP *Para* na janela **Nova Regra de Firewall** para a *IP final*. 
   1. Selecione **Salvar** e tente se conectar ao SQL Server Management Studio novamente. 

1. Quando estiver conectado, crie o usuário de banco de dados independente. O comando SQL a seguir cria um usuário de banco de dados independente que tem o mesmo nome que o seu trabalho do Stream Analytics. Certifique-se de incluir os colchetes em torno do *ASA_JOB_NAME*. Use a sintaxe T-SQL a seguir e execute a consulta. 

   ```sql
   CREATE USER [ASA_JOB_NAME] FROM EXTERNAL PROVIDER; 
   ```
   
    Para verificar se você adicionou o usuário de banco de dados independente corretamente, execute o seguinte comando no SSMS no banco de dados pertinente e verifique se o *ASA_JOB_NAME* está na coluna "nome".

   ```sql
   SELECT * FROM <SQL_DB_NAME>.sys.database_principals 
   WHERE type_desc = 'EXTERNAL_USER' 
   ```

1. Para o Azure Active Directory da Microsoft verificar se o trabalho de Stream Analytics tem acesso ao Banco de Dados SQL, precisamos conceder permissão ao Azure Active Directory para se comunicar com o banco de dados. Para fazer isso, vá para a página "firewalls e rede virtual"/"firewalls" em portal do Azure novamente e habilite "permitir que serviços e recursos do Azure acessem este servidor/espaço de trabalho".

   ![Firewall e rede virtual](./media/sql-db-output-managed-identity/allow-access.png)

## <a name="grant-stream-analytics-job-permissions"></a>Conceder permissões de trabalho ao Stream Analytics

#### <a name="azure-sql-database"></a>[Banco de Dados SQL do Azure](#tab/azure-sql)

Depois de criar um usuário de banco de dados independente e receber acesso aos serviços do Azure no portal, conforme descrito na seção anterior, seu trabalho de Stream Analytics tem permissão da identidade gerenciada para **se conectar** ao recurso de banco de dados SQL do Azure por meio de identidade gerenciada. Recomendamos que você conceda as permissões **Select** e **Insert** ao trabalho de Stream Analytics, pois elas serão necessárias mais tarde no fluxo Stream Analytics. A permissão **Select** permite que o trabalho teste sua conexão com a tabela no banco de dados SQL do Azure. A permissão **Insert** permite o teste de Stream Analytics de ponta a ponta em consultas depois de configurar uma entrada e a saída do banco de dados SQL do Azure.

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

Depois de criar um usuário de banco de dados independente e receber acesso aos serviços do Azure no portal, conforme descrito na seção anterior, seu trabalho de Stream Analytics tem permissão da identidade gerenciada para **se conectar** ao recurso de banco de dados Synapse do Azure por meio de identidade gerenciada. Recomendamos que você conceda as permissões **Select**, **Insert** e **ADminister em massa de banco de dados** ao trabalho de Stream Analytics, pois elas serão necessárias mais tarde no fluxo Stream Analytics. A permissão **Select** permite que o trabalho teste sua conexão com a tabela no banco de dados Synapse do Azure. As permissões **Inserir** e **administrar operações em massa de banco de dados** permitem testar consultas de Stream Analytics de ponta a ponta depois de configurar uma entrada e a saída do banco de dados Synapse do Azure.

Para conceder a permissão **administrar operações em massa de banco de dados** , você precisará conceder todas as permissões rotuladas como **controle** sob permissão de [banco de dados implícita](/sql/t-sql/statements/grant-database-permissions-transact-sql?view=azure-sqldw-latest&preserve-view=true#remarks) para o trabalho de Stream Analytics. Você precisa dessa permissão porque o trabalho de Stream Analytics executa a instrução de **cópia** , que requer a [Administração de operações em massa de banco de dados e a inserção](/sql/t-sql/statements/copy-into-transact-sql).

---

Você pode conceder essas permissões para o trabalho do Stream Analytics usando o SQL Server Management Studio. Para obter mais informações, consulte a referência GRANT (Transact-SQL).

Para conceder permissão apenas a uma determinada tabela ou objeto no banco de dados, use a sintaxe T-SQL a seguir e execute a consulta. 

#### <a name="azure-sql-database"></a>[Banco de Dados SQL do Azure](#tab/azure-sql)

```sql
GRANT CONNECT, SELECT, INSERT ON OBJECT::TABLE_NAME TO ASA_JOB_NAME;
```

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

```sql
GRANT CONNECT, SELECT, INSERT, CONTROL, ADMINISTER DATABASE BULK OPERATIONS OBJECT::TABLE_NAME TO ASA_JOB_NAME;
```

---

Como alternativa, você pode clicar com o botão direito do mouse no Azure SQL ou no banco de dados Synapse do Azure em SQL Server Management Studio e selecionar **propriedades > permissões**. No menu permissões, você pode ver o trabalho do Stream Analytics que você adicionou anteriormente e pode conceder ou negar manualmente as permissões como desejar.

Para examinar todas as permissões que você adicionou ao usuário *ASA_JOB_NAME* , execute o seguinte comando no SSMS no banco de usuários pertinente: 

```sql
SELECT dbprin.name, dbprin.type_desc, dbperm.permission_name, dbperm.state_desc, dbperm.class_desc, object_name(dbperm.major_id) 
FROM sys.database_principals dbprin 
LEFT JOIN sys.database_permissions dbperm 
ON dbperm.grantee_principal_id = dbprin.principal_id 
WHERE dbprin.name = '<ASA_JOB_NAME>' 
```

## <a name="create-an-azure-sql-database-or-azure-synapse-output"></a>Criar um banco de dados SQL do Azure ou uma saída Synapse do Azure

#### <a name="azure-sql-database"></a>[Banco de Dados SQL do Azure](#tab/azure-sql)

Agora que sua identidade gerenciada está configurada, você está pronto para adicionar um banco de dados SQL do Azure ou uma saída do Azure Synapse ao seu trabalho de Stream Analytics.

Verifique se você criou uma tabela em seu Banco de Dados SQL com o esquema de saída adequado. O nome desta tabela é uma das propriedades necessárias que devem ser preenchidas quando você adiciona a saída do Banco de Dados SQL ao trabalho do Stream Analytics. Além disso, verifique se o trabalho tem as permissões **SELECT** e **INSERT** para testar a conexão e executar consultas do Stream Analytics. Consulte a seção [Conceder permissões de trabalho do Stream Analytics](#grant-stream-analytics-job-permissions) se você ainda não fez isso. 

1. Volte para o trabalho do Stream Analytics e navegue até a página **Saídas** em **Topologia do trabalho**. 

1. Selecione **Adicionar > Banco de Dados SQL**. Na janela Propriedades de saída do coletor de saída do Banco de Dados SQL, selecione **Identidade Gerenciada** na lista suspensa do modo de Autenticação.

1. Preencha o restante das propriedades. Para saber mais sobre como criar uma saída do Banco de Dados SQL, consulte [Criar uma saída do Banco de Dados SQL com o Stream Analytics](sql-database-output.md). Quando tiver terminado, selecione **Salvar**.

1. Depois de clicar em **salvar**, um teste de conexão para o recurso deve disparar automaticamente. Depois de concluída com êxito, você configurou com êxito seu trabalho de Stream Analytics para se conectar ao banco de dados SQL do Azure ou ao banco de dados SQL do Synapse usando o modo de autenticação de identidade gerenciada. 

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

Agora que sua identidade gerenciada e sua conta de armazenamento estão configuradas, você está pronto para adicionar um banco de dados SQL do Azure ou uma saída do Azure Synapse ao seu trabalho de Stream Analytics.

Certifique-se de ter criado uma tabela em seu banco de dados Synapse do Azure com o esquema de saída apropriado. O nome dessa tabela é uma das propriedades necessárias que devem ser preenchidas quando você adiciona a saída do Azure Synapse ao trabalho de Stream Analytics. Além disso, verifique se o trabalho tem as permissões **SELECT** e **INSERT** para testar a conexão e executar consultas do Stream Analytics. Consulte a seção [Conceder permissões de trabalho do Stream Analytics](#grant-stream-analytics-job-permissions) se você ainda não fez isso.

1. Volte para o trabalho do Stream Analytics e navegue até a página **Saídas** em **Topologia do trabalho**.

1. Selecione **adicionar > Azure Synapse Analytics**. Na janela Propriedades de saída do coletor de saída do Banco de Dados SQL, selecione **Identidade Gerenciada** na lista suspensa do modo de Autenticação.

1. Preencha o restante das propriedades. Para saber mais sobre como criar uma saída do Azure Synapse, consulte [saída do Azure Synapse Analytics de Azure Stream Analytics](azure-synapse-analytics-output.md). Quando tiver terminado, selecione **Salvar**.

1. Depois de clicar em **salvar**, um teste de conexão para o recurso deve disparar automaticamente. Depois que ele for concluído com êxito, agora você estará pronto para continuar usando a identidade gerenciada para o recurso de análise de Synapse do Azure com o Stream Analytics. 

---

## <a name="remove-managed-identity"></a>Remover identidade gerenciada

A identidade gerenciada criada para um trabalho de Stream Analytics é excluída somente quando o trabalho é excluído. Não é possível excluir a identidade gerenciada sem excluir o trabalho. Se você não quiser mais usar a identidade gerenciada, poderá alterar o método de autenticação para a saída. A identidade gerenciada continuará existindo até que o trabalho seja excluído e será usado se você decidir usar a autenticação de identidade gerenciada novamente.

## <a name="next-steps"></a>Próximas etapas

* [Entender as saídas do Azure Stream Analytics](stream-analytics-define-outputs.md)
* [Saída do Azure Stream Analytics para o Banco de Dados SQL do Azure](stream-analytics-sql-output-perf.md)
* [Saída do Azure Synapse Analytics de Azure Stream Analytics](azure-synapse-analytics-output.md)
