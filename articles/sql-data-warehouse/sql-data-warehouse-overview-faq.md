---
title: Perguntas frequentes sobre o Azure SQL Data Warehouse | Microsoft Docs
description: Este artigo lista as perguntas frequentes sobre o Azure SQL Data Warehouse de clientes e desenvolvedores
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: 
ms.assetid: 812CA525-3BF3-49DF-8DF3-FB4342464F4F
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: overview
ms.date: 3/1/2017
ms.author: elbutter;barbkess
ms.openlocfilehash: 4c00710ecc0c91f8407eca81b78176075fcbd6ad
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="sql-data-warehouse-frequently-asked-questions"></a>Perguntas frequentes sobre o SQL Data Warehouse

## <a name="general"></a>Geral

P. O que o SQL DW oferece em relação à segurança de dados?

R. O SQL DW oferece diversas soluções para proteger dados como TDE e auditoria. Para saber mais, consulte [Segurança].

P. Em que local posso encontrar os padrões legais ou comerciais com os quais o SQL DW está em conformidade?

R. Visite a página [Conformidade da Microsoft] para ver várias ofertas de conformidade por produto, como SOC e ISO. Primeiro escolha pelo título Conformidade e, em seguida, expanda Azure na seção de serviços de nuvem no escopo da Microsoft no lado direito da página para ver quais serviços do Azure estão em conformidade.

P. Posso conectar o Power BI?

R. Sim! Embora o Power BI dê suporte à consulta direta com o SQL DW, ele não se destina a um grande número de usuários nem a dados em tempo real. Para o uso em produção do Power BI, recomendamos usar o Power BI no Azure Analysis Services ou no IaaS do Analysis Service. 

P. Quais são os limites de capacidade do SQL Data Warehouse?

R. Consulte nossa página de [limites de capacidade] atuais. 

P. Por que Escalar/Pausar/Retomar está demorando tanto para mim?

R. Uma variedade de fatores pode influenciar o tempo de computação das operações de gerenciamento. Um caso comum para operações de execução longa é a reversão transacional. Quando uma operação de escala ou pausa é iniciada, todas as sessões de entrada são bloqueadas e as consultas são esvaziadas. Para deixar o sistema em um estado estável, as transações devem ser revertidas antes do início de uma operação. Quanto maior o número e maior o tamanho do log de transações, por mais tempo a operação ficará interrompida na restauração do sistema para um estado estável.

## <a name="user-support"></a>Suporte ao usuário

P. Tenho uma solicitação de recurso. Para qual local devo enviá-la?

R. Caso tenha uma solicitação de recurso, envie-a para nossa página do [UserVoice]

P. Como posso fazer?

R. Para obter ajuda no desenvolvimento com o SQL Data Warehouse, faça perguntas em nossa página do [Stack Overflow]. 

P. Como fazer para enviar um tíquete de suporte?

R. Os [Tíquetes de Suporte] podem ser apresentados por meio do portal do Azure.

## <a name="sql-languagefeature-support"></a>Suporte a linguagem/recurso do SQL 

P. Para quais tipos de dados o SQL Data Warehouse dá suporte?

R. Consulte [tipos de dados] do SQL Data Warehouse.

P. Para quais recursos de tabela há suporte?

R. Embora o SQL Data Warehouse dê suporte a vários recursos, não há suporte para alguns deles, que são documentados em [Recursos de tabela sem suporte].

## <a name="tooling-and-administration"></a>Ferramentas e administração

P. Há suporte para projetos de Banco de Dados no Visual Studio?

R. Atualmente, não há suporte para projetos de Banco de Dados no Visual Studio para o SQL Data Warehouse. Se desejar votar para obter esse recurso, visite nossa [solicitação de recursos de projetos de Banco de Dados] do User Voice.

P. O SQL Data Warehouse dá suporte a APIs REST?

R. Sim. A maioria das funcionalidades REST que pode ser usada com o Banco de Dados SQL também está disponível no SQL Data Warehouse. É possível encontrar informações sobre API nas páginas de documentação do REST ou no [MSDN].


## <a name="loading"></a>Carregando

P. Para quais drivers de cliente há suporte?

R. O suporte ao driver para o DW pode ser encontrado na página [Cadeias de conexão]

P: Para quais formatos de arquivo há suporte no PolyBase com o SQL Data Warehouse?

R: Orc, RC, Parquet e texto simples delimitado

P: Ao que posso me conectar do SQL DW usando o PolyBase? 

R: O [Azure Data Lake Store] e o [Azure Storage Blobs]

P: A aplicação de computação é possível ao conectar aos Azure Storage Blobs ou ao ADLS? 

R: Não, o PolyBase do SQL DW interage apenas com os componentes de armazenamento. 

P: Posso me conectar ao HDI?

R: O HDI pode usar o ADLS ou o WASB como a camada HDFS. Se você tiver como a camada HDFS, será possível carregar esse dados no SQL DW. No entanto, não é possível gerar o cálculo de aplicação para a instância HDI. 

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre o SQL Data Warehouse como um todo, consulte nossa página [Visão geral].


<!-- Article references -->
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Cadeias de conexão]: ./sql-data-warehouse-connection-strings.md
[Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Tíquetes de Suporte]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Segurança]: ./sql-data-warehouse-overview-manage-security.md
[Conformidade da Microsoft]: https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings
[limites de capacidade]: ./sql-data-warehouse-service-capacity-limits.md
[tipos de dados]: ./sql-data-warehouse-tables-data-types.md
[Recursos de tabela sem suporte]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Azure Data Lake Store]: ./sql-data-warehouse-load-from-azure-data-lake-store.md
[Azure Storage Blobs]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[solicitação de recursos de projetos de Banco de Dados]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
[MSDN]: https://msdn.microsoft.com/en-us/library/azure/mt163685.aspx
[Visão geral]: ./sql-data-warehouse-overview-faq.md