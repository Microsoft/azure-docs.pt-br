---
title: Perguntas frequentes sobre a API do Azure Cosmos DB para MongoDB
description: Obtenha respostas para perguntas frequentes sobre a API do Azure Cosmos DB para MongoDB
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: sngun
ms.openlocfilehash: 08899018d03209dab09f61d4dd74feceee03b246
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98018996"
---
# <a name="frequently-asked-questions-about-the-azure-cosmos-dbs-api-for-mongodb"></a>Perguntas frequentes sobre a API do Azure Cosmos DB para MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

A API do Azure Cosmos DB para MongoDB é uma camada de compatibilidade de protocolo Wire que permite que os aplicativos se comuniquem de forma fácil e transparente com o mecanismo de banco de dados nativo do Azure Cosmos usando SDKs e drivers existentes com suporte da Comunidade para o MongoDB. Os desenvolvedores agora podem usar cadeias e habilidades existentes do MongoDB para criar aplicativos que tiram proveito do Azure Cosmos DB. Os desenvolvedores se beneficiam dos recursos exclusivos do Azure Cosmos DB, que incluem a distribuição global com replicação de gravação de várias regiões, indexação automática, manutenção de backup, SLAs (contratos de nível de serviço) com suporte financeiro etc.

## <a name="how-do-i-connect-to-my-database"></a>Como faço para me conectar com o meu banco de dados?

A maneira mais rápida de se conectar a um banco de dados do Cosmos com a API do Azure Cosmos DB para MongoDB é acessar o [portal do Azure](https://portal.azure.com). Vá para sua conta e, em seguida, no menu de navegação à esquerda, clique em **Início Rápido**. O início rápido é a melhor maneira de obter trechos de código para se conectar ao banco de dados.

O Azure Cosmos DB impõe padrões e requisitos de segurança rígidos. Azure Cosmos DB contas exigem autenticação e comunicação segura via TLS, portanto, certifique-se de usar o TLSv 1.2.

Para saber mais, veja [Conectar-se ao banco de dados do Cosmos com a API do Azure Cosmos DB para MongoDB](connect-mongodb-account.md).

## <a name="error-codes-while-using-azure-cosmos-dbs-api-for-mongodb"></a>Códigos de erro ao usar a API do Azure Cosmos DB para MongoDB?

Junto com os códigos de erro comuns do MongoDB, a API do Azure Cosmos DB para MongoDB tem seus próprios códigos de erro específicos. Eles podem ser encontrados no [Guia de solução de problemas](mongodb-troubleshoot.md).

## <a name="supported-drivers"></a>Drivers com suporte

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>O driver Simba para MongoDB tem suporte para usar a API do Azure Cosmos DB para MongoDB?

Sim, você pode usar o driver ODBC do Mongo da Simba com a API do Azure Cosmos DB para MongoDB

## <a name="next-steps"></a>Próximas etapas

* [Compilar um aplicativo Web .NET usando a API do Azure Cosmos DB para MongoDB](create-mongodb-dotnet.md)
* [Criar um aplicativo de console com Java e a API do MongoDB no Azure Cosmos DB](create-mongodb-java.md)
