---
title: "Aplicativos multilocatários com ferramentas de banco de dados elástico e segurança em nível de linha"
description: "Saiba como usar as ferramentas de banco de dados elástico com segurança em nível de linha para criar um aplicativo com uma camada de dados altamente dimensionável no Banco de Dados SQL que dá suporte a fragmentos multilocatários."
metakeywords: azure sql database elastic tools multi tenant row level security rls
services: sql-database
documentationcenter: 
manager: jhubbard
author: tmullaney
ms.assetid: e72d3cfe-e9be-4326-b776-9c6d96c0a18e
ms.service: sql-database
ms.custom: scale out apps
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/27/2016
ms.author: thmullan;torsteng
ms.openlocfilehash: 73f1210b8d1f5ceca8fac9534d498bdc23d96d48
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="multi-tenant-applications-with-elastic-database-tools-and-row-level-security"></a>Aplicativos multilocatários com ferramentas de banco de dados elástico e segurança em nível de linha
As [ferramentas de banco de dados elástico](sql-database-elastic-scale-get-started.md) e a [RLS (segurança em nível de linha)](https://msdn.microsoft.com/library/dn765131) oferecem um conjunto avançado de funcionalidades para escalar a camada de dados de um aplicativo multilocatário com um Banco de Dados SQL do Azure de maneira flexível e eficiente. Confira [Padrões de design para aplicativos SaaS multilocatário com o Banco de Dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md) para saber mais. 

Este artigo ilustra como usar essas tecnologias em conjunto para criar um aplicativo com uma camada de dados altamente escalonável e que dá suporte a fragmentos multilocatários, usando o **ADO.NET SqlClient** e/ou o **Entity Framework**.  

* **Ferramentas de banco de dados elástico** permitem que os desenvolvedores expandir a camada de dados de um aplicativo por meio de práticas de fragmentação padrão do setor usando um conjunto de bibliotecas .NET e modelos de serviço do Azure. Gerenciar fragmentos usando a Biblioteca Cliente do Banco de Dados Elástico ajuda a automatizar e simplificar muitas das tarefas infraestruturais normalmente associadas à fragmentação. 
* **segurança em nível de linha** permite aos desenvolvedores armazenar dados para vários locatários no mesmo banco de dados usando políticas de segurança para filtrar linhas que não pertencem ao locatário que está executando uma consulta. Centralizar a lógica de acesso com RLS no banco de dados, em vez de no aplicativo, simplifica a manutenção e reduz o risco de erros conforme a base de código de um aplicativo é expandida. 

Ao usar esses recursos em conjunto, um aplicativo pode desfrutar de ganhos de eficiência e economia de custo armazenando dados para vários locatários no mesmo banco de dados de fragmentos. Ao mesmo tempo, um aplicativo ainda terá a flexibilidade para oferecer fragmentos isolados de um locatário para locatários "premium" que exigem garantias de desempenho mais rígidas, já que os fragmentos multilocatários não garantem a distribuição uniforme dos recursos entre os locatários.  

Em resumo, as APIs de [roteamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md) da biblioteca cliente do banco de dados elástico são conectadas automaticamente ao banco de dados de fragmentos correto que contém sua chave de fragmentação (geralmente um "TenantId"). Após o estabelecimento da conexão, uma política de segurança RLS no banco de dados garante que os locatários possam acessar somente as linhas que contêm seu TenantId. Presume-se que todas as tabelas contenham uma coluna TenantId para indicar quais linhas pertencem a cada locatário. 

![Arquitetura de aplicativo de blog][1]

## <a name="download-the-sample-project"></a>Baixar o projeto de exemplo
### <a name="prerequisites"></a>Pré-requisitos
* Usar o Visual Studio (2012 ou superior) 
* Criar três Bancos de Dados SQL do Azure 
* Baixar o projeto de exemplo: [Ferramentas de Banco de Dados Elástico para o SQL do Azure - Fragmentos Multilocatários](http://go.microsoft.com/?linkid=9888163)
  * Preencha as informações dos seus bancos de dados no início do **Program.cs** 

Esse projeto expande o descrito em [Ferramentas de Banco de Dados Elástico para o SQL do Azure - Integração com o Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) adicionando suporte para bancos de dados de fragmentos multilocatários. Ele cria um aplicativo de console simples para a criação de blogs e postagens, com quatro locatários e dois bancos de dados de fragmentos multilocatários, conforme é ilustrado no diagrama acima. 

Compile e execute o aplicativo. Isso inicializará o gerenciador de mapas de fragmentos das ferramentas de banco de dados elástico e executará os seguintes testes: 

1. Usando o Entity Framework e o LINQ, crie um novo blog e exiba todos os blogs para cada locatário
2. Usando o ADO.NET SqlClient, exiba todos os blogs para um locatário
3. Tente inserir um blog para o locatário errado para verificar se um erro será gerado  

Observe que, como a RLS ainda não foi habilitada nos bancos de dados de fragmentos, cada um desses testes revela um problema: locatários podem ver blogs que não pertencem a eles e o aplicativo não é impedido de inserir um blog para o locatário errado. O restante deste artigo descreve como resolver esses problemas impondo o isolamento de locatários com RLS. Há duas etapas: 

1. **Camada de aplicativo**: modifique o código do aplicativo para sempre definir a TenantId atual em SESSION_CONTEXT depois de abrir uma conexão. O projeto de exemplo já fez isso. 
2. **Camada de dados**: crie uma política de segurança RLS em cada banco de dados de fragmentos para filtrar as linhas com base na TenantId armazenada em SESSION_CONTEXT. Você precisará fazer isso para cada um dos seus bancos de dados de fragmentos, caso contrário, linhas em fragmentos multilocatários não serão filtradas. 

## <a name="step-1-application-tier-set-tenantid-in-the-sessioncontext"></a>Etapa 1) Camada de aplicativo: definir a TenantId em SESSION_CONTEXT
Depois de se conectar a um banco de dados de fragmentos usando dados da biblioteca cliente do banco de dados elástico que depende de APIs de roteamento, o aplicativo ainda precisa informar o banco de dados qual TenantId está usando essa conexão para que uma política de segurança RLS possa filtrar linhas pertencentes a outros locatários. A maneira recomendada para passar essa informação é armazenar a TenantId atual dessa conexão em [SESSION_CONTEXT](https://msdn.microsoft.com/library/mt590806.aspx). (Observação: como alternativa, use também [CONTEXT_INFO](https://msdn.microsoft.com/library/ms180125.aspx), mas SESSION_CONTEXT é uma opção melhor porque é mais fácil de usar, retorna NULL por padrão e dá suporte aos pares chave-valor.)

### <a name="entity-framework"></a>Entity Framework
Para os aplicativos que usam o Entity Framework, a abordagem mais fácil é definir SESSION_CONTEXT na substituição de ElasticScaleContext descrita em [Roteamento dependente de dados usando o EF DbContext](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md#data-dependent-routing-using-ef-dbcontext). Antes de retornar a conexão negociada por meio do roteamento dependente de dados, simplesmente crie e execute um SqlCommand que define 'TenantId' em SESSION_CONTEXT para o shardingKey especificado para essa conexão. Dessa maneira, só é preciso gravar o código uma vez para definir SESSION_CONTEXT. 

```
// ElasticScaleContext.cs 
// ... 
// C'tor for data dependent routing. This call will open a validated connection routed to the proper 
// shard by the shard map manager. Note that the base class c'tor call will fail for an open connection 
// if migrations need to be done and SQL credentials are used. This is the reason for the  
// separation of c'tors into the DDR case (this c'tor) and the internal c'tor for new shards. 
public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
    : base(OpenDDRConnection(shardMap, shardingKey, connectionStr), true /* contextOwnsConnection */)
{
}

public static SqlConnection OpenDDRConnection(ShardMap shardMap, T shardingKey, string connectionStr)
{
    // No initialization
    Database.SetInitializer<ElasticScaleContext<T>>(null);

    // Ask shard map to broker a validated connection for the given key
    SqlConnection conn = null;
    try
    {
        conn = shardMap.OpenConnectionForKey(shardingKey, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey to enable Row-Level Security filtering
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"exec sp_set_session_context @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", shardingKey);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }

        throw;
    }
} 
// ... 
```

Agora, o SESSION_CONTEXT será automaticamente definido com a TenantId especificada sempre que ElasticScaleContext for chamado: 

```
// Program.cs 
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{   
    using (var db = new ElasticScaleContext<int>(sharding.ShardMap, tenantId, connStrBldr.ConnectionString))   
    {     
        var query = from b in db.Blogs
                    orderby b.Name
                    select b;

        Console.WriteLine("All blogs for TenantId {0}:", tenantId);     
        foreach (var item in query)     
        {       
            Console.WriteLine(item.Name);     
        }   
    } 
}); 
```

### <a name="adonet-sqlclient"></a>ADO.NET SqlClient
Para os aplicativos que usam o ADO.NET SqlClient, a abordagem recomendada é criar uma função de wrapper em torno de ShardMap.OpenConnectionForKey() que define automaticamente 'TenantId' em SESSION_CONTEXT para a TenantId correta antes de retornar uma conexão. Para garantir que SESSION_CONTEXT seja sempre definido, você só deve abrir conexões usando essa função de wrapper.

```
// Program.cs
// ...

// Wrapper function for ShardMap.OpenConnectionForKey() that automatically sets SESSION_CONTEXT with the correct
// tenantId before returning a connection. As a best practice, you should only open connections using this 
// method to ensure that SESSION_CONTEXT is always set before executing a query.
public static SqlConnection OpenConnectionForTenant(ShardMap shardMap, int tenantId, string connectionStr)
{
    SqlConnection conn = null;
    try
    {
        // Ask shard map to broker a validated connection for the given key
        conn = shardMap.OpenConnectionForKey(tenantId, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey to enable Row-Level Security filtering
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"exec sp_set_session_context @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", tenantId);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }

        throw;
    }
}

// ...

// Example query via ADO.NET SqlClient
// If row-level security is enabled, only Tenant 4's blogs will be listed
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (SqlConnection conn = OpenConnectionForTenant(sharding.ShardMap, tenantId4, connStrBldr.ConnectionString))
    {
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"SELECT * FROM Blogs";

        Console.WriteLine("--\nAll blogs for TenantId {0} (using ADO.NET SqlClient):", tenantId4);
        SqlDataReader reader = cmd.ExecuteReader();
        while (reader.Read())
        {
            Console.WriteLine("{0}", reader["Name"]);
        }
    }
});

```

## <a name="step-2-data-tier-create-row-level-security-policy"></a>Etapa 2) Camada de dados: criar política de segurança no nível da linha
### <a name="create-a-security-policy-to-filter-the-rows-each-tenant-can-access"></a>Criar uma política de segurança para filtrar as linhas que cada locatário pode acessar
Agora que o aplicativo está definindo SESSION_CONTEXT com a TenantId atual antes de consultar, uma política de segurança RLS pode filtrar as consultas e excluir as linhas que têm uma TenantId diferente.  

A RLS é implementada no T-SQL: uma função definida pelo usuário define a lógica de acesso e uma política de segurança associa essa função a qualquer quantidade de tabelas. Para este projeto, a função simplesmente verificará se o aplicativo (em vez de outro usuário do SQL) está conectado ao banco de dados e se a 'TenantId' armazenada em SESSION_CONTEXT corresponde à TenantId de uma determinada linha. Um predicado de filtro permitirá que as linhas que atendem a essas condições passem no filtro para as consultas SELECT, UPDATE e DELETE; e um predicado de bloco impedirá que as linhas que violam essas condições sejam inseridas ou atualizadas com INSERT ou UPDATE, respectivamente. Se SESSION_CONTEXT não tiver sido definido, retornará NULL e nenhuma linha será visível ou poderá ser inserida. 

Para habilitar a RLS, execute o seguinte comando do T-SQL em todos os fragmentos usando Visual Studio (SSDT), SSMS ou o script do PowerShell incluído no projeto (ou, se você estiver usando [Trabalhos de Banco de Dados Elástico](sql-database-elastic-jobs-overview.md), você poderá usá-lo para automatizar a execução desse T-SQL em todos os fragmentos): 

```
CREATE SCHEMA rls -- separate schema to organize RLS objects 
GO

CREATE FUNCTION rls.fn_tenantAccessPredicate(@TenantId int)     
    RETURNS TABLE     
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult          
        WHERE DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- the user in your application’s connection string (dbo is only for demo purposes!)         
        AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
GO

CREATE SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD BLOCK PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts,
    ADD BLOCK PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts
GO 
```

> [!TIP]
> Para os projetos mais complexos que precisam adicionar o predicado a centenas de tabelas, você poderá usar um procedimento auxiliar armazenado que gera uma política de segurança automaticamente adicionando um predicado a todas as tabelas em um esquema. Confira [Apply Row-Level Security to all tables - helper script (blog)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/03/31/apply-row-level-security-to-all-tables-helper-script) (Aplicar segurança no nível de linha a todas as tabelas – script auxiliar (blog)).  
> 
> 

Agora, se você executar novamente o aplicativo de exemplo, os locatários poderão ver apenas as linhas que pertencem a eles. Além disso, o aplicativo não poderá inserir as linhas que pertencem aos locatários diferentes dos atualmente conectados ao banco de dados de fragmentos e não poderá atualizar as linhas visíveis com uma TenantId diferente. Se o aplicativo tentar qualquer uma dessas operações, será gerado um DbUpdateException.

Se você adicionar uma nova tabela posteriormente, bastará alterar com ALTER a política de segurança e adicionar predicados de filtro e bloco à nova tabela: 

```
ALTER SECURITY POLICY rls.tenantAccessPolicy     
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
    ADD BLOCK PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable
GO 
```

### <a name="add-default-constraints-to-automatically-populate-tenantid-for-inserts"></a>Adicionar restrições padrão para preencher automaticamente o TenantId para INSERTs
Você pode colocar uma restrição padrão em cada tabela para preencher automaticamente a TenantId com o valor armazenado atualmente em SESSION_CONTEXT ao inserir as linhas. Por exemplo: 

```
-- Create default constraints to auto-populate TenantId with the value of SESSION_CONTEXT for inserts 
ALTER TABLE Blogs     
    ADD CONSTRAINT df_TenantId_Blogs      
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId 
GO

ALTER TABLE Posts     
    ADD CONSTRAINT df_TenantId_Posts      
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId 
GO 
```

Agora, o aplicativo não precisa especificar um TenantId ao inserir linhas: 

```
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{   
    using (var db = new ElasticScaleContext<int>(sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
    {
        var blog = new Blog { Name = name }; // default constraint sets TenantId automatically     
        db.Blogs.Add(blog);     
        db.SaveChanges();   
    } 
}); 
```

> [!NOTE]
> Se você usar restrições padrão para um projeto do Entity Framework, é recomendável não incluir a coluna TenantId em seu modelo de dados do EF. Isso ocorre porque as consultas do Entity Framework fornecem automaticamente os valores padrão que substituirão as restrições padrão criadas no T-SQL que usa o SESSION_CONTEXT. Para usar restrições padrão no projeto de exemplo, por exemplo, você deve remover TenantId de DataClasses.cs (e executar Add-Migration no Console do Gerenciador de Pacotes) e usar o T-SQL para garantir que o campo só exista nas tabelas do banco de dados. Dessa forma, o EF não fornecerá valores padrão incorretos automaticamente ao inserir dados. 
> 
> 

### <a name="optional-enable-a-superuser-to-access-all-rows"></a>(Opcional) Habilitar um "superusuário" acessar todas as linhas
Alguns aplicativos talvez queiram criar um "superusuário" que pode acessar todas as linhas, por exemplo, para permitir a emissão de relatórios em todos os locatários em todos os fragmentos ou para executar operações de divisão/mesclagem em fragmentos que envolvem a movimentação de linhas de locatário entre bancos de dados. Para habilitar isso, você deve criar um novo usuário do SQL ("superusuário" neste exemplo) em cada banco de dados do fragmento. Em seguida, altere a política de segurança com uma nova função de predicado que permite que esse usuário acesse todas as linhas:

```
-- New predicate function that adds superuser logic
CREATE FUNCTION rls.fn_tenantAccessPredicateWithSuperUser(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult 
        WHERE 
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- note, should not be dbo!
            AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
        ) 
        OR
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('superuser')
        )
GO

-- Atomically swap in the new predicate function on each table
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER BLOCK PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts,
    ALTER BLOCK PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts
GO
```


### <a name="maintenance"></a>Manutenção
* **Adicionando novos fragmentos**: você deve executar o script T-SQL para habilitar a RLS em qualquer novo fragmento; do contrário, as consultas nesses fragmentos não serão filtradas.
* **Adicionando novas tabelas**: você deve adicionar predicados de filtro e bloco à política de segurança em todos os fragmentos sempre que uma nova tabela for criada; do contrário, as consultas na nova tabela não serão filtradas. Isso pode ser automatizado usando um gatilho DDL, conforme é descrito em [Aplicar Segurança em Nível de Linha automaticamente a tabelas recém-criadas (blog)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/05/22/apply-row-level-security-automatically-to-newly-created-tables.aspx).

## <a name="summary"></a>Resumo
Ferramentas de banco de dados elástico e segurança em nível de linha podem ser usadas em conjunto para expandir a camada de dados de um aplicativo com suporte para fragmentos multilocatários e de um locatário. Fragmentos multilocatários podem ser usados para armazenar dados com mais eficiência (principalmente em casos em que uma grande quantidade de locatários têm apenas algumas linhas de dados), enquanto fragmentos de um locatário podem ser usados para dar suporte a locatários premium com requisitos de desempenho e isolamento mais rígidos.  Para obter mais informações, confira [a referência à Segurança em Nível de Linha](https://msdn.microsoft.com/library/dn765131). 

## <a name="additional-resources"></a>Recursos adicionais
* [O que é um pool elástico do Azure?](sql-database-elastic-pool.md)
* [Escalando horizontalmente com o Banco de Dados SQL do Azure](sql-database-elastic-scale-introduction.md)
* [Padrões de design para aplicativos SaaS multilocatários com o Banco de Dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [Autenticação em aplicativos multilocatários usando o Azure AD e o OpenID Connect](../guidance/guidance-multitenant-identity-authenticate.md)
* [Aplicativo Tailspin Surveys](../guidance/guidance-multitenant-identity-tailspin.md)

## <a name="questions-and-feature-requests"></a>Perguntas e solicitações de recursos
Em caso de dúvidas, entre em contato conosco pelo [fórum do Banco de Dados SQL](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) e, para solicitações de recursos, adicione-as ao [fórum de comentários sobre o Banco de Dados SQL](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->


