---
title: Habilitar registro de informações de segurança combinadas-Azure Active Directory
description: Saiba como simplificar a experiência do usuário final com a autenticação multifator do Azure AD e o registro de redefinição de senha de autoatendimento.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 01/27/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: dcfaef2f518028762958477a5b0d326acc237d1f
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98938439"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>Habilitar o registro combinado de informações de segurança no Azure Active Directory

Antes do registro combinado, os usuários registraram métodos de autenticação para a autenticação multifator do Azure AD e a SSPR (redefinição de senha de autoatendimento) separadamente. As pessoas foram confundidas de que métodos semelhantes foram usados para a autenticação multifator do Azure AD e o SSPR, mas tinham que se registrar para ambos os recursos. Agora, com o registro combinado, os usuários podem registrar uma vez e obter os benefícios da autenticação multifator do Azure AD e do SSPR.

> [!NOTE]
> A partir de 15 de agosto de 2020, todos os novos locatários do Azure AD serão habilitados automaticamente para o registro combinado. 

Para entender a funcionalidade e os efeitos antes de habilitar a nova experiência, consulte os [conceitos combinados de registro de informações de segurança](concept-registration-mfa-sspr-combined.md).

![Experiência aprimorada do registro combinado de informações de segurança](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>Habilitar o registro combinado

Para habilitar o registro combinado, conclua estas etapas:

1. Entre no portal do Azure como administrador global ou de usuários.
2. Acesse **Azure Active Directory** > **Configurações do usuário** > **Gerenciar as configurações de visualização de recursos do usuário**.
3. Em **Os usuários podem usar a experiência de registro combinado de informações de segurança**, opte por habilitar para um grupo **Seleto** de usuários ou **Todos** os usuários.

   ![Habilitar a experiência de informações de segurança combinadas para usuários](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info.png)

> [!NOTE]
> Depois de habilitar o registro combinado, os usuários que registrarem ou confirmarem seu número de telefone ou aplicativo móvel por meio da nova experiência poderão usá-los para a autenticação multifator do Azure AD e o SSPR, se esses métodos estiverem habilitados nas políticas de SSPR e autenticação multifator do Azure AD.
>
> Se você desabilitar essa experiência, os usuários que acessarem a página de registro SSPR anterior em `https://aka.ms/ssprsetup` serão necessários para executar a autenticação multifator antes de poderem acessar a página.

Se você tiver configurado a *lista de atribuição de site a zona* no Internet Explorer, os seguintes sites devem estar na mesma zona:

* *[https://login.microsoftonline.com](https://login.microsoftonline.com)*
* *[https://mysignins.microsoft.com](https://mysignins.microsoft.com)*
* *[https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)*

## <a name="conditional-access-policies-for-combined-registration"></a>Políticas de acesso condicional para o registro combinado

Para proteger quando e como os usuários se registram para a autenticação multifator do Azure AD e a redefinição de senha de autoatendimento, você pode usar as ações do usuário na política de acesso condicional. Essa funcionalidade pode ser habilitada em organizações que desejam que os usuários se registrem para autenticação multifator do Azure AD e SSPR de um local central, como um local de rede confiável durante a integração de RH.

> [!NOTE]
> Essa política se aplica somente quando um usuário acessa uma página de registro combinada. Essa política não impõe o registro de MFA quando um usuário acessa outros aplicativos.
>
> Você pode criar uma política de registro de MFA usando [a proteção de identidade do Azure – configurar a política de MFA](../identity-protection/howto-identity-protection-configure-mfa-policy.md).

Para obter mais informações sobre como criar locais confiáveis no acesso condicional, consulte [qual é a condição de local em Azure Active Directory acesso condicional?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Criar uma política para exigir o registro de um local confiável

Conclua as etapas a seguir para criar uma política que se aplica a todos os usuários selecionados que tentam se registrar usando a experiência de registro combinada e bloqueia o acesso, a menos que eles estejam se conectando de um local marcado como rede confiável:

1. No **Portal do Azure**, navegue até **Azure Active Directory** > **Segurança** > **Acesso Condicional**.
1. Selecione **+ Nova política**.
1. Insira um nome para essa política, como *Registro combinado de informações de segurança em redes confiáveis*.
1. Em **Atribuições**, selecione **Usuários e grupos**. Escolha os usuários e os grupos aos quais você deseja que essa política seja aplicada. Depois, selecione **Concluído**.

   > [!WARNING]
   > Os usuários devem estar habilitados para registro combinado.

1. Em **Aplicativos ou ações de nuvem**, selecione **Ações do usuário**. Marque **Registrar informações de segurança** e selecione **Concluído**.

    ![Criar uma política de acesso condicional para controlar o registro de informações de segurança](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. Em **Condições** > **Locais**, configure as seguintes opções:
   1. Configure **Sim**.
   1. Inclua **Qualquer localização**.
   1. Excluir **Todos os locais confiáveis**.
1. Selecione **Concluído** na janela *Locais*. Depois, selecione **Concluído** na janela *Condições*.
1. Em **Controles de acesso** > **Conceder**, escolha **Bloquear acesso** e **Selecionar**.
1. Defina **Habilitar política** como **Ativo**.
1. Para finalizar a política, selecione **Criar**.

## <a name="next-steps"></a>Próximas etapas

Se precisar de ajuda, consulte [solucionar problemas de registro de informações de segurança combinadas](howto-registration-mfa-sspr-combined-troubleshoot.md) ou saiba [qual é a condição de localização no acesso condicional do Azure ad?](../conditional-access/location-condition.md)

Depois que os usuários estiverem habilitados para registro combinado, você poderá [habilitar a redefinição de senha de autoatendimento](tutorial-enable-sspr.md) e [habilitar a autenticação multifator do Azure ad](tutorial-enable-azure-mfa.md).

Se necessário, saiba como [forçar os usuários a registrar novamente os métodos de autenticação](howto-mfa-userdevicesettings.md#manage-user-authentication-options).
