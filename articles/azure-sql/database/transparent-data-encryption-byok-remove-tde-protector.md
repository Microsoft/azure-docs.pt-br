---
title: Remover o protetor de TDE (PowerShell & o CLI do Azure)
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: Saiba como responder a um protetor de TDE potencialmente comprometido para o banco de dados SQL do Azure ou a análise de Synapse do Azure usando o TDE com suporte ao BYOK (Traga a sua própria chave).
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 02/24/2020
ms.openlocfilehash: 31298be4d50c7f562e2e2b9adbf889b165b197e5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96461865"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Remover um protetor de TDE (Transparent Data Encryption) usando PowerShell
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


Este tópico descreve como responder a uma proteção TDE potencialmente comprometida para o banco de dados SQL do Azure ou o Azure Synapse Analytics que está usando o TDE com chaves gerenciadas pelo cliente no suporte a BYOK (Azure Key Vault Bring Your Own Key). Para saber mais sobre o suporte BYOK para TDE, consulte a [página de visão geral](transparent-data-encryption-byok-overview.md).

> [!CAUTION]
> Os procedimentos descritos neste artigo devem ser feitos apenas em casos extremos ou em ambientes de teste. Examine as etapas cuidadosamente, pois excluir os protetores de TDE usados ativamente de Azure Key Vault resultará no **banco de dados ficando indisponível**.

Se uma chave já suspeita de comprometimento, de modo que um serviço ou usuário tivesse acesso não autorizado à chave, é melhor excluir a chave.

Tenha em mente que, depois que o protetor de TDE for excluído no Key Vault, em até 10 minutos, todos os bancos de dados criptografados começarão a negar todas as conexões com a mensagem de erro correspondente e alterarão seu estado para [inacessível](./transparent-data-encryption-byok-overview.md#inaccessible-tde-protector).

Este guia de instruções passa por duas abordagens dependendo do resultado desejado após uma resposta de incidente comprometida:

- Para tornar os bancos de dados no Azure SQL Database/Azure Synapse Analytics **inacessíveis**.
- Para tornar os bancos de dados no Azure SQL Database/Azure Synapse Analytics **inacessíveis**.

## <a name="prerequisites"></a>Pré-requisitos

- É necessário ter uma assinatura do Azure e ser um administrador nessa assinatura
- Você deve ter Azure PowerShell instalado e em execução.
- Este guia de instruções pressupõe que você já está usando uma chave de Azure Key Vault como o protetor de TDE para um banco de dados SQL do Azure ou Synapse do Azure. Consulte [Transparent Data Encryption com suporte de BYOK](transparent-data-encryption-byok-overview.md) para saber mais.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

 Para obter instruções de instalação do módulo Az, confira [Instalar o Azure PowerShell](/powershell/azure/install-az-ps). Para obter cmdlets específicos, consulte [AzureRM. SQL](/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell (RM) ainda tem suporte, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. O módulo AzureRM continuará a receber as correções de bugs até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. Para saber mais sobre a compatibilidade entre eles, confira [Apresentação do novo módulo Az do Azure PowerShell](/powershell/azure/new-azureps-module-az).

# <a name="the-azure-cli"></a>[A CLI do Azure](#tab/azure-cli)

Para instalação, consulte [instalar o CLI do Azure](/cli/azure/install-azure-cli).

* * *

## <a name="check-tde-protector-thumbprints"></a>Verificar as impressões digitais do protetor TDE

As etapas a seguir descrevem como verificar as impressões digitais do protetor de TDE que ainda estão em uso por VLF (arquivos de log virtuais) de um determinado banco de dados.
A impressão digital do protetor de TDE atual do banco de dados e a ID do banco de dados podem ser encontradas executando:

```sql
SELECT [database_id],
       [encryption_state],
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/
       [encryptor_thumbprint]
 FROM [sys].[dm_database_encryption_keys]
```

A consulta a seguir retorna o VLFs e o protetor de TDE respectivas impressões digitais em uso. Cada impressão digital diferente refere-se a uma chave diferente em Azure Key Vault (AKV):

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

Como alternativa, você pode usar o PowerShell ou o CLI do Azure:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

O comando **Get-AzureRmSqlServerKeyVaultKey** do PowerShell   fornece a impressão digital do protetor de TDE usado na consulta, para que você possa ver quais chaves manter e quais chaves serão excluídas em akv. Somente as chaves que não são mais usadas pelo banco de dados podem ser excluídas com segurança do Azure Key Vault.

# <a name="the-azure-cli"></a>[A CLI do Azure](#tab/azure-cli)

O comando **AZ SQL Server Key show**   fornece a impressão digital do protetor TDE usado na consulta, para que você possa ver quais chaves manter e quais chaves excluir em akv. Somente as chaves que não são mais usadas pelo banco de dados podem ser excluídas com segurança do Azure Key Vault.

* * *

## <a name="keep-encrypted-resources-accessible"></a>Manter os recursos criptografados acessíveis

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Crie uma [nova chave no Key Vault](/powershell/module/az.keyvault/add-azkeyvaultkey). Certifique-se de que essa nova chave seja criada em um cofre de chaves separado do protetor de TDE potencialmente comprometido, já que o controle de acesso é provisionado em um nível de segurança.

2. Adicione a nova chave ao servidor usando os cmdlets [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) e [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) e atualize-o como o novo protetor TDE do servidor.

   ```powershell
   # add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

   # set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> `
       -ServerName <LogicalServerName> -Type AzureKeyVault -KeyId <KeyVaultKeyId>
   ```

3. Verifique se o servidor e todas as réplicas foram atualizadas para o novo protetor TDE usando o cmdlet [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) .

   > [!NOTE]
   > Pode demorar alguns minutos para o novo protetor de TDE se propagar em todos os bancos de dados e bancos de dados secundários no servidor.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Faça um [backup da nova chave](/powershell/module/az.keyvault/backup-azkeyvaultkey) no Key Vault.

   ```powershell
   # -OutputFile parameter is optional; if removed, a file name is automatically generated.
   Backup-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName> -OutputFile <DesiredBackupFilePath>
   ```

5. Exclua a chave comprometida de Key Vault usando o cmdlet [Remove-AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey) .

   ```powershell
   Remove-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName>
   ```

6. Para restaurar uma chave para Key Vault no futuro usando o cmdlet [Restore-AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) :

   ```powershell
   Restore-AzKeyVaultKey -VaultName <KeyVaultName> -InputFile <BackupFilePath>
   ```

# <a name="the-azure-cli"></a>[A CLI do Azure](#tab/azure-cli)

Para referência de comando, consulte o [CLI do Azure keyvault](/cli/azure/keyvault/key).

1. Crie uma [nova chave no Key Vault](/cli/azure/keyvault/key#az-keyvault-key-create). Certifique-se de que essa nova chave seja criada em um cofre de chaves separado do protetor de TDE potencialmente comprometido, já que o controle de acesso é provisionado em um nível de segurança.

2. Adicione a nova chave ao servidor e atualize-a como o novo protetor de TDE do servidor.

   ```azurecli
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. Verifique se o servidor e as réplicas foram atualizados para o novo protetor de TDE.

   > [!NOTE]
   > Pode demorar alguns minutos para o novo protetor de TDE se propagar em todos os bancos de dados e bancos de dados secundários no servidor.

   ```azurecli
   az sql server tde-key show --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

4. Faça um backup da nova chave no Key Vault.

   ```azurecli
   # --file parameter is optional; if removed, a file name is automatically generated.
   az keyvault key backup --file <DesiredBackupFilePath> --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

5. Exclua a chave comprometida de Key Vault.

   ```azurecli
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. Para restaurar uma chave para Key Vault no futuro.

   ```azurecli
   az keyvault key restore --file <BackupFilePath> --vault-name <KeyVaultName>
   ```

* * *

## <a name="make-encrypted-resources-inaccessible"></a>Tornar os recursos criptografados inacessíveis

1. Remova os bancos de dados que estão sendo criptografados pela chave potencialmente comprometida.

   O backup do banco de dados e dos arquivos de log é feito automaticamente, portanto, uma recuperação pontual do banco de dados poderá ser realizada a qualquer momento (contanto que você forneça a chave). Os bancos de dados deverão ser removidos antes da exclusão de um protetor de TDE ativo para evitar uma possível perda de dados de até 10 minutos das transações mais recentes.

2. Faça backup do material da chave do protetor de TDE no Key Vault.
3. Remover a chave potencialmente comprometida do Key Vault

[!INCLUDE [sql-database-akv-permission-delay](../includes/sql-database-akv-permission-delay.md)]

## <a name="next-steps"></a>Próximas etapas

- Saiba como girar o protetor de TDE de um servidor para atender aos requisitos de segurança: [girar o protetor de Transparent Data Encryption usando o PowerShell](transparent-data-encryption-byok-key-rotation.md)
- Introdução ao suporte Bring Your Own Key para TDE: [Ativar o TDE com sua própria chave do Key Vault usando PowerShell](transparent-data-encryption-byok-configure.md)