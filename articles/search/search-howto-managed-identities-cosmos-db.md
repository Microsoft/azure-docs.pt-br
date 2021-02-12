---
title: Configurar uma conexão com uma conta de Cosmos DB usando uma identidade gerenciada
titleSuffix: Azure Cognitive Search
description: Saiba como configurar uma conexão de indexador para uma conta de Cosmos DB usando uma identidade gerenciada
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 2a1744feedc3e0ffae6cf2cd45cd090a6c2f06d5
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422086"
---
# <a name="set-up-an-indexer-connection-to-a-cosmos-db-database-using-a-managed-identity"></a>Configurar uma conexão de indexador para um banco de dados Cosmos DB usando uma identidade gerenciada

Esta página descreve como configurar uma conexão de indexador a um banco de dados do Cosmos DB do Azure usando uma identidade gerenciada, em vez de fornecer credenciais na cadeia de conexão do objeto da fonte de dados.

Antes de saber mais sobre esse recurso, é recomendável compreender o que é um indexador e como configurá-lo para a fonte de dados. Encontre mais informações nos links a seguir:

* [Visão geral do indexador](search-indexer-overview.md)
* [Indexador de Banco de dados do Azure Cosmos](search-howto-index-cosmosdb.md)

## <a name="set-up-a-connection-using-a-managed-identity"></a>Configurar uma conexão usando uma identidade gerenciada

### <a name="1---turn-on-system-assigned-managed-identity"></a>1 – Habilitar a identidade gerenciada atribuída ao sistema

Quando uma identidade gerenciada atribuída pelo sistema é habilitada, o Azure cria uma identidade para o serviço de pesquisa que pode ser usada para autenticar outros serviços do Azure no mesmo locatário e assinatura. Você pode usar essa identidade em atribuições do Azure RBAC (controle de acesso baseado em função) que permitem o acesso aos dados durante a indexação.

![Habilitar a identidade gerenciada atribuída ao sistema](./media/search-managed-identities/turn-on-system-assigned-identity.png "Ativar a identidade gerenciada atribuída ao sistema")

Depois de selecionar **Salvar** , você verá uma ID do objeto que foi atribuída ao serviço de pesquisa.

![ID do objeto](./media/search-managed-identities/system-assigned-identity-object-id.png "ID de objeto")
 
### <a name="2---add-a-role-assignment"></a>2 – Adicionar uma atribuição de função

Nesta etapa, você dará permissão ao seu serviço do Azure Cognitive Search para ler dados do banco de dados do Cosmos DB.

1. No portal do Azure, navegue até a conta de armazenamento do Cosmos DB que contém os dados que você deseja indexar.
2. Selecione **Controle de Acesso (IAM)**
3. Selecione **Adicionar** > **Adicionar atribuição de função**

    ![Adicionar atribuição de função](./media/search-managed-identities/add-role-assignment-cosmos-db.png "Adicionar atribuição de função")

4. Selecione **Função de Leitor de Conta do Cosmos DB**
5. Deixe **Atribuir acesso a** como **Usuário, grupo ou entidade de serviço do Azure AD**
6. Pesquise pelo serviço de pesquisa, selecione-o e, em seguida, selecione **Salvar**

    ![Adicionar a atribuição de função Acesso a Dados e Leitor](./media/search-managed-identities/add-role-assignment-cosmos-db-account-reader-role.png "Adicionar a atribuição de função Acesso a Dados e Leitor")

### <a name="3---create-the-data-source"></a>3 – Criar a fonte de dados

A [API REST](/rest/api/searchservice/create-data-source), portal do Azure e o [SDK do .net](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) oferecem suporte à cadeia de conexão de identidade gerenciada. Abaixo está um exemplo de como criar uma fonte de dados para indexar dados de Cosmos DB usando a [API REST](/rest/api/searchservice/create-data-source) e uma cadeia de conexão de identidade gerenciada. O formato da cadeia de conexão de identidade gerenciada é o mesmo para a API REST, o SDK do .NET e o portal do Azure.

Ao usar identidades gerenciadas para autenticar, as **credenciais** não incluirão uma chave de conta.

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [Search service admin key]

{
    "name": "cosmos-db-datasource",
    "type": "cosmosdb",
    "credentials": {
        "connectionString": "Database=sql-test-db;ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/cosmos-db-resource-group/providers/Microsoft.DocumentDB/databaseAccounts/my-cosmos-db-account/;"
    },
    "container": { "name": "myCollection", "query": null },
    "dataChangeDetectionPolicy": {
        "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName": "_ts"
    }
}
```    

O corpo da solicitação contém a definição da fonte de dados, que deve incluir os seguintes campos:

| Campo   | Descrição |
|---------|-------------|
| **name** | Obrigatórios. Escolha qualquer nome para representar o objeto da fonte de dados. |
|**tipo**| Obrigatórios. Deve ser `cosmosdb`. |
|**credentials** | Obrigatórios. <br/><br/>Ao se conectar usando uma identidade gerenciada, o formato de **credenciais** deve ser: *Database=[database-name];ResourceId=[resource-id-string];(ApiKind=[api-kind];)*<br/> <br/>O formato de ResourceId: *ResourceId=/subscriptions/ **sua ID de assinatura** /resourceGroups/ **nome do seu grupo de recursos** /providers/Microsoft.DocumentDB/databaseAccounts/ **nome da sua conta do cosmos db** /;*<br/><br/>Para coleções SQL, a cadeia de conexão não requer um ApiKind.<br/><br/>Para coleções do MongoDB, adicione **ApiKind=MongoDb** à cadeia de conexão. <br/><br/>Para gráficos Gremlin e tabelas Cassandra, inscreva-se na [versão prévia restrita do indexador](https://aka.ms/azure-cognitive-search/indexer-preview) para obter acesso à versão prévia e informações sobre como formatar as credenciais.<br/>|
| **contêiner** | Contém os seguintes elementos: <br/>**nome** : Obrigatórios. Especifique a ID da coleção do banco de dados a ser indexada.<br/>**query** : Opcional. Você pode especificar uma consulta para nivelar um documento JSON arbitrário, criando um esquema nivelado que o Azure Cognitive Search possa indexar.<br/>Para a API do MongoDB, API do Gremlin e API do Cassandra, não há suporte para consultas. |
| **dataChangeDetectionPolicy** | Recomendadas |
|**dataDeletionDetectionPolicy** | Opcional |

### <a name="4---create-the-index"></a>4 – Criar o índice

O índice especifica os campos em um documento, atributos e outras construções que modelam a experiência de pesquisa.

Confira como criar um índice com um campo `booktitle` pesquisável:

```
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-target-index",
    "fields": [
    { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
    { "name": "booktitle", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}
```

Para obter mais informações sobre a criação de índices, consulte [Criar Índice](/rest/api/searchservice/create-index)

### <a name="5---create-the-indexer"></a>5 – Criar o indexador

Um indexador conecta uma fonte de dados a um índice de pesquisa de destino e fornece um agendamento para automatizar a atualização de dados.

Uma vez que o índice e a fonte de dados forem criados, será possível criar o indexador.

Exemplo de definição de indexador:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "cosmos-db-indexer",
      "dataSourceName" : "cosmos-db-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

Esse indexador será executado a cada duas horas (o intervalo de agendamento é definido como "PT2H"). Para executar um indexador a cada 30 minutos, defina o intervalo para "PT30M". O intervalo mais curto com suporte é de 5 minutos. O agendamento é opcional – se ele for omitido, um indexador será executado apenas uma vez quando for criado. No entanto, você pode executar um indexador sob demanda a qualquer momento.   

Para saber mais sobre a API Criar Indexador, veja [Criar indexador](/rest/api/searchservice/create-indexer).

Para saber mais sobre como definir as agendas do indexador, consulte [Como agendar indexadores para o Azure Cognitive Search](search-howto-schedule-indexers.md).

## <a name="troubleshooting"></a>Solução de problemas

Se você achar que não é possível indexar dados de Cosmos DB considere o seguinte:

1. Se você tiver girado recentemente suas chaves de conta Cosmos DB, precisará aguardar até 15 minutos para que a cadeia de conexão de identidade gerenciada funcione.

1. Verifique se a conta de Cosmos DB tem seu acesso restrito para selecionar redes. Se tiver, consulte o [acesso do indexador ao conteúdo protegido pelos recursos de segurança de rede do Azure](search-indexer-securing-resources.md).

## <a name="next-steps"></a>Próximas etapas

* [Indexador de Banco de dados do Azure Cosmos](search-howto-index-cosmosdb.md)