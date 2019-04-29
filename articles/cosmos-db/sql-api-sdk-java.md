---
title: 'O Azure Cosmos DB: SQL Java API, SDK e recursos'
description: Saiba tudo sobre o SDK e a API Java do SQL, incluindo datas de lançamento, datas de desativação e alterações feitas entre cada versão do SDK Java do SQL do Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 03/13/2019
ms.author: sngun
ms.openlocfilehash: cb8a97a29a14ecd5601495048bcbe1aff748d892
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60626764"
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>SDK do Java do Azure Cosmos DB para a API do SQL: Notas sobre a versão e recursos
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

O Java SDK da API do SQL dá suporte a operações síncronas. Para obter suporte assíncrono, use o [Java SDK assíncrono da API do SQL](sql-api-sdk-async-java.md). 

| |  |
|---|---|
|**Baixe o SDK**|[Maven](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)|
|**Documentação da API**|[Documentação de referência de API Java](/java/api/com.microsoft.azure.documentdb)|
|**Contribuir para o SDK**|[GitHub](https://github.com/Azure/azure-documentdb-java/)|
|**Introdução**|[Introdução ao SDK do Java](sql-api-java-get-started.md)|
|**Tutorial do aplicativo Web**|[Desenvolvimento de aplicativos Web com o Azure Cosmos DB](sql-api-java-application.md)|
|**Tempo de execução mínimo com suporte**|[Java Development Kit (JDK) 7 +](https://aka.ms/azure-jdks)|

## <a name="release-notes"></a>Notas de versão

### <a name="a-name213213"></a><a name="2.1.3"/>2.1.3
* Corrigido um bug em PartitionKey para V2 de Hash.

### <a name="a-name212212"></a><a name="2.1.2"/>2.1.2
* Adicionado suporte para índices compostos.
* Corrigido o bug no Gerenciador de ponto de extremidade global para forçar a atualização.
* Correção do bug para upserts com pré-condições no modo direto.

### <a name="a-name211211"></a><a name="2.1.1"/>2.1.1
* Corrigido o bug no cache de endereço de gateway.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Suporte à gravação de várias regiões adicionado ao modo direto.
* Adicionada compatibilidade com a manipulação IOExceptions geradas como exceções de ServiceUnavailable, de um proxy.
* Corrigido um bug na política de repetição de descoberta do ponto de extremidade.
* Corrigido um bug para garantir que as exceções de ponteiro nulo não sejam geradas no BaseDatabaseAccountConfigurationProvider.
* Correção de bug para garantir que QueryIterator não retorna valores nulos.
* Corrigido um bug para garantir a que PartitionKey grande seja permitida

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Suporte à gravação de várias regiões adicionado ao modo de gateway.

### <a name="a-name11641164"></a><a name="1.16.4"/>1.16.4
* Corrigido um bug nos intervalos de chave de partição de leitura para uma consulta.

### <a name="a-name11631163"></a><a name="1.16.3"/>1.16.3
* Corrigido um bug no tamanho do cabeçalho do token de continuação de configuração no modo DirectHttps.

### <a name="a-name11621162"></a><a name="1.16.2"/>1.16.2
* Adicionada a falha de streaming em suporte.
* Adicionado suporte para metadados personalizados.
* Lógica de tratamento de sessão aprimorada.
* Corrigido um bug no cache de intervalo de chave de partição.
* Corrigido um bug NPE no modo direto.

### <a name="a-name11611161"></a><a name="1.16.1"/>1.16.1
* Adição de suporte para o Índice Exclusivo.
* Adição de suporte para limitação do tamanho do token de continuação nas opções de feed.
* Correção de bug na Serialização JSON (carimbo de data/hora).
* Correção de bug na Serialização JSON (enum).
* Atualização da dependência com.fasterxml.jackson.core:jackson-databind para o 2.9.5.

### <a name="a-name11601160"></a><a name="1.16.0"/>1.16.0
* Melhoria do Pool de Conexões para o Modo Direto.
* Melhoria da Pré-busca para a consulta de partição cruzada não orderby.
* Melhoria da geração de UUID.
* Melhoria da lógica de consistência da Sessão.
* Adição de suporte para multipolígono.
* Adição de suporte para Estatísticas de Intervalo de Chaves de Partição para a Coleção.
* Correção de bug no Suporte a várias regiões.

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
* Melhor desempenho de serialização Json.
* Esta versão do SDK requer a versão mais recente do Emulador do Azure Cosmos DB disponível para fazer o download em https://aka.ms/cosmosdb-emulator.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
* Alterações internas para bibliotecas de amigos da Microsoft.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* Corrigido um problema na leitura de intervalos de chaves de partição única.
* Corrigido um problema na análise do ResourceID que afeta o banco de dados com nomes curtos.
* Corrigido um problema causado pela codificação da chave de partição.

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
* Suporte adicionado para consultas de agregação (COUNT, MIN, MAX, SUM e AVG). Veja [Suporte de agregação](how-to-sql-query.md#Aggregates).
* Adicionado suporte para alteração de feed.
* Adicionado suporte para informações de cota de coleção por meio de RequestOptions.setPopulateQuotaInfo.
* Foi adicionado suporte para o log de script de procedimento armazenado por meio de RequestOptions.setScriptLoggingEnabled.
* Corrigido um bug em que a consulta no modo de DirectHttps pode parar de responder quando encontrar falhas de limitação.
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
* Corrigido um bug na consulta TOP, em que uma exceção NullReference poderia ser gerada.
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
* Adicionado suporte a TTL (tempo de vida) para documentos.

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

## <a name="release-and-retirement-dates"></a>Datas de lançamento e desativação
A Microsoft fornecerá uma notificação pelo menos **12 meses** antes de desativar um SDK, a fim de realizar uma transição tranquila para uma versão mais recente/com suporte.

Os novos recursos, funcionalidades e otimizações são adicionados apenas ao SDK atual. Portanto, recomendamos que você atualize sempre que possível para a versão do SDK mais recente.

Qualquer solicitação feita ao Cosmos DB com o uso de um SDK desativado será rejeitada pelo serviço.

> [!WARNING]
> Todas as versões do SDK do SQL para Java anteriores à versão **1.0.0** foram desativadas em **29 de fevereiro de 2016**.
> 
> 

<br/>

| Versão | Data do lançamento | Data de desativação |
| --- | --- | --- |
| [2.1.3](#2.1.3) |13 de março de 2018 |--- |
| [2.1.2](#2.1.2) |Mar 09, 2018 |--- |
| [2.1.1](#2.1.1) |13 de dezembro de 2018 |--- |
| [2.1.0](#2.1.0) |20 de novembro de 2018 |--- |
| [2.0.0](#2.0.0) |21 de setembro de 2018 |--- |
| [1.16.4](#1.16.4) |10 de setembro de 2018 |--- |
| [1.16.3](#1.16.3) |9 de setembro de 2018 |--- |
| [1.16.2](#1.16.2) |Junho de 29, 2018 |--- |
| [1.16.1](#1.16.1) |16 de maio de 2018 |--- |
| [1.16.0](#1.16.0) |15 de março de 2018 |--- |
| [1.15.0](#1.15.0) |14 de novembro de 2017 |--- |
| [1.14.0](#1.14.0) |28 de outubro de 2017 |--- |
| [1.13.0](#1.13.0) |25 de agosto de 2017 |--- |
| [1.12.0](#1.12.0) |11 de julho de 2017 |--- |
| [1.11.0](#1.11.0) |10 de maio de 2017 |--- |
| [1.10.0](#1.10.0) |11 de março de 2017 |--- |
| [1.9.6](#1.9.6) |21 de fevereiro de 2017 |--- |
| [1.9.5](#1.9.5) |31 de janeiro de 2017 |--- |
| [1.9.4](#1.9.4) |24 de novembro de 2016 |--- |
| [1.9.3](#1.9.3) |30 de outubro de 2016 |--- |
| [1.9.2](#1.9.2) |28 de outubro de 2016 |--- |
| [1.9.1](#1.9.1) |26 de outubro de 2016 |--- |
| [1.9.0](#1.9.0) |03 de outubro de 2016 |--- |
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
| 1.0.1 |12 de maio de 2015 |--- |
| [1.0.0](#1.0.0) |7 de abril de 2015 |--- |
| 0.9.5-prelease |9 de março de 2015 |29 de fevereiro de 2016 |
| 0.9.4-prelease |17 de fevereiro de 2015 |29 de fevereiro de 2016 |
| 0.9.4-prelease |13 de janeiro de 2015 |29 de fevereiro de 2016 |
| 0.9.2-prelease |19 de dezembro de 2014 |29 de fevereiro de 2016 |
| 0.9.1-prelease |19 de dezembro de 2014 |29 de fevereiro de 2016 |
| 0.9.0-prelease |10 de dezembro de 2014 |29 de fevereiro de 2016 |

## <a name="faq"></a>Perguntas frequentes
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Consulte também
Para saber mais sobre o Cosmos DB, consulte a página de serviço do [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

