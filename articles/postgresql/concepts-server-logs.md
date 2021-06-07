---
title: Logs-banco de dados do Azure para PostgreSQL-servidor único
description: Descreve a configuração de log, o armazenamento e a análise no banco de dados do Azure para PostgreSQL-servidor único
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/25/2020
ms.openlocfilehash: 31b3c0a1894280e90f696f6c107b67a3af7b1880
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605341"
---
# <a name="logs-in-azure-database-for-postgresql---single-server"></a>Logs no banco de dados do Azure para PostgreSQL-servidor único

O banco de dados do Azure para PostgreSQL permite configurar e acessar os logs padrão do Postgres. Os logs podem ser usados para identificar, solucionar problemas e reparar erros de configuração e desempenho inferior. As informações de registro em log que você pode configurar e acessar incluem erros, informações de consulta, registros de vácuo, conexões e pontos de verificação. (O acesso aos logs de transações não está disponível).

O log de auditoria é disponibilizado por meio de uma extensão Postgres, pgaudit. Para saber mais, visite o artigo [conceitos de auditoria](concepts-audit.md) .


## <a name="configure-logging"></a>Configurar o registro em log 
Você pode configurar o log padrão do postgres no servidor usando os parâmetros do servidor de registro em log. Em cada servidor de banco de dados do Azure para PostgreSQL `log_checkpoints` e `log_connections` estão ativados por padrão. Há parâmetros adicionais que você pode ajustar para atender às suas necessidades de registro em log: 

:::image type="content" source="./media/concepts-server-logs/log-parameters.png" alt-text="Banco de Dados do Azure para PostgreSQL – parâmetros de registro em log":::

Para saber mais sobre os parâmetros de log do Postgres, visite as seções [quando registrar](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN) e [o que registrar](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHAT) na documentação do Postgres. A maioria dos parâmetros de log Postgres, mas não todos, estão disponíveis para configurar no banco de dados do Azure para PostgreSQL.

Para saber como configurar parâmetros no banco de dados do Azure para PostgreSQL, consulte a [documentação do portal](howto-configure-server-parameters-using-portal.md) ou a [documentação da CLI](howto-configure-server-parameters-using-cli.md). 

> [!NOTE]
> Configurar um alto volume de logs, por exemplo, log de instruções, pode adicionar uma sobrecarga de desempenho significativa. 

## <a name="access-log-files"></a>Acessar arquivos. log
O formato de log padrão no banco de dados do Azure para PostgreSQL é. log. Uma linha de exemplo desse log é semelhante a:

```
2019-10-14 17:00:03 UTC-5d773cc3.3c-LOG: connection received: host=101.0.0.6 port=34331 pid=16216
```

O banco de dados do Azure para PostgreSQL fornece um local de armazenamento de curto prazo para os arquivos. log. Um novo arquivo começa A cada 1 hora ou 100 MB, o que vier primeiro. Os logs são anexados ao arquivo atual à medida que são emitidos de Postgres.  

Você pode definir o período de retenção para esse armazenamento de log de curto prazo usando o `log_retention_period` parâmetro. O valor padrão é de 3 dias; o valor máximo é de 7 dias. O local de armazenamento de curto prazo pode conter até 1 GB de arquivos de log. Após 1 GB, os arquivos mais antigos, independentemente do período de retenção, serão excluídos para liberar espaço para novos logs. 

Para retenção de longo prazo de logs e análise de log, você pode baixar os arquivos. log e movê-los para um serviço de terceiros. Você pode baixar os arquivos usando o [portal do Azure](howto-configure-server-logs-in-portal.md), [CLI do Azure](howto-configure-server-logs-using-cli.md). Como alternativa, você pode definir Azure Monitor configurações de diagnóstico que emite automaticamente os logs (no formato JSON) para locais de longo prazo. Saiba mais sobre essa opção na seção abaixo. 

Você pode parar de gerar arquivos. log definindo o parâmetro `logging_collector` como off. A desativação da geração do arquivo de log é recomendada se você estiver usando Azure Monitor configurações de diagnóstico. Essa configuração reduzirá o impacto no desempenho do log adicional.

## <a name="resource-logs"></a>Logs de recursos

O banco de dados do Azure para PostgreSQL é integrado com Azure Monitor configurações de diagnóstico. As configurações de diagnóstico permitem que você envie os logs do postgres no formato JSON para Azure Monitor logs para análise e alertas, hubs de eventos para streaming e armazenamento do Azure para arquivamento. 

> [!IMPORTANT]
> Esse recurso de diagnóstico para logs de servidor só está disponível nos [tipos de preço](concepts-pricing-tiers.md)uso geral e com otimização de memória.


### <a name="configure-diagnostic-settings"></a>Definir as configurações de diagnóstico

Você pode habilitar as configurações de diagnóstico para o servidor postgres usando o portal do Azure, a CLI, a API REST e o PowerShell. A categoria de log a ser selecionada é **PostgreSQLLogs**. (Há outros logs que você pode configurar se estiver usando [repositório de consultas](concepts-query-store.md).)

Para habilitar os logs de recursos usando o portal do Azure:

   1. No portal, vá para *configurações de diagnóstico* no menu de navegação do seu servidor do Postgres.
   2. Selecione *Adicionar configuração de diagnóstico*.
   3. Nomeie essa configuração. 
   4. Selecione seu ponto de extremidade preferido (conta de armazenamento, Hub de eventos, log Analytics). 
   5. Selecione o tipo de log **PostgreSQLLogs**.
   7. Salve sua configuração.

Para habilitar logs de recursos usando o PowerShell, a CLI ou a API REST, visite o artigo [configurações de diagnóstico](../azure-monitor/essentials/diagnostic-settings.md) .

### <a name="access-resource-logs"></a>Acessar logs de recursos

A maneira como você acessa os logs depende do ponto de extremidade escolhido. Para o armazenamento do Azure, consulte o artigo [conta de armazenamento de logs](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) . Para os hubs de eventos, consulte o artigo [fluxos de logs do Azure](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs) .

Para logs de Azure Monitor, os logs são enviados para o espaço de trabalho selecionado. Os logs do postgres usam o modo de coleta **AzureDiagnostics** , para que possam ser consultados a partir da tabela AzureDiagnostics. Os campos na tabela são descritos abaixo. Saiba mais sobre como consultar e alertar na visão geral de [consulta de logs de Azure monitor](../azure-monitor/logs/log-query-overview.md) .

Veja a seguir as consultas que você pode tentar começar. Você pode configurar alertas com base em consultas.

Pesquisar todos os logs do postgres para um servidor específico no último dia
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where Category == "PostgreSQLLogs"
| where TimeGenerated > ago(1d) 
```

Pesquisar todas as tentativas de conexão não localhost
```
AzureDiagnostics
| where Message contains "connection received" and Message !contains "host=127.0.0.1"
| where Category == "PostgreSQLLogs" and TimeGenerated > ago(6h)
```
A consulta acima mostrará os resultados nas últimas 6 horas para qualquer log de servidor postgres nesse espaço de trabalho.

### <a name="log-format"></a>Formato de log

A tabela a seguir descreve os campos para o tipo **PostgreSQLLogs** . Dependendo do ponto de extremidade de saída escolhido, os campos incluídos e a ordem em que aparecem podem variar. 

|**Campo** | **Descrição** |
|---|---|
| TenantId | Sua ID de locatário |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | Carimbo de data/hora quando o log foi gravado, em UTC |
| Tipo | Tipo do log. Sempre `AzureDiagnostics` |
| SubscriptionId | GUID para a assinatura a que o servidor pertence |
| ResourceGroup | Nome do grupo de recursos ao qual o servidor pertence |
| ResourceProvider | Nome do provedor de recursos. Sempre `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | URI de recurso |
| Recurso | Nome do servidor |
| Categoria | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| errorLevel | Nível de log, exemplo: LOG, ERROR, NOTICE |
| Mensagem | Mensagem de log primária | 
| Domínio | Versão do servidor, o exemplo: postgres-10 |
| Detalhe | Mensagem de log secundária (se aplicável) |
| ColumnName | Nome da coluna (se aplicável) |
| SchemaName | Nome do esquema (se aplicável) |
| DatatypeName | Nome do tipo de dados (se aplicável) |
| LogicalServerName | Nome do servidor | 
| _ResourceId | URI de recurso |
| Prefixo | Prefixo da linha de log |


## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre como acessar logs no [portal do Azure](howto-configure-server-logs-in-portal.md) ou na [CLI do Azure](howto-configure-server-logs-using-cli.md).
- Saiba mais sobre [preços de Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).
- Saiba mais sobre [os logs de auditoria](concepts-audit.md)