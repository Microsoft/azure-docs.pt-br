---
title: Usar o feed de alterações para criar um exemplo de aplicativo do SDK v4 do Java de ponta a ponta do Azure Cosmos DB
description: Este guia descreve um aplicativo Java simples de API do SQL que insere documentos em um contêiner do Azure Cosmos DB, mantendo uma exibição materializada do contêiner usando o feed de alterações.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 06/11/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 765fd3afc7fe688d3e6b0e3394e7dc8c39af69b3
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096845"
---
# <a name="how-to-create-a-java-application-that-uses-azure-cosmos-db-sql-api-and-change-feed-processor"></a>Como criar um aplicativo Java que usa a API do SQL do Azure Cosmos DB e o processador do feed de alterações
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este guia de instruções descreve um aplicativo Java simples que usa a API do SQL do Azure Cosmos DB para inserir documentos em um contêiner de Azure Cosmos DB, mantendo uma exibição materializada do contêiner usando o feed de alterações e o processador do feed de alterações. O aplicativo Java se comunica com a API do SQL do Azure Cosmos DB usando o SDK v4 do Java do Azure Cosmos DB.

> [!IMPORTANT]  
> Este tutorial é somente para o SDK do Java v4 do Azure Cosmos DB. Confira as [Notas sobre a versão](sql-api-sdk-java-v4.md) do SDK do Java v4 do Azure Cosmos DB, o [repositório do Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos), as [dicas de desempenho](performance-tips-java-sdk-v4-sql.md) do SDK do Java v4 do Azure Cosmos DB e o [Guia de solução de problemas](troubleshoot-java-sdk-v4-sql.md) do SDK do Java v4 do Azure Cosmos DB para obter mais informações. Se você estiver usando uma versão mais antiga do que a v4, confira o guia [Migrar para o SDK do Java v4 do Azure Cosmos DB](migrate-java-v4-sdk.md) para obter ajuda na atualização para a v4.
>

## <a name="prerequisites"></a>Pré-requisitos

* O URI e a chave da sua conta do Azure Cosmos DB

* Maven

* Java 8

## <a name="background"></a>Segundo plano

O feed de alterações do Azure Cosmos DB fornece uma interface controlada por evento para disparar ações em resposta à inserção de documentos. Há muitos usos para isso. Por exemplo, em aplicativos que executam muitas operações de leitura e gravação, o uso do feed de alterações é criar uma **exibição materializada** em tempo real de um contêiner, pois ele está ingerindo documentos. O contêiner de exibição materializada armazenará os mesmos dados, mas particionados para leituras eficientes, tornando o aplicativo de leitura e gravação eficiente.

O trabalho de gerenciar eventos do feed de alterações é amplamente realizado pela biblioteca do processador do feed de alterações interna do SDK. Essa biblioteca é eficiente o suficiente para distribuir eventos do feed de alterações entre vários trabalhos, se desejado. Basta fornecer um retorno de chamada à biblioteca do feed de alterações.

Este exemplo simples demonstra a biblioteca do processador do feed de alterações com um só trabalho criando e excluindo documentos de uma exibição materializada.

## <a name="setup"></a>Instalação

Caso ainda não tenha feito isso, clone o repositório de exemplo do aplicativo:

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example.git
```

Abra um terminal no diretório do repositório. Crie o aplicativo executando

```bash
mvn clean package
```

## <a name="walkthrough"></a>Passo a passo

1. Como uma primeira verificação, você deve ter uma conta do Azure Cosmos DB. Abra o **portal do Azure** no navegador, acesse a sua conta do Azure Cosmos DB e, no painel esquerdo, navegue até **Data Explorer** .

   :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_account_empty.JPG" alt-text="Conta do Azure Cosmos DB":::

1. Execute o aplicativo no terminal usando o seguinte comando:

    ```bash
    mvn exec:java -Dexec.mainClass="com.azure.cosmos.workedappexample.SampleGroceryStore" -DACCOUNT_HOST="your-account-uri" -DACCOUNT_KEY="your-account-key" -Dexec.cleanupDaemonThreads=false
    ```

1. Pressione ENTER quando vir

    ```bash
    Press enter to create the grocery store inventory system...
    ```

    e, em seguida, volte ao portal do Azure Data Explorer no navegador. Você verá que um banco de dados **GroceryStoreDatabase** foi adicionado com três contêineres vazios: 

    * **InventoryContainer** : o registro de inventário do nosso supermercado de exemplo, particionado no item ```id```, que é um UUID.
    * **InventoryContainer-pktype** : uma exibição materializada do registro de inventário, otimizada para consultas no item ```type```
    * **InventoryContainer-concessões** : um contêiner de concessões é sempre necessário para o feed de alterações; as concessões acompanham o progresso do aplicativo na leitura do feed de alterações.

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_account_resources_lease_empty.JPG" alt-text="Conta do Azure Cosmos DB":::

1. No terminal, agora você deverá ver um aviso

    ```bash
    Press enter to start creating the materialized view...
    ```

    Pressione ENTER. Agora, o seguinte bloco de código executará e inicializará o processador do feed de alterações em outro thread: 

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>API assíncrona do SDK v4 do Java (Maven com.azure::azure-cosmos)

    [!code-java[](~/azure-cosmos-java-sql-app-example/src/main/java/com/azure/cosmos/workedappexample/SampleGroceryStore.java?name=InitializeCFP)]

    ```"SampleHost_1"``` é o nome do trabalho do processador do feed de alterações. ```changeFeedProcessorInstance.start()``` é o que realmente inicia o processador do feed de alterações.

    Volte ao portal do Azure Data Explorer no navegador. No contêiner **InventoryContainer-concessões** , clique em **items** para ver o conteúdo. Você verá que o processador do feed de alterações preencheu o contêiner de concessão, ou seja, o processador atribuiu ao trabalho ```SampleHost_1``` uma concessão em algumas partições do **InventoryContainer** .

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_leases.JPG" alt-text="Conta do Azure Cosmos DB":::

1. Pressione ENTER novamente no terminal. Isso disparará dez documentos a serem inseridos no **InventoryContainer** . Cada inserção de documento é exibida no feed de alterações como JSON; o seguinte código de retorno de chamada processa esses eventos espelhando os documentos JSON em uma exibição materializada:

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>API assíncrona do SDK v4 do Java (Maven com.azure::azure-cosmos)

    [!code-java[](~/azure-cosmos-java-sql-app-example/src/main/java/com/azure/cosmos/workedappexample/SampleGroceryStore.java?name=CFPCallback)]

1. Permita que o código seja executado de 5 a 10 segundos. Em seguida, volte ao portal do Azure Data Explorer e navegue até **InventoryContainer > items** . Você deverá ver que os itens estão sendo inseridos no contêiner de inventário; observe a chave de partição (```id```).

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_items.JPG" alt-text="Conta do Azure Cosmos DB":::

1. Agora, no Data Explorer, navegue até **InventoryContainer-pktype > items** . Essa é a exibição materializada: os itens desse contêiner espelham **InventoryContainer** porque foram inseridos de maneira programática pelo feed de alterações. Anote a chave de partição (```type```). Portanto, essa exibição materializada é otimizada para filtragem de consultas em ```type```, o que será ineficiente em **InventoryContainer** , porque ele é particionado em ```id```.

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_materializedview2.JPG" alt-text="Conta do Azure Cosmos DB":::

1. Vamos excluir um documento de **InventoryContainer** e de **InventoryContainer-pktype** usando apenas uma só chamada ```upsertItem()```. Primeiro, dê uma olhada no portal do Azure Data Explorer. Excluiremos o documento para o qual ```/type == "plums"```; ele é circulado em vermelho abaixo

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_materializedview-emph-todelete.JPG" alt-text="Conta do Azure Cosmos DB":::

    Pressione ENTER novamente para chamar a função ```deleteDocument()``` no código de exemplo. Essa função, mostrada abaixo, faz upsert de uma nova versão do documento com ```/ttl == 5```, que define a TTL (vida útil) do documento como 5 segundos. 
    
    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>API assíncrona do SDK v4 do Java (Maven com.azure::azure-cosmos)

    [!code-java[](~/azure-cosmos-java-sql-app-example/src/main/java/com/azure/cosmos/workedappexample/SampleGroceryStore.java?name=DeleteWithTTL)]

    O feed de alterações do ```feedPollDelay``` é definido como 100 ms; portanto, o feed de alterações responde a essa atualização quase instantaneamente e chama ```updateInventoryTypeMaterializedView()``` mostrado acima. Essa última chamada de função fará upsert do novo documento com uma TTL de 5 segundos em **InventoryContainer-pktype** .

    O efeito é que, após cerca de 5 segundos, o documento vai expirar e ser excluído de ambos os contêineres.

    Esse procedimento é necessário porque o feed de alterações só emite eventos na inserção ou na atualização de item, não na exclusão de item.

1. Pressione ENTER mais uma vez para fechar o programa e limpar os recursos.
