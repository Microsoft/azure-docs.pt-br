---
title: Configurar como os usuários finais consentem nos aplicativos que usam o Azure AD
description: Saiba como gerenciar como e quando os usuários podem consentir nos aplicativos que terão acesso aos dados da sua organização.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2020
ms.author: kenwith
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: 68bb846ebb0199691161bc501441df908eb8ad87
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101643602"
---
# <a name="configure-how-end-users-consent-to-applications"></a>Configurar como os usuários finais consentem nos aplicativos

Você pode integrar seus aplicativos à plataforma de identidade da Microsoft para permitir que os usuários entrem com sua conta corporativa ou de estudante e acessem os dados da sua organização a fim de fornecer experiências avançadas controladas por dados.

Antes que um aplicativo possa acessar os dados da sua organização, um usuário precisa conceder permissões ao aplicativo para isso. Permissões diferentes permitem diferentes níveis de acesso. Por padrão, todos os usuários têm permissão para consentir nos aplicativos em relação a permissões que não exijam o consentimento do administrador. Por exemplo, por padrão, um usuário pode permitir que um aplicativo acesse sua caixa de correio, mas não pode permitir que um aplicativo tenha acesso irrestrito para ler e gravar em todos os arquivos em sua organização.

Ao permitir que os usuários concedam aos aplicativos acesso aos dados, os usuários poderão facilmente adquirir aplicativos úteis e ser produtivos. No entanto, em algumas situações, essa configuração pode representar um risco se não for monitorada e controlada com cuidado.

> [!IMPORTANT]
> Para reduzir o risco de aplicativos mal-intencionados tentarem induzir os usuários a conceder acesso aos dados da sua organização, recomendamos que você permita o consentimento do usuário apenas em aplicativos que foram publicados por um [fornecedor verificado](../develop/publisher-verification-overview.md).

## <a name="user-consent-settings"></a>Configurações de consentimento do usuário

As políticas de consentimento do aplicativo descrevem as condições que devem ser atendidas antes que um aplicativo possa ser consentido. Essas políticas podem incluir condições no aplicativo que solicita acesso, bem como as permissões que o aplicativo está solicitando.

Ao escolher quais políticas de consentimento do aplicativo se aplicam a todos os usuários, você pode definir limites quando os usuários finais têm permissão para conceder consentimento aos aplicativos e quando eles serão solicitados a solicitar a revisão e a aprovação do administrador:

* **Desabilitar o consentimento do usuário**: os usuários não podem conceder permissões a aplicativos. Os usuários podem continuar a entrar em aplicativos que tinham consentido anteriormente ou cujo consentimento tenha sido feito por administradores em seu nome, mas eles não terão permissão para dar consentimento a novas permissões ou a novos aplicativos por conta própria. Somente os usuários que receberam uma função de diretório que inclui a permissão para conceder consentimento poderão consentir os novos aplicativos.

* **Os usuários podem consentir aplicativos de editores verificados ou de sua organização, mas somente para permissões que você selecionar** – todos os usuários só podem dar consentimento a aplicativos que foram publicados por um [Publicador verificado](../develop/publisher-verification-overview.md) e aplicativos que são registrados em seu locatário. Os usuários só podem consentir as permissões que você classificou como "impacto baixo". Você deve [classificar permissões](configure-permission-classifications.md) para selecionar quais permissões os usuários têm permissão para consentir.

* **Os usuários podem consentir com todos os aplicativos** – essa opção permite que todos os usuários consentim em qualquer permissão que não exija o consentimento do administrador, para qualquer aplicativo.

* **Política de consentimento de aplicativo personalizada** -para obter mais opções sobre as condições que regem o consentimento do usuário, você pode [criar uma política de consentimento de aplicativo personalizada](manage-app-consent-policies.md#create-a-custom-app-consent-policy)e configurá-las para serem aplicadas ao consentimento do usuário.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para definir as configurações de consentimento do usuário por meio do portal do Azure:

1. Entre no [portal do Azure](https://portal.azure.com) como um [Administrador Global](../roles/permissions-reference.md#global-administrator).
1. Selecione **Azure Active Directory** > **Aplicativos empresariais** > **Consentimento e permissões** > **Configurações de consentimento do usuário**.
1. Em **Consentimento do usuário para aplicativos**, selecione a configuração de consentimento que você deseja configurar para todos os usuários.
1. Selecione **salvar** para salvar suas configurações.

:::image type="content" source="media/configure-user-consent/setting-for-all-users.png" alt-text="Configurações de consentimento do usuário":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Você pode usar o último módulo de visualização do PowerShell do Azure AD, [AzureADPreview](/powershell/azure/active-directory/install-adv2?preserve-view=true&view=azureadps-2.0-preview), para escolher qual política de consentimento do aplicativo governa o consentimento do usuário para aplicativos.

#### <a name="disable-user-consent"></a>Desabilitar consentimento do usuário

Para desabilitar o consentimento do usuário, defina as políticas de consentimento que regem o consentimento do usuário como vazio:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @()
  ```

#### <a name="allow-user-consent-subject-to-an-app-consent-policy"></a>Permitir o consentimento do usuário sujeito a uma política de consentimento do aplicativo

Para permitir o consentimento do usuário, escolha qual política de consentimento do aplicativo deve controlar a autorização dos usuários para conceder consentimento aos aplicativos:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("managePermissionGrantsForSelf.{consent-policy-id}")
  ```

Substitua `{consent-policy-id}` pela ID da política que você deseja aplicar. Você pode escolher uma [política de consentimento de aplicativo personalizada](manage-app-consent-policies.md#create-a-custom-app-consent-policy) que você criou ou pode escolher entre as seguintes políticas internas:

| ID | Descrição |
|:---|:------------|
| Microsoft-usuário-padrão-baixo | **Permitir consentimento do usuário para aplicativos de Publicadores verificados, para permissões selecionadas**<br /> Permitir consentimento de usuário limitado somente para aplicativos de Publicadores e aplicativos verificados em seu locatário e somente para permissões que você classificar como "impacto baixo". (Não se esqueça de [classificar permissões](configure-permission-classifications.md) para selecionar a quais permissões os usuários têm permissão para consentir.) |
| Microsoft-usuário-padrão-herdado | **Permitir consentimento do usuário para aplicativos**<br /> Essa opção permite que todos os usuários consentim em qualquer permissão que não exija o consentimento do administrador, para qualquer aplicativo |
  
Por exemplo, para habilitar o consentimento do usuário sujeito à política interna `microsoft-user-default-low` :

```powershell
Set-AzureADMSAuthorizationPolicy `
   -Id "authorizationPolicy" `
   -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("managePermissionGrantsForSelf.microsoft-user-default-low")
```

---

> [!TIP]
> [Habilite o fluxo de trabalho de consentimento do administrador](configure-admin-consent-workflow.md) para permitir que os usuários solicitem uma revisão e aprovação do administrador de um aplicativo ao qual o usuário não tenha permissão para consentir — por exemplo, quando o consentimento do usuário foi desabilitado ou quando um aplicativo está solicitando permissões que o usuário não tem permissão para conceder.

## <a name="risk-based-step-up-consent"></a>Consentimento em etapas com base em risco

O consentimento de step-up baseado em risco ajuda a reduzir a exposição do usuário a aplicativos mal-intencionados que fazem [solicitações de consentimento ilícitas](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants). Se a Microsoft detectar uma solicitação arriscada de consentimento ao usuário final, a solicitação exigirá um "step-up" para o consentimento do administrador. Esse recurso é habilitado por padrão, mas só resultará em uma alteração de comportamento quando o consentimento do usuário final estiver habilitado.

Quando uma solicitação de consentimento arriscado for detectada, a solicitação de consentimento exibirá uma mensagem indicando que a aprovação do administrador é necessária. Se o [fluxo de trabalho de solicitação de consentimento do administrador](configure-admin-consent-workflow.md) estiver habilitado, o usuário poderá enviar a solicitação a um administrador para análise diretamente do prompt de consentimento. Se não estiver habilitado, a seguinte mensagem será exibida:

* **AADSTS90094:** &lt;clientAppDisplayName&gt; precisa de permissão para acessar recursos em sua organização que apenas um administrador pode conceder. Peça a um administrador para conceder permissão ao aplicativo antes de poder usá-lo.

Nesse caso, um evento de auditoria também será registrado com a categoria "ApplicationManagement", o tipo de atividade "Consentimento para aplicativo" e o motivo do status "Aplicativo arriscado detectado".

> [!IMPORTANT]
> Os administradores devem [avaliar todas as solicitações de consentimento](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent) cuidadosamente antes de aprová-las, especialmente quando a Microsoft tiver detectado risco.

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>Desabilitar ou reabilitar o consentimento de step-up baseado em risco usando o PowerShell

Você pode usar o módulo de versão prévia do PowerShell do Azure AD, [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview), para desabilitar o step-up até o consentimento do administrador necessário quando a Microsoft detecta um risco ou reabilitá-lo se tiver sido desabilitado anteriormente.

1. Verifique se você está usando o módulo [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview). Esta etapa será importante se você tiver instalado os módulos [AzureAD](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0) e [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview)).

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

1. Conecte-se ao Azure AD PowerShell.

   ```powershell
   Connect-AzureAD
   ```

1. Recupere o valor atual das configurações do diretório **Configurações de Política de Consentimento** em seu locatário. Isso exige verificar se as configurações de diretório para esse recurso foram criadas e, se não, usar os valores do modelo de configurações de diretório correspondente.

    ```powershell
    $consentSettingsTemplateId = "dffd5d46-495d-40a9-8e21-954ff55e198a" # Consent Policy Settings
    $settings = Get-AzureADDirectorySetting -All $true | Where-Object { $_.TemplateId -eq $consentSettingsTemplateId }

    if (-not $settings) {
        $template = Get-AzureADDirectorySettingTemplate -Id $consentSettingsTemplateId
        $settings = $template.CreateDirectorySetting()
    }

    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```

1. Entenda o valor das configurações:

    | Configuração       | Type         | Descrição  |
    | ------------- | ------------ | ------------ |
    | _BlockUserConsentForRiskyApps_   | Boolean |  Sinalizador que indica se o consentimento do usuário será bloqueado quando uma solicitação arriscada for detectada. |

1. Atualize o valor das configurações para a configuração desejada:

    ```powershell
    # Disable risk-based step-up consent entirely
    $riskBasedConsentEnabledValue.Value = "False"
    ```

    ```powershell
    # Re-enable risk-based step-up consent, if disabled previously
    $riskBasedConsentEnabledValue.Value = "True"
    ```

1. Salve suas configurações.

    ```powershell
    if ($settings.Id) {
        # Update an existing directory settings
        Set-AzureADDirectorySetting -Id $settings.Id -DirectorySetting $settings
    } else {
        # Create a new directory settings to override the default setting 
        New-AzureADDirectorySetting -DirectorySetting $settings
    }
    ```

## <a name="next-steps"></a>Próximas etapas

Para saber mais:

* [Definir configurações de consentimento do usuário](configure-user-consent.md)
* [Gerenciar as políticas de consentimento do aplicativo](manage-app-consent-policies.md)
* [Configurar o fluxo de trabalho de consentimento do administrador](configure-admin-consent-workflow.md)
* [Saiba como gerenciar o consentimento em aplicativos e avaliar solicitações de consentimento](manage-consent-requests.md)
* [Conceder consentimento de administrador em todo o locatário para um aplicativo](grant-admin-consent.md)
* [Permissões e consentimento na plataforma de identidade da Microsoft](../develop/v2-permissions-and-consent.md)

Para obter ajuda ou encontrar respostas às suas perguntas:
* [Azure AD no Microsoft Q&A.](/answers/topics/azure-active-directory.html)