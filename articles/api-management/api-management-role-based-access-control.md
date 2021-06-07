---
title: Como usar o Controle de Acesso Baseado em Função no Gerenciamento de API do Azure | Microsoft Docs
description: Saiba como usar as funções internas e criar funções personalizadas no Gerenciamento de API do Azure
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 364cd53e-88fb-4301-a093-f132fa1f88f5
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: fc41d2904d5a6676f1e9e1e2d178985bef0a9aa3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101095553"
---
# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>Como usar o Controle de Acesso Baseado em Função no Gerenciamento de API do Azure

O gerenciamento de API do Azure conta com o Azure RBAC (controle de acesso baseado em função) para habilitar o gerenciamento de acesso refinado para serviços e entidades de gerenciamento de API (por exemplo, APIs e políticas). Este artigo fornece uma visão geral das funções internas e personalizadas no Gerenciamento de API. Para obter mais informações sobre gerenciamento de acesso no Portal do Azure, confira [Introdução ao gerenciamento de acesso no portal do Azure](../role-based-access-control/overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="built-in-roles"></a>Funções internas

No momento, o Gerenciamento de API oferece três funções internas e adicionará outras duas funções em breve. Essas funções podem ser atribuídas em escopos diferentes, incluindo assinatura, grupo de recursos e instância individual do Gerenciamento de API. Por exemplo, se você atribuir a função "leitor de serviço de gerenciamento de API" a um usuário no nível do grupo de recursos, o usuário terá acesso de leitura a todas as instâncias de gerenciamento de API dentro do grupo de recursos. 

A tabela a seguir fornece breves descrições das funções internas. Atribua essas funções usando o portal do Azure ou outras ferramentas, incluindo o Azure [PowerShell](../role-based-access-control/role-assignments-powershell.md), [CLI do Azure](../role-based-access-control/role-assignments-cli.md) e [API REST](../role-based-access-control/role-assignments-rest.md). Para obter detalhes sobre como atribuir funções internas, consulte [atribuir funções do Azure para gerenciar o acesso aos recursos de assinatura do Azure](../role-based-access-control/role-assignments-portal.md).

| Função          | Acesso de leitura<sup>[1]</sup> | Acesso de gravação<sup>[2]</sup> | Criação de serviço, exclusão, colocação em escala, VPN e configuração de domínio personalizado | Acesso ao portal do editor herdado | Descrição
| ------------- | ---- | ---- | ---- | ---- | ---- 
| Colaborador de serviço de gerenciamento de API | ✓ | ✓ | ✓ | ✓ | Superusuário. Tem acesso total de CRUD aos serviços e entidades do Gerenciamento de API (por exemplo, APIs e políticas). Tem acesso ao portal do publicador herdado. |
| Leitor de serviço de gerenciamento de API | ✓ | | || Tem acesso somente leitura aos serviços e entidades do Gerenciamento de API. |
| Operador de serviço de gerenciamento de API | ✓ | | ✓ | | Pode gerenciar os serviços de Gerenciamento de API, mas não entidades.|
| Editor de serviço de gerenciamento de API<sup>*</sup> | ✓ | ✓ | |  | Pode gerenciar as entidades de Gerenciamento de API, mas não os serviços.|
| Gerenciador de conteúdo de gerenciamento de API<sup>*</sup> | ✓ | | | ✓ | Pode gerenciar o portal do desenvolvedor. Acesso somente leitura aos serviços e entidades.|

<sup>[1] Acesso de leitura a serviços e entidades de Gerenciamento de API (por exemplo, APIs e políticas).</sup>

<sup>[2] Acesso de gravação aos serviços e entidades do Gerenciamento de API, exceto pelas seguintes operações: criação, exclusão e colocação em escala de instância; configuração de VPN; e configuração de nome de domínio personalizado.</sup>

<sup>\* A função editor de serviço estará disponível depois de migrarmos toda a interface do usuário do administrador do portal do Publicador existente para a portal do Azure. A função Gerenciador de conteúdo estará disponível depois que o portal do Publicador for refatorado para conter apenas a funcionalidade relacionada ao gerenciamento do portal do desenvolvedor.</sup>  

## <a name="custom-roles"></a>Funções personalizadas

Se nenhuma das funções internas atender às suas necessidades específicas, será possível criar funções personalizadas para fornecer um gerenciamento de acesso mais refinado às entidades do Gerenciamento de API. Por exemplo, você pode criar uma função personalizada que tem acesso somente leitura a um serviço do Gerenciamento de API, mas que somente tem acesso de gravação a uma API específica. Para saber mais sobre funções personalizadas, consulte [Funções personalizadas no Azure RBAC](../role-based-access-control/custom-roles.md). 

> [!NOTE]
> Para poder ver uma instância de gerenciamento de API no portal do Azure, uma função personalizada deve incluir a ```Microsoft.ApiManagement/service/read``` ação.

Ao criar uma função personalizada, é mais fácil começar com uma das funções internas. Edite os atributos para adicionar **Actions**, **NotActions** ou **AssignableScopes**, então salve as alterações como uma nova função. O exemplo a seguir começa com a função "leitor de serviço de gerenciamento de API" e cria uma função personalizada chamada "editor de API de calculadora". Você pode atribuir a função personalizada a uma API específica. Consequentemente, essa função só tem acesso a aquela API. 

```powershell
$role = Get-AzRoleDefinition "API Management Service Reader Role"
$role.Id = $null
$role.Name = 'Calculator API Contributor'
$role.Description = 'Has read access to Contoso APIM instance and write access to the Calculator API.'
$role.Actions.Add('Microsoft.ApiManagement/service/apis/write')
$role.Actions.Add('Microsoft.ApiManagement/service/apis/*/write')
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add('/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>')
New-AzRoleDefinition -Role $role
New-AzRoleAssignment -ObjectId <object ID of the user account> -RoleDefinitionName 'Calculator API Contributor' -Scope '/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>'
```

O artigo [Operações do provedor de recursos do Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement) contém a lista de permissões que podem ser concedidas no nível de Gerenciamento de API.

## <a name="video"></a>Vídeo


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player]
>
>

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o Controle de Acesso Baseado em Função no Azure, consulte os seguintes artigos:
  * [Introdução ao gerenciamento de acesso no portal do Azure](../role-based-access-control/overview.md)
  * [Atribuir funções do Azure para gerenciar o acesso aos recursos de assinatura do Azure](../role-based-access-control/role-assignments-portal.md)
  * [Funções personalizadas no RBAC do Azure](../role-based-access-control/custom-roles.md)
  * [Operações do provedor de recursos do Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement)
