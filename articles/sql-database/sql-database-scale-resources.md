---
title: Recursos de escala do Banco de Dados SQL do Azure | Microsoft Docs
description: Este artigo explica como dimensionar o banco de dados, adicionando ou removendo os recursos alocados.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 06/25/2019
ms.openlocfilehash: d8949f63dfa9b409cc14fe9c3bbed70f23a73c86
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/25/2019
ms.locfileid: "67357146"
---
# <a name="dynamically-scale-database-resources-with-minimal-downtime"></a>Dimensionar dinamicamente os recursos de banco de dados com o tempo de inatividade mínimo

Banco de dados SQL do Azure permite que você adicionar dinamicamente mais recursos ao seu banco de dados com pouca [tempo de inatividade](https://azure.microsoft.com/support/legal/sla/sql-database/v1_2/); no entanto, há uma opção de período onde conectividade for perdida ao banco de dados por um curto período de tempo, o que pode ser atenuada com o uso de lógica de repetição.

## <a name="overview"></a>Visão geral

Quando a demanda de seu aplicativo aumenta de alguns dispositivos e clientes para milhões, o Banco de Dados SQL do Azure é dimensionado de maneira dinâmica e com tempo de inatividade mínimo. A escalabilidade é uma das características mais importantes do PaaS que possibilita adicionar dinamicamente mais recursos ao serviço, quando necessário. O Banco de Dados SQL do Azure permite que você altere com facilidade os recursos (potência da CPU, memória, taxa de transferência de E/S e armazenamento) alocados a seus bancos de dados.

Você pode atenuar problemas de desempenho devido ao aumento no uso do aplicativo que não pode ser corrigido usando métodos de indexação ou de reescrita de consulta. A adição de mais recursos permite que você responda rapidamente quando seu banco de dados atingir os limites de recursos atuais e precisar de mais capacidade para manipular a carga de trabalho de entrada. O Banco de Dados SQL do Azure também possibilita que você reduza verticalmente os recursos quando eles não forem necessários para reduzir o custo.

Você não precisa se preocupar com a compra do hardware e a alteração da infraestrutura subjacente. O dimensionamento do banco de dados pode ser feito com facilidade por meio do portal do Azure usando um controle deslizante.

![Dimensionar o desempenho do banco de dados](media/sql-database-scalability/scale-performance.svg)

O Banco de Dados SQL do Azure oferece o [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) e o [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md).

- O [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) oferece uma mistura de computação, memória e recursos de E/S em três camadas de serviço para dar suporte a cargas de trabalho leves e pesadas de banco de dados: Básico, Standard e Premium. Níveis de desempenho dentro de cada camada fornecem uma mistura diferente desses recursos, aos quais você pode adicionar recursos de armazenamento.
- O [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md) permite que você escolha o número de vCores, a quantidade ou memória e a quantidade e velocidade de armazenamento. O modelo de compra oferece três camadas de serviço: Uso geral, essenciais para os negócios e em hiperescala.

Você pode criar seu primeiro aplicativo em um banco de dados individual pequeno com um baixo custo mensal na camada de serviço de Básica, Geral ou de Uso Geral e, depois, alterar a camada de serviço manualmente ou de forma programática a qualquer momento para a camada de serviço Premium ou Comercialmente Crítico para atender às necessidades da solução. Você pode ajustar o desempenho sem tempo de inatividade para seu aplicativo ou para seus clientes. A escalabilidade dinâmica permite que o banco de dados responda de forma transparente às mudanças rápidas de requisitos de recursos e que você pague apenas pelos recursos de que precisa, quando precisar deles.

> [!NOTE]
> A escalabilidade dinâmica é diferente do dimensionamento automático. Dimensionamento automático é quando um serviço é escalado automaticamente com base em critérios, enquanto a escalabilidade dinâmica permite o dimensionamento manual com um tempo de inatividade mínimo.

O Banco de Dados SQL do Azure Individual oferece suporte à escalabilidade dinâmica manual, mas não ao dimensionamento automático. Para uma experiência mais *automática*, considere o uso de pools elásticos, que permitem que os bancos de dados compartilhem recursos em um pool com base nas necessidades individuais do banco de dados.
No entanto, há scripts que podem ajudar a automatizar a escalabilidade de um Banco de Dados SQL do Azure individual. Para ver um exemplo, consulte [Usar o PowerShell para monitorar e dimensionar um Banco de Dados SQL individual](scripts/sql-database-monitor-and-scale-database-powershell.md).

Você pode alterar as [camadas de serviço de DTU](sql-database-service-tiers-dtu.md) ou as [características de vCore](sql-database-vcore-resource-limits-single-databases.md) a qualquer momento com tempo de inatividade mínimo para seu aplicativo (tempo médio de quatro segundos). Para muitos negócios e aplicativos, ser capaz de criar bancos de dados e ajustar o desempenho sob demanda é o suficiente, especialmente se os padrões de uso forem relativamente previsíveis. Mas se você tiver os padrões de uso imprevisíveis, pode ser difícil de gerenciar os custos e o seu modelo de negócios. Para este cenário, você pode usar um pool elástico com um determinado número de eDTUs que são compartilhados entre vários bancos de dados no pool.

![Introdução ao Banco de Dados SQL: DTUs do banco de dados individual por camada e nível](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

Todos os três tipos de Bancos de Dados SQL do Azure oferecem alguma capacidade de dimensionar dinamicamente os bancos de dados:

- Com um [banco de dados individual](sql-database-single-database-scale.md), você pode usar modelos de [DTU](sql-database-dtu-resource-limits-single-databases.md) ou [vCore](sql-database-vcore-resource-limits-single-databases.md) para definir a quantidade máxima de recursos que serão atribuídos a cada banco de dados.
- Uma [Instância Gerenciada](sql-database-managed-instance.md) usa o modo [vCores](sql-database-managed-instance.md#vcore-based-purchasing-model) e permite que você defina o máximo de núcleos de CPU e o máximo de armazenamento alocado para sua instância. Todos os bancos de dados na instância compartilharão os recursos alocados para a instância.
- Os [pools elásticos](sql-database-elastic-pool-scale.md) permitem que você defina o limite máximo de recursos por grupo de bancos de dados no pool.

> [!NOTE]
> Você pode esperar a conclusão de uma quebra de conexão curto quando a escala vertical/processo de redução vertical. Se você tiver implementado [lógica de repetição para erros transitórios padrão](sql-database-connectivity-issues.md#retry-logic-for-transient-errors), você não irá notar o failover.

## <a name="alternative-scale-methods"></a>Métodos alternativos de escala

O dimensionamento de recursos é a maneira mais fácil e mais eficiente para melhorar o desempenho do banco de dados sem alterar o código do banco de dados ou do aplicativo. Em alguns casos, até mesmo as camadas de serviço, os tamanhos da computação e as otimizações de desempenho mais altas podem não conseguir manipular a carga de trabalho com sucesso e de forma econômica. Nesses casos, há outras opções para dimensionar o banco de dados:

- A [escala de leitura](sql-database-read-scale-out.md) é um recurso disponível em que você obtém uma réplica somente leitura de seus dados, na qual você pode executar consultas somente leitura mais exigentes, como relatórios. A réplica somente leitura manipulará a carga de trabalho somente leitura sem afetar o uso de recursos no banco de dados primário.
- A [fragmentação de banco de dados](sql-database-elastic-scale-introduction.md) é um conjunto de técnicas que permite dividir os dados em vários bancos de dados e dimensioná-los de forma independente.

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre como melhorar o desempenho do banco de dados alterando o código do banco de dados, confira [Encontrar e aplicar recomendações de desempenho](sql-database-advisor-portal.md).
- Para obter informações sobre como deixar a inteligência interna do banco de dados otimizar o banco de dados, confira [Ajuste automático](sql-database-automatic-tuning.md).
- Para obter informações sobre a Escala de Leitura no serviço Banco de Dados SQL do Azure, confira como [usar réplicas somente leitura para balancear a carga de cargas de trabalho de consulta somente leitura](sql-database-read-scale-out.md).
- Para obter informações sobre a Fragmentação de um banco de dados, confira [Escalando horizontalmente com o Banco de Dados SQL do Azure](sql-database-elastic-scale-introduction.md).
