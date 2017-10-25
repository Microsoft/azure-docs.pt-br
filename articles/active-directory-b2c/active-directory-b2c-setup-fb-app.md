---
title: "Azure Active Directory B2C: configuração do Facebook | Microsoft Docs"
description: "Forneça inscrição e entrada para consumidores com contas do Facebook em seus aplicativos protegidos pelo Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: sromeroz
manager: krassk
editor: sromeroz
ms.assetid: b875f235-a1d2-4abb-b9f0-b89beac38a32
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/7/2017
ms.author: sromeroz
ms.openlocfilehash: 8c2154fcf33537358b549395d15b4ba937371cd0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-facebook-accounts"></a>Azure Active Directory B2C: fornecer inscrição e entrada para consumidores com contas do Facebook
## <a name="create-a-facebook-application"></a>Criar um aplicativo do Facebook
Para usar o Facebook como provedor de identidade no Azure AD (Azure Active Directory) B2C, será necessário primeiro criar um aplicativo do Facebook e fornecê-lo com os parâmetros corretos. Você precisa de uma conta do Facebook para fazer isso. Se você não tiver uma, poderá obtê-la em [https://www.facebook.com/](https://www.facebook.com/).

1. Acesse o site [Desenvolvedores do Facebook](https://developers.facebook.com/) e entre com suas credenciais de conta do Facebook.
2. Se ainda não tiver feito isso, você precisará registrar-se como desenvolvedor do Facebook. Para fazer isso, clique em **Registrar** (no canto superior direito da página), aceite as políticas do Facebook e conclua as etapas de registro.
3. Clique em **Meus Aplicativos** e em **Adicionar um Novo Aplicativo**. 
4. No formulário, forneça um **Nome de Exibição** e um **Email de Contato** válido.
5. Clique em **Criar ID de Aplicativo**. Isso pode exigir a aceitação das políticas de plataforma do Facebook e a conclusão de uma verificação de segurança online.
6. Na coluna à esquerda, clique em **Configurações** e selecione **Básica**, caso ainda não esteja selecionado.
7. Selecione uma **Categoria**. 
8. Clique em **+Adicionar Plataforma** e selecione **Site**.
   
    ![Facebook - Configurações](./media/active-directory-b2c-setup-fb-app/fb-settings.png)
   
    ![Facebook - Configurações - Site](./media/active-directory-b2c-setup-fb-app/fb-website.png)
9. Digite `https://login.microsoftonline.com/` no campo **URL do Site** e clique em **Salvar Alterações** na parte inferior da página.
   
    ![Facebook - URL do Site](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)

10. Copie o valor de **ID do aplicativo**. Clique em **Mostrar** e copie o valor de **Segredo do Aplicativo**. Você precisará de ambos para configurar o Facebook como um provedor de identidade em seu locatário. **Segredo do Aplicativo** é uma credencial de segurança importante.
   
    ![Facebook - ID do Aplicativo e Segredo do Aplicativo](./media/active-directory-b2c-setup-fb-app/fb-app-id-app-secret.png)
11. Clique em **+ Adicionar Produto** no painel de navegação esquerdo e no botão **Configurar** para **Logon do Facebook**.
   
    ![Facebook - Logon no Facebook](./media/active-directory-b2c-setup-fb-app/fb-login.png)
12. Clique em **Configurações** na barra de navegação direita em **Logon do Facebook**

    ![Facebook - configurações de Logon no Facebook](./media/active-directory-b2c-setup-fb-app/fb-login-settings.png)
13. Digite `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` no campo **URIs de redirecionamento OAuth válidos** na seção **Configurações do Cliente OAuth**. Substitua **{tenant}** pelo nome do locatário (por exemplo, contosob2c.onmicrosoft.com). Clique em **Salvar Alterações** na parte inferior da página.
    
    ![Facebook - URI de Redirecionamento de OAuth](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)
14. Para tornar seu aplicativo do Facebook utilizável pelo Azure AD B2C, você precisa torná-lo público. É possível fazer isso clicando em **Análise de Aplicativos** na navegação à esquerda, ativando a opção na parte superior da página para **SIM** e clicando em **Confirmar**.
    
    ![Facebook - Público do aplicativo](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## <a name="configure-facebook-as-an-identity-provider-in-your-tenant"></a>Configurar o Facebook como um provedor de identidade no seu locatário
1. Siga estas etapas para [navegar até a folha de recursos do B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) no portal do Azure.
2. Na folha de recursos do B2C, clique em **Provedores de identidade**.
3. Clique em **+Adicionar** , na parte superior da folha.
4. Forneça um **Nome** amigável para a configuração do provedor de identidade. Por exemplo, insira “Facebook”.
5. Clique em **Tipo de provedor de identidade**, selecione **Facebook** e clique em **OK**.
6. Clique em **Configurar este provedor de identidade** e insira a ID e o segredo do aplicativo (do aplicativo do Facebook criado anteriormente) nos campos **ID do Cliente** e **Segredo do cliente**, respectivamente.
7. Clique em **OK** e em **Criar** para salvar sua configuração do Facebook.

> [!NOTE]
> A adição de um **Provedor de identidade** ao seu locatário não modifica as políticas existentes. Lembre-se de atualizar as políticas, incluindo o provedor de identidade que você acabou de criar.
>