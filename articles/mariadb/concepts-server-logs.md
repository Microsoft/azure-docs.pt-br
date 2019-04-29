---
title: Logs do servidor para Banco de Dados do Azure para MariaDB
description: Descreve os logs disponíveis no Banco de Dados do Azure para MariaDB para os parâmetros disponíveis para habilitar níveis de log diferentes.
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: a26f61eb199d8f370e1a9dd010932dc868b74ae4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61041251"
---
# <a name="server-logs-in-azure-database-for-mariadb"></a>Logs de servidor no Banco de Dados do Azure para MariaDB
No Banco de Dados do Azure para MariaDB, o log de consultas lentas está disponível para os usuários. No entanto, não há suporte para acesso ao log de transação. O log de consultas lentas pode ser usado para identificar gargalos de desempenho para solução de problemas.

Para obter mais informações sobre o log de consulta lenta, leia a documentação do MariaDB para [log de consulta lenta](https://mariadb.com/kb/en/library/slow-query-log-overview/).

## <a name="access-server-logs"></a>Acessar logs do servidor
É possível listar e baixar logs de servidor do Banco de Dados do Azure para MariaDB usando o portal do Azure e a CLI do Azure.

No portal do Azure, selecione o servidor do Banco de Dados do Azure para MariaDB. Sob o título **Monitoramento**, selecione a página **Logs do Servidor**.

<!-- For more information on Azure CLI, see [Configure and access server logs using Azure CLI](howto-configure-server-logs-in-cli.md).-->

## <a name="log-retention"></a>Retenção de log
Logs estão disponíveis por até sete dias desde a criação deles. Se o tamanho total dos logs disponíveis exceder 7 GB, os arquivos mais antigos serão excluídos até que haja espaço disponível.

Logs são reciclados a cada 24 horas ou 7 GB, o que ocorrer primeiro.

## <a name="configure-logging"></a>Configurar o registro em log
Por padrão, o log de consultas lentas está desabilitado. Para habilitá-lo, defina slow_query_log para ON.

Outros parâmetros que você pode ajustar incluem:

- **long_query_time**: se uma consulta demorar mais do que long_query_time (em segundos), essa consulta será registrada. O padrão é 10 segundos.
- **log_slow_admin_statements**: se ON inclui instruções administrativas como ALTER_TABLE e ANALYZE_TABLE nas instruções gravadas para o slow_query_log.
- **log_queries_not_using_indexes**: determina se as consultas que não usam índices são ou não registradas para o slow_query_log
- **log_throttle_queries_not_using_indexes**: Esse parâmetro limita o número de consultas não indexadas que podem ser gravadas no log de consulta lenta. Esse parâmetro tem efeito quando log_queries_not_using_indexes está definido como ON.

Consulte a [documentação de log de consulta lenta](https://mariadb.com/kb/en/library/slow-query-log-overview/) do MariaDB para ver descrições completas dos parâmetros de log de consulta lenta.

## <a name="next-steps"></a>Próximas etapas
- [Como configurar e acessar logs de servidor a partir da CLI do Azure](howto-configure-server-logs-portal.md).
