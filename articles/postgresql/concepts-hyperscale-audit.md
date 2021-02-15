---
title: Log de auditoria-banco de dados do Azure para PostgreSQL-Citus (hiperescala)
description: Conceitos para o log de auditoria do pgAudit no banco de dados do Azure para PostgreSQL-hiperescala (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: d8a21a5583ec4655a2ee8593e50be5c7b5f702b7
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2021
ms.locfileid: "99227576"
---
# <a name="audit-logging-in-azure-database-for-postgresql---hyperscale-citus"></a>Log de auditoria no banco de dados do Azure para PostgreSQL-Citus (hiperescala)

Log de auditoria de atividades de banco de dados no banco de dados do Azure para PostgreSQL-o Citus (hiperescala) está disponível por meio da extensão de auditoria PostgreSQL: [pgAudit](https://www.pgaudit.org/). o pgAudit fornece o log de auditoria detalhado de sessão e/ou objeto.

> [!IMPORTANT]
> pgAudit está em visualização no banco de dados do Azure para PostgreSQL-hiperescala (Citus)

Se você quiser logs no nível de recursos do Azure para operações como o dimensionamento de computação e armazenamento, consulte o [log de atividades do Azure](../azure-monitor/platform/platform-logs-overview.md).

## <a name="usage-considerations"></a>Considerações sobre o uso
Por padrão, as instruções de log pgAudit são emitidas junto com suas instruções de log regulares usando o recurso de log padrão do Postgres. No banco de dados do Azure para PostgreSQL-Citus (hiperescala), você pode configurar todos os logs a serem enviados para Azure Monitor repositório de log para análise posterior no Log Analytics. Se você habilitar Azure Monitor log de recursos, seus logs serão enviados automaticamente (no formato JSON) para o armazenamento do Azure, hubs de eventos e/ou logs de Azure Monitor, dependendo de sua escolha.

## <a name="enabling-pgaudit"></a>Habilitando pgAudit

A extensão pgAudit é pré-instalada e habilitada em todos os nós do grupo de servidores de hiperescala (Citus). Nenhuma ação é necessária para habilitá-la.

## <a name="pgaudit-settings"></a>configurações de pgAudit

o pgAudit permite que você configure o log de auditoria de sessão ou objeto. O [log de auditoria de sessão](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) emite logs detalhados de instruções executadas. O [log de auditoria de objeto](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) tem o escopo de auditoria para relações específicas. Você pode optar por configurar um ou ambos os tipos de registro em log. 

> [!NOTE]
> as configurações de pgAudit são especificadas globalmente e não podem ser especificadas em um nível de banco de dados ou função.
>
> Além disso, as configurações de pgAudit são especificadas por nó em um grupo de servidores. Para fazer uma alteração em todos os nós, você deve aplicá-lo a cada nó individualmente.

Você deve configurar os parâmetros de pgAudit para iniciar o registro em log. A [documentação do pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) fornece a definição de cada parâmetro. Teste os parâmetros primeiro e confirme que você está obtendo o comportamento esperado.

> [!NOTE]
> Definir `pgaudit.log_client` como on redirecionará os logs para um processo de cliente (como psql) em vez de ser gravado no arquivo. Essa configuração deve ser deixada desabilitada. <br> <br>
> `pgaudit.log_level` é habilitado somente quando o `pgaudit.log_client` está ativado.

> [!NOTE]
> No banco de dados do Azure para PostgreSQL-Citus (hiperescala), `pgaudit.log` não é possível definir usando um `-` atalho de sinal (menos), conforme descrito na documentação do pgAudit. Todas as classes de instrução necessárias (leitura, gravação, etc.) devem ser especificadas individualmente.

## <a name="audit-log-format"></a>Formato do log de auditoria
Cada entrada de auditoria é indicada `AUDIT:` próximo ao início da linha de log. O formato do restante da entrada é detalhado na [documentação do pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#format).

## <a name="getting-started"></a>Introdução
Para começar rapidamente, defina `pgaudit.log` como `WRITE` e abra os logs do servidor para examinar a saída. 

## <a name="viewing-audit-logs"></a>Exibindo logs de auditoria
A maneira como você acessa os logs depende do ponto de extremidade escolhido. Para o armazenamento do Azure, consulte o artigo [conta de armazenamento de logs](../azure-monitor/platform/resource-logs.md#send-to-azure-storage) . Para os hubs de eventos, consulte o artigo [fluxos de logs do Azure](../azure-monitor/platform/resource-logs.md#send-to-azure-event-hubs) .

Para logs de Azure Monitor, os logs são enviados para o espaço de trabalho selecionado. Os logs do postgres usam o modo de coleta **AzureDiagnostics** , para que possam ser consultados a partir da tabela AzureDiagnostics. Os campos na tabela são descritos abaixo. Saiba mais sobre como consultar e alertar na visão geral de [consulta de logs de Azure monitor](../azure-monitor/log-query/log-query-overview.md) .

Você pode usar essa consulta para começar. Você pode configurar alertas com base em consultas.

Pesquisar todas as entradas de pgAudit nos logs do postgres para um servidor específico no último dia
```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>Próximas etapas

- [Saiba como configurar o log no banco de dados do Azure para PostgreSQL-Citus (hiperescala) e como acessar logs](howto-hyperscale-logging.md)
