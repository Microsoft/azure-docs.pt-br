---
title: Início Rápido – Use o Spring Data Azure Cosmos DB v3 a fim de criar um banco de dados de documentos usando o Azure Cosmos DB
description: Este início rápido apresenta um exemplo de código do Spring Data Azure Cosmos DB v3 que pode ser usado para se conectar à API do SQL do Azure Cosmos DB e consultá-la
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 10/06/2020
ms.author: anfeldma
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: f31eb0fa6dbb881f7a09b21b9dd4842fdfd291f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93090283"
---
# <a name="quickstart-build-a-spring-data-azure-cosmos-db-v3-app-to-manage-azure-cosmos-db-sql-api-data"></a>Início Rápido: criar um de aplicativo do Spring Data Azure Cosmos DB v3 a fim de gerenciar os dados da API do SQL do Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [SDK do Java v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Neste início rápido, você criará e gerenciará uma conta da API do SQL do Azure Cosmos DB no portal do Azure e usando um aplicativo do Spring Data Azure Cosmos DB v3 clonado do GitHub. Primeiro, crie uma conta da API do SQL do Azure Cosmos DB usando o portal do Azure, depois crie um aplicativo Spring Boot usando o conector do Spring Data Azure Cosmos DB v3 e, por fim, adicione recursos à conta do Cosmos DB usando o aplicativo do Spring Boot. O Azure Cosmos DB é um serviço de banco de dados multimodelo que permite criar e consultar rapidamente bancos de dados de documentos, tabelas, pares chave-valor e grafo com funcionalidades de escala horizontal e distribuição global.

> [!IMPORTANT]  
> Estas notas sobre a versão são para a versão 3 do Spring Data Azure Cosmos DB. Você pode encontrar as [notas sobre a versão 2 aqui](sql-api-sdk-java-spring-v2.md). 
>
> O Spring Data Azure Cosmos DB é compatível apenas com a API do SQL.
>
> Confira estes artigos para obter informações sobre o Spring Data em outras APIs do Azure Cosmos DB:
> * [Spring Data para Apache Cassandra com Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Spring Data MongoDB com Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring Data Gremlin com Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie um gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Ou então [experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma assinatura do Azure. Você também pode usar o [Emulador do Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) com um URI de `https://localhost:8081` e a chave `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`.
- [JDK (Java Development Kit) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Aponte a variável de ambiente `JAVA_HOME` para a pasta em que o JDK está instalado.
- Um [arquivo binário do Maven](https://maven.apache.org/download.cgi). No Ubuntu, execute `apt-get install maven` para instalar o Maven.
- [Git](https://www.git-scm.com/downloads). No Ubuntu, execute `sudo apt-get install git` para instalar o Git.

## <a name="introductory-notes"></a>Notas introdutórias

*A estrutura de uma conta do Cosmos DB.* Independentemente da API ou da linguagem de programação, uma *conta* do Cosmos DB contém zero ou mais *bancos de dados*, um *DB* (banco de dados) contém zero ou mais *contêineres* e um *contêiner* contém zero ou mais itens, conforme mostrado no diagrama abaixo:

:::image type="content" source="./media/account-databases-containers-items/cosmos-entities.png" alt-text="Entidades de conta do Azure Cosmos" border="false":::

Você pode ler mais sobre bancos de dados, contêineres e itens [aqui.](account-databases-containers-items.md) Algumas propriedades importantes são definidas no nível do contêiner, entre elas a *taxa de transferência provisionada* e a *chave de partição*. 

A taxa de transferência provisionada é medida em *RUs* (Unidades de Solicitação) que têm um preço monetário e são um fator determinante substancial no custo operacional da conta. A taxa de transferência provisionada pode ser selecionada na granularidade por contêiner ou na granularidade por banco de dados. No entanto, há preferência pela especificação da taxa de transferência no nível de contêiner normalmente. Você pode ler mais sobre o provisionamento da taxa de transferência [aqui.](set-throughput.md)

À medida que itens são inseridos em um contêiner do Cosmos DB, o banco de dados cresce horizontalmente adicionando mais armazenamento e computação para lidar com solicitações. As capacidades de armazenamento e computação são adicionadas em unidades discretas conhecidas como *partições* e você deve escolher um campo em seus documentos para ser a chave de partição que mapeia cada documento para uma partição. A maneira como as partições são gerenciadas é que cada participação recebe uma fatia aproximadamente igual do intervalo dos valores de chave de partição; portanto, é recomendável escolher uma chave de partição relativamente aleatória ou distribuída uniformemente. Caso contrário, algumas partições terão substancialmente mais solicitações (*partição a quente*), enquanto outras partições terão substancialmente menos solicitações (*partição a frio*), o que deve ser evitado. Você pode saber mais sobre particionamento [aqui](partitioning-overview.md).

## <a name="create-a-database-account"></a>Criar uma conta de banco de dados

Antes de criar um banco de dados de documentos, você precisa criar uma conta de API do SQL com o Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Adicionar um contêiner

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Adicionar dados de exemplo

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Consultar seus dados

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Agora, vamos trabalhar com o código. Vamos clonar um aplicativo de API do SQL do GitHub, definir a cadeia de conexão e executá-lo. Você verá como é fácil trabalhar usando dados de forma programática. 

Execute o comando a seguir para clonar o repositório de exemplo. Este comando cria uma cópia do aplicativo de exemplo no seu computador.

```bash
git clone https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started.git
```

## <a name="review-the-code"></a>Examine o código

Esta etapa é opcional. Se você estiver interessado em aprender como os recursos de banco de dados são criados no código, poderá examinar os snippets de código a seguir. Caso contrário, você poderá seguir para [Executar o aplicativo](#run-the-app). 

### <a name="application-configuration-file"></a>Arquivo de configuração de aplicativo

Aqui, demonstramos como o Spring Boot e o Spring Data aprimoram a experiência do usuário – o processo de estabelecer um cliente Cosmos e se conectar a recursos do Cosmos agora é configuração em vez de código. Na inicialização do aplicativo, o Spring Boot lida com todo esse texto clichê usando as configurações em **application.properties**:

```xml
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

Depois de criar uma conta, um banco de dados e um contêiner no Azure Cosmos DB, basta preencher os espaços em branco no arquivo de configuração e o Spring Boot/Spring Data fará automaticamente o seguinte: (1) criará uma instância de `CosmosClient` do SDK do Java subjacente com o URI e a chave e (2) conectar-se-á ao banco de dados e ao contêiner. Está tudo configurado, **não precisa mais de código para gerenciamento de recursos.**

### <a name="java-source"></a>Código-fonte Java

O valor agregado pelo Spring Data também vem da sua interface simples, limpa, padronizada e independente de plataforma para operação em armazenamentos de dados. Explorando o exemplo do Spring Data GitHub vinculado acima, abaixo há exemplos de CRUD e de consultas para manipulação de documentos do Azure Cosmos DB com o Spring Data Azure Cosmos DB.

* Criação e atualizações de item usando o método `save`.

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Create)]
   
* Leituras de pontos usando o método de consulta derivada definido no repositório. O `findByIdAndLastName` executa leituras de pontos para `UserRepository`. Os campos mencionados no nome do método fazem com que o Spring Data execute um ponto de leitura definido pelos campos `id` e `lastName`:

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Read)]

* Exclusões de itens usando `deleteAll`:

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Delete)]

* Consulta derivada com base no nome do método do repositório. O Spring Data implementa o método `UserRepository` `findByFirstName` como uma consulta SQL do SDK do Java no campo `firstName` (não foi possível implementar essa consulta como uma leitura de ponto):

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Query)]

## <a name="run-the-app"></a>Executar o aplicativo

Agora volte ao portal do Azure para obter as informações de cadeia de conexão e iniciar o aplicativo com as informações de ponto de extremidade. Isso permite que seu aplicativo se comunique com o banco de dados hospedado.

1. Na janela do terminal do Git, execute `cd` na pasta do código de exemplo.

    ```bash
    cd azure-spring-data-cosmos-java-sql-api-getting-started/azure-spring-data-cosmos-java-getting-started/
    ```

2. Na janela do terminal do Git, use o comando a seguir para instalar os pacotes necessários do Spring Data Azure Cosmos DB.

    ```bash
    mvn clean package
    ```

3. Na janela do terminal do Git, use o seguinte comando para iniciar o aplicativo do Spring Data Azure Cosmos DB:

    ```bash
    mvn spring-boot:run
    ```
    
4. O aplicativo carrega **application.properties** e conecta os recursos à conta do Azure Cosmos DB.
5. O aplicativo executará as operações de ponto do CRUD descritas acima.
6. O aplicativo executará uma consulta derivada.
7. O aplicativo não exclui os recursos. Volte para o portal a fim de [limpar os recursos](#clean-up-resources) da sua conta se quiser evitar incorrer em encargos.

## <a name="review-slas-in-the-azure-portal"></a>Examinar SLAs no Portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a criar uma conta da API do SQL do Azure Cosmos DB, criar um banco de dados de documentos e um contêiner usando o Data Explorer e executar um aplicativo do Spring Data para fazer a mesma coisa de maneira programática. Agora, é possível importar outros dados para sua conta do Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importar dados no Azure Cosmos DB](import-data.md)