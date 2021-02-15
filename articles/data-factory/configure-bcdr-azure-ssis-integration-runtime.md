---
title: Configurar o Azure-SSIS Integration Runtime para failover do Banco de Dados SQL
description: Este artigo descreve como configurar o Azure-SSIS Integration Runtime com a replicação geográfica e o failover do Banco de Dados SQL do Azure para o banco de dados SSISDB
ms.service: data-factory
ms.devlang: powershell
author: swinarko
ms.author: sawinark
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/06/2020
ms.openlocfilehash: e12939d1003ce708889ca0b3dbc710096f9ee955
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364433"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-sql-database-geo-replication-and-failover"></a>Configurar o Azure-SSIS Integration Runtime com a replicação geográfica e o failover do Banco de Dados SQL

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve como configurar o Azure-SSIS IR (Integration Runtime) com a replicação geográfica do Banco de Dados SQL do Azure para o banco de dados SSISDB. Quando ocorre um failover, você pode garantir que o IR do Azure-SSIS continue trabalhando com o banco de dados secundário.

Para obter mais informações sobre a replicação geográfica e o failover do Banco de Dados SQL, confira [Visão geral: replicação geográfica ativa e grupos de failover automático](../azure-sql/database/auto-failover-group-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-ssis-ir-failover-with-a-sql-managed-instance"></a>Failover de Azure-SSIS IR com um SQL Instância Gerenciada

### <a name="prerequisites"></a>Pré-requisitos

Um Instância Gerenciada do SQL do Azure usa uma *DMK (chave mestra de banco de dados)* para ajudar a proteger dados, credenciais e informações de conexão armazenadas em um banco de dado. Para habilitar a descriptografia automática da DMK, uma cópia da chave é criptografada por meio da *SMK (chave mestra do servidor)* . 

A SMK não é replicada em um grupo de failover. Você precisa adicionar uma senha nas instâncias primária e secundária para a descriptografia da DMK após o failover.

1. Execute o comando a seguir para o SSISDB na instância primária. Esta etapa adiciona uma nova senha de criptografia.

   ```sql
   ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'password'
   ```

2. Crie um grupo de failover em um Instância Gerenciada do SQL.

3. Execute **sp_control_dbmasterkey_password** na instância secundária usando a nova senha de criptografia.

   ```sql
   EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB', @password = N'<password>', @action = N'add';  
   GO
   ```

### <a name="scenario-1-azure-ssis-ir-is-pointing-to-a-readwrite-listener-endpoint"></a>Cenário 1: o Azure-SSIS IR está apontando para um ponto de extremidade do ouvinte de leitura/gravação

Se você quiser que o Azure-SSIS IR aponte para um ponto de extremidade do ouvinte de leitura/gravação, primeiro, precisará apontá-lo para o ponto de extremidade do servidor primário. Depois de colocar o SSISDB em um grupo de failover, você pode interromper o Azure-SSIS IR, alterá-lo para apontar para o ponto de extremidade do ouvinte de leitura/gravação usando Azure PowerShell e reiniciá-lo.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -CatalogServerEndpoint "Azure SQL Managed Instance read/write listener endpoint"
```

#### <a name="solution"></a>Solução

Quando o failover ocorrer, execute as seguintes etapas:

1. Pare o Azure-SSIS IR na região primária.

2. Edite o Azure-SSIS IR com a nova região, a rede virtual e as informações de URI de SAS (Assinatura de Acesso Compartilhado) para a instalação personalizada na instância secundária. Como o Azure-SSIS IR está apontando para um ouvinte de leitura/gravação e o ponto de extremidade é transparente para o Azure-SSIS IR, você não precisa editar o ponto de extremidade.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
      -VNetId "new VNet" `
      -Subnet "new subnet" `
      -SetupScriptContainerSasUri "new custom setup SAS URI"
   ```

3. Reinicie o Azure-SSIS IR.

### <a name="scenario-2-azure-ssis-ir-is-pointing-to-a-primary-server-endpoint"></a>Cenário 2: o Azure-SSIS IR está apontando para o ponto de extremidade do servidor primário

Esse cenário será adequado se o Azure-SSIS IR estiver apontando para um ponto de extremidade do servidor primário.

#### <a name="solution"></a>Solução

Quando o failover ocorrer, execute as seguintes etapas:

1. Pare o Azure-SSIS IR na região primária.

2. Edite o Azure-SSIS IR com as novas informações da região, do ponto de extremidade e da rede virtual da instância secundária.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
      -CatalogServerEndpoint "Azure SQL Database endpoint" `
      -CatalogAdminCredential "Azure SQL Database admin credentials" `
      -VNetId "new VNet" `
      -Subnet "new subnet" `
      -SetupScriptContainerSasUri "new custom setup SAS URI"
   ```

3. Reinicie o Azure-SSIS IR.

### <a name="scenario-3-azure-ssis-ir-is-pointing-to-a-public-endpoint-of-a-sql-managed-instance"></a>Cenário 3: Azure-SSIS IR está apontando para um ponto de extremidade público de um Instância Gerenciada SQL

Esse cenário é adequado se o Azure-SSIS IR estiver apontando para um ponto de extremidade público de um Instância Gerenciada SQL do Azure e não ingressar em uma rede virtual. A única diferença em relação ao cenário 2 é que você não precisa editar as informações de rede virtual para o Azure-SSIS IR após o failover.

#### <a name="solution"></a>Solução

Quando o failover ocorrer, execute as seguintes etapas:

1. Pare o Azure-SSIS IR na região primária.

2. Edite o Azure-SSIS IR com as novas informações da região e do ponto de extremidade da instância secundária.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
      -CatalogServerEndpoint "Azure SQL Database server endpoint" `
      -CatalogAdminCredential "Azure SQL Database server admin credentials" `
      -SetupScriptContainerSasUri "new custom setup SAS URI"
   ```

3. Reinicie o Azure-SSIS IR.

### <a name="scenario-4-attach-an-existing-ssisdb-instance-ssis-catalog-to-a-new-azure-ssis-ir"></a>Cenário 4: anexar uma instância existente do SSISDB (catálogo do SSIS) a um novo Azure-SSIS IR

Esse cenário será adequado se você quiser que o SSISDB trabalhe com um novo Azure-SSIS IR em uma nova região quando ocorrer um desastre do Azure Data Factory ou do Azure-SSIS IR na região atual.

#### <a name="solution"></a>Solução

Quando ocorrer o failover, execute as etapas a seguir.

> [!NOTE]
> Use o PowerShell para a etapa 4 (criação do IR). Caso contrário, o portal do Azure relatará um erro informando que o SSISDB já existe.

1. Pare o Azure-SSIS IR na região primária.

2. Execute um procedimento armazenado para atualizar os metadados no SSISDB para aceitar conexões de **\<new_data_factory_name\>** e **\<new_integration_runtime_name\>** .
   
   ```sql
   EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
   ```

3. Crie um novo data factory chamado **\<new_data_factory_name\>** na nova região.

   ```powershell
   Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
      -Location "new region"`
      -Name "<new_data_factory_name>"
   ```
   
   Para obter mais informações sobre esse comando do PowerShell, confira [Criar um Azure data factory usando o PowerShell](quickstart-create-data-factory-powershell.md).

4. Crie um novo Azure-SSIS IR nomeado **\<new_integration_runtime_name\>** na nova região usando Azure PowerShell.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
      -DataFactoryName "new data factory name" `
      -Name "<new_integration_runtime_name>" `
      -Description $AzureSSISDescription `
      -Type Managed `
      -Location $AzureSSISLocation `
      -NodeSize $AzureSSISNodeSize `
      -NodeCount $AzureSSISNodeNumber `
      -Edition $AzureSSISEdition `
      -LicenseType $AzureSSISLicenseType `
      -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
      -VnetId "new vnet" `
      -Subnet "new subnet" `
      -CatalogServerEndpoint $SSISDBServerEndpoint `
      -CatalogPricingTier $SSISDBPricingTier
   ```
   
   Para obter mais informações sobre esse comando do PowerShell, confira [Criar o Azure-SSIS Integration Runtime no Azure Data Factory](create-azure-ssis-integration-runtime.md).

## <a name="azure-ssis-ir-failover-with-sql-database"></a>Failover do Azure-SSIS IR com o Banco de Dados SQL

### <a name="scenario-1-azure-ssis-ir-is-pointing-to-a-readwrite-listener-endpoint"></a>Cenário 1: o Azure-SSIS IR está apontando para um ponto de extremidade do ouvinte de leitura/gravação

Esse cenário será adequado quando:

- O Azure-SSIS IR estiver apontando para o ponto de extremidade do ouvinte de leitura/gravação do grupo de failover.
- O servidor do banco de dados SQL *não* estiver configurado com a regra do ponto de extremidade de serviço de rede virtual.

Se você quiser que o Azure-SSIS IR aponte para um ponto de extremidade do ouvinte de leitura/gravação, primeiro, precisará apontá-lo para o ponto de extremidade do servidor primário. Depois de colocar o SSISDB em um grupo de failover, você pode interromper o Azure-SSIS IR, alterá-lo para apontar para o ponto de extremidade do ouvinte de leitura/gravação usando Azure PowerShell e reiniciá-lo.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -CatalogServerEndpoint "Azure SQL Database read/write listener endpoint"
```

#### <a name="solution"></a>Solução

Quando ocorrer um failover, ele será transparente para o Azure-SSIS IR. O Azure-SSIS IR conecta-se automaticamente ao novo primário do grupo de failover. 

Caso deseje atualizar a região ou outras informações no Azure-SSIS IR, você pode interrompê-lo, editá-lo e reiniciá-lo.


### <a name="scenario-2-azure-ssis-ir-is-pointing-to-a-primary-server-endpoint"></a>Cenário 2: o Azure-SSIS IR está apontando para o ponto de extremidade do servidor primário

Esse cenário será adequado se o Azure-SSIS IR estiver apontando para um ponto de extremidade do servidor primário.

#### <a name="solution"></a>Solução

Quando o failover ocorrer, execute as seguintes etapas:

1. Pare o Azure-SSIS IR na região primária.

2. Edite o Azure-SSIS IR com as novas informações da região, do ponto de extremidade e da rede virtual da instância secundária.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
      -CatalogServerEndpoint "Azure SQL Database endpoint" `
      -CatalogAdminCredential "Azure SQL Database admin credentials" `
      -VNetId "new VNet" `
      -Subnet "new subnet" `
      -SetupScriptContainerSasUri "new custom setup SAS URI"
   ```

3. Reinicie o Azure-SSIS IR.

### <a name="scenario-3-attach-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Cenário 3: anexar um SSISDB (catálogo do SSIS) existente a um novo Azure-SSIS IR

Esse cenário será adequado se você quiser provisionar um novo Azure-SSIS IR em uma região secundária. Também será adequado se você quiser que o SSISDB continue trabalhando com um novo Azure-SSIS IR em uma nova região quando ocorrer um desastre do Azure Data Factory ou do Azure-SSIS IR na região atual.

#### <a name="solution"></a>Solução

Quando ocorrer o failover, execute as etapas a seguir.

> [!NOTE]
> Use o PowerShell para a etapa 4 (criação do IR). Caso contrário, o portal do Azure relatará um erro informando que o SSISDB já existe.

1. Pare o Azure-SSIS IR na região primária.

2. Execute um procedimento armazenado para atualizar os metadados no SSISDB para aceitar conexões de **\<new_data_factory_name\>** e **\<new_integration_runtime_name\>** .
   
   ```sql
   EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
   ```

3. Crie um novo data factory chamado **\<new_data_factory_name\>** na nova região.

   ```powershell
   Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
      -Location "new region"`
      -Name "<new_data_factory_name>"
   ```
   
   Para obter mais informações sobre esse comando do PowerShell, confira [Criar um Azure data factory usando o PowerShell](quickstart-create-data-factory-powershell.md).

4. Crie um novo Azure-SSIS IR nomeado **\<new_integration_runtime_name\>** na nova região usando Azure PowerShell.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
      -DataFactoryName "new data factory name" `
      -Name "<new_integration_runtime_name>" `
      -Description $AzureSSISDescription `
      -Type Managed `
      -Location $AzureSSISLocation `
      -NodeSize $AzureSSISNodeSize `
      -NodeCount $AzureSSISNodeNumber `
      -Edition $AzureSSISEdition `
      -LicenseType $AzureSSISLicenseType `
      -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
      -VnetId "new vnet" `
      -Subnet "new subnet" `
      -CatalogServerEndpoint $SSISDBServerEndpoint `
      -CatalogPricingTier $SSISDBPricingTier
   ```

   Para obter mais informações sobre esse comando do PowerShell, confira [Criar o Azure-SSIS Integration Runtime no Azure Data Factory](create-azure-ssis-integration-runtime.md).

## <a name="next-steps"></a>Próximas etapas

Considere estas outras opções de configuração para o IR do Azure-SSIS:

- [Configurar o Azure-SSIS Integration Runtime para alto desempenho](configure-azure-ssis-integration-runtime-performance.md)

- [Personalizar a instalação para o runtime de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Provisionar a Edição Enterprise para o Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
