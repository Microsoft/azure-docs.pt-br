---
title: Gerenciamento de carga de trabalho
description: Diretrizes para implementação do gerenciamento de carga de trabalho no Azure Synapse Analytics.
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
ms.openlocfilehash: 5640c1e254c4738ab53881544a09808b4894a462
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98676962"
---
# <a name="what-is-workload-management"></a>O que é o gerenciamento de carga de trabalho?

A execução de cargas de trabalho mistas pode representar desafios de recursos em sistemas ocupados.  Os arquitetos de soluções buscam maneiras de separar as atividades clássicas de data warehouse (como carregar, transformar e consultar dados) para garantir que existam recursos suficientes para atingir os SLAs.  

O isolamento físico do servidor pode levar a pockets de infraestrutura subutilizados, com excesso de reservas ou em um estado em que os caches são constantemente preparados para iniciar e parar o hardware.  Um esquema de gerenciamento de carga de trabalho bem-sucedido gerencia efetivamente os recursos, garante uma utilização de recursos altamente eficiente e maximiza o retorno sobre o investimento (ROI).

Uma carga de trabalho do data warehouse refere-se a todas as operações que são realizadas em realizada a um data warehouse. O detalhamento e a amplitude desses componentes dependem do nível de maturidade do data warehouse.  A carga de trabalho do data warehouse abrange:

- Todo o processo de carregamento de dados no warehouse
- Análise e relatórios do data warehouse
- Gerenciamento de dados no data warehouse
- Exportação de dados do data warehouse

A capacidade de desempenho de um data warehouse é determinada pelo [unidades do data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md).

- Para exibir os recursos alocados para todos os perfis de desempenho, confira [Limites de memória e simultaneidade](memory-concurrency-limits.md).
- Para ajustar a capacidade, você pode [escalar ou reduzir verticalmente](quickstart-scale-compute-portal.md).

## <a name="workload-management-concepts"></a>Conceitos do gerenciamento da carga de trabalho

No passado, para o SQL do Synapse no Azure Synapse, você gerenciou o desempenho das consultas por meio das [classes de recurso](resource-classes-for-workload-management.md).  As classes de recursos permitiram atribuir memória a uma consulta com base na associação da função.  O principal desafio das classes de recursos é que, uma vez configuradas, não havia governança nem capacidade para controlar a carga de trabalho.  

Por exemplo, conceder uma associação da função de usuário ad hoc ao smallrc permitia que o usuário consumisse 100% da memória no sistema.  Com as classes de recursos, não há como reservar e garantir que os recursos estejam disponíveis para cargas de trabalho críticas.

O gerenciamento de carga de trabalho do pool SQL dedicado no Azure Synapse consiste em três conceitos de alto nível: [classificação de carga de trabalho](sql-data-warehouse-workload-classification.md), importância da [carga](sql-data-warehouse-workload-importance.md)de trabalho e [isolamento da carga](sql-data-warehouse-workload-isolation.md)de trabalho  Esses recursos oferecem mais controle sobre como sua carga de trabalho utiliza os recursos do sistema.

A classificação da carga de trabalho é o conceito de atribuir uma solicitação a um grupo de carga de trabalho e definir níveis de importância.  Historicamente, essa atribuição foi feita por meio de associação da função usando [sp_addrolemember](resource-classes-for-workload-management.md#change-a-users-resource-class).  Agora essa ação pode ser feita por meio do [CREATE WORKLOAD CLASSIFER](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  O recurso de classificação fornece um conjunto mais avançado de opções, como rótulo, sessão e tempo para classificar solicitações.

A importância da carga de trabalho influencia a ordem em que uma solicitação obtém acesso aos recursos.  Em um sistema ocupado, uma solicitação com maior importância acessa os recursos primeiro.  A importância também pode garantir o acesso ordenado a bloqueios.

O isolamento da carga de trabalho reserva recursos para um grupo de carga de trabalho.  Os recursos reservados em um grupo de carga de trabalho são mantidos exclusivamente para esse grupo de carga de trabalho, para garantir a execução.  Os grupos de carga de trabalho também permitem que você defina a quantidade de recursos atribuídos por solicitação, assim como as classes de recursos.  Os grupos de carga de trabalho oferecem a capacidade de reservar ou limitar a quantidade de recursos que um conjunto de solicitações pode consumir.  Por fim, os grupos de carga de trabalho são um mecanismo para aplicar regras às solicitações, como o tempo limite de consulta.  

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre a classificação da carga de trabalho, confira [Classificação da carga de trabalho](sql-data-warehouse-workload-classification.md).  
- Para obter mais informações sobre o isolamento de carga de trabalho, confira [Isolamento da carga de trabalho](sql-data-warehouse-workload-isolation.md).  
- Para obter mais informações sobre a importância da carga de trabalho, confira [Importância da carga de trabalho](sql-data-warehouse-workload-importance.md).  
- Para obter mais informações sobre o monitoramento do gerenciamento da carga de trabalho, confira [Monitoramento do Portal de Gerenciamento da Carga de Trabalho](sql-data-warehouse-workload-management-portal-monitor.md).  
