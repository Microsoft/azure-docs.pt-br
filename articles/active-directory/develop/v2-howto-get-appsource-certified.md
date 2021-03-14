---
title: Obtenha o AppSource Certified para Azure Active Directory | Microsoft Docs
description: Saiba mais detalhes sobre como fazer com que seu aplicativo AppSource certificado para Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/23/2020
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ms.openlocfilehash: 7731aa2eb1a962d4674ed382911ba0058e1ebdaf
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101647499"
---
# <a name="get-appsource-certified-for-azure-active-directory"></a>Obtenha o AppSource Certified para Azure Active Directory

O [Microsoft AppSource](https://appsource.microsoft.com/) é um local onde os usuários de negócios podem descobrir, experimentar e gerenciar aplicativos SaaS de linha de negócios (SaaS autônomos e o complemento para produtos SaaS da Microsoft existentes).

Para listar um aplicativo SaaS autônomo no AppSource, o aplicativo deve aceitar logon único a partir de contas corporativas de qualquer empresa ou organização que possua o Azure Active Directory (Azure AD). O processo de entrada deve usar o protocolo [OpenID Connect](v2-protocols-oidc.md) ou [OAuth 2.0](v2-oauth2-auth-code-flow.md). A integração SAML não é aceita para certificação AppSource.

## <a name="multi-tenant-applications"></a>Aplicativos multilocatários

Um *aplicativo multilocatário* é aquele que aceita entradas de usuários de qualquer empresa ou organização que tenha o Azure AD sem a necessidade de uma instância, configuração ou implantação separada. O AppSource recomenda que os aplicativos implementem multilocação para habilitar a experiência de avaliação com um *clique simples*.

Para habilitar a multilocação em seu aplicativo, siga estas etapas:
1. Definir a propriedade `Multi-Tenanted` para `Yes` nas informações do registro do aplicativo no [portal do Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps). Por padrão, os aplicativos criados no portal do Azure são configurados como *[locatário único](#single-tenant-applications)*.
1. Atualize o código para enviar solicitações ao ponto de extremidade `common`. Para fazer isso, atualize o ponto de extremidade de `https://login.microsoftonline.com/{yourtenant}` para `https://login.microsoftonline.com/common*`.
1. Para algumas plataformas, como ASP.NET, você também precisa atualizar seu código para aceitar vários emissores.

Para obter mais informações sobre multilocação, consulte: [Como conectar qualquer usuário do Azure Active Directory (Azure AD) usando o padrão de aplicativo multilocatário](howto-convert-app-to-be-multi-tenant.md).

### <a name="single-tenant-applications"></a>Aplicativos de locatário único

Um *aplicativo de locatário único* é aquele que somente aceita entradas de usuários de uma instância definida do Azure AD. Os usuários externos (incluindo as contas corporativas ou de estudante de outras organizações, ou contas pessoais) podem entrar em um aplicativo de locatário único após adicionar cada usuário como conta convidado à instância do Azure AD em que o aplicativo está registrado. 

É possível adicionar usuários como contas de convidado ao Azure Active Directory por meio de [Colaboração B2B do Azure Active Directory](../external-identities/what-is-b2b.md), sendo que isso pode ser feito [via programação](../../active-directory-b2c/code-samples.md). Ao usar o B2B, os usuários podem criar um portal de autoatendimento que não requer um convite para entrar. Para obter mais informações, consulte [Portal de autoatendimento para inscrição de colaboração B2B do Azure AD](../external-identities/self-service-portal.md).

Os aplicativos de locatário único podem habilitar a experiência *Entre em Contato Comigo*, mas se você quer habilitar a experiência de avaliação gratuita/clique simples recomendada pelo AppSource, em vez disso, habilite a multilocação no aplicativo.

## <a name="appsource-trial-experiences"></a>Experiências de avaliação do AppSource

### <a name="free-trial-customer-led-trial-experience"></a>Avaliação gratuita (experiência de avaliação orientada pelo cliente)

A avaliação orientada pelo cliente é a experiência que o AppSource recomenda, pois oferece acesso a o aplicativo com um clique simples. O exemplo a seguir mostra a aparência desta experiência:

1.  Um usuário encontra seu aplicativo no site do AppSource e seleciona a opção de **avaliação gratuita** .

    ![Mostra a avaliação gratuita da experiência de avaliação orientada pelo cliente](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png)

2.  O AppSource redireciona o usuário para uma URL no seu site da Web. Seu site inicia o processo de *logon único* automaticamente (no carregamento da página).

    ![Mostra como o usuário é redirecionado para uma URL no seu site da Web](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png)

3.  O usuário é redirecionado para a página de entrada da Microsoft e o usuário fornece as credenciais para entrar.

    ![Mostra a página de entrada da Microsoft](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png)

4. O usuário dá consentimento para seu aplicativo.

    ![Exemplo: página de consentimento para um aplicativo](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png)

5.  A entrada é concluída e o usuário é Redirecionado de volta ao seu site da Web.  O usuário inicia a avaliação gratuita.

    ![Mostra a experiência que o usuário vê quando Redirecionado de volta para seu site](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png)

### <a name="contact-me-partner-led-trial-experience"></a>Entre em Contato Comigo (experiência de avaliação orientada pelo parceiro)

Você pode usar a experiência de avaliação do parceiro quando uma operação manual ou de longo prazo precisa ser feita para provisionar o usuário/empresa - por exemplo, seu aplicativo precisa provisionar máquinas virtuais, instâncias de banco de dados ou operações que levam muito tempo para serem concluídas. Nesse caso, após o usuário selecionar o botão **Solicitar avaliação** e preencher um formulário, o AppSource enviará as informações de contato do usuário. Depois de receber essas informações, você provisiona o ambiente e envia as instruções para o usuário sobre como acessar a experiência de avaliação:<br/><br/>

1. Um usuário encontra seu aplicativo no site do AppSource e, em seguida, seleciona **entrar em contato comigo**.

    ![Mostra entre em contato comigo para obter uma experiência de avaliação ministrada por parceiros](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png)

2. O usuário preenche um formulário com informações de contato.

    ![Mostra um formulário de exemplo com informações de contato](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png)

3. Você recebe as informações do usuário, configura uma instância de avaliação e envia o hiperlink para acessar o aplicativo para o usuário.

    ![Mostra o espaço reservado para informações do usuário](./media/active-directory-devhowto-appsource-certified/usercontact.png)

4. O usuário acessa seu aplicativo e conclui o processo de logon único.

    ![Mostra a tela de entrada do aplicativo](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png)

5. O usuário dá consentimento para seu aplicativo.

    ![Mostra uma página de consentimento de exemplo para um aplicativo](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png)

6. A entrada é concluída e o usuário é Redirecionado de volta ao seu site da Web. O usuário inicia a avaliação gratuita.

    ![Mostra a experiência que o usuário vê quando Redirecionado de volta para seu site](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png)

### <a name="more-information"></a>Mais informações

Para saber mais sobre a experiência de avaliação do AppSource, confira [este vídeo](https://aka.ms/trialexperienceforwebapps). 

## <a name="get-support"></a>Obter suporte

Para a integração do Azure AD, usamos o [Microsoft Q&](/answers/topics/azure-active-directory.html) a com a Comunidade para fornecer suporte.

É altamente recomendável que você faça suas perguntas no [Microsoft Q&um](/answers/topics/azure-active-directory.html) primeiro e navegue pelos problemas existentes para ver se alguém fez sua pergunta antes. Certifique-se de que suas perguntas ou comentários estão marcados com [`[azure-active-directory]`](/answers/topics/azure-active-directory.html) .

Use a seção de comentários a seguir para fornecer seus comentários e nos ajudar a aprimorar e adaptar nosso conteúdo.

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre como compilar aplicativos que dão suporte a entradas do Azure AD, consulte [Cenários de autenticação do Azure AD](authentication-flows-app-scenarios.md).
- Para saber mais sobre como listar seu aplicativo SaaS no AppSource, confira [Informações de parceiro do AppSource](https://appsource.microsoft.com/partners)