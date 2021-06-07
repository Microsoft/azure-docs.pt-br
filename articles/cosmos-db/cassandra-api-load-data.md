---
title: 'Tutorial: Aplicativo Java para carregar dados de exemplo em uma tabela da API do Cassandra do Azure Cosmos DB'
description: Este tutorial mostra a carregar dados de usuário de exemplo em uma tabela da API do Cassandra do Azure Cosmos DB usando um aplicativo Java.
author: kanshiG
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: ad3dfc8c86d22e58377ebfbdb94b2beb004ded56
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93092459"
---
# <a name="tutorial-load-sample-data-into-a-cassandra-api-table-in-azure-cosmos-db"></a>Tutorial: Carregar dados de exemplo em uma tabela da API do Cassandra do Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Como desenvolvedor, é possível ter aplicativos que usam os pares de chave/valor. Você pode usar a conta de API do Cassandra no Azure Cosmos DB para armazenar e gerenciar dados de chave/valor. Este tutorial mostra a carregar dados de usuário de exemplo em uma tabela em uma conta da API do Cassandra do Azure Cosmos DB usando um aplicativo Java. O aplicativo Java usa o [driver Java](https://github.com/datastax/java-driver) e carrega os dados do usuário, como ID, nome e cidade do usuário. 

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Carregar dados em uma tabela do Cassandra
> * Executar o aplicativo

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Este artigo pertence a um tutorial com várias partes. Antes de começar com este documento, certifique-se [criar a conta, o keyspace e a tabela de API do Cassandra](create-cassandra-api-account-java.md).   

## <a name="load-data-into-the-table"></a>Carregar dados na tabela

Use as etapas a seguir para carregar dados em sua tabela de API do Cassandra:

1. Abra o arquivo "UserRepository.java" na pasta "src\main\java\com\azure\cosmosdb\cassandra" e acrescente o código para inserir os campos user_id, user_name e user_bcity na tabela:

   ```java
   /**
   * Insert a row into user table
   *
   * @param id   user_id
   * @param name user_name
   * @param city user_bcity
   */
   public void insertUser(PreparedStatement statement, int id, String name, String city) {
        BoundStatement boundStatement = new BoundStatement(statement);
        session.execute(boundStatement.bind(id, name, city));
   }

   /**
   * Create a PrepareStatement to insert a row to user table
   *
   * @return PreparedStatement
   */
   public PreparedStatement prepareInsertStatement() {
      final String insertStatement = "INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (?,?,?)";
   return session.prepare(insertStatement);
   }
   ```
 
2. Abra o arquivo "UserProfile.java" na pasta "src\main\java\com\azure\cosmosdb\cassandra". Essa classe contém o método principal que chama os métodos createKeyspace e createTable definidos anteriormente. Agora, acrescente o seguinte código para inserir alguns dados de exemplo na tabela de API do Cassandra.

   ```java
   //Insert rows into user table
   PreparedStatement preparedStatement = repository.prepareInsertStatement();
     repository.insertUser(preparedStatement, 1, "JohnH", "Seattle");
     repository.insertUser(preparedStatement, 2, "EricK", "Spokane");
     repository.insertUser(preparedStatement, 3, "MatthewP", "Tacoma");
     repository.insertUser(preparedStatement, 4, "DavidA", "Renton");
     repository.insertUser(preparedStatement, 5, "PeterS", "Everett");
   ```

## <a name="run-the-app"></a>Executar o aplicativo

Abra um prompt de comando ou a janela do terminal e altere o caminho para a pasta onde você criou o projeto. Execute o comando de "mvn clean install" para gerar o arquivo cosmosdb-cassandra-Examples.jar dentro da pasta de destino e executar o aplicativo. 

```bash
cd "cassandra-demo"

mvn clean install

java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
```

Agora você pode abrir o Data Explorer no portal do Azure para confirmar se as informações do usuário foram adicionadas à tabela.
    
## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a carregar dados de exemplo para uma conta de API do Cassandra do Azure Cosmos DB. Prossiga agora para o próximo artigo:

> [!div class="nextstepaction"]
> [Consultar dados da conta de API do Cassandra](cassandra-api-query-data.md)
