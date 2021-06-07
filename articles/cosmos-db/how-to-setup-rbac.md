---
title: Configurar o controle de acesso baseado em função para sua conta do Azure Cosmos DB com o Azure AD
description: Saiba como configurar o controle de acesso baseado em função com o Azure Active Directory para sua conta do Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/30/2021
ms.author: thweiss
ms.openlocfilehash: 1a6bdf55e52a7060423d2a016f07eee3608f50d4
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063467"
---
# <a name="configure-role-based-access-control-with-azure-active-directory-for-your-azure-cosmos-db-account-preview"></a>Configurar o controle de acesso baseado em função com o Azure Active Directory para sua conta do Azure Cosmos DB (versão prévia)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Atualmente, o controle de acesso baseado em função do Azure Cosmos DB está em versão prévia. Essa versão prévia é fornecida sem um Contrato de Nível de Serviço e não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte os [Termos de uso complementares de versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!NOTE]
> Este artigo trata do controle de acesso baseado em função para operações do plano de dados no Azure Cosmos DB. Se você estiver usando operações do plano de gerenciamento, confira o artigo [Controle de acesso baseado em função](role-based-access-control.md) aplicado às operações do plano de gerenciamento.

O Azure Cosmos DB expõe um sistema interno de RBAC (controle de acesso baseado em função) que permite:

- Autenticar as solicitações de dados com uma identidade do Azure AD (Azure Active Directory).
- Autorizar as solicitações de dados com um modelo de permissão refinado e baseado em função.

## <a name="concepts"></a>Conceitos

O RBAC do plano de dados do Azure Cosmos DB se baseia em conceitos que normalmente são encontrados em outros sistemas RBAC, como o [RBAC do Azure](../role-based-access-control/overview.md):

- O [modelo de permissão](#permission-model) é composto por um conjunto de **ações**. Cada uma dessas ações é mapeada para uma ou várias operações de banco de dados. Entre alguns exemplos de ações estão a leitura e a gravação de um item ou a execução de uma consulta.
- Os usuários do Azure Cosmos DB criam **[definições de função](#role-definitions)** contendo uma lista de ações permitidas.
- As definições de função são atribuídas a identidades específicas do Azure AD por meio de **[atribuições de função](#role-assignments)** . Uma atribuição de função também define o escopo ao qual a definição de função se aplica. Atualmente, há três escopos:
    - Uma conta do Azure Cosmos DB;
    - Um banco de dados do Azure Cosmos DB;
    - Um contêiner do Azure Cosmos DB.

  :::image type="content" source="./media/how-to-setup-rbac/concepts.png" alt-text="Conceitos sobre o RBAC":::

> [!NOTE]
> Atualmente, o RBAC do Azure Cosmos DB não expõe nenhuma definição de função interna.

## <a name="permission-model"></a><a id="permission-model"></a> Modelo de permissão

A tabela abaixo lista todas as ações expostas pelo modelo de permissão.

| Nome | Operações de banco de dados correspondentes |
|---|---|
| `Microsoft.DocumentDB/databaseAccounts/readMetadata` | Ler metadados da conta. Confira [Solicitações de metadados](#metadata-requests) para obter detalhes. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/create` | Criar um novo item. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read` | Ler um item individual pela ID e chave de partição (leitura de ponto). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/replace` | Substituir um item existente. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/upsert` | Executar upsert de um item, o que significa criá-lo se ele não existir ou substituí-lo se ele existir. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/delete` | Excluir um item. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery` | Executar uma [consulta SQL](sql-query-getting-started.md). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed` | Ler o [feed de alterações](read-change-feed.md) do contêiner. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeStoredProcedure` | Executar um [procedimento armazenado](stored-procedures-triggers-udfs.md). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/manageConflicts` | Gerenciar [conflitos](conflict-resolution-policies.md) de contas de região de várias gravações (ou seja, listar e excluir itens do feed de conflitos). |

Há suporte para curingas nos níveis de *contêineres* e *itens*:

- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*`
- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*`

> [!IMPORTANT]
> Esse modelo de permissão só aborda as operações de banco de dados que permitem ler e gravar dados. Ele **não** abrange nenhum tipo de operação de gerenciamento, como criação de contêineres ou alteração da taxa de transferência. Para autenticar as operações de gerenciamento em uma identidade do AAD, use o [RBAC do Azure](role-based-access-control.md).

### <a name="metadata-requests"></a><a id="metadata-requests"></a> Solicitações de metadados

Ao usar SDKs do Azure Cosmos DB, esses SDKs emitem solicitações de metadados somente leitura durante a inicialização com a finalidade de atender a solicitações de dados específicas. Essas solicitações de metadados buscam vários detalhes de configuração, como: 

- A configuração global da sua conta, que inclui as regiões do Azure nas quais a conta está disponível.
- A chave de partição ou a política de indexação dos seus contêineres.
- A lista de partições físicas que constituem um contêiner e os respectivos endereços.

Elas *não* buscam nenhum dos dados que você armazenou na sua conta.

Para garantir a melhor transparência do nosso modelo de permissão, essas solicitações de metadados são explicitamente abordadas pela ação `Microsoft.DocumentDB/databaseAccounts/readMetadata`. Essa ação deve ser permitida em todas as situações em que a sua conta do Azure Cosmos DB é acessada por meio de um dos SDKs do Azure Cosmos DB. Ela pode ser atribuída (por meio de uma atribuição de função) em qualquer nível da hierarquia do Azure Cosmos DB (ou seja, conta, banco de dados ou contêiner).

As solicitações de metadados reais permitidas pela ação `Microsoft.DocumentDB/databaseAccounts/readMetadata` dependem do escopo ao qual a ação é atribuída:

| Escopo | Solicitações permitidas pela ação |
|---|---|
| Conta | – Lista dos bancos de dados na conta<br>– Para cada banco de dados na conta, as ações permitidas no escopo do banco de dados |
| Banco de dados | – Leitura dos metadados do banco de dados<br>– Lista dos contêineres no banco de dados<br>– Para cada contêiner no banco de dados, as ações permitidas no escopo do contêiner |
| Contêiner | – Leitura dos metadados do contêiner<br>– Lista das partições físicas no contêiner<br>– Resolução do endereço de cada partição física |

## <a name="create-role-definitions"></a><a id="role-definitions"></a> Criar definições de função

Ao criar uma definição de função, você precisa fornecer:

- O nome da sua conta do Azure Cosmos DB.
- O grupo de recursos que contém a conta.
- O tipo da definição de função. No momento, só há suporte para `CustomRole`.
- O nome da definição de função.
- Uma lista de [ações](#permission-model) que você deseja permitir por meio da função.
- Um ou vários escopos aos quais a definição de função pode ser atribuída. Os escopos compatíveis são:
    - `/` (nível da conta);
    - `/dbs/<database-name>` (nível do banco de dados);
    - `/dbs/<database-name>/colls/<container-name>` (nível do contêiner).

> [!NOTE]
> No momento, as operações descritas abaixo estão disponíveis em:
> - Azure PowerShell: [Az.CosmosDB versão 2.0.1-preview](https://www.powershellgallery.com/packages/Az.CosmosDB/2.0.1-preview)
> - CLI do Azure: ['cosmosdb-preview' versão de extensão 0.4.0](https://github.com/Azure/azure-cli-extensions/tree/master/src/cosmosdb-preview)

### <a name="using-azure-powershell"></a>Usando o PowerShell do Azure

Crie uma função chamada *MyReadOnlyRole* que contenha apenas ações de leitura:

```powershell
$resourceGroupName = "<myResourceGroup>"
$accountName = "<myCosmosAccount>"
New-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -Type CustomRole -RoleName MyReadOnlyRole `
    -DataAction @( `
        'Microsoft.DocumentDB/databaseAccounts/readMetadata',
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed') `
    -AssignableScope "/"
```

Crie uma função chamada *MyReadWriteRole* que contenha todas as ações:

```powershell
New-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -Type CustomRole -RoleName MyReadWriteRole `
    -DataAction @( `
        'Microsoft.DocumentDB/databaseAccounts/readMetadata',
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*') `
    -AssignableScope "/"
```

Liste as definições de função que você criou para buscar as respectivas IDs:

```powershell
Get-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName
```

```
RoleName         : MyReadWriteRole
Id               : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAcc
                   ounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>
Type             : CustomRole
Permissions      : {Microsoft.Azure.Management.CosmosDB.Models.Permission}
AssignableScopes : {/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAc
                   counts/<myCosmosAccount>}

RoleName         : MyReadOnlyRole
Id               : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAcc
                   ounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>
Type             : CustomRole
Permissions      : {Microsoft.Azure.Management.CosmosDB.Models.Permission}
AssignableScopes : {/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAc
                   counts/<myCosmosAccount>}
```

### <a name="using-the-azure-cli"></a>Usando a CLI do Azure

Crie uma função chamada *MyReadOnlyRole* que contenha apenas ações de leitura:

```json
// role-definition-ro.json
{
    "RoleName": "MyReadOnlyRole",
    "Type": "CustomRole",
    "AssignableScopes": ["/"],
    "Permissions": [{
        "DataActions": [
            "Microsoft.DocumentDB/databaseAccounts/readMetadata",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed"
        ]
    }]
}
```

```azurecli
resourceGroupName='<myResourceGroup>'
accountName='<myCosmosAccount>'
az cosmosdb sql role definition create --account-name $accountName --resource-group $resourceGroupName --body @role-definition-ro.json
```

Crie uma função chamada *MyReadWriteRole* que contenha todas as ações:

```json
// role-definition-rw.json
{
    "RoleName": "MyReadWriteRole",
    "Type": "CustomRole",
    "AssignableScopes": ["/"],
    "Permissions": [{
        "DataActions": [
            "Microsoft.DocumentDB/databaseAccounts/readMetadata",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*"
        ]
    }]
}
```

```azurecli
az cosmosdb sql role definition create --account-name $accountName --resource-group $resourceGroupName --body @role-definition-rw.json
```

Liste as definições de função que você criou para buscar as respectivas IDs:

```azurecli
az cosmosdb sql role definition list --account-name $accountName --resource-group $resourceGroupName
```

```
[
  {
    "assignableScopes": [
      "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>"
    ],
    "id": "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>",
    "name": "<roleDefinitionId>",
    "permissions": [
      {
        "dataActions": [
          "Microsoft.DocumentDB/databaseAccounts/readMetadata",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*"
        ],
        "notDataActions": []
      }
    ],
    "resourceGroup": "<myResourceGroup>",
    "roleName": "MyReadWriteRole",
    "sqlRoleDefinitionGetResultsType": "CustomRole",
    "type": "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions"
  },
  {
    "assignableScopes": [
      "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>"
    ],
    "id": "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>",
    "name": "<roleDefinitionId>",
    "permissions": [
      {
        "dataActions": [
          "Microsoft.DocumentDB/databaseAccounts/readMetadata",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed"
        ],
        "notDataActions": []
      }
    ],
    "resourceGroup": "<myResourceGroup>",
    "roleName": "MyReadOnlyRole",
    "sqlRoleDefinitionGetResultsType": "CustomRole",
    "type": "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions"
  }
]
```

## <a name="create-role-assignments"></a><a id="role-assignments"></a> Criar atribuições de função

Depois de criar as definições de função, você poderá associá-las às suas identidades do AAD. Ao criar uma atribuição de função, você precisa fornecer:

- O nome da sua conta do Azure Cosmos DB.
- O grupo de recursos que contém a conta.
- A ID da definição de função a ser atribuída.
- A ID da entidade de segurança da identidade à qual a definição de função deve ser atribuída.
- O escopo da atribuição de função. Os escopos compatíveis são:
    - `/` (nível da conta)
    - `/dbs/<database-name>` (nível do banco de dados)
    - `/dbs/<database-name>/colls/<container-name>` (nível do contêiner)

  O escopo precisa corresponder a um dos escopos atribuíveis da definição de função ou ser um subescopo de um deles.

> [!NOTE]
> Caso deseje criar uma atribuição de função para uma entidade de serviço, lembre-se de usar a **ID de objeto** encontrada na seção **Aplicativos empresariais** da folha do portal do **Azure Active Directory**.

> [!NOTE]
> No momento, as operações descritas abaixo estão disponíveis em:
> - Azure PowerShell: [Az.CosmosDB versão 2.0.1-preview](https://www.powershellgallery.com/packages/Az.CosmosDB/2.0.1-preview)
> - CLI do Azure: ['cosmosdb-preview' versão de extensão 0.4.0](https://github.com/Azure/azure-cli-extensions/tree/master/src/cosmosdb-preview)

### <a name="using-azure-powershell"></a>Usando o PowerShell do Azure

Atribua uma função a uma identidade:

```powershell
$resourceGroupName = "<myResourceGroup>"
$accountName = "<myCosmosAccount>"
$readOnlyRoleDefinitionId = "<roleDefinitionId>" // as fetched above
$principalId = "<aadPrincipalId>"
New-AzCosmosDBSqlRoleAssignment -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -RoleDefinitionId $readOnlyRoleDefinitionId `
    -Scope $accountName `
    -PrincipalId $principalId
```

### <a name="using-the-azure-cli"></a>Usando a CLI do Azure

Atribua uma função a uma identidade:

```azurecli
resourceGroupName='<myResourceGroup>'
accountName='<myCosmosAccount>'
readOnlyRoleDefinitionId = '<roleDefinitionId>' // as fetched above
principalId = '<aadPrincipalId>'
az cosmosdb sql role assignment create --account-name $accountName --resource-group $resourceGroupName --scope "/" --principal-id $principalId --role-definition-id $readOnlyRoleDefinitionId
```

## <a name="initialize-the-sdk-with-azure-ad"></a>Inicializar o SDK com o Azure AD

Para usar o RBAC do Azure Cosmos DB no seu aplicativo, é necessário atualizar a maneira como você inicializa o SDK do Azure Cosmos DB. Em vez de transmitir a chave primária da sua conta, você precisa transmitir uma instância de uma classe `TokenCredential`. Essa instância fornece ao SDK do Azure Cosmos DB o contexto necessário para buscar um token do AAD em nome da identidade que você deseja usar.

A maneira como uma instância de `TokenCredential` é criada não está incluída no escopo deste artigo. Há várias maneiras de criar essa instância, dependendo do tipo de identidade do AAD que você deseja usar (entidade de usuário, entidade de serviço, grupo etc.). O mais importante é que a instância de `TokenCredential` precisa ser resolvida para a identidade (ID da entidade de segurança) à qual você atribuiu as funções. Encontre exemplos de como criar uma classe `TokenCredential`:

- [no .NET](/dotnet/api/overview/azure/identity-readme#credential-classes)
- [no Java](/java/api/overview/azure/identity-readme#credential-classes)
- [no JavaScript](/javascript/api/overview/azure/identity-readme#credential-classes)

Os exemplos abaixo usam uma entidade de serviço com uma instância de `ClientSecretCredential`.

### <a name="in-net"></a>In .NET

No momento, há suporte para o RBAC do Azure Cosmos DB na versão `preview` do [SDK do .NET V3](sql-api-sdk-dotnet-standard.md).

```csharp
TokenCredential servicePrincipal = new ClientSecretCredential(
    "<azure-ad-tenant-id>",
    "<client-application-id>",
    "<client-application-secret>");
CosmosClient client = new CosmosClient("<account-endpoint>", servicePrincipal);
```

### <a name="in-java"></a>No Java

No momento, há suporte para o RBAC do Azure Cosmos DB no [SDK do Java V4](sql-api-sdk-java-v4.md).

```java
TokenCredential ServicePrincipal = new ClientSecretCredentialBuilder()
    .authorityHost("https://login.microsoftonline.com")
    .tenantId("<azure-ad-tenant-id>")
    .clientId("<client-application-id>")
    .clientSecret("<client-application-secret>")
    .build();
CosmosAsyncClient Client = new CosmosClientBuilder()
    .endpoint("<account-endpoint>")
    .credential(ServicePrincipal)
    .build();
```

### <a name="in-javascript"></a>No JavaScript

Atualmente, há suporte para o RBAC do Azure Cosmos DB no [SDK do JavaScript V3](sql-api-sdk-node.md).

```javascript
const servicePrincipal = new ClientSecretCredential(
    "<azure-ad-tenant-id>",
    "<client-application-id>",
    "<client-application-secret>");
const client = new CosmosClient({
    "<account-endpoint>",
    aadCredentials: servicePrincipal
});
```

## <a name="auditing-data-requests"></a>Como auditar as solicitações de dados

Durante o uso do RBAC do Azure Cosmos DB, os [logs de diagnóstico](cosmosdb-monitor-resource-logs.md) são aumentados com informações de identidade e autorização para cada operação de dados. Isso permite que você execute uma auditoria detalhada e recupere a identidade do AAD usada para cada solicitação de dados enviada à sua conta do Azure Cosmos DB.

Essas informações adicionais fluem na categoria de log **DataPlaneRequests** e consistem em duas colunas extras:

- `aadPrincipalId_g` mostra a ID da entidade de segurança da identidade do AAD que foi usada para autenticar a solicitação.
- `aadAppliedRoleAssignmentId_g` mostra a [atribuição de função](#role-assignments) que foi respeitada ao autorizar a solicitação.

## <a name="limits"></a>Limites

- Você pode criar até cem definições de função e duas mil atribuições de função por conta do Azure Cosmos DB.
- Só é possível atribuir definições de função às identidades do Azure AD que pertencem ao mesmo locatário do Azure AD da sua conta do Azure Cosmos DB.
- Atualmente, não há suporte para a resolução de grupo do Azure AD em identidades que pertencem a mais de 200 grupos.
- O token do Azure AD é transmitido atualmente como um cabeçalho com cada solicitação individual enviada ao serviço do Azure Cosmos DB, aumentando o tamanho geral do conteúdo.
- Ainda não há suporte para o acesso aos seus dados com o Azure AD por meio do [Azure Cosmos DB Explorer](data-explorer.md). Por enquanto, para usar o Azure Cosmos DB Explorer, ainda é preciso que o usuário tenha acesso à chave primária da conta.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="which-azure-cosmos-db-apis-are-supported-by-rbac"></a>Quais APIs do Azure Cosmos DB são compatíveis com RBAC?

No momento, há compatibilidade apenas com a API do SQL.

### <a name="is-it-possible-to-manage-role-definitions-and-role-assignments-from-the-azure-portal"></a>É possível gerenciar definições de função e atribuições de função pelo portal do Azure?

A compatibilidade do portal do Azure com o gerenciamento de função ainda não está disponível.

### <a name="which-sdks-in-azure-cosmos-db-sql-api-support-rbac"></a>Quais SDKs na API do SQL para o Azure Cosmos DB dão suporte ao RBAC?

No momento, há suporte para os SDKs do [.NET V3](sql-api-sdk-dotnet-standard.md) e do [Java V4](sql-api-sdk-java-v4.md).

### <a name="is-the-azure-ad-token-automatically-refreshed-by-the-azure-cosmos-db-sdks-when-it-expires"></a>O token do Microsoft Azure Active Directory é atualizado automaticamente pelos SDKs do Azure Cosmos DB quando ele expira?

Sim.

### <a name="is-it-possible-to-disable-the-usage-of-the-account-primary-key-when-using-rbac"></a>É possível desabilitar o uso da chave primária da conta ao usar o RBAC?

Não é possível desabilitar a chave primária da conta no momento.

## <a name="next-steps"></a>Próximas etapas

- Obtenha uma visão geral de [acesso seguro aos dados no Cosmos DB](secure-access-to-data.md).
- Saiba mais sobre o [RBAC para o gerenciamento do Azure Cosmos DB](role-based-access-control.md).