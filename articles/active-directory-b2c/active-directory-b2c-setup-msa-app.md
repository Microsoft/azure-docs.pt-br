---
title: "Azure Active Directory B2C: configuração da conta da Microsoft | Microsoft Docs"
description: "Forneça inscrição e entrada aos consumidores com contas da Microsoft em seus aplicativos protegidos pelo Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 06407322-142c-4cb3-9106-a8d752c4c853
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
ms.openlocfilehash: 59879dc0b3fc1d7af3e2a1f67f1701f451de9126
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-microsoft-accounts"></a>Azure Active Directory B2C: fornecer inscrição e entrada aos consumidores com contas da Microsoft
## <a name="create-a-microsoft-account-application"></a>Criar um aplicativo de conta da Microsoft
Para usar a conta da Microsoft como um provedor de identidade no Azure AD (Azure Active Directory) B2C, você precisará criar um aplicativo de conta da Microsoft e fornecer a ele os parâmetros certos. Para fazer isso, é necessário ter uma conta da Microsoft. Se não tiver uma, você poderá obtê-la em [https://www.live.com/](https://www.live.com/).

1. Vá para o [Portal de Registro de Aplicativos da Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) e entre com suas credenciais da conta da Microsoft.
2. Clique em **Adicionar um aplicativo**.
   
    ![Conta da Microsoft – Adicionar um novo aplicativo](./media/active-directory-b2c-setup-msa-app/msa-add-new-app.png)
3. Forneça um **Nome** para seu aplicativo e clique em **Criar aplicativos**.
   
    ![Conta da Microsoft – Nome do aplicativo](./media/active-directory-b2c-setup-msa-app/msa-app-name.png)
4. Copie o valor de **ID do Aplicativo**. Você precisará dele para configurar a conta da Microsoft como um provedor de identidade em seu locatário.
   
    ![Conta da Microsoft - Id do Aplicativo](./media/active-directory-b2c-setup-msa-app/msa-app-id.png)
5. Clique em **Adicionar plataforma** e escolha **Web**.
   
    ![Conta da Microsoft - Adicionar plataforma](./media/active-directory-b2c-setup-msa-app/msa-add-platform.png)
   
    ![Conta da Microsoft - Web](./media/active-directory-b2c-setup-msa-app/msa-web.png)
6. Insira `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` no campo **URLs de Redirecionamento** . Substitua **{tenant}** pelo nome do locatário (por exemplo, contosob2c.onmicrosoft.com).
   
    ![Conta da Microsoft – URL de redirecionamento](./media/active-directory-b2c-setup-msa-app/msa-redirect-url.png)
7. Clique em **Gerar Nova Senha** na seção **Segredos do Aplicativo**. Copie a nova senha exibida na tela. Você precisará dele para configurar a conta da Microsoft como um provedor de identidade em seu locatário. A senha é uma credencial de segurança importante.
   
    ![Conta da Microsoft - Gerar nova senha](./media/active-directory-b2c-setup-msa-app/msa-generate-new-password.png)
   
    ![Conta da Microsoft - Nova senha](./media/active-directory-b2c-setup-msa-app/msa-new-password.png)
8. Marque a caixa que diz **suporte do SDK do Live** na seção **Opções Avançadas**. Clique em **Salvar**.
   
    ![Conta da Microsoft - Suporte ao SDK do Live](./media/active-directory-b2c-setup-msa-app/msa-live-sdk-support.png)

## <a name="configure-microsoft-account-as-an-identity-provider-in-your-tenant"></a>Configurar a conta da Microsoft como um provedor de identidade no locatário
1. Siga estas etapas para [navegar até a folha de recursos do B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) no portal do Azure.
2. Na folha de recursos do B2C, clique em **Provedores de identidade**.
3. Clique em **+Adicionar** , na parte superior da folha.
4. Forneça um **Nome** amigável para a configuração do provedor de identidade. Por exemplo, insira "MSA".
5. Clique em **Tipo do provedor de identidade**, selecione **Conta da Microsoft** e clique em **OK**.
6. Clique em **Configurar este provedor de identidade** e insira a ID do cliente e o segredo do cliente do aplicativo da conta da Microsoft criado anteriormente.
7. Clique em **OK** e em **Criar** para salvar a configuração da conta da Microsoft.

