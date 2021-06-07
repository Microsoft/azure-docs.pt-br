---
title: Configurar uma conexão com o banco de dados SQL do Azure usando uma identidade gerenciada
titleSuffix: Azure Cognitive Search
description: Saiba como configurar uma conexão de indexador com o banco de dados SQL do Azure usando uma identidade gerenciada
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: b940da2cf754e7e1cac91df6b517ecebe55e8c40
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94358415"
---
# <a name="set-up-an-indexer-connection-to-azure-sql-database-using-a-managed-identity"></a>Configurar uma conexão de indexador para o banco de dados SQL do Azure usando uma identidade gerenciada

Esta página descreve como configurar uma conexão de indexador com o banco de dados SQL do Azure usando uma identidade gerenciada em vez de fornecer credenciais na cadeia de conexão do objeto de fonte de dado.

Antes de saber mais sobre esse recurso, é recomendável compreender o que é um indexador e como configurá-lo para a fonte de dados. Encontre mais informações nos links a seguir:

* [Visão geral do indexador](search-indexer-overview.md)
* [Indexador do SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

## <a name="set-up-a-connection-using-a-managed-identity"></a>Configurar uma conexão usando uma identidade gerenciada

### <a name="1---turn-on-system-assigned-managed-identity"></a>1 – Habilitar a identidade gerenciada atribuída ao sistema

Quando uma identidade gerenciada atribuída pelo sistema é habilitada, o Azure cria uma identidade para o serviço de pesquisa que pode ser usada para autenticar outros serviços do Azure no mesmo locatário e assinatura. Você pode usar essa identidade em atribuições do Azure RBAC (controle de acesso baseado em função) que permitem o acesso aos dados durante a indexação.

![Habilitar a identidade gerenciada atribuída ao sistema](./media/search-managed-identities/turn-on-system-assigned-identity.png "Habilitar a identidade gerenciada atribuída ao sistema")

Depois de selecionar **Salvar**, você verá uma ID do objeto que foi atribuída ao serviço de pesquisa.

![ID do objeto](./media/search-managed-identities/system-assigned-identity-object-id.png "ID de objeto")

### <a name="2---provision-azure-active-directory-admin-for-sql-server"></a>2 – Provisionar o administrador do Azure Active Directory para SQL Server

Ao se conectar ao banco de dados na próxima etapa, será necessário se conectar a uma conta do Azure AD (Azure Active Directory) que tenha acesso de administrador ao banco de dados para dar permissão de acesso no banco de dados ao seu serviço de pesquisa.

Siga [estas](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell#provision-azure-ad-admin-sql-database) instruções para conceder ao administrador da conta do Azure AD acesso ao banco de dados.

### <a name="3---assign-the-search-service-permissions"></a>3 – Atribuir as permissões ao serviço de pesquisa

Execute as etapas abaixo para atribuir ao serviço de pesquisa a permissão para ler o banco de dados.

1. Conectar-se ao Visual Studio

    ![Conectar-se ao Visual Studio](./media/search-managed-identities/connect-with-visual-studio.png "Conectar-se ao Visual Studio")

2. Autenticar com sua conta do Azure AD

    ![Autenticar](./media/search-managed-identities/visual-studio-authentication.png "Authenticate")

3. Execute os seguintes comandos:

    Inclua os colchetes ao redor do nome do serviço de pesquisa.
    
    ```
    CREATE USER [your search service name here] FROM EXTERNAL PROVIDER;
    EXEC sp_addrolemember 'db_datareader', [your search service name here];
    ```

    ![Nova consulta](./media/search-managed-identities/visual-studio-new-query.png "Nova Consulta")

    ![Executar consulta](./media/search-managed-identities/visual-studio-execute-query.png "Executar consulta")

>[!NOTE]
> Se a identidade do serviço de pesquisa da etapa 1 for alterada após a conclusão desta etapa, você deverá remover a associação de função e remover o usuário no banco de dados SQL e, em seguida, adicionar as permissões novamente concluindo de novo a etapa 3.
> A remoção da associação de função e do usuário pode ser realizada executando os seguintes comandos:
> ```
> sp_droprolemember 'db_datareader', [your search service name];
> DROP USER IF EXISTS [your search service name];
> ```

### <a name="4---add-a-role-assignment"></a>4 – Adicionar uma atribuição de função

Nesta etapa, você dará permissão ao seu serviço Azure Cognitive Search para ler dados do seu SQL Server.

1. No portal do Azure, navegue até sua página do SQL Server do Azure.
2. Selecione **Controle de Acesso (IAM)**
3. Selecione **Adicionar** > **Adicionar atribuição de função**

    ![Adicionar atribuição de função](./media/search-managed-identities/add-role-assignment-sql-server.png "Adicionar atribuição de função")

4. Selecione a função de **Leitor** apropriada.
5. Deixe **Atribuir acesso a** como **Usuário, grupo ou entidade de serviço do Azure AD**
6. Pesquise pelo serviço de pesquisa, selecione-o e, em seguida, selecione **Salvar**

    ![Adicionar atribuição de função de leitor](./media/search-managed-identities/add-role-assignment-sql-server-reader-role.png "Adicionar atribuição de função de leitor")

### <a name="5---create-the-data-source"></a>5 – Criar a fonte de dados

A [API REST](/rest/api/searchservice/create-data-source), portal do Azure e o [SDK do .net](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) oferecem suporte à cadeia de conexão de identidade gerenciada. Veja abaixo um exemplo de como criar uma fonte de dados para indexar dados de um banco de dados SQL do Azure usando a [API REST](/rest/api/searchservice/create-data-source) e uma cadeia de conexão de identidade gerenciada. O formato da cadeia de conexão de identidade gerenciada é o mesmo para a API REST, o SDK do .NET e o portal do Azure.

Ao criar uma fonte de dados usando a [API REST](/rest/api/searchservice/create-data-source), a fonte de dados deve ter as seguintes propriedades necessárias:

* **name** é o nome exclusivo da fonte de dados dentro de seu serviço de pesquisa.
* **type** é `azuresql`
* **credentials**
    * Ao usar uma identidade gerenciada para autenticar, o formato **credentials** é diferente de quando não usa uma identidade gerenciada. Aqui, você fornecerá nome de Catálogo ou Banco de dados Inicial e um ResourceId sem chave de conta ou senha. O ResourceId deve incluir a ID da assinatura do banco de dados SQL do Azure, o grupo de recursos do banco de dados SQL e o nome do banco de dados SQL. 
    * Formato da cadeia de conexão de identidade gerenciada:
        * *Initial Catalog|Database=**nome do banco de dados**;ResourceId=/subscriptions/**sua ID de assinatura**/resourceGroups/**nome do seu grupo de recursos**/providers/Microsoft.Sql/servers/**nome do seu SQL Server**/;Connection Timeout=**duração do tempo limite de conexão**;*
* **container** especifica o nome da tabela ou exibição que você deseja indexar.

Exemplo de como criar um objeto de fonte de dados do SQL do Azure usando a [API REST](/rest/api/searchservice/create-data-source):

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sql-datasource",
    "type" : "azuresql",
    "credentials" : { "connectionString" : "Database=sql-database;ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azure-sql-resource-group/providers/Microsoft.Sql/servers/sql-server-search-demo;Connection Timeout=30;" },
    "container" : { "name" : "my-table" }
} 
```

### <a name="6---create-the-index"></a>6 – Criar o índice

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

### <a name="7---create-the-indexer"></a>7 – Criar o indexador

Um indexador conecta uma fonte de dados a um índice de pesquisa de destino e fornece um agendamento para automatizar a atualização de dados.

Uma vez que o índice e a fonte de dados forem criados, será possível criar o indexador.

Exemplo de definição de indexador para um indexador de SQL do Azure:

```
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sql-indexer",
    "dataSourceName" : "sql-datasource",
    "targetIndexName" : "my-target-index",
    "schedule" : { "interval" : "PT2H" }
}
```    

Esse indexador será executado a cada duas horas (o intervalo de agendamento é definido como "PT2H"). Para executar um indexador a cada 30 minutos, defina o intervalo para "PT30M". O intervalo mais curto com suporte é de 5 minutos. O agendamento é opcional – se ele for omitido, um indexador será executado apenas uma vez quando for criado. No entanto, você pode executar um indexador sob demanda a qualquer momento.   

Para saber mais sobre a API Criar Indexador, veja [Criar indexador](/rest/api/searchservice/create-indexer).

Para saber mais sobre como definir as agendas do indexador, consulte [Como agendar indexadores para o Azure Cognitive Search](search-howto-schedule-indexers.md).

## <a name="troubleshooting"></a>Solução de problemas

Se você receber um erro quando o indexador tentar se conectar à fonte de dados, indicando que o cliente não tem permissão para acessar o servidor, confira [erros comuns do indexador](./search-indexer-troubleshooting.md).

## <a name="see-also"></a>Confira também

Saiba mais sobre o indexador SQL do Azure:
* [Indexador do SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)