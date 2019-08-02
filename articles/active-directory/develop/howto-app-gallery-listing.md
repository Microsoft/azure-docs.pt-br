---
title: Listar seu aplicativo na galeria de aplicativos do Azure Active Directory | Microsoft Docs
description: Saiba como listar um aplicativo que oferece suporte a logon único na galeria de aplicativos do Azure Active Directory
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/04/2019
ms.author: ryanwi
ms.reviewer: elisol, bryanla
ms.custom: aaddev, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0198e5f5df3d25ef825248ff78db95326dc755c2
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68477053"
---
# <a name="how-to-list-your-application-in-the-azure-active-directory-application-gallery"></a>Como: Listar seu aplicativo na galeria de aplicativos do Azure Active Directory

Este artigo mostra como listar um aplicativo na Galeria de aplicativos do Azure AD, implementar SSO (logon único) e gerenciar a listagem.

## <a name="what-is-the-azure-ad-application-gallery"></a>O que é a galeria de aplicativos do Azure Active Directory?

- Os clientes encontram a melhor experiência de logon único possível.
- A configuração do aplicativo é simples e mínima.
- Uma pesquisa rápida localiza seu aplicativo na galeria.
- Clientes do Azure Active Directory Premium, Básico e Gratuito podem usar essa integração.
- Os clientes mútuos recebem um tutorial de configuração passo a passo.
- Os clientes que usam SCIM podem usar o provisionamento para o mesmo aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

- Para aplicativos federados (Open ID e SAML / WS-Fed), o aplicativo deve oferecer suporte ao modelo SaaS para ser listado na galeria do Azure AD. Os aplicativos de galeria da empresa devem oferecer suporte a várias configurações de cliente e não a nenhum cliente específico.

- Para o Open ID Connect, o aplicativo deve ser multitenariado e a [estrutura de consentimento do Azure AD](consent-framework.md) deve ser implementada adequadamente para o aplicativo. O usuário pode enviar a solicitação de login para um terminal comum para que qualquer cliente possa fornecer consentimento para o aplicativo. É possível controlar acesso de usuário com base na ID do locatário e o UPN do usuário recebido no token.

- Para o SAML 2.0 / WS-Fed, seu aplicativo precisa ter a capacidade de fazer a integração SSO de SAML / WS-Fed no modo SP ou IDP. Por favor, verifique se está funcionando corretamente antes de enviar a solicitação.

- Para o SSO de senha, certifique-se de que seu aplicativo dê suporte à autenticação de formulário para que o cofre para senhas possa ser feito para que o logon único funcione conforme o esperado.

- Para solicitações de provisionamento automático de usuários, o aplicativo deve ser listado na galeria com o recurso de logon único habilitado usando SAML 2.0/WS-Fed. Você pode solicitar o fornecimento de SSO e Usuário juntos no portal, se ele ainda não estiver listado.

- Precisa de uma conta permanente para teste com pelo menos 2 usuários registrados.

>[!NOTE]
>Estamos executando com alto número de solicitações de conector SCIM, portanto, vamos parar de fazer novas solicitações em nosso portal. Aguarde suas solicitações até um aviso adicional. Nós desculpasmos esse atraso e qualquer inconveniente que isso possa ter causado.

## <a name="submit-the-request-in-the-portal"></a>Enviar a solicitação no portal

Após testar que a integração do aplicativo funciona com o Azure Active Directory, envie sua solicitação para acesso no nosso [Portal de Rede de Aplicativos](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Se você tiver uma conta do Office 365, use-a para entrar nesse portal. Caso contrário, use sua conta da Microsoft (como Outlook ou Hotmail) para entrar.

Se a página a seguir for exibida depois de entrar, entre em contato com a [equipe de integração de SSO do Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) e forneça a conta de email que você deseja usar para enviar a solicitação. Em seguida, a equipe do Azure AD adicionará a conta no Portal de rede de aplicativos da Microsoft.

![Solicitação de acesso no portal do SharePoint](./media/howto-app-gallery-listing/errorimage.png)

Depois que a conta é adicionada, você pode entrar no Portal de rede de aplicativos da Microsoft.

Se a página a seguir aparecer depois de entrar, forneça uma justificativa de negócios para a necessidade de acesso na caixa de texto e, em seguida, selecione **Solicitar Acesso**.

  ![Solicitação de acesso no portal do SharePoint](./media/howto-app-gallery-listing/accessrequest.png)

Nossa equipe analisa os detalhes e fornece-lhe o acesso adequadamente. Depois que sua solicitação for aprovada, você poderá entrar no portal e enviar a solicitação clicando no bloco **Enviar Solicitação (ISV)** na home page.

![Home page do portal do SharePoint](./media/howto-app-gallery-listing/homepage.png)

> [!NOTE]
> Se você tiver algum problema relacionado ao acesso, contate a [Equipe de Integração de SSO do Azure Active Directory](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implementing-sso-using-federation-protocol"></a>Implementando o SSO usando o protocolo de federação

Para listar um aplicativo na galeria de aplicativos do Azure AD, primeiro é necessário implementar um dos seguintes protocolos de federação compatíveis com o Azure AD, além de concordar com os termos e condições da Galeria de aplicativos do Azure AD. Leia [aqui](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/) os termos e condições da galeria de aplicativos do Azure AD.

- **OpenID Connect**: para integrar o seu aplicativo ao Azure AD usando o protocolo Open ID Connect, siga as [instruções dos desenvolvedores](authentication-scenarios.md).

    ![TimeLine de listagem de aplicativos OpenID Connect na galeria](./media/howto-app-gallery-listing/openid.png)

    * Se você desejar adicionar o aplicativo à lista na galeria usando OpenID Connect, selecione **OpenID Connect & OAuth 2.0** como acima.
    * Se você tiver algum problema relacionado ao acesso, contate a [Equipe de Integração de SSO do Azure Active Directory](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). 

- **SAML 2.0** ou **WS-Fed**: Se o aplicativo fornecer suporte para SAML 2.0, você poderá integrá-lo diretamente com um locatário do Azure Active Directory, usando as [instruções para adicionar uma aplicativo personalizado](../active-directory-saas-custom-apps.md).

  ![TimeLine de listagem de aplicativos SAML 2.0 ou WS-Fed na galeria](./media/howto-app-gallery-listing/saml.png)

  * Se você desejar adicionar o aplicativo à lista na galeria usando **SAML 2.0** ou **WS-Fed**, selecione **SAMl 2.0, WS-Fed** como acima.
  * Se você tiver algum problema relacionado ao acesso, contate a [Equipe de Integração de SSO do Azure Active Directory](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implementing-sso-using-password-sso"></a>Implementação do SSO usando o SSO de senha

Crie um aplicativo da Web que tenha uma página de login HTML para configurar a [conexão única baseada em senha](../manage-apps/what-is-single-sign-on.md). O SSO baseado em senha, também conhecido como armazenamento de senha em cofre, permite que você gerencie o acesso de usuários e senhas para aplicativos Web que não dão suporte a federação de identidades. Também é útil para cenários, nos quais vários usuários precisam compartilhar uma única conta, como contas de aplicativo de mídia social da sua organização.

![TimeLine de listagem de aplicativos SSO de Senha na galeria](./media/howto-app-gallery-listing/passwordsso.png)

* Se você desejar adicionar o aplicativo à lista na galeria usando SSO de Senha, selecione **SSO de Senha** como acima.
* Se você tiver algum problema relacionado ao acesso, contate a [Equipe de Integração de SSO do Azure Active Directory](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="updateremove-existing-listing"></a>Atualizar/remover listagem existente

Para atualizar ou remover um aplicativo existente na galeria de aplicativos do Microsoft Azure AD, primeiro você precisa enviar a solicitação no [Portal de Aplicativos de Rede ](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Se você tiver uma conta do Office 365, use-a para entrar nesse portal. Caso contrário, use sua conta da Microsoft (como Outlook ou Hotmail) para entrar.

- Selecione a opção apropriada, conforme mostrado na imagem a seguir:

    ![TimeLine de listagem de aplicativos SAML na galeria](./media/howto-app-gallery-listing/updateorremove.png)

    * Se você deseja atualizar um aplicativo existente, selecione **Atualizar listagem de aplicativo existente**.
    * Se você quiser remover um aplicativo existente da galeria do Microsoft Azure AD, selecione **Remover a listagem de aplicativo existente**.
    * Se você tiver algum problema relacionado ao acesso, contate a [Equipe de Integração de SSO do Azure Active Directory](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). 

## <a name="listing-requests-by-customers"></a>Listando solicitações por clientes

Os clientes podem enviar a solicitação de listagem de um aplicativo clicando em **solicitações de aplicativo por clientes** -> **Enviar nova solicitação**.

![Mostra o bloco aplicativos solicitados pelo cliente](./media/howto-app-gallery-listing/customer-submit-request.png)

Abaixo está o fluxo de aplicativos solicitados pelo cliente-

![Mostra o fluxo de aplicativos solicitados pelo cliente](./media/howto-app-gallery-listing/customer-request.png)

## <a name="timelines"></a>Linhas do Tempo

A linha do tempo para o processo de listagem de um aplicativo WS-Fed ou SAML 2.0 na galeria é 7 a 10 dias úteis.

   ![Linha do tempo de listagem do aplicativo SAML na Galeria](./media/howto-app-gallery-listing/timeline.png)

A linha do tempo para o processo de listagem de um aplicativo OpenID Connect na galeria é 2 a 5 dias úteis.

   ![Linha do tempo de listagem do aplicativo SAML na Galeria](./media/howto-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>Escalonamentos

Para quaisquer escalonamentos, envie email para [Equipe de Integração de SSO do Microsoft Azure AD](mailto:SaaSApplicationIntegrations@service.microsoft.com)  que é SaaSApplicationIntegrations@service.microsoft.com e responderemos o mais breve possível.
