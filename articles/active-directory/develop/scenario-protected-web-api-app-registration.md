---
title: Registro de aplicativo de API Web protegido | Azure
titleSuffix: Microsoft identity platform
description: Saiba como criar uma API Web protegida e as informações necessárias para registrar o aplicativo.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5d93df0b6d59e013c22e138942ab4651784421ae
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584373"
---
# <a name="protected-web-api-app-registration"></a>API Web protegida: registro de aplicativo

Este artigo explica as especificidades de registro de aplicativo para uma API Web protegida.

Para ver as etapas comuns para registrar um aplicativo, consulte [início rápido: registrar um aplicativo com a plataforma de identidade da Microsoft](quickstart-register-app.md).

## <a name="accepted-token-version"></a>Versão do token aceito

A plataforma de identidade da Microsoft pode emitir tokens v 1.0 e tokens v 2.0. Para obter mais informações sobre esses tokens, consulte [tokens de acesso](access-tokens.md).

A versão do token que sua API pode aceitar depende da seleção de **tipos de conta com suporte** ao criar o registro de aplicativo da API web no portal do Azure.

- Se o valor de **tipos de conta com suporte** for **contas em qualquer diretório organizacional e contas pessoais da Microsoft (por exemplo, Skype, Xbox, Outlook.com)**, a versão de token aceita deve ser v 2.0.
- Caso contrário, a versão do token aceita pode ser v 1.0.

Depois de criar o aplicativo, você pode determinar ou alterar a versão do token aceito seguindo estas etapas:

1. No portal do Azure, selecione seu aplicativo e, em seguida, selecione **manifesto**.
1. Localize a propriedade **accessTokenAcceptedVersion** no manifesto.
1. O valor especifica para Azure Active Directory (AD do Azure) qual versão de token aceita pela API da Web.
    - Se o valor for 2, a API da Web aceitará tokens v 2.0.
    - Se o valor for **NULL**, a API da Web aceitará tokens v 1.0.
1. Se você alterou a versão do token, selecione **salvar**.

> [!NOTE]
> A API da Web especifica qual versão de token aceita. Quando um cliente solicita um token para sua API Web da plataforma de identidade da Microsoft, o cliente obtém um token que indica qual versão de token a API da Web aceita.

## <a name="no-redirect-uri"></a>Nenhum URI de redirecionamento

APIs da Web não precisam registrar um URI de redirecionamento porque nenhum usuário está conectado interativamente.

## <a name="exposed-api"></a>API exposta

Outras configurações específicas para APIs Web são a API exposta e os escopos expostos ou funções de aplicativo.

### <a name="application-id-uri-and-scopes"></a>URI e escopos da ID do aplicativo

Os escopos geralmente têm o formato `resourceURI/scopeName` . Por Microsoft Graph, os escopos têm atalhos. Por exemplo, `User.Read` é um atalho para `https://graph.microsoft.com/user.read` .

Durante o registro do aplicativo, defina esses parâmetros:

- O URI do recurso
- Um ou mais escopos
- Uma ou mais funções de aplicativo

Por padrão, o portal de registro de aplicativos recomenda que você use o URI de recurso `api://{clientId}` . Esse URI é exclusivo, mas não é legível por humanos. Se você alterar o URI, verifique se o novo valor é exclusivo. O portal de registro de aplicativos garantirá que você use um [domínio de editor configurado](howto-configure-publisher-domain.md).

Para aplicativos cliente, os escopos aparecem como *permissões delegadas e as* funções de aplicativo são exibidas como *permissões de aplicativo* para sua API Web.

Os escopos também aparecem na janela de consentimento que é apresentada aos usuários do seu aplicativo. Portanto, forneça as cadeias de caracteres correspondentes que descrevem o escopo:

- Como visto por um usuário.
- Como visto por um administrador de locatários, que pode conceder consentimento de administrador.

As funções de aplicativo não podem ser consentidas por um usuário (pois elas são usadas por um aplicativo que chama a API da Web em nome de si mesmo). Um administrador de locatários precisará concordar com os aplicativos cliente de sua API Web expondo funções de aplicativo. Consulte [consentimento do administrador](v2-admin-consent.md) para obter detalhes

### <a name="exposing-delegated-permissions-scopes"></a>Expondo permissões delegadas (escopos)

1. Selecione **expor uma API** no registro do aplicativo.
1. Selecione **Adicionar um escopo**.
1. Se solicitado, aceite o URI da ID do aplicativo proposto ( `api://{clientId}` ) selecionando **salvar e continuar**.
1. Especifique estes valores:
    - Selecione **nome do escopo** e insira **access_as_user**.
    - Selecione **quem pode consentir** e se certificar de que **Administradores e usuários** está selecionado.
    - Selecione **nome de exibição do consentimento do administrador** e insira **TodoListService de acesso como um usuário**.
    - Selecione **Descrição de consentimento do administrador** e digite **acessar a API Web do TodoListService como um usuário**.
    - Selecione **nome de exibição do consentimento do usuário** e insira **TodoListService de acesso como um usuário**.
    - Selecione a **Descrição de consentimento do usuário** e insira **acessa a API Web TodoListService como um usuário**.
    - Mantenha o valor de **estado** definido como **habilitado**.
 1. Selecione **Adicionar escopo**.

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>Se sua API Web for chamada por um aplicativo daemon

Nesta seção, você aprenderá a registrar sua API Web protegida para que os aplicativos de daemon possam chamá-la com segurança.

- Você declara e expõe *permissões de aplicativo* somente porque os aplicativos de daemon não interagem com os usuários. As permissões delegadas não farão sentido.
- Os administradores de locatários podem exigir que o Azure AD emita tokens de API da Web somente para aplicativos que se registraram para acessar uma das permissões de aplicativo da API.

#### <a name="exposing-application-permissions-app-roles"></a>Expondo permissões de aplicativo (funções de aplicativo)

Para expor permissões de aplicativo, edite o manifesto.

1. No registro do aplicativo para seu aplicativo, selecione **manifesto**.
1. Para editar o manifesto, localize a `appRoles` configuração e adicione funções de aplicativo. As definições de função são fornecidas no bloco JSON de exemplo a seguir.
1. Deixe `allowedMemberTypes` definido como `"Application"` somente.
1. Certifique-se `id` de que seja um GUID exclusivo.
1. Certifique-se `displayName` de que `value` não contêm espaços.
1. Salve o manifesto.

O exemplo a seguir mostra o conteúdo de `appRoles` , onde o valor de `id` pode ser qualquer Guid exclusivo.

```json
"appRoles": [
    {
    "allowedMemberTypes": [ "Application" ],
    "description": "Accesses the TodoListService-Cert as an application.",
    "displayName": "access_as_application",
    "id": "ccf784a6-fd0c-45f2-9c08-2f9d162a0628",
    "isEnabled": true,
    "lang": null,
    "origin": "Application",
    "value": "access_as_application"
    }
],
```

#### <a name="ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients"></a>Garantindo que o Azure AD emita tokens para sua API Web somente para clientes permitidos

A API da Web verifica a função do aplicativo. Essa função é uma maneira do desenvolvedor de software de expor permissões de aplicativo. Você também pode configurar o Azure AD para emitir tokens de API somente para aplicativos que o administrador de locatários aprova para acesso à API.

Para adicionar esse aumento de segurança:

1. Vá para a página de **visão geral** do aplicativo para o registro do aplicativo.
1. Em **aplicativo gerenciado no diretório local**, selecione o link com o nome do seu aplicativo. O rótulo dessa seleção pode ser truncado. Por exemplo, você pode ver o **aplicativo gerenciado no.** ..

   > [!NOTE]
   >
   > Ao selecionar esse link, você vai para a página **visão geral do aplicativo empresarial** . Essa página é associada à entidade de serviço para seu aplicativo no locatário onde você a criou. Você pode ir para a página de registro do aplicativo usando o botão voltar do seu navegador.

1. Selecione a página **Propriedades** na seção **gerenciar** das páginas do aplicativo empresarial.
1. Se você quiser que o Azure AD permita o acesso à sua API Web somente de determinados clientes, defina **atribuição de usuário necessária?** para **Sim**.

   > [!IMPORTANT]
   >
   > Se você definir a **atribuição de usuário necessária?** para **Sim**, o Azure ad verificará as atribuições de função de aplicativo de um cliente quando ele solicitar um token de acesso à API Web. Se o cliente não estiver atribuído a nenhuma função de aplicativo, o Azure AD retornará a mensagem de erro "invalid_client: AADSTS501051: \<application name\> o aplicativo não está atribuído a uma função para o \<web API\> ".
   >
   > Se você mantiver a **atribuição de usuário necessária?** definida como **não**, o Azure ad não verificará as atribuições de função de aplicativo quando um cliente solicitar um token de acesso para sua API Web. Qualquer cliente daemon, ou seja, qualquer cliente que use o fluxo de credenciais do cliente, pode obter um token de acesso para a API apenas especificando seu público-alvo. Qualquer aplicativo pode acessar a API sem precisar solicitar permissões para ela.
   >
   > Mas, conforme explicado na seção anterior, sua API Web sempre pode verificar se o aplicativo tem a função certa, que é autorizada pelo administrador de locatários. A API executa essa verificação Validando se o token de acesso tem uma declaração de funções e se o valor dessa declaração está correto. No exemplo JSON anterior, o valor é `access_as_application` .

1. Clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas

Vá para o próximo artigo neste cenário, configuração de [código do aplicativo](scenario-protected-web-api-app-configuration.md).
