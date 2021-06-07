---
title: 'Tutorial: Introdução ao Always Encrypted com enclaves seguros no Banco de Dados SQL do Azure'
description: Este tutorial ensina a criar um ambiente básico para o Always Encrypted com enclaves seguros no Banco de Dados SQL do Azure e a criptografar dados in-loco, bem como emitir consultas confidenciais avançadas em colunas criptografadas usando o SSMS (SQL Server Management Studio).
keywords: criptografar dados, criptografia do SQL, criptografia de banco de dados, informações confidenciais, Always Encrypted, enclaves seguros, SGX, atestado
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: tutorial
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: 809ac72977b670faff984ad39effb1c70767e141
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120939"
---
# <a name="tutorial-getting-started-with-always-encrypted-with-secure-enclaves-in-azure-sql-database"></a>Tutorial: Introdução ao Always Encrypted com enclaves seguros no Banco de Dados SQL do Azure

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Atualmente, o Always Encrypted com enclaves seguros para o Banco de Dados SQL do Azure está em **versão prévia pública**.

Este tutorial ensina você a começar a usar o [Always Encrypted com enclaves seguros](/sql/relational-databases/security/encryption/always-encrypted-enclaves) no Banco de Dados SQL do Azure. Ela mostrará a você:

> [!div class="checklist"]
> - Como criar um ambiente para testar e avaliar o Always Encrypted com enclaves seguros.
> - Como criptografar dados in-loco e emitir consultas confidenciais avançadas em colunas criptografadas usando o SSMS (SQL Server Management Studio).

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial exige o Azure PowerShell e o [SSMS](/sql/ssms/download-sql-server-management-studio-ssms).

### <a name="powershell-requirements"></a>Requisitos do PowerShell

Confira [Visão geral do Azure PowerShell](/powershell/azure) para obter informações sobre como instalar e executar o Azure PowerShell. 

Versão mínima dos módulos Az necessária para dar suporte às operações de atestado:

- Az 4.5.0
- Az.Accounts 1.9.2
- Az.Attestation 0.1.8

Execute o comando abaixo para verificar a versão instalada de todos os módulos Az:

```powershell
Get-InstalledModule
```

Se as versões não forem correspondentes ao requisito mínimo, execute o comando `Update-Module`.

A Galeria do PowerShell preteriu as versões 1.0 e 1.1 do protocolo TLS. Recomendamos usar o TLS 1.2 ou uma versão posterior. Você poderá receber os seguintes erros se estiver usando uma versão do TLS inferior a 1.2:

- `WARNING: Unable to resolve package source 'https://www.powershellgallery.com/api/v2'`
- `PackageManagement\Install-Package: No match was found for the specified search criteria and module name.`

Para continuar a interagir com a Galeria do PowerShell, execute o comando a seguir antes dos comandos Install-Module

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
```

### <a name="ssms-requirements"></a>Requisitos do SSMS

Confira [Baixar o SSMS (SQL Server Management Studio)](/sql/ssms/download-sql-server-management-studio-ssms) para obter informações sobre como baixar o SSMS.

A versão mínima necessária do SSMS é 18.8.


## <a name="step-1-create-and-configure-a-server-and-a-dc-series-database"></a>Etapa 1: criar e configurar um servidor e um banco de dados da série DC

Nesta etapa, você criará um servidor lógico do Banco de Dados SQL do Azure e um banco de dados usando a geração de hardware da série DC, necessário para Always Encrypted com enclaves seguros. Para obter mais informações, confira [Série DC](service-tiers-vcore.md#dc-series).

1. Abra um console do PowerShell e importe a versão necessária do AZ.

  ```PowerShell
  Import-Module "Az" -MinimumVersion "4.5.0"
  ```
  
2. Entre no Azure. Se necessário, [alterne para a assinatura](/powershell/azure/manage-subscriptions-azureps) que você está usando neste tutorial.

  ```PowerShell
  Connect-AzAccount
  $subscriptionId = "<your subscription ID>"
  Set-AzContext -Subscription $subscriptionId
  ```

3. Criar um grupo de recursos. 

  > [!IMPORTANT]
  > Você precisa criar seu grupo de recursos em uma região (local) que dá suporte à geração de hardware da série DC e ao Atestado do Microsoft Azure. Para obter a lista de regiões que dão suporte à série CD, confira [Disponibilidade da série DC](service-tiers-vcore.md#dc-series-1). [Aqui](https://azure.microsoft.com/global-infrastructure/services/?products=azure-attestation) está a disponibilidade regional do Atestado do Microsoft Azure.

  ```powershell
  $resourceGroupName = "<your new resource group name>"
  $location = "<Azure region supporting DC-series and Microsoft Azure Attestation>"
  New-AzResourceGroup -Name $resourceGroupName -Location $location
  ```

4. Crie um servidor lógico do SQL do Azure. Quando solicitado, insira o nome do administrador do servidor e uma senha. Lembre o nome do administrador e a senha – você precisará deles mais tarde para se conectar ao servidor.

  ```powershell
  $serverName = "<your server name>" 
  New-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName -Location $location 
  ```

5. Crie uma regra de firewall do servidor que permita o acesso por meio do intervalo de IP especificado.
  
  ```powershell
  $startIp = "<start of IP range>"
  $endIp = "<end of IP range>"
  $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
  ```

6. Atribua uma identidade do sistema gerenciada ao servidor. 

  ```PowerShell
  $server = Set-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName -AssignIdentity
  $serverObjectId = $server.Identity.PrincipalId
  ```

7. Crie um banco de dados da série DC.

  ```powershell
  $databaseName = "ContosoHR"
  $edition = "GeneralPurpose"
  $vCore = 2
  $generation = "DC"
  New-AzSqlDatabase -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -DatabaseName $databaseName `
    -Edition $edition `
    -Vcore $vCore `
    -ComputeGeneration $generation
  ```

8. Recupere e salve as informações sobre o servidor e o banco de dados. Nas seções posteriores, você precisará dessas informações, bem como do nome do administrador e da senha da etapa 4 desta seção.

  ```powershell
  Write-Host 
  Write-Host "Fully qualified server name: $($server.FullyQualifiedDomainName)" 
  Write-Host "Server Object Id: $serverObjectId"
  Write-Host "Database name: $databaseName"
  ```
  
## <a name="step-2-configure-an-attestation-provider"></a>Etapa 2: Configurar um provedor de atestado 

Nesta etapa, você vai criar e configurar um provedor de atestado no Atestado do Microsoft Azure. Isso é necessário para atestar o enclave seguro que seu banco de dados usa.

1. Copie a política de atestado abaixo e salve-a em um arquivo de texto (txt). Para obter informações sobre a política abaixo, confira [Criar e configurar um provedor de atestado](always-encrypted-enclaves-configure-attestation.md#create-and-configure-an-attestation-provider).

  ```output
  version= 1.0;
  authorizationrules 
  {
       [ type=="x-ms-sgx-is-debuggable", value==false ]
        && [ type=="x-ms-sgx-product-id", value==4639 ]
        && [ type=="x-ms-sgx-svn", value>= 0 ]
        && [ type=="x-ms-sgx-mrsigner", value=="e31c9e505f37a58de09335075fc8591254313eb20bb1a27e5443cc450b6e33e5"] 
    => permit();
  };
  ```

2. Importe a versão necessária de `Az.Attestation`.  

  ```powershell
  Import-Module "Az.Attestation" -MinimumVersion "0.1.8"
  ```
  
3. Crie um provedor de atestado. 

  ```powershell
  $attestationProviderName = "<your attestation provider name>" 
  New-AzAttestation -Name $attestationProviderName -ResourceGroupName $resourceGroupName -Location $location
  ```

4. Configure a política de atestado.
  
  ```powershell
  $policyFile = "<the pathname of the file from step 1 in this section>"
  $teeType = "SgxEnclave"
  $policyFormat = "Text"
  $policy=Get-Content -path $policyFile -Raw
  Set-AzAttestationPolicy -Name $attestationProviderName `
    -ResourceGroupName $resourceGroupName `
    -Tee $teeType `
    -Policy $policy `
    -PolicyFormat  $policyFormat
  ```

5. Permita o acesso do provedor de atestado ao servidor lógico do SQL do Azure. Nesta etapa, você usará a ID de objeto da identidade do serviço gerenciado que você atribuiu ao servidor anteriormente.

  ```powershell
  New-AzRoleAssignment -ObjectId $serverObjectId `
    -RoleDefinitionName "Attestation Reader" `
    -ResourceName $attestationProviderName `
    -ResourceType "Microsoft.Attestation/attestationProviders" `
    -ResourceGroupName $resourceGroupName  
  ```

6. Recupere a URL de atestado que aponta para uma política de atestado configurada para o enclave SGX. Salve a URL, pois você precisará dela mais tarde.

  ```powershell
  $attestationProvider = Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $resourceGroupName 
  $attestationUrl = $attestationProvider.AttestUri + “/attest/SgxEnclave”
  Write-Host
  Write-Host "Your attestation URL is: $attestationUrl"
  ```
  
  A URL de atestado terá a seguinte aparência: `https://contososqlattestation.uks.attest.azure.net/attest/SgxEnclave`

## <a name="step-3-populate-your-database"></a>Etapa 3: Preencher seu banco de dados

Nesta etapa, você criará uma tabela e a preencherá com alguns dados que posteriormente criptografará e consultará.

1. Abra o SSMS e conecte-se ao banco de dados **ContosoHR** no servidor lógico do SQL do Azure que você criou **sem** o Always Encrypted habilitado na conexão de banco de dados.
    1. Na caixa de diálogo **Conectar ao servidor**, especifique o nome totalmente qualificado do seu servidor (por exemplo, *myserver123.database.windows.net*) e insira o nome de usuário do administrador e a senha que você especificou quando criou o servidor.
    2. Clique em **Opções >>** e selecione a guia **Propriedades de Conexão**. Lembre-se de escolher o banco de dados **ContosoHR** (não o padrão, o banco de dados mestre). 
    3. Selecione a guia **Always Encrypted**.
    4. Verifique se a caixa de seleção **Habilitar o Always Encrypted (criptografia de coluna)** **não** está selecionada.

        ![Conectar-se sem o Always Encrypted](media/always-encrypted-enclaves/connect-without-always-encrypted-ssms.png)

    5. Clique em **Conectar**.

2. Crie uma nova tabela denominada **Funcionários**.

    ```sql
    CREATE SCHEMA [HR];
    GO

    CREATE TABLE [HR].[Employees]
    (
        [EmployeeID] [int] IDENTITY(1,1) NOT NULL,
        [SSN] [char](11) NOT NULL,
        [FirstName] [nvarchar](50) NOT NULL,
        [LastName] [nvarchar](50) NOT NULL,
        [Salary] [money] NOT NULL
    ) ON [PRIMARY];
    GO
    ```

3. Adicione alguns registros de funcionários à tabela **Funcionários**.

    ```sql
    INSERT INTO [HR].[Employees]
            ([SSN]
            ,[FirstName]
            ,[LastName]
            ,[Salary])
        VALUES
            ('795-73-9838'
            , N'Catherine'
            , N'Abel'
            , $31692);

    INSERT INTO [HR].[Employees]
            ([SSN]
            ,[FirstName]
            ,[LastName]
            ,[Salary])
        VALUES
            ('990-00-6818'
            , N'Kim'
            , N'Abercrombie'
            , $55415);
    ```


## <a name="step-4-provision-enclave-enabled-keys"></a>Etapa 4: Provisionar chaves habilitadas para enclave

Nesta etapa, você criará uma chave mestra de coluna e uma chave de criptografia de coluna que permitem computações de enclave.

1. Usando a instância de SSMS da etapa anterior, no **Pesquisador de Objetos**, expanda o banco de dados e navegue até **Segurança** > **Chaves Always Encrypted**.
1. Provisione uma nova chave mestra da coluna habilitada para enclave:
    1. Clique com o botão direito do mouse em **Chaves Always Encrypted** e selecione **Nova chave mestra da coluna...** .
    2. Selecione o nome da chave mestra da coluna: **CMK1**.
    3. Certifique-se de selecionar **Repositório de certificados do Windows (usuário atual ou computador local)** ou **Azure Key Vault**.
    4. Selecione **Permitir computações de enclave**.
    5. Se você selecionou o Azure Key Vault, entre no Azure e escolha seu cofre de chaves. Para obter mais informações sobre como criar um cofre de chaves para Always Encrypted, veja [Gerenciar cofres de chaves do portal do Azure](/archive/blogs/kv/manage-your-key-vaults-from-new-azure-portal).
    6. Selecione seu certificado ou chave do Azure Key Value se ela já existir, ou clique no botão **Gerar Certificado** para criar um novo.
    7. Selecione **OK**.

        ![Permitir computações de enclave](media/always-encrypted-enclaves/allow-enclave-computations.png)

1. Crie uma nova chave de criptografia de coluna habilitada para enclave:

    1. Clique com o botão direito do mouse em **Chaves Always Encrypted** e selecione **Nova chave de criptografia da coluna**.
    2. Insira um nome para a nova chave de criptografia da coluna: **CEK1**.
    3. No menu suspenso **Chave mestra da coluna**, selecione a chave mestra da coluna criada nas etapas anteriores.
    4. Selecione **OK**.

## <a name="step-5-encrypt-some-columns-in-place"></a>Etapa 5: Criptografar algumas colunas em vigor

Nesta etapa, você criptografará os dados armazenados nas colunas **SSN** e **Salário** dentro do enclave do lado do servidor e testará uma consulta SELECT nos dados.

1. Abra uma nova instância do SSMS e conecte-se ao seu banco de dados **com** o Always Encrypted habilitado para a conexão de banco de dados.
    1. Inicie uma nova instância do SSMS.
    2. Na caixa de diálogo **Conectar ao servidor**, especifique o nome totalmente qualificado do seu servidor (por exemplo, *myserver123.database.windows.net*) e insira o nome de usuário do administrador e a senha que você especificou quando criou o servidor.
    3. Clique em **Opções >>** e selecione a guia **Propriedades de Conexão**. Lembre-se de escolher o banco de dados **ContosoHR** (não o padrão, o banco de dados mestre). 
    4. Selecione a guia **Always Encrypted**.
    5. Verifique se a caixa de seleção **Habilitar o Always Encrypted (criptografia de coluna)** está marcada.
    6. Especifique a URL de atestado de enclave que você obteve seguindo as etapas na [Etapa 2: Configurar um provedor de atestado](#step-2-configure-an-attestation-provider). Confira a captura de tela abaixo.

        ![Conectar-se com o atestado](media/always-encrypted-enclaves/connect-to-server-configure-attestation.png)

    7. Selecione **Conectar**.
    8. Se precisar habilitar a parametrização para consultas do Always Encrypted, selecione **Habilitar**.



1. Usando a mesma instância do SSMS (com o Always Encrypted habilitado), abra uma nova janela de consulta e criptografe as colunas **SSN** e **Salário** executando as instruções abaixo.

    ```sql
    ALTER TABLE [HR].[Employees]
    ALTER COLUMN [SSN] [char] (11) COLLATE Latin1_General_BIN2
    ENCRYPTED WITH (COLUMN_ENCRYPTION_KEY = [CEK1], ENCRYPTION_TYPE = Randomized, ALGORITHM = 'AEAD_AES_256_CBC_HMAC_SHA_256') NOT NULL
    WITH
    (ONLINE = ON);

    ALTER TABLE [HR].[Employees]
    ALTER COLUMN [Salary] [money]
    ENCRYPTED WITH (COLUMN_ENCRYPTION_KEY = [CEK1], ENCRYPTION_TYPE = Randomized, ALGORITHM = 'AEAD_AES_256_CBC_HMAC_SHA_256') NOT NULL
    WITH
    (ONLINE = ON);

    ALTER DATABASE SCOPED CONFIGURATION CLEAR PROCEDURE_CACHE;
    ```

    > [!NOTE]
    > Observe a instrução ALTER DATABASE SCOPED CONFIGURATION CLEAR PROCEDURE_CACHE para limpar o cache do plano de consulta para o banco de dados no script acima. Depois de alterar a tabela, você precisará limpar nos planos todos os lotes e os procedimentos armazenados que acessam a tabela, a fim de atualizar as informações de criptografia de parâmetros. 

1. Para verificar se agora as colunas **SSN** e **Salário** estão criptografadas, abra uma nova janela de consulta na instância do SSMS **sem** Always Encrypted habilitado para a conexão de banco de dados e execute a instrução abaixo. A janela de consulta deve retornar valores criptografados nas colunas **SSN** e **Salário**. Se você executar a mesma consulta usando a instância do SSMS com o Always Encrypted habilitado, você deve ver os dados descriptografados.

    ```sql
    SELECT * FROM [HR].[Employees];
    ```

## <a name="step-6-run-rich-queries-against-encrypted-columns"></a>Etapa 6: Executar consultas avançadas em colunas criptografadas

Você poderá executar consultas avançadas nas colunas criptografadas. Algum processamento de consulta será executado dentro de seu enclave do lado do servidor. 

1. Na instância do SSMS **com** Always Encrypted habilitado, verifique se a parametrização de Always Encrypted também está habilitada.
    1. Selecione **Ferramentas** no menu principal do SSMS.
    2. Selecione **Opções...** .
    3. Navegue para **Execução da Consulta** > **SQL Server** > **Avançado**.
    4. A opção **Habilitar Parametrização do Always Encrypted** precisa estar marcada.
    5. Selecione **OK**.
2. Abra uma nova janela de consulta, cole a consulta abaixo e execute-a. A consulta deve retornar valores de texto sem formatação e linhas que atendem a critérios de pesquisa especificados.

    ```sql
    DECLARE @SSNPattern [char](11) = '%6818';
    DECLARE @MinSalary [money] = $1000;
    SELECT * FROM [HR].[Employees]
    WHERE SSN LIKE @SSNPattern AND [Salary] >= @MinSalary;
    ```

3. Tente a mesma consulta novamente na instância do SSMS que não tem o Always Encrypted habilitado. Uma falha ocorrerá.
 
## <a name="next-steps"></a>Próximas etapas

Depois de concluir este tutorial, você pode ir para um dos seguintes tutoriais:
- [Tutorial: Desenvolver um aplicativo .NET usando o Always Encrypted com enclaves seguros](/sql/connect/ado-net/sql/tutorial-always-encrypted-enclaves-develop-net-apps)
- [Tutorial: Desenvolver um aplicativo .NET Framework usando o Always Encrypted com enclaves seguros](/sql/relational-databases/security/tutorial-always-encrypted-enclaves-develop-net-framework-apps)
- [Tutorial: Como criar e usar índices em colunas habilitadas para enclave com criptografia aleatória](/sql/relational-databases/security/tutorial-creating-using-indexes-on-enclave-enabled-columns-using-randomized-encryption)

## <a name="see-also"></a>Consulte Também

- [Configurar e usar o Always Encrypted com enclaves seguros](/sql/relational-databases/security/encryption/configure-always-encrypted-enclaves)
