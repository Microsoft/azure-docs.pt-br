---
title: "Gerenciar RBAC (controle de acesso baseado em função) com a CLI do Azure | Microsoft Docs"
description: "Saiba como gerenciar o RBAC (Controle de Acesso baseado em função) com a interface de linha de comando do Azure listando as funções e ações de função, e atribuindo funções às assinaturas e escopos de aplicativo."
services: active-directory
documentationcenter: 
author: andredm7
manager: femila
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: andredm
ms.reviewer: rqureshi
ms.openlocfilehash: 77315171754304c965f296670fbba3a4751a3656
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-role-based-access-control-with-the-azure-command-line-interface"></a>Gerenciar o Controle de Acesso baseado em função com a Interface de Linha de Comando do Azure
> [!div class="op_single_selector"]
> * [PowerShell](role-based-access-control-manage-access-powershell.md)
> * [CLI do Azure](role-based-access-control-manage-access-azure-cli.md)
> * [API REST](role-based-access-control-manage-access-rest.md)


Você pode usar o RBAC (Controle de Acesso baseado em função) no Portal do Azure e na API do Azure Resource Manager para gerenciar o acesso a sua assinatura e aos recursos de maneira detalhada. Com esse recurso, você pode conceder acesso aos usuários, grupos ou entidades de serviço do Active Directory atribuindo algumas funções para eles em um determinado escopo.

Antes de poder usar a CLI (interface de linha de comando) do Azure para gerenciar o RBAC, é necessário ter os seguintes pré-requisitos:

* CLI do Azure versão 0.8.8 ou posterior. Para instalar a versão mais recente e associá-la à sua assinatura do Azure, consulte [Instalar e configurar a CLI do Azure](../cli-install-nodejs.md).
* Azure Resource Manager na Azure CLI. Acesse [Usando a Azure CLI com o Resource Manager](../xplat-cli-azure-resource-manager.md) para obter mais detalhes.

## <a name="list-roles"></a>Listar funções
### <a name="list-all-available-roles"></a>Relacionar todas as funções disponíveis
Para listar todas as funções disponíveis, use:

        azure role list

O exemplo a seguir mostra a relação de *todas as funções disponíveis*.

```
azure role list --json | jq '.[] | {"roleName":.properties.roleName, "description":.properties.description}'
```

![Linha de comando do Azure RBAC  - lista de funções do azure - captura de tela](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-list.png)

### <a name="list-actions-of-a-role"></a>Relacionar ações de uma função
Para listar as ações de uma função, use:

    azure role show "<role name>"

O exemplo a seguir mostra as ações das funções *Colaborador* e *Colaborador da Máquina Virtual*.

```
azure role show "contributor" --json | jq '.[] | {"Actions":.properties.permissions[0].actions,"NotActions":properties.permissions[0].notActions}'

azure role show "virtual machine contributor" --json | jq '.[] | .properties.permissions[0].actions'
```

![Linha de comando do Azure RBAC  - exibição de funções do azure - captura de tela](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-show.png)

## <a name="list-access"></a>Relacionar acesso
### <a name="list-role-assignments-effective-on-a-resource-group"></a>Relacionar as atribuições de função como efetivas em um grupo de recursos
Para listar as atribuições de função que existem em um grupo de recursos, use:

    azure role assignment list --resource-group <resource group name>

O exemplo a seguir mostra as atribuições de função no grupo *pharma-sales-projecforcast* .

```
azure role assignment list --resource-group pharma-sales-projecforcast --json | jq '.[] | {"DisplayName":.properties.aADObject.displayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'
```

![Linha de comando do RBAC do Azure – lista de atribuição de funções do Azure por grupo – captura de tela](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-1.png)

### <a name="list-role-assignments-for-a-user"></a>Listar as atribuições de função de um usuário
Para listar as atribuições de função para um usuário específico e as atribuições que são atribuídas aos grupos do usuário, use:

    azure role assignment list --signInName <user email>

Você também pode ver as atribuições da função herdadas dos grupos modificando o comando:

    azure role assignment list --expandPrincipalGroups --signInName <user email>

O exemplo a seguir mostra as atribuições da função concedidas ao usuário *sameert@aaddemo.com* . Isso inclui funções atribuídas diretamente ao usuário e funções herdadas de grupos.

```
azure role assignment list --signInName sameert@aaddemo.com --json | jq '.[] | {"DisplayName":.properties.aADObject.DisplayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'

azure role assignment list --expandPrincipalGroups --signInName sameert@aaddemo.com --json | jq '.[] | {"DisplayName":.properties.aADObject.DisplayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'
```

![Linha de comando do Azure RBAC  - lista de atribuição de funções do azure por usuário - captura de tela](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-2.png)

## <a name="grant-access"></a>Conceder acesso
Para conceder acesso após ter identificado a função que você deseja atribuir, use:

    azure role assignment create

### <a name="assign-a-role-to-group-at-the-subscription-scope"></a>Atribuir uma função ao grupo no escopo da assinatura
Para atribuir uma função a um grupo no escopo da assinatura, use:

    azure role assignment create --objectId  <group object id> --roleName <name of role> --subscription <subscription> --scope <subscription/subscription id>

O exemplo a seguir atribui a função *Leitor* à *equipe de Christine Koch* no escopo da *assinatura*.

![Linha de comando do Azure RBAC – criação de atribuição de funções do Azure por grupo – captura de tela](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-1.png)

### <a name="assign-a-role-to-an-application-at-the-subscription-scope"></a>Atribuir uma função a um aplicativo no escopo da assinatura
Para atribuir uma função a um aplicativo no escopo da assinatura, use:

    azure role assignment create --objectId  <applications object id> --roleName <name of role> --subscription <subscription> --scope <subscription/subscription id>

O exemplo a seguir concede a função *Colaborador* a um aplicativo *Azure AD* na assinatura selecionada.

 ![Linha de comando do Azure RBAC  - criação de atribuição de funções do azure por aplicativo](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-2.png)

### <a name="assign-a-role-to-a-user-at-the-resource-group-scope"></a>Atribuir uma função ao usuário no escopo do grupo de recursos
Para atribuir uma função a um usuário no escopo do grupo de recursos, use:

    azure role assignment create --signInName  <user email address> --roleName "<name of role>" --resourceGroup <resource group name>

O exemplo a seguir concede a função *Colaborador da Máquina Virtual* ao usuário *samert@aaddemo.com* no escopo do grupo de recursos *Pharma-Sales-ProjectForcast*.

![Linha de comando do RBAC do Azure – criação de atribuição de funções do Azure por usuário – captura de tela](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-3.png)

### <a name="assign-a-role-to-a-group-at-the-resource-scope"></a>Atribuir uma função a um grupo no escopo de recursos
Para atribuir uma função a um grupo no escopo de recursos, use:

    azure role assignment create --objectId <group id> --role "<name of role>" --resource-name <resource group name> --resource-type <resource group type> --parent <resource group parent> --resource-group <resource group>

O exemplo a seguir concede a função *Colaborador da Máquina Virtual* a um grupo do *Azure AD* em uma *sub-rede*.

![Linha de comando do Azure RBAC – criação de atribuição de funções do Azure por grupo – captura de tela](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-4.png)

## <a name="remove-access"></a>Remover acesso
Para remover uma atribuição de função, use:

    azure role assignment delete --objectId <object id to from which to remove role> --roleName "<role name>"

O exemplo a seguir remove a atribuição da função *Colaborador da Máquina Virtual* do usuário *sammert@aaddemo.com* no grupo de recursos *Pharma-Sales-ProjectForcast*.
Em seguida, o exemplo remove a atribuição de função de um grupo na assinatura.

![Linha de comando do Azure RBAC  - exclusão de atribuição de funções do azure - captura de tela](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-assignment-delete.png)

## <a name="create-a-custom-role"></a>Criar uma função personalizada
Para criar uma função personalizada, use:

    azure role definition create --role-definition <file path>

O exemplo a seguir cria uma função personalizada chamada *Operador de Máquina Virtual*. Essa função personalizada concede acesso a todas as operações de leitura dos provedores de recursos *Microsoft.Compute*, *Microsoft.Storage* e *Microsoft.Network*, além de conceder acesso para iniciar, reiniciar e monitorar máquinas virtuais. Essa função personalizada pode ser usada em duas assinaturas. Este exemplo utiliza um arquivo JSON como entrada.

![JSON - definição de função personalizada - captura de tela](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-1.png)

![Linha de comando do Azure RBAC  - criação de função do azure - captura de tela](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-2.png)

## <a name="modify-a-custom-role"></a>Modificar uma função personalizada
Para modificar uma função personalizada, use o comando `azure role definition list` para recuperar a definição da função. Depois, faça as alterações desejadas no arquivo de definição da função. Por fim, use `azure role definition update` para salvar a definição da função modificada.

    azure role definition update --role-definition <file path>

O exemplo a seguir adiciona a operação *Microsoft.Insights/diagnosticSettings/* a **Actions** e uma assinatura do Azure a **AssignableScopes** da função personalizada Operador de Máquina Virtual.

![JSON - modificar definição de função personalizada - captura de tela](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set-1.png)

![Linha de comando do Azure RBAC  - conjunto de funções do azure - captura de tela](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set2.png)

## <a name="delete-a-custom-role"></a>Excluir uma função personalizada
Para excluir uma função personalizada, primeiro use o comando `azure role definition list` para determinar a **ID** da função. Em seguida, use o comando `azure role definition delete` para excluir a função especificando a **ID**.

O exemplo a seguir remove a função personalizada *Operador de Máquina Virtual* .

![Linha de comando do Azure RBAC  - exclusão de funções do azure - captura de tela](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-delete.png)

## <a name="list-custom-roles"></a>Listar funções personalizadas
Para listar as funções disponíveis para atribuição em um escopo, use o comando `azure role list` .

O comando a seguir lista todas as funções disponíveis para atribuição na assinatura selecionada.

```
azure role definition list --json | jq '.[] | {"name":.properties.roleName, type:.properties.type}'
```

![Linha de comando do Azure RBAC  - lista de funções do azure - captura de tela](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list1.png)

No exemplo a seguir, a função personalizada *Operador de Máquina Virtual* não está disponível na assinatura *Production4*, pois essa assinatura não está nos **AssignableScopes** da função.

```
azure role definition list --json | jq '.[] | if .properties.type == "CustomRole" then .properties.roleName else empty end'
```

![Linha de comando do Azure RBAC  - lista de funções do azure para funções personalizadas - captura de tela](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list2.png)

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

