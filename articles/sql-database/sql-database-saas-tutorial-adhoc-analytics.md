---
title: "Executar consultas de análise ad hoc entre vários bancos de dados Azure SQL | Microsoft Docs"
description: "Execute consultas de análise ad hoc em vários bancos de dados SQL em um aplicativo de exemplo multilocatário."
keywords: tutorial do banco de dados SQL
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: billgib; sstein
ms.openlocfilehash: a27a65627fecf35b59122110250a40c6fe8077b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="run-ad-hoc-analytics-queries-across-multiple-azure-sql-databases"></a>Executar consultas de análise ad hoc entre vários bancos de dados SQL do Azure

Neste tutorial, você executa consultas distribuídas em todo o conjunto de bancos de dados multilocatários para habilitar análises ad hoc. A Consulta Elástica é usada para habilitar consultas distribuídas, o que exige que um banco de dados de análise adicional seja implantado (para o servidor de catálogo). Essas consultas podem extrair informações escondidas nos dados operacionais diários do aplicativo de SaaS do Wingtip.


Neste tutorial, você aprende:

> [!div class="checklist"]

> * Sobre as exibições globais em cada banco de dados, que permitem a consulta eficiente entre locatários
> * Como implantar um banco de dados de análise ad hoc
> * Como executar consultas distribuídas entre todos os bancos de dados de locatário



Para concluir este tutorial, verifique se todos os pré-requisitos a seguir são atendidos:

* O aplicativo SaaS Wingtip é implantado. Para implantar em menos de cinco minutos, confira [Implantar e explorar o aplicativo de SaaS do Wingtip](sql-database-saas-tutorial.md)
* O Azure PowerShell está instalado. Para obter detalhes, consulte [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* O SSMS (SQL Server Management Studio) está instalado. Para baixar e instalar a versão mais recente do SSMS, confira [Baixar o SSMS (SQL Server Management Studio)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="ad-hoc-analytics-pattern"></a>Padrão de análise ad hoc

Uma das ótimas oportunidades com aplicativos SaaS é usar a grande quantidade de dados do locatário armazenados centralmente na nuvem. Use esses dados para obter informações sobre a operação e o uso do aplicativo. Essas informações podem guiar o desenvolvimento do recurso, aprimoramentos de usabilidade e outros investimentos no aplicativo e nos serviços.

É fácil acessar esses dados em um único banco de dados multilocatário, mas não tão fácil quando são distribuídos em escala por, potencialmente, milhares de bancos de dados. Uma abordagem é usar a [Consulta Elástica](sql-database-elastic-query-overview.md), que permite consultas ad hoc em um conjunto distribuído de bancos de dados com esquema comum. A Consulta Elástica usa um único banco de dados *principal* no qual as tabelas externas são definidas que espelham tabelas ou modos de exibição nos bancos de dados distribuídos (de locatário). As consultas enviadas ao banco de dados principal são compiladas para produzir um plano de consulta distribuído, com partes da consulta propagadas para os bancos de dados de locatário, conforme necessário. As Consulta Elásticas usam o mapa de fragmento no banco de dados de catálogo para fornecer o local dos bancos de dados de locatário. A instalação e a consulta são diretas usando o [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference) padrão e dá suporte para consultas ad hoc de ferramentas, como o Power BI e o Excel.

Ao distribuir consultas entre os bancos de dados de locatário, a Consulta Elástica fornece ideias imediatas sobre dados de produção em tempo real. No entanto, como a Consulta Elástica extrai dados possivelmente de vários bancos de dados, a latência da consulta, às vezes, pode ser maior que para consultas equivalentes enviadas para um único banco de dados de vários locatários. Certifique-se de criar consultas para minimizar os dados retornados. A Consulta Elástica é geralmente mais adequada para consultar pequenas quantidades de dados em tempo real, em vez de criar consultas ou relatórios de análise complexos ou usados com frequência. Se as consultas não tiverem um bom desempenho, examine o [plano de execução](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) para ver qual parte da consulta foi propagada para o banco de dados remoto e a quantidade de dados sendo retornada. Consultas que exigem processamento analítico complexo podem ser melhor atendidas em alguns casos extraindo dados de locatário em um banco de dados dedicado ou data warehouse otimizado para consultas de análise. Esse padrão é explicado no [tutorial de análise de locatário](sql-database-saas-tutorial-tenant-analytics.md). 

## <a name="get-the-wingtip-application-scripts"></a>Obter os scripts do aplicativo Wingtip

Os scripts de SaaS do Wingtip e o código-fonte do aplicativo estão disponíveis no repositório GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). [Etapas para baixar os scripts do SaaS Wingtip](sql-database-wtp-overview.md#download-and-unblock-the-wingtip-saas-scripts).

## <a name="create-ticket-sales-data"></a>Criar dados de vendas de ingresso

Para executar consultas em um conjunto de dados mais interessante, crie dados de vendas de ingresso executando o gerador de ingressos.

1. No *ISE do PowerShell*, abra o script ...\\Learning Modules\\Operational Analytics\\Adhoc Analytics\\*Demo-AdhocAnalytics.ps1* e defina os seguintes valores:
   * **$DemoScenario** = 1, **Comprar ingressos de eventos em todos os locais**.
2. Pressione **F5** para executar o script e gerar vendas de ingresso. Enquanto o script é executado, continue as etapas neste tutorial. Os dados de ingresso são consultados na seção *Executar consultas distribuídas ad hoc*, portanto, aguarde o gerador de ingressos ser concluído.

## <a name="explore-the-global-views"></a>Explore as exibições globais

O aplicativo SaaS Wingtip é criado usando um modelo de locatário por banco de dados e, por isso, o esquema de banco de dados do locatário é definido de uma perspectiva de locatário único. As informações específicas do locatário existem em uma tabela, *Local*, que sempre tem uma única linha e é implementada como um heap, sem uma chave primária. As outras tabelas no esquema não precisam estar relacionadas com a tabela *Local* porque, em condições normais, nunca existe dúvida sobre a qual locatário pertencem os dados.

No entanto, ao consultar em todos os bancos de dados, é importante que a Consulta Elástica possa tratar os dados como se fizessem parte de um único banco de dados lógico fragmentado por locatário. Para simular este padrão, um conjunto de exibições 'globais' é adicionado ao banco de dados do locatário que projeta uma ID de locatário em cada uma das tabelas consultadas globalmente. Por exemplo, a exibição *VenueEvents* adiciona uma *VenueId* computada às colunas projetadas da tabela *Eventos*. Ao definir a tabela externa no banco de dados principal em *VenueEvents* (em vez da tabela *Eventos* subjacente), A Consulta Elástica é capaz de enviar por push junções com base em *VenueId* para que elas possam ser executadas em paralelo em cada banco de dados remoto (e não no banco de dados principal). Isso reduz significativamente a quantidade de dados retornada, o que resulta em um aumento significativo no desempenho para várias consultas. Essas exibições globais foram pré-criadas em todos os bancos de dados de locatário (e em *basetenantdb*).

1. Abra o SSMS e [conecte-se ao servidor tenants1 -&lt;USER&gt;](sql-database-wtp-overview.md#explore-database-schema-and-execute-sql-queries-using-ssms).
2. Expanda **Bancos de Dados**, clique com botão direito do mouse em **contosoconcerthall**e selecione **Nova Consulta**.
3. Execute as seguintes consultas para explorar a diferença entre as tabelas de único locatário e as exibições globais:

   ```T-SQL
   -- The base Venue table, that has no VenueId associated.
   SELECT * FROM Venue

   -- Notice the plural name 'Venues'. This view projects a VenueId column.
   SELECT * FROM Venues

   -- The base Events table, which has no VenueId column.
   SELECT * FROM Events

   -- This view projects the VenueId retrieved from the Venues table.
   SELECT * FROM VenueEvents
   ```

Nessas exibições, a *VenueId* é calculada como um hash do nome local, mas qualquer abordagem pode ser usada para introduzir um valor exclusivo. Essa abordagem é semelhante à forma como a chave de locatário é calculada para uso no catálogo.

Para examinar a definição da exibição *Locais*:

1. Em **Gerenciador de Objeto**, expanda **contosoconcethall** > **Exibições**:

   ![Modos de exibição](media/sql-database-saas-tutorial-adhoc-analytics/views.png)

2. Clique com botão direito do mouse em **dbo.Venues**.
3. Selecione **Modo de Exibição de Script como** > **CRIAR para** > **Janela do Editor Nova Consulta**

Gere o script de qualquer uma das outras exibições *Local* para ver como elas adicionam a *VenueId*.

## <a name="deploy-the-database-used-for-ad-hoc-distributed-queries"></a>Implantar o banco de dados usado para consultas distribuídas ad hoc

Neste exercício implanta o banco de dados *adhocanalytics*. Esse é o banco de dados principal que contém o esquema usado para consultar em todos os bancos de dados de locatário. O banco de dados é implantado no servidor de catálogo existente, que é o servidor usado para todos os bancos de dados relacionados ao gerenciamento no aplicativo de exemplo.

1. Abra... \\Módulos de Aprendizado\\Análise Operacional\\Análise Adhoc\\*Demo-AdhocAnalytics.ps1* no *PowerShell ISE* e defina os seguintes valores:
   * **$DemoScenario** = 2, **Implantar banco de dados de análise Ad hoc**.

2. Pressione **F5** para executar o script e criar o banco de dados *adhocanalytics*.

Na próxima seção, você adiciona o esquema ao banco de dados para que ele possa ser usado para executar consultas distribuídas.

## <a name="configure-the-database-for-running-distributed-queries"></a>Configurar o banco de dados para executar consultas distribuídas

Este exercício adiciona o esquema (a fonte de dados externa e as definições de tabela externa) ao banco de dados de análise ad hoc que habilita a consulta em todos os bancos de dados de locatário.

1. Abra o SQL Server Management Studio e conecte-se ao banco de dados de Análise Ad hoc criado na etapa anterior. O nome do banco de dados é *adhocanalytics*.
2. Abra ...\Learning Modules\Operational Analytics\Adhoc Analytics\ *Initialize-AdhocAnalyticsDB.sql* no SSMS.
3. Examine o script SQL e observe o seguinte:

   A Consulta Elástica usa uma credencial com escopo de banco de dados para acessar cada um dos bancos de dados de locatário. Essa credencial precisa estar disponível em todos os bancos de dados e normalmente recebem os direitos mínimos para permitir essas consultas ad hoc.

    ![criar credencial](media/sql-database-saas-tutorial-adhoc-analytics/create-credential.png)

   A fonte de dados externa, que é definida para usar o mapa do fragmento locatário no banco de dados de catálogo. Usando isso como fonte de dados externa, as consultas são distribuídas para todos os bancos de dados registrados no catálogo quando a consulta é executada. Como os nomes de servidor são diferentes para cada implantação, esse script de inicialização obtém a localização do banco de dados de catálogo recuperando o servidor atual (@@servername) em que o script é executado.

    ![Criar fonte de dados externa](media/sql-database-saas-tutorial-adhoc-analytics/create-external-data-source.png)

   As tabelas externas que fazem referência a exibições globais descritas na seção anterior e definidas com **DISTRIBUTION = SHARDED(VenueId)**. Como cada *VenueId* mapeia para um banco de dados individual, isso melhora o desempenho em muitos cenários, como mostrado na próxima seção.

    ![criar tabelas externas](media/sql-database-saas-tutorial-adhoc-analytics/external-tables.png)

   A tabela local *VenueTypes*, que é criada e preenchida. Essa tabela de dados de referência é comum em todos os bancos de dados de locatário, portanto ela pode ser representada como uma tabela local e populada com os dados comuns. Para algumas consultas isso pode reduzir a quantidade de dados movidos entre os bancos de dados de locatário e o banco de dados *adhocanalytics*.

    ![criar tabela](media/sql-database-saas-tutorial-adhoc-analytics/create-table.png)

   Se você incluir tabelas de referência dessa maneira, certifique-se de atualizar o esquema de tabela e os dados sempre que atualizar os bancos de dados de locatário.

4. Pressione **F5** para executar o script e inicializar o banco de dados *adhocanalytics*. 

Agora você pode executar consultas distribuídas e reunir informações entre todos os locatários!

## <a name="run-ad-hoc-distributed-queries"></a>Executar consultas distribuídas ad hoc

Agora que o banco de dados *adhocanalytics* está configurado, prossiga e execute algumas consultas distribuídas. Inclua o plano de execução para uma melhor compreensão sobre onde o processamento de consultas está acontecendo. 

Ao inspecionar o plano de execução, passe o mouse sobre os ícones de plano para obter detalhes. 

É importante observar que configurar **DISTRIBUTION = SHARDED(VenueId)** quando definimos a fonte de dados externa melhora o desempenho em muitos cenários. Como cada *VenueId* mapeia para um banco de dados individual, a filtragem é facilmente feita de forma remota, retornando apenas os dados necessários.

1. Abra... \\Módulos de Aprendizado\\Análise Operacional\\Análise Ad Hoc\\*Demo-AdhocAnalyticsQueries.sql* no SSMS.
2. Verifique se está conectado ao banco de dados do **adhocanalytics**.
3. Selecione o menu **Consulta** e clique em **Incluir Plano de Execução Atual**
4. Realce a consulta *Which venues are currently registered? (Quais locais estão registrados no momento?)* e pressione **F5**.

   A consulta retorna a lista de locais inteira, ilustrando o quão rápido e fácil é consultar todos os locatários e retornar dados de cada um.

   Inspecione o plano e veja que todo o custo é a consulta remota, pois simplesmente estamos indo até cada banco de dados de locatário e selecionando as informações de local.

   ![SELECT * FROM dbo.Venues](media/sql-database-saas-tutorial-adhoc-analytics/query1-plan.png)

5. Selecione a próxima consulta e pressione **F5**.

   Essa consulta une dados de bancos de dados de locatário e da tabela *VenueTypes* local (local, como é uma tabela no banco de dados *adhocanalytics*).

   Inspecione o plano e veja que a maior parte do custo é a consulta remota porque consultamos as informações de local de cada locatário (dbo.Venues) e, em seguida, realizamos uma rápida união com a tabela *VenueTypes* local para exibir o nome amigável.

   ![Unir dados remotos e locais](media/sql-database-saas-tutorial-adhoc-analytics/query2-plan.png)

6. Agora selecione a consulta *On which day were the most tickets sold? (Em que dia a maioria dos ingressos foi vendida?)* e pressione **F5**.

   Essa consulta faz uma união e uma agregação um pouco mais complexas. O que é importante observar é que a maioria do processamento é feita remotamente e novamente, trazemos de volta apenas as linhas que precisamos, retornando apenas uma única linha para a contagem de venda de ingressos agregada por dia de cada local.

   ![query](media/sql-database-saas-tutorial-adhoc-analytics/query3-plan.png)


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]

> * Executar consultas distribuídas entre todos os bancos de dados de locatário
> * Implantar um banco de dados de análise ad hoc e adicionar o esquema a ele para executar consultas distribuídas.


Agora, experimente o [Tutorial de análise de locatário](sql-database-saas-tutorial-tenant-analytics.md) para explorar a extração de dados para um banco de dados de análise separado para o processamento de análise mais complexo.

## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que aproveitam o aplicativo de SaaS do Wingtip](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [Consulta elástica](sql-database-elastic-query-overview.md)
