---
title: "Limitações e restrições de ponto de extremidade v2.0 do Azure Active Directory | Microsoft Docs"
description: "Uma lista de limitações e restrições para o ponto de extremidade v2.0 do Azure AD."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: a99289c0-e6ce-410c-94f6-c279387b4f66
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 00a6749d00c6e66a957b0a89c6658511a1bafe4d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="should-i-use-the-v20-endpoint"></a>Devo usar o ponto de extremidade da v2.0?
Ao criar aplicativos que se integram ao Azure Active Directory, você precisa decidir se os protocolos de autenticação e o ponto de extremidade v2.0 atendem às suas necessidades. O ponto de extremidade original do Azure Active Directory ainda tem suporte completo e, em alguns aspectos, tem mais recursos do que o v2.0. No entanto, o ponto de extremidade v2.0 [apresenta vantagens consideráveis](active-directory-v2-compare.md) para desenvolvedores.

No momento, esta é nossa recomendação simplificada para desenvolvedores:

* Se você precisa dar suporte a contas pessoais da Microsoft em seu aplicativo, use o ponto de extremidade v2.0. Mas antes disso, entenda as limitações que abordamos neste artigo.
* Caso seu aplicativo precise apenas de suporte a contas corporativas ou de estudante da Microsoft, não use o ponto de extremidade v2.0. Em vez disso, consulte nosso [guia do desenvolvedor do Azure AD](active-directory-developers-guide.md).

No futuro, o ponto de extremidade v2.0 vai expandir e eliminar as restrições listadas aqui, para que você só precise usar o ponto de extremidade v2.0. Enquanto isso, este artigo destina-se a ajudá-lo a determinar se o ponto de extremidade v2.0 é adequado para você. Continuaremos a atualizar este artigo para refletir o estado atual do ponto de extremidade v2.0. Verifique novamente para reavaliar seus requisitos em relação aos recursos da v2.0.

Se você tiver um aplicativo do Azure AD existente que não use o ponto de extremidade v2.0, não será necessário começar do zero. No futuro, forneceremos uma maneira de usar seus aplicativos existentes do Azure AD com o ponto de extremidade v2.0.

## <a name="restrictions-on-app-types"></a>Restrições de tipos de aplicativo
No momento, os seguintes tipos de aplicativos atualmente não têm suporte do ponto de extremidade v2.0. Para obter uma descrição dos tipos de aplicativos com suporte, veja [Tipos de aplicativo para o ponto de extremidade v2.0 do Azure Active Directory](active-directory-v2-flows.md).

### <a name="standalone-web-apis"></a>APIs da Web autônomas
Você pode usar o ponto de extremidade v2.0 para [criar uma API Web segura com OAuth 2.0](active-directory-v2-flows.md#web-apis). No entanto, essa API Web só pode receber tokens de um aplicativo que tenha a mesma ID do aplicativo. Você não pode acessar uma API Web de um cliente com uma ID de aplicativo diferente. O cliente não poderá solicitar nem obter permissões para sua API Web.

Para ver como criar uma API Web que aceita tokens de um cliente com a mesma ID de Aplicativo, confira os exemplos de API Web do ponto de extremidade da v 2.0 na seção [Introdução](active-directory-appmodel-v2-overview.md#getting-started).

## <a name="restrictions-on-app-registrations"></a>Restrições quanto a registros de aplicativos
Atualmente, para cada aplicativo que deseja integrar ao ponto de extremidade v2.0, você deve criar um registro de aplicativo no novo [Portal de Registro de Aplicativo da Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). O aplicativos do Azure AD ou de conta da Microsoft existentes não são compatíveis com o ponto de extremidade v2.0. Os aplicativos registrados em qualquer portal que não seja o Portal de Registro de Aplicativo não são compatíveis com o ponto de extremidade v2.0. No futuro, planejamos fornecer uma maneira de usar um aplicativo existente como um aplicativo v2.0. Atualmente, no entanto, há um caminho de migração para que um aplicativo existente funcione com o ponto de extremidade v2.0.

Além disso, os registros do aplicativo que você cria no [Portal de Registro de Aplicativo](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) têm as seguintes condições:

* Apenas dois segredos do aplicativo são permitidos por ID do Aplicativo.
* Um registro de aplicativo registrado por um usuário com uma conta pessoal da Microsoft pode ser exibido e gerenciado somente por uma única conta de desenvolvedor. Ele não pode ser compartilhado entre vários desenvolvedores.  Se você desejar compartilhar o registro do aplicativo entre vários desenvolvedores, poderá criar o aplicativo entrando no portal de registro com uma conta do Azure AD.
* Há várias restrições em relação ao formato de URI de redirecionamento permitido. Para saber mais sobre URIs de redirecionamento, veja a próxima seção.

## <a name="restrictions-on-redirect-uris"></a>Restrições de URIs de redirecionamento
No momento, os aplicativos registrados no novo Portal de Registro de Aplicativo são atualmente restritos a um conjunto limitado de valores de URI de redirecionamento. O URI de redirecionamento para serviços e aplicativos Web deve começar com o esquema `https`, e todos os valores de URI de redirecionamento devem compartilhar um único domínio DNS. Por exemplo, não é possível registrar um aplicativo Web que tem um destes URIs de redirecionamento:

`https://login-east.contoso.com`  
`https://login-west.contoso.com`

O sistema de registro compara o nome DNS completo do URI de redirecionamento existente ao nome DNS do URI de redirecionamento que você está adicionando. A solicitação para adicionar o nome DNS falhará se alguma das condições abaixo for verdadeira:  

* Se o nome DNS completo do URI de redirecionamento novo não coincidir com o nome DNS do URI de redirecionamento existente.
* Se o nome DNS completo do URI de redirecionamento novo não for um subdomínio do URI de redirecionamento existente.

Por exemplo, se o aplicativo tiver este URI de redirecionamento:

`https://login.contoso.com`

Você pode adicionar a ele, desta forma:

`https://login.contoso.com/new`

Nesse caso, os nome DNS corresponde exatamente. Ou você pode fazer isto:

`https://new.login.contoso.com`

Nesse caso, você está se referindo a um subdomínio DNS logon.contoso.com. Se você quiser ter um aplicativo com login-east.contoso.com e login-west.contoso.com como URIs de redirecionamento, deverá adicionar o seguinte URIs de redirecionamento nesta ordem:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Os dois últimos podem ser adicionados porque eles são subdomínios do primeiro URI de redirecionamento, contoso.com. Essa limitação será removida em uma versão futura.

Para saber como registrar um aplicativo no Portal de Registro de Aplicativo, veja [Como registrar um aplicativo no ponto de extremidade v2.0](active-directory-v2-app-registration.md).

## <a name="restrictions-on-services-and-apis"></a>Restrições de serviços e APIs
Atualmente, o ponto de extremidade v2.0 dá suporte à entrada para qualquer aplicativo registrado no Portal de Registro de Aplicativo e que se enquadre na lista de [fluxos de autenticação com suporte](active-directory-v2-flows.md). No entanto, esses aplicativos podem adquirir tokens de acesso do OAuth 2.0 para um conjunto muito limitado de recursos. O ponto de extremidade v2.0 só emite tokens de acesso para:

* O aplicativo que solicitou o token. Um aplicativo pode adquirir um token de acesso para si mesmo, se o aplicativo lógico é constituído de várias camadas ou componentes diferentes. Para ver esse cenário em ação, confira nossos tutoriais de [Introdução](active-directory-appmodel-v2-overview.md#getting-started) .
* As APIs REST de Email, Calendário e Contatos do Outlook, que estão localizadas em https://outlook.office.com. Para saber como escrever um aplicativo que acessa essas APIs, veja os tutoriais de [Introdução ao Office](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).
* APIs do Microsoft Graph. Você pode saber mais sobre o [Microsoft Graph](https://graph.microsoft.io) e os dados que estão disponíveis para você.

Nenhum outro serviço tem suporte no momento. Mais Microsoft Online Services serão adicionados no futuro, bem como suporte para seus próprios serviços e APIs Web personalizados.

## <a name="restrictions-on-libraries-and-sdks"></a>Restrição de bibliotecas e SDKs
Atualmente, o suporte de biblioteca para o ponto de extremidade v2.0 é limitado. Se deseja usar o ponto de extremidade v2.0 em um aplicativo de produção, você tem estas opções:

* Se estiver criando um aplicativo Web, você poderá usar com segurança nosso middleware do servidor disponível da Microsoft para realizar a entrada e a validação de token. Isso inclui o middleware OWIN Open ID Connect para ASP.NET e o plug-in Passport do Node.js. Para obter exemplos de código que usam o middleware da Microsoft, veja a nossa seção [Introdução](active-directory-appmodel-v2-overview.md#getting-started).
* Se você estiver criando um aplicativo móvel ou de área de trabalho, poderá usar uma de nossa MSAL (Biblioteca de Autenticação da Microsoft) de versão prévia.  Essas bibliotecas são uma versão prévia com suporte de produção; portanto, é seguro usá-las em aplicativos de produção. Leia mais sobre os termos da versão prévia e as bibliotecas disponíveis em nossa [referência de bibliotecas de autenticação](active-directory-v2-libraries.md).
* Para outras plataformas não abrangidas pelas bibliotecas da Microsoft, é possível fazer a integração com o ponto de extremidade v2.0 enviando e recebendo diretamente mensagens de protocolo no código do aplicativo. Os protocolos OAuth e OpenID Connect v2.0 [foram explicitamente documentados](active-directory-v2-protocols.md) para ajudar você a executar essa integração.
* Por fim, você pode usar bibliotecas de software livre do Open ID Connect e do OAuth para fazer a integração com o ponto de extremidade v2.0. O protocolo v2.0 deve ser compatível com muitas bibliotecas de protocolo de software livre sem grandes alterações. A disponibilidade desses tipos de bibliotecas varia por idioma e plataforma. Os sites do [Open ID Connect](http://openid.net/connect/) e do [OAuth 2.0](http://oauth.net/2/) mantêm uma lista das implementações populares. Para saber mais, confira [Bibliotecas de autenticação e v2.0 do Azure Active Directory](active-directory-v2-libraries.md) e para obter a lista de bibliotecas de cliente de software livre e exemplos testados com o ponto de extremidade v2.0.

## <a name="restrictions-on-protocols"></a>Restrições quanto a protocolos
O ponto de extremidade v2.0 não dá suporte ao SAML ou Web Services Federation; ele dá suporte apenas ao Open ID Connect e OAuth 2.0.  Nem todos os recursos e funcionalidades dos protocolos OAuth foram incorporados ao ponto de extremidade v2.0. No momento, estes recursos e estas funcionalidades de protocolo *não estão disponíveis* no ponto de extremidade v2.0:

* Os tokens de ID emitidos pelo ponto de extremidade v2.0 não contêm uma declaração `email` para o usuário, mesmo se você adquire permissão do usuário para exibir seus emails.
* O ponto de extremidade UserInfo do OpenID Connect não está implementado no ponto de extremidade v2.0. No entanto, todos os dados de perfil de usuário que você possivelmente receberia nesse ponto de extremidade estão disponíveis no ponto de extremidade `/me` do Microsoft Graph.
* O ponto de extremidade v2.0 não dá suporte à emissão de declarações de função ou de grupo em tokens de ID.
* Não há suporte para a [Concessão de Credenciais de Senha de Proprietário do Recurso do OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.3) no ponto de extremidade v2.0.

Além disso, o ponto de extremidade v2.0 não dá suporte a nenhum formato dos protocolos SAML ou Web Services Federation.

Para entender melhor o escopo da funcionalidade de protocolo com suporte no ponto de extremidade v2.0, leia nossa [referência de protocolo do OpenID Connect e OAuth 2.0](active-directory-v2-protocols.md).

## <a name="restrictions-for-work-and-school-accounts"></a>Restrições de contas corporativas e de estudante
Se você usou a ADAL (Active Directory Authentication Library) em aplicativos Windows, poderá aproveitar a autenticação integrada do Windows, que usa a concessão de declaração SAML (Security Assertion Markup Language). Com essa concessão, os usuários de locatários do Azure AD federados se autenticam com suas instâncias do Active Directory local sem inserir as credenciais. Atualmente, a concessão de asserção SAML não tem suporte no ponto de extremidade v2.0.