---
title: Glossário de ferramentas do banco de dados elástico
description: Explicação dos termos usados para as ferramentas de banco de dados elástico
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: 166e365a4ead8ad6d0f7e543c081161ebff5f027
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92330454"
---
# <a name="elastic-database-tools-glossary"></a>Glossário de ferramentas do banco de dados elástico
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Os termos a seguir são definidos para as [ferramentas de banco de dados elástico](elastic-scale-introduction.md). As ferramentas são usadas para gerenciar [mapas de fragmentos](elastic-scale-shard-map-management.md) e incluem a [biblioteca de cliente](elastic-database-client-library.md), a [ferramenta de divisão e mesclagem](elastic-scale-overview-split-and-merge.md), [pools elásticos](elastic-pool-overview.md) e [consultas](elastic-query-overview.md). 

Esses termos são usados em [Adicionando um fragmento usando ferramentas de Banco de Dados Elástico](elastic-scale-add-a-shard.md) e [Usando a classe RecoveryManager para corrigir problemas de mapas de fragmentos](elastic-database-recovery-manager.md).

![Termos de escala elástica][1]

**Banco de dados**: um banco de dados no banco de dados SQL do Azure. 

**Roteamento dependente de dados**: a funcionalidade que permite que um aplicativo se conecte a um fragmento dada uma chave de fragmentação específica. Consulte [Roteamento dependente de dados](elastic-scale-data-dependent-routing.md). Compare com a **[Multi-Shard Query](elastic-scale-multishard-querying.md)**.

**Mapa de fragmentos global**: o mapa entre chaves de fragmentação e seus respectivos fragmentos em um **conjunto de fragmentos**. O mapa de fragmentos global é armazenado no **gerenciador do mapa de fragmentos**. Compare com o **mapa de fragmentos local**.

**Mapa de fragmentos de lista**: um mapa de fragmentos no qual as chaves de fragmentação são mapeadas individualmente. Compare com o **Mapa de fragmentos de intervalo**.   

**Mapa de fragmentos local**: armazenado em um fragmento, o mapa de fragmentos local contém mapeamentos para os shardlets que residem no fragmento.

**Consulta de vários fragmentos**: a capacidade de executar uma consulta em vários fragmentos; os conjuntos de resultados são retornados usando a semântica UNION ALL (também conhecida como “consulta do tipo fan-out”). Compare com o **roteamento dependente de dados**.

**Multilocatário** e **Locatário único**: mostra um banco de dados com locatário único e um banco de dados multilocatário:

![Captura de tela que mostra um banco de dados de locatário único e um banco de dados de vários locatários.](./media/elastic-scale-glossary/multi-single-simple.png)

Esta é uma representação de bancos de dados de único locatário e multilocatário **fragmentados** . 

![Bancos de dados de único locatário e multilocatário](./media/elastic-scale-glossary/shards-single-multi.png)

**Mapa de fragmentos de intervalo**: um mapa de fragmentos em que a estratégia de distribuição de fragmentos é baseada em vários intervalos de valores contíguos. 

**Tabelas de referência**: tabelas que não são fragmentadas, mas replicadas nos fragmentos. Por exemplo, códigos postais podem ser armazenados em uma tabela de referência. 

**Fragmento**: um banco de dados no banco de dados SQL do Azure que armazena dados de um conjunto de dado fragmentado. 

**Elasticidade de fragmento**: a capacidade de executar **escala horizontal** e **escala vertical**.

**Tabelas fragmentadas**: tabelas que são fragmentadas, ou seja, cujos dados são distribuídos por meio de fragmentos com base em seus valores de chave de fragmentação. 

**Chave de fragmentação**: um valor de coluna que determina como os dados são distribuídos nos fragmentos. O tipo do valor pode ser um dos seguintes: **int**, **bigint**, **varbinary** ou **uniqueidentifier**. 

**Conjunto de fragmentos**: a coleção de fragmentos que são atribuídos ao mesmo mapa de fragmentos no gerenciador de mapa de fragmentos.  

**Shardlet**: todos os dados associados a um único valor de uma chave de fragmentação em um fragmento. Um shardlet é a menor unidade de movimentação de dados possível ao redistribuir tabelas fragmentadas. 

**Mapa de fragmentos**: o conjunto de mapeamentos entre chaves de fragmentação e seus respectivos fragmentos.

**Gerenciador de mapa de fragmentos**: um objeto de gerenciamento e um repositório de dados que contém o(s) mapa(s) de fragmentos, locais de fragmentos e mapeamentos para um ou mais conjuntos de fragmentos.

![O diagrama mostra um Gerenciador de mapa de fragmentos associado a shardmaps_global, shards_global e shard_mappings_global.][2]

## <a name="verbs"></a>Verbos
**Escala horizontal**: o ato de escalar horizontal (ou verticalmente) uma coleção de fragmentos adicionando ou removendo fragmentos de um mapa de fragmentos, como mostrado abaixo.

![Dimensionamento horizontal e vertical][3]

**Mesclar**: o ato de mover shardlets de dois fragmentos para um e atualizar o mapa de fragmentos de acordo.

**Mover shardlet**: o ato de mover um único shardlet para um fragmento diferente. 

**Fragmentar**: o ato de particionar horizontalmente dados estruturados de modo idêntico em vários bancos de dados com base em uma chave de fragmentação.

**Dividir**: o ato de mover vários shardlets de um fragmento para outro (normalmente novo). Uma chave de fragmentação é fornecida pelo usuário como o ponto de divisão.

**Dimensionamento vertical**: o ato de escalar (ou reduzir) verticalmente o tamanho da computação de um fragmento individual. Por exemplo, alterar um fragmento de Standard para Premium (o que resulta em mais recursos de computação). 

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/elastic-scale-glossary/glossary.png
[2]: ./media/elastic-scale-glossary/mappings.png
[3]: ./media/elastic-scale-glossary/h_versus_vert.png

