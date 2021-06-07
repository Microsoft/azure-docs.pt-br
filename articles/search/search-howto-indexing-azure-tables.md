---
title: Pesquisar o conteúdo do armazenamento de tabelas do Azure
titleSuffix: Azure Cognitive Search
description: Saiba como indexar dados armazenados no armazenamento de tabelas do Azure com um indexador Pesquisa Cognitiva do Azure.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/11/2020
ms.openlocfilehash: 2c67cd4d071660da2ca5714623695ca434329263
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91275176"
---
# <a name="how-to-index-tables-from-azure-table-storage-with-azure-cognitive-search"></a>Como indexar tabelas do armazenamento de tabelas do Azure com o Azure Pesquisa Cognitiva

Este artigo mostra como usar os Pesquisa Cognitiva do Azure para indexar dados armazenados no armazenamento de tabelas do Azure.

## <a name="set-up-azure-table-storage-indexing"></a>Indexador do Armazenamento de Tabelas do Azure

Você pode configurar um indexador de armazenamento de Tabela do Azure usando estes recursos:

* [Portal do Azure](https://ms.portal.azure.com)
* [API REST](/rest/api/searchservice/Indexer-operations) do Azure pesquisa cognitiva
* SDK do [.net](/dotnet/api/overview/azure/search) pesquisa cognitiva do Azure

Aqui, demonstraremos o fluxo usando a API REST. 

### <a name="step-1-create-a-datasource"></a>Etapa 1: Criar uma fonte de dados

Um DataSource especifica quais dados indexar, as credenciais necessárias para acessar os dados e as políticas que permitem ao Azure Pesquisa Cognitiva identificar com eficiência as alterações nos dados.

Para a indexação de tabela, a fonte de dados deve ter as seguintes propriedades:

- **nome** é o nome exclusivo da fonte de dados dentro de seu serviço de pesquisa.
- **type** deve ser `azuretable`.
- O parâmetro **credentials** contém a cadeia de conexão da conta de armazenamento. Consulte a seção [Especificar credenciais](#Credentials) para obter detalhes.
- **contêiner** define o nome da tabela e uma consulta opcional.
    - Especifique o nome da tabela usando o parâmetro `name`.
    - Opcionalmente, especifique uma consulta usando o parâmetro `query`. 

> [!IMPORTANT] 
> Sempre que possível, use um filtro em PartitionKey para obter um melhor desempenho. Qualquer outra consulta faz uma verificação de tabela completa, resultando em mau desempenho se usada em grandes tabelas. Consulte a seção [Considerações sobre desempenho](#Performance).


Para criar uma fonte de dados:

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   
```

Para obter mais informações sobre Criar a API da Fonte de Dados, consulte [Criar Fonte de Dados](/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="ways-to-specify-credentials"></a>Maneiras de especificar credenciais ####

Você pode fornecer as credenciais para a tabela de uma das seguintes maneiras: 

- **Cadeia de conexão de identidade gerenciada**: `ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Storage/storageAccounts/<your storage account name>/;` essa cadeia de conexão não requer uma chave de conta, mas você deve seguir as instruções para [Configurar uma conexão com uma conta de armazenamento do Azure usando uma identidade gerenciada](search-howto-managed-identities-storage.md).
- **Cadeia de conexão da conta de armazenamento de acesso completo**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` você pode obter a cadeia de conexão do portal do Azure acessando as chaves de configurações da **folha da conta de armazenamento**  >    >   (para contas de armazenamento clássicas) ou **configurações**  >  **chaves de acesso** (para contas de armazenamento de Azure Resource Manager).
- **Cadeia de conexão de assinatura de acesso compartilhado da conta de armazenamento**: `TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl` a assinatura de acesso compartilhado deve ter as permissões de lista e leitura em contêineres (tabelas neste caso) e objetos (linhas de tabela).
-  **Assinatura de acesso compartilhado de tabela**: `ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r` a assinatura de acesso compartilhado deve ter permissões de consulta (leitura) na tabela.

Para saber mais sobre assinaturas de acesso compartilhado, confira [Uso de assinaturas de acesso compartilhado](../storage/common/storage-sas-overview.md).

> [!NOTE]
> Se você usar credenciais de assinaturas de acesso compartilhado, você precisará atualizar as credenciais de fonte de dados periodicamente com assinaturas renovadas para impedir sua expiração. Se as credenciais de assinatura de acesso compartilhado expirarem, o indexador falha com uma mensagem de erro semelhante a "Credenciais fornecidas na cadeia de conexão são inválidas ou expiraram."  

### <a name="step-2-create-an-index"></a>Etapa 2: Criar um índice
O índice especifica os campos em um documento, os atributos e outras construções que modelam a experiência de pesquisa.

Para criar um índice:

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
          ]
    }
```

Para obter mais informações sobre a criação de índices, consulte [Criar Índice](/rest/api/searchservice/create-index).

### <a name="step-3-create-an-indexer"></a>Etapa 3: Criar um indexador
Um indexador conecta uma fonte de dados a um índice de pesquisa de destino e fornece um agendamento para automatizar a atualização de dados. 

Após o índice e a fonte de dados terem sido criados, será possível criar o indexador:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

Esse indexador é executado a cada duas horas. (O intervalo de agendamento é definido como "PT2H".) Para executar um indexador a cada 30 minutos, defina o intervalo como "PT30M". O intervalo mais curto com suporte é de cinco minutos. O agendamento é opcional; se ele for omitido, um indexador será executado apenas uma vez quando for criado. No entanto, você pode executar um indexador sob demanda a qualquer momento.   

Para obter mais informações sobre Criar a API do Indexador, consulte [Criar Indexador](/rest/api/searchservice/create-indexer).

Para obter mais informações sobre como definir as agendas do indexador, confira [Como agendar indexadores para o Azure Cognitive Search](search-howto-schedule-indexers.md).

## <a name="deal-with-different-field-names"></a>Lidar com nomes de campos diferentes
Algumas vezes, os nomes de campos no índice existente são diferentes dos nomes de propriedades na sua tabela. Você pode usar os mapeamentos de campo para mapear os nomes de propriedade da tabela para os nomes de campo em seu índice de pesquisa. Para saber mais sobre mapeamentos de campo, confira [mapeamentos de campo do indexador de pesquisa cognitiva do Azure ponte das diferenças entre fontes de pesquisa e índices](search-indexer-field-mappings.md).

## <a name="handle-document-keys"></a>Manipular chaves de documento
No Azure Pesquisa Cognitiva, a chave do documento identifica exclusivamente um documento. Cada índice de pesquisa deve ter exatamente um campo de chave do tipo `Edm.String`. O campo de chave é necessário para cada documento adicionado ao índice. (Na verdade, ele é o único campo obrigatório.)

Como as linhas de tabela têm uma chave composta, o Azure Pesquisa Cognitiva gera um campo sintético chamado `Key` que é uma concatenação dos valores de chave de partição e de linha de coluna. Por exemplo, se a PartitionKey de uma linha for `PK1` e a RowKey for `RK1`, o valor do campo `Key` será `PK1RK1`.

> [!NOTE]
> O valor `Key` pode conter caracteres inválidos em chaves de documento, como traços. É possível lidar com caracteres inválidos usando a `base64Encode` [função de mapeamento de campo](search-indexer-field-mappings.md#base64EncodeFunction). Se você fizer isso, lembre-se também de usar a codificação de Base 64 protegida por URL ao transmitir as chaves de documento nas chamadas à API como Pesquisa.
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>Indexação incremental e detecção de exclusão 
Ao configurar um indexador de tabela para ser executado em um agendamento, ele reindexará somente linhas novas ou atualizadas, conforme determinado pelo valor `Timestamp` de uma linha. Você não precisa especificar uma política de detecção de alteração. Indexação incremental é habilitada automaticamente para você.

Para indicar que determinados documentos devem ser removidos do índice, você pode usar uma estratégia de exclusão reversível. Em vez de excluir uma linha, adicione uma propriedade para indicar que ela foi excluída e configure uma política de detecção de exclusão reversível na fonte de dados. Por exemplo, a política a seguir considerará que uma linha foi excluída se esta tiver uma propriedade de metadados `IsDeleted` com o valor `"true"`:

```http
    PUT https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "<query>" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   
```

<a name="Performance"></a>
## <a name="performance-considerations"></a>Considerações sobre o desempenho

Por padrão, o Azure Pesquisa Cognitiva usa o seguinte filtro de consulta: `Timestamp >= HighWaterMarkValue` . Já que as tabelas do Azure não têm um índice secundário no campo `Timestamp`, esse tipo de consulta requer uma verificação completa e, portanto, é lenta para tabelas grandes.


Aqui estão duas abordagens possíveis para melhorar o desempenho de indexação de tabela. Ambas as abordagens dependem do uso de partições de tabela: 

- Se seus dados podem ser naturalmente particionados em vários intervalos de partição, crie uma fonte de dados e um indexador correspondente para cada intervalo de partição. Cada indexador deve processar apenas um intervalo de partição específico, resultando em um melhor desempenho de consulta. Se os dados que precisam ser indexados têm um pequeno número de partições fixas, isso é ainda melhor: nesse caso, cada indexador faz apenas uma verificação de partição. Por exemplo, para criar uma fonte de dados para o processamento de um intervalo de partição com chaves de `000` a `100`, use uma consulta como esta: 
    ```
    "container" : { "name" : "my-table", "query" : "PartitionKey ge '000' and PartitionKey lt '100' " }
    ```

- Se os dados são particionados por hora (por exemplo, se você cria uma nova partição a cada dia ou semana), considere a seguinte abordagem: 
    - Use uma consulta com o seguinte formato: `(PartitionKey ge <TimeStamp>) and (other filters)`. 
    - Monitore o progresso do indexador usando a [API Obter Status do Indexador](/rest/api/searchservice/get-indexer-status) e atualize periodicamente a condição `<TimeStamp>` da consulta com base no valor de marca d'água alta bem-sucedido mais recente. 
    - Com essa abordagem, se você precisar disparar uma reindexação completa, você precisará redefinir a consulta de fonte de dados, além de redefinir o indexador. 


## <a name="help-us-make-azure-cognitive-search-better"></a>Ajude-nos a tornar o Azure Pesquisa Cognitiva melhor
Se você tiver solicitações de recursos ou ideias para aperfeiçoamentos, envie-os por meio do nosso [site UserVoice](https://feedback.azure.com/forums/263029-azure-search/).