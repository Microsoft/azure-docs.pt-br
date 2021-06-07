---
title: Logon único para aplicativos com o Proxy de Aplicativo do Azure AD | Microsoft Docs
description: Ative o logon único para aplicativos locais publicados com o Proxy de Aplicativo do Azure AD no Portal do Azure.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/12/2018
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0cb2830c019635e9020a4b586bdc370450fddb0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99253995"
---
# <a name="password-vaulting-for-single-sign-on-with-application-proxy"></a>Compartimentação de senhas para logon único com o Proxy de Aplicativo

O Proxy de Aplicativo do Azure Active Directory o ajuda a aprimorar a produtividade ao publicar aplicativos locais de forma que funcionários remotos também possam acessá-los com segurança. No Portal do Azure, você também pode configurar o logon único (SSO) para esses aplicativos. Os usuários só precisam autenticar com o Azure AD, assim podem acessar seu aplicativo corporativo sem precisar entrar novamente.

O Proxy de Aplicativo dá suporte a vários [modos de logon único](sso-options.md#choosing-a-single-sign-on-method). O logon único baseado em senha destina-se a aplicativos que usam uma combinação de nome de usuário e senha para autenticação. Quando você configura logon único baseado em senha para seu aplicativo, os usuários devem entrar no aplicativo local uma vez. Depois disso, o Azure Active Directory armazena as informações de entrada, fornecendo-as automaticamente ao aplicativo quando os usuários o acessarem remotamente.

Você já deve ter publicado e testado seu aplicativo com o Proxy de Aplicativo. Caso contrário, siga as etapas em [Publicar aplicativos usando o Proxy de Aplicativo do Azure AD](application-proxy-add-on-premises-application.md) e depois volte para este artigo.

## <a name="set-up-password-vaulting-for-your-application"></a>Definir o cofre de senha para seu aplicativo

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador.
1. Selecione **Azure Active Directory**  >  **aplicativos empresariais**  >  **todos os aplicativos**.
1. Na lista, selecione o aplicativo para o qual deseja configurar o SSO.  
1. Selecione **Proxy de Aplicativo**. 
1. Altere o **tipo de pré-autenticação** para **passagem** e selecione **salvar**. Posteriormente, você poderá voltar para o tipo de **Azure Active Directory** novamente! 
1. Selecione **Logon único**.

   ![Selecionar logon único na página de visão geral do aplicativo](./media/application-proxy-configure-single-sign-on-password-vaulting/select-sso.png)

1. Para o modo SSO, escolha **Logon Único Baseado em Senha**.
1. Para a URL de logon, insira a URL da página em que os usuários devem inserir o nome de usuário e a senha para entrar em seu aplicativo fora da rede corporativa. Ela pode ser a URL externa criada ao publicar o aplicativo por meio do Proxy de Aplicativo.

   ![Escolha Logon Único Baseado em Senha e digite a URL](./media/application-proxy-configure-single-sign-on-password-vaulting/password-sso.png)

1. Clique em **Salvar**.
1. Selecione **Proxy de Aplicativo**. 
1. Altere o **tipo de pré-autenticação** para **Azure Active Directory** e selecione **salvar**. 
1. Selecione **Usuários e Grupos**.
1. Atribua usuários ao aplicativo com a seleção de **Adicionar usuário**. 
1. Se você quiser predefinir credenciais para um usuário, marque a caixa de diálogo do nome de usuário e selecione **Atualizar credenciais**.
1. Selecione **Azure Active Directory**  >  **registros de aplicativo**  >  **todos os aplicativos**.
1. Na lista, selecione o aplicativo que você configurou com o SSO de senha.
1. Selecione **Identidade Visual**. 
1. Atualize a **URL da Home Page** com a **URL de logon** da página SSO de senha e selecione **salvar**.  



<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>Testar seu aplicativo

Vá para o portal meus aplicativos. Entre com suas credenciais (ou as credenciais de uma conta de teste que você configurou com o Access). Depois de entrar com êxito, clique no ícone do aplicativo. Isso pode disparar a instalação da extensão de navegador de entrada segura de meus aplicativos. Se o usuário tivesse credenciais predefinidas, a autenticação para o aplicativo deve ocorrer automaticamente, caso contrário, você deve especificar o nome de usuário ou a senha pela primeira vez. 

## <a name="next-steps"></a>Próximas etapas

- Leia sobre outras maneiras de implementar [logon único](what-is-single-sign-on.md)
- Saiba mais sobre [Considerações de segurança para acessar aplicativos remotamente com o Proxy de Aplicativo do Azure AD](application-proxy-security.md)
