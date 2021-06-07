---
title: Protocolos de consentimento do administrador da plataforma de identidade da Microsoft
description: Uma descrição da autorização na plataforma de identidade da Microsoft, incluindo escopos, permissões e consentimento.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: b6fb5f680dfa5e2c87533083e3df4c2bae1ed12a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102097016"
---
# <a name="admin-consent-on-the-microsoft-identity-platform"></a>Consentimento do administrador na plataforma Microsoft Identity

Algumas permissões exigem consentimento de um administrador antes que possam ser concedidas dentro de um locatário.  Você também pode usar o ponto de extremidade de consentimento do administrador para conceder permissões a um locatário inteiro.

## <a name="recommended-sign-the-user-into-your-app"></a>Recomendado: conectar o usuário ao aplicativo

Normalmente, quando você cria um aplicativo que usa o ponto de extremidade de consentimento do administrador, o aplicativo precisa de uma página ou de um modo de exibição em que o administrador possa aprovar as permissões do aplicativo. Essa página pode ser parte do fluxo de inscrição no aplicativo, parte das configurações do aplicativo ou um fluxo dedicado de "conexão". Em muitos casos, faz sentido que o aplicativo somente mostre o modo de exibição "conectar" depois que o usuário entra com uma conta corporativa ou de estudante da Microsoft.

Ao conectar o usuário ao seu aplicativo, você pode identificar a organização à qual o administrador pertence antes de pedir a ele que aprove as permissões necessárias. Embora não seja estritamente necessário, isso pode ajudá-lo a criar uma experiência mais intuitiva para os usuários empresariais. Para conectar o usuário, siga os [tutoriais do protocolo de plataforma de identidade da Microsoft](active-directory-v2-protocols.md).

## <a name="request-the-permissions-from-a-directory-admin"></a>Solicitar permissões de um administrador de diretório

Quando você estiver pronto para solicitar permissões de administrador da empresa, redirecione o usuário para o *ponto de extremidade de consentimento do administrador* da plataforma de identidade da Microsoft.

```none
https://login.microsoftonline.com/{tenant}/v2.0/adminconsent
        ?client_id=6731de76-14a6-49ae-97bc-6eba6914391e
        &scope=https://graph.microsoft.com/Calendars.Read https://graph.microsoft.com/Mail.Send
        &redirect_uri=http://localhost/myapp/permissions
        &state=12345
```

| Parâmetro | Condição | Descrição |
| :--- | :--- | :--- |
| `tenant` | Obrigatório | O locatário do diretório para o qual você deseja solicitar permissão. Pode ser fornecido no GUID ou formato de nome amigável, OU referenciado de maneira genérica com `organizations`, como visto no exemplo. Não use ' Common ', pois as contas pessoais não podem fornecer consentimento de administrador, exceto no contexto de um locatário. Para garantir melhor compatibilidade com as contas pessoais que gerenciam locatários, use a ID do locatário se possível. |
| `client_id` | Obrigatório | A **ID do Aplicativo (cliente)** que a experiência [Portal do Microsoft Azure - Registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) atribui ao seu aplicativo. |
| `redirect_uri` | Obrigatório |O URI de redirecionamento onde você deseja que a resposta seja enviada para ser tratada pelo aplicativo. Ela deve corresponder exatamente a um redirecionamento de URIs que você registrou no portal de registro de aplicativo. |
| `state` | Recomendadas | Um valor incluído na solicitação que também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. Use o estado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página ou exibição em que ele estava. |
|`scope` | Obrigatório | Define o conjunto de permissões que estão sendo solicitadas pelo aplicativo. Isso pode ser estático (usando `/.default` ) ou escopos dinâmicos. Isso pode incluir os escopos OIDC ( `openid` , `profile` , `email` ). |

Neste ponto, o Azure AD requer um administrador de locatários para entrar e concluir a solicitação. O administrador é solicitado a aprovar todas as permissões que você solicitou no `scope` parâmetro.  Se você usou um valor estático ( `/.default` ), ele funcionará como o ponto de extremidade de consentimento do administrador v 1.0 e solicitará consentimento para todos os escopos encontrados nas permissões necessárias (usuário e aplicativo). Para solicitar permissões de aplicativo, você deve usar o `/.default` valor. Se você não quiser que os administradores vejam uma determinada permissão na tela de consentimento do administrador o tempo todo ao usar `/.default` , a prática recomendada é não colocar a permissão na seção permissões necessárias. Em vez disso, você pode usar o consentimento dinâmico para adicionar as permissões que deseja que estejam na tela de consentimento em tempo de execução, em vez de usar `/.default` .

### <a name="successful-response"></a>Resposta bem-sucedida

Se o administrador aprovar as permissões para o seu aplicativo, a resposta bem-sucedida será:

```none
http://localhost/myapp/permissions
    ?admin_consent=True
    &tenant=fa00d692-e9c7-4460-a743-29f2956fd429
    &scope=https://graph.microsoft.com/Calendars.Read https://graph.microsoft.com/Mail.Send
    &state=12345
```

| Parâmetro | Descrição |
| :--- | :--- |
| `tenant`| O locatário do diretório que concedeu as permissões solicitadas, no formato de GUID.|
| `state` | Um valor incluído na solicitação também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. O estado é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página ou exibição em que ele estava.|
| `scope` | O conjunto de permissões ao qual foi concedido acesso para o aplicativo.|
| `admin_consent` | Será definido como `True`.|

### <a name="error-response"></a>Resposta de erro

```none
http://localhost/myapp/permissions
        ?admin_consent=True
        &tenant=fa15d692-e9c7-4460-a743-29f2956fd429
        &error=consent_required
        &error_description=AADSTS65004%3a+The+resource+owner+or+authorization+server+denied+the+request.%0d%0aTrace+ID%3a+d320620c-3d56-42bc-bc45-4cdd85c41f00%0d%0aCorrelation+ID%3a+8478d534-5b2c-4325-8c2c-51395c342c89%0d%0aTimestamp%3a+2019-09-24+18%3a34%3a26Z
        &state=12345
```

Adicionando aos parâmetros vistos em uma resposta bem-sucedida, os parâmetros de erro são vistos como abaixo.

| Parâmetro | Descrição |
|:-------------------|:-------------------------------------------------------------------------------------------------|
| `error` | Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros.|
| `error_description` | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro.|
| `tenant`| O locatário do diretório que concedeu as permissões solicitadas, no formato de GUID.|
| `state` | Um valor incluído na solicitação também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. O estado é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página ou exibição em que ele estava.|
| `admin_consent` | Será definido como `True` para indicar que essa resposta ocorreu em um fluxo de consentimento do administrador.|

## <a name="next-steps"></a>Próximas etapas
- Veja [como converter um aplicativo em multilocatário](howto-convert-app-to-be-multi-tenant.md)
- Saiba como [o consentimento tem suporte na camada de protocolo OAuth 2,0 durante o fluxo de concessão de código de autorização](v2-oauth2-auth-code-flow.md#request-an-authorization-code).
- Saiba [como um aplicativo multilocatário pode usar a estrutura de consentimento](./howto-convert-app-to-be-multi-tenant.md) para implementar o consentimento de "usuário" e "administrador", dando suporte a padrões de aplicativos de várias camadas mais avançados.
- Noções básicas sobre [experiências de consentimento de aplicativo do Azure ad](application-consent-experience.md)
