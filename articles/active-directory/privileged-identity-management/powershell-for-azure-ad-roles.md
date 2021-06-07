---
title: PowerShell para funções do Azure AD no PIM – Azure AD | Microsoft Docs
description: Gerencie funções do Azure AD usando cmdlets do PowerShell no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2774c63d34de3de951aa8076b56d203b976158ec
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102548743"
---
# <a name="powershell-for-azure-ad-roles-in-privileged-identity-management"></a>PowerShell para funções do Azure AD no Privileged Identity Management

Este artigo contém instruções para usar os cmdlets do PowerShell do Azure Active Directory (AD do Azure) para gerenciar funções do Azure AD no Privileged Identity Management (PIM). Ele também explica como configurar usando o módulo PowerShell do Azure AD.

> [!Note]
> Nosso PowerShell oficial só terá suporte se você estiver na nova versão do Azure AD Privileged Identity Management. Vá para Privileged Identity Management e verifique se você tem a seguinte faixa na folha início rápido.
> [![Verifique a versão do Privileged Identity Management que você tem](media/pim-how-to-add-role-to-user/pim-new-version.png "Selecione o Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox) Se você não tiver essa faixa, aguarde, pois estamos atualmente no processo de distribuir essa experiência atualizada nas próximas semanas.
> Os cmdlets Privileged Identity Management PowerShell têm suporte por meio do módulo de visualização do Azure AD. Se você estiver usando um módulo diferente e esse módulo agora estiver retornando uma mensagem de erro, comece a usar esse novo módulo. Se você tiver sistemas de produção criados com base em um módulo diferente, entre em contato com [pim_preview@microsoft.com](mailto:pim_preview@microsoft.com) .

## <a name="installation-and-setup"></a>Instalação e configuração

1. Instalar o módulo de visualização do Azure AD

    ```powershell
    Install-module AzureADPreview
    ```

1. Verifique se você tem as permissões de função necessárias antes de continuar. Se você estiver tentando executar tarefas de gerenciamento como atribuir uma atribuição de função ou atualizar a configuração de função, verifique se você tem a função de administrador global ou de administrador de função privilegiada. Se você estiver apenas tentando ativar sua própria atribuição, nenhuma permissão além das permissões de usuário padrão será necessária.

1. Conecte-se ao Azure AD.

    ```powershell
    $AzureAdCred = Get-Credential  
    Connect-AzureAD -Credential $AzureAdCred
    ```

1. Localize a ID de locatário da sua organização do Azure ad acessando **Azure Active Directory**  >    >  **ID do diretório** de propriedades. Na seção cmdlets, use essa ID sempre que precisar fornecer o resourceId.

    ![Localizar a ID da organização nas propriedades da organização do Azure AD](./media/powershell-for-azure-ad-roles/tenant-id-for-Azure-ad-org.png)

> [!Note]
> As seções a seguir são exemplos simples que podem ajudar você a colocar em funcionamento. Você pode encontrar uma documentação mais detalhada sobre os seguintes cmdlets em [https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#privileged_role_management](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#privileged_role_management) . No entanto, você deve substituir "azureResources" no parâmetro ProviderID por "aadRoles". Você também precisará se lembrar de usar a ID de locatário para sua organização do Azure AD como o parâmetro ResourceId.

## <a name="retrieving-role-definitions"></a>Recuperando definições de função

Use o cmdlet a seguir para obter todas as funções internas e personalizadas do Azure AD na sua organização do Azure AD. Essa etapa importante fornece o mapeamento entre o nome da função e o roleDefinitionId. O roleDefinitionId é usado em todos esses cmdlets para fazer referência a uma função específica.

O roleDefinitionId é específico para sua organização do Azure AD e é diferente do roleDefinitionId retornado pela API de gerenciamento de função.

```powershell
Get-AzureADMSPrivilegedRoleDefinition -ProviderId aadRoles -ResourceId 926d99e7-117c-4a6a-8031-0cc481e9da26
```

Resultado:

![Obter todas as funções para a organização do Azure AD](./media/powershell-for-azure-ad-roles/get-all-roles-result.png)

## <a name="retrieving-role-assignments"></a>Recuperando atribuições de função

Use o cmdlet a seguir para recuperar todas as atribuições de função em sua organização do Azure AD.

```powershell
Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26"
```

Use o cmdlet a seguir para recuperar todas as atribuições de função para um usuário específico. Essa lista também é conhecida como "minhas funções" no portal do AD do Azure. A única diferença aqui é que você adicionou um filtro para a ID da entidade. A ID do assunto neste contexto é a ID de usuário ou a ID do grupo.

```powershell
Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "subjectId eq 'f7d1887c-7777-4ba3-ba3d-974488524a9d'" 
```

Use o cmdlet a seguir para recuperar todas as atribuições de função para uma função específica. O roleDefinitionId aqui é a ID que é retornada pelo cmdlet anterior.

```powershell
Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "roleDefinitionId eq '0bb54a22-a3df-4592-9dc7-9e1418f0f61c'"
```

Os cmdlets resultam em uma lista de objetos de atribuição de função mostrados abaixo. A ID de assunto é a ID de usuário para a qual a função é atribuída. O estado de atribuição pode estar ativo ou qualificado. Se o usuário estiver ativo e houver uma ID no campo LinkedEligibleRoleAssignmentId, isso significa que a função está ativada no momento.

Resultado:

![Recuperar todas as atribuições de função para a organização do Azure AD](./media/powershell-for-azure-ad-roles/get-all-role-assignments-result.png)

## <a name="assign-a-role"></a>Atribuir uma função

Use o cmdlet a seguir para criar uma atribuição qualificada.

```powershell
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'ff690580-d1c6-42b1-8272-c029ded94dec' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'adminAdd' -AssignmentState 'Eligible' -schedule $schedule -reason "dsasdsas" 
```

O agendamento, que define a hora de início e de término da atribuição, é um objeto que pode ser criado como o exemplo a seguir:

```powershell
$schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
$schedule.Type = "Once"
$schedule.StartDateTime = (Get-Date).ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ss.fffZ")
$schedule.endDateTime = "2020-07-25T20:49:11.770Z"
```
> [!Note]
> Se o valor de EndDateTime for definido como NULL, ele indicará uma atribuição permanente.

## <a name="activate-a-role-assignment"></a>Ativar uma atribuição de função

Use o cmdlet a seguir para ativar uma atribuição qualificada.

```powershell
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'f55a9a68-f424-41b7-8bee-cee6a442d418' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'UserAdd' -AssignmentState 'Active' -schedule $schedule -reason "dsasdsas"
``` 

Esse cmdlet é quase idêntico ao cmdlet para criar uma atribuição de função. A principal diferença entre os cmdlets é que, para o parâmetro – Type, a ativação é "userAdd" em vez de "adminAdd". A outra diferença é que o parâmetro – Assignmentstate é "ativo" em vez de "elegível".

> [!Note]
> Há dois cenários de limitação para a ativação de função por meio do PowerShell.
> 1. Se você precisar de um número de tíquete/sistema de tíquete em sua configuração de função, não será possível fornecê-los como um parâmetro. Portanto, não seria possível ativar a função além da portal do Azure. Este recurso está sendo distribuído para o PowerShell nos próximos meses.
> 1. Se você precisar de autenticação multifator para ativação de função, atualmente não há como o PowerShell desafiar o usuário quando ele ativar sua função. Em vez disso, os usuários precisarão disparar o desafio de MFA quando se conectarem ao Azure AD seguindo [esta postagem de blog](http://www.anujchaudhary.com/2020/02/connect-to-azure-ad-powershell-with-mfa.html) de um de nossos engenheiros. Se você estiver desenvolvendo um aplicativo para PIM, uma implementação possível será desafiar os usuários e reconectá-los ao módulo depois que eles receberem um erro de "MfaRule".

## <a name="retrieving-and-updating-role-settings"></a>Recuperando e atualizando configurações de função

Use o cmdlet a seguir para obter todas as configurações de função em sua organização do Azure AD.

```powershell
Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq '926d99e7-117c-4a6a-8031-0cc481e9da26'"
```

Há quatro objetos principais na configuração. Somente três desses objetos são usados atualmente pelo PIM. As UserMemberSettings são configurações de ativação, AdminEligibleSettings são configurações de atribuição para atribuições qualificadas e o AdminmemberSettings são configurações de atribuição para atribuições ativas.

[![Obter e atualizar as configurações de função.](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png)](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png#lightbox)

Para atualizar a configuração de função, você deve obter o objeto de configuração existente para uma função específica e fazer alterações nele:

```powershell
Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq 'tenant id' and RoleDefinitionId eq 'role id'"
$settinga = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedRuleSetting
$settinga.RuleIdentifier = "JustificationRule"
$settinga.Setting = '{"required":false}'
```

Em seguida, você pode aplicar a configuração a um dos objetos para uma função específica, conforme mostrado abaixo. A ID aqui é a ID de configuração de função que pode ser recuperada do resultado do cmdlet List role Settings.

```powershell
Set-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Id 'ff518d09-47f5-45a9-bb32-71916d9aeadf' -ResourceId '3f5887ed-dd6e-4821-8bde-c813ec508cf9' -RoleDefinitionId '2387ced3-4e95-4c36-a915-73d803f93702' -UserMemberSettings $settinga 
```

## <a name="next-steps"></a>Próximas etapas

- [Atribuir uma função personalizada do Azure AD](azure-ad-custom-roles-assign.md)
- [Remover ou atualizar uma atribuição de função personalizada do Azure AD](azure-ad-custom-roles-update-remove.md)
- [Configurar uma atribuição de função personalizada do Azure AD](azure-ad-custom-roles-configure.md)
- [Definições de função no Azure AD](../roles/permissions-reference.md)
