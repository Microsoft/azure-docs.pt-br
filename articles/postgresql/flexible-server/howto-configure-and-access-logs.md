---
title: Configurar e acessar logs-servidor flexível-banco de dados do Azure para PostgreSQL
description: Como acessar os logs de banco de dados para o Azure Database para PostgreSQL – servidor flexível
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 59a2ddcc68a7c5a3b6fa3a3b315f4294d1625204
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105607398"
---
# <a name="configure-and-access-logs-in-azure-database-for-postgresql---flexible-server"></a>Configurar e acessar logs no banco de dados do Azure para PostgreSQL – servidor flexível

> [!IMPORTANT]
> O Servidor Flexível do Banco de Dados do Azure para PostgreSQL está em versão prévia

Os logs do PostgreSQL estão disponíveis em todos os nós de um servidor flexível. Você pode enviar logs para um servidor de armazenamento ou para um serviço de análise. Os logs podem ser usados para identificar, solucionar problemas e reparar erros de configuração e desempenho inferior.

## <a name="configure-diagnostic-settings"></a>Definir as configurações de diagnóstico

Você pode habilitar as configurações de diagnóstico para o servidor postgres usando o portal do Azure, a CLI, a API REST e o PowerShell. A categoria de log a ser selecionada é **PostgreSQLLogs**.

Para habilitar os logs de recursos usando o portal do Azure:

1. No portal, vá para *configurações de diagnóstico* no menu de navegação do seu servidor do Postgres.
   
2. Selecione *Adicionar configuração de diagnóstico*.
   :::image type="content" source="media/howto-logging/diagnostic-settings.png" alt-text="Botão Adicionar configurações de diagnóstico":::

3. Nomeie essa configuração. 

4. Selecione seu ponto de extremidade preferido (conta de armazenamento, Hub de eventos, log Analytics). 

5. Selecione o tipo de log **PostgreSQLLogs**.
   :::image type="content" source="media/howto-logging/diagnostic-create-setting.png" alt-text="Escolher logs do PostgreSQL":::

7. Salve sua configuração.

Para habilitar logs de recursos usando o PowerShell, a CLI ou a API REST, visite o artigo [configurações de diagnóstico](../../azure-monitor/essentials/diagnostic-settings.md) .

### <a name="access-resource-logs"></a>Acessar logs de recursos

A maneira como você acessa os logs depende do ponto de extremidade escolhido. Para o armazenamento do Azure, consulte o artigo [conta de armazenamento de logs](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) . Para os hubs de eventos, consulte o artigo [fluxos de logs do Azure](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs) .

Para logs de Azure Monitor, os logs são enviados para o espaço de trabalho selecionado. Os logs do postgres usam o modo de coleta **AzureDiagnostics** , para que possam ser consultados a partir da tabela AzureDiagnostics. Os campos na tabela são descritos abaixo. Saiba mais sobre como consultar e alertar na visão geral de [consulta de logs de Azure monitor](../../azure-monitor/logs/log-query-overview.md) .

Veja a seguir as consultas que você pode tentar começar. Você pode configurar alertas com base em consultas.

Pesquisar todos os logs do postgres para um servidor específico no último dia

```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where Category == "PostgreSQLLogs"
| where TimeGenerated > ago(1d) 
```

Pesquisar todas as tentativas de conexão não localhost

```kusto
AzureDiagnostics
| where Message contains "connection received" and Message !contains "host=127.0.0.1"
| where Category == "PostgreSQLLogs" and TimeGenerated > ago(6h)
```

A consulta acima mostrará os resultados nas últimas 6 horas para qualquer log de servidor postgres nesse espaço de trabalho.

## <a name="next-steps"></a>Próximas etapas

- [Introdução às consultas do log Analytics](../../azure-monitor/logs/log-analytics-tutorial.md)
- Saiba mais sobre os [hubs de eventos do Azure](../../event-hubs/event-hubs-about.md)