---
title: Como certificar o AppSource para o Azure Active Directory| Microsoft Docs
description: Detalhes sobre como certificar o AppSource do seu aplicativo para o Azure Active Directory.
services: active-directory
documentationcenter: 
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 21206407-49f8-4c0b-84d1-c25e17cd4183
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/03/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: d8e2f8fc19ff879e6a7b632f033fd0ed9d77392a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>Como certificar o AppSource para o Azure AD
O [Microsoft AppSource](https://appsource.microsoft.com/) é um local onde os usuários de negócios podem descobrir, experimentar e gerenciar aplicativos SaaS de linha de negócios (SaaS autônomos e o complemento para produtos SaaS da Microsoft existentes).

Para listar um aplicativo SaaS autônomo no AppSource, seu aplicativo deve aceitar logon único de contas corporativas de qualquer empresa ou organização que possua o Azure Active Directory. O processo de entrada deve usar o protocolo [OpenID Connect](./active-directory-protocols-openid-connect-code.md) ou [OAuth 2.0](./active-directory-protocols-oauth-code.md). A integração SAML não é aceita para certificação AppSource.

## <a name="guides-and-code-samples"></a>Guias e exemplos de código
Se você quiser saber mais sobre como integrar seu aplicativo com o Azure Active Directory usando Open ID Connect, siga nossos guias e exemplos de código no [guia do desenvolvedor do Azure Active Directory](./active-directory-developers-guide.md#get-started "Introdução ao Azure AD para desenvolvedores").

## <a name="multi-tenant-applications"></a>Aplicativos multilocatários

Um aplicativo que aceita entradas de usuários de qualquer empresa ou organização que tenha o Azure Active Directory sem a necessidade de uma instância, configuração ou implantação separada é conhecido como *aplicativo multilocatário*. O AppSource recomenda que os aplicativos implementem multilocação para habilitar a experiência de avaliação com um *clique simples*.

Para habilitar a multilocação em seu aplicativo:
- Defina a propriedade `Multi-Tenanted` como `Yes` nas informações do registro do aplicativo no [Portal do Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) (por padrão, os aplicativos criados no Portal do Azure são configurados como *único locatário*)
- Atualize seu código para enviar solicitações para o ponto de extremidade '`common`' (atualize o ponto de extremidade em *https://login.microsoftonline.com/{seulocatario}* para *https://login.microsoftonline.com/common*)
- Para algumas plataformas, como o ASP.NET, você também precisa atualizar seu código para aceitar vários emissores

Para saber mais sobre multilocação, confira: [Como conectar qualquer usuário do Azure AD (Active Directory) usando o padrão de aplicativo multilocatário](./active-directory-devhowto-multi-tenant-overview.md).

### <a name="single-tenant-applications"></a>Aplicativos de locatário único
Aplicativos que só aceitam entradas de usuários de uma instância definida do Azure Active Directory são conhecidos como *aplicativo de locatário único*. Os usuários externos (incluindo as contas corporativas ou de estudante de outras organizações, ou conta pessoal) podem entrar em um aplicativo de locatário único depois de adicionar cada usuário como *conta convidado* à instância do Azure Active Directory em que o aplicativo está registrado. Você pode adicionar usuários como contas de convidado a um Azure Active Directory por meio de [ *colaboração B2B do Azure AD* ](../active-directory-b2b-what-is-azure-ad-b2b.md), e isso pode ser feito [programaticamente](../active-directory-b2b-code-samples.md). Quando você adiciona um usuário como conta de convidado a um Azure Active Directory, um email de convite é enviado para o usuário, que tem de aceitar o convite clicando no link no email de convite. Convites enviados para um usuário adicional em uma organização que convida e também é membro da organização do parceiro não são necessários para se aceitar um convite para entrar.

Os aplicativos de único locatário podem habilitar a experiência *Entre em Contato Comigo*, mas se você deseja habilitar a experiência de avaliação gratuita/clique simples recomendada pelo AppSource, habilite a multilocação em seu aplicativo.


## <a name="appsource-trial-experiences"></a>Experiências de avaliação do AppSource

### <a name="free-trial-customer-led-trial-experience"></a>Avaliação gratuita (experiência de avaliação orientada pelo cliente) 
A *avaliação orientada pelo cliente* é a experiência que o AppSource recomenda, pois oferece acesso a seu aplicativo com um clique simples. Abaixo, uma ilustração de como essa experiência se parece:<br/><br/>

<table >
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%"/><ul><li>O usuário localiza o aplicativo no Site do AppSource</li><li>Seleciona a opção “Avaliação gratuita”</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" /><ul><li>O AppSource redireciona o usuário para uma URL no seu site</li><li>Seu site inicia o processo <i>single-sign-on</i> automaticamente (no carregamento da página)</li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%"/><ul><li>O usuário é redirecionado para a página de entrada da Microsoft</li><li>O usuário fornece credenciais para entrar</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%"/><ul><li>O usuário dá consentimento ao seu aplicativo</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%"/><ul><li>A entrada é concluída e o usuário é redirecionado ao seu site</li><li>O usuário começa a avaliação gratuita</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>Entre em Contato Comigo (experiência de avaliação orientada pelo parceiro)
A *experiência de avaliação do parceiro* pode ser usada quando uma operação de longo prazo ou manual precisa ser feita para provisionar o usuário ou a empresa: por exemplo, seu aplicativo precisa provisionar máquinas virtuais, instâncias de banco de dados ou operações que levam muito tempo para serem concluídas. Nesse caso, após o usuário selecionar o botão *'Solicitar avaliação'* e preencher um formulário, o AppSource enviará as informações de contato do usuário. Depois de receber essas informações, provisione o ambiente e envie instruções para o usuário sobre como acessar a experiência de avaliação:<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%"/><ul><li>O usuário localiza o aplicativo no Site do AppSource</li><li>Seleciona a opção “Entrar em contato comigo”</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png" width="85%"/><ul><li>Preenche um formulário com as informações de contato</li></ul></td>
     <td valign="top" width="33%">3.<br/><br/>
        <table bgcolor="#f7f7f7">
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/UserContact.png" width="55%"/></td>
            <td>Você recebe as informações do usuário</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/SetupEnv.png" width="55%"/></td>
            <td>Configure o ambiente</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/ContactCustomer.png" width="55%"/></td>
            <td>Entre em contato com usuário com as informações da avaliação</td>
        </tr>
        </table><br/><br/>
        <ul><li>Você recebe informações do usuário e configura a instância de avaliação</li><li>Você envia o hiperlink de acesso ao aplicativo para o usuário</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%"/><ul><li>O usuário acessa o seu aplicativo e conclui o processo de logon único</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%"/><ul><li>O usuário dá consentimento ao seu aplicativo</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%"/><ul><li>A entrada é concluída e o usuário é redirecionado ao seu site</li><li>O usuário começa a avaliação gratuita</li></ul></td>
   
</tr>
</table>

### <a name="more-information"></a>Mais informações
Para saber mais sobre a experiência de avaliação do AppSource, confira [este vídeo](https://aka.ms/trialexperienceforwebapps). 
 
## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como criar aplicativos que dão suporte a logons do Azure Active Directory, confira [Cenários de autenticação do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios) 

- Para saber mais sobre como listar seu aplicativo SaaS no AppSource, confira [Informações de parceiro do AppSource](https://appsource.microsoft.com/partners)


## <a name="get-support"></a>Obtenha suporte
Para a integração do Azure Active Directory, usamos [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory) com a comunidade para suporte. 

Recomendamos fazer suas perguntas no Stack Overflow primeiro e navegar pelos problemas existentes para ver se alguém já fez sua pergunta antes. Marque suas perguntas ou comentários com `[azure-active-directory]`.

Use a seção de comentários a seguir para fornecer seus comentários e nos ajudar a aprimorar e adaptar nosso conteúdo.

<!--Reference style links -->
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]: ./active-directory-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Howto-Multitenant-Overview]: ./active-directory-devhowto-multi-tenant-overview.md
[AAD-QuickStart-Web-Apps]: ./active-directory-developers-guide.md#get-started


<!--Image references-->