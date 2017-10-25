---
title: "Operações de indexador (API REST do Serviço da Pesquisa do Azure: 2015-02-28-Preview) | Microsoft Docs"
description: "Operações de indexador (API REST do serviço Azure Search: 2015-02-28-Preview)"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 42b5f85c-8304-4bc7-8e1e-a9c76b8ca25a
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/01/2017
ms.author: eugenesh
ms.openlocfilehash: 356ceb98106d080d8c24dedc3547bee33750156e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="indexer-operations-azure-search-service-rest-api-2015-02-28-preview"></a>Operações de indexador (API REST do serviço Azure Search: 2015-02-28-Preview)
> [!NOTE]
> Este artigo descreve os indexadores na versão [API REST 2015-02-28-Preview](search-api-2015-02-28-preview.md). Esta versão de API adiciona versões de visualização do indexador do Armazenamento de Blobs do Azure com a extração de documentos, o indexador do Armazenamento de Tabelas do Azure e outros aprimoramentos. A API também dá suporte a indexadores GA (disponíveis), incluindo indexadores para o Banco de Dados SQL do Azure, o SQL Server em VMs do Azure e o Azure Cosmos DB.
> 
> 

## <a name="overview"></a>Visão geral
O Azure Search pode se integrar diretamente a algumas fontes de dados comuns, eliminando a necessidade de escrever código para indexar seus dados. Para configurar isso, você pode chamar a API do Azure Search para criar e gerenciar **indexadores** e **fontes de dados**. 

Um **indexador** é um recurso que conecta fontes de dados a índices de pesquisa de destino. Um indexador é usado para as seguintes finalidades: 

* Executar uma cópia única dos dados para preenchimento de um índice.
* Sincronizar um índice com as alterações da fonte de dados segundo uma agenda. A agenda faz parte da definição do indexador.
* Executar sob demanda para atualização de um índice, conforme necessário. 

Um **indexador** é útil quando você deseja atualizações regulares em um índice. Você pode configurar uma agenda interna como parte de uma definição de indexador ou executá-lo em demanda usando [Executar indexador](#RunIndexer). 

Uma **fonte de dados** especifica quais dados precisam ser indexados, as credenciais para acessar os dados e políticas para habilitar o Azure Search a identificar com eficiência as alterações nos dados (como linhas modificadas ou excluídas em uma tabela de banco de dados). Ela é definida como um recurso independente para que possa ser usada por vários indexadores.

Atualmente, há suporte às seguintes fontes de dados:

* **Banco de Dados SQL do Azure** e **SQL Server em VMs do Azure**. Para obter um passo a passo direcionado, confira [este artigo](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md). 
* **Azure Cosmos DB**. Para obter um passo a passo direcionado, confira [este artigo](search-howto-index-documentdb.md). 
* **Armazenamento de Blobs do Azure**, incluindo as seguintes formatos de documentos: PDF, Microsoft Office (DOCX/DOC, XSLX/XLS, PPT/PPTX, MSG), HTML, XML, ZIP e arquivos de texto sem formatação (inclusive JSON). Para obter um passo a passo direcionado, confira [este artigo](search-howto-indexing-azure-blob-storage.md).
* **Armazenamento de Tabelas do Azure**. Para obter um passo a passo direcionado, confira [este artigo](search-howto-indexing-azure-tables.md).

Estamos considerando a adição de suporte a fontes de dados adicionais no futuro. Para nos ajudar a priorizar essas decisões, forneça seus comentários no [Fórum de comentários do Azure Search](http://feedback.azure.com/forums/263029-azure-search).

Veja [Limites de Serviço](search-limits-quotas-capacity.md) para obter os limites máximos relacionados aos recursos do indexador e da fonte de dados.

## <a name="typical-usage-flow"></a>Fluxo de uso típico
Você pode criar e gerenciar índices e fontes de dados por meio de solicitações HTTP simples (POST, GET, PUT, DELETE) em relação a um recurso `data source` ou `indexer` específico.

A configuração indexação automática normalmente é um processo em quatro etapas:

1. Identifique a fonte de dados que contém os dados que precisam ser indexados. Lembre-se de que o Azure Search pode não dar suporte a todos os tipos de dados presentes na fonte de dados Confira [Tipos de dados com suporte](https://msdn.microsoft.com/library/azure/dn798938.aspx) para obter a lista.
2. Crie um índice do Azure Search cujo esquema seja compatível com a fonte de dados
3. Crie uma fonte de dados do Azure Search conforme descrito na seção [Criar fonte de dados](#CreateDataSource).
4. Crie um indexador de pesquisa do Azure conforme descrito em [Criar indexador](#CreateIndexer).

Você deve planejar a criação de um indexador para cada combinação de índice de destino e fonte de dados. Você pode ter vários indexadores gravando no mesmo índice e pode reutilizar a mesma fonte de dados para vários indexadores. No entanto, um indexador só pode consumir uma fonte de dados por vez e pode gravar apenas em um único índice. 

Depois de criar um indexador, você pode recuperar seu status de execução usando a operação [Obter Status do Indexador](#GetIndexerStatus) . Você também pode executar um indexador a qualquer momento (em vez de ou além de executá-lo periodicamente de acordo com uma agenda) usando a operação [Executar Indexador](#RunIndexer) .

<!-- MSDN has 2 art files plus a API topic link list -->


## <a name="create-data-source"></a>Criar fonte de dados
Na Pesquisa do Azure, uma fonte de dados é usada com indexadores, fornecendo as informações de conexão para a atualização de dados ad hoc ou agendada de um índice de destino. Você pode criar uma nova fonte de dados  em um serviço Azure Search usando uma solicitação HTTP POST.

    POST https://[service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Como alternativa, você pode usar PUT e especificar o nome da fonte de dados  no URI. Se a fonte de dados não existir, ela será criada.

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]

> [!NOTE]
> O número máximo de fontes de dados permitidas varia por camada de preços. O serviço gratuito permite até três fontes de dados. O serviço padrão permite 50 fontes de dados. Veja [Limites de Serviço](search-limits-quotas-capacity.md) para obter detalhes.
> 
> 

**Solicitação**

HTTPS é necessário para todas as solicitações de serviço. A solicitação **Criar Fonte de Dados** pode ser criada usando um método POST ou PUT. Ao usar POST, você deve fornecer um nome de fonte de dados no corpo da solicitação, juntamente com a definição de esquema de fonte de dados. Com PUT, o nome da fonte de dados é parte da URL. Se a fonte de dados não existir, ela será criada. Se já existir, ela será atualizada para a nova definição. 

O nome da fonte de dados deve estar em letras minúsculas, começar com uma letra ou número, não conter barras ou pontos e ter menos de 128 caracteres. Depois de iniciar o nome da fonte de dados com uma letra ou número, o restante do nome pode incluir qualquer letra, número e traços, desde que os traços não sejam consecutivos. Consulte [Regras de nomenclatura](https://msdn.microsoft.com/library/azure/dn857353.aspx) para obter detalhes.

A `api-version` é obrigatória. A versão atual é `2015-02-28`.

**Cabeçalhos da solicitação**

A lista a seguir descreve os cabeçalhos de solicitação obrigatórios e opcionais. 

* `Content-Type`: obrigatório. Defina-o como `application/json`
* `api-key`: obrigatório. A `api-key` é usada para autenticar a solicitação para o serviço de pesquisa. É um valor de cadeia de caracteres exclusivo de seu serviço. A solicitação **Criar Fonte de Dados** deve incluir um cabeçalho de `api-key` definido como a chave de administração (em vez de uma chave de consulta). 

Você também precisará do nome de serviço para criar a URL da solicitação. Você pode obter o nome do serviço e a `api-key` por meio do painel de serviço no [Portal do Azure](https://portal.azure.com/). Consulte [Criar um serviço de pesquisa no portal](search-create-service-portal.md) para obter ajuda sobre a navegação na página.

<a name="CreateDataSourceRequestSyntax"></a>
**Sintaxe do Corpo da Solicitação**

O corpo da solicitação contém uma definição de fonte de dados, que inclui o tipo de fonte de dados, credenciais para ler os dados, bem como políticas de detecção de alterações de dados opcionais e detecção de exclusão de dados que são usadas para identificar com eficiência dados alterados ou excluídos na fonte de dados quando usadas com um indexador agendado periodicamente. 

A sintaxe para estruturar a carga da solicitação é indicada a seguir. Uma solicitação de exemplo é fornecida mais adiante neste tópico.

    { 
        "name" : "Required for POST, optional for PUT. The name of the data source",
        "description" : "Optional. Anything you want, or nothing at all",
        "type" : "Required. Must be one of 'azuresql', 'documentdb', 'azureblob', or 'azuretable'",
        "credentials" : { "connectionString" : "Required. Connection string for your data source" },
        "container" : { "name" : "Required. The name of the table, collection, or blob container you wish to index" },
        "dataChangeDetectionPolicy" : { Optional. See below for details }, 
        "dataDeletionDetectionPolicy" : { Optional. See below for details }
    }

A contém as seguintes propriedades: 

* `name`: obrigatório. O nome da fonte de dados. Um nome de fonte de dados deve conter apenas letras minúsculas, dígitos ou traços, não pode começar ou terminar com traços e está limitado a 128 caracteres.
* `description`: uma descrição opcional. 
* `type`: obrigatório. Deve ser um dos tipos de fonte de dados com suporte:
  * `azuresql` ‒ banco de dados do Azure SQL ou SQL Server em máquinas virtuais do Azure
  * `documentdb` – Azure Cosmos DB
  * `azureblob` – Armazenamento de Blobs do Azure
  * `azuretable` - Armazenamento de Tabelas do Azure
* `credentials`:
  * A propriedade `connectionString` obrigatória especifica a cadeia de conexão da fonte de dados. O formato da cadeia de conexão depende do tipo de fonte de dados: 
    * Para o Azure SQL, essa é a cadeia de conexão do SQL Server normal. Se você estiver usando o Portal do Azure para recuperar a cadeia de conexão, use a opção `ADO.NET connection string` .
    * Para o Azure Cosmos DB, a cadeia de conexão deve estar no seguinte formato: `"AccountEndpoint=https://[your account name].documents.azure.com;AccountKey=[your account key];Database=[your database id]"`. Todos os valores são obrigatórios. Você pode encontrá-los no [Portal do Azure](https://portal.azure.com/).  
    * Para o Armazenamento de Blobs e Tabelas do Azure, essa é a cadeia de conexão da conta de armazenamento. O formato é descrito [aqui](https://azure.microsoft.com/documentation/articles/storage-configure-connection-string/). É necessário ter um protocolo de ponto de extremidade HTTPS.  
* `container`, obrigatório: especifica os dados a serem indexados com as propriedades `name` e `query`: 
  * `name`, obrigatório:
    * SQL Azure: especifica a tabela ou a exibição. Você pode usar nomes qualificados pelo esquema, como `[dbo].[mytable]`.
    * DocumentDB: especifica a coleção. 
    * Armazenamento de Blobs do Azure: especifica o contêiner de armazenamento.
    * Armazenamento de Tabelas do Azure: especifica o nome da tabela. 
  * `query`, opcional:
    * DocumentDB: permite especificar uma consulta que nivela um layout de documento JSON arbitrário em um esquema simples que pode ser indexado pelo Azure Search.  
    * Armazenamento de Blobs do Azure: permite que você especifique uma pasta virtual dentro do contêiner de blob. Por exemplo, para o caminho de blob `mycontainer/documents/blob.pdf`, `documents` pode ser usada como a pasta virtual.
    * Armazenamento de Tabelas do Azure: permite que você especifique uma consulta que filtra o conjunto de linhas a serem importadas.
    * SQL Azure: não há suporte para consulta. Se você precisar dessa funcionalidade, vote [nesta sugestão](https://feedback.azure.com/forums/263029-azure-search/suggestions/9893490-support-user-provided-query-in-sql-indexer)
* As propriedades `dataChangeDetectionPolicy` e `dataDeletionDetectionPolicy` opcionais são descritas abaixo.

<a name="DataChangeDetectionPolicies"></a>
**Políticas de Detecção de Alteração de Dados**

A finalidade de uma política de detecção de alteração de dados é identificar de maneira eficaz dados alterados. As políticas com suporte variam com base no tipo de fonte de dados. As seções a seguir descrevem cada política. 

***Política de Detecção de Alteração de Marca d’água Alta*** 

Use essa política quando sua fonte de dados contiver uma coluna ou propriedade que atenda aos seguintes critérios:

* Todas as inserções especificam um valor para a coluna. 
* Todas as atualizações de um item também alteram o valor da coluna. 
* O valor dessa coluna aumenta com cada alteração.
* As consultas que usam uma cláusula de filtro semelhante à `WHERE [High Water Mark Column] > [Current High Water Mark Value]` a seguir podem ser executadas com eficiência.

Por exemplo, ao se usar fontes de dados Azure SQL, uma coluna `rowversion` indexada é o candidato ideal para uso com a política de marca d'água alta. 

Essa política pode ser especificada da seguinte maneira:

    { 
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "[a row version or last_updated column name]" 
    } 

Ao usar fontes de dados do Azure Cosmos DB, é necessário usar a propriedade `_ts` fornecida pelo Azure Cosmos DB. 

Ao usar fontes de dados do Blob do Azure, a Pesquisa do Azure usa automaticamente uma política de detecção de alteração de marca d’água alta baseada no último carimbo de data/hora modificado de um blob; não é preciso especificar essa política por conta própria.   

***Política de Detecção de Alteração de Dados Integrada do SQL***

Se o banco de dados SQL oferece suporte ao [controle de alterações](https://msdn.microsoft.com/library/bb933875.aspx), recomendamos o uso da Política de controle integrado de alterações do SQL. Essa política habilita o controle de alterações mais eficiente e permite que o Azure Search identifique linhas excluídas sem que seja preciso ter uma coluna explícita de "exclusão reversível" no esquema.

O controle integrado de alterações tem suporte a partir das seguintes versões de banco de dados do SQL Server: 

* SQL Server 2008 R2, se você estiver usando o SQL Server em VMs do Azure.
* Banco de Dados SQL do Azure V12, se você estiver usando o Banco de Dados SQL do Azure.  

Ao usar a política de Controle Integrado de Alterações do SQL, não especifique uma política separada de detecção de exclusão de dados, pois essa política tem suporte interno para identificação de linhas excluídas. 

Essa política só pode ser usada com tabelas; ela não pode ser usada com exibições. Você precisa habilitar o controle de alterações para a tabela que está usando antes de poder usar essa política. Confira [Habilitar e desabilitar o controle de alterações](https://msdn.microsoft.com/library/bb964713.aspx) para obter instruções.    

Ao estruturar a solicitação **Criar fonte de dados** , a política de controle integrado de alterações do SQL pode ser especificada da seguinte maneira:

    { 
        "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy" 
    }

<a name="DataDeletionDetectionPolicies"></a>
**Políticas de Detecção de Exclusão de Dados**

A finalidade de uma política de detecção de exclusão de dados é identificar de maneira eficaz dados excluídos. Atualmente, é a única política com suporte à política `Soft Delete`, que permite identificar itens excluídos com base no valor de uma coluna `soft delete` ou propriedade na fonte de dados. Essa política pode ser especificada da seguinte maneira:

    { 
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the column that specifies whether a row was deleted", 
        "softDeleteMarkerValue" : "the value that identifies a row as deleted" 
    }

> [!NOTE]
> Somente colunas com valores de cadeia de caracteres, inteiros ou Boolianos têm suporte. O valor usado como `softDeleteMarkerValue` deve ser uma cadeia de caracteres, mesmo que a coluna correspondente contenha números inteiros ou boolianos. Por exemplo, se o valor que aparece em sua fonte de dados for 1, use `"1"` como o `softDeleteMarkerValue`.    
> 
> 

<a name="CreateDataSourceRequestExamples"></a>
**Exemplos de corpo da solicitação**

Se você pretende usar a fonte de dados com um indexador que é executado em um agendamento, este exemplo mostra como especificar políticas de detecção de alteração e exclusão: 

    { 
        "name" : "asqldatasource",
        "description" : "a description",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" },
        "dataChangeDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy", "highWaterMarkColumnName" : "RowVersion" }, 
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }

Se você pretende usar a fonte de dados para uma cópia única dos dados, as políticas podem ser omitidas:

    { 
        "name" : "asqldatasource",
        "description" : "anything you want, or nothing at all",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" }
    } 

**Resposta**

Para uma solicitação bem-sucedida: "201 Criado". 

<a name="UpdateDataSource"></a>

## <a name="update-data-source"></a>Atualizar Fonte de Dados
Você pode atualizar uma fonte de dados usando uma solicitação HTTP PUT. Especifique o nome da fonte de dados a ser atualizada no URI da solicitação:

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

A `api-version` é obrigatória. A versão atual é `2015-02-28`. [Versões da API do Azure Search](https://msdn.microsoft.com/library/azure/dn864560.aspx) tem detalhes e mais informações sobre versões alternativas.

`api-key` deve ser uma chave de administração (em vez de uma chave de consulta). Consulte a seção de autenticação na [API REST do serviço de pesquisa](https://msdn.microsoft.com/library/azure/dn798935.aspx) para saber mais sobre as chaves. [Criar um serviço de pesquisa no portal](search-create-service-portal.md) explica como obter a URL do serviço e as propriedades de chave usadas na solicitação.

**Solicitação**

A sintaxe do corpo da solicitação é a mesma usada para [Criar solicitações de Fonte de Dados](#CreateDataSourceRequestSyntax).

Algumas propriedades não podem ser atualizadas em uma fonte de dados existente. Por exemplo, você não pode alterar o tipo de fonte de dados existente.  

Se você não quiser alterar a cadeia de conexão de uma fonte de dados existente, poderá especificar o `<unchanged>` literal da cadeia de conexão. Isso é útil em situações onde você precisa atualizar a fonte de dados, mas não tem acesso conveniente à cadeia de conexão, já que são dados confidenciais de segurança.

**Resposta**

Para uma solicitação bem-sucedida: 201 Criado se uma nova fonte de dados for criada e 204 Sem Conteúdo se uma fonte de dados for atualizada.

<a name="ListDataSource"></a>

## <a name="list-data-sources"></a>Listar Fontes de Dados
A operação **Listar Fontes de Dados** retorna uma lista das fontes de dados existentes no momento em seu serviço Azure Search. 

    GET https://[service name].search.windows.net/datasources?api-version=[api-version]
    api-key: [admin key]

A `api-version` é obrigatória. A versão atual é `2015-02-28`. 

`api-key` deve ser uma chave de administração (em vez de uma chave de consulta). Consulte a seção de autenticação na [API REST do serviço de pesquisa](https://msdn.microsoft.com/library/azure/dn798935.aspx) para saber mais sobre as chaves. [Criar um serviço de pesquisa no portal](search-create-service-portal.md) explica como obter a URL do serviço e as propriedades de chave usadas na solicitação.

**Resposta**

Para uma solicitação bem-sucedida: 200 OK.

Aqui está um exemplo de corpo de resposta:

    {
      "value" : [
        {
          "name": "datasource1",
          "type": "azuresql",
          ... other data source properties
        }]
    }

Observe que você pode filtrar a resposta para apenas as propriedades em que está interessado. Por exemplo, se você quiser apenas uma lista de nomes de fonte de dados, use a opção de consulta OData `$select` :

    GET /datasources?api-version=205-02-28&$select=name

Nesse caso, a resposta do exemplo anterior seria exibida da seguinte maneira: 

    {
      "value" : [ { "name": "datasource1" }, ... ]
    }

Essa é uma técnica útil para economizar largura de banda se você tiver muitas fontes de dados em seu serviço de pesquisa.

<a name="GetDataSource"></a>

## <a name="get-data-source"></a>Obter Fonte de Dados
A operação **Obter Fonte de Dados** obtém a definição da fonte de dados do Azure Search.

    GET https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

A `api-version` é obrigatória. A versão atual é `2015-02-28`. 

`api-key` deve ser uma chave de administração (em vez de uma chave de consulta). Consulte a seção de autenticação na [API REST do serviço de pesquisa](https://msdn.microsoft.com/library/azure/dn798935.aspx) para saber mais sobre as chaves. [Criar um serviço de pesquisa no portal](search-create-service-portal.md) explica como obter a URL do serviço e as propriedades de chave usadas na solicitação.

**Resposta**

Código de status: 200 OK é retornado para uma resposta bem-sucedida.

A resposta é semelhante ao exemplos em [Solicitações de exemplo Criar Fonte de Dados](#CreateDataSourceRequestExamples): 

    { 
        "name" : "asqldatasource",
        "description" : "a description",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" },
        "dataChangeDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName" : "RowVersion" }, 
        "dataDeletionDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted", 
            "softDeleteMarkerValue" : "true" }
    }

> [!NOTE]
> Não defina o cabeçalho da solicitação `Accept` como `application/json;odata.metadata=none` ao chamar essa API, pois isso fará com que o atributo `@odata.type` seja omitido da resposta, e você não conseguirá diferenciar as políticas de detecção de alteração de dados e de exclusão de dados de diferentes tipos. 
> 
> 

<a name="DeleteDataSource"></a>

## <a name="delete-data-source"></a>Excluir Fonte de Dados
A operação **Excluir Fonte de Dados** remove uma fonte de dados de seu serviço Azure Search.

    DELETE https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

> [!NOTE]
> Se algum indexador fizer referência à fonte de dados que você está excluindo, a operação de exclusão ainda prosseguirá. No entanto, esses indexadores farão a transição para um estado de erro em sua próxima execução.  
> 
> 

A `api-version` é obrigatória. A versão atual é `2015-02-28`. 

`api-key` deve ser uma chave de administração (em vez de uma chave de consulta). Consulte a seção de autenticação na [API REST do serviço de pesquisa](https://msdn.microsoft.com/library/azure/dn798935.aspx) para saber mais sobre as chaves. [Criar um serviço de pesquisa no portal](search-create-service-portal.md) explica como obter a URL do serviço e as propriedades de chave usadas na solicitação.

**Resposta**

Código de status: 204 Sem Conteúdo é retornado para uma resposta bem-sucedida.

<a name="CreateIndexer"></a>

## <a name="create-indexer"></a>Criar indexador
Você pode criar um novo indexador em um serviço Azure Search usando uma solicitação HTTP POST.

    POST https://[service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Como alternativa, você pode usar PUT e especificar o nome da fonte de dados  no URI. Se a fonte de dados não existir, ela será criada.

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]

> [!NOTE]
> O número máximo de indexadores permitidos varia por tipo de preço. O serviço gratuito permite até três indexadores. O serviço padrão permite 50 indexadores. Veja [Limites de Serviço](search-limits-quotas-capacity.md) para obter detalhes.
> 
> 

A `api-version` é obrigatória. A versão atual é `2015-02-28`. 

`api-key` deve ser uma chave de administração (em vez de uma chave de consulta). Consulte a seção de autenticação na [API REST do serviço de pesquisa](https://msdn.microsoft.com/library/azure/dn798935.aspx) para saber mais sobre as chaves. [Criar um serviço de pesquisa no portal](search-create-service-portal.md) explica como obter a URL do serviço e as propriedades de chave usadas na solicitação.

<a name="CreateIndexerRequestSyntax"></a>
**Sintaxe do Corpo da Solicitação**

O corpo da solicitação contém uma definição de indexador, que especifica a fonte de dados e o índice de destino para indexação, bem como a agenda e parâmetros de indexação opcionais. 

A sintaxe para estruturar a carga da solicitação é indicada a seguir. Uma solicitação de exemplo é fornecida mais adiante neste tópico.

    { 
        "name" : "Required for POST, optional for PUT. The name of the indexer",
        "description" : "Optional. Anything you want, or null",
        "dataSourceName" : "Required. The name of an existing data source",
        "targetIndexName" : "Required. The name of an existing index",
        "schedule" : { Optional. See Indexing Schedule below. },
        "parameters" : { Optional. See Indexing Parameters below. },
        "fieldMappings" : { Optional. See Field Mappings below. },
        "disabled" : Optional boolean value indicating whether the indexer is disabled. False by default.  
    }

**Agenda do indexador**

Um indexador pode, também, especificar uma agenda. Se houver uma agenda, o indexador será executado periodicamente segundo a agenda. A agenda tem os seguintes atributos:

* `interval`: obrigatório. Um valor de duração que especifica o intervalo ou período de execução do indexador. O menor intervalo permitido é de cinco minutos, e o maior é de um dia. Ele deve ser formatado como um valor XSD de "dayTimeDuration" (um subconjunto restrito de um [valor de duração ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). O padrão para isso é: `"P[nD][T[nH][nM]]"`. Exemplos: `PT15M` para cada 15 minutos, `PT2H` para cada 2 horas. 
* `startTime`: obrigatório. Uma data/hora, no horário UTC, quando o indexador deve começar a ser executado. 

**Parâmetros do indexador**

Um indexador pode, opcionalmente, especificar vários parâmetros que afetam seu comportamento. Todos os parâmetros são opcionais.  

* `maxFailedItems` : o número de itens que podem não ser indexados antes que a execução de um indexador seja considerada uma falha. O padrão é 0. Informações sobre itens com falha são retornadas pela operação [Obter Status do Indexador](#GetIndexerStatus) . 
* `maxFailedItemsPerBatch` : o número de itens que podem não ser indexados em cada lote antes que a execução de um indexador seja considerada uma falha. O padrão é 0.
* `base64EncodeKeys`: especifica se as chaves de documento serão ou não codificadas em base 64. O Azure Search impõe restrições em relação aos caracteres que podem estar presentes em uma chave de documento. No entanto, os valores na fonte de dados podem conter caracteres que são inválidos. Se for necessário indexar esses valores como chaves de documento, esse sinalizador poderá ser definido como true. O padrão é `false`.
* `batchSize`: especifica o número de itens lidos da fonte de dados e indexados como um único lote para melhorar o desempenho. O padrão depende do tipo de fonte de dados: 1.000 para o SQL do Azure e o Azure Cosmos DB e 10 para o Armazenamento de Blobs do Azure.

**Mapeamentos de campo**

Você pode usar mapeamentos de campo para mapear um nome de campo na fonte de dados para outro nome de campo no índice de destino. Por exemplo, considere uma tabela de origem com um campo `_id`. O Azure search não permite um nome de campo iniciado com um sublinhado, portanto, o campo deve ser renomeado. Isso pode ser feito usando a propriedade `fieldMappings` do indexador da seguinte maneira: 

    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ] 

Você pode especificar vários mapeamentos de campo: 

    "fieldMappings" : [ 
        { "sourceFieldName" : "_id", "targetFieldName" : "id" },
        { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" },
     ]

Nomes de campos de origem e destino diferenciam maiúsculas de minúsculas.

<a name="FieldMappingFunctions"></a>
***Funções de mapeamento de campo***

Mapeamentos de campo também podem ser usados para transformar os valores de campo de origem usando *funções de mapeamento*.

Apenas uma dessas funções tem suporte: `jsonArrayToStringCollection`. Analisa um campo que contém uma cadeia de caracteres formatada como uma matriz JSON em um campo Collection(Edm.String) no índice de destino. Ele se destina ao uso com o indexador do Azure SQL em particular, pois o SQL não tem um tipo de dados nativo de coleção. Ele pode ser usado da seguinte maneira: 

    "fieldMappings" : [ { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } } ] 

Por exemplo, se o campo de origem contiver a cadeia de caracteres `["red", "white", "blue"]`, o campo de destino do tipo `Collection(Edm.String)` será preenchido com os três valores `"red"`, `"white"` e `"blue"`.

Observe que a propriedade `targetFieldName` é opcional; se omitida, o valor `sourceFieldName` será usado. 

<a name="CreateIndexerRequestExamples"></a>
**Exemplos de corpo da solicitação**

O exemplo a seguir cria um indexador que copia dados da tabela referenciada pela fonte de dados `ordersds` para o índice `orders` em uma agenda que se inicia em 1º de janeiro de 12015 UTC e é executado de hora em hora. Cada invocação do indexador será bem-sucedida se não mais do que cinco itens não forem indexados em cada lote e se não mais do que dez itens não forem indexados no total. 

    {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
    }

**Resposta**

201 Criado para uma solicitação bem-sucedida.

<a name="UpdateIndexer"></a>

## <a name="update-indexer"></a>Atualizar Indexador
Você pode atualizar um indexador usando uma solicitação HTTP PUT. Especifique o nome do indexador a ser atualizado no URI da solicitação:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

A `api-version` é obrigatória. A versão atual é `2015-02-28`. 

`api-key` deve ser uma chave de administração (em vez de uma chave de consulta). Consulte a seção de autenticação na [API REST do serviço de pesquisa](https://msdn.microsoft.com/library/azure/dn798935.aspx) para saber mais sobre as chaves. [Criar um serviço de pesquisa no portal](search-create-service-portal.md) explica como obter a URL do serviço e as propriedades de chave usadas na solicitação.

**Solicitação**

A sintaxe do corpo da solicitação é a mesma usada para [solicitações Criar Indexador](#CreateIndexerRequestSyntax).

**Resposta**

Para uma solicitação bem-sucedida: 201 Criado se um novo indexador for criado e 204 sem Conteúdo se um indexador existente for atualizado.

<a name="ListIndexers"></a>

## <a name="list-indexers"></a>Listar Indexadores
A operação **Listar Indexadores** retorna a lista de indexadores em seu serviço Azure Search. 

    GET https://[service name].search.windows.net/indexers?api-version=[api-version]
    api-key: [admin key]


A `api-version` é obrigatória. A versão de visualização é `2015-02-28-Preview`. [Controle de versão do Azure Search](https://msdn.microsoft.com/library/azure/dn864560.aspx) tem detalhes e mais informações sobre versões alternativas.

`api-key` deve ser uma chave de administração (em vez de uma chave de consulta). Consulte a seção de autenticação na [API REST do serviço de pesquisa](https://msdn.microsoft.com/library/azure/dn798935.aspx) para saber mais sobre as chaves. [Criar um serviço de pesquisa no portal](search-create-service-portal.md) explica como obter a URL do serviço e as propriedades de chave usadas na solicitação.

**Resposta**

Para uma solicitação bem-sucedida: 200 OK.

Aqui está um exemplo de corpo de resposta:

    {
      "value" : [
      {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        ... other indexer properties
      }]
    }

Observe que você pode filtrar a resposta para apenas as propriedades em que está interessado. Por exemplo, se você quiser apenas uma lista de nomes de indexadores, use a opção de consulta OData `$select` :

    GET /indexers?api-version=2014-10-20-Preview&$select=name

Nesse caso, a resposta do exemplo anterior seria exibida da seguinte maneira: 

    {
      "value" : [ { "name": "myindexer" } ]
    }

Essa é uma técnica útil para economizar largura de banda se você tiver muitos indexadores em seu serviço de pesquisa.

<a name="GetIndexer"></a>

## <a name="get-indexer"></a>Obter Indexador
A operação **Obter Indexador** obtém a definição de indexador do Azure Search.

    GET https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

A `api-version` é obrigatória. A versão de visualização é `2015-02-28-Preview`. 

`api-key` deve ser uma chave de administração (em vez de uma chave de consulta). Consulte a seção de autenticação na [API REST do serviço de pesquisa](https://msdn.microsoft.com/library/azure/dn798935.aspx) para saber mais sobre as chaves. [Criar um serviço de pesquisa no portal](search-create-service-portal.md) explica como obter a URL do serviço e as propriedades de chave usadas na solicitação.

**Resposta**

Código de status: 200 OK é retornado para uma resposta bem-sucedida.

A resposta é semelhante aos exemplos em [Solicitações de exemplo criar Indexador](#CreateIndexerRequestExamples): 

    {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
    }


<a name="DeleteIndexer"></a>

## <a name="delete-indexer"></a>Excluir Indexador
A operação **Excluir Indexador** remove um indexador de seu serviço Azure Search.

    DELETE https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

Quando um indexador é excluído, as execuções do indexador em andamento no momento serão executadas até a conclusão, mas outras execuções não serão agendadas. As tentativas de usar um indexador inexistente resultarão no código de status HTTP 404 Não Encontrado. 

A `api-version` é obrigatória. A versão de visualização é `2015-02-28-Preview`. 

`api-key` deve ser uma chave de administração (em vez de uma chave de consulta). Consulte a seção de autenticação na [API REST do serviço de pesquisa](https://msdn.microsoft.com/library/azure/dn798935.aspx) para saber mais sobre as chaves. [Criar um serviço de pesquisa no portal](search-create-service-portal.md) explica como obter a URL do serviço e as propriedades de chave usadas na solicitação.

**Resposta**

Código de status: 204 Sem Conteúdo é retornado para uma resposta bem-sucedida.

<a name="RunIndexer"></a>

## <a name="run-indexer"></a>Executar indexador
Além de ser executado periodicamente segundo uma agenda, o indexador também pode ser invocado sob demanda por meio da operação **Executar Indexador** : 

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [admin key]

A `api-version` é obrigatória. A versão de visualização é `2015-02-28-Preview`. 

`api-key` deve ser uma chave de administração (em vez de uma chave de consulta). Consulte a seção de autenticação na [API REST do serviço de pesquisa](https://msdn.microsoft.com/library/azure/dn798935.aspx) para saber mais sobre as chaves. [Criar um serviço de pesquisa no portal](search-create-service-portal.md) explica como obter a URL do serviço e as propriedades de chave usadas na solicitação.

**Resposta**

Código de status: 202 Aceito é retornado para uma resposta bem-sucedida.

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>Obter Status do Indexador
A operação **Obter Status do Indexador** recupera o status atual e o histórico de execução de um indexador: 

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [admin key]


A `api-version` é obrigatória. A versão de visualização é `2015-02-28-Preview`. 

`api-key` deve ser uma chave de administração (em vez de uma chave de consulta). Consulte a seção de autenticação na [API REST do serviço de pesquisa](https://msdn.microsoft.com/library/azure/dn798935.aspx) para saber mais sobre as chaves. [Criar um serviço de pesquisa no portal](search-create-service-portal.md) explica como obter a URL do serviço e as propriedades de chave usadas na solicitação.

**Resposta**

Código de status: 200 OK para uma resposta bem-sucedida.

O corpo da resposta contém informações sobre o status geral de integridade, a última invocação e o histórico de invocações recentes do indexador (se houver). 

Um exemplo de corpo de resposta tem esta aparência: 

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

**Status do Indexador**

O status do indexador pode ter um dos seguintes valores:

* `running` indica que o indexador tem execução normal. Observe que algumas das execuções do indexador ainda podem estar falhando. Portanto, é uma boa ideia verificar também a propriedade `lastResult`. 
* `error` indica que o indexador encontrou um erro que não pode ser corrigido sem intervenção humana. Por exemplo, as credenciais da fonte de dados podem ter expirado ou o esquema da fonte de dados ou do índice de destino foi alterado de maneira que causa erros. 

**Resultado da Execução do Indexador**

Um resultado de execução do indexador contém informações sobre a execução de um único indexador. O resultado mais recente é mostrado como a propriedade `lastResult` do status do indexador. Outros resultados recentes, caso presentes, serão retornados como a propriedade `executionHistory` do status do indexador. 

O resultado da execução do indexador contém as seguintes propriedades: 

* `status`: o status de uma execução. Consulte [Status de Execução do Indexador](#IndexerExecutionStatus) a seguir para obter detalhes. 
* `errorMessage`: mensagem de erro para uma execução com falha. 
* `startTime`: a hora em UTC quando essa execução foi iniciada.
* `endTime`: a hora em UTC quando essa execução terminou. Esse valor não será definido se a execução ainda estiver em andamento.
* `errors`: uma matriz de erros de nível de item, se houver. Cada entrada contém uma chave de documento (`key` propriedade) e uma mensagem de erro (`errorMessage` propriedade). 
* `itemsProcessed`: o número de itens da fonte de dados (por exemplo, linhas de tabela) que o indexador tentou indexar durante esta execução. 
* `itemsFailed`: o número de itens que falharam durante esta execução.  
* `initialTrackingState`: sempre `null` para a primeira execução do indexador ou se a política de controle de alteração de dados não estiver habilitada na fonte de dados usada. Se essa política estiver habilitada, em execuções subsequentes, esse valor indicará o primeiro valor de controle de alterações (menor) processado por essa execução. 
* `finalTrackingState`: sempre `null` se a política de controle de alteração de dados não estiver habilitada na fonte de dados usada. Caso contrário, indica o último valor de controle de alterações (mais alto) processado com êxito por essa execução. 

<a name="IndexerExecutionStatus"></a>
**Status de Execução do Indexador**

O status de execução do indexador captura o status de uma única execução do indexador. Ele pode ter os seguintes valores:

* `success` indica que a execução do indexador foi concluída com êxito.
* `inProgress` indica que a execução do indexador está em andamento. 
* `transientFailure` indica que a execução de um indexador falhou. Consulte a propriedade `errorMessage` para obter detalhes. A falha pode ou não exigir a intervenção humana para a correção – por exemplo, corrigir uma incompatibilidade de esquema entre a fonte de dados e o índice de destino requer a ação do usuário, ao contrário de um tempo de inatividade temporário da fonte de dados. As invocações do indexador continuarão de acordo com a agenda, se houver. 
* `persistentFailure` indica que o indexador falhou em uma forma que requer intervenção humana. As execuções agendadas do indexador param. Depois de abordar o problema, use Redefinir a API do indexador para reiniciar as execuções agendadas. 
* `reset` indica que o indexador foi redefinido por uma chamada para Redefinir a API do Indexador (veja a seguir). 

<a name="ResetIndexer"></a>

## <a name="reset-indexer"></a>Redefinir Indexador
A operação **Redefinir Indexador** redefine o estado de controle de alterações associado ao indexador. Isso permite disparar a reindexação do zero (por exemplo, se o esquema de fonte de dados for alterado) ou alterar a política de detecção de alteração de dados para uma fonte de dados associada ao indexador.   

    POST https://[service name].search.windows.net/indexers/[indexer name]/reset?api-version=[api-version]
    api-key: [admin key]

A `api-version` é obrigatória. A versão de visualização é `2015-02-28-Preview`. 

`api-key` deve ser uma chave de administração (em vez de uma chave de consulta). Consulte a seção de autenticação na [API REST do serviço de pesquisa](https://msdn.microsoft.com/library/azure/dn798935.aspx) para saber mais sobre as chaves. [Criar um serviço de pesquisa no portal](search-create-service-portal.md) explica como obter a URL do serviço e as propriedades de chave usadas na solicitação.

**Resposta**

Código de status: 204 sem Conteúdo para uma resposta bem-sucedida.

## <a name="mapping-between-sql-data-types-and-azure-search-data-types"></a>Mapeamento entre tipos de dados SQL e tipos de dados do Azure Search
<table style="font-size:12">
<tr>
<td>Tipo de dados SQL</td>    
<td>Tipos de campos de índice de destino permitidos</td>
<td>Observações</td>
</tr>
<tr>
<td>bit</td>
<td>Edm.Boolean, Edm.String</td>
<td></td>
</tr>
<tr>
<td>int, smallint, tinyint</td>
<td>Edm.Int32, Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>bigint</td>
<td>Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>real, float</td>
<td>Edm.Double, Edm.String</td>
<td></td>
</tr>
<tr>
<td>smallmoney, dinheiro<br>decimal<br>numérico
</td>
<td>Edm.String</td>
<td>O Azure Search não dá suporte à conversão de tipos decimais em Edm.Double porque isso causaria a perda de precisão
</td>
</tr>
<tr>
<td>char, nchar, varchar, nvarchar</td>
<td>Edm.String<br/>Collection(Edm.String)</td>
<td>Confira [Funções de mapeamento de campos](#FieldMappingFunctions) neste documento para obter detalhes sobre como transformar uma coluna de cadeia de caracteres em uma coleção(Edm.String)</td>
</tr>
<tr>
<td>smalldatetime, datetime, datetime2, date, datetimeoffset</td>
<td>Edm.DateTimeOffset, Edm.String</td>
<td></td>
</tr>
<tr>
<td>uniqueidentifer</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>geografia</td>
<td>Edm.GeographyPoint</td>
<td>Há suporte apenas para instâncias de Geografia do tipo POINT com SRID 4326 (que é o padrão)</td>
</tr>
<tr>
<td>rowversion</td>
<td>N/D</td>
<td>Colunas de linha-versão não podem ser armazenadas no índice de pesquisa, mas podem ser usadas para o controle de alterações</td>
</tr>
<tr>
<td>tempo, período de tempo<br>binário, varbinary, imagem<br>XML, geometria, tipos CLR</td>
<td>N/D</td>
<td>Sem suporte</td>
</tr>
</table>

## <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>Mapeamento entre tipos de dados JSON e tipos de dados da Pesquisa do Azure
<table style="font-size:12">
<tr>
<td>Tipo de dados JSON</td>    
<td>Tipos de campos de índice de destino permitidos</td>
<td>Observações</td>
</tr>
<tr>
<td>bool</td>
<td>Edm.Boolean, Edm.String</td>
<td></td>
</tr>
<tr>
<td>Números integrais</td>
<td>Edm.Int32, Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>Números de ponto flutuante</td>
<td>Edm.Double, Edm.String</td>
<td></td>
</tr>
<tr>
<td>string</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>Matrizes de tipos primitivos, como [ "a", "b", "c" ]</td>
<td>Collection(Edm.String)</td>
<td></td>
</tr>
<tr>
<td>Cadeias de caracteres que se parecem com datas</td>
<td>Edm.DateTimeOffset, Edm.String</td>
<td></td>
</tr>
<tr>
<td>Objetos de ponto GeoJSON</td>
<td>Edm.GeographyPoint</td>
<td>Pontos GeoJSON são objetos JSON no seguinte formato: {"tipo": "Ponto", "coordenadas": [long, lat]} </td>
</tr>
<tr>
<td>Outros objetos JSON</td>
<td>N/D</td>
<td>Sem suporte; no momento, o Azure Search dá suporte apenas a tipos primitivos e coleções de cadeias de caracteres</td>
</tr>
</table>
