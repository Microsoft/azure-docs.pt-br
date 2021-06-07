---
title: 'O Azure Cosmos DB: SQL Java API, SDK e recursos'
description: Saiba tudo sobre o SDK e a API Java do SQL, incluindo datas de lançamento, datas de desativação e alterações feitas entre cada versão do SDK Java do SQL do Azure Cosmos DB.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 12/18/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: f18458d0c2ce857ba71463a14844414e631a73ad
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104595685"
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>SDK do Java do Azure Cosmos DB para a API do SQL: Notas sobre a versão e recursos
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
> [!div class="op_single_selector"]
> * [SDK v3 do .NET](sql-api-sdk-dotnet-standard.md)
> * [SDK do .NET v2](sql-api-sdk-dotnet.md)
> * [SDK v2 do .NET Core](sql-api-sdk-dotnet-core.md)
> * [SDK do Feed de Alterações do .NET v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [SDK do Java v4](sql-api-sdk-java-v4.md)
> * [SDK do Java Assíncrono v2](sql-api-sdk-async-java.md)
> * [SDK do Java Síncrono v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Conector do Spark](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Provedor de recursos REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Executor em massa – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor em massa – Java](sql-api-sdk-bulk-executor-java.md)

Este é o SDK v2 do Java de sincronização do Azure Cosmos DB original para API do SQL compatível com operações síncronas.

> [!IMPORTANT]  
> Esse *não* é o SDK de Java mais recente para Azure Cosmos DB! Considere usar o [SDK do Java v4 do Azure Cosmos DB](sql-api-sdk-java-v4.md) em seu projeto. Para atualizar, siga as instruções no guia [Migrar para SDK do Java v4 do Azure Cosmos DB](migrate-java-v4-sdk.md) e o guia [Reator vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md). 
>

| | Links |
|---|---|
|**Baixe o SDK**|[Maven](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)|
|**Documentação da API**|[Documentação de referência de API Java](/java/api/com.microsoft.azure.documentdb)|
|**Contribuir para o SDK**|[GitHub](https://github.com/Azure/azure-documentdb-java/)|
|**Introdução**|[Introdução ao SDK do Java](./create-sql-api-java.md)|
|**Tutorial do aplicativo Web**|[Desenvolvimento de aplicativos Web com o Azure Cosmos DB](sql-api-java-application.md)|
|**runtime mínimo com suporte**|[Java Development Kit (JDK) 7 +](/java/azure/jdk/)|

## <a name="release-notes"></a>Notas de versão

### <a name="261"></a><a name="2.6.1"></a>2.6.1
* Correção de um bug no tratamento de uma consulta por meio da interoperabilidade de serviço.

### <a name="260"></a><a name="2.6.0"></a>2.6.0
* Adicionado suporte para consultar o feed de alterações a partir do ponto no tempo.

### <a name="251"></a><a name="2.5.1"></a>2.5.1
* Corrige o problema de cache de partição primária na consulta documentcollection.

### <a name="250"></a><a name="2.5.0"></a>2.5.0
* Suporte adicionado para a configuração personalizada de repetição de 449.

### <a name="247"></a><a name="2.4.7"></a>2.4.7
* Corrige o problema de tempo limite do pool de conexões.
* Corrige a atualização do token de autenticação em novas tentativas internas.

### <a name="246"></a><a name="2.4.6"></a>2.4.6
* Marca de política de réplica correta atualizada do lado do cliente em databaseAccount e leituras feitas na configuração de databaseAccount do cache.

### <a name="245"></a><a name="2.4.5"></a>2.4.5
* Evitando nova tentativa com erro de intervalo de chave de partição inválido, se o usuário fornecer pkRangeId.

### <a name="244"></a><a name="2.4.4"></a>2.4.4
* Atualizações otimizadas do cache do intervalo da chave de partição.
* Corrige o cenário em que o SDK não tem a relação de divisão da partição do servidor e resulta na atualização incorreta dos caches de roteamento do lado do cliente.

### <a name="242"></a><a name="2.4.2"></a>2.4.2
* Atualizações otimizadas do cache de coleção.

### <a name="241"></a><a name="2.4.1"></a>2.4.1
* Adicionado suporte para recuperar a mensagem de exceção interna da cadeia de caracteres de diagnóstico de solicitação.

### <a name="240"></a><a name="2.4.0"></a>2.4.0
* Versão introduzida da api no PartitionKeyDefinition.

### <a name="230"></a><a name="2.3.0"></a>2.3.0
* Adicionado suporte de tempo limite separado para o modo direto.

### <a name="223"></a><a name="2.2.3"></a>2.2.3
* Consumindo mensagem de erro nula do serviço e produzindo exceção do cliente do documento.

### <a name="222"></a><a name="2.2.2"></a>2.2.2
* Melhoria na conexão do soquete, adicionando true ao padrão SoKeepAlive.

### <a name="220"></a><a name="2.2.0"></a>2.2.0
* Adicionado suporte à cadeia de caracteres de diagnóstico de solicitação.

### <a name="213"></a><a name="2.1.3"></a>2.1.3
* Corrigido o bug no PartitionKey para Hash V2.

### <a name="212"></a><a name="2.1.2"></a>2.1.2
* Adicionado suporte para índices compostos.
* Corrigido o bug no gerenciador de ponto de extremidade global para forçar a atualização.
* Corrigido o bug para upserts com pré-condições no modo direto.

### <a name="211"></a><a name="2.1.1"></a>2.1.1
* Corrigido o bug no cache do endereço do gateway.

### <a name="210"></a><a name="2.1.0"></a>2.1.0
* Suporte à gravação de várias regiões adicionado ao modo direto.
* Adicionada compatibilidade com a manipulação IOExceptions geradas como exceções de ServiceUnavailable, de um proxy.
* Corrigido um bug na política de repetição de descoberta do ponto de extremidade.
* Corrigido um bug para garantir que as exceções de ponteiro nulo não sejam geradas no BaseDatabaseAccountConfigurationProvider.
* Corrigido um bug para garantir que o QueryIterator não retorne valores nulos.
* Corrigido um bug para garantir a que PartitionKey grande seja permitida

### <a name="200"></a><a name="2.0.0"></a>2.0.0
* Suporte à gravação de várias regiões adicionado ao modo de gateway.

### <a name="1164"></a><a name="1.16.4"></a>1.16.4
* Corrigido um bug nos intervalos de chave de partição de leitura para uma consulta.

### <a name="1163"></a><a name="1.16.3"></a>1.16.3
* Corrigido um bug no tamanho do cabeçalho do token de continuação de configuração no modo DirectHttps.

### <a name="1162"></a><a name="1.16.2"></a>1.16.2
* Adicionada a falha de streaming em suporte.
* Adicionado suporte para metadados personalizados.
* Lógica de tratamento de sessão aprimorada.
* Corrigido um bug no cache de intervalo de chave de partição.
* Corrigido um bug NPE no modo direto.

### <a name="1161"></a><a name="1.16.1"></a>1.16.1
* Adição de suporte para o Índice Exclusivo.
* Adição de suporte para limitação do tamanho do token de continuação nas opções de feed.
* Correção de bug na Serialização JSON (carimbo de data/hora).
* Correção de bug na Serialização JSON (enum).
* Atualização da dependência com.fasterxml.jackson.core:jackson-databind para o 2.9.5.

### <a name="1160"></a><a name="1.16.0"></a>1.16.0
* Melhoria do Pool de Conexões para o Modo Direto.
* Melhoria da Pré-busca para a consulta de partição cruzada não orderby.
* Melhoria da geração de UUID.
* Melhoria da lógica de consistência da Sessão.
* Adição de suporte para multipolígono.
* Adição de suporte para Estatísticas de Intervalo de Chaves de Partição para a Coleção.
* Correção de bug no Suporte a várias regiões.

### <a name="1150"></a><a name="1.15.0"></a>1.15.0
* Melhor desempenho de serialização Json.
* Esta versão do SDK requer a versão mais recente do [Emulador do Azure Cosmos DB](https://aka.ms/cosmosdb-emulator).

### <a name="1140"></a><a name="1.14.0"></a>1.14.0
* Alterações internas para bibliotecas de amigos da Microsoft.

### <a name="1130"></a><a name="1.13.0"></a>1.13.0
* Corrigido um problema na leitura de intervalos de chaves de partição única.
* Corrigido um problema na análise do ResourceID que afeta o banco de dados com nomes curtos.
* Corrigido um problema causado pela codificação da chave de partição.

### <a name="1120"></a><a name="1.12.0"></a>1.12.0
* Correções de bugs críticos para solicitar processamento durante divisões de partição.
* Corrigido um problema com os níveis de consistência Strong e BoundedStaleness.

### <a name="1110"></a><a name="1.11.0"></a>1.11.0
* Adição de suporte a um novo nível de consistência chamado ConsistentPrefix.
* Corrigido um bug na leitura da coleção no modo de sessão.

### <a name="1100"></a><a name="1.10.0"></a>1.10.0
* Suporte habilitado para coleção particionada com 2.500 RU/s e escala em incrementos de 100 RU/s.
* Correção de um bug no assembly nativo que pode causar exceção NullRef em algumas consultas.

### <a name="196"></a><a name="1.9.6"></a>1.9.6
* Corrigido um erro na configuração do mecanismo de consulta que pode causar exceções para consultas no modo de gateway.
* Corrigidos alguns bugs no contêiner de sessão que podem causar uma exceção "Recurso proprietário não encontrado" para solicitações imediatamente após a criação da coleção.

### <a name="195"></a><a name="1.9.5"></a>1.9.5
* Suporte adicionado para consultas de agregação (COUNT, MIN, MAX, SUM e AVG). Veja [Suporte de agregação](sql-query-aggregate-functions.md).
* Adicionado suporte para alteração de feed.
* Adicionado suporte para informações de cota de coleção por meio de RequestOptions.setPopulateQuotaInfo.
* Foi adicionado suporte para o log de script de procedimento armazenado por meio de RequestOptions.setScriptLoggingEnabled.
* Corrigido um bug em que a consulta no modo DirectHttps pode parar de responder ao encontrar falhas de limitação.
* Corrigido um bug no modo de sessão de consistência.
* Corrigido um erro que pode causar a NullReferenceException no HttpContext quando a taxa de solicitação é alta.
* Desempenho aprimorado de modo DirectHttps.

### <a name="194"></a><a name="1.9.4"></a>1.9.4
* Acréscimo de suporte a proxy com base em instância simples do cliente com API ConnectionPolicy.setProxy().
* Acréscimo da API DocumentClient.close() para desligar apropriadamente a instância DocumentClient.
* Melhor desempenho de consulta no modo de conectividade direta, derivando o plano de consulta do assembly nativo em vez do Gateway.
* Defina FAIL_ON_UNKNOWN_PROPERTIES = false para que os usuários não precisem definir JsonIgnoreProperties em seu POJO.
* Registro em log refatorado para usar SLF4J.
* Alguns outros bugs corrigidos no leitor de consistência.

### <a name="193"></a><a name="1.9.3"></a>1.9.3
* Correção de um erro no gerenciamento de conexão para evitar perdas de conexão no modo de conectividade direta.
* Corrigido um bug na consulta TOP, em que uma exceção NullReference poderia ser gerada.
* Desempenho aprimorado por meio da redução do número de chamadas de rede para os caches internos.
* Acréscimo do código de status, ActivityID e URI de Solicitação em DocumentClientException para melhor solução de problemas.

### <a name="192"></a><a name="1.9.2"></a>1.9.2
* Correção de um problema no gerenciamento de conexão para estabilidade.

### <a name="191"></a><a name="1.9.1"></a>1.9.1
* Adicionado suporte para o nível de consistência BoundedStaleness.
* Adicionado suporte para conectividade direta para operações CRUD para coleções particionadas.
* Corrigido um erro na consulta de um banco de dados com SQL.
* Corrigido um erro no cache da sessão em que o token de sessão pode ser definido incorretamente.

### <a name="190"></a><a name="1.9.0"></a>1.9.0
* Adicionado suporte para várias consultas paralelas de partição.
* Adição de suporte a consultas TOP/ORDER BY de coleções particionadas.
* Adicionado suporte para consistência forte.
* Adicionado suporte para solicitações com base em nome ao usar a conectividade direta.
* Corrigido para deixar o ActivityId consistente em todas as tentativas de solicitação.
* Corrigido um erro relacionado ao cache de sessão ao recriar uma coleção com o mesmo nome.
* Adicionado Polygon e LineString DataTypes ao especificar a política de indexação de coleção para consultas espaciais de isolamento geográfico.
* Problemas corrigidos com Java Doc para Java 1.8.

### <a name="181"></a><a name="1.8.1"></a>1.8.1
* Um bug foi corrigido em PartitionKeyDefinitionMap para armazenar as coleções de partição única em cache e para não fazer solicitações extras de chave de partição de busca.
* Um bug foi corrigido para não tentar novamente quando um valor de chave de partição incorreto for fornecido.

### <a name="180"></a><a name="1.8.0"></a>1.8.0
* Suporte adicionado para contas de banco de dados de várias regiões.
* Suporte adicionado para repetição automática em solicitações limitadas, com opções para personalizar o número máximo de repetições e o tempo de espera máximo de repetição.  Consulte RetryOptions e ConnectionPolicy.getRetryOptions().
* IPartitionResolver preterido com base no código de particionamento personalizado. Use coleções particionadas para uma taxa de transferência e armazenamento superiores.

### <a name="171"></a><a name="1.7.1"></a>1.7.1
* Adicionado suporte à política de repetição para limitação.  

### <a name="170"></a><a name="1.7.0"></a>1.7.0
* Adicionado suporte a TTL (tempo de vida) para documentos.

### <a name="160"></a><a name="1.6.0"></a>1.6.0
* Implementação de [coleções particionadas](partitioning-overview.md) e [níveis de desempenho definidos pelo usuário](performance-levels.md).

### <a name="151"></a><a name="1.5.1"></a>1.5.1
* Foi corrigido um bug no HashPartitionResolver para gerar valores de hash em little-endian para ser consistente com outros SDKs.

### <a name="150"></a><a name="1.5.0"></a>1.5.0
* Adicionar resolvedores de hash e intervalo para ajudar com a fragmentação de arquivos em várias partições.

### <a name="140"></a><a name="1.4.0"></a>1.4.0
* Implementar o Upsert. Novos métodos upsertXXX adicionados para dar suporte ao recurso Upsert.
* Implementar o roteamento com base em ID. Nenhuma alteração pública de API, todas as alterações são internas.

### <a name="130"></a><a name="1.3.0"></a>1.3.0
* Versão ignorada para alinhar os números de versão com outros SDKs

### <a name="120"></a><a name="1.2.0"></a>1.2.0
* Oferece suporte ao Índice Geoespacial.
* Valida a propriedade de ID de todos os recursos. As IDs de recursos não podem conter caracteres ?, /, #, \, ou terminar com um espaço.
* Adiciona o novo cabeçalho "andamento de transformação do índice" ao ResourceResponse.

### <a name="110"></a><a name="1.1.0"></a>1.1.0
* Implementa a política de indexação V2

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* SDK DO GA

## <a name="release-and-retirement-dates"></a>Datas de lançamento e desativação

A Microsoft fornecerá uma notificação pelo menos **12 meses** antes de desativar um SDK, a fim de realizar uma transição tranquila para uma versão mais recente/com suporte. Os novos recursos, funcionalidades e otimizações são adicionados apenas ao SDK atual. Portanto, recomendamos que você atualize sempre que possível para a versão do SDK mais recente.

> [!WARNING]
> Depois de 30 de maio de 2020, o Azure Cosmos DB não fará mais correções de bug, adicionará novos recursos e dará suporte às versões 1. x do SDK do Java do Azure Cosmos DB para a API do SQL. Se você preferir não fazer a atualização, as solicitações enviadas da versão 1.x do SDK continuarão a ser atendidas pelo serviço Azure Cosmos DB.
>
> Depois de 29 de fevereiro de 2016, a Azure Cosmos DB não fará mais correções de bugs, adicionará novos recursos e dará suporte às versões 0. x do SDK do Java do Azure Cosmos DB para a API do SQL. Se você preferir não atualizar, as solicitações enviadas da versão 0. x do SDK continuarão a ser servidas pelo serviço de Azure Cosmos DB.


| Versão | Data de lançamento | Data de desativação |
| --- | --- | --- |
| [2.6.1](#2.6.1) |17 de dezembro de 2020 |--- |
| [2.6.0](#2.6.0) |16 de julho de 2020 |--- |
| [2.5.1](#2.5.1) |03 de junho de 2020 |--- |
| [2.5.0](#2.5.0) |12 de maio de 2020 |--- |
| [2.4.7](#2.4.7) |Fev 20, 2020 |--- |
| [2.4.6](#2.4.6) |Jan 24, 2020 |--- |
| [2.4.5](#2.4.5) |Nov 10, 2019 |--- |
| [2.4.4](#2.4.4) |Out 24, 2019 |--- |
| [2.4.2](#2.4.2) |Set 26, 2019 |--- |
| [2.4.1](#2.4.1) |Jul 18, 2019 |--- |
| [2.4.0](#2.4.0) |Mai 04, 2019 |--- |
| [2.3.0](#2.3.0) |Abr 24, 2019 |--- |
| [2.2.3](#2.2.3) |Abr 16, 2019 |--- |
| [2.2.2](#2.2.2) |Abr 05, 2019 |--- |
| [2.2.0](#2.2.0) |Mar 27, 2019 |--- |
| [2.1.3](#2.1.3) |Mar 13, 2019 |--- |
| [2.1.2](#2.1.2) |Mar 09, 2019 |--- |
| [2.1.1](#2.1.1) |Dez 13, 2018 |--- |
| [2.1.0](#2.1.0) |Nov 20, 2018 |--- |
| [2.0.0](#2.0.0) |21 de setembro de 2018 |--- |
| [1.16.4](#1.16.4) |10 de setembro de 2018 |Mai 30, 2020 |
| [1.16.3](#1.16.3) |9 de setembro de 2018 |Mai 30, 2020 |
| [1.16.2](#1.16.2) |29 de junho de 2018 |Mai 30, 2020 |
| [1.16.1](#1.16.1) |16 de maio de 2018 |Mai 30, 2020 |
| [1.16.0](#1.16.0) |15 de março de 2018 |Mai 30, 2020 |
| [1.15.0](#1.15.0) |14 de novembro de 2017 |Mai 30, 2020 |
| [1.14.0](#1.14.0) |28 de outubro de 2017 |Mai 30, 2020 |
| [1.13.0](#1.13.0) |25 de agosto de 2017 |Mai 30, 2020 |
| [1.12.0](#1.12.0) |11 de julho de 2017 |Mai 30, 2020 |
| [1.11.0](#1.11.0) |10 de maio de 2017 |Mai 30, 2020 |
| [1.10.0](#1.10.0) |11 de março de 2017 |Mai 30, 2020 |
| [1.9.6](#1.9.6) |21 de fevereiro de 2017 |Mai 30, 2020 |
| [1.9.5](#1.9.5) |31 de janeiro de 2017 |Mai 30, 2020 |
| [1.9.4](#1.9.4) |24 de novembro de 2016 |Mai 30, 2020 |
| [1.9.3](#1.9.3) |30 de outubro de 2016 |Mai 30, 2020 |
| [1.9.2](#1.9.2) |28 de outubro de 2016 |Mai 30, 2020 |
| [1.9.1](#1.9.1) |26 de outubro de 2016 |Mai 30, 2020 |
| [1.9.0](#1.9.0) |03 de outubro de 2016 |Mai 30, 2020 |
| [1.8.1](#1.8.1) |30 de junho de 2016 |Mai 30, 2020 |
| [1.8.0](#1.8.0) |14 de junho de 2016 |Mai 30, 2020 |
| [1.7.1](#1.7.1) |30 de abril de 2016 |Mai 30, 2020 |
| [1.7.0](#1.7.0) |27 de abril de 2016 |Mai 30, 2020 |
| [1.6.0](#1.6.0) |29 de março de 2016 |Mai 30, 2020 |
| [1.5.1](#1.5.1) |31 de dezembro de 2015 |Mai 30, 2020 |
| [1.5.0](#1.5.0) |4 de dezembro de 2015 |Mai 30, 2020 |
| [1.4.0](#1.4.0) |5 de outubro de 2015 |Mai 30, 2020 |
| [1.3.0](#1.3.0) |5 de outubro de 2015 |Mai 30, 2020 |
| [1.2.0](#1.2.0) |5 de agosto de 2015 |Mai 30, 2020 |
| [1.1.0](#1.1.0) |9 de julho de 2015 |Mai 30, 2020 |
| 1.0.1 |12 de maio de 2015 |Mai 30, 2020 |
| [1.0.0](#1.0.0) |7 de abril de 2015 |Mai 30, 2020 |
| 0.9.5-prelease |9 de março de 2015 |29 de fevereiro de 2016 |
| 0.9.4-prelease |17 de fevereiro de 2015 |29 de fevereiro de 2016 |
| 0.9.4-prelease |13 de janeiro de 2015 |29 de fevereiro de 2016 |
| 0.9.2-prelease |19 de dezembro de 2014 |29 de fevereiro de 2016 |
| 0.9.1-prelease |19 de dezembro de 2014 |29 de fevereiro de 2016 |
| 0.9.0-prelease |10 de dezembro de 2014 |29 de fevereiro de 2016 |

## <a name="faq"></a>Perguntas frequentes
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Confira também
Para saber mais sobre o Cosmos DB, consulte a página de serviço do [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
