---
title: Habilitar TDE SQL com Azure Key Vault
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Saiba como configurar um banco de dados SQL do Azure e o Azure Synapse Analytics para começar a usar o Transparent Data Encryption (TDE) para criptografia em repouso usando o PowerShell ou o CLI do Azure.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: 610f0a9692c18afbd7bb446959b09bac14d6f629
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102507612"
---
# <a name="powershell-and-the-azure-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell e o CLI do Azure: habilitar Transparent Data Encryption com chave gerenciada pelo cliente de Azure Key Vault
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Este artigo explica como usar uma chave de Azure Key Vault para Transparent Data Encryption (TDE) no banco de dados SQL do Azure ou no Azure Synapse Analytics. Para saber mais sobre a integração da TDE com o Azure Key Vault - Suporte a Bring Your Own Key (BYOK), visite [TDE com chaves gerenciadas pelo cliente no Azure Key Vault](transparent-data-encryption-byok-overview.md).

> [!NOTE] 
> O SQL do Azure agora dá suporte ao uso de uma chave RSA armazenada em um HSM gerenciado como um protetor de TDE. Este recurso está em **Visualização pública**. Azure Key Vault HSM gerenciado é um serviço de nuvem compatível com os padrões de um único locatário, altamente disponível e totalmente gerenciado que permite proteger chaves criptográficas para seus aplicativos de nuvem, usando HSMs validados pelo FIPS 140-2 nível 3. Saiba mais sobre [HSMs gerenciados](../../key-vault/managed-hsm/index.yml).

## <a name="prerequisites-for-powershell"></a>Pré-requisitos para PowerShell

- É necessário ter uma assinatura do Azure e ser um administrador nessa assinatura.
- [Recomendado, mas opcional] Tenha um HSM (Módulo de Segurança de Hardware) ou armazenamento de chave local para criar uma cópia local do material da chave do Protetor de TDE.
- Você deve ter Azure PowerShell instalado e em execução.
- Crie um Azure Key Vault e Chave para usar na TDE.
  - [Instruções para usar um HSM (Módulo de Segurança de Hardware) e Key Vault](../../key-vault/keys/hsm-protected-keys.md)
    - O cofre de chaves deve ter a seguinte propriedade a ser usada para TDE:
  - [exclusão reversível](../../key-vault/general/soft-delete-overview.md) e limpeza da proteção
- A chave deve ter os seguintes atributos a serem usados para TDE:
  - Sem data de validade
  - Não desabilitado
  - Capaz de realizar operações *get*, *codificar chave*, *decodificar chave*
- **(Em versão prévia)** Para usar uma chave HSM gerenciada, siga as instruções para [criar e ativar um HSM gerenciado usando o CLI do Azure](../../key-vault/managed-hsm/quick-create-cli.md)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para obter instruções de instalação do módulo Az, confira [Instalar o Azure PowerShell](/powershell/azure/install-az-ps). Para obter cmdlets específicos, consulte [AzureRM. SQL](/powershell/module/AzureRM.Sql/).

Para obter informações específicas sobre Key Vault, consulte [instruções do PowerShell do Key Vault](../../key-vault/secrets/quick-create-powershell.md) e [como usar Key Vault exclusão reversível com o PowerShell](../../key-vault/general/key-vault-recovery.md).

> [!IMPORTANT]
> Ainda há suporte para o módulo do ARM (Azure Resource Manager) do PowerShell, mas todo o desenvolvimento futuro destina-se ao módulo Az.Sql. O módulo AzureRM continuará a receber as correções de bugs até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. Para saber mais sobre a compatibilidade entre eles, confira [Apresentação do novo módulo Az do Azure PowerShell](/powershell/azure/new-azureps-module-az).

## <a name="assign-an-azure-active-directory-azure-ad-identity-to-your-server"></a>Atribuir uma identidade do Azure Active Directory (Azure AD) ao seu servidor

Se você tiver um [servidor](logical-servers.md)existente, use o seguinte para adicionar uma identidade do Azure Active Directory (Azure AD) ao seu servidor:

   ```powershell
   $server = Set-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -AssignIdentity
   ```

Se você estiver criando um servidor, use o cmdlet [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) com a marca-Identity para adicionar uma identidade do Azure ad durante a criação do servidor:

   ```powershell
   $server = New-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -Location <RegionName> `
       -ServerName <LogicalServerName> -ServerVersion "12.0" -SqlAdministratorCredentials <PSCredential> -AssignIdentity
   ```

## <a name="grant-key-vault-permissions-to-your-server"></a>Conceder permissões do Key Vault ao servidor

Use o cmdlet [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) para conceder ao servidor acesso ao cofre de chaves antes de usar uma chave a partir dele para TDE.

   ```powershell
   Set-AzKeyVaultAccessPolicy -VaultName <KeyVaultName> `
       -ObjectId $server.Identity.PrincipalId -PermissionsToKeys get, wrapKey, unwrapKey
   ```
Para adicionar permissões ao seu servidor em um HSM gerenciado, adicione a função de RBAC local ' criptografia do serviço de criptografia HSM gerenciada ' ao servidor. Isso permitirá que o servidor execute operações Get, wrap Key, sem encapsulamento de chave nas chaves no HSM gerenciado.
[Instruções para provisionar o acesso ao servidor no HSM gerenciado](../../key-vault/managed-hsm/role-management.md)

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Adicionar a chave do Key Vault ao servidor e definir o Protetor de TDE

- Use o cmdlet [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) para recuperar a ID de chave do Key Vault
- Use o cmdlet [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) para adicionar a chave do Key Vault ao servidor.
- Use o cmdlet [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) para definir a chave como o protetor de TDE para todos os recursos do servidor.
- Use o cmdlet [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) para confirmar se o protetor de TDE foi configurado como pretendido.

> [!NOTE]
> **(Em versão prévia)** Para chaves HSM gerenciadas, use AZ. SQL 2.11.1 versão do PowerShell.

> [!NOTE]
> O comprimento combinado para o nome do cofre de chaves e o nome da chave não pode exceder 94 caracteres.

> [!TIP]
> Um exemplo de KeyId do Key Vault: <br/>https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>
> Um KeyId de exemplo do HSM gerenciado:<br/>https://contosoMHSM.managedhsm.azure.net/keys/myrsakey

```powershell
# add the key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId>

# confirm the TDE protector was configured as intended
Get-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName>
```

## <a name="turn-on-tde"></a>Ativar TDE

Use o cmdlet [set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) para ativar o TDE.

```powershell
Set-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> -State "Enabled"
```

Agora, o banco de dados ou data warehouse possui a TDE habilitada com uma chave de criptografia no Key Vault.

## <a name="check-the-encryption-state-and-encryption-activity"></a>Verificar o estado de criptografia e a atividade de criptografia

Use [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) para obter o estado de criptografia e [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) para verificar o progresso da criptografia para um banco de dados ou data warehouse.

```powershell
# get the encryption state
Get-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> `

# check the encryption progress for a database or data warehouse
Get-AzSqlDatabaseTransparentDataEncryptionActivity -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName>  
```

# <a name="the-azure-cli"></a>[A CLI do Azure](#tab/azure-cli)

Para instalar a versão necessária do CLI do Azure (versão 2,0 ou posterior) e conectar-se à sua assinatura do Azure, consulte [instalar e configurar a interface de Command-Line de plataforma cruzada do azure 2,0](/cli/azure/install-azure-cli).

Para obter informações específicas sobre Key Vault, consulte [gerenciar Key Vault usando a cli 2,0](../../key-vault/general/manage-with-cli2.md) e [como usar Key Vault exclusão reversível com a CLI](../../key-vault/general/key-vault-recovery.md).

## <a name="assign-an-azure-ad-identity-to-your-server"></a>Atribuir uma identidade do Azure AD ao servidor

```azurecli
# create server (with identity) and database
az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
az sql db create --name <dbname> --server <servername> --resource-group <rgname>
```

> [!TIP]
> Impeça que a "principalID" crie o servidor; a ID de objeto usada para atribuir permissões de cofre de chaves na próxima etapa

## <a name="grant-key-vault-permissions-to-your-server"></a>Conceder permissões do Key Vault ao servidor

```azurecli
# create key vault, key and grant permission
az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
az keyvault key create --name <keyname> --vault-name <kvname> --protection software
az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get
```

> [!TIP]
> Salve o URI da chave ou a keyID da nova chave para a próxima etapa, por exemplo: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Adicionar a chave do Key Vault ao servidor e definir o Protetor de TDE

```azurecli
# add server key and update encryption protector
az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>
```

> [!NOTE]
> O comprimento combinado para o nome do cofre de chaves e o nome da chave não pode exceder 94 caracteres.

## <a name="turn-on-tde"></a>Ativar TDE

```azurecli
# enable encryption
az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled
```

Agora, o banco de dados ou data warehouse possui a TDE habilitada com uma chave de criptografia gerenciada pelo cliente no Azure Key Vault.

## <a name="check-the-encryption-state-and-encryption-activity"></a>Verificar o estado de criptografia e a atividade de criptografia

```azurecli
# get encryption scan progress
az sql db tde list-activity --database <dbname> --server <servername> --resource-group <rgname>  

# get whether encryption is on or off
az sql db tde show --database <dbname> --server <servername> --resource-group <rgname>
```

* * *

## <a name="useful-powershell-cmdlets"></a>Cmdlets úteis do PowerShell

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- Use o cmdlet [set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) para desligar o TDE.

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName> `
      -DatabaseName <DatabaseName> -State "Disabled"
   ```

- Use o cmdlet [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) para retornar a lista de chaves de Key Vault adicionadas ao servidor.

   ```powershell
   # KeyId is an optional parameter, to return a specific key version
   Get-AzSqlServerKeyVaultKey -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

- Use o [Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) para remover uma chave de Key Vault do servidor.

   ```powershell
   # the key set as the TDE Protector cannot be removed
   Remove-AzSqlServerKeyVaultKey -KeyId <KeyVaultKeyId> -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

# <a name="the-azure-cli"></a>[A CLI do Azure](#tab/azure-cli)

- Para configurações gerais de banco de dados, consulte [AZ SQL](/cli/azure/sql).

- Para configurações de chave do cofre, consulte [AZ SQL Server Key](/cli/azure/sql/server/key).

- Para configurações de TDE, consulte [AZ SQL Server TDE-Key](/cli/azure/sql/server/tde-key) e [AZ SQL DB TDE](/cli/azure/sql/db/tde).

* * *

## <a name="troubleshooting"></a>Solução de problemas

Se um problema ocorrer, verifique o seguinte:

- Se o cofre de chaves não puder ser encontrado, verifique se você está na assinatura correta.

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell
   Get-AzSubscription -SubscriptionId <SubscriptionId>
   ```

   # <a name="the-azure-cli"></a>[A CLI do Azure](#tab/azure-cli)

   ```powershell
   az account show - s <SubscriptionId>
   ```

   * * *

- Se a nova chave não puder ser adicionada ao servidor, ou a nova chave não puder ser atualizada como o protetor de TDE, verifique o seguinte:
   - A chave não deve ter uma data de validade
   - A chave deve ter as operações *get*, *codificar chave* e *decodificar chave* habilitadas.

## <a name="next-steps"></a>Próximas etapas

- Saiba como girar o protetor de TDE de um servidor para atender aos requisitos de segurança: [Girar o Protetor de TDE usando PowerShell](transparent-data-encryption-byok-key-rotation.md).
- Em caso de risco de segurança, saiba como remover um Protetor de TDE potencialmente comprometido: [Remover uma chave potencialmente comprometida](transparent-data-encryption-byok-remove-tde-protector.md).
