---
title: 'Tutorial do Azure Cosmos DB: Criar, consultar e percorrer no Console do Apache TinkerPops Gremlin'
description: Um início rápido do Azure Cosmos DB para criar vértices, bordas e consultas usando a API do Gremlin do Azure Cosmos DB.
author: luisbosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: quickstart
ms.date: 07/23/2019
ms.author: lbosq
ms.openlocfilehash: 0b5af9a2f244f64aa77252a5cef4cb3eb9b64a25
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565482"
---
# <a name="quickstart-create-query-and-traverse-an-azure-cosmos-db-graph-database-using-the-gremlin-console"></a>Início Rápido: Criar, consultar e percorrer um banco de dados de grafo do Azure Cosmos DB usando o console do Gremlin

> [!div class="op_single_selector"]
> * [Console do Gremlin](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

O Azure Cosmos DB é o serviço de banco de dados multimodelo distribuído globalmente da Microsoft. É possível criar e consultar rapidamente documentos, chave/valor e bancos de dados do grafo. Todos se beneficiam de recursos de escala horizontal e distribuição global no núcleo do Azure Cosmos DB. 

Este início rápido demonstra como criar uma conta de [API do Gremlin](graph-introduction.md) do Azure Cosmos DB, um banco de dados e um grafo (contêiner) usando o Portal do Azure e, depois, use o [console do Gremlin](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) do [Apache TinkerPop](https://tinkerpop.apache.org) para trabalhar com os dados da API do Gremlin. Neste tutorial, você criará e consultará vértices e bordas ao atualizar uma propriedade de vértice, consultar vértices, percorrer o grafo e remover um vértice.

![BD Cosmos do Azure do console do Apache Gremlin](./media/create-graph-gremlin-console/gremlin-console.png)

O console Gremlin é baseado em Groovy/Java e é executado no Linux, Mac e Windows. Você pode baixá-lo no [site do Apache TinkerPop](https://tinkerpop.apache.org/downloads.html).

## <a name="prerequisites"></a>Pré-requisitos

Você precisa ter uma assinatura do Azure para criar uma conta do BD Cosmos do Azure para este início rápido.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Também é necessário instalar o [Console do Gremlin](https://tinkerpop.apache.org/). Use a versão 3.2.5 ou posterior. (Para usar o Console do Gremlin no Windows, você precisa instalar o [Java Runtime](https://www.oracle.com/technetwork/java/javase/overview/index.html).)

## <a name="create-a-database-account"></a>Criar uma conta de banco de dados

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Adicionar um grafo

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a id="ConnectAppService"></a>Conectar-se ao serviço de aplicativo
1. Antes de iniciar o console do Gremlin, crie ou modifique seu arquivo de configuração remote-secure.yaml no diretório `apache-tinkerpop-gremlin-console-3.2.5/conf`.
2. Preencha as configurações de *host*, *porta*, *nome de usuário*, *senha*, *connectionPool* e *serializador* como definido nesta tabela:

    Configuração|Valor sugerido|DESCRIÇÃO
    ---|---|---
    hosts|[*account-name*.gremlin.cosmos.azure.com]|Consulte a seguinte captura de tela. Esse é o valor de URI Gremlin na página Visão geral do portal do Azure, entre colchetes, com :443 / à direita removido.
    porta|443|Definir para 443.
    Nome de Usuário|*Seu nome de usuário*|O recurso do formulário `/dbs/<db>/colls/<coll>` onde `<db>` é o nome do banco de dados e `<coll>` é o nome da coleção.
    Senha|*Sua chave primária*| Confira a segunda captura de tela abaixo. Esta é sua chave primária, que você pode recuperar da página de chaves do portal do Azure, na caixa de chave Primária. Use o botão de cópia no lado esquerdo da caixa para copiar o valor.
    connectionPool|{enableSsl: true}|Sua configuração do pool de conexão para SSL.
    serializador|{ className: org.apache.tinkerpop.gremlin.<br>driver.ser.GraphSONMessageSerializerV1d0,<br> config: { serializeResultToString: true }}|Defina para esse valor e exclua todas as quebras de linha `\n` ao colar o valor.

    Para obter o valor de hosts, copie o valor **URI do Gremlin** na página **Visão geral**: ![Exibir e copiar o valor URI do Gremlin na página Visão geral no portal do Azure](./media/create-graph-gremlin-console/gremlin-uri.png)

    Para obter o valor da senha, copie a **Chave Primária** da página **Chaves**: ![Exibir e copiar sua chave primária no portal do Azure, página Chaves](./media/create-graph-gremlin-console/keys.png)

O arquivo remote-secure.yaml deve ter esta aparência:

```
hosts: [your_database_server.gremlin.cosmos.azure.com] 
port: 443
username: /dbs/your_database_account/colls/your_collection
password: your_primary_key
connectionPool: {
  enableSsl: true
}
serializer: { className: org.apache.tinkerpop.gremlin.driver.ser.GraphSONMessageSerializerV1d0, config: { serializeResultToString: true }}
```

certifique-se de colocar o valor dos parâmetros de host dentro de colchetes []. 

1. No seu terminal, execute `bin/gremlin.bat` ou `bin/gremlin.sh` para iniciar o [Gremlin Console](https://tinkerpop.apache.org/docs/3.2.5/tutorials/getting-started/).
1. No seu terminal, execute `:remote connect tinkerpop.server conf/remote-secure.yaml` para se conectar ao seu serviço de aplicativo.

    > [!TIP]
    > Se você receber o erro `No appenders could be found for logger`, verifique se atualizou o valor de serializador no arquivo remote-secure.yaml, conforme descrito na etapa 2. 

1. Em seguida execute `:remote console` para redirecionar todos os comandos de console para o servidor remoto.

   > [!NOTE]
   > Se você não executar o comando `:remote console`, mas quiser redirecionar todos os comandos de console para o servidor remoto, prefixe o comando com `:>`. Por exemplo, execute o comando como `:> g.V().count()`. Esse prefixo faz parte do comando e é importante ao usar o console do Gremlin com o Azure Cosmos DB. Omitir este prefixo instrui o console a executar o comando localmente, geralmente em um grafo na memória. O uso desse prefixo `:>` informa que o console deve executar um comando remoto, nesse caso, no Azure Cosmos DB (no emulador do localhost ou em uma instância do Azure).

Ótimo! Agora que a instalação está concluída, vamos começar a executar alguns dos comandos de console.

Vamos tentar um comando count() simples. Digite o seguinte no console no prompt:

```
g.V().count()
```

## <a name="create-vertices-and-edges"></a>Criar vértices e bordas

Vamos começar pela adição de quatro vértices pessoais para *Paulo*, *Maria Eduarda*, *Valentina*, *Pedro* e *Davi*.

Entrada (Thomas):

```
g.addV('person').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44).property('userid', 1).property('pk', 'pk')
```

Saída:

```
==>[id:796cdccc-2acd-4e58-a324-91d6f6f5ed6d,label:person,type:vertex,properties:[firstName:[[id:f02a749f-b67c-4016-850e-910242d68953,value:Thomas]],lastName:[[id:f5fa3126-8818-4fda-88b0-9bb55145ce5c,value:Andersen]],age:[[id:f6390f9c-e563-433e-acbf-25627628016e,value:44]],userid:[[id:796cdccc-2acd-4e58-a324-91d6f6f5ed6d|userid,value:1]]]]
```
Entrada (Mary Kay):

```
g.addV('person').property('firstName', 'Mary Kay').property('lastName', 'Andersen').property('age', 39).property('userid', 2).property('pk', 'pk')

```

Saída:

```
==>[id:0ac9be25-a476-4a30-8da8-e79f0119ea5e,label:person,type:vertex,properties:[firstName:[[id:ea0604f8-14ee-4513-a48a-1734a1f28dc0,value:Mary Kay]],lastName:[[id:86d3bba5-fd60-4856-9396-c195ef7d7f4b,value:Andersen]],age:[[id:bc81b78d-30c4-4e03-8f40-50f72eb5f6da,value:39]],userid:[[id:0ac9be25-a476-4a30-8da8-e79f0119ea5e|userid,value:2]]]]

```

Entrada (Robin):

```
g.addV('person').property('firstName', 'Robin').property('lastName', 'Wakefield').property('userid', 3).property('pk', 'pk')
```

Saída:

```
==>[id:8dc14d6a-8683-4a54-8d74-7eef1fb43a3e,label:person,type:vertex,properties:[firstName:[[id:ec65f078-7a43-4cbe-bc06-e50f2640dc4e,value:Robin]],lastName:[[id:a3937d07-0e88-45d3-a442-26fcdfb042ce,value:Wakefield]],userid:[[id:8dc14d6a-8683-4a54-8d74-7eef1fb43a3e|userid,value:3]]]]
```

Entrada (Ben):

```
g.addV('person').property('firstName', 'Ben').property('lastName', 'Miller').property('userid', 4).property('pk', 'pk')

```

Saída:

```
==>[id:ee86b670-4d24-4966-9a39-30529284b66f,label:person,type:vertex,properties:[firstName:[[id:a632469b-30fc-4157-840c-b80260871e9a,value:Ben]],lastName:[[id:4a08d307-0719-47c6-84ae-1b0b06630928,value:Miller]],userid:[[id:ee86b670-4d24-4966-9a39-30529284b66f|userid,value:4]]]]
```

Entrada (tomada):

```
g.addV('person').property('firstName', 'Jack').property('lastName', 'Connor').property('userid', 5).property('pk', 'pk')
```

Saída:

```
==>[id:4c835f2a-ea5b-43bb-9b6b-215488ad8469,label:person,type:vertex,properties:[firstName:[[id:4250824e-4b72-417f-af98-8034aa15559f,value:Jack]],lastName:[[id:44c1d5e1-a831-480a-bf94-5167d133549e,value:Connor]],userid:[[id:4c835f2a-ea5b-43bb-9b6b-215488ad8469|userid,value:5]]]]
```


Em seguida, vamos adicionar bordas para relações entre as pessoas.

Entrada (Thomas -> Mary Kay):

```
g.V().hasLabel('person').has('firstName', 'Thomas').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Mary Kay'))
```

Saída:

```
==>[id:c12bf9fb-96a1-4cb7-a3f8-431e196e702f,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:0d1fa428-780c-49a5-bd3a-a68d96391d5c,outV:1ce821c6-aa3d-4170-a0b7-d14d2a4d18c3]
```

Entrada (Thomas -> Robin):

```
g.V().hasLabel('person').has('firstName', 'Thomas').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Robin'))
```

Saída:

```
==>[id:58319bdd-1d3e-4f17-a106-0ddf18719d15,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:3e324073-ccfc-4ae1-8675-d450858ca116,outV:1ce821c6-aa3d-4170-a0b7-d14d2a4d18c3]
```

Entrada (Robin -> Ben):

```
g.V().hasLabel('person').has('firstName', 'Robin').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Ben'))
```

Saída:

```
==>[id:889c4d3c-549e-4d35-bc21-a3d1bfa11e00,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:40fd641d-546e-412a-abcc-58fe53891aab,outV:3e324073-ccfc-4ae1-8675-d450858ca116]
```

## <a name="update-a-vertex"></a>Atualizar um vértice

Vamos atualizar o vértice do *Thomas* com uma nova idade de *45*.

Entrada:
```
g.V().hasLabel('person').has('firstName', 'Thomas').property('age', 45)
```
Saída:

```
==>[id:ae36f938-210e-445a-92df-519f2b64c8ec,label:person,type:vertex,properties:[firstName:[[id:872090b6-6a77-456a-9a55-a59141d4ebc2,value:Thomas]],lastName:[[id:7ee7a39a-a414-4127-89b4-870bc4ef99f3,value:Andersen]],age:[[id:a2a75d5a-ae70-4095-806d-a35abcbfe71d,value:45]]]]
```

## <a name="query-your-graph"></a>Consultar o grafo

Agora, vamos executar uma variedade de consultas em relação ao grafo.

Primeiro, vamos tentar uma consulta com um filtro para retornar somente pessoas com mais de 40 anos de idade.

Entrada (consulta de filtro):

```
g.V().hasLabel('person').has('age', gt(40))
```

Saída:

```
==>[id:ae36f938-210e-445a-92df-519f2b64c8ec,label:person,type:vertex,properties:[firstName:[[id:872090b6-6a77-456a-9a55-a59141d4ebc2,value:Thomas]],lastName:[[id:7ee7a39a-a414-4127-89b4-870bc4ef99f3,value:Andersen]],age:[[id:a2a75d5a-ae70-4095-806d-a35abcbfe71d,value:45]]]]
```

Em seguida, vamos projetar o primeiro nome para as pessoas que têm mais de 40 anos de idade.

Entrada (filtro + consulta de projeção):

```
g.V().hasLabel('person').has('age', gt(40)).values('firstName')
```

Saída:

```
==>Thomas
```

## <a name="traverse-your-graph"></a>Percorrer o grafo

Vamos percorrer o grafo para retornar todos os amigos de Thomas.

Entrada (amigos de Thomas):

```
g.V().hasLabel('person').has('firstName', 'Thomas').outE('knows').inV().hasLabel('person')
```

Saída: 

```
==>[id:f04bc00b-cb56-46c4-a3bb-a5870c42f7ff,label:person,type:vertex,properties:[firstName:[[id:14feedec-b070-444e-b544-62be15c7167c,value:Mary Kay]],lastName:[[id:107ab421-7208-45d4-b969-bbc54481992a,value:Andersen]],age:[[id:4b08d6e4-58f5-45df-8e69-6b790b692e0a,value:39]]]]
==>[id:91605c63-4988-4b60-9a30-5144719ae326,label:person,type:vertex,properties:[firstName:[[id:f760e0e6-652a-481a-92b0-1767d9bf372e,value:Robin]],lastName:[[id:352a4caa-bad6-47e3-a7dc-90ff342cf870,value:Wakefield]]]]
```

Em seguida, vamos obter a próxima camada de vértices. Percorra o grafo para retornar todos os amigos de Thomas.

Entrada (amigos de amigos de Thomas):

```
g.V().hasLabel('person').has('firstName', 'Thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')
```
Saída:

```
==>[id:a801a0cb-ee85-44ee-a502-271685ef212e,label:person,type:vertex,properties:[firstName:[[id:b9489902-d29a-4673-8c09-c2b3fe7f8b94,value:Ben]],lastName:[[id:e084f933-9a4b-4dbc-8273-f0171265cf1d,value:Miller]]]]
```

## <a name="drop-a-vertex"></a>Remover um vértice

Agora vamos excluir um vértice do banco de dados do grafo.

Entrada (tirar conector da tomada):

```
g.V().hasLabel('person').has('firstName', 'Jack').drop()
```

## <a name="clear-your-graph"></a>Limpar o grafo

Por fim, vamos limpar o banco de dados de todos os vértices e bordas.

Entrada:

```
g.E().drop()
g.V().drop()
```

Parabéns! Você concluiu este tutorial do Azure Cosmos DB: API do Gremlin!

## <a name="review-slas-in-the-azure-portal"></a>Examinar SLAs no Portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a criar uma conta do Azure Cosmos DB, criar um grafo usando o Data Explorer, criar vértices e bordas e percorrer o grafo usando o console de Gremlin. Agora, você pode criar consultas mais complexas e implementar uma lógica de passagem de grafo avançada usando o Gremlin. 

> [!div class="nextstepaction"]
> [Consultar usando o Gremlin](tutorial-query-graph.md)
