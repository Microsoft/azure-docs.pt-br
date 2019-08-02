---
title: Por que atualizar para a plataforma de identidade da Microsoft (v2.0) | Azure
description: Saber as diferenças entre o ponto de extremidade do Microsoft identity platform (v2.0) e o ponto de extremidade do Azure Active Directory (Azure AD) v 1.0 e conheça os benefícios de atualizar para a versão 2.0.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: ryanwi
ms.reviewer: saeeda, hirsin, jmprieur, sureshja, jesakowi, lenalepa, kkrishna, negoe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22f3e4cde892a70ec331523524508a50008a4073
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67483015"
---
# <a name="why-update-to-microsoft-identity-platform-v20"></a>Por que atualizar para a plataforma de identidade da Microsoft (v2.0)?

Ao desenvolver um novo aplicativo, é importante saber as diferenças entre a plataforma de identidade da Microsoft (v2.0) e pontos de extremidade do Azure Active Directory (v1.0). Este artigo aborda as principais diferenças entre os pontos de extremidade e algumas limitações existentes para a plataforma de identidade da Microsoft.

> [!NOTE]
> O ponto de extremidade de plataforma de identidade do Microsoft não oferece suporte a todos os recursos e cenários do AD do Azure. Para determinar se deve usar o ponto de extremidade de plataforma do Microsoft identity, leia sobre [limitações da plataforma Microsoft identity](#limitations).

## <a name="who-can-sign-in"></a>Quem pode entrar

![Quem pode entrar com endpoints v1.0 e v2.0](media/azure-ad-endpoint-comparison/who-can-sign-in.svg)

* O ponto de extremidade v1.0 permite que apenas contas do trabalho e da escola entrem no seu aplicativo (Azure AD)
* O ponto de extremidade de plataforma de identidade do Microsoft permite o trabalho e contas de estudante do Azure AD e contas pessoais da Microsoft (MSA), como hotmail.com, outlook.com e msn.com, para entrar.
* Ambos os pontos de extremidade também aceitam entradas de *[os usuários convidados](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)* de um diretório do AD do Azure para aplicativos configurados como *[único locatário](single-and-multi-tenant-apps.md)* ou para *multilocatário* aplicativos configurados para apontar para o ponto de extremidade específico do locatário (`https://login.microsoftonline.com/{TenantId_or_Name}`).

O ponto de extremidade de plataforma de identidade do Microsoft permite que você criar aplicativos que aceitam entradas de contas pessoais da Microsoft e contas corporativas e de estudante. Isso lhe dá a capacidade de escrever seu aplicativo completamente independente de conta. Por exemplo, se seu aplicativo chamar o [Microsoft Graph](https://graph.microsoft.io), alguns recursos e dados adicionais estarão disponíveis para contas de trabalho, como seus sites do SharePoint ou dados do Diretório. Mas, para muitas ações, como [a leitura de um e-mail de um usuário](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_messages), o mesmo código pode acessar o e-mail para contas pessoais e de trabalho e escola.

Ponto de extremidade de plataforma do Microsoft identity, você pode usar a biblioteca de autenticação Microsoft (MSAL) para obter acesso para o consumidor, educacional e os mundos empresarial. O ponto de extremidade do Azure AD v1.0 aceita logins apenas de contas do trabalho e da escola.

## <a name="incremental-and-dynamic-consent"></a>Consentimento incremental e dinâmico

Os aplicativos que usam o endpoint do Azure AD v1.0 precisam especificar suas permissões necessárias do OAuth 2.0 antecipadamente, por exemplo:

![Exemplo que mostra a interface do usuário permissões de registro](./media/azure-ad-endpoint-comparison/app_reg_permissions.png)

As permissões definidas diretamente no registro do aplicativo são **static**. Embora as permissões estáticas do aplicativo definidas no portal do Azure mantivessem o código simples e agradável, ele apresenta alguns problemas para os desenvolvedores:

* O aplicativo precisa solicitar todas as permissões que precisaria no primeiro login do usuário. Isso pode resultar em uma lista longa de permissões, o que desencorajava os usuários finais a aprovarem o acesso do aplicativo na entrada inicial.

* O aplicativo precisa conhecer todos os recursos que jamais acessaria antes do tempo. Era difícil criar aplicativos que pudessem acessar um número arbitrário de recursos.

Com o ponto de extremidade de plataforma identidade Microsoft, você pode ignorar as permissões estáticas definidas nas informações de registro de aplicativo nas permissões do Azure do portal e solicitação incrementalmente em vez disso, que significa solicitando um bare conjunto mínimo de permissões com antecedência e o crescimento mais ao longo do tempo como o cliente usa os recursos de aplicativo adicionais. Para fazer isso, você pode especificar os escopos que seu aplicativo precisa, incluindo os novos escopos no parâmetro `scope` ao solicitar um token de acesso, sem a necessidade de pré-defini-los nas informações de registro do aplicativo. Se o usuário ainda não tiver consentido em novos escopos adicionados à solicitação, ele será solicitado a consentir apenas com as novas permissões. Para obter mais informações, consulte [permissões, autorização e escopos](v2-permissions-and-consent.md).

Permitir que um aplicativo solicite permissões dinamicamente por meio do parâmetro `scope` fornece aos desenvolvedores controle total sobre a experiência do usuário. Você também pode optar por carregar sua experiência de consentimento e solicitar todas as permissões em uma solicitação inicial de autorização. Ou, se seu aplicativo precisar de um grande número de permissões, você pode reunir essas permissões do usuário de forma incremental, à medida que ele tentar usar determinados recursos do seu aplicativo ao longo do tempo.

Consentimento do administrador realizado em nome de uma organização ainda exige as permissões estáticas registradas para o aplicativo, portanto, você deve definir essas permissões para aplicativos no portal de registro do aplicativo se precisar de um administrador para dar consentimento em nome de toda a organização. Isso reduz os ciclos exigidos pelo administrador da organização para configurar o aplicativo.

## <a name="scopes-not-resources"></a>Escopos, não recursos

Para aplicativos que usam o endpoint v1.0, um aplicativo pode se comportar como um **recurso** ou um destinatário de tokens. Um recurso pode definir um número de **escopos** ou **oAuth2Permissions** que ele entende, permitindo que os aplicativos cliente solicitem tokens a esse recurso de um determinado conjunto de escopos. Considere a API do Graph do AD do Azure como um exemplo de um recurso:

* Identificador de recurso, ou `AppID URI`: `https://graph.windows.net/`
* Escopos, ou `oAuth2Permissions`: `Directory.Read`, `Directory.Write`e assim por diante.

Isso se aplica para o ponto de extremidade de plataforma do Microsoft identity. Um aplicativo ainda pode se comportar como recurso, definir escopos e ser identificado por um URI. Aplicativos cliente ainda podem solicitar acesso a esses escopos. No entanto, a maneira que um cliente solicita essas permissões foram alteradas.

Para o ponto de extremidade v1.0, uma solicitação de autorização do OAuth 2.0 para o Azure AD pode ter parecido com:

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.windows.net/
...
```

Aqui, o parâmetro **recurso** indicava o recurso para o qual o aplicativo cliente estava solicitando a autorização. O Microsoft Azure Active Directory computava as permissões exigidas pelo aplicativo com base na configuração estática no Portal do Azure e emitia os tokens de acordo.

Para aplicativos que usam o ponto de extremidade de plataforma do Microsoft identity, mesmo OAuth 2.0 autorizar a solicitação é semelhante:

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.windows.net/directory.read%20https://graph.windows.net/directory.write
...
```

Aqui, parâmetro **escopo** indica para qual recurso e permissões o aplicativo está solicitando autorização. O recurso desejado ainda está presente na solicitação; ele simplesmente está englobado em cada um dos valores do parâmetro de escopo. Usar o parâmetro de escopo dessa maneira permite que o ponto de extremidade de plataforma do Microsoft identity seja mais compatível com a especificação do OAuth 2.0 e esteja alinhado mais estreitamente com práticas comuns do setor. Ele também permite que os aplicativos façam [consentimento incremental](#incremental-and-dynamic-consent) - somente solicitando permissões quando o aplicativo requê-los em vez de antecipadamente.

## <a name="well-known-scopes"></a>Escopos conhecidos

### <a name="offline-access"></a>Acesso offline

Aplicativos que usam o ponto de extremidade de plataforma do Microsoft identidade podem exigir o uso de uma nova permissão conhecida para aplicativos – o `offline_access` escopo. Todos os aplicativos terão que solicitar essa permissão se precisarem acessar recursos em nome de um usuário por um longo período de tempo, mesmo quando o usuário pode não estiver usando o aplicativo de maneira ativa. O escopo `offline_access` aparecerá para o usuário em diálogos de consentimento como **Acesse seus dados a qualquer momento**, com os quais o usuário deve concordar. Solicitando o `offline_access` permissão permitirá que seu aplicativo web receba refresh_tokens do OAuth 2.0 do ponto de extremidade de plataforma de identidade Microsoft. Os tokens de atualização são de longa duração e podem ser trocados por novos tokens de acesso do OAuth 2.0 por longos períodos de acesso.

Se o aplicativo não solicitar o `offline_access` escopo, ele não receberá tokens de atualização. Isso significa que, quando você resgatar um código de autorização no fluxo do código de autorização do OAuth 2.0, receberá apenas um token de acesso do ponto de extremidade `/token`. Esse token de acesso permanecerá válido por um curto período de tempo (geralmente uma hora), mas acabará expirando. Nesse momento, seu aplicativo precisará redirecionar o usuário de volta ao ponto de extremidade `/authorize` para recuperar um novo código de autorização. Durante esse redirecionamento, o usuário pode ou não precisar digitar suas credenciais novamente ou consentir de novo as permissões, dependendo do tipo do aplicativo.

Para saber mais sobre o OAuth 2.0 `refresh_tokens`, e `access_tokens`, fazer check-out a [referência de protocolo de plataforma de identidade Microsoft](active-directory-v2-protocols.md).

### <a name="openid-profile-and-email"></a>OpenID, perfil e email

Historicamente, o mais básica OpenID Connect fluxo de entrada com a plataforma de identidade Microsoft forneceria muitas informações sobre o usuário no resultante *id_token*. As declarações em um id_token podem incluir o nome do usuário, nome de usuário preferido, endereço de email, ID do objeto e muito mais.

As informações que o escopo de `openid` permite que seu aplicativo acesse agora estão restritas. O escopo de `openid` apenas permitirá que seu aplicativo faça logon do usuário e receba um identificador específico do aplicativo para o usuário. Se você quiser obter dados pessoais sobre o usuário em seu aplicativo, seu aplicativo precisará solicitar permissões adicionais do usuário. Dois novos escopos `email` e `profile` – permitirão que você solicite permissões adicionais.

* O escopo de `email` permite que seu aplicativo acesse o endereço de email principal do usuário por meio da declaração `email` no id_token, supondo que o usuário tem um endereço de email endereçável.
* O `profile` escopo permite que seu aplicativo acesse todas as outras informações básicas sobre o usuário, como seu nome, nome de usuário preferido, objeto ID e assim por diante, no id_token.

Esse escopo permite que você codifique seu aplicativo com uma divulgação mínima. Você só pode solicitar ao usuário o conjunto de informações de que seu aplicativo precisa para fazer seu trabalho. Para obter mais informações sobre os escopos, consulte [referência de escopo da plataforma de identidade do Microsoft](v2-permissions-and-consent.md).

## <a name="token-claims"></a>Declarações de token

O ponto de extremidade de plataforma de identidade do Microsoft emite um conjunto menor de declarações em seus tokens por padrão para manter cargas pequeno. Se você tiver aplicativos e serviços que têm uma dependência em uma declaração específica em um token de v 1.0 não é mais fornecido por padrão em um token de plataforma do Microsoft identity, considere usar o [declarações opcionais](active-directory-optional-claims.md) recurso para incluir essa declaração.

## <a name="limitations"></a>Limitações

Há algumas restrições a serem consideradas ao usar a plataforma de identidade da Microsoft.

Quando você compila aplicativos que se integram com a plataforma de identidade da Microsoft, você precisa decidir se os protocolos de autenticação e o ponto de extremidade de plataforma do identidade Microsoft atendem às suas necessidades. O ponto de extremidade v1.0 e a plataforma ainda é totalmente suportado e, em alguns aspectos, tem mais recursos de plataforma de identidade da Microsoft. No entanto, plataforma de identidade da Microsoft [apresenta vantagens consideráveis](azure-ad-endpoint-comparison.md) para desenvolvedores.

Aqui está uma recomendação simplificada para desenvolvedores agora:

* Se você deseja ou precisa dar suporte a contas pessoais da Microsoft em seu aplicativo, ou você estiver escrevendo um novo aplicativo, use a plataforma de identidade da Microsoft. Mas antes de fazer isso, tenha certeza de que entende as limitações discutidas neste artigo.
* Se você estiver migrando ou atualizando um aplicativo que se baseia em SAML, você não pode usar a plataforma de identidade da Microsoft. Em vez disso, consulte a [guia do Azure AD v 1.0](v1-overview.md).

O ponto de extremidade de plataforma do Microsoft identity evolui para eliminar as restrições listadas aqui, para que você só precisará usar o ponto de extremidade de plataforma do Microsoft identity. Enquanto isso, use este artigo para determinar se o ponto de extremidade de plataforma do Microsoft identity é ideal para você. Continuaremos a atualizar este artigo para refletir o estado atual do ponto de extremidade de plataforma de identidade de Microsoft. Verifique novamente para reavaliar seus requisitos em relação aos recursos da plataforma Microsoft identity.

### <a name="restrictions-on-app-registrations"></a>Restrições quanto a registros de aplicativos

Para cada aplicativo que você deseja integrar com o ponto de extremidade de plataforma do Microsoft identity, você pode criar um registro de aplicativo no novo [ **registros do aplicativo** experiência](https://aka.ms/appregistrations) no portal do Azure. Aplicativos de conta da Microsoft existentes não são compatíveis com o portal, mas são todos os aplicativos do Azure AD, independentemente de onde e quando eles foram registrados.

Registros de aplicativo que dão suporte ao trabalho e contas de estudante e contas pessoais têm as seguintes condições:

* Apenas dois segredos do aplicativo são permitidos por ID do Aplicativo.
* Um aplicativo que não foi registrado em um locatário só pode ser gerenciado pela conta que o registrou. Ele não pode ser compartilhado com outros desenvolvedores. Esse é o caso para a maioria dos aplicativos que foram registrados usando uma conta pessoal da Microsoft no Portal de registro do aplicativo. Se você desejar compartilhar o registro do seu aplicativo com vários desenvolvedores, registrar o aplicativo em um locatário usando o novo **registros de aplicativo** seção do portal do Azure.
* Existem várias restrições quanto ao formato do URL de redirecionamento permitido. Para mais informações sobre o URL de redirecionamento, consulte a próxima seção.

### <a name="restrictions-on-redirect-urls"></a>Restrições em URLs de redirecionamento

Aplicativos que são registrados para a plataforma de identidade da Microsoft estão restritos a um conjunto limitado de valores da URL de redirecionamento. O URL de redirecionamento para aplicativos e serviços da web deve começar com o esquema `https`, e todos os valores de URL de redirecionamento devem compartilhar um único domínio DNS.  O sistema de registro compara todo o nome DNS do URL de redirecionamento existente com o nome DNS do URL de redirecionamento que você está adicionando. `http://localhost` também é compatível como uma URL de redirecionamento.  

A solicitação para adicionar o nome DNS falhará se alguma das condições abaixo for verdadeira:  

* Todo o nome DNS do novo URL de redirecionamento não corresponde ao nome DNS do URL de redirecionamento existente.
* O nome DNS completo da URL de redirecionamento novo não for um subdomínio da URL de redirecionamento existente.

#### <a name="example-1"></a>Exemplo 1

Se o aplicativo tem uma URL de redirecionamento de `https://login.contoso.com`, você pode adicionar uma URL de redirecionamento em que o nome DNS corresponde exatamente, conforme mostrado no exemplo a seguir:

`https://login.contoso.com/new`

Ou, você pode se referir a um DNS de subdomínio logon.contoso.com, conforme mostrado no exemplo a seguir:

`https://new.login.contoso.com`

#### <a name="example-2"></a>Exemplo 2

Se você quiser ter um aplicativo que tenha `login-east.contoso.com` e `login-west.contoso.com` como URLs de redirecionamento, adicione os URLs de redirecionamento nesta ordem:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Os dois últimos podem ser adicionados porque eles são subdomínios da primeira URL de redirecionamento, contoso.com.

Você pode ter apenas 20 URLs de resposta para um determinado aplicativo - esse limite se aplica em todos os tipos de aplicativo que o registro dá suporte a (aplicativo de página única (SPA), cliente nativo, aplicativo web e serviço).  

Para saber como registrar um aplicativo para uso com a plataforma de identidade da Microsoft, consulte [registrar um aplicativo usando a nova experiência de registros de aplicativo](quickstart-register-app.md).

### <a name="restrictions-on-libraries-and-sdks"></a>Restrição de bibliotecas e SDKs

Atualmente, o suporte de biblioteca para o ponto de extremidade de plataforma do Microsoft identity é limitado. Se você quiser usar o ponto de extremidade de plataforma de identidade do Microsoft em um aplicativo de produção, você tem estas opções:

* Se você estiver criando um aplicativo web, você pode usar com segurança o middleware de servidor disponível para entrada e validação de token. Isso inclui o middleware OWIN Open ID Connect para ASP.NET e o plug-in Passport do Node.js. Para obter exemplos de código que usam o middleware da Microsoft, consulte o [guia de introdução de plataforma de identidade Microsoft](v2-overview.md#getting-started) seção.
* Se você estiver criando um aplicativo de desktop ou mobile, você pode usar um de bibliotecas de autenticação a Microsoft (MSAL). Essas bibliotecas estão disponíveis ou em uma versão prévia com suporte de produção, portanto, é seguro usá-los em aplicativos de produção. Leia mais sobre os termos da versão prévia e as bibliotecas disponíveis na [referência de bibliotecas de autenticação](reference-v2-libraries.md).
* Para plataformas não cobertas pelas bibliotecas da Microsoft, você pode integrar com o ponto de extremidade de plataforma do Microsoft identity diretamente enviando e recebendo mensagens de protocolo no código do aplicativo. Os protocolos OAuth e OpenID Connect [foram explicitamente documentados](active-directory-v2-protocols.md) para ajudá-lo a fazer essa integração.
* Por fim, você pode usar bibliotecas de código-fonte aberto OAuth e OpenID Connect para integrar com o ponto de extremidade de plataforma do Microsoft identity. O ponto de extremidade de plataforma do Microsoft identity deve ser compatível com muitas bibliotecas de protocolo do código-fonte aberto sem alterações. A disponibilidade desses tipos de bibliotecas varia por idioma e plataforma. Os sites do [Open ID Connect](https://openid.net/connect/) e do [OAuth 2.0](https://oauth.net/2/) mantêm uma lista das implementações populares. Para obter mais informações, consulte [bibliotecas de plataforma e a autenticação de identidade Microsoft](reference-v2-libraries.md)e a lista de bibliotecas de cliente de software livre e exemplos que foram testados com o ponto de extremidade de plataforma do Microsoft identity.
* Para referência, o `.well-known` ponto de extremidade para o Microsoft identity platform ponto de extremidade comum é `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`. Substitua `common` pela ID do locatário para obter dados específicos para o locatário.  

### <a name="protocol-changes"></a>Alterações do protocolo

O ponto de extremidade de plataforma de identidade do Microsoft não oferece suporte a SAML ou WS-Federation; ele só dá suporte a OAuth 2.0 e OpenID Connect.  As alterações importantes para os protocolos OAuth 2.0 do ponto de extremidade v1.0 são: 

* A declaração `email` será retornada somente se uma declaração opcional estiver configurada **ou** scope=email tiver sido especificado na solicitação. 
* O parâmetro `scope` agora tem suporte em vez do `resource` parâmetro.  
* Muitas respostas foram modificadas para torná-lo mais compatível com a especificação do OAuth 2.0, por exemplo, retornando corretamente `expires_in` como um int em vez de uma cadeia de caracteres.  

Para entender melhor o escopo da funcionalidade de protocolo com suporte no ponto de extremidade de plataforma de identidade Microsoft, consulte [referência do protocolo OAuth 2.0 e OpenID Connect](active-directory-v2-protocols.md).

#### <a name="saml-restrictions"></a>Restrições de SAML

Se você já usou a biblioteca de autenticação do Active Directory (ADAL) em aplicativos do Windows, você poderá aproveitar da autenticação integrada do Windows, que usa a concessão de asserção da marcação linguagem SAML (Security Assertion). Com essa concessão, os usuários de locatários do Azure AD federados se autenticam com suas instâncias do Active Directory local sem inserir as credenciais. A concessão de asserção SAML não tem suporte no ponto de extremidade de plataforma de identidade Microsoft.
