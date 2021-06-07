---
title: Configuração de autenticação da Microsoft
description: Saiba como configurar a autenticação da Conta Microsoft como um provedor de identidade para seu Serviço de Aplicativo ou aplicativo Azure Functions.
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.topic: article
ms.date: 08/08/2019
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: 9ec9c102680496407106a3bf9b7683890c7a63ee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93043246"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-microsoft-account-login"></a>Configuração do Serviço de Aplicativo ou do aplicativo Azure Functions para usar o logon da Conta Microsoft

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra como configurar o Serviço de Aplicativo do Azure ou o Azure Functions para usar o AAD para dar suporte a logons de conta Microsoft pessoal.

> [!NOTE]
> As contas Microsoft pessoais e as contas organizacionais usam o provedor de identidade do AAD. Atualmente não é possível configurar esse provedor de identidade para dar suporte a ambos os tipos de logon.

## <a name="register-your-app-with-microsoft-account"></a><a name="register-microsoft-account"> </a>Registro do seu aplicativo com a Conta Microsoft

1. Acesse [**Registros de aplicativo**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) no portal do Azure. Entre com sua conta Microsoft, se necessário.
1. Selecione **Novo registro** e, em seguida, insira um nome de aplicativo.
1. Em **Tipos de conta com suporte**, selecione **Contas em qualquer diretório organizacional (Qualquer diretório do Azure AD – Multilocatário) e contas Microsoft pessoais (por exemplo, Skype, Xbox)** .
1. Em **URI de redirecionamento**, selecione **Web** e, em seguida, digite `https://<app-domain-name>/.auth/login/aad/callback`. Substitua *\<app-domain-name>* pelo nome de domínio do seu aplicativo.  Por exemplo, `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Lembre-se de usar o esquema HTTPS na URL.

1. Selecione **Registrar**.
1. Copie a **ID do aplicativo (cliente)** . Você precisará dela mais tarde.
1. No painel à esquerda, selecione **Certificados e segredos** > **Novo segredo do cliente**. Insira uma descrição, selecione a duração da validade e selecione **Adicionar**.
1. Copie o valor que aparece na página **Certificados e segredos**. Depois de sair da página, ele não será exibido novamente.

    > [!IMPORTANT]
    > O valor do segredo do cliente (senha) é uma credencial de segurança importante. Não compartilhe a senha com ninguém nem distribua-a em um aplicativo cliente.

## <a name="add-microsoft-account-information-to-your-app-service-application"></a><a name="secrets"> </a>Adicionar informações da Conta Microsoft ao seu aplicativo de Serviço de Aplicativo

1. Acesse o seu aplicativo no [Azure portal].
1. Selecione **Configurações** > **Autenticação/Autorização** e verifique se a **Autenticação do Serviço de Aplicativo** está **Ativada**.
1. Em **Provedores de Autenticação**, selecione **Azure Active Directory**. Selecione **Avançado** em **Modo de gerenciamento**. Cole a ID do aplicativo (cliente) e o segredo do cliente obtidos anteriormente. Use **`https://login.microsoftonline.com/9188040d-6c67-4c5b-b112-36a304b66dad/v2.0`** para o campo **URL do emissor**.
1. Selecione **OK**.

   O Serviço de Aplicativo fornece autenticação, mas não restringe o acesso autorizado ao conteúdo do site e às APIs. Você deve autorizar os usuários no código do aplicativo.

1. (Opcional) Para restringir o acesso aos usuários da conta Microsoft, defina a **Ação a ser executada quando a solicitação não for autenticada** como **Logon com o Azure Active Directory**. Quando você define essa funcionalidade, seu aplicativo requer que todas as solicitações sejam autenticadas. Ele também redireciona todas as solicitações não autenticadas para usar o AAD para autenticação. Observe que, como você configurou a **URL do emissor** para usar o locatário da conta da Microsoft, somente as contas pessoais serão autenticadas com êxito.

   > [!CAUTION]
   > Restringir o acesso dessa maneira se aplica a todas as chamadas para seu aplicativo, o que pode não ser desejável para aplicativos que têm uma página inicial publicamente disponível, como em muitos aplicativos de página única. Para esses aplicativos, **Permitir solicitações anônimas (nenhuma ação)** pode ser preferível, para que o aplicativo inicie a autenticação manualmente por conta própria. Para obter mais informações, consulte [Fluxo de autenticação](overview-authentication-authorization.md#authentication-flow).

1. Clique em **Salvar**.

Agora você está pronto para usar uma conta da Microsoft para autenticação em seu aplicativo.

## <a name="next-steps"></a><a name="related-content"> </a>Próximas etapas

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure portal]: https://portal.azure.com/
