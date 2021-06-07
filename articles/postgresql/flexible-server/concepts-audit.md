---
title: Log de auditoria-banco de dados do Azure para PostgreSQL-servidor flexível
description: Conceitos para o log de auditoria do pgAudit no banco de dados do Azure para PostgreSQL – servidor flexível.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: b344e2a845a9da8333860599bd4ff9041108202f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100588258"
---
# <a name="audit-logging-in-azure-database-for-postgresql---flexible-server"></a>Log de auditoria no banco de dados do Azure para PostgreSQL – servidor flexível

Log de auditoria de atividades de banco de dados no banco de dados do Azure para PostgreSQL-o servidor flexível está disponível por meio da extensão de auditoria PostgreSQL: [pgAudit](https://www.pgaudit.org/). o pgAudit fornece o log de auditoria detalhado de sessão e/ou objeto.

> [!IMPORTANT]
> Banco de dados do Azure para PostgreSQL-o servidor flexível está em versão prévia

Se você quiser logs no nível de recursos do Azure para operações como o dimensionamento de computação e armazenamento, consulte o [log de atividades do Azure](../../azure-monitor/essentials/platform-logs-overview.md).

## <a name="usage-considerations"></a>Considerações sobre o uso
Por padrão, as instruções de log pgAudit são emitidas junto com suas instruções de log regulares usando o recurso de log padrão do Postgres. No banco de dados do Azure para PostgreSQL – servidor flexível, você pode configurar todos os logs a serem enviados para Azure Monitor repositório de log para análise posterior no Log Analytics. Se você habilitar Azure Monitor log de recursos, seus logs serão enviados automaticamente (no formato JSON) para o armazenamento do Azure, hubs de eventos e/ou logs de Azure Monitor, dependendo de sua escolha.

Para saber como configurar o log no armazenamento do Azure, nos hubs de eventos ou nos logs de Azure Monitor, visite a seção logs de recursos do [artigo logs do servidor](concepts-logging.md).

## <a name="enabling-pgaudit"></a>Habilitando pgAudit

Para habilitar o pgAudit, você precisa se conectar ao servidor usando um cliente (como psql) e habilitar a extensão pgAudit executando o seguinte comando:
```SQL
CREATE EXTENSION pgaudit;
```

## <a name="pgaudit-settings"></a>configurações de pgAudit

o pgAudit permite que você configure o log de auditoria de sessão ou objeto. O [log de auditoria de sessão](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) emite logs detalhados de instruções executadas. O [log de auditoria de objeto](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) tem o escopo de auditoria para relações específicas. Você pode optar por configurar um ou ambos os tipos de registro em log. 

> [!NOTE]
> as configurações de pgAudit são especificadas globalmente e não podem ser especificadas em um nível de banco de dados ou função.

Depois de [habilitar o pgAudit](#enabling-pgaudit), você poderá configurar seus parâmetros para iniciar o registro em log. A [documentação do pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) fornece a definição de cada parâmetro. Teste os parâmetros primeiro e confirme que você está obtendo o comportamento esperado.

> [!NOTE]
> Definir `pgaudit.log_client` como on redirecionará os logs para um processo de cliente (como psql) em vez de ser gravado no arquivo. Essa configuração deve ser deixada desabilitada. <br> <br>
> `pgaudit.log_level` é habilitado somente quando `pgaudit.log_client` está ativado.

> [!NOTE]
> No banco de dados do Azure para PostgreSQL – servidor flexível, `pgaudit.log` não pode ser definido usando um `-` atalho de sinal (menos), conforme descrito na documentação do pgAudit. Todas as classes de instrução necessárias (leitura, gravação, etc.) devem ser especificadas individualmente.

## <a name="audit-log-format"></a>Formato do log de auditoria
Cada entrada de auditoria é indicada `AUDIT:` próximo ao início da linha de log. O formato do restante da entrada é detalhado na [documentação do pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#format).

## <a name="getting-started"></a>Introdução
Para começar rapidamente, defina `pgaudit.log` como `WRITE` e abra os logs do servidor para examinar a saída. 

## <a name="viewing-audit-logs"></a>Exibindo logs de auditoria
A maneira como você acessa os logs depende do ponto de extremidade escolhido. Para o armazenamento do Azure, consulte o artigo [conta de armazenamento de logs](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) . Para os hubs de eventos, consulte o artigo [fluxos de logs do Azure](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs) .

Para logs de Azure Monitor, os logs são enviados para o espaço de trabalho selecionado. Os logs do postgres usam o modo de coleta **AzureDiagnostics** , para que possam ser consultados a partir da tabela AzureDiagnostics. Os campos na tabela são descritos abaixo. Saiba mais sobre como consultar e alertar na visão geral de [consulta de logs de Azure monitor](../../azure-monitor/logs/log-query-overview.md) .

Você pode usar essa consulta para começar. Você pode configurar alertas com base em consultas.

Pesquisar todas as entradas de pgAudit nos logs do postgres para um servidor específico no último dia
```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>Próximas etapas
- [Saiba mais sobre o log no banco de dados do Azure para PostgreSQL-servidor flexível](concepts-logging.md)
- [Saiba como configurar o log no banco de dados do Azure para PostgreSQL – servidor flexível e como acessar logs](howto-configure-and-access-logs.md)