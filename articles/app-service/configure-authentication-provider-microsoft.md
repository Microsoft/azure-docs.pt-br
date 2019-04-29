---
title: Configurar a autenticação de Conta Microsoft – Serviço de Aplicativo do Azure
description: Saiba como configurar a autenticação de conta da Microsoft para o seu aplicativo de Serviços de Aplicativos.
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: e3da856efd7d44f15f9de27c9e38375d40dc211d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60850915"
---
# <a name="how-to-configure-your-app-service-application-to-use-microsoft-account-login"></a>Como configurar seu aplicativo do Serviço de Aplicativo para usar o logon da Conta da Microsoft
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra como configurar o Serviço de Aplicativo do Azure para usar a conta da Microsoft como um provedor de autenticação. 

## <a name="register-microsoft-account"> </a>Registrar seu aplicativo na conta da Microsoft
1. Faça logon no [portal do Azure]e navegue até o seu aplicativo. Copie a **URL**, que posteriormente você usa para configurar seu aplicativo com a conta da Microsoft.
2. Navegue até a página [Meus Aplicativos] no Centro de Desenvolvedoras da conta da Microsoft e faça logon com sua conta da Microsoft, se necessário.
3. Clique em **Adicionar um aplicativo** e digite um nome de aplicativo e clique em **Criar**.
4. Anote a **ID do aplicativo**, pois você precisará dela mais tarde. 
5. Em "Plataformas", clique em **Adicionar plataforma** e selecione "Web".
6. Em "Redirecionar URIs", forneça o ponto de extremidade para seu aplicativo e clique em **Salvar**. 
   
   > [!NOTE]
   > O URI de redirecionamento é a URL do seu aplicativo acrescentada com o caminho */.auth/login/microsoftaccount/callback*. Por exemplo, `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`.   
   > Certifique-se de que você está usando o esquema HTTPS.
   
7. Em "Segredos do Aplicativo", clique em **Gerar nova senha**. Anote o valor que aparece. Depois que você sair da página, ela não será exibida novamente.

    > [!IMPORTANT]
    > A senha é uma credencial de segurança importante. Não compartilhe a senha com ninguém nem distribua-a em um aplicativo cliente.
    
8. Clique em **Salvar**

## <a name="secrets"> </a>Adicionar informações da conta da Microsoft ao seu aplicativo de Serviço de Aplicativo
1. De volta ao [Portal do Azure], navegue até o aplicativo, clique em **Configurações** > **Autenticação/Autorização**.
2. Se o recurso Autenticação/Autorização não estiver habilitado, mude-o para **Ativado**.
3. Clique em **Conta da Microsoft**. Cole os valores de ID do Aplicativo e da Senha que você obteve anteriormente e, como alternativa, habilite os escopos exigidos pelo seu aplicativo. Em seguida, clique em **OK**.
   
    ![][1]
   
    Por padrão, o Serviço de Aplicativo fornece autenticação, mas não restringe o acesso autorizado ao conteúdo do site e às APIs. Você deve autorizar os usuários no código do aplicativo.
4. (Opcional) Para restringir o acesso ao seu site somente para usuários autenticados pela conta da Microsoft, defina **Ação a ser executada quando a solicitação não for autenticada** como **Conta da Microsoft**. Isso exige que todas as solicitações sejam autenticadas e todas as solicitações não autenticadas sejam redirecionadas à conta da Microsoft para autenticação.
5. Clique em **Salvar**.

Agora você está pronto para usar uma conta da Microsoft para autenticação em seu aplicativo.

## <a name="related-content"> </a>Conteúdo relacionado
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[Meus Aplicativos]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Portal do Azure]: https://portal.azure.com/
