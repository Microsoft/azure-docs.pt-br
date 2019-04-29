---
title: Roteamento dependente de dados com o Banco de Dados SQL do Azure | Microsoft Docs
description: Como usar a classe ShardMapManager em aplicativos .NET para o roteamento dependente de dados, um recurso dos bancos de dados fragmentados para o Banco de Dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: fe9098592fcfde2d5e23b78a3e33f2b4ebb9e2dc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60584957"
---
# <a name="use-data-dependent-routing-to-route-a-query-to-appropriate-database"></a>Use o roteamento dependente dos dados para rotear uma consulta para o banco de dados apropriado

O **roteamento dependente de dados** é a capacidade de usar os dados em uma consulta para encaminhar a solicitação para um banco de dados apropriado. O roteamento dependente de dados é um padrão fundamental ao trabalhar com bancos de dados fragmentados. O contexto da solicitação também poderá ser usado para encaminhar a solicitação, especialmente se a chave de fragmentação não fizer parte da consulta. Cada consulta ou transação específica em um aplicativo que usa o roteamento dependente de dados é restrita a acessar um único banco de dados por solicitação. Para as ferramentas do Banco de Dados SQL do Azure Elástico, este roteamento é feito com a classe **ShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)).

O aplicativo não precisa controlar a várias cadeias de caracteres de conexão ou locais de BD associados a diferentes subconjuntos de dados no ambiente fragmentado. Em vez disso, o [Gerenciador de Mapa de Fragmento](sql-database-elastic-scale-shard-map-management.md) abre as conexões no banco de dados correto quando necessário, com base nos dados do mapa do fragmento e o valor da chave de fragmentação é o destino da solicitação do aplicativo. A chave é geralmente a *customer_id*, a *tenant_id*, a *date_key* ou outro identificador específico que seja um parâmetro fundamental da solicitação do banco de dados.

Para obter mais informações, confira [Scaling Out SQL Server with Data-Dependent Routing](https://technet.microsoft.com/library/cc966448.aspx) (Escalando horizontalmente o SQL Server com roteamento dependente de dados).

## <a name="download-the-client-library"></a>Baixar a biblioteca de cliente

Para baixar:

* A versão do Java da biblioteca, consulte [Repositório Maven Central](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* A versão do .NET da biblioteca, consulte [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="using-a-shardmapmanager-in-a-data-dependent-routing-application"></a>Usando um ShardMapManager em um aplicativo de roteamento dependente de dados

Os aplicativos devem criar a instância do **ShardMapManager** durante a inicialização, usando a chamada do alocador **GetSQLShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)). Neste exemplo, tanto um **ShardMapManager** quanto um **ShardMap** específico que ele contém são inicializados. Este exemplo mostra os métodos GetSqlShardMapManager e GetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getrangeshardmap), [.NET](https://docs.microsoft.com/previous-versions/azure/dn824173(v=azure.100))).

```Java
ShardMapManager smm = ShardMapManagerFactory.getSqlShardMapManager(connectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> rangeShardMap = smm.getRangeShardMap(Configuration.getRangeShardMapName(), ShardKeyType.Int32);
```

```csharp
ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 
```

### <a name="use-lowest-privilege-credentials-possible-for-getting-the-shard-map"></a>Use as credenciais com o privilégio mais baixo possível para obter o mapa do fragmento

Se o aplicativo não estiver manipulando o mapa de fragmentos por conta própria, as credenciais usadas no método de fábrica deverão ter permissões somente leitura no banco de dados **Mapa de Fragmentos Global**. Essas credenciais são normalmente diferentes das credenciais usadas para abrir conexões com o Gerenciador de mapa do fragmento. Consulte também [Credenciais usadas para acessar a biblioteca de cliente do Banco de Dados Elástico](sql-database-elastic-scale-manage-credentials.md).

## <a name="call-the-openconnectionforkey-method"></a>Chame o método OpenConnectionForKey

O **método ShardMap.OpenConnectionForKey** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)) retorna uma conexão pronta para emitir comandos para o banco de dados apropriado com base no valor do parâmetro **key**. As informações de fragmentos são armazenadas em cache no aplicativo pelo **ShardMapManager**, portanto, essas solicitações normalmente não envolvem uma pesquisa de banco de dados no banco de dados do **Mapa de Fragmentos Global**.

```Java
// Syntax:
public Connection openConnectionForKey(Object key, String connectionString, ConnectionOptions options)
```

```csharp
// Syntax:
public SqlConnection OpenConnectionForKey<TKey>(TKey key, string connectionString, ConnectionOptions options)
```

* O parâmetro **key** é usado como uma chave de pesquisa no mapa de fragmentos para determinar o banco de dados apropriado para a solicitação.
* **connectionString** é usado para transmitir somente as credenciais do usuário para a conexão desejada. Não há nenhum nome de banco de dados ou nome do servidor incluído nesta *connectionString* pois o método determina o banco de dados e o servidor usando o **ShardMap**.
* O **connectionOptions** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.connectionoptions), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.connectionoptions)) deverá ser definido como **ConnectionOptions.Validate** em um ambiente em que os mapeamentos de fragmento possam ser alterados e as linhas possam ser movidas para outros bancos de dados como resultado de operações de divisão ou mesclagem. Essa validação envolve uma breve consulta no mapa de fragmentos local no banco de dados de destino (não no mapa de fragmentos global) antes que a conexão seja entregue ao aplicativo.

Se a validação em relação ao mapa de fragmentos local falhar (indicando que o cache está incorreto), o Gerenciador de Mapa de Fragmentos consultará o mapa de fragmentos global para obter o novo valor correto para a pesquisa, atualizará o cache e obterá e retornará a conexão de banco de dados apropriada.

Use **ConnectionOptions.None** somente quando as alterações de mapeamento de fragmentos não são esperadas enquanto um aplicativo estiver online. Nesse caso, os valores do cache podem ser considerados sempre corretas, e a chamada de validação extra ida e volta ao banco de dados de destino pode ser ignorada com segurança. Isso reduz o tráfego de banco de dados. **connectionOptions** também pode ser definido por meio de um valor em um arquivo de configuração para indicar se as alterações de fragmentação são ou não esperadas durante um período.  

Este exemplo usa o valor de uma chave de inteiro **CustomerID**, usando um objeto **ShardMap** chamado **customerShardMap**.  

```Java
int customerId = 12345;
int productId = 4321;
// Looks up the key in the shard map and opens a connection to the shard
try (Connection conn = shardMap.openConnectionForKey(customerId, Configuration.getCredentialsConnectionString())) {
    // Create a simple command that will insert or update the customer information
    PreparedStatement ps = conn.prepareStatement("UPDATE Sales.Customer SET PersonID = ? WHERE CustomerID = ?");

    ps.setInt(1, productId);
    ps.setInt(2, customerId);
    ps.executeUpdate();
} catch (SQLException e) {
    e.printStackTrace();
}
```

```csharp
int customerId = 12345;
int newPersonId = 4321;

// Connect to the shard for that customer ID. No need to call a SqlConnection
// constructor followed by the Open method.
using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate))
{
    // Execute a simple command.
    SqlCommand cmd = conn.CreateCommand();
    cmd.CommandText = @"UPDATE Sales.Customer
                        SET PersonID = @newPersonID WHERE CustomerID = @customerID";

    cmd.Parameters.AddWithValue("@customerID", customerId);cmd.Parameters.AddWithValue("@newPersonID", newPersonId);
    cmd.ExecuteNonQuery();
}  
```

O método **OpenConnectionForKey** retorna uma nova conexão já aberta para o banco de dados correto. As conexões utilizadas dessa forma ainda aproveitam ao máximo do pool de conexões.

O **método OpenConnectionForKeyAsync method** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkeyasync), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkeyasync)) também estará disponível se o seu aplicativo usar programação assíncrona.

## <a name="integrating-with-transient-fault-handling"></a>Integrando a manipulação de falhas transitórias

É uma melhor prática no desenvolvimento de aplicativos de acesso de dados na nuvem garantir que as falhas transitórias são capturadas pelo aplicativo e que as operações serão repetidas várias vezes antes que ocorra um erro. O tratamento de falha transitória para aplicativos em nuvem é discutido em Transient Fault Handling (Tratamento de falha transitória) ([Java](/java/api/com.microsoft.azure.elasticdb.core.commons.transientfaulthandling), [.NET](https://docs.microsoft.com/previous-versions/msp-n-p/dn440719(v=pandp.60))).

O tratamento de falhas transitórias pode coexistir naturalmente com o padrão de roteamento dependente de dados. O principal requisito é repetir a solicitação de acesso de dados inteira incluindo o bloqueio **using** que obteve a conexão de roteamento dependente de dados. O exemplo anterior poderia ser reescrito da seguinte maneira.

### <a name="example---data-dependent-routing-with-transient-fault-handling"></a>Exemplo – roteamento dependente de dados com tratamento de falhas transitórias

```Java
int customerId = 12345;
int productId = 4321;
try {
    SqlDatabaseUtils.getSqlRetryPolicy().executeAction(() -> {
        // Looks up the key in the shard map and opens a connection to the shard
        try (Connection conn = shardMap.openConnectionForKey(customerId, Configuration.getCredentialsConnectionString())) {
            // Create a simple command that will insert or update the customer information
            PreparedStatement ps = conn.prepareStatement("UPDATE Sales.Customer SET PersonID = ? WHERE CustomerID = ?");

            ps.setInt(1, productId);
            ps.setInt(2, customerId);
            ps.executeUpdate();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    });
} catch (Exception e) {
    throw new StoreException(e.getMessage(), e);
}
```

```csharp
int customerId = 12345;
int newPersonId = 4321;

Configuration.SqlRetryPolicy.ExecuteAction(() =&gt;
{
    // Connect to the shard for a customer ID.
    using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate))
    {
        // Execute a simple command
        SqlCommand cmd = conn.CreateCommand();

        cmd.CommandText = @"UPDATE Sales.Customer
                            SET PersonID = @newPersonID
                            WHERE CustomerID = @customerID";

        cmd.Parameters.AddWithValue("@customerID", customerId);
        cmd.Parameters.AddWithValue("@newPersonID", newPersonId);
        cmd.ExecuteNonQuery();

        Console.WriteLine("Update completed");
    }
});
```

Pacotes necessários para implementar o tratamento de falhas transitórias são baixados automaticamente quando você compila o aplicativo de exemplo de banco de dados elástico.

## <a name="transactional-consistency"></a>Consistência transacional

Propriedades transacionais são garantidas para todas as operações locais para um fragmento. Por exemplo, transações enviadas por meio do roteamento dependentes de dados executado dentro do escopo do fragmento de destino para a conexão. Neste momento, não há nenhum recurso fornecido para inscrever-se várias conexões em uma transação e, portanto, não há nenhuma garantia transacional para operações executadas pelos fragmentos.

## <a name="next-steps"></a>Próximas etapas

Para desanexar um fragmento ou anexar um fragmento novamente, consulte [Usando a classe RecoveryManager para corrigir problemas do mapa de fragmentos](sql-database-elastic-database-recovery-manager.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]