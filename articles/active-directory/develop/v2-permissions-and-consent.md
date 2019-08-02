---
title: Escopos de plataforma do Microsoft identity, permissões e consentimento | Microsoft Docs
description: Uma descrição de autorização no Microsoft identity platform ponto de extremidade, incluindo escopos, permissões e consentimento.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 8f98cbf0-a71d-4e34-babf-e644ad9ff423
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: hirsin, jesakowi, jmprieur
ms.custom: fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 032cc0edaa140d82124a7369232cb82bf6c00c10
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67702701"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Permissões e consentimento no ponto de extremidade de plataforma de identidade Microsoft

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Os aplicativos que se integram à plataforma de identidade da Microsoft seguem um modelo de autorização que dá aos usuários e administradores controle sobre como os dados podem ser acessados. A implementação do modelo de autorização foi atualizada no ponto de extremidade de plataforma de identidade Microsoft e ele é alterado como um aplicativo deve interagir com a plataforma de identidade da Microsoft. Este artigo aborda os conceitos básicos deste modelo de autorização, incluindo escopos, permissões e consentimento.

> [!NOTE]
> O ponto de extremidade de plataforma de identidade do Microsoft não oferece suporte a todos os cenários e recursos. Para determinar se deve usar o ponto de extremidade de plataforma do Microsoft identity, leia sobre [limitações da plataforma Microsoft identity](active-directory-v2-limitations.md).

## <a name="scopes-and-permissions"></a>Permissões e escopos

A plataforma de identidade da Microsoft implementa o protocolo de autorização [OAuth 2.0](active-directory-v2-protocols.md). O OAuth 2.0 é um método pelo qual um aplicativo de terceiros pode acessar recursos hospedados na Web em nome do usuário. Qualquer recurso hospedado na Web que se integre à plataforma de identidade da Microsoft tem um identificador de recurso ou *URI de ID de Aplicativo*. Por exemplo, alguns dos recursos hospedados na Web da Microsoft incluem:

* Microsoft Graph: `https://graph.microsoft.com`
* API de Email do Office 365: `https://outlook.office.com`
* Azure AD Graph: `https://graph.windows.net`

> [!NOTE]
> Recomendamos que você use o Microsoft Graph em vez do Azure AD Graph, API de Email do Office 365 e outros recursos.

Isso se aplica a todos os recursos de terceiros que se integraram à plataforma de identidade da Microsoft. Qualquer um desses recursos também pode definir um conjunto de permissões que pode ser usado para dividir a funcionalidade desse recurso em partes menores. Por exemplo, o [Microsoft Graph](https://graph.microsoft.com) definiu permissões para realizar as seguintes tarefas, entre outras:

* Ler o calendário de um usuário
* Escrever no calendário de um usuário
* Enviar email como um usuário

Ao definir esses tipos de permissões, o recurso tem controle refinado sobre seus dados e como a funcionalidade de API é exposta. Um aplicativo de terceiros pode solicitar essas permissões de usuários e administradores que devem aprovar a solicitação antes que o aplicativo possa acessar dados ou agir em nome do usuário. Ao dividir a funcionalidade do recurso em conjuntos menores de permissão, os aplicativos de terceiros podem ser criados para solicitar apenas as permissões específicas que eles precisam para realizar suas funções. Os usuários e administradores podem saber exatamente quais dados o aplicativo tem acesso ao e podem ser mais confiantes de que ele não está se comportando com más intenções. Os desenvolvedores devem sempre obedecer o conceito de privilégios mínimos, solicitando apenas as permissões necessárias para que seus aplicativos funcionem.

No OAuth 2.0, esses tipos de permissão são chamados de *escopos*. Eles são também conhecidos como *permissões*. Uma permissão é representada na plataforma de identidade da Microsoft como um valor de cadeia de caracteres. Continuando com o exemplo do Microsoft Graph,o valor da cadeia de caracteres para cada permissão é:

* Ler o calendário de um usuário usando o `Calendars.Read`
* Escrever no calendário de um usuário usando o `Calendars.ReadWrite`
* Enviar email como um usuário usando `Mail.Send`

Um aplicativo mais comumente solicita essas permissões especificando os escopos em solicitações para a plataforma de identidade Microsoft autorizam o ponto de extremidade. No entanto, determinadas permissões com privilégios elevados podem apenas ser concedidos por meio do consentimento do administrador e solicitado/concedidas usando o [ponto de extremidade de consentimento de administrador](v2-permissions-and-consent.md#admin-restricted-permissions). Continue lendo para saber mais.

## <a name="permission-types"></a>Tipos de permissão

A plataforma de identidade da Microsoft dá suporte a dois tipos de permissões: **permissões delegadas** e **permissões de aplicativo**.

* **Permissões delegadas** são usadas por aplicativos que têm um usuário conectado presente. Para esses aplicativos, o usuário ou administrador concede sua permissão para as permissões que as solicitações de aplicativo e o aplicativo é uma permissão delegada para atuar como o usuário conectado ao fazer chamadas para o recurso de destino. Algumas permissões delegadas podem ser concedidas por usuários não administrativos, mas algumas permissões de privilégios mais altos exigem o [consentimento do administrador](v2-permissions-and-consent.md#admin-restricted-permissions). Para saber quais funções de administrador podem consentir as permissões delegadas, consulte [Permissões da função de administrador no Microsoft Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

* **Permissões de aplicativo** são usadas por aplicativos executados sem um usuário conectado presente, por exemplo, aplicativos executados como serviços em segundo plano ou daemons.  As permissões de aplicativo só podem ser [concedidas pelo administrador](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

_Permissões efetivas_ são as permissões que seu aplicativo terá ao fazer solicitações para o recurso de destino. É importante compreender a diferença entre o delegado e permissões de aplicativo que recebe seu aplicativo e suas permissões efetivas ao fazer chamadas para o recurso de destino.

- Para obter permissões delegadas, as _permissões efetivas_ do aplicativo serão a interseção menos privilegiada das permissões delegadas que tiverem recebido o aplicativo (por meio de consentimento) e os privilégios do usuário conectado no momento. Seu aplicativo não pode ter mais privilégios que o usuário conectado. Dentro das organizações, os privilégios do usuário conectado podem ser determinados pela política ou por associação em uma ou mais funções de administrador. Para saber quais funções de administrador podem consentir as permissões delegadas, consulte [Permissões da função de administrador no Microsoft Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

   Por exemplo, suponha que seu aplicativo tenha recebido a permissão delegada _User.ReadWrite.All_. Essa permissão concede uma permissão ao seu aplicativo para ler e atualizar o perfil de todos os usuários em uma organização. Se o usuário conectado for um administrador global, seu aplicativo poderá atualizar o perfil de todos os usuários na organização. No entanto, se o usuário conectado não está em uma função de administrador, seu aplicativo poderá atualizar apenas o perfil do usuário conectado. Não poderá atualizar os perfis de outros usuários na organização porque o que ele tem permissão para agir em nome de usuário não tem os privilégios.
  
- Para permissões de aplicativo, as _permissões efetivas_ do aplicativo serão o nível completo de privilégios indicado pela permissão. Por exemplo, um aplicativo que tenha a permissão de aplicativo _User.ReadWrite.All_ poderá atualizar o perfil de todos os usuários na organização. 

## <a name="openid-connect-scopes"></a>Escopos do OpenID Connect

A implementação de plataforma de identidade da Microsoft do OpenID Connect tem alguns escopos bem definidos que não se aplicam a um recurso específico: `openid`, `email`, `profile`, e `offline_access`. Os escopos `address` e `phone` do OpenID Connect não são compatíveis.

### <a name="openid"></a>openid

Se um aplicativo fizer conexão usando o [OpenID Connect](active-directory-v2-protocols.md), ele deverá solicitar o escopo `openid`. O escopo `openid` aparecerá na página de consentimento da conta corporativa como a permissão "Conectar você" e na página de consentimento da conta pessoal da Microsoft como a permissão "Exibir seu perfil e se conectar a aplicativos e serviços usando sua conta da Microsoft". Com essa permissão, um aplicativo pode receber um identificador exclusivo para o usuário na forma da declaração `sub`. Ele também fornece ao aplicativo acesso ao ponto de extremidade UserInfo. O `openid` escopo pode ser usado no Microsoft identity platform token ponto de extremidade para adquirir tokens de ID, que podem ser usados pelo aplicativo para autenticação.

### <a name="email"></a>email

O escopo do `email` pode ser usado com o escopo do `openid` e com muitos outros. Ele concede ao aplicativo acesso ao endereço de email principal do usuário na forma da declaração `email` . O `email` declaração está incluída em um token somente se um endereço de email está associado com a conta de usuário, o que nem sempre é o caso. Se estiver usando o escopo de `email`, seu aplicativo deverá estar preparado para lidar com casos em que a declaração `email` não existe no token.

### <a name="profile"></a>profile

O escopo do `profile` pode ser usado com o escopo do `openid` e com muitos outros. Ele fornece o acesso de aplicativo a uma quantidade substancial de informações sobre o usuário. As informações que ele possa acessar inclui, mas não está limitadas ao nome fornecido do usuário, sobrenome, nome de usuário preferido e ID de objeto. Para obter uma lista completa de declarações de perfil disponíveis no parâmetro id_tokens para um usuário específico, confira a [`id_tokens`referência](id-tokens.md).

### <a name="offlineaccess"></a>offline_access

O [escopo do `offline_access`](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) concede ao seu aplicativo acesso a recursos em nome do usuário por um longo período. Na página de consentimento, esse escopo aparece como a permissão "Manter o acesso aos dados para os quais recebeu acesso". Quando um usuário aprovar a `offline_access` escopo, seu aplicativo pode receber tokens de atualização do Microsoft identity platform ponto de extremidade token. Os tokens de atualização têm uma vida longa. Seu aplicativo pode obter novos tokens de acesso quando os mais antigos expirarem.

Se o aplicativo não solicitar explicitamente o escopo `offline_access`, ele não receberá tokens de atualização. Isso significa que, ao resgatar um código de autorização no [fluxo de código de autorização do OAuth 2.0](active-directory-v2-protocols.md), você só receberá de volta um token de acesso do ponto de extremidade `/token`. O token de acesso é válido por um curto período. Geralmente, o token de acesso expira em uma hora. Nesse ponto, seu aplicativo precisa redirecionar o usuário novamente para o ponto de extremidade `/authorize` para obter um novo código de autorização. Durante esse redirecionamento, dependendo do tipo de aplicativo, o usuário poderá ou não precisar inserir suas credenciais novamente ou consentir de novo as permissões. Enquanto o `offline_access` escopo é automaticamente solicitado pelo servidor, o cliente ainda deve solicitá-la para receber os tokens de atualização.

Para obter mais informações sobre como obter e usar tokens de atualização, consulte o [referência de protocolo de plataforma de identidade Microsoft](active-directory-v2-protocols.md).

## <a name="requesting-individual-user-consent"></a>Solicitando consentimento de usuário individual

Em uma solicitação de autorização do [OpenID Connect ou OAuth 2.0](active-directory-v2-protocols.md), um aplicativo pode solicitar as permissões de que precisa usando o parâmetro de consulta `scope`. Por exemplo, quando um usuário entra em um aplicativo, o aplicativo envia uma solicitação como o exemplo a seguir (com quebras de linha adicionadas para legibilidade):

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendars.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

O parâmetro `scope` é uma lista de permissões delegadas separadas por espaço que o aplicativo está solicitando. Cada permissão é indicada acrescentando o valor da permissão ao identificador do recurso (URI da ID de Aplicativo). No exemplo de solicitação, o aplicativo precisa de permissão para ler o calendário e enviar emails como o usuário.

Depois do usuário insere suas credenciais, o ponto de extremidade de plataforma de identidade do Microsoft verifica se há um registro correspondente do *consentimento do usuário*. Se o usuário não tiver consentido nenhuma das permissões solicitadas no passado, nem um administrador consentiu essas permissões em nome de toda a organização, o ponto de extremidade de plataforma de identidade do Microsoft pede ao usuário para conceder as permissões solicitadas.

> [!NOTE]
> Nesse momento, as permissões `offline_access` ("Manter o acesso aos dados para os quais recebeu acesso") e `user.read` ("Entrar e ler o seu perfil") são incluídas automaticamente no consentimento inicial para um aplicativo.  Essas permissões geralmente são necessárias para a funcionalidade adequada do aplicativo: `offline_access` dá ao aplicativo acesso aos tokens de atualização, essenciais para aplicativos Web e nativos, enquanto `user.read` dá acesso à declaração `sub`, permitindo que o cliente ou aplicativo identifique corretamente o usuário ao longo do tempo e acesse informações básicas do usuário.  

![Captura de tela de exemplo que mostra que o consentimento de conta de trabalho](./media/v2-permissions-and-consent/work_account_consent.png)

Quando o usuário aprovar a solicitação de permissão, o consentimento será registrado para que o usuário não tenha que consentir novamente a cada entrada no aplicativo.

## <a name="requesting-consent-for-an-entire-tenant"></a>Solicitando consentimento para um locatário inteiro

Muitas vezes, quando uma organização adquire uma licença ou assinatura para um aplicativo, a organização deseja configurar proativamente o aplicativo para que ele seja usado por todos os membros. Como parte desse processo, um administrador pode autorizar que o aplicativo atue em nome de todos os usuários no locatário. Se o administrador conceder permissão para todo o locatário, os funcionários da empresa não virão uma página de consentimento para o aplicativo.

Para solicitar consentimento de permissões delegadas para todos os usuários em um locatário, o aplicativo pode usar o ponto de extremidade de consentimento de administrador.

Além disso, os aplicativos devem usar o ponto de extremidade de consentimento do administrador para solicitar permissões de aplicativo.

## <a name="admin-restricted-permissions"></a>Permissões restringidas pelo administrador

Algumas permissões de alto privilégio no ecossistema da Microsoft podem ser definidas como *restritas ao administrador*. Exemplos desses tipos de permissão incluem as seguintes:

* Ler os perfis completos de todos os usuários usando `User.Read.All`
* Gravar dados no diretório da organização usando o `Directory.ReadWrite.All`
* Ler todos os grupos no diretório da organização usando `Groups.Read.All`

Embora um usuário consumidor possa conceder acesso de aplicativo para esse tipo de dados, os usuários organizacionais são impedidos de conceder acesso ao mesmo conjunto de dados confidenciais da empresa. Se seu aplicativo solicita acesso a uma dessas permissões de um usuário organizacional, o usuário recebe uma mensagem de erro que diz que ele não está autorizado a consentir com as permissões do seu aplicativo.

Se seu aplicativo requer acesso a escopos restritos a administradores para as organizações, você deve solicitá-los diretamente de um administrador da empresa também usando o ponto de extremidade de consentimento do administrador descrito a seguir.

Se o aplicativo está solicitando permissões delegadas de privilégio alto e um administrador concede essas permissões pelo ponto de extremidade de consentimento do administrador, a permissão é concedida para todos os usuários no locatário.

Se o aplicativo está solicitando permissões de aplicativo e um administrador concede que essas permissões por meio do administrador de ponto de extremidade de consentimento, essa concessão não for feito em nome de qualquer usuário específico. Na verdade, o aplicativo cliente recebe as permissões *diretamente*. Esses tipos de permissões só são usados pelos serviços de daemon e outros aplicativos não interativos que são executados em segundo plano.

## <a name="using-the-admin-consent-endpoint"></a>Usando o ponto de extremidade de consentimento do administrador

Quando um administrador da empresa usa seu aplicativo e é direcionado para o ponto de extremidade autorizado, a plataforma de identidade da Microsoft detecta a função do usuário e pergunta se ele gostaria de conceder as permissões solicitadas por você em nome de todo o locatário. No entanto, há também um ponto de extremidade de consentimento de administrador dedicado que você pode usar se quiser solicitar proativamente que um administrador conceda permissão em nome do locatário inteiro. Usar esse ponto de extremidade também é necessário para solicitar permissões de aplicativo (que não pode ser solicitado a usar o ponto de extremidade de autorização).

Se você seguir estas etapas, seu aplicativo poderá solicitar permissões para todos os usuários em um locatário, incluindo escopos restringidos pelo administrador. Isso é uma operação com privilégios elevados e só deverá ser feita se for necessária para seu cenário.

Para obter um exemplo de código que implementa as etapas, veja o [exemplo de escopos restritos ao administrador](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Solicitar as permissões no portal de registro do aplicativo

O consentimento do administrador não aceita um parâmetro de escopo e, portanto, todas as permissões solicitadas devem ser definidas estaticamente no registro do aplicativo. Em geral, é prática recomendada para garantir que as permissões definidas estaticamente para um determinado aplicativo são um subconjunto das permissões que ele esteja solicitando dinamicamente/incrementalmente.

#### <a name="to-configure-the-list-of-statically-requested-permissions-for-an-application"></a>Para configurar a lista de permissões solicitadas estaticamente para um aplicativo

1. Vá para seu aplicativo na [portal do Azure – registros do aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) experiência, ou [criar um aplicativo](quickstart-register-app.md) se você ainda não fez isso.
2. Localize o **permissões de API** seção e, dentro das permissões de API, clique em Adicionar uma permissão.
3. Selecione **Microsoft Graph** na lista de APIs disponíveis e, em seguida, adicione as permissões que seu aplicativo requer.
3. **Salve** o registro do aplicativo.

### <a name="recommended-sign-the-user-into-your-app"></a>Recomendável: Conectar o usuário ao seu aplicativo

Normalmente, quando você cria um aplicativo que usa o ponto de extremidade de consentimento do administrador, o aplicativo precisa de uma página ou de um modo de exibição em que o administrador possa aprovar as permissões do aplicativo. Essa página pode ser parte do fluxo de inscrição no aplicativo, parte das configurações do aplicativo ou um fluxo dedicado de "conexão". Em muitos casos, faz sentido que o aplicativo somente mostre o modo de exibição "conectar" depois que o usuário entra com uma conta corporativa ou de estudante da Microsoft.

Ao conectar o usuário ao seu aplicativo, você pode identificar a organização à qual o administrador pertence antes de pedir a ele que aprove as permissões necessárias. Embora não seja estritamente necessário, isso pode ajudá-lo a criar uma experiência mais intuitiva para os usuários empresariais. Para conectar o usuário, siga nossos [tutoriais de protocolo da plataforma de identidade Microsoft](active-directory-v2-protocols.md).

### <a name="request-the-permissions-from-a-directory-admin"></a>Solicitar permissões de um administrador de diretório

Quando você estiver pronto para solicitar permissões de administrador da sua organização, você pode redirecionar o usuário para a plataforma de identidade da Microsoft *ponto de extremidade de consentimento de administrador*.

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

| Parâmetro | Condição | DESCRIÇÃO |
| --- | --- | --- |
| `tenant` | Obrigatório | O locatário do diretório para o qual você deseja solicitar permissão. Pode ser fornecido no GUID ou formato de nome amigável, OU referenciado de maneira genérica com `common`, como visto no exemplo. |
| `client_id` | Obrigatório | O **ID do aplicativo (cliente)** que o [portal do Azure – registros do aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) experiência atribuída ao seu aplicativo. |
| `redirect_uri` | Obrigatório |O URI de redirecionamento onde você deseja que a resposta seja enviada para ser tratada pelo aplicativo. Ela deve corresponder exatamente a um redirecionamento de URIs que você registrou no portal de registro de aplicativo. |
| `state` | Recomendado | Um valor incluído na solicitação também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. Use o estado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página ou exibição em que ele estava. |

Neste ponto, o Azure AD requer um administrador de locatários para entrar e concluir a solicitação. O administrador deverá aprovar todas as permissões que você solicitou para o aplicativo no portal de registro de aplicativos.

#### <a name="successful-response"></a>Resposta bem-sucedida

Se o administrador aprovar as permissões para o seu aplicativo, a resposta bem-sucedida será:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parâmetro | DESCRIÇÃO |
| --- | --- |
| `tenant` | O locatário do diretório que concedeu as permissões solicitadas, no formato de GUID. |
| `state` | Um valor incluído na solicitação também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. O estado é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página ou exibição em que ele estava. |
| `admin_consent` | Será definido como `True`. |

#### <a name="error-response"></a>Resposta de erro

Se o administrador não aprovar as permissões para o seu aplicativo, a resposta de falha será:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parâmetro | DESCRIÇÃO |
| --- | --- |
| `error` | Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro. |

Depois de receber uma resposta bem-sucedida do ponto de extremidade de consentimento do administrador, o aplicativo terá as permissões solicitadas por ele. Em seguida, você pode solicitar um token para o recurso desejado.

## <a name="using-permissions"></a>Usando permissões

Depois que o usuário consente permissões para o aplicativo, este pode adquirir tokens de acesso que representam a permissão do seu aplicativo para acessar um recurso em alguma capacidade. Um token de acesso só pode ser usado para um único recurso, mas codificada dentro do token de acesso estará cada permissão que o aplicativo recebeu para esse recurso. Para adquirir um token de acesso, seu aplicativo pode fazer uma solicitação para o Microsoft identity platform token ponto de extremidade, como este:

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

Para obter mais informações sobre o protocolo OAuth 2.0 e como obter tokens de acesso, consulte o [referência de protocolo de ponto de extremidade do Microsoft identity platform](active-directory-v2-protocols.md).

## <a name="the-default-scope"></a>O escopo /.default

Você pode usar o `/.default` escopo para ajudar a migrar seus aplicativos do ponto de extremidade v 1.0 para o ponto de extremidade de plataforma do Microsoft identity. Esse é o escopo interno para cada aplicativo que se refere à lista estática de permissões configuradas no registro de aplicativo. Um valor `scope` de `https://graph.microsoft.com/.default` tem funcionalidade igual à dos pontos de extremidade v1.0 `resource=https://graph.microsoft.com`, isto é, solicita um token com os escopos no Microsoft Graph para os quais o aplicativo foi registrado no portal do Azure.

O escopo de /.default pode ser usado em qualquer fluxo de OAuth 2.0, mas é necessário o [fluxo On-Behalf-Of](v2-oauth2-on-behalf-of-flow.md) e [fluxo de credenciais de cliente](v2-oauth2-client-creds-grant-flow.md).  

> [!NOTE]
> Os clientes não é possível combinar estático (`/.default`) e o consentimento dinâmico em uma única solicitação. Portanto, `scope=https://graph.microsoft.com/.default+mail.read` resultará em um erro devido à combinação de tipos de escopo.

### <a name="default-and-consent"></a>/.default e consentimento

O escopo `/.default` também dispara o comportamento de ponto de extremidade v1.0 para `prompt=consent`. Ele solicita consentimento para todas as permissões registrada pelo aplicativo, independentemente do recurso. Se for incluído como parte da solicitação, o `/.default` escopo retorna um token que contém os escopos para o recurso solicitado.

### <a name="default-when-the-user-has-already-given-consent"></a>/.default quando o usuário já recebeu consentimento

Como `/.default` tem funcionalidade idêntica à do comportamento do ponto de extremidade v1.0 do `resource`, ele também carrega consigo o comportamento do consentimento do ponto de extremidade v1.0. Ou seja, `/.default` dispara uma solicitação de consentimento somente se nenhuma permissão for concedida pelo usuário entre o cliente e o recurso. Se houver algum consentimento, um token retorna contendo todos os escopos concedidos pelo usuário para tal recurso. No entanto, se nenhuma permissão for concedida ou se o parâmetro `prompt=consent` for fornecido, é exibida uma solicitação de consentimento para todos os escopos registrados pelo aplicativo cliente.

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>Exemplo 1: O usuário, ou administrador de locatários, concedeu permissões

O usuário (ou um administrador de locatários) concedeu ao cliente as permissões `mail.read` e `user.read` do Microsoft Graph. Se o cliente faz uma solicitação por `scope=https://graph.microsoft.com/.default`, nenhuma solicitação de consentimento é exibida, independentemente do conteúdo das permissões registradas pelos aplicativos cliente para o Microsoft Graph. Um token retorna contendo os escopos `mail.read` e `user.read`.

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>Exemplo 2: O usuário não concedeu permissões entre o cliente e o recurso

Não existe consentimento para o usuário entre o cliente e o Microsoft Graph. O cliente foi registrado para as permissões `user.read` e `contacts.read`, e para o escopo `https://vault.azure.net/user_impersonation` do Azure Key Vault. Quando o cliente solicita um token para `scope=https://graph.microsoft.com/.default`, o usuário vê uma tela de consentimento para `user.read`, `contacts.read` e os escopos `user_impersonation` de Key Vault. O token retornado tem apenas os escopos `user.read` e `contacts.read`.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-additional-scopes"></a>Exemplo 3: O usuário consentiu e o cliente solicita escopos adicionais

O usuário já consentiu `mail.read` para o cliente. O cliente foi registrado para o escopo `contacts.read` em seu registro. Quando o cliente faz uma solicitação por um token usando `scope=https://graph.microsoft.com/.default` e solicita consentimento por meio de `prompt=consent`, o usuário vê uma tela de consentimento apenas e para todas as permissões registradas pelo aplicativo. `contacts.read` está presente na tela de consentimento, mas `mail.read`, não. O token retornado é para o Microsoft Graph e contém `mail.read` e `contacts.read`.

### <a name="using-the-default-scope-with-the-client"></a>Usando o escopo /.default com o cliente

Há um caso especial do escopo `/.default` em que um cliente solicita o seu próprio escopo `/.default`. O exemplo a seguir demonstra esse cenário.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

Isso gera uma tela de consentimento para todas as permissões registradas (se aplicável com base nas descrições de consentimento e `/.default` acima) e, em seguida, retorna um id_token, e não um token de acesso.  Esse comportamento existe para determinados clientes herdados de movimentação de ADAL para a MSAL e não deve ser usado por clientes novos direcionando o ponto de extremidade de plataforma do Microsoft identity.  

## <a name="troubleshooting-permissions-and-consent"></a>Solucionar problemas com permissões e consentimento

Se você ou os usuários do aplicativo veem erros inesperados durante o processo de consentimento, consulte este artigo para ver etapas de solução de problemas: [Erro inesperado ao executar o consentimento para um aplicativo](../manage-apps/application-sign-in-unexpected-user-consent-error.md).
