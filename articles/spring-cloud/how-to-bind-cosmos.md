---
title: Associar um Azure Cosmos DB ao aplicativo Azure Spring Cloud
description: Aprenda a associar um Azure Cosmos DB ao aplicativo Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/06/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: bf761bded38ffa975610c7a7f217e911704ae3bf
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227935"
---
# <a name="bind-an-azure-cosmos-db-database-to-your-azure-spring-cloud-application"></a>Associar um banco de dados do Azure Cosmos DB ao aplicativo do Azure Spring Cloud

**Este artigo aplica-se a:** ✔️ Java

Em vez de configurar manualmente seus aplicativos Spring Boot, associe automaticamente serviços do Azure selecionados aos seus aplicativos usando o Azure Spring Cloud. Este artigo demonstra como associar seu aplicativo a um banco de dados do Azure Cosmos DB.

Pré-requisitos:

* Uma instância do Azure Spring Cloud implantada. Siga nosso [início rápido sobre implantação por meio da CLI do Azure](spring-cloud-quickstart.md) para obter uma introdução.
* Uma conta do Azure Cosmos DB com um nível mínimo de permissão de colaborador.

## <a name="bind-azure-cosmos-db"></a>Associar o Azure Cosmos DB

O Azure Cosmos DB tem cinco tipos de API diferentes que dão suporte à associação. O seguinte procedimento mostra como usá-los:

1. Crie um banco de dados do Azure Cosmos DB. Veja o início rápido sobre como [criar um banco de dados](../cosmos-db/create-cosmosdb-resources-portal.md) para obter ajuda. 

1. Anote o nome do banco de dados. Neste procedimento, o nome do banco de dados é **testdb**.

1. Adicione uma das dependências a seguir ao arquivo pom.xml do aplicativo do Azure Spring Cloud. Escolha a dependência apropriada para o tipo de API.

    * Tipo de API: Núcleo (SQL)

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
          <version>2.1.6</version>
      </dependency>
      ```

    * Tipo de API: MongoDB

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-mongodb</artifactId>
      </dependency>
      ```

    * Tipo de API: Cassandra

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-cassandra</artifactId>
      </dependency>
      ```

    * Tipo de API: Gremlin (grafo)

      ```xml
      <dependency>
          <groupId>com.microsoft.spring.data.gremlin</groupId>
          <artifactId>spring-data-gremlin</artifactId>
          <version>2.1.7</version>
      </dependency>
      ```

    * Tipo de API: tabela do Azure

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-storage-spring-boot-starter</artifactId>
          <version>2.0.5</version>
      </dependency>
      ```

1. Use `az spring-cloud app update` para atualizar a implantação atual ou `az spring-cloud app deployment create` para criar uma implantação. Esses comandos atualizarão ou criarão o aplicativo com a nova dependência.

1. Acesse sua página de serviços do Azure Spring Cloud no portal do Azure. Acesse o **Painel do Aplicativo** e selecione o aplicativo a ser associado ao Azure Cosmos DB. Esse é o mesmo aplicativo que você atualizou ou implantou na etapa anterior.

1. Selecione **Associação de serviço** e **Criar associação de serviço**. Para preencher o formulário, selecione:
   * O valor de **Tipo de associação** **Azure Cosmos DB**.
   * O tipo de API.
   * O nome do banco de dados.
   * A conta do Azure Cosmos DB.

    > [!NOTE]
    > Se estiver usando o Cassandra, use um espaço de chave para o nome do banco de dados.

1. Reinicie o aplicativo selecionando **Reiniciar** na página do aplicativo.

1. Para garantir que o serviço esteja associado corretamente, selecione o nome da associação e verifique os detalhes. O campo `property` deverá ser semelhante a este exemplo:

    ```
    azure.cosmosdb.uri=https://<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a associar seu aplicativo do Azure Spring Cloud a um banco de dados do Azure Cosmos DB. Para saber mais sobre como associar serviços ao seu aplicativo, confira [Associar a um cache do Cache do Azure para Redis](spring-cloud-howto-bind-redis.md).