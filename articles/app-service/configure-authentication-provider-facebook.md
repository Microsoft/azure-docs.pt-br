---
title: Configurar a autenticação do Facebook
description: Saiba como configurar a autenticação do Facebook como um provedor de identidade para seu serviço de aplicativo ou Azure Functions aplicativo.
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.topic: article
ms.date: 06/06/2019
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: b6aad323c0d6fa8f59c9fad203640c477b162503
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "80519952"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-facebook-login"></a>Configurar seu serviço de aplicativo ou Azure Functions aplicativo para usar o logon do Facebook

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este artigo mostra como configurar Azure App serviço ou Azure Functions para usar o Facebook como um provedor de autenticação.

Para concluir o procedimento neste artigo, você precisa de uma conta do Facebook que tenha um endereço de email verificado e um número de telefone celular. Para criar uma nova conta do Facebook, vá para [facebook.com].

## <a name="register-your-application-with-facebook"></a><a name="register"> </a>Registrar seu aplicativo com o Facebook

1. Vá para o site de [desenvolvedores do Facebook] e entre com suas credenciais de conta do Facebook.

   Se você não tiver uma conta do Facebook para desenvolvedores, **selecione introdução** e siga as etapas de registro.
1. Selecione **meus aplicativos**  >  **Adicionar novo aplicativo**.
1. No campo **nome de exibição** :
   1. Digite um nome exclusivo para seu aplicativo.
   1. Forneça seu **email de contato**.
   1. Selecione **Criar ID do aplicativo**.
   1. Conclua a verificação de segurança.

   O painel do desenvolvedor para seu novo aplicativo do Facebook é aberto.
1. Selecione **painel**  >  **logon do Facebook**  >  **Configurar**  >  **Web**.
1. No painel de navegação esquerdo, em **logon do Facebook**, selecione **configurações**.
1. No campo **URIs de redirecionamento OAuth válidos** , digite `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback` . Lembre-se de substituir `<app-name>` pelo nome do seu aplicativo de serviço de Azure app.
1. Selecione **Salvar alterações**.
1. No painel esquerdo, selecione **configurações**  >  **básico**. 
1. No campo **segredo do aplicativo** , selecione **Mostrar**. Copie os valores da **ID do aplicativo** e do **segredo do aplicativo**. Você os usará posteriormente para configurar seu aplicativo do serviço de aplicativo no Azure.

   > [!IMPORTANT]
   > O segredo do aplicativo é uma credencial de segurança importante. Não compartilhe essa senha com ninguém nem distribua-a em um aplicativo cliente.
   >

1. A conta do Facebook que você usou para registrar o aplicativo é um administrador do aplicativo. Neste ponto, somente os administradores podem entrar neste aplicativo.

   Para autenticar outras contas do Facebook, selecione **revisão do aplicativo** e habilitar **tornar \<your-app-name> pública** para permitir que o público geral acesse o aplicativo usando a autenticação do Facebook.

## <a name="add-facebook-information-to-your-application"></a><a name="secrets"> </a>Adicionar informações do Facebook ao seu aplicativo

1. Entre no [portal do Azure] e navegue até o aplicativo do serviço de aplicativo.
1. Selecione **Configurações** > **Autenticação/Autorização** e verifique se a **Autenticação do Serviço de Aplicativo** está **Ativada**.
1. Selecione **Facebook** e cole os valores de ID do aplicativo e segredo do aplicativo que você obteve anteriormente. Habilite todos os escopos necessários para seu aplicativo.
1. Selecione **OK**.

   ![Captura de tela das configurações do Facebook do aplicativo móvel][0]

    Por padrão, o serviço de aplicativo fornece autenticação, mas não restringe o acesso autorizado ao conteúdo e às APIs do site. Você precisa autorizar usuários em seu código de aplicativo.
1. Adicional Para restringir o acesso somente aos usuários autenticados pelo Facebook, defina **a ação a ser tomada quando a solicitação não for autenticada** no **Facebook**. Quando você define essa funcionalidade, seu aplicativo requer que todas as solicitações sejam autenticadas. Ele também redireciona todas as solicitações não autenticadas para o Facebook para autenticação.

   > [!CAUTION]
   > Restringir o acesso dessa maneira se aplica a todas as chamadas para seu aplicativo, o que pode não ser desejável para aplicativos que têm uma página inicial publicamente disponível, como em muitos aplicativos de página única. Para esses aplicativos, **Permitir solicitações anônimas (nenhuma ação)** pode ser preferível, para que o aplicativo inicie a autenticação manualmente por conta própria. Para obter mais informações, consulte [Fluxo de autenticação](overview-authentication-authorization.md#authentication-flow).

1. Clique em **Salvar**.

Agora você está pronto para usar o Facebook para autenticação em seu aplicativo.

## <a name="next-steps"></a><a name="related-content"> </a>Próximas etapas

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Desenvolvedores do Facebook]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure portal]: https://portal.azure.com/
