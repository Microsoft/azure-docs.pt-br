---
title: 'PowerShell: migrar SQL Server para o banco de dados SQL'
titleSuffix: Azure Database Migration Service
description: Saiba como migrar um banco de dados do SQL Server para o banco de dados SQL do Azure usando Azure PowerShell com o serviço de migração de banco de dados do Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: 3152fe0bf77b73e593ac61efb5f386827bdc96ef
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643717"
---
# <a name="migrate-a-sql-server-database-to-azure-sql-database-using-azure-powershell"></a>Migrar um banco de dados SQL Server para o banco de dados SQL do Azure usando Azure PowerShell

Neste artigo, você migra o banco de dados **Adventureworks2012** restaurado para uma instância local do SQL Server 2016 ou superior para o banco de dados SQL do Azure usando Microsoft Azure PowerShell. Você pode migrar bancos de dados de uma instância SQL Server para o banco de dados SQL do Azure usando o `Az.DataMigration` módulo no Microsoft Azure PowerShell.

Neste artigo, você aprenderá como:
> [!div class="checklist"]
>
> * Crie um grupos de recursos.
> * Criar uma instância do Serviço de Migração de Banco de Dados do Azure.
> * Crie um projeto de migração em uma instância do Serviço de Migração de Banco de Dados do Azure.
> * Executar a migração.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir essas etapas, você precisa:

* [SQL Server 2016 ou posterior](https://www.microsoft.com/sql-server/sql-server-downloads) (qualquer edição)
* Para habilitar o protocolo TCP/IP, que está desabilitado por padrão com a instalação do SQL Server Express. Habilite o protocolo TCP/IP seguindo o artigo [Habilitar ou desabilitar um protocolo de rede de servidor](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
* Configurar o [Firewall do Windows para acesso ao mecanismo de banco de dados](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Uma instância do Banco de Dados SQL do Azure. Você pode criar uma instância do banco de dados SQL do Azure seguindo os detalhes no artigo [criar um banco de dados no banco de dados SQL do Azure no portal do Azure](../azure-sql/database/single-database-create-quickstart.md).
* [Assistente de Migração de Dados](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 ou posterior.
* Para ter criado um Rede Virtual do Microsoft Azure usando o modelo de implantação Azure Resource Manager, que fornece o serviço de migração de banco de dados do Azure com conectividade site a site para seus servidores de origem locais usando o [ExpressRoute](../expressroute/expressroute-introduction.md) ou [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* Para concluir a avaliação de seu banco de dados local e a migração de esquema usando Assistente de Migração de Dados, conforme descrito no artigo [executando uma avaliação de migração de SQL Server](/sql/dma/dma-assesssqlonprem)
* Para baixar e instalar o módulo AZ. datamigration por meio do Galeria do PowerShell usando o [cmdlet Install-Module PowerShell](/powershell/module/powershellget/Install-Module); Certifique-se de abrir a janela de comando do PowerShell usando executar como administrador.
* Para garantir que as credenciais usadas para conectar a instância do SQL Server de origem tenham a permissão [CONTROL SERVER](/sql/t-sql/statements/grant-server-permissions-transact-sql).
* Para garantir que as credenciais usadas para conectar A instância do Azure SQL DB de destino tenham a permissão CONTROL DATABASE nos bancos de dados do Banco de Dados SQL do Azure de destino.
* Uma assinatura do Azure. Se você não tiver uma, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Fazer logon em sua assinatura do Microsoft Azure

Use as instruções do artigo [Fazer logon com o Azure PowerShell](/powershell/azure/authenticate-azureps) para fazer logon em sua assinatura do Azure usando o PowerShell.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Crie um grupo de recursos antes de criar uma máquina virtual.

Crie um grupo de recursos usando o comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) .

O exemplo a seguir cria um grupo de recursos chamado *MyResource* Group na região *eastus* .

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Criar uma instância do Serviço de Migração de Banco de Dados do Azure

Crie uma nova instância do Serviço de Migração de Banco de Dados do Azure usando o cmdlet `New-AzDataMigrationService`. Esse cmdlet espera os seguintes parâmetros obrigatórios:

* *Nome do grupo de recursos do Azure*. Você pode usar o comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) para criar o grupo de recursos do Azure como mostrado anteriormente e fornecer seu nome como um parâmetro.
* *Nome do serviço*. Cadeia de caracteres que corresponde ao nome de serviço exclusivo desejado para o Serviço de Migração de Banco de Dados do Azure 
* *Localização*. Especifica o local do serviço. Especifique um local de data center do Azure, como Oeste dos EUA ou Sudeste Asiático
* *SKU*. Esse parâmetro corresponde ao nome do SKU do DMS. É o nome da Sku com suporte no momento *GeneralPurpose_4vCores*.
* *Identificador de Sub-rede Virtual*. Você pode usar o cmdlet [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) para criar uma sub-rede. 

O exemplo a seguir cria um serviço nomeado *MyDMS* no grupo de recursos *MyDMSResourceGroup* localizado na região *Leste dos EUA* com uma rede virtual nomeada *MyVNET* e sub-rede virtual chamada *MySubnet*.

```powershell
 $vNet = Get-AzVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Depois de criar uma instância do Serviço de Migração de Banco de Dados do Azure, crie um projeto de migração. Um projeto do Serviço de Migração de Banco de Dados do Azure exige informações de conexão para as instâncias de origem e de destino, bem como uma lista de bancos de dados que você deseja migrar como parte do projeto.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Criar um objeto de Informações de Conexão de Banco de Dados para as conexões de origem e destino

Crie um objeto de Informações de Conexão de Banco de Dados usando o cmdlet `New-AzDmsConnInfo`. Esse cmdlet espera os seguintes parâmetros:

* *ServerType*. O tipo de conexão de banco de dados solicitado, por exemplo, SQL, Oracle ou MySQL. Use o SQL para SQL Server e SQL do Azure.
* *Fonte de fontes*. O nome ou IP de uma instância de SQL Server ou banco de dados SQL do Azure.
* *AuthType*. O tipo de autenticação da conexão, que pode ser SqlAuthentication ou WindowsAuthentication.
* O parâmetro *TrustServerCertificate* define um valor que indica se o canal é criptografado durante o bypass percorrendo a cadeia de certificados para validar a relação de confiança. O valor pode ser verdadeiro ou falso.

O exemplo a seguir cria o objeto Connection Info para SQL Server de origem chamado MySourceSQLServer usando autenticação sql:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

O exemplo a seguir mostra a criação de informações de conexão para um servidor chamado SQLAzureTarget usando a autenticação do SQL:

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "sqlazuretarget.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Fornecer bancos de dados para o projeto de migração

Crie uma lista de objetos `AzDataMigrationDatabaseInfo` que especifica os bancos de dados como parte do projeto da Migração de Banco de Dados do Azure que podem ser fornecidos como parâmetro para a criação do projeto. O cmdlet `New-AzDataMigrationDatabaseInfo` pode ser usado para criar AzDataMigrationDatabaseInfo. 

O exemplo a seguir cria o projeto `AzDataMigrationDatabaseInfo` para o banco de dados **AdventureWorks2016** e adiciona-o à lista a ser fornecida como parâmetro para a criação do projeto.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks2016
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Criar um objeto de projeto

Por fim, crie o projeto de Migração de Banco de Dados do Azure chamado *MyDMSProject* localizado no *Leste dos EUA* usando `New-AzDataMigrationProject` e adicionando as conexões de origem e destino criadas anteriormente e a lista de bancos de dados a serem migrados.

```powershell
$project = New-AzDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLDB `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>Criar e iniciar uma tarefa de migração

Por fim, crie e inicie a tarefa de Migração de Banco de Dados do Azure. A tarefa de Migração de Banco de Dados do Azure exige informações de credenciais da conexão para a origem e o destino e a lista de tabelas de banco de dados a serem migrados, além das informações já fornecidas com o projeto criado como um pré-requisito.

### <a name="create-credential-parameters-for-source-and-target"></a>Criar parâmetros de credenciais para a origem e o destino

As credenciais de segurança da conexão podem ser criadas como o objeto [PSCredential](/dotnet/api/system.management.automation.pscredential).

O exemplo a seguir mostra a criação de objetos *PSCredential* para fornecer para as conexões de origem e destino fornecendo senhas como variáveis de cadeia de caracteres *$sourcePassword* e *$ targetPassword*.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-table-map-and-select-source-and-target-parameters-for-migration"></a>Criar um mapa de tabela e selecionar os parâmetros de origem e destino para migração

Outro parâmetro necessário para a migração é o mapeamento de tabelas da origem para o destino a serem migradas. Crie um dicionário de tabelas que fornece um mapeamento entre as tabelas de origem e destino para migração. O exemplo a seguir ilustra o mapeamento entre o esquema de Recursos Humanos das tabelas de origem e destino do banco de dados AdventureWorks 2016.

```powershell
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]'
$tableMap.Add("HumanResources.Department", "HumanResources.Department")
$tableMap.Add("HumanResources.Employee","HumanResources.Employee")
$tableMap.Add("HumanResources.EmployeeDepartmentHistory","HumanResources.EmployeeDepartmentHistory")
$tableMap.Add("HumanResources.EmployeePayHistory","HumanResources.EmployeePayHistory")
$tableMap.Add("HumanResources.JobCandidate","HumanResources.JobCandidate")
$tableMap.Add("HumanResources.Shift","HumanResources.Shift")
```

A próxima etapa é selecionar os bancos de dados de origem e destino e fornecer o mapeamento de tabela a ser migrado como um parâmetro usando o cmdlet `New-AzDmsSelectedDB`, conforme mostrado no seguinte exemplo:

```powershell
$selectedDbs = New-AzDmsSelectedDB -MigrateSqlServerSqlDb -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -TableMap $tableMap
```

### <a name="create-the-migration-task-and-start-it"></a>Criar a tarefa de migração e iniciá-la

Use o cmdlet `New-AzDataMigrationTask` para criar e iniciar uma tarefa de migração. Esse cmdlet espera os seguintes parâmetros:

* *TaskType*. O tipo de tarefa de migração a ser criado para o SQL Server para o tipo de migração de Banco de Dados SQL do Azure *MigrateSqlServerSqlDb* é esperado. 
* *Nome do grupo de recursos*. Nome do grupo de recursos do Azure no qual a tarefa será criada.
* *ServiceName*. Instância do Serviço de Migração de Banco de Dados do Azure na qual a tarefa será criada.
* *ProjectName*. Nome do projeto do Serviço de Migração de Banco de Dados do Azure no qual criar a tarefa. 
* *Nome_Tarefa*. Nome da tarefa a ser criada. 
* *SourceConnection*. Objeto AzDmsConnInfo que representa a conexão SQL Server de origem.
* *TargetConnection*. Objeto AzDmsConnInfo que representa a conexão de banco de dados SQL do Azure de destino.
* *SourceCred*. Objeto [PSCredential](/dotnet/api/system.management.automation.pscredential) para se conectar ao servidor de origem.
* *TargetCred*. Objeto [PSCredential](/dotnet/api/system.management.automation.pscredential) para se conectar ao servidor de destino.
* *SelectedDatabase*. Objeto AzDataMigrationSelectedDB que representa o mapeamento de banco de dados de origem e destino.
* *SchemaValidation*. (opcional, parâmetro de opção) Após a migração, o executa uma comparação das informações de esquema entre a origem e o destino.
* *DataIntegrityValidation*. (opcional, parâmetro de opção) Após a migração, o executa uma validação de integridade de dados baseada em soma de verificação entre a origem e o destino.
* *QueryAnalysisValidation*. (opcional, parâmetro de opção) Após a migração, o executa uma análise de consulta rápida e inteligente recuperando consultas do banco de dados de origem e as executa no destino.

O seguinte exemplo cria e inicia uma tarefa de migração chamada myDMSTask:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDb `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
```

O exemplo a seguir cria e inicia a mesma tarefa de migração acima, mas também executa todas as três validações:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDb `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -SchemaValidation `
  -DataIntegrityValidation `
  -QueryAnalysisValidation `
```

## <a name="monitor-the-migration"></a>Monitorar a migração

Monitore a tarefa de migração em execução consultando a propriedade de estado da tarefa, conforme mostrado no seguinte exemplo:

```powershell
if (($mytask.ProjectTask.Properties.State -eq "Running") -or ($mytask.ProjectTask.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="deleting-the-dms-instance"></a>Excluir a instância do DMS

Quando a migração é concluída, você pode excluir a instância do DMS do Azure:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="next-step"></a>Próxima etapa

* Verificar as diretrizes de migração no [Guia de Migração de Banco de Dados da Microsoft](https://datamigration.microsoft.com/).