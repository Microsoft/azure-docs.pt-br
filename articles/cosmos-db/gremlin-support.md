---
title: Suporte e compatibilidade do Gremlin do Azure Cosmos DB com recursos do TinkerPop
description: Saiba mais sobre a linguagem Gremlin do Apache TinkerPop. Saiba quais recursos e etapas estão disponíveis no Azure Cosmos DB e as diferenças de compatibilidade do mecanismo de grafo do TinkerPop.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 11/11/2020
ms.author: sngun
ms.openlocfilehash: 036338e90a3e7b466924d419400c0dcc692dec5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97630744"
---
# <a name="azure-cosmos-db-gremlin-graph-support-and-compatibility-with-tinkerpop-features"></a>Suporte e compatibilidade de grafo do Gremlin do Azure Cosmos DB com recursos do TinkerPop
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Azure Cosmos DB suporta a linguagem transversal de gráficos [Apache Tinkerpop](https://tinkerpop.apache.org), conhecida como gráfica [Gremlin](https://tinkerpop.apache.org/docs/3.3.2/reference/#graph-traversal-steps). É possível usar a linguagem Gremlin para criar entidades de grafo (vértices e bordas), modificar propriedades dentro dessas entidades, executar consultas e passagens e excluir entidades.

O mecanismo de grafo do Azure Cosmos DB segue estreitamente a especificação de etapas de travessia do [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps), mas há diferenças na implementação que são específicas para o Azure Cosmos DB. Neste artigo, fornecemos instruções passo a passo rápidas do Gremlin e enumeramos os recursos do Gremlin compatíveis com a API do Gremlin.

## <a name="compatible-client-libraries"></a>Bibliotecas de cliente compatíveis

A tabela a seguir mostra drivers Gremlin populares que você pode usar com o BD Cosmos do Azure:

| Baixar | Fonte | Introdução | Versão do conector com suporte |
| --- | --- | --- | --- |
| [.NET](https://tinkerpop.apache.org/docs/3.4.6/reference/#gremlin-DotNet) | [Gremlin.NET no GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-dotnet) | [Criar Grafo usando .NET](create-graph-dotnet.md) | 3.4.6 |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) | [Gremlin JavaDoc](https://tinkerpop.apache.org/javadocs/current/full/) | [Criar Grafo usando Java](create-graph-java.md) | 3.2.0+ |
| [Node.js](https://www.npmjs.com/package/gremlin) | [Gremlin-JavaScript no GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-javascript) | [Criar Grafo usando Node.js](create-graph-nodejs.md) | 3.3.4+ |
| [Python](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-python) | [Gremlin-Python no GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-python) | [Criar Grafo usando Python](create-graph-python.md) | 3.2.7 |
| [PHP](https://packagist.org/packages/brightzone/gremlin-php) | [Gremlin-PHP no GitHub](https://github.com/PommeVerte/gremlin-php) | [Criar Grafo usando PHP](create-graph-php.md) | 3.1.0 |
| [Linguagem Go](https://github.com/supplyon/gremcos/) | [Linguagem Go](https://github.com/supplyon/gremcos/) | | Essa biblioteca foi criada por colaboradores externos. A equipe do Azure Cosmos DB não oferece suporte nem manutenção à biblioteca. |
| [Console do Gremlin](https://tinkerpop.apache.org/downloads.html) | [Documentos do TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |  [Criar Grafo usando Console do Gremlin](create-graph-gremlin-console.md) | 3.2.0 + |

## <a name="supported-graph-objects"></a>Suporte para objetos de gráficos

O TinkerPop é um padrão que abrange uma ampla variedade de tecnologias de grafo. Portanto, ele tem uma terminologia padrão para descrever quais recursos são fornecidos por um provedor de grafo. O Azure Cosmos DB fornece um banco de dados de grafo gravável, persistente e de alta simultaneidade que pode ser particionado em vários servidores ou clusters. 

A tabela a seguir lista os recursos do TinkerPop que são implementados pelo BD Cosmos do Azure: 

| Categoria | Implementação do BD Cosmos do Azure |  Observações | 
| --- | --- | --- |
| Recursos de grafo | Fornece persistência e ConcurrentAccess. Projetado para dar suporte a Transactions | Métodos de computador podem ser implementados por meio do conector Spark. |
| Recursos variáveis | Dá suporte a Boolean, Integer, Byte, Double, Float, Integer, Long, String | Dá suporte a tipos primitivos, é compatível com tipos complexos por meio do modelo de dados |
| Recursos do vértice | Dá suporte a RemoveVertices, MetaProperties, AddVertices, MultiProperties, StringIds, UserSuppliedIds, AddProperty, RemoveProperty  | Dá suporte à criação, modificação e exclusão de vértices |
| Recursos de propriedade do vértice | StringIds, UserSuppliedIds, AddProperty, RemoveProperty, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Dá suporte à criação, modificação e exclusão de propriedades do vértice |
| Recursos da borda | AddEdges, RemoveEdges, StringIds, UserSuppliedIds, AddProperty, RemoveProperty | Dá suporte à criação, modificação e exclusão de bordas |
| Recursos de propriedade da borda | Properties, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Dá suporte à criação, modificação e exclusão de propriedades da borda |

## <a name="gremlin-wire-format"></a>Formato de transmissão do Gremlin

O Azure Cosmos DB usa o formato JSON ao retornar resultados de operações do Gremlin. No momento, o Azure Cosmos DB é compatível com o formato JSON. Por exemplo, o snippet de código a seguir mostra uma representação em JSON de um vértice *retornado ao cliente* do Azure Cosmos DB:

```json
  {
    "id": "a7111ba7-0ea1-43c9-b6b2-efc5e3aea4c0",
    "label": "person",
    "type": "vertex",
    "outE": {
      "knows": [
        {
          "id": "3ee53a60-c561-4c5e-9a9f-9c7924bc9aef",
          "inV": "04779300-1c8e-489d-9493-50fd1325a658"
        },
        {
          "id": "21984248-ee9e-43a8-a7f6-30642bc14609",
          "inV": "a8e3e741-2ef7-4c01-b7c8-199f8e43e3bc"
        }
      ]
    },
    "properties": {
      "firstName": [
        {
          "value": "Thomas"
        }
      ],
      "lastName": [
        {
          "value": "Andersen"
        }
      ],
      "age": [
        {
          "value": 45
        }
      ]
    }
  }
```

As propriedades usadas pelo formato JSON para vértices são descritas abaixo:

| Propriedade | Descrição | 
| --- | --- | --- |
| `id` | A ID do vértice. Deve ser exclusiva (em combinação com o valor de `_partition`, se aplicável). Se nenhum valor for fornecido, ele será automaticamente fornecido com um GUID | 
| `label` | O rótulo do vértice. Esta propriedade é usada para descrever o tipo de entidade. |
| `type` | Usado para distinguir vértices de documentos que não são grafos |
| `properties` | Recipiente de propriedades definidas pelo usuário associadas ao vértice. Cada propriedade pode ter vários valores. |
| `_partition` | A chave de partição do vértice. Usado para [particionamento de gráfico](graph-partitioning.md). |
| `outE` | Essa propriedade contém uma lista de bordas externas de um vértice. Armazenar as informações de adjacência com o vértice permite a execução rápida de passagens. As bordas são agrupadas com base em seus rótulos. |

E a borda contém as seguintes informações para ajudar com a navegação para outras partes do grafo.

| Propriedade | Descrição |
| --- | --- |
| `id` | A ID da borda. Deve ser exclusiva (em combinação com o valor de `_partition`, se aplicável) |
| `label` | O rótulo da borda. Esta propriedade é opcional e é usada para descrever o tipo de relacionamento. |
| `inV` | Ela contém uma lista nos vértices de uma borda. Armazenar as informações de adjacência com a borda permite a execução rápida das passagens. Os vértices são agrupados com base em seus rótulos. |
| `properties` | Recipiente de propriedades definidas pelo usuário associadas à borda. Cada propriedade pode ter vários valores. |

Cada propriedade pode armazenar diversos valores em uma matriz. 

| Propriedade | Descrição |
| --- | --- |
| `value` | O valor da propriedade

## <a name="gremlin-steps"></a>Etapas do Gremlin

Agora, vejamos as etapas do Gremlin com suporte do BD Cosmos do Azure. Para obter uma referência completa sobre o Gremlin, consulte [Referência do TinkerPop](https://tinkerpop.apache.org/docs/3.3.2/reference).

| Etapa | Descrição | Documentação do TinkerPop 3.2 |
| --- | --- | --- |
| `addE` | Adiciona uma borda entre dois vértices | [Etapa addE](https://tinkerpop.apache.org/docs/3.3.2/reference/#addedge-step) |
| `addV` | Adiciona um vértice ao grafo | [Etapa addV](https://tinkerpop.apache.org/docs/3.3.2/reference/#addvertex-step) |
| `and` | Garante que todas as passagens retornem um valor | [e uma etapa](https://tinkerpop.apache.org/docs/3.3.2/reference/#and-step) |
| `as` | Um modulador de etapa para atribuir uma variável à saída de uma etapa | [Etapa as](https://tinkerpop.apache.org/docs/3.3.2/reference/#as-step) |
| `by` | Um modulador de etapa usado com `group` e `order` | [Etapa by](https://tinkerpop.apache.org/docs/3.3.2/reference/#by-step) |
| `coalesce` | Retorna a primeira passagem que retorna um resultado | [Etapa coalesce](https://tinkerpop.apache.org/docs/3.3.2/reference/#coalesce-step) |
| `constant` | Retorna um valor constante. Usada com `coalesce`| [Etapa constant](https://tinkerpop.apache.org/docs/3.3.2/reference/#constant-step) |
| `count` | Retorna a contagem da passagem | [Etapa count](https://tinkerpop.apache.org/docs/3.3.2/reference/#count-step) |
| `dedup` | Retorna os valores com as duplicatas removidas | [Etapa dedup](https://tinkerpop.apache.org/docs/3.3.2/reference/#dedup-step) |
| `drop` | Remove os valores (vértice/borda) | [Etapa drop](https://tinkerpop.apache.org/docs/3.3.2/reference/#drop-step) |
| `executionProfile` | Cria uma descrição de todas as operações gerada pela etapa executada Gremlin | [etapa executionProfile](graph-execution-profile.md) |
| `fold` | Atua como uma barreira que calcula o valor agregado dos resultados| [Etapa fold](https://tinkerpop.apache.org/docs/3.3.2/reference/#fold-step) |
| `group` | Agrupa os valores com base nos rótulos especificados| [Etapa group](https://tinkerpop.apache.org/docs/3.3.2/reference/#group-step) |
| `has` | Usada para filtrar propriedades, vértices e bordas. Dá suporte às variantes `hasLabel`, `hasId`, `hasNot` e `has`. | [Etapa has](https://tinkerpop.apache.org/docs/3.3.2/reference/#has-step) |
| `inject` | Insere valores em um fluxo| [Etapa inject](https://tinkerpop.apache.org/docs/3.3.2/reference/#inject-step) |
| `is` | Usada para executar um filtro usando uma expressão booliana | [Etapa is](https://tinkerpop.apache.org/docs/3.3.2/reference/#is-step) |
| `limit` | Usada para limitar o número de itens na passagem| [Etapa limit](https://tinkerpop.apache.org/docs/3.3.2/reference/#limit-step) |
| `local` | A etapa local encapsula uma seção de uma passagem, de forma semelhante a uma subconsulta | [Etapa local](https://tinkerpop.apache.org/docs/3.3.2/reference/#local-step) |
| `not` | Usada para produzir a negação de um filtro | [Etapa not](https://tinkerpop.apache.org/docs/3.3.2/reference/#not-step) |
| `optional` | Retorna o resultado da passagem especificada se ela produzir um resultado. Caso contrário, retorna o elemento de chamada | [Etapa optional](https://tinkerpop.apache.org/docs/3.3.2/reference/#optional-step) |
| `or` | Garante que pelo menos uma das passagens retorne um valor | [Etapa or](https://tinkerpop.apache.org/docs/3.3.2/reference/#or-step) |
| `order` | Retorna os resultados na ordem de classificação especificada | [Etapa order](https://tinkerpop.apache.org/docs/3.3.2/reference/#order-step) |
| `path` | Retorna o caminho completo da passagem | [Etapa path](https://tinkerpop.apache.org/docs/3.3.2/reference/#path-step) |
| `project` | Projeta as propriedades como um mapa | [Etapa project](https://tinkerpop.apache.org/docs/3.3.2/reference/#project-step) |
| `properties` | Retorna as propriedades para os rótulos especificados | [Etapa properties](https://tinkerpop.apache.org/docs/3.3.2/reference/#_properties_step) |
| `range` | Filtra para o intervalo de valores especificado| [Etapa range](https://tinkerpop.apache.org/docs/3.3.2/reference/#range-step) |
| `repeat` | Repete a etapa o número de vezes especificado. Usada para efetuar loop | [Etapa repeat](https://tinkerpop.apache.org/docs/3.3.2/reference/#repeat-step) |
| `sample` | Usada para fazer a amostragem dos resultados da passagem | [Etapa sample](https://tinkerpop.apache.org/docs/3.3.2/reference/#sample-step) |
| `select` | Usada para projetar resultados da passagem |  [Etapa select](https://tinkerpop.apache.org/docs/3.3.2/reference/#select-step) |
| `store` | Usada para agregações sem bloqueio da passagem | [Etapa store](https://tinkerpop.apache.org/docs/3.3.2/reference/#store-step) |
| `TextP.startingWith(string)` | Função de filtragem de cadeia de caracteres. Essa função é usada como um predicado para a etapa `has()` para corresponder uma propriedade com o início de uma determinada cadeia de caracteres | [Predicados TextP](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.endingWith(string)` |  Função de filtragem de cadeia de caracteres. Essa função é usada como um predicado para a etapa `has()` para corresponder uma propriedade com o final de uma determinada cadeia de caracteres | [Predicados TextP](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.containing(string)` | Função de filtragem de cadeia de caracteres. Essa função é usada como um predicado para a etapa `has()` para corresponder uma propriedade com o conteúdo de uma determinada cadeia de caracteres | [Predicados TextP](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notStartingWith(string)` | Função de filtragem de cadeia de caracteres. Essa função é usada como um predicado para a etapa `has()` para corresponder uma propriedade que não começa com uma determinada cadeia de caracteres | [Predicados TextP](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notEndingWith(string)` | Função de filtragem de cadeia de caracteres. Essa função é usada como um predicado para a etapa `has()` para corresponder uma propriedade que não termina com uma determinada cadeia de caracteres | [Predicados TextP](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notContaining(string)` | Função de filtragem de cadeia de caracteres. Essa função é usada como um predicado para a etapa `has()` para corresponder uma propriedade que não contém uma determinada cadeia de caracteres | [Predicados TextP](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `tree` | Agrega os caminhos de um vértice em uma árvore | [Etapa tree](https://tinkerpop.apache.org/docs/3.3.2/reference/#tree-step) |
| `unfold` | Desenrola um iterador como uma etapa| [Etapa unfold](https://tinkerpop.apache.org/docs/3.3.2/reference/#unfold-step) |
| `union` | Mescla resultados de várias passagens| [Etapa union](https://tinkerpop.apache.org/docs/3.3.2/reference/#union-step) |
| `V` | Inclui as etapas necessárias para passagens entre vértices e bordas `V`, `E`, `out`, `in`, `both`, `outE`, `inE`, `bothE`, `outV`, `inV`, `bothV` e `otherV` para | [Etapa vertex](https://tinkerpop.apache.org/docs/3.3.2/reference/#vertex-steps) |
| `where` | Usada para filtrar os resultados da passagem. Dá suporte aos operadores `eq`, `neq`, `lt`, `lte`, `gt`, `gte` e `between`  | [Etapa where](https://tinkerpop.apache.org/docs/3.3.2/reference/#where-step) |

O mecanismo otimizado para gravação do Azure Cosmos DB dá suporte à indexação automática de todas as propriedades dentro dos vértices e bordas por padrão. Sendo assim, consultas com filtros, consultas de intervalo, classificações ou agregações de qualquer propriedade são processadas no índice e atendidas de modo eficiente. Para obter mais informações sobre como a indexação funciona no BD Cosmos do Azure, consulte nosso artigo sobre [Indexação independente do esquema](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf).

## <a name="behavior-differences"></a>Diferenças de comportamento

* O mecanismo de grafo do Azure Cosmos DB executa a travessia do ***balanceamento em largura***, enquanto o Gremlin do TinkerPop é de balanceamento em profundidade. Esse comportamento atinge um melhor desempenho no sistema escalonável horizontalmente como o Cosmos DB.

## <a name="unsupported-features"></a>Recursos sem suporte

* O ***[Código de bytes do Gremlin](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** é uma especificação independente da linguagem de programação para passagens de gráfico. O grafo do Cosmos DB ainda não dá suporte a isso. Use `GremlinClient.SubmitAsync()` e envie a travessia como uma cadeia de caracteres de texto.

* Não há suporte à definição da cardinalidade ***`property(set, 'xyz', 1)`*** no momento. Use `property(list, 'xyz', 1)` em seu lugar. Para saber mais, confira [Propriedades de vértice com TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties).

* A ***etapa `match()`*** não está disponível no momento. Esta etapa fornece funcionalidades de consulta declarativa.

* Não há suporte a ***objetos como propriedades*** nos vértices ou bordas. As propriedades somente podem ser tipos primitivos ou matrizes.

* Não há suporte à ***classificação por propriedades de matriz*** `order().by(<array property>)`. É possível classifica apenas por tipos primitivos.

* Não há suporte a ***tipos JSON não primitivos***. Use os tipos `string`, `number` ou `true`/`false`. Não há suporte para valores `null`. 

* No momento, não há suporte para o serializador ***GraphSONv3***. Use as classes de Serializador, Leitor e Gravador `GraphSONv2` na configuração de conexão. Os resultados retornados pela API do Gremlin do Azure Cosmos DB não têm o mesmo formato que o formato GraphSON. 

* Atualmente, não há suporte para as **expressões e funções lambda**. Isso inclui as funções `.map{<expression>}`, `.by{<expression>}` e `.filter{<expression>}`. Para saber mais e aprender a reescrevê-las usando as etapas do Gremlin, confira [Uma observação sobre Lambdas](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas).

* Não há suporte a ***Transações*** devido à natureza distribuída do sistema.  Configure o modelo de consistência apropriado na conta do Gremlin para "ler as próprias gravações" e use a simultaneidade otimista para resolver as gravações conflitantes.

## <a name="known-limitations"></a>Limitações conhecidas

* **Utilização de índice para consultas do Gremlin com etapas de `.V()`meia travessia**: No momento, somente a primeira chamada `.V()` de travessia usará o índice para resolver filtros ou predicados anexados a ela. As chamadas subsequentes não consultarão o índice, o que pode aumentar a latência e o custo da consulta.
    
Pressupondo a indexação padrão, uma consulta do Gremlin de leitura típica que começa com a etapa `.V()` usaria parâmetros nas etapas de filtragem anexadas, como `.has()` ou `.where()` para otimizar o custo e o desempenho da consulta. Por exemplo:

```java
g.V().has('category', 'A')
```

No entanto, quando mais de uma etapa `.V()` é incluída na consulta do Gremlin, a resolução dos dados para a consulta pode não ser ideal. Use a seguinte consulta como um exemplo:

```java
g.V().has('category', 'A').as('a').V().has('category', 'B').as('b').select('a', 'b')
```

Essa consulta retornará dois grupos de vértices com base na propriedade deles chamada `category`. Nesse caso, somente a primeira chamada, `g.V().has('category', 'A')`, fará uso do índice para resolver os vértices com base nos valores das propriedades deles.

Uma solução alternativa para essa consulta é usar etapas de subtravessia como `.map()` e `union()`. Isso é exemplificado abaixo:

```java
// Query workaround using .map()
g.V().has('category', 'A').as('a').map(__.V().has('category', 'B')).as('b').select('a','b')

// Query workaround using .union()
g.V().has('category', 'A').fold().union(unfold(), __.V().has('category', 'B'))
```

Você pode examinar o desempenho das consultas usando a [etapa `executionProfile()` do Gremlin](graph-execution-profile.md).

## <a name="next-steps"></a>Próximas etapas

* Comece a compilar um aplicativo de grafo [usando nossos SDKs](create-graph-dotnet.md) 
* Para saber mais sobre o [suporte para grafo](graph-introduction.md) no Azure Cosmos DB
