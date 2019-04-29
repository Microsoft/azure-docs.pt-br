---
title: Aplicativos multilocatários com RLS e ferramentas de banco de dados elástico | Microsoft Docs
description: Use ferramentas de banco de dados elástico com segurança em nível de linha para criar um aplicativo com uma camada de dados altamente dimensionável.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: 71d2d542d71977f9d8dfe07370dffd7fe508bc92
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485309"
---
# <a name="multi-tenant-applications-with-elastic-database-tools-and-row-level-security"></a>Aplicativos multilocatários com ferramentas de banco de dados elástico e segurança em nível de linha

As [ferramentas de banco de dados elástico](sql-database-elastic-scale-get-started.md) e a [RLS (segurança em nível de linha)][rls] cooperam para escalar a camada de dados de um aplicativo multilocatário com um Banco de Dados SQL do Azure. Juntas, essas tecnologias ajudam a criar um aplicativo que tem uma camada de dados altamente escalonável. A camada de dados é compatível com fragmentos de multilocatários e usa **ADO.NET SqlClient** ou **Entity Framework**. Para obter mais informações, confira [Padrões de design para aplicativos SaaS multilocatário com o Banco de Dados SQL do Azure](saas-tenancy-app-design-patterns.md).

- **Ferramentas de banco de dados elástico** permitem que os desenvolvedores expandam a camada de dados com a fragmentação padrão usando bibliotecas .NET e modelos de serviço do Azure. Gerenciar fragmentos usando a [Biblioteca Cliente do Banco de Dados Elástico][s-d-elastic-database-client-library] ajuda a automatizar e simplificar muitas das tarefas infraestruturais normalmente associadas à fragmentação.
- A **segurança em nível de linha** permite que os desenvolvedores armazenem com segurança os dados para vários locatários no mesmo banco de dados. Políticas de segurança de RLS filtram linhas que não pertencem ao locatário executando uma consulta. Centralizar a lógica do filtro dentro do banco de dados simplifica a manutenção e reduz o risco de um erro de segurança. A alternativa de depender de todo o código de cliente para aplicar a segurança é arriscada.

Ao usar esses recursos em conjunto, um aplicativo pode armazenar dados para vários locatários no mesmo banco de dados de fragmentos. Custa menos por locatário quando os locatários compartilham um banco de dados. Embora o mesmo aplicativo também pode oferecer a seus locatários premium a opção de pagar para seus próprios fragmentos de banco de dados de único locatário dedicado. Um dos benefícios do isolamento de locatário único é uma maior garantia de desempenho. Em um banco de dados de locatário único, não há nenhum outro locatário competindo pelos recursos.

O objetivo é usar as APIs do [roteamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md) da biblioteca de cliente do banco de dados elástico para conectar automaticamente a cada locatário especificado ao banco de dados de fragmento correto. Somente um fragmento contém o valor de TenantId específico para o locatário determinado. O TenantId é a *chave de fragmentação*. Depois que a conexão é estabelecida, uma política de segurança de RLS no banco de dados garante que o locatário especificado pode acessar somente as linhas de dados que contêm seu TenantId.

> [!NOTE]
> O identificador do locatário pode consistir em mais de uma coluna. Para esta discussão, vamos supor informalmente um TenantId de coluna única.

![Arquitetura de aplicativo de blog][1]

## <a name="download-the-sample-project"></a>Baixar o projeto de exemplo

### <a name="prerequisites"></a>Pré-requisitos

- Usar o Visual Studio (2012 ou superior)
- Criar três bancos de dados SQL do Azure
- Faça o download do projeto de exemplo: [Ferramentas de Banco de Dados Elástico para o SQL do Azure - Fragmentos Multilocatários](https://go.microsoft.com/?linkid=9888163)
  - Preencha as informações dos seus bancos de dados no início do **Program.cs**

Esse projeto expande o descrito em [Ferramentas de Banco de Dados Elástico para o SQL do Azure - Integração com o Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) adicionando suporte para bancos de dados de fragmentos multilocatários. O projeto cria um aplicativo de console simples para a criação de blogs e postagens. O projeto inclui quatro locatários, além de dois bancos de dados de fragmento de multilocatário. Essa configuração é ilustrada no diagrama anterior.

Compile e execute o aplicativo. Essa execução inicializa o gerenciador de mapas de fragmentos das ferramentas de banco de dados elástico e executa os seguintes testes:

1. Usando o Entity Framework e o LINQ, crie um novo blog e exiba todos os blogs para cada locatário
2. Usando o ADO.NET SqlClient, exiba todos os blogs para um locatário
3. Tente inserir um blog para o locatário errado para verificar se um erro será gerado

Observe que, como a RLS ainda não foi habilitada nos bancos de dados de fragmentos, cada um desses testes revela um problema: locatários podem ver blogs que não pertencem a eles e o aplicativo não é impedido de inserir um blog para o locatário errado. O restante deste artigo descreve como resolver esses problemas impondo o isolamento de locatários com RLS. Há duas etapas:

1. **Camada de aplicativo**: Modifique o código do aplicativo para sempre definir TenantId atual em SESSION\_CONTEXT depois de abrir uma conexão. O projeto de exemplo já define o TenantId dessa maneira.
2. **Camada de dados**: Crie uma política de segurança RLS em cada banco de dados de fragmentos para filtrar as linhas com base na TenantId armazenada em SESSION\_CONTEXT. Crie uma política para cada um dos seus bancos de dados de fragmentos, caso contrário, linhas em fragmentos multilocatários não serão filtradas.

## <a name="1-application-tier-set-tenantid-in-the-sessioncontext"></a>1. Camada de aplicativo: Defina TenantId em SESSION\_CONTEXT

Primeiro, você se conecta a um banco de dados do fragmento usando as APIs de roteamentos dependentes de dados da biblioteca de cliente do banco de dados elástico. O aplicativo ainda deve informar ao banco de dados qual TenantId está usando a conexão. O TenantId informa à política de segurança de RLS quais linhas devem ser filtradas como pertencentes a outros locatários. Armazene o TenantId atual no [SESSION\_CONTEXT](https://docs.microsoft.com/sql/t-sql/functions/session-context-transact-sql) da conexão.

Uma alternativa à SESSION\_CONTEXT é usar [CONTEXT\_INFO](https://docs.microsoft.com/sql/t-sql/functions/context-info-transact-sql). Mas a SESSION\_CONTEXT é uma opção melhor. SESSION\_CONTEXT é mais fácil de usar, ele retorna NULL por padrão e é compatível com pares chave e valor.

### <a name="entity-framework"></a>Entity Framework

Para os aplicativos que usam o Entity Framework, a abordagem mais fácil é definir SESSION\_CONTEXT na substituição de ElasticScaleContext descrita em [Roteamento dependente de dados usando o EF DbContext](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md#data-dependent-routing-using-ef-dbcontext). Crie e execute um SqlCommand que define o TenantId no SESSION\_CONTEXT como o shardingKey especificado para a conexão. Em seguida, retorne a conexão agenciada por meio do roteamento dependente de dados. Dessa maneira, só é preciso gravar o código uma vez para definir SESSION\_CONTEXT.

```csharp
// ElasticScaleContext.cs
// Constructor for data-dependent routing.
// This call opens a validated connection that is routed to the
// proper shard by the shard map manager.
// Note that the base class constructor call fails for an open connection
// if migrations need to be done and SQL credentials are used.
// This is the reason for the separation of constructors.
// ...
public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
    : base(
        OpenDDRConnection(shardMap, shardingKey, connectionStr),
        true)  // contextOwnsConnection
{
}

public static SqlConnection OpenDDRConnection(
    ShardMap shardMap,
    T shardingKey,
    string connectionStr)
{
    // No initialization.
    Database.SetInitializer<ElasticScaleContext<T>>(null);

    // Ask shard map to broker a validated connection for the given key.
    SqlConnection conn = null;
    try
    {
        conn = shardMap.OpenConnectionForKey(
            shardingKey,
            connectionStr,
            ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey
        // to enable Row-Level Security filtering.
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText =
            @"exec sp_set_session_context
                @key=N'TenantId', @value=@shardingKey";
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

Agora, o SESSION\_CONTEXT será automaticamente definido com a TenantId especificada sempre que ElasticScaleContext for chamado:

```csharp
// Program.cs
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (var db = new ElasticScaleContext<int>(
        sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
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

Para aplicativos que usam o ADO.NET SqlClient, crie uma função de wrapper em torno do método ShardMap.OpenConnectionForKey. Faça com que o wrapper defina automaticamente o TenantId no SESSION\_CONTEXT como o TenantId atual antes de retornar uma conexão. Para garantir que SESSION\_CONTEXT seja sempre definido, você só deve abrir conexões usando essa função de wrapper.

```csharp
// Program.cs
// Wrapper function for ShardMap.OpenConnectionForKey() that
// automatically sets SESSION_CONTEXT with the correct
// tenantId before returning a connection.
// As a best practice, you should only open connections using this method
// to ensure that SESSION_CONTEXT is always set before executing a query.
// ...
public static SqlConnection OpenConnectionForTenant(
    ShardMap shardMap, int tenantId, string connectionStr)
{
    SqlConnection conn = null;
    try
    {
        // Ask shard map to broker a validated connection for the given key.
        conn = shardMap.OpenConnectionForKey(
            tenantId, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey
        // to enable Row-Level Security filtering.
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText =
            @"exec sp_set_session_context
                @key=N'TenantId', @value=@shardingKey";
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

// Example query via ADO.NET SqlClient.
// If row-level security is enabled, only Tenant 4's blogs are listed.
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (SqlConnection conn = OpenConnectionForTenant(
        sharding.ShardMap, tenantId4, connStrBldr.ConnectionString))
    {
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"SELECT * FROM Blogs";

        Console.WriteLine(@"--
All blogs for TenantId {0} (using ADO.NET SqlClient):", tenantId4);

        SqlDataReader reader = cmd.ExecuteReader();
        while (reader.Read())
        {
            Console.WriteLine("{0}", reader["Name"]);
        }
    }
});

```

## <a name="2-data-tier-create-row-level-security-policy"></a>2. Camada de dados: Crie a política de segurança no nível da linha

### <a name="create-a-security-policy-to-filter-the-rows-each-tenant-can-access"></a>Criar uma política de segurança para filtrar as linhas que cada locatário pode acessar

Agora que o aplicativo está definindo SESSION\_CONTEXT com a TenantId atual antes de consultar, uma política de segurança RLS pode filtrar as consultas e excluir as linhas que têm uma TenantId diferente.

O RLS é implementado em Transact-SQL. A função definida pelo usuário define a lógica de acesso e uma política de segurança associa essa função a qualquer quantidade de tabelas. Para este projeto:

1. A função verifica se o aplicativo está conectado ao banco de dados e se a 'TenantId' armazenada em SESSION\_CONTEXT corresponde à TenantId de uma determinada linha.
    - O aplicativo está conectado, em vez de algum outro usuário do SQL.

2. Um predicado de FILTER permite que as linhas que atendem ao filtro TenantId passem pelas consultas SELECT, UPDATE e DELETE.
    - Um predicado BLOCK impede que as linhas que não passam no filtro sejam inseridas ou atualizadas.
    - Se SESSION\_CONTEXT não tiver sido definido, a função retornará NULL e nenhuma linha estará visível ou poderá ser inserida.

Para habilitar a RLS em todos os fragmentos, execute o T-SQL a seguir usando o Visual Studio (SSDT), SSMS ou o script do PowerShell incluído no projeto. Ou, se você estiver usando [Trabalhos do banco de dados elástico](sql-database-elastic-jobs-overview.md), você poderá automatizar a execução desse T-SQL em todos os fragmentos.

```sql
CREATE SCHEMA rls; -- Separate schema to organize RLS objects.
GO

CREATE FUNCTION rls.fn_tenantAccessPredicate(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult
        -- Use the user in your application’s connection string.
        -- Here we use 'dbo' only for demo purposes!
        WHERE DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo')
        AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId;
GO

CREATE SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts;
GO
```

> [!TIP]
> Em um projeto complexo, talvez seja necessário adicionar o predicado em centenas de tabelas, o que pode ser entediante. Há um procedimento armazenado auxiliar que gera automaticamente uma política de segurança e adiciona um predicado em todas as tabelas em um esquema. Para obter mais informações, consulte [Aplicar segurança em nível de linha a todas as tabelas – script auxiliar (blog)](https://blogs.msdn.com/b/sqlsecurity/archive/20../../apply-row-level-security-to-all-tables-helper-script).

Agora, se você executar novamente o aplicativo de exemplo, os locatários verão apenas as linhas que pertencem a eles. Além disso, o aplicativo não pode inserir as linhas que pertencem aos locatários diferentes dos atualmente conectados ao banco de dados de fragmentos. Além disso, o aplicativo não pode atualizar a TenantId em todas as linhas que ele pode ver. Se o aplicativo tentar qualquer uma dessas operações, será gerada uma DbUpdateException.

Se você adicionar uma nova tabela posteriormente, altere com ALTER a política de segurança para adicionar predicados de FILTER e BLOCK à nova tabela.

```sql
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable;
GO
```

### <a name="add-default-constraints-to-automatically-populate-tenantid-for-inserts"></a>Adicionar restrições padrão para preencher automaticamente o TenantId para INSERTs

Você pode colocar uma restrição padrão em cada tabela para preencher automaticamente a TenantId com o valor armazenado atualmente em SESSION\_CONTEXT ao inserir as linhas. Há um exemplo a seguir.

```sql
-- Create default constraints to auto-populate TenantId with the
-- value of SESSION_CONTEXT for inserts.
ALTER TABLE Blogs
    ADD CONSTRAINT df_TenantId_Blogs
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId;
GO

ALTER TABLE Posts
    ADD CONSTRAINT df_TenantId_Posts
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId;
GO
```

Agora, o aplicativo não precisa especificar um TenantId ao inserir linhas:

```csharp
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (var db = new ElasticScaleContext<int>(
        sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
    {
        // The default constraint sets TenantId automatically!
        var blog = new Blog { Name = name };
        db.Blogs.Add(blog);
        db.SaveChanges();
    }
});
```

> [!NOTE]
> Se você usa restrições padrão para um projeto do Entity Framework, é recomendável *NÃO* incluir a coluna TenantId em seu modelo de dados do EF. Isso ocorre porque as consultas do Entity Framework fornecem automaticamente valores padrão que substituem as restrições padrão criadas no T-SQL que usa o SESSION\_CONTEXT.
> Para usar restrições padrão no projeto de exemplo, por exemplo, você deve remover TenantId de DataClasses.cs (e executar Add-Migration no Console do Gerenciador de Pacotes) e usar o T-SQL para garantir que o campo só exista nas tabelas do banco de dados. Dessa forma, o EF fornecerá valores padrão incorretos automaticamente ao inserir dados.

### <a name="optional-enable-a-superuser-to-access-all-rows"></a>(Opcional) Habilitar um *superusuário* para acessar todas as linhas

Alguns aplicativos podem querer criar um *superusuário* que pode acessar todas as linhas. Um superusuário pode permitir a emissão de relatórios em todos os locatários em todos os fragmentos. Ou um superusuário poderia executar operações de mesclagem/divisão em fragmentos que envolvem a movimentação de linhas de locatário entre bancos de dados.

Para habilitar um superusuário, crie um novo usuário do SQL (`superuser` neste exemplo) em cada banco de dados do fragmento. Em seguida, altere a política de segurança com uma nova função de predicado que permite que esse usuário acesse todas as linhas. Essa função é fornecida em seguida.

```sql
-- New predicate function that adds superuser logic.
CREATE FUNCTION rls.fn_tenantAccessPredicateWithSuperUser(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult
        WHERE
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- Replace 'dbo'.
            AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
        )
        OR
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('superuser')
        );
GO

-- Atomically swap in the new predicate function on each table.
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER BLOCK  PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts,
    ALTER BLOCK  PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts;
GO
```


### <a name="maintenance"></a>Manutenção 

- **Adicionando novo fragmentos**: Execute o script T-SQL para habilitar RLS em qualquer novo fragmento; do contrário, as consultas nesses fragmentos não serão filtradas.
- **Adicionando novas tabelas**: Adicione um predicado de FILTER e BLOCK à política de segurança em todos os fragmentos sempre que uma nova tabela for criada. Caso contrário, consultas na nova tabela não serão filtrados. Essa adição pode ser automatizada usando um gatilho DDL, conforme é descrito em [Aplicar Segurança em Nível de Linha automaticamente a tabelas recém-criadas (blog)](https://blogs.msdn.com/b/sqlsecurity/archive/20../../apply-row-level-security-automatically-to-newly-created-tables.aspx).

## <a name="summary"></a>Resumo

Ferramentas de banco de dados elástico e segurança em nível de linha podem ser usadas em conjunto para expandir a camada de dados de um aplicativo com suporte para fragmentos multilocatários e de um locatário. Fragmentos multilocatários podem ser usados para armazenar dados com mais eficiência. Essa eficiência é pronunciada quando um grande número de locatários tiver apenas algumas linhas de dados. Fragmentos de locatário único podem dar suporte a locatários premium que têm requisitos de desempenho e de isolamento mais rígidos. Para obter mais informações, confira [a referência à Segurança em Nível de Linha][rls].

## <a name="additional-resources"></a>Recursos adicionais

- [O que é um pool elástico do Azure?](sql-database-elastic-pool.md)
- [Escalando horizontalmente com o Banco de Dados SQL do Azure](sql-database-elastic-scale-introduction.md)
- [Padrões de design para aplicativos SaaS multilocatários com o Banco de Dados SQL do Azure](saas-tenancy-app-design-patterns.md)
- [Autenticação em aplicativos multilocatários usando o Azure AD e o OpenID Connect](../guidance/guidance-multitenant-identity-authenticate.md)
- [Aplicativo Tailspin Surveys](../guidance/guidance-multitenant-identity-tailspin.md)

## <a name="questions-and-feature-requests"></a>Perguntas e solicitações de recursos

Para fazer perguntas, entre em contato conosco no [Fórum do Banco de Dados SQL](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted). E adicione uma solicitação de recursos no [Fórum de comentários do Banco de Dados SQL](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/saas-tenancy-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->
[rls]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security
[s-d-elastic-database-client-library]: sql-database-elastic-database-client-library.md
