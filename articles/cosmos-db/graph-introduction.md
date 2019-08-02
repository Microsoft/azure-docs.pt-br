---
title: Introdução à API do Gremlin do Azure Cosmos DB
description: Saiba como você pode usar o Azure Cosmos DB para armazenar, consultar e percorrer grafos grandes com baixa latência usando a linguagem de consulta de grafos Gremlin do Apache TinkerPop.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 07/18/2019
ms.author: lbosq
ms.openlocfilehash: 1f46eb1995e2e7cb098098ebd22eedbd194dc6a6
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310250"
---
# <a name="introduction-to-azure-cosmos-db-gremlin-api"></a>Introdução ao Azure Cosmos DB: API do Gremlin

O [Azure Cosmos DB](introduction.md) é o serviço de banco de dados multimodelo distribuído globalmente da Microsoft para aplicativos de missão crítica. Ele é um banco de dados multimodelo e é compatível com modelos de dados de documentos, de chave-valor, de grafos e de família de colunas. A API do Gremlin do Azure Cosmos DB é usada para armazenar e operar com os dados de grafo em um serviço de banco de dados totalmente gerenciado criado para qualquer escala.  

![Arquitetura de grafo do Azure Cosmos DB](./media/graph-introduction/cosmosdb-graph-architecture.png)

Este artigo fornece uma visão geral da API do Gremlin do Azure Cosmos DB e explica como você pode usá-lo para armazenar grandes grafos com bilhões de vértices e bordas. Você pode consultar os grafos com latência de milissegundo e desenvolver a estrutura do grafo facilmente. API do Gremlin do Azure Cosmos DB se baseia no padrão do banco de dados de grafo do [Apache TinkerPop](https://tinkerpop.apache.org)  e usa a linguagem de consulta Gremlin. 

A API do Gremlin do Azure Cosmos DB combina o poder dos algoritmos de banco de dados de grafo com a infraestrutura altamente escalonável e gerenciada para fornecer uma solução exclusiva e flexível para os problemas mais comuns de dados associados à falta de flexibilidade e abordagens relacionais. 

## <a name="features-of-azure-cosmos-db-graph-database"></a>Recursos do banco de dados de grafo do Azure Cosmos DB
 
O Azure Cosmos DB é um banco de dados de grafo totalmente gerenciado que oferece distribuição global, dimensionamento elástico do armazenamento e da taxa de transferência, indexação e consulta automática, níveis de consistência ajustáveis e suporte ao padrão TinkerPop. 

Estes são recursos diferenciados oferecidos pela API do Gremlin do Azure Cosmos DB:

* **Dimensionamento elástico do armazenamento e da taxa de transferência**

  Grafos no mundo real precisam ser dimensionados além da capacidade de um único servidor. O Azure Cosmos DB é compatível com bancos de dados de grafo que podem ter um tamanho praticamente ilimitado em termos de armazenamento e de taxa de transferência provisionada. À medida que aumenta a escala do banco de dados de grafo, os dados serão distribuídos automaticamente usando o [particionamento do graph](https://docs.microsoft.com/azure/cosmos-db/graph-partitioning).

* **Replicação de várias regiões**

  O Azure Cosmos DB pode replicar automaticamente seus dados de grafo para qualquer região do Azure no mundo. A replicação global simplifica o desenvolvimento de aplicativos que exigem acesso global aos dados. Além de minimizar a latência de leitura e de gravação em qualquer lugar em todo o mundo, o Azure Cosmos DB fornece um mecanismo de failover regional automático que pode garantir a continuidade do seu aplicativo no caso raro de uma interrupção de serviço em uma região. 

* **Passagens e consultas rápidas com o padrão de consulta de grafo mais amplamente adotado**

  Armazene bordas e vértices heterogêneos e consulte esses documentos por meio de uma sintaxe Gremlin familiar. O Gremlin é uma linguagem de consulta imperativa e funcional que fornece uma interface avançada para implementar algoritmos de grafo comuns. 
  
  O Azure Cosmos DB habilita passagens e consultas avançadas em tempo real sem a necessidade de especificar dicas de esquema, índices secundários ou exibições. Saiba mais em [Consultar grafos usando Gremlin](gremlin-support.md).

* **Banco de dados de grafo totalmente gerenciado**

  O BD Cosmos do Azure elimina a necessidade de gerenciar recursos do computador e do banco de dados. A maioria das plataformas existentes de banco de dados de grafo está associada às limitações de infraestrutura dela e geralmente exigem um alto grau de manutenção para garantir sua operação. 
  
  Como um serviço totalmente gerenciado, o Cosmos DB acaba com a necessidade de gerenciar máquinas virtuais, atualizar o software de tempo de execução, gerenciar a fragmentação ou a replicação ou lidar com atualizações de camadas de dados complexas. Cada grafo é salvo em backup automaticamente e protegido contra falhas regionais. Essas garantias permitem que os desenvolvedores se concentrem no fornecimento de valor para o aplicativo em vez de operar e gerenciar os bancos de dados de grafo deles. 

* **Indexação automática**

  Por padrão, o Azure Cosmos DB indexa automaticamente todas as propriedades dentro dos nós e bordas do grafo e não espera ou exige qualquer esquema ou criação de índices secundários. Saiba mais sobre a [indexação no Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/index-overview). 

* **Compatibilidade com o Apache TinkerPop**

  O Azure Cosmos DB dá suporte a [padrão do código-fonte aberto Apache TinkerPop](http://tinkerpop.apache.org/). O padrão Tinkerpop tem um amplo ecossistema de aplicativos e bibliotecas que podem ser facilmente integradas com a API do Gremlin do Azure Cosmos DB. 

* **Níveis de consistência ajustáveis**

  O Azure Cosmos DB fornece cinco níveis de consistência bem-definidos para alcançar a compensação certa entre consistência e desempenho do seu aplicativo. Para operações de consulta e leitura, o Azure Cosmos DB oferece cinco níveis de consistência diferentes: forte, desatualização limitada, sessão, prefixo constante e eventual. Esses níveis de consistência granulares e bem-definidos permitem que você faça compensações seguras entre consistência, disponibilidade e latência. Saiba mais em [Níveis ajustáveis de consistência de dados no Azure Cosmos DB](consistency-levels.md).

## <a name="scenarios-that-can-use-gremlin-api"></a>Cenários que podem usar a API Gremlin
Veja alguns cenários em que o suporte para grafo do Azure Cosmos DB pode ser útil:

* **Redes sociais/Customer 365**

  Combinando dados sobre seus clientes e as interações deles com outras pessoas, você pode desenvolver experiências personalizadas, prever o comportamento do cliente ou conectar pessoas com interesses semelhantes. O BD Cosmos do Azure pode ser usado para gerenciar redes sociais e monitorar dados e preferências do cliente.

* **Mecanismos de recomendação**

  Este cenário costuma ser usado no setor de varejo. Combinando informações sobre produtos, usuários e interações do usuário, como compras, navegação ou a classificação de um item, você pode criar recomendações personalizadas. O Azure Cosmos DB, com sua baixa latência, dimensionamento elástico e suporte para grafo nativo, é ideal para esses cenários.

* **Geoespacial**

  Muitos aplicativos de telecomunicações, logística e planejamento de viagens precisam encontrar um local de interesse em uma área específica ou localizar a rota mais curta/ideal entre dois locais. O BD Cosmos do Azure é uma solução natural para esses problemas.

* **Internet das Coisas**

  Com a rede e as conexões entre os dispositivos IoT modelados como um grafo, você pode criar uma melhor compreensão do estado de seus dispositivos e ativos. Você também pode saber mais como as alterações em uma parte da rede podem afetar potencialmente outra parte.

## <a name="introduction-to-graph-databases"></a>Introdução aos bancos de dados de grafo
Os dados da forma como aparecem no mundo real são conectados naturalmente. A modelagem de dados tradicional se concentra em definir as entidades separadamente e computar as relações delas no tempo de execução. Embora esse modelo tenha suas vantagens, dados altamente conectados podem ser desafiadores de gerenciar em conformidade com as restrições deles.  

Por sua vez, uma abordagem de banco de dados de grafo se baseia em manter relações na camada de armazenamento,o que leva a operações de recuperação de grafo altamente eficientes. A API do Gremlin do Azure Cosmos DB dá suporte ao [modelo de grafo da propriedade](https://tinkerpop.apache.org/docs/current/reference/#intro).

### <a name="property-graph-objects"></a>Objetos de grafo de propriedade

Um [grafo](http://mathworld.wolfram.com/Graph.html) de propriedade é uma estrutura composta por [vértices](http://mathworld.wolfram.com/GraphVertex.html) e [bordas](http://mathworld.wolfram.com/GraphEdge.html). Ambos os objetos têm um número arbitrário de pares chave-valor como propriedades. 

* **Vértices** – os vértices denotam entidades individuais, como uma pessoa, um lugar ou um evento.

* **Bordas** - as bordas indicam relações entre os vértices. Por exemplo, uma pessoa pode conhecer a outra pessoa, estar envolvida em um evento e foi recentemente a um local. 

* **Propriedades** - as propriedades expressam informações sobre os vértices e as bordas. Pode haver qualquer número de propriedades nos vértices ou nas bordas e elas podem ser usadas para descrever e filtrar os objetos em uma consulta. As propriedades de exemplo incluem um vértice com um nome, uma idade e um limite com um carimbo de data/hora ou um peso. 

Os bancos de dados de grafo costumam ser incluídos na categoria de banco de dados de NoSQL ou não relacional, uma vez que não há nenhuma dependência no modelo de dados restrito ou de esquema. Essa falta de esquema permite modelar e armazenar estruturas conectadas de forma natural e eficiente. 

### <a name="gremlin-by-example"></a>Gremlin pelo exemplo
Vamos usar um grafo de exemplo para entender como as consultas podem ser expressas no Gremlin. A figura a seguir mostra um aplicativo de negócios que gerencia dados sobre usuários, interesses e dispositivos na forma de um grafo.  

![Banco de dados de exemplo mostrando interesses, dispositivos e pessoas](./media/gremlin-support/sample-graph.png) 

Este grafo tem os seguintes tipos de *vértice* (chamados de "rótulo" no Gremlin):

- **Pessoas**: O grafo tem três pessoas; Robin, Thomas e Ben
- **Interesses**: Os interesses dessas pessoas, que neste exemplo é o jogo de futebol
- **Dispositivos**: Os dispositivos que as pessoas usam
- **Sistemas operacionais**: Os sistemas operacionais em que os dispositivos executam

Nós representamos as relações entre essas entidades usando os seguintes tipos/rótulos de *borda*:

- **Conhece**: Por exemplo, "Thomas conhece Robin"
- **Interessado**: Para representar os interesses das pessoas em nosso grafo, por exemplo, "Ben está interessado em futebol"
- **RunsOS**: O laptop executa o sistema operacional Windows
- **Usa**: Para representar qual dispositivo uma pessoa usa. Por exemplo, Robin usa um telefone Motorola com o número de série 77

Vamos executar algumas operações nesse grafo usando o [Console do Gremlin](https://tinkerpop.apache.org/docs/3.3.2/reference/#gremlin-console). Você também pode executar essas operações usando drivers do Gremlin na plataforma de sua escolha (Java, Node.js, Python ou .NET).  Antes de examinarmos o que tem suporte no BD Cosmos do Azure, vejamos alguns exemplos para nos familiarizarmos com a sintaxe.

Primeiro, vamos ver o CRUD. A seguinte instrução do Gremlin insere o vértice "Thomas" no grafo:

```java
:> g.addV('person').property('id', 'thomas.1').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44)
```

Em seguida, a seguinte instrução do Gremlin insere uma borda do tipo "conhece" entre Thomas e Robin.

```java
:> g.V('thomas.1').addE('knows').to(g.V('robin.1'))
```

A consulta a seguir retorna os vértices do tipo "pessoa" na ordem decrescente de seus nomes:
```java
:> g.V().hasLabel('person').order().by('firstName', decr)
```

Os grafos se destacam em situações em que você precisa responder perguntas como "Quais sistemas operacionais os amigos de Thomas usam?". Você pode executar esse transversal do Gremlin para obter informações do gráfico:

```java
:> g.V('thomas.1').out('knows').out('uses').out('runsos').group().by('name').by(count())
```

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o suporte para grafo no Azure Cosmos DB, consulte:

* Introdução ao [Tutorial de grafo do Azure Cosmos DB](create-graph-dotnet.md).
* Saiba como [consultar grafos no Azure Cosmos DB usando o Gremlin](gremlin-support.md).
