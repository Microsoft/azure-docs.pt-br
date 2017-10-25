---
title: "Metadados de Federação do Azure AD | Microsoft Docs"
description: "Este artigo descreve o documento de metadados de federação que o Azure Active Directory publica para serviços que aceitam tokens do Azure Active Directory."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: c2d5f80b-aa74-452c-955b-d8eb3ed62652
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: ecafb02a6ac13d1c3cd1fe77ef710cd8525e32b0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="federation-metadata"></a>Metadados de federação
O Azure Active Directory (Azure AD) publica um documento de metadados federados para serviços que são configurados para aceitar os tokens de segurança que o Azure AD emite. O formato de documento de metadados federados é descrito no [Web Services Federation Language (WS-Federation) Version 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html), que se estende para [Metadata for the OASIS Security Assertion Markup Language (SAML) v2.0](http://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf).

## <a name="tenant-specific-and-tenant-independent-metadata-endpoints"></a>Pontos de extremidade de metadados específicos de locatário e independentes de locatário
O AD do Azure publica pontos de extremidade específicos de locatário e independentes de locatário.

Pontos de extremidade específicos de locatário destinam-se a um locatário específico. Os metadados de federação específicos de locatário incluem informações sobre o locatário, incluindo informações de emissor e o ponto de extremidade específico de locatário. Os aplicativos que restringem o acesso a um único locatário usam pontos de extremidade específicos de locatário.

Os pontos de extremidade independentes de locatário fornecem informações que são comuns para todos os locatários do AD do Azure. Essas informações se aplicam aos locatários hospedados em *login.microsoftonline.com* e são compartilhadas entre locatários. Os pontos de extremidade independentes de locatário são recomendados para aplicativos com vários locatários, pois eles não estão associados a qualquer locatário específico.

## <a name="federation-metadata-endpoints"></a>Pontos de extremidade de metadados de federação
O Azure AD publica metadados de federação em `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`.

Para **pontos de extremidade específicos de locatário**, o `TenantDomainName` pode ser um dos seguintes tipos:

* Um nome de domínio registrado de um locatário do Azure AD, como: `contoso.onmicrosoft.com`.
* A ID de locatário imutável do domínio, como `72f988bf-86f1-41af-91ab-2d7cd011db45`.

Para **pontos de extremidade independentes de locatário**, o `TenantDomainName` é `common`. Este documento lista apenas os elementos de Metadados de Federação que são comuns a todos os locatários do Azure AD hospedados em login.microsoftonline.com.

Por exemplo, um ponto de extremidade específico de locatário pode ser `https://login.microsoftonline.com/contoso.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml`. O ponto de extremidade independente de locatário é [https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml). Você pode exibir o documento de metadados de federação digitando essa URL em um navegador.

## <a name="contents-of-federation-metadata"></a>Conteúdo de metadados de federação
A seção a seguir fornece as informações necessárias para serviços que consomem os tokens emitidos pelo AD do Azure.

### <a name="entity-id"></a>ID da Entidade
O elemento `EntityDescriptor` contém um atributo `EntityID`. O valor do atributo `EntityID` representa o emissor, ou seja, o STS (serviço de token de segurança) que emitiu o token. É importante validar o emissor ao receber um token.

Os metadados a seguir mostram um exemplo de elemento `EntityDescriptor` específico de locatário com um elemento `EntityID`.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b"
entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">
```
Você pode substituir a ID de locatário no ponto de extremidade independente de locatário por sua ID de locatário para criar um valor `EntityID` específico de locatário. O valor resultante será igual ao emissor do token. Essa estratégia permite que um aplicativo multilocatário valide o emissor para determinado locatário.

Os metadados a seguir mostram um exemplo de elemento independente de locatário `EntityID` . Nesse elemento, observe que `{tenant}` é um literal, não um espaço reservado.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd"
entityID="https://sts.windows.net/{tenant}/">
```

### <a name="token-signing-certificates"></a>Certificados de autenticação de tokens
Quando um serviço recebe um token emitido por um locatário do AD do Azure, a assinatura do token deve ser validada com uma chave de assinatura publicada no documento de metadados de federação. Os metadados de federação incluem a parte pública dos certificados que os locatários usam para autenticação de tokens. Os bytes brutos do certificado aparecem no elemento `KeyDescriptor` . O certificado de assinatura de token é válido para a assinatura somente quando o valor do atributo `use` é `signing`.

Um documento de metadados de federação publicado pelo Azure AD pode ter várias chaves de assinatura, como quando o Azure AD está se preparando para atualizar o certificado de autenticação. Quando um documento de metadados de federação inclui mais de um certificado, um serviço que valida os tokens deve dar suporte a todos os certificados no documento.

Os metadados a seguir mostram um elemento de exemplo `KeyDescriptor` com uma chave de assinatura.

```
<KeyDescriptor use="signing">
<KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
<X509Data>
<X509Certificate>
MIIDPjCCAiqgAwIBAgIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTIwNjA3MDcwMDAwWhcNMTQwNjA3MDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEArCz8Sn3GGXmikH2MdTeGY1D711EORX/lVXpr+ecGgqfUWF8MPB07XkYuJ54DAuYT318+2XrzMjOtqkT94VkXmxv6dFGhG8YZ8vNMPd4tdj9c0lpvWQdqXtL1TlFRpD/P6UMEigfN0c9oWDg9U7Ilymgei0UXtf1gtcQbc5sSQU0S4vr9YJp2gLFIGK11Iqg4XSGdcI0QWLLkkC6cBukhVnd6BCYbLjTYy3fNs4DzNdemJlxGl8sLexFytBF6YApvSdus3nFXaMCtBGx16HzkK9ne3lobAwL2o79bP4imEGqg+ibvyNmbrwFGnQrBc1jTF9LyQX9q+louxVfHs6ZiVwIDAQABo2IwYDBeBgNVHQEEVzBVgBCxDDsLd8xkfOLKm4Q/SzjtoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAA4IBAQAkJtxxm/ErgySlNk69+1odTMP8Oy6L0H17z7XGG3w4TqvTUSWaxD4hSFJ0e7mHLQLQD7oV/erACXwSZn2pMoZ89MBDjOMQA+e6QzGB7jmSzPTNmQgMLA8fWCfqPrz6zgH+1F1gNp8hJY57kfeVPBiyjuBmlTEBsBlzolY9dd/55qqfQk6cgSeCbHCy/RU/iep0+UsRMlSgPNNmqhj5gmN2AFVCN96zF694LwuPae5CeR2ZcVknexOWHYjFM0MgUSw0ubnGl0h9AJgGyhvNGcjQqu9vd1xkupFgaN+f7P3p3EVN5csBg5H94jEcQZT7EKeTiZ6bTrpDAnrr8tDCy8ng
</X509Certificate>
</X509Data>
</KeyInfo>
</KeyDescriptor>
  ```

O elemento `KeyDescriptor` aparece em dois lugares no documento de metadados federados na seção específica do WS-Federation e na seção específica de SAML. Os certificados publicados em ambas as seções serão os mesmos.

Na seção específica do WS-Federation, um leitor de metadados de WS-Federation leria os certificados de um elemento `RoleDescriptor` com o tipo `SecurityTokenServiceType`.

Os metadados a seguir mostram um elemento `RoleDescriptor` de exemplo.

```
<RoleDescriptor xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:fed="http://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType"protocolSupportEnumeration="http://docs.oasis-open.org/wsfed/federation/200706">
```

Na seção específica de SAML, um leitor de metadados de WS-Federation leria os certificados de um `IDPSSODescriptor` elemento.

Os metadados a seguir mostram um elemento `IDPSSODescriptor` de exemplo.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```
Não há diferenças no formato de certificados específicos de locatário e independentes de locatário.

### <a name="ws-federation-endpoint-url"></a>URL de ponto de extremidade de WS-Federation
Os metadados de federação incluem a URL que usa o AD do Azure para logon único e saída única no protocolo WS-Federation. Esse ponto de extremidade é mostrado no `PassiveRequestorEndpoint` elemento.

Os metadados a seguir mostram um elemento `PassiveRequestorEndpoint` de exemplo para um ponto de extremidade específico de locatário.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```
Para o ponto de extremidade independente de locatário, a URL de WS-Federation aparece no ponto de extremidade WS-Federation, conforme mostrado no exemplo a seguir.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/common/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```

### <a name="saml-protocol-endpoint-url"></a>URL de ponto de extremidade do protocolo SAML
Os metadados de Federação incluem a URL que o AD do Azure usa para logon único e saída única no protocolo SAML 2.0. Esses pontos de extremidade aparecem no `IDPSSODescriptor` elemento.

As URLs de entrada e saída aparecem nos elementos `SingleSignOnService` e `SingleLogoutService`.

Os metadados a seguir mostram um `PassiveResistorEndpoint` de exemplo para um ponto de extremidade específico de locatário.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com /saml2" />
  </IDPSSODescriptor>
```

Da mesma forma, os pontos de extremidade para os pontos de extremidade de protocolo SAML 2.0 comuns são publicados nos metadados de federação independentes de locatário, conforme mostrado no exemplo a seguir.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
  </IDPSSODescriptor>
```
