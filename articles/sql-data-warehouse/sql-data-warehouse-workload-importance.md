---
title: Importância da carga de trabalho | Microsoft Docs
description: Diretrizes de configuração de importância para consultas no Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload management
ms.date: 03/13/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: 12e7d9bc22eff14bbf302aed50080412d04a40d3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61474695"
---
# <a name="sql-data-warehouse-workload-importance-preview"></a>Importância de carga de trabalho do SQL Data Warehouse (visualização)

Este artigo explica como a importância da carga de trabalho pode influenciar a ordem de execução para solicitações de SQL Data Warehouse.

> [!Note]
> A classificação de carga de trabalho está disponível para versão prévia no SQL Data Warehouse Gen2. A versão prévia de Classificação e Importância de Gerenciamento de Carga de Trabalho destina-se a builds com uma data de lançamento de 9 de abril de 2019 ou posterior.  Os usuários devem evitar usar builds anteriores a essa data para o teste de gerenciamento de carga de trabalho.  Para determinar se o build é compatível com o gerenciamento de carga de trabalho, execute select @@version quando estiver conectado à instância do SQL Data Warehouse.

## <a name="importance"></a>Importância

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

Necessidades de negócios podem exigir o data warehouse cargas de trabalho para ser mais importante do que outras pessoas.  Considere um cenário em que os dados de vendas de missão crítica seja carregados antes do período fechar o fiscal.  Carregamento dos dados para outras fontes, como dados de clima não tem SLAs estritos.   A definição de alta importância para uma solicitação para carregar dados de vendas e baixa importância para uma solicitação para carregar se dados garante que a carga de dados de vendas obtém primeiro acesso aos recursos e conclui o mais rápido.

## <a name="importance-levels"></a>Níveis de importância

Há cinco níveis de importância: baixa, below_normal, normal, above_normal e alta.  As solicitações que não definem a importância recebem o nível padrão normal.  As solicitações que têm o mesmo nível de prioridade têm o mesmo comportamento de agendamento que existe atualmente.

## <a name="importance-scenarios"></a>Cenários de importância

Fora do cenário de importância básico descrito acima, com as vendas e dados meteorológicos, há outros cenários em que a importância da carga de trabalho ajuda a atender às necessidades de consulta e processamento de dados.

### <a name="locking"></a>Bloqueio

Acessar para bloqueios de leitura e gravação a atividade é uma área de contenção natural.  As atividades, como [alternância de partição](/azure/sql-data-warehouse/sql-data-warehouse-tables-partition) ou [RENOMEAR objeto](/sql/t-sql/statements/rename-transact-sql) requerem bloqueios com privilégios elevados.  Sem importância da carga de trabalho, o SQL Data Warehouse otimiza a taxa de transferência.  Otimização de taxa de transferência significa que, quando em execução e solicitações em fila têm as mesmas necessidades de bloqueio e recursos estão disponíveis, as solicitações em fila podem ignorar solicitações com mais necessidades de bloqueio que foi recebido na fila de solicitação anteriormente.  Depois que a importância da carga de trabalho é aplicada às solicitações de bloqueio do mais alto precisa. Solicitação com importância mais alta será executada antes da solicitação com prioridade mais baixa.

Considere o exemplo a seguir:

Q1 está ativamente em execução e selecionar os dados da SalesFact.
Q2 está na fila aguardando para Q1 ser concluída.  Ele foi enviado às 9h e está tentando fazer novos dados de switch de partição em SalesFact.
3º trimestre é enviado às 9h: 01 e deseja selecionar dados de SalesFact.

Se Q2 e Q3 têm a mesma importância e Q1 ainda está em execução, p3 inicia a execução. Q2 continuará a aguardar um bloqueio exclusivo no SalesFact.  Se Q2 tem importância mais alta que Q3, p3 aguardará até que Q2 seja concluída antes de iniciar a execução.

### <a name="non-uniform-requests"></a>Solicitações de não-uniforme

Outro cenário em que a importância pode ajudar a atender às demandas de consultas é quando as solicitações com classes de recursos diferentes são enviadas.  Conforme mencionado anteriormente, sob a mesma importância, o SQL Data Warehouse otimiza a taxa de transferência.  Quando as solicitações de tamanho misto (por exemplo, smallrc ou mediumrc) são enfileiradas, o SQL Data Warehouse escolherá a solicitação que chegam mais antigo que se encaixa dentro dos recursos disponíveis.  Se for aplicada a importância da carga de trabalho, a solicitação de importância mais alta é agendada a seguir.
  
Considere o exemplo a seguir no DW500c:

P1, P2, p3 e p4 estão em execução no smallrc consultas.
P5 é enviado com a classe de recurso mediumrc às 9:00.
P6 é enviado com a classe de recurso smallrc às 9:00: 01.

Como P5 é mediumrc, ele requer dois slots de simultaneidade.  P5 precisa esperar para duas das consultas em execução para ser concluída.  No entanto, quando é concluído em uma das consultas em execução (Q1 P4), P6 é agendado imediatamente porque os recursos existem para executar a consulta.  Se o P5 tem importância mais alta que P6, P6 aguarda P5 está em execução antes de começar a executar.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a classificação de carga de trabalho do SQL Data Warehouse, consulte [classificação de carga de trabalho do SQL Data Warehouse](sql-data-warehouse-workload-classification.md) e [criar um classificador de carga de trabalho](quickstart-create-a-workload-classifier-tsql.md). Confira [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) para exibir consultas e a importância atribuída.
