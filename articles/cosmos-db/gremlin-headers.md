---
title: Azure Cosmos DB cabeçalhos de resposta do Gremlin
description: Documentação de referência para metadados de resposta do servidor que habilita a solução de problemas adicional
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/03/2019
author: christopheranderson
ms.author: chrande
ms.openlocfilehash: 0442d21aebe1cf577c50d14a5aeff40bd1f6cd9c
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98600531"
---
# <a name="azure-cosmos-db-gremlin-server-response-headers"></a>Azure Cosmos DB cabeçalhos de resposta do servidor Gremlin
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Este artigo aborda os cabeçalhos que o servidor Gremlin do Cosmos DB retorna ao chamador mediante a execução da solicitação. Esses cabeçalhos são úteis para solucionar problemas de desempenho de solicitação, por meio da criação de um aplicativo que se integra nativamente ao serviço Cosmos DB e simplificação do atendimento ao cliente.

Tenha em mente que assumir a dependência desses cabeçalhos você está limitando a portabilidade do seu aplicativo a outras implementações de Gremlin. Em retorno, você está ganhando uma integração mais rígida com Cosmos DB Gremlin. Esses cabeçalhos não são um padrão TinkerPop.

## <a name="headers"></a>Cabeçalhos

| Cabeçalho | Type | Valor de exemplo | Quando incluído | Explicação |
| --- | --- | --- | --- | --- |
| **x-MS-Request-encargos** | double | 11,3243 | Êxito e Falha | Quantidade de produtividade de coleta ou banco de dados consumida em [unidades de solicitação (ru/s ou RUs)](request-units.md) para uma mensagem de resposta parcial. Esse cabeçalho está presente em cada continuação para solicitações que têm várias partes. Ele reflete a cobrança de uma parte de resposta específica. Somente para solicitações que consistem em uma única parte de resposta, esse cabeçalho corresponde ao custo total de passagem. No entanto, para a maioria das passagens complexas, esse valor representa um custo parcial. |
| **x-MS-total-solicitação-encargo** | double | 423,987 | Êxito e Falha | Quantidade de produtividade de coleta ou banco de dados consumida em [unidades de solicitação (ru/s ou RUs)](request-units.md) para toda a solicitação. Esse cabeçalho está presente em cada continuação para solicitações que têm várias partes. Indica a cobrança cumulativa desde o início da solicitação. O valor desse cabeçalho na última parte indica o encargo de solicitação completo. |
| **x-MS-Server-time-MS** | double | 13.75 | Êxito e Falha | Esse cabeçalho é incluído para fins de solução de problemas de latência. Indica a quantidade de tempo, em milissegundos, que Cosmos DB servidor Gremlin levou para executar e produzir uma mensagem de resposta parcial. Usar o valor desse cabeçalho e compará-lo a aplicativos de latência de solicitação geral pode calcular a sobrecarga de latência de rede. |
| **x-MS-total-Server-time-MS** | double | 130,512 | Êxito e Falha | Tempo total, em milissegundos, que Cosmos DB servidor Gremlin levou para executar uma passagem inteira. Esse cabeçalho é incluído em todas as respostas parciais. Representa o tempo de execução cumulativo desde o início da solicitação. A última resposta indica o tempo de execução total. Esse cabeçalho é útil para diferenciar entre o cliente e o servidor como uma fonte de latência. Você pode comparar o tempo de execução de passagem no cliente com o valor desse cabeçalho. |
| **x-MS-status-código** | long | 200 | Êxito e Falha | O cabeçalho indica o motivo interno da conclusão ou término da solicitação. O aplicativo é aconselhado a examinar o valor desse cabeçalho e tomar uma ação corretiva. |
| **x-MS-substatus-código** | long | 1003 | Somente falha | Cosmos DB é um banco de dados multimodelo criado com base na camada de armazenamento unificada. Esse cabeçalho contém informações adicionais sobre o motivo da falha quando a falha ocorre nas camadas inferiores da pilha de alta disponibilidade. É aconselhável que o aplicativo armazene esse cabeçalho e use-o ao contatar Cosmos DB suporte ao cliente. O valor desse cabeçalho é útil para o engenheiro de Cosmos DB para solução rápida de problemas. |
| **x-MS-Retry-After-MS** | Cadeia de caracteres (TimeSpan) | "00:00:03.9500000" | Somente falha | Esse cabeçalho é uma representação de cadeia de caracteres de um tipo de [TimeSpan](/dotnet/api/system.timespan) do .net. Esse valor só será incluído em solicitações com falha devido à esgotamento da taxa de transferência provisionada. O aplicativo deve reenviar a passagem novamente após o período de tempo instruído. |
| **x-MS-Activity-ID** | Cadeia de caracteres (GUID) | "A9218E01-3A3A-4716-9636-5BD86B056613" | Êxito e Falha | O cabeçalho contém um identificador exclusivo do lado do servidor de uma solicitação. Cada solicitação recebe um identificador exclusivo pelo servidor para fins de acompanhamento. Os aplicativos devem registrar em log os identificadores de atividade retornados pelo servidor para solicitar que os clientes possam contatar o atendimento ao cliente. Cosmos DB equipe de suporte pode encontrar solicitações específicas por esses identificadores em Cosmos DB telemetria de serviço. |

## <a name="status-codes"></a>Códigos de status

Os códigos mais comuns retornados para `x-ms-status-code` o atributo de status pelo servidor estão listados abaixo.

| Status | Explicação |
| --- | --- |
| **401** | A mensagem de erro `"Unauthorized: Invalid credentials provided"` é retornada quando a senha de autenticação não corresponde Cosmos DB chave de conta. Navegue até sua conta do Cosmos DB Gremlin no portal do Azure e confirme se a chave está correta.|
| **404** | Operações simultâneas que tentam excluir e atualizar a mesma borda ou vértice simultaneamente. A mensagem de erro `"Owner resource does not exist"` indica que a coleção ou o banco de dados especificado  está incorreto nos parâmetros de conexão no formato `/dbs/<database name>/colls/<collection or graph name>`.|
| **409** | `"Conflicting request to resource has been attempted. Retry to avoid conflicts."`Isso geralmente acontece quando já existe um vértice ou uma borda com um identificador no gráfico.| 
| **412** | O código de status é complementado com a mensagem de erro `"PreconditionFailedException": One of the specified pre-condition is not met` . Esse erro é um indicativo de uma violação de controle de simultaneidade otimista entre ler uma borda ou um vértice e gravá-lo de volta no repositório após a modificação. As situações mais comuns quando esse erro ocorre são a modificação da propriedade, por exemplo `g.V('identifier').property('name','value')` . O mecanismo de Gremlinia ler o vértice, modificá-lo e escrevê-lo novamente. Se houver outra passagem em execução em paralelo tentando gravar o mesmo vértice ou uma borda, um deles receberá esse erro. O aplicativo deve enviar passagem para o servidor novamente.| 
| **429** | A solicitação foi limitada e deve ser repetida após o valor em **x-ms-retry-after-ms**| 
| **500** | A mensagem de erro que contém `"NotFoundException: Entity with the specified id does not exist in the system."` indica que um banco de dados e/ou coleção foi recriado com o mesmo nome. Este erro desaparecerá dentro de 5 minutos, à medida que a alteração se propaga e invalida os caches em diferentes componentes do Cosmos DB. Para evitar esse problema, use nomes de bancos de dados e coleções exclusivos sempre.| 
| **1000** | Esse código de status é retornado quando o servidor analisa com êxito uma mensagem, mas não foi capaz de executar. Normalmente, isso indica um problema com a consulta.| 
| **1001** | Esse código é retornado quando o servidor conclui a execução de passagem, mas falha ao serializar a resposta de volta para o cliente. Esse erro pode ocorrer quando a passagem gera um resultado complexo, que é muito grande ou não está de acordo com a especificação do protocolo TinkerPop. O aplicativo deve simplificar a passagem quando encontrar esse erro. | 
| **1003** | `"Query exceeded memory limit. Bytes Consumed: XXX, Max: YYY"` é retornado quando a passagem excede o limite de memória permitido. O limite de memória é de **2 GB** por passagem.| 
| **1004** | Esse código de status indica uma solicitação de grafo malformada. A solicitação pode ser malformada quando falha na desserialização; o tipo de não valor está sendo desserializado como tipo de valor ou operação Gremlin sem suporte solicitada. O aplicativo não deve repetir a solicitação porque não será bem-sucedido. | 
| **1.007** | Normalmente, esse código de status é retornado com a mensagem de erro `"Could not process request. Underlying connection has been closed."` . Essa situação pode ocorrer se o driver do cliente tentar usar uma conexão que está sendo fechada pelo servidor. O aplicativo deve repetir a passagem em uma conexão diferente.
| **1.008** | Cosmos DB servidor Gremlin pode encerrar conexões para reequilibrar o tráfego no cluster. Os drivers de cliente devem lidar com essa situação e usar somente conexões dinâmicas para enviar solicitações ao servidor. Ocasionalmente, os drivers de cliente podem não detectar que a conexão foi fechada. Quando o aplicativo encontra um erro, `"Connection is too busy. Please retry after sometime or open more connections."` ele deve repetir a passagem em uma conexão diferente.
| **1.009** | A operação não foi concluída no tempo alocado e foi cancelada pelo servidor. Otimize seus atravessamentos para serem executados rapidamente filtrando vértices ou bordas em cada salto de passagem para restringir o escopo da pesquisa. O padrão de tempo limite de solicitação é de **60 segundos**. |

## <a name="samples"></a>Exemplos

Um aplicativo cliente de exemplo baseado em Gremlin.Net que lê um atributo de status:

```csharp
// Following example reads a status code and total request charge from server response attributes.
// Variable "server" is assumed to be assigned to an instance of a GremlinServer that is connected to Cosmos DB account.
using (GremlinClient client = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
{
  ResultSet<dynamic> responseResultSet = await GremlinClientExtensions.SubmitAsync<dynamic>(client, requestScript: "g.V().count()");
  long statusCode = (long)responseResultSet.StatusAttributes["x-ms-status-code"];
  double totalRequestCharge = (double)responseResultSet.StatusAttributes["x-ms-total-request-charge"];

  // Status code and request charge are logged into application telemetry.
}
```

Um exemplo que demonstra como ler o atributo status do cliente Java Gremlin:

```java
try {
  ResultSet resultSet = this.client.submit("g.addV().property('id', '13')");
  List<Result> results = resultSet.all().get();

  // Process and consume results

} catch (ResponseException re) {
  // Check for known errors that need to be retried or skipped
  if (re.getStatusAttributes().isPresent()) {
    Map<String, Object> attributes = re.getStatusAttributes().get();
    int statusCode = (int) attributes.getOrDefault("x-ms-status-code", -1);

    // Now we can check for specific conditions
    if (statusCode == 409) {
        // Handle conflicting writes
      }
    }

    // Check if we need to delay retry
    if (attributes.containsKey("x-ms-retry-after-ms")) {
      // Read the value of the attribute as is
      String retryAfterTimeSpan = (String) attributes.get("x-ms-retry-after-ms"));

      // Convert the value into actionable duration
            LocalTime locaTime = LocalTime.parse(retryAfterTimeSpan);
            Duration duration = Duration.between(LocalTime.MIN, locaTime);

      // Perform a retry after "duration" interval of time has elapsed
    }
  }
}

```

## <a name="next-steps"></a>Próximas etapas
* [Códigos de status HTTP para Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) 
* [Cabeçalhos de resposta REST Azure Cosmos DB comuns](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers)
* [Requisitos do provedor de driver do TinkerPop Graph]( http://tinkerpop.apache.org/docs/current/dev/provider/#_graph_driver_provider_requirements)
