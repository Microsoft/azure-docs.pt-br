---
title: "Azure Active Directory B2C: configuração do Weibo | Microsoft Docs"
description: "Forneça inscrição e conexão para consumidores com contas do Weibo em seus aplicativos protegidos pelo Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 1860de34-94cb-4ceb-851e-102f930f7230
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/26/2017
ms.author: parakhj
ms.openlocfilehash: 00c5d3781455c80b33bdbb4c872ae354531baf3e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-weibo-accounts"></a>Azure Active Directory B2C: fornecer inscrição e conexão para consumidores com contas do Weibo

> [!NOTE]
> Esse recurso está em visualização. Não use esse provedor de identidade no ambiente de produção.
> 

## <a name="create-a-weibo-application"></a>Criar um aplicativo Weibo

Para usar o Weibo como um provedor de identidade no Azure AD (Active Directory) B2C, você precisa criar um aplicativo Weibo e fornecer a ele os parâmetros certos. Para fazer isso, é necessário ter uma conta Weibo. Caso não tenha, você pode obter uma em [http://weibo.com/signup/signup.php?lang=en-us](http://weibo.com/signup/signup.php?lang=en-us).

### <a name="register-for-the-weibo-developer-program"></a>Registrar-se no programa de desenvolvedores do Weibo

1. Vá para o [portal do desenvolvedor do Weibo](http://open.weibo.com/) e entre com suas credenciais de conta do Weibo.
2. Depois de entrar, clique no seu nome de exibição no canto superior direito.
3. No menu suspenso, selecione **编辑开发者信息** (editar informações do desenvolvedor).
4. Insira as informações necessárias no formulário e clique em **提交** (enviar).
5. Conclua o processo de verificação de email.
6. Acesse a [página de verificação de identidade](http://open.weibo.com/developers/identity/edit).
7. Insira as informações necessárias no formulário e clique em **提交** (enviar).

### <a name="register-a-weibo-application"></a>Registrar um aplicativo Weibo

1. Acesse a [nova página de registro de aplicativo Weibo](http://open.weibo.com/apps/new).
2. Insira as informações necessárias de aplicativo.
3. Clique em **创建** (criar).
4. Copie os valores de **Chave do Aplicativo** e **Segredo do Aplicativo**. Você precisará dessas informações posteriormente.
5. Carregue as fotos necessárias e insira as informações necessárias.
6. Clique em **保存以上信息** (salvar).
7. Clique em **高级信息** (informações avançadas).
8. Clique em **编辑** (editar) ao lado do campo para **授权设置** (URL de redirecionamento) do OAuth2.0.
9. Insira `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp` para **授权设置** (URL de redirecionamento) do OAuth2.0. Por exemplo, se seu `tenant_name` for contoso.onmicrosoft.com, defina a URL para ser `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
10. Clique em **提交** (enviar).  

## <a name="configure-weibo-as-an-identity-provider-in-your-tenant"></a>Configurar o Weibo como um provedor de identidade em seu locatário
1. Siga estas etapas para [navegar até a folha de recursos do B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) no portal do Azure.
2. Na folha de recursos do B2C, clique em **Provedores de identidade**.
3. Clique em **+Adicionar** , na parte superior da folha.
4. Forneça um **Nome** amigável para a configuração do provedor de identidade. Por exemplo, insira "Weibo".
5. Clique em **Tipo de provedor de identidade**, selecione **Weibo** e clique em **OK**.
6. Clique em **Configurar este provedor de identidade**
7. Insira a **Chave do Aplicativo** que você copiou anteriormente como a **ID do Cliente**.
8. Insira o **Segredo do Aplicativo** que você copiou anteriormente como o **Segredo do Cliente**.
9. Clique em **OK** e em **Criar** para salvar sua configuração Weibo.

