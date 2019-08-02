---
title: Depurar o logon único baseado em SAML – Azure Active Directory | Microsoft Docs
description: Depurar o logon único baseado em SAML para aplicativos no Azure Active Directory.
services: active-directory
author: rwike77
documentationcenter: na
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: luleon, hirsin, smalser
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4331acf639af90448b5508e3487f4979e9b82c45
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482719"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Depurar o logon único baseado em SAML para aplicativos no Azure Active Directory

Saiba como localizar e corrigir problemas de [logon único](../manage-apps/what-is-single-sign-on.md) em aplicativos no Azure AD (Azure Active Directory) compatíveis com o [SAML (Security Assertion Markup Language) 2.0](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language). 

## <a name="before-you-begin"></a>Antes de começar

É recomendado instalar a [My Apps Secure Sign-in Extension](../user-help/my-apps-portal-end-user-troubleshoot.md#im-having-trouble-installing-the-my-apps-secure-sign-in-extension). Essa extensão de navegador torna fácil reunir a solicitação SAML e informações de resposta SAML que você precisa para resolver problemas com o logon único. Caso você não possa instalar a extensão, este artigo mostrará como resolver problemas com e sem a extensão instalada.

Para baixar e instalar a My Apps Secure Sign-in Extension, use um dos links a seguir.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

## <a name="test-saml-based-single-sign-on"></a>Testar o logon único baseado em SAML

Para testar com base em SAML logon único entre o Azure AD e um aplicativo de destino:

1. Entre no [portal do Azure](https://portal.azure.com) como um administrador global ou outro administrador autorizado a gerenciar aplicativos.
1. Na folha à esquerda, selecione **Azure Active Directory**e, em seguida, selecione **aplicativos empresariais**. 
1. Na lista de aplicativos empresariais, selecione o aplicativo para a qual você deseja testar o logon único e, em seguida, entre as opções na esquerda, selecione **logon único**.
1. Para abrir a baseado em SAML única teste experiência de logon, acesse **testar o logon único** (etapa 5). Se o **teste** botão estiver esmaecido, você precisará preencher atenção e economizar os atributos requeridos pela primeira vez o **básicas de configuração de SAML** seção.
1. Na folha **Testar logon único**, use as credenciais corporativas para entrar no aplicativo de destino. Você pode entrar como o usuário atual ou como um usuário diferente. Se você entrar como um usuário diferente, um prompt solicitará que você se autentique.

    ![Captura de tela mostrando a página de SSO do SAML de teste](./media/howto-v1-debug-saml-sso-issues/test-single-sign-on.png)

Se você tiver entrado com êxito, o teste terá sido aprovado. Nesse caso, o Azure AD emitiu um token de resposta SAML para o aplicativo. O aplicativo usou o token SAML para você entrar com êxito.

Se houver um erro na página de entrada da empresa ou na página do aplicativo, use uma das seções a seguir para resolver o erro.

## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>Resolver um erro de entrada na página de entrada da empresa

Quando você tentar entrar, você pode ver um erro em sua página de entrada da empresa que é semelhante ao exemplo a seguir.

![Exemplo mostrando um erro na página de entrada da empresa](./media/howto-v1-debug-saml-sso-issues/error.png)

Para depurar esse erro, você precisa da mensagem de erro e da solicitação SAML. A My Apps Secure Sign-in Extension coleta essas informações automaticamente e exibe as diretrizes de resolução no Azure AD.

### <a name="to-resolve-the-sign-in-error-with-the-my-apps-secure-sign-in-extension-installed"></a>Para resolver o erro de conexão com o segura dos meus aplicativos extensão de entrada instalados

1. Quando ocorre um erro, a extensão redireciona para o Azure AD **testar o logon único** folha.
1. Sobre o **testar o logon único** folha, selecione **baixar a solicitação SAML**.
1. Serão exibidas diretrizes de resolução específicas com base no erro e nos valores na solicitação SAML.
1. Você verá uma **corrigi-lo** botão para atualizar automaticamente a configuração do AD do Azure para resolver o problema. Se você não vir esse botão, em seguida, o problema de entrada não é devido a uma configuração incorreta no Azure AD.

Se nenhuma resolução é fornecida para o erro de conexão, sugerimos que você use a caixa de texto de comentários para informar-nos.

### <a name="to-resolve-the-error-without-installing-the-my-apps-secure-sign-in-extension"></a>Para resolver o erro sem instalar o segura dos meus aplicativos extensão de entrada

1. Copie a mensagem de erro no canto inferior direito da página. A mensagem de erro inclui:
    - Um CorrelationID e um carimbo de data/hora. Esses valores são importantes quando você cria um caso de suporte com a Microsoft porque eles ajudam os engenheiros a identificar o problema e a fornecer uma resolução precisa do problema.
    - Uma instrução que identifica a causa raiz do problema.
1. Volte para o Azure AD e encontre a folha **Testar logon único**.
1. Na caixa de texto acima de **Obter diretrizes de resolução**, cole a mensagem de erro.
1. Clique em **Obter diretrizes de resolução** para exibir as etapas para resolver o problema. As diretrizes podem exigir informações da solicitação SAML ou da resposta SAML. Se você não estiver usando o segura dos meus aplicativos extensão de entrada, talvez seja necessário uma ferramenta, como [Fiddler](https://www.telerik.com/fiddler) para recuperar a solicitação SAML e a resposta.
1. Verifique se o destino na solicitação SAML corresponde para o SAML Single Sign-On URL do serviço obtido no Azure AD.
1. Verifique se que o emissor na solicitação SAML é o mesmo identificador que você configurou para o aplicativo no Azure AD. O Azure AD usa o emissor para localizar um aplicativo no diretório.
1. Verifique se que assertionconsumerserviceurl é onde o aplicativo espera receber o token SAML do Azure AD. Você pode configurar esse valor no AD do Azure, mas não é obrigatório, se ele fizer parte da solicitação SAML.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>Resolver um erro de entrada na página do aplicativo

Você pode entrar com êxito e, em seguida, encontrar um erro na página do aplicativo. Isso ocorre quando o Azure AD emite um token para o aplicativo, mas o aplicativo não aceita a resposta.

Para resolver o erro, siga estas etapas:

1. Se o aplicativo na Galeria do Azure AD, verifique se você seguiu todas as etapas para integrar o aplicativo com o Azure AD. Para obter as instruções de integração do aplicativo, confira a [lista de tutoriais de integração de aplicativos SaaS](../saas-apps/tutorial-list.md).
1. Recupere a resposta de SAML.
    - Se a My Apps Secure Sign-in Extension estiver instalada, na folha **Testar logon único**, clique em **Baixar resposta SAML**.
    - Se a extensão não estiver instalada, use uma ferramenta como a [Fiddler](https://www.telerik.com/fiddler) para recuperar a resposta SAML.
1. Observe estes elementos no token da resposta SAML:
   - Identificador exclusivo do usuário do valor e do formato de NameID
   - Declarações emitidas no token
   - Certificado usado para assinar o token.

     Para obter mais informações sobre a resposta SAML, confira [Protocolo SAML de Logon Único](single-sign-on-saml-protocol.md).

1. Agora que você revisou a resposta SAML, consulte [erro na página do aplicativo depois de entrar no](../manage-apps/application-sign-in-problem-application-error.md) para obter orientação sobre como resolver o problema. 
1. Se você estiver ainda não é possível entrar com êxito, você pode fazer com que o fornecedor do aplicativo o que está faltando da resposta SAML.

## <a name="next-steps"></a>Próximas etapas

Agora que o logon único está funcionando para seu aplicativo, você poderia [automatizar o provisionamento e desprovisionamento para aplicativos SaaS](../manage-apps/user-provisioning.md) ou [Introdução ao acesso condicional](../conditional-access/app-based-conditional-access.md).
