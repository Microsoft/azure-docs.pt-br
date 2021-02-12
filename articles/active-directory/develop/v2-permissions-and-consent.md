---
title: Escopos da plataforma de identidade da Microsoft, permissões, & consentimento
description: Saiba mais sobre a autorização no ponto de extremidade da plataforma Microsoft Identity, incluindo escopos, permissões e consentimento.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: ryanwi
ms.reviewer: hirsin, jesakowi, jmprieur, marsma
ms.custom: aaddev, fasttrack-edit, contperf-fy21q1, identityplatformtop40
ms.openlocfilehash: 570314bcaedb86cc593846ffc1d6846d1d2fe335
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2021
ms.locfileid: "99090180"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform"></a>Permissões e consentimento na plataforma de identidade da Microsoft

Os aplicativos que se integram à plataforma de identidade da Microsoft seguem um modelo de autorização que dá aos usuários e administradores controle sobre como os dados podem ser acessados. A implementação do modelo de autorização foi atualizada na plataforma de identidade da Microsoft. Ele altera como um aplicativo deve interagir com a plataforma de identidade da Microsoft. Este artigo aborda os conceitos básicos deste modelo de autorização, incluindo escopos, permissões e consentimento.

## <a name="scopes-and-permissions"></a>Permissões e escopos

A plataforma de identidade da Microsoft implementa o protocolo de autorização [OAuth 2.0](active-directory-v2-protocols.md). O OAuth 2.0 é um método pelo qual um aplicativo de terceiros pode acessar recursos hospedados na Web em nome do usuário. Qualquer recurso hospedado na Web que se integre com a plataforma de identidade da Microsoft tem um identificador de recurso ou um *URI de ID do aplicativo*. 

Aqui estão alguns exemplos de recursos hospedados na Web da Microsoft:

* Microsoft Graph: `https://graph.microsoft.com`
* API do Microsoft 365 mail: `https://outlook.office.com`
* Azure Key Vault: `https://vault.azure.net`

Isso se aplica a todos os recursos de terceiros que se integraram à plataforma de identidade da Microsoft. Qualquer um desses recursos também pode definir um conjunto de permissões que pode ser usado para dividir a funcionalidade desse recurso em partes menores. Por exemplo, o [Microsoft Graph](https://graph.microsoft.com) definiu permissões para realizar as seguintes tarefas, entre outras:

* Ler o calendário de um usuário
* Escrever no calendário de um usuário
* Enviar email como um usuário

Devido a esses tipos de definições de permissão, o recurso tem um controle refinado sobre seus dados e como a funcionalidade da API é exposta. Um aplicativo de terceiros pode solicitar essas permissões de usuários e administradores que devem aprovar a solicitação antes que o aplicativo possa acessar dados ou agir em nome do usuário. 

Quando a funcionalidade de um recurso é incluída em pequenos conjuntos de permissões, aplicativos de terceiros podem ser criados para solicitar apenas as permissões necessárias para executar sua função. Os usuários e administradores podem saber quais dados o aplicativo pode acessar. E pode ser mais confiante que o aplicativo não está se comportando com más intenções. Os desenvolvedores devem sempre obedecer ao princípio de privilégios mínimos, solicitando apenas as permissões necessárias para que seus aplicativos funcionem.

No OAuth 2,0, esses tipos de conjuntos de permissões são chamados de *escopos*. Eles também são chamados de *permissões*. Na plataforma Microsoft Identity, uma permissão é representada como um valor de cadeia de caracteres. Para o exemplo de Microsoft Graph, aqui está o valor da cadeia de caracteres para cada permissão:

* Ler o calendário de um usuário usando o `Calendars.Read`
* Escrever no calendário de um usuário usando o `Calendars.ReadWrite`
* Enviar email como um usuário usando `Mail.Send`

Um aplicativo geralmente solicita essas permissões especificando os escopos em solicitações para o ponto de extremidade de autorização da plataforma de identidade da Microsoft. No entanto, algumas permissões de alto privilégio só podem ser concedidas por meio do consentimento do administrador. Eles podem ser solicitados ou concedidos usando o [ponto de extremidade de consentimento do administrador](#admin-restricted-permissions). Continue lendo para saber mais.

## <a name="permission-types"></a>Tipos de permissão

A plataforma de identidade da Microsoft dá suporte a dois tipos de permissões: *permissões delegadas* e *permissões de aplicativo*.

* **Permissões delegadas** são usadas por aplicativos que têm um usuário conectado presente. Para esses aplicativos, o usuário ou um administrador consentirá com as permissões solicitadas pelo aplicativo. O aplicativo é uma permissão delegada para atuar como o usuário conectado quando faz chamadas para o recurso de destino. 

    Algumas permissões delegadas podem ser consentidas por não administradores. Mas algumas permissões com altos privilégios exigem o [consentimento do administrador](#admin-restricted-permissions). Para saber quais funções de administrador podem consentir as permissões delegadas, consulte [permissões de função de administrador no Azure Active Directory (Azure AD)](../roles/permissions-reference.md).

* **As permissões de aplicativo** são usadas por aplicativos que são executados sem um usuário conectado presente, por exemplo, aplicativos que são executados como daemons ou serviços em segundo plano. Somente [um administrador pode consentir](#requesting-consent-for-an-entire-tenant) as permissões do aplicativo.

_As permissões efetivas_ são as permissões que seu aplicativo tem quando faz solicitações para o recurso de destino. É importante entender a diferença entre as permissões delegadas e as permissões de aplicativo que seu aplicativo recebe e as permissões efetivas que seu aplicativo recebe quando faz chamadas para o recurso de destino.

- Para permissões delegadas, as _permissões efetivas_ de seu aplicativo são a interseção com privilégios mínimos das permissões delegadas que o aplicativo recebeu (por consentimento) e os privilégios do usuário conectado no momento. Seu aplicativo não pode ter mais privilégios que o usuário conectado. 

   Nas organizações, os privilégios do usuário conectado podem ser determinados pela política ou por associação em uma ou mais funções de administrador. Para saber quais funções de administrador podem consentir as permissões delegadas, consulte [Permissões da função de administrador no Microsoft Azure Active Directory](../roles/permissions-reference.md).

   Por exemplo, suponha que seu aplicativo tenha recebido a permissão delegada _User.ReadWrite.All_. Essa permissão concede uma permissão ao seu aplicativo para ler e atualizar o perfil de todos os usuários em uma organização. Se o usuário conectado for um administrador global, seu aplicativo poderá atualizar o perfil de cada usuário na organização. No entanto, se o usuário conectado não tiver uma função de administrador, seu aplicativo poderá atualizar somente o perfil do usuário conectado. Ele não pode atualizar os perfis de outros usuários na organização porque o usuário com permissão para agir em nome de não tem esses privilégios.

- Para permissões de aplicativo, as _permissões efetivas_ de seu aplicativo são o nível completo de privilégios implícitos pela permissão. Por exemplo, um aplicativo que tenha a permissão de aplicativo _User.ReadWrite.All_ poderá atualizar o perfil de todos os usuários na organização.

## <a name="openid-connect-scopes"></a>Escopos do OpenID Connect

A implementação da plataforma Microsoft Identity do OpenID Connect tem alguns escopos bem definidos que também são hospedados em Microsoft Graph: `openid` ,, `email` `profile` e `offline_access` . `address` `phone` Não há suporte para os escopos do e do OpenID Connect.

Se você solicitar os escopos do OpenID Connect e um token, obterá um token para chamar o [ponto de extremidade de UserInfo](userinfo.md).

### <a name="openid"></a>openid

Se um aplicativo entrar usando o [OpenID Connect](active-directory-v2-protocols.md), ele deverá solicitar o `openid` escopo. O `openid` escopo aparece na página de consentimento da conta de trabalho como a permissão de **entrada** . Na página de consentimento de conta Microsoft pessoal, ele aparece como **exibir seu perfil e se conectar a aplicativos e serviços usando a permissão conta Microsoft** . 

Ao usar essa permissão, um aplicativo pode receber um identificador exclusivo para o usuário na forma da `sub` declaração. A permissão também fornece ao aplicativo acesso ao ponto de extremidade de UserInfo. O `openid` escopo pode ser usado no ponto de extremidade de token da plataforma de identidade da Microsoft para adquirir tokens de ID. O aplicativo pode usar esses tokens para autenticação.

### <a name="email"></a>email

O `email` escopo pode ser usado com o `openid` escopo e quaisquer outros escopos. Ele concede ao aplicativo acesso ao endereço de email principal do usuário na forma da declaração `email` . 

A `email` declaração será incluída em um token somente se um endereço de email estiver associado à conta de usuário, que nem sempre é o caso. Se seu aplicativo usar o `email` escopo, o aplicativo precisará ser capaz de lidar com um caso no qual nenhuma `email` declaração existe no token.

### <a name="profile"></a>perfil

O `profile` escopo pode ser usado com o `openid` escopo e qualquer outro escopo. Ele dá ao aplicativo acesso a uma grande quantidade de informações sobre o usuário. As informações que ele pode acessar incluem, mas não se limitam a, o nome do usuário, o sobrenome, o nome de usuário preferencial e a ID de objeto. 

Para obter uma lista completa das `profile` declarações disponíveis no `id_tokens` parâmetro para um usuário específico, consulte a [ `id_tokens` referência](id-tokens.md).

### <a name="offline_access"></a>offline_access

O [ `offline_access` escopo](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) dá ao aplicativo acesso a recursos em nome do usuário por um longo tempo. Na página autorização, esse escopo é exibido como **manter o acesso aos dados que você concedeu acesso à** permissão. 

Quando um usuário aprova o `offline_access` escopo, seu aplicativo pode receber tokens de atualização do ponto de extremidade de token da plataforma de identidade da Microsoft. Os tokens de atualização têm uma vida longa. Seu aplicativo pode obter novos tokens de acesso quando os mais antigos expirarem.

> [!NOTE]
> Essa permissão atualmente aparece em todas as páginas de autorização, mesmo para fluxos que não fornecem um token de atualização (como o [fluxo implícito](v2-oauth2-implicit-grant-flow.md)). Essa configuração aborda cenários em que um cliente pode começar dentro do fluxo implícito e, em seguida, mover para o fluxo de código onde um token de atualização é esperado.

Na plataforma de identidade da Microsoft (solicitações feitas ao ponto de extremidade v 2.0), seu aplicativo deve solicitar explicitamente o `offline_access` escopo para receber tokens de atualização. Assim, quando você resgatar um código de autorização no [fluxo de código de autorização do OAuth 2,0](active-directory-v2-protocols.md), receberá apenas um token de acesso do ponto de `/token` extremidade. 

O token de acesso é válido por um curto período. Normalmente, ele expira em uma hora. Nesse ponto, seu aplicativo precisa redirecionar o usuário novamente para o ponto de extremidade `/authorize` para obter um novo código de autorização. Durante esse redirecionamento, dependendo do tipo de aplicativo, o usuário poderá ou não precisar inserir suas credenciais novamente ou consentir de novo as permissões.

Para obter mais informações sobre como obter e usar tokens de atualização, consulte a [referência de protocolo de plataforma de identidade da Microsoft](active-directory-v2-protocols.md).

## <a name="requesting-individual-user-consent"></a>Solicitando consentimento de usuário individual

Em uma solicitação de autorização do [OpenID Connect ou OAuth 2.0](active-directory-v2-protocols.md), um aplicativo pode solicitar as permissões de que precisa usando o parâmetro de consulta `scope`. Por exemplo, quando um usuário entra em um aplicativo, o aplicativo envia uma solicitação como o exemplo a seguir. (As quebras de linha são adicionadas para legibilidade.)

```HTTP
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

O parâmetro `scope` é uma lista de permissões delegadas separadas por espaço que o aplicativo está solicitando. Cada permissão é indicada acrescentando o valor de permissão ao identificador do recurso (o URI da ID do aplicativo). No exemplo de solicitação, o aplicativo precisa de permissão para ler o calendário e enviar emails como o usuário.

Depois que o usuário inserir suas credenciais, a plataforma de identidade da Microsoft verificará se há um registro correspondente de *consentimento do usuário*. Se o usuário não consentiu em nenhuma das permissões solicitadas no passado e se o administrador não consentiu essas permissões em nome de toda a organização, a plataforma de identidade da Microsoft solicitará que o usuário conceda as permissões solicitadas.

Neste momento, a permissão `offline_access` ("manter o acesso aos dados que você concedeu acesso") e a `user.read` permissão ("entrar e ler seu perfil") são incluídas automaticamente no consentimento inicial para um aplicativo.  Essas permissões geralmente são necessárias para a funcionalidade de aplicativo adequada. A `offline_access` permissão concede ao aplicativo acesso para atualizar tokens que são essenciais para aplicativos nativos e aplicativos Web. A `user.read` permissão concede acesso à `sub` declaração. Ele permite que o cliente ou aplicativo identifique corretamente o usuário ao longo do tempo e acesse informações de usuário rudimentares.

![Captura de tela de exemplo que mostra o consentimento da conta corporativa.](./media/v2-permissions-and-consent/work_account_consent.png)

Quando o usuário aprova a solicitação de permissão, o consentimento é registrado. O usuário não precisa consentir novamente quando eles entram mais tarde no aplicativo.

## <a name="requesting-consent-for-an-entire-tenant"></a>Solicitando consentimento para um locatário inteiro

Quando uma organização adquire uma licença ou assinatura para um aplicativo, a organização geralmente deseja configurar proativamente o aplicativo para uso por todos os membros da organização. Como parte desse processo, um administrador pode autorizar que o aplicativo atue em nome de todos os usuários no locatário. Se o administrador conceder consentimento para o locatário inteiro, os usuários da organização não verão uma página de consentimento para o aplicativo.

Para solicitar consentimento de permissões delegadas para todos os usuários em um locatário, o aplicativo pode usar o ponto de extremidade de consentimento de administrador.

Além disso, os aplicativos devem usar o ponto de extremidade de consentimento do administrador para solicitar permissões de aplicativo.

## <a name="admin-restricted-permissions"></a>Permissões restringidas pelo administrador

Algumas permissões de alto privilégio nos recursos da Microsoft podem ser definidas como *restritas ao administrador*. Aqui estão alguns exemplos desses tipos de permissões:

* Ler os perfis completos de todos os usuários usando `User.Read.All`
* Gravar dados no diretório da organização usando o `Directory.ReadWrite.All`
* Ler todos os grupos no diretório da organização usando `Groups.Read.All`

Embora um usuário consumidor possa conceder a um aplicativo acesso a esse tipo de dados, os usuários organizacionais não podem conceder acesso ao mesmo conjunto de dados confidenciais da empresa. Se seu aplicativo solicitar acesso a uma dessas permissões de um usuário organizacional, o usuário receberá uma mensagem de erro que informa que não está autorizado a consentir com as permissões de seu aplicativo.

Se seu aplicativo requer escopos para permissões restritas ao administrador, o administrador de uma organização deve consentir esses escopos em nome dos usuários da organização. Para evitar a exibição de prompts para os usuários que solicitam consentimento para permissões que eles não podem conceder, seu aplicativo pode usar o ponto de extremidade de consentimento do administrador. O ponto de extremidade de consentimento do administrador é abordado na próxima seção.

Se o aplicativo solicitar permissões delegadas de alto privilégio e um administrador conceder essas permissões por meio do ponto de extremidade de consentimento do administrador, o consentimento será concedido para todos os usuários no locatário.

Se o aplicativo solicitar permissões de aplicativo e um administrador conceder essas permissões por meio do ponto de extremidade de consentimento do administrador, essa concessão não será feita em nome de qualquer usuário específico. Na verdade, o aplicativo cliente recebe as permissões *diretamente*. Esses tipos de permissões são usados somente por serviços de daemon e outros aplicativos não interativos que são executados em segundo plano.

## <a name="using-the-admin-consent-endpoint"></a>Usando o ponto de extremidade de consentimento do administrador

Depois de usar o ponto de extremidade de consentimento do administrador para conceder o consentimento do administrador, você terá terminado. Os usuários não precisam executar nenhuma ação adicional. Depois que o consentimento do administrador é concedido, os usuários podem obter um token de acesso por meio de um fluxo de autenticação típico. O token de acesso resultante tem as permissões consentidas.

Quando um administrador global usa seu aplicativo e é direcionado para o ponto de extremidade de autorização, a plataforma de identidade da Microsoft detecta a função do usuário. Ele pergunta se o administrador global deseja consentir em nome do locatário inteiro para as permissões solicitadas. Em vez disso, você poderia usar um ponto de extremidade de consentimento de administrador dedicado para solicitar proativamente um administrador para conceder permissão em nome do locatário inteiro. Esse ponto de extremidade também é necessário para solicitar permissões de aplicativo. Não é possível solicitar permissões de aplicativo usando o ponto de extremidade de autorização.

Se você seguir estas etapas, seu aplicativo poderá solicitar permissões para todos os usuários em um locatário, incluindo escopos restringidos pelo administrador. Esta operação é de alto privilégio. Use a operação somente se necessário para seu cenário.

Para ver um exemplo de código que implementa as etapas, consulte o [exemplo de escopos restritos pelo administrador](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2) no github.

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Solicitar as permissões no portal de registro do aplicativo

No portal de registro de aplicativo, os aplicativos podem listar as permissões de que precisam, incluindo permissões delegadas e permissões de aplicativo. Essa configuração permite o uso do `/.default` escopo e a opção **conceder consentimento de administrador** do portal do Azure.  

Em geral, as permissões devem ser definidas estaticamente para um determinado aplicativo. Eles devem ser um superconjunto das permissões que o aplicativo solicitará de forma dinâmica ou incremental.

> [!NOTE]
>As permissões de aplicativo podem ser solicitadas somente por meio do uso de [`/.default`](#the-default-scope) . Portanto, se seu aplicativo precisar de permissões de aplicativo, verifique se eles estão listados no portal de registro de aplicativo.

Para configurar a lista de permissões solicitadas estaticamente para um aplicativo:

1. Vá para seu aplicativo na experiência de início rápido de <a href="https://go.microsoft.com/fwlink/?linkid=2083908" target="_blank">portal do Azure registros de aplicativo <span class="docon docon-navigate-external x-hidden-focus"></span> </a> .
1. Selecione um aplicativo ou [crie um aplicativo](quickstart-register-app.md) , caso ainda não tenha feito isso.
1. Na página **visão geral** do aplicativo, em **gerenciar**, selecione **permissões de API**  >  **Adicionar uma permissão**.
1. Selecione **Microsoft Graph** na lista de APIs disponíveis. Em seguida, adicione as permissões que seu aplicativo requer.
1. Selecione **adicionar permissões**.

### <a name="recommended-sign-the-user-in-to-your-app"></a>Recomendado: conectar o usuário ao aplicativo

Normalmente, quando você cria um aplicativo que usa o ponto de extremidade de consentimento do administrador, o aplicativo precisa de uma página ou de um modo de exibição em que o administrador possa aprovar as permissões do aplicativo. Esta página pode ser:

* Parte do fluxo de inscrição do aplicativo.
* Parte das configurações do aplicativo.
* Um fluxo de "conexão" dedicado. 

Em muitos casos, faz sentido que o aplicativo mostre esse modo de exibição "conectar" somente depois que um usuário tiver entrado com um conta Microsoft de trabalho ou escola conta Microsoft.

Ao conectar o usuário ao seu aplicativo, você pode identificar a organização à qual o administrador pertence antes de pedir que eles aprovem as permissões necessárias. Embora essa etapa não seja estritamente necessária, ela pode ajudá-lo a criar uma experiência mais intuitiva para seus usuários organizacionais. 

Para conectar o usuário, siga os [tutoriais do protocolo de plataforma de identidade da Microsoft](active-directory-v2-protocols.md).

### <a name="request-the-permissions-from-a-directory-admin"></a>Solicitar permissões de um administrador de diretório

Quando estiver pronto para solicitar permissões do administrador da sua organização, você poderá redirecionar o usuário para o ponto de extremidade de consentimento do administrador da plataforma de identidade da Microsoft.

```HTTP
// Line breaks are for legibility only.
GET https://login.microsoftonline.com/{tenant}/v2.0/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
&scope=
https://graph.microsoft.com/calendars.read
https://graph.microsoft.com/mail.send
```


| Parâmetro        | Condição        | Descrição                                                                                |
|:--------------|:--------------|:-----------------------------------------------------------------------------------------|
| `tenant` | Obrigatório | O locatário do diretório para o qual você deseja solicitar permissão. Ele pode ser fornecido em um formato de nome amigável ou GUID. Ou pode ser referenciado genericamente com organizações, como visto no exemplo. Não use "comum", porque as contas pessoais não podem fornecer consentimento de administrador, exceto no contexto de um locatário. Para garantir a melhor compatibilidade com contas pessoais que gerenciam locatários, use a ID do locatário quando possível. |
| `client_id` | Obrigatório | A ID do aplicativo (cliente) que a [portal do Azure – registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) experiência atribuída ao seu aplicativo. |
| `redirect_uri` | Obrigatório |O URI de redirecionamento onde você deseja que a resposta seja enviada para ser tratada pelo aplicativo. Ela deve corresponder exatamente a um redirecionamento de URIs que você registrou no portal de registro de aplicativo. |
| `state` | Recomendadas | Um valor incluído na solicitação que também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. Use o estado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página ou exibição em que ele estava. |
|`scope`        | Obrigatório        | Define o conjunto de permissões que estão sendo solicitadas pelo aplicativo. Os escopos podem ser estáticos (usando [`/.default`](#the-default-scope) ) ou dinâmicos.  Esse conjunto pode incluir os escopos do OpenID Connect ( `openid` , `profile` , `email` ). Se você precisar de permissões de aplicativo, deverá usar `/.default` para solicitar a lista de permissões configuradas estaticamente.  |


Neste ponto, o Azure AD requer um administrador de locatários para entrar e concluir a solicitação. O administrador é solicitado a aprovar todas as permissões que você solicitou no `scope` parâmetro.  Se você usou um valor estático ( `/.default` ), ele funcionará como o ponto de extremidade de consentimento do administrador v 1.0 e solicitará o consentimento para todos os escopos encontrados nas permissões necessárias para o aplicativo.

#### <a name="successful-response"></a>Resposta bem-sucedida

Se o administrador aprovar as permissões para o seu aplicativo, a resposta bem-sucedida será:

```HTTP
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parâmetro | Descrição |
| --- | --- |
| `tenant` | O locatário do diretório que concedeu as permissões solicitadas, no formato de GUID. |
| `state` | Um valor incluído na solicitação também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. O estado é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página ou exibição em que ele estava. |
| `admin_consent` | Será definido como `True`. |

#### <a name="error-response"></a>Resposta de erro

Se o administrador não aprovar as permissões para seu aplicativo, a resposta com falha terá esta aparência:

```HTTP
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parâmetro | Descrição |
| --- | --- |
| `error` | Uma cadeia de caracteres de código de erro que pode ser utilizada para classificar os tipos de erros que ocorrem. Ele também pode ser usado para reagir a erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro. |

Depois de receber uma resposta bem-sucedida do ponto de extremidade de consentimento do administrador, o aplicativo terá as permissões solicitadas por ele. Em seguida, você pode solicitar um token para o recurso desejado.

## <a name="using-permissions"></a>Usando permissões

Depois que o usuário consentir permissões para seu aplicativo, seu aplicativo poderá adquirir tokens de acesso que representem a permissão do aplicativo para acessar um recurso em alguma capacidade. Um token de acesso pode ser usado somente para um único recurso. Mas codificado dentro do token de acesso é cada permissão que seu aplicativo recebeu para esse recurso. Para adquirir um token de acesso, seu aplicativo pode fazer uma solicitação para o ponto de extremidade de token da plataforma de identidade da Microsoft, desta forma:

```HTTP
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

Você pode usar o token de acesso resultante em solicitações HTTP para o recurso. Ele indica de maneira confiável o recurso de que seu aplicativo tem a permissão apropriada para realizar uma tarefa específica.

Para obter mais informações sobre o protocolo OAuth 2,0 e como obter tokens de acesso, consulte a [referência do protocolo de ponto de extremidade da plataforma de identidade da Microsoft](active-directory-v2-protocols.md).

## <a name="the-default-scope"></a>O escopo /.default

Você pode usar o `/.default` escopo para ajudar a migrar seus aplicativos do ponto de extremidade v 1.0 para o ponto de extremidade da plataforma Microsoft Identity. O `/.default` escopo é interno para cada aplicativo que se refere à lista estática de permissões configuradas no registro do aplicativo. 

Um `scope` valor de `https://graph.microsoft.com/.default` é funcionalmente o mesmo que `resource=https://graph.microsoft.com` no ponto de extremidade v 1.0. Ao especificar o `https://graph.microsoft.com/.default` escopo em sua solicitação, seu aplicativo está solicitando um token de acesso que inclui escopos para cada Microsoft Graph permissão que você selecionou para o aplicativo no portal de registro de aplicativo. O escopo é construído usando o URI de recurso e `/.default` . Portanto, se o URI do recurso for `https://contosoApp.com` , o escopo solicitado será `https://contosoApp.com/.default` .  Para casos em que você deve incluir uma segunda barra para solicitar corretamente o token, consulte a [seção sobre barras à direita](#trailing-slash-and-default).

O `/.default` escopo pode ser usado em qualquer fluxo OAuth 2,0. Mas é necessário no [fluxo em nome de e no](v2-oauth2-on-behalf-of-flow.md) [fluxo de credenciais do cliente](v2-oauth2-client-creds-grant-flow.md). Você também precisará dela quando usar o ponto de extremidade de consentimento de administrador v2 para solicitar permissões de aplicativo.

Os clientes não podem combinar o consentimento estático ( `/.default` ) e o consentimento dinâmico em uma única solicitação. Portanto, `scope=https://graph.microsoft.com/.default+mail.read` resulta em um erro porque ele combina tipos de escopo.

### <a name="default-and-consent"></a>/.default e consentimento

O escopo `/.default` também dispara o comportamento de ponto de extremidade v1.0 para `prompt=consent`. Ele solicita consentimento para todas as permissões que o aplicativo registrou, independentemente do recurso. Se ele estiver incluído como parte da solicitação, o `/.default` escopo retornará um token que contém os escopos para o recurso solicitado.

### <a name="default-when-the-user-has-already-given-consent"></a>/.default quando o usuário já recebeu consentimento

O `/.default` escopo é funcionalmente idêntico ao comportamento do ponto de `resource` extremidade v 1.0 centrado em. Ele também transporta o comportamento de consentimento do ponto de extremidade v 1.0. Ou seja, `/.default` dispara uma solicitação de consentimento somente se o usuário não tiver concedido nenhuma permissão entre o cliente e o recurso. 

Se esse tipo de consentimento existir, o token retornado conterá todos os escopos concedidos pelo usuário para esse recurso. No entanto, se nenhuma permissão tiver sido concedida ou se o `prompt=consent` parâmetro tiver sido fornecido, uma solicitação de consentimento será mostrada para todos os escopos que o aplicativo cliente registrou.

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>Exemplo 1: o usuário, ou administrador de locatário, concedeu permissões

Neste exemplo, o usuário ou um administrador de locatários concedeu `mail.read` as `user.read` permissões e Microsoft Graph ao cliente. 

Se o cliente solicitar `scope=https://graph.microsoft.com/.default` , nenhum prompt de consentimento será mostrado, independentemente do conteúdo das permissões registradas do aplicativo cliente para Microsoft Graph. O token retornado contém os escopos `mail.read` e `user.read` .

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>Exemplo 2: o usuário não concedeu permissões entre o cliente e o recurso

Neste exemplo, o usuário não concedeu consentimento entre o cliente e o Microsoft Graph. O cliente se registrou para as permissões `user.read` e `contacts.read` . Ele também foi registrado para o escopo de Azure Key Vault `https://vault.azure.net/user_impersonation` . 

Quando o cliente solicita um token para `scope=https://graph.microsoft.com/.default` , o usuário vê uma página de consentimento para o `user.read` escopo, o `contacts.read` escopo e os `user_impersonation` escopos de Key Vault. O token retornado contém apenas os `user.read` `contacts.read` escopos e. Ele pode ser usado somente em Microsoft Graph.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-more-scopes"></a>Exemplo 3: o usuário consentiu e o cliente solicita mais escopos

Neste exemplo, o usuário já consentiu o `mail.read` para o cliente. O cliente foi registrado para o `contacts.read` escopo. 

Quando o cliente solicita um token usando `scope=https://graph.microsoft.com/.default` o e solicita consentimento por meio `prompt=consent` do, o usuário vê uma página de consentimento para todas (e apenas) as permissões que o aplicativo registrou. O `contacts.read` escopo está na página de consentimento, mas `mail.read` não é. O token retornado é para Microsoft Graph. Ele contém `mail.read` e `contacts.read` .

### <a name="using-the-default-scope-with-the-client"></a>Usando o escopo /.default com o cliente

Em alguns casos, um cliente pode solicitar seu próprio `/.default` escopo. O exemplo a seguir demonstra esse cenário.

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //Code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

Este exemplo de código produz uma página de consentimento para todas as permissões registradas se as descrições anteriores de consentimento e `/.default` se aplicam ao cenário. Em seguida, o código retorna um `id_token` , em vez de um token de acesso.  

Esse comportamento acomoda alguns clientes herdados que estão mudando da ADAL (biblioteca de autenticação do Azure AD) para a MSAL (biblioteca de autenticação da Microsoft). Essa configuração não *deve* ser usada por novos clientes que se destinam à plataforma de identidade da Microsoft.

### <a name="client-credentials-grant-flow-and-default"></a>Fluxo de concessão de credenciais de cliente e/.Default  

Outro uso do `/.default` é solicitar permissões de aplicativo (ou *funções*) em um aplicativo não interativo, como um aplicativo de daemon que usa o fluxo de concessão de [credenciais de cliente](v2-oauth2-client-creds-grant-flow.md) para chamar uma API da Web.

Para criar permissões de aplicativo (funções) para uma API da Web, consulte [adicionar funções de aplicativo em seu aplicativo](howto-add-app-roles-in-azure-ad-apps.md).

As solicitações de credenciais de cliente em seu aplicativo cliente *devem* incluir `scope={resource}/.default` . Aqui `{resource}` está a API Web que seu aplicativo pretende chamar. *Não* há suporte para a emissão de uma solicitação de credenciais de cliente usando permissões de aplicativo individuais (funções). Todas as permissões de aplicativo (funções) que foram concedidas para essa API Web são incluídas no token de acesso retornado.

Para conceder acesso às permissões de aplicativo que você definir, incluindo a concessão de consentimento do administrador para o aplicativo, consulte [configurar um aplicativo cliente para acessar uma API da Web](quickstart-configure-app-access-web-apis.md).

### <a name="trailing-slash-and-default"></a>Barra e/.Default à direita

Alguns URIs de recurso têm uma barra à direita, por exemplo, `https://contoso.com/` em oposição a `https://contoso.com` . A barra à direita pode causar problemas com a validação do token. Os problemas ocorrem principalmente quando um token é solicitado para Azure Resource Manager ( `https://management.azure.com/` ). Nesse caso, uma barra à direita no URI de recurso significa que a barra deve estar presente quando o token é solicitado.  Assim, quando você solicitar um token para `https://management.azure.com/` e usar `/.default` , deverá solicitar `https://management.azure.com//.default` (Observe a barra dupla!). Em geral, se você verificar que o token está sendo emitido e se o token estiver sendo rejeitado pela API que deve aceitá-lo, considere adicionar uma segunda barra e tentar novamente. 

## <a name="troubleshooting-permissions-and-consent"></a>Solucionar problemas com permissões e consentimento

Para obter as etapas de solução de problemas, consulte [erro inesperado ao executar o consentimento para um aplicativo](../manage-apps/application-sign-in-unexpected-user-consent-error.md).

## <a name="next-steps"></a>Próximas etapas

* [Tokens de ID na plataforma de identidade da Microsoft](id-tokens.md)
* [Tokens de acesso na plataforma de identidade da Microsoft](access-tokens.md)
