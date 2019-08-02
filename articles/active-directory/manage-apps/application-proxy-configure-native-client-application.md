---
title: Publicar aplicativos cliente nativos - Azure AD | Microsoft Docs
description: Aborda como habilitar aplicativos clientes nativos para se comunicar com o Conector de Proxy do aplicativo Azure AD para fornecer acesso remoto seguro aos seus aplicativos locais.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/15/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6cdc46ea3a45d04e6e837d0b7ad52ed8bf565cd2
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67702418"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>Como habilitar aplicativos cliente nativos interagir com aplicativos de proxy

Você pode usar o Proxy de aplicativo do Azure Active Directory (Azure AD) para publicar aplicativos web, mas ele também pode ser usado para publicar aplicativos cliente nativos que são configurados com biblioteca de autenticação de AD (ADAL) do Azure. Os aplicativos cliente nativos diferem dos aplicativos Web porque eles são instalados em um dispositivo, enquanto os aplicativos Web são acessados por meio de um navegador.

Para dar suporte a aplicativos cliente nativos, o Proxy de aplicativo aceita tokens emitidos pelo AD do Azure que são enviados no cabeçalho. O serviço de Proxy de aplicativo faz a autenticação para os usuários. Essa solução não usa tokens de aplicativo para autenticação.

![Relação entre usuários finais, o Azure AD e aplicativos publicados](./media/application-proxy-configure-native-client-application/richclientflow.png)

Para publicar aplicativos nativos, use a biblioteca de autenticação do AD do Azure, que cuida da autenticação e dá suporte a vários ambientes de cliente. O Proxy de Aplicativo se encaixa no [cenário de Aplicativo Nativo para API Web](../develop/native-app.md).

Este artigo explica as quatro etapas para publicar um aplicativo nativo com o Proxy de Aplicativo e a Biblioteca de Autenticação do Azure AD.

## <a name="step-1-publish-your-proxy-application"></a>Etapa 1: Publicar seu aplicativo de proxy

Publique seu aplicativo de proxy como faria com qualquer outro aplicativo e atribua aos usuários acesso a ele. Para saber mais, consulte [Publicar aplicativos com o Proxy de Aplicativo](application-proxy-add-on-premises-application.md).

## <a name="step-2-register-your-native-application"></a>Etapa 2: Registrar seu aplicativo nativo

Agora, você precisará registrar seu aplicativo no Azure AD, da seguinte maneira:

1. Entrar para o [portal do Azure Active Directory](https://aad.portal.azure.com/). O **Dashboard** para o **Centro de administração do Azure Active Directory** é exibida.
1. Na barra lateral, selecione **Azure Active Directory**. O **Azure Active Directory** página de visão geral é exibida.
1. Na barra lateral de visão geral do AD do Azure, selecione **registros de aplicativo**. A lista de todos os registros do aplicativo é exibida.
1. Selecione **Novo registro**. O **registrar um aplicativo** página será exibida.

   ![Criar um novo registro de aplicativo no portal do Azure](./media/application-proxy-configure-native-client-application/create.png)

1. No **nome** título, especifique um nome de exibição voltados para o usuário para seu aplicativo.
1. Sob o **suporte para tipos de conta** título, selecione um nível de acesso usando estas diretrizes:

   - Para direcionar apenas as contas que são internas para sua organização, selecione **contas neste diretório organizacional apenas**.
   - Para definir o destino apenas os negócios ou clientes educacionais, selecione **contas em qualquer diretório organizacional**.
   - Para direcionar o conjunto mais amplo de identidades da Microsoft, selecione **contas em qualquer diretório organizacional e contas pessoais da Microsoft**.

1. No **URI de redirecionamento** título, selecione **cliente público (dispositivos móvel e desktop)** , e, em seguida, digite o URI de redirecionamento para seu aplicativo.
1. Selecione e leia as **políticas de plataforma da Microsoft**e, em seguida, selecione **registrar**. Uma página de visão geral para o novo registro de aplicativo é criada e exibida.

Para obter mais informações sobre como criar um novo registro de aplicativo, consulte [integrando aplicativos com o Azure Active Directory](../develop/quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="step-3-grant-access-to-your-proxy-application"></a>Etapa 3: Conceder acesso ao seu aplicativo de proxy

Agora que você registrou seu aplicativo nativo, você pode atribuir acesso a outros aplicativos em seu diretório, nesse caso, para acessar o proxy de aplicativo. Para habilitar o aplicativo nativo a ser exposta para o proxy de aplicativo:

1. Na barra lateral da página de registro do novo aplicativo, selecione **permissões de API**. O **permissões de API** página para o novo registro de aplicativo é exibido.
1. Selecione **Adicionar uma permissão**. O **permissões de API de solicitação** página será exibida.
1. Sob o **selecionar uma API** configuração, selecione **APIs minha organização usa**. Aparece uma lista que contém os aplicativos em seu diretório que expõem APIs.
1. Digite na caixa de pesquisa ou role para localizar o aplicativo de proxy que você publicou no [etapa 1: Publique seu aplicativo proxy](#step-1-publish-your-proxy-application)e, em seguida, selecione o aplicativo de proxy.
1. No **que tipo de permissões de seu aplicativo exigir?** título, selecione o tipo de permissão. Se seu aplicativo nativo precisa acessar a API do application proxy como o usuário conectado, escolha **permissões delegadas**. Se seu aplicativo nativo é executado como um serviço em segundo plano ou daemon sem um usuário conectado, escolha **permissões de aplicativo**.
1. No **selecionar permissões** título, selecione a permissão desejada e selecione **adicionar permissões**. O **permissões de API** página para seu aplicativo nativo agora mostra o proxy de aplicativo e a permissão de API que você adicionou.

## <a name="step-4-edit-the-active-directory-authentication-library"></a>Etapa 4: Editar a Biblioteca de Autenticação do Active Directory

Edite o código de aplicativo nativo no contexto de autenticação da ADAL (Biblioteca de Autenticação do Active Directory) para incluir o seguinte texto:

```
// Acquire Access Token from AAD for Proxy Application
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<Tenant ID>");
AuthenticationResult result = await authContext.AcquireTokenAsync("< External Url of Proxy App >",
        "<App ID of the Native app>",
        new Uri("<Redirect Uri of the Native App>"),
        PromptBehavior.Never);

//Use the Access Token to access the Proxy Application
HttpClient httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");
```

As informações necessárias no código de exemplo podem ser encontradas no portal do Azure AD, da seguinte maneira:

| Informações necessárias | Como encontrá-lo no portal do Azure AD |
| --- | --- |
| \<ID do locatário > | **O Azure Active Directory** > **Properties** > **ID de diretório** |
| \<Url externa de Proxy de aplicativo > | **Aplicativos empresariais** > *seu aplicativo de proxy* > **proxy de aplicativo** > **Url externa** |
| \<ID do aplicativo do aplicativo nativo > | **Aplicativos empresariais** > *seu aplicativo nativo* > **propriedades** > **ID do aplicativo** |
| \<URI de redirecionamento do aplicativo nativo > | **O Azure Active Directory** > **registros de aplicativo** > *seu aplicativo nativo* > **URIs de redirecionamento** |
| \<Url de API do aplicativo de proxy > | **O Azure Active Directory** > **registros de aplicativo** > *seu aplicativo nativo* > **permissões de API**  >  **API / nome de PERMISSÕES** |

Depois de editar o ADAL com esses parâmetros, seus usuários possam autenticar para aplicativos cliente nativos, mesmo quando estiverem fora da rede corporativa.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o fluxo do aplicativo nativo, consulte [aplicativos nativos no Azure Active Directory](../develop/native-app.md).

Saiba mais sobre como configurar o [logon único para aplicativos no Azure Active Directory](what-is-single-sign-on.md#choosing-a-single-sign-on-method).
