---
title: API da web - registro de aplicativo protegida | Azure
description: Saiba como criar uma API web protegida e as informações necessárias registrar o aplicativo.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4622cffedc159ce85166eafe571ccb26c2c1b4d
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536859"
---
# <a name="protected-web-api-app-registration"></a>API web protegida: Registro do aplicativo

Este artigo explica as especificidades de registro de aplicativo para uma API web protegida.

Confira [Início Rápido: Registrar um aplicativo com a plataforma de identidade Microsoft](quickstart-register-app.md) para obter as etapas comuns para registrar um aplicativo.

## <a name="accepted-token-version"></a>Versão aceita de token

O ponto de extremidade de plataforma de identidade do Microsoft pode emitir a dois tipos de tokens: tokens da v1.0 e v2.0 tokens. Para obter mais informações sobre esses tokens, consulte [tokens de acesso](access-tokens.md). A versão de token aceita depende de **suporte para tipos de conta** você escolheu ao criar seu aplicativo:

- Se o valor de **suporte para tipos de conta** é **contas em qualquer diretório organizacional e contas pessoais da Microsoft (por exemplo, o Skype, Xbox, Outlook.com)** , a versão do token aceito será v 2.0.
- Caso contrário, a versão de token aceita será v1.0.

Depois de criar o aplicativo, você pode determinar ou alterar a versão de token aceita seguindo estas etapas:

1. No portal do Azure, selecione seu aplicativo e, em seguida, selecione a **manifesto** para seu aplicativo.
2. No manifesto, pesquise **"accessTokenAcceptedVersion"** . Observe que seu valor será **2**. Esta propriedade especifica para o Azure Active Directory (Azure AD) que a API da web aceita tokens da v2.0. Se o valor for **nulo**, a versão do token aceita é v 1.0.
3. Se você tiver alterado a versão de token, selecione **salvar**.

> [!NOTE]
> A API da web Especifica qual versão de token (v 1.0 ou 2.0) aceita. Quando os clientes solicitam um token para sua API da web do ponto de extremidade de v2.0 do Microsoft identity platform, ele obterá um token que indica qual versão é aceito pela API da web.

## <a name="no-redirect-uri"></a>Nenhum URI de redirecionamento

As APIs da Web não precisam registrar um URI de redirecionamento porque nenhum usuário está conectado interativamente.

## <a name="expose-an-api"></a>Expor uma API

Outra configuração específica para APIs da web é a API exposta e os escopos expostos.

### <a name="resource-uri-and-scopes"></a>URI de recurso e escopos

Escopos são geralmente na forma `resourceURI/scopeName`. Para o Microsoft Graph, os escopos têm atalhos como `User.Read`. Essa cadeia de caracteres é um atalho para `https://graph.microsoft.com/user.read`.

Durante o registro de aplicativo, você precisará definir esses parâmetros:

- O URI do recurso. Por padrão, o portal de registro de aplicativo recomenda que você use `api://{clientId}`. Esse URI de recurso é exclusivo, mas não é humana legível. Você pode alterá-lo, mas verifique se que o novo valor é exclusivo.
- Um ou mais *escopos*. (Para aplicativos cliente, eles serão mostrados como *permissões delegadas* para sua API da web.)
- Um ou mais *funções de aplicativo*. (Para aplicativos cliente, eles serão mostrados como *permissões de aplicativo* para sua API da web.)

Os escopos também são exibidos na tela de consentimento é apresentada aos usuários finais do seu aplicativo. Portanto, você precisará fornecer as cadeias de caracteres correspondentes que descrevem o escopo:

- Como visto pelo usuário final.
- Como visto pelo administrador do locatário, que poderá conceder consentimento do administrador.

### <a name="exposing-delegated-permissions-scopes"></a>Expondo permissões (escopos)

1. Selecione o **expor uma API** seção no registro do aplicativo.
1. Selecione **Adicionar um escopo**.
1. Se solicitado, aceite o URI de ID do aplicativo de propostas (`api://{clientId}`), selecionando **salvar e continuar**.
1. Insira estes parâmetros:
      - Para **nome do escopo**, use **access_as_user**.
      - Para **quem pode consentir**, verifique se **administradores e usuários** está selecionado.
      - Na **nome de exibição de consentimento do administrador**, insira **acessar TodoListService como um usuário**.
      - Na **descrição de consentimento do administrador**, insira **acessa a API da Web TodoListService como um usuário**.
      - Na **nome de exibição de consentimento do usuário**, insira **acessar TodoListService como um usuário**.
      - Na **descrição do consentimento do usuário**, insira **acessa a API da Web TodoListService como um usuário**.
      - Manter **estado** definido como **habilitado**.
      - Selecione **adicionar escopo**.

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>Se sua API da web é chamado por um aplicativo daemon

Nesta seção, você aprenderá a registrar sua API web protegida, ele pode ser chamado com segurança por aplicativos daemon.

- Você precisará expor *permissões de aplicativo*. Você irá declarar somente as permissões de aplicativo porque aplicativos daemon não interagem com usuários, portanto, não fazem sentido, as permissões delegadas.
- Os administradores de locatários podem exigir o Azure Active Directory (Azure AD) para emitir tokens para sua API da web somente para aplicativos que tenham registrado para acessar uma das permissões de aplicativo da API web.

#### <a name="exposing-application-permissions-app-roles"></a>Expondo permissões de aplicativo (funções de aplicativo)

Para expor permissões de aplicativo, você precisará editar o manifesto.

1. No registro do aplicativo para seu aplicativo, selecione **manifesto**.
1. Editar o manifesto, localizando o `appRoles` definindo e adicionando um ou mais funções de aplicativo. A definição de função é fornecida no bloco JSON de exemplo a seguir. Deixe o `allowedMemberTypes` definido como `"Application"` apenas. Verifique se o `id` é um GUID exclusivo e que `displayName` e `value` não contêm espaços.
1. Salve o manifesto.

O exemplo a seguir mostra o conteúdo de `appRoles`. (O `id` pode ser qualquer GUID exclusivo.)

```JSon
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

#### <a name="ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients"></a>Garantindo que o Azure AD emite tokens para seu web API para permitido apenas clientes

Verifica a API da web para a função de aplicativo. (Que é a maneira de desenvolvedor para expor permissões de aplicativo.) Mas você também pode configurar o Azure AD para emitir um token para sua API da web somente aos aplicativos que são aprovados pelo administrador locatário para acessar sua API. Para adicionar esse aumento de segurança:

1. No aplicativo **visão geral** registro do aplicativo, selecione o link com o nome do seu aplicativo sob **aplicativo gerenciado no diretório local**. O título para esse campo pode ser truncado. Você pode, por exemplo, consulte **aplicativo gerenciado no...**

   > [!NOTE]
   >
   > Quando você seleciona esse link, você será levado até a **visão geral do aplicativo Enterprise** página associada com a entidade de serviço para seu aplicativo no locatário em que você criou. Você pode navegar para a página de registro de aplicativo usando o botão Voltar do navegador.

1. Selecione o **propriedades** página na **gerenciar** seção das páginas de aplicativo empresarial.
1. Se desejar que o Azure AD para permitir o acesso à sua API web de apenas determinados clientes, defina **atribuição de usuário necessária?** à **Sim**.

   > [!IMPORTANT]
   >
   > Se você definir **atribuição de usuário necessária?** à **Sim**, Azure AD verificará as atribuições de função de aplicativo dos clientes quando eles solicitam um token de acesso para a API da web. Se o cliente não está atribuído a qualquer função de aplicativo, o Azure AD retornará o erro `invalid_client: AADSTS501051: Application <application name> is not assigned to a role for the <web API>`.
   >
   > Se você mantiver **atribuição de usuário necessária?** definido como **nenhuma**, *Azure AD não verificar as atribuições de função de aplicativo quando um cliente solicita um token de acesso para sua API web*. Qualquer cliente de daemon (ou seja, qualquer cliente usando o fluxo de credenciais do cliente) será capaz de obter um token de acesso para a API apenas especificando seu público-alvo. Qualquer aplicativo será capaz de acessar a API sem ter que solicitar permissões para ele. Mas sua API web pode sempre, conforme explicado na seção anterior, verifique se o aplicativo tem a função correta (que é autorizada pelo administrador do Locatário). A API executa essa verificação, validando se o token de acesso tem uma funções de declaração e se o valor para essa declaração está correto. (No nosso caso, o valor é `access_as_application`.)

1. Clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Configuração de código do aplicativo](scenario-protected-web-api-app-configuration.md)
