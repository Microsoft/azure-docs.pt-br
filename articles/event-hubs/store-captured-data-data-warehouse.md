---
title: 'Tutorial: Migrar dados de evento para o Azure Synapse Analytics – Hubs de Eventos do Azure'
description: Descreve como usar a Grade de Eventos do Azure e o Functions para migrar dados capturados pelos Hubs de Eventos para o Azure Synapse Analytics.
services: event-hubs
ms.date: 12/07/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 77a2b35dea57c71e9e6f07056bd106df2ac109b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96854064"
---
# <a name="tutorial-migrate-captured-event-hubs-data-to-azure-synapse-analytics-using-event-grid-and-azure-functions"></a>Tutorial: Migrar os dados dos Hubs de Eventos capturados para o Azure Synapse Analytics usando a Grade de Eventos e o Azure Functions
A [Captura](./event-hubs-capture-overview.md) dos Hubs de Eventos do Azure habilita você a capturar automaticamente os dados de streaming nos Hubs de Eventos em uma conta do Armazenamento de Blobs do Azure ou do Azure Data Lake Storage. Este tutorial mostra como migrar dados capturados pelos Hubs de Eventos do Armazenamento ao Azure Synapse Analytics usando uma função do Azure disparada pela [Grade de Eventos](../event-grid/overview.md).

[!INCLUDE [event-grid-event-hubs-functions-synapse-analytics.md](../../includes/event-grid-event-hubs-functions-synapse-analytics.md)]

## <a name="next-steps"></a>Próximas etapas 
Você pode usar as ferramentas de visualização de dados avançada com o data warehouse para obter insights acionáveis.

Este artigo mostra como usar o [Power BI com o Azure Synapse Analytics](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)