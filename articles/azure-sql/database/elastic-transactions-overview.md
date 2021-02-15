---
title: Transações distribuídas entre bancos de dados na nuvem (visualização)
description: Visão geral das transações de banco de dados elástico com o banco de dados SQL do Azure e o Azure SQL Instância Gerenciada.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 5504b9bc87f78682ff584006255d4e75e5e69fa7
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92793340"
---
# <a name="distributed-transactions-across-cloud-databases-preview"></a>Transações distribuídas entre bancos de dados na nuvem (visualização)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

As transações de banco de dados elástico para o banco de dados SQL do Azure e o Azure SQL Instância Gerenciada permitem que você execute transações que abrangem vários bancos de dados. As transações de banco de dados elástico estão disponíveis para aplicativos .NET usando o ADO.NET e se integram com a experiência de programação familiar usando as classes [System. Transaction](/dotnet/api/system.transactions) . Para obter a biblioteca, confira [.NET Framework 4.6.1 (Web Installer)](https://www.microsoft.com/download/details.aspx?id=49981).
Além disso, para Instância Gerenciada transações distribuídas estão disponíveis no [Transact-SQL](/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql).

No local, um cenário como esse geralmente requer a execução do Microsoft Coordenador de Transações Distribuídas (MSDTC). Como o MSDTC não está disponível para aplicativos de plataforma como serviço no Azure, a capacidade de coordenar transações distribuídas agora foi diretamente integrada ao banco de dados SQL ou Instância Gerenciada. Os aplicativos podem se conectar a qualquer banco de dados para iniciar transações distribuídas, e um dos bancos de dados ou servidores coordenará de forma transparente a transação distribuída, conforme mostrado na figura a seguir.

Neste documento, os termos "transações distribuídas" e "transações de banco de dados elástico" são considerados sinônimos e serão usados de maneira intercambiável.

  ![Transações distribuídas com o Banco de Dados SQL do Azure usando transações de banco de dados elástico ][1]

## <a name="common-scenarios"></a>Cenários comuns

As transações de banco de dados elástico permitem que os aplicativos façam alterações atômicas nos dados armazenados em vários bancos diferentes. A visualização se concentra nas experiências de desenvolvimento do lado do cliente em C# e .NET. Uma experiência do lado do servidor (código escrito em procedimentos armazenados ou scripts do lado do servidor) usando o [Transact-SQL](/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql) está disponível somente para instância gerenciada.
> [!IMPORTANT]
> Em versão prévia, não há suporte para a execução de transações de banco de dados elástico entre o banco de dados SQL do Azure e o Azure SQL Instância Gerenciada no momento. A transação de banco de dados elástico só pode abranger em conjunto de bancos de dados SQL ou em conjunto de instâncias gerenciadas.

As transações de banco de dados elástico visam os seguintes cenários:

* Aplicativos com vários bancos de dados no Azure: com esse cenário, os dados são particionados verticalmente em vários bancos de dados no banco de dados SQL ou Instância Gerenciada de forma que os diferentes tipos de dado residam em bancos diferentes. Algumas operações exigem alterações nos dados, que são mantidas em dois ou mais bancos de dado. O aplicativo usa transações de banco de dados elástico para coordenar as alterações nos bancos de dados e garantir a atomicidade.
* Aplicativos de banco de dados fragmentados no Azure: com esse cenário, a camada data usa a [biblioteca de cliente do banco](elastic-database-client-library.md) de dado elástico ou a autofragmentação para particionar horizontalmente os dados em vários bancos de dado no banco de dados SQL ou instância gerenciada. Um caso de uso importante é a necessidade de realizar mudanças atômicas em um aplicativo multilocatário fragmentado quando as alterações se estendem aos locatários. Pense, por exemplo, em uma transferência de um locatário para outro, ambos residentes em bancos de dados diferentes. Um segundo caso é a fragmentação refinada para acomodar as necessidades de capacidade para um locatário grande, que, por sua vez, normalmente implica que algumas operações atômicas precisam ser ampliadas em vários bancos de dados usados para o mesmo locatário. Um terceiro caso são as atualizações atômicas de dados de referência que são replicados nos bancos de dados. As operações atômicas e transacionadas, juntamente com estas linhas, agora podem ser coordenadas em vários bancos de dados usando a visualização.
  As transações de banco de dados elástico usam uma confirmação de duas fases para garantir a atomicidade da transação entre bancos de dados. É uma boa opção para transações que envolvem menos de 100 bancos de dados por vez em uma única transação. Esses limites não são impostos, mas um deve esperar taxas de desempenho e êxito para que as transações de banco de dados elástico sejam prejudicadas ao exceder esses limites.

## <a name="installation-and-migration"></a>Instalação e migração

Os recursos para transações de banco de dados elástico são fornecidos por meio de atualizações para as bibliotecas .NET System.Data.dll e System.Transactions.dll. As DLLs garantem que o protocolo 2PC seja usado quando necessário para garantir a atomicidade. Para começar a desenvolver aplicativos usando as transações de banco de dados elástico, instale o [.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) ou uma versão posterior. Quando executadas em uma versão anterior do .NET Framework, as transações não serão promovidas a uma transação distribuída, e uma exceção será gerada.

Após a instalação, você pode usar as APIs de transação distribuída no System. Transactions com conexões com o banco de dados SQL e Instância Gerenciada. Se você tiver aplicativos MSDTC existentes usando essas APIs, recompile os aplicativos existentes para o .NET 4,6 depois de instalar a estrutura 4.6.1. Se seus projetos tiverem como destino o .NET 4,6, eles usarão automaticamente as DLLs atualizadas da nova versão da estrutura e as chamadas da API de transação distribuída em combinação com as conexões com o banco de dados SQL ou Instância Gerenciada agora serão bem sucedidos.

Lembre-se de que transações de banco de dados elástico não exigem a instalação do MSDTC. Em vez disso, as transações de banco de dados elástico são gerenciadas diretamente pelo e dentro do serviço. Isso simplifica significativamente os cenários de nuvem, já que uma implantação do MSDTC não é necessária para usar transações distribuídas com o banco de dados SQL ou Instância Gerenciada. A Seção 4 explica detalhadamente como implantar as transações de banco de dados elástico e o .NET Framework necessário juntamente com seus aplicativos em nuvem no Azure.

## <a name="net-installation-for-azure-cloud-services"></a>Instalação do .NET para os Serviços de Nuvem do Azure

O Azure fornece várias ofertas para hospedar aplicativos .NET. Uma comparação entre as diferentes ofertas está disponível em [Comparação entre o Serviço de Aplicativo do Azure, os Serviços de Nuvem e as Máquinas Virtuais](/azure/architecture/guide/technology-choices/compute-decision-tree). Se o SO convidado da oferta for inferior ao .NET 4.6.1 exigido para transações elásticas, será necessário atualizar o SO para 4.6.1.

Para Azure App serviço, não há suporte para atualizações para o SO convidado no momento. Para as Máquinas virtuais do Azure, basta fazer logon na VM e executar o instalador do .NET Framework mais recente. Para os Serviços de Nuvem do Azure, você precisará incluir a instalação de uma versão mais recente do .NET em tarefas de inicialização da sua implantação. Os conceitos e as etapas estão documentados em [Instalar o .NET em uma Função do Serviço de Nuvem](../../cloud-services/cloud-services-dotnet-install-dotnet.md).  

Observe que o instalador do .NET 4.6.1 pode exigir mais armazenamento temporário durante o processo de inicialização nos Serviços de Nuvem do Azure que o instalador do .NET 4.6. Para garantir uma instalação bem-sucedida, você precisa aumentar o armazenamento temporário para o serviço de nuvem do Azure no seu arquivo ServiceDefinition.csdef na seção LocalResources e nas configurações do ambiente de sua tarefa de inicialização, conforme mostrado no exemplo a seguir:

```xml
    <LocalResources>
    ...
        <LocalStorage name="TEMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
        <LocalStorage name="TMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
        <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
            <Environment>
        ...
                <Variable name="TEMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TEMP']/@path" />
                </Variable>
                <Variable name="TMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TMP']/@path" />
                </Variable>
            </Environment>
        </Task>
    </Startup>
```

## <a name="net-development-experience"></a>Experiência de desenvolvimento do .NET

### <a name="multi-database-applications"></a>Aplicativos de vários bancos de dados

O exemplo de código a seguir usa a experiência de programação conhecida com o .NET System.Transactions. A classe TransactionScope estabelece uma transação de ambiente no .NET. (Uma "transação de ambiente" é aquela que reside no thread atual.) Todas as conexões abertas no TransactionScope participam da transação. Se diferentes bancos de dados participarem, a transação será elevada automaticamente a uma transação distribuída. O resultado da transação é controlado pela definição do escopo a concluir para indicar uma confirmação.

```csharp
    using (var scope = new TransactionScope())
    {
        using (var conn1 = new SqlConnection(connStrDb1))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = new SqlConnection(connStrDb2))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T2 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }
```

### <a name="sharded-database-applications"></a>Aplicativos de banco de dados fragmentado

As transações de banco de dados elástico para o banco de dados SQL e o Instância Gerenciada também oferecem suporte à coordenação de transações distribuídas em que você usa o método OpenConnectionForKey da biblioteca de cliente do banco de dado elástico para abrir conexões para uma camada expandida. Considere os casos em que você precisa garantir a consistência transacional das alterações em vários valores-chave de fragmentação diferentes. As conexões com os fragmentos que hospedam os diferentes valores-chave de fragmentação são intermediadas usando o OpenConnectionForKey. Em geral, as conexões podem ocorrer para diferentes fragmentos, de modo que para assegurar as garantias transacionais seja necessária uma transação distribuída.
O exemplo de código a seguir ilustra essa abordagem. Ele pressupõe que uma variável chamada shardmap seja usada para representar um mapa do fragmento na biblioteca do cliente de banco de dados elástico:

```csharp
    using (var scope = new TransactionScope())
    {
        using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
        {
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
        {
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T1 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }
```

## <a name="transact-sql-development-experience"></a>Experiência de desenvolvimento do Transact-SQL

As transações distribuídas no lado do servidor usando o Transact-SQL estão disponíveis somente para o Azure SQL Instância Gerenciada. A transação distribuída pode ser executada somente entre instâncias gerenciadas que pertencem ao mesmo [grupo de confiança do servidor](../managed-instance/server-trust-group-overview.md). Nesse cenário, as instâncias gerenciadas precisam usar o [servidor vinculado](/sql/relational-databases/linked-servers/create-linked-servers-sql-server-database-engine#TsqlProcedure) para fazer referência entre si.

O código Transact-SQL de exemplo a seguir usa [Iniciar transação distribuída](/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql) para iniciar a transação distribuída.

```Transact-SQL

    -- Configure the Linked Server
    -- Add one Azure SQL Managed Instance as Linked Server
    EXEC sp_addlinkedserver
        @server='RemoteServer', -- Linked server name
        @srvproduct='',
        @provider='sqlncli', -- SQL Server Native Client
        @datasrc='managed-instance-server.46e7afd5bc81.database.windows.net' -- Managed Instance endpoint

    -- Add credentials and options to this Linked Server
    EXEC sp_addlinkedsrvlogin
        @rmtsrvname = 'RemoteServer', -- Linked server name
        @useself = 'false',
        @rmtuser = '<login_name>',         -- login
        @rmtpassword = '<secure_password>' -- password

    USE AdventureWorks2012;
    GO
    SET XACT_ABORT ON;
    GO
    BEGIN DISTRIBUTED TRANSACTION;
    -- Delete candidate from local instance.
    DELETE AdventureWorks2012.HumanResources.JobCandidate
        WHERE JobCandidateID = 13;
    -- Delete candidate from remote instance.
    DELETE RemoteServer.AdventureWorks2012.HumanResources.JobCandidate
        WHERE JobCandidateID = 13;
    COMMIT TRANSACTION;
    GO
```

## <a name="combining-net-and-transact-sql-development-experience"></a>Combinando a experiência de desenvolvimento do .NET e do Transact-SQL

Aplicativos .NET que usam classes System. Transaction podem combinar a classe TransactionScope com a instrução Transact-SQL iniciar transação DISTRIBUÍda. No TransactionScope, a transação interna que executa a transação iniciar DITRIBUTED será explicitamente promovida para a transação distribuída. Além disso, quando o segundo SqlConnection for aberto dentro do TransactionScope, ele será promovido implicitamente para a transação distribuída. Depois que a transação distribuída for iniciada, todas as solicitações de transações subsequentes, se forem provenientes do .NET ou do Transact-SQL, ingressarão na transação distribuída pai. Como consequência, todos os escopos de transação aninhados iniciados pela instrução BEGIN terminarão na mesma transação e as instruções COMMIT/ROLLBACK terão o seguinte efeito sobre o resultado geral:
 * A instrução COMMIT não terá nenhum efeito sobre o escopo da transação iniciado pela instrução BEGIN, ou seja, nenhum resultado será confirmado antes de o método Complete () ser invocado no objeto TransactionScope. Se o objeto TransactionScope for destruído antes de ser concluído, todas as alterações feitas no escopo serão revertidas.
 * A instrução ROLLBACK fará com que o TransactionScope inteiro seja revertido. Qualquer tentativa de inscrever novas transações dentro do TransactionScope falhará posteriormente, bem como tentar invocar Complete () no objeto TransactionScope.

Aqui está um exemplo em que a transação é promovida explicitamente para a transação distribuída com Transact-SQL.

```csharp
    using (TransactionScope s = new TransactionScope())
    {
        using (SqlConnection conn = new SqlConnection(DB0_ConnectionString)
        {
            conn.Open();
        
            // Transaction is here promoted to distributed by BEGIN statement
            //
            Helper.ExecuteNonQueryOnOpenConnection(conn, "BEGIN DISTRIBUTED TRAN");
            // ...
        }
     
        using (SqlConnection conn2 = new SqlConnection(DB1_ConnectionString)
        {
            conn2.Open();
            // ...
        }
        
        s.Complete();
    }
```

O exemplo a seguir mostra uma transação que é promovida implicitamente para a transação distribuída depois que a segunda SqlConnection foi iniciada dentro do TransactionScope.

```csharp
    using (TransactionScope s = new TransactionScope())
    {
        using (SqlConnection conn = new SqlConnection(DB0_ConnectionString)
        {
            conn.Open();
            // ...
        }
        
        using (SqlConnection conn = new SqlConnection(DB1_ConnectionString)
        {
            // Because this is second SqlConnection within TransactionScope transaction is here implicitly promoted distributed.
            //
            conn.Open(); 
            Helper.ExecuteNonQueryOnOpenConnection(conn, "BEGIN DISTRIBUTED TRAN");
            Helper.ExecuteNonQueryOnOpenConnection(conn, lsQuery);
            // ...
        }
        
        s.Complete();
    }
```

## <a name="transactions-across-multiple-servers-for-azure-sql-database"></a>Transações em vários servidores para o banco de dados SQL do Azure

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do Banco de Dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para esses cmdlets, confira [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

As transações de banco de dados elástico têm suporte em diferentes servidores no banco de dados SQL do Azure. Quando as transações cruzam os limites do servidor, os servidores participantes precisam primeiro ser inseridos em uma relação de comunicação mútua. Após a relação de comunicação ser estabelecida, qualquer banco de dados em qualquer um dos dois servidores poderá participar de transações elásticas com bancos de dados do outro servidor. Com transações que abrangem mais de dois servidores, uma relação de comunicação precisa estar em vigor para qualquer par de servidores.

Use os cmdlets do PowerShell a seguir para gerenciar as relações de comunicação entre servidores para transações de Banco de Dados Elástico:

* **New-AzSqlServerCommunicationLink** : Use este cmdlet para criar uma nova relação de comunicação entre dois servidores no banco de dados SQL do Azure. A relação é simétrica, o que significa que ambos os servidores podem iniciar transações com o outro servidor.
* **Get-AzSqlServerCommunicationLink** : Use este cmdlet para recuperar relações de comunicação existentes e suas propriedades.
* **Remove-AzSqlServerCommunicationLink** : Use este cmdlet para remover uma relação de comunicação existente.

## <a name="transactions-across-multiple-servers-for-azure-sql-managed-instance"></a>Transações em vários servidores para o Azure SQL Instância Gerenciada

As transações distribuídas têm suporte em diferentes servidores no Azure SQL Instância Gerenciada. Quando as transações passam por limites de Instância Gerenciada, as instâncias participantes precisam primeiro ser inseridas em uma relação de segurança e comunicação mútua. Isso é feito com a criação de um [grupo de confiança do servidor](../managed-instance/server-trust-group-overview.md), que pode ser feito em portal do Azure. Se as instâncias gerenciadas não estiverem na mesma rede virtual, o [emparelhamento de rede virtual](../../virtual-network/virtual-network-peering-overview.md) precisará ser configurado e as regras de entrada e saída do grupo de segurança de rede precisarão permitir as portas 5024 e 11000-12000 em todas as redes virtuais de participação.

  ![Grupos de confiança do servidor no portal do Azure][3]

O diagrama a seguir mostra o grupo de confiança do servidor com instâncias gerenciadas que podem executar transações distribuídas com o .NET ou o Transact-SQL.

  ![Transações distribuídas com o Azure SQL Instância Gerenciada usando transações elásticas][2]

## <a name="monitoring-transaction-status"></a>Monitorando o status da transação

Use DMVs (exibições de gerenciamento dinâmico) para monitorar o status e o progresso de suas transações de banco de dados elástico contínuas. Todas as DMVs relacionadas a transações são relevantes para transações distribuídas no banco de dados SQL e Instância Gerenciada. Você pode encontrar a lista correspondente de DMVs aqui: [Funções e exibições de gerenciamento dinâmico relacionadas a transações (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql).

Estas DMVs são especialmente úteis:

* **sys.dm\_tran\_active\_transactions** : lista as transações atualmente ativas e seu status. A coluna UOW (Unidade de Trabalho) pode identificar as diferentes transações filho que pertencem à mesma transação distribuída. Todas as transações dentro da mesma transação distribuída carregam o mesmo valor UOW. Para obter mais informações, consulte a [documentação da DMV](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-active-transactions-transact-sql).
* **sys.dm\_tran\_database\_transactions** : fornece informações adicionais sobre as transações, como a colocação da transação no log. Para obter mais informações, consulte a [documentação da DMV](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-database-transactions-transact-sql).
* **sys.dm\_tran\_locks** : fornece informações sobre os bloqueios que são atualmente mantidos por transações em andamento. Para obter mais informações, consulte a [documentação da DMV](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql).

## <a name="limitations"></a>Limitações

As seguintes limitações atualmente se aplicam a transações de banco de dados elástico no banco de dados SQL:

* Há suporte apenas para transações entre bancos de dados no SQL Database. Outros provedores de recursos [X/Open XA](https://en.wikipedia.org/wiki/X/Open_XA) e bancos de dados fora do banco de dados SQL não podem participar de transações de banco de dados elástico. Isso significa que as transações de banco de dados elástico não podem ser ampliadas em SQL Server locais e no banco de dados SQL do Azure. Para transações distribuídas no local, continue a usar o MSDTC.
* Há suporte somente para transações coordenadas pelo cliente a partir de um aplicativo .NET. Há planos para suporte do lado do servidor para o T-SQL, como INICIAR TRANSAÇÃO DISTRIBUÍDA, mas ainda não está disponível.
* Não há suporte para transações entre serviços WCF. Por exemplo, você tem um método de serviço WCF que executa uma transação. Colocar a chamada dentro de um escopo de transação falhará como [System.ServiceModel.ProtocolException](/dotnet/api/system.servicemodel.protocolexception).

As seguintes limitações atualmente se aplicam a transações distribuídas no Instância Gerenciada:

* Há suporte apenas para transações entre bancos de dados no Instância Gerenciada. Outros provedores de recursos [X/Open XA](https://en.wikipedia.org/wiki/X/Open_XA) e bancos de dados fora do Azure SQL instância gerenciada não podem participar de transações distribuídas. Isso significa que as transações distribuídas não podem ser ampliadas entre SQL Server locais e o SQL Instância Gerenciada do Azure. Para transações distribuídas no local, continue a usar o MSDTC.
* Não há suporte para transações entre serviços WCF. Por exemplo, você tem um método de serviço WCF que executa uma transação. Colocar a chamada dentro de um escopo de transação falhará como [System.ServiceModel.ProtocolException](/dotnet/api/system.servicemodel.protocolexception).
* O Azure SQL Instância Gerenciada deve fazer parte de um [grupo de confiança do servidor](../managed-instance/server-trust-group-overview.md) para participar da transação distribuída.
* As limitações dos [grupos de confiança do servidor](../managed-instance/server-trust-group-overview.md) afetam as transações distribuídas.
* As instâncias gerenciadas que participam de transações distribuídas precisam ter conectividade sobre pontos de extremidade privados (usando o endereço IP privado da rede virtual onde eles são implantados) e precisam ser referenciadas mutuamente usando FQDNs privados. Os aplicativos cliente podem usar transações distribuídas em pontos de extremidade privados. Além disso, em casos em que o Transact-SQL aproveita os servidores vinculados que fazem referência a pontos de extremidade privados, os aplicativos cliente também podem usar transações distribuídas em pontos de extremidade públicos. Essa limitação é explicada no diagrama a seguir.
  ![Limitação de conectividade de ponto de extremidade privado][4]
## <a name="next-steps"></a>Próximas etapas

* Para dúvidas, entre em contato conosco no [Microsoft Q&uma página de perguntas para o banco de dados SQL](/answers/topics/azure-sql-database.html).
* Para solicitações de recursos, adicione-as ao [Fórum de comentários do banco de dados SQL](https://feedback.azure.com/forums/217321-sql-database/) ou ao [Fórum instância gerenciada](https://feedback.azure.com/forums/915676-sql-managed-instance).



<!--Image references-->
[1]: ./media/elastic-transactions-overview/distributed-transactions.png
[2]: ./media/elastic-transactions-overview/sql-mi-distributed-transactions.png
[3]: ./media/elastic-transactions-overview/server-trust-groups-azure-portal.png
[4]: ./media/elastic-transactions-overview/managed-instance-distributed-transactions-private-endpoint-limitations.png
