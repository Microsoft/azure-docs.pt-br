---
title: Girar o protetor TDE (PowerShell & o CLI do Azure)
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: Saiba como girar o protetor de Transparent Data Encryption (TDE) para um servidor no Azure usado pelo banco de dados SQL do Azure e a análise de Synapse do Azure usando o PowerShell e o CLI do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: 37931aee24e2dbcab03bca400d58f236601c87e5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93321425"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector"></a>Girar o protetor de Transparent Data Encryption (TDE)
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


Este artigo descreve a rotação de chaves para um [servidor](logical-servers.md) usando um protetor de TDE de Azure Key Vault. Girar o protetor de TDE lógico para um servidor significa alternar para uma nova chave assimétrica que protege os bancos de dados no servidor. A rotação de chaves é uma operação online e deve levar apenas alguns segundos para ser concluída, porque isso apenas descriptografa e criptografa novamente a chave de criptografia de dados do banco de dado, não o banco de dados inteiro.

Este guia aborda duas opções para girar o protetor de TDE no servidor.

> [!NOTE]
> Um pool SQL dedicado em pausa no Azure Synapse Analytics deve ser retomado antes das rotações de chave.

> [!IMPORTANT]
> Não exclua as versões anteriores da chave após uma substituição. Quando as teclas são substituídas, alguns dados ainda são criptografados com as chaves anteriores, como backups de banco de dados mais antigos.

## <a name="prerequisites"></a>Pré-requisitos

- Este guia de instruções pressupõe que você já está usando uma chave de Azure Key Vault como o protetor de TDE para o banco de dados SQL do Azure ou a análise de Synapse do Azure. Consulte [Transparent Data Encryption com suporte de BYOK](transparent-data-encryption-byok-overview.md).
- Você deve ter Azure PowerShell instalado e em execução.
- [Recomendado, mas opcional] Crie o material da chave para o protetor de TDE em um HSM (Módulo de Segurança de Hardware) ou o armazenamento de chaves local primeiro e importe o material da chave para o Azure Key Vault. Siga as [instruções para usar um HSM (Módulo de Segurança de Hardware) e Key Vault](../../key-vault/general/overview.md) para saber mais.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para obter instruções de instalação do módulo Az, confira [Instalar o Azure PowerShell](/powershell/azure/install-az-ps). Para obter cmdlets específicos, consulte [AzureRM. SQL](/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> Ainda há suporte para o módulo do ARM (Azure Resource Manager) do PowerShell, mas todo o desenvolvimento futuro destina-se ao módulo Az.Sql. O módulo AzureRM continuará a receber as correções de bugs até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. Para saber mais sobre a compatibilidade entre eles, confira [Apresentação do novo módulo Az do Azure PowerShell](/powershell/azure/new-azureps-module-az).

# <a name="the-azure-cli"></a>[A CLI do Azure](#tab/azure-cli)

Para instalação, consulte [instalar o CLI do Azure](/cli/azure/install-azure-cli).

* * *

## <a name="manual-key-rotation"></a>Rotação manual de chaves

A rotação de teclas manual usa os seguintes comandos para adicionar uma chave totalmente nova, que pode estar sob um novo nome de chave ou até mesmo em outro cofre de chaves. Usar essa abordagem dá suporte à adição da mesma chave a diferentes cofres de chaves para dar suporte a cenários de alta disponibilidade e recuperação de desastres geográficos.

> [!NOTE]
> O comprimento combinado para o nome do cofre de chaves e o nome da chave não pode exceder 94 caracteres.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Use os cmdlets [Add-AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)e [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) .

```powershell
# add a new key to Key Vault
Add-AzKeyVaultKey -VaultName <keyVaultName> -Name <keyVaultKeyName> -Destination <hardwareOrSoftware>

# add the new key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
  
# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault -KeyId <keyVaultKeyId> `
   -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
```

# <a name="the-azure-cli"></a>[A CLI do Azure](#tab/azure-cli)

Use os comandos [AZ keyvault Key Create](/cli/azure/keyvault/key#az-keyvault-key-create), [AZ SQL Server Key Create](/cli/azure/sql/server/key#az-sql-server-key-create)e [AZ SQL Server TDE-Key Set](/cli/azure/sql/server/tde-key#az-sql-server-tde-key-set) .

```azurecli
# add a new key to Key Vault
az keyvault key create --name <keyVaultKeyName> --vault-name <keyVaultName> --protection <hsmOrSoftware>

# add the new key from Key Vault to the server
az sql server key create --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>

# set the key as the TDE protector for all resources under the server
az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
```

* * *

## <a name="switch-tde-protector-mode"></a>Alternar modo de protetor TDE

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- Para alternar o protetor de TDE do modo gerenciado pela Microsoft para o BYOK, use o cmdlet [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) .

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault `
       -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Para alternar o protetor de TDE de modo BYOK para gerenciado pela Microsoft, use o cmdlet [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) .

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type ServiceManaged `
       -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

# <a name="the-azure-cli"></a>[A CLI do Azure](#tab/azure-cli)

Os exemplos a seguir usam [AZ SQL Server TDE-Key Set](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector).

- Para alternar o protetor de TDE do modo gerenciado pela Microsoft para o BYOK,

   ```azurecli
   az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

- Para alternar o protetor de TDE do modo BYOK para o gerenciado pela Microsoft,

   ```azurecli
   az sql server tde-key set --server-key-type ServiceManaged --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

* * *

## <a name="next-steps"></a>Próximas etapas

- No caso de um risco de segurança, saiba como remover um protetor de TDE potencialmente comprometido: [remover uma chave potencialmente comprometida](transparent-data-encryption-byok-remove-tde-protector.md).

- Introdução à integração de Azure Key Vault e Bring Your Own Key suporte para TDE: [ative o TDE usando sua própria chave de Key Vault usando o PowerShell](transparent-data-encryption-byok-configure.md).