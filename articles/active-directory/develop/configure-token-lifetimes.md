---
title: Definir tempos de vida para tokens
titleSuffix: Microsoft identity platform
description: Saiba como definir tempos de vida para tokens emitidos pela plataforma de identidade da Microsoft. Saiba como saber como gerenciar a política padrão de uma organização, criar uma política para entrada na Web, criar uma política para um aplicativo nativo que chama uma API da Web e gerenciar uma política avançada.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 02/01/2021
ms.author: ryanwi
ms.custom: aaddev, content-perf, FY21Q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: 3ec94543a53e3e5b7709801de8f4cf1dde3fc3d9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99428108"
---
# <a name="configure-token-lifetime-policies-preview"></a>Configurar políticas de tempo de vida do token (versão prévia)
Você pode especificar o tempo de vida de um token de acesso, SAML ou ID emitido pela plataforma de identidade da Microsoft. Configure os tempos de vida de token de todos os aplicativos em uma organização, para um aplicativo multilocatário (várias organizações) ou para uma entidade de serviço específica em sua organização. Para obter mais informações, leia [tempos de vida de token configuráveis](active-directory-configurable-token-lifetimes.md).

Nesta seção, percorremos um cenário de política comum que pode ajudá-lo a impor novas regras para o tempo de vida do token. No exemplo, você aprende a criar uma política que exige que os usuários se autentiquem com mais frequência em seu aplicativo Web.

## <a name="get-started"></a>Introdução
Para começar, execute uma destas etapas:

1. Baixe a versão mais recente da [Visualização pública do módulo do Azure ad PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
1. Execute o comando `Connect` para entrar em sua conta do administrador do Azure AD. Execute esse comando sempre que você iniciar uma nova sessão.

    ```powershell
    Connect-AzureAD -Confirm
    ```

1. Para ver todas as políticas que foram criadas em sua organização, execute o cmdlet [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) .  Todos os resultados com valores de propriedade definidos que diferem dos padrões listados acima estão no escopo da desativação.

    ```powershell
    Get-AzureADPolicy -All $true
    ```

1. Para ver quais aplicativos e entidades de serviço estão vinculados a uma política específica que você identificou, execute o seguinte cmdlet [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) substituindo **1a37dad8-5da7-4cc8-87c7-efbc0326cf20** por qualquer uma das suas IDs de política. Em seguida, você pode decidir se deseja configurar a frequência de entrada de acesso condicional ou permanecer com os padrões do Azure AD.

    ```powershell
    Get-AzureADPolicyAppliedObject -id 1a37dad8-5da7-4cc8-87c7-efbc0326cf20
    ```

Se seu locatário tiver políticas que definem valores personalizados para as propriedades de atualização e de configuração de token de sessão, a Microsoft recomenda que você atualize essas políticas para valores que reflitam os padrões descritos acima. Se nenhuma alteração for feita, o Azure AD honrará automaticamente os valores padrão.

## <a name="create-a-policy-for-web-sign-in"></a>Criar uma política para entrada na Web

Neste exemplo, crie uma política que exige dos usuários a autenticação mais frequente no aplicativo Web. Essa política define o tempo de vida dos tokens de acesso/Id e a idade máxima de um token de sessão multifator para a entidade de serviço do aplicativo Web.

1. Crie uma política de tempo de vida de token.

    Essa política, para entrada na Web, define o tempo de vida do token de acesso/ID e a idade máxima do Token de sessão de fator único como duas horas.

    1. Para criar a política, execute o cmdlet [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Para ver sua nova política e obter o **ObjectID** da política, execute o cmdlet [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Atribuir a política à entidade de serviço. Você também precisa obter o **ObjectID** da entidade de serviço.

    1. Use o cmdlet [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) para ver todas as entidades de serviço de sua organização ou uma única entidade de serviço.
        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
        ```

    1. Quando você tiver a entidade de serviço, execute o cmdlet [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) :
        ```powershell
        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

## <a name="create-token-lifetime-policies-for-refresh-and-session-tokens"></a>Criar políticas de tempo de vida de token para atualização e tokens de sessão
> [!IMPORTANT]
> A partir de 30 de janeiro de 2021, você não pode configurar tempos de vida de atualização e de token de sessão. Azure Active Directory não honra mais a configuração de token de atualização e de sessão nas políticas existentes.  Novos tokens emitidos após os tokens existentes expiraram agora são definidos para a [configuração padrão](active-directory-configurable-token-lifetimes.md#configurable-token-lifetime-properties-after-the-retirement). Você ainda pode configurar o acesso, SAML e tempos de vida de token de ID após a atualização e a desativação da configuração do token de sessão.
>
> O tempo de vida do token existente não será alterado. Depois que eles expirarem, um novo token será emitido com base no valor padrão.
>
> Se você precisar continuar a definir o período de tempo antes que um usuário seja solicitado a entrar novamente, configure a frequência de entrada no acesso condicional. Para saber mais sobre o acesso condicional, leia [Configurar gerenciamento de sessão de autenticação com acesso condicional](../conditional-access/howto-conditional-access-session-lifetime.md).

### <a name="manage-an-organizations-default-policy"></a>Gerenciar a política padrão de uma organização
Neste exemplo, você cria uma política que permite que os usuários façam logon com menos frequência em toda a organização. Para fazer isso, crie uma política de tempo de vida de token para tokens de atualização de fator único, que é aplicada em sua organização. Essa política será aplicada a todos os aplicativos no a organização e a cada entidade de serviço que ainda não tenha uma política definida para ela.

1. Crie uma política de tempo de vida de token.

    1. Defina o token de atualização de fator único para "até a revogação". O token não expira até que o acesso seja revogado. Crie a seguinte definição de política:

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    1. Para criar a política, execute o cmdlet [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. Para remover qualquer espaço em branco, execute o cmdlet [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        Get-AzureADPolicy -id | set-azureadpolicy -Definition @($((Get-AzureADPolicy -id ).Replace(" ","")))
        ```

    1. Para ver a nova política e obter a **ObjectId** da política, execute o comando a seguir:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Atualizar a política.

    Talvez você decida que a primeira política definida neste exemplo não é tão estrita quanto seu serviço exige. Para definir o token de atualização de fator único para expirar em dois dias, execute o seguinte comando:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Criar uma política para um aplicativo nativo que chama uma API da Web
Neste exemplo, crie uma política que exige dos usuários a autenticação menos frequente. A política também aumenta a quantidade de tempo que um usuário pode ficar inativo antes que precise autenticar novamente. A política é aplicada à API da web. Quando o aplicativo nativo solicita a API da Web como um recurso, essa política é aplicada.

1. Crie uma política de tempo de vida de token.

    1. Para criar uma política estrita para uma API Web, execute o cmdlet [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Para ver sua nova política, execute o seguinte comando:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Atribua a política à sua API da Web. Você também precisará da **ObjectId** de seu aplicativo. Use o cmdlet [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) para localizar o **ObjectID** do seu aplicativo ou use o [portal do Azure](https://portal.azure.com/).

    Obtenha o **ObjectID** do seu aplicativo e atribua a política:

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

### <a name="manage-an-advanced-policy"></a>Gerenciar uma política avançada
Neste exemplo, você cria algumas políticas para saber como o sistema de prioridade funciona. Você também aprenderá como gerenciar várias políticas que são aplicadas a vários objetos.

1. Crie uma política de tempo de vida de token.

    1. Para criar uma política padrão da organização que defina o tempo de vida do token de atualização de fator único para 30 dias, execute o cmdlet [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. Para ver sua nova política, execute o cmdlet [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Atribuir a política a uma entidade de serviço.

    Agora, você tem uma política que se aplica a toda a organização. Convém preservar essa política de 30 dias para uma entidade de serviço específica, mas altere a política padrão de organização para o limite superior de "until-revoked".

    1. Para ver todas as entidades de serviço de sua organização, use o cmdlet [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) .

    1. Quando você tiver a entidade de serviço, execute o cmdlet [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

1. Defina o sinalizador `IsOrganizationDefault` como false:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

1. Crie uma nova política padrão de organização:

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    Agora você tem a política original vinculada à entidade de serviço, e a nova política definida como a política padrão de organização. É importante lembrar que as políticas aplicadas a entidades de serviço têm prioridade sobre as políticas padrão de organização.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre os [recursos de gerenciamento de sessão de autenticação](../conditional-access/howto-conditional-access-session-lifetime.md) no acesso condicional do Azure AD.
