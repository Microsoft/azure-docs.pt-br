---
title: Introdução às ferramentas do Banco de Dados Elástico
description: A explicação básica do recurso das Ferramentas de Banco de Dados Elástico do Banco de Dados SQL do Azure, incluindo um exemplo de aplicativo fácil de executar.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: anumjs
ms.author: anjangsh
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 74343b2f05bb4a59e475449c87524ff66cdd605d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98919537"
---
# <a name="get-started-with-elastic-database-tools"></a>Introdução às ferramentas do Banco de Dados Elástico
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este documento apresenta a experiência do desenvolvedor para a [biblioteca de cliente do banco de dados elástico](elastic-database-client-library.md), ajudando você a executar um aplicativo de exemplo. O aplicativo de exemplo cria um aplicativo fragmentado simples e explora os principais recursos das Ferramentas de Banco de Dados Elástico do Banco de Dados SQL do Azure. Ele se concentra nos casos de uso para [gerenciamento de mapa de fragmentos](elastic-scale-shard-map-management.md), [roteamento dependente de dados](elastic-scale-data-dependent-routing.md) e [consulta de vários fragmentos](elastic-scale-multishard-querying.md). A biblioteca de clientes está disponível para .NET, bem como para Java.

## <a name="elastic-database-tools-for-java"></a>Ferramentas de Banco de Dados Elástico para Java

### <a name="prerequisites"></a>Pré-requisitos

* Um Java Developer Kit (JDK) versão 1.8 ou posterior
* [Maven](https://maven.apache.org/download.cgi)
* Banco de dados SQL ou uma instância de SQL Server local

### <a name="download-and-run-the-sample-app"></a>Baixar e executar o aplicativo de amostra

Para criar os arquivos JAR e começar a trabalhar com o projeto de exemplo, siga as etapas a seguir:

1. Clone o [repositório GitHub](https://github.com/Microsoft/elastic-db-tools-for-java) que contém a biblioteca de clientes junto com o aplicativo de exemplo.

2. Edite o arquivo _./sample/src/main/resources/resource.properties_ para definir o seguinte:
    * TEST_CONN_USER
    * TEST_CONN_PASSWORD
    * TEST_CONN_SERVER_NAME

3. Para criar o projeto de exemplo, no diretório _./sample_, execute o seguinte comando:

    ```
    mvn install
    ```

4. Para iniciar o projeto de exemplo, no diretório _./sample_, execute o seguinte comando:

    ```
    mvn -q exec:java "-Dexec.mainClass=com.microsoft.azure.elasticdb.samples.elasticscalestarterkit.Program"
    ```

5. Para saber mais sobre os recursos da biblioteca de clientes, experimente com as diferentes opções. Fique à vontade para explorar o código para saber mais sobre a implementação do aplicativo de exemplo.

    ![Progresso de java][5]

Parabéns! Você criou e executou com sucesso seu primeiro aplicativo fragmentado usando as Ferramentas de Banco de Dados Elástico no Banco de Dados SQL do Azure. Use o Visual Studio ou SQL Server Management Studio para se conectar ao seu banco de dados e dar uma olhada rápida nos fragmentos que o exemplo criou. Você observará novos bancos de dados de fragmentos de amostra e um banco de dados de gerenciador de mapa de fragmentos que a amostra criou.

Para adicionar a biblioteca de clientes a seu próprio projeto Maven, adicione a seguinte dependência em seu arquivo POM:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>elastic-db-tools</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="elastic-database-tools-for-net"></a>Ferramentas de Banco de Dados Elástico para .NET

### <a name="prerequisites"></a>Pré-requisitos

* Visual Studio 2012 ou posterior com C#. Baixe a versão gratuita em [Downloads do Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* NuGet 2.7 ou posterior. Para obter a versão mais recente, veja [Instalação do NuGet](https://docs.nuget.org/docs/start-here/installing-nuget).

### <a name="download-and-run-the-sample-app"></a>Baixar e executar o aplicativo de amostra

Para instalar a biblioteca, acesse [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). A biblioteca é instalada com o exemplo de aplicativo descrito na seção a seguir.

Para baixar e executar a amostra, siga estas etapas:

1. Baixe as [ferramentas de banco de BD elástico para o SQL do Azure-introdução exemplo](https://github.com/Azure/elastic-db-tools). Descompacte o exemplo em um local de sua escolha.

2. Para criar um projeto, abra a solução *ElasticDatabaseTools. sln* no diretório *elástico-DB-Tools-Master* . 

3. Defina o projeto *solução elasticscalestarterkit* como o projeto de inicialização.

4. No projeto *solução elasticscalestarterkit* , abra o arquivo *App.config* . Em seguida, siga as instruções no arquivo para adicionar o nome do servidor e suas informações de entrada (nome de usuário e senha).

5. Crie e execute o aplicativo. Quando você receber a solicitação, permita que o Visual Studio restaure os pacotes NuGet da solução. Essa ação faz o download da versão mais recente das bibliotecas de clientes do banco de dados elástico do NuGet.

6. Para saber mais sobre os recursos da biblioteca de clientes, experimente com as diferentes opções. Observe as etapas que o aplicativo realiza na saída do console e fique à vontade para explorar o código nos bastidores.

   ![Progresso][4]

Parabéns! Você criou e executou com sucesso seu primeiro aplicativo fragmentado usando as Ferramentas de Banco de Dados Elástico no Banco de Dados SQL. Use o Visual Studio ou SQL Server Management Studio para se conectar ao seu banco de dados e dar uma olhada rápida nos fragmentos que o exemplo criou. Você observará novos bancos de dados de fragmentos de amostra e um banco de dados de gerenciador de mapa de fragmentos que a amostra criou.

> [!IMPORTANT]
> Recomendamos que você sempre use a versão mais recente do Management Studio a fim de permanecer sincronizado com as atualizações no Azure e no Banco de Dados SQL. [Atualizar o SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

## <a name="key-pieces-of-the-code-sample"></a>Principais partes do exemplo de código

* **Gerenciamento de fragmentos e mapas de fragmentos**: o código ilustra como trabalhar com fragmentos, intervalos e mapeamentos no arquivo *ShardManagementUtils.cs*. Para saber mais, confira [Escale horizontalmente os bancos de dados com o gerenciador do mapa de fragmentos](https://go.microsoft.com/?linkid=9862595).  

* **Roteamento dependente de dados**: o roteamento de transações para o fragmento correto é mostrado no arquivo *DataDependentRoutingSample.cs*. Para obter mais detalhes, veja [Roteamento dependente de dados](https://go.microsoft.com/?linkid=9862596).

* **Consulta em vários fragmentos**: a consulta entre os fragmentos é ilustrada no arquivo *MultiShardQuerySample.cs*. Para saber mais, confira [Consulta de vários fragmentos](https://go.microsoft.com/?linkid=9862597).

* **Consulta em vários fragmentos**: a adição iterativa de novos fragmentos vazios é realizada pelo código no arquivo *CreateShardSample.cs*. Para saber mais, confira [Escale horizontalmente os bancos de dados com o gerenciador do mapa de fragmentos](https://go.microsoft.com/?linkid=9862595).

## <a name="other-elastic-scale-operations"></a>Outras operações da escala elástica

* **Divisão de um fragmento existente**: a capacidade de dividir fragmentos é fornecida pela ferramenta de divisão e mesclagem. Para saber mais, confira [Mover dados entre bancos de dados na nuvem escalados horizontalmente](elastic-scale-overview-split-and-merge.md).

* **Mesclagem de fragmentos existentes**: a mesclagem de fragmentos também é realizada usando a ferramenta de divisão e mesclagem. Para saber mais, confira [Mover dados entre bancos de dados na nuvem escalados horizontalmente](elastic-scale-overview-split-and-merge.md).

## <a name="cost"></a>Cost

A biblioteca das Ferramentas de Banco de Dados Elástico é gratuita. Ao usar as Ferramentas de Banco de Dados Elástico, você não é cobrado por mais nada além do custo de uso do Azure.

Por exemplo, o aplicativo de exemplo cria novos bancos de dados. O custo desse recurso depende da edição de Banco de Dados SQL do Azure escolhida e do uso que o Azure faz de seu aplicativo.

Para obter informações sobre preços, consulte [detalhes de preços do banco de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as Ferramentas de Banco de Dados Elástico, confira os arquivos a seguir:

* Exemplos de código:
  * Ferramentas do Banco de Dados Elástico ([.NET](https://github.com/Azure/elastic-db-tools), [Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22))
  * [Ferramentas de Banco de Dados Elástico para SQL Azure – Integração ao Entity Framework](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
  * [Elasticidade do fragmento no Script Center](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* Blog: [Anúncio da escala elástica](https://azure.microsoft.com/blog/20../../introducing-elastic-scale-preview-for-azure-sql-database/)
* Channel 9: [Visão geral da Escala Elástica](https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
* Fórum de discussão: [A Microsoft Q&uma página de perguntas para o banco de dados SQL do Azure](/answers/topics/azure-sql-database.html)
* Para medir o desempenho: [Contadores de desempenho do gerenciador de mapas do fragmento](elastic-database-client-library.md)

<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/elastic-scale-get-started/newProject.png
[2]: ./media/elastic-scale-get-started/click-online.png
[3]: ./media/elastic-scale-get-started/click-CSharp.png
[4]: ./media/elastic-scale-get-started/output2.png
[5]: ./media/elastic-scale-get-started/java-client-library.PNG