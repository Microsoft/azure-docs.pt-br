---
title: Como certificar o AppSource para o Azure Active Directory| Microsoft Docs
description: Detalhes sobre como certificar o AppSource do seu aplicativo para o Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: how-to
ms.workload: identity
ms.date: 08/21/2018
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: d9a4da6fe65fda07609c7399518fa324017ea44c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101649338"
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>Como certificar o AppSource para o Azure AD

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

O [Microsoft AppSource](https://appsource.microsoft.com/) é um local onde os usuários de negócios podem descobrir, experimentar e gerenciar aplicativos SaaS de linha de negócios (SaaS autônomos e o complemento para produtos SaaS da Microsoft existentes).

Para listar um aplicativo SaaS autônomo no AppSource, o aplicativo deve aceitar logon único a partir de contas corporativas de qualquer empresa ou organização que possua o Azure Active Directory (Azure AD). O processo de entrada deve usar o protocolo [OpenID Connect](v1-protocols-openid-connect-code.md) ou [OAuth 2.0](v1-protocols-oauth-code.md). A integração SAML não é aceita para certificação AppSource.

## <a name="guides-and-code-samples"></a>Guias e exemplos de código

Se você quiser saber mais sobre como integrar seu aplicativo com o Azure AD usando o Open ID Connect, siga nossos guias e exemplos de código no [Guia do desenvolvedor de Azure Active Directory](v1-overview.md#get-started "Introdução ao Azure AD para desenvolvedores").

## <a name="multi-tenant-applications"></a>Aplicativos multilocatários

Um *aplicativo multilocatário* é aquele que aceita entradas de usuários de qualquer empresa ou organização que tenha o Azure AD sem a necessidade de uma instância, configuração ou implantação separada. O AppSource recomenda que os aplicativos implementem multilocação para habilitar a experiência de avaliação com um *clique simples*.

Para habilitar a multilocação em seu aplicativo, siga estas etapas:
1. Definir a propriedade `Multi-Tenanted` para `Yes` nas informações do registro do aplicativo no [portal do Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps). Por padrão, os aplicativos criados no portal do Azure são configurados como *[locatário único](#single-tenant-applications)*.
1. Atualize o código para enviar solicitações ao ponto de extremidade `common`. Para fazer isso, atualize o ponto de extremidade de `https://login.microsoftonline.com/{yourtenant}` para `https://login.microsoftonline.com/common*`.
1. Em algumas plataformas, como o ASP.NET, também é necessário atualizar o código para aceitar vários emissores.

Para obter mais informações sobre multilocação, consulte: [Como conectar qualquer usuário do Azure Active Directory (Azure AD) usando o padrão de aplicativo multilocatário](../develop/howto-convert-app-to-be-multi-tenant.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

### <a name="single-tenant-applications"></a>Aplicativos de locatário único

Um *aplicativo de locatário único* é aquele que somente aceita entradas de usuários de uma instância definida do Azure AD. Os usuários externos (incluindo as contas corporativas ou de estudante de outras organizações, ou contas pessoais) podem entrar em um aplicativo de locatário único após adicionar cada usuário como conta convidado à instância do Azure AD em que o aplicativo está registrado. 

É possível adicionar usuários como contas de convidado ao Azure Active Directory por meio de [Colaboração B2B do Azure Active Directory](../external-identities/what-is-b2b.md), sendo que isso pode ser feito [via programação](../../active-directory-b2c/code-samples.md). Ao usar o B2B, os usuários podem criar um portal de autoatendimento que não requer um convite para entrar. Para obter mais informações, consulte [Portal de autoatendimento para inscrição de colaboração B2B do Azure AD](../external-identities/self-service-portal.md).

Os aplicativos de locatário único podem habilitar a experiência *Entre em Contato Comigo*, mas se você quer habilitar a experiência de avaliação gratuita/clique simples recomendada pelo AppSource, em vez disso, habilite a multilocação no aplicativo.

## <a name="appsource-trial-experiences"></a>Experiências de avaliação do AppSource

### <a name="free-trial-customer-led-trial-experience"></a>Avaliação gratuita (experiência de avaliação orientada pelo cliente)

A avaliação orientada pelo cliente é a experiência que o AppSource recomenda, pois oferece acesso a o aplicativo com um clique simples. O exemplo a seguir mostra a aparência desta experiência:

<table >
<tr>    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%" alt="Shows Free trial for customer-led trial experience."/><ul><li>O usuário localiza o aplicativo no Site do AppSource</li><li>Seleciona a opção ' avaliação gratuita '</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" alt="Shows how user is redirected to a URL in your web site."/><ul><li>O AppSource redireciona o usuário para uma URL no seu site</li><li>Seu site inicia o processo <i>single-sign-on</i> automaticamente (no carregamento da página)</li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%" alt="Shows the Microsoft sign-in page."/><ul><li>O usuário é redirecionado para a página de entrada da Microsoft</li><li>O usuário fornece credenciais para entrar</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%" alt="Example: Consent page for an application."/><ul><li>O usuário dá consentimento ao seu aplicativo</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt="Shows the experience the user sees when redirected back to your site."/><ul><li>A entrada é concluída e o usuário é redirecionado ao seu site</li><li>O usuário começa a avaliação gratuita</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>Entre em Contato Comigo (experiência de avaliação orientada pelo parceiro)

Você pode usar a experiência de avaliação do parceiro quando uma operação manual ou de longo prazo precisa ser feita para provisionar o usuário/empresa - por exemplo, seu aplicativo precisa provisionar máquinas virtuais, instâncias de banco de dados ou operações que levam muito tempo para serem concluídas. Nesse caso, após o usuário selecionar o botão **Solicitar avaliação** e preencher um formulário, o AppSource enviará as informações de contato do usuário. Depois de receber essas informações, você provisiona o ambiente e envia as instruções para o usuário sobre como acessar a experiência de avaliação:<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%" alt="Shows Contact me for partner-led trial experience"/><ul><li>O usuário localiza o aplicativo no Site do AppSource</li><li>Seleciona a opção ' entrar em contato comigo '</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png" width="85%" alt="Shows an example form with contact info"/><ul><li>Preenche um formulário com as informações de contato</li></ul></td>
     <td valign="top" width="33%">3.<br/><br/>
        <table bgcolor="#f7f7f7">
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/usercontact.png" width="55%" alt="Shows placeholder for user information"/></td>
            <td>Você recebe as informações do usuário</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/setupenv.png" width="55%" alt="Shows placeholder for setup environment info"/></td>
            <td>Ambiente de configuração</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/contactcustomer.png" width="55%" alt="Shows placeholder for trial info"/></td>
            <td>Entre em contato com usuário com as informações da avaliação</td>
        </tr>
        </table><br/><br/>
        <ul><li>Você recebe informações do usuário e configura a instância de avaliação</li><li>Você envia o hiperlink de acesso ao aplicativo para o usuário</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%" alt="Shows the application sign-in screen"/><ul><li>O usuário acessa o seu aplicativo e conclui o processo de logon único</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%" alt="Shows an example consent page for an application"/><ul><li>O usuário dá consentimento ao seu aplicativo</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt="Shows the experience the user sees when redirected back to your site"/><ul><li>A entrada é concluída e o usuário é redirecionado ao seu site</li><li>O usuário começa a avaliação gratuita</li></ul></td>  
</tr>
</table>

### <a name="more-information"></a>Mais informações

Para saber mais sobre a experiência de avaliação do AppSource, confira [este vídeo](https://aka.ms/trialexperienceforwebapps). 

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre como compilar aplicativos que dão suporte a entradas do Azure AD, consulte [Cenários de autenticação do Azure AD](./v1-authentication-scenarios.md).
- Para saber mais sobre como listar seu aplicativo SaaS no AppSource, confira [Informações de parceiro do AppSource](https://appsource.microsoft.com/partners)

## <a name="get-support"></a>Obter suporte

Para a integração do Azure AD, usamos o [Microsoft Q&](/answers/products/) a com a Comunidade para fornecer suporte.

É altamente recomendável que você faça suas perguntas no Microsoft Q&um primeiro e navegue pelos problemas existentes para ver se alguém fez sua pergunta antes. Certifique-se de que suas perguntas ou comentários estão marcados com [`[azure-active-directory]`](/answers/topics/azure-active-directory.html) .

Use a seção de comentários a seguir para fornecer seus comentários e nos ajudar a aprimorar e adaptar nosso conteúdo.

<!--Reference style links -->
[AAD-Auth-Scenarios]:v1-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]:v1-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: v1-overview.md
[AAD-Howto-Multitenant-Overview]: howto-convert-app-to-be-multi-tenant.md
[AAD-QuickStart-Web-Apps]: v1-overview.md#get-started

<!--Image references-->