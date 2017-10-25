---
title: Tutorial da Adventure Works do Azure Analysis Services | Microsoft Docs
description: Apresenta o tutorial da Adventure Works para o Azure Analysis Services
services: analysis-services
documentationcenter: 
author: Minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 06/01/2017
ms.author: owend
ms.openlocfilehash: 81a378c6b1a15cc240ca30fa19b557312ca54922
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-analysis-services---adventure-works-tutorial"></a>Azure Analysis Services – tutorial da Adventure Works

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Este tutorial fornece lições sobre como criar e implantar um modelo tabular no nível de compatibilidade 1.400 usando [SSDT (SQL Server Data Tools)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).  

Se você estiver pouco familiarizado com o Analysis Services e a modelagem tabular, concluir este tutorial é a maneira mais rápida de aprender a criar e a implantar um modelo tabular básico. Uma vez satisfeitos todos o pré-requisitos, ele deverá levar cerca de duas ou três horas para ser concluído.  
  
## <a name="what-you-learn"></a>O que você aprenderá   
  
-   Como criar um novo projeto de modelo tabular no **nível de compatibilidade 1.400** no SSDT.
  
-   Como importar dados de um banco de dados relacional para um projeto de modelo tabular.  
  
-   Como criar e gerenciar relações entre tabelas no modelo.  
  
-   Como criar colunas calculadas, medidas e indicadores chave de desempenho que ajudam os usuários a analisar as métricas de negócios críticas.  
  
-   Como criar e gerenciar perspectivas e hierarquias que ajudam os usuários a procurar dados de modelo mais facilmente, fornecendo pontos de vista específicos de aplicativos e de negócios.  
  
-   Como criar partições que dividem dados de tabela em partes lógicas menores que podem ser processadas independentemente de outras partições.  
  
-   Como proteger os dados e objetos de modelo criando funções com membros de usuário.  
  
-   Como implantar um modelo tabular para um servidor do **Azure Analysis Services** ou um servidor local do SQL Server 2017 Analysis Services.  
  
## <a name="prerequisites"></a>Pré-requisitos  
Para concluir este tutorial, você precisará:  
  
-   Uma instância do Azure Analysis Services ou SQL Server 2017 Analysis Services na qual implantar seu modelo. Inscreva-se para uma [avaliação gratuita do Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) e [crie um servidor](../analysis-services-create-server.md). Ou então, inscreva-se e baixe o [SQL Server 2017 Community Technology Preview](https://www.microsoft.com/evalcenter/evaluate-sql-server-vnext-ctp). 

-   Um SQL Server Data Warehouse ou SQL Data Warehouse do Azure com o [banco de dados de exemplo AdventureWorksDW2014](http://go.microsoft.com/fwlink/?LinkID=335807). Esse banco de dados de exemplo inclui os dados necessários para concluir este tutorial. Baixe as [edições gratuitas do SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads). Ou então, inscreva-se para uma [avaliação gratuita do Banco de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/). 

    **Importante:** se você instalou o banco de dados de exemplo em um SQL Server local e você está implantando seu modelo em um servidor do Azure Analysis Services, um [gateway de dados local](../analysis-services-gateway.md) é necessário.

-   A versão mais recente do [SSDT (SQL Server Data Tools)](https://msdn.microsoft.com/library/mt204009.aspx).

-   A versão mais recente do [SSMS (SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)).    

-   Um aplicativo cliente como [Power BI Desktop](https://powerbi.microsoft.com/desktop/) ou Excel. 

## <a name="scenario"></a>Cenário  
Esse tutorial se baseia na Adventure Works Cycles, uma empresa fictícia. A Adventure Works é uma empresa multinacional de grande porte de manufatura, que produz e distribui bicicletas, peças e acessórios para mercados comerciais na América do Norte, Europa e Ásia. A empresa emprega 500 trabalhadores. Além disso, a Adventure Works emprega várias equipes regionais de vendas em toda a sua base de mercado. Seu projeto deve criar um modelo tabular para usuários de vendas e marketing analisarem dados de vendas pela Internet no banco de dados AdventureWorksDW.  
  
Para concluir o tutorial, você deverá concluir várias lições. Em cada lição, há tarefas. A conclusão de cada tarefa na ordem é necessária para concluir a lição. Embora em uma lição específica possam existir várias tarefas que geram um resultado semelhante, o modo como você conclui cada tarefa é ligeiramente diferente. Este método mostra que geralmente há mais de uma maneira de concluir uma tarefa e de desafiar você usando as habilidades aprendidas em tarefas e lições anteriores.  
  
A finalidade das lições é orientá-lo na criação de um modelo tabular básico usando muitos dos recursos incluídos no SSDT. Já que cada lição faz uso do conteúdo da lição anterior, você deve concluir as lições em ordem.
  
Este tutorial não fornece lições sobre como gerenciar um servidor no portal do Azure, gerenciar um servidor ou banco de dados usando o SSMS ou usando um aplicativo cliente para procurar dados de modelo. 


## <a name="lessons"></a>Lições  
Este tutorial inclui as seguintes lições:  
  
|Lição|Tempo estimado para conclusão|  
|----------|------------------------------|  
|[1 - Criar um novo projeto de modelo tabular](../tutorials/aas-lesson-1-create-a-new-tabular-model-project.md)|10 minutos|  
|[2 - Obter dados](../tutorials/aas-lesson-2-get-data.md)|10 minutos|  
|[3 - Marcar como tabela de data](../tutorials/aas-lesson-3-mark-as-date-table.md)|3 minutos|  
|[4 - Criar relações](../tutorials/aas-lesson-4-create-relationships.md)|10 minutos|  
|[5 - Criar colunas calculadas](../tutorials/aas-lesson-5-create-calculated-columns.md)|15 minutos|
|[6 - Criar medidas](../tutorials/aas-lesson-6-create-measures.md)|30 minutos|  
|[7 - Criar KPIs (indicadores chave de desempenho)](../tutorials/aas-lesson-7-create-key-performance-indicators.md)|15 minutos|  
|[8 - Criar perspectivas](../tutorials/aas-lesson-8-create-perspectives.md)|5 minutos|  
|[9 - Criar hierarquias](../tutorials/aas-lesson-9-create-hierarchies.md)|20 minutos|  
|[10 - Criar partições](../tutorials/aas-lesson-10-create-partitions.md)|15 minutos|  
|[11 - Criar funções](../tutorials/aas-lesson-11-create-roles.md)|15 minutos|  
|[12 - Analisar no Excel](../tutorials/aas-lesson-12-analyze-in-excel.md)|5 minutos| 
|[13 - Implantar](../tutorials/aas-lesson-13-deploy.md)|5 minutos|  
  
## <a name="supplemental-lessons"></a>Lições suplementares  
Essas lições não são necessárias para concluir este tutorial, mas podem ser úteis para uma melhor compreensão dos recursos avançados de criação de modelo tabular.  
  
|Lição|Tempo estimado para conclusão|  
|----------|------------------------------|  
|[Linhas de Detalhes](../tutorials/aas-supplemental-lesson-detail-rows.md)|10 minutos|
|[Segurança dinâmica](../tutorials/aas-supplemental-lesson-dynamic-security.md)|30 minutos|
|[Hierarquias desbalanceadas](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)|20 minutos| 

  
## <a name="next-steps"></a>Próximas etapas  
Para uma introdução, veja [Lição 1: criar um novo projeto de modelo tabular](../tutorials/aas-lesson-1-create-a-new-tabular-model-project.md).  
  
  
  

