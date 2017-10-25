---
title: "Monitorar o armazenamento na memória XTP | Microsoft Docs"
description: "Estimar e monitorar o uso do armazenamento na memória XTP, capacidade; resolver o erro de capacidade 41823"
services: sql-database
documentationcenter: 
author: jodebrui
manager: jhubbard
editor: 
ms.assetid: b617308e-692c-4938-8fa2-070034a3ecef
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jodebrui
ms.openlocfilehash: e953b60493c5a7c7a7ad74533471bd321d42abef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-in-memory-oltp-storage"></a>Monitorar o armazenamento OLTP In-Memory
Ao usar o [In-Memory OLTP](sql-database-in-memory.md), os dados em tabelas com otimização de memória e as variáveis de tabela residem no armazenamento OLTP in-memory. Cada camada de serviço Premium tem um tamanho máximo de armazenamento OLTP In-Memory, que está documentado em [limites de recursos do banco de dados individual](sql-database-resource-limits.md#single-database-storage-sizes-and-performance-levels) e [limites de recursos do pool elástico](sql-database-resource-limits.md#elastic-pool-change-storage-size). Quando esse limite for excedido, as operações insert e update poderão começar a falhar (com o erro 41823). Nesse ponto, você precisará excluir dados para obter memória ou atualizar a camada de desempenho do seu banco de dados.

## <a name="determine-whether-data-will-fit-within-the-in-memory-storage-cap"></a>Determinar se os dados se ajustarão ao limite de armazenamento na memória
Determine os limites de armazenamento das diferentes camadas de serviço Premium. Consulte [limites de recursos do banco de dados individual](sql-database-resource-limits.md#single-database-storage-sizes-and-performance-levels) e [limites de recursos do pool elástico](sql-database-resource-limits.md#elastic-pool-change-storage-size).

A estimativa dos requisitos de memória para uma tabela com otimização de memória funciona no SQL Server da mesma forma como no Banco de Dados SQL do Azure. Reserve alguns minutos para examinar este tópico no [MSDN](https://msdn.microsoft.com/library/dn282389.aspx).

Observe que a tabela e as linhas de variável de tabela, bem como índices, contam para o tamanho máximo dos dados do usuário. Além disso, ALTER TABLE precisa de espaço suficiente para criar uma nova versão da tabela inteira e de seus índices.

## <a name="monitoring-and-alerting"></a>Monitoramento e alertas
Você pode monitorar o uso de armazenamento na memória como uma porcentagem do limite de armazenamento para sua camada de desempenho no [Portal do Azure](https://portal.azure.com/): 

1. Na folha Banco de Dados, localize a caixa de utilização Recurso e clique em Editar.
2. Selecione a métrica `In-Memory OLTP Storage percentage`.
3. Para adicionar um alerta, clique na caixa Utilização de Recursos para abrir a folha Métrica e clique em Adicionar alerta.

Ou use a consulta a seguir para mostrar a utilização de armazenamento na memória:

    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats


## <a name="correct-out-of-memory-situations---error-41823"></a>Corrigir situações de memória insuficiente - Erro 41823
A insuficiência de memória resulta na falha das operações INSERT, UPDATE e CREATE com a mensagem de erro 41823.

A mensagem de erro 41823 indica que as variáveis de tabela e as tabelas com otimização de memória excederam o tamanho máximo.

Para resolver esse erro:

* Exclua os dados das tabelas com otimização de memória, potencialmente descarregando os dados em tabelas baseadas em disco tradicionais ou
* Atualize a camada de serviço para uma com armazenamento na memória suficiente para os dados que você precisa manter em tabelas com otimização de memória.

## <a name="next-steps"></a>Próximas etapas
Para obter diretrizes sobre monitoramento, consulte [Monitoramento de Banco de Dados SQL do Azure usando exibições de gerenciamento dinâmico](sql-database-monitoring-with-dmvs.md).
