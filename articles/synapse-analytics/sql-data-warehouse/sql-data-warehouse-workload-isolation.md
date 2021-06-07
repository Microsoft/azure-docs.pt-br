---
title: Isolamento de carga de trabalho
description: Diretrizes para definir o isolamento de carga de trabalho com grupos de carga de trabalho no Azure Synapse Analytics.
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
ms.openlocfilehash: 506aed16f1b8a6c631a759bb1367aef8242859ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98734773"
---
# <a name="azure-synapse-analytics-workload-group-isolation"></a>Isolamento do grupo de carga de trabalho do Azure Synapse Analytics

Este artigo explica como os grupos de carga de trabalho podem ser usados para configurar o isolamento de carga de trabalho, conter recursos e aplicar regras de tempo de execução para execução de consulta.

## <a name="workload-groups"></a>Grupos de carga de trabalho

Grupos de carga de trabalho são contêineres para um conjunto de solicitações e são a base de como o gerenciamento de cargas de trabalho, incluindo isolamento de carga de trabalho, é configurado em um sistema.  Os grupos de carga de trabalho são criados por meio da sintaxe [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  Uma configuração simples de gerenciamento de carga de trabalho pode gerenciar cargas de dados e consultas de usuário.  Por exemplo, um grupo de carga de trabalho chamado `wgDataLoads` definirá os aspectos de carga de trabalho dos dados que estão sendo carregados no sistema. Além disso, um grupo de carga de trabalho chamado `wgUserQueries` definirá os aspectos de carga de trabalho dos usuários que executam consultas para ler dados do sistema.

As seções a seguir destacarão como os grupos de carga de trabalho permitem definir o isolamento e contenção, solicitar definição de recursos e obedecer às regras de execução.

## <a name="workload-isolation"></a>Isolamento de carga de trabalho

O isolamento da carga de trabalho significa que os recursos são reservados, exclusivamente, para um grupo de carga de trabalho.  O isolamento da carga de trabalho é obtido pela configuração do parâmetro MIN_PERCENTAGE_RESOURCE como maior que zero na sintaxe [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  Para cargas de trabalho de execução contínua que precisem aderir a SLAs rígidos, o isolamento faz com que os recursos estejam sempre disponíveis para o grupo de carga de trabalho.

Configurar o isolamento de carga de trabalho define um nível garantido de simultaneidade implicitamente. Por exemplo, um grupo de carga de trabalho com `MIN_PERCENTAGE_RESOURCE` definido como 30% e `REQUEST_MIN_RESOURCE_GRANT_PERCENT` definido como 2% tem 15 de simultaneidade garantidos.  O nível de simultaneidade é garantido porque 15-2% slots de recursos são reservados no grupo de carga de trabalho a qualquer hora (independentemente de como `REQUEST_*MAX*_RESOURCE_GRANT_PERCENT` estiver configurado).  Se `REQUEST_MAX_RESOURCE_GRANT_PERCENT` for maior que `REQUEST_MIN_RESOURCE_GRANT_PERCENT` e `CAP_PERCENTAGE_RESOURCE` for maior do que `MIN_PERCENTAGE_RESOURCE`, os recursos adicionais serão adicionados mediante solicitação.  Se `REQUEST_MAX_RESOURCE_GRANT_PERCENT` e `REQUEST_MIN_RESOURCE_GRANT_PERCENT` forem iguais e `CAP_PERCENTAGE_RESOURCE` for maior que `MIN_PERCENTAGE_RESOURCE`, será possível ter simultaneidade adicional.  Considere o método abaixo para determinar a simultaneidade garantida:

[Simultaneidade garantida] = [`MIN_PERCENTAGE_RESOURCE`]/[`REQUEST_MIN_RESOURCE_GRANT_PERCENT`]

> [!NOTE]
> Existem valores mínimos viáveis de nível de serviço específicos para min_percentage_resource.  Para obter mais informações e detalhes, confira [Valores efetivos](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json?view=azure-sqldw-latest&preserve-view=true#effective-values).

Na ausência de isolamento de carga de trabalho, as solicitações operam no [pool compartilhado](#shared-pool-resources) de recursos.  O acesso aos recursos no pool compartilhado não é garantido e é atribuído de acordo com a [importância](sql-data-warehouse-workload-importance.md).

A configuração do isolamento de carga de trabalho deve ser feita com cautela, pois os recursos são alocados para o grupo de carga de trabalho mesmo que não haja nenhuma solicitação ativa nele. O excesso de configuração do isolamento pode levar à redução geral da utilização do sistema.

Os usuários devem evitar uma solução de gerenciamento de carga de trabalho que configure 100% de isolamento de carga de trabalho: 100% de isolamento é obtido quando a soma de min_percentage_resource configurada em todos os grupos de cargas de trabalho é igual a 100%.  Esse tipo de configuração é excessivamente restritivo e rígido, deixando pouco espaço para solicitações de recursos que sejam classificadas incorretamente por acidente. Há uma provisão que permite que uma solicitação seja executada em grupos de carga de trabalho não configurados para isolamento. Os recursos alocados a essa solicitação aparecerão como zero nos DMVs dos sistemas e pegarão emprestado um pouco da concessão de recurso dos recursos reservados ao sistema.

> [!NOTE]
> Para garantir a melhor utilização de recursos, pense em uma solução de gerenciamento de carga de trabalho que aproveite algum isolamento para fazer com que os SLAs sejam atendidos e misturados com recursos compartilhados que são acessados de acordo com a [importância da carga de trabalho ](sql-data-warehouse-workload-importance.md).

## <a name="workload-containment"></a>Contenção da carga de trabalho

A contenção da carga de trabalho refere-se à limitação da quantidade de recursos que um grupo de carga de trabalho pode consumir.  A contenção da carga de trabalho é obtida pela configuração do parâmetro CAP_PERCENTAGE_RESOURCE para menos de 100 na sintaxe [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  Considere o cenário no qual os usuários precisam de acesso de leitura ao sistema para que possam executar uma análise hipotética por meio de consultas ad hoc.  Esses tipos de solicitações podem ter um impacto negativo em outras cargas de trabalho em execução no sistema.  A configuração da contenção garante a limitação da quantidade de recursos.

Configurar a contenção de carga de trabalho define implicitamente um nível máximo de simultaneidade.  Com um CAP_PERCENTAGE_RESOURCE definido como 60% e um REQUEST_MIN_RESOURCE_GRANT_PERCENT definido como 1%, é permitido um nível de simultaneidade de até 60 para o grupo de cargas de trabalho.  Considere o método incluído abaixo para determinar a simultaneidade máxima:

[Simultaneidade máxima] = [`CAP_PERCENTAGE_RESOURCE`]/[`REQUEST_MIN_RESOURCE_GRANT_PERCENT`]

> [!NOTE]
> O CAP_PERCENTAGE_RESOURCE efetivo de um grupo de carga de trabalho não alcançará 100% quando forem criados grupos de carga de trabalho com MIN_PERCENTAGE_RESOURCE em um nível maior que zero.  Consulte [sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para obter os valores de tempo de execução efetivos.

## <a name="resources-per-request-definition"></a>Recursos por definição de solicitação

Os grupos de carga de trabalho fornecem um mecanismo para definir a quantidade mínima e máxima de recursos alocados por solicitação com os parâmetros REQUEST_MIN_RESOURCE_GRANT_PERCENT e REQUEST_MAX_RESOURCE_GRANT_PERCENT na sintaxe [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  Nesse caso, os recursos são CPU e memória.  A configuração desses valores determina a quantidade de recursos e o nível de simultaneidade que podem ser obtidos no sistema.

> [!NOTE]
> REQUEST_MAX_RESOURCE_GRANT_PERCENT é um parâmetro opcional que usa como padrão o mesmo valor especificado para REQUEST_MIN_RESOURCE_GRANT_PERCENT.

Assim como ocorre na escolha de uma classe de recurso, a configuração de REQUEST_MIN_RESOURCE_GRANT_PERCENT define o valor dos recursos utilizados por uma solicitação.  A quantidade de recursos indicados pelo valor definido é garantida à solicitação para alocação antes do início da execução.  Para clientes que estão migrando de classes de recursos para grupos de carga de trabalho, considere seguir o artigo de [Tutorial](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md) para fazer o mapeamento de classes de recursos para grupos de carga de trabalho como ponto de partida.

A configuração de REQUEST_MAX_RESOURCE_GRANT_PERCENT com um valor maior que REQUEST_MIN_RESOURCE_GRANT_PERCENT permite que o sistema aloque mais recursos por solicitação.  Durante o agendamento de uma solicitação, o sistema determina a alocação de recursos real para a solicitação, que fica entre REQUEST_MIN_RESOURCE_GRANT_PERCENT e REQUEST_MAX_RESOURCE_GRANT_PERCENT, com base na disponibilidade dos recursos no pool compartilhado e na carga atual no sistema.  Os recursos devem existir no [pool compartilhado](#shared-pool-resources) de recursos quando a consulta é agendada.  

> [!NOTE]
> REQUEST_MIN_RESOURCE_GRANT_PERCENT e REQUEST_MAX_RESOURCE_GRANT_PERCENT têm valores efetivos que dependem dos valores efetivos de MIN_PERCENTAGE_RESOURCE e CAP_PERCENTAGE_RESOURCE.  Consulte [sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para obter os valores de tempo de execução efetivos.

## <a name="execution-rules"></a>Regras de execução

Em sistemas de relatórios ad hoc, os clientes podem executar acidentalmente consultas sem controle que afetam seriamente a produtividade de outras.  Os administradores do sistema são forçados a gastar tempo eliminando as consultas sem controle para liberar recursos do sistema.  Os grupos de cargas de trabalho oferecem a capacidade de configurar uma regra de tempo limite de execução de consulta para cancelar consultas que excederam o valor especificado.  A regra é configurada pela definição do parâmetro `QUERY_EXECUTION_TIMEOUT_SEC` na sintaxe [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="shared-pool-resources"></a>Recursos do pool compartilhado

Os recursos do pool compartilhado são os recursos não configurados para isolamento.  Os grupos de cargas de trabalho com MIN_PERCENTAGE_RESOURCE definido como zero aproveitam os recursos no pool compartilhado para executar solicitações.  Os grupos de cargas de trabalho com um CAP_PERCENTAGE_RESOURCE maior que MIN_PERCENTAGE_RESOURCE também usam recursos compartilhados.  A quantidade de recursos disponíveis no pool compartilhado é calculada da maneira indicada abaixo.

[Pool compartilhado] = 100 - [soma de `MIN_PERCENTAGE_RESOURCE` em todos os grupos de carga de trabalho]

O acesso aos recursos no pool compartilhado é alocado de acordo com a [importância](sql-data-warehouse-workload-importance.md).  As solicitações com o mesmo nível de importância acessarão os recursos do pool compartilhado com base em “primeiro a entrar, primeiro a sair”.

## <a name="next-steps"></a>Próximas etapas

- [Início Rápido: configurar o isolamento de carga de trabalho](quickstart-configure-workload-isolation-tsql.md)
- [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Converter classes de recursos em grupos de cargas de trabalho](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md).
- [Monitoramento do Portal de Gerenciamento de Carga de Trabalho](sql-data-warehouse-workload-management-portal-monitor.md).  
