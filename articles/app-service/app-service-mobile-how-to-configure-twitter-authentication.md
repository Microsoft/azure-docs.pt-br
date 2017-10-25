---
title: "Como configurar a autenticação do Twitter para seu aplicativo de Serviços de Aplicativos"
description: "Saiba como configurar a autenticação do Twitter para seu aplicativo de Serviços de Aplicativos."
services: app-service
documentationcenter: 
author: mattchenderson
manager: syntaxc4
editor: 
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: afde020b7817dc58ecea24eb4a09cf93d0986eb2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-your-app-service-application-to-use-twitter-login"></a>Como configurar seu aplicativo do Serviço de Aplicativo para usar o logon do Twitter
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra como configurar o Serviço de Aplicativo do Azure para usar o Twitter como um provedor de autenticação.

Para concluir o procedimento deste tópico, você deve ter uma conta do Twitter com um endereço de email verificado e um número de telefone. Para criar uma nova conta do Twitter, vá para <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

## <a name="register"> </a>Registre seu aplicativo com o Twitter
1. Faça logon no [portal do Azure]e navegue até o seu aplicativo. Copie a **URL**. Você a usará para configurar o seu aplicativo do Twitter.
2. Navegue até o site de [Desenvolvedores do Twitter,] entre com suas credenciais da conta do Twitter e clique em **Criar Novo Aplicativo**.
3. Digite o **Nome** e uma **Descrição** para o novo aplicativo. Cole a **URL** do aplicativo no valor **Site**. Em seguida, em **URL de Callback**, cole a **URL de Callback** copiada anteriormente. Esse é o seu gateway de Aplicativo Móvel, acrescentado com o caminho */.auth/login/twitter/callback*. Por exemplo: `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Certifique-se de que você está usando o esquema HTTPS.
4. Na parte inferior da página, leia e aceite os termos. Em seguida, clique em **Criar seu Aplicativo do Twitter**. Isso registrará o aplicativo e exibirá os detalhes do aplicativo.
5. Clique na guia **Configurações**, marque **Permitir que este aplicativo seja usado para entrar com o Twitter** e, em seguida, clique em **Atualizar Configurações**.
6. Selecione a guia **Chaves e Tokens de Acesso** . Tome nota dos valores da **Chave do Consumidor (Chave de API)** e **Segredo do consumidor (Segredo de API)**.
   
   > [!NOTE]
   > O segredo do consumidor é uma credencial de segurança importante. Não compartilhe esse segredo com ninguém nem o distribua com seu aplicativo.
   > 
   > 

## <a name="secrets"> </a>Adicione informações do Twitter ao seu aplicativo
1. De volta ao [portal do Azure], navegue até o seu aplicativo. Clique em **Configurações** e em **Autenticação/Autorização**.
2. Se o recurso Autenticação / Autorização não estiver habilitado, mude a opção para **Ativado**.
3. Clique em **Twitter**. Cole os valores de ID do aplicativo e segredo do aplicativo obtidos anteriormente. Em seguida, clique em **OK**.
   
   ![][1]
   
   Por padrão, o Serviço de Aplicativo fornece autenticação, mas não restringe o acesso autorizado ao conteúdo do site e às APIs. Você deve autorizar os usuários no código do aplicativo.
4. (Opcional) Para restringir o acesso ao seu site somente para usuários autenticados pelo Twitter, defina **Ação a ser executada quando a solicitação não for autenticada** como **Twitter**. Isso exige que todas as solicitações sejam autenticadas e todas as solicitações não autenticadas sejam redirecionadas ao Twitter para autenticação.
5. Clique em **Salvar**.

Agora você está pronto para usar o Twitter para autenticação em seu aplicativo.

## <a name="related-content"> </a>Conteúdo relacionado
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Desenvolvedores do Twitter,]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[portal do Azure]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
