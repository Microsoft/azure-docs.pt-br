---
title: 'Azure Cosmos DB: API, SDK e recursos em Java do DocumentDB, | Microsoft Docs'
description: "Saiba tudo sobre o SDK e a API Java, incluindo datas de lançamento, datas de desativação e alterações feitas entre cada versão do SDK Java do DocumentDB do Azure Cosmos DB."
services: cosmos-db
documentationcenter: java
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 7861cadf-2a05-471a-9925-0fec0599351b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 07/11/2017
ms.author: khdang
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 15e3f7ef3bfd6b1f61fe6081a378bdb29e0a1aa2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-documentdb-java-sdk-release-notes-and-resources"></a>Azure Cosmos DB: notas de versão e recursos do SDK Java do DocumentDB
> [!div class="op_single_selector"]
> * [.NET](documentdb-sdk-dotnet.md)
> * [Feed de alterações do .NET](documentdb-sdk-dotnet-changefeed.md)
> * [.NET Core](documentdb-sdk-dotnet-core.md)
> * [Node.js](documentdb-sdk-node.md)
> * [Java](documentdb-sdk-java.md)
> * [Python](documentdb-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/documentdb/)
> * [Provedor de recursos REST](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

<table>

<tr><td>**Baixe o SDK**</td><td>[Maven](http://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)</td></tr>

<tr><td>**Documentação da API**</td><td>[Documentação de referência de API Java](/java/api/com.microsoft.azure.documentdb)</td></tr>

<tr><td>**Contribuir para o SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-java/)</td></tr>

<tr><td>**Introdução**</td><td>[Introdução ao SDK do Java](documentdb-java-get-started.md)</td></tr>

<tr><td>**Tutorial do aplicativo Web**</td><td>[Desenvolvimento de aplicativos Web com o Azure Cosmos DB](documentdb-java-application.md)</td></tr>

<tr><td>**Tempo de execução atual com suporte**</td><td>[JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)</td></tr>
</table></br>

## <a name="release-notes"></a>Notas de versão

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* Correções de bugs críticos para solicitar processamento durante divisões de partição.
* Corrigido um problema com os níveis de consistência Strong e BoundedStaleness.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* Adição de suporte a um novo nível de consistência chamado ConsistentPrefix.
* Corrigido um bug na leitura da coleção no modo de sessão.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* Suporte habilitado para coleção particionada com 2.500 RU/s e escala em incrementos de 100 RU/s.
* Correção de um bug no assembly nativo que pode causar exceção NullRef em algumas consultas.

### <a name="a-name196196"></a><a name="1.9.6"/>1.9.6
* Corrigido um erro na configuração do mecanismo de consulta que pode causar exceções para consultas no modo de gateway.
* Corrigidos alguns bugs no contêiner de sessão que podem causar uma exceção "Recurso proprietário não encontrado" para solicitações imediatamente após a criação da coleção.

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* Suporte adicionado para consultas de agregação (COUNT, MIN, MAX, SUM e AVG). Veja [Suporte de agregação](documentdb-sql-query.md#Aggregates).
* Adicionado suporte para alteração de feed.
* Adicionado suporte para informações de cota de coleção por meio de RequestOptions.setPopulateQuotaInfo.
* Foi adicionado suporte para o log de script de procedimento armazenado por meio de RequestOptions.setScriptLoggingEnabled.
* Corrigido um bug em que a consulta no modo de DirectHttps pode travar ao encontrar falhas de limitação.
* Corrigido um bug no modo de sessão de consistência.
* Corrigido um erro que pode causar a NullReferenceException no HttpContext quando a taxa de solicitação é alta.
* Desempenho aprimorado de modo DirectHttps.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* Acréscimo de suporte a proxy com base em instância simples do cliente com API ConnectionPolicy.setProxy().
* Acréscimo da API DocumentClient.close() para desligar apropriadamente a instância DocumentClient.
* Melhor desempenho de consulta no modo de conectividade direta, derivando o plano de consulta do assembly nativo em vez do Gateway.
* Defina FAIL_ON_UNKNOWN_PROPERTIES = false para que os usuários não precisem definir JsonIgnoreProperties em seu POJO.
* Registro em log refatorado para usar SLF4J.
* Alguns outros bugs corrigidos no leitor de consistência.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* Correção de um erro no gerenciamento de conexão para evitar perdas de conexão no modo de conectividade direta.
* Correção de um erro na consulta TOP, na qual pode lançar exceções NullReferenece.
* Desempenho aprimorado por meio da redução do número de chamadas de rede para os caches internos.
* Acréscimo do código de status, ActivityID e URI de Solicitação em DocumentClientException para melhor solução de problemas.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* Correção de um problema no gerenciamento de conexão para estabilidade.

### <a name="a-name191191"></a><a name="1.9.1"/>1.9.1
* Adicionado suporte para o nível de consistência BoundedStaleness.
* Adicionado suporte para conectividade direta para operações CRUD para coleções particionadas.
* Corrigido um erro na consulta de um banco de dados com SQL.
* Corrigido um erro no cache da sessão em que o token de sessão pode ser definido incorretamente.

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* Adicionado suporte para várias consultas paralelas de partição.
* Adição de suporte a consultas TOP/ORDER BY de coleções particionadas.
* Adicionado suporte para consistência forte.
* Adicionado suporte para solicitações com base em nome ao usar a conectividade direta.
* Corrigido para deixar o ActivityId consistente em todas as tentativas de solicitação.
* Corrigido um erro relacionado ao cache de sessão ao recriar uma coleção com o mesmo nome.
* Adicionado Polygon e LineString DataTypes ao especificar a política de indexação de coleção para consultas espaciais de isolamento geográfico.
* Problemas corrigidos com Java Doc para Java 1.8.

### <a name="a-name181181"></a><a name="1.8.1"/>1.8.1
* Um bug foi corrigido em PartitionKeyDefinitionMap para armazenar as coleções de partição única em cache e para não fazer solicitações extras de chave de partição de busca.
* Um bug foi corrigido para não tentar novamente quando um valor de chave de partição incorreto for fornecido.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Suporte adicionado para contas de banco de dados de várias regiões.
* Suporte adicionado para repetição automática em solicitações limitadas, com opções para personalizar o número máximo de repetições e o tempo de espera máximo de repetição.  Consulte RetryOptions e ConnectionPolicy.getRetryOptions().
* IPartitionResolver preterido com base no código de particionamento personalizado. Use coleções particionadas para uma taxa de transferência e armazenamento superiores.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* Adicionado suporte à política de repetição para limitação.  

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Adicionado suporte a TTL (vida útil) para documentos.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* Implementação de [coleções particionadas](partition-data.md) e [níveis de desempenho definidos pelo usuário](performance-levels.md).

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* Foi corrigido um bug no HashPartitionResolver para gerar valores de hash em little-endian para ser consistente com outros SDKs.

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Adicionar resolvedores de hash e intervalo para ajudar com a fragmentação de arquivos em várias partições.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* Implementar o Upsert. Novos métodos upsertXXX adicionados para dar suporte ao recurso Upsert.
* Implementar o roteamento com base em ID. Nenhuma alteração pública de API, todas as alterações são internas.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Versão ignorada para alinhar os números de versão com outros SDKs

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Oferece suporte ao Índice Geoespacial.
* Valida a propriedade de ID de todos os recursos. As IDs de recursos não podem conter caracteres ?, /, #, \, ou terminar com um espaço.
* Adiciona o novo cabeçalho "andamento de transformação do índice" ao ResourceResponse.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Implementa a política de indexação V2

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* SDK DO GA

## <a name="release--retirement-dates"></a>Datas de lançamento e desativação
A Microsoft fornecerá uma notificação pelo menos **12 meses** antes de desativar um SDK, a fim de realizar uma transição tranquila para uma versão mais recente/com suporte.

Os novos recursos, funcionalidades e otimizações são adicionados apenas ao SDK atual. Portanto, recomendamos que você atualize sempre que possível para a versão do SDK mais recente.

Qualquer solicitação feita ao Cosmos DB com o uso de um SDK desativado será rejeitada pelo serviço.

> [!WARNING]
> Todas as versões do SDK do DocumentDB para Java anteriores à versão **1.0.0** foram desativadas em **29 de fevereiro de 2016**.
> 
> 

<br/>

| Versão | Data do lançamento | Data de desativação |
| --- | --- | --- |
| [1.12.0](#1.12.0) |11 de julho de 2017 |--- |
| [1.11.0](#1.11.0) |10 de maio de 2017 |--- |
| [1.10.0](#1.10.0) |11 de março de 2017 |--- |
| [1.9.6](#1.9.6) |21 de fevereiro de 2017 |--- |
| [1.9.5](#1.9.5) |31 de janeiro de 2017 |--- |
| [1.9.4](#1.9.4) |24 de novembro de 2016 |--- |
| [1.9.3](#1.9.3) |30 de outubro de 2016 |--- |
| [1.9.2](#1.9.2) |28 de outubro de 2016 |--- |
| [1.9.1](#1.9.1) |26 de outubro de 2016 |--- |
| [1.9.0](#1.9.0) |3 de outubro de 2016 |--- |
| [1.8.1](#1.8.1) |30 de junho de 2016 |--- |
| [1.8.0](#1.8.0) |14 de junho de 2016 |--- |
| [1.7.1](#1.7.1) |30 de abril de 2016 |--- |
| [1.7.0](#1.7.0) |27 de abril de 2016 |--- |
| [1.6.0](#1.6.0) |29 de março de 2016 |--- |
| [1.5.1](#1.5.1) |31 de dezembro de 2015 |--- |
| [1.5.0](#1.5.0) |4 de dezembro de 2015 |--- |
| [1.4.0](#1.4.0) |5 de outubro de 2015 |--- |
| [1.3.0](#1.3.0) |5 de outubro de 2015 |--- |
| [1.2.0](#1.2.0) |5 de agosto de 2015 |--- |
| [1.1.0](#1.1.0) |9 de julho de 2015 |--- |
| [1.0.1](#1.0.1) |12 de maio de 2015 |--- |
| [1.0.0](#1.0.0) |7 de abril de 2015 |--- |
| 0.9.5-prelease |9 de março de 2015 |29 de fevereiro de 2016 |
| 0.9.4-prelease |17 de fevereiro de 2015 |29 de fevereiro de 2016 |
| 0.9.3-prelease |13 de janeiro de 2015 |29 de fevereiro de 2016 |
| 0.9.2-prelease |19 de dezembro de 2014 |29 de fevereiro de 2016 |
| 0.9.1-prelease |19 de dezembro de 2014 |29 de fevereiro de 2016 |
| 0.9.0-prelease |10 de dezembro de 2014 |29 de fevereiro de 2016 |

## <a name="faq"></a>Perguntas frequentes
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Consulte também
Para saber mais sobre o Cosmos DB, consulte a página de serviço do [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

