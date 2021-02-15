---
title: Recomendações do Azure Advisor para o pool SQL dedicado
description: Saiba mais sobre as recomendações do SQL do Synapse e como elas são geradas
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 06/26/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 6510a7143e78c054130a1f1dddba78342a6785fe
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685269"
---
# <a name="azure-advisor-recommendations-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Recomendações do Azure Advisor para o pool SQL dedicado no Azure Synapse Analytics

Este artigo descreve as recomendações do pool do SQL dedicado disponíveis no Azure Advisor.  

O pool SQL dedicado fornece recomendações para garantir que sua carga de trabalho data warehouse seja consistentemente otimizada para o desempenho. As recomendações são totalmente integradas ao [Assistente do Azure](../../advisor/advisor-performance-recommendations.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) para fornecer as melhores práticas diretamente no [portal do Azure](https://aka.ms/Azureadvisor). O pool do SQL dedicado coleta recomendações de telemetria e de superfícies para sua carga de trabalho ativa em uma cadência diária. Os cenários de recomendação compatíveis são descritos abaixo, juntamente com a forma de aplicar as ações recomendadas.

Você pode [verificar suas recomendações](https://aka.ms/Azureadvisor) hoje mesmo! 

## <a name="data-skew"></a>Distorção de dados

A distorção de dados pode causar movimentação adicional de dados ou afunilamentos de recursos ao executar sua carga de trabalho. A seguinte documentação descreve mostrar para identificar distorção de dados e impedir que isso aconteça selecionando uma chave de distribuição ideal.

- [Identificar e Remover distorção](sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice)

## <a name="no-or-outdated-statistics"></a>Sem estatísticas ou estatísticas desatualizadas

Ter estatísticas abaixo do ideal pode impactar gravemente o desempenho da consulta, pois pode fazer com que o otimizador de consulta do SQL gere planos de consulta abaixo do ideal. A documentação a seguir descreve as melhores práticas de criação e atualização de estatísticas:

- [Criando e atualizando estatísticas da tabela](sql-data-warehouse-tables-statistics.md)

Para ver a lista de tabelas afetadas por essas recomendações, execute o seguinte [script T-SQL](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables). O Assistente executa continuamente o mesmo script T-SQL para gerar essas recomendações.

## <a name="replicate-tables"></a>Replicar tabelas

Para obter recomendações de tabela replicada, o Assistente detecta possíveis tabelas com base nas seguintes características físicas:

- Tamanho da tabela replicada
- Número de Colunas
- Tipo de distribuição de tabelas
- Número of partições

O Assistente continuamente utiliza heurística com base em carga de trabalho, como, por exemplo, frequência de acesso de tabela, linhas retornadas em média e limites de tamanho e atividade do data warehouse, para garantir que recomendações de alta qualidade sejam geradas.

A seção a seguir descreve a heurística com base em carga de trabalho que pode ser encontrada no portal do Azure para cada recomendação de tabela replicada:

- Examinar o percentual médio avg- de linhas que foram retornadas da tabela para cada acesso de tabela nos últimos sete dias
- Leitura frequente, nenhuma atualização - indica que a tabela não foi atualizada nos últimos sete dias e mostra a atividade de acesso
- Taxa de leitura/atualização - a frequência em que a tabela foi acessada em relação a quando foi atualizada nos últimos sete dias
- Atividade - mede o uso com base na atividade de acesso. Essa atividade se compara à atividade de acesso de tabela em relação à atividade média de acesso de tabela no data warehouse nos últimos sete dias.

Atualmente, o Assistente mostrará apenas no máximo quatro tabelas replicadas possíveis por vez, com índices columnstore clusterizados priorizando a atividade mais alta.

> [!IMPORTANT]
> A recomendação de tabela replicada não é à prova de falhas e não leva em consideração as operações de movimentação de dados da conta. Estamos trabalhando para adicionar isso como uma heurística, mas por enquanto você deverá sempre validar sua carga de trabalho após a aplicação da recomendação. Para saber mais sobre as tabelas replicadas, visite a seguinte [documentação](design-guidance-for-replicated-tables.md#what-is-a-replicated-table).


## <a name="adaptive-gen2-cache-utilization"></a>Utilização de cache adaptável (Gen2)
Quando você tem um grande conjunto de trabalho, pode experimentar um percentual baixo de falha de cache e uma alta utilização de cache. Nesse cenário, você deve escalar verticalmente para aumentar a capacidade do cache e executar novamente a carga de trabalho. Visite a seguinte [documentação](./sql-data-warehouse-how-to-monitor-cache.md) para saber mais. 

## <a name="tempdb-contention"></a>Contenção de Tempdb

O desempenho da consulta pode diminuir quando há uma contenção de tempdb alta.  A contenção de tempdb pode ocorrer por meio de tabelas temporárias definidas pelo usuário ou quando há uma grande quantidade de movimentação de dados. Neste cenário, você pode aumentar a alocação de tempdb e [configurar o gerenciamento de carga de trabalho e classes de recursos](./sql-data-warehouse-workload-management.md) para fornecer mais memória às consultas. 

## <a name="data-loading-misconfiguration"></a>Carregamento de dados de configuração incorreta

Você sempre deve carregar dados de uma conta de armazenamento na mesma região que o pool SQL dedicado para minimizar a latência. Use a [instrução de cópia para a ingestão de dados de alta taxa de transferência](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) e divida os arquivos de preparo em sua conta de armazenamento para maximizar a taxa de transferência. Se você não puder usar a instrução de cópia, poderá usar a API SqlBulkCopy ou o bcp com um tamanho de lote alto para obter uma melhor taxa de transferência. Para obter diretrizes adicionais de carregamento de dados, visite a [documentação](./guidance-for-loading-data.md)a seguir.