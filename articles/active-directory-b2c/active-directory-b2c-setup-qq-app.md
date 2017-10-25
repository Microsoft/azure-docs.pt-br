---
title: "Azure Active Directory B2C: configuração do QQ | Microsoft Docs"
description: "Forneça inscrição e entrada para consumidores com contas do QQ em seus aplicativos protegidos pelo Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 18c2cf94-8004-4de1-81c2-e45be65ce12d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/26/2017
ms.author: parakhj
ms.openlocfilehash: b32e81494b8c84799485f154ae43ad30af394caa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-qq-accounts"></a>Azure Active Directory B2C: fornecer inscrição e entrada para consumidores com contas do QQ

> [!NOTE]
> Esse recurso está em visualização.
> 

## <a name="create-a-qq-application"></a>Criar um aplicativo QQ

Para usar o QQ como um provedor de identidade no Azure AD (Azure Active Directory B2C), você precisará criar um aplicativo do QQ e fornecer a ele os parâmetros certos. Para fazer isso, é necessário ter uma conta QQ. Se você não tiver uma, poderá obtê-la em [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033).

### <a name="register-for-the-qq-developer-program"></a>Registrar-se no programa de desenvolvedores do QQ

1. Vá para o [portal do desenvolvedor do QQ](http://open.qq.com) e entre com suas credenciais de conta do QQ.
2. Depois de entrar, vá para [http://open.qq.com/reg](http://open.qq.com/reg) para registrar-se como desenvolvedor.
3. No menu, selecione **个人** (desenvolvedor individual).
4. Insira as informações necessárias no formulário e clique em **下一步** (próxima etapa).
5. Conclua o processo de verificação de email.

> [!NOTE]
> Você precisará aguardar alguns dias para ser aprovado depois de registrar-se como desenvolvedor. 

### <a name="register-a-qq-application"></a>Registrar um aplicativo do QQ

1. Vá para [https://connect.qq.com/index.html](https://connect.qq.com/index.html).
2. Clique em **应用管理** (gerenciamento de aplicativo).
3. Clique em **创建应用** (criar aplicativo).
4. Insira as informações do aplicativo necessárias.
5. Clique em **创建应用** (criar aplicativo).
6. Insira as informações necessárias.
7. Para o campo **授权回调域** (URL de retorno de chamada), digite `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`. Por exemplo, se sua `tenant_name` é contoso.onmicrosoft.com, defina a URL como `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
8. Clique em **创建应用** (criar aplicativo).
9. Na página de confirmação, clique em **应用管理** (gerenciamento de aplicativo) para retornar à página de gerenciamento de aplicativo.
10. Clique em **查看** (exibir) ao lado do aplicativo que você acabou de criar.
11. Clique em **修改** (editar).
12. Na parte superior da página, copie a **ID DO APLICATIVO** e a **CHAVE DO APLICATIVO**.

## <a name="configure-qq-as-an-identity-provider-in-your-tenant"></a>Configurar a QQ como um provedor de identidade em seu locatário
1. Siga estas etapas para [navegar até a folha de recursos do B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) no portal do Azure.
2. Na folha de recursos do B2C, clique em **Provedores de identidade**.
3. Clique em **+Adicionar** , na parte superior da folha.
4. Forneça um **Nome** amigável para a configuração do provedor de identidade. Por exemplo, insira "QQ".
5. Clique em **Tipo de provedor de identidade**, selecione **QQ** e clique em **OK**.
6. Clique em **Configurar este provedor de identidade**
7. Insira a **Chave do Aplicativo** que você copiou anteriormente como a **ID do Cliente**.
8. Insira o **Segredo do Aplicativo** que você copiou anteriormente como o **Segredo do Cliente**.
9. Clique em **OK** e em **Criar** para salvar sua configuração do QQ.

