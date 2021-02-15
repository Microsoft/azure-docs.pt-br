---
title: Configurar a autenticação do Google
description: Saiba como configurar a autenticação do Google como um provedor de identidade para seu serviço de aplicativo ou Azure Functions aplicativo.
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.topic: article
ms.date: 09/02/2019
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: e8a9fbe6072f3628d755ad3ad5aa5a623fc3ab23
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "80519936"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-google-login"></a>Configurar seu serviço de aplicativo ou Azure Functions aplicativo para usar o logon do Google

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra como configurar Azure App serviço ou Azure Functions para usar o Google como um provedor de autenticação.

Para concluir o procedimento neste tópico, você deve ter uma conta do Google com um endereço de email verificado. Para criar uma nova conta do Google, vá para [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register-your-application-with-google"></a><a name="register"> </a>Registre seu aplicativo com o Google

1. Siga a documentação do Google no [google Sign-In para aplicativos do lado do servidor](https://developers.google.com/identity/sign-in/web/server-side-flow) para criar uma ID do cliente e um segredo do cliente. Não há necessidade de fazer nenhuma alteração de código. Basta usar as seguintes informações:
    - Para **origens JavaScript autorizadas**, use `https://<app-name>.azurewebsites.net` com o nome do seu aplicativo no *\<app-name>* .
    - Para **URI de redirecionamento autorizado**, use `https://<app-name>.azurewebsites.net/.auth/login/google/callback` .
1. Copie a ID do aplicativo e os valores de segredo do aplicativo.

    > [!IMPORTANT]
    > O segredo do aplicativo é uma credencial de segurança importante. Não compartilhe essa senha com ninguém nem distribua-a em um aplicativo cliente.

## <a name="add-google-information-to-your-application"></a><a name="secrets"> </a>Adicionar informações do Google ao seu aplicativo

1. Na [portal do Azure], vá para o aplicativo do serviço de aplicativo.
1. Selecione **Configurações** > **Autenticação/Autorização** e verifique se a **Autenticação do Serviço de Aplicativo** está **Ativada**.
1. Selecione **Google**e cole os valores de ID do aplicativo e segredo do aplicativo que você obteve anteriormente. Habilite todos os escopos necessários para seu aplicativo.
1. Selecione **OK**.

   O serviço de aplicativo fornece autenticação, mas não restringe o acesso autorizado ao conteúdo e às APIs do site. Para obter mais informações, consulte [autorizar ou negar usuários](app-service-authentication-how-to.md#authorize-or-deny-users).

1. Adicional Para restringir o acesso ao site somente para usuários autenticados pelo Google, defina **a ação a ser tomada quando a solicitação não for autenticada** para o **Google**. Quando você define essa funcionalidade, seu aplicativo requer que todas as solicitações sejam autenticadas. Ele também redireciona todas as solicitações não autenticadas para o Google para autenticação.

    > [!CAUTION]
    > Restringir o acesso dessa maneira se aplica a todas as chamadas para seu aplicativo, o que pode não ser desejável para aplicativos que têm uma página inicial publicamente disponível, como em muitos aplicativos de página única. Para esses aplicativos, **Permitir solicitações anônimas (nenhuma ação)** pode ser preferível, para que o aplicativo inicie a autenticação manualmente por conta própria. Para obter mais informações, consulte [Fluxo de autenticação](overview-authentication-authorization.md#authentication-flow).

1. Clique em **Salvar**.

Agora você está pronto para usar o Google para autenticação em seu aplicativo.

## <a name="next-steps"></a><a name="related-content"> </a>Próximas etapas

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure portal]: https://portal.azure.com/

