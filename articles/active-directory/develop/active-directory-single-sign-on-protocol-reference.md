---
title: "Protocolo SAML de Logon Único do Azure | Microsoft Docs"
description: "Este artigo descreve o protocolo SAML de logon único no Azure Active Directory"
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: 
ms.assetid: ad8437f5-b887-41ff-bd77-779ddafc33fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: priyamo
ms.custom: aaddev
ms.openlocfilehash: f41402fc2cb282975b93071d998365fdb0a21941
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# Protocolo SAML de Logon Único
Este artigo trata das solicitações e respostas de autenticação SAML 2.0 a que o Azure AD (Azure Active Directory) dá suporte para Logon Único.

O diagrama de protocolo abaixo descreve a sequência de logon único. O serviço de nuvem (o provedor de serviço) usa uma associação de redirecionamento HTTP para passar um elemento `AuthnRequest` (solicitação de autenticação) para o Azure AD (o provedor de identidade). Em seguida, o Azure AD usa uma associação HTTP post a fim de postar um elemento `Response` para o serviço de nuvem.

![Fluxo de trabalho de Logon Único](media/active-directory-single-sign-on-protocol-reference/active-directory-saml-single-sign-on-workflow.png)

## AuthnRequest
Para solicitar uma autenticação de usuário, os serviços de nuvem enviam um elemento `AuthnRequest` ao Azure AD. Um exemplo de SAML 2.0 `AuthnRequest` poderia ter esta aparência:

```
<samlp:AuthnRequest
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="id6c1c178c166d486687be4aaf5e482730"
Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
</samlp:AuthnRequest>
```


| Parâmetro |  | Descrição |
| --- | --- | --- |
| ID |obrigatório |O Azure AD usa esse atributo para popular o atributo `InResponseTo` da resposta retornada. A ID não deve começar com um número. Uma estratégia comum é anexar uma cadeia de caracteres como "id" à representação de cadeia de caracteres de um GUID. Por exemplo, `id6c1c178c166d486687be4aaf5e482730` é uma ID válida. |
| Versão |obrigatório |Ele deve ser **2.0**. |
| IssueInstant |obrigatório |Isso é uma cadeia de caracteres DateTime com um valor de UTC e [formato de ida e volta ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). O Azure AD espera um valor DateTime desse tipo, mas não avalia ou usa o valor. |
| AssertionConsumerServiceUrl |opcional |Se fornecido, ele deve corresponder ao `RedirectUri` do serviço de nuvem no Azure AD. |
| ForceAuthn |opcional | Esse é um valor booliano. Se for true, isso significa que o usuário será forçado a autenticar novamente, mesmo que ele tenha uma sessão válida no Azure AD. |
| IsPassive |opcional |Esse é um valor booliano que especifica se o Azure AD deve autenticar o usuário silenciosamente, sem a interação do usuário, usando o cookie da sessão, se existir. Se for true, o Azure AD tentará autenticar o usuário usando o cookie da sessão. |

Todos os outros atributos `AuthnRequest` , como Consent, Destination, AssertionConsumerServiceIndex, AttributeConsumerServiceIndex e ProviderName são **ignorados**.

O Azure AD também ignora o elemento `Conditions` na `AuthnRequest`.

### Emissor
O elemento `Issuer` em uma `AuthnRequest` deve corresponder exatamente a um dos **ServicePrincipalNames** no serviço de nuvem no Azure AD. Normalmente, isso é definido como o **URI da ID do aplicativo** que é especificado durante o registro do aplicativo.

Um trecho de exemplo de SAML contendo o elemento `Issuer` tem esta aparência:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### NameIDPolicy
Esse elemento solicita um formato de ID de nome específico na resposta e é opcional nos elementos `AuthnRequest` enviados ao Azure AD.

Um elemento de exemplo `NameIdPolicy` tem esta aparência:

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

Se `NameIDPolicy` for fornecido, você poderá incluir seu atributo `Format` opcional. O atributo `Format` pode ter apenas um dos valores a seguir. Qualquer outro valor resulta em um erro.

* `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`: o Azure Active Directory emite a declaração NameID como um identificador de par.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`: o Azure Active Directory emite a declaração NameID no formato de endereço de email.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`: esse valor permite que o Azure Active Directory selecione o formato de declaração. O Azure Active Directory emite o NameID como um identificador de par.
* `urn:oasis:names:tc:SAML:2.0:nameid-format:transient`: o Azure Active Directory emite a declaração NameID como um valor gerado aleatoriamente que é exclusivo à operação de SSO atual. Isso significa que o valor é temporário e não pode ser usado para identificar o usuário da autenticação.

O Azure AD ignora o atributo `AllowCreate` .

### RequestAuthnContext
O elemento `RequestedAuthnContext` especifica os métodos de autenticação desejados. É opcional nos elementos `AuthnRequest` enviados ao Azure AD. O Azure AD dá suporte a apenas um valor `AuthnContextClassRef`: `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`.

### Scoping
O elemento `Scoping`, que inclui uma lista de provedores de identidade, é opcional em `AuthnRequest` enviadas ao Azure AD.

Se fornecido, não inclua o atributo `ProxyCount` ou elemento `IDPListOption` ou `RequesterID`, pois eles não têm suporte.

### Signature
Não inclua um elemento `Signature` nos elementos `AuthnRequest`, pois o Azure AD não dá suporte a solicitações de autenticação assinadas.

### Subject
O Azure AD ignora o elemento `Subject` dos elementos `AuthnRequest`.

## Response
Quando um logon solicitado for concluído com êxito, o Azure AD postará uma resposta no serviço de nuvem. Um exemplo de resposta a uma tentativa de logon bem-sucedida tem esta aparência:

```
<samlp:Response ID="_a4958bfd-e107-4e67-b06d-0d85ade2e76a" Version="2.0" IssueInstant="2013-03-18T07:38:15.144Z" Destination="https://contoso.com/identity/inboundsso.aspx" InResponseTo="id758d0ef385634593a77bdf7e632984b6" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
    ...
  </ds:Signature>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
  <Assertion ID="_bf9c623d-cc20-407a-9a59-c2d0aee84d12" IssueInstant="2013-03-18T07:38:15.144Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
    <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
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

### Response
O elemento `Response` inclui os resultados da solicitação de autorização. O Azure AD define os valores `ID`, `Version` e `IssueInstant` no elemento `Response`. Ele também define os seguintes atributos:

* `Destination`: quando o logon for concluído com êxito, ele será definido como o `RedirectUri` do provedor de serviços (serviço de nuvem).
* `InResponseTo`: isso é definido como o atributo `ID` do elemento `AuthnRequest` que iniciou a resposta.

### Issuer
O Azure AD define o elemento `Issuer` como `https://login.microsoftonline.com/<TenantIDGUID>/`, em que <TenantIDGUID> é a ID de locatário do locatário do Azure AD.

Por exemplo, uma resposta de exemplo com o elemento Issuer poderia ser assim:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### Status
O elemento `Status` transmite o êxito ou a falha de logon. Ele inclui o elemento `StatusCode` , que contém um código ou um conjunto de códigos aninhados que representam o status da solicitação. Ele também inclui o elemento `StatusMessage` , que contém mensagens de erro personalizadas que são geradas durante o processo de logon.

<!-- TODO: Add a authentication protocol error reference -->

A seguir temos uma resposta SAML para uma tentativa de logon com falha.

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

### Asserção
Além de `ID`, `IssueInstant` e `Version`, o Azure AD define os elementos a seguir no elemento `Assertion` da resposta.

#### Emissor
Isso é definido como `https://sts.windows.net/<TenantIDGUID>/`, em que <TenantIDGUID> é a ID de locatário do locatário do Azure AD.

```
<Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### Signature
O Azure AD assina a asserção em resposta a um logon bem-sucedido. O elemento `Signature` contém uma assinatura digital que o serviço de nuvem pode usar para autenticar e verificar a integridade da asserção.

Para gerar a assinatura digital, o Azure AD usa a chave de assinatura no elemento `IDPSSODescriptor` do documento de metadados.

```
<ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### Subject
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

#### Conditions
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

#### Audience
Ele contém um URI que identifica um público-alvo. O Azure AD define o valor desse elemento como o valor do elemento `Issuer` da `AuthnRequest` que iniciou o logon. Para avaliar o valor `Audience`, use o valor do `App ID URI` que foi especificado durante o registro do aplicativo.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

Como o valor `Issuer`, o valor `Audience` deve corresponder exatamente a um dos nomes de entidade de serviço que representa o serviço de nuvem no Azure AD. No entanto, se o valor do elemento `Issuer` não é um valor URI, o valor `Audience` na resposta é o `Issuer` valor prefixado com `spn:`.

#### AttributeStatement
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
* **Declaração ObjectIdentifier**: o valor do atributo `ObjectIdentifier` (`http://schemas.microsoft.com/identity/claims/objectidentifier`) é o `ObjectId` do objeto de diretório representando o usuário autenticado no Azure AD. `ObjectId` é um identificador seguro globalmente exclusivo, imutável e reutilizável do usuário autenticado.

#### AuthnStatement
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