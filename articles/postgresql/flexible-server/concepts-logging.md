---
title: Logs-banco de dados do Azure para PostgreSQL-servidor flexível
description: Descreve a configuração de log, o armazenamento e a análise no banco de dados do Azure para PostgreSQL – servidor flexível
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: e09c01fcfb9c4725ac169151e85c8b030d8bb18c
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606378"
---
# <a name="logs-in-azure-database-for-postgresql---flexible-server"></a>Logs no banco de dados do Azure para PostgreSQL – servidor flexível

> [!IMPORTANT]
> O Servidor Flexível do Banco de Dados do Azure para PostgreSQL está em versão prévia

O banco de dados do Azure para PostgreSQL permite configurar e acessar os logs padrão do Postgres. Os logs podem ser usados para identificar, solucionar problemas e reparar erros de configuração e desempenho inferior. As informações de registro em log que você pode configurar e acessar incluem erros, informações de consulta, registros de vácuo, conexões e pontos de verificação. (O acesso aos logs de transações não está disponível).

O log de auditoria é disponibilizado por meio de uma extensão Postgres, `pgaudit` . Para saber mais, visite o artigo [conceitos de auditoria](concepts-audit.md) .

## <a name="configure-logging"></a>Configurar o registro em log

Você pode configurar o log padrão do postgres no servidor usando os parâmetros do servidor de registro em log. Para saber mais sobre os parâmetros de log do Postgres, visite as seções [quando registrar](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN) e [o que registrar](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHAT) na documentação do Postgres. A maioria dos parâmetros de log Postgres, mas não todos, estão disponíveis para configurar no banco de dados do Azure para PostgreSQL.

Para saber como configurar parâmetros no banco de dados do Azure para PostgreSQL, consulte a [documentação do portal](howto-configure-server-parameters-using-portal.md) ou a [documentação da CLI](howto-configure-server-parameters-using-cli.md).

> [!NOTE]
> Configurar um alto volume de logs, por exemplo, log de instruções, pode adicionar uma sobrecarga de desempenho significativa. 

## <a name="accessing-logs"></a>Acessando os logs

O banco de dados do Azure para PostgreSQL é integrado com Azure Monitor configurações de diagnóstico. As configurações de diagnóstico permitem que você envie os logs do postgres no formato JSON para Azure Monitor logs para análise e alertas, hubs de eventos para streaming e armazenamento do Azure para arquivamento. 

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

- Saiba mais sobre como [configurar e acessar logs](howto-configure-and-access-logs.md).
- Saiba mais sobre [preços de Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).
- Saiba mais sobre [os logs de auditoria](concepts-audit.md)
