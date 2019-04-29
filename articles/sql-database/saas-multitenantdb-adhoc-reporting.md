---
title: Executar consultas de relatório ad hoc em vários bancos de dados SQL do Azure | Microsoft Docs
description: Execute consultas de relatório ad hoc em vários bancos de dados SQL em um exemplo de aplicativo multilocatário.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: AyoOlubeko
ms.author: ayolubek
ms.reviewer: sstein
manager: craigg
ms.date: 10/30/2018
ms.openlocfilehash: d4c5a2ca88f982626c8c2a8b37e4a7d6dfdbe599
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485693"
---
# <a name="run-ad-hoc-analytics-queries-across-multiple-azure-sql-databases"></a>Executar consultas de análise ad hoc em vários bancos de dados SQL do Azure

Neste tutorial, você executa consultas distribuídas em todo o conjunto de bancos de dados de locatários para habilitar os relatórios ad hoc interativos. Essas consultas podem extrair insights escondidos nos dados operacionais diários do aplicativo SaaS Wingtip Tickets. Para fazer essas extrações, você implanta um banco de dados de análise adicional no servidor de catálogo e usa a Consulta Elástica para habilitar consultas distribuídas.


Neste tutorial, você aprende:

> [!div class="checklist"]
> 
> * Como implantar um banco de dados de relatórios ad hoc
> * Como executar consultas distribuídas entre todos os bancos de dados de locatário


Para concluir este tutorial, verifique se todos os pré-requisitos a seguir são atendidos:

* O aplicativo SaaS de Banco de Dados Multilocatário Wingtip Tickets foi implantado. Para implantá-lo em menos de cinco minutos, confira [Implantar e explorar o aplicativo SaaS de Banco de Dados Multilocatário Wingtip Tickets](saas-multitenantdb-get-started-deploy.md)
* O Azure PowerShell está instalado. Para obter detalhes, consulte [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* O SSMS (SQL Server Management Studio) está instalado. Para baixar e instalar a versão mais recente do SSMS, confira [Baixar o SSMS (SQL Server Management Studio)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="ad-hoc-reporting-pattern"></a>Padrão dos relatórios ad hoc

![padrão do relatório ad hoc](media/saas-multitenantdb-adhoc-reporting/adhocreportingpattern_shardedmultitenantDB.png)

Os aplicativos SaaS podem analisar a enorme quantidade de dados de locatários armazenados centralmente na nuvem. As análises revelam insights sobre a operação e o uso do aplicativo. Essas informações podem guiar o desenvolvimento do recurso, aprimoramentos de usabilidade e outros investimentos no aplicativo e nos serviços.

É fácil acessar esses dados em um único banco de dados multilocatário, mas não tão fácil quando são distribuídos em escala por, potencialmente, milhares de bancos de dados. Uma abordagem é usar a [Consulta Elástica](sql-database-elastic-query-overview.md), que permite consultas ad hoc em um conjunto distribuído de bancos de dados com esquema comum. Esses bancos de dados podem ser distribuídos entre diferentes grupos de recursos e assinaturas. Apesar disso, um logon comum deve ter acesso para extrair dados de todos os bancos de dados. A Consulta Elástica usa um único banco de dados *principal* no qual as tabelas externas são definidas que espelham tabelas ou modos de exibição nos bancos de dados distribuídos (de locatário). As consultas enviadas ao banco de dados principal são compiladas para produzir um plano de consulta distribuído, com partes da consulta propagadas para os bancos de dados de locatário, conforme necessário. A Consulta Elástica usa o mapa de fragmentos no banco de dados de catálogo para determinar o local de todos os bancos de dados de locatários. A instalação e a consulta são simples com o [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference) padrão e dão suporte a consultas ad hoc em ferramentas como o Power BI e o Excel.

Ao distribuir consultas entre os bancos de dados de locatário, a Consulta Elástica fornece ideias imediatas sobre dados de produção em tempo real. No entanto, como a Consulta Elástica extrai dados possivelmente de vários bancos de dados, a latência da consulta, às vezes, pode ser maior que para consultas equivalentes enviadas para um único banco de dados de vários locatários. Certifique-se de criar consultas para minimizar os dados retornados. A Consulta Elástica é geralmente mais adequada para consultar pequenas quantidades de dados em tempo real, em vez de criar consultas ou relatórios de análise complexos ou usados com frequência. Se as consultas não tiverem um bom desempenho, examine o [plano de execução](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) para ver qual parte da consulta foi propagada para o banco de dados remoto. Além disso, avalie a quantidade de dados que está sendo retornada. As consultas que exigem um processamento analítico complexo podem ser mais bem atendidas salvando os dados de locatários extraídos em um banco de dados otimizado para consultas de análise. O Banco de Dados SQL e o SQL Data Warehouse podem hospedar esse banco de dados de análise.

Esse padrão para análise é explicado no [tutorial de análise de locatário](saas-multitenantdb-tenant-analytics.md).

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Obter o código-fonte e os scripts do aplicativo de banco de dados multilocatário SaaS Wingtip Tickets

Os scripts e o código-fonte do aplicativo SaaS de Banco de Dados Multilocatário Wingtip Tickets estão disponíveis no repositório [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) do GitHub. Confira as [diretrizes gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter as etapas para baixar e desbloquear os scripts SaaS do Wingtip Tickets.

## <a name="create-ticket-sales-data"></a>Criar dados de vendas de ingresso

Para executar consultas em um conjunto de dados mais interessante, crie dados de vendas de ingresso executando o gerador de ingressos.

1. No *ISE do PowerShell*, abra o script ...\\Módulos de Aprendizado\\Operational Analytics\\Adhoc Reporting\\ *Demo-AdhocReporting.ps1* e defina os seguintes valores:
   * **$DemoScenario** = 1, **Comprar ingressos de eventos em todos os locais**.
2. Pressione **F5** para executar o script e gerar vendas de ingresso. Enquanto o script é executado, continue as etapas neste tutorial. Os dados de ingresso são consultados na seção *Executar consultas ad hoc distribuídas*; portanto, aguarde a conclusão do gerador de ingressos.

## <a name="explore-the-tenant-tables"></a>Explorar as tabelas de locatários 

No aplicativo SaaS de Banco de Dados Multilocatário Wingtip Tickets, os locatários são armazenados em um modelo híbrido de gerenciamento de locatários – em que os dados de locatários são armazenados em um banco de dados multilocatário ou em um banco de dados de locatário único e podem ser movidos entre os dois. No entanto, ao consultar em todos os bancos de dados de locatários, é importante que a Consulta Elástica possa tratar os dados como se fizessem parte de um único banco de dados lógico fragmentado por locatário. 

Para obter esse padrão, todas as tabelas de locatários incluem uma coluna *VenueId* que identifica a qual locatário os dados pertencem. A *VenueId* é calculada como um hash do nome Venue, mas qualquer abordagem pode ser usada para introduzir um valor exclusivo nessa coluna. Essa abordagem é semelhante à forma como a chave de locatário é calculada para uso no catálogo. As tabelas que contém *VenueId* são usadas pela Consulta Elástica para paralelizar consultas e enviá-las por push para o banco de dados de locatários remoto apropriado. Isso reduz consideravelmente a quantidade de dados retornados e resulta em um aumento no desempenho, especialmente quando há vários locatários cujos dados são armazenados em bancos de dados de locatário único.

## <a name="deploy-the-database-used-for-ad-hoc-distributed-queries"></a>Implantar o banco de dados usado para consultas ad hoc distribuídas

Este exercício implanta o banco de dados *adhocreporting*. Esse é o banco de dados principal que contém o esquema usado para consultar em todos os bancos de dados de locatário. O banco de dados é implantado no servidor de catálogo existente, que é o servidor usado para todos os bancos de dados relacionados ao gerenciamento no aplicativo de exemplo.

1. Abra ...\\Módulos de Aprendizado\\Análise Operacional\\Relatórios Ad hoc\\*Demo-AdhocReporting.ps1* no *ISE do PowerShell* e defina os seguintes valores:
   * **$DemoScenario** = 2, **Implantar banco de dados de análise Ad hoc**.

2. Pressione **F5** para executar o script e criar o banco de dados *adhocreporting*.

Na próxima seção, você adiciona o esquema ao banco de dados para que ele possa ser usado para executar consultas distribuídas.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Configurar o banco de dados ‘principal’ para executar consultas distribuídas

Este exercício adiciona o esquema (a fonte de dados externa e as definições de tabela externa) ao banco de dados de relatórios ad hoc que habilita a consulta em todos os bancos de dados de locatários.

1. Abra o SQL Server Management Studio e conecte-se ao banco de dados de relatórios Ad hoc criado na etapa anterior. O nome do banco de dados é *adhocreporting*.
2. Abra ...\Módulos de Aprendizado\Operational Analytics\Adhoc Reporting\ *Initialize-AdhocReportingDB.sql* no SSMS.
3. Examine o script SQL e observe o seguinte:

   A Consulta Elástica usa uma credencial com escopo de banco de dados para acessar cada um dos bancos de dados de locatário. Essa credencial precisa estar disponível em todos os bancos de dados e normalmente deve receber os direitos mínimos necessários para permitir essas consultas ad hoc.

    ![criar credencial](media/saas-multitenantdb-adhoc-reporting/create-credential.png)

   Usando o banco de dados do catálogo como a fonte de dados externa, as consultas são distribuídas para todos os bancos de dados registrados no catálogo quando a consulta é executada. Como os nomes de servidor são diferentes para cada implantação, esse script de inicialização obtém a localização do banco de dados de catálogo recuperando o servidor atual (@@servername) em que o script é executado.

    ![Criar fonte de dados externa](media/saas-multitenantdb-adhoc-reporting/create-external-data-source.png)

   As tabelas externas que referenciam as tabelas de locatários são definidas com **DISTRIBUTION = SHARDED(VenueId)**. Isso encaminha uma consulta de uma *VenueId* específica para o banco de dados apropriado e melhora o desempenho para muitos cenários, conforme mostrado na próxima seção.

    ![criar tabelas externas](media/saas-multitenantdb-adhoc-reporting/external-tables.png)

   A tabela local *VenueTypes*, que é criada e preenchida. Essa tabela de dados de referência é comum em todos os bancos de dados de locatário, portanto ela pode ser representada como uma tabela local e populada com os dados comuns. Para algumas consultas, isso pode reduzir a quantidade de dados movidos entre os bancos de dados de locatários e o banco de dados *adhocreporting*.

    ![criar tabela](media/saas-multitenantdb-adhoc-reporting/create-table.png)

   Se você incluir tabelas de referência dessa maneira, certifique-se de atualizar o esquema de tabela e os dados sempre que atualizar os bancos de dados de locatário.

4. Pressione **F5** para executar o script e inicializar o banco de dados *adhocreporting*. 

Agora você pode executar consultas distribuídas e reunir informações entre todos os locatários!

## <a name="run-ad-hoc-distributed-queries"></a>Executar consultas ad hoc distribuídas

Agora que o banco de dados *adhocreporting* está configurado, siga em frente e execute algumas consultas distribuídas. Inclua o plano de execução para uma melhor compreensão sobre onde o processamento de consultas está acontecendo. 

Ao inspecionar o plano de execução, passe o mouse sobre os ícones de plano para obter detalhes. 

1. Em *SSMS*, abra ...\\Módulos de Aprendizado\\Análise Operacional\\Relatórios Ad hoc\\*Demo-AdhocReportingQueries.sql*.
2. Verifique se você está conectado ao banco de dados **adhocreporting**.
3. Selecione o menu **Consulta** e clique em **Incluir Plano de Execução Atual**
4. Realce a consulta *Which venues are currently registered? (Quais locais estão registrados no momento?)* e pressione **F5**.

   A consulta retorna a lista de locais inteira, ilustrando o quão rápido e fácil é consultar todos os locatários e retornar dados de cada um.

   Inspecione o plano e veja que todo o custo é a consulta remota, pois simplesmente estamos indo até cada banco de dados de locatário e selecionando as informações de local.

   ![SELECT * FROM dbo.Venues](media/saas-multitenantdb-adhoc-reporting/query1-plan.png)

5. Selecione a próxima consulta e pressione **F5**.

   Essa consulta une dados dos bancos de dados de locatários e da tabela *VenueTypes* local (local, pois é uma tabela do banco de dados *adhocreporting*).

   Inspecione o plano e veja que a maior parte do custo é a consulta remota porque consultamos as informações de local de cada locatário (dbo.Venues) e, em seguida, realizamos uma rápida união com a tabela *VenueTypes* local para exibir o nome amigável.

   ![Unir dados remotos e locais](media/saas-multitenantdb-adhoc-reporting/query2-plan.png)

6. Agora selecione a consulta *On which day were the most tickets sold? (Em que dia a maioria dos ingressos foi vendida?)* e pressione **F5**.

   Essa consulta faz uma união e uma agregação um pouco mais complexas. O que é importante observar é que a maioria do processamento é feita remotamente e novamente, trazemos de volta apenas as linhas que precisamos, retornando apenas uma única linha para a contagem de venda de ingressos agregada por dia de cada local.

   ![query](media/saas-multitenantdb-adhoc-reporting/query3-plan.png)


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> 
> * Executar consultas distribuídas entre todos os bancos de dados de locatário
> * Implante um banco de dados de relatórios ad hoc e adicione o esquema a ele para executar consultas distribuídas.

Agora, experimente o [Tutorial de análise de locatário](saas-multitenantdb-tenant-analytics.md) para explorar a extração de dados para um banco de dados de análise separado para o processamento de análise mais complexo.

## <a name="additional-resources"></a>Recursos adicionais

<!-- ??
* Additional [tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->

* [Consulta elástica](sql-database-elastic-query-overview.md)
