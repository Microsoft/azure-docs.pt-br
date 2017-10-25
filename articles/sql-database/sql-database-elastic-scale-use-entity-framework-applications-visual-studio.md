---
title: "Usando a biblioteca de cliente do banco de dados elástico com o Entity Framework | Microsoft Docs"
description: "Usar a biblioteca de cliente do Banco de Dados Elástico e o Entity Framework para bancos de dados de codificação"
services: sql-database
documentationcenter: 
manager: jhubbard
author: torsteng
editor: 
ms.assetid: b9c3065b-cb92-41be-aa7f-deba23e7e159
ms.service: sql-database
ms.custom: scale out apps
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: torsteng
ms.openlocfilehash: 2f0bff394c1e11a270cb324be5a1a45e9e531d7f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="elastic-database-client-library-with-entity-framework"></a>Biblioteca cliente do Banco de Dados Elástico com Entity Framework
Este documento mostra as alterações em um aplicativo do Entity Framework necessárias para integrar os recursos das [ferramentas de Banco de Dados Elástico](sql-database-elastic-scale-introduction.md). O foco está na composição do [gerenciamento do mapa de fragmentos](sql-database-elastic-scale-shard-map-management.md) e no [roteamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md) com a abordagem do **Entity Framework Code First**. O tutorial [Code First – New Database](http://msdn.microsoft.com/data/jj193542.aspx) para o EF serve como exemplo de execução para este documento. O código de exemplo que acompanha este documento faz parte do conjunto de ferramentas de banco de dados elástico de exemplos código do Visual Studio.

## <a name="downloading-and-running-the-sample-code"></a>Baixar e executar o código de exemplo
Para baixar o código para este artigo:

* É necessário o Visual Studio 2012 ou posterior. 
* Baixe as [Ferramentas de Banco de Dados Elástico para Azure SQL – amostra de integração ao Entity Framework](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-bae904ba) do MSDN. Descompacte o exemplo em um local de sua escolha.
* Inicie o Visual Studio. 
* No Visual Studio, selecione Arquivo -> Abrir Projeto/Solução. 
* Na caixa de diálogo **Abrir Projeto**, navegue até o exemplo que você baixou e selecione **EntityFrameworkCodeFirst.sln** para abrir a amostra. 

Para executar o exemplo, você precisa criar três bancos de dados vazios no Banco de Dados SQL do Azure:

* Banco de dados do Gerenciador do mapa do fragmento
* Banco de dados do fragmento 1
* Banco de dados do fragmento 2

Depois de criar esses bancos de dados, preencha os espaços reservados em **Program.cs** com o nome do servidor do Banco de Dados SQL do Azure, os nomes dos banco de dados e suas credenciais para conectar-se aos bancos de dados. Compile a solução no Visual Studio. O Visual Studio baixará os pacotes NuGet necessários para a biblioteca de cliente do banco de dados elástico, Entity Framework e tratamento de Falha Transitória como parte do processo de compilação. Certifique-se de que a restauração de pacotes NuGet está habilitada para sua solução. Você pode habilitar essa configuração clicando com o botão direito no arquivo de solução no Gerenciador de Soluções do Visual Studio. 

## <a name="entity-framework-workflows"></a>Fluxos de trabalho do Entity Framework
Os desenvolvedores do Entity Framework dependem de um dos seguintes quatro fluxos de trabalho para criar aplicativos e para garantir a persistência de objetos de aplicativo: 

* **Code First (Novo banco de dados)**: o desenvolvedor do EF cria o modelo no código do aplicativo e, em seguida, o EF gera o banco de dados por meio dele. 
* **Code First (Banco de dados existente)**: o desenvolvedor deixa o EF criar código do aplicativo para o modelo por meio de um banco de dados existente.
* **Model First**: o desenvolvedor cria o modelo no designer de EF e, em seguida, o EF cria o banco de dados por meio do modelo.
* **Database First**: o desenvolvedor usa ferramentas do EF para inferir o modelo a por meio de um banco de dados existente. 

Todas essas abordagens contam com a classe DbContext para gerenciar, transparentemente, conexões de banco de dados e o esquema de banco de dados para um aplicativo. Como abordaremos em mais detalhes posteriormente neste documento, construtores diferentes na classe base DbContext permitem diferentes níveis de controle sobre a criação de conexão, a criação de inicialização e esquema de banco de dados. Desafios surgem principalmente do fato de que o gerenciamento de conexão de banco de dados fornecido pelo EF faz interseção com os recursos de gerenciamento de conexão de interfaces de roteamento dependentes de dados fornecidos pela biblioteca de cliente de banco de dados elástico. 

## <a name="elastic-database-tools-assumptions"></a>Suposições sobre ferramentas de banco de dados elástico
Para obter definições de termos, consulte o [Glossário de ferramentas do Banco de Dados Elástico](sql-database-elastic-scale-glossary.md).

Com a biblioteca de cliente de banco de dados elástico, você define partições dos seus dados de aplicativo chamados shardlets. Os shardlets são identificados por uma chave de fragmentação e são mapeados para bancos de dados específicos. Um aplicativo pode ter tantos bancos de dados quanto necessários e distribuir shardlets para fornecer capacidade ou desempenho suficientes, considerando os requisitos de negócio atuais. O mapeamento de valores chave de fragmentação para os bancos de dados é armazenado por um mapa de fragmentos fornecido pelas APIs de banco de dados elástico. Chamamos essa funcionalidade de **Gerenciamento de Mapa de Fragmentos**ou SMM (abreviada do inglês: Shard Map Management). O mapa do fragmento também serve como o agente de conexões de banco de dados para solicitações que carregam uma chave de fragmentação. Chamamos essa funcionalidade de **roteamento dependente de dados**. 

O gerenciador de mapa de fragmentos protege os usuários contra exibições inconsistentes nos dados do shardlet que podem ocorrer quando operações simultâneas de gerenciamento de shardlet (ex.: realocar dados de um fragmento para outro) estão acontecendo. Para fazer isso, os mapas de fragmentos gerenciados pela biblioteca de cliente intermediam as conexões de banco de dados para um aplicativo. Isso permite que a funcionalidade de mapa do fragmento interrompa automaticamente uma conexão de banco de dados quando as operações de gerenciamento de fragmento podem afetar o shardlet para o qual a conexão foi criado. Essa abordagem precisa se integrar com algumas funcionalidades do EF, como a criação de novas conexões de uma existente para verificar a existência do banco de dados. Em geral, nossa observação é a de que os construtores DbContext padrão só funcionam confiavelmente para conexões fechadas de banco de dados que podem ser clonadas com segurança para funcionar no EF. Diferentemente, o princípio de design do banco de dados elástico é de apenas intermediar as conexões abertas. É possível imaginar que fechar uma conexão intermediada pela biblioteca de cliente antes de entregá-la ao EF DbContext pode resolver esse problema. No entanto, ao fechar a conexão e confiar no EF para abri-la novamente, ela perde as verificações de consistência e de validação executadas pela biblioteca. No entanto, a funcionalidade de migrações no EF, usa essas conexões para gerenciar o esquema de banco de dados subjacente de forma transparente para o aplicativo. O ideal seria manter e combinar todos esses recursos de biblioteca de cliente do banco de dados elástico e o EF no mesmo aplicativo. A seção a seguir aborda essas propriedades e requisitos mais detalhadamente. 

## <a name="requirements"></a>Requisitos
Ao trabalhar com a biblioteca de cliente do banco de dados elástico e de APIs do Entity Framework, queremos manter as seguintes propriedades: 

* **Escala vertical**: para adicionar ou remover bancos de dados da camada de dados do aplicativo fragmentado, conforme necessário para as demandas de capacidade do aplicativo. Isso significa controle sobre a criação e a exclusão de bancos de dados e a utilização de APIs do gerenciador de mapa de fragmento de banco de dados elástico para gerenciar bancos de dados e mapeamentos de shardlets. 
* **Consistência**: o aplicativo utiliza a fragmentação e usa os recursos de roteamento dependente de dados da biblioteca de cliente. Para evitar corrompimento ou resultados incorretos de consulta, as conexões são intermediadas por meio do gerenciador de mapa de fragmentos. Isso também mantém a consistência e a validação.
* **Code First**: para reter a conveniência do paradigma de code first do EF. No Code First, as classes do aplicativo são mapeadas de forma transparente para as estruturas de banco de dados subjacentes. O código do aplicativo interage com DbSets que mascaram a maioria dos aspectos envolvidos no processamento do banco de dados subjacente.
* **Esquema**: o Entity Framework lida com a criação de esquema do banco de dados inicial e com a evolução de esquemas subsequentes por meio de migrações. Mantendo esses recursos, é fácil adaptar seu aplicativo à medida que os dados evoluem. 

As diretrizes a seguir ensinam como atender a esses requisitos para aplicativos Code First usando as ferramentas de banco de dados elástico. 

## <a name="data-dependent-routing-using-ef-dbcontext"></a>Roteamento dependente de dados usando o EF DbContext
Conexões de banco de dados com o Entity Framework são normalmente gerenciadas por meio de subclasses do **DbContext**. Crie essas subclasses derivando por meio do **DbContext**. É aqui que você define seus **DbSets** que implementam as coleções de banco de dados de backup dos objetos CLR para seu aplicativo. No contexto do roteamento dependente de dados, podemos identificar várias propriedades úteis que não necessariamente valem para outros cenários de aplicativo EF código primeiro: 

* O banco de dados já existe e foi registrado no mapa de fragmento de banco de dados elástico. 
* O esquema do aplicativo já foi implantado no banco de dados (explicado abaixo). 
* Conexões de roteamento dependentes de dados no banco de dados são intermediadas pelo mapa de fragmentos. 

Para integrar o **DbContexts** com o roteamento dependente de dados para expansão:

1. Crie conexões físicas de banco de dados por meio das interfaces de cliente do banco de dados elástico do gerenciador de mapa de fragmentos. 
2. Encapsule a conexão com a subclasse **DbContext**
3. Passe a conexão para baixo para as classes base **DbContext** para garantir que todo o processamento no lado do EF também aconteça. 

O código de exemplo a seguir ilustra essa abordagem. (Esse código também está no projeto do Visual Studio que acompanha este artigo)

    public class ElasticScaleContext<T> : DbContext
    {
    public DbSet<Blog> Blogs { get; set; }
    …

        // C'tor for data dependent routing. This call will open a validated connection 
        // routed to the proper shard by the shard map manager. 
        // Note that the base class c'tor call will fail for an open connection
        // if migrations need to be done and SQL credentials are used. This is the reason for the 
        // separation of c'tors into the data-dependent routing case (this c'tor) and the internal c'tor for new shards.
        public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
            : base(CreateDDRConnection(shardMap, shardingKey, connectionStr), 
            true /* contextOwnsConnection */)
        {
        }

        // Only static methods are allowed in calls into base class c'tors.
        private static DbConnection CreateDDRConnection(
        ShardMap shardMap, 
        T shardingKey, 
        string connectionStr)
        {
            // No initialization
            Database.SetInitializer<ElasticScaleContext<T>>(null);

            // Ask shard map to broker a validated connection for the given key
            SqlConnection conn = shardMap.OpenConnectionForKey<T>
                                (shardingKey, connectionStr, ConnectionOptions.Validate);
            return conn;
        }    

## <a name="main-points"></a>Principais pontos
* Um novo construtor substitui o construtor padrão na subclasse DbContext 
* O novo construtor usa os argumentos necessários para roteamento dependente de dados por meio da biblioteca de cliente de banco de dados elástico:
  
  * o mapa do fragmento para acessar as interfaces de roteamento dependentes de dados,
  * a chave de fragmentação para identificar o shardlet,
  * uma cadeia de caracteres de conexão com as credenciais para a conexão de roteamento dependentes de dados para o fragmento. 
* A chamada para o construtor da classe base leva um desvio em um método estático que executa todas as etapas necessárias para roteamento dependente de dados. 
  
  * Ele usa a chamada OpenConnectionForKey das interfaces do cliente de banco de dados elástico no mapa de fragmento para estabelecer uma conexão aberta.
  * O mapa do fragmento cria a conexão aberta ao fragmento que mantém o shardlet para a chave de fragmentação determinada.
  * Essa conexão aberta é passada para o construtor da classe base do DbContext para indicar que essa conexão deve ser usada pelo EF em vez de deixar o EF criar uma nova conexão automaticamente. Dessa forma, a conexão será marcada pela API do cliente de banco de dados elástico para poder garantir a consistência em operações de gerenciamento de mapa de fragmentos.

Use o novo construtor para sua subclasse DbContext em vez do construtor padrão em seu código. Aqui está um exemplo: 

    // Create and save a new blog.

    Console.Write("Enter a name for a new blog: "); 
    var name = Console.ReadLine(); 

    using (var db = new ElasticScaleContext<int>( 
                            sharding.ShardMap,  
                            tenantId1,  
                            connStrBldr.ConnectionString)) 
    { 
        var blog = new Blog { Name = name }; 
        db.Blogs.Add(blog); 
        db.SaveChanges(); 

        // Display all Blogs for tenant 1 
        var query = from b in db.Blogs 
                    orderby b.Name 
                    select b; 
     … 
    }

O novo construtor abre a conexão para o fragmento que mantém os dados para o shardlet identificado pelo valor de **tenantid1**. O código no bloco **using** permanece inalterado para acessar o **DbSet** para blogs usando o EF no fragmento para **tenantid1**. Isso altera a semântica para o código do bloco em uso, de modo que todas as operações de banco de dados sejam agora abrangidas pelo fragmento onde o **tenantid1** é mantido. Por exemplo, uma consulta LINQ sobre os blogs **DbSet** apenas retornaria blogs armazenados no fragmento atual, mas não os armazenados em outros fragmentos.  

#### <a name="transient-faults-handling"></a>Tratamento de falhas transitórias
A equipe Microsoft Patterns & Practices publicou o [Bloco de aplicativos de tratamento de falhas transitórias](https://msdn.microsoft.com/library/dn440719.aspx). A biblioteca é usada com a biblioteca de cliente de escala elástica em combinação com o EF. No entanto, certifique-se de que qualquer exceção transitória retorne para um local onde podemos garantir que o novo construtor está sendo usado após uma falha temporária para que qualquer nova tentativa de conexão seja feita usando os construtores que podemos ter ajustado. Caso contrário, uma conexão com o fragmento correto não é garantida e não há nenhuma garantia que a conexão seja mantida enquanto ocorrem alterações no mapa do fragmento. 

O exemplo de código a seguir ilustra como uma política de repetição SQL pode ser usada em torno dos novos construtores de subclasse **DbContext** : 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
    { 
        using (var db = new ElasticScaleContext<int>( 
                                sharding.ShardMap,  
                                tenantId1,  
                                connStrBldr.ConnectionString)) 
            { 
                    var blog = new Blog { Name = name }; 
                    db.Blogs.Add(blog); 
                    db.SaveChanges(); 
            … 
            } 
        }); 

**SqlDatabaseUtils.SqlRetryPolicy** no código acima é definido como um **SqlDatabaseTransientErrorDetectionStrategy** com uma contagem de repetições de 10, com tempo de espera de 5 segundos entre as tentativas. Essa abordagem é semelhante ás diretrizes para o EF e transações iniciadas pelo usuário (consulte [Limitações com estratégias de repetição de execução (EF6 em diante)](http://msdn.microsoft.com/data/dn307226). Ambas as situações exigem que o programa aplicativo controle o escopo ao qual a exceção transitória retorna: para reabrir a transação, ou (conforme mostrado) recriar o contexto do construtor apropriado que usa as bibliotecas de cliente de banco de dados elástico.

A necessidade de controlar quais exceções temporárias levaríamos no escopo também impede o uso da **SqlAzureExecutionStrategy** incorporada que acompanha o EF. **SqlAzureExecutionStrategy** reabriria uma conexão, mas não usa **OpenConnectionForKey** e, portanto, ignora a validação que é executada como parte da chamada **OpenConnectionForKey**. Em vez disso, o exemplo de código usa a **DefaultExecutionStrategy** incorporada que também vem com o EF. Em vez de **SqlAzureExecutionStrategy**, ela funciona corretamente em combinação com a política de repetição de tratamento de falhas transitórias. A política  de execução é definida na classe **ElasticScaleDbConfiguration** . Observe que decidimos não usar **DefaultSqlExecutionStrategy**,pois ele sugere usar **SqlAzureExecutionStrategy** se ocorrerem exceções temporárias, o que poderia levar a um comportamento incorreto conforme discutido. Para obter mais informações sobre as políticas de repetição diferentes e EF, consulte [Resiliência de conexão no EF](http://msdn.microsoft.com/data/dn456835.aspx).     

#### <a name="constructor-rewrites"></a>O construtor reescreve
Os exemplos de código acima ilustram as reescritas do construtor padrão necessárias para seu aplicativo para usar roteamento dependente de dados com o Entity Framework. A tabela a seguir generaliza essa abordagem para outros construtores. 

| Construtor atual | Construtor regravado para dados | Construtor base | Observações |
| --- | --- | --- | --- |
| MyContext() |ElasticScaleContext(ShardMap, TKey) |DbContext(DbConnection, bool) |A conexão precisa ser uma função de mapa do fragmento e a chave de roteamento dependente de dados. É necessário contornar a criação de conexão automática do EF e usar, ao invés disso, o mapa do fragmento para arranjar a conexão. |
| MyContext(string) |ElasticScaleContext(ShardMap, TKey) |DbContext(DbConnection, bool) |A conexão é uma função de mapa do fragmento e a chave de roteamento de dependente de dado. Uma cadeia de caracteres de conexão ou nome fixo de banco de dados não funcionará enquanto desviam a validação pelo mapa do fragmento. |
| MyContext(DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext(DbConnection, DbCompiledModel, bool) |A conexão será criada para o determinado mapa do fragmento e chave de fragmentação com o modelo fornecido. O modelo compilado será passado para o construtor base. |
| MyContext(DbConnection, bool) |ElasticScaleContext(ShardMap, TKey, bool) |DbContext(DbConnection, bool) |A conexão precisa ser inferida a partir do mapa do fragmento e a chave. Ele não pode ser fornecido como uma entrada (a menos que essa entrada já esteja usando o mapa do fragmento e a chave). O valor booliano será passado. |
| MyContext(string, DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext(DbConnection, DbCompiledModel, bool) |A conexão precisa ser inferida a partir do mapa do fragmento e a chave. Ela não pode ser fornecida como uma entrada (a menos que essa entrada esteja usando o mapa do fragmento e a chave). O modelo compilado será passado. |
| MyContext(ObjectContext, bool) |ElasticScaleContext(ShardMap, TKey, ObjectContext, bool) |DbContext(ObjectContext, bool) |O novo construtor precisa garantir que qualquer conexão no ObjectContext passada como entrada é redirecionado para uma conexão gerenciada pela Escala Elástica. Uma discussão detalhada sobre ObjectContexts está além do escopo deste documento. |
| MyContext(DbConnection, DbCompiledModel,bool) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel, bool) |DbContext(DbConnection, DbCompiledModel, bool); |A conexão precisa ser inferida a partir do mapa do fragmento e a chave. A conexão não pode ser fornecido como uma entrada (a menos que essa entrada já esteja usando o mapa do fragmento e a chave). Modelo e boolianos são passados para o construtor de classe base. |

## <a name="shard-schema-deployment-through-ef-migrations"></a>Implantação de esquema de fragmento por meio de migrações do EF
Gerenciamento automático de esquema é uma conveniência fornecida pelo Entity Framework. No contexto do aplicativo que usam ferramentas de banco de dados elástico, queremos manter essa capacidade de provisionar automaticamente o esquema para fragmentos recém criados quando os bancos de dados são adicionados ao aplicativo fragmentado. O caso de uso primário é aumentar a capacidade na camada de dados para aplicativos fragmentados usando o EF. Contar com recursos do EF para gerenciamento de esquema reduz o esforço de administração de banco de dados com um aplicativo fragmentado criado no EF. 

A implantação de esquema por meio de migrações EF funciona melhor em **conexões não abertas**. Isso é diferente do cenário de roteamento dependente de dados que se baseia na conexão aberta fornecida pela API do cliente de banco de dados elástico. Outra diferença é o requisito de consistência: apesar de desejável para garantir a consistência em todas as conexões de roteamento dependentes de dados para se proteger contra manipulação de mapa do fragmento simultânea, não é uma preocupação com a implantação de esquema inicial para um novo banco de dados que ainda não tenha sido registrado no mapa do fragmento e que ainda não tenha sido alocado para manter shardlets. Portanto, podemos contar com conexões de banco de dados regulares para esses cenários, em vez de roteamento dependente de dados.  

Isso leva a uma abordagem em que a implantação de esquema por meio de migrações do EF está intimamente ligada com o registro do novo banco de dados como um fragmento no mapa do fragmento do aplicativo. Isso depende dos seguintes pré-requisitos: 

* O banco de dados já foi criado. 
* O banco de dados está vazio – ele não mantém nenhum esquema de usuário e nenhum dado de usuário.
* O banco de dados ainda não pode ser acessado através das APIs de cliente de banco de dados elástico para roteamento dependente de dados. 

Com esses pré-requisitos, podemos criar um **SqlConnection** regular não aberto para mandar as migrações do EF para implantação de esquema. O exemplo de código a seguir ilustra essa abordagem. 

        // Enter a new shard - i.e. an empty database - to the shard map, allocate a first tenant to it  
        // and kick off EF intialization of the database to deploy schema 

        public void RegisterNewShard(string server, string database, string connStr, int key) 
        { 

            Shard shard = this.ShardMap.CreateShard(new ShardLocation(server, database)); 

            SqlConnectionStringBuilder connStrBldr = new SqlConnectionStringBuilder(connStr); 
            connStrBldr.DataSource = server; 
            connStrBldr.InitialCatalog = database; 

            // Go into a DbContext to trigger migrations and schema deployment for the new shard. 
            // This requires an un-opened connection. 
            using (var db = new ElasticScaleContext<int>(connStrBldr.ConnectionString)) 
            { 
                // Run a query to engage EF migrations 
                (from b in db.Blogs 
                    select b).Count(); 
            } 

            // Register the mapping of the tenant to the shard in the shard map. 
            // After this step, data-dependent routing on the shard map can be used 

            this.ShardMap.CreatePointMapping(key, shard); 
        } 


Este exemplo mostra o método **RegisterNewShard** que registra o fragmento no mapa de fragmentos, implanta o esquema por meio de migrações do EF e armazena um mapeamento de uma chave de fragmentação para o fragmento. Ele se baseia em um construtor da subclasse **DbContext** (**ElasticScaleContext** no exemplo) que usa uma cadeia de conexão SQL como entrada. O código desse construtor é direto, como mostra o exemplo a seguir: 

        // C'tor to deploy schema and migrations to a new shard 
        protected internal ElasticScaleContext(string connectionString) 
            : base(SetInitializerForConnection(connectionString)) 
        { 
        } 

        // Only static methods are allowed in calls into base class c'tors 
        private static string SetInitializerForConnection(string connnectionString) 
        { 
            // We want existence checks so that the schema can get deployed 
            Database.SetInitializer<ElasticScaleContext<T>>( 
        new CreateDatabaseIfNotExists<ElasticScaleContext<T>>()); 

            return connnectionString; 
        } 

Alguém pode ter usado a versão do construtor herdado da classe base. Mas o código precisa garantir que o inicializador padrão do EF é usado na conexão. Portanto, o curto desvio para o método estático, antes de chamar o construtor da classe base com a cadeia de conexão. Observe que o registro de fragmentos deve ser executado em um domínio ou processo de aplicativo diferente para garantir que as configurações do inicializador para o EF não entrem em conflito. 

## <a name="limitations"></a>Limitações
As abordagens descritas neste documento envolvem algumas limitações: 

* Aplicativos de EF que utilizem o **LocalDb** primeiro precisam migrar para um banco de dados SQL Server normal antes de usar a biblioteca de cliente de banco de dados elástico. Não é possível escalar verticalmente um aplicativo por meio de fragmentação com Escala Elástica com o **LocalDb**. Observe que o desenvolvimento ainda pode usar **LocalDb**. 
* As alterações para o aplicativo que implicam alterações de esquema do banco de dados precisam passar pelas migrações do EF em todos os fragmentos. O código de exemplo para este documento não mostra como fazer isso. Considere o uso de Update-Database com um parâmetro ConnectionString para iterar em todos os fragmentos; ou extraia o script T-SQL para a migração pendente usando Update-Database com a opção -Script e aplique o script T-SQL aos seus fragmentos.  
* Dada a solicitação, presume-se que todo o processamento do banco de dados está contido dentro de um único fragmento, conforme identificado pela chave de fragmentação fornecida pela solicitação. No entanto, esse pressuposto nem sempre é verdadeiro. Por exemplo, quando não é possível disponibilizar uma chave de fragmentação. Para resolver isso, a biblioteca de cliente fornece a classe **MultiShardQuery** que implementa uma abstração de conexão para consultas em vários fragmentos. Aprender a usar o **MultiShardQuery** combinado com o EF está além do escopo deste documento

## <a name="conclusion"></a>Conclusão
Através das etapas descritas neste documento, os aplicativos do EF podem usar a funcionalidade da biblioteca de cliente de banco de dados elástico para roteamento dependente por construtores de refatoração das subclasses **DbContext** usadas no aplicativo EF. Isso limita as alterações necessárias para os locais onde as classes do **DbContext** já existem. Além disso, aplicativos do EF podem continuar a se beneficiar com a implantação automática do esquema, combinando as etapas que invocam as migrações necessárias do EF com o registro de novos fragmentos e mapeamentos no mapa de fragmentos. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-use-entity-framework-applications-visual-studio/sample.png
