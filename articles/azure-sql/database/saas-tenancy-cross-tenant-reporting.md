---
title: Consultas de relatórios entre vários bancos de dados
description: Relatório entre locatários usando consultas distribuídas.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewers: ''
ms.date: 01/25/2019
ms.openlocfilehash: 18a02b81e459217ccca53d48a08e35a706b071b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793255"
---
# <a name="cross-tenant-reporting-using-distributed-queries"></a>Relatório entre locatários usando consultas distribuídas
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Neste tutorial, você executa consultas distribuídas em todo o conjunto de bancos de dados locatários para relatório. Essas consultas podem extrair informações escondidas nos dados operacionais diários dos locatários de SaaS do Wingtip Tickets. Para fazer isso, você implanta um banco de dados de relatório adicional no servidor de catálogo e usa a Consulta Elástica para habilitar consultas distribuídas.


Neste tutorial, você aprende:

> [!div class="checklist"]
> 
> * Como implantar um banco de dados de relatórios
> * Como executar consultas distribuídas entre todos os bancos de dados de locatário
> * Como as exibições globais em cada banco de dados podem permitir a consulta eficiente entre locatários


Para concluir este tutorial, verifique se todos os pré-requisitos a seguir são atendidos:


* O aplicativo Wingtip Tickets SaaS Database Per Tenant é implantado. Para implantá-lo em menos de cinco minutos, veja [Implantar e explorar o aplicativo Wingtip Tickets SaaS Database Per Tenant](./saas-dbpertenant-get-started-deploy.md)
* O Azure PowerShell está instalado. Para obter detalhes, consulte [Introdução ao Azure PowerShell](/powershell/azure/get-started-azureps)
* O SSMS (SQL Server Management Studio) está instalado. Para baixar e instalar a versão mais recente do SSMS, confira [Baixar o SSMS (SQL Server Management Studio)](/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="cross-tenant-reporting-pattern"></a>Padrão de relatório entre locatários

![padrão de consulta distribuída entre locatários](./media/saas-tenancy-cross-tenant-reporting/cross-tenant-distributed-query.png)

Uma boa oportunidade com aplicativos SaaS é poder usar a enorme quantidade de dados de locatário armazenados centralmente na nuvem para obter informações sobre a operação e o uso do seu aplicativo. Essas informações podem guiar o desenvolvimento do recurso, aprimoramentos de usabilidade e outros investimentos no aplicativo e nos serviços.

É fácil acessar esses dados em um único banco de dados multilocatário, mas não tão fácil quando são distribuídos em escala por, potencialmente, milhares de bancos de dados. Uma abordagem é usar a [Consulta Elástica](elastic-query-overview.md), que permite consultas ad hoc em um conjunto distribuído de bancos de dados com esquema comum. Esses bancos de dados podem ser distribuídos entre diferentes grupos de recursos e assinaturas, mas precisam compartilhar um logon comum. A Consulta Elástica usa um único banco de dados *principal* no qual as tabelas externas são definidas que espelham tabelas ou modos de exibição nos bancos de dados distribuídos (de locatário). As consultas enviadas ao banco de dados principal são compiladas para produzir um plano de consulta distribuído, com partes da consulta propagadas para os bancos de dados de locatário, conforme necessário. A Consulta Elástica usa o mapa de fragmentos no banco de dados de catálogo para determinar o local de todos os bancos de dados de locatários. A instalação e a consulta do banco de dados principal são simples com o [Transact-SQL](/sql/t-sql/language-reference) padrão e dão suporte a consultas em ferramentas como o Power BI e o Excel.

Ao distribuir consultas entre os bancos de dados de locatário, a Consulta Elástica fornece ideias imediatas sobre dados de produção em tempo real. Conforme a Consulta Elástica extrai dados possivelmente de vários bancos de dados, a latência da consulta pode ser maior que a de consultas equivalentes enviadas para um único banco de dados de vários locatários. Crie consultas para minimizar os dados retornados ao banco de dados principal. A Consulta Elástica é geralmente mais adequada para consultar pequenas quantidades de dados em tempo real, em vez de criar consultas ou relatórios de análise complexos ou usados com frequência. Se as consultas não tiverem um bom desempenho, examine o [plano de execução](/sql/relational-databases/performance/display-an-actual-execution-plan) para ver qual parte da consulta é propagada para o banco de dados remoto e a quantidade de dados sendo retornada. Consultas que exigem agregação ou processamento analítico complexo podem ser melhor atendidas pela extração de dados de locatário em um banco de dados dedicado ou data warehouse otimizado para consultas de análise. Esse padrão é explicado no [tutorial de análise de locatário](saas-tenancy-tenant-analytics.md). 

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obter os scripts do aplicativo Wingtip Tickets SaaS Database Per Tenant

Os scripts e o código-fonte do aplicativo SaaS de Banco de Dados Multilocatário Wingtip Tickets estão disponíveis no repositório [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) do GitHub. Confira as [diretrizes gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter as etapas para baixar e desbloquear os scripts SaaS do Wingtip Tickets.

## <a name="create-ticket-sales-data"></a>Criar dados de vendas de ingresso

Para executar consultas em um conjunto de dados mais interessante, crie dados de vendas de ingresso executando o gerador de ingressos.

1. No *ISE do PowerShell*, abra o script ...\\Módulos de Aprendizado\\Operational Analytics\\Adhoc Reporting\\*Demo-AdhocReporting.ps1* e defina os seguinte valor:
   * **$DemoScenario** = 1, **Comprar ingressos de eventos em todos os locais**.
2. Pressione **F5** para executar o script e gerar vendas de ingresso. Enquanto o script é executado, continue as etapas neste tutorial. Os dados de ingresso são consultados na seção *Executar consultas ad hoc distribuídas*; portanto, aguarde a conclusão do gerador de ingressos.

## <a name="explore-the-global-views"></a>Explore as exibições globais

No aplicativo Wingtip Tickets SaaS Database Per Tenant, cada locatário recebe um banco de dados. Portanto, os dados contidos nas tabelas de banco de dados estão no escopo da perspectiva de um único locatário. No entanto, ao consultar em todos os bancos de dados, é importante que a Consulta Elástica possa tratar os dados como se fizessem parte de um único banco de dados lógico fragmentado por locatário. 

Para simular este padrão, um conjunto de exibições 'globais' é adicionado ao banco de dados do locatário que projeta uma ID de locatário em cada uma das tabelas consultadas globalmente. Por exemplo, a exibição *VenueEvents* adiciona uma *VenueId* computada às colunas projetadas da tabela *Eventos*. Da mesma forma, as exibições *VenueTicketPurchases* e *VenueTickets* adicionam uma coluna *VenueId* computada projetada desde as respectivas tabelas. Essas exibiç~eos são usadas pela Consulta Elástica para paralelizar consultas e enviá-las por push para o banco de dados de locatário remoto adequado quando uma coluna *VenueId* estiver presente. Isso reduz significativamente a quantidade de dados retornada, o que resulta em um aumento significativo no desempenho para várias consultas. Essas exibições globais foram pré-criadas em todos os bancos de dados de locatário.

1. Abra o SSMS e [conecte-se ao servidor tenants1 -&lt;USER&gt;](saas-tenancy-wingtip-app-guidance-tips.md#explore-database-schema-and-execute-sql-queries-using-ssms).
1. Expanda **Bancos de Dados**, clique com botão direito do mouse em _contosoconcerthall_ e selecione **Nova Consulta**.
1. Execute as seguintes consultas para explorar a diferença entre as tabelas de único locatário e as exibições globais:

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

1. Em **Pesquisador de Objetos**, expanda **contosoconcerthall** > **Exibições**:

   ![Captura de tela mostra o conteúdo do nó "Exibições", incluindo quatro tipos de Local.](./media/saas-tenancy-cross-tenant-reporting/views.png)

2. Clique com botão direito do mouse em **dbo.Venues**.
3. Selecione **Modo de Exibição de Script como** > **CRIAR para** > **Janela do Editor Nova Consulta**

Gere o script de qualquer uma das outras exibições *Local* para ver como elas adicionam a *VenueId*.

## <a name="deploy-the-database-used-for-distributed-queries"></a>Implantar o banco de dados usado para consultas distribuídas

Este exercício implanta o banco de dados _adhocreporting_. Esse é o banco de dados principal que contém o esquema usado para consultar em todos os bancos de dados de locatário. O banco de dados é implantado no servidor de catálogo existente, que é o servidor usado para todos os bancos de dados relacionados ao gerenciamento no aplicativo de exemplo.

1. No *ISE do PowerShell*, abra ...\\Módulos de Aprendizado\\Análise Operacional\\Relatórios Ad hoc\\*Demo-AdhocReporting.ps1*. 

1. Defina **$DemoScenario = 2**, _Implantar banco de dados de relatórios de relatório ad hoc_.

1. Pressione **F5** para executar o script e criar o banco de dados *adhocreporting*.

Na próxima seção, você adiciona o esquema ao banco de dados para que ele possa ser usado para executar consultas distribuídas.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Configurar o banco de dados ‘principal’ para executar consultas distribuídas

Este exercício adiciona o esquema (a fonte de dados externa e as definições de tabela externa) ao banco de dados _adhocreporting_ para permitir a consulta em todos os bancos de dados de locatários.

1. Abra o SQL Server Management Studio e conecte-se ao banco de dados Relatórios Ad hoc criado na etapa anterior. O nome do banco de dados é *adhocreporting*.
2. Abra ...\Módulos de Aprendizado\Operational Analytics\Adhoc Reporting\ _Initialize-AdhocReportingDB.sql_ no SSMS.
3. Examine o script SQL e observe:

   A Consulta Elástica usa uma credencial com escopo de banco de dados para acessar cada um dos bancos de dados de locatário. Essa credencial precisa estar disponível em todos os bancos de dados e normalmente deve receber os direitos mínimos necessários para permitir essas consultas.

    ![criar credencial](./media/saas-tenancy-cross-tenant-reporting/create-credential.png)

   Com o banco de dados do catálogo como a fonte de dados externa, as consultas são distribuídas para todos os bancos de dados registrados no catálogo no momento em que a consulta é executada. Como os nomes de servidor são diferentes para cada implantação, essa inicialização obtém a localização do banco de dados de catálogo do servidor atual (@@servername) em que o script é executado.

    ![Criar fonte de dados externa](./media/saas-tenancy-cross-tenant-reporting/create-external-data-source.png)

   As tabelas externas que fazem referência a exibições globais descritas na seção anterior e definidas com **DISTRIBUTION = SHARDED(VenueId)**. Como cada *VenueId* mapeia para um banco de dados individual, isso melhora o desempenho em muitos cenários, como mostrado na próxima seção.

    ![criar tabelas externas](./media/saas-tenancy-cross-tenant-reporting/external-tables.png)

   A tabela local _VenueTypes_, que é criada e preenchida. Essa tabela de dados de referência é comum em todos os bancos de dados de locatário, portanto ela pode ser representada como uma tabela local e populada com os dados comuns. Para algumas consultas, ter essa tabela definida no banco de dados principal pode reduzir a quantidade de dados que precisam ser movidos para o banco de dados principal.

    ![criar tabela](./media/saas-tenancy-cross-tenant-reporting/create-table.png)

   Se você incluir tabelas de referência dessa maneira, certifique-se de atualizar o esquema de tabela e os dados sempre que atualizar os bancos de dados de locatário.

4. Pressione **F5** para executar o script e inicializar o banco de dados *adhocreporting*. 

Agora você pode executar consultas distribuídas e reunir informações entre todos os locatários!

## <a name="run-distributed-queries"></a>Executar consultas distribuídas

Agora que o banco de dados *adhocreporting* está configurado, siga em frente e execute algumas consultas distribuídas. Inclua o plano de execução para uma melhor compreensão sobre onde o processamento de consultas está acontecendo. 

Ao inspecionar o plano de execução, passe o mouse sobre os ícones de plano para obter detalhes. 

É importante observar que configurar **DISTRIBUTION = SHARDED(VenueId)**, quando definida a fonte de dados externa, melhora o desempenho em muitos cenários. Como cada *VenueId* mapeia para um banco de dados individual, a filtragem é facilmente feita de maneira remota, retornando apenas os dados necessários.

1. Abra ...\\Módulos de Aprendizado\\Análise Operacional\\Relatórios Ad hoc\\*Demo-AdhocReportingQueries.sql* no SSMS.
2. Verifique se você está conectado ao banco de dados **adhocreporting**.
3. Selecione o menu **Consulta** e clique em **Incluir Plano de Execução Atual**
4. Realce a consulta *Which venues are currently registered? (Quais locais estão registrados no momento?)* e pressione **F5**.

   A consulta retorna a lista de locais inteira, ilustrando o quão rápido e fácil é consultar todos os locatários e retornar dados de cada um.

   Inspecione o plano e veja que o custo total está na consulta remota. Cada banco de dados do locatário executa a consulta remotamente e retorna suas informações de local para o banco de dados principal.

   ![SELECT * FROM dbo.Venues](./media/saas-tenancy-cross-tenant-reporting/query1-plan.png)

5. Selecione a próxima consulta e pressione **F5**.

   Essa consulta une dados dos bancos de dados de locatários e da tabela *VenueTypes* local (local, pois é uma tabela do banco de dados *adhocreporting*).

   Inspecionar o plano e veja que a maior parte do custo é a consulta remota. Cada banco de dados de locatário retorna suas informações de local e executa uma junção local com a tabela *VenueTypes* para exibir o nome amigável.

   ![Unir dados remotos e locais](./media/saas-tenancy-cross-tenant-reporting/query2-plan.png)

6. Agora selecione a consulta *On which day were the most tickets sold? (Em que dia a maioria dos ingressos foi vendida?)* e pressione **F5**.

   Essa consulta faz uma união e uma agregação um pouco mais complexas. A maior parte do processamento ocorre remotamente.  Apenas linhas simples contendo a contagem de venda diária de ingressos são retornadas para o banco de dados principal.

   ![Consulta](./media/saas-tenancy-cross-tenant-reporting/query3-plan.png)


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> 
> * Executar consultas distribuídas entre todos os bancos de dados de locatário
> * Implante um banco de dados de relatório e defina o esquema necessário para executar consultas distribuídas.


Agora, experimente o [Tutorial de análise de locatário](saas-tenancy-tenant-analytics.md) para explorar a extração de dados para um banco de dados de análise separado para o processamento de análise mais complexo.

## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais criados com base no aplicativo implantação inicial do aplicativo Wingtip Tickets SaaS Database Per Tenant](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Consulta elástica](elastic-query-overview.md)