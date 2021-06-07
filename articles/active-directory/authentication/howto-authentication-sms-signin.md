---
title: Entrada de usuário baseada em SMS para o Azure Active Directory
description: Saiba como configurar e habilitar os usuários para entrarem no Azure Active Directory usando o SMS
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rateller
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8e9052502eba71f025bb6724278b7001173c5217
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103491610"
---
# <a name="configure-and-enable-users-for-sms-based-authentication-using-azure-active-directory"></a>Configurar e habilitar usuários para autenticação baseada em SMS usando Azure Active Directory 

Para simplificar e proteger a entrada em aplicativos e serviços, Azure Active Directory (Azure AD) fornece várias opções de autenticação. A autenticação baseada em SMS permite que os usuários entrem sem fornecer, nem mesmo saber, seu nome de usuário e senha. Depois que sua conta é criada por um administrador de identidade, ela pode inserir seu número de telefone no prompt de entrada. Eles recebem um código de autenticação por mensagem de texto que eles podem fornecer para concluir a entrada. Esse método de autenticação simplifica o acesso a aplicativos e serviços, especialmente para trabalhadores frentes.

Este artigo mostra como habilitar a autenticação baseada em SMS para usuários ou grupos selecionados no Azure AD.

## <a name="before-you-begin"></a>Antes de começar

Para concluir este artigo, você precisará dos seguintes recursos e privilégios:

* Uma assinatura ativa do Azure.
    * Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado à sua assinatura.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* É necessário ter privilégios de *administrador global* no locatário do Azure AD para habilitar a autenticação baseada em SMS.
* Cada usuário habilitado na política de método de autenticação por mensagem de texto deve ser licenciado, mesmo que não o use. Cada usuário habilitado deve ter uma das seguintes licenças do Azure AD, EMS, Microsoft 365:
    * [Microsoft 365 (M365) F1 ou F3][m365-firstline-workers-licensing]
    * [Enterprise Mobility + Security (EMS) E3 ou E5][ems-licensing] ou [Microsoft 365 (M365) E3 ou E5][m365-licensing]
    * [Office 365 F3][o365-f3]

## <a name="limitations"></a>Limitações

As seguintes limitações se aplicam à autenticação baseada em SMS:

* Atualmente, a autenticação baseada em SMS não é compatível com a autenticação multifator do Azure AD.
* Exceto para equipes, a autenticação baseada em SMS não é compatível com aplicativos nativos do Office.
* A autenticação baseada em SMS não é recomendada para contas B2B.
* Os usuários federados não serão autenticados no locatário inicial. Eles se autenticam apenas na nuvem.

## <a name="enable-the-sms-based-authentication-method"></a>Habilitar o método de autenticação baseada em SMS

Há três etapas principais para habilitar e usar a autenticação baseada em SMS em sua organização:

* Habilite a política de método de autenticação.
* Selecione usuários ou grupos que podem usar o método de autenticação baseado em SMS.
* Atribua um número de telefone para cada conta de usuário.
    * Esse número de telefone pode ser atribuído no portal do Azure (que é mostrado neste artigo) e na *minha equipe* ou *minha conta*.

Primeiro, vamos habilitar a autenticação baseada em SMS para seu locatário do Azure AD.

1. Entre no [portal do Azure][azure-portal] como *administrador global*.
1. Pesquise **Azure Active Directory** e selecione-o.
1. No menu de navegação no lado esquerdo da janela de Azure Active Directory, selecione **segurança > métodos de autenticação > política de método de autenticação**.

    [![Navegue até e selecione a janela diretiva de método de autenticação na portal do Azure.](media/howto-authentication-sms-signin/authentication-method-policy-cropped.png)](media/howto-authentication-sms-signin/authentication-method-policy.png#lightbox)

1. Na lista de métodos de autenticação disponíveis, selecione **Mensagem de texto**.
1. Defina **Habilitar** como *Sim*.

    ![Habilitar a autenticação de texto na janela de política de método de autenticação](./media/howto-authentication-sms-signin/enable-text-authentication-method.png)

    Você pode optar por habilitar a autenticação baseada em SMS para *Todos os usuários* ou *Selecionar usuários* e grupos. Na próxima seção, você habilitará a autenticação baseada em SMS para um usuário de teste.

## <a name="assign-the-authentication-method-to-users-and-groups"></a>Atribuir o método de autenticação a usuários e grupos

Com a autenticação baseada em SMS habilitada no seu locatário do Azure AD, agora selecione alguns usuários ou grupos para ter permissão para usar esse método de autenticação.

1. Na janela política de autenticação de mensagem de texto, defina **Destino** como *Selecionar usuários*.
1. Escolha **Adicionar usuários ou grupos** e selecione um usuário ou grupo de teste, como *Usuário Contoso* ou *Usuários SMS Contoso*.

    [![Escolha usuários ou grupos para habilitar a autenticação baseada em SMS no portal do Azure.](media/howto-authentication-sms-signin/add-users-or-groups-cropped.png)](media/howto-authentication-sms-signin/add-users-or-groups.png#lightbox)

1. Depois de selecionar os usuários ou grupos, escolha **Selecionar** e **Salvar** a política de método de autenticação atualizada.

Cada usuário habilitado na política de método de autenticação por mensagem de texto deve ser licenciado, mesmo que não o use. Verifique se você tem as licenças apropriadas para os usuários habilitados na política de método de autenticação, especialmente ao habilitar o recurso para grandes grupos de usuários.

## <a name="set-a-phone-number-for-user-accounts"></a>Definir um número de telefone para contas de usuário

Agora, os usuários estão habilitados para autenticação baseada em SMS, mas o número de telefone deles deve ser associado ao perfil do usuário no Azure AD para que eles possam entrar. O usuário pode [definir esse número de telefone](../user-help/sms-sign-in-explainer.md) em *minha conta* ou você pode atribuir o número de telefone usando o portal do Azure. Os números de telefone podem ser definidos por *administradores globais*, *administradores de autenticação* ou *administradores de autenticação privilegiada*.

Quando um número de telefone é definido para o SMS-Sign, ele também é disponibilizado para uso com a [autenticação multifator do Azure ad][tutorial-azure-mfa] e [a redefinição de senha de autoatendimento][tutorial-sspr].

1. Pesquise **Azure Active Directory** e selecione-o.
1. No menu de navegação no lado esquerdo da janela Azure Active Directory, selecione **Usuários**.
1. Selecione o usuário que você habilitou para a autenticação baseada em SMS na seção anterior, como *Usuário Contoso* e selecione **Métodos de autenticação**.
1. Selecione **+ Adicionar método de autenticação** e, em seguida, no menu suspenso *escolher método* , escolha **número de telefone**.

    Insira o número de telefone do usuário, incluindo o código do país, como *+ 1 xxxxxxxxx*. O portal do Azure valida o número de telefone no formato correto.

    Em seguida, no menu suspenso *tipo de telefone* , selecione *móvel*, *celular alternativo* ou *outro* , conforme necessário.

    :::image type="content" source="media/howto-authentication-sms-signin/set-user-phone-number.png" alt-text="Definir um número de telefone para um usuário na portal do Azure para usar com a autenticação baseada em SMS":::

    O número de telefone precisa ser exclusivo em seu locatário. Se você tentar usar o mesmo número de telefone para vários usuários, uma mensagem de erro será mostrada.

1. Para aplicar o número de telefone à conta de um usuário, selecione **Adicionar**.

Quando provisionado com êxito, uma marca de verificação é exibida para *Entrada de SMS habilitada*.

## <a name="test-sms-based-sign-in"></a>Testar entrada baseada em SMS

Para testar a conta de usuário que agora está habilitada para entrada baseada em SMS, conclua as seguintes etapas:

1. Abra uma nova janela InPrivate ou anônima no navegador da Web para [https://www.office.com][office]
1. No canto superior direito, selecione **Entrar**.
1. No prompt de entrada, insira o número de telefone associado ao usuário na seção anterior e selecione **Avançar**.

    ![Inserir um número de telefone no prompt de entrada para o usuário de teste](./media/howto-authentication-sms-signin/sign-in-with-phone-number.png)

1. Uma mensagem de texto é enviada ao número de telefone fornecido. Para concluir o processo de entrada, insira o código de seis dígitos fornecido na mensagem de texto no prompt de entrada.

    ![Inserir o código de confirmação enviado por mensagem de texto ao número de telefone do usuário](./media/howto-authentication-sms-signin/sign-in-with-phone-number-confirmation-code.png)

1. O usuário agora está conectado sem a necessidade de fornecer um nome de usuário ou uma senha.

## <a name="troubleshoot-sms-based-sign-in"></a>Solucionar problemas de entrada baseada em SMS

Os cenários e as etapas de solução de problemas a seguir poderão ser usados se você tiver problemas com a habilitação e o uso de entrada baseada em SMS.

### <a name="phone-number-already-set-for-a-user-account"></a>Número de telefone já definido para uma conta de usuário

Se um usuário já tiver se registrado na autenticação multifator do Azure AD e/ou SSPR (redefinição de senha de autoatendimento), eles já terão um número de telefone associado à sua conta. Esse número de telefone não está disponível automaticamente para uso com a entrada baseada em SMS.

Um usuário com um número de telefone já definido para sua conta vê um botão para *Habilitar para entrada de SMS* na página **Meu Perfil**. Selecione esse botão e a conta está habilitada para uso com o logon baseado em SMS e a autenticação multifator do Azure AD ou o registro SSPR anterior.

Para obter mais informações sobre a experiência do usuário final, consulte [experiência do usuário de entrada do SMS para número de telefone](../user-help/sms-sign-in-explainer.md).

### <a name="error-when-trying-to-set-a-phone-number-on-a-users-account"></a>Erro ao tentar definir um número de telefone na conta de um usuário

Se você receber um erro ao tentar definir um número de telefone para uma conta de usuário no portal do Azure, examine as seguintes etapas de solução de problemas:

1. Certifique-se de que você está habilitado para a entrada baseada em SMS.
1. Confirme se a conta de usuário está habilitada na política de método de autenticação de *Mensagem de texto*.
1. Defina o número de telefone com a formatação correta, como validado no portal do Azure (como *+ 1 4251234567*).
1. Verifique se o número de telefone não é usado em outro lugar em seu locatário.
1. Verifique se não há número de voz definido na conta. Se um número de voz estiver definido, exclua e tente novamente o número de telefone.

## <a name="next-steps"></a>Próximas etapas

Para obter outras maneiras de entrar no Azure AD sem uma senha, como o aplicativo Microsoft Authenticator ou as chaves de segurança FIDO2, confira [Passwordless authentication options for Azure AD][concepts-passwordless] (Opções de autenticação sem senha para o Azure AD).

Você também pode usar a API REST do Microsoft Graph versão beta para [habilitar][rest-enable] ou [desabilitar][rest-disable] a entrada baseada em SMS.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../fundamentals/active-directory-how-subscriptions-associated-directory.md
[concepts-passwordless]: concept-authentication-passwordless.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[tutorial-sspr]: tutorial-enable-sspr.md
[rest-enable]: /graph/api/phoneauthenticationmethod-enablesmssignin?view=graph-rest-beta&tabs=http
[rest-disable]: /graph/api/phoneauthenticationmethod-disablesmssignin?view=graph-rest-beta&tabs=http

<!-- EXTERNAL LINKS -->
[azure-portal]: https://portal.azure.com
[office]: https://www.office.com
[m365-firstline-workers-licensing]: https://www.microsoft.com/licensing/news/m365-firstline-workers
[azuread-licensing]: https://azure.microsoft.com/pricing/details/active-directory/
[ems-licensing]: https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing
[m365-licensing]: https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans
[o365-f1]: https://www.microsoft.com/microsoft-365/business/office-365-f1?market=af
[o365-f3]: https://www.microsoft.com/microsoft-365/business/office-365-f3?activetab=pivot%3aoverviewtab
