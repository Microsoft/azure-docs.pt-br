---
title: PowerShell - Girar o protetor de TDE - Banco de Dados SQL do Azure| Microsoft Docs
description: Saiba como girar o protetor de TDE (Transparent Data Encryption) para um servidor SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: jhubbard
ms.date: 03/12/2019
ms.openlocfilehash: c3e982c0fc46ea72692d5fa7f27e14b88c6383df
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274262"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector-using-powershell"></a>Girar o protetor de TDE (Transparent Data Encryption) usando PowerShell

Este artigo descreve a rotação de chave para um servidor SQL do Azure usando um protetor de TDE do Azure Key Vault. Girar um protetor de TDE do servidor SQL do Azure significa alternar para uma nova chave assimétrica que protege os bancos de dados no servidor. A rotação de chave é uma operação online e deve demorar apenas alguns segundos para ser concluída, porque isso somente descriptografa e criptografa novamente a chave de criptografia de dados do banco de dados, não o banco de dados inteiro.

Este guia aborda duas opções para girar o protetor de TDE no servidor.

> [!NOTE]
> Um SQL Data Warehouse pausado deve ser retomado antes das rotações de chave.
>

> [!IMPORTANT]
> **Não Exclua** as versões anteriores da chave após uma substituição.  Quando as teclas são substituídas, alguns dados ainda são criptografados com as chaves anteriores, como backups de banco de dados mais antigos. 
>

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo do PowerShell do Azure Resource Manager ainda é compatível com o banco de dados SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para esses cmdlets, consulte [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

- Este guia de instruções assume que você já esteja usando uma chave do Azure Key Vault como protetor de TDE para um Data Warehouse ou Banco de Dados SQL do Azure. Consulte [Transparent Data Encryption com suporte de BYOK](transparent-data-encryption-byok-azure-sql.md).
- Você deve ter o Azure PowerShell instalado e em execução.
- [Recomendado, mas opcional] Crie o material da chave para o protetor de TDE em um HSM (Módulo de Segurança de Hardware) ou o armazenamento de chaves local primeiro e importe o material da chave para o Azure Key Vault. Siga as [instruções para usar um HSM (Módulo de Segurança de Hardware) e Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started) para saber mais.

## <a name="manual-key-rotation"></a>Rotação manual de chaves

Rotação manual de chaves usa o [adicionar AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [AzSqlServerKeyVaultKey adicionar](/powershell/module/az.sql/add-azsqlserverkeyvaultkey), e [conjunto AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlets para adicionar um chave totalmente nova, que pode estar sob um novo nome de chave ou até mesmo outro Cofre de chaves. Usar essa abordagem dá suporte à adição da mesma chave a diferentes cofres de chaves para dar suporte a cenários de alta disponibilidade e recuperação de desastres geográficos.

>[!NOTE]
>O comprimento combinado para o nome do cofre de chaves e o nome da chave não pode exceder 94 caracteres.

   ```powershell
   # Add a new key to Key Vault
   Add-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -Destination <HardwareOrSoftware>

   # Add the new key from Key Vault to the server
   Add-AzSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
  
   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```


## <a name="other-useful-powershell-cmdlets"></a>Outros cmdlets do PowerShell úteis

- Para alternar o protetor de TDE do gerenciada pela Microsoft para o modo BYOK, use o [AzSqlServerTransparentDataEncryptionProtector conjunto](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Para alternar o protetor de TDE do modo BYOK para gerenciada pela Microsoft, use o [AzSqlServerTransparentDataEncryptionProtector conjunto](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type ServiceManaged `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName> 
   ``` 

## <a name="next-steps"></a>Próximas etapas

- Em caso de risco de segurança, saiba como remover um protetor de TDE potencialmente comprometido: [Remover uma chave potencialmente comprometida](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md) 

- Introdução à integração do Azure Key Vault e suporte de Bring Your Own Key para a TDE: [Ativar o TDE usando sua própria chave do Key Vault usando o PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)
