---
title: Atribuir rótulos de sensibilidade a grupos – Azure AD | Microsoft Docs
description: Saiba como atribuir rótulos de sensibilidade a grupos. Consulte informações de solução de problemas e exiba recursos adicionais disponíveis.
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
ms.openlocfilehash: a78cf9b7d78078030ac0db8bd2f0fddb93a8dda4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97881389"
---
# <a name="assign-sensitivity-labels-to-microsoft-365-groups-in-azure-active-directory"></a>Atribuir rótulos de sensibilidade a grupos de Microsoft 365 no Azure Active Directory

O Azure Active Directory (AD do Azure) dá suporte à aplicação de rótulos de sensibilidade publicados pelo [centro de conformidade do Microsoft 365](https://sip.protection.office.com/homepage) a grupos de Microsoft 365. Os rótulos de sensibilidade se aplicam ao grupo entre serviços, como o Outlook, o Microsoft Teams e o SharePoint. Para obter mais informações sobre o suporte a aplicativos Microsoft 365, consulte [suporte a Microsoft 365 para rótulos de sensibilidade](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#support-for-the-sensitivity-labels).

> [!IMPORTANT]
> Para configurar esse recurso, deve haver pelo menos uma licença do Active Azure Active Directory Premium P1 na sua organização do Azure AD.

## <a name="enable-sensitivity-label-support-in-powershell"></a>Habilitar o suporte ao rótulo de sensibilidade no PowerShell

Para aplicar rótulos publicados a grupos, você deve primeiro habilitar o recurso. Essas etapas habilitam o recurso no Azure AD.

1. Abra uma janela do Windows PowerShell em seu computador. Você pode abri-lo sem privilégios elevados.
1. Execute os comandos a seguir para preparar para executar os cmdlets.

    ```PowerShell
    Import-Module AzureADPreview
    Connect-AzureAD
    ```

    Na página **entrar na sua conta** , insira sua conta de administrador e senha para conectá-lo ao serviço e selecione **entrar**.
1. Busque as configurações de grupo atuais para a organização do Azure AD.

    ```PowerShell
    $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
    ```

    > [!NOTE]
    > Se nenhuma configuração de grupo tiver sido criada para esta organização do Azure AD, você receberá um erro no cmdlet acima que lê "não é possível associar o argumento ao parâmetro ' ID ' porque ele é nulo". Nesse caso, você deve primeiro criar as configurações. Siga as etapas em [Azure Active Directory cmdlets para definir configurações de grupo](../enterprise-users/groups-settings-cmdlets.md) para criar configurações de grupo para esta organização do Azure AD.

1. Em seguida, exiba as configurações de grupo atuais.

    ```PowerShell
    $Setting.Values
    ```

1. Em seguida, habilite o recurso:

    ```PowerShell
    $Setting["EnableMIPLabels"] = "True"
    ```

1. Em seguida, salve as alterações e aplique as configurações:

    ```PowerShell
    Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
    ```

Você também precisará sincronizar seus rótulos de sensibilidade com o Azure AD. Para obter instruções, consulte [como habilitar rótulos de sensibilidade para contêineres e sincronizar rótulos](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#how-to-enable-sensitivity-labels-for-containers-and-synchronize-labels).

## <a name="assign-a-label-to-a-new-group-in-azure-portal"></a>Atribuir um rótulo a um novo grupo no portal do Azure

1. Entre no centro de [Administração do Azure ad](https://aad.portal.azure.com).
1. Selecione **grupos** e, em seguida, selecione **novo grupo**.
1. Na página **novo grupo** , selecione **Office 365** e preencha as informações necessárias para o novo grupo e selecione um rótulo de sensibilidade na lista.

   ![Atribuir um rótulo de sensibilidade na página novos grupos](./media/groups-assign-sensitivity-labels/new-group-page.png)

1. Salve as alterações e selecione **criar**.

Seu grupo é criado e as configurações de site e grupo associadas ao rótulo selecionado são automaticamente impostas.

## <a name="assign-a-label-to-an-existing-group-in-azure-portal"></a>Atribuir um rótulo a um grupo existente no portal do Azure

1. Entre no centro de [Administração do Azure ad](https://aad.portal.azure.com) com uma conta de administrador de grupos ou como um proprietário de grupo.
1. Selecione **Grupos**.
1. Na página **todos os grupos** , selecione o grupo que você deseja rotular.
1. Na página do grupo selecionado, selecione **Propriedades** e selecione um rótulo de sensibilidade na lista.

   ![Atribuir um rótulo de sensibilidade na página Visão geral de um grupo](./media/groups-assign-sensitivity-labels/assign-to-existing.png)

1. Selecione **Salvar** para salvar as alterações.

## <a name="remove-a-label-from-an-existing-group-in-azure-portal"></a>Remover um rótulo de um grupo existente no portal do Azure

1. Entre no centro de [Administração do Azure ad](https://aad.portal.azure.com) com uma conta de administrador global ou de grupos ou como um proprietário de grupo.
1. Selecione **Grupos**.
1. Na página **todos os grupos** , selecione o grupo do qual você deseja remover o rótulo.
1. Na página **grupo** , selecione **Propriedades**.
1. Selecione **Remover**.
1. Selecione **Salvar** para salvar suas alterações.

## <a name="using-classic-azure-ad-classifications"></a>Usando classificações clássicas do Azure AD

Depois de habilitar esse recurso, as classificações "clássicas" para grupos só aparecerão em grupos e sites existentes, e você deverá usá-los para novos grupos somente se criar grupos em aplicativos que não dão suporte a rótulos de sensibilidade. O administrador poderá convertê-los em rótulos de sensibilidade posteriormente, se necessário. As classificações clássicas são as classificações antigas que você define definindo valores para a `ClassificationList` configuração no PowerShell do Azure AD. Quando esse recurso estiver habilitado, essas classificações não serão aplicadas a grupos.

## <a name="troubleshooting-issues"></a>Solução de problemas

### <a name="sensitivity-labels-are-not-available-for-assignment-on-a-group"></a>Os rótulos de sensibilidade não estão disponíveis para atribuição em um grupo

A opção de rótulo de sensibilidade só é exibida para grupos quando todas as condições a seguir são atendidas:

1. Os rótulos são publicados no centro de conformidade Microsoft 365 para esta organização do Azure AD.
1. O recurso está habilitado, EnableMIPLabels é definido como true no do módulo do PowerShell do Azure AD.
1. Os rótulos são sincronizados com o Azure AD com o cmdlet Execute-AzureAdLabelSync no módulo Security & Compliance PowerShell.
1. O grupo é um grupo de Microsoft 365.
1. A organização tem uma licença do Active Azure Active Directory Premium P1.
1. O usuário conectado atual tem privilégios suficientes para atribuir rótulos. O usuário deve ser um administrador global, administrador de grupo ou o proprietário do grupo.

Certifique-se de que todas as condições sejam atendidas para atribuir rótulos a um grupo.

### <a name="the-label-i-want-to-assign-is-not-in-the-list"></a>O rótulo que desejo atribuir não está na lista

Se o rótulo que você está procurando não estiver na lista, esse pode ser o caso por um dos seguintes motivos:

- O rótulo pode não ser publicado no centro de conformidade Microsoft 365. Isso também pode ser aplicado a rótulos que não são mais publicados. Consulte o administrador para obter mais informações.
- O rótulo pode ser publicado, no entanto, não está disponível para o usuário que está conectado. Consulte o administrador para obter mais informações sobre como obter acesso ao rótulo.

### <a name="how-to-change-the-label-on-a-group"></a>Como alterar o rótulo em um grupo

Os rótulos podem ser trocados a qualquer momento usando as mesmas etapas que atribui um rótulo a um grupo existente, da seguinte maneira:

1. Entre no centro de [Administração do Azure ad](https://aad.portal.azure.com) com uma conta global ou de administrador de grupo ou como proprietário do grupo.
1. Selecione **Grupos**.
1. Na página **todos os grupos** , selecione o grupo que você deseja rotular.
1. Na página do grupo selecionado, selecione **Propriedades** e selecione um novo rótulo de sensibilidade na lista.
1. Clique em **Salvar**.

### <a name="group-setting-changes-to-published-labels-are-not-updated-on-the-groups"></a>As alterações de configuração de grupo em rótulos publicados não são atualizadas nos grupos

Como prática recomendada, não recomendamos que você altere as configurações de grupo para um rótulo depois que o rótulo for aplicado a grupos. Quando você faz alterações nas configurações de grupo associadas aos rótulos publicados no [centro de conformidade Microsoft 365](https://sip.protection.office.com/homepage), essas alterações de política não são aplicadas automaticamente nos grupos afetados.

Se você precisar fazer uma alteração, use um [script do PowerShell do Azure ad](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1) para aplicar manualmente as atualizações aos grupos afetados. Esse método garante que todos os grupos existentes impõem a nova configuração.

## <a name="next-steps"></a>Próximas etapas

- [Use rótulos de sensibilidade com o Microsoft Teams, grupos de Microsoft 365 e sites do SharePoint](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)
- [Atualizar grupos após a alteração da política de rótulo manualmente com o script do PowerShell do Azure AD](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1)
- [Editar as configurações de grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Gerenciar grupos usando comandos do PowerShell](../enterprise-users/groups-settings-v2-cmdlets.md)
