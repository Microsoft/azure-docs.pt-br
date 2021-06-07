---
title: Código do arquivo de evento XEvent
description: Fornece PowerShell e Transact-SQL para um exemplo de código de duas fases que demonstra o destino de Arquivo de evento em um evento estendido no Banco de Dados SQL do Azure. O Armazenamento do Azure é uma parte obrigatória deste cenário.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 06/06/2020
ms.openlocfilehash: d7a57f98551cf91ed87858caba0907471bcf6b12
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96501219"
---
# <a name="event-file-target-code-for-extended-events-in-azure-sql-database"></a>Código de destino do arquivo de evento para eventos estendidos no Banco de Dados SQL do Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[!INCLUDE [sql-database-xevents-selectors-1-include](../../../includes/sql-database-xevents-selectors-1-include.md)]

Você deseja um exemplo de código completo para uma maneira robusta de capturar e relatar informações para um evento estendido.

No Microsoft SQL Server, o [destino de Arquivo de Evento](/previous-versions/sql/sql-server-2016/ff878115(v=sql.130)) é usado para armazenar as saídas de eventos em um arquivo do disco rígido local. Porém, esses arquivos não estão disponíveis para o Banco de Dados SQL do Azure. Em vez disso, usamos o serviço de Armazenamento do Azure para oferecer suporte ao destino de Arquivo de evento.

Este tópico apresenta um exemplo de código em duas fases:

- PowerShell, a fim de criar o contêiner de Armazenamento do Azure na nuvem.
- Transact-SQL:
  - Para atribuir o contêiner de Armazenamento do Azure a um destino de Arquivo de evento.
  - Para criar e iniciar a sessão de evento etc.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do Banco de Dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para esses cmdlets, confira [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

- Uma conta e uma assinatura do Azure. Você pode se inscrever em uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
- Qualquer banco de dados no qual você possa criar uma tabela.
  
  - Como alternativa, você pode [criar um banco de dados de demonstração do **AdventureWorksLT**](single-database-create-quickstart.md) em questão minutos.

- O SQL Server Management Studio (ssms.exe), idealmente na sua versão de atualização mensal mais recente.
  Você pode baixar o ssms.exe mais recente de:
  
  - Tópico [Baixar o SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).
  - [Um link direto para o download.](https://go.microsoft.com/fwlink/?linkid=616025)

- Você deve ter os [módulos do Azure PowerShell](https://go.microsoft.com/?linkid=9811175) instalados.

  - Os módulos fornecem comandos como: **New-AzStorageAccount**.

## <a name="phase-1-powershell-code-for-azure-storage-container"></a>Fase 1: código do PowerShell para o contêiner de Armazenamento do Azure

Esse PowerShell é a fase 1 do exemplo de código de duas fases.

O script começa com comandos para limpeza após uma possível execução anterior e é reutilizável.

1. Cole o script do PowerShell em um editor de texto simples, como o Notepad.exe, e salve o script como um arquivo com a extensão **.ps1**.
2. Inicie o ISE do PowerShell como Administrador.
3. No prompt, digite<br/>`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser`<br/>e pressione Enter.
4. No ISE do PowerShell, abra seu arquivo **.ps1** . Execute o script.
5. Primeiro, o script inicia uma nova janela para efetuar logon no Azure.

   - Se você executar novamente o script sem interromper a sessão, terá a opção conveniente de comentar o comando **Add-AzureAccount** .

![ISE do PowerShell, com o módulo do Azure instalado e pronto para executar o script.](./media/xevent-code-event-file/event-file-powershell-ise-b30.png)

### <a name="powershell-code"></a>Código do PowerShell

Este script do PowerShell pressupõe que você já instalou o módulo Az. Para saber mais, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps).

```powershell
## TODO: Before running, find all 'TODO' and make each edit!!

cls;

#--------------- 1 -----------------------

'Script assumes you have already logged your PowerShell session into Azure.
But if not, run  Connect-AzAccount (or  Connect-AzAccount), just one time.';
#Connect-AzAccount;   # Same as  Connect-AzAccount.

#-------------- 2 ------------------------

'
TODO: Edit the values assigned to these variables, especially the first few!
';

# Ensure the current date is between
# the Expiry and Start time values that you edit here.

$subscriptionName    = 'YOUR_SUBSCRIPTION_NAME';
$resourceGroupName   = 'YOUR_RESOURCE-GROUP-NAME';

$policySasExpiryTime = '2018-08-28T23:44:56Z';
$policySasStartTime  = '2017-10-01';

$storageAccountLocation = 'YOUR_STORAGE_ACCOUNT_LOCATION';
$storageAccountName     = 'YOUR_STORAGE_ACCOUNT_NAME';
$contextName            = 'YOUR_CONTEXT_NAME';
$containerName          = 'YOUR_CONTAINER_NAME';
$policySasToken         = ' ? ';

$policySasPermission = 'rwl';  # Leave this value alone, as 'rwl'.

#--------------- 3 -----------------------

# The ending display lists your Azure subscriptions.
# One should match the $subscriptionName value you assigned
#   earlier in this PowerShell script.

'Choose an existing subscription for the current PowerShell environment.';

Select-AzSubscription -Subscription $subscriptionName;

#-------------- 4 ------------------------

'
Clean up the old Azure Storage Account after any previous run,
before continuing this new run.';

if ($storageAccountName) {
    Remove-AzStorageAccount `
        -Name              $storageAccountName `
        -ResourceGroupName $resourceGroupName;
}

#--------------- 5 -----------------------

[System.DateTime]::Now.ToString();

'
Create a storage account.
This might take several minutes, will beep when ready.
  ...PLEASE WAIT...';

New-AzStorageAccount `
    -Name              $storageAccountName `
    -Location          $storageAccountLocation `
    -ResourceGroupName $resourceGroupName `
    -SkuName           'Standard_LRS';

[System.DateTime]::Now.ToString();
[System.Media.SystemSounds]::Beep.Play();

'
Get the access key for your storage account.
';

$accessKey_ForStorageAccount = `
    (Get-AzStorageAccountKey `
        -Name              $storageAccountName `
        -ResourceGroupName $resourceGroupName
        ).Value[0];

"`$accessKey_ForStorageAccount = $accessKey_ForStorageAccount";

'Azure Storage Account cmdlet completed.
Remainder of PowerShell .ps1 script continues.
';

#--------------- 6 -----------------------

# The context will be needed to create a container within the storage account.

'Create a context object from the storage account and its primary access key.
';

$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey  $accessKey_ForStorageAccount;

'Create a container within the storage account.
';

$containerObjectInStorageAccount = New-AzStorageContainer `
    -Name    $containerName `
    -Context $context;

'Create a security policy to be applied to the SAS token.
';

New-AzStorageContainerStoredAccessPolicy `
    -Container  $containerName `
    -Context    $context `
    -Policy     $policySasToken `
    -Permission $policySasPermission `
    -ExpiryTime $policySasExpiryTime `
    -StartTime  $policySasStartTime;

'
Generate a SAS token for the container.
';
try {
    $sasTokenWithPolicy = New-AzStorageContainerSASToken `
        -Name    $containerName `
        -Context $context `
        -Policy  $policySasToken;
}
catch {
    $Error[0].Exception.ToString();
}

#-------------- 7 ------------------------

'Display the values that YOU must edit into the Transact-SQL script next!:
';

"storageAccountName: $storageAccountName";
"containerName:      $containerName";
"sasTokenWithPolicy: $sasTokenWithPolicy";

'
REMINDER: sasTokenWithPolicy here might start with "?" character, which you must exclude from Transact-SQL.
';

'
(Later, return here to delete your Azure Storage account. See the preceding  Remove-AzStorageAccount -Name $storageAccountName)';

'
Now shift to the Transact-SQL portion of the two-part code sample!';

# EOFile
```

Anote os valores nomeados que o script do PowerShell imprime quando termina. Você deve editar esses valores no script Transact-SQL na fase 2.

<!--
TODO:   Consider whether the preceding PowerShell code example deserves to be updated to the latest package (AzureRM.SQL?).
2020/June/06   Adding the !NOTE below about "ADLS Gen2 storage accounts".
Related to   https://github.com/MicrosoftDocs/azure-docs/issues/56520
-->

> [!NOTE]
> No exemplo anterior de código do PowerShell, os eventos estendidos do SQL não são compatíveis com as contas de armazenamento do ADLS Gen2.

## <a name="phase-2-transact-sql-code-that-uses-azure-storage-container"></a>Fase 2: código Transact-SQL que usa o contêiner de Armazenamento do Azure

- Na fase 1 deste exemplo de código, você executou um script PowerShell para criar um contêiner de Armazenamento do Azure.
- Em seguida na fase 2, o script Transact-SQL a seguir deverá usar o contêiner.

O script começa com comandos para limpeza após uma possível execução anterior e é reutilizável.

O script PowerShell imprimiu alguns valores nomeados quando terminou. Você precisa editar o script Transact-SQL a fim de usar esses valores. Localize **TODO** no script Transact-SQL para localizar os pontos de edição.

1. Abra o SQL Server Management Studio (ssms.exe).
2. Conecte-se a seu banco de dados no Banco de Dados SQL do Azure.
3. Clique para abrir um novo painel de consulta.
4. Cole o script Transact-SQL a seguir no painel de consulta.
5. Localize todos os **TODO** no script e faça as edições apropriadas.
6. Salve e execute o script.

> [!WARNING]
> O valor da chave de SAS gerada pelo script do PowerShell anterior pode começar com um '?' (ponto de interrogação). Quando você usa a chave de SAS no script T-SQL a seguir, deve *remover os '?' iniciais*. Caso contrário, seus esforços poderão ser bloqueados pela segurança.

### <a name="transact-sql-code"></a>Transact-SQL code

```sql
---- TODO: First, run the earlier PowerShell portion of this two-part code sample.
---- TODO: Second, find every 'TODO' in this Transact-SQL file, and edit each.

---- Transact-SQL code for Event File target on Azure SQL Database.

SET NOCOUNT ON;
GO

----  Step 1.  Establish one little table, and  ---------
----  insert one row of data.

IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'gmTabEmployee')
BEGIN
    DROP TABLE gmTabEmployee;
END
GO

CREATE TABLE gmTabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO

INSERT INTO gmTabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO

------  Step 2.  Create key, and  ------------
------  Create credential (your Azure Storage container must already exist).

IF NOT EXISTS
    (SELECT * FROM sys.symmetric_keys
        WHERE symmetric_key_id = 101)
BEGIN
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '0C34C960-6621-4682-A123-C7EA08E3FC46' -- Or any newid().
END
GO

IF EXISTS
    (SELECT * FROM sys.database_scoped_credentials
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        WHERE name = 'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent')
BEGIN
    DROP DATABASE SCOPED CREDENTIAL
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent] ;
END
GO

CREATE
    DATABASE SCOPED
    CREDENTIAL
        -- use '.blob.',   and not '.queue.' or '.table.' etc.
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    WITH
        IDENTITY = 'SHARED ACCESS SIGNATURE',  -- "SAS" token.
        -- TODO: Paste in the long SasToken string here for Secret, but exclude any leading '?'.
        SECRET = 'sv=2014-02-14&sr=c&si=gmpolicysastoken&sig=EjAqjo6Nu5xMLEZEkMkLbeF7TD9v1J8DNB2t8gOKTts%3D'
    ;
GO

------  Step 3.  Create (define) an event session.  --------
------  The event session has an event with an action,
------  and a has a target.

IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'gmeventsessionname240b')
BEGIN
    DROP
        EVENT SESSION
            gmeventsessionname240b
        ON DATABASE;
END
GO

CREATE
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE

    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE 'UPDATE gmTabEmployee%'
            )
    ADD TARGET
        package0.event_file
            (
            -- TODO: Assign AzureStorageAccount name, and the associated Container name.
            -- Also, tweak the .xel file name at end, if you like.
            SET filename =
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b.xel'
            )
    WITH
        (MAX_MEMORY = 10 MB,
        MAX_DISPATCH_LATENCY = 3 SECONDS)
    ;
GO

------  Step 4.  Start the event session.  ----------------
------  Issue the SQL Update statements that will be traced.
------  Then stop the session.

------  Note: If the target fails to attach,
------  the session must be stopped and restarted.

ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = START;
GO

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
GO

ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = STOP;
GO

-------------- Step 5.  Select the results. ----------

SELECT
        *, 'CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS!' as [CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS],
        CAST(event_data AS XML) AS [event_data_XML]  -- TODO: In ssms.exe results grid, double-click this cell!
    FROM
        sys.fn_xe_file_target_read_file
            (
                -- TODO: Fill in Storage Account name, and the associated Container name.
                -- TODO: The name of the .xel file needs to be an exact match to the files in the storage account Container (You can use Storage Account explorer from the portal to find out the exact file names or you can retrieve the name using the following DMV-query: select target_data from sys.dm_xe_database_session_targets. The 3rd xml-node, "File name", contains the name of the file currently written to.)
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b',
                null, null, null
            );
GO

-------------- Step 6.  Clean up. ----------

DROP
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE;
GO

DROP DATABASE SCOPED CREDENTIAL
    -- TODO: Assign AzureStorageAccount name, and the associated Container name.
    [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    ;
GO

DROP TABLE gmTabEmployee;
GO

PRINT 'Use PowerShell Remove-AzStorageAccount to delete your Azure Storage account!';
GO
```

Se o destino não for anexado durante a execução, você deverá parar e reiniciar a sessão de eventos:

```sql
ALTER EVENT SESSION ... STATE = STOP;
GO
ALTER EVENT SESSION ... STATE = START;
GO
```

## <a name="output"></a>Saída

Após a conclusão do script Transact-SQL, clique em uma célula sob o cabeçalho da coluna **event_data_XML**. Um elemento **\<event>** é exibido mostrando uma instrução UPDATE.

Veja um elemento **\<event>** gerado durante o teste:

```xml
<event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T19:18:45.420Z">
  <data name="state">
    <value>0</value>
    <text>Normal</text>
  </data>
  <data name="line_number">
    <value>5</value>
  </data>
  <data name="offset">
    <value>148</value>
  </data>
  <data name="offset_end">
    <value>368</value>
  </data>
  <data name="statement">
    <value>UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe'</value>
  </data>
  <action name="sql_text" package="sqlserver">
    <value>

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
</value>
  </action>
</event>
```

O script Transact-SQL anterior usou a função de sistema a seguir para ler event_file:

- [sys.fn_xe_file_target_read_file (Transact-SQL)](/sql/relational-databases/system-functions/sys-fn-xe-file-target-read-file-transact-sql)

Há uma explicação das opções avançadas para a visualização de dados de eventos estendidos disponível em:

- [Exibição avançada de dados de destino de eventos estendidos](/sql/relational-databases/extended-events/advanced-viewing-of-target-data-from-extended-events-in-sql-server)

## <a name="converting-the-code-sample-to-run-on-sql-server"></a>Convertendo o exemplo de código para execução no SQL Server

Vamos supor que você queira executar o exemplo anterior de Transact-SQL no Microsoft SQL Server.

- Para manter a simplicidade, convém substituir completamente o uso do contêiner de Armazenamento do Azure por um arquivo simples, como *C:\myeventdata.xel*. O arquivo seria gravado no disco rígido local do computador que hospeda o SQL Server.
- Você não precisaria de nenhuma variante de instrução Transact-SQL para **CREATE MASTER KEY** e **CREATE CREDENTIAL**.
- Na instrução **CREATE EVENT SESSION**, em sua cláusula **ADD TARGET**, você pode substituir o valor de Http atribuído a **filename=** por uma cadeia de caracteres de caminho completo, como *C:\myfile.xel*.
  
  - Não é necessário envolver qualquer conta de Armazenamento do Azure.

## <a name="more-information"></a>Mais informações

Para saber mais sobre contas e contêineres no serviço de Armazenamento do Azure, consulte:

- [Como usar o Armazenamento de blob do .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [Nomeando e referenciando contêineres, blobs e metadados](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)
- [Trabalhando com o contêiner raiz](/rest/api/storageservices/Working-with-the-Root-Container)
- [Lição 1: Criar uma política de acesso armazenado e uma assinatura de acesso compartilhado em um contêiner do Azure](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage)
  - [Lição 2: Criar uma credencial do SQL Server usando uma Assinatura de Acesso Compartilhado](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#2---create-a-sql-server-credential-using-a-shared-access-signature)
- [Eventos estendidos para o Microsoft SQL Server](/sql/relational-databases/extended-events/extended-events)