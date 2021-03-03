---
title: Usar transações de vários documentos na API Azure Cosmos DB para MongoDB
description: Saiba como criar um aplicativo de shell Mongo de exemplo que pode executar uma transação de vários documentos (semântica de tudo ou nada) em uma coleção fixa na API Azure Cosmos DB para MongoDB 4,0.
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.author: gahllevy
ms.openlocfilehash: 4d7dcc829f25b7f1b7c6cb6b1d13a664d301bfe6
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661931"
---
# <a name="use-multi-document-transactions-in-azure-cosmos-db-api-for-mongodb"></a>Usar transações de vários documentos na API Azure Cosmos DB para MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Neste artigo, você criará um aplicativo de shell Mongo que executa uma transação de vários documentos em uma coleção fixa na API Azure Cosmos DB para MongoDB com a versão de servidor 4,0.

## <a name="what-are-multi-document-transactions"></a>O que são transações de vários documentos?

Na API Azure Cosmos DB para MongoDB, as operações em um único documento são atômicas. As transações de vários documentos permitem que os aplicativos executem operações atômicas em vários documentos. Ele oferece a semântica "tudo ou nada" para as operações. Na confirmação, as alterações feitas dentro das transações são persistidas e, se a transação falhar, todas as alterações dentro da transação serão descartadas.

As transações de vários documentos seguem a semântica **Acid** :

* Atomicidade: todas as operações tratadas como uma
* Consistência: os dados confirmados são válidos
* Isolamento: isolado de outras operações
* Durabilidade: os dados da transação são persistidos quando o cliente é informado para

## <a name="requirements"></a>Requisitos

Há suporte para transações de vários documentos em uma coleção não fragmentada na versão 4,0. Não há suporte para transações de vários documentos em coleções ou em coleções fragmentadas.

Todos os drivers que suportam o protocolo de conexão versão 4,0 ou superior oferecerão suporte à API de Azure Cosmos DB para transações de vários documentos do MongoDB.

## <a name="run-multi-document-transactions-in-mongodb-shell"></a>Executar transações de vários documentos no Shell do MongoDB

1. Abra um prompt de comando, vá para o diretório em que o Shell Mongo versão 4,0 e superior está instalado:

   ```powershell
   cd <path_to_mongo_shell_>
   ```

2. Criar um script de shell Mongo *connect_friends.js* e adicionar o conteúdo a seguir

   ```javascript
   // insert data into friends collection
   db.getMongo().getDB("users").friends.insert({name:"Tom"})
   db.getMongo().getDB("users").friends.insert({name:"Mike"})
   // start transaction
   var session = db.getMongo().startSession();
   var friendsCollection = session.getDatabase("users").friends;
   session.startTransaction();
   // operations in transaction
   try {
       friendsCollection.updateOne({ name: "Tom" }, { $set: { friendOf: "Mike" } } );
       friendsCollection.updateOne({ name: "Mike" }, { $set: { friendOf: "Tom" } } );
   } catch (error) {
       // abort transaction on error
       session.abortTransaction();
       throw error;
   }

    // commit transaction
    session.commitTransaction();

    ```

3. Execute o comando a seguir para executar a transação de vários documentos. O host, a porta, o usuário e a chave podem ser encontrados no portal do Azure.

   ```powershell
   mongo "<HOST>:<PORT>" -u "<USER>" -p "KEY" --ssl connect_friends.js
   ```

## <a name="next-steps"></a>Próximas etapas

Explore o que há de novo na [API de Azure Cosmos DB para MongoDB 4,0](mongodb-feature-support-40.md)
