---
title: Logs-hiperescala (Citus)-banco de dados do Azure para PostgreSQL
description: Como acessar logs de banco de dados do banco de dados do Azure para PostgreSQL-Citus (hiperescala)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 7/13/2020
ms.openlocfilehash: f8840d5115cb552ed203705d37f8c692b3418947
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492328"
---
# <a name="logs-in-azure-database-for-postgresql---hyperscale-citus"></a>Logs no banco de dados do Azure para PostgreSQL-Citus (hiperescala)

Os logs do PostgreSQL estão disponíveis em todos os nós de um grupo de servidores de hiperescala (Citus). Você pode enviar logs para um servidor de armazenamento ou para um serviço de análise. Os logs podem ser usados para identificar, solucionar problemas e reparar erros de configuração e desempenho inferior.

## <a name="accessing-logs"></a>Acessando os logs

Para acessar os logs do PostgreSQL para um coordenador ou nó de trabalho de hiperescala (Citus), abra o nó no portal do Azure:

:::image type="content" source="media/howto-hyperscale-logging/choose-node.png" alt-text="lista de nós":::

Para o nó selecionado, abra **configurações de diagnóstico** e clique em **+ Adicionar configuração de diagnóstico**.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-settings.png" alt-text="Botão Adicionar configurações de diagnóstico":::

Escolha um nome para as novas configurações de diagnóstico e marque a caixa **PostgreSQLLogs** .  Escolha quais destinos devem receber os logs.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-create-setting.png" alt-text="Escolher logs do PostgreSQL":::

## <a name="next-steps"></a>Próximas etapas

- [Introdução às consultas do log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md)
- Saiba mais sobre os [hubs de eventos do Azure](../event-hubs/event-hubs-about.md)