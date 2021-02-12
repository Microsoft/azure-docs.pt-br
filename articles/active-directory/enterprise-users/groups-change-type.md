---
title: Alterar a associação de grupo estático para dinâmico – Azure AD | Microsoft Docs
description: Saiba como converter grupos existentes de associação estática para dinâmica usando o centro de administração do Azure AD ou cmdlets do PowerShell.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a46cc3f4a0f2fb25fc693103a64a319dcec0324
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96860874"
---
# <a name="change-static-group-membership-to-dynamic-in-azure-active-directory"></a>Alterar o tipo de associação de grupo estático para dinâmico no Microsoft Azure Active Directory

Você pode alterar a associação do grupo de estático para dinâmico (ou vice-versa) no Microsoft Azure Active Directory (Azure AD). O Microsoft Azure Active Directory mantém o mesmo nome do grupo e ID no sistema, para que todas as referências existentes ao grupo ainda são válidas. Se você criar um novo grupo, em vez disso, você precisaria atualizar essas referências. Associação dinâmica de grupo elimina a adição de sobrecarga de gerenciamento e a remoção de usuários. Este artigo informa como converter grupos existentes de estático para associação dinâmica usando o Centro de administração Microsoft Azure Active Directory ou cmdlets do PowerShell.

> [!WARNING]
> Ao alterar um grupo estático existente para um grupo dinâmico, todos os membros existentes serão removidos do grupo e, em seguida, a regra de associação será processada para adicionar novos membros. Se o grupo for utilizado para controlar o acesso a aplicativos ou recursos, observe os membros originais poderão perder o acesso até que a regra de associação seja totalmente processada.
>
> É recomendável testar a nova regra de associação antes para garantir que a nova associação no grupo esteja conforme esperado.

## <a name="change-the-membership-type-for-a-group"></a>Alterar o tipo de associação para um grupo

1. Entre no centro de [Administração do Azure ad](https://aad.portal.azure.com) com uma conta que seja um administrador global, administrador de usuário ou administrador de grupos na sua organização do Azure AD.
2. Selecione **Grupos**.
3. Na lista **Todos os grupos**, abra o grupo que você deseja alterar.
4. Selecione **Propriedades**.
5. Na página **Propriedades** do grupo, selecione um **Tipo de Associação** entre Atribuído (estático), Usuário Dinâmico ou Dispositivo Dinâmico, dependendo do tipo de associação desejado. Para associação dinâmica, é possível usar o construtor de regras para selecionar opções para uma regra simples ou gravar uma regra avançada de associação. 

As etapas a seguir são um exemplo de alteração de um grupo de associação estática para dinâmica para um grupo de usuários.

1. Na página **Propriedades** do grupo selecionado, selecione um **Tipo de Associação** do **Usuário Dinâmico** e, em seguida, selecione Sim na caixa de diálogo explicando as alterações na associação do grupo para continuar. 
  
   ![selecionar tipo de associação do usuário dinâmico](./media/groups-change-type/select-group-to-convert.png)
  
2. Selecione **Adicionar consulta dinâmica** e, em seguida, forneça a regra.
  
   ![Insira a regra para o grupo dinâmico](./media/groups-change-type/enter-rule.png)
  
3. Depois de criar a regra, selecione **Adicionar consulta** na parte inferior da página.
4. Selecione **Salvar** na página **Propriedades** para o grupo salvar suas alterações. O **Tipo de Associação** do grupo é imediatamente atualizado na lista de grupos.

> [!TIP]
> A conversão de grupo poderá falhar se a regra de associação que você inseriu estiver incorreta. Uma notificação é exibida no canto superior direito do portal, explicando porque a regra não pode ser aceita pelo sistema. Leia com atenção para entender como você pode ajustar a regra para torná-la válida. Para obter exemplos de sintaxe de regra e uma lista completa de propriedades com suporte, operadores e valores para uma regra de associação, consulte [regras de associação dinâmica para grupos no Microsoft Azure Active Directory](groups-dynamic-membership.md).

## <a name="change-membership-type-for-a-group-powershell"></a>Alterar o tipo de associação para um grupo (PowerShell)

> [!NOTE]
> Para alterar as propriedades de grupo dinâmico, você precisará usar os cmdlets **da versão prévia da** [versão 2 do PowerShell do Azure AD](/powershell/azure/active-directory/install-adv2). Você pode instalar a versão prévia da [Galeria do PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).

Aqui está um exemplo de funções que alternam o gerenciamento de associação em um grupo existente. Neste exemplo, é preciso ter cuidado para manipular corretamente a propriedade GroupTypes e preservar quaisquer valores não relacionados à associação dinâmica.

```powershell
#The moniker for dynamic groups as used in the GroupTypes property of a group object
$dynamicGroupTypeString = "DynamicMembership"

function ConvertDynamicGroupToStatic
{
    Param([string]$groupId)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -eq $null -or !$groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a static group. Aborting conversion.";
    }


    #remove the type for dynamic groups, but keep the other type values
    $groupTypes.Remove($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to remove the dynamic type, ii) pause execution of the current rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "Paused"
}

function ConvertStaticGroupToDynamic
{
    Param([string]$groupId, [string]$dynamicMembershipRule)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -ne $null -and $groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a dynamic group. Aborting conversion.";
    }
    #add the dynamic group type to existing types
    $groupTypes.Add($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to add the dynamic type, ii) start execution of the rule, iii) set the rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "On" -MembershipRule $dynamicMembershipRule
}
```
Para tornar um grupo estático:

```powershell
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```

Para tornar um grupo estático:

```powershell
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```

## <a name="next-steps"></a>Próximas etapas

Esses artigos fornecem mais informações sobre grupos no Azure Active Directory.

* [Consultar grupos existentes](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Criar um novo grupo e adicionando membros](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Gerenciar configurações de um grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Gerenciar associações de um grupo](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Gerenciar regras dinâmicas para usuários em um grupo](groups-dynamic-membership.md)
