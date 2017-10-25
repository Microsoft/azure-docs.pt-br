---
title: "Criar funções personalizadas no RBAC do Azure | Microsoft Docs"
description: "Saiba como definir funções personalizadas com o Controle de Acesso Baseado em Função para um gerenciamento de identidade mais preciso na sua assinatura do Azure."
services: active-directory
documentationcenter: 
author: andredm7
manager: femila
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/11/2017
ms.author: andredm
ms.reviewer: rqureshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8e72f2c8095d13c4b6df3c6576bd58806a3c0f2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-custom-roles-for-azure-role-based-access-control"></a>Criar funções personalizadas para o Controle de Acesso Baseado em Função do Azure
Crie uma função personalizada no RBAC (Controle de Acesso Baseado em Função) do Azure se nenhuma das funções internas atende às suas necessidades de acesso específicas. É possível criar funções personalizadas usando o [Azure PowerShell](role-based-access-control-manage-access-powershell.md), a [CLI (interface de linha de comando) do Azure](role-based-access-control-manage-access-azure-cli.md) e a [API REST](role-based-access-control-manage-access-rest.md). Assim como as funções internas, é possível atribuir funções personalizadas a usuários, grupos e aplicativos na assinatura, no grupo de recursos e nos escopos de recurso. As funções personalizadas são armazenadas em um locatário do Azure AD e podem ser compartilhadas entre assinaturas.

Cada locatário pode criar até 2000 funções personalizadas. 

O seguinte exemplo mostra uma função personalizada para monitorar e reiniciar máquinas virtuais:

```
{
  "Name": "Virtual Machine Operator",
  "Id": "cadb4a5a-4e7a-47be-84db-05cad13b6769",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624",
    "/subscriptions/34370e90-ac4a-4bf9-821f-85eeedeae1a2"
  ]
}
```
## <a name="actions"></a>Ações
A propriedade **Actions** de uma função personalizada especifica as operações do Azure às quais a função concede acesso. É uma coleção de cadeias de operação que identificam as operações protegíveis dos provedores de recursos do Azure. Cadeias de caracteres de operação seguem o formato de `Microsoft.<ProviderName>/<ChildResourceType>/<action>`. As cadeias de operação que contêm caracteres curinga (\*) permitem acesso a todas as operações que correspondem à cadeia de operação. Por exemplo:

* `*/read` concede acesso a operações de leitura a todos os tipos de recursos de todos os provedores de recursos do Azure.
* `Microsoft.Compute/*` concede acesso a todas as operações a todos os tipos de recursos no provedor de recursos Microsoft.Compute.
* `Microsoft.Network/*/read` concede acesso a operações de leitura a todos os tipos de recursos no provedor de recursos Microsoft.Network do Azure.
* `Microsoft.Compute/virtualMachines/*` concede acesso a todas as operações de máquinas virtuais e seus tipos de recursos filhos.
* `Microsoft.Web/sites/restart/Action` concede acesso para reinicialização de sites.

Use `Get-AzureRmProviderOperation` (no PowerShell) ou `azure provider operations show` (na CLI do Azure) para listar as operações dos provedores de recursos do Azure. Você também pode usar esses comandos para verificar se uma cadeia de operação é válida e para expandir as cadeias de operação curinga.

```
Get-AzureRMProviderOperation Microsoft.Compute/virtualMachines/*/action | FT Operation, OperationName

Get-AzureRMProviderOperation Microsoft.Network/*
```

![PowerShell screenshot - Get-AzureRMProviderOperation](./media/role-based-access-control-configure/1-get-azurermprovideroperation-1.png)

```
azure provider operations show "Microsoft.Compute/virtualMachines/*/action" --js on | jq '.[] | .operation'

azure provider operations show "Microsoft.Network/*"
```

![Captura de tela da CLI do Azure – as operações do provedor do Azure mostram "Microsoft.Compute/virtualMachines/\*/action" ](./media/role-based-access-control-configure/1-azure-provider-operations-show.png)

## <a name="notactions"></a>NotActions
Use a propriedade **NotActions** se o conjunto de operações que você deseja permitir fica definido mais facilmente pela exclusão das operações restritas. A permissão de acesso concedida por uma função personalizada é computada subtraindo as operações **NotActions** das operações **Actions**.

> [!NOTE]
> Se um usuário for atribuído a uma função que exclui uma operação em **NotActions** e for atribuído a uma segunda função que concede acesso à mesma operação, ele terá permissão para executar essa operação. **NotActions** não é uma regra de negação, é simplesmente uma maneira conveniente de criar um conjunto de operações permitidas quando for necessário excluir operações específicas.
>
>

## <a name="assignablescopes"></a>AssignableScopes
A propriedade **AssignableScopes** da função personalizada especifica os escopos (assinaturas, grupos de recursos ou recursos) nos quais a função personalizada ficará disponível para a atribuição. Você pode disponibilizar a função personalizada para atribuição apenas nas assinaturas ou grupos de recursos que a exijam e não sobrecarregar a experiência do usuário nas assinaturas ou grupos de recursos restantes.

Exemplos de escopos válidos que podem ser atribuídos incluem:

* “/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e”, “/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624” – disponibiliza a função para atribuição em duas assinaturas.
* “/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e” – disponibiliza a função para atribuição em uma única assinatura.
* “/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network” – disponibiliza a função para atribuição apenas no Grupo de recursos de rede.

> [!NOTE]
> Você deve usar pelo menos uma assinatura, grupo de recursos ou ID de recurso.
>
>

## <a name="custom-roles-access-control"></a>Controle de acesso de funções personalizadas
A propriedade **AssignableScopes** da função personalizada também controla quem pode exibir, modificar e excluir a função.

* Quem pode criar uma função personalizada?
    Os Proprietários (e os Administradores de acesso do usuário) de assinaturas, grupos de recursos e recursos podem criar funções personalizadas para uso nesses escopos.
    O usuário que está criando a função precisa ser capaz de executar a operação `Microsoft.Authorization/roleDefinition/write` em todos os **AssignableScopes** da função.
* Quem pode modificar uma função personalizada?
    Os Proprietários (e os Administradores de acesso do usuário) de assinaturas, grupos de recursos e recursos podem modificar funções personalizadas nesses escopos. Os usuários precisam poder executar a operação `Microsoft.Authorization/roleDefinition/write` em todos os **AssignableScopes** de uma função personalizada.
* Quem pode exibir funções personalizadas?
    Todas as funções internas no RBAC do Azure permitem a visualização de funções disponíveis para atribuição. Os usuários que podem executar a operação `Microsoft.Authorization/roleDefinition/read` em um escopo podem exibir as funções de RBAC disponíveis para atribuição nesse escopo.

## <a name="see-also"></a>Confira também
* [Controle de Acesso com Base em Funções](role-based-access-control-configure.md): introdução ao RBAC no portal do Azure.
* Saiba como gerenciar o acesso com:
  * [PowerShell](role-based-access-control-manage-access-powershell.md)
  * [CLI do Azure](role-based-access-control-manage-access-azure-cli.md)
  * [API REST](role-based-access-control-manage-access-rest.md)
* [Funções internas](role-based-access-built-in-roles.md): obtenha detalhes sobre as funções que são incluídas por padrão no RBAC.
