---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 08/22/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c54aa861a47b11756f05e003e9b944df6c5b0e28
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61342820"
---
As associações do Azure Cosmos DB têm suporte apenas para usar com a API do SQL. Para todas as outras APIs do Azure Cosmos DB, você deve acessar o banco de dados por meio da sua função usando o cliente estático da API, incluindo a [API do Azure Cosmos DB para MongoDB](../articles/cosmos-db/mongodb-introduction.md), a [API do Cassandra](../articles/cosmos-db/cassandra-introduction.md), a [API do Gremlin](../articles/cosmos-db/graph-introduction.md) e a [API de Tabela](../articles/cosmos-db/table-introduction.md).
