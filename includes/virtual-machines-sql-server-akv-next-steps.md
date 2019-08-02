---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 22f16a7382cb0fe1f3fe2a6ef5e7c00a6989623c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171872"
---
## <a name="next-steps"></a>Próximas etapas

Depois de habilitar a integração do Cofre da Chave do Azure, você poderá habilitar a criptografia do SQL Server em sua VM do SQL. Primeiro, você precisará criar uma chave assimétrica dentro de seu cofre de chave e uma chave simétrica no SQL Server em sua VM. Em seguida, você poderá executar instruções T-SQL para habilitar a criptografia para seus bancos de dados e backups.

Há várias formas de criptografia das quais você pode tirar proveito:

* [Transparent Data Encryption (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)
* [Backups criptografados](https://msdn.microsoft.com/library/dn449489.aspx)
* [Criptografia de nível de coluna (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)

Os scripts Transact-SQL a seguir fornecem exemplos para cada uma dessas áreas.

### <a name="prerequisites-for-examples"></a>Pré-requisitos para exemplos

Cada exemplo tem base em dois pré-requisitos: uma chave assimétrica de seu cofre de chaves chamado **CONTOSO_KEY** e uma credencial criada pelo recurso de Integração de AKV chamada **Azure_EKM_TDE_cred**. Os comandos Transact-SQL a seguir configuram os pré-requisitos para executar os exemplos.

``` sql
USE master;
GO

--create credential
--The <<SECRET>> here requires the <Application ID> (without hyphens) and <Secret> to be passed together without a space between them.
CREATE CREDENTIAL sysadmin_ekm_cred
    WITH IDENTITY = 'keytestvault', --keyvault
    SECRET = '<<SECRET>>'
FOR CRYPTOGRAPHIC PROVIDER AzureKeyVault_EKM_Prov;


--Map the credential to a SQL login that has sysadmin permissions. This allows the SQL login to access the key vault when creating the asymmetric key in the next step.
ALTER LOGIN [SQL_Login]
ADD CREDENTIAL sysadmin_ekm_cred;


CREATE ASYMMETRIC KEY CONTOSO_KEY
FROM PROVIDER [AzureKeyVault_EKM_Prov]
WITH PROVIDER_KEY_NAME = 'KeyName_in_KeyVault',  --The key name here requires the key we created in the key vault
CREATION_DISPOSITION = OPEN_EXISTING;
```

### <a name="transparent-data-encryption-tde"></a>Transparent Data Encryption (TDE)

1. Crie um logon do SQL Server que será usado pelo Mecanismo de banco de dados para TDE e adicione a credencial a ele.

   ``` sql
   USE master;
   -- Create a SQL Server login associated with the asymmetric key
   -- for the Database engine to use when it loads a database
   -- encrypted by TDE.
   CREATE LOGIN EKM_Login
   FROM ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the TDE Login to add the credential for use by the
   -- Database Engine to access the key vault
   ALTER LOGIN EKM_Login
   ADD CREDENTIAL Azure_EKM_cred;
   GO
   ```

1. Crie a chave de criptografia do banco de dados que será usada para a TDE.

   ``` sql
   USE ContosoDatabase;
   GO

   CREATE DATABASE ENCRYPTION KEY 
   WITH ALGORITHM = AES_128 
   ENCRYPTION BY SERVER ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the database to enable transparent data encryption.
   ALTER DATABASE ContosoDatabase
   SET ENCRYPTION ON;
   GO
   ```

### <a name="encrypted-backups"></a>Backups criptografados

1. Crie um logon do SQL Server que será usado pelo Mecanismo de banco de dados para criptografia de backups e adicione a credencial a ele.

   ``` sql
   USE master;
   -- Create a SQL Server login associated with the asymmetric key
   -- for the Database engine to use when it is encrypting the backup.
   CREATE LOGIN EKM_Login
   FROM ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the Encrypted Backup Login to add the credential for use by
   -- the Database Engine to access the key vault
   ALTER LOGIN EKM_Login
   ADD CREDENTIAL Azure_EKM_cred ;
   GO
   ```

1. Faça o backup do banco de dados especificando a criptografia com a chave assimétrica armazenada no cofre de chave.

   ``` sql
   USE master;
   BACKUP DATABASE [DATABASE_TO_BACKUP]
   TO DISK = N'[PATH TO BACKUP FILE]'
   WITH FORMAT, INIT, SKIP, NOREWIND, NOUNLOAD,
   ENCRYPTION(ALGORITHM = AES_256, SERVER ASYMMETRIC KEY = [CONTOSO_KEY]);
   GO
   ```

### <a name="column-level-encryption-cle"></a>Criptografia de nível de coluna (CLE)

Esse script cria uma chave simétrica protegida pela chave assimétrica no cofre de chave e usa a chave simétrica para criptografar dados no banco de dados.

``` sql
CREATE SYMMETRIC KEY DATA_ENCRYPTION_KEY
WITH ALGORITHM=AES_256
ENCRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;

DECLARE @DATA VARBINARY(MAX);

--Open the symmetric key for use in this session
OPEN SYMMETRIC KEY DATA_ENCRYPTION_KEY
DECRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;

--Encrypt syntax
SELECT @DATA = ENCRYPTBYKEY(KEY_GUID('DATA_ENCRYPTION_KEY'), CONVERT(VARBINARY,'Plain text data to encrypt'));

-- Decrypt syntax
SELECT CONVERT(VARCHAR, DECRYPTBYKEY(@DATA));

--Close the symmetric key
CLOSE SYMMETRIC KEY DATA_ENCRYPTION_KEY;
```

## <a name="additional-resources"></a>Recursos adicionais

Para saber mais sobre como usar esses recursos de criptografia, consulte [Usando EKM com recursos de criptografia do SQL Server](https://msdn.microsoft.com/library/dn198405.aspx#UsesOfEKM).

Observe que as etapas neste artigo presumem que o SQL Server já está em execução em uma máquina virtual do Azure. Se não estiver, confira [Provisionar uma máquina virtual do SQL Server no Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md). Para obter orientação sobre como executar o SQL Server em VMs do Azure, consulte [Visão geral sobre SQL Server em máquinas virtuais do Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md).
