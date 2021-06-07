---
title: Migrar um banco de dados de SQL Server para o SQL habilitado para Arc do Azure Instância Gerenciada
description: Migrar banco de dados de SQL Server para o SQL habilitado para o Azure Instância Gerenciada
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 86563b0a44bade2cedaf76af3c247821756111fe
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90933306"
---
# <a name="migrate-sql-server-to-azure-arc-enabled-sql-managed-instance"></a>Migrar: SQL Server para o SQL habilitado para Arc do Azure Instância Gerenciada

Este cenário orienta você pelas etapas para migrar um banco de dados de uma instância do SQL Server para a instância gerenciada do SQL do Azure no Azure Arc por meio de dois métodos de backup e restauração diferentes.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="use-azure-blob-storage"></a>Usar o armazenamento de BLOBs do Azure 

Use o armazenamento de BLOBs do Azure para migrar para o SQL Instância Gerenciada habilitado para Arc do Azure.

Esse método usa o armazenamento de BLOBs do Azure como um local de armazenamento temporário no qual você pode fazer backup e, em seguida, restaurar do.

### <a name="prerequisites"></a>Pré-requisitos

- [Instalar o Azure Data Studio](install-client-tools.md)
- [Instalar Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/)
- Assinatura do Azure

### <a name="step-1-provision-azure-blob-storage"></a>Etapa 1: provisionar o armazenamento de BLOBs do Azure

1. Siga as etapas descritas em [criar uma conta de armazenamento de BLOBs do Azure](../../storage/blobs/storage-blob-create-account-block-blob.md?tabs=azure-portal)
1. Iniciar Gerenciador de Armazenamento do Azure
1. [Entre no Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#sign-in-to-azure) para acessar o armazenamento de BLOBs criado na etapa anterior
1. Clique com o botão direito do mouse na conta de armazenamento de BLOBs e selecione **criar contêiner de blob** para criar um novo contêiner no qual o arquivo de backup será armazenado

### <a name="step-2-get-storage-blob-credentials"></a>Etapa 2: obter credenciais de blob de armazenamento

1. Em Gerenciador de Armazenamento do Azure, clique com o botão direito do mouse no contêiner de BLOB que acabou de ser criado e selecione **obter assinatura de acesso compartilhado**

1. Selecione **ler**, **gravar** e **listar**

1. Escolha **Criar**

   Anote o URI e a cadeia de caracteres de consulta desta tela. Eles serão necessários em etapas posteriores. Clique no botão **copiar** para salvar em um bloco de notas/OneNote, etc.

1. Feche a janela de **assinatura de acesso compartilhado** .

### <a name="step-3-backup-database-file-to-azure-blob-storage"></a>Etapa 3: backup do arquivo de banco de dados no armazenamento de BLOBs do Azure

Nesta etapa, vamos nos conectar ao SQL Server de origem e criar o arquivo de backup do banco de dados que desejamos migrar para o SQL Instância Gerenciada-Azure Arc.

1. Iniciar Azure Data Studio
1. Conecte-se à instância de SQL Server que tem o banco de dados que você deseja migrar para o SQL Instância Gerenciada-arco do Azure
1. Clique com o botão direito do mouse no banco de dados e selecione **nova consulta**
1. Prepare sua consulta no formato a seguir, substituindo os espaços reservados indicados pelo `<...>` usando as informações da assinatura de acesso compartilhado em etapas anteriores.  Depois de substituir os valores, execute a consulta.

   ```sql
   IF NOT EXISTS  
   (SELECT * FROM sys.credentials
   WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
   CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
     WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
      SECRET = '<SAS_TOKEN>';  
   ```

1. Da mesma forma, prepare o comando **backup Database** da seguinte maneira para criar um arquivo de backup para o contêiner de BLOB.  Depois de substituir os valores, execute a consulta.

   ```sql
   BACKUP DATABASE <database name> TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>'
   ```

1. Abra Gerenciador de Armazenamento do Azure e valide se o arquivo de backup criado na etapa anterior está visível no contêiner de BLOBs

### <a name="step-4-restore-the-database-from-azure-blob-storage-to-sql-managed-instance---azure-arc"></a>Etapa 4: restaurar o banco de dados do armazenamento de BLOBs do Azure para o SQL Instância Gerenciada-arco do Azure

1. De Azure Data Studio, faça logon e conecte-se ao SQL Instância Gerenciada-o Arc do Azure.
1. Expanda os bancos de dados do **sistema**, clique com o botão direito do mouse em Database **mestre** e selecione **nova consulta**.
1. Na janela Editor de consultas, prepare e execute a mesma consulta da etapa anterior para criar as credenciais.

   ```sql
   IF NOT EXISTS  
   (SELECT * FROM sys.credentials
   WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
   CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
     WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
     SECRET = '<SAS_TOKEN>';  
   ```

1. Prepare e execute o comando abaixo para verificar se o arquivo de backup está legível e intacto.

   ```console
   RESTORE FILELISTONLY FROM URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/<file name>.bak'
   ```

1. Prepare e execute o comando **Restore Database** da seguinte maneira para restaurar o arquivo de backup para um banco de dados no SQL instância gerenciada-Arc do Azure

   ```sql
   RESTORE DATABASE <database name> FROM URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/<file name>'
   WITH MOVE 'Test' to '/var/opt/mssql/data/<file name>.mdf'
   ,MOVE 'Test_log' to '/var/opt/mssql/data/<file name>.ldf'
   ,RECOVERY  
   ,REPLACE  
   ,STATS = 5;  
   GO
   ```

Saiba mais sobre o backup para URL aqui:

[Documentos de backup para URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)

[Backup para URL usando o SQL Server Management Studio (SSMS)](/sql/relational-databases/tutorial-sql-server-backup-and-restore-to-azure-blob-storage-service)

-------

## <a name="method-2-copy-the-backup-file-into-an-azure-sql-managed-instance---azure-arc-pod-using-kubectl"></a>Método 2: copiar o arquivo de backup em um Azure SQL Instância Gerenciada-pod de arco do Azure usando kubectl

Esse método mostra como fazer um arquivo de backup que você cria por meio de qualquer método e, em seguida, copiá-lo no armazenamento local no pod da instância gerenciada do SQL do Azure para que você possa fazer a restauração a partir daí, como faria em um sistema de arquivos típico no Windows ou no Linux. Nesse cenário, você usará o comando `kubectl cp` para copiar o arquivo de um local para o sistema de arquivos do pod.

### <a name="prerequisites"></a>Pré-requisitos

- Instalar e configurar o kubectl para apontar para o cluster do kubernetes onde os serviços de dados Arc do Azure são implantados
- Tenha uma ferramenta como o Azure Data Studio ou o SQL Server Management Server instalado e conectado ao SQL Server no qual você deseja criar o arquivo de backup ou ter um arquivo. bak existente já criado no sistema de arquivos local.

### <a name="step-1-backup-the-database-if-you-havent-already"></a>Etapa 1: fazer backup do banco de dados se você ainda não tiver feito isso

Faça backup do banco de dados SQL Server em seu caminho de arquivo local como qualquer backup típico do SQL Server para o disco:

 ```sql
BACKUP DATABASE Test
TO DISK = 'c:\tmp\test.bak'
WITH FORMAT, MEDIANAME = 'Test’ ;
GO
```

### <a name="step-2-copy-the-backup-file-into-the-pods-file-system"></a>Etapa 2: copiar o arquivo de backup no sistema de arquivos do pod

Localize o nome do pod onde a instância do SQL é implantada. Normalmente, ele deve ser parecido com `pod/<sqlinstancename>-0`

Obtenha a lista de todos os pods executando:

 ```console
kubectl get pods -n <namespace of data controller>
```

Exemplo:

Copie o arquivo de backup do armazenamento local para o Pod do SQL no cluster.

 ```console
kubectl cp <source file location> <pod name>:var/opt/mssql/data/<file name> -n <namespace name>

#Example:
kubectl cp C:\Backupfiles\test.bak sqlinstance1-0:var/opt/mssql/data/test.bak -n arc
```

### <a name="step-3-restore-the-database"></a>Etapa 3: restaurar o banco de dados

Preparar e executar o comando RESTOre para restaurar o arquivo de backup para a instância gerenciada do SQL do Azure-Arc do Azure

```sql
RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/<file name>.bak'
WITH MOVE '<database name>' to '/var/opt/mssql/data/<file name>.mdf'  
,MOVE '<database name>' to '/var/opt/mssql/data/<file name>_log.ldf'  
,RECOVERY  
,REPLACE  
,STATS = 5;  
GO
```

Exemplo:

```sql
RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/test.bak'
WITH MOVE 'test' to '/var/opt/mssql/data/test.mdf'  
,MOVE 'test' to '/var/opt/mssql/data/test_log.ldf'  
,RECOVERY  
,REPLACE  
,STATS = 5;  
GO
```


## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre os recursos e as funcionalidades do SQL Instância Gerenciada habilitado para o Arc do Azure](managed-instance-features.md)

[Comece criando um Controlador de Dados](create-data-controller.md)

[Já criou um controlador de dados? Criar um SQL Instância Gerenciada habilitado para Arc do Azure](create-sql-managed-instance.md)