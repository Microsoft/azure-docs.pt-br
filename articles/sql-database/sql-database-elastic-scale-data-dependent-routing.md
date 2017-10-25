---
title: Roteamento dependente de dados com o Banco de Dados SQL do Azure | Microsoft Docs
description: Como usar a classe ShardMapManager em aplicativos .NET para o roteamento dependente de dados, um recurso dos bancos de dados fragmentados para o Banco de Dados SQL do Azure
services: sql-database
documentationcenter: 
manager: jhubbard
author: torsteng
editor: 
ms.assetid: cad09e15-5561-4448-aa18-b38f54cda004
ms.service: sql-database
ms.custom: scale out apps
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2017
ms.author: ddove
ms.openlocfilehash: 6b68bbb0133afd1493acdb58f79f3eeaf6a8d7cd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="data-dependent-routing"></a>Roteamento dependente de dados
**Roteamento dependente de dados** é a capacidade de usar os dados em uma consulta para encaminhar a solicitação para um banco de dados apropriado. Trata-se de um padrão fundamental ao trabalhar com bancos de dados fragmentados. O contexto da solicitação também poderá ser usado para encaminhar a solicitação, especialmente se a chave de fragmentação não fizer parte da consulta. Cada consulta específica ou a transação em um aplicativo que usa o roteamento dependentes de dados é restrita a acessar um banco de dados individual por solicitação. Para as ferramentas do Banco de Dados Elástico SQL do Azure, esse encaminhamento é feito com a **[classe ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx)** nos aplicativos ADO.NET.

O aplicativo não precisa controlar a várias cadeias de caracteres de conexão ou locais de BD associados a diferentes subconjuntos de dados no ambiente fragmentado. Em vez disso, o [Gerenciador de Mapa de Fragmento](sql-database-elastic-scale-shard-map-management.md) abre as conexões no banco de dados correto quando necessário, com base nos dados do mapa do fragmento e o valor da chave de fragmentação é o destino da solicitação do aplicativo. A chave é normalmente o *customer_id*, *tenant_id*, *date_key* ou outro identificador específico que é um parâmetro fundamental da solicitação de banco de dados. 

Para obter mais informações, consulte [Dimensionamento do SQL Server com roteamento dependente de dados](https://technet.microsoft.com/library/cc966448.aspx).

## <a name="download-the-client-library"></a>Baixar a biblioteca de cliente
Para obter a classe, vá para a [Biblioteca do Cliente do Banco de Dados Elástico](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). 

## <a name="using-a-shardmapmanager-in-a-data-dependent-routing-application"></a>Usando um ShardMapManager em um aplicativo de roteamento dependente de dados
Aplicativos devem instanciar o **ShardMapManager** durante a inicialização, usando a chamada de fábrica **[GetSQLShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx)**. Neste exemplo, tanto um **ShardMapManager** quanto um **ShardMap** específico que ele contém são inicializados. Este exemplo mostra os métodos GetSqlShardMapManager e [GetRangeShardMap](https://msdn.microsoft.com/library/azure/dn824173.aspx) .

    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString, 
                      ShardMapManagerLoadPolicy.Lazy);
    RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 

### <a name="use-lowest-privilege-credentials-possible-for-getting-the-shard-map"></a>Use as credenciais com o privilégio mais baixo possível para obter o mapa do fragmento
Se um aplicativo não estiver manipulando o mapa de fragmentos, as credenciais usadas no método de fábrica deverão ter permissões somente leitura no banco de dados **Mapa de Fragmento Global** . Essas credenciais são normalmente diferentes das credenciais usadas para abrir conexões com o Gerenciador de mapa do fragmento. Consulte também [Credenciais usadas para acessar a biblioteca de cliente do Banco de Dados Elástico](sql-database-elastic-scale-manage-credentials.md). 

## <a name="call-the-openconnectionforkey-method"></a>Chame o método OpenConnectionForKey
O método **[ShardMap.OpenConnectionForKey](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx)** retorna uma conexão do ADO.NET pronta para emitir executar comandos para o banco de dados apropriado com base no valor do parâmetro **key**. As informações de fragmentos são armazenadas em cache no aplicativo pelo **ShardMapManager**, portanto, essas solicitações normalmente não envolvem uma pesquisa de banco de dados no banco de dados do **Mapa de Fragmentos Global**. 

    // Syntax: 
    public SqlConnection OpenConnectionForKey<TKey>(
        TKey key,
        string connectionString,
        ConnectionOptions options
    )


* O parâmetro **key** é usado como uma chave de pesquisa no mapa de fragmentos para determinar o banco de dados apropriado para a solicitação. 
* **connectionString** é usado para transmitir somente as credenciais do usuário para a conexão desejada. Nenhum nome de banco de dados ou de servidor está incluído nessa *connectionString* , pois o método determinará o banco de dados e o servidor usando o **ShardMap**. 
* **[connectionOptions](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.connectionoptions.aspx)** deve ser definido como **ConnectionOptions.Validate** se um ambiente em que mapas de fragmento podem ser alterados e linhas podem mover para outros bancos de dados como resultado de operações de divisão ou mesclagem. Isso envolve uma consulta curta para o mapa do fragmento local de destino do banco de dados (não para o mapa do fragmento global) antes que a conexão é entregue ao aplicativo. 

Se a validação em relação ao mapa de fragmento local falhar (indicando que o cache está incorreto), o Gerenciador de mapa do fragmento consultará o mapa do fragmento global para obter o novo valor correto para a pesquisa, atualizar o cache e obtenha e retorne a conexão de banco de dados apropriado. 

Use **ConnectionOptions.None** somente quando as alterações de mapeamento de fragmentos não são esperadas enquanto um aplicativo estiver online. Nesse caso, os valores do cache podem ser considerados sempre corretas, e a chamada de validação extra ida e volta ao banco de dados de destino pode ser ignorada com segurança. Isso reduz o tráfego de banco de dados. **connectionOptions** também pode ser definido por meio de um valor em um arquivo de configuração para indicar se as alterações de fragmentação são ou não esperadas durante um período.  

Este exemplo usa o valor de uma chave de inteiro **CustomerID**, usando um objeto **ShardMap** chamado **customerShardMap**.  

    int customerId = 12345; 
    int newPersonId = 4321; 

    // Connect to the shard for that customer ID. No need to call a SqlConnection 
    // constructor followed by the Open method.
    using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, 
        Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
    { 
        // Execute a simple command. 
        SqlCommand cmd = conn.CreateCommand(); 
        cmd.CommandText = @"UPDATE Sales.Customer 
                            SET PersonID = @newPersonID 
                            WHERE CustomerID = @customerID"; 

        cmd.Parameters.AddWithValue("@customerID", customerId); 
        cmd.Parameters.AddWithValue("@newPersonID", newPersonId); 
        cmd.ExecuteNonQuery(); 
    }  

O método **OpenConnectionForKey** retorna uma nova conexão já aberta para o banco de dados correto. Conexões utilizados dessa forma ainda se beneficiar do pool de conexões do ADO.Net. Como transações e solicitações podem ser atendidas por um fragmento por vez, isso deve ser a única modificação necessária em um aplicativo já usando ADO.Net. 

O método **[OpenConnectionForKeyAsync method](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkeyasync.aspx)** também está disponível se o seu aplicativo usa a programação assíncrona com o ADO.NET. Seu comportamento é equivalente ao roteamento dependente de dados do método **[Connection.OpenAsync](https://msdn.microsoft.com/library/hh223688\(v=vs.110\).aspx)** do ADO.Net.

## <a name="integrating-with-transient-fault-handling"></a>Integrando a manipulação de falhas transitórias
É uma melhor prática no desenvolvimento de aplicativos de acesso de dados na nuvem garantir que as falhas transitórias são capturadas pelo aplicativo e que as operações serão repetidas várias vezes antes que ocorra um erro. O tratamento de falha transitória para aplicativos em nuvem é discutido em [Transient Fault Handling](https://msdn.microsoft.com/library/dn440719\(v=pandp.60\).aspx). 

A manipulação de falhas transitórias pode coexistir naturalmente com o padrão de Roteamento dependente de dados. O principal requisito é repetir a solicitação de acesso de dados inteira incluindo o bloqueio **using** que obteve a conexão de roteamento dependente de dados. O exemplo anterior poderia ser reescrito da seguinte maneira (alteração de anotação realçada). 

### <a name="example---data-dependent-routing-with-transient-fault-handling"></a>Exemplo – roteamento com falhas transitórias manipulação dependentes dos dados
<pre><code>int customerId = 12345; 
int newPersonId = 4321; 

<span style="background-color:  #FFFF00">Configuration.SqlRetryPolicy.ExecuteAction(() =&gt; </span> 
<span style="background-color:  #FFFF00">    { </span>
        // Connect to the shard for a customer ID. 
        using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId,  
        Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
        { 
            // Execute a simple command 
            SqlCommand cmd = conn.CreateCommand(); 

            cmd.CommandText = @&quot;UPDATE Sales.Customer 
                            SET PersonID = @newPersonID 
                            WHERE CustomerID = @customerID&quot;; 

            cmd.Parameters.AddWithValue(&quot;@customerID&quot;, customerId); 
            cmd.Parameters.AddWithValue(&quot;@newPersonID&quot;, newPersonId); 
            cmd.ExecuteNonQuery(); 

            Console.WriteLine(&quot;Update completed&quot;); 
        } 
<span style="background-color:  #FFFF00">    }); </span> 
</code></pre>


Pacotes necessários para implementar o tratamento de falhas transitórias são baixados automaticamente quando você compila o aplicativo de exemplo de banco de dados elástico. Pacotes também estão disponíveis separadamente na [Biblioteca Corporativa - Bloco de aplicativo de tratamento de falha transitória](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/). Use a versão 6.0 ou posterior. 

## <a name="transactional-consistency"></a>Consistência transacional
Propriedades transacionais são garantidas para todas as operações locais para um fragmento. Por exemplo, transações enviadas por meio do roteamento dependentes de dados executado dentro do escopo do fragmento de destino para a conexão. Neste momento, não há nenhum recurso fornecido para inscrever-se várias conexões em uma transação e, portanto, não há nenhuma garantia transacional para operações executadas pelos fragmentos.

## <a name="next-steps"></a>Próximas etapas
Para desanexar um fragmento ou anexar um fragmento novamente, consulte [Usando a classe RecoveryManager para corrigir problemas do mapa de fragmentos](sql-database-elastic-database-recovery-manager.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

