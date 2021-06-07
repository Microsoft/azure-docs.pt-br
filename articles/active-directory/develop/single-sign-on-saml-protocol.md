---
title: Protocolo SAML de Logon Único do Azure
titleSuffix: Microsoft identity platform
description: Este artigo descreve o protocolo SAML de logon único (SSO) no Azure Active Directory
services: active-directory
documentationcenter: .net
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: kenwith
ms.custom: aaddev
ms.reviewer: paulgarn
ms.openlocfilehash: 40bf202e0f14f18d817e4e918f8372ba3c0a4ad8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91950662"
---
# <a name="single-sign-on-saml-protocol"></a>Protocolo SAML de Logon Único

Este artigo trata das solicitações e respostas de autenticação SAML 2.0 com as quais o Azure Active Directory permite Logon Único (SSO).

O diagrama de protocolo abaixo descreve a sequência de logon único. O serviço de nuvem (o provedor de serviço) usa uma associação de redirecionamento HTTP para passar um elemento `AuthnRequest` (solicitação de autenticação) para o Azure AD (o provedor de identidade). Em seguida, o Azure AD usa uma associação HTTP post a fim de postar um elemento `Response` para o serviço de nuvem.

![Fluxo de trabalho de logon único (SSO)](./media/single-sign-on-saml-protocol/active-directory-saml-single-sign-on-workflow.png)

> [!NOTE]
> Este artigo aborda o uso do SAML para logon único. Para obter mais informações sobre outras maneiras de lidar com o logon único (por exemplo, usando o OpenID Connect ou a autenticação integrada do Windows), consulte [Logon único para aplicativos no Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="authnrequest"></a>AuthnRequest

Para solicitar uma autenticação de usuário, os serviços de nuvem enviam um elemento `AuthnRequest` ao Azure AD. Um exemplo de SAML 2.0 `AuthnRequest` teria a seguinte a aparência:

```
<samlp:AuthnRequest
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="id6c1c178c166d486687be4aaf5e482730"
Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
</samlp:AuthnRequest>
```

| Parâmetro | Tipo | Descrição |
| --- | --- | --- |
| ID | Obrigatório | O Azure AD usa esse atributo para popular o atributo `InResponseTo` da resposta retornada. A ID não deve começar com um número. Uma estratégia comum é anexar uma cadeia de caracteres como "id" à representação de cadeia de caracteres de um GUID. Por exemplo, `id6c1c178c166d486687be4aaf5e482730` é uma ID válida. |
| Versão | Obrigatório | Esse parâmetro deve ser definido como **2.0**. |
| IssueInstant | Obrigatório | Isso é uma cadeia de caracteres DateTime com um valor de UTC e [formato de ida e volta ("o")](/dotnet/standard/base-types/standard-date-and-time-format-strings). O Azure AD espera um valor DateTime desse tipo, mas não avalia ou usa o valor. |
| AssertionConsumerServiceURL | Opcional | Se fornecido, esse parâmetro deve corresponder ao `RedirectUri` do serviço de nuvem no Azure AD. |
| ForceAuthn | Opcional | Esse é um valor booliano. Se for true, isso significa que o usuário será forçado a autenticar novamente, mesmo que ele tenha uma sessão válida no Azure AD. |
| IsPassive | Opcional | Esse é um valor booliano que especifica se o Azure AD deve autenticar o usuário silenciosamente, sem a interação do usuário, usando o cookie da sessão, se existir. Se for true, o Azure AD tentará autenticar o usuário usando o cookie da sessão. |

Todos os outros atributos `AuthnRequest`, como Consent, Destination, AssertionConsumerServiceIndex, AttributeConsumerServiceIndex e ProviderName são **ignorados**.

O Azure AD também ignora o elemento `Conditions` na `AuthnRequest`.

### <a name="issuer"></a>Emissor

O elemento `Issuer` em uma `AuthnRequest` deve corresponder exatamente a um dos **ServicePrincipalNames** no serviço de nuvem no Azure AD. Normalmente, isso é definido como o **URI da ID do aplicativo** que é especificado durante o registro do aplicativo.

Um trecho de SAML contendo o elemento `Issuer` se parece com o exemplo a seguir:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### <a name="nameidpolicy"></a>NameIDPolicy

Esse elemento solicita um formato de ID de nome específico na resposta e é opcional nos elementos `AuthnRequest` enviados ao Azure AD.

Um elemento `NameIdPolicy` se parece com o seguinte exemplo:

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

Se `NameIDPolicy` for fornecido, você poderá incluir seu atributo `Format` opcional. O atributo `Format` pode ter apenas um dos valores a seguir. Qualquer outro valor resulta em um erro.

* `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`: o Azure Active Directory emite a declaração NameID como um identificador de par.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`: o Azure Active Directory emite a declaração NameID no formato de endereço de e-mail.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`: esse valor permite que o Azure Active Directory selecione o formato de declaração. O Azure Active Directory emite o NameID como um identificador de par.
* `urn:oasis:names:tc:SAML:2.0:nameid-format:transient`: o Azure Active Directory emite a declaração NameID como um valor gerado aleatoriamente que é exclusivo à operação de SSO atual. Isso significa que o valor é temporário e não pode ser usado para identificar o usuário da autenticação.

Se `SPNameQualifier` for especificado, o AD do Azure incluirá o mesmo `SPNameQualifier` na resposta.

O Azure AD ignora o atributo `AllowCreate` .

### <a name="requestauthncontext"></a>RequestAuthnContext
O elemento `RequestedAuthnContext` especifica os métodos de autenticação desejados. É opcional nos elementos `AuthnRequest` enviados ao Azure AD. O Azure Active Directory permite valores `AuthnContextClassRef`, como `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`.

### <a name="scoping"></a>Scoping
O elemento `Scoping`, que inclui uma lista de provedores de identidade, é opcional em `AuthnRequest` enviadas ao Azure AD.

Se fornecido, não inclua o atributo `ProxyCount` ou elemento `IDPListOption` ou `RequesterID`, pois eles não têm suporte.

### <a name="signature"></a>Assinatura
Um `Signature` elemento em `AuthnRequest` elementos é opcional. O Azure AD não valida solicitações de autenticação assinadas se uma assinatura estiver presente. A verificação do solicitante é fornecida apenas respondendo a URLs registradas do Serviço do Consumidor de Declaração.

### <a name="subject"></a>Assunto
Não inclua um elemento `Subject`. O Azure Active Directory não é compatível com a especificação de um assunto para uma solicitação e retornará um erro se uma especificação for fornecida.

## <a name="response"></a>Resposta
Quando um logon solicitado for concluído com êxito, o Azure AD postará uma resposta no serviço de nuvem. Uma resposta para uma tentativa de logon bem-sucedida se parece com este exemplo:

```
<samlp:Response ID="_a4958bfd-e107-4e67-b06d-0d85ade2e76a" Version="2.0" IssueInstant="2013-03-18T07:38:15.144Z" Destination="https://contoso.com/identity/inboundsso.aspx" InResponseTo="id758d0ef385634593a77bdf7e632984b6" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
    ...
  </ds:Signature>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
  <Assertion ID="_bf9c623d-cc20-407a-9a59-c2d0aee84d12" IssueInstant="2013-03-18T07:38:15.144Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
    <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      ...
    </ds:Signature>
    <Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
  </Assertion>
</samlp:Response>
```

### <a name="response"></a>Resposta

O elemento `Response` inclui os resultados da solicitação de autorização. O Azure AD define os valores `ID`, `Version` e `IssueInstant` no elemento `Response`. Ele também define os seguintes atributos:

* `Destination`: quando o logon for concluído com êxito, ele será definido como o `RedirectUri` do provedor de serviços (serviço de nuvem).
* `InResponseTo`: isso é definido como o atributo `ID` do elemento `AuthnRequest` que iniciou a resposta.

### <a name="issuer"></a>Emissor

O Azure AD define o elemento `Issuer` como `https://sts.windows.net/<TenantIDGUID>/`, em que \<TenantIDGUID> é a ID de locatário do locatário do Azure AD.

Por exemplo, uma resposta com o elemento Issuer poderia ter a aparência deste exemplo:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### <a name="status"></a>Status

O elemento `Status` transmite o êxito ou a falha de logon. Ele inclui o elemento `StatusCode`, que contém um código ou um conjunto de códigos aninhados que representam o status da solicitação. Ele também inclui o elemento `StatusMessage` , que contém mensagens de erro personalizadas que são geradas durante o processo de logon.

<!-- TODO: Add an authentication protocol error reference -->

O exemplo a seguir é uma resposta SAML para uma tentativa de logon com falha.

```
<samlp:Response ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Requester">
      <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:RequestUnsupported" />
    </samlp:StatusCode>
    <samlp:StatusMessage>AADSTS75006: An error occurred while processing a SAML2 Authentication request. AADSTS90011: The SAML authentication request property 'NameIdentifierPolicy/SPNameQualifier' is not supported.
Trace ID: 66febed4-e737-49ff-ac23-464ba090d57c
Timestamp: 2013-03-18 08:49:24Z</samlp:StatusMessage>
  </samlp:Status>
```

### <a name="assertion"></a>Asserção

Além de `ID`, `IssueInstant` e `Version`, o Azure AD define os elementos a seguir no elemento `Assertion` da resposta.

#### <a name="issuer"></a>Emissor

Isso é definido como `https://sts.windows.net/<TenantIDGUID>/`, em que \<TenantIDGUID> é a ID de locatário do locatário do Azure AD.

```
<Issuer>https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### <a name="signature"></a>Assinatura

O Azure AD assina a asserção em resposta a um logon bem-sucedido. O elemento `Signature` contém uma assinatura digital que o serviço de nuvem pode usar para autenticar e verificar a integridade da asserção.

Para gerar a assinatura digital, o Azure AD usa a chave de assinatura no elemento `IDPSSODescriptor` do documento de metadados.

```
<ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### <a name="subject"></a>Assunto

Ele especifica a entidade que é o assunto das instruções na asserção. Ele contém um elemento `NameID` que representa o usuário autenticado. O valor `NameID` é um identificador de destino que é direcionado somente para o provedor de serviços que é o público-alvo do token. É persistente - pode ser revogado, mas nunca é reatribuído. Ele também é opaco, no sentido de não revelar nada sobre o usuário e não poder ser usado como um identificador para consultas de atributo.

O atributo `Method` do elemento `SubjectConfirmation` é sempre definido como `urn:oasis:names:tc:SAML:2.0:cm:bearer`.

```
<Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
</Subject>
```

#### <a name="conditions"></a>Condições

Esse elemento especifica as condições que definem o uso aceitável de asserções SAML.

```
<Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
</Conditions>
```

Os atributos `NotBefore` e `NotOnOrAfter` especificam o intervalo durante o qual a asserção é válida.

* O valor do atributo `NotBefore` é igual ou ligeiramente mais lento (menos de um segundo) do que o valor do atributo `IssueInstant` do elemento `Assertion`. O Azure AD não conta diferenças de tempo entre ele mesmo e o serviço de nuvem (provedor de serviços) e não adiciona buffer a esse tempo.
* O valor do atributo `NotOnOrAfter` é 70 minutos depois do valor do atributo `NotBefore`.

#### <a name="audience"></a>Público

Ele contém um URI que identifica um público-alvo. O Azure AD define o valor desse elemento como o valor do elemento `Issuer` da `AuthnRequest` que iniciou o logon. Para avaliar o valor `Audience`, use o valor do `App ID URI` que foi especificado durante o registro do aplicativo.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

Como o valor `Issuer`, o valor `Audience` deve corresponder exatamente a um dos nomes de entidade de serviço que representa o serviço de nuvem no Azure AD. No entanto, se o valor do elemento `Issuer` não é um valor URI, o valor `Audience` na resposta é o `Issuer` valor prefixado com `spn:`.

#### <a name="attributestatement"></a>AttributeStatement

Ele contém declarações sobre o assunto ou o usuário. O trecho a seguir contém um exemplo de elemento `AttributeStatement` . As reticências indicam que o elemento pode incluir vários atributos e valores de atributo.

```
<AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
</AttributeStatement>
```        

* **Declaração de Nome**: o valor do atributo `Name` (`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`) é o nome UPN do usuário autenticado, como `testuser@managedtenant.com`.
* **Declaração ObjectIdentifier**: o valor do atributo `ObjectIdentifier` (`http://schemas.microsoft.com/identity/claims/objectidentifier`) é o `ObjectId` do objeto do directory representando o usuário autenticado no Azure AD. `ObjectId` é um identificador seguro globalmente exclusivo, imutável e reutilizável do usuário autenticado.

#### <a name="authnstatement"></a>AuthnStatement

Esse elemento declara que o assunto de asserção foi autenticado por um meio específico em determinado momento.

* O atributo `AuthnInstant` especifica a hora em que o usuário autenticou com o Azure AD.
* O elemento `AuthnContext` especifica o contexto de autenticação usado para autenticar o usuário.

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```
