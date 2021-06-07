---
title: Migrar instância gerenciada do certificado TDE
description: Migrar um certificado que protege a chave de criptografia de banco de dados de um banco de dados com Transparent Data Encryption para SQL do Azure Instância Gerenciada
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein, jovanpop
ms.date: 07/21/2020
ms.openlocfilehash: c465da3d5d812ea7e811cbe59318122700c6e786
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97824674"
---
# <a name="migrate-a-certificate-of-a-tde-protected-database-to-azure-sql-managed-instance"></a>Migrar um certificado de um banco de dados protegido por TDE para o Azure SQL Instância Gerenciada
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Quando você estiver migrando um banco de dados protegido por [Transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) para o SQL do Azure instância gerenciada usando a opção de restauração nativa, o certificado correspondente da instância SQL Server precisará ser migrado antes da restauração do banco de dados. Este artigo orienta você pelo processo de migração manual do certificado para o Azure SQL Instância Gerenciada:

> [!div class="checklist"]
>
> * Exportar o certificado para um arquivo de troca de informações pessoais (. pfx)
> * Extrair o certificado de um arquivo para uma cadeia de caracteres de base 64
> * Carregue-o usando um cmdlet do PowerShell

Para obter uma opção alternativa usando um serviço totalmente gerenciado para a migração direta de um banco de dados protegido por TDE e um certificado correspondente, consulte [como migrar seu banco de dados local para o Azure SQL instância gerenciada usando o serviço de migração de banco de dados do Azure](../../dms/tutorial-sql-server-to-managed-instance.md).

> [!IMPORTANT]
> Um certificado migrado é usado somente para restauração do banco de dados protegido por TDE. Logo após a conclusão da restauração, o certificado migrado é substituído por um protetor diferente, um certificado gerenciado por serviço ou uma chave assimétrica do cofre de chaves, dependendo do tipo de TDE que você definiu na instância.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisa dos seguintes pré-requisitos:

* Ferramenta de linha de comando [Pvk2Pfx](/windows-hardware/drivers/devtest/pvk2pfx) instalada no servidor local ou em outro computador com acesso ao certificado exportado como arquivo. A ferramenta Pvk2Pfx faz parte do [Kit de driver do Windows Enterprise](/windows-hardware/drivers/download-the-wdk), um ambiente de linha de comando independente.
* [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell) versão 5.0 ou posterior instalada.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verifique se você tem o seguinte:

* Azure PowerShell módulo [instalado e atualizado](/powershell/azure/install-az-ps).
* [Módulo AZ. SQL](https://www.powershellgallery.com/packages/Az.Sql).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda é compatível com o SQL Azure Instância Gerenciada, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, confira [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRM são substancialmente idênticos.

Execute os seguintes comandos no PowerShell para instalar/atualizar o módulo:

```azurepowershell
Install-Module -Name Az.Sql
Update-Module -Name Az.Sql
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

* * *

## <a name="export-the-tde-certificate-to-a-pfx-file"></a>Exportar o certificado TDE para um arquivo. pfx

O certificado pode ser exportado diretamente da instância de SQL Server de origem ou do repositório de certificados se ele estiver sendo mantido lá.

### <a name="export-the-certificate-from-the-source-sql-server-instance"></a>Exportar o certificado da instância de SQL Server de origem

Use as etapas a seguir para exportar o certificado com SQL Server Management Studio e convertê-lo no formato. pfx. Os nomes genéricos *TDE_Cert* e *full_path* estão sendo usados para o certificado e os nomes de arquivo e caminhos por meio das etapas. Eles devem ser substituídos por nomes reais.

1. No SSMS, abra uma nova janela de consulta e conecte-se à instância de SQL Server de origem.

1. Use o script a seguir para listar bancos de dados protegidos por TDE e obter o nome do certificado que protege a criptografia do banco de dados a ser migrado:

   ```sql
   USE master
   GO
   SELECT db.name as [database_name], cer.name as [certificate_name]
   FROM sys.dm_database_encryption_keys dek
   LEFT JOIN sys.certificates cer
   ON dek.encryptor_thumbprint = cer.thumbprint
   INNER JOIN sys.databases db
   ON dek.database_id = db.database_id
   WHERE dek.encryption_state = 3
   ```

   ![Lista de certificados TDE](./media/tde-certificate-migrate/onprem-certificate-list.png)

1. Execute o script a seguir para exportar o certificado para um par de arquivos (.cer e .pvk), mantendo as informações de chave pública e privada:

   ```sql
   USE master
   GO
   BACKUP CERTIFICATE TDE_Cert
   TO FILE = 'c:\full_path\TDE_Cert.cer'
   WITH PRIVATE KEY (
     FILE = 'c:\full_path\TDE_Cert.pvk',
     ENCRYPTION BY PASSWORD = '<SomeStrongPassword>'
   )
   ```

   ![Certificado TDE de backup](./media/tde-certificate-migrate/backup-onprem-certificate.png)

1. Use o console do PowerShell para copiar informações de certificado de um par de arquivos recém-criados para um arquivo. pfx, usando a ferramenta Pvk2Pfx:

   ```cmd
   .\pvk2pfx -pvk c:/full_path/TDE_Cert.pvk  -pi "<SomeStrongPassword>" -spc c:/full_path/TDE_Cert.cer -pfx c:/full_path/TDE_Cert.pfx
   ```

### <a name="export-the-certificate-from-a-certificate-store"></a>Exportar o certificado de um repositório de certificados

Se o certificado for mantido no repositório de certificados do computador local SQL Server, ele poderá ser exportado usando as seguintes etapas:

1. Abra o console do PowerShell e execute o seguinte comando para abrir o snap-in de certificados do console de gerenciamento Microsoft:

   ```cmd
   certlm
   ```

2. No snap-in de certificados do MMC, expanda o caminho Personal > certificados para ver a lista de certificados.

3. Clique com o botão direito do mouse no certificado e clique em **Exportar**.

4. Siga o assistente para exportar o certificado e a chave privada para um formato. pfx.

## <a name="upload-the-certificate-to-azure-sql-managed-instance-using-an-azure-powershell-cmdlet"></a>Carregar o certificado no Azure SQL Instância Gerenciada usando um cmdlet Azure PowerShell

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Comece com as etapas de preparação no PowerShell:

   ```azurepowershell
   # import the module into the PowerShell session
   Import-Module Az
   # connect to Azure with an interactive dialog for sign-in
   Connect-AzAccount
   # list subscriptions available and copy id of the subscription target the managed instance belongs to
   Get-AzSubscription
   # set subscription for the session
   Select-AzSubscription <subscriptionId>
   ```

2. Depois que todas as etapas de preparação forem concluídas, execute os seguintes comandos para carregar o certificado codificado em base 64 na instância gerenciada de destino:

   ```azurepowershell
   $fileContentBytes = Get-Content 'C:/full_path/TDE_Cert.pfx' -AsByteStream
   $base64EncodedCert = [System.Convert]::ToBase64String($fileContentBytes)
   $securePrivateBlob = $base64EncodedCert  | ConvertTo-SecureString -AsPlainText -Force
   $password = "<password>"
   $securePassword = $password | ConvertTo-SecureString -AsPlainText -Force
   Add-AzSqlManagedInstanceTransparentDataEncryptionCertificate -ResourceGroupName "<resourceGroupName>" `
       -ManagedInstanceName "<managedInstanceName>" -PrivateBlob $securePrivateBlob -Password $securePassword
   ```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Primeiro, você precisa [configurar um Azure Key Vault](../../key-vault/general/manage-with-cli2.md) com seu arquivo *. pfx* .

1. Comece com as etapas de preparação no PowerShell:

   ```azurecli
   # connect to Azure with an interactive dialog for sign-in
   az login

   # list subscriptions available and copy id of the subscription target the managed instance belongs to
   az account list

   # set subscription for the session
   az account set --subscription <subscriptionId>
   ```

1. Depois que todas as etapas de preparação forem concluídas, execute os seguintes comandos para carregar o certificado codificado em base 64 para a instância gerenciada de destino:

   ```azurecli
   az sql mi tde-key set --server-key-type AzureKeyVault --kid "<keyVaultId>" `
       --managed-instance "<managedInstanceName>" --resource-group "<resourceGroupName>"
   ```

* * *

O certificado agora está disponível para a instância gerenciada especificada e o backup do banco de dados protegido por TDE pode ser restaurado com êxito.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a migrar um certificado que protege a chave de criptografia de um banco de dados com Transparent Data Encryption, da instância local ou IaaS SQL Server para Instância Gerenciada do SQL do Azure.

Consulte [restaurar um backup de banco de dados para um Azure sql instância gerenciada](restore-sample-database-quickstart.md) para saber como restaurar um backup de banco de dados para o SQL do Azure instância gerenciada.