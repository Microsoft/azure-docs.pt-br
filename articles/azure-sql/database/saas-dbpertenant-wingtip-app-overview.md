---
title: Exemplo de aplicativo multilocatário – SaaS Wingtip
description: Aprenda usando um aplicativo de multilocatário de exemplo que usa o Banco de Dados SQL do Azure, o exemplo do Wingtip SaaS
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: 0efc8105295ca7f97e6f465071c31b193173c5cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100588729"
---
# <a name="introduction-to-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-azure-sql-database"></a>Introdução a um aplicativo SaaS multilocatário que usa o padrão de banco de dados por locatário com o Banco de Dados SQL do Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

O aplicativo SaaS Wingtip é um exemplo de aplicativo multilocatário. O aplicativo usa o padrão de aplicativo SaaS de um banco de dados por locatário para atender a vários locatários. O aplicativo demonstra recursos do Banco de Dados SQL do Azure que habilitam cenários de SaaS usando vários padrões de design e gerenciamento de SaaS. Para obter rapidamente e executar, o aplicativo Wingtip SaaS é implantado em menos de cinco minutos.

O código-fonte do aplicativo e os scripts de gerenciamento estão disponíveis no repositório [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) do GitHub. Antes de começar, veja as [diretrizes gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter as etapas a fim de fazer o download e desbloquear os scripts SaaS do Wingtip Tickets.

## <a name="application-architecture"></a>Arquitetura do aplicativo

O aplicativo de SaaS Wingtip usa o modelo de banco de dados por locatário. Ele usa pools elásticos do SQL para maximizar a eficiência. Para provisionar e mapear locatários para os seus dados, é usado um banco de dados do catálogo. O aplicativo SaaS Wingtip principal, usa um pool com três locatários de exemplo, além do banco de dados do catálogo. Os servidores de catálogo e locatário foram provisionados com aliases DNS. Esses aliases são usados para manter uma referência aos recursos ativos usados pelo aplicativo Wingtip. Esses aliases são atualizados para apontar para recursos de recuperação nos tutoriais de recuperação de desastre. A conclusão de muitos dos tutoriais de SaaS do Wingtip resulta em complementos para a implantação inicial. Complementos, como bancos de dados analíticos e gerenciamento de esquemas entre bancos de dados, são apresentados.


![Arquitetura de SaaS Wingtip](./media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


Ao percorrer os tutoriais e trabalhar com o aplicativo, tenha em mente os padrões de SaaS como eles se relacionam com a camada de dados. Em outras palavras, concentre-se na camada de dados e não na análise excessiva do aplicativo em si. É fundamental reconhecer a implementação desses padrões de SaaS para a implementação desses padrões em seus aplicativos. Também considere todas as modificações necessárias para suas necessidades de negócios específicas.

## <a name="sql-database-wingtip-saas-tutorials"></a>Tutoriais do SaaS Wingtip do Banco de Dados SQL

Depois de implantar o aplicativo, explore a seguinte coleção de tutoriais que aproveitam a implantação inicial. Esses tutoriais exploram padrões comuns de SaaS que aproveitam os recursos internos do Banco de Dados SQL, do Azure Synapse Analytics e outros serviços do Azure. Os tutoriais incluem scripts do PowerShell com explicações detalhadas. As explicações simplificam a compreensão e a implementação dos mesmos padrões de gerenciamento de SaaS em seus aplicativos.


| Tutorial | Descrição |
|:--|:--|
| [Diretrizes e dicas para o exemplo de aplicativo SaaS de multilocatário do Banco de Dados SQL](saas-tenancy-wingtip-app-guidance-tips.md) | Baixe e execute scripts do PowerShell para preparar as partes do aplicativo. |
|[Implantar e explorar o aplicativo SaaS Wingtip](./saas-dbpertenant-get-started-deploy.md)|  Implantar e explorar o aplicativo SaaS Wingtip com sua assinatura do Azure. |
|[Provisionar e catalogar locatários](./saas-dbpertenant-provision-and-catalog.md)| Saiba como o aplicativo se conecta aos locatários usando um banco de dados do catálogo, e como o catálogo mapeia locatários para seus dados. |
|[Monitorar e gerenciar o desempenho](./saas-dbpertenant-performance-monitoring.md)| Saiba como usar os recursos de monitoramento do Banco de Dados SQL e definir alertas quando os limites de desempenho são excedidos. |
|[Monitorar com os logs do Azure Monitor](./saas-dbpertenant-log-analytics.md) | Saiba mais sobre como usar os [logs do Azure Monitor](../../azure-monitor/logs/log-query-overview.md) para monitorar grandes quantidades de recursos, em vários pools. |
|[Restaurar um único locatário](./saas-dbpertenant-restore-single-tenant.md)| Saiba como restaurar um banco de dados de locatário em um ponto anterior no tempo. Também saiba como restaurar um banco de dados paralelo, o que deixa o banco de dados existente do locatário online. |
|[Gerenciar o esquema de banco de dados do locatário](saas-tenancy-schema-management.md)| Saiba como atualizar o esquema e atualizar dados de referência, em todos os bancos de dados de locatários. |
|[Executar consultas distribuídas entre locatários](saas-tenancy-cross-tenant-reporting.md) | Crie um banco de dados de análise ad hoc e execute consultas distribuídas em tempo real em todos os locatários.  |
|[Executar análise em dados extraídos de locatário](saas-tenancy-tenant-analytics.md) | Extraia dados de locatário em um análise banco de dados ou data warehouse para consultas analíticas offline. |


## <a name="next-steps"></a>Próximas etapas

- [Diretrizes gerais e dicas ao implementar e usar o exemplo de aplicativo SaaS Wingtip Tickets](saas-tenancy-wingtip-app-guidance-tips.md)
- [Implantar o aplicativo SaaS Wingtip](./saas-dbpertenant-get-started-deploy.md)