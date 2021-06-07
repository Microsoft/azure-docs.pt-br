---
title: Entender as definições de função do Azure-RBAC do Azure
description: Saiba mais sobre as definições de função do Azure no Azure RBAC (controle de acesso baseado em função) para o gerenciamento de acesso refinado de recursos do Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 03/22/2021
ms.author: rolyon
ms.custom: ''
ms.openlocfilehash: 5b2ec3289d187997763ee0d9280a777d4fa1f396
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801750"
---
# <a name="understand-azure-role-definitions"></a>Entender as definições de função do Azure

Se você estiver tentando entender como uma função do Azure funciona ou se você estiver criando sua própria [função personalizada do Azure](custom-roles.md), é útil entender como as funções são definidas. Este artigo descreve os detalhes das definições de função e fornece alguns exemplos.

## <a name="role-definition"></a>Definição de função

Uma *definição de função* é um conjunto de permissões. Às vezes, é chamado apenas de *função*. Uma definição de função lista as operações que podem ser executadas, como leitura, gravação e exclusão. Ele também pode listar as operações que são excluídas das operações permitidas ou operações relacionadas aos dados subjacentes.

Veja a seguir um exemplo das propriedades em uma definição de função quando exibida usando Azure PowerShell:

```
Name
Id
IsCustom
Description
Actions []
NotActions []
DataActions []
NotDataActions []
AssignableScopes []
```

Veja a seguir um exemplo das propriedades em uma definição de função quando exibida usando o portal do Azure, CLI do Azure ou a API REST:

```
roleName
name
type
description
actions []
notActions []
dataActions []
notDataActions []
assignableScopes []
```

A tabela a seguir descreve o que significam as propriedades de função.

| Propriedade | Descrição |
| --- | --- |
| `Name`</br>`roleName` | O nome de exibição da função. |
| `Id`</br>`name` | A ID exclusiva da função. As funções internas têm a mesma ID de função entre nuvens. |
| `IsCustom`</br>`roleType` | Indica se esta é uma função personalizada. Defina como `true` ou `CustomRole` para funções personalizadas. Defina como `false` ou `BuiltInRole` para funções internas. |
| `Description`</br>`description` | A descrição da função. |
| `Actions`</br>`actions` | Uma matriz de cadeias de caracteres que especifica as operações de gerenciamento permitidas pela função. |
| `NotActions`</br>`notActions` | Uma matriz de cadeias de caracteres que especifica as operações de gerenciamento que são excluídas do `Actions` permitido. |
| `DataActions`</br>`dataActions` | Uma matriz de cadeias de caracteres que especifica as operações de dados permitidas pela função em seus dados dentro desse objeto. |
| `NotDataActions`</br>`notDataActions` | Uma matriz de cadeias de caracteres que especifica as operações de dados excluídas do `DataActions` permitido. |
| `AssignableScopes`</br>`assignableScopes` | Uma matriz de cadeias de caracteres que especifica os escopos que a função está disponível para atribuição. |

### <a name="operations-format"></a>Formato de operações

Operações são especificadas com cadeias de caracteres que têm o seguinte formato:

- `{Company}.{ProviderName}/{resourceType}/{action}`

A parte `{action}` de uma cadeia de caracteres de operação especifica o tipo de operações que você pode executar em um tipo de recurso. Por exemplo, você verá as seguintes subcadeias de caracteres em `{action}`:

| Subcadeia de caracteres de ação    | Description         |
| ------------------- | ------------------- |
| `*` | O caractere curinga concede acesso a todas as operações que correspondem à cadeia de caracteres. |
| `read` | Habilita operações de leitura (GET). |
| `write` | Habilita operações de gravação (PUT ou PATCH). |
| `action` | Habilita operações personalizadas como reiniciar máquinas virtuais (POST). |
| `delete` | Habilita operações de exclusão (DELETE). |

### <a name="role-definition-example"></a>Exemplo de definição de função

Aqui está a definição da função [colaborador](built-in-roles.md#contributor) , conforme exibido em Azure PowerShell e CLI do Azure. A operação curinga (`*`) em `Actions` indica que a entidade de segurança atribuída a essa função pode executar todas as ações ou, em outras palavras, pode gerenciar tudo. Isso inclui ações definidas no futuro, conforme o Azure adiciona novos tipos de recurso. As operações em `NotActions` são subtraídas de `Actions`. No caso da função de [colaborador](built-in-roles.md#contributor) , o `NotActions` Remove a capacidade dessa função de gerenciar o acesso a recursos e também gerenciar atribuições de Azure Blueprint.

Função de colaborador, conforme exibido na Azure PowerShell:

```json
{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Função de colaborador, conforme exibido na CLI do Azure:

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything except access to resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
  "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [
        "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action",
        "Microsoft.Blueprint/blueprintAssignments/write",
        "Microsoft.Blueprint/blueprintAssignments/delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="management-and-data-operations"></a>Operações de gerenciamento e dados

O controle de acesso baseado em função para operações de gerenciamento é especificado nas propriedades `Actions` e `NotActions` de uma definição de função. Aqui estão alguns exemplos de operações de gerenciamento no Azure:

- Gerenciar o acesso a uma conta de armazenamento
- Criar, atualizar ou excluir um contêiner de blob
- Excluir um grupo de recursos e todos os seus recursos

O acesso de gerenciamento não é herdado para seus dados desde que o método de autenticação de contêiner esteja definido como "conta de usuário do Azure AD" e não "chave de acesso". Essa separação impede que funções com curingas (`*`) tenham acesso irrestrito aos seus dados. Por exemplo, se um usuário tiver uma [leitor](built-in-roles.md#reader) função em uma assinatura, em seguida, eles podem exibir a conta de armazenamento, mas não podem exibir os dados subjacentes por padrão.

Anteriormente, o controle de acesso baseado em função não foi usado para operações de dados. Autorização para operações de dados variadas em provedores de recursos. O mesmo modelo de autorização de controle de acesso baseado em função usado para operações de gerenciamento foi estendido para operações de dados.

Para dar suporte a operações de dados, novas propriedades de dados foram adicionadas à definição de função. Operações de dados são especificadas nas propriedades `DataActions` e `NotDataActions`. Adicionando essas propriedades de dados, a separação entre o gerenciamento e de dados é mantida. Isso impede que as atribuições de função atuais com curingas (`*`) tenham acesso a dados repentinamente. Estas são algumas operações de dados que podem ser especificadas em `DataActions` e `NotDataActions`:

- Ler uma lista de blobs em um contêiner
- Gravar um blob de armazenamento em um contêiner
- Excluir uma mensagem em uma fila

Aqui está a definição da função de [leitor de dados do blob de armazenamento](built-in-roles.md#storage-blob-data-reader) , que inclui operações nas `Actions` `DataActions` Propriedades e. Essa função permite que você leia o contêiner de blob e também os dados blob subjacentes.

Função de leitor de dados de blob de armazenamento, conforme exibido no Azure PowerShell:

```json
{
  "Name": "Storage Blob Data Reader",
  "Id": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "IsCustom": false,
  "Description": "Allows for read access to Azure Storage blob containers and data",
  "Actions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/read",
    "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
  ],
  "NotActions": [],
  "DataActions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
  ],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Função de leitor de dados de blob de armazenamento, conforme exibido no CLI do Azure:

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

Apenas operações de dados podem ser adicionadas às propriedades `DataActions` e `NotDataActions`. Provedores de recursos identificam quais operações são operações de dados, definindo o `isDataAction` propriedade `true`. Para ver uma lista das operações em que `isDataAction` é `true`, consulte [ Operações do provedor de recursos ](resource-provider-operations.md). Funções que não têm as operações de dados não precisam ter `DataActions` e `NotDataActions` propriedades dentro da definição de função.

Autorização para todas as chamadas de API de operação de gerenciamento é tratada pelo Gerenciador de recursos do Azure. A autorização para chamadas de API de operação de dados é tratada por um provedor de recursos ou o Gerenciador de recursos do Azure.

### <a name="data-operations-example"></a>Exemplo de operações de dados

Para entender melhor como funcionam as operações de gerenciamento e de dados, vamos considerar um exemplo específico. Alice foi atribuída a [proprietário](built-in-roles.md#owner) função no escopo da assinatura. Bob recebeu a função [colaborador de dados de blob de armazenamento](built-in-roles.md#storage-blob-data-contributor) em um escopo de conta de armazenamento. O diagrama a seguir mostra este exemplo.

![Controle de acesso baseado em função foi estendido para dar suporte a operações de dados e de gerenciamento](./media/role-definitions/rbac-management-data.png)

A função de [proprietário](built-in-roles.md#owner) para Alice e a função de [colaborador de dados de blob de armazenamento](built-in-roles.md#storage-blob-data-contributor) para Bob têm as seguintes ações:

Proprietário

&nbsp;&nbsp;&nbsp;&nbsp;Ações<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Colaborador de dados de blob de armazenamento

&nbsp;&nbsp;&nbsp;&nbsp;Ações<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action`<br>
&nbsp;&nbsp;&nbsp;&nbsp;DataActions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Como Alice tem uma ação curinga ( `*` ) em um escopo de assinatura, suas permissões herdam para permitir que executem todas as ações de gerenciamento. Alice pode ler, gravar e excluir os contêineres. No entanto, Alice não pode executar operações de dados sem realizar etapas adicionais. Por exemplo, por padrão, Alice não pode ler os blobs dentro de um contêiner. Para ler os blobs, Alice teria que recuperar as chaves de acesso de armazenamento e usá-las para acessar os blobs.

As permissões de Bob são restritas apenas ao `Actions` e `DataActions` especificadas na função [colaborador de dados do blob de armazenamento](built-in-roles.md#storage-blob-data-contributor) . Com base na função, Bob pode executar operações de dados e de gerenciamento. Por exemplo, Bob pode ler, gravar e excluir contêineres na conta de armazenamento especificada e também pode ler, gravar e excluir os BLOBs.

Para saber mais sobre a segurança de plano de dados e gerenciamento, confira o [Guia de segurança de Armazenamento do Azure](../storage/blobs/security-recommendations.md).

### <a name="what-tools-support-using-azure-roles-for-data-operations"></a>Quais ferramentas dão suporte ao uso de funções do Azure para operações de dados?

Para visualizar e trabalhar com operações de dados, você deve ter as versões corretas das ferramentas ou SDKs:

| Ferramenta  | Versão  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0 ou posterior |
| [CLI do Azure](/cli/azure/install-azure-cli) | 2.0.30 ou posterior |
| [Azure para .NET](/dotnet/azure/) | 2.8.0-versão prévia ou posterior |
| [SDK do Azure para ir](/azure/go/azure-sdk-go-install) | 15.0.0 ou posterior |
| [Azure para Java](/java/azure/) | 1.9.0 ou posterior |
| [Azure para Python](/azure/python/) | 0.40.0 ou posterior |
| [SDK do Azure para Ruby](https://rubygems.org/gems/azure_sdk) | 0.17.1 ou posterior |

Para exibir e usar as operações de dados na API REST, você precisa definir o parâmetro **api-version** para a seguinte versão ou outra posterior:

- 2018-07-01

## <a name="actions"></a>Ações

Uma permissão `Actions`especifica as operações de gerenciamento permitidas pela função a ser realizada. É uma coleção de cadeias de operação que identificam as operações protegíveis dos provedores de recursos do Azure. Aqui estão alguns exemplos de operações de gerenciamento que podem ser usadas no `Actions`.

> [!div class="mx-tableFixed"]
> | Cadeia de caracteres da operação    | Description         |
> | ------------------- | ------------------- |
> | `*/read` | Concede acesso a operações de leitura a todos os tipos de recursos de todos os provedores de recursos do Azure.|
> | `Microsoft.Compute/*` | Concede acesso a todas as operações a todos os tipos de recursos no provedor de recursos Microsoft.Compute.|
> | `Microsoft.Network/*/read` | Concede acesso a operações de leitura a todos os tipos de recursos no provedor de recursos Microsoft.Network do Azure.|
> | `Microsoft.Compute/virtualMachines/*` | Concede acesso a todas as operações de máquinas virtuais e seus tipos de recursos filho.|
> | `microsoft.web/sites/restart/Action` | Concede acesso para reinicialização de um aplicativo Web.|

## <a name="notactions"></a>NotActions

A `NotActions` permissão especifica as operações de gerenciamento que são subtraídas ou excluídas do permitido `Actions` que têm um curinga ( `*` ). Use a `NotActions` permissão se o conjunto de operações que você deseja permitir for definido mais facilmente com a subtração de `Actions` que tenha um curinga ( `*` ). O acesso concedido por uma função (permissões efetivas) é calculado subtraindo as operações `NotActions` das operações `Actions`.

`Actions - NotActions = Effective management permissions`

A tabela a seguir mostra dois exemplos de permissões efetivas para uma operação curinga [Microsoft. CostManagement](resource-provider-operations.md#microsoftcostmanagement) :

> [!div class="mx-tableFixed"]
> | Actions | NotActions | Permissões de gerenciamento efetivas |
> | --- | --- | --- |
> | `Microsoft.CostManagement/exports/*` | *nenhum* | `Microsoft.CostManagement/exports/action`</br>`Microsoft.CostManagement/exports/read`</br>`Microsoft.CostManagement/exports/write`</br>`Microsoft.CostManagement/exports/delete`</br>`Microsoft.CostManagement/exports/run/action` |
> | `Microsoft.CostManagement/exports/*` | `Microsoft.CostManagement/exports/delete` | `Microsoft.CostManagement/exports/action`</br>`Microsoft.CostManagement/exports/read`</br>`Microsoft.CostManagement/exports/write`</br>`Microsoft.CostManagement/exports/run/action` |

> [!NOTE]
> Se um usuário for atribuído a uma função que exclui uma operação em `NotActions` e for atribuído a uma segunda função que concede acesso à mesma operação, ele terá permissão para executar essa operação. `NotActions` não é uma regra de negação, é simplesmente uma maneira conveniente de criar um conjunto de operações permitidas quando for necessário excluir operações específicas.
>

### <a name="differences-between-notactions-and-deny-assignments"></a>Diferenças entre não ações e atribuições de negação

`NotActions` e as atribuições de negação não são as mesmas e servem para fins diferentes. `NotActions` são uma maneira conveniente de subtrair ações específicas de uma `*` operação curinga ().

As designações de negação impedem que os usuários executem ações específicas, mesmo que uma atribuição de função conceda a eles acesso. Para obter mais informações, confira [Compreender atribuições de negação do Azure](deny-assignments.md).

## <a name="dataactions"></a>DataActions

Uma permissão `DataActions` que especifica as operações de dados permitidas pela função em seus dados dentro desse objeto. Por exemplo, se um usuário tem acesso de leitura blob dados para uma conta de armazenamento, eles podem ler blobs dentro dessa conta de armazenamento. Aqui estão alguns exemplos de operações de dados que podem ser usados em `DataActions`.

> [!div class="mx-tableFixed"]
> | Cadeia de caracteres da operação    | Description         |
> | ------------------- | ------------------- |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` | Retorna um blob ou uma lista de blobs. |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` | Retorna o resultado de escrever um blob. |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read` | Retorna uma mensagem. |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | Retorna uma mensagem ou o resultado de gravar ou excluir uma mensagem. |

## <a name="notdataactions"></a>NotDataActions

A `NotDataActions` permissão especifica as operações de dados que são subtraídas ou excluídas do permitido `DataActions` que têm um curinga ( `*` ). Use a `NotDataActions` permissão se o conjunto de operações que você deseja permitir for definido mais facilmente com a subtração de `DataActions` que tenha um curinga ( `*` ). O acesso concedido por uma função (permissões efetivas) é calculado subtraindo as operações `NotDataActions` das operações `DataActions`. Cada provedor de recursos fornece seu respectivo conjunto de APIs para atender as operações de dados.

`DataActions - NotDataActions = Effective data permissions`

A tabela a seguir mostra dois exemplos de permissões efetivas para uma operação curinga do [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) :

> [!div class="mx-tableFixed"]
> | DataActions | NotDataActions | Permissões de dados efetivas |
> | --- | --- | --- |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | *nenhum* | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/write`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action` |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete`</br> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/write`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action` |

> [!NOTE]
> Se um usuário é atribuído a uma função que exclui uma operação de dados em `NotDataActions`e é atribuído a uma segunda função que concede acesso à mesma operação de dados, o usuário tem permissão para executar essa operação de dados. `NotDataActions` não é uma negação de regra – é simplesmente uma maneira conveniente de criar um conjunto de operações de dados permitidos quando precisam de operações de dados específicos a serem excluídos.
>

## <a name="assignablescopes"></a>AssignableScopes

A `AssignableScopes` propriedade especifica os escopos (grupos de gerenciamento, assinaturas ou grupos de recursos) que têm essa definição de função disponível. Você pode tornar a função disponível para atribuição somente nos grupos de gerenciamento, assinaturas ou grupos de recursos que o exigem. Você deve usar pelo menos um grupo de gerenciamento, assinatura ou grupo de recursos.

As funções internas têm `AssignableScopes` definido como o escopo raiz (`"/"`). O escopo raiz indica que a função está disponível para atribuição em todos os escopos. Exemplos de escopos válidos que podem ser atribuídos incluem:

> [!div class="mx-tableFixed"]
> | A função está disponível para atribuição | Exemplo |
> |----------|---------|
> | Uma assinatura | `"/subscriptions/{subscriptionId1}"` |
> | Duas assinaturas | `"/subscriptions/{subscriptionId1}", "/subscriptions/{subscriptionId2}"` |
> | Grupo de recursos de rede | `"/subscriptions/{subscriptionId1}/resourceGroups/Network"` |
> | Um grupo de gerenciamento | `"/providers/Microsoft.Management/managementGroups/{groupId1}"` |
> | Grupo de gerenciamento e uma assinatura | `"/providers/Microsoft.Management/managementGroups/{groupId1}", /subscriptions/{subscriptionId1}",` |
> | Todos os escopos (aplica-se somente a funções internas) | `"/"` |

Para obter informações sobre as `AssignableScopes` funções personalizadas, consulte [funções personalizadas do Azure](custom-roles.md).

## <a name="next-steps"></a>Próximas etapas

* [Funções internas do Azure](built-in-roles.md)
* [Funções personalizadas do Azure](custom-roles.md)
* [Operações do provedor de recursos do Azure](resource-provider-operations.md)
