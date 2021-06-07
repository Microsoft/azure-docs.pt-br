---
title: Perguntas frequentes sobre a API Gremlin no Azure Cosmos DB
description: Obtenha respostas para perguntas frequentes sobre a API Gremlin no Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: sngun
ms.openlocfilehash: 0374886482b43eefe6df5e107fd4da371633099d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359769"
---
# <a name="frequently-asked-questions-about-the-gremlin-api-in-azure-cosmos-db"></a>Perguntas frequentes sobre a API Gremlin no Azure Cosmos DB
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Este artigo explica as respostas para algumas perguntas frequentes sobre a API Gremlin no Azure Cosmos DB.

## <a name="how-to-evaluate-the-efficiency-of-gremlin-queries"></a>Como avaliar a eficiência das consultas do Gremlin

A etapa de versão prévia do **executionProfile()** pode ser usada para fornecer uma análise do plano de execução de consulta. Essa etapa precisa ser adicionada ao final de qualquer consulta Gremlin, como ilustrado no exemplo a seguir:

**Exemplo de consulta**

```
g.V('mary').out('knows').executionProfile()
```

**Saída de exemplo**

```json
[
  {
    "gremlin": "g.V('mary').out('knows').executionProfile()",
    "totalTime": 8,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 3,
        "annotations": {
          "percentTime": 37.5
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 62.5
        },
        "counts": {
          "resultCount": 0
        },
        "storeOps": [
          {
            "count": 0,
            "size": 0,
            "time": 0.6
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      }
    ]
  }
]
```

A saída do perfil acima mostra quanto tempo é gasto para obter os objetos de vértice, os objetos de borda e o tamanho do conjunto de dados de trabalho. Isso está relacionado às medidas de custo padrão para as consultas do Azure Cosmos DB.

## <a name="other-frequently-asked-questions"></a>Outras perguntas frequentes

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>Como as RU/s são cobradas ao executar consultas em um banco de dados do gráfico?

Todos os objetos do grafo, vértices e bordas são mostrados como documentos JSON no back-end. Já que uma consulta Gremlin pode modificar um ou muitos objetos do gráfico por vez, o custo associado a ela está diretamente relacionado aos objetos, que são bordas processadas pela consulta. É o mesmo processo que o Azure Cosmos DB usa para todas as outras APIs. Para obter mais informações, consulte [Unidades de Solicitação no Azure Cosmos DB](request-units.md).

O custo da RU é baseado no conjunto de dados de trabalho da passagem, não no conjunto de resultados. Por exemplo, se uma consulta tem por objetivo obter um único vértice como resultado, mas precisa atravessar vários outros objetos no caminho, então, o custo se baseará em todos os objetos do gráfico necessários para calcular o vértice de um resultado.

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>Qual é a escala máxima que um banco de dados de grafo pode ter em Azure Cosmos DB API Gremlin?

O Azure Cosmos DB utiliza o [particionamento horizontal](partitioning-overview.md) para aumentar automaticamente o endereço nos requisitos de armazenamento e da taxa de transferência. A taxa de transferência máxima e a capacidade de armazenamento de uma carga de trabalho são determinadas pelo número de partições associadas a um determinado contêiner. No entanto, um contêiner de API do Gremlin tem um conjunto específico de diretrizes para garantir uma experiência de desempenho adequada em escala. Para obter mais informações sobre particionamento e práticas recomendadas, consulte o [particionamento no artigo do Azure Cosmos DB](partitioning-overview.md).

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>Para o desenvolvimento em C#/.NET, posso usar o pacote Microsoft.Azure.Graphs ou o Gremlin.NET?

A API do Gremlin do Azure Cosmos DB aproveita os drivers de fonte aberta como os principais conectores do serviço. Portanto, a opção recomendada é usar os [drivers compatíveis com o Apache Tinkerpop](https://tinkerpop.apache.org/).

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>Como me proteger contra ataques de injeção usando os drivers do Gremlin?

A maioria dos drivers Tinkerpop Gremlin do Apache nativos permitem a opção de fornecer um dicionário de parâmetros para a execução da consulta. Este é um exemplo de como fazer isso no [Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) e no [Gremlin-Javascript](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/main/app.js).

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>Por que estou recebendo o erro "erro de compilação de consulta Gremlin: não foi possível encontrar nenhum método"?

A API do Gremlin do Azure Cosmos DB implementa um subconjunto da funcionalidade definido na área de superfície do Gremlin. Para obter etapas com suporte e mais informações, confira o artigo [Suporte do Gremlin](gremlin-support.md).

A melhor solução é regravar as etapas necessárias do Gremlin com a funcionalidade compatível, pois todas as etapas essenciais do Gremlin são compatíveis com o Azure Cosmos DB.

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>Por que estou recebendo o "websocketexception: o servidor retornou o código de status ' 200 ' quando o código de status ' 101 ' era esperado"?

Esse erro provavelmente é gerado quando o ponto de extremidade incorreto é usado. O ponto de extremidade que gera esse erro tem o seguinte padrão:

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/`

Este é o ponto de extremidade do documento para o banco de dados do gráfico.  O correto a usar é o Ponto de Extremidade do Gremlin, que tem o seguinte formato:

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>Por que estou recebendo o erro "RequestRateIsTooLarge"?

Esse erro significa que as Unidades de Solicitação alocadas por segundo não são suficientes para atender a consulta. O erro geralmente é visto quando você executa uma consulta que obtém todos os vértices:

```
// Query example:
g.V()
```

Esta consulta tentará recuperar todos os vértices do gráfico. Portanto, o custo será igual a, pelo menos, o número de vértices em termos de RUs. A configuração de RU/s deve ser ajustada para atender a essa consulta.

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>Por que minhas conexões de driver do Gremlin são descartadas com o tempo?

Uma conexão do Gremlin é feita por meio de uma conexão WebSocket. Embora as conexões WebSocket não tenham um tempo específico de duração, a API do Gremlin do Azure Cosmos DB encerrará as conexões ociosas após 30 minutos de inatividade.

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>Por que não consigo usar chamadas de API fluentes nos drivers Gremlin nativos?

As chamadas da API fluentes ainda não têm suporte da API do Gremlin do Azure Cosmos DB. Chamadas à API fluentes exigem um recurso de formatação interno conhecido como suporte do código de bytes que atualmente não é compatível com a API do Gremlin do Azure Cosmos DB. Devido à mesma razão, o driver do JavaScript mais recente do Gremlin também não é suportado atualmente.

## <a name="next-steps"></a>Próximas etapas

* [Suporte ao protocolo Azure Cosmos DB Wire Gremlin](gremlin-support.md)
* Criar, consultar e percorrer um banco de dados de Azure Cosmos DB grafo usando o [console do Gremlin](create-graph-gremlin-console.md)
