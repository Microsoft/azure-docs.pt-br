---
title: 'O Azure Cosmos DB: API do SQL Async Java, SDK e recursos'
description: Saiba tudo sobre o SDK e a API Java Assíncrona SQL, incluindo datas de lançamento, datas de desativação e alterações feitas entre cada versão do SDK Java Assíncrono SQL do Azure Cosmos DB.
author: moderakh
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 07/01/2019
ms.author: moderakh
ms.openlocfilehash: 3cafa4d5aecaa4c8f3863c3269ec02793340e3e6
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509270"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>SDK do Java Async do Azure Cosmos DB para a API do SQL: Notas sobre a versão e recursos
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Feed de alterações do .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Provedor de recursos REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

O SDK Java Assíncrono de API SQL difere do SDK Java de API SQL fornecendo operações assíncronas com o suporte da [biblioteca Netty](https://netty.io/). O [SDK Java de API SQL](sql-api-sdk-java.md) preexistente não oferece suporte a operações assíncronas. 

| |  |
|---|---|
| **Baixe o SDK** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) |
|**Documentação da API** |[Documentação de referência de API Java](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient?view=azure-java-stable) | 
|**Contribuir para o SDK** | [GitHub](https://github.com/Azure/azure-cosmosdb-java) | 
|**Introdução** | [Introdução ao SDK Java Assíncrono](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) | 
|**Exemplo de código** | [GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)| 
| **Dicas de desempenho**| [Leiame do GitHub](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)| 
| **Tempo de execução mínimo com suporte**|[JDK 8](https://aka.ms/azure-jdks) | 

## <a name="release-notes"></a>Notas de versão

### <a name="a-name250250"></a><a name="2.5.0"/>2.5.0
* Modo TCP agora em por padrão
* Métricas de consulta na partição cruzada agora retorna todas as partições
* Forte global agora funciona corretamente
* Failover para consultas não corretamente repetirá para vários mestres
* Dependência impactos para os hotfixes de segurança

### <a name="a-name245245"></a><a name="2.4.5"/>2.4.5
* Correção de bug para suporte a Hash V2

### <a name="a-name243243"></a><a name="2.4.3"/>2.4.3
* Correção de bug para vazamento de recursos em client#close() ([github 88 #](https://github.com/Azure/azure-cosmosdb-java/issues/88)).

### <a name="a-name242242"></a><a name="2.4.2"/>2.4.2
* Continuação adicionado suporte para token para consultas entre partições.

### <a name="a-name241241"></a><a name="2.4.1"/>2.4.1
* Corrigimos alguns bugs no modo direto.
* Log aprimorado no modo direto.
* Gerenciamento aprimorado de conexão.

### <a name="a-name240240"></a><a name="2.4.0"/>2.4.0
* Agora a conectividade de modo direto está GA (Geralmente Disponível). Para obter um exemplo que usa a conectividade de modo direto, consulte o repositório do GitHub [azure-cosmosdb-java](https://github.com/Azure/azure-cosmosdb-java).
* Adicionado suporte para QueryMetrics.
* Alteração das APIs aceitando o java.util.Collection para o qual a ordem é importante para aceitar java.util.List no lugar. Agora o ConnectionPolicy#getPreferredLocations(), JsonSerialization e PartitionKey(.) aceitam listas.

### <a name="a-name240-beta-1240-beta-1"></a><a name="2.4.0-beta-1"/>2.4.0-Beta-1
* Adicionado suporte para conectividade de modo direto.
* Alteração das APIs aceitando o java.util.Collection para o qual a ordem é importante para aceitar java.util.List no lugar.
  Agora o ConnectionPolicy#getPreferredLocations(), JsonSerialization e PartitionKey(.) aceitam listas.
* Correção de um bug de sessão para consulta de documento no modo de gateway.
* Atualização das dependências (netty 0.4.20 [github #79](https://github.com/Azure/azure-cosmosdb-java/issues/79), RxJava 1.3.8).

### <a name="a-name231231"></a><a name="2.3.1"/>2.3.1
* Corrige o tratamento de respostas de consultas muito grandes.
* Corrige a manipulação de tokens de recursos ao instanciar um cliente ([GitHub n. 78](https://github.com/Azure/azure-cosmosdb-java/issues/78)).
* A dependência vulnerável jackson-databind foi atualizada ([GitHub n. 77](https://github.com/Azure/azure-cosmosdb-java/pull/77)).

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* Corrigido um bug de vazamento de recursos.
* Adição de suporte para MultiPolygon
* Suporte adicionado para cabeçalhos personalizados em RequestOptions.

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2
* Corrigido um bug de empacotamento.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Corrigido um bug NPE no caminho de nova tentativa de gravação.
* Corrigido um bug NPE no gerenciamento de ponto de extremidade.
* Dependências vulneráveis atualizadas ([GitHub N.68](https://github.com/Azure/azure-cosmosdb-java/issues/68)).
* Adicionado suporte para o log de rede Netty para solução de problemas.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Adicionado suporte para gravação em várias regiões.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Adicionado suporte para Proxy.
* Adicionado suporte para autorização de token de recurso.
* Corrigido um bug no tratamento de chaves de partição grande ([GitHub N.63](https://github.com/Azure/azure-cosmosdb-java/issues/63)).
* Documentação aprimorada.
* SDK reestruturado em módulos mais granulares.

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* Corrigido um bug para localidades que não são de língua inglesa ([GitHub N.51](https://github.com/Azure/azure-cosmosdb-java/issues/51)).
* Adicionado métodos auxiliares no Recurso de Conflito.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Substituição da dependência org.json por jackson por motivos de desempenho e licenciamento ([GitHub N. 29](https://github.com/Azure/azure-cosmosdb-java/issues/29)).
* Remoção da classe OfferV2 preterida.
* Adição de método de acessador à classe Offer para taxa de transferência de conteúdo.
* Qualquer método no Documentos/Recurso que retorne tipos org.json foram alterados para retornar um tipo de objeto jackson.
* Método getObject(.) de classes que estendem JsonSerializable alterado para retornar um tipo ObjectNode jackson.
* Método getCollection(.) alterado para retornar a coleção de ObjectNode.
* Remoção de construtores das subclasses JsonSerializable com argumento org.json.JSONObject.
* Agora, JsonSerializable.toJson (SerializationFormattingPolicy.Indented) usa dois espaços para recuo.
  
### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2
* Suporte adicionado para Política de Índice Exclusivo.
* Suporte adicionado para limitação do tamanho do token de continuação do resposta em opções de feed.
* Suporte adicionado para a Divisão de Partição na consulta de Partição Cruzada.
* Correção de bug na Serialização de carimbo de data/hora Json ([GitHub N. 32](https://github.com/Azure/azure-cosmosdb-java/issues/32)).
* Correção de bug na serialização enum Json.
* Correção de bug no gerenciamento de documentos de tamanho de 2MB ([GitHub N. 33](https://github.com/Azure/azure-cosmosdb-java/issues/33)).
* Dependência com.fasterxml.jackson.core:jackson-databind atualizado para 2.9.5 devido a um bug ([jackson databind: GitHub N. 1599](https://github.com/FasterXML/jackson-databind/issues/1599))
* Dependência rxjava-extras atualizado para 0.8.0.17 devido a um bug ([rxjava extras: GitHub N. 30](https://github.com/davidmoten/rxjava-extras/issues/30)).
* A descrição de metadados no arquivo pom atualizada para estar embutido com o restante da documentação.
* Aperfeiçoamento de sintaxe ([GitHub N. 41](https://github.com/Azure/azure-cosmosdb-java/issues/41)), ([GitHub N. 40](https://github.com/Azure/azure-cosmosdb-java/issues/40)).

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Suporte de pressão contrária adicionado na consulta.
* Suporte adicionado para ID de intervalo de chaves de partição na consulta.
* Correção para permitir token de continuação maior no cabeçalho de solicitação (bugfix GitHub N. 24).
* Dependência netty atualizada para 4.1.22.Final para garantir que a JVM será desligada após a conclusão do thread principal.
* Correção para evitar transmitir o token de sessão ao ler recursos mestres.
* Mais exemplos adicionados.
* Mais cenários de parâmetros de comparação adicionados.
* Arquivos de cabeçalho de Java fixos para a geração de java doc apropriada.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* SDK de GA com suporte de ponta a ponta para E/S sem bloqueio usando a [biblioteca Netty](https://netty.io/) no modo de gateway. 

## <a name="release-and-retirement-dates"></a>Datas de lançamento e desativação
A Microsoft fornecerá uma notificação pelo menos **12 meses** antes de desativar um SDK, a fim de realizar uma transição tranquila para uma versão mais recente/com suporte.

Novos recursos e funcionalidade e otimizações são adicionados apenas ao SDK atual. Portanto, é recomendável que você sempre atualize para a versão mais recente do SDK o quanto antes.

Qualquer solicitação feita ao Cosmos DB com o uso de um SDK desativado será rejeitada pelo serviço.

<br/>

| Versão | Data do lançamento | Data de desativação |
| --- | --- | --- |
| [2.4.3](#2.4.3) |5 de março de 2019|--- |
| [2.4.2](#2.4.2) |1 de março de 2019|--- |
| [2.4.1](#2.4.1) |20 de fevereiro de 2019|--- |
| [2.4.0](#2.4.0) |8 de fevereiro de 2019|--- |
| [2.4.0-beta-1](#2.4.0-beta-1) |4 de fevereiro de 2019|--- |
| [2.3.1](#2.3.1) |15 de janeiro de 2019|--- |
| [2.3.0](#2.3.0) |29 de novembro 2018|--- |
| [2.2.2](#2.2.2) |8 de Nov, 2018|--- |
| [2.2.1](#2.2.1) |2 de Nov, 2018|--- |
| [2.2.0](#2.2.0) |22 de Setembro, 2018|--- |
| [2.1.0](#2.1.0) |5 de setembro de 2018|--- |
| [2.0.1](#2.0.1) |16 de agosto de 2018|--- |
| [2.0.0](#2.0.0) |20 de junho de 2018|--- |
| [1.0.2](#1.0.2) |18 de maio de 2018|--- |
| [1.0.1](#1.0.1) |20 de abril de 2018|--- |
| [1.0.0](#1.0.0) |27 de fevereiro de 2018|--- |

## <a name="faq"></a>Perguntas frequentes
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Consulte também
Para saber mais sobre o Cosmos DB, consulte a página de serviço do [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

