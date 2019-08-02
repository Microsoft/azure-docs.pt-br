---
title: Gerenciar tokens (Biblioteca de Autenticação da Microsoft) | Azure
description: Saiba mais sobre a aquisição e o armazenamento em cache de tokens usando a MSAL (Biblioteca de Autenticação da Microsoft).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ca011ec7185b084de6d1d346556c1c270c7aee3
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546059"
---
# <a name="acquiring-and-caching-tokens-using-msal"></a>Adquirir e armazenar tokens em cache usando a MSAL
Os [tokens de acesso](access-tokens.md) permitem que os clientes chamem com segurança as APIs Web protegidas pelo Azure. Há muitas maneiras de adquirir um token usando a MSAL (Biblioteca de Autenticação da Microsoft). Algumas formas exigem interações do usuário por meio de um navegador da Web. Algumas não exigem interações do usuário. Geralmente, a maneira de adquirir um token dependerá se o aplicativo for um aplicativo cliente público (área de trabalho ou aplicativo móvel) ou um aplicativo cliente confidencial (Aplicativo Web, API da Web ou aplicativo daemon, como um serviço do Windows).

A MSAL armazena um token em cache após sua aquisição.  Primeiro, o código do aplicativo deve tentar obter um token silenciosamente (a partir do cache), antes de adquirir um token por outros meios.

Também é possível limpar o cache do token removendo as contas do cache. No entanto, isso não remove o cookie de sessão que está no navegador.

## <a name="scopes-when-acquiring-tokens"></a>Escopos ao adquirir tokens
Os [escopos](v2-permissions-and-consent.md) são as permissões que uma API da Web expõe para que os aplicativos cliente solicitem acesso. Os aplicativos cliente solicitam o consentimento do usuário para esses escopos quando fazem solicitações de autenticação para obter tokens para acessar as APIs da Web. A MSAL permite que você obtenha tokens para acessar o Azure AD para desenvolvedores (v1.0) e APIs da plataforma de identidade da Microsoft (v2.0). O protocolo v2.0 usa escopos em vez de recursos nas solicitações. Confira mais informações na [comparação entre a v1.0 e a v2.0](active-directory-v2-compare.md). Com base na configuração da API Web da versão do token que ela aceita, o ponto de extremidade da v2.0 retorna o token de acesso à MSAL.

Vários métodos de token de aquisição da MSAL exigem um parâmetro de *escopos*. Esse parâmetro é uma lista simples de cadeias de caracteres que declaram as permissões e os recursos desejados que são solicitados. As [Permissões do Microsoft Graph](/graph/permissions-reference) são escopos conhecidos.

Na MSAL, também é possível acessar recursos da versão 1.0. Veja mais informações em [Escopos de um aplicativo v1.0](msal-v1-app-scopes.md).

### <a name="request-specific-scopes-for-a-web-api"></a>Solicitar escopos específicos para uma API Web
Quando seu aplicativo precisar solicitar tokens com permissões específicas para uma API de recursos, transmita os escopos que contêm o URI da ID do aplicativo da API no formato abaixo: *&lt;escopo do&gt;/&lt;URI da ID do aplicativo&gt;*

Por exemplo, escopos da API do Microsoft Graph: `https://graph.microsoft.com/User.Read`

Ou, por exemplo, escopos da API Web personalizada: `api://abscdefgh-1234-abcd-efgh-1234567890/api.read`

No caso da API do Microsoft Graph, um valor de escopo `user.read` é mapeado para o formato `https://graph.microsoft.com/User.Read` e pode ser usado de maneira intercambiável.

> [!NOTE]
> Determinadas APIs Web, como a API do Azure Resource Manager (https://management.core.windows.net/) ) exigem uma “/” na declaração do público-alvo (aud) do token de acesso. Nesse caso, é importante transmitir o escopo como https://management.core.windows.net//user_impersonation (observe as barras duplas) para que o token seja válido na API.

### <a name="request-dynamic-scopes-for-incremental-consent"></a>Solicitar escopos dinâmicos de consentimento incremental
Quando você criava aplicativos com a v1.0, precisava registrar o conjunto completo de permissões (escopos estáticos) exigido pelo aplicativo para que o usuário consentisse durante o login. Na v2.0, você pode solicitar permissões adicionais conforme necessário usando o parâmetro do escopo. Eles são chamados escopos dinâmicos e permitem que o usuário forneça consentimento incremental aos escopos.

Por exemplo, inicialmente você pode conectar o usuário e negar a ele qualquer tipo de acesso. Posteriormente, pode oferecer a ele permissão para ler o calendário do usuário solicitando o escopo do calendário nos métodos de token de aquisição e obter o consentimento do usuário.

Por exemplo: `https://graph.microsoft.com/User.Read` e `https://graph.microsoft.com/Calendar.Read`

## <a name="acquiring-tokens-silently-from-the-cache"></a>Adquirir tokens silenciosamente (do cache)
A MSAL mantém um cache de tokens (ou dois caches para aplicativos cliente confidenciais) e armazena um token em cache após sua aquisição.  Em muitos casos, a tentativa de obter um token silenciosamente adquirirá outro token com mais escopos tendo como base um token armazenado no cache. Além disso, a MSAL também pode atualizar um token quando a expiração estiver próxima (pois o cache de tokens também contém um token de atualização).

### <a name="recommended-call-pattern-for-public-client-applications"></a>Padrão de chamada recomendado para aplicativos cliente públicos
Primeiro, o código do aplicativo deve tentar obter um token silenciosamente (a partir do cache).  Se a chamada do método retornar um erro ou exceção "IU necessária", tente adquirir um token por outros meios. 

No entanto, existem dois fluxos antes dos quais você **não deve** tentar adquirir um token silenciosamente:

- [fluxo de credenciais de cliente](msal-authentication-flows.md#client-credentials), que não usa o cache de tokens do usuário, mas usa um cache de tokens do aplicativo. Esse método se encarrega de verificar o cache de tokens do aplicativo antes de enviar uma solicitação ao STS.
- [fluxo de código de autorização](msal-authentication-flows.md#authorization-code) em aplicativos Web já que ele resgata o código recebido pelo aplicativo quando ele realizou a conexão do usuário e deu consentimento para mais escopos. Como um código é passado como parâmetro, e não uma conta, o método não pode procurar no cache antes de resgatar o código, o que requer, de qualquer maneira, uma chamada para o serviço.

### <a name="recommended-call-pattern-in-web-apps-using-the-authorization-code-flow"></a>Padrão de chamada recomendado em aplicativos Web usando o fluxo do Código de Autorização 
Para aplicativos Web que usam o [fluxo de código de autorização do OpenID Connect](v2-protocols-oidc.md), o padrão recomendado nos controladores é:

- Instancie um aplicativo cliente confidencial com um cache de tokens com serialização personalizada. 
- Adquira o token usando o fluxo do código de autorização

## <a name="acquiring-tokens"></a>Adquirir tokens
Em geral, o método de aquisição de um token variará se ele for um aplicativo cliente público ou confidencial.

### <a name="public-client-applications"></a>Aplicativos cliente públicos
Para aplicativos cliente públicos (área de trabalho ou aplicativo móvel), você:
- Geralmente adquire tokens interativamente fazendo com que o usuário faça conexão pela interface do usuário ou por uma janela pop-up.
- Pode [obter um token silenciosamente para o usuário conectado](msal-authentication-flows.md#integrated-windows-authentication) usando a Autenticação Integrada do Windows (IWA/Kerberos) se o aplicativo da área de trabalho estiver sendo executado em um computador Windows associado a um domínio ou ao Azure.
- Pode [obter um token com um nome de usuário e senha](msal-authentication-flows.md#usernamepassword) em aplicativos cliente de área de trabalho do .NET Framework, mas isso não é recomendado. Não use o nome de usuário e senha em aplicativos cliente confidenciais.
- Pode adquirir um token através do [fluxo de código do dispositivo](msal-authentication-flows.md#device-code) em aplicativos executados em dispositivos que não têm um navegador da Web. O usuário recebe uma URL e um código. Depois, ele acessa um navegador da Web em outro dispositivo, insere o código e entra.  Em seguida, o Azure AD envia um token de volta ao dispositivo sem navegador.

### <a name="confidential-client-applications"></a>Aplicativos cliente confidenciais 
Para aplicativos cliente confidenciais (aplicativo Web, API Web ou aplicativo daemon como um serviço do Windows), você:
- Adquire tokens **para o próprio aplicativo** e não para um usuário utilizando o [fluxo de credenciais de cliente](msal-authentication-flows.md#client-credentials). Isso pode ser usado para ferramentas de sincronização ou ferramentas que processam usuários em geral e não um usuário específico. 
- Use o [fluxo On-behalf-of](msal-authentication-flows.md#on-behalf-of) para uma API Web para chamar uma API em nome do usuário. O aplicativo é identificado com as credenciais do cliente para adquirir um token com base na declaração de um usuário (SAML, por exemplo, ou um token JWT). Esse fluxo é usado por aplicativos que precisam acessar recursos de um usuário específico em chamadas de serviço a serviço.
- Adquira tokens usando o [fluxo de código de autorização](msal-authentication-flows.md#authorization-code) em aplicativos Web depois que o usuário entrar usando a URL de solicitação de autorização. O aplicativo do OpenID Connect geralmente usa esse mecanismo que permite ao usuário entrar usando o Open ID Connect e acessar as APIs Web em nome do usuário.


## <a name="authentication-results"></a>Resultados da autenticação 
Quando seu cliente solicita um token de acesso, o Azure AD também retorna um resultado da autenticação que inclui alguns metadados sobre o token de acesso. Essas informações incluem a data de expiração do token de acesso e os escopos para os quais ele é válido. Esses dados permitem ao aplicativo realizar o cache inteligente dos tokens de acesso sem precisar analisar o token de acesso em si.  O resultado da autenticação expõe:

- O [token de acesso](access-tokens.md) da API Web para acessar os recursos. O token é uma cadeia de caracteres, geralmente um JWT codificado em base64. Porém, o cliente não deve explorar o token de acesso. Não há garantias que o formato permanecerá estável e ele pode ser criptografado para o recurso. A gravação de código que depende do conteúdo do token de acesso no cliente é uma das maiores origens de erros e quebras de lógica do cliente.
- A [ID do token](id-tokens.md) para o usuário (esse é um JWT).
- A expiração do token, que informa a data/hora da expiração do token.
- A ID do locatário contém o locatário no qual o usuário foi encontrado. Para usuários convidados (cenários B2B do Azure AD), a ID do locatário é o locatário convidado e não o locatário exclusivo. Quando o token é entregue em nome de um usuário, o resultado da autenticação também contém informações sobre esse usuário. Para fluxos de clientes confidenciais em que os tokens são solicitados sem nenhum usuário (para o aplicativo), essas informações sobre o usuário são nulas.
- Os escopos para os quais o token foi emitido.
- A ID exclusiva para o usuário.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre o [tratamento de erros e exceções](msal-handling-exceptions.md). 
