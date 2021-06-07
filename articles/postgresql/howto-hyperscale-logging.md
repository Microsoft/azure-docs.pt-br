---
title: Logs-hiperescala (Citus)-banco de dados do Azure para PostgreSQL
description: Como acessar logs de banco de dados do banco de dados do Azure para PostgreSQL-Citus (hiperescala)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 7/13/2020
ms.openlocfilehash: ca3cc2873fbc6db72b10c80daecddf1471e30ff4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100577058"
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

- [Introdução às consultas do log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)
- Saiba mais sobre os [hubs de eventos do Azure](../event-hubs/event-hubs-about.md)