---
title: Atribuir uma função a um grupo usando Privileged Identity Management no Azure AD | Microsoft Docs
description: Saiba como você pode atribuir uma função de Azure Active Directory (Azure AD) a um grupo usando Azure AD Privileged Identity Management (PIM).
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02cd3f54823b80ae201316fee29c02616b9d8502
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103012030"
---
# <a name="assign-a-role-to-a-group-using-privileged-identity-management"></a>Atribuir uma função a um grupo usando Privileged Identity Management

Este artigo descreve como você pode atribuir uma função de Azure Active Directory (Azure AD) a um grupo usando Azure AD Privileged Identity Management (PIM).

> [!NOTE]
> Você deve usar a versão atualizada do Privileged Identity Management para poder atribuir um grupo a uma função do Azure AD usando o PIM. Você pode estar em uma versão mais antiga do PIM se sua organização do Azure AD aproveitar a API Privileged Identity Management. Nesse caso, entre em contato com o alias pim_preview@microsoft.com para mover sua organização e atualizar sua API. Saiba mais em [funções e recursos do Azure AD no PIM](../privileged-identity-management/azure-ad-roles-features.md).

## <a name="using-azure-ad-admin-center"></a>Usando o centro de administração do Azure AD

1. Entre no [Azure ad Privileged Identity Management](https://ms.portal.azure.com/?Microsoft_AAD_IAM_GroupRoles=true&Microsoft_AAD_IAM_userRolesV2=true&Microsoft_AAD_IAM_enablePimIntegration=true#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) como um administrador de função com privilégios ou administrador global em sua organização.

1. Selecione **Privileged Identity Management**  >  funções de **funções do Azure ad**  >    >  **Adicionar atribuições**

1. Selecione uma função e, em seguida, selecione um grupo. Somente os grupos qualificados para atribuição de função (grupos de função atribuíveis) são exibidos e não todos os grupos.

    ![Captura de tela que mostra a página "Adicionar atribuições" com as seções "selecionar função" e "selecionar membro (s)" realçadas.](./media/groups-pim-eligible/select-member.png)

1. Selecione a configuração de associação desejada. Para funções que exigem ativação, escolha **qualificado**. Por padrão, o usuário estaria permanentemente qualificado, mas você também pode definir uma hora de início e de término para a elegibilidade do usuário. Quando estiver concluído, pressione salvar e adicionar para concluir a atribuição de função.

    ![selecionar o usuário a quem você está atribuindo a função](./media/groups-pim-eligible/set-assignment-settings.png)

## <a name="using-powershell"></a>Usando o PowerShell

### <a name="download-the-azure-ad-preview-powershell-module"></a>Baixar o módulo PowerShell de visualização do Azure AD

Para instalar o módulo de #PowerShell do Azure AD, use os seguintes cmdlets:

```powershell
Install-Module -Name AzureADPreview
Import-Module -Name AzureADPreview
```

Para verificar se o módulo está pronto para uso, use o seguinte cmdlet:

```powershell
Get-Module -Name AzureADPreview
```

### <a name="assign-a-group-as-an-eligible-member-of-a-role"></a>Atribuir um grupo como um membro qualificado de uma função

```powershell
$schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
$schedule.Type = "Once"
$schedule.StartDateTime = "2019-04-26T20:49:11.770Z"
$schedule.endDateTime = "2019-07-25T20:49:11.770Z"
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId aadRoles -Schedule $schedule -ResourceId "[YOUR TENANT ID]" -RoleDefinitionId "9f8c1837-f885-4dfd-9a75-990f9222b21d" -SubjectId "[YOUR GROUP ID]" -AssignmentState "Eligible" -Type "AdminAdd"
```

## <a name="using-microsoft-graph-api"></a>Usando a API Microsoft Graph

```http
POST
https://graph.microsoft.com/beta/privilegedAccess/aadroles/roleAssignmentRequests
{
 "roleDefinitionId": {roleDefinitionId},
 "resourceId": {tenantId},
 "subjectId": {GroupId},
 "assignmentState": "Eligible",
 "type": "AdminAdd",
 "reason": "reason string",
 "schedule": {
   "startDateTime": {DateTime},
   "endDateTime": {DateTime},
   "type": "Once"
 }
}
```

## <a name="next-steps"></a>Próximas etapas

- [Usar grupos de nuvem para gerenciar atribuições de função](groups-concept.md)
- [Solução de problemas de funções atribuídas a grupos de nuvem](groups-faq-troubleshooting.md)
- [Definir as configurações de função de administrador do Azure AD no Privileged Identity Management](../privileged-identity-management/pim-how-to-change-default-settings.md)
- [Atribuir funções de recurso do Azure no Privileged Identity Management](../privileged-identity-management/pim-resource-roles-assign-roles.md)
