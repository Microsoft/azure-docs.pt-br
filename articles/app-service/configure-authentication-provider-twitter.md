---
title: Configurar a autenticação do Twitter
description: Saiba como configurar a autenticação do Twitter como um provedor de identidade para seu serviço de aplicativo ou Azure Functions aplicativo.
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.topic: article
ms.date: 02/28/2020
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: 11c913b12b4dcb7d2a5ffa532064b347b82904ef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "80519906"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-twitter-login"></a>Configurar seu serviço de aplicativo ou Azure Functions aplicativo para usar o logon do Twitter

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este artigo mostra como configurar Azure App serviço ou Azure Functions para usar o Twitter como um provedor de autenticação.

Para concluir o procedimento neste artigo, você precisa de uma conta do Twitter que tenha um endereço de email e número de telefone verificados. Para criar uma nova conta do Twitter, vá para [twitter.com].

## <a name="register-your-application-with-twitter"></a><a name="register"> </a>Registre seu aplicativo com o Twitter

1. Entre no [portal do Azure] e vá para seu aplicativo. Copie a **URL**. Você o usará para configurar seu aplicativo do Twitter.
1. Vá para o site de [desenvolvedores do Twitter] , entre com suas credenciais de conta do Twitter e selecione **criar um aplicativo**.
1. Insira o **nome do aplicativo** e a **Descrição do aplicativo** para o novo aplicativo. Cole a **URL** do aplicativo no campo **URL do site** . Na seção **URLs de retorno de chamada** , insira a URL https do seu aplicativo do serviço de aplicativo e acrescente o caminho `/.auth/login/twitter/callback` . Por exemplo, `https://contoso.azurewebsites.net/.auth/login/twitter/callback`.
1. Na parte inferior da página, digite pelo menos 100 caracteres em **diga-nos como esse aplicativo será usado**e, em seguida, selecione **criar**. Clique em **criar** novamente no pop-up. Os detalhes do aplicativo são exibidos.
1. Selecione a guia **Chaves e Tokens de Acesso** .

   Anote esses valores:
   - Chave de API
   - Chave secreta da API

   > [!NOTE]
   > A chave secreta da API é uma credencial de segurança importante. Não compartilhe esse segredo com ninguém nem o distribua com seu aplicativo.

## <a name="add-twitter-information-to-your-application"></a><a name="secrets"> </a>Adicione informações do Twitter ao seu aplicativo

1. Acesse o seu aplicativo no [Azure portal].
1. Selecione **Configurações** > **Autenticação/Autorização** e verifique se a **Autenticação do Serviço de Aplicativo** está **Ativada**.
1. Selecione **Twitter**.
1. Cole os `API key` valores e `API secret key` que você obteve anteriormente.
1. Selecione **OK**.

   ![Captura de tela das configurações do Twitter do aplicativo móvel][1]

   Por padrão, o serviço de aplicativo fornece autenticação, mas não restringe o acesso autorizado ao conteúdo e às APIs do site. Você deve autorizar os usuários no código do aplicativo.

1. (Opcional) Para restringir o acesso ao seu site somente para usuários autenticados pelo Twitter, defina **Ação a ser executada quando a solicitação não for autenticada** como **Twitter**. Quando você define essa funcionalidade, seu aplicativo requer que todas as solicitações sejam autenticadas. Ele também redireciona todas as solicitações não autenticadas para o Twitter para autenticação.

   > [!CAUTION]
   > Restringir o acesso dessa maneira se aplica a todas as chamadas para seu aplicativo, o que pode não ser desejável para aplicativos que têm uma página inicial publicamente disponível, como em muitos aplicativos de página única. Para esses aplicativos, **Permitir solicitações anônimas (nenhuma ação)** pode ser preferível, para que o aplicativo inicie a autenticação manualmente por conta própria. Para obter mais informações, consulte [Fluxo de autenticação](overview-authentication-authorization.md#authentication-flow).

1. Clique em **Salvar**.

Agora você está pronto para usar o Twitter para autenticação em seu aplicativo.

## <a name="next-steps"></a><a name="related-content"> </a>Próximas etapas

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Desenvolvedores do Twitter,]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Azure portal]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
