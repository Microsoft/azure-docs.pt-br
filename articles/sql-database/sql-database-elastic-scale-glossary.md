---
title: Glossário de ferramentas de Banco de Dados Elástico | Microsoft Docs
description: Explicação dos termos usados para as ferramentas de banco de dados elástico
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 446203b45744a95c32cd41d9ded26fd960ac8a22
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60585538"
---
# <a name="elastic-database-tools-glossary"></a>Glossário de ferramentas do banco de dados elástico

Os termos a seguir são definidos para as [ferramentas de Banco de Dados Elástico](sql-database-elastic-scale-introduction.md), um recurso do Banco de Dados SQL do Azure. As ferramentas são usadas para gerenciar [mapas de fragmentos](sql-database-elastic-scale-shard-map-management.md) e incluem a [biblioteca de cliente](sql-database-elastic-database-client-library.md), a [ferramenta de divisão e mesclagem](sql-database-elastic-scale-overview-split-and-merge.md), [pools elásticos](sql-database-elastic-pool.md) e [consultas](sql-database-elastic-query-overview.md). 

Esses termos são usados em [Adicionando um fragmento usando ferramentas de Banco de Dados Elástico](sql-database-elastic-scale-add-a-shard.md) e [Usando a classe RecoveryManager para corrigir problemas de mapas de fragmentos](sql-database-elastic-database-recovery-manager.md).

![Termos de escala elástica][1]

**Banco de dados**: Um banco de dados SQL do Azure. 

**Roteamento dependente de dados**: A funcionalidade que permite que um aplicativo conecte um fragmento com uma chave de fragmentação específica. Consulte [Roteamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md). Compare com a **[Multi-Shard Query](sql-database-elastic-scale-multishard-querying.md)**.

**Mapa de fragmentos global**: O mapa entre as chaves de fragmentação seus respectivos fragmentos em um **conjunto de fragmentos**. O mapa de fragmentos global é armazenado no **gerenciador do mapa de fragmentos**. Compare com o **mapa de fragmentos local**.

**Mapa de fragmentos de lista**: Um mapa de fragmentos no qual as chaves de fragmentação são mapeadas individualmente. Compare com o **Mapa de fragmentos de intervalo**.   

**Mapa de fragmentos local**: Armazenado em um fragmento, o mapa de fragmento local contém mapeamentos para os shardlets que residem no fragmento.

**Consulta de vários fragmentos**: A capacidade de emitir uma consulta em vários fragmentos, os conjuntos de resultados são retornados usando a semântica UNION ALL (também conhecida como "consulta do tipo fan-out"). Compare com o **roteamento dependente de dados**.

**Multilocatário** e **Locatário único**: Isso mostra um banco de dados de locatário único e um banco de dados de vários locatários:

![Bancos de dados de único locatário e multilocatário](./media/sql-database-elastic-scale-glossary/multi-single-simple.png)

Esta é uma representação de bancos de dados de único locatário e multilocatário **fragmentados** . 

![Bancos de dados de único locatário e multilocatário](./media/sql-database-elastic-scale-glossary/shards-single-multi.png)

**Mapas de fragmentos de intervalo**: Um mapa de fragmentos no qual a estratégia de distribuição de fragmentos é baseada em vários intervalos de valores contíguos. 

**Tabelas de referência**: Tabelas que não são fragmentadas, mas replicadas nos fragmentos. Por exemplo, códigos postais podem ser armazenados em uma tabela de referência. 

**Fragmento**: Um banco de dados SQL do Azure que armazena dados de um conjunto de dados fragmentados. 

**Elasticidade de fragmento**: A capacidade de executar **dimensionamento horizontal** e **dimensionamento vertical**.

**Tabelas fragmentadas**: Tabelas que são fragmentadas, ou seja, cujos dados são distribuídos entre fragmentos com base em seus valores de chave de fragmentação. 

**Chave de fragmentação**: Um valor de coluna que determina como os dados são distribuídos entre os fragmentos. O tipo do valor pode ser um dos seguintes: **int**, **bigint**, **varbinary** ou **uniqueidentifier**. 

**Conjunto de fragmentos**: A coleção de fragmentos que são atribuídos ao mesmo mapa de fragmentos no gerenciador de mapas de fragmentos.  

**Shardlet**: Todos os dados associados a um único valor de uma chave de fragmentação em um fragmento. Um shardlet é a menor unidade de movimentação de dados possível ao redistribuir tabelas fragmentadas. 

**Mapa de fragmentos**: O conjunto de mapeamentos entre chaves de fragmentação e seus respectivos fragmentos.

**Gerenciador de mapa de fragmentos**: Um objeto de gerenciamento e um repositório de dados que contém os mapas de fragmentos, locais do fragmento e mapeamentos para um ou mais conjuntos de fragmentos.

![Mapeamentos][2]

## <a name="verbs"></a>Verbos
**Dimensionamento horizontal**: O ato de escalar horizontal (ou verticalmente) uma coleção de fragmentos, adicionando ou removendo fragmentos de um mapa de fragmentos, conforme mostrado abaixo.

![Dimensionamento horizontal e vertical][3]

**Mesclar**: O ato de mover shardlets de dois fragmentos para um e atualizar o mapa de fragmentos de adequadamente.

**Movimentação de shardlet**: O ato de mover um único shardlet para um fragmento diferente. 

**Fragmentar**: O ato de particionar horizontalmente dados de estrutura idêntica em vários bancos de dados com base em uma chave de fragmentação.

**Dividir**: O ato de movimentar vários shardlets de um fragmento para outro (normalmente novo) fragmento. Uma chave de fragmentação é fornecida pelo usuário como o ponto de divisão.

**Dimensionamento vertical**: O ato de escalar verticalmente (ou reduzir verticalmente) o tamanho da computação de um fragmento individual. Por exemplo, alterar um fragmento de Standard para Premium (o que resulta em mais recursos de computação). 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png

