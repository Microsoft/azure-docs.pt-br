---
title: "Introdução às ferramentas de banco de dados elástico | Microsoft Docs"
description: "A explicação básica do recurso das ferramentas de banco de dados elástico do Banco de Dados SQL do Azure, incluindo um exemplo de aplicativo fácil de executar."
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: CarlRabeler
ms.assetid: b6911f8d-2bae-4d04-9fa8-f79a3db7129d
ms.service: sql-database
ms.custom: scale out apps
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: ddove
ms.openlocfilehash: 637463399593f4bc9ff5bfcbf67bf93b816efc7f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-elastic-database-tools"></a>Introdução às ferramentas do Banco de Dados Elástico
Este documento apresenta a experiência do desenvolvedor, ajudando você a executar o exemplo de aplicativo. A amostra cria um aplicativo fragmentado simples e explora os principais recursos das ferramentas de banco de dados elástico. O exemplo demonstra as funções da [biblioteca de cliente do banco de dados elástico](sql-database-elastic-database-client-library.md).

Para instalar a biblioteca, acesse [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). A biblioteca é instalada com o exemplo de aplicativo descrito na seção a seguir.

## <a name="prerequisites"></a>Pré-requisitos
* Visual Studio 2012 ou posterior com C#. Baixe a versão gratuita em [Downloads do Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* NuGet 2.7 ou posterior. Para obter a versão mais recente, veja [Instalação do NuGet](http://docs.nuget.org/docs/start-here/installing-nuget).

## <a name="download-and-run-the-sample-app"></a>Baixar e executar o aplicativo de amostra
O aplicativo de exemplo **Banco de dados elástico para SQL do Azure - Introdução** ilustra os aspectos mais importantes da experiência de desenvolvimento de aplicativos fragmentados que usam as ferramentas de banco de dados elástico. Ele se concentra nos principais casos de uso para [gerenciamento de mapa de fragmentos](sql-database-elastic-scale-shard-map-management.md), [roteamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md) e [consulta de vários fragmentos](sql-database-elastic-scale-multishard-querying.md). Para baixar e executar a amostra, siga estas etapas: 

1. Baixe as [Ferramentas de Banco de Dados Elástico para Azure SQL – Introdução](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-a80d8dc6) do MSDN. Descompacte o exemplo em um local de sua escolha.

2. Para criar um projeto, abra a solução **ElasticScaleStarterKit.sln** do diretório **C#**.

3. Na solução para o exemplo de projeto, abra o arquivo **app.config**. Siga as instruções no arquivo para adicionar o nome de seu servidor de Banco de Dados SQL do Azure e suas informações de entrada (nome de usuário e senha).

4. Compile e execute o aplicativo. Quando receber a solicitação, permita que o Visual Studio restaure os pacotes NuGet da solução. Isso baixa a versão mais recente das bibliotecas de cliente do banco de dados elástico do NuGet.

5. Experimente com as diferentes opções para saber mais sobre os recursos da biblioteca de clientes. Observe as etapas que o aplicativo realiza na saída do console e fique à vontade para explorar o código nos bastidores.
   
    ![Andamento][4]

Parabéns -- você criou e executou com sucesso seu primeiro aplicativo fragmentado usando as ferramentas de banco de dados elástico no Banco de Dados SQL. Use o Visual Studio ou o SQL Server Management Studio para conectar-se ao seu banco de dados SQL e observe rapidamente os fragmentos criados pelo exemplo. Você observará novos bancos de dados de fragmentos de amostra e um banco de dados de gerenciador de mapa de fragmentos que a amostra criou.

> [!IMPORTANT]
> Recomendamos que você sempre use a versão mais recente do Management Studio a fim de permanecer sincronizado com as atualizações no Azure e no Banco de Dados SQL. [Atualizar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

### <a name="key-pieces-of-the-code-sample"></a>Principais partes do exemplo de código
* **Gerenciamento de fragmentos e mapas de fragmentos**: o código ilustra como trabalhar com fragmentos, intervalos e mapeamentos no arquivo **ShardManagementUtils.cs**. Para saber mais, confira [Escale horizontalmente os bancos de dados com o gerenciador do mapa de fragmentos](http://go.microsoft.com/?linkid=9862595).  

* **Roteamento dependente de dados**: o roteamento de transações para o fragmento correto é mostrado em **DataDependentRoutingSample.cs**. Para obter mais detalhes, veja [Roteamento dependente de dados](http://go.microsoft.com/?linkid=9862596). 

* **Consulta em vários fragmentos**: a consulta entre os fragmentos é ilustrada no arquivo **MultiShardQuerySample.cs**. Para saber mais, confira [Consulta de vários fragmentos](http://go.microsoft.com/?linkid=9862597).

* **Consulta em vários fragmentos**: a adição iterativa de novos fragmentos vazios é realizada pelo código no arquivo **CreateShardSample.cs**. Para saber mais, confira [Escale horizontalmente os bancos de dados com o gerenciador do mapa de fragmentos](http://go.microsoft.com/?linkid=9862595).

### <a name="other-elastic-scale-operations"></a>Outras operações da escala elástica
* **Divisão de um fragmento existente**: a capacidade de dividir fragmentos é fornecida pela **ferramenta de divisão e mesclagem**. Para saber mais, confira [Mover dados entre bancos de dados na nuvem escalados horizontalmente](sql-database-elastic-scale-overview-split-and-merge.md).

* **Mesclagem de fragmentos existentes**: a mesclagem de fragmentos também é realizada usando a **ferramenta de divisão e mesclagem**. Para saber mais, confira [Mover dados entre bancos de dados na nuvem escalados horizontalmente](sql-database-elastic-scale-overview-split-and-merge.md).   

## <a name="cost"></a>Custo
As ferramentas de banco de dados elástico são gratuitas. Ao usar as ferramentas de banco de dados elástico, você não é cobrado por mais nada além do custo de uso do Azure. 

Por exemplo, o aplicativo de exemplo cria novos bancos de dados. O custo disso depende da edição de Banco de Dados SQL do Azure escolhida e do uso que o Azure faz de seu aplicativo.

Para obter informações sobre os preços, veja [Detalhes de preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre as ferramentas de banco de dados elástico, confira estas páginas:

* Exemplos de código: 
  * [Ferramentas de Banco de Dados Elástico para Azure SQL - Introdução](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE)
  * [Ferramentas de Banco de Dados Elástico para SQL Azure – Integração ao Entity Framework](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
  * [Elasticidade do fragmento no Script Center](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* Blog: [Anúncio da escala elástica](https://azure.microsoft.com/blog/2014/10/02/introducing-elastic-scale-preview-for-azure-sql-database/)
* Microsoft Virtual Academy: [implementando a fragmentação usando escala vertical com o vídeo da biblioteca do cliente do banco de dados elástico](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554?l=lWyQhF1fC_6306218965) 
* Channel 9: [Visão geral da Escala Elástica](http://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
* Fórum de discussão: [Fórum do Banco de Dados SQL do Azure](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)
* Para medir o desempenho: [Contadores de desempenho do gerenciador de mapas do fragmento](sql-database-elastic-database-client-library.md)

<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png

