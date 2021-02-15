---
title: Acesso delegado na área de trabalho virtual do Windows – Azure
description: Como delegar recursos administrativos em uma implantação de área de trabalho virtual do Windows, incluindo exemplos.
author: Heidilohr
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f2aa2c74704cf89c082d2837b39e82902efa0a62
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88010048"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Acesso delegado na Área de Trabalho Virtual do Windows

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver usando a Área de Trabalho Virtual do Windows (clássica) sem objetos do Azure Resource Manager, confira [este artigo](./virtual-desktop-fall-2019/delegated-access-virtual-desktop-2019.md).

A área de trabalho virtual do Windows tem um modelo de acesso delegado que permite definir a quantidade de acesso que um usuário específico tem permissão para ter atribuindo a eles uma função. Uma atribuição de função tem três componentes: entidade de segurança, definição de função e escopo. O modelo de acesso delegado da área de trabalho virtual do Windows baseia-se no modelo RBAC do Azure. Para saber mais sobre atribuições de função específicas e seus componentes, consulte [a visão geral do controle de acesso baseado em função do Azure](../role-based-access-control/built-in-roles.md).

O acesso delegado da área de trabalho virtual do Windows oferece suporte aos seguintes valores para cada elemento da atribuição de função:

* Entidade de segurança
    * Usuários
    * Grupos de usuários
    * Entidades de serviço
* Definição de função
    * Funções internas
    * Funções personalizadas
* Escopo
    * Pools de hosts
    * Grupos de aplicativos
    * Workspaces

## <a name="powershell-cmdlets-for-role-assignments"></a>Cmdlets do PowerShell para atribuições de função

Antes de começar, certifique-se de seguir as instruções em [Configurar o módulo do PowerShell](powershell-module.md) para configurar o módulo do PowerShell da área de trabalho virtual do Windows, caso ainda não tenha feito isso.

A área de trabalho virtual do Windows usa o Azure RBAC (controle de acesso baseado em função) ao publicar grupos de aplicativos para usuários ou grupos de usuários. A função de usuário de virtualização de desktop é atribuída ao usuário ou grupo de usuários e o escopo é o grupo de aplicativos. Essa função dá ao usuário acesso especial a dados no grupo de aplicativos.

Execute o seguinte cmdlet para adicionar Azure Active Directory usuários a um grupo de aplicativos:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Execute o seguinte cmdlet para adicionar Azure Active Directory grupo de usuários a um grupo de aplicativos:

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

## <a name="next-steps"></a>Próximas etapas

Para obter uma lista mais completa de cmdlets do PowerShell que cada função pode usar, consulte a [referência do PowerShell](/powershell/windows-virtual-desktop/overview).

Para obter uma lista completa de funções com suporte no RBAC do Azure, confira [funções internas do Azure](../role-based-access-control/built-in-roles.md).

Para obter diretrizes sobre como configurar um ambiente de área de trabalho virtual do Windows, consulte [ambiente de área de trabalho virtual do Windows](environment-setup.md).
