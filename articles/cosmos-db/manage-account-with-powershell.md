---
title: "Automação do Azure Cosmos DB – Gerenciamento com o PowerShell | Microsoft Docs"
description: Use o Azure PowerShell para gerenciar suas contas do Azure Cosmos DB.
services: cosmos-db
author: dmakwana
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2017
ms.author: dimakwan
ms.openlocfilehash: 25c543528119410dff0684845a713dcb0d6151d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-cosmos-db-account-using-powershell"></a>Criar uma conta do Azure Cosmos DB usando o PowerShell

O guia a seguir descreve os comandos para automatizar o gerenciamento de suas contas de banco de dados do Azure Cosmos DB usando o Azure PowerShell. Isso também inclui comandos para gerenciar as chaves de conta e as prioridades de failover em [contas de banco de dados com várias regiões][scaling-globally]. A atualização de sua conta de banco de dados permite que você adicione ou remova regiões e modifique políticas de consistência. Para o gerenciamento de plataforma cruzada de sua conta do Azure Cosmos DB, use a [CLI do Azure](cli-samples.md), a [API REST do Provedor de Recursos][rp-rest-api] ou o [portal do Azure](create-documentdb-dotnet.md#create-account).

## <a name="getting-started"></a>Introdução

Siga as instruções em [Como instalar e configurar o Azure PowerShell][powershell-install-configure] para instalar e fazer logon em sua conta do Azure Resource Manager no Powershell.

### <a name="notes"></a>Observações

* Se você quiser executar os comandos a seguir sem a necessidade de confirmação do usuário, acrescente o sinalizador `-Force` ao comando.
* Todos os comandos a seguir são síncronos.

## <a id="create-documentdb-account-powershell"></a> Criar uma conta do Azure Cosmos DB

Esse comando permite que você crie uma conta de banco de dados do Azure Cosmos DB. Configure sua nova conta de banco de dados como região única ou [várias regiões][scaling-globally] com uma [política de consistência](consistency-levels.md) determinada.

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $iprangefilter = "<ip-range-filter>"
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name>  -Location "<resource-group-location>" -Name <database-account-name> -Properties $CosmosDBProperties
    
* `<write-region-location>` O nome do local da região de gravação da conta do banco de dados. Esse local deve ter um valor de prioridade de failover de 0. Deve haver exatamente uma região de gravação por conta do banco de dados.
* `<read-region-location>` O nome do local da região de leitura da conta do banco de dados. Esse local deve ter um valor de prioridade de failover maior do que 0. Pode haver mais de uma região de leitura por conta do banco de dados.
* `<ip-range-filter>` Especifica o conjunto de endereços IP ou os intervalos de endereços IP no formato CIDR a serem incluídos como a lista de permissões de IPs de cliente para uma determinada conta de banco de dados. Os intervalos/endereços IP devem ser separados por vírgula e não devem conter espaços. Para obter mais informações, consulte [Suporte ao firewall do Azure Cosmos DB](firewall-support.md)
* `<default-consistency-level>` O nível de consistência padrão da conta do Azure Cosmos DB. Para obter mais informações, consulte [Níveis de consistência no Azure Cosmos DB](consistency-levels.md).
* `<max-interval>` Ao ser usado com a consistência Estagnação Limitada, esse valor representa a quantidade de tempo de desatualização (em segundos) tolerada. O intervalo aceito para este valor é de 1 a 100.
* `<max-staleness-prefix>` Ao ser usado com a consistência Estagnação Limitada, esse valor representa o número de solicitações de estagnação tolerado. O intervalo aceito para este valor é de 1 a 2,147,483,647.
* `<resource-group-name>` O nome do [Grupo de Recursos do Azure][azure-resource-groups] ao qual pertence a nova conta de banco de dados do Azure Cosmos DB.
* `<resource-group-location>` A localização do Grupo de Recursos do Azure à qual pertence a nova conta de banco de dados do Azure Cosmos DB.
* `<database-account-name>` O nome da conta de banco de dados do Azure Cosmos DB a ser criada. Pode usar apenas letras minúsculas, números, o caractere '-' e deve ter entre três e 50 caracteres.

Exemplo: 

    $locations = @(@{"locationName"="West US"; "failoverPriority"=0}, @{"locationName"="East US"; "failoverPriority"=1})
    $iprangefilter = ""
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Location "West US" -Name "docdb-test" -Properties $CosmosDBProperties

### <a name="notes"></a>Observações
* O exemplo anterior cria uma conta de banco de dados com duas regiões. Também é possível criar uma conta de banco de dados com uma região (que é designada como a região de gravação e tem um valor de prioridade de failover de 0) ou com mais de duas regiões. Para saber mais, veja [Contas de banco de dados com várias regiões][scaling-globally].
* As localizações devem ser regiões nas quais o Azure Cosmos DB está disponível. Confira a lista atual de regiões na [página Regiões do Azure](https://azure.microsoft.com/regions/#services).

## <a id="update-documentdb-account-powershell"></a> Atualizar uma conta de banco de dados do DocumentDB

Esse comando permite que você atualize as propriedades de sua conta de banco de dados do Azure Cosmos DB. Isso inclui a política de consistência e os locais nos quais a conta de banco de dados existe.

> [!NOTE]
> Esse comando permite adicionar e remover regiões, mas não permite modificar as prioridades de failover. Para modificar as prioridades de failover, confira [abaixo](#modify-failover-priority-powershell).

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $iprangefilter = "<ip-range-filter>"
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    Set-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name> -Name <database-account-name> -Properties $CosmosDBProperties
    
* `<write-region-location>` O nome do local da região de gravação da conta do banco de dados. Esse local deve ter um valor de prioridade de failover de 0. Deve haver exatamente uma região de gravação por conta do banco de dados.
* `<read-region-location>` O nome do local da região de leitura da conta do banco de dados. Esse local deve ter um valor de prioridade de failover maior do que 0. Pode haver mais de uma região de leitura por conta do banco de dados.
* `<default-consistency-level>` O nível de consistência padrão da conta do Azure Cosmos DB. Para obter mais informações, consulte [Níveis de consistência no Azure Cosmos DB](consistency-levels.md).
* `<ip-range-filter>` Especifica o conjunto de endereços IP ou os intervalos de endereços IP no formato CIDR a serem incluídos como a lista de permissões de IPs de cliente para uma determinada conta de banco de dados. Os intervalos/endereços IP devem ser separados por vírgula e não devem conter espaços. Para obter mais informações, consulte [Suporte ao firewall do Azure Cosmos DB](firewall-support.md)
* `<max-interval>` Ao ser usado com a consistência Estagnação Limitada, esse valor representa a quantidade de tempo de desatualização (em segundos) tolerada. O intervalo aceito para este valor é de 1 a 100.
* `<max-staleness-prefix>` Ao ser usado com a consistência Estagnação Limitada, esse valor representa o número de solicitações de estagnação tolerado. O intervalo aceito para este valor é de 1 a 2,147,483,647.
* `<resource-group-name>` O nome do [Grupo de Recursos do Azure][azure-resource-groups] ao qual pertence a nova conta de banco de dados do Azure Cosmos DB.
* `<resource-group-location>` A localização do Grupo de Recursos do Azure à qual pertence a nova conta de banco de dados do Azure Cosmos DB.
* `<database-account-name>` O nome da conta de banco de dados do Azure Cosmos DB a ser atualizada.

Exemplo: 

    $locations = @(@{"locationName"="West US"; "failoverPriority"=0}, @{"locationName"="East US"; "failoverPriority"=1})
    $iprangefilter = ""
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    Set-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Properties $CosmosDBProperties

## <a id="delete-documentdb-account-powershell"></a> Excluir uma conta de banco de dados do DocumentDB

Esse comando permite que você exclua uma conta de banco de dados existente do Azure Cosmos DB.

    Remove-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"
    
* `<resource-group-name>` O nome do [Grupo de Recursos do Azure][azure-resource-groups] ao qual pertence a nova conta de banco de dados do Azure Cosmos DB.
* `<database-account-name>` O nome da conta de banco de dados do Azure Cosmos DB a ser excluída.

Exemplo:

    Remove-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="get-documentdb-properties-powershell"></a>Obter propriedades de uma conta de banco de dados do DocumentDB

Esse comando permite que você obtenha as propriedades de uma conta de banco de dados existente do Azure Cosmos DB.

    Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` O nome do [Grupo de Recursos do Azure][azure-resource-groups] ao qual pertence a nova conta de banco de dados do Azure Cosmos DB.
* `<database-account-name>` O nome da conta de banco de dados do Azure Cosmos DB.

Exemplo:

    Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="update-tags-powershell"></a> Atualizar as marcações de uma conta de banco de dados do Azure Cosmos DB

O exemplo a seguir descreve como definir [marcações de recurso do Azure][azure-resource-tags] em sua conta de banco de dados do Azure Cosmos DB.

> [!NOTE]
> Este comando pode ser combinado com os comandos de criação ou atualização anexando o sinalizador `-Tags` com o parâmetro correspondente.

Exemplo:

    $tags = @{"dept" = "Finance”; environment = “Production”}
    Set-AzureRmResource -ResourceType “Microsoft.DocumentDB/databaseAccounts”  -ResourceGroupName "rg-test" -Name "docdb-test" -Tags $tags

## <a id="list-account-keys-powershell"></a> Listar chaves da conta

Quando você cria uma conta do Azure Cosmos DB, o serviço gera duas chaves de acesso mestras que podem ser usadas para autenticação quando a conta do Azure Cosmos DB é acessada. Ao fornecer duas chaves de acesso, o Azure Cosmos DB permite regenerar as chaves sem nenhuma interrupção na conta do Azure Cosmos DB. Também estão disponíveis chaves somente leitura para autenticação de operações somente leitura. Há duas chaves de leitura/gravação (primária e secundária) e duas chaves somente leitura (primária e secundária).

    $keys = Invoke-AzureRmResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` O nome do [Grupo de Recursos do Azure][azure-resource-groups] ao qual pertence a nova conta de banco de dados do Azure Cosmos DB.
* `<database-account-name>` O nome da conta de banco de dados do Azure Cosmos DB.

Exemplo:

    $keys = Invoke-AzureRmResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="list-connection-strings-powershell"></a> Listar Cadeias de Conexão

Para contas do MongoDB, a cadeia de conexão para conectar seu aplicativo do MongoDB à conta do banco de dados pode ser recuperada usando o comando a seguir.

    $keys = Invoke-AzureRmResourceAction -Action listConnectionStrings -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` O nome do [Grupo de Recursos do Azure][azure-resource-groups] ao qual pertence a nova conta de banco de dados do Azure Cosmos DB.
* `<database-account-name>` O nome da conta de banco de dados do Azure Cosmos DB.

Exemplo:

    $keys = Invoke-AzureRmResourceAction -Action listConnectionStrings -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="regenerate-account-key-powershell"></a> Recuperar a chave da conta

Você deve alterar as chaves de acesso de sua conta do Azure Cosmos DB periodicamente para ajudar a manter as conexões mais seguras. Duas chaves de acesso são atribuídas para permitir que você mantenha conexões com a conta do Azure Cosmos DB usando uma chave de acesso enquanto regenera a outra.

    Invoke-AzureRmResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"keyKind"="<key-kind>"}

* `<resource-group-name>` O nome do [Grupo de Recursos do Azure][azure-resource-groups] ao qual pertence a nova conta de banco de dados do Azure Cosmos DB.
* `<database-account-name>` O nome da conta de banco de dados do Azure Cosmos DB.
* `<key-kind>` Um dos quatro tipos de chaves: ["Primary"|"Secondary"|"PrimaryReadonly"|"SecondaryReadonly"] que você quer gerar novamente.

Exemplo:

    Invoke-AzureRmResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"keyKind"="Primary"}

## <a id="modify-failover-priority-powershell"></a> Modificar a Prioridade de Failover de uma Conta de Banco de Dados do Azure Cosmos DB

Para contas de banco de dados com várias regiões, altere a prioridade de failover das várias regiões nas quais a conta de banco de dados do Azure Cosmos DB existe. Para obter mais informações sobre failover em sua conta de banco de dados do Azure Cosmos DB, consulte [Distribuir dados globalmente com o Azure Cosmos DB][distribute-data-globally].

    $failoverPolicies = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0},@{"locationName"="<read-region-location>"; "failoverPriority"=1})
    Invoke-AzureRmResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"failoverPolicies"=$failoverPolicies}

* `<write-region-location>` O nome do local da região de gravação da conta do banco de dados. Esse local deve ter um valor de prioridade de failover de 0. Deve haver exatamente uma região de gravação por conta do banco de dados.
* `<read-region-location>` O nome do local da região de leitura da conta do banco de dados. Esse local deve ter um valor de prioridade de failover maior do que 0. Pode haver mais de uma região de leitura por conta do banco de dados.
* `<resource-group-name>` O nome do [Grupo de Recursos do Azure][azure-resource-groups] ao qual pertence a nova conta de banco de dados do Azure Cosmos DB.
* `<database-account-name>` O nome da conta de banco de dados do Azure Cosmos DB.

Exemplo:

    $failoverPolicies = @(@{"locationName"="East US"; "failoverPriority"=0},@{"locationName"="West US"; "failoverPriority"=1})
    Invoke-AzureRmResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"failoverPolicies"=$failoverPolicies}

## <a name="next-steps"></a>Próximas etapas

* Para se conectar usando o .NET, consulte [Conectar e consultar com o .NET](create-documentdb-dotnet.md).
* Para se conectar usando o .NET Core, consulte [Conectar e consultar com o .NET Core](create-documentdb-dotnet-core.md).
* Para se conectar usando o Node.js, consulte [Conectar e consultar com o Node.js e um aplicativo do MongoDB](create-mongodb-nodejs.md).

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/documentdbresourceprovider/
