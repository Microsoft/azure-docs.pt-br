---
title: "Banco de Dados SQL: O que é um DTU? | Microsoft Docs"
description: "Noções básicas sobre o que uma unidade de transação do Banco de Dados SQL."
keywords: "opções de banco de dados, desempenho do banco de dados"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: CarlRabeler
ms.assetid: 89e3e9ce-2eeb-4949-b40f-6fc3bf520538
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: NA
ms.date: 04/14/2017
ms.author: carlrab
ms.openlocfilehash: da3399b9c6642435dc7b40ed1c843217c984d15e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="database-transaction-units-dtus-and-elastic-database-transaction-units-edtus"></a>DTUs (Unidades de transação de banco de dados) e eDTUs (Unidades de transação de banco de dados elástico)
Este artigo explica as DTUs (Unidades de transação de banco de dados) e eDTUs (Unidades de transação de banco de dados elástico), e o que acontece quando você atinge o máximo de DTUs ou eDTUs.  

## <a name="what-are-database-transaction-units-dtus"></a>O que são DTUs (Unidades de transação do banco de dados)?
Para um único Banco de Dados SQL do Azure em um nível de desempenho específico dentro de uma [camada de serviço](sql-database-single-database-resources.md), a Microsoft garante um certo nível de recursos para o banco de dados (independente de qualquer outro banco de dados na nuvem do Azure) e fornecendo um nível previsível de desempenho. Essa quantidade de recursos é calculada como um número de unidades de transação do banco de dados ou DTUs e é uma medida combinada de CPU, memória, E/S (E/S de dados e log de transações). A proporção entre esses recursos originalmente foi determinada por uma [carga de trabalho de parâmetro de comparação de OLTP](sql-database-benchmark-overview.md) projetada para ser o cenário típico de cargas de trabalho OLTP reais. Quando sua carga de trabalho excede o valor de qualquer um desses recursos, a taxa de transferência é restringida, resultando em desempenho mais lento e inatividade. Os recursos usados pela sua carga de trabalho não afetam os recursos disponíveis para outros Bancos de Dados SQL na nuvem do Azure, e os recursos usados por outras cargas de trabalho não afetam os recursos disponíveis para o Banco de Dados SQL.

![caixa delimitadora](./media/sql-database-what-is-a-dtu/bounding-box.png)

DTUs são úteis para entender a quantidade relativa de recursos entre Bancos de Dados SQL do Azure em diferentes níveis de desempenho e de serviço. Por exemplo, dobrar as DTUs aumentando o nível de desempenho de um banco de dados equivale a dobrar o conjunto de recursos disponíveis para esse banco de dados. Por exemplo, um banco de dados Premium P11 com 1.750 DTUs fornece 350x mais capacidade de computação DTU que um banco de dados básico com 5 DTUs.  

Para ver informações mais aprofundadas sobre o consumo de recursos (DTU) da sua carga de trabalho, use a [Análise de Desempenho de Consultas do Banco de Dados SQL do Azure](sql-database-query-performance.md) para:

- Identifique as principais consultas por contagem de CPU/Duração/Execução, que potencialmente podem ser ajustadas para melhorar o desempenho. Por exemplo, uma consulta com uso intensivo de E/S pode se beneficiar do uso de [técnicas de otimização de memória](sql-database-in-memory.md) para fazer melhor uso da memória disponível em uma determinada camada de serviço e nível de desempenho.
- Analise detalhadamente os dados de uma consulta e exiba o texto e o histórico de utilização de recursos.
- Acesse recomendações de ajuste de desempenho que mostram as ações executadas pelo [Assistente do Banco de Dados SQL do Azure](sql-database-advisor.md).

Você pode [alterar os níveis de serviço](sql-database-service-tiers.md) a qualquer momento com tempo de inatividade mínimo para seu aplicativo (tempo médio de quatro segundos). Para muitos negócios e aplicativos, ser capaz de criar bancos de dados e ajustar o desempenho sob demanda é o suficiente, especialmente se os padrões de uso forem relativamente previsíveis. Mas se você tiver os padrões de uso imprevisíveis, pode ser difícil de gerenciar os custos e o seu modelo de negócios. Para este cenário, você pode usar um pool elástico com um determinado número de eDTUs que são compartilhados entre vários banco de dados no pool.

![Introdução ao Banco de Dados SQL: DTUs de banco de dados individual por camada e por nível](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

## <a name="what-are-elastic-database-transaction-units-edtus"></a>O que são eDTUs (Unidades de transação de banco de dados elástico)?
Em vez de fornecer um conjunto dedicado de recursos (DTUs) para um Banco de Dados SQL que está sempre disponível independentemente de ser necessário ou não, é possível colocar os bancos de dados em um [pool elástico](sql-database-elastic-pool.md) em um servidor de Banco de Dados SQL que compartilha um pool de recursos entre esses bancos de dados. Os recursos compartilhados em um pool elástico medidos por Unidades de Transação de Banco de Dados elástico ou eDTUs. Pools elásticos fornecem uma solução simples e econômica para gerenciar as metas de desempenho para vários bancos de dados que têm padrões de uso muito variáveis e imprevisíveis. Em um pool elástico, é possível garantir que nenhum banco de dados use todos os recursos no pool, bem como que uma quantidade mínima de recursos esteja sempre disponível para um banco de dados em um pool elástico. 

![Introdução ao Banco de Dados SQL: eDTUs por camada e por nível](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Um pool é fornecido com um número definido de eDTUs por um preço definido. No pool elástico, os bancos de dados individuais recebem a flexibilidade do dimensionamento automático dentro dos limites configurados. Sob carga pesada, um banco de dados pode consumir mais eDTUs para atender à demanda, enquanto os bancos de dados sob carga leve consomem menos, até o ponto que os bancos de dados sem carga não consomem qualquer eDTU. Ao provisionar recursos para o pool inteiro, em vez de por banco de dados, as tarefas de gerenciamento são simplificadas, e você tem um orçamento previsível para o pool.

eDTUs adicionais podem ser adicionados a um pool existente sem tempo de inatividade do banco de dados ou impacto sobre os bancos de dados no pool. Da mesma forma, se eDTUs extras não forem mais necessários, eles poderão ser removidos de um pool existente a qualquer momento. Você pode adicionar ou subtrair bancos de dados para o pool ou limitar a quantidade de eDTUs que um banco de dados pode usar sob carga pesada para reservar eDTUs para outros bancos de dados. Se um banco de dados estiver subutilizando recursos de forma previsível, você poderá movê-lo para fora do pool e configurá-lo como um único banco de dados com a quantidade previsível de recursos necessários.

## <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>Como determinar o número de DTUs necessárias para minha carga de trabalho?
Se você quiser migrar uma carga de trabalho local existente, ou uma carga de trabalho virtual, para o Banco de Dados SQL do Azure, use a [Calculadora de DTU](http://dtucalculator.azurewebsites.net/) para aproximar o número de DTUs necessárias. Para uma carga de trabalho existente do Banco de Dados SQL do Azure, use [Análise de Desempenho de Consultas do Banco de Dados SQL](sql-database-query-performance.md) para compreender o consumo de recursos do banco de dados (DTUs) e saber mais sobre como otimizar sua carga de trabalho. Você também pode usar o DMV [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) para obter as informações de consumo de recursos da última hora. Como alternativa, também é possível consultar o modo de exibição de catálogo [Sys. resource_stats](http://msdn.microsoft.com/library/dn269979.aspx) para obter os mesmos dados para os últimos 14 dias, porém com uma fidelidade menor de médias de cinco minutos.

## <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>Como posso saber se eu teria benefícios com um pool elástico de recursos?
Pools também são indicados para um grande número de bancos de dados com padrões de utilização específicos. Para um determinado banco de dados, esse padrão é caracterizado por baixa utilização média com picos de utilização relativamente pouco frequentes. O Banco de Dados SQL avalia automaticamente a utilização histórica de recursos dos bancos de dados em um servidor de Banco de Dados SQL existente e recomenda a configuração de pool apropriada no portal do Azure. Para saber mais , confira [Quando um pool elástico deve ser usado?](sql-database-elastic-pool.md)

## <a name="what-happens-when-i-hit-my-maximum-dtus"></a>O que acontece quando eu atinjo o máximo de DTUs?
Os níveis de desempenho são calibrados e controlados para fornecer os recursos necessários para executar sua carga de trabalho de banco de dados até os limites máximos permitidos para a sua camada de serviço e nível de desempenho selecionados. Se a sua carga de trabalho está atingindo os limites em um dos limites de CPU/E/S de dados/E/S de Log, você continuará a receber os recursos no nível máximo permitido, mas provavelmente você observará latências maiores para suas consultas. Esses limites não resultam em erros, mas apenas em uma lentidão na carga de trabalho, a menos que a lentidão se torne tão grave que as consultas comecem a não serem executadas a tempo. Se você está atingindo os limites do máximo permitido de sessões/solicitações de usuários simultâneos (threads de trabalho), você verá erros explícitos. Confira [Limites de recursos do Banco de Dados SQL]( sql-database-resource-limits.md#what-happens-when-database-and-elastic-pool-resource-limits-are-reached) para saber mais sobre limite de recursos, além de CPU, memória, E/S de dados e E/S do log de transações.

## <a name="next-steps"></a>Próximas etapas
* Consulte [Camada de serviço](sql-database-service-tiers.md) para saber mais sobre os DTUs e os eDTUs disponíveis para bancos de dados individuais e para pools elásticos, bem como limites de recursos além da CPU, memória, E/S de dados e E/S de log de transações.
* Confira [Análise de Desempenho de Consultas de Banco de Dados SQL](sql-database-query-performance.md) para compreender o consumo (DTUs).
* Confira [Visão geral do parâmetro de Banco de Dados SQL](sql-database-benchmark-overview.md) para entender a metodologia por trás da carga de trabalho OLTP de parâmetro usada para determinar a combinação de DTU.
