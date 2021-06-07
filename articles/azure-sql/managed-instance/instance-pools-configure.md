---
title: Implantar o SQL Instância Gerenciada em um pool de instâncias
titleSuffix: Azure SQL Managed Instance
description: Este artigo descreve como criar e gerenciar pools de Instância Gerenciada do SQL do Azure (versão prévia).
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: bonova
ms.author: bonova
ms.reviewer: sstein
ms.date: 09/05/2019
ms.openlocfilehash: 732a9bab018103321a9a3a759b31495bd67a209e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92793102"
---
# <a name="deploy-azure-sql-managed-instance-to-an-instance-pool"></a>Implantar o Azure SQL Instância Gerenciada em um pool de instâncias
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este artigo fornece detalhes sobre como criar um [pool de instâncias](instance-pools-overview.md) e implantar o Azure SQL instância gerenciada nele. 

## <a name="instance-pool-operations"></a>Operações de pool de instâncias

A tabela a seguir mostra as operações disponíveis relacionadas a pools de instâncias e sua disponibilidade no portal do Azure e no PowerShell.

|Comando|Portal do Azure|PowerShell|
|:---|:---|:---|
|Criar um pool de instância|Não|Sim|
|Atualizar um pool de instâncias (número limitado de propriedades)|Não |Sim |
|Verificar o uso e as propriedades do pool de instâncias|Não|Sim |
|Excluir um pool de instâncias|Não|Sim|
|Criar uma instância gerenciada dentro de um pool de instâncias|Não|Sim|
|Atualizar o uso de recursos para uma instância gerenciada|Sim |Sim|
|Verificar o uso e as propriedades de uma instância gerenciada|Sim|Sim|
|Excluir uma instância gerenciada do pool|Sim|Sim|
|Criar um banco de dados em instância dentro do pool|Sim|Sim|
|Excluir um banco de dados do SQL Instância Gerenciada|Sim|Sim|

[Comandos do PowerShell](/powershell/module/az.sql/)disponíveis:

|Cmdlet |Descrição |
|:---|:---|
|[New-AzSqlInstancePool](/powershell/module/az.sql/new-azsqlinstancepool/) | Cria um pool de Instância Gerenciada do SQL. |
|[Get-AzSqlInstancePool](/powershell/module/az.sql/get-azsqlinstancepool/) | Retorna informações sobre um pool de instâncias. |
|[Set-AzSqlInstancePool](/powershell/module/az.sql/set-azsqlinstancepool/) | Define as propriedades de um pool de instâncias no SQL Instância Gerenciada. |
|[Remove-AzSqlInstancePool](/powershell/module/az.sql/remove-azsqlinstancepool/) | Remove um pool de instâncias no SQL Instância Gerenciada. |
|[Get-AzSqlInstancePoolUsage](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | Retorna informações sobre o uso do pool de Instância Gerenciada do SQL. |


Para usar o PowerShell, [instale a última versão do PowerShell Core](/powershell/scripting/install/installing-powershell#powershell) e siga as instruções para [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps).

Para operações relacionadas a instâncias dentro de pools e instâncias únicas, use os [comandos de instância gerenciada padrão](api-references-create-manage-instance.md#powershell-create-and-configure-managed-instances), mas o *nome do pool de instâncias* propriedade deve ser populada ao usar esses comandos para uma instância em um pool.

## <a name="deployment-process"></a>Processo de implantação

Para implantar uma instância gerenciada em um pool de instâncias, você deve primeiro implantar o pool de instâncias, que é uma operação de execução única, em que a duração é a mesma que a implantação de uma [única instância criada em uma sub-rede vazia](sql-managed-instance-paas-overview.md#management-operations). Depois disso, você pode implantar uma instância gerenciada no pool, que é uma operação relativamente rápida que normalmente leva até cinco minutos. O parâmetro do pool de instâncias deve ser especificado explicitamente como parte dessa operação.

Na visualização pública, as duas ações só têm suporte usando o PowerShell e os modelos de Azure Resource Manager. A experiência de portal do Azure não está disponível no momento.

Depois que uma instância gerenciada é implantada em um pool, você *pode* usar a portal do Azure para alterar suas propriedades na página de tipo de preço.

## <a name="create-a-virtual-network-with-a-subnet"></a>Criar uma rede virtual com uma sub-rede 

Para posicionar vários pools de instância dentro da mesma rede virtual, veja os artigos a seguir:

- [Determine o tamanho da sub-rede VNet para o Azure SQL instância gerenciada](vnet-subnet-determine-size.md).
- Crie uma nova rede virtual e sub-rede usando o [modelo de portal do Azure](virtual-network-subnet-create-arm-template.md) ou siga as instruções para [preparar uma rede virtual existente](vnet-existing-add-subnet.md).
 

## <a name="create-an-instance-pool"></a>Criar um pool de instância 

Depois de concluir as etapas anteriores, você estará pronto para criar um pool de instâncias.

As restrições a seguir se aplicam a pools de instâncias:

- Somente Uso Geral e Gen5 estão disponíveis em visualização pública.
- O nome do pool pode conter apenas letras minúsculas, números e hifens e não pode começar com um hífen.
- Se você quiser usar Benefício Híbrido do Azure, ela será aplicada no nível do pool de instâncias. Você pode definir o tipo de licença durante a criação do pool ou atualizá-lo a qualquer momento após a criação.

> [!IMPORTANT]
> A implantação de um pool de instâncias é uma operação de execução prolongada que leva aproximadamente 4,5 horas.

Para obter os parâmetros de rede:

```powershell
$virtualNetwork = Get-AzVirtualNetwork -Name "miPoolVirtualNetwork" -ResourceGroupName "myResourceGroup"
$subnet = Get-AzVirtualNetworkSubnetConfig -Name "miPoolSubnet" -VirtualNetwork $virtualNetwork
```

Para criar um pool de instância:

```powershell
$instancePool = New-AzSqlInstancePool `
  -ResourceGroupName "myResourceGroup" `
  -Name "mi-pool-name" `
  -SubnetId $subnet.Id `
  -LicenseType "LicenseIncluded" `
  -VCore 8 `
  -Edition "GeneralPurpose" `
  -ComputeGeneration "Gen5" `
  -Location "westeurope"
```

> [!IMPORTANT]
> Como a implantação de um pool de instâncias é uma operação de execução prolongada, você precisa aguardar até que seja concluída antes de executar qualquer uma das etapas a seguir neste artigo.

## <a name="create-a-managed-instance"></a>Criar uma instância gerenciada

Após a implantação bem-sucedida do pool de instâncias, é hora de criar uma instância gerenciada dentro dela.

Para criar uma instância gerenciada, execute o comando a seguir:

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-one-name" -VCore 2 -StorageSizeInGB 256
```

A implantação de uma instância dentro de um pool leva alguns minutos. Depois que a primeira instância tiver sido criada, instâncias adicionais poderão ser criadas:

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-two-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database"></a>Criar um banco de dados 

Para criar e gerenciar bancos de dados em uma instância gerenciada que esteja dentro de um pool, use os comandos de instância única.

Criar um banco de dados dentro de uma instância gerenciada:

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-pool-usage"></a>Obter uso do pool 
 
Para obter uma lista de instâncias dentro de um pool:

```powershell
$instancePool | Get-AzSqlInstance
```


Para obter o uso de recursos de pool:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage
```


Para obter uma visão geral do uso detalhado do pool e das instâncias dentro dele:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage –ExpandChildren
```

Bancos de dados em uma instância:

```powershell
$databases = Get-AzSqlInstanceDatabase -InstanceName "pool-mi-001" -ResourceGroupName "resource-group-name"
```


> [!NOTE]
> Há um limite de 100 bancos de dados por pool (não por instância).


## <a name="scale"></a>Escala 


Depois de preencher uma instância gerenciada com bancos de dados, você pode atingir limites de instância em relação ao armazenamento ou desempenho. Nesse caso, se o uso do pool não tiver sido excedido, você poderá dimensionar sua instância.
Dimensionar uma instância gerenciada dentro de um pool é uma operação que leva alguns minutos. O pré-requisito para o dimensionamento é vCores e armazenamento disponíveis no nível do pool de instâncias.

Para atualizar o número de vCores e o tamanho do armazenamento:

```powershell
$instanceOne | Set-AzSqlInstance -VCore 8 -StorageSizeInGB 512 -InstancePoolName "mi-pool-name"
```


Para atualizar apenas o tamanho do armazenamento somente:

```powershell
$instance | Set-AzSqlInstance -StorageSizeInGB 1024 -InstancePoolName "mi-pool-name"
```

## <a name="connect"></a>Conectar 

Para se conectar a uma instância gerenciada em um pool, as duas etapas a seguir são necessárias:

1. [Habilite o ponto de extremidade público para a instância](#enable-the-public-endpoint).
2. [Adicionar uma regra ao grupo de segurança de rede de entrada (NSG)](#add-an-inbound-rule-to-the-network-security-group).

Depois que as duas etapas forem concluídas, você poderá se conectar à instância usando um endereço de ponto de extremidade público, uma porta e credenciais fornecidas durante a criação da instância. 

### <a name="enable-the-public-endpoint"></a>Habilitar o ponto de extremidade público

A habilitação do ponto de extremidade público para uma instância pode ser feita por meio do portal do Azure ou usando o seguinte comando do PowerShell:


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

Esse parâmetro também pode ser definido durante a criação da instância.

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>Adicionar uma regra ao grupo de segurança de rede de entrada 

Essa etapa pode ser feita por meio do portal do Azure ou usando comandos do PowerShell e pode ser feita a qualquer momento após a sub-rede estar preparada para a instância gerenciada.

Para obter mais detalhes, confira [Permitir tráfego de ponto de extremidade público no grupo de segurança de rede](public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).


## <a name="move-an-existing-single-instance-to-a-pool"></a>Mover uma instância única existente para um pool
 
Mover instâncias para dentro e fora de um pool é uma das limitações de visualização pública. Uma solução alternativa depende da restauração pontual de bancos de dados de uma instância fora de um pool para uma instância que já esteja em um pool. 

As duas instâncias devem estar na mesma assinatura e região. Atualmente, não há suporte para a restauração entre regiões e entre assinaturas.

Esse processo tem um período de inatividade.

Para mover bancos de dados existentes:

1. Pause cargas de trabalho na instância gerenciada da qual você está migrando.
2. Gere scripts para criar bancos de dados do sistema e executá-los na instância que está dentro do pool de instâncias.
3. Faça uma restauração pontual de cada banco de dados da instância única para a instância no pool.

    ```powershell
    $resourceGroupName = "my resource group name"
    $managedInstanceName = "my managed instance name"
    $databaseName = "my source database name"
    $pointInTime = "2019-08-21T08:51:39.3882806Z"
    $targetDatabase = "name of the new database that will be created"
    $targetResourceGroupName = "resource group of instance pool"
    $targetInstanceName = "pool instance name"
       
    Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
      -ResourceGroupName $resourceGroupName `
      -InstanceName $managedInstanceName `
      -Name $databaseName `
      -PointInTime $pointInTime `
      -TargetInstanceDatabaseName $targetDatabase `
      -TargetResourceGroupName $targetResourceGroupName `
      -TargetInstanceName $targetInstanceName
    ```

4. Aponte seu aplicativo para a nova instância e retome suas cargas de trabalho.

Se houver vários bancos de dados, repita o processo para cada um.


## <a name="next-steps"></a>Próximas etapas

- Para obter uma lista de recursos e de comparação, consulte [Recursos comuns do SQL](../database/features-comparison.md).
- Para obter mais informações sobre a configuração de VNet, confira [Configuração de VNet de Instância Gerenciada de SQL](connectivity-architecture-overview.md).
- Para obter um início rápido que cria uma instância gerenciada e restaura um banco de dados de um arquivo de backup, confira [Criar uma instância gerenciada](instance-create-quickstart.md).
- Para obter um tutorial sobre como usar o Serviço de Migração de Banco de Dados do Azure para migração, confira [Migração de Instância Gerenciada de SQL usando o Serviço de Migração de Banco de Dados](../../dms/tutorial-sql-server-to-managed-instance.md).
- Para obter o monitoramento avançado do desempenho de banco de dados da Instância Gerenciada de SQL com inteligência de solução de problemas interna, confira [Monitorar a Instância Gerenciada de SQL do Azure usando a Análise de SQL do Azure](../../azure-monitor/insights/azure-sql.md).
- Para obter informações sobre preços, consulte [preços do SQL instância gerenciada](https://azure.microsoft.com/pricing/details/sql-database/managed/).