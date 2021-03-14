---
title: Configurar o controle de acesso baseado em função para sua conta de Azure Cosmos DB com o Azure AD
description: Saiba como configurar o controle de acesso baseado em função com Azure Active Directory para sua conta de Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/03/2021
ms.author: thweiss
ms.openlocfilehash: 7c5497615ce71d0be713ef9ae28ab1e0f85b7ddb
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102177225"
---
# <a name="configure-role-based-access-control-with-azure-active-directory-for-your-azure-cosmos-db-account-preview"></a>Configurar o controle de acesso baseado em função com Azure Active Directory para sua conta de Azure Cosmos DB (versão prévia)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> O controle de acesso baseado em função Azure Cosmos DB está atualmente em versão prévia. Esta versão de visualização é fornecida sem um Contrato de Nível de Serviço e não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte os [Termos de uso complementares de versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!NOTE]
> Este artigo é sobre o controle de acesso baseado em função para operações de plano de dados no Azure Cosmos DB. Se você estiver usando operações do plano de gerenciamento, consulte o [controle de acesso baseado em função](role-based-access-control.md) aplicado ao artigo operações do plano de gerenciamento.

O Azure Cosmos DB expõe um sistema de RBAC (controle de acesso baseado em função) interno que permite:

- Autentique suas solicitações de dados com uma identidade Azure Active Directory (Azure AD).
- Autorize suas solicitações de dados com um modelo de permissão refinado e baseado em função.

## <a name="concepts"></a>Conceitos

O RBAC do plano de dados Azure Cosmos DB se baseia em conceitos que normalmente são encontrados em outros sistemas RBAC, como o [RBAC do Azure](../role-based-access-control/overview.md):

- O [modelo de permissão](#permission-model) é composto de um conjunto de **ações**; cada uma dessas ações é mapeada para uma ou várias operações de banco de dados. Alguns exemplos de ações incluem a leitura de um item, a gravação de um item ou a execução de uma consulta.
- Azure Cosmos DB usuários criam **[definições de função](#role-definitions)** contendo uma lista de ações permitidas.
- Definições de função são atribuídas a identidades específicas do Azure AD por meio de **[atribuições de função](#role-assignments)**. Uma atribuição de função também define o escopo ao qual a definição de função se aplica; Atualmente, há três escopos:
    - Uma conta Azure Cosmos DB,
    - Um banco de dados Azure Cosmos DB,
    - Um contêiner Azure Cosmos DB.

  :::image type="content" source="./media/how-to-setup-rbac/concepts.png" alt-text="Conceitos de RBAC":::

> [!NOTE]
> Atualmente, o RBAC Azure Cosmos DB não expõe nenhuma definição de função interna.

## <a name="permission-model"></a><a id="permission-model"></a> Modelo de permissão

A tabela a seguir lista todas as ações expostas pelo modelo de permissão.

| Nome | Operação (ões) de banco de dados correspondente |
|---|---|
| `Microsoft.DocumentDB/databaseAccounts/readMetadata` | Ler metadados da conta. Consulte [solicitações de metadados](#metadata-requests) para obter detalhes. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/create` | Criar um novo item. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read` | Ler um item individual por sua ID e chave de partição (ponto-leitura). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/replace` | Substituir um item existente. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/upsert` | "Upsert" um item, que significa criá-lo se ele não existir, ou substituí-lo se ele existir. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/delete` | Excluir um item. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery` | Executar uma [consulta SQL](sql-query-getting-started.md). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed` | Leia o [feed de alterações](read-change-feed.md)do contêiner. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeStoredProcedure` | Execute um [procedimento armazenado](stored-procedures-triggers-udfs.md). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/manageConflicts` | Gerenciar [conflitos](conflict-resolution-policies.md) para contas de região de várias gravações (ou seja, listar e excluir itens do feed de conflitos). |

Há suporte para curingas nos níveis de *contêineres* e *itens* :

- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*`
- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*`

> [!IMPORTANT]
> Esse modelo de permissão aborda apenas as operações de banco de dados que permitem ler e gravar o dado. Ele **não** abrange nenhum tipo de operação de gerenciamento, como criar contêineres ou alterar sua taxa de transferência. Para autenticar operações de gerenciamento com uma identidade do AAD, use o [RBAC do Azure](role-based-access-control.md) em vez disso.

### <a name="metadata-requests"></a><a id="metadata-requests"></a> Solicitações de metadados

Ao usar SDKs de Azure Cosmos DB, esses SDKs emitem solicitações de metadados somente leitura durante a inicialização e para atender a solicitações de dados específicas. Essas solicitações de metadados buscam vários detalhes de configuração, como: 

- A configuração global da sua conta, que inclui as regiões do Azure nas quais a conta está disponível.
- A chave de partição de seus contêineres ou sua política de indexação.
- A lista de partições físicas que fazem um contêiner e seus endereços.

Eles *não* buscam nenhum dos dados que você armazenou em sua conta.

Para garantir a melhor transparência do nosso modelo de permissão, essas solicitações de metadados são explicitamente cobertas pela `Microsoft.DocumentDB/databaseAccounts/readMetadata` ação. Essa ação deve ser permitida em todas as situações em que sua conta de Azure Cosmos DB é acessada por meio de um dos SDKs do Azure Cosmos DB. Ele pode ser atribuído (por meio de uma atribuição de função) em qualquer nível na hierarquia de Azure Cosmos DB (ou seja, conta, banco de dados ou contêiner).

As solicitações de metadados reais permitidas pela `Microsoft.DocumentDB/databaseAccounts/readMetadata` ação dependem do escopo ao qual a ação é atribuída:

| Escopo | Solicitações permitidas pela ação |
|---|---|
| Conta | -Listando os bancos de dados na conta<br>-Para cada banco de dados na conta, as ações permitidas no escopo do banco de dados |
| Banco de dados | -Lendo metadados de banco de dados<br>-Listando os contêineres no banco de dados<br>-Para cada contêiner no banco de dados, as ações permitidas no escopo do contêiner |
| Contêiner | -Lendo os metadados do contêiner<br>-Listando partições físicas no contêiner<br>-Resolvendo o endereço de cada partição física |

## <a name="create-role-definitions"></a><a id="role-definitions"></a> Criar definições de função

Ao criar uma definição de função, você precisa fornecer:

- O nome da sua conta de Azure Cosmos DB.
- O grupo de recursos que contém sua conta.
- O tipo da definição de função; somente tem `CustomRole` suporte no momento.
- O nome da definição de função.
- Uma lista de [ações](#permission-model) que você deseja que a função permita.
- Um ou vários escopos para os quais a definição de função pode ser atribuída; os escopos com suporte são:
    - `/` (nível de conta),
    - `/dbs/<database-name>` (nível de banco de dados),
    - `/dbs/<database-name>/colls/<container-name>` (nível de contêiner).

> [!NOTE]
> As operações descritas abaixo estão disponíveis no momento em:
> - Azure PowerShell: [AZ. CosmosDB versão 2.0.1 – Preview](https://www.powershellgallery.com/packages/Az.CosmosDB/2.0.1-preview)
> - CLI do Azure: [' cosmosdb-preview ' versão de extensão 0.4.0](https://github.com/Azure/azure-cli-extensions/tree/master/src/cosmosdb-preview)

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

Crie uma função chamada *MyReadWriteRole* que contém todas as ações:

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

Liste as definições de função que você criou para buscar suas IDs:

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

Crie uma função chamada *MyReadWriteRole* que contém todas as ações:

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

Liste as definições de função que você criou para buscar suas IDs:

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

Depois de criar suas definições de função, você poderá associá-las às suas identidades do AAD. Ao criar uma atribuição de função, você precisa fornecer:

- O nome da sua conta de Azure Cosmos DB.
- O grupo de recursos que contém sua conta.
- A ID da definição de função a ser atribuída.
- A ID da entidade de segurança da identidade à qual a definição de função deve ser atribuída.
- O escopo da atribuição de função; os escopos com suporte são:
    - `/` (nível de conta)
    - `/dbs/<database-name>` (nível de banco de dados)
    - `/dbs/<database-name>/colls/<container-name>` (nível de contêiner)

  O escopo deve corresponder ou ser um subescopo de um dos escopos atribuíveis da definição de função.

> [!NOTE]
> Se você quiser criar uma atribuição de função para uma entidade de serviço, certifique-se de usar sua **ID de objeto** como encontrada na seção **aplicativos empresariais** da folha do portal do **Azure Active Directory** .

> [!NOTE]
> As operações descritas abaixo estão disponíveis no momento em:
> - Azure PowerShell: [AZ. CosmosDB versão 2.0.1 – Preview](https://www.powershellgallery.com/packages/Az.CosmosDB/2.0.1-preview)
> - CLI do Azure: [' cosmosdb-preview ' versão de extensão 0.4.0](https://github.com/Azure/azure-cli-extensions/tree/master/src/cosmosdb-preview)

### <a name="using-azure-powershell"></a>Usando o PowerShell do Azure

Atribuir uma função a uma identidade:

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

Atribuir uma função a uma identidade:

```azurecli
resourceGroupName='<myResourceGroup>'
accountName='<myCosmosAccount>'
readOnlyRoleDefinitionId = '<roleDefinitionId>' // as fetched above
principalId = '<aadPrincipalId>'
az cosmosdb sql role assignment create --account-name $accountName --resource-group --scope "/" --principal-id $principalId --role-definition-id $readOnlyRoleDefinitionId
```

## <a name="initialize-the-sdk-with-azure-ad"></a>Inicializar o SDK com o Azure AD

Para usar o Azure Cosmos DB RBAC em seu aplicativo, é necessário atualizar a maneira como você inicializa o SDK do Azure Cosmos DB. Em vez de passar a chave primária da sua conta, você precisa passar uma instância de uma `TokenCredential` classe. Esta instância fornece o SDK do Azure Cosmos DB com o contexto necessário para buscar um token do AAD em nome da identidade que você deseja usar.

A maneira como você cria uma `TokenCredential` instância está além do escopo deste artigo. Há várias maneiras de criar essa instância, dependendo do tipo de identidade do AAD que você deseja usar (entidade de usuário, entidade de serviço, grupo, etc.). O mais importante `TokenCredential` é que sua instância deve resolver para a identidade (ID da entidade de segurança) à qual você atribuiu suas funções. Você pode encontrar exemplos de criação de uma `TokenCredential` classe:

- [no .NET](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme#credential-classes)
- [em Java](https://docs.microsoft.com/java/api/overview/azure/identity-readme#credential-classes)

Os exemplos a seguir usam uma entidade de serviço com uma `ClientSecretCredential` instância do.

### <a name="in-net"></a>In .NET

> [!NOTE]
> Você deve usar a `preview` versão do SDK do .net Azure Cosmos DB para acessar esse recurso.

```csharp
TokenCredential servicePrincipal = new ClientSecretCredential(
    "<azure-ad-tenant-id>",
    "<client-application-id>",
    "<client-application-secret>");
CosmosClient client = new CosmosClient("<account-endpoint>", servicePrincipal);
```

### <a name="in-java"></a>No Java

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

## <a name="auditing-data-requests"></a>Auditando solicitações de dados

Ao usar o Azure Cosmos DB RBAC, [os logs de diagnóstico](cosmosdb-monitor-resource-logs.md) são aumentados com informações de identidade e autorização para cada operação de dados. Isso permite que você execute a auditoria detalhada e recupere a identidade do AAD usada para cada solicitação de dados enviada à sua conta de Azure Cosmos DB.

Essas informações adicionais fluem na categoria de log **DataPlaneRequests** e consistem em duas colunas extras:

- `aadPrincipalId_g` mostra a ID da entidade de segurança da identidade do AAD que foi usada para autenticar a solicitação.
- `aadAppliedRoleAssignmentId_g` mostra a [atribuição de função](#role-assignments) que foi respeitada ao autorizar a solicitação.

## <a name="limits"></a>limites

- Você pode criar até 100 definições de função e 2.000 atribuições de função por Azure Cosmos DB conta.
- Atualmente, a resolução de grupo do Azure AD não tem suporte para identidades que pertencem a mais de 200 grupos.
- O token do Azure AD é passado atualmente como um cabeçalho com cada solicitação individual enviada ao serviço de Azure Cosmos DB, aumentando o tamanho geral da carga.
- O acesso a seus dados com o Azure AD por meio do [Azure Cosmos DB Explorer](data-explorer.md) ainda não tem suporte. O uso do Azure Cosmos DB Explorer ainda exige que o usuário tenha acesso à chave primária da conta por enquanto.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="which-azure-cosmos-db-apis-are-supported-by-rbac"></a>Quais APIs do Azure Cosmos DB são compatíveis com o RBAC?

No momento, há suporte apenas para a API do SQL.

### <a name="is-it-possible-to-manage-role-definitions-and-role-assignments-from-the-azure-portal"></a>É possível gerenciar definições de função e atribuições de função do portal do Azure?

Portal do Azure suporte para gerenciamento de função ainda não está disponível.

### <a name="which-sdks-in-azure-cosmos-db-sql-api-support-rbac"></a>Quais SDKs no Azure Cosmos DB API do SQL dão suporte a RBAC?

Os SDKs do [.net v3](sql-api-sdk-dotnet-standard.md) e do [Java v4](sql-api-sdk-java-v4.md) têm suporte no momento.

### <a name="is-the-azure-ad-token-automatically-refreshed-by-the-azure-cosmos-db-sdks-when-it-expires"></a>O token do Azure AD é atualizado automaticamente pelos SDKs de Azure Cosmos DB quando ele expira?

Sim.

### <a name="is-it-possible-to-disable-the-usage-of-the-account-primary-key-when-using-rbac"></a>É possível desabilitar o uso da chave primária da conta ao usar o RBAC?

A desabilitação da chave primária da conta não é possível no momento.

## <a name="next-steps"></a>Próximas etapas

- Obtenha uma visão geral de [acesso seguro aos dados no Cosmos DB](secure-access-to-data.md).
- Saiba mais sobre o [RBAC para gerenciamento de Azure Cosmos DB](role-based-access-control.md).
