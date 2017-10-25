---
title: "Proteger conectores personalizados com o Azure AD – Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Adicionar segurança e autenticação à sua API e ao conector com o Azure Active Directory (Azure AD)"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: bcee842cb880002daaa3ae9d9110ee1734941b6d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="add-security-to-your-api-and-connector-with-azure-active-directory-azure-ad"></a>Adicionar segurança à sua API e ao conector com o Azure Active Directory (Azure AD)

Para proteger chamadas entre a sua API e um conector personalizado, adicione a autenticação do Azure AD à sua API Web e ao conector. Para este cenário, crie dois aplicativos do Azure Active Directory (Azure AD) – um aplicativo do Azure AD protege sua API Web, enquanto o outro aplicativo do Azure AD protege o registro do conector e adiciona o acesso delegado. 

Este tutorial usa a API do Azure Resource Manager como um exemplo. O Azure Resource Manager ajuda a gerenciar os componentes de uma solução que você construiu no Azure, como bancos de dados, máquinas virtuais e aplicativos web. Um conector personalizado para o Azure Resource Manager pode ser útil quando você desejar gerenciar recursos do Azure em seus fluxos de trabalho. Para saber mais, consulte [Visão geral do Gerenciador de Recursos do Azure](../azure-resource-manager/resource-group-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura, poderá iniciar com uma [conta gratuita do Azure](https://azure.microsoft.com/free/). Caso contrário, inscreva-se para uma [assinatura de Pagamento conforme o uso](https://azure.microsoft.com/pricing/purchase-options/).

* Para este tutorial, o [arquivo OpenAPI de exemplo para o Azure Resource Manager](https://pwrappssamples.blob.core.windows.net/samples/AzureResourceManager.json)

  > [!NOTE] 
  > O arquivo OpenAPI de exemplo não define todas as operações do Azure Resource Manager e contém somente a operação para [Listar todas as assinaturas](https://docs.microsoft.com/rest/api/resources/subscriptions#Subscriptions_List). Você pode editar este OpenAPI ou criar outro arquivo OpenAPI usando o [editor OpenAPI online](http://editor.swagger.io/).
  >
  > Você pode aplicar este tutorial a qualquer API RESTful em que desejar usar a autenticação do Azure AD.

## <a name="1-create-your-first-azure-ad-app-for-securing-your-web-api"></a>1. Criar seu primeiro aplicativo do Azure AD para proteger sua API Web

Seu primeiro aplicativo do Azure AD executa a autenticação quando seu conector personalizado chama sua API, que é a API do Azure Resource Manager neste exemplo.

1. Entre no [Portal do Azure](https://portal.azure.com). Se você tiver mais de um locatário do Azure Active Directory, confirme que você está conectado ao diretório correto verificando o diretório sob seu nome de usuário. 

   ![Confirmar seu diretório](./media/custom-connector-azure-active-directory-authentication/check-user-directory.png)

   > [!TIP]
   > Para alterar os diretórios, escolha seu nome de usuário e selecione o diretório que desejar.

2. No menu principal do Azure, escolha **Azure Active Directory** para que você possa exibir o diretório atual.

   ![Escolha "Azure Active Directory"](./media/custom-connector-azure-active-directory-authentication/azure-active-directory.png)

   > [!TIP]
   > Se o menu principal do Azure não mostrar **Azure Active Directory**, escolha **Mais serviços**. Na caixa **Filtro**, digite "Azure Active Directory" como o filtro e escolha **Azure Active Directory**.

3. No menu de diretório, em **Gerenciar**, escolha **Registros do aplicativo**. Na lista de aplicativos registrados, escolha **+ Novo registro de aplicativo**.

   ![Selecione “Registros de aplicativo”, “+ Novo registro de aplicativo”](./media/custom-connector-azure-active-directory-authentication/add-app-registrations.png)

4. Em **Criar**, forneça os detalhes para seu aplicativo do Azure AD, conforme descrito na tabela e escolha **Criar**. 

   ![Criar um aplicativo do Azure AD](./media/custom-connector-azure-active-directory-authentication/create-app1-registration.png)

   | Configuração | Valor sugerido | Descrição | 
   | ------- | --------------- | ----------- | 
   | **Nome** | *web-api-app-name* | O nome do aplicativo do Azure AD da API Web | 
   | **Tipo de Aplicativo** | **Aplicativo Web/API** | Seu tipo de aplicativo | 
   | **URL de logon** | `https://login.windows.net` | | 
   |||| 

5. Ao retornar para a lista de **Registros do aplicativo** de seu diretório, selecione o aplicativo do Azure AD.

   ![Selecione seu aplicativo do Azure AD na lista](./media/custom-connector-azure-active-directory-authentication/app1-registration-created.png)

6. Quando for exibida a página de detalhes do aplicativo, certifique-se de **copiar e salvar a *ID do aplicativo* em um lugar seguro**. Você precisará dessa ID posteriormente.

   ![Salvar "ID do aplicativo" para uso posterior](./media/custom-connector-azure-active-directory-authentication/application-id-app1.png)

7. Agora, forneça uma URL de resposta para seu aplicativo do Azure AD. No menu de **Configurações** do aplicativo, escolha **URLs de resposta**. Insira esta URL e, em seguida, escolha **Salvar**.

   ![URLs de resposta](./media/custom-connector-azure-active-directory-authentication/add-reply-url1.png)

   | Configuração | Valor sugerido | Descrição | 
   | ------- | --------------- | ----------- | 
   | **URLs de resposta** | Para a sua própria API, inserir este URL: </br>`https://{your-web-app-root-URL}/.auth/login/aad/callback` | | 
   | **Permissões delegadas** | {não é necessário} | | 
   | **Chave do cliente** | {não é necessário} | | 
   |||| 

   > [!TIP]
   > Se o menu de **Configurações** não apareceu anteriormente, escolha **Configurações** aqui:
   >
   > ![Escolha "Configurações"](./media/custom-connector-azure-active-directory-authentication/show-app1-settings-menu.png)

## <a name="2-create-your-second-azure-ad-app-for-your-custom-connector"></a>2. Criar o segundo aplicativo do Azure AD para seu conector personalizado

O segundo aplicativo do Azure AD protege o registro do conector personalizado e adiciona acesso delegado à API Web protegida pelo primeiro aplicativo do Azure AD. 

> [!IMPORTANT]
> Certifique-se de que ambos os aplicativos do Azure AD existem no mesmo diretório.

1. Volte para a lista de **Registros do aplicativo** e escolha **+ Novo registro do aplicativo** novamente.

   ![Selecione “Registros de aplicativo”, “+ Novo registro de aplicativo”](./media/custom-connector-azure-active-directory-authentication/add-app-registrations2.png)

2. Em **Criar**, forneça os detalhes para seu segundo aplicativo do Azure AD, conforme descrito na tabela e escolha **Criar**. 

   ![Criar um aplicativo do Azure AD](./media/custom-connector-azure-active-directory-authentication/create-app2-registration.png)

   | Configuração | Valor sugerido | Descrição | 
   | ------- | --------------- | ----------- | 
   | **Nome** | *your-connector-name* | O nome do aplicativo do Azure AD do seu conector | 
   | **Tipo de Aplicativo** | **Aplicativo Web/API** | Seu tipo de aplicativo | 
   | **URL de logon** | `https://login.windows.net` | | 
   |||| 

3. Ao retornar para a lista de **Registros do aplicativo** de seu diretório, selecione o seu segundo aplicativo do Azure AD.

   ![Selecione seu segundo aplicativo do Azure AD na lista](./media/custom-connector-azure-active-directory-authentication/app2-registration-created.png)

4. Quando for exibida a página de detalhes do aplicativo, certifique-se de também **copiar e salvar a *ID do aplicativo* em um lugar seguro**. Você precisará dessa ID posteriormente.

   ![Salvar "ID do aplicativo" para uso posterior](./media/custom-connector-azure-active-directory-authentication/application-id-app2.png)

5. Agora, forneça uma URL de resposta para seu aplicativo do Azure AD. No menu de **Configurações** do aplicativo, escolha **URLs de resposta**. Insira esta URL e, em seguida, escolha **Salvar**.

   | Configuração | Valor sugerido | 
   | ------- | --------------- | 
   | **URLs de resposta** | Para o conector personalizado do Azure Resource Manager, insira esta URL:`https://msmanaged-na.consent.azure-apim.net/redirect` | 
   ||| 

   > [!TIP]
   > Se o menu de **Configurações** não apareceu anteriormente, escolha **Configurações** aqui:
   >
   > ![Escolha "Configurações"](./media/custom-connector-azure-active-directory-authentication/show-app2-settings-menu.png)

6. De volta ao menu **Configurações**, selecione em **Permissões necessárias** > **Adicionar**.

   ![Permissões necessárias> Adicionar](./media/custom-connector-azure-active-directory-authentication/add-api-access1-select-permissions.png)

7. Quando o menu **Adicionar acesso à API** é aberto, escolha **Selecionar uma API** > 
**API de Gerenciamento de Serviços do Microsoft Azure** > **Selecionar**.

   ![Selecionar uma API](./media/custom-connector-azure-active-directory-authentication/add-api-access2-select-api.png)

8. No menu **Adicionar acesso à API**, escolha **Selecionar permissões**. Em **Permissões delegadas**, escolha **Acessar o Gerenciamento de Serviços do Azure como usuários de organização** > **Selecionar**.

   ![Selecione “Permissões delegadas” > “Acessar o Gerenciamento de Serviços do Azure como usuários de organização”](./media/custom-connector-azure-active-directory-authentication/add-api-access3-select-permissions.png)

   Caso contrário, para outras opções:

   | Opção | Valor sugerido | Descrição | 
   | ------ | --------------- | ----------- | 
   | **Permissões delegadas** | | Selecionar as permissões para acesso delegado à API Web | 
   |||| 

9. Agora, no menu **Adicionar acesso à API**, escolha **Concluído**.

   ![Menu "Adicionar acesso à API" > "Concluído"](./media/custom-connector-azure-active-directory-authentication/add-api-access4-done.png)

10. Agora, gere uma *chave de cliente* ou "segredo", para o aplicativo do Azure AD do seu conector. 

    1. Novamente no menu **Configurações**, escolha **Chaves**. 
    Forneça um nome para a sua chave com 16 caracteres ou menos, selecione um período de validade e, em seguida, escolha **Salvar**.

       ![Criar uma chave de cliente](./media/custom-connector-azure-active-directory-authentication/add-key.png)

    2. Quando sua chave gerada é exibida, **certifique-se de copiar e salvar essa chave em um lugar seguro** antes de sair da página **Chaves**.
    
       ![Copiar e salvar manualmente sua chave](./media/custom-connector-azure-active-directory-authentication/save-key.png)

11. Depois de salvar sua chave, você pode fechar com segurança o menu **Configurações**.

## <a name="3-add-azure-ad-authentication-to-your-web-api-app"></a>3. Adicionar a autenticação do Azure AD a seu aplicativo de API Web

Agora, ative a autenticação para a API Web com seu primeiro aplicativo do Azure AD. Para obter mais informações, saiba como [configurar seu aplicativo de Serviço de Aplicativo para usar o logon do Azure Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md).

1. No [portal do Azure](https://portal.azure.com), localize seu aplicativo de API Web que você publicou anteriormente em [Criar conectores personalizados de APIs Web](../logic-apps/custom-connector-build-web-api-app-tutorial.md).

2. Em **Configurações**, escolha **Autenticação/Autorização**. 

   1. Em **Autenticação do Serviço de Aplicativo**, escolha **Ativada**.
   2. Para **Ação a tomar quando a solicitação não está autenticada**, selecione **Efetuar logon com o Azure Active Directory**.
   3. Para **Provedores de Autenticação**, selecione **Azure Active Directory**. 

   ![Selecione “Autenticação / Autorização”](./media/custom-connector-azure-active-directory-authentication/web-api-app-authentication.png)

3. Na página **Configurações do Azure Active Directory**:

   1. Em **Modo de gerenciamento**, escolha **Expresso**.

   2. Agora, selecione o aplicativo Azure AD que seu aplicativo de API Web usa para autenticação. 
   Escolha **Selecionar aplicativo do AD existente** > **Aplicativo Azure AD**.

   3. Em **Aplicativos Azure AD**, selecione o aplicativo Azure AD que você criou anteriormente para seu aplicativo de API Web. 
   
   4. Escolha **OK** até voltar para a página **Autenticação / Autorização**.

   Por exemplo:

   ![Escolha o aplicativo Azure AD que representa seu aplicativo de API Web](./media/custom-connector-azure-active-directory-authentication/web-api-app-select-azure-ad-app.png)

4. Na página **Autenticação/Autorização**, escolha **Salvar**.

   ![Salvar configurações de autenticação](./media/custom-connector-azure-active-directory-authentication/web-api-app-azure-ad-app-save.png)

   Agora, seu aplicativo da API Web pode usar o Azure AD para autenticação.

## <a name="4-set-up-azure-ad-authentication-in-your-web-apis-openapi-file"></a>4. Configurar a autenticação do Azure AD no arquivo de OpenAPI da sua API Web

Abra o arquivo de OpenAPI para seu aplicativo de API Web para que você possa adicionar o `securityDefintions` objeto e a autenticação do Azure AD sob a propriedade `host`. Eis um exemplo que mostra a propriedade `host` e o objeto `securityDefinitions`:

``` json
// Your OpenAPI file header appears here...

"host": "{your-web-api-app-root-url}",
"schemes": [
    "https" // Make sure this is https!
],
"securityDefinitions": {
    "AAD": {
        "type": "oauth2",
        "flow": "accessCode",
        "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
        "tokenUrl": "https://login.windows.net/common/oauth2/token",
        "scopes": {}
    }
},

// Your OpenAPI document continues here...
```
Agora, você está pronto para criar e registrar seu conector personalizado.

## <a name="next-steps"></a>Próximas etapas

* [Registrar seu conector](../logic-apps/logic-apps-custom-connector-register.md)
* [Perguntas frequentes do conector personalizado](../logic-apps/custom-connector-faq.md)
