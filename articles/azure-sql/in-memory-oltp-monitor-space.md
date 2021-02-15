---
title: Monitorar o armazenamento na memória XTP
description: Estimar e monitorar o uso do armazenamento na memória XTP, capacidade; resolver o erro de capacidade 41823
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 5717c2479c1d894117bae44826a814c3cfeaa98a
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96493365"
---
# <a name="monitor-in-memory-oltp-storage-in-azure-sql-database-and-azure-sql-managed-instance"></a>Monitorar In-Memory armazenamento OLTP no banco de dados SQL do Azure e SQL do Azure Instância Gerenciada
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Ao usar o [In-Memory OLTP](in-memory-oltp-overview.md), os dados em tabelas com otimização de memória e as variáveis de tabela residem no armazenamento OLTP in-memory.

## <a name="determine-whether-data-fits-within-the-in-memory-oltp-storage-cap"></a>Determinar se os dados se ajustam ao limite de armazenamento OLTP na memória

Determine os limites de armazenamento das diferentes camadas de serviço. Cada camada de serviço Premium e Comercialmente Crítico tem um tamanho máximo de armazenamento OLTP in-memory.

- [Limites de recursos baseados em DTU-banco de dados individual](database/resource-limits-dtu-single-databases.md)
- [Limites de recursos baseados em DTU-pools elásticos](database/resource-limits-dtu-elastic-pools.md)
- [Limites de modelo de recursos baseado em vCore – Banco de dados individual](database/resource-limits-vcore-single-databases.md)
- [limites de recursos baseados em vCore – pools elásticos](database/resource-limits-vcore-elastic-pools.md)
- [limites de recursos baseados em vCore-instância gerenciada](managed-instance/resource-limits.md)

Estimar os requisitos de memória para uma tabela com otimização de memória funciona da mesma maneira para SQL Server como faz no banco de dados SQL do Azure e no SQL Instância Gerenciada do Azure. Reserve alguns minutos para examinar [os requisitos de memória estimados](/sql/relational-databases/in-memory-oltp/estimate-memory-requirements-for-memory-optimized-tables).

A tabela e as linhas de variável de tabela, bem como índices, contam para o tamanho máximo dos dados do usuário. Além disso, ALTER TABLE precisa de espaço suficiente para criar uma nova versão da tabela inteira e de seus índices.

Quando esse limite for excedido, as operações de inserção e atualização poderão começar a falhar com o erro 41823 para bancos de dados individuais no banco de dados SQL do Azure e bancos de dados no Azure SQL Instância Gerenciada e o erro 41840 para pools elásticos no banco de dados SQL do Azure. Nesse ponto, você precisa excluir dados para recuperar a memória, atualizar a camada de serviço ou o tamanho da computação do banco de dados.

## <a name="monitoring-and-alerting"></a>Monitoramento e alertas

Você pode monitorar o uso de armazenamento na memória como um percentual do limite de armazenamento do tamanho da computação no [portal do Azure](https://portal.azure.com/):

1. Na folha Banco de Dados, localize a caixa de utilização Recurso e clique em Editar.
2. Selecione a métrica `In-Memory OLTP Storage percentage`.
3. Para adicionar um alerta, clique na caixa Utilização de Recursos para abrir a folha Métrica e clique em Adicionar alerta.

Ou use a consulta a seguir para mostrar a utilização de armazenamento na memória:

```sql
    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats
```

## <a name="correct-out-of-in-memory-oltp-storage-situations---errors-41823-and-41840"></a>Corrigir situações de armazenamento OLTP fora de memória - Erros 41823 e 41840

Atingir o limite de armazenamento OLTP in-memory em seus bancos de dados resulta na falha de operações INSERT, UPDATE, ALTER e CREATE com mensagens de erro 41823 (para bancos de dados individuais) ou 41840 (para pools elásticos). Ambos os erros fazem com que a transação ativa seja anulada.

Mensagens de erro 41823 e 41840 indicam que as tabelas com otimização de memória e variáveis de tabela no banco de dados ou pool atingiu o tamanho máximo de armazenamento OLTP na memória.

Para resolver esse erro:

- Exclua os dados das tabelas com otimização de memória, potencialmente descarregando os dados em tabelas baseadas em disco tradicionais ou
- Atualize a camada de serviço para uma com armazenamento na memória suficiente para os dados que você precisa manter em tabelas com otimização de memória.

> [!NOTE]
> Em casos raros, erros 41823 e 41840 podem ser transitórios, o que significa que há armazenamento suficiente OLTP na memória disponível e que repetir a operação será bem-sucedida. Portanto, é recomendável monitorar o armazenamento OLTP na memória global disponível e tentar novamente quando encontrar erro 41823 ou 41840 pela primeira vez. Para obter mais informações sobre a lógica de repetição, consulte [Detecção de conflito e lógica de repetição com OLTP na memória](/sql/relational-databases/In-memory-oltp/transactions-with-memory-optimized-tables#conflict-detection-and-retry-logic).

## <a name="next-steps"></a>Próximas etapas

Para obter diretrizes de monitoramento, consulte [monitoramento usando exibições de gerenciamento dinâmico](database/monitoring-with-dmvs.md).