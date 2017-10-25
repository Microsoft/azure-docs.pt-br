---
title: "Escopos, permissões e consentimento do Azure Active Directory v2.0 | Microsoft Docs"
description: "Uma descrição da autorização no ponto de extremidade v2.0 do Azure AD, incluindo escopos, permissões e consentimento."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 8f98cbf0-a71d-4e34-babf-e644ad9ff423
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 04869a7627ecb3e6a0d11733fae7da2ecb04ed51
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="scopes-permissions-and-consent-in-the-azure-active-directory-v20-endpoint"></a>Escopos, permissões e consentimento no ponto de extremidade v2.0 do Azure Active Directory
Os aplicativos que se integram ao Azure AD (Azure Active Directory) seguem um modelo de autorização que fornece aos usuários controle sobre como um aplicativo pode acessar seus dados. A implementação v2.0 desse modelo de autorização foi atualizada e altera a maneira como um aplicativo deve interagir com o Azure AD. Este artigo aborda os conceitos básicos deste modelo de autorização, incluindo escopos, permissões e consentimento.

> [!NOTE]
> O ponto de extremidade v2.0 não dá suporte a todos os cenários e recursos do Azure Active Directory. Para determinar se você deve usar o ponto de extremidade v2.0, leia sobre as [limitações da v2.0](active-directory-v2-limitations.md).
>
>

## <a name="scopes-and-permissions"></a>Permissões e escopos
O Azure AD implementa o protocolo de autorização [OAuth 2.0](active-directory-v2-protocols.md). O OAuth 2.0 é um método pelo qual um aplicativo de terceiros pode acessar recursos hospedados na Web em nome do usuário. Qualquer recurso hospedado na Web que se integre ao Azure AD terá um identificador de recurso, ou *URI de ID de Aplicativo*. Por exemplo, alguns dos recursos hospedados na Web da Microsoft incluem:

* A API de Email Unificado do Office 365: `https://outlook.office.com`
* A Graph API do AD do Azure: `https://graph.windows.net`
* Microsoft Graph: `https://graph.microsoft.com`

Isso se aplica a todos os recursos de terceiros que se integraram ao Azure AD. Qualquer um desses recursos também pode definir um conjunto de permissões que pode ser usado para dividir a funcionalidade desse recurso em partes menores. Por exemplo, o [Microsoft Graph](https://graph.microsoft.io) definiu permissões para realizar as seguintes tarefas, entre outras:

* Ler o calendário de um usuário
* Escrever no calendário de um usuário
* Enviar email como um usuário

Definindo esses tipos de permissões, o recurso tem controle refinado sobre seus dados e como os dados são expostos. Um aplicativo de terceiros pode solicitar essas permissões de um usuário do aplicativo. O usuário do aplicativo deverá aprovar as permissões para que o aplicativo possa agir em nome do usuário. Ao dividir a funcionalidade do recurso em conjuntos menores de permissão, os aplicativos de terceiros podem ser criados para solicitar apenas as permissões específicas que eles precisam para realizar suas funções. Agora, os usuários do aplicativo saber exatamente como um aplicativo usará seus dados, de modo que eles se sentem mais seguros de que o aplicativo não está se comportando com más intenções.

No Azure AD e no OAuth, esses tipos de permissões são chamados de *escopos*. Eles também são chamados de *oAuth2Permissions*. Um escopo é representado no AD do Azure como um valor de cadeia de caracteres. Continuando com o exemplo do Microsoft Graph,o valor do escopo para cada permissão é:

* Ler o calendário de um usuário usando o `Calendar.Read`
* Escrever no calendário de um usuário usando o `Mail.ReadWrite`
* Enviar email como um usuário usando `Mail.Send`

Um aplicativo pode solicitar essas permissões especificando os escopos em solicitações para o ponto de extremidade v2.0.

## <a name="openid-connect-scopes"></a>Escopos do OpenID Connect
A implementação v2.0 do OpenID Connect tem alguns escopos bem definidos que não se aplicam a nenhum recurso específico: `openid`, `email`, `profile` e `offline_access`.

### <a name="openid"></a>openid
Se um aplicativo fizer conexão usando o [OpenID Connect](active-directory-v2-protocols.md), ele deverá solicitar o escopo `openid`. O escopo `openid` aparecerá na página de consentimento da conta corporativa como a permissão "Conectar você" e na página de consentimento da conta pessoal da Microsoft como a permissão "Exibir seu perfil e se conectar a aplicativos e serviços usando sua conta da Microsoft". Com essa permissão, um aplicativo pode receber um identificador exclusivo para o usuário na forma da declaração `sub`. Ele também fornece ao aplicativo acesso ao ponto de extremidade UserInfo. O escopo `openid` também pode ser usado no ponto de extremidade v2.0 para adquirir tokens de ID, que podem ser usados para proteger chamadas HTTP entre diferentes componentes de um aplicativo.

### <a name="email"></a>email
O escopo do `email` pode ser usado com o escopo do `openid` e com muitos outros. Ele concede ao aplicativo acesso ao endereço de email principal do usuário na forma da declaração `email` . A declaração `email` só será incluída nos tokens se um endereço de email estiver associado à conta de usuário, o que nem sempre é o caso. Se estiver usando o escopo de `email`, seu aplicativo deverá estar preparado para lidar com casos em que a declaração `email` não existe no token.

### <a name="profile"></a>Perfil
O escopo do `profile` pode ser usado com o escopo do `openid` e com muitos outros. Ele fornece o acesso de aplicativo a uma quantidade substancial de informações sobre o usuário. As informações que ele pode acessar incluem, mas sem limitação, o nome, sobrenome, nome de usuário preferido e ID de objeto. Para obter uma lista completa de declarações de perfil disponíveis no parâmetro id_tokens para um usuário específico, veja a [referência de tokens v2.0](active-directory-v2-tokens.md).

### <a name="offlineaccess"></a>offline_access
O [escopo do `offline_access`](http://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) concede ao seu aplicativo acesso a recursos em nome do usuário por um longo período. Na página de consentimento de conta corporativa, esse escopo aparecerá como a permissão "Acessar dados a qualquer momento". Na página de consentimento de conta pessoal da Microsoft, ele aparecerá como a permissão "Acessar dados a qualquer momento". Quando um usuário aprovar o escopo `offline_access`, seu aplicativo poderá receber tokens de atualização do ponto de extremidade do token v2.0. Os tokens de atualização têm uma vida longa. Seu aplicativo pode obter novos tokens de acesso quando os mais antigos expirarem.

Se o aplicativo não solicitar o escopo `offline_access`, ele não receberá tokens de atualização. Isso significa que, ao resgatar um código de autorização no [fluxo de código de autorização do OAuth 2.0](active-directory-v2-protocols.md), você só receberá de volta um token de acesso do ponto de extremidade `/token`. O token de acesso é válido por um curto período. Geralmente, o token de acesso expira em uma hora. Nesse ponto, seu aplicativo precisa redirecionar o usuário novamente para o ponto de extremidade `/authorize` para obter um novo código de autorização. Durante esse redirecionamento, dependendo do tipo de aplicativo, o usuário poderá ou não precisar inserir suas credenciais novamente ou consentir de novo as permissões.

Para saber mais sobre como obter e usar tokens de atualização, veja a [referência do protocolo v2.0](active-directory-v2-protocols.md).

## <a name="requesting-individual-user-consent"></a>Solicitando consentimento de usuário individual
Em uma solicitação de autorização do [OpenID Connect ou OAuth 2.0](active-directory-v2-protocols.md), um aplicativo pode solicitar as permissões de que precisa usando o parâmetro de consulta `scope`. Por exemplo, quando um usuário entra em um aplicativo, o aplicativo envia uma solicitação como o exemplo a seguir (com quebras de linha adicionadas para legibilidade):

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendar.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

O parâmetro `scope` é uma lista de escopos separados por espaço que o aplicativo está solicitando. Cada escopo é indicado acrescentando o valor de escopo para o identificador do recurso (URI da ID de Aplicativo). No exemplo de solicitação, o aplicativo precisa de permissão para ler o calendário e enviar emails como o usuário.

Depois que o usuário insere suas credenciais, o ponto de extremidade v2.0 verifica se há um registro correspondente do *consentimento de usuário*. Se o usuário não tiver consentido nenhuma das permissões solicitadas no passado, o ponto de extremidade v2.0 pedirá que o usuário as conceda.

![Consentimento de conta de trabalho](../../media/active-directory-v2-flows/work_account_consent.png)

Quando o usuário aprovar a permissão, o consentimento será registrado para que o usuário não tenha que consentir novamente em conexões subsequentes à conta.

## <a name="requesting-consent-for-an-entire-tenant"></a>Solicitando consentimento para um locatário inteiro
Geralmente, quando uma organização adquire uma assinatura para um aplicativo ou uma licença, a organização deseja configurar totalmente o aplicativo para seus funcionários. Como parte desse processo, um administrador pode autorizar que o aplicativo atue em nome de todos os funcionários. Se o administrador concede permissão para o locatário inteiro, os funcionários da empresa não verá uma página de consentimento para o aplicativo.

Para solicitar consentimento para todos os usuários em um locatário, seu aplicativo pode usar o ponto de extremidade de consentimento de administrador.

## <a name="admin-restricted-scopes"></a>Escopos restritos ao administrador
Algumas permissões de alto privilégio no ecossistema da Microsoft podem ser definidas como *restritas ao administrador*. Exemplos desses tipos de escopos incluem as seguintes permissões:

* Ler dados do diretório da organização usando `Directory.Read`
* Gravar dados no diretório da organização usando o `Directory.ReadWrite`
* Ler grupos de segurança no diretório da organização usando o `Groups.Read.All`

Embora um usuário consumidor possa conceder acesso de aplicativo para esse tipo de dados, os usuários organizacionais são impedidos de conceder acesso ao mesmo conjunto de dados confidenciais da empresa. Se seu aplicativo solicita acesso a uma dessas permissões de um usuário organizacional, o usuário receberá uma mensagem de erro que diz que não está autorizado a consentir com as permissões de seu aplicativo.

Se seu aplicativo requer acesso a escopos restritos a administradores para as organizações, você deve solicitá-los diretamente de um administrador da empresa também usando o ponto de extremidade de consentimento do administrador descrito a seguir.

Quando um administrador concede essas permissões pelo ponto de extremidade de consentimento do administrador, a permissão é concedida para todos os usuários no locatário.

## <a name="using-the-admin-consent-endpoint"></a>Usando o ponto de extremidade de consentimento do administrador
Se você seguir estas etapas, seu aplicativo poderá obter permissões para todos os usuários em um locatário, incluindo escopos restringidos pelo administrador. Para obter um exemplo de código que implementa as etapas, veja o [exemplo de escopos restritos ao administrador](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Solicitar as permissões no portal de registro do aplicativo
1. Vá para seu aplicativo no [Portal de Registro de Aplicativo](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) ou [crie um aplicativo](active-directory-v2-app-registration.md) se ainda não tiver feito isso.
2. Localize a seção **Permissões do Microsoft Graph** e adicione as permissões que seu aplicativo requer.
3. **Salve** o registro do aplicativo.

### <a name="recommended-sign-the-user-in-to-your-app"></a>Recomendado: conectar o usuário ao aplicativo
Normalmente, quando você cria um aplicativo que usa o ponto de extremidade de consentimento do administrador, o aplicativo precisa de uma página ou de um modo de exibição em que o administrador possa aprovar as permissões do aplicativo. Essa página pode ser parte do fluxo de inscrição no aplicativo, parte das configurações do aplicativo ou um fluxo dedicado de "conexão". Em muitos casos, faz sentido que o aplicativo somente mostre o modo de exibição "conectar" depois que o usuário entra com uma conta corporativa ou de estudante da Microsoft.

Quando o usuário entra em seu aplicativo, você pode identificar a organização à qual o administrador pertence antes de pedir a ele que aprove as permissões necessárias. Embora não seja estritamente necessário, isso pode ajudá-lo a criar uma experiência mais intuitiva para os usuários empresariais. Para conectar o usuário, siga nossos [tutoriais de protocolo v2.0](active-directory-v2-protocols.md).

### <a name="request-the-permissions-from-a-directory-admin"></a>Solicitar permissões de um administrador de diretório
Quando você estiver pronto para solicitar permissões de administrador da empresa, redirecione o usuário para o *ponto de extremidade de consentimento do administrador* v2.0.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro tip: Try pasting the below request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parâmetro | Condição | Descrição |
| --- | --- | --- |
| locatário |Obrigatório |O locatário do diretório para o qual você deseja solicitar permissão. Pode ser fornecido no formato de nome amigável ou de GUID. |
| client_id |Obrigatório |A ID de aplicativo do [Portal de Registro de Aplicativo](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) atribuída ao seu aplicativo. |
| redirect_uri |Obrigatório |O URI de redirecionamento onde você deseja que a resposta seja enviada para ser tratada pelo aplicativo. Ela deve corresponder exatamente a um redirecionamento de URIs que você registrou no portal de registro de aplicativo. |
| state |Recomendadas |Um valor incluído na solicitação também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. Use o estado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página ou exibição em que ele estava. |

Neste ponto, o Azure AD requer um administrador de locatários para entrar e concluir a solicitação. O administrador deverá aprovar todas as permissões que você solicitou para o aplicativo no portal de registro de aplicativos.

#### <a name="successful-response"></a>Resposta bem-sucedida
Se o administrador aprovar as permissões para o seu aplicativo, a resposta bem-sucedida será:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parâmetro | Descrição |
| --- | --- | --- |
| locatário |O locatário do diretório que concedeu as permissões solicitadas, no formato de GUID. |
| state |Um valor incluído na solicitação também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. O estado é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página ou exibição em que ele estava. |
| admin_consent |Será definido como **true**. |

#### <a name="error-response"></a>Resposta de erro
Se o administrador não aprovar as permissões para o seu aplicativo, a resposta de falha será:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parâmetro | Descrição |
| --- | --- | --- |
| error |Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| error_description |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro. |

Depois de receber uma resposta bem-sucedida do ponto de extremidade de consentimento do administrador, o aplicativo terá as permissões solicitadas por ele. Em seguida, você pode solicitar um token para o recurso desejado.

## <a name="using-permissions"></a>Usando permissões
Depois que o usuário consente permissões para o aplicativo, este pode adquirir tokens de acesso que representam a permissão do seu aplicativo para acessar um recurso em alguma capacidade. Um token de acesso só pode ser usado para um único recurso, mas codificada dentro do token de acesso estará cada permissão que o aplicativo recebeu para esse recurso. Para adquirir um token de acesso, o aplicativo poderá fazer uma solicitação ao ponto de extremidade do token v2.0 como esta:

```
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

Você pode usar o token de acesso resultante em solicitações HTTP para o recurso. Ele confiável indica ao recurso que seu aplicativo tem a permissão apropriada para executar uma tarefa específica.  

Para saber mais sobre o protocolo OAuth 2.0 e como obter tokens de acesso, consulte a [referência do protocolo do ponto de extremidade v2.0](active-directory-v2-protocols.md).
