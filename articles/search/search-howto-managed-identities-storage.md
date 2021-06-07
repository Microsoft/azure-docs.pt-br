---
title: Configurar uma conexão com uma conta de armazenamento usando uma identidade gerenciada
titleSuffix: Azure Cognitive Search
description: Saiba como configurar uma conexão de indexador para uma conta de armazenamento do Azure usando uma identidade gerenciada
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: f26ca04955dfa854a8ee17b7aa255a6ed991b8df
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94358364"
---
# <a name="set-up-a-connection-to-an-azure-storage-account-using-a-managed-identity"></a>Configurar uma conexão com uma conta de armazenamento do Azure usando uma identidade gerenciada

Esta página descreve como configurar uma conexão de indexador com uma conta de armazenamento do Azure usando uma identidade gerenciada, em vez de fornecer credenciais na cadeia de conexão do objeto da fonte de dados.

Antes de saber mais sobre esse recurso, é recomendável compreender o que é um indexador e como configurá-lo para a fonte de dados. Encontre mais informações nos links a seguir:
* [Visão geral do indexador](search-indexer-overview.md)
* [Indexador de blobs do Azure](search-howto-indexing-azure-blob-storage.md)
* [Indexador do Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
* [Indexador de tabela do Azure](search-howto-indexing-azure-tables.md)

## <a name="set-up-the-connection"></a>Configurar a conexão

### <a name="1---turn-on-system-assigned-managed-identity"></a>1 - Habilitar a identidade gerenciada atribuída ao sistema

Quando uma identidade gerenciada atribuída pelo sistema é habilitada, o Azure cria uma identidade para o serviço de pesquisa que pode ser usada para autenticar outros serviços do Azure no mesmo locatário e assinatura. Você pode usar essa identidade em atribuições do Azure RBAC (controle de acesso baseado em função) que permitem o acesso aos dados durante a indexação.

![Habilitar a identidade gerenciada atribuída ao sistema](./media/search-managed-identities/turn-on-system-assigned-identity.png "Habilitar a identidade gerenciada atribuída ao sistema")

Depois de selecionar **Salvar**, você verá uma ID do objeto que foi atribuída ao serviço de pesquisa.

![ID do objeto](./media/search-managed-identities/system-assigned-identity-object-id.png "ID de objeto")
 
### <a name="2---add-a-role-assignment"></a>2 - Adicionar uma atribuição de função

Nesta etapa, você dará permissão ao seu serviço do Azure Cognitive Search para ler dados da conta de armazenamento.

1. No portal do Azure, navegue até a conta de armazenamento que contém os dados que você deseja indexar.
2. Selecione **Controle de Acesso (IAM)**
3. Selecione **Adicionar** > **Adicionar atribuição de função**

    ![Adicionar atribuição de função](./media/search-managed-identities/add-role-assignment-storage.png "Adicionar atribuição de função")

4. Selecione as funções apropriadas com base no tipo de conta de armazenamento que você deseja indexar:
    1. O armazenamento de BLOBs do Azure requer que você adicione o serviço de pesquisa à função de **leitor de dados de blob de armazenamento** .
    1. Azure Data Lake Storage Gen2 requer que você adicione o serviço de pesquisa à função de **leitor de dados de blob de armazenamento** .
    1. O armazenamento de tabelas do Azure requer que você adicione o serviço de pesquisa à função de **acesso a dados e leitor** .
5.  Deixe **Atribuir acesso a** como **Usuário, grupo ou entidade de serviço do Azure AD**
6.  Pesquise pelo serviço de pesquisa, selecione-o e, em seguida, selecione **Salvar**

    Exemplo para o armazenamento de BLOBs do Azure e Azure Data Lake Storage Gen2:

    ![Adicionar atribuição de função Leitor de Dados do Blob de Armazenamento](./media/search-managed-identities/add-role-assignment-storage-blob-data-reader.png "Adicionar atribuição de função Leitor de Dados do Blob de Armazenamento")

    Exemplo para o armazenamento de tabelas do Azure:

    ![Adicionar a atribuição de função Acesso a Dados e Leitor](./media/search-managed-identities/add-role-assignment-reader-and-data-access.png "Adicionar a atribuição de função Acesso a Dados e Leitor")

### <a name="3---create-the-data-source"></a>3 – Criar a fonte de dados

A [API REST](/rest/api/searchservice/create-data-source), portal do Azure e o [SDK do .net](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) oferecem suporte à cadeia de conexão de identidade gerenciada. Veja abaixo um exemplo de como criar uma fonte de dados para indexar dados de uma conta de armazenamento usando a [API REST](/rest/api/searchservice/create-data-source) e uma cadeia de conexão de identidade gerenciada. O formato da cadeia de conexão de identidade gerenciada é o mesmo para a API REST, o SDK do .NET e o portal do Azure.

Ao indexar de uma conta de armazenamento, a fonte de dados deve ter as seguintes propriedades obrigatórias:

* **name** é o nome exclusivo da fonte de dados dentro de seu serviço de pesquisa.
* **tipo**
    * Armazenamento de blobs do Azure: `azureblob`
    * Armazenamento de tabelas do Azure: `azuretable`
    * Azure Data Lake Storage Gen2: **type** será fornecido quando você se inscrever na visualização usando [este formulário](https://aka.ms/azure-cognitive-search/mi-preview-request).
* **credentials**
    * Ao usar uma identidade gerenciada para autenticar, o formato **credentials** é diferente de quando não usa uma identidade gerenciada. Aqui, você fornecerá um ResourceId sem chave de conta ou senha. O ResourceId deve incluir a ID da assinatura da conta de armazenamento, o grupo de recursos da conta de armazenamento e o nome da conta de armazenamento.
    * Formato da identidade gerenciada: 
        * *ResourceId=/subscriptions/**your subscription ID**/resourceGroups/**your resource group name**/providers/Microsoft.Storage/storageAccounts/**your storage account name**/;*
* **container** especifica um contêiner ou tabela na conta de armazenamento. Por padrão, todos os blobs no contêiner são recuperáveis. Se você quiser apenas blobs de índice em um diretório virtual específico, especifique o diretório usando o parâmetro opcional **query**.

Exemplo de como criar um objeto de fonte de dados de blob usando a [API REST](/rest/api/searchservice/create-data-source):

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name/;" },
    "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
}   
```

### <a name="4---create-the-index"></a>4 – Criar o índice

O índice especifica os campos em um documento, atributos e outras construções que modelam a experiência de pesquisa.

Veja como criar um índice com um campo `content` pesquisável para armazenar o texto extraído dos blobs:   

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }
```

Para obter mais informações sobre a criação de índices, consulte [Criar Índice](/rest/api/searchservice/create-index)

### <a name="5---create-the-indexer"></a>5 - Criar o indexador

Um indexador conecta uma fonte de dados a um índice de pesquisa de destino e fornece um agendamento para automatizar a atualização de dados.

Uma vez que o índice e a fonte de dados forem criados, será possível criar o indexador.

Definição de indexador de exemplo para um indexador de blob:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

Esse indexador será executado a cada duas horas (o intervalo de agendamento é definido como "PT2H"). Para executar um indexador a cada 30 minutos, defina o intervalo para "PT30M". O intervalo mais curto com suporte é de 5 minutos. O agendamento é opcional – se ele for omitido, um indexador será executado apenas uma vez quando for criado. No entanto, você pode executar um indexador sob demanda a qualquer momento.   

Para saber mais sobre a API Criar Indexador, veja [Criar indexador](/rest/api/searchservice/create-indexer).

Para obter mais informações sobre como definir as agendas do indexador, confira [Como agendar indexadores para o Azure Cognitive Search](search-howto-schedule-indexers.md).

## <a name="accessing-secure-data-in-storage-accounts"></a>Acessando dados seguros em contas de armazenamento

As contas de armazenamento do Azure podem ser protegidas ainda mais com firewalls e redes virtuais. Se você quiser indexar o conteúdo de uma conta de armazenamento de BLOBs ou Data Lake conta de armazenamento Gen2 que é protegida usando um firewall ou rede virtual, siga as instruções para [acessar dados em contas de armazenamento com segurança por meio de exceção de serviço confiável](search-indexer-howto-access-trusted-service-exception.md).

## <a name="see-also"></a>Confira também

Saiba mais sobre os indexadores de armazenamento do Azure:

* [Indexador de blobs do Azure](search-howto-indexing-azure-blob-storage.md)
* [Indexador do Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
* [Indexador de tabela do Azure](search-howto-indexing-azure-tables.md)