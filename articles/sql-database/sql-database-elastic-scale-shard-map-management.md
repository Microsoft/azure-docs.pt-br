---
title: Escalar horizontalmente um banco de dados SQL do Azure | Microsoft Docs
description: "Como usar o ShardMapManager, a biblioteca de cliente do banco de dados elástico"
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: 
ms.assetid: 0e9d647a-9ba9-4875-aa22-662d01283439
ms.service: sql-database
ms.custom: scale out apps
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: ddove
ms.openlocfilehash: f626cf417d8b3f1761f3c900d49039b3ff83b093
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="scale-out-databases-with-the-shard-map-manager"></a>Escale horizontalmente os bancos de dados com o gerenciador do mapa de fragmentos
Para escalar horizontalmente os bancos de dados no SQL Azure, use um gerenciador do mapa de fragmentos. O gerenciador do mapa de fragmentos é um banco de dados especial que mantém informações de mapeamento global sobre todos os fragmentos (bancos de dados) em um conjunto de fragmentos. Os metadados permitem que um aplicativo se conecte ao banco de dados correto com base no valor da **chave de fragmentação**. Além disso, cada fragmento no conjunto contém mapas que acompanham os dados de fragmento local (conhecido como **shardlets**). 

![Gerenciamento de mapa de fragmentos](./media/sql-database-elastic-scale-shard-map-management/glossary.png)

Entender como esses mapas são construídos é essencial para o gerenciamento de mapa de fragmentos. Isso é feito usando a [classe ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx), localizada na [biblioteca de cliente do Banco de Dados Elástico](sql-database-elastic-database-client-library.md) para gerenciar mapas de fragmentos.  

## <a name="shard-maps-and-shard-mappings"></a>Mapas de fragmentos e mapeamentos de fragmentos
Para cada fragmento, você deve selecionar o tipo do mapa de fragmentos a ser criado. A escolha depende da arquitetura do banco de dados: 

1. Um locatário único por banco de dados  
2. Vários locatários por banco de dados (dois tipos):
   1. Mapeamento de lista
   2. Mapeamento de intervalo

Para um modelo de locatário único, crie um mapa de fragmentos de **mapeamento de lista** . O modelo de locatário único atribui um banco de dados por locatário. Esse é um modelo eficaz para desenvolvedores de SaaS, pois simplifica o gerenciamento.

![Mapeamento de lista][1]

O modelo multilocatário atribui vários locatários a um banco de dados individual (e você pode distribuir grupos de locatários entre vários bancos de dados). Use esse modelo quando você esperar que cada locatário tenha necessidades de dados pequenas. Nesse modelo, atribuímos um intervalo de locatários a um banco de dados usando **mapeamento de intervalo**. 

![Mapeamento de intervalo][2]

Ou então, você pode implementar um modelo de banco de dados multilocatário usando um *mapeamento de lista* para atribuir vários locatários a um banco de dados individual. Por exemplo, DB1 é usado para armazenar informações sobre os locatários com IDs 1 e 5, e DB2 armazena dados dos locatários 7 e 10. 

![Vários locatários em um banco de dados individual][3] 

### <a name="supported-net-types-for-sharding-keys"></a>Tipos .Net com suporte para chaves de fragmentação
A Escala Elástica dá suporte aos seguintes tipos de .Net Framework como chaves de fragmentação:

* inteiro
* longo
* guid
* byte[]  
* datetime
* timespan
* datetimeoffset

### <a name="list-and-range-shard-maps"></a>Mapas de fragmentos de lista e intervalo
Mapas de fragmentos podem ser construídos usando **listas de valores de chave de fragmentação individuais** ou **intervalos de valores de chave de fragmentação**. 

### <a name="list-shard-maps"></a>Mapas de fragmentos de lista
**Fragmentos** contêm **shardlets** e o mapeamento de shardlets para fragmentos é mantido por um mapa de fragmentos. Uma **lista de mapas de fragmento** é uma associação entre os valores de chave individuais, que identificam os shardlets e os bancos de dados que servem como fragmentos.  **Mapeamentos de lista** são valores de chaves explícitos e diferentes que podem ser mapeados para o mesmo banco de dados. Por exemplo, a chave 1 é mapeada para o banco de dados A e os valores 3 e 6 fazem referência ao banco de dados B.

| Chave | Local de fragmento |
| --- | --- |
| 1 |Database_A |
| 3 |Database_B |
| 4 |Database_C |
| 6 |Database_B |
| ... |... |

### <a name="range-shard-maps"></a>Mapas de fragmentos de intervalo
Em um **intervalo de mapas de fragmentos**, o intervalo de chaves é descrito por um par **[Valor baixo, Valor alto)**, em que o *Valor baixo* é a chave mínima no intervalo e o *Valor alto* é o primeiro valor maior que o intervalo. 

Por exemplo, **(0, 100)** inclui todos os números inteiros iguais ou maiores que 0 e menores que 100. Observe que vários intervalos podem apontar para o mesmo banco de dados e intervalos separados tem suporte (por exemplo, ambos [100,200) e [400,600) apontam para o banco de dados C no exemplo abaixo.)

| Chave | Local de fragmento |
| --- | --- |
| [1,50) |Database_A |
| [50,100) |Database_B |
| [100,200) |Database_C |
| [400,600) |Database_C |
| ... |... |

Cada uma das tabelas mostradas acima é um exemplo conceitual de um objeto **ShardMap** . Cada linha é um exemplo simplificado de um objeto de **PointMapping** individual (para o mapa do fragmento de lista) ou **RangeMapping** (para o mapa do fragmento de mapa).

## <a name="shard-map-manager"></a>Gerenciador de mapa de fragmentos
Na biblioteca do cliente, o gerenciador de mapa de fragmentos é uma coleção de mapas de fragmentos. Os dados gerenciados por uma instância **ShardMapManager** são mantidos em três locais: 

1. **GSM (Mapa de Fragmentos Global)**: você especifica um banco de dados para servir como o repositório para todos os seus mapas de fragmento e mapeamentos. Procedimentos armazenados e tabelas especiais são criados automaticamente para gerenciar as informações. Geralmente trata-se de um banco de dados pequeno e pouco acessado, que não deve ser usado para outras necessidades do aplicativo. As tabelas estão em um esquema especial chamado **__ShardManagement**. 
2. **LSM (Mapa de Fragmentos Local)**: cada banco de dados que você especificar para ser um fragmento é modificado para conter várias pequenas tabelas e procedimentos especiais armazenados que contêm e gerenciem informações de mapa de fragmentos específicas a esse fragmento. Essas informações são redundantes com as informações de GSM e permitem que o aplicativo validar informações de mapa do fragmento em cache sem colocar nenhuma carga no GSM. O aplicativo usa o LSM para determinar se um mapeamento em cache ainda é válido. As tabelas correspondentes ao LSM em cada fragmento também estão no esquema **__ShardManagement**.
3. **Cache do aplicativo**: cada instância do aplicativo que acessa um objeto **ShardMapManager** mantém um cache na memória local dos seus mapeamentos. Ele armazena informações de roteamento que recentemente foi recuperadas. 

## <a name="constructing-a-shardmapmanager"></a>Construindo um ShardMapManager
Um objeto **ShardMapManager** é construído usando um padrão [factory](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.aspx) . O método **[ShardMapManagerFactory.GetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx)** usa credenciais (incluindo o nome do servidor e o nome do banco de dados que contém o GSM) na forma de uma **ConnectionString** e retorna uma instância de um **ShardMapManager**.  

**Observação:** o **ShardMapManager** deve ser instanciado apenas uma vez por domínio de aplicativo, dentro do código de inicialização de um aplicativo. A criação de instâncias adicionais de ShardMapManager no mesmo domínio de aplicativo resultará em uma maior utilização de memória e CPU do aplicativo. Um **ShardMapManager** pode conter diversos mapas de fragmento. Enquanto um mapa do fragmento único pode ser suficiente para muitos aplicativos, há vezes quando conjuntos diferentes de bancos de dados são usados para esquemas diferentes ou para fins exclusivos e, nesses casos, vários mapas de fragmentação podem ser preferíveis. 

Nesse código, um aplicativo tenta abrir um **ShardMapManager** existente com o [método TryGetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx).  Se os objetos que representam um **ShardMapManager** Global (GSM) ainda não existirem no banco de dados, a biblioteca de cliente os criará no local usando o [método CreateSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager.aspx).

    // Try to get a reference to the Shard Map Manager 
     // via the Shard Map Manager database.  
    // If it doesn't already exist, then create it. 
    ShardMapManager shardMapManager; 
    bool shardMapManagerExists = ShardMapManagerFactory.TryGetSqlShardMapManager(
                                        connectionString, 
                                        ShardMapManagerLoadPolicy.Lazy, 
                                        out shardMapManager); 

    if (shardMapManagerExists) 
     { 
        Console.WriteLine("Shard Map Manager already exists");
    } 
    else
    {
        // Create the Shard Map Manager. 
        ShardMapManagerFactory.CreateSqlShardMapManager(connectionString);
        Console.WriteLine("Created SqlShardMapManager"); 

        shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
            connectionString, 
            ShardMapManagerLoadPolicy.Lazy);

        // The connectionString contains server name, database name, and admin credentials 
        // for privileges on both the GSM and the shards themselves.
    } 

Como alternativa, é possível usar o Powershell para criar um novo Gerenciador de Mapa de Fragmentos. Um exemplo está disponível [aqui](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

## <a name="get-a-rangeshardmap-or-listshardmap"></a>Obter um RangeShardMap ou ListShardMap
Após criar um gerenciador de mapa de fragmentos, você pode obter o [RangeShardMap](https://msdn.microsoft.com/library/azure/dn807318.aspx) ou [ListShardMap](https://msdn.microsoft.com/library/azure/dn807370.aspx) usando o método [TryGetRangeShardMap](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx), [TryGetListShardMap](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx) ou [GetShardMap](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx).

    /// <summary>
    /// Creates a new Range Shard Map with the specified name, or gets the Range Shard Map if it already exists.
    /// </summary>
    public static RangeShardMap<T> CreateOrGetRangeShardMap<T>(ShardMapManager shardMapManager, string shardMapName)
    {
        // Try to get a reference to the Shard Map.
        RangeShardMap<T> shardMap;
        bool shardMapExists = shardMapManager.TryGetRangeShardMap(shardMapName, out shardMap);

        if (shardMapExists)
        {
            ConsoleUtils.WriteInfo("Shard Map {0} already exists", shardMap.Name);
        }
        else
        {
            // The Shard Map does not exist, so create it
            shardMap = shardMapManager.CreateRangeShardMap<T>(shardMapName);
            ConsoleUtils.WriteInfo("Created Shard Map {0}", shardMap.Name);
        }

        return shardMap;
    } 

### <a name="shard-map-administration-credentials"></a>Credenciais de administração de mapa de fragmentos
Os aplicativos que administram e manipulam mapas de fragmentos são diferentes daqueles que utilizam os mapas de fragmentos para encaminhar conexões. 

Para administrar mapas de fragmentos (adicionar ou alterar fragmentos, mapas de fragmentos, mapeamentos de fragmentos etc.), é necessário criar uma instância do **ShardMapManager** usando as **credenciais que têm privilégios de leitura/gravação no banco de dados do GSM e em cada banco de dados que atua como um fragmento**. As credenciais devem permitir gravações nas tabelas no GSM e LSM como informações de mapa do fragmento são inseridas ou alteradas, bem como para criar tabelas LSM em novos fragmentos.  

Veja [Credenciais usadas para acessar a biblioteca de cliente do Banco de Dados Elástico](sql-database-elastic-scale-manage-credentials.md).

### <a name="only-metadata-affected"></a>Apenas os metadados afetados
Os métodos usados para preencher ou alterar os dados de **ShardMapManager** não alteram os dados de usuário armazenados nos próprios fragmentos. Por exemplo, métodos como **CreateShard**, **DeleteShard**, **UpdateMapping** etc., afetam apenas os metadados do mapa de fragmentos. Não remova, adicione ou altere dados de usuário contidos nos fragmentos. Em vez disso, esses métodos destinam-se a ser usado em conjunto com operações separadas, que você pode executar para criar ou remover bancos de dados real ou que move linhas de um fragmento para outro para reequilibrar um ambiente fragmentado.  (A ferramenta de **divisão e mesclagem** incluída com as ferramentas de banco de dados elástico usa essas APIs, além de orquestrar a movimentação de dados real entre fragmentos.) Veja [Escalando com a ferramenta de divisão e mesclagem de Banco de Dados Elástico](sql-database-elastic-scale-overview-split-and-merge.md).

## <a name="populating-a-shard-map-example"></a>Popular um mapa de fragmentos: exemplo
Uma sequência de operações para preencher um mapa de fragmento específicos exemplo é mostrada abaixo. O código executa estas etapas: 

1. Um novo mapa de fragmento é criado dentro de um Gerenciador de mapa do fragmento. 
2. Os metadados para dois fragmentos diferentes é adicionado ao mapa de fragmento. 
3. Uma variedade de mapeamentos de intervalo de chave são adicionados e o conteúdo geral do mapa do fragmento é exibido. 

O código é escrito para que o método possa ser executado novamente caso ocorra um erro. Cada solicitação testa se um fragmento ou mapeamento já existe antes de tentar criá-lo. O código pressupõe que bancos de dados chamados **sample_shard_0**, **sample_shard_1** e **sample_shard_2** já foram criados no servidor referenciado pela cadeia de caracteres **shardServer**. 

    public void CreatePopulatedRangeMap(ShardMapManager smm, string mapName) 
        {            
            RangeShardMap<long> sm = null; 

            // check if shardmap exists and if not, create it 
            if (!smm.TryGetRangeShardMap(mapName, out sm)) 
            { 
                sm = smm.CreateRangeShardMap<long>(mapName); 
            } 

            Shard shard0 = null, shard1=null; 
            // Check if shard exists and if not, 
            // create it (Idempotent / tolerant of re-execute) 
            if (!sm.TryGetShard(new ShardLocation(
                                     shardServer, 
                                     "sample_shard_0"), 
                                     out shard0)) 
            { 
                Shard0 = sm.CreateShard(new ShardLocation(
                                            shardServer, 
                                            "sample_shard_0")); 
            } 

            if (!sm.TryGetShard(new ShardLocation(
                                    shardServer, 
                                    "sample_shard_1"), 
                                    out shard1)) 
            { 
                Shard1 = sm.CreateShard(new ShardLocation(
                                             shardServer, 
                                            "sample_shard_1"));  
            } 

            RangeMapping<long> rmpg=null; 

            // Check if mapping exists and if not,
            // create it (Idempotent / tolerant of re-execute) 
            if (!sm.TryGetMappingForKey(0, out rmpg)) 
            { 
                sm.CreateRangeMapping(
                          new RangeMappingCreationInfo<long>
                          (new Range<long>(0, 50), 
                          shard0, 
                          MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(50, out rmpg)) 
            { 
                sm.CreateRangeMapping(
                         new RangeMappingCreationInfo<long> 
                         (new Range<long>(50, 100), 
                         shard1, 
                         MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(100, out rmpg)) 
            { 
                sm.CreateRangeMapping(
                         new RangeMappingCreationInfo<long>
                         (new Range<long>(100, 150), 
                         shard0, 
                         MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(150, out rmpg)) 
            { 
                sm.CreateRangeMapping(
                         new RangeMappingCreationInfo<long> 
                         (new Range<long>(150, 200), 
                         shard1, 
                         MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(200, out rmpg)) 
            { 
               sm.CreateRangeMapping(
                         new RangeMappingCreationInfo<long> 
                         (new Range<long>(200, 300), 
                         shard0, 
                         MappingStatus.Online)); 
            } 

            // List the shards and mappings 
            foreach (Shard s in sm.GetShards()
                         .OrderBy(s => s.Location.DataSource)
                         .ThenBy(s => s.Location.Database))
            { 
               Console.WriteLine("shard: "+ s.Location); 
            } 

            foreach (RangeMapping<long> rm in sm.GetMappings()) 
            { 
                Console.WriteLine("range: [" + rm.Value.Low.ToString() + ":" 
                        + rm.Value.High.ToString()+ ")  ==>" +rm.Shard.Location); 
            } 
        } 

Como alternativa, você pode usar scripts do PowerShell para alcançar o mesmo resultado. Alguns dos exemplos do PowerShell de exemplo estão disponíveis [aqui](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).     

Depois de mapas de fragmento são preenchidos, aplicativos de acesso de dados podem ser criados ou adaptados para funcionar com os mapas. Não será necessário preencher ou manipular os mapas novamente até que o **layout do mapa** precise ser alterado.  

## <a name="data-dependent-routing"></a>Roteamento dependente de dados
O gerenciador de mapa de fragmento será mais usado em aplicativos que exigem conexões de banco de dados para executar as operações de dados específicos do aplicativo. Essas conexões devem estar associadas ao banco de dados correto. Isso é conhecido como **Roteamento Dependente de Dados**. Para esses aplicativos, instancie um objeto do Gerenciador do mapa de fragmento de fábrica usando as credenciais que têm acesso somente leitura no banco de dados GSM. As solicitações individuais para conexões posteriores fornecerão credenciais necessárias para conectar-se ao banco de dados apropriado do fragmento.

Observe que esses aplicativos (usando o **ShardMapManager** aberto com as credenciais somente leitura) não poderão alterar os mapas ou mapeamentos. Para essas necessidades, crie aplicativos administrativos específicos ou scripts do PowerShell que fornecem credenciais de privilégios mais altos, conforme discutido anteriormente. Veja [Credenciais usadas para acessar a biblioteca de cliente do Banco de Dados Elástico](sql-database-elastic-scale-manage-credentials.md).

Para obter mais detalhes, consulte [Roteamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md). 

## <a name="modifying-a-shard-map"></a>Modificar um mapa de fragmentos
Um mapa do fragmento pode ser alterado de diferentes maneiras. Todos os seguintes métodos de modificar os metadados que descrevem os fragmentos e seus mapeamentos, mas eles não modificam dados dentro de fragmentos fisicamente, nem que criar ou excluir os bancos de dados reais.  Algumas das operações no mapa de fragmento descrito abaixo talvez precisem ser coordenada com ações administrativas que mover fisicamente os dados ou que adicionar e remover bancos de dados que serve como fragmentos.

Esses métodos funcionam juntos como blocos de construção disponíveis para modificar a distribuição global de dados em seu ambiente de banco de dados fragmentado.  

* Para adicionar ou remover fragmentos: use **[CreateShard](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard.aspx)** e **[DeleteShard](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.deleteshard.aspx)** da [classe Shardmap](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.aspx). 
  
    O servidor e o banco de dados que representa o fragmento de destino já devem existir para executar essas operações. Esses métodos não têm qualquer impacto nos bancos de dados, apenas nos metadados no mapa do fragmento.
* Para criar ou remover pontos ou intervalos mapeados para os fragmentos: use **[CreateRangeMapping](https://msdn.microsoft.com/library/azure/dn841993.aspx)**, **[DeleteMapping](https://msdn.microsoft.com/library/azure/dn824200.aspx)** da [classe RangeShardMapping](https://msdn.microsoft.com/library/azure/dn807318.aspx) e **[CreatePointMapping](https://msdn.microsoft.com/library/azure/dn807218.aspx)** de [ListShardMap](https://msdn.microsoft.com/library/azure/dn842123.aspx)
  
    Vários pontos diferentes ou intervalos podem ser mapeados para o mesmo fragmento. Esses métodos afetam somente metadados – eles não afetam os dados que podem já estar presentes em fragmentos. Se for necessário remover os dados do banco de dados para que eles sejam consistentes com as operações **DeleteMapping** , você precisará realizar essas operações separadamente, mas em conjunto com esses métodos.  
* Para dividir intervalos existentes em dois ou mesclar intervalos adjacentes em um: use **[SplitMapping](https://msdn.microsoft.com/library/azure/dn824205.aspx)** e **[MergeMappings](https://msdn.microsoft.com/library/azure/dn824201.aspx)**.  
  
    Observe que as operações de divisão e mesclagem **não alteram o fragmento para o qual os valores de chave são mapeados**. Uma divisão divide um intervalo existente em duas partes, mas deixa ambos como mapeada para o mesmo fragmento. Uma mesclagem opera em dois intervalos adjacentes que já são mapeados para o mesmo fragmento, juntando-os em um único intervalo.  A movimentação dos próprios pontos ou intervalos entre fragmentos precisa ser coordenada com **UpdateMapping** em conjunto com a movimentação real de dados.  É possível usar o serviço de **Divisão/Mesclagem** que faz parte das ferramentas de banco de dados elástico para coordenar as alterações de mapa de fragmentos com a movimentação de dados, quando a movimentação for necessária. 
* Para remapear (ou mover) pontos ou intervalos individuais para fragmentos diferentes: use **[UpdateMapping](https://msdn.microsoft.com/library/azure/dn824207.aspx)**.  
  
    Já que pode ser necessário mover os dados de um fragmento para outro para que eles sejam consistentes com as operações **UpdateMapping** , você precisará executar essa movimentação separadamente, mas em conjunto com esses métodos.
* Para deixar mapeamentos online e offline: use **[MarkMappingOffline](https://msdn.microsoft.com/library/azure/dn824202.aspx)** e **[MarkMappingOnline](https://msdn.microsoft.com/library/azure/dn807225.aspx)** para controlar o estado online de um mapeamento. 
  
    Algumas operações nos mapeamentos de fragmentos são permitidas apenas quando um mapeamento está em um estado “offline”, incluindo **UpdateMapping** e **DeleteMapping**. Quando um mapeamento estiver offline, uma solicitação dependente de dados com base em uma chave incluída nesse mapeamento retornará um erro. Além disso, quando um intervalo primeiro fica offline, todas as conexões para o fragmento afetado serão eliminadas automaticamente para evitar resultados inconsistentes ou incompletos para consultas direcionadas com intervalos que está sendo alterados. 

Mapeamentos são objetos imutáveis no .Net.  Todos os métodos acima que alteram mapeamentos também invalidam todas as referências a eles em seu código. Para facilitar a execução de sequências de operações que alteram o estado do mapeamento, todos os métodos que alteram um mapeamento retornam uma nova referência de mapeamento, para que as operações possam ser encadeadas. Por exemplo, para excluir um mapeamento existente em sm shardmap que contém a chave 25, você pode executar o seguinte: 

        sm.DeleteMapping(sm.MarkMappingOffline(sm.GetMappingForKey(25)));

## <a name="adding-a-shard"></a>Adicionar um fragmento
Geralmente, os aplicativos precisam simplesmente adicionar novos fragmentos para lidar com dados que são esperados de novas chaves ou intervalos de chaves para um mapa do fragmento que já existe. Por exemplo, um aplicativo fragmentado por ID de locatário talvez tenha provisionar um novo fragmento para um novo locatário ou dados mensalmente fragmentados talvez precisem de um novo fragmento provisionado antes do início de cada novo mês. 

Se o novo intervalo de valores de chave já não é parte de um mapeamento existente e nenhuma movimentação de dados é necessária, é muito simples adicionar o novo fragmento e associar a nova chave ou o intervalo para esse fragmento. Para obter detalhes sobre como adicionar novos fragmentos, veja [Adicionar um novo fragmento](sql-database-elastic-scale-add-a-shard.md).

Para cenários que exigem a movimentação de dados, no entanto, a ferramenta de divisão/mesclagem é necessária para orquestrar a movimentação dos dados entre os fragmentos em conjunto com as atualizações necessárias do mapa de fragmentos. Para obter detalhes sobre como usar a ferramenta de divisão/mesclagem, confira [Visão geral de divisão/mesclagem](sql-database-elastic-scale-overview-split-and-merge.md) 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-shard-map-management/listmapping.png
[2]: ./media/sql-database-elastic-scale-shard-map-management/rangemapping.png
[3]: ./media/sql-database-elastic-scale-shard-map-management/multipleonsingledb.png
