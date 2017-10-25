---
title: "Como conceder permissões a um aplicativo personalizado | Microsoft Docs"
description: "Como conceder permissões a seu aplicativo personalizado usando o portal do Azure AD ou um parâmetro de URL"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 336b945929f80e1a566f7cf71b40fd799a98c12d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-grant-permissions-to-a-custom-developed-application"></a>Como conceder permissões a um aplicativo personalizado

Para dar consentimento de forma preventiva em seu aplicativo ou se você estiver encontrado um erro afirmando que você não consentiu com um aplicativo, tente as etapas a seguir.

## <a name="how-to-perform-admin-consent-for-your-application"></a>Como executar consentimento de administrador em seu aplicativo

Isso tem o efeito de dar consentimento ao aplicativo para todos os usuários na organização.

1. Navegue até a folha **Registros do aplicativo** como um **administrador global** e selecione o aplicativo.

2. Selecione **Permissões Necessárias** e toque no botão **Conceder Permissões** na parte superior da folha.

Como alternativa, você pode construir uma solicitação para *login.microsoftonline.com* com as configurações do seu aplicativo e anexar um consentimento *&prompt=admin\_*. Após entrar com credenciais de administrador, o aplicativo recebe consentimento para todos os usuários.

## <a name="how-to-force-user-consent-for-your-application"></a>Como forçar o consentimento de usuário em seu aplicativo

* Anexe nas solicitações de autenticação *&prompt=consent*, que exige o consentimento dos usuários finais cada vez que eles se autenticam.

## <a name="next-steps"></a>Próximas etapas

[Consentir e integrar aplicativos ao AzureAD](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-integrating-applications)

[Consentimento e permissão para aplicativos convergidos do AzureAD v2.0](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-v2-scopes)<br>

[StackOverflow do AzureAD](http://stackoverflow.com/questions/tagged/azure-active-directory)
