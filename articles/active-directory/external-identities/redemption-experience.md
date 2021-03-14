---
title: Resgate de convite na colaboração B2B – Azure AD
description: Descreve a experiência de resgate de convite de colaboração B2B do Azure AD para usuários finais, incluindo o contrato de termos de privacidade.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/04/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cd0febe5ffbc1b17718043d5fc97b804f87cc46
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103199738"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Resgate do convite de colaboração do Azure Active Directory B2B

Este artigo descreve como os usuários convidados podem acessar seus recursos e o processo de consentimento que eles percorrerão. Se você enviar um email de convite ao convidado, ele incluirá um link que o convidado pode resgatar para obter acesso ao seu aplicativo ou portal. O email de convite é apenas uma das maneiras que os convidados podem usar para obter acesso aos seus recursos. Como alternativa, você pode adicionar convidados ao seu diretório e fornecer a eles um link direto para o portal ou aplicativo que deseja compartilhar. Independentemente do método usado, os convidados são guiados durante o processo de consentimento pela primeira vez. Esse processo faz com que seus convidados aceitem os termos de privacidade e eventuais [termos de uso](../conditional-access/terms-of-use.md) que você tenha configurado.

Quando você adiciona um usuário convidado ao seu diretório, a conta de usuário convidado tem um status de consentimento (visível no PowerShell) que é inicialmente definido como **PendingAcceptance**. Essa configuração permanece até que o convidado aceite seu convite e concorde com a política de privacidade e os termos de uso. Depois disso, o status de consentimento muda para **Accepted** e as páginas de consentimento não são mais apresentadas ao convidado.

   > [!IMPORTANT]
   > - **A partir de 4 de janeiro de 2021**, o Google está [preterindo o suporte de entrada do WebView](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Se estiver usando a federação do Google ou a inscrição por autoatendimento com o Gmail, você deverá [testar seus aplicativos nativos de linha de negócios para garantir a compatibilidade](google-federation.md#deprecation-of-webview-sign-in-support).
   > - **A partir de outubro de 2021**, a Microsoft não dará mais suporte ao resgate de convites criando contas e locatários do Azure AD não gerenciados para cenários de colaboração B2B. Durante a preparação, incentivamos os clientes a aceitarem a [autenticação de senha avulsa por email](one-time-passcode.md). Agradecemos seus comentários sobre essa versão prévia do recurso pública e estamos empolgados em criar ainda mais maneiras de colaborar.

## <a name="redemption-and-sign-in-through-a-common-endpoint"></a>Resgate e entre em um ponto de extremidade comum

Os usuários convidados agora podem entrar em seus aplicativos de multilocatário ou de terceiros da Microsoft por meio de um ponto de extremidade comum (URL), por exemplo `https://myapps.microsoft.com` . Anteriormente, uma URL comum redirecionaria um usuário convidado para seu locatário inicial em vez de seu locatário de recursos para autenticação, de modo que um link específico de locatário era necessário (por exemplo `https://myapps.microsoft.com/?tenantid=<tenant id>` ). Agora o usuário convidado pode ir para a URL comum do aplicativo, escolher **as opções de entrada** e, em seguida, selecionar **entrar em uma organização**. Em seguida, o usuário digita o nome da sua organização.

![Entrada de ponto de extremidade comum](media/redemption-experience/common-endpoint-flow-small.png)

Em seguida, o usuário é redirecionado para seu ponto de extremidade locatário, no qual eles podem entrar com seu endereço de email ou selecionar um provedor de identidade que você configurou.

## <a name="redemption-through-a-direct-link"></a>Resgate por meio de um link direto

Como alternativa ao email de convite ou à URL comum de um aplicativo, você pode dar a um convidado um link direto para seu aplicativo ou Portal. Primeiro, você precisa adicionar o usuário convidado ao seu diretório por meio do [portal do Azure](./b2b-quickstart-add-guest-users-portal.md) ou do [PowerShell](./b2b-quickstart-invite-powershell.md). Em seguida, você pode usar qualquer uma das [formas de personalização da implantação de aplicativos para usuários](../manage-apps/end-user-experiences.md), incluindo links de logon diretos. Quando um convidado usa um link direto em vez do email de convite, ele é igualmente guiado pela experiência de consentimento na primeira vez.

> [!NOTE]
> Um link direto é específico do locatário. Em outras palavras, ele inclui uma ID de locatário ou um domínio verificado para que o convidado possa ser autenticado em seu locatário, onde o aplicativo compartilhado está localizado. Veja alguns exemplos de links diretos com o contexto do locatário:
 > - Painel de acesso aos aplicativos: `https://myapps.microsoft.com/?tenantid=<tenant id>`
 > - Painel de acesso aos aplicativos para um domínio verificado: `https://myapps.microsoft.com/<;verified domain>`
 > - Portal do Azure: `https://portal.azure.com/<tenant id>`
 > - Aplicativo individual: confira como usar um [link de logon direto](../manage-apps/end-user-experiences.md#direct-sign-on-links)

Em alguns casos, recomendamos o email de convite em vez de um link direto. Se esses casos especiais forem importantes para sua organização, recomendamos que você convide usuários usando métodos que ainda enviam emails de convite:
 - O usuário não tem uma conta do Azure AD, uma MSA ou uma conta de email em uma organização federada. A menos que você esteja usando o recurso de senha de uso único, o convidado precisará resgatar o email de convite para ser guiado pelas etapas de criação de uma MSA.
 - Às vezes, o objeto de usuário convidado não pode ter um endereço de email devido a um conflito com um objeto de contato (por exemplo, um objeto de contato Outlook). Nesse caso, o usuário deve clicar na URL de resgate no email de convite.
 - O usuário pode entrar com um alias do endereço de email que foi convidado. (Um alias é um endereço de email adicional associado a uma conta de email.) Nesse caso, o usuário deve clicar na URL de resgate no email de convite.

## <a name="redemption-through-the-invitation-email"></a>Resgate por meio de email de convite

Quando você adiciona um usuário convidado ao seu diretório [por meio do portal do Azure](./b2b-quickstart-add-guest-users-portal.md), ele recebe um email de convite no processo. Você também pode optar por enviar emails de convite quando [usa o PowerShell](./b2b-quickstart-invite-powershell.md) para adicionar usuários convidados ao seu diretório. Aqui está uma descrição da experiência do convidado ao resgatar o link no email.

1. O convidado recebe um [email de convite](./invitation-email-elements.md) enviado pelo **Microsoft Invitations**.
2. Ele seleciona **Aceitar o convite** no email.
3. O convidado usará suas próprias credenciais para entrar no seu diretório. Se ele não tiver uma conta que possa ser federada para o seu diretório e o recurso [enviar senha de uso único por email (OTP)](./one-time-passcode.md) não estiver habilitado; o convidado precisará criar uma [MSA](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) pessoal ou uma [conta de autoatendimento do Azure AD](../enterprise-users/directory-self-service-signup.md). Consulte o [fluxo de resgate de convite](#invitation-redemption-flow) para obter detalhes.
4. O convidado é guiado pela [experiência de consentimento](#consent-experience-for-the-guest) descrita abaixo.
## <a name="invitation-redemption-flow"></a>Fluxo de resgate de convite

Quando um usuário clica no link **Aceitar convite** em um [email de convite](invitation-email-elements.md), o Azure AD resgata o convite automaticamente com base no fluxo de resgate, conforme mostrado abaixo:

![Captura de tela que mostra o diagrama de fluxo de resgate](media/redemption-experience/invitation-redemption-flow.png)

**Se o UPN (nome principal de usuário) do usuário corresponder a uma conta existente do Azure AD e do MSA pessoal, o usuário será solicitado a escolher a conta que deseja resgatar.*

1. O Azure AD executa a descoberta baseada no usuário para determinar se ele existe em um [locatário do Azure AD](./what-is-b2b.md#easily-invite-guest-users-from-the-azure-ad-portal).

2. Se um administrador tiver habilitado [federação direta](./direct-federation.md), o Azure AD verificará se o sufixo de domínio do usuário corresponde ao domínio de um provedor de identidade SAML/WS-FED configurado e redirecionará o usuário para o provedor de identidade pré-configurado.

3. Se um administrador tiver habilitado a [federação com o Google](./google-federation.md), o Azure AD verificará se o sufixo de domínio do usuário é gmail.com ou googlemail.com e redirecionará o usuário para o Google.

4. O processo de resgate verifica se o usuário tem uma [MSA (conta Microsoft pessoal)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) existente para resgate JIT (just-in-time), mas não para resgate de link de email de convite. Se o usuário já tiver um MSA existente, ele entrará com o MSA existente.

5. Depois que o **diretório base** do usuário for identificado, ele será enviado ao provedor de identidade correspondente para se conectar.  

6. Se as etapas 1 a 4 não localizarem um diretório base para o usuário convidado, o Azure AD determinará se o locatário que ele está convidando habilitou o recurso [Enviar OTP (senha de uso único) por email](./one-time-passcode.md) para convidados.

7. Se a opção [Enviar senha de uso único para convidados por email estiver habilitada](./one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode), uma senha será enviada ao usuário pelo email convidado. O usuário vai recuperar e inserir essa senha na página de entrada do Azure AD.

8. Se o email de senha de uso único para convidados estiver desabilitado, o Azure AD verificará o sufixo de domínio para determinar se ele pertence a uma conta de consumidor. Nesse caso, será solicitado que o usuário crie uma [conta Microsoft](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) pessoal. Caso contrário, o usuário será solicitado a criar uma [conta de autoatendimento do Azure AD](../enterprise-users/directory-self-service-signup.md).

9. O Azure AD tenta criar uma [conta de autoatendimento do Azure AD](../enterprise-users/directory-self-service-signup.md) com a verificação do acesso ao email. A verificação da conta é feita como envio de um código por email e instruções para que o usuário a recupere e envie ao Azure AD. No entanto, se o locatário do usuário convidado for federado ou se o campo AllowEmailVerifiedUsers for definido como false no locatário do usuário convidado, ele não poderá concluir o resgate e o fluxo resultará em erro. Para obter mais informações, confira [Solução de problemas de colaboração B2B do Azure Active Directory](./troubleshoot.md#the-user-that-i-invited-is-receiving-an-error-during-redemption).

10. O usuário precisará criar uma conta [MSA (Microsoft)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) pessoal.

11. Após a autenticação no provedor de identidade correto, o usuário é redirecionado para o Azure AD a fim de concluir a [experiência de consentimento](#consent-experience-for-the-guest).  

Em resgates JIT (just-in-time), feitos por meio de um link de aplicativo locatário, as etapas 8 a 10 não estão disponíveis. Se um usuário chegar à etapa 6 e o recurso de envio de senha de uso único por email não estiver habilitado, o usuário receberá uma mensagem de erro e não poderá resgatar o convite. Para evitar esse erro, os administradores devem [habilitar o envio de senha de uso único por email](./one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode) ou fazer com que o usuário clique em um link de convite.

## <a name="consent-experience-for-the-guest"></a>Experiência de consentimento para o convidado

Quando um convidado entra para acessar recursos em uma organização parceira pela primeira vez, ele é guiado pelas páginas seguintes. 

1. O convidado analisa a página **Analisar permissões** que descreve a política de privacidade da organização que está fazendo o convite. Um usuário precisa **aceitar** o uso de suas informações de acordo com as políticas de privacidade da organização que está fazendo o convite para continuar.

   ![Captura de tela mostrando a página Analisar as permissões](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Para obter informações sobre como você, como administrador de locatários, pode fornecer um link para a política de privacidade de sua organização, confira [Como: Adicionar informações de privacidade de sua organização ao Azure Active Directory](../fundamentals/active-directory-properties-area.md).

2. Se os termos de uso estiverem configurados, o convidado abrirá e analisará os termos de uso para depois selecionar **Aceitar**. 

   ![Captura de tela mostrando os novos termos de uso](media/redemption-experience/terms-of-use-accept.png) 

   Você pode configurar os [termos de uso](../conditional-access/terms-of-use.md) em **Identidades Externas** > **Termos de uso**.

3. Se não houver especificação em contrário, o convidado será redirecionado para o painel de acesso Aplicativos, que lista os aplicativos que o convidado pode acessar.

   ![Captura de tela que mostra o painel de acesso Aplicativos](media/redemption-experience/myapps.png) 

No seu diretório, o valor de **Convite aceito** do convidado muda para **Sim**. Se uma MSA foi criada, a **Origem** do convidado aparece como **Conta Microsoft**. Para obter mais informações sobre as propriedades de conta de usuário convidado, confira [Propriedades de um usuário de colaboração B2B do Azure AD](user-properties.md). 

## <a name="next-steps"></a>Próximas etapas

- [O que é a colaboração B2B do AD do Azure?](what-is-b2b.md)
- [Adicionar usuários de colaboração B2B do Azure Active Directory no Portal do Azure](add-users-administrator.md)
- [Como os operadores de informação adicionam usuários de colaboração B2B ao Azure Active Directory?](add-users-information-worker.md)
- [Adicionar usuários de colaboração B2B do Azure Active Directory usando o PowerShell](customize-invitation-api.md#powershell)
- [Deixar uma organização como usuário convidado](leave-the-organization.md)
