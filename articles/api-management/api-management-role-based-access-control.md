---
title: "Como usar o Controle de Acesso Baseado em Função no Gerenciamento de API do Azure | Microsoft Docs"
description: "Saiba como usar as funções internas e criar funções personalizadas no Gerenciamento de API do Azure"
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 364cd53e-88fb-4301-a093-f132fa1f88f5
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: apimpm
ms.openlocfilehash: a3872aae3a9eb8da0b881ec9388f54546e84b08b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>Como usar o Controle de Acesso Baseado em Função no Gerenciamento de API do Azure
O Gerenciamento de API do Azure depende do RBAC (Controle de Acesso Baseado em Função) do Azure para habilitar o gerenciamento de acesso refinado de serviços e entidades de Gerenciamento de API (por exemplo, APIs e políticas). Este artigo fornece uma visão geral das funções internas e personalizadas no Gerenciamento de API. Para obter mais informações sobre gerenciamento de acesso no Portal do Azure, confira [Introdução ao gerenciamento de acesso no portal do Azure](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/).

## <a name="built-in-roles"></a>Funções internas
No momento, o Gerenciamento de API oferece três funções internas e adicionará outras duas funções em breve. Essas funções podem ser atribuídas em escopos diferentes, incluindo assinatura, grupo de recursos e instância individual do Gerenciamento de API. Por exemplo, se a função "Leitor de Serviço do Gerenciamento de API do Azure" for atribuída a um usuário no nível do grupo de recursos, o usuário terá acesso de leitura a todas as instâncias do Gerenciamento de API dentro do grupo de recursos. 

A tabela a seguir fornece breves descrições das funções internas. Atribua essas funções usando o portal do Azure ou outras ferramentas, incluindo o Azure [PowerShell](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-powershell), [CLI do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-azure-cli) e [API REST](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-rest). Para obter detalhes sobre como atribuir funções internas, confira [Usar atribuições de função para gerenciar o acesso aos recursos de sua assinatura do Azure](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/).

| Função          | Acesso de leitura<sup>[1]</sup> | Acesso de gravação<sup>[2]</sup> | Criação de serviço, exclusão, colocação em escala, VPN e configuração de domínio personalizado | Acesso ao portal do editor herdado | Descrição
| ------------- | ---- | ---- | ---- | ---- | ---- | ---- |
| Colaborador do serviço de Gerenciamento de API do Azure | ✓ | ✓  | ✓  | ✓ | Superusuário. Tem acesso total de CRUD aos serviços e entidades do Gerenciamento de API (por exemplo, APIs e políticas). Tem acesso ao portal do publicador herdado. |
| Leitor do serviço de Gerenciamento de API do Azure | ✓ | | || Tem acesso somente leitura aos serviços e entidades do Gerenciamento de API. |
| Operador do serviço de Gerenciamento de API do Azure | ✓ | | ✓  | | Pode gerenciar os serviços de Gerenciamento de API, mas não entidades.|
| Editor do serviço de Gerenciamento de API do Azure<sup>*</sup> | ✓ | ✓  | |  | Pode gerenciar as entidades de Gerenciamento de API, mas não os serviços.|
| Gerenciador de conteúdo do Gerenciamento de API do Azure<sup>*</sup> | ✓ | | | ✓  | Pode gerenciar o portal do desenvolvedor. Acesso somente leitura aos serviços e entidades.|

<sup>[1] Acesso de leitura a serviços e entidades de Gerenciamento de API (por exemplo, APIs e políticas).</sup>

<sup>[2] Acesso de gravação aos serviços e entidades do Gerenciamento de API, exceto pelas seguintes operações: criação, exclusão e colocação em escala de instância; configuração de VPN; e configuração de nome de domínio personalizado.</sup>

<sup>\* A função Editor de Serviço estará disponível após migrarmos toda a interface do usuário do administrador do portal do editor existente para o Portal do Azure. A função Gerenciador de Conteúdo estará disponível após o portal do editor ser refatorado para conter apenas as funcionalidades relacionadas ao gerenciamento do portal do desenvolvedor.</sup>  

## <a name="custom-roles"></a>Funções personalizadas
Se nenhuma das funções internas atender às suas necessidades específicas, será possível criar funções personalizadas para fornecer um gerenciamento de acesso mais refinado às entidades do Gerenciamento de API. Por exemplo, você pode criar uma função personalizada que tem acesso somente leitura a um serviço do Gerenciamento de API, mas que somente tem acesso de gravação a uma API específica. Para saber mais sobre funções personalizadas, consulte [Funções personalizadas no Azure RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles). 

Ao criar uma função personalizada, é mais fácil começar com uma das funções internas. Edite os atributos para adicionar **Actions**, **NotActions** ou **AssignableScopes**, então salve as alterações como uma nova função. O exemplo a seguir começa com a função "Leitor de Serviço do Gerenciamento de API do Azure" e cria uma função personalizada chamada "Editor de API da Calculadora". Você pode atribuir a função personalizada a uma API específica. Consequentemente, essa função só tem acesso a aquela API. 

```
$role = Get-AzureRmRoleDefinition "API Management Service Reader Role"
$role.Id = $null
$role.Name = 'Calculator API Contributor'
$role.Description = 'Has read access to Contoso APIM instance and write access to the Calculator API.'
$role.Actions.Add('Microsoft.ApiManagement/service/apis/write')
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add('/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>')
New-AzureRmRoleDefinition -Role $role
New-AzureRmRoleAssignment -ObjectId <object ID of the user account> -RoleDefinitionName 'Calculator API Contributor' -Scope '/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>'
```

O artigo [Operações do provedor de recursos do Azure Resource Manager](../active-directory/role-based-access-control-resource-provider-operations.md#microsoftapimanagement) contém a lista de permissões que podem ser concedidas no nível de Gerenciamento de API.

## <a name="watch-a-video-overview"></a>Assista a uma visão geral em vídeo

Para obter mais informações, você pode assistir ao vídeo [Controle de acesso baseado em função no Gerenciamento de API](https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o Controle de Acesso Baseado em Função no Azure, consulte os seguintes artigos:
  * [Introdução ao gerenciamento de acesso no portal do Azure](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/)
  * [Usar as atribuições de função para gerenciar o acesso aos recursos de assinatura do Azure](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/)
  * [Funções personalizadas no Azure RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles)
  * [Operações do provedor de recursos do Azure Resource Manager](../active-directory/role-based-access-control-resource-provider-operations.md#microsoftapimanagement)

