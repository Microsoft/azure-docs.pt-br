---
title: Depurar logon único baseado em SAML-Azure Active Directory
description: Depurar o logon único baseado em SAML para aplicativos no Azure Active Directory.
services: active-directory
ms.author: kenwith
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: troubleshooting
ms.workload: identity
ms.date: 02/18/2019
ms.reviewer: luleon, hirsin, paulgarn
ms.openlocfilehash: 4a5fbef3380db8c9b357c83b4cb8240a41a0012c
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259280"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Depurar o logon único baseado em SAML para aplicativos no Azure Active Directory

Saiba como localizar e corrigir problemas de [logon único](what-is-single-sign-on.md) para aplicativos no Azure Active Directory (AD do Azure) que usam o logon único baseado em SAML. 

## <a name="before-you-begin"></a>Antes de começar

É recomendado instalar a [My Apps Secure Sign-in Extension](../user-help/my-apps-portal-end-user-troubleshoot.md#im-having-trouble-installing-the-my-apps-secure-sign-in-extension). Essa extensão de navegador facilita a coleta de informações de resposta SAML e de solicitação SAML necessárias para resolver problemas com o logon único. Caso você não possa instalar a extensão, este artigo mostrará como resolver problemas com e sem a extensão instalada.

Para baixar e instalar a My Apps Secure Sign-in Extension, use um dos links a seguir.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

## <a name="test-saml-based-single-sign-on"></a>Testar o logon único baseado em SAML

Para testar o logon único baseado em SAML entre o Azure AD e um aplicativo de destino:

1. Entre no [portal do Azure](https://portal.azure.com) como um administrador global ou outro administrador autorizado a gerenciar aplicativos.
1. Na folha à esquerda, selecione **Azure Active Directory** e, em seguida, selecione **aplicativos empresariais**. 
1. Na lista de aplicativos empresariais, selecione o aplicativo para o qual você deseja testar o logon único e, em seguida, nas opções à esquerda, selecione **logon único**.
1. Para abrir a experiência de teste de logon único baseada em SAML, vá para **testar logon único** (etapa 5). Se o botão **testar** estiver esmaecido, você precisará preencher e salvar os atributos necessários primeiro na seção **configuração básica do SAML** .
1. Na folha **Testar logon único**, use as credenciais corporativas para entrar no aplicativo de destino. Você pode entrar como o usuário atual ou como um usuário diferente. Se você entrar como um usuário diferente, um prompt solicitará que você se autentique.

    ![Captura de tela mostrando a página testar SSO do SAML](./media/debug-saml-sso-issues/test-single-sign-on.png)

Se você tiver entrado com êxito, o teste terá sido aprovado. Nesse caso, o Azure AD emitiu um token de resposta SAML para o aplicativo. O aplicativo usou o token SAML para você entrar com êxito.

Se houver um erro na página de entrada da empresa ou na página do aplicativo, use uma das seções a seguir para resolver o erro.

## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>Resolver um erro de entrada na página de entrada da empresa

Ao tentar entrar, você poderá ver um erro na página de entrada da empresa que é semelhante ao exemplo a seguir.

![Exemplo mostrando um erro na página de entrada da empresa](./media/debug-saml-sso-issues/error.png)

Para depurar esse erro, você precisa da mensagem de erro e da solicitação SAML. A My Apps Secure Sign-in Extension coleta essas informações automaticamente e exibe as diretrizes de resolução no Azure AD.

### <a name="to-resolve-the-sign-in-error-with-the-my-apps-secure-sign-in-extension-installed"></a>Para resolver o erro de entrada com a extensão de entrada segura de meus aplicativos instalada

1. Quando ocorre um erro, a extensão redireciona você de volta para a folha de **logon único de teste** do Azure AD.
1. Na folha **testar logon único** , selecione **baixar a solicitação SAML**.
1. Serão exibidas diretrizes de resolução específicas com base no erro e nos valores na solicitação SAML.
1. Você verá um botão **corrigir** para atualizar automaticamente a configuração no Azure ad para resolver o problema. Se você não vir esse botão, o problema de entrada não será devido a uma configuração incorreta no Azure AD.

Se nenhuma resolução for fornecida para o erro de entrada, sugerimos que você use a caixa de texto comentários para informar-nos.

### <a name="to-resolve-the-error-without-installing-the-my-apps-secure-sign-in-extension"></a>Para resolver o erro sem instalar a extensão de entrada segura de meus aplicativos

1. Copie a mensagem de erro no canto inferior direito da página. A mensagem de erro inclui:
    - Um CorrelationID e um carimbo de data/hora. Esses valores são importantes quando você cria um caso de suporte com a Microsoft porque eles ajudam os engenheiros a identificar o problema e a fornecer uma resolução precisa do problema.
    - Uma instrução que identifica a causa raiz do problema.
1. Volte para o Azure AD e encontre a folha **Testar logon único**.
1. Na caixa de texto acima de **Obter diretrizes de resolução**, cole a mensagem de erro.
1. Clique em **Obter diretrizes de resolução** para exibir as etapas para resolver o problema. As diretrizes podem exigir informações da solicitação SAML ou da resposta SAML. Se você não estiver usando a extensão de entrada segura de meus aplicativos, talvez precise de uma ferramenta como o [Fiddler](https://www.telerik.com/fiddler) para recuperar a solicitação e a resposta SAML.
1. Verifique se o destino na solicitação SAML corresponde à URL do serviço de Sign-On único SAML obtida do Azure AD.
1. Verifique se o emissor na solicitação SAML é o mesmo identificador que você configurou para o aplicativo no Azure AD. O Azure AD usa o emissor para localizar um aplicativo no diretório.
1. Verifique se AssertionConsumerServiceURL é onde o aplicativo espera receber o token SAML do Azure AD. Você pode configurar esse valor no Azure AD, mas ele não é obrigatório se fizer parte da solicitação SAML.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>Resolver um erro de entrada na página do aplicativo

Você pode entrar com êxito e, em seguida, encontrar um erro na página do aplicativo. Isso ocorre quando o Azure AD emite um token para o aplicativo, mas o aplicativo não aceita a resposta.

Para resolver o erro, siga estas etapas ou assista a este [vídeo rápido sobre como usar o Azure ad para solucionar problemas de SSO do SAML](https://www.youtube.com/watch?v=poQCJK0WPUk&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0&index=8):

1. Se o aplicativo estiver na galeria do Azure AD, verifique se você seguiu todas as etapas para integrar o aplicativo com o Azure AD. Para obter as instruções de integração do aplicativo, confira a [lista de tutoriais de integração de aplicativos SaaS](../saas-apps/tutorial-list.md).
1. Recupere a resposta de SAML.
    - Se a My Apps Secure Sign-in Extension estiver instalada, na folha **Testar logon único**, clique em **Baixar resposta SAML**.
    - Se a extensão não estiver instalada, use uma ferramenta como a [Fiddler](https://www.telerik.com/fiddler) para recuperar a resposta SAML.
1. Observe estes elementos no token da resposta SAML:
   - Identificador exclusivo do usuário do valor e do formato de NameID
   - Declarações emitidas no token
   - Certificado usado para assinar o token.

     Para obter mais informações sobre a resposta SAML, confira [Protocolo SAML de Logon Único](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

1. Agora que você revisou a resposta SAML, consulte [erro na página de um aplicativo depois de entrar](application-sign-in-problem-application-error.md) para obter orientação sobre como resolver o problema. 
1. Se ainda não for possível entrar com êxito, você poderá perguntar ao fornecedor do aplicativo o que está faltando na resposta SAML.

## <a name="next-steps"></a>Próximas etapas

Agora que o logon único está funcionando para seu aplicativo, você pode [automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS](../app-provisioning/user-provisioning.md) ou começar a usar o [acesso condicional](../conditional-access/app-based-conditional-access.md).
