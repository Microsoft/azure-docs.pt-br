---
title: Importância da carga de trabalho
description: Diretrizes para definir a importância para consultas de pool de SQL dedicadas no Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 06d1957d182f2cabc336afcfc47a790442a3cb9a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98678399"
---
# <a name="azure-synapse-analytics-workload-importance"></a>Importância da carga de trabalho do Azure Synapse Analytics

Este artigo explica como a importância da carga de trabalho pode influenciar a ordem de execução de solicitações de pools SQL dedicadas no Azure Synapse.

## <a name="importance"></a>Importância

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

As necessidades comerciais podem exigir que as cargas de trabalho de data warehousing sejam mais importantes do que outras.  Considere um cenário em que os dados de vendas de missão crítica são carregados antes do fechamento do período fiscal.  Cargas de dados para outras fontes, como dados de clima, não têm SLAs estritos. Definir a alta importância para uma solicitação para carregar dados de vendas e baixa importância para uma solicitação para carregar dados meteorológicos garante que o carregamento dos dados de vendas receba o primeiro acesso aos recursos e seja concluído mais rapidamente.

## <a name="importance-levels"></a>Níveis de importância

Há cinco níveis de importância: baixa, below_normal, normal, above_normal e alta.  As solicitações que não definem a importância são atribuídas ao nível padrão normal. As solicitações que têm o mesmo nível de importância têm o mesmo comportamento de agendamento que existe hoje.

## <a name="importance-scenarios"></a>Cenários de importância

Além do cenário de importância básica descrito acima com dados de vendas e clima, há outros cenários em que a importância da carga de trabalho ajuda a atender às necessidades de processamento e consulta de dados.

### <a name="locking"></a>Bloqueio

O acesso a bloqueios para atividade de leitura e gravação é uma área de contenção natural. Atividades como [troca de partição](sql-data-warehouse-tables-partition.md) ou [renomear objeto](/sql/t-sql/statements/rename-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) exigem bloqueios elevados.  Sem importância de carga de trabalho, o pool SQL dedicado no Azure Synapse otimiza para a taxa de transferência. Otimizar para taxa de transferência significa que, quando as solicitações em execução e em fila tiverem as mesmas necessidades de bloqueio e os recursos estiverem disponíveis, as solicitações enfileiradas poderão ignorar as solicitações com maiores necessidades de bloqueio que chegaram na fila de solicitações anteriormente. Quando a importância da carga de trabalho é aplicada a solicitações com mais necessidades de bloqueio. A solicitação com maior importância será executada antes da solicitação com menor importância.

Considere o seguinte exemplo:

- Q1 está executando ativamente e selecionando dados de SalesFact.
- O Q2 está na fila aguardando a conclusão de Q1.  Ele foi enviado às 9h e está tentando particionar novos dados no SalesFact.
- Q3 é enviado em 9:01am e deseja selecionar dados de SalesFact.

Se Q2 e Q3 tiverem a mesma importância e Q1 ainda estiver em execução, Q3 começará a ser executado. O Q2 continuará aguardando um bloqueio exclusivo em SalesFact.  Se Q2 tiver maior importância do que Q3, Q3 aguardará até que Q2 seja concluído antes de iniciar a execução.

### <a name="non-uniform-requests"></a>Solicitações não uniformes

Outro cenário em que a importância pode ajudar a atender às demandas de consulta é quando as solicitações com diferentes classes de recursos são enviadas.  Como foi mencionado anteriormente, sob a mesma importância, o pool de SQL dedicado no Azure Synapse otimiza para a taxa de transferência. Quando solicitações de tamanho misto (como smallrc ou mediumrc) são enfileiradas, o pool SQL dedicado escolherá a primeira solicitação de chegada que se ajusta dentro dos recursos disponíveis. Se a importância da carga de trabalho for aplicada, a solicitação de maior importância será agendada a seguir.
  
Considere o seguinte exemplo em DW500c:

- Q1, T2, Q3 e Q4 estão executando consultas smallrc.
- P5 é enviado com a classe de recurso mediumrc às 9h.
- P6 é enviado com a classe de recurso smallrc em 9:01am.

Como P5 é mediumrc, ele requer dois slots de simultaneidade. O P5 precisa aguardar a conclusão de duas consultas em execução.  No entanto, quando uma das consultas em execução (Q1-T4) for concluída, o P6 será agendado imediatamente porque os recursos existem para executar a consulta.  Se P5 tiver maior importância do que P6, P6 aguardará até que P5 esteja em execução antes que possa começar a execução.

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre como criar um classificador, consulte [criar classificação de carga de trabalho (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  
- Para obter mais informações sobre a classificação da carga de trabalho, confira [Classificação da carga de trabalho](sql-data-warehouse-workload-classification.md).  
- Consulte o [classificador início rápido criar carga de trabalho](quickstart-create-a-workload-classifier-tsql.md) para saber como criar um classificador de carga de trabalho.
- Confira os artigos de instruções [Configurar a importância da carga de trabalho](sql-data-warehouse-how-to-configure-workload-importance.md) e como [Gerenciar e monitorar o gerenciamento de carga de trabalho](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
- Confira [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para exibir consultas e a importância atribuída.
