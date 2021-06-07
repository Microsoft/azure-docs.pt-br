---
title: O que é um banco de dados individual?
description: Saiba mais sobre o tipo de recurso de banco de dados individual no banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 04/08/2019
ms.openlocfilehash: 8f92fe8e4a4ebbc2d970bf28e415859249b9f67c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "84343312"
---
# <a name="what-is-a-single-database-in-azure-sql-database"></a>O que é um banco de dados individual no banco de dados SQL do Azure?
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

O tipo de recurso de banco de dados individual cria um banco de dados no banco de dados SQL do Azure com seu próprio conjunto de recursos e é gerenciado por meio de um [servidor](logical-servers.md). Com um único banco de dados, cada banco de dados é isolado e portátil. Cada uma tem sua própria camada de serviço dentro do [modelo de compra baseado em DTU](service-tiers-dtu.md) ou [modelo de compra baseado em vCore](service-tiers-vcore.md) e um tamanho de computação garantido.

> [!IMPORTANT]
> O banco de dados individual é um tipo de recurso para o banco de dados SQL do Azure. A outra é de [pools elásticos](elastic-pool-overview.md).

## <a name="dynamic-scalability"></a>Dimensionamento dinâmico

Você pode criar seu primeiro aplicativo em um banco de dados pequeno e único com baixo custo na camada de computação sem servidor ou em um tamanho de computação pequeno na camada de computação provisionada. Você altera a [camada de computação ou de serviço](single-database-scale.md) manualmente ou programaticamente a qualquer momento para atender às necessidades de sua solução. Você pode ajustar o desempenho sem tempo de inatividade para seu aplicativo ou para seus clientes. A escalabilidade dinâmica permite que o banco de dados responda de forma transparente às mudanças rápidas de requisitos de recursos e que você pague apenas pelos recursos de que precisa, quando precisar deles.

## <a name="single-databases-and-elastic-pools"></a>Bancos de dados individuais e pools elásticos

Um banco de dados individual pode ser movido de ou para um [pool elástico](elastic-pool-overview.md) para compartilhamento de recursos. Para muitas empresas e aplicativos, ser capaz de criar bancos de dados únicos e ajustar o desempenho sob demanda é o suficiente, especialmente se os padrões de uso forem relativamente previsíveis. Mas se você tiver os padrões de uso imprevisíveis, pode ser difícil de gerenciar os custos e o seu modelo de negócios. Os pools elásticos são projetados para resolver esse problema. O conceito é simples. Você aloca recursos de desempenho a um pool em vez de a um banco de dados individual e paga pelos recursos de desempenho coletivo do pool, em vez de pelo desempenho de banco de dados único.

## <a name="monitoring-and-alerting"></a>Monitoramento e alertas

Use as ferramentas de [monitoramento de desempenho interno](performance-guidance.md) e [alerta](alerts-insights-configure-portal.md) em conjunto com as classificações de desempenho baseadas nos vCores. Usando essas ferramentas, você pode avaliar rapidamente o impacto da expansão ou redução com base nas suas necessidades de desempenho atuais ou de projeto. Além disso, o Banco de Dados SQL pode [emitir métrica e logs de recursos](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) para facilitar o monitoramento.

## <a name="availability-capabilities"></a>Recursos de disponibilidade

Os bancos de dados individuais e os pools elásticos fornecem muitas características de disponibilidade. Para obter informações, confira [Características de disponibilidade](sql-database-paas-overview.md#availability-capabilities).

## <a name="transact-sql-differences"></a>Diferenças do Transact-SQL

Há suporte total, tanto no Microsoft SQL Server quanto no Banco de Dados SQL do Azure, para a maioria dos aplicativos e recursos Transact-SQL. Por exemplo, os componentes principais do SQL como tipos de dados, operadores, cadeia de caracteres, funções aritméticas, lógicas e de cursor funcionam da mesma maneira no SQL Server e no Banco de Dados SQL. No entanto, existem algumas diferenças de T-SQL em DDL (linguagem de definição de dados) e elementos DML (linguagem de manipulação de dados), resultando em instruções T-SQL e consultas que têm suporte apenas parcial (o que discutiremos posteriormente neste artigo).

Além disso, há alguns recursos e sintaxe que não têm suporte porque o banco de dados SQL do Azure foi projetado para isolar recursos de dependências no banco de dados mestre e no sistema operacional. Assim, a maioria das atividades no nível do servidor são inapropriadas para o Banco de Dados SQL. As instruções e opções T-SQL não estarão disponíveis se configurarem opções de nível de servidor, configurar componentes do sistema operacional ou especificar a configuração do sistema de arquivos. Quando essas funcionalidades são necessárias, uma alternativa apropriada costuma estar disponível de alguma forma no Banco de Dados SQL ou em outro recurso ou serviço do Azure.

Para obter mais informações, confira [Resolvendo diferenças de Transact-SQL durante a migração para o Banco de Dados SQL](transact-sql-tsql-differences-sql-server.md).

## <a name="security"></a>Segurança

O banco de dados SQL fornece uma variedade de recursos [internos de segurança e conformidade](security-overview.md) para ajudar seu aplicativo a atender a vários requisitos de segurança e conformidade.

> [!IMPORTANT]
> O banco de dados SQL do Azure foi certificado em relação a vários padrões de conformidade. Para saber mais, confira a [Central de Confiabilidade do Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), onde é possível encontrar a lista mais atualizada de certificações de conformidade do Banco de Dados SQL do Azure.

## <a name="next-steps"></a>Próximas etapas

- Para começar rapidamente com um único banco de dados, comece com o [Guia de início rápido do banco de dados individual](quickstart-content-reference-guide.md).
- Para saber mais sobre como migrar um banco de dados do SQL Server para o Azure, confira [Migrar para o Banco de Dados SQL do Azure](migrate-to-database-from-sql-server.md).
- Para obter informações sobre os recursos com suporte, consulte [Recursos](features-comparison.md).
