---
title: incluir arquivo
description: incluir arquivo
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/22/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: f7495c52e36bdc207145f17ec72eb57b7ebf1784
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "76279279"
---
As associações do Azure Cosmos DB têm suporte apenas para usar com a API do SQL. Para todas as outras APIs do Azure Cosmos DB, você deve acessar o banco de dados por meio da sua função usando o cliente estático da API, incluindo a [API do Azure Cosmos DB para MongoDB](../articles/cosmos-db/mongodb-introduction.md), a [API do Cassandra](../articles/cosmos-db/cassandra-introduction.md), a [API do Gremlin](../articles/cosmos-db/graph-introduction.md) e a [API de Tabela](../articles/cosmos-db/table-introduction.md).
