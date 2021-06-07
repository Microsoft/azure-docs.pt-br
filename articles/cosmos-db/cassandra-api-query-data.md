---
title: 'Tutorial: Consultar dados de uma conta da API do Cassandra no Azure Cosmos DB'
description: Este tutorial mostra como consultar dados de usuário de uma conta da API do Cassandra do Azure Cosmos DB usando um aplicativo Java.
ms.service: cosmos-db
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: 693984ceaef1a71ebfbe4122c18a45a563c9c101
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93100415"
---
# <a name="tutorial-query-data-from-a-cassandra-api-account-in-azure-cosmos-db"></a>Tutorial: Consultar dados de uma conta da API do Cassandra no Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Como desenvolvedor, é possível ter aplicativos que usam os pares de chave/valor. Você pode usar uma conta da API do Cassandra do Azure Cosmos DB para armazenar e consultar dados de chave/valor. Este tutorial mostra como consultar dados de usuário de uma conta da API do Cassandra do Azure Cosmos DB usando um aplicativo Java. O aplicativo Java usa o [driver Java](https://github.com/datastax/java-driver) e consulta dados do usuário, como ID, nome e cidade do usuário. 

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Consultar dados de uma tabela do Cassandra
> * Executar o aplicativo

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Este artigo pertence a um tutorial com várias partes. Antes de começar, certifique-se de concluir as etapas anteriores para criar a conta da API do Cassandra, keyspace, tabela e [carregar dados de exemplo na tabela](cassandra-api-load-data.md). 

## <a name="query-data"></a>Consultar dados

Use as etapas a seguir para consultar dados de sua conta de API do Cassandra:

1. Abra o arquivo `UserRepository.java` na pasta `src\main\java\com\azure\cosmosdb\cassandra`. Acrescente o seguinte bloco de código. Esse código fornece três métodos: 

   * Para consultar todos os usuários no banco de dados
   * Para consultar um usuário específico, filtrado por ID de usuário
   * Para excluir uma tabela

   ```java
   /**
   * Select all rows from user table
   */
   public void selectAllUsers() {

     final String query = "SELECT * FROM uprofile.user";
     List<Row> rows = session.execute(query).all();

     for (Row row : rows) {
        LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
     }
   }

   /**
   * Select a row from user table
   *
   * @param id user_id
   */
   public void selectUser(int id) {
      final String query = "SELECT * FROM uprofile.user where user_id = 3";
      Row row = session.execute(query).one();

      LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
   }

   /**
   * Delete user table.
   */
   public void deleteTable() {
     final String query = "DROP TABLE IF EXISTS uprofile.user";
     session.execute(query);
   }
   ```

2. Abra o arquivo `UserProfile.java` na pasta `src\main\java\com\azure\cosmosdb\cassandra`. Essa classe contém o método principal que chama createKeyspace e createTable. Insira os métodos de dados definidos anteriormente. Agora, acrescente o seguinte código que consulta todos os usuários ou um usuário específico:

   ```java
   LOGGER.info("Select all users");
   repository.selectAllUsers();

   LOGGER.info("Select a user by id (3)");
   repository.selectUser(3);

   LOGGER.info("Delete the users profile table");
   repository.deleteTable();
   ```

## <a name="run-the-java-app"></a>Executar o aplicativo Java
1. Abra um prompt de comando ou uma janela de terminal. Cole o seguinte bloco de código. 

   Esse código altera o diretório (cd) para o caminho da pasta onde você criou o projeto. Em seguida, ele executa o comando `mvn clean install` para gerar o arquivo `cosmosdb-cassandra-examples.jar` dentro da pasta de destino. Por fim, ele executa o aplicativo Java.

   ```bash
   cd "cassandra-demo"
   
   mvn clean install
   
   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
   ```

2. Agora, no portal do Azure, abra o **Data Explorer** e confirme se a tabela de usuário foi excluída.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não forem necessários, você pode excluir o grupo de recursos, a conta do Azure Cosmos e todos os recursos relacionados. Para fazer isso, escolha o grupo de recursos da máquina virtual, escolha **Excluir** e, em seguida, confirme o nome do grupo de recursos que será excluído.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a consultar dados de uma conta da API do Cassandra do Azure Cosmos DB. Prossiga agora para o próximo artigo:

> [!div class="nextstepaction"]
> [Migrar dados para a conta de API do Cassandra](cassandra-import-data.md)


