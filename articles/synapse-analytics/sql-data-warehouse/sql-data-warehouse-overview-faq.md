---
title: Perguntas frequentes sobre o pool dedicado do SQL (anteriormente SQL DW)
description: Este artigo lista as perguntas frequentes sobre o pool de SQL dedicado (anteriormente conhecido como SQL DW) no Azure Synapse Analytics de clientes e desenvolvedores.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: c8f8ae81f6f030245899ec82dbe16b29846dab23
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460520"
---
# <a name="dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-frequently-asked-questions"></a>Pool de SQL dedicado (anteriormente conhecido como SQL DW) em perguntas frequentes sobre o Azure Synapse Analytics

## <a name="general"></a>Geral

Q. O que é o Azure Synapse?

a. O Azure Synapse é um serviço de análise que reúne data warehousing e análise de Big Data. O Azure Synapse traz esses dois mundos junto com uma experiência unificada para ingerir, preparar, gerenciar e servir dados para necessidades de BI e aprendizado de máquina. Para obter mais informações, consulte [o que é o Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

Q. O que aconteceu com o Azure SQL Data Warehouse?

a. O Azure Synapse é um SQL Data Warehouse do Azure evoluído. Nós tiramos o mesmo data warehouse líder do setor para um nível totalmente novo de desempenho e recursos. Você pode continuar executando suas cargas de trabalho de data warehouse existentes em produção com o pool SQL dedicado (anteriormente conhecido como SQL DW) no Azure Synapse. Para saber mais, confira [O que é o Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

Q. O que é um pool SQL dedicado (anteriormente conhecido como SQL DW) no Azure Synapse Analytics?

a. O pool dedicado do SQL (anteriormente conhecido como SQL DW) refere-se aos recursos corporativos de data warehousing que estão geralmente disponíveis com o Azure Synapse. Para obter mais informações, consulte [o que é o Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

Q. Como fazer introdução ao Azure Synapse?

a. Você pode começar com uma [conta gratuita do Azure](https://azure.microsoft.com/free/sql-data-warehouse/) ou [entre em contato com as vendas para obter mais informações](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html).

Q. O que o Azure Synapse oferece para segurança de dados?

a. O Azure Synapse oferece várias soluções para proteger dados, como TDE e auditoria. Para saber mais, consulte [Segurança](sql-data-warehouse-overview-manage-security.md).

Q. Onde posso descobrir quais padrões legais ou de negócios o Azure Synapse está em conformidade?

a. Visite a página [Conformidade da Microsoft](https://www.microsoft.com/trustcenter/compliance/complianceofferings) para ver várias ofertas de conformidade por produto, como SOC e ISO. Primeiro, escolha por título de conformidade. Em seguida, expanda Azure na seção serviços de nuvem no escopo da Microsoft no lado direito da página para ver quais serviços são compatíveis com o Azure Synapse.

Q. Posso conectar Power BI?

a. Sim. Embora Power BI ofereça suporte à consulta direta com o Azure Synapse, ele não se destina a um grande número de usuários ou dados em tempo real. Para otimizar ainda mais o desempenho do Power BI, considere usar Power BI sobre o Azure Analysis Services ou o IaaS do Analysis Service.

Q. Quais são os limites de capacidade do pool de SQL dedicado (anteriormente conhecido como SQL DW)?

a. Consulte nossa página de [limites de capacidade](sql-data-warehouse-service-capacity-limits.md) atuais.

Q. Por que Escalar/Pausar/Retomar está demorando tanto para mim?

a. Vários fatores podem influenciar o tempo para operações de gerenciamento de computação. Um caso comum para operações de longa execução é a reversão transacional. Quando uma operação de escala ou pausa é iniciada, todas as sessões de entrada são bloqueadas e as consultas são esvaziadas. Para deixar o sistema em um estado estável, as transações devem ser revertidas antes do início de uma operação. Quanto maior o número e maior o tamanho do log de transações, por mais tempo a operação ficará interrompida na restauração do sistema para um estado estável.

## <a name="user-support"></a>Suporte ao usuário

Q. Tenho uma solicitação de recurso. Para qual local devo enviá-la?

a. Caso tenha uma solicitação de recurso, envie-a para nossa página do [UserVoice](https://feedback.azure.com/forums/307516-sql-data-warehouse)

Q. Como posso fazer?

a. Para obter ajuda com o desenvolvimento com o Azure Synapse, você pode fazer perguntas em nossa página de [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw) .

Q. Como fazer para enviar um tíquete de suporte?

a. Os [Tíquetes de Suporte](sql-data-warehouse-get-started-create-support-ticket.md) podem ser apresentados por meio do portal do Azure.

## <a name="sql-languagefeature-support"></a>Suporte a linguagem/recurso do SQL

Q. Há suporte para quais tipos de dados?

a. Consulte  [tipos de dados](sql-data-warehouse-tables-data-types.md).

Q. Para quais recursos de tabela há suporte?

a. Há suporte para muitos recursos. Recursos que não têm suporte podem ser encontrados em [recursos de tabela sem suporte](sql-data-warehouse-tables-data-types.md).

## <a name="tooling-and-administration"></a>Ferramentas e administração

Q. O pool de SQL dedicado (anteriormente conhecido como SQL DW) oferece suporte a APIs REST?

a. Sim. A maioria das funcionalidades REST que podem ser usadas com o banco de dados SQL também está disponível com o pool SQL dedicado (anteriormente conhecido como SQL DW). Você pode encontrar informações de API em [bancos](/rest/api/sql/databases?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)de dados ou páginas de documentação REST.

## <a name="loading"></a>Carregando

Q. Para quais drivers de cliente há suporte?

a. O suporte de driver para pool de SQL dedicado (anteriormente conhecido como SQL DW) pode ser encontrado na página [cadeias de conexão](sql-data-warehouse-connection-strings.md)

P: quais formatos de arquivo são compatíveis com o polybase?

R: Orc, RC, Parquet e texto simples delimitado

P: quais fontes de dados posso me conectar usando o polybase?

R: [Azure data Lake Storage](sql-data-warehouse-load-from-azure-data-lake-store.md) e [blobs de armazenamento do Azure](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

P: a aplicação de computação é possível ao se conectar a blobs de armazenamento do Azure ou ADLS?

R: não, o polybase interage apenas com os componentes de armazenamento.

P: Posso me conectar ao HDI?

R: O HDI pode usar o ADLS ou o WASB como a camada HDFS. Se você tiver o como sua camada HDFS, poderá carregar esses dados em um pool SQL dedicado (anteriormente conhecido como SQL DW). No entanto, não é possível gerar o cálculo de aplicação para a instância HDI.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o pool de SQL dedicado (anteriormente conhecido como SQL DW) no Azure Synapse, consulte nossa página de [visão geral](sql-data-warehouse-overview-what-is.md) .
