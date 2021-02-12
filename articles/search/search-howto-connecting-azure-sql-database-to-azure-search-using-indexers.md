---
title: Pesquisar em dados SQL do Azure
titleSuffix: Azure Cognitive Search
description: Importe dados do banco de dados SQL do Azure ou do SQL Instância Gerenciada usando indexadores, para pesquisa de texto completo no Azure Pesquisa Cognitiva. Este artigo aborda conexões, configuração do indexador e ingestão de dados.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/12/2020
ms.openlocfilehash: 04e4801c26b0ac8ef91af0b028d9dc2bb9a3cd1c
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358619"
---
# <a name="connect-to-and-index-azure-sql-content-using-an-azure-cognitive-search-indexer"></a>Conectar e indexar conteúdo SQL do Azure usando um indexador Pesquisa Cognitiva do Azure

Antes de poder consultar um [índice de pesquisa cognitiva do Azure](search-what-is-an-index.md), você deve preenchê-lo com seus dados. Se os dados estiverem no banco de dados SQL do Azure ou no SQL Instância Gerenciada, um **indexador pesquisa cognitiva do Azure para o banco de dados SQL do Azure** (ou o **indexador SQL do Azure** para curto) pode automatizar o processo de indexação, o que significa menos código para escrever e menos infraestrutura para se preocupar.

Este artigo aborda a mecânica de uso de [indexadores](search-indexer-overview.md), mas também descreve os recursos disponíveis somente com o banco de dados SQL do Azure ou o SQL instância gerenciada (por exemplo, controle de alterações integrado). 

Além do banco de dados SQL do Azure e do SQL Instância Gerenciada, o Azure Pesquisa Cognitiva fornece indexadores para [Azure Cosmos DB](search-howto-index-cosmosdb.md), [armazenamento de BLOBs do Azure](search-howto-indexing-azure-blob-storage.md)e [armazenamento de tabelas do Azure](search-howto-indexing-azure-tables.md). Para solicitar suporte para outras fontes de dados, forneça seus comentários sobre o [Fórum de comentários do Azure pesquisa cognitiva](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="indexers-and-data-sources"></a>Indexadores e fontes de dados

Uma **fonte de dados** especifica quais dados indexar, as credenciais para o acesso a dados e as políticas que identificam com eficiência as alterações nos dados (linhas novas, modificadas ou excluídas). Ela é definida como um recurso independente para que possa ser usada por vários indexadores.

Um **indexador** é um recurso que conecta uma fonte de dados individual a um índice de pesquisa de destino. Um indexador é usado para as seguintes finalidades:

* Executar uma cópia única dos dados para preenchimento de um índice.
* Atualizar um índice com as alterações feitas na fonte de dados com base em uma agenda.
* Executar sob demanda para atualização de um índice, conforme necessário.

Um único indexador pode consumir apenas uma tabela ou exibição, mas você poderá criar vários indexadores, se desejar popular vários índices de pesquisa. Para obter mais informações sobre os conceitos, consulte [Operações do indexador: Fluxo de trabalho típico](/rest/api/searchservice/Indexer-operations#typical-workflow).

Você pode definir e configurar um indexador do SQL Azure usando:

* Assistente para Importação de Dados no [portal do Azure](https://portal.azure.com)
* SDK do [.net](/dotnet/api/azure.search.documents.indexes.models.searchindexer) pesquisa cognitiva do Azure
* [API REST](/rest/api/searchservice/indexer-operations) do Azure pesquisa cognitiva

Neste artigo, usaremos a API REST para criar **indexadores** e **fontes de dados**.

## <a name="when-to-use-azure-sql-indexer"></a>Quando usar o Indexador SQL do Azure
O uso do indexador do SQL Azure pode ou não ser apropriado dependendo de vários fatores relacionados aos seus dados. Se os dados atenderem aos requisitos a seguir, você poderá usar o indexador SQL do Azure.

| Critérios | Detalhes |
|----------|---------|
| Os dados são originados de uma única tabela ou exibição | Se os dados estiverem espalhados por várias tabelas, você poderá criar uma única exibição dos dados. No entanto, se você usar uma exibição, não poderá usar a detecção de alteração integrada do SQL Server para atualizar um índice com alterações incrementais. Para obter mais informações, consulte [Capturando linhas alteradas e excluídas](#CaptureChangedRows) abaixo. |
| Os tipos de dados são compatíveis | A maioria dos tipos SQL não tem suporte em um índice de Pesquisa Cognitiva do Azure. Para obter uma lista, consulte [Mapeando tipos de dados](#TypeMapping). |
| A sincronização de dados em tempo real não é necessária | Um indexador pode reindexar a tabela, no máximo, a cada cinco minutos. Se os dados forem alterados com frequência e as alterações precisarem ser refletidas no índice em questão de segundos ou minutos, recomendamos o uso da [API REST](/rest/api/searchservice/AddUpdate-or-Delete-Documents) ou do [SDK do .NET](./search-get-started-dotnet.md) para enviar por push as linhas atualizadas diretamente. |
| A indexação incremental é possível | Se você tiver um conjunto de dados grande e planejar a execução do indexador em um agendamento, o Azure Pesquisa Cognitiva deverá ser capaz de identificar com eficiência as linhas novas, alteradas ou excluídas. A indexação não incremental só será permitida se você estiver indexando sob demanda (não com base em um agendamento) ou menos de 100.000 linhas. Para obter mais informações, consulte [Capturando linhas alteradas e excluídas](#CaptureChangedRows) abaixo. |

> [!NOTE] 
> O Pesquisa Cognitiva do Azure dá suporte apenas à autenticação SQL Server. Se você precisar de suporte para a Autenticação da Senha do Microsoft Azure Active Directory, votar nesta [sugestão do UserVoice](https://feedback.azure.com/forums/263029-azure-search/suggestions/33595465-support-azure-active-directory-password-authentica).

## <a name="create-an-azure-sql-indexer"></a>Criar um Indexador SQL do Azure

1. Crie a fonte de dados:

   ```
    POST https://myservice.search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }
   ```

   A cadeia de conexão pode seguir qualquer um dos formatos abaixo:
    1. Obtenha a cadeia de conexão no [portal do Azure](https://portal.azure.com); use a opção `ADO.NET connection string`.
    1. Uma cadeia de conexão de identidade gerenciada que não inclui uma chave de conta com o seguinte formato: `Initial Catalog|Database=<your database name>;ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Sql/servers/<your SQL Server name>/;Connection Timeout=connection timeout length;` . Para usar essa cadeia de conexão, siga as instruções para configurar [uma conexão de indexador com um banco de dados SQL do Azure usando uma identidade gerenciada](search-howto-managed-identities-sql.md).

2. Crie o índice de Pesquisa Cognitiva de destino do Azure se você ainda não tiver um. Crie um índice usando o [portal](https://portal.azure.com) ou a [API de Criação de Índices](/rest/api/searchservice/Create-Index). Verifique se o esquema do índice de destino é compatível com o esquema da tabela de origem-consulte [mapeamento entre tipos de dados de pesquisa cognitiva do SQL e do Azure](#TypeMapping).

3. Crie o indexador dando um nome a ele e referenciando a fonte de dados e o índice de destino:

   ```
    POST https://myservice.search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }
   ```

Um indexador criado dessa maneira não tem um agenda. Ele é executado automaticamente assim que é criado. Você pode executá-lo novamente a qualquer momento usando uma solicitação **executar indexador** :

```
    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2020-06-30
    api-key: admin-key
```

Você pode personalizar vários aspectos do comportamento do indexador, como tamanho do lote e quantos documentos podem ser ignorados antes de uma execução do indexador falhar. Para obter mais informações, consulte [Criar API do indexador](/rest/api/searchservice/Create-Indexer).

Talvez seja necessário permitir a conexão dos serviços do Azure ao banco de dados. Confira [Conexão a partir do Azure](../azure-sql/database/firewall-configure.md) para obter instruções sobre como fazer isso.

Para monitorar o status do indexador e o histórico de execução (número de itens indexados, falhas etc.), use uma solicitação **status do indexador** :

```
    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2020-06-30
    api-key: admin-key
```

A resposta deve parecer com a seguinte:

```
    {
        "\@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2015-02-21T00:23:24.957Z",
            "endTime":"2015-02-21T00:36:47.752Z",
            "errors":[],
            "itemsProcessed":1599501,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        },
        "executionHistory":
        [
            {
                "status":"success",
                "errorMessage":null,
                "startTime":"2015-02-21T00:23:24.957Z",
                "endTime":"2015-02-21T00:36:47.752Z",
                "errors":[],
                "itemsProcessed":1599501,
                "itemsFailed":0,
                "initialTrackingState":null,
                "finalTrackingState":null
            },
            ... earlier history items
        ]
    }
```

O histórico de execução contém até 50 execuções mais recentes, classificadas em ordem cronológica inversa (de modo que a execução mais recente apareça em primeiro lugar na resposta).
Informações adicionais sobre a resposta podem ser encontradas em [Obter o status do indexador](/rest/api/searchservice/get-indexer-status)

## <a name="run-indexers-on-a-schedule"></a>Executar indexadores de acordo com uma agenda
Você também pode organizar o indexador para que execute periodicamente com base em uma agenda. Para fazer isso, adicione a propriedade **schedule** ao criar ou atualizar o indexador. O exemplo a seguir mostra uma solicitação PUT para atualização do indexador:

```
    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }
```

O parâmetro **interval** é necessário. O intervalo refere-se ao tempo entre o início de duas execuções consecutivas do indexador. O menor intervalo permitido é de cinco minutos, e o maior é de um dia. Ele deve ser formatado como um valor XSD de "dayTimeDuration" (um subconjunto restrito de um [valor de duração ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). O padrão para isso é: `P(nD)(T(nH)(nM))`. Exemplos: `PT15M` para cada 15 minutos, `PT2H` para cada 2 horas.

Para obter mais informações sobre como definir as agendas do indexador, confira [Como agendar indexadores para o Azure Cognitive Search](search-howto-schedule-indexers.md).

<a name="CaptureChangedRows"></a>

## <a name="capture-new-changed-and-deleted-rows"></a>Capturar linhas novas, alteradas e excluídas

O Azure Pesquisa Cognitiva usa a **indexação incremental** para evitar a reindexação de toda a tabela ou exibição toda vez que um indexador é executado. O Azure Pesquisa Cognitiva fornece duas políticas de detecção de alteração para dar suporte à indexação incremental. 

### <a name="sql-integrated-change-tracking-policy"></a>Política de controle integrado de alterações do SQL
Se o banco de dados SQL der suporte ao [controle de alterações](/sql/relational-databases/track-changes/about-change-tracking-sql-server), é recomendável usar a **política de controle de alterações integrada do SQL**. Essa é a política mais eficiente. Além disso, ele permite que o Azure Pesquisa Cognitiva identifique linhas excluídas sem a necessidade de adicionar uma coluna explícita de "exclusão reversível" à tabela.

#### <a name="requirements"></a>Requisitos 

+ Requisitos de versão do banco de dados:
  * O SQL Server 2012 SP3 e posterior, se você estiver usando o SQL Server em VMs do Azure.
  * Banco de dados SQL do Azure ou SQL Instância Gerenciada.
+ Apenas tabelas (sem exibições). 
+ No banco de dados, [habilite o controle de alterações](/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server) na tabela. 
+ Sem chave primária de composição (uma chave primária que contém mais de uma coluna) na tabela.  

#### <a name="usage"></a>Uso

Para usar essa política, crie ou atualize a fonte de dados da seguinte maneira:

```
    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy"
      }
    }
```

Ao usar a política de controle integrado de alterações do SQL, não especifique uma política separada de detecção de exclusão de dados, pois essa política tem suporte interno para identificação de linhas excluídas. No entanto, para que as exclusões sejam detectadas “de forma mágica”, a chave de documento no índice de pesquisa deve ser o mesmo da chave primária na tabela SQL. 

> [!NOTE]  
> Ao usar [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql) para remover um grande número de linhas de uma tabela SQL, o indexador precisa ser [redefinido](/rest/api/searchservice/reset-indexer) para também redefinir o estado de acompanhamento de alterações para coletar exclusões de linhas.

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>Política de detecção de alteração de marca d’água alta

Essa política de detecção de alteração se baseia em uma coluna de “marca d'água alta” que captura a versão ou a hora em que uma linha foi atualizada pela última vez. Se você estiver usando uma exibição, deverá usar uma política de marca d'água alta. A coluna de marca d'água alta deve atender aos requisitos a seguir.

#### <a name="requirements"></a>Requisitos 

* Todas as inserções especificam um valor para a coluna.
* Todas as atualizações de um item também alteram o valor da coluna.
* O valor dessa coluna aumenta com cada inserção ou atualização.
* Consultas com as seguintes cláusulas WHERE e ORDER BY podem ser executadas com eficiência: `WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`

> [!IMPORTANT] 
> É altamente recomendável usar o tipo de dados [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) para a coluna de marca d'água alta. Se qualquer outro tipo de dados for usado, não será possível assegurar a captura, pelo acompanhamento de alterações, de todas as alterações na presença de transações em execução simultaneamente com uma consulta do indexador. Ao usar **rowversion** em uma configuração com réplicas somente leitura, você deve apontar o indexador para a réplica primária. Apenas uma réplica primária pode ser usada para cenários de sincronização de dados.

#### <a name="usage"></a>Uso

Para usar uma política de marca d'água alta, crie ou atualize a fonte de dados da seguinte maneira:

```
    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[a rowversion or last_updated column name]"
      }
    }
```

> [!WARNING]
> Se a tabela de origem não tiver um índice na coluna de marca d' água alta, as consultas usadas pelo indexador do SQL poderão atingir o tempo limite. Em particular, a `ORDER BY [High Water Mark Column]` cláusula requer que um índice seja executado com eficiência quando a tabela contiver muitas linhas.
>
>

<a name="convertHighWaterMarkToRowVersion"></a>

##### <a name="converthighwatermarktorowversion"></a>convertHighWaterMarkToRowVersion

Se você estiver usando um tipo de dados do [Multiversion](/sql/t-sql/data-types/rowversion-transact-sql) para a coluna de marca d' água alta, considere usar a `convertHighWaterMarkToRowVersion` definição de configuração do indexador. `convertHighWaterMarkToRowVersion` faz duas coisas:

* Use o tipo de dados do Subversion para a coluna de marca d' água alta na consulta SQL do indexador. O uso do tipo de dados correto melhora o desempenho da consulta do indexador.
* Subtraia 1 do valor de objectVersion antes da execução da consulta do indexador. Exibições com 1 a muitas junções podem ter linhas com valores de número de réplicas duplicados. Subtrair 1 garante que a consulta do indexador não perca essas linhas.

Para habilitar esse recurso, crie ou atualize o indexador com a seguinte configuração:

```
    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "convertHighWaterMarkToRowVersion" : true } }
    }
```

<a name="queryTimeout"></a>

##### <a name="querytimeout"></a>queryTimeout

Se você encontrar erros de tempo limite, use a definição da configuração do indexador `queryTimeout` para definir o tempo limite da consulta como um valor maior que o tempo limite padrão de 5 minutos. Por exemplo, para definir o tempo limite de 10 minutos, crie ou atualize o indexador com a seguinte configuração:

```
    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }
```

<a name="disableOrderByHighWaterMarkColumn"></a>

##### <a name="disableorderbyhighwatermarkcolumn"></a>disableOrderByHighWaterMarkColumn

Você também pode desabilitar a cláusula `ORDER BY [High Water Mark Column]`. No entanto, isso não é recomendado porque, se a execução do indexador for interrompida por um erro, o indexador deverá processar novamente todas as linhas se ele for executado mais tarde – mesmo se o indexador já tiver processado quase todas as linhas no momento em que foi interrompido. Para desabilitar a cláusula `ORDER BY`, use a configuração `disableOrderByHighWaterMarkColumn` na definição do indexador:  

```
    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }
```

### <a name="soft-delete-column-deletion-detection-policy"></a>Política de detecção de exclusão de coluna por exclusão reversível
Quando as linhas são excluídas da tabela de origem, provavelmente você deseja excluí-las também do índice de pesquisa. Se você usar a política de controle integrado de alterações do SQL, isso será resolvido para você. No entanto, a política de controle de alterações de marca d'água alta não ajuda você com relação às linhas excluídas. O que fazer?

Se as linhas forem fisicamente removidas da tabela, o Azure Pesquisa Cognitiva não terá como inferir a presença de registros que não existem mais.  No entanto, você pode usar a técnica de "exclusão reversível" para excluir logicamente linhas sem removê-las da tabela. Adicione uma coluna à sua tabela ou exiba e marque as linhas como excluídas usando essa coluna.

Ao usar a técnica de exclusão reversível, você pode especificar a política de exclusão reversível da seguinte maneira ao criar ou atualizar a fonte de dados:

```
    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }
```

O **softDeleteMarkerValue** deve ser uma cadeia de caracteres – use a representação de cadeia de caracteres de seu valor real. Por exemplo, se você tiver uma coluna de inteiros na qual as linhas excluídas são marcadas com o valor 1, use `"1"`. Se você tiver uma coluna BIT na qual as linhas excluídas são marcadas com o valor true booliano, use a cadeia de caracteres literal `True` ou `true`, o caso não importa.

<a name="TypeMapping"></a>

## <a name="mapping-between-sql-and-azure-cognitive-search-data-types"></a>Mapeamento entre o SQL e o Azure Pesquisa Cognitiva tipos de dados
| Tipo de dados SQL | Tipos de campos de índice de destino permitidos | Observações |
| --- | --- | --- |
| bit |Edm.Boolean, Edm.String | |
| int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String | |
| bigint |Edm.Int64, Edm.String | |
| real, float |Edm.Double, Edm.String | |
| smallmoney, numérico decimal dinheiro |Edm.String |O Azure Pesquisa Cognitiva não dá suporte à conversão de tipos decimais em EDM. Double porque isso perderia a precisão |
| char, nchar, varchar, nvarchar |Edm.String<br/>Collection(Edm.String) |Uma cadeia de caracteres SQL poderá ser usada para preencher um campo Collection(Edm.String) se a cadeia de caracteres representar uma matriz JSON de cadeias de caracteres: `["red", "white", "blue"]` |
| smalldatetime, datetime, datetime2, date, datetimeoffset |Edm.DateTimeOffset, Edm.String | |
| uniqueidentifer |Edm.String | |
| geografia |Edm.GeographyPoint |Há suporte apenas para instâncias de Geografia do tipo POINT com SRID 4326 (que é o padrão) |
| rowversion |N/D |Colunas de linha-versão não podem ser armazenadas no índice de pesquisa, mas podem ser usadas para o controle de alterações |
| tempo, timespan, binário, varbinário, imagem, xml, geometria, tipos CLR |N/D |Sem suporte |

## <a name="configuration-settings"></a>Definições de configuração
O indexador do SQL expõe várias definições de configuração:

| Configuração | Tipo de dados | Finalidade | Valor padrão |
| --- | --- | --- | --- |
| queryTimeout |string |Define o tempo limite de execução da consulta SQL |5 minutos ("00:05:00") |
| disableOrderByHighWaterMarkColumn |bool |Faz com que a consulta SQL usada pela política de marca d'água alta omita a cláusula ORDER BY. Consulte [Política de marca d'água alta](#HighWaterMarkPolicy) |false |

Essas configurações são usadas no objeto `parameters.configuration` na definição do indexador. Por exemplo, para definir o tempo limite da consulta para 10 minutos, crie ou atualize o indexador com a seguinte configuração:

```
    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }
```

## <a name="faq"></a>Perguntas frequentes

**P: posso usar o indexador do SQL Azure com bancos de dados SQL em execução em VMs de IaaS no Azure?**

Sim. No entanto, você precisa permitir que o serviço de pesquisa se conecte ao banco de dados. Para obter mais informações, consulte [Configurar uma conexão de um indexador pesquisa cognitiva do Azure para SQL Server em uma VM do Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md).

**P: Posso usar o indexador SQL do Azure com bancos de dados SQL em execução local?**

Não diretamente. Não recomendamos nem damos suporte a uma conexão direta, pois isso exigirá a abertura dos bancos de dados ao tráfego da Internet. Os clientes tiveram sucesso com esse cenário usando tecnologias de ponte como o Azure Data Factory. Para obter mais informações, consulte [enviar dados por push a um índice de pesquisa cognitiva do Azure usando Azure data Factory](../data-factory/v1/data-factory-azure-search-connector.md).

**P: posso usar o indexador do SQL Azure com bancos de dados diferentes de SQL Server em execução no IaaS no Azure?**

Não. Não damos suporte a esse cenário, pois não testamos o indexador com nenhum outro banco de dados que não o SQL Server.  

**P: posso criar vários indexadores em execução em um agendamento?**

Sim. No entanto, somente um indexador pode ser executado por vez em um nó. Se você precisar de vários indexadores em execução simultaneamente, considere o aumento de seu serviço de pesquisa para mais de uma unidade de pesquisa.

**P: a execução de um indexador afeta a minha carga de trabalho de consulta?**

Sim. O indexador é executado em um dos nós em seu serviço de pesquisa, e os recursos do nó são compartilhados entre a indexação e o atendimento ao tráfego de consultas e outras solicitações da API. Se você executar uma indexação intensiva e cargas de trabalho de consulta e encontrar uma alta taxa de erros 503 ou tempos de resposta crescentes, considere [escalar verticalmente o serviço de pesquisa](search-capacity-planning.md).

**P: Posso usar uma réplica secundária em um [cluster de failover](../azure-sql/database/auto-failover-group-overview.md) como fonte de dados?**

Depende. Para a indexação completa de uma tabela ou exibição, você pode usar uma réplica secundária. 

Para indexação incremental, o Azure Pesquisa Cognitiva dá suporte a duas políticas de detecção de alteração: controle integrado de alterações do SQL e marca d' água alta.

Em réplicas somente leitura, o banco de dados SQL não dá suporte ao controle de alterações integrado. Portanto, você deve usar a política de Marca D'água Alta. 

Nossa recomendação padrão é usar o tipo de dados rowversion para a coluna de marca d'água alta. No entanto, o uso do timeversion depende da `MIN_ACTIVE_ROWVERSION` função, que não tem suporte em réplicas somente leitura. Portanto, você deve apontar o indexador para uma réplica primária se estiver usando rowversion.

Se você tentar usar rowversion em uma réplica somente leitura, verá o seguinte erro: 

"Não há suporte para o uso de uma coluna do Subversion para o controle de alterações em réplicas de disponibilidade secundárias (somente leitura). Atualize a fonte de fontes e especifique uma conexão com a réplica de disponibilidade primária. A propriedade ' data de atualização ' do banco de dados atual é ' READ_ONLY ' ".

**P: Posso usar uma coluna não rowversion alternativa para o controle de alterações de marca d'água alta?**

Isso não é recomendável. Somente **rowversion** permite a sincronização de dados confiável. No entanto, dependendo da lógica do aplicativo, isso poderá ser seguro se:

+ Você pode garantir que, quando o indexador for executado, não haja transações pendentes na tabela que está sendo indexada (por exemplo, todas as atualizações de tabela acontecem como um lote em um agendamento, e a agenda do indexador Pesquisa Cognitiva do Azure é definida para evitar sobreposição com a agenda de atualização de tabela).  

+ Periodicamente, você fizer uma reindexação completa para selecionar as linhas ausentes.