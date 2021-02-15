---
title: Transparent Data Encryption
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Uma visão geral da Transparent Data Encryption para o banco de dados SQL do Azure, Azure SQL Instância Gerenciada e Azure Synapse Analytics. O documento abrange os benefícios e as opções de configuração que incluem Transparent Data Encryption gerenciado pelo serviço e Bring Your Own Key.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 10/12/2020
ms.openlocfilehash: 8fbbd7a2aabc9de417f1eefd2513edba3119bfc0
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791385"
---
# <a name="transparent-data-encryption-for-sql-database-sql-managed-instance-and-azure-synapse-analytics"></a>Transparent Data Encryption para o banco de dados SQL, o SQL Instância Gerenciada e o Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

A [TDE (Transparent Data Encryption)](/sql/relational-databases/security/encryption/transparent-data-encryption) ajuda a proteger o banco de dados SQL do Azure, o SQL instância gerenciada do Azure e a análise de Synapse do Azure contra a ameaça de atividades offline mal-intencionadas, criptografando os dados em repouso. Ela realiza a criptografia e a descriptografia em tempo real do banco de dados, de backups associados e de arquivos de log de transações em repouso, sem a necessidade de alterações no aplicativo. Por padrão, o TDE está habilitado para todos os bancos de dados SQL recentemente implantados e deve ser habilitado manualmente para bancos de dados do Azure SQL, Instância Gerenciada do Azure SQL. O TDE deve ser habilitado manualmente para a análise de Synapse do Azure.

A TDE realiza a criptografia e a descriptografia de E/S em tempo real dos dados no nível da página. Cada página é descriptografada quando é lida na memória e, em seguida, criptografada antes de ser gravada no disco. O TDE criptografa o armazenamento de um banco de dados inteiro usando uma chave simétrica chamada de DEK (chave de criptografia de banco de dados). Na inicialização do banco de dados, o DEK criptografado é descriptografado e usado para descriptografia e recriptografia dos arquivos de banco de dados no processo do mecanismo de banco de dados SQL Server. DEK é protegido pelo protetor de TDE. O protetor de TDE é um certificado gerenciado por serviço (criptografia de dados transparente gerenciada por serviço) ou uma chave assimétrica armazenada em [Azure Key Vault](../../key-vault/general/secure-your-key-vault.md) (Transparent Data Encryption gerenciada pelo cliente).

Para o banco de dados SQL do Azure e o Azure Synapse, o protetor de TDE é definido no nível do [servidor](logical-servers.md) e é herdado por todos os bancos de dados associados a esse servidor. Para a Instância Gerenciada do SQL do Azure, o protetor de TDE é definido no nível de instância e é herdado por todos os bancos de dados criptografados nessa instância. O termo *servidor* refere-se ao servidor e instância ao longo deste documento, a menos que indicado de forma diferente.

> [!IMPORTANT]
> Todos os bancos de dados recém-criados no banco de dados SQL são criptografados por padrão usando a Transparent Data Encryption gerenciada por serviço. Os bancos de dados SQL existentes criados antes de 2017 de maio e de SQL criados por meio da restauração, da replicação geográfica e da cópia do banco de dados não são criptografados por padrão. Os bancos de dados existentes do SQL Instância Gerenciada criados antes de fevereiro de 2019 não são criptografados por padrão. Os bancos de dados do SQL Instância Gerenciada criados por meio da restauração herdam o status de criptografia da origem.

> [!NOTE]
> TDE não pode ser usado para criptografar bancos de dados do sistema, como o banco de dados **mestre** , no banco de dados SQL do Azure e no azure SQL instância gerenciada. O banco de dados **mestre** contém os objetos necessários para executar as operações de TDE nos bancos de dados do usuário. É recomendável não armazenar nenhum dado confidencial nos bancos de dados do sistema. A [criptografia de infraestrutura](transparent-data-encryption-byok-overview.md#doubleencryption) agora está sendo distribuída, o que criptografa os bancos de dados do sistema, incluindo o mestre. 

## <a name="service-managed-transparent-data-encryption"></a>Transparent Data Encryption de serviço gerenciado

No Azure, a configuração padrão para TDE é que o DEK é protegido por um certificado de servidor interno. O certificado de servidor interno é exclusivo para cada servidor e o algoritmo de criptografia usado é o AES 256. Se um banco de dados estiver em um relacionamento de replicação geográfica, tanto os bancos de dados primários como os secundários geográficos serão protegidos pela chave de servidor pai do banco de dados primário. Se dois bancos de dados estiverem conectados ao mesmo servidor, eles também compartilharão o mesmo certificado interno. A Microsoft gira automaticamente esses certificados em conformidade com a política de segurança interna e a chave raiz é protegida por um armazenamento secreto interno da Microsoft. Os clientes podem verificar o banco de dados SQL e a conformidade do SQL Instância Gerenciada com políticas de segurança interna em relatórios de auditoria de terceiros independentes disponíveis na [central de confiabilidade da Microsoft](https://servicetrust.microsoft.com/).

A Microsoft também move e gerencia as chaves conforme necessário para replicação geográfica e restaurações.

## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Transparent Data Encryption gerenciada pelo cliente – Bring Your Own Key

O TDE gerenciado pelo cliente também é conhecido como suporte de Bring Your Own Key (BYOK) para TDE. Nesse cenário, o protetor de TDE que criptografa o DEK é uma chave assimétrica gerenciada pelo cliente, que é armazenada em um Azure Key Vault gerenciado e de Propriedade do cliente (sistema de gerenciamento de chaves externas baseado em nuvem do Azure) e nunca deixa o cofre de chaves. O protetor de TDE pode ser [gerado pelo cofre de chaves ou transferido para o cofre de chaves](../../key-vault/keys/hsm-protected-keys.md) de um dispositivo de HSM (módulo de segurança de hardware) local. O banco de dados SQL, o SQL Instância Gerenciada e o Azure Synapse precisam receber permissões para o cofre de chaves de Propriedade do cliente para descriptografar e criptografar o DEK. Se as permissões do servidor para o cofre de chaves forem revogadas, um banco de dados ficará inacessível e todos serão criptografados

Com a integração do TDE ao Azure Key Vault, os usuários podem controlar as principais tarefas de gerenciamento, incluindo rotações de chave, permissões de cofre de chaves, backups de chaves e habilitar auditoria/relatório em todos os protetores de TDE usando a funcionalidade do Azure Key Vault. O Key Vault fornece gerenciamento de chaves central, aproveita os HSMs monitorados rigidamente e permite a separação de tarefas entre o gerenciamento de chaves e dados para ajudar a atender à conformidade com as políticas de segurança.
Para saber mais sobre o BYOK para o banco de dados SQL do Azure e o Azure Synapse, consulte [Transparent Data Encryption com integração de Azure Key Vault](transparent-data-encryption-byok-overview.md).

Para começar a usar o TDE com a integração do Azure Key Vault, consulte o guia de instruções [ativar a Transparent Data Encryption usando sua própria chave de Key Vault](transparent-data-encryption-byok-configure.md).

## <a name="move-a-transparent-data-encryption-protected-database"></a>Mover um banco de dados protegido por Transparent Data Encryption

Não é necessário descriptografar bancos de dados para operações no Azure. As configurações de TDE no banco de dados de origem ou banco de dados primário são herdadas de forma transparente no destino. As operações incluídas envolvem:

- Restauração geográfica
- Restauração via autoatendimento point-in-time
- Restauração de um banco de dados excluído
- Replicação geográfica ativa
- Criação de uma cópia do banco de dados
- Restauração de arquivo de backup para a instância gerenciada do SQL do Azure

> [!IMPORTANT]
> Não há suporte para fazer backup somente cópia manual de um banco de dados criptografado pelo TDE gerenciado pelo serviço no Azure SQL Instância Gerenciada, pois o certificado usado para criptografia não está acessível. Use o recurso de restauração pontual para mover esse tipo de banco de dados para outro Instância Gerenciada SQL ou alterne para a chave gerenciada pelo cliente.

Quando você exporta um banco de dados protegido por TDE, o conteúdo exportado do banco de dados não é criptografado. Esse conteúdo exportado é armazenado em arquivos BACPAC não criptografados. Certifique-se de proteger os arquivos BACPAC adequadamente e habilite TDE após a conclusão da importação do novo banco de dados.

Por exemplo, se o arquivo BACPAC for exportado de uma instância de SQL Server, o conteúdo importado do novo banco de dados não será criptografado automaticamente. Da mesma forma, se o arquivo BACPAC for importado para uma instância do SQL Server, o novo banco de dados também não será criptografado automaticamente.

A única exceção é quando você exporta um banco de dados do para o e do banco de dados SQL. O TDE está habilitado no novo banco de dados, mas o próprio arquivo BACPAC ainda não está criptografado.

## <a name="manage-transparent-data-encryption"></a>Gerenciar a Transparent Data Encryption

# <a name="the-azure-portal"></a>[O portal do Azure](#tab/azure-portal)

Gerenciar TDE no portal do Azure.

Para configurar o TDE por meio do portal do Azure, você deve estar conectado como o proprietário do Azure, o colaborador ou o Gerenciador de segurança do SQL.

Habilite e desabilite o TDE no nível do banco de dados. Para o Azure SQL Instância Gerenciada use Transact-SQL (T-SQL) para ativar e desativar o TDE em um banco de dados. Para o banco de dados SQL do Azure e o Azure Synapse, você pode gerenciar TDE para o banco de dados no [portal do Azure](https://portal.azure.com) depois de entrar com a conta de administrador ou colaborador do Azure. Localize as configurações da TDE em seu banco de dados do usuário. Por padrão, é utilizada a Transparent Data Encryption de serviço gerenciado. Um certificado TDE é gerado automaticamente para o servidor que contém o banco de dados.

![Transparent Data Encryption de serviço gerenciado](./media/transparent-data-encryption-tde-overview/service-managed-transparent-data-encryption.png)  

Você define a chave mestra TDE, conhecida como o protetor de TDE, no nível do servidor ou da instância. Para usar o TDE com suporte do BYOK e proteger seus bancos de dados com uma chave de Key Vault, abra as configurações do TDE em seu servidor.

![Transparent Data Encryption com suporte Bring Your Own Key](./media/transparent-data-encryption-tde-overview/tde-byok-support.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Gerenciar TDE usando o PowerShell.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, confira [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

Para configurar a TDE por meio do PowerShell, você deve estar conectado como o proprietário do Azure, o Colaborador ou o Gerenciador de segurança do SQL.

### <a name="cmdlets-for-azure-sql-database-and-azure-synapse"></a>Cmdlets para o banco de dados SQL do Azure e Azure Synapse

Use os seguintes cmdlets para o banco de dados SQL do Azure e o Azure Synapse:

| Cmdlet | Descrição |
| --- | --- |
| [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |Habilita ou desabilita a Transparent Data Encryption para um banco de dados.|
| [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |Obtém o estado da Transparent Data Encryption para um banco de dados. |
| [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |Verifica o progresso da criptografia para um banco de dados. |
| [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |Adiciona uma chave de Key Vault a um servidor. |
| [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |Obter as chaves de Key Vault para um servidor  |
| [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |Define o protetor de Transparent Data Encryption para um servidor. |
| [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |Obtém o protetor de Transparent Data Encryption |
| [Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |Remove uma chave de Key Vault de um servidor. |
|  | |

> [!IMPORTANT]
> Para o Azure SQL Instância Gerenciada, use o comando T-SQL [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) para ativar e desativar o TDE em um nível de banco de dados e verifique o [exemplo de script do PowerShell](transparent-data-encryption-byok-configure.md) para gerenciar o TDE em um nível de instância.

# <a name="transact-sql"></a>[Transact-SQL](#tab/azure-TransactSQL)

Gerencie o TDE usando o Transact-SQL.

Conecte o banco de dados usando um logon que seja um administrador ou membro da função **dbmanager** no banco de dados mestre.

| Comando | Descrição |
| --- | --- |
| [ALTER DATABASE (Banco de Dados SQL do Azure)](/sql/t-sql/statements/alter-database-azure-sql-database) | SET ENCRYPTION ON/OFF criptografa ou descriptografa um banco de dados |
| [sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Retorna informações sobre o estado de criptografia de um banco de dados e as chaves de criptografia de banco de dados associadas |
| [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Retorna informações sobre o estado de criptografia de cada nó Synapse do Azure e suas chaves de criptografia de banco de dados associadas |
|  | |

Não é possível alternar o protetor TDE para uma chave de Key Vault usando Transact-SQL. Use PowerShell o portal do Azure.

# <a name="rest-api"></a>[REST API](#tab/azure-RESTAPI)

Gerencie o TDE usando a API REST.

Para configurar o TDE por meio da API REST, você deve estar conectado como o proprietário do Azure, o colaborador ou o Gerenciador de segurança do SQL.
Use o seguinte conjunto de comandos para o banco de dados SQL do Azure e o Azure Synapse:

| Comando | Descrição |
| --- | --- |
|[Criar ou atualizar o servidor](/rest/api/sql/servers/createorupdate)|Adiciona uma identidade de Azure Active Directory a um servidor. (usado para conceder acesso ao Key Vault)|
|[Criar ou atualizar a chave do servidor](/rest/api/sql/serverkeys/createorupdate)|Adiciona uma chave de Key Vault a um servidor.|
|[Excluir chave do servidor](/rest/api/sql/serverkeys/delete)|Remove uma chave de Key Vault de um servidor. |
|[Obter chaves do servidor](/rest/api/sql/serverkeys/get)|Obtém uma chave de Key Vault específica de um servidor.|
|[Listar chaves do servidor por servidor](/rest/api/sql/serverkeys/listbyserver)|Obtém as chaves de Key Vault para um servidor. |
|[Criar ou atualizar o protetor de criptografia](/rest/api/sql/encryptionprotectors/createorupdate)|Define o protetor de TDE para um servidor.|
|[Obter protetor de criptografia](/rest/api/sql/encryptionprotectors/get)|Obtém o protetor de TDE para um servidor.|
|[Listar protetores de criptografia por servidor](/rest/api/sql/encryptionprotectors/listbyserver)|Obtém os protetores de TDE para um servidor. |
|[Criar ou atualizar a configuração de Transparent Data Encryption](/rest/api/sql/transparentdataencryptions/createorupdate)|Habilita ou desabilita a TDE para um banco de dados.|
|[Obter configuração de Transparent Data Encryption](/rest/api/sql/transparentdataencryptions/get)|Obtém a configuração de TDE para um banco de dados.|
|[Lista de resultados de configuração de Transparent Data Encryption](/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Obtém o resultado de criptografia para um banco de dados.|

## <a name="see-also"></a>Consulte Também

- O SQL Server em execução em uma máquina virtual do Azure também pode usar uma chave assimétrica do Key Vault. As etapas de configuração são diferentes de usar uma chave assimétrica no Banco de Dados SQL e Instância Gerenciada do Banco de Dados SQL. Para obter mais informações, consulte [Gerenciamento de chave extensível usando Azure Key Vault (SQL Server)](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).
- Para obter uma descrição geral do TDE, consulte [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption).
- Para saber mais sobre o TDE com suporte do BYOK para o banco de dados SQL do Azure, Azure SQL Instância Gerenciada e Azure Synapse, consulte [Transparent Data Encryption com suporte a Bring your own Key](transparent-data-encryption-byok-overview.md).
- Para começar a usar o TDE com suporte a Bring Your Own Key, consulte o guia de instruções, [ativar a Transparent Data Encryption usando sua própria chave de Key Vault](transparent-data-encryption-byok-configure.md).
- Para obter mais informações sobre Key Vault, consulte [proteger o acesso a um cofre de chaves](../../key-vault/general/secure-your-key-vault.md).