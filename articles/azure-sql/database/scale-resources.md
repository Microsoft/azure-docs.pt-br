---
title: Escalar recursos
description: Este artigo explica como dimensionar seu banco de dados no banco de dados SQL do Azure e no SQL Instância Gerenciada adicionando ou removendo recursos alocados.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: wiassaf, sstein
ms.date: 06/25/2019
ms.openlocfilehash: ca1a2edec70b13f111ffd89278aa39d1ddea7f67
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105035635"
---
# <a name="dynamically-scale-database-resources-with-minimal-downtime"></a>Dimensionar dinamicamente os recursos de banco de dados com o tempo de inatividade mínimo
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

O banco de dados SQL do Azure e o SQL Instância Gerenciada permitem que você adicione dinamicamente mais recursos ao seu banco de dados com [tempo de inatividade](https://azure.microsoft.com/support/legal/sla/sql-database)mínimo; no entanto, há uma mudança no período em que a conectividade é perdida no banco de dados por um curto período de tempo, o que pode ser mitigado usando a lógica de repetição.

## <a name="overview"></a>Visão geral

Quando a demanda de seu aplicativo cresce de alguns dispositivos e clientes para milhões, o banco de dados SQL do Azure e o SQL Instância Gerenciada são dimensionados de forma dinâmica com tempo de inatividade mínimo. A escalabilidade é uma das características mais importantes da PaaS (plataforma como serviço) que permite que você adicione dinamicamente mais recursos ao seu serviço quando necessário. O Banco de Dados SQL do Azure permite que você altere com facilidade os recursos (potência da CPU, memória, taxa de transferência de E/S e armazenamento) alocados a seus bancos de dados.

Você pode atenuar problemas de desempenho devido ao aumento no uso do aplicativo que não pode ser corrigido usando métodos de indexação ou de reescrita de consulta. A adição de mais recursos permite que você responda rapidamente quando seu banco de dados atingir os limites de recursos atuais e precisar de mais capacidade para manipular a carga de trabalho de entrada. O Banco de Dados SQL do Azure também possibilita que você reduza verticalmente os recursos quando eles não forem necessários para reduzir o custo.

Você não precisa se preocupar com a compra do hardware e a alteração da infraestrutura subjacente. O dimensionamento de um banco de dados pode ser feito facilmente por meio do portal do Azure usando um controle deslizante.

![Dimensionar o desempenho do banco de dados](./media/scale-resources/scale-performance.svg)

O banco de dados SQL do Azure oferece o [modelo de compra baseado em DTU](service-tiers-dtu.md) e o [modelo de compra baseado em VCORE](service-tiers-vcore.md), enquanto o Azure SQL instância gerenciada oferece apenas o modelo de [compra baseado em VCORE](service-tiers-vcore.md). 

- O [modelo de compra baseado em DTU](service-tiers-dtu.md) oferece uma mistura de recursos de computação, memória e e/s em três camadas de serviço para dar suporte a cargas de trabalho de banco de dados leves a pesadas: básico, Standard e Premium. Níveis de desempenho dentro de cada camada fornecem uma mistura diferente desses recursos, aos quais você pode adicionar recursos de armazenamento.
- O [modelo de compra baseado em vCore](service-tiers-vcore.md) permite que você escolha o número de vCores, a quantidade ou a memória e a quantidade e a velocidade de armazenamento. Esse modelo de compra oferece três camadas de serviço: Uso Geral, Comercialmente Crítico e hiperescala.

Você pode criar seu primeiro aplicativo em um banco de dados individual pequeno com um baixo custo mensal na camada de serviço de Básica, Geral ou de Uso Geral e, depois, alterar a camada de serviço manualmente ou de forma programática a qualquer momento para a camada de serviço Premium ou Comercialmente Crítico para atender às necessidades da solução. Você pode ajustar o desempenho sem tempo de inatividade para seu aplicativo ou para seus clientes. A escalabilidade dinâmica permite que o banco de dados responda de forma transparente às mudanças rápidas de requisitos de recursos e que você pague apenas pelos recursos de que precisa, quando precisar deles.

> [!NOTE]
> A escalabilidade dinâmica é diferente do dimensionamento automático. O dimensionamento automático é quando um serviço é dimensionado automaticamente com base em critérios, enquanto a escalabilidade dinâmica permite o dimensionamento manual com um tempo de inatividade mínimo.

Bancos de dados individuais no Banco de Dados SQL do Azure dão suporte à escalabilidade dinâmica manual, mas não ao dimensionamento automático. Para obter uma experiência mais *automática* , considere o uso de pools elásticos, que permitem que os bancos de dados compartilhem recursos em um pool com base nas necessidades individuais do banco.
No entanto, há scripts que podem ajudar a automatizar a escalabilidade de um único banco de dados no banco de dados SQL do Azure. Para ver um exemplo, consulte [Usar o PowerShell para monitorar e dimensionar um Banco de Dados SQL individual](scripts/monitor-and-scale-database-powershell.md).

Você pode alterar as [camadas de serviço de DTU](service-tiers-dtu.md) ou as [características de vCore](resource-limits-vcore-single-databases.md) a qualquer momento com tempo de inatividade mínimo para seu aplicativo (tempo médio de quatro segundos). Para muitos negócios e aplicativos, ser capaz de criar bancos de dados e ajustar o desempenho sob demanda é o suficiente, especialmente se os padrões de uso forem relativamente previsíveis. Mas se você tiver os padrões de uso imprevisíveis, pode ser difícil de gerenciar os custos e o seu modelo de negócios. Para este cenário, você pode usar um pool elástico com um determinado número de eDTUs que são compartilhados entre vários bancos de dados no pool.

![Introdução ao Banco de Dados SQL: DTUs de banco de dados individual por camada e por nível](./media/scale-resources/single_db_dtus.png)

O banco de dados SQL do Azure oferece a capacidade de dimensionar dinamicamente seus bancos de dados:

- Com um [banco de dados individual](single-database-scale.md), você pode usar modelos de [DTU](resource-limits-dtu-single-databases.md) ou [vCore](resource-limits-vcore-single-databases.md) para definir a quantidade máxima de recursos que serão atribuídos a cada banco de dados.
- Os [pools elásticos](elastic-pool-scale.md) permitem que você defina o limite máximo de recursos por grupo de bancos de dados no pool.

O Azure SQL Instância Gerenciada também permite que você dimensione: 

- O [SQL instância gerenciada](../managed-instance/sql-managed-instance-paas-overview.md) usa o modo [vCores](../managed-instance/sql-managed-instance-paas-overview.md#vcore-based-purchasing-model) e permite que você defina os núcleos máximos de CPU e o máximo de armazenamento alocado para sua instância. Todos os bancos de dados dentro da instância gerenciada compartilharão os recursos alocados para a instância.

Iniciar a ação escalar verticalmente ou reduzir horizontalmente em qualquer um dos tipos reiniciaria o processo do mecanismo de banco de dados e o moveria para uma máquina virtual diferente, se necessário. Mover o processo do mecanismo de banco de dados para uma nova máquina virtual é um **processo online** no qual você pode continuar usando o serviço de banco de dados SQL do Azure existente enquanto o processo está em andamento. Depois que o mecanismo de banco de dados de destino estiver totalmente inicializado e pronto para processar as consultas, as conexões serão [alternadas da origem para o mecanismo de banco de dados de destino](single-database-scale.md#impact).

> [!NOTE]
> Não é recomendável dimensionar sua instância gerenciada se uma transação de longa execução, como importação de dados, trabalhos de processamento de dados, recompilação de índice, etc., estiver em execução ou se você tiver qualquer conexão ativa na instância. Para evitar que o dimensionamento demorasse mais tempo para ser concluído do que o normal, você deve dimensionar a instância após a conclusão de todas as operações de execução longa.

> [!NOTE]
> Você pode esperar uma pequena interrupção de conexão quando o processo de escalar/reduzir verticalmente for concluído. Se você tiver implementado a [lógica de repetição para erros transitórios padrão](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors), não perceberá o failover.

## <a name="alternative-scale-methods"></a>Métodos alternativos de escala

O dimensionamento de recursos é a maneira mais fácil e mais eficiente de melhorar o desempenho do banco de dados sem alterar o código do banco de dados ou do aplicativo. Em alguns casos, até mesmo as mais altas camadas de serviço, tamanhos de computação e otimizações de desempenho podem não lidar com a carga de trabalho de uma maneira bem-sucedida e econômica. Nesse caso, você tem essas opções adicionais para dimensionar seu banco de dados:

- [A expansão de leitura](read-scale-out.md) é um recurso disponível no qual você está obtendo uma réplica somente leitura de seus dados, em que você pode executar consultas somente leitura, como relatórios. Uma réplica somente leitura tratará sua carga de trabalho somente leitura sem afetar o uso de recursos no banco de dados primário.
- A [fragmentação de banco de dados](elastic-scale-introduction.md) é um conjunto de técnicas que permite dividir os dados em vários bancos de dados e dimensioná-los de forma independente.

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre como melhorar o desempenho do banco de dados alterando o código do banco de dados, confira [Encontrar e aplicar recomendações de desempenho](database-advisor-find-recommendations-portal.md).
- Para obter informações sobre como deixar a inteligência interna do banco de dados otimizar o banco de dados, confira [Ajuste automático](automatic-tuning-overview.md).
- Para obter informações sobre a expansão de leitura no banco de dados SQL do Azure, consulte como [usar réplicas somente leitura para balancear a carga de cargas de trabalho de consulta somente leitura](read-scale-out.md).
- Para obter informações sobre a Fragmentação de um banco de dados, confira [Escalando horizontalmente com o Banco de Dados SQL do Azure](elastic-scale-introduction.md).
