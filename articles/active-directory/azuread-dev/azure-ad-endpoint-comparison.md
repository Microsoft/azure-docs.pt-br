---
title: Por que atualizar para a plataforma Microsoft Identity (v 2.0) | Azure
description: Conheça as diferenças entre o ponto de extremidade da plataforma Microsoft Identity (v 2.0) e o ponto de extremidade do Azure Active Directory (Azure AD) v 1.0 e Aprenda os benefícios da atualização para o v 2.0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 07/17/2020
ms.author: ryanwi
ms.reviewer: saeeda, hirsin, jmprieur, sureshja, jesakowi, lenalepa, kkrishna, negoe
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 8f6170de65ae5e1ca8ecb5f7cc8a78f4f194ac41
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92055283"
---
# <a name="why-update-to-microsoft-identity-platform-v20"></a>Por que atualizar para plataforma de identidade da Microsoft (v2.0)?

Ao desenvolver um novo aplicativo, é importante conhecer as diferenças entre os pontos de extremidade da plataforma de identidade da Microsoft (v 2.0) e do Azure Active Directory (v 1.0). Este artigo aborda as principais diferenças entre os pontos de extremidade e algumas limitações existentes para a plataforma Microsoft Identity.

## <a name="who-can-sign-in"></a>Quem pode entrar

![Quem pode entrar com endpoints v1.0 e v2.0](media/azure-ad-endpoint-comparison/who-can-signin.svg)

* O ponto de extremidade v1.0 permite que apenas contas do trabalho e da escola entrem no seu aplicativo (Azure AD)
* O ponto de extremidade da plataforma de identidade da Microsoft permite contas corporativas e de estudante do Azure AD e do MSA (contas pessoais da Microsoft), como hotmail.com, outlook.com e msn.com, para entrar.
* Os dois pontos de extremidade também aceitam entradas de *[usuários convidados](../external-identities/what-is-b2b.md)* de um diretório do Azure ad para aplicativos configurados como *[locatário único](../develop/single-and-multi-tenant-apps.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)* ou para aplicativos *multilocatários* configurados para apontarem para o ponto de extremidade específico do locatário ( `https://login.microsoftonline.com/{TenantId_or_Name}` ).

O ponto de extremidade da plataforma de identidade da Microsoft permite que você escreva aplicativos que aceitem entradas de contas pessoais da Microsoft e contas corporativas e de estudante. Isso lhe dá a capacidade de escrever seu aplicativo completamente independente de conta. Por exemplo, se seu aplicativo chamar o [Microsoft Graph](https://graph.microsoft.io), alguns recursos e dados adicionais estarão disponíveis para contas de trabalho, como seus sites do SharePoint ou dados do Diretório. Mas, para muitas ações, como [a leitura de um e-mail de um usuário](/graph/api/user-list-messages), o mesmo código pode acessar o e-mail para contas pessoais e de trabalho e escola.

Para o ponto de extremidade da plataforma Microsoft Identity, você pode usar a MSAL (biblioteca de autenticação da Microsoft) para obter acesso aos mundos do consumidor, da educação e da empresa. O ponto de extremidade do Azure AD v1.0 aceita logins apenas de contas do trabalho e da escola.

## <a name="incremental-and-dynamic-consent"></a>Consentimento incremental e dinâmico

Os aplicativos que usam o endpoint do Azure AD v1.0 precisam especificar suas permissões necessárias do OAuth 2.0 antecipadamente, por exemplo:

![Exemplo mostrando a interface do usuário de registro de permissões](./media/azure-ad-endpoint-comparison/app-reg-permissions.png)

As permissões definidas diretamente no registro do aplicativo são **static**. Embora as permissões estáticas do aplicativo definidas no portal do Azure mantivessem o código simples e agradável, ele apresenta alguns problemas para os desenvolvedores:

* O aplicativo precisa solicitar todas as permissões que precisaria no primeiro login do usuário. Isso pode resultar em uma lista longa de permissões, o que desencorajava os usuários finais a aprovarem o acesso do aplicativo na entrada inicial.

* O aplicativo precisa conhecer todos os recursos que jamais acessaria antes do tempo. Era difícil criar aplicativos que pudessem acessar um número arbitrário de recursos.

Com o ponto de extremidade da plataforma Microsoft Identity, você pode ignorar as permissões estáticas definidas nas informações de registro do aplicativo no portal do Azure e solicitar permissões incrementalmente, o que significa solicitar um conjunto mínimo de permissões antecipadamente e crescendo mais ao longo do tempo conforme o cliente usa recursos de aplicativo adicionais. Para fazer isso, você pode especificar os escopos que seu aplicativo precisa, incluindo os novos escopos no parâmetro `scope` ao solicitar um token de acesso, sem a necessidade de pré-defini-los nas informações de registro do aplicativo. Se o usuário ainda não tiver consentido em novos escopos adicionados à solicitação, ele será solicitado a consentir apenas com as novas permissões. Para obter mais informações, consulte [permissões, autorização e escopos](../develop/v2-permissions-and-consent.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

Permitir que um aplicativo solicite permissões dinamicamente por meio do parâmetro `scope` fornece aos desenvolvedores controle total sobre a experiência do usuário. Você também pode optar por carregar sua experiência de consentimento e solicitar todas as permissões em uma solicitação inicial de autorização. Ou, se seu aplicativo precisar de um grande número de permissões, você pode reunir essas permissões do usuário de forma incremental, à medida que ele tentar usar determinados recursos do seu aplicativo ao longo do tempo.

Consentimento do administrador realizado em nome de uma organização ainda exige as permissões estáticas registradas para o aplicativo, portanto, você deve definir essas permissões para aplicativos no portal de registro do aplicativo se precisar de um administrador para dar consentimento em nome de toda a organização. Isso reduz os ciclos exigidos pelo administrador da organização para configurar o aplicativo.

## <a name="scopes-not-resources"></a>Escopos, não recursos

Para aplicativos que usam o endpoint v1.0, um aplicativo pode se comportar como um **recurso** ou um destinatário de tokens. Um recurso pode definir um número de **escopos** ou **oAuth2Permissions** que ele entende, permitindo que os aplicativos cliente solicitem tokens a esse recurso de um determinado conjunto de escopos. Considere a API Microsoft Graph como um exemplo de um recurso:

* Identificador de recurso, ou `AppID URI`: `https://graph.microsoft.com/`
* Escopos, ou `oAuth2Permissions`: `Directory.Read`, `Directory.Write`e assim por diante.

Isso se aplica ao ponto de extremidade da plataforma Microsoft Identity. Um aplicativo ainda pode se comportar como recurso, definir escopos e ser identificado por um URI. Aplicativos cliente ainda podem solicitar acesso a esses escopos. No entanto, a maneira como um cliente solicita essas permissões foi alterada.

Para o ponto de extremidade v1.0, uma solicitação de autorização do OAuth 2.0 para o Azure AD pode ter parecido com:

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.microsoft.com/
...
```

Aqui, o parâmetro **recurso** indicava o recurso para o qual o aplicativo cliente estava solicitando a autorização. O Microsoft Azure Active Directory computava as permissões exigidas pelo aplicativo com base na configuração estática no Portal do Azure e emitia os tokens de acordo.

Para aplicativos que usam o ponto de extremidade da plataforma Microsoft Identity, a mesma solicitação de autorização OAuth 2,0 é semelhante a:

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.microsoft.com/directory.read%20https://graph.microsoft.com/directory.write
...
```

Aqui, parâmetro **escopo** indica para qual recurso e permissões o aplicativo está solicitando autorização. O recurso desejado ainda está presente na solicitação; ele simplesmente está englobado em cada um dos valores do parâmetro de escopo. Usar o parâmetro scope dessa maneira permite que o ponto de extremidade da plataforma de identidade da Microsoft seja mais compatível com a especificação OAuth 2,0 e se alinhe mais de acordo com as práticas comuns do setor. Ele também permite que os aplicativos façam o [consentimento incremental](#incremental-and-dynamic-consent) , solicitando permissões somente quando o aplicativo os exige, em oposição à frente.

## <a name="well-known-scopes"></a>Escopos conhecidos

### <a name="offline-access"></a>Acesso offline

Aplicativos que usam o ponto de extremidade da plataforma Microsoft Identity podem exigir o uso de uma nova permissão conhecida para aplicativos – o `offline_access` escopo. Todos os aplicativos terão que solicitar essa permissão se precisarem acessar recursos em nome de um usuário por um longo período de tempo, mesmo quando o usuário pode não estiver usando o aplicativo de maneira ativa. O escopo `offline_access` aparecerá para o usuário em diálogos de consentimento como **Acesse seus dados a qualquer momento**, com os quais o usuário deve concordar. Solicitar a `offline_access` permissão permitirá que seu aplicativo Web receba Refresh_tokens OAuth 2,0 do ponto de extremidade da plataforma Microsoft Identity. Os tokens de atualização são de longa duração e podem ser trocados por novos tokens de acesso do OAuth 2.0 por longos períodos de acesso.

Se seu aplicativo não solicitar o `offline_access` escopo, ele não receberá tokens de atualização. Isso significa que, quando você resgatar um código de autorização no fluxo do código de autorização do OAuth 2.0, receberá apenas um token de acesso do ponto de extremidade `/token`. Esse token de acesso permanecerá válido por um curto período de tempo (geralmente uma hora), mas acabará expirando. Nesse momento, seu aplicativo precisará redirecionar o usuário de volta ao ponto de extremidade `/authorize` para recuperar um novo código de autorização. Durante esse redirecionamento, o usuário pode ou não precisar digitar suas credenciais novamente ou consentir de novo as permissões, dependendo do tipo do aplicativo.

Para saber mais sobre o OAuth 2,0, `refresh_tokens` `access_tokens` o e o, confira a referência do protocolo de plataforma de identidade da [Microsoft](../develop/active-directory-v2-protocols.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

### <a name="openid-profile-and-email"></a>OpenID, perfil e email

Historicamente, o fluxo de entrada mais básico do OpenID Connect com a plataforma de identidade da Microsoft forneceria muitas informações sobre o usuário no *id_token*resultante. As declarações em um id_token podem incluir o nome do usuário, nome de usuário preferido, endereço de email, ID do objeto e muito mais.

As informações que o escopo de `openid` permite que seu aplicativo acesse agora estão restritas. O escopo de `openid` apenas permitirá que seu aplicativo faça logon do usuário e receba um identificador específico do aplicativo para o usuário. Se você quiser obter dados pessoais sobre o usuário em seu aplicativo, seu aplicativo precisará solicitar permissões adicionais do usuário. Dois novos escopos `email` e `profile` – permitirão que você solicite permissões adicionais.

* O `email` escopo permite que o aplicativo acesse o endereço de email principal do usuário por meio da `email` declaração no id_token, supondo que o usuário tenha um endereço de email endereçável.
* O `profile` escopo dá ao seu aplicativo acesso a todas as outras informações básicas sobre o usuário, como nome, nome de usuário preferencial, ID de objeto e assim por diante, no id_token.

Esse escopo permite que você codifique seu aplicativo com uma divulgação mínima. Você só pode solicitar ao usuário o conjunto de informações de que seu aplicativo precisa para fazer seu trabalho. Para obter mais informações sobre esses escopos, consulte [a referência de escopo da plataforma de identidade da Microsoft](../develop/v2-permissions-and-consent.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="token-claims"></a>Declarações de token

O ponto de extremidade da plataforma de identidade da Microsoft emite um conjunto menor de declarações em seus tokens por padrão para manter as cargas pequenas. Se você tiver aplicativos e serviços que têm uma dependência em uma declaração específica em um token v 1.0 que não é mais fornecido por padrão em um token da plataforma de identidade da Microsoft, considere usar o recurso de [declarações opcionais](../develop/active-directory-optional-claims.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) para incluir essa declaração.

> [!IMPORTANT]
> os tokens v 1.0 e v 2.0 podem ser emitidos pelos pontos de extremidade v 1.0 e v 2.0! id_tokens *sempre* corresponder ao ponto de extremidade de onde são solicitados e os tokens de acesso *sempre* correspondem ao formato esperado pela API Web, o cliente chamará usando esse token.  Portanto, se seu aplicativo usar o ponto de extremidade v 2.0 para obter um token para chamar Microsoft Graph, que espera tokens de acesso de formato v 1.0, seu aplicativo receberá um token no formato v 1.0.

## <a name="limitations"></a>Limitações

Há algumas restrições a serem consideradas ao usar a plataforma de identidade da Microsoft.

Ao criar aplicativos que se integram com a plataforma de identidade da Microsoft, você precisa decidir se os protocolos de autenticação e o ponto de extremidade da plataforma de identidade da Microsoft atendem às suas necessidades. O ponto de extremidade v 1.0 e a plataforma ainda têm suporte total e, em alguns aspectos, é mais rico em recursos do que a plataforma de identidade da Microsoft. No entanto, a plataforma de identidade da Microsoft [apresenta benefícios significativos](azure-ad-endpoint-comparison.md) para os desenvolvedores.

Aqui está uma recomendação simplificada para os desenvolvedores agora:

* Se você quiser ou precisar dar suporte a contas pessoais da Microsoft em seu aplicativo ou se estiver escrevendo um novo aplicativo, use a plataforma de identidade da Microsoft. Mas antes de fazer isso, tenha certeza de que entende as limitações discutidas neste artigo.
* Se você estiver migrando ou atualizando um aplicativo que dependa do SAML, não poderá usar a plataforma de identidade da Microsoft. Em vez disso, consulte o [Guia do Azure ad v 1.0](v1-overview.md).

O ponto de extremidade da plataforma de identidade da Microsoft evoluirá para eliminar as restrições listadas aqui, para que você nunca precise usar o ponto de extremidade da plataforma de identidade da Microsoft. Enquanto isso, use este artigo para determinar se o ponto de extremidade da plataforma de identidade da Microsoft é ideal para você. Continuaremos a atualizar este artigo para refletir o estado atual do ponto de extremidade da plataforma Microsoft Identity. Verifique novamente para reavaliar seus requisitos em relação aos recursos da plataforma de identidade da Microsoft.

### <a name="restrictions-on-app-registrations"></a>Restrições quanto a registros de aplicativos

Para cada aplicativo que você deseja integrar com o ponto de extremidade da plataforma de identidade da Microsoft, você pode criar um registro de aplicativo na nova experiência de [ **Registros de aplicativo** ](https://aka.ms/appregistrations) no portal do Azure. Os aplicativos conta Microsoft existentes não são compatíveis com o portal, mas todos os aplicativos do Azure AD são, independentemente de onde ou quando eles foram registrados.

Registros de aplicativo que dão suporte ao trabalho e contas de estudante e contas pessoais têm as seguintes condições:

* Apenas dois segredos do aplicativo são permitidos por ID do Aplicativo.
* Um aplicativo que não foi registrado em um locatário só pode ser gerenciado pela conta que o registrou. Ele não pode ser compartilhado com outros desenvolvedores. Esse é o caso para a maioria dos aplicativos que foram registrados usando uma conta pessoal da Microsoft no Portal de registro do aplicativo. Se você quiser compartilhar seu registro de aplicativo com vários desenvolvedores, registre o aplicativo em um locatário usando a nova seção **registros de aplicativo** da portal do Azure.
* Existem várias restrições quanto ao formato do URL de redirecionamento permitido. Para mais informações sobre o URL de redirecionamento, consulte a próxima seção.

### <a name="restrictions-on-redirect-urls"></a>Restrições em URLs de redirecionamento

Para obter as informações mais atualizadas sobre restrições em URLs de redirecionamento para aplicativos que são registrados para a plataforma de identidade da Microsoft, consulte [redirecionar restrições de URL/resposta e limitações](../develop/reply-url.md) na documentação da plataforma Microsoft Identity.

Para saber como registrar um aplicativo para uso com a plataforma de identidade da Microsoft, consulte [registrar um aplicativo usando a nova experiência de registros de aplicativo](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

### <a name="restrictions-on-libraries-and-sdks"></a>Restrição de bibliotecas e SDKs

Atualmente, o suporte à biblioteca para o ponto de extremidade da plataforma Microsoft Identity é limitado. Se você quiser usar o ponto de extremidade da plataforma de identidade da Microsoft em um aplicativo de produção, terá estas opções:

* Se você estiver criando um aplicativo Web, você pode usar com segurança o middleware do lado do servidor disponível para fazer logon e validação de token. Isso inclui o middleware OWIN Open ID Connect para ASP.NET e o plug-in Passport do Node.js. Para obter exemplos de código que usam o middleware da Microsoft, consulte a seção [introdução à plataforma de identidade da Microsoft](../develop/v2-overview.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#getting-started) .
* Se você estiver criando um aplicativo móvel ou desktop, poderá usar uma das MSAL (bibliotecas de autenticação da Microsoft). Essas bibliotecas estão geralmente disponíveis ou em uma versão prévia com suporte de produção, portanto, é seguro usá-las em aplicativos de produção. Leia mais sobre os termos da versão prévia e as bibliotecas disponíveis na [referência de bibliotecas de autenticação](../develop/reference-v2-libraries.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).
* Para plataformas não cobertas pelas bibliotecas da Microsoft, você pode integrar-se ao ponto de extremidade da plataforma de identidade da Microsoft enviando e recebendo diretamente mensagens de protocolo no código do aplicativo. Os protocolos do OpenID Connect e do OAuth [são documentados explicitamente](../develop/active-directory-v2-protocols.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) para ajudá-lo a fazer essa integração.
* Por fim, você pode usar o OpenID Connect e as bibliotecas do OAuth de software livre para integrar com o ponto de extremidade da plataforma de identidade da Microsoft. O ponto de extremidade da plataforma de identidade da Microsoft deve ser compatível com muitas bibliotecas de protocolo de código aberto sem alterações. A disponibilidade desses tipos de bibliotecas varia por idioma e plataforma. Os sites do [Open ID Connect](https://openid.net/connect/) e do [OAuth 2.0](https://oauth.net/2/) mantêm uma lista das implementações populares. Para obter mais informações, consulte [plataforma de identidade da Microsoft e bibliotecas de autenticação](../develop/reference-v2-libraries.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)e a lista de bibliotecas de cliente de software livre e exemplos que foram testados com o ponto de extremidade da plataforma Microsoft Identity.
* Para referência, o `.well-known` ponto de extremidade para o ponto de extremidade comum da plataforma de identidade da Microsoft é `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration` . Substitua `common` pela ID do locatário para obter dados específicos para o locatário.

### <a name="protocol-changes"></a>Alterações do protocolo

O ponto de extremidade da plataforma de identidade da Microsoft não oferece suporte a SAML ou WS-Federation; Ele só dá suporte a OpenID Connect e a OAuth 2,0.  As alterações importantes para os protocolos OAuth 2.0 do ponto de extremidade v1.0 são:

* A declaração `email` será retornada somente se uma declaração opcional estiver configurada **ou** scope=email tiver sido especificado na solicitação.
* O parâmetro `scope` agora tem suporte em vez do `resource` parâmetro.
* Muitas respostas foram modificadas para torná-lo mais compatível com a especificação do OAuth 2.0, por exemplo, retornando corretamente `expires_in` como um int em vez de uma cadeia de caracteres.

Para entender melhor o escopo da funcionalidade de protocolo com suporte no ponto de extremidade da plataforma de identidade da Microsoft, consulte [OpenID Connect e referência de protocolo OAuth 2,0](../develop/active-directory-v2-protocols.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

#### <a name="saml-usage"></a>Uso de SAML

Se você usou o Biblioteca de Autenticação do Active Directory (ADAL) em aplicativos do Windows, você pode ter aproveitado a autenticação integrada do Windows, que usa a concessão de asserção do Security Assertion Markup Language (SAML). Com essa concessão, os usuários de locatários do Azure AD federados se autenticam com suas instâncias do Active Directory local sem inserir as credenciais. Embora o [SAML ainda seja um protocolo com suporte](../develop/active-directory-saml-protocol-reference.md) para uso com usuários corporativos, o ponto de extremidade v 2.0 é usado apenas com aplicativos OAuth 2,0.

## <a name="next-steps"></a>Próximas etapas

Saiba mais na [documentação da plataforma de identidade da Microsoft](../develop/index.yml).
