---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 08/22/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c68b56463625a09f9c8341881284706ab8250f46
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608079"
---
As associações do Azure Cosmos DB têm suporte apenas para usar com a API do SQL. Para todas as outras APIs do Azure Cosmos DB, você deve acessar o banco de dados por meio da sua função usando o cliente estático da API, incluindo a [API do Azure Cosmos DB para MongoDB](../articles/cosmos-db/mongodb-introduction.md), a [API do Cassandra](../articles/cosmos-db/cassandra-introduction.md), a [API do Gremlin](../articles/cosmos-db/graph-introduction.md) e a [API de Tabela](../articles/cosmos-db/table-introduction.md).
