---
title: "Azure Active Directory B2C: configuração do LinkedIn | Microsoft Docs"
description: "Forneça inscrição e entrada para consumidores com contas do LinkedIn em seus aplicativos protegidos pelo Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: fa51a16b-9ce9-4e27-9eff-0869b4c4f0ef
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
ms.openlocfilehash: 1a6c4b19261aa34e668554ccad2b6340cddf9bf5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-linkedin-accounts"></a>Azure Active Directory B2C: fornecer inscrição e entrada aos consumidores com contas do LinkedIn
## <a name="create-a-linkedin-application"></a>Criar um aplicativo do LinkedIn
Para usar o LinkedIn como provedor de identidade no Azure AD (Azure Active Directory) B2C, será necessário primeiro criar um aplicativo do LinkedIn  e fornecê-lo com os parâmetros corretos. Você precisa de uma conta do LinkedIn para fazer isso. Se você não tiver uma, poderá obtê-la em [https://www.linkedin.com/](https://www.linkedin.com/).

1. Acesse o [site de Desenvolvedores do LinkedIn](https://www.developer.linkedin.com/) e entre com suas credencias de conta do LinkedIn.
2. Clique em **Meus Aplicativos** na barra de menus superior e em **Criar Aplicativo**.
   
    ![LinkedIn — Novo aplicativo](./media/active-directory-b2c-setup-li-app/linkedin-new-app.png)
3. No formulário **Criar um Novo Aplicativo**, preencha as informações relevantes (**nome da empresa**, **nome**, **descrição**, **URL do logotipo do aplicativo**, **uso do aplicativo**, **URL do site**, **email comercial** e **telefone comercial**).
4. Aceite os **Termos de Uso da API do LinkedIn** e clique em **Enviar**.
   
    ![LinkedIn — Registrar aplicativo](./media/active-directory-b2c-setup-li-app/linkedin-register-app.png)
5. Copie os valores de **ID do cliente** e **Segredo do cliente**. (Você pode encontrá-los em **Chaves de Autenticação**.) Você precisará de ambos para configurar o LinkedIn como um provedor de identidade no seu locatário.
   
   > [!NOTE]
   > **Segredo do Cliente** é uma credencial de segurança importante.
   > 
   > 
6. Digite `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` no campo **URLs de Redirecionamento Autorizado** (em **OAuth 2.0**). Substitua **{tenant}** pelo nome do locatário (por exemplo, contoso.onmicrosoft.com). Clique em **Adicionar** e depois em **Atualizar**. O valor **{tenant}** diferencia letras maiúsculas de minúsculas.
   
    ![LinkedIn — Configurar aplicativo](./media/active-directory-b2c-setup-li-app/linkedin-setup.png)

## <a name="configure-linkedin-as-an-identity-provider-in-your-tenant"></a>Configurar o LinkedIn como um provedor de identidade no locatário
1. Siga estas etapas para [navegar até a folha de recursos do B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) no Portal do Azure.
2. Na folha de recursos do B2C, clique em **Provedores de identidade**.
3. Clique em **+Adicionar** , na parte superior da folha.
4. Forneça um **Nome** amigável para a configuração do provedor de identidade. Por exemplo, insira "LI".
5. Clique em **Tipo do provedor de identidade**, selecione **LinkedIn** e clique em **OK**.
6. Clique em **Configurar esse provedor de identidade** e insira a ID e o segredo do cliente do aplicativo do LinkedIn que você criou anteriormente.
7. Clique em **OK** e em **Criar** para salvar sua configuração do LinkedIn.

