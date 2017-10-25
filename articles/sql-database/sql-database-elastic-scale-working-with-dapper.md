---
title: "Usando a biblioteca de cliente de banco de dados elástico com o Dapper | Microsoft Docs"
description: "Usar a biblioteca de cliente de banco de dados elástico com Dapper."
services: sql-database
documentationcenter: 
manager: jhubbard
author: torsteng
ms.assetid: 463d2676-3b19-47c2-83df-f8c50492c9d2
ms.service: sql-database
ms.custom: scale out apps
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/27/2016
ms.author: torsteng
ms.openlocfilehash: f0efd37a39c1a60eee7b47304483c27727ca8833
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="using-elastic-database-client-library-with-dapper"></a>Usando a biblioteca de cliente do banco de dados elástico com Dapper
Este documento é destinado aos desenvolvedores que usam Dapper na criação de aplicativos, mas que também desejam adotar as [ferramentas de banco de dados elástico](sql-database-elastic-scale-introduction.md) para criar aplicativos de aplicativos que implementam a fragmentação para escalar horizontalmente sua camada de dados.  Este documento ilustra as alterações em aplicativos baseados em Dapper que são necessários para integrar as ferramentas de banco de dados elástico. Nosso foco é em criar o gerenciamento de fragmento de banco de dados elástico e roteamento dependente de dados com o Dapper. 

**Código de exemplo**: [ferramentas de banco de dados elástico para o Banco de Dados SQL do Azure - integração com o Dapper](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-e19fc77f).

É muito fácil integrar o **Dapper** e **DapperExtensions** com a biblioteca de cliente de banco de dados elástico do Banco de Dados SQL do Azure. Seus aplicativos podem usar roteamento dependente de dados alterando a criação e abertura de novos objetos [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) para usar a chamada [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) na [biblioteca de cliente](http://msdn.microsoft.com/library/azure/dn765902.aspx). Isso limita as alterações em seu aplicativo apenas quando novas conexões forem criadas e abertas. 

## <a name="dapper-overview"></a>Visão geral do Dapper
**Dapper** é um mapeador relacional de objetos. Ele mapeia objetos .NET a partir de seu aplicativo para um banco de dados relacional (e vice-versa). A primeira parte do código de exemplo ilustra como você pode integrar a biblioteca de cliente do banco de dados elástico com aplicativos baseados em Dapper. A segunda parte do código de exemplo ilustra como integrar ao usar o Dapper e as DapperExtensions.  

A funcionalidade do mapeador em Dapper fornece métodos de extensão em conexões de banco de dados que simplificam o envio de instruções T-SQL para execução ou consulta ao banco de dados. Por exemplo, o Dapper torna mais fácil mapear entre seus objetos .NET e os parâmetros de instruções SQL por chamadas **Execute** ou para consumir os resultados de suas consultas SQL em objetos .NET usando chamadas **Query** no Dapper. 

Ao usar DapperExtensions, você não precisa fornecer as instruções SQL. Métodos de extensão, como **GetList** ou **Insert** através da conexão de banco de dados cria as instruções SQL nos bastidores.

Outro benefício do Dapper e também das DapperExtensions é que o aplicativo controla a criação da conexão de banco de dados. Isso ajuda a interagir com a biblioteca de cliente de banco de dados elástico que intermedia as conexões com base no mapeamento de shardlets para bancos de dados.

Para obter os assemblies do Dapper, consulte [Dapper dot net](http://www.nuget.org/packages/Dapper/). Para ver as extensões do Dapper, consulte [DapperExtensions](http://www.nuget.org/packages/DapperExtensions).

## <a name="a-quick-look-at-the-elastic-database-client-library"></a>Uma olhada rápida pela biblioteca de cliente de banco de dados elástico
Com a biblioteca de cliente do banco de dados elástico, você definir partições de dados do aplicativo chamados *shardlets*, mapeá-las para bancos de dados e identificá-las por *chaves de fragmentação*. Você pode ter quantos bancos de dados conforme necessário e distribuir seu shardlets entre esses bancos de dados. O mapeamento de valores chave de fragmentação para os bancos de dados é armazenado por um mapa de fragmentos fornecido pelas APIs da biblioteca. Essa funcionalidade é chamada de **gerenciamento de mapa de fragmentos**. O mapa do fragmento também serve como o agente de conexões de banco de dados para solicitações que carregam uma chave de fragmentação. Essa funcionalidade é conhecida como **Roteamento dependente de dados**.

![Mapas de fragmentos e roteamento dependente de dados][1]

O gerenciador de mapa de fragmentos protege os usuários contra exibições inconsistentes em dados de shardlet que podem ocorrer quando as operações de gerenciamento simultâneo de shardlet ocorrem nos bancos de dados. Para fazer isso, os mapas de fragmento intermediam as conexões de banco de dados para um aplicativo criado com a biblioteca. Quando as operações de gerenciamento de fragmentação podem afetar o shardlet, isso permite que a funcionalidade de mapa do fragmento elimine automaticamente uma conexão de banco de dados. 

Em vez de usar a maneira tradicional de criar conexões para Dapper, precisamos usar o método [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn824099.aspx). Isso garante que todos as validações ocorram e as conexões são gerenciadas corretamente quando os dados se movem entre os fragmentos.

### <a name="requirements-for-dapper-integration"></a>Requisitos para a integração do Dapper
Ao trabalhar com a biblioteca de cliente do banco de dados elástico e de APIs do Dapper, queremos manter as seguintes propriedades:

* **Escala horizontal**: queremos adicionar ou remover bancos de dados da camada de dados do aplicativo fragmentado, conforme necessário para as demandas de capacidade do aplicativo. 
* **Consistência**: como nosso aplicativo é dimensionado com o uso de fragmentação, precisamos executar roteamento dependente de dados. Queremos usar os recursos de roteamento dependentes de dados da biblioteca para fazer isso. Queremos reter especialmente a validação e garantir a consistência fornecida pelas conexões intermediadas por meio do gerenciador de mapa de fragmentos para evitar corrompimento ou resultados de consulta incorretos. Isso garante que conexões para um determinado shardlet sejam rejeitadas ou interrompidas se (por exemplo) o shardlet é movido num momento para um fragmento diferente usando APIs de divisão/mesclagem.
* **Mapeamento de objetos**: queremos manter a conveniência dos mapeamentos fornecidos pelo Dapper para converter entre classes no aplicativo e as estruturas de banco de dados subjacente. 

A seção a seguir fornece diretrizes para esses requisitos de aplicativos com base em **Dapper** e **DapperExtensions**.

## <a name="technical-guidance"></a>Orientações técnicas
### <a name="data-dependent-routing-with-dapper"></a>Roteamento dependente de dados com Dapper
Com Dapper, o aplicativo é geralmente responsável por criar e abrir as conexões de banco de dados subjacente. Fornecendo um tipo T pelo aplicativo, o Dapper retorna resultados da consulta como coleções .NET do tipo T. O Dapper executa o mapeamento das linhas de resultado do T-SQL para os objetos do tipo T. Da mesma forma, o Dapper mapeia objetos .NET em valores ou parâmetros SQL para instruções de linguagem de manipulação de dados (DML). O Dapper oferece essa funcionalidade por meio de métodos de extensão no objeto [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) normal por meio de bibliotecas do Cliente SQL do ADO .NET. A conexão do SQL retornada por APIs de escala elástica para DDR também são objetos regulares da [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx). Isso nos permite usar extensões Dapper diretamente no tipo retornado pela API de DDR da biblioteca de cliente, pois ele também é uma conexão SQL Client simples.

Essas observações simplificam o uso de conexões intermediadas pela biblioteca de cliente de banco de dados elástico para Dapper.

Este exemplo de código (do exemplo que acompanha este artigo) ilustra a abordagem em que a chave de fragmentação é fornecida pelo aplicativo para a biblioteca para intermediar a conexão para o fragmento correto.   

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                     key: tenantId1, 
                     connectionString: connStrBldr.ConnectionString, 
                     options: ConnectionOptions.Validate))
    {
        var blog = new Blog { Name = name };
        sqlconn.Execute(@"
                      INSERT INTO
                            Blog (Name)
                            VALUES (@name)", new { name = blog.Name }
                        );
    }

A chamada para a API [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) substitui a criação e a abertura padrão de uma conexão de cliente SQL. A chamada [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) usa os argumentos que são necessários para roteamento dependente de dados com escala elástica: 

* o mapa do fragmento para acessar as interfaces de roteamento dependente de dados
* a chave de fragmentação para identificar o shardlet
* as credenciais (nome de usuário e senha) para conectar-se ao fragmento

o objeto do mapa do fragmento cria uma conexão para o fragmento que mantém o shardlet para a chave de fragmentação determinada. As APIs de cliente do banco de dados elástico também marca a conexão para implementar suas garantias de consistência. Uma vez que a chamada para [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) retorna um objeto de conexão do Cliente SQL regular, a chamada subsequente para o método de extensão **Execute** do Dapper segue a prática padrão do Dapper.

As consultas funcionam praticamente da mesma forma — você primeiro abre a conexão usando [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) na API do cliente. Em seguida, você deve usar os métodos de extensão Dapper regulares para mapear os resultados da consulta SQL nos objetos .NET:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId1, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate ))
    {    
           // Display all Blogs for tenant 1
           IEnumerable<Blog> result = sqlconn.Query<Blog>(@"
                                SELECT * 
                                FROM Blog
                                ORDER BY Name");

           Console.WriteLine("All blogs for tenant id {0}:", tenantId1);
           foreach (var item in result)
           {
                Console.WriteLine(item.Name);
            }
    }

Observe que o bloco **using** com a conexão DDR abrange todas as operações de banco de dados dentro do bloco para o único fragmento em que tenantId1 é mantido. A consulta retorna apenas os blogs armazenados no fragmento atual, mas não os arquivos armazenados em quaisquer outros fragmentos. 

## <a name="data-dependent-routing-with-dapper-and-dapperextensions"></a>Roteamento dependente de dados com Dapper e DapperExtensions
O Dapper vem com um ecossistema de extensões adicionais que podem fornecer mais conveniência e abstração do banco de dados ao desenvolver aplicativos de banco de dados. O DapperExtensions é um exemplo. 

O uso do DapperExtensions em seu aplicativo não muda como conexões de banco de dados são criadas e gerenciadas. Ainda é responsabilidade do aplicativo abrir conexões, e objetos de conexão do Cliente SQL regulares são aguardados pelos métodos de extensão. Podemos usar a [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) conforme descrito acima. Como mostram os exemplos de código a seguir, a única alteração é que não temos de escrever as instruções T-SQL:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate))
    {
           var blog = new Blog { Name = name2 };
           sqlconn.Insert(blog);
    }

E aqui está o exemplo de código para a consulta: 

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate))
    {
           // Display all Blogs for tenant 2
           IEnumerable<Blog> result = sqlconn.GetList<Blog>();
           Console.WriteLine("All blogs for tenant id {0}:", tenantId2);
           foreach (var item in result)
           {
               Console.WriteLine(item.Name);
           }
    }

### <a name="handling-transient-faults"></a>Tratamento de falhas transitórias
A equipe Microsoft Patterns & Practices publicou o [Bloco de aplicativos de tratamento de falhas transitórias](http://msdn.microsoft.com/library/hh680934.aspx) para ajudar os desenvolvedores de aplicativos a atenuarem condições de falha transitória comuns encontradas durante a execução na nuvem. Para obter mais informações, consulte [Perseverança, segredo de todos os triunfos: usando o bloco de aplicativos de tratamento de falhas temporárias](http://msdn.microsoft.com/library/dn440719.aspx).

O código de exemplo se baseia na biblioteca de falha transitória para proteger contra falhas transitórias. 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
    {
       using (SqlConnection sqlconn = 
          shardingLayer.ShardMap.OpenConnectionForKey(tenantId2, connStrBldr.ConnectionString, ConnectionOptions.Validate))
          {
              var blog = new Blog { Name = name2 };
              sqlconn.Insert(blog);
          }
    });

**SqlDatabaseUtils.SqlRetryPolicy** no código acima é definido como um **SqlDatabaseTransientErrorDetectionStrategy** com uma contagem de repetições de 10, com tempo de espera de 5 segundos entre as tentativas. Se você estiver usando transações, certifique-se de que o escopo de repetição volta para o início da transação no caso de uma falha temporária.

## <a name="limitations"></a>Limitações
As abordagens descritas neste documento envolvem algumas limitações:

* O código de exemplo para este documento demonstra como gerenciar o esquema em fragmentos.
* Recebida uma solicitação, pressupomos que todo o processamento de seu banco de dados está contido em um único fragmento como identificado pela chave de fragmentação fornecida pela solicitação. No entanto, esse pressuposto não é sempre mantido, por exemplo, quando não é possível disponibilizar uma chave de fragmentação. Para resolver isso, a biblioteca de cliente do banco de dados elástico inclui a [classe MultiShardQuery](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardexception.aspx). A classe implementa uma abstração de conexão para consultas em vários fragmentos. O uso da MultiShardQuery em combinação com Dapper está além do escopo deste documento.

## <a name="conclusion"></a>Conclusão
Aplicativos que usam Dapper e DapperExtensions podem aproveitar facilmente as ferramentas de banco de dados elástico para o Banco de Dados SQL do Azure. Através das etapas descritas neste documento, esses aplicativos podem usar funcionalidade da ferramenta para roteamento dependente de dados alterando a criação e abertura de novos objetos [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) para usar a chamada [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) da biblioteca de cliente de banco de dados elástico. Isso limita as alterações de aplicativo necessárias para os locais onde as novas conexões são criadas e abertas. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-working-with-dapper/dapperimage1.png
