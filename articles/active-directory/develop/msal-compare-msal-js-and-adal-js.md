---
title: Diferenças entre a MSAL.js e a ADAL.js | Azure
description: Saiba mais sobre as diferenças entre a Biblioteca de Autenticação da Microsoft para JavaScript (MSAL.js) e a Biblioteca de Autenticação do Microsoft Azure AD para JavaScript (ADAL.js) e como escolher qual usar.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 328db116cebda5eb288f04cc89e2c85550c38083
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68421196"
---
# <a name="differences-between-msal-js-and-adal-js"></a>Diferenças entre a MSAL para JS e a ADAL para JS

Tanto a Biblioteca de Autenticação da Microsoft para JavaScript (MSAL.js) e a Biblioteca de Autenticação do Active Directory para JavaScript (ADAL.js) são usadas para autenticar as entidades do Azure AD e solicitar tokens do Azure AD. Até agora, a maioria dos desenvolvedores trabalharam com o Azure AD para desenvolvedores (v1.0) para autenticar identidades do Azure AD (contas corporativas e de estudante), solicitando tokens usando a ADAL. Agora, usando a MSAL.js, você pode autenticar um conjunto mais amplo de identidades da Microsoft (identidades do Azure AD e contas da Microsoft, além de contas sociais e locais por meio do Azure AD B2C) através do ponto de extremidade da plataforma de identidade da Microsoft (v2.0).

Este artigo descreve como escolher entre a Biblioteca de Autenticação da Microsoft para JavaScript (MSAL.js) e a Biblioteca de Autenticação do Microsoft Azure AD para JavaScript (ADAL.js) e comparar as duas bibliotecas.

## <a name="choosing-between-adaljs-and-msaljs"></a>Escolher entre a ADAL.js e a MSAL.js

Na maioria dos casos, convém usar a plataforma de identidade da Microsoft e a MSAL.js, que é a geração mais recente das bibliotecas de autenticação da Microsoft. Ao usar a MSAL.js, você adquire tokens para que os usuários entrem em seu aplicativo com o Azure AD (contas corporativas e de estudante), contas da Microsoft (MSA) (pessoais) ou o Azure AD B2C.

Se você já estiver familiarizado com o ponto de extremidade v1.0 (e ADAL.js), talvez queira ler [O que há de diferente no ponto de extremidade v2.0?](active-directory-v2-compare.md).

No entanto, você ainda precisará usar a ADAL.js se seu aplicativo precisar conectar os usuários com versões anteriores dos [Serviços de Federação do Active Directory (AD FS)](/windows-server/identity/active-directory-federation-services).

## <a name="key-differences-in-authentication-with-msaljs"></a>Principais diferenças na autenticação com MSAL.js

### <a name="core-api"></a>API principal

* A ADAL.js usa [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext) como representação de uma instância de sua conexão com o servidor de autorização ou provedor de identidade por meio da URL de uma autoridade. Inversamente, a API da MSAL.js é projetada em torno do aplicativo cliente do agente do usuário (uma forma de aplicativo cliente público em que o código do cliente é executado em um agente do usuário, como um navegador da Web). Ela fornece a classe `UserAgentApplication` para representar uma instância do contexto de autenticação do aplicativo com o servidor de autorização. Para obter mais detalhes, confira [Inicializar usando a MSAL.js](msal-js-initializing-client-applications.md).

* Na ADAL.js, os métodos para adquirir tokens são associados a uma autoridade única definida no `AuthenticationContext`. Na MSAL.js, as solicitações de token de aquisição podem levar a valores diferentes de autoridade que aquele definido no `UserAgentApplication`. Isso permite que a MSAL.js adquira e armazene tokens em cache separadamente para vários locatários e contas de usuário no mesmo aplicativo.

* Na ADAL.js, o método para adquirir e renovar tokens silenciosamente sem avisar os usuários é chamado de `acquireToken`. Na MSAL.js, esse método chama-se `acquireTokenSilent` para descrever melhor essa funcionalidade.

### <a name="authority-value-common"></a>Valor da autoridade `common`

Na v1.0, ao usar a autoridade `https://login.microsoftonline.com/common`, você permitirá que os usuários entrem com qualquer conta do Azure AD (para qualquer organização).

Na v2.0, ao usar a autoridade `https://login.microsoftonline.com/common`, você permitirá que os usuários entrem com uma conta da organização do Azure AD ou com uma conta pessoal da Microsoft (MSA). Para restringir a entrada apenas a contas do Azure AD (mesmo comportamento com a ADAL.js), será preciso usar `https://login.microsoftonline.com/organizations`. Para ver detalhes, confira a opção de configuração `authority` em [Inicializar usando a MSAL.js](msal-js-initializing-client-applications.md).

### <a name="scopes-for-acquiring-tokens"></a>Escopos para adquirir tokens
* Escopo em vez do parâmetro de recursos em solicitações de autenticação para adquirir tokens

    O protocolo v2.0 usa escopos em vez de recursos nas solicitações. Ou seja, quando seu aplicativo precisa solicitar tokens com permissões para um recurso, como o MS Graph, a diferença nos valores passados para os métodos de biblioteca é a seguinte:

    v1.0: recurso = https\://graph.microsoft.com

    v2.0: escopo = https\://graph.microsoft.com/User.Read

    Você pode solicitar escopos para qualquer recurso de API, usando o URI da API neste formato: appidURI/scope Por exemplo: https:\//mytenant.onmicrosoft.com/myapi/api.read

    Apenas para a API do MS Graph, um valor de escopo `user.read` é mapeado para https://graph.microsoft.com/User.Read e pode ser usado de maneira intercambiável.

    ```javascript
    var request = {
        scopes = ["User.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Escopos dinâmicos de consentimento incremental.

    Quando você criava aplicativos com a v1.0, precisava registrar o conjunto completo de permissões (escopos estáticos) exigidas pelo aplicativo para que o usuário consentisse durante o momento do logon. Na v2.0, você pode usar o parâmetro de escopo para solicitar as permissões no momento em que desejá-las. Eles são chamados de escopos dinâmicos. Esses escopos permitem que o usuário forneça consentimento incremental aos escopos. Portanto, se no início você só quiser que o usuário entre em seu aplicativo e não precise de nenhum tipo de acesso, poderá fazê-lo. Se, posteriormente, você precisar que o usuário consiga ler o calendário, solicite o escopo do calendário nos métodos de token de aquisição e obtenha o consentimento do usuário. Por exemplo:

    ```javascript
    var request = {
        scopes = ["https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Calendar.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Escopos para APIs V1.0

    Quando obter tokens para APIs V1.0, usando a MSAL.js, você pode solicitar todos os escopos estáticos registrados na API, anexando `.default` ao URI da ID do aplicativo da API como escopo. Por exemplo:

    ```javascript
    var request = {
        scopes = [ appidURI + "/.default"];
    };

    acquireTokenPopup(request);
    ```

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações, confira a [comparação entre a v1.0 e a v2.0](active-directory-v2-compare.md).
