---
title: Migrar do CouchBase para a API do SQL do Azure Cosmos DB
description: Diretrizes passo a passo para migrar do CouchBase para a API do SQL do Azure Cosmos DB
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 02/11/2020
ms.author: mansha
author: manishmsfte
ms.custom: devx-track-java
ms.openlocfilehash: a15c6b5919f428b28daab86fea9c3b6473d19162
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97606191"
---
# <a name="migrate-from-couchbase-to-azure-cosmos-db-sql-api"></a>Migrar do CouchBase para a API do SQL do Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

O Azure Cosmos DB é um banco de dados escalonável, distribuído globalmente e totalmente gerenciado. Ele fornece acesso garantido de baixa latência aos seus dados. Para saber mais sobre a API do Azure Cosmos DB, veja o artigo de [visão geral](introduction.md). Este artigo fornece instruções para migrar aplicativos Java que estão conectados ao Couchbase para uma conta da API do SQL no Azure Cosmos DB.

## <a name="differences-in-nomenclature"></a>Diferenças na nomenclatura

A seguir estão os principais recursos que funcionam de maneira diferente no Azure Cosmos DB em comparação com o Couchbase:

| Couchbase | Azure Cosmos DB |
|--|--|
| Servidor do Couchbase | Conta |
| Bucket | Banco de dados |
| Bucket | Contêiner/coleção |
| Documento JSON | Item/documento |

## <a name="key-differences"></a>Principais diferenças

* O Azure Cosmos DB tem um campo “ID” dentro do documento, enquanto o Couchbase tem a ID como parte do bucket. O campo “ID” é exclusivo na partição.

* O Azure Cosmos DB é dimensionado usando a técnica de particionamento ou fragmentação. Isso significa que ele divide os dados em vários fragmentos/partições. Essas partições/fragmentos são criados com base na propriedade da chave de partição que você fornece. Você pode selecionar a chave de partição para otimizar as operações de leitura e gravação ou também a leitura/gravação otimizada. Para saber mais, consulte o artigo [particionamento](./partitioning-overview.md).

* No Azure Cosmos DB, não é necessário que a hierarquia de nível superior denote a coleção porque o nome da coleção já existe. Esse recurso torna a estrutura JSON muito mais simples. Veja a seguir um exemplo que mostra as diferenças no modelo de dados entre o Couchbase e o Azure Cosmos DB:

   **Couchbase**: ID do documento =  “99FF4444”

    ```json
    {
      "TravelDocument":
      {
       "Country":"India",
      "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    }
   ```

   **Azure Cosmos DB**: Consulte a “ID” dentro do documento, conforme mostrado abaixo

    ```json
    {
      "id" : "99FF4444",
    
      "Country":"India",
       "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    
    ```
         
## <a name="java-sdk-support"></a>Suporte ao SDK do Java

O Azure Cosmos DB tem os SDKs a seguir para dar suporte a estruturas de Java diferentes:

* SDK assíncrono
* SDK do Spring Boot

As seções a seguir descrevem quando usar cada um desses SDKs. Considere um exemplo em que temos três tipos de cargas de trabalho:

## <a name="couchbase-as-document-repository--spring-data-based-custom-queries"></a>Couchbase como repositório de documentos e consultas personalizadas baseadas em dados da primavera

Se a carga de trabalho que você está migrando for baseada no SDK baseado no Spring Boot, você poderá usar as seguintes etapas:

1. Adicione o pai ao arquivo POM.xml:

   ```java
   <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-parent</artifactId>
      <version>2.1.5.RELEASE</version>
      <relativePath/>
   </parent>
   ```

1. Adicione propriedades ao arquivo POM.xml:

   ```java
   <azure.version>2.1.6</azure.version>
   ```

1. Adicione dependências ao arquivo POM.xml:

   ```java
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
       <version>2.1.6</version>
   </dependency>
   ```

1. Adicione propriedades do aplicativo em recursos e especifique o seguinte. Certifique-se de substituir os parâmetros de URL, chave e nome do banco de dados:

   ```java
      azure.cosmosdb.uri=<your-cosmosDB-URL>
      azure.cosmosdb.key=<your-cosmosDB-key>
      azure.cosmosdb.database=<your-cosmosDB-dbName>
   ```

1. Defina o nome da coleção no modelo. Você também pode especificar mais anotações. Por exemplo, ID, chave de partição para denotá-los explicitamente:

   ```java
   @Document(collection = "mycollection")
       public class User {
           @id
           private String id;
           private String firstName;
           @PartitionKey
           private String lastName;
       }
   ```

A seguir estão os snippets de código para operações CRUD:

### <a name="insert-and-update-operations"></a>Operações de inserção e atualização

Onde *_repo* é o objeto do repositório e *doc* é o objeto da classe POJO. Você pode usar `.save` para inserir ou upsert (se o documento com a ID especificada for encontrado). O snippet de código a seguir mostra como inserir ou atualizar um objeto doc:

```_repo.save(doc);```

### <a name="delete-operation"></a>Operação de Exclusão

Considere o seguinte snippet de código, em que o objeto doc terá a ID e a chave de partição obrigatórias para localizar e excluir o objeto:

```_repo.delete(doc);```

### <a name="read-operation"></a>Operação de Leitura

Você pode ler o documento especificando ou não a chave de partição. Se você não especificar a chave de partição, ela será tratada como uma consulta entre partições. Considere os códigos de exemplo a seguir: o primeiro executará a operação usando o campo ID e chave de partição. O segundo exemplo usa um campo comum e sem especificar o campo da chave da partição.

* ```_repo.findByIdAndName(objDoc.getId(),objDoc.getName());```
* ```_repo.findAllByStatus(objDoc.getStatus());```

É isso! Agora você pode usar seu aplicativo com o Azure Cosmos DB. O código de exemplo completo para o exemplo descrito neste doc está disponível repositório [CouchbaseToCosmosDB-SpringCosmos](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/main/SpringCosmos) do GitHub.

## <a name="couchbase-as-a-document-repository--using-n1ql-queries"></a>Couchbase como repositório de documentos e usando consultas N1QL

As consultas N1QL são a maneira de definir consultas no Couchbase.

|Consulta N1QL | Consulta do Azure CosmosDB|
|-------------------|-------------------|
|SELECT META(`TravelDocument`).id AS id, `TravelDocument`.* FROM `TravelDocument` WHERE `_type` = "com.xx.xx.xx.xxx.xxx.xxxx " and country = 'India’ and ANY m in Visas SATISFIES m.type == 'Multi-Entry' and m.Country IN ['India', Bhutan’] ORDER BY ` Validity` DESC LIMIT 25 OFFSET 0 | SELECT c.id,c FROM c JOIN m in  c.country=’India’ WHERE c._type = " com.xx.xx.xx.xxx.xxx.xxxx" and c.country = 'India' and m.type = 'Multi-Entry' and m.Country IN ('India', 'Bhutan') ORDER BY c.Validity DESC OFFSET 0 LIMIT 25 |

Você pode observar as seguintes alterações nas consultas N1QL:

* Você não precisa usar a palavra-chave META ou consultar o documento de primeiro nível. Em vez disso, você pode criar sua própria referência ao contêiner. Neste exemplo, consideramos como “c” (pode ser qualquer coisa). Essa referência é usada como um prefixo para todos os campos de primeiro nível. Por exemplo, c.id, c.country etc.

* Em vez de “ANY”, agora você pode fazer uma junção no subdocumento e consultá-lo com um alias dedicado, como “m”. Depois de criar o alias para um subdocumento, você precisará usar o alias. Por exemplo, m.Country.

* A sequência de OFFSET é diferente na consulta do Azure Cosmos DB, primeiro você precisa especificar OFFSET e LIMIT. É recomendável não usar o SDK dos dados da primavera se você estiver usando o máximo de consultas personalizadas definidas, pois pode haver sobrecarga desnecessária no lado do cliente ao passar a consulta para o Azure Cosmos DB. Em vez disso, temos um SDK do Java assíncrono direto, que pode ser utilizado com muita eficiência nesse caso.

### <a name="read-operation"></a>Operação de leitura

Use o SDK do Java assíncrono com as seguintes etapas:

1. Configure a seguinte dependência no arquivo POM.xml:

   ```java
   <!-- https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb -->
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmos</artifactId>
       <version>3.0.0</version>
   </dependency>
   ```

1. Crie um objeto de conexão para o Azure Cosmos DB usando o método `ConnectionBuilder`, como mostrado no exemplo a seguir. Certifique-se de colocar essa declaração no bean de modo que o código a seguir seja executado apenas uma vez:

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
    
   if(client==null)
      client= CosmosClient.builder()
         .endpoint(Host)//(Host, PrimaryKey, dbName, collName).Builder()
          .connectionPolicy(cp)
          .key(PrimaryKey)
          .consistencyLevel(ConsistencyLevel.EVENTUAL)
          .build();
   
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

1. Para executar a consulta, você precisa executar o seguinte snippet de código:

   ```java
   Flux<FeedResponse<CosmosItemProperties>> objFlux= container.queryItems(query, fo);
   ```

Agora, com a ajuda do método acima, você pode passar várias consultas e executá-las sem complicações. Caso você tenha a necessidade de executar uma consulta grande, que pode ser dividida em várias consultas, tente o seguinte snippet de código em vez do anterior:

```java
for(SqlQuerySpec query:queries)
{
   objFlux= container.queryItems(query, fo);
   objFlux .publishOn(Schedulers.elastic())
         .subscribe(feedResponse->
            {
               if(feedResponse.results().size()>0)
               {
                  _docs.addAll(feedResponse.results());
               }
            
            },
            Throwable::printStackTrace,latch::countDown);
   lstFlux.add(objFlux);
}
                  
      Flux.merge(lstFlux);
      latch.await();
}
```

Você pode executar consultas em paralelo com o código anterior e aumentar as execuções distribuídas para otimização. Além disso, você também pode executar as operações de inserção e atualização:

### <a name="insert-operation"></a>Operação de Inserção

Para inserir o documento, execute o seguinte código:

```java
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

Em seguida, assine Mono como:

```java
CountDownLatch latch=new CountDownLatch(1);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
           if(resourceResponse.statusCode()!=successStatus)
              {
                 throw new RuntimeException(resourceResponse.toString());
              }
           },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="upsert-operation"></a>Operação upsert

A operação upsert exige que você especifique o documento que precisa ser atualizado. Para buscar o documento completo, você pode usar o snippet mencionado em operação de leitura de cabeçalho e modificar os campos obrigatórios. O seguinte snippet de código upserts o documento:

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
Em seguida, assine mono. Consulte o snippet de assinatura do mono na operação de inserção.

### <a name="delete-operation"></a>Operação de exclusão

O snippet a seguir fará a operação de exclusão:

```java
CosmosItem objItem= container.getItem(doc.Id, doc.Tenant);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Em seguida, assine mono, consulte o snippet de assinatura mono na operação de inserção. O código de exemplo completo está disponível no repositório [CouchbaseToCosmosDB-AsyncInSpring](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/main/AsyncInSpring) do GitHub.

## <a name="couchbase-as-a-keyvalue-pair"></a>Couchbase como um par de chave/valor

Esse é um tipo simples de carga de trabalho no qual você pode executar pesquisas em vez de consultas. Use as seguintes etapas para pares de chave/valor:

1. Considere ter “/ID” como chave primária, o que garantirá que você possa executar a operação de pesquisa diretamente na partição específica. Crie uma coleção e especifique “/ID” como chave de partição.

1. Desative completamente a indexação. Como você executará operações de pesquisa, não há sentido em sobrecarregar a indexação. Para desativar a indexação, entre no portal do Azure, vá para Conta do Azure Cosmos DB. Abra o **Data Explorer**, selecione seu **Banco de Dados** e o **Contêiner**. Abra a guia **Escala e configurações** e selecione a **Política de indexação**. A política de indexação atualmente é semelhante ao seguinte:
    
   ```json
   {
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/\"_etag\"/?"
        }
    ]
    }
   ````

   Substitua a política de indexação acima pela seguinte política:

   ```json
   {
    "indexingMode": "none",
    "automatic": false,
    "includedPaths": [],
    "excludedPaths": []
    }
   ```

1. Use o seguinte snippet de código para criar o objeto de conexão. Objeto de conexão (a ser colocado em @Bean ou torná-lo estático):

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
   
   if(client==null)
      client= CosmosClient.builder()
         .endpoint(Host)//(Host, PrimaryKey, dbName, collName).Builder()
          .connectionPolicy(cp)
          .key(PrimaryKey)
          .consistencyLevel(ConsistencyLevel.EVENTUAL)
          .build();
    
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

Agora você pode executar as operações CRUD da seguinte maneira:

### <a name="read-operation"></a>Operação de leitura

Para ler o item, use o seguinte snippet:

```java        
CosmosItemRequestOptions ro=new CosmosItemRequestOptions();
ro.partitionKey(new PartitionKey(documentId));
CountDownLatch latch=new CountDownLatch(1);
      
var objCosmosItem= container.getItem(documentId, documentId);
Mono<CosmosItemResponse> objMono = objCosmosItem.read(ro);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
           if(resourceResponse.item()!=null)
           {
              doc= resourceResponse.properties().toObject(UserModel.class);
           }
        },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="insert-operation"></a>Operação de Inserção

Para inserir um item, você pode executar o seguinte código:

```java
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

Em seguida, assine mono como:

```java
CountDownLatch latch=new CountDownLatch(1);
objMono.subscribeOn(Schedulers.elastic())
      .subscribe(resourceResponse->
      {
         if(resourceResponse.statusCode()!=successStatus)
            {
               throw new RuntimeException(resourceResponse.toString());
            }
         },
      Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="upsert-operation"></a>Operação upsert

Para atualizar o valor de um item, consulte o snippet de código abaixo:

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
Em seguida, assine mono, consulte o snippet de assinatura mono na operação de inserção.

### <a name="delete-operation"></a>Operação de exclusão

Use o snippet a seguir para executar a operação de exclusão:

```java
CosmosItem objItem= container.getItem(id, id);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Em seguida, assine mono, consulte o snippet de assinatura mono na operação de inserção. O código de exemplo completo está disponível no repositório [CouchbaseToCosmosDB-AsyncKeyValue](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/main/AsyncKeyValue) do GitHub.

## <a name="data-migration"></a>Migração de Dados

Há duas maneiras de migrar dados.

* **Usar o Azure Data Factory:** Esse é o método mais recomendado para migrar os dados. Configure a origem como Couchbase e o coletor como API do SQL do Azure Cosmos DB, consulte o artigo [Conector do Data Factory do Cosmos DB](../data-factory/connector-azure-cosmos-db.md) do Azure para obter etapas detalhadas.

* **Use a ferramenta de importação de dados do Azure Cosmos DB:** Essa opção é recomendada para migrar usando VMs com menos quantidade de dados. Para ver etapas detalhadas, consulte o artigo [Importador de dados](./import-data.md).

## <a name="next-steps"></a>Próximas etapas

* Para realizar testes de desempenho, consulte o artigo [Teste de desempenho e escala com o Azure Cosmos DB](./performance-testing.md).
* Para otimizar o código, consulte o artigo [Dicas de desempenho para o Azure Cosmos DB](./performance-tips-async-java.md).
* Explore o SDK do Java assíncrono V3, repositório de [Referência do SDK](https://github.com/Azure/azure-cosmosdb-java/tree/v3) do GitHub.
