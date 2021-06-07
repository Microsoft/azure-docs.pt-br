---
title: Referência de declarações de token 2,0 SAML | Azure
titleSuffix: Microsoft identity platform
description: Referência de declarações com detalhes sobre as declarações incluídas nos tokens SAML 2,0 emitidos pela plataforma de identidade da Microsoft, incluindo seus equivalentes de JWT.
author: kenwith
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 09/09/2020
ms.author: kenwith
ms.reviewer: paulgarn
ms.custom: aaddev
ms.openlocfilehash: 9c3132985866a4c245984ef632107c05ca1b3350
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96348375"
---
# <a name="saml-token-claims-reference"></a>Referência de declarações de token SAML

A plataforma de identidade da Microsoft emite vários tipos de tokens de segurança no processamento de cada fluxo de autenticação. Este documento descreve o formato, as características de segurança e o conteúdo dos tokens SAML 2,0.

## <a name="claims-in-saml-tokens"></a>Declarações em tokens SAML

> [!div class="mx-codeBreakAll"]
> | Nome | Declaração JWT Equivalente | Descrição | Exemplo |
> | --- | --- | --- | ------------|
> |Público | `aud` |O destinatário pretendido do token. O aplicativo que recebe o token deve verificar se o valor de público-alvo está correto e rejeitar quaisquer tokens destinados a um público-alvo diferente. | `<AudienceRestriction>`<br>`<Audience>`<br>`https://contoso.com`<br>`</Audience>`<br>`</AudienceRestriction>`  |
> | Instante da autenticação | |Registra a data e o horário em que ocorreu a autenticação. | `<AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">` |
> |Método de autenticação | `amr` |Identifica como o assunto do token foi autenticado. | `<AuthnContextClassRef>`<br>`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password`<br>`</AuthnContextClassRef>` |
> |Nome | `given_name` |Fornece o nome ou nome “determinado” do usuário, como definido no objeto de usuário do Azure AD. | `<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">`<br>`<AttributeValue>Frank<AttributeValue>`  |
> |Grupos | `groups` |Fornece IDs de objetos que representam as associações de grupo do assunto. Esses valores são exclusivos (consulte a ID de objeto) e podem ser usados com segurança para gerenciar o acesso, como a imposição da autorização para acessar um recurso. Os grupos incluídos na declaração grupos são configurados por aplicativo, por meio da propriedade "groupMembershipClaims" do manifesto do aplicativo. Um valor de NULL excluirá todos os grupos, um valor de "grupo de segurança" incluirá apenas Active Directory associações de grupos de segurança, e um valor de "All" incluirá os grupos de segurança e as listas de distribuição de Microsoft 365. <br><br> **Observações**: <br> Se o número de grupos a que o usuário pertence ultrapassar determinado limite (150 para SAML, 200 para JWT), será adicionada uma declaração excedente às fontes de declaração que apontam para o ponto de extremidade do Graph que contém a lista de grupos do usuário. | `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">`<br>`<AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>` |
> | Indicador de excedente de grupos | `groups:src1` | Para solicitações de token que não são limitadas por comprimento, mas que ainda são muito grandes para o token, um link para a lista de grupos completos do usuário será incluído. Para o SAML, isso é adicionado como uma nova declaração no lugar da declaração `groups`. | `<Attribute Name=" http://schemas.microsoft.com/claims/groups.link">`<br>`<AttributeValue>https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects<AttributeValue>` |
> |Provedor de identidade | `idp` |Registra o provedor de identidade que autenticou a entidade do token. Esse valor é idêntico ao valor da declaração Emissor, a menos que a conta de usuário esteja em um locatário diferente que o emissor. | `<Attribute Name=" http://schemas.microsoft.com/identity/claims/identityprovider">`<br>`<AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>` |
> |IssuedAt | `iat` |Armazena a hora em que o token foi emitido. Ela geralmente é usada para mensurar o quanto o token é recente. | `<Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">` |
> |Emissor | `iss` |Identifica o STS (serviço de token de segurança) que constrói e retorna o token. Nos tokens que o AD do Azure retorna, o emissor é sts.windows.net. A GUID no valor de declaração do emissor é a ID do locatário do diretório do AD do Azure. A ID do locatário é um identificador imutável e confiável do diretório. | `<Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>` |
> |Sobrenome | `family_name` |Fornece o último nome, o sobrenome ou o nome da família do usuário conforme definido no objeto de usuário do Azure AD. | `<Attribute Name=" http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">`<br>`<AttributeValue>Miller<AttributeValue>` |
> |Nome | `unique_name` |Fornece um valor legível que identifica a entidade do token. Não há garantia de que esse valor seja exclusivo dentro de um locatário e ele é projetado para ser usado apenas para fins de exibição. | `<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">`<br>`<AttributeValue>frankm@contoso.com<AttributeValue>`|
> |ID de objeto | `oid` |Contém um identificador único de um objeto no Azure AD. Esse valor é imutável e não pode ser reatribuído nem reutilizado. Use a ID de objeto para identificar um objeto em consultas ao AD do Azure. | `<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">`<br>`<AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>` |
> |Funções | `roles` |Representa todas as funções de aplicativo que a entidade recebeu direta e indiretamente por meio da associação a um grupo e pode ser usado para impor o controle de acesso baseado em função. As funções de aplicativo são definidas segundo o aplicativo, por meio da propriedade `appRoles` do manifesto do aplicativo. A propriedade `value` de cada função de aplicativo é o valor que aparece na declaração de funções. | `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">`|
> |Assunto | `sub` |Identifica o item mais importante sobre o qual o token declara informações, como o usuário de um aplicativo. Esse valor é imutável e não pode ser reatribuído ou reutilizado, então ele pode ser usado para executar verificações de autorização com segurança. Como o assunto está sempre presente nos tokens emitidos pelo Azure AD, é recomendável usar esse valor em um sistema de autorização de uso geral. <br> `SubjectConfirmation` não é uma declaração. Ele descreve como o assunto do token é verificado. `Bearer` indica que o assunto é confirmado pela posse do token. | `<Subject>`<br>`<NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>`<br>`<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />`<br>`</Subject>`|
> |ID do locatário | `tid` |Um identificador imutável, não reutilizável que identifica o locatário do diretório que emitiu o token. Você pode usar esse valor para acessar os recursos de diretório específicos de um determinado locatário em um aplicativo multilocatário. Por exemplo, você pode usar esse valor para identificar o locatário em uma chamada à API do Graph. | `<Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">`<br>`<AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>`|
> |Tempo de vida do token | `nbf`, `exp` |Define o intervalo de tempo no qual um token é válido. O serviço que valida o token deve verificar se a data atual está dentro do tempo de vida do token; se não estiver, ele deverá rejeitar o token. O serviço pode fornecer uma margem de cinco minutos além do intervalo de tempo de vida do token para levar em conta as diferenças na hora do relógio ("distorção de tempo") entre o AD do Azure e o serviço. | `<Conditions`<br>`NotBefore="2013-03-18T21:32:51.261Z"`<br>`NotOnOrAfter="2013-03-18T22:32:51.261Z"`<br>`>` <br>|

## <a name="sample-saml-token"></a>Exemplo de Token de SAML

Este é um exemplo de um token SAML típico.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<t:RequestSecurityTokenResponse xmlns:t="http://schemas.xmlsoap.org/ws/2005/02/trust">
    <t:Lifetime>
        <wsu:Created xmlns:wsu="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T05:15:47.060Z</wsu:Created>
        <wsu:Expires xmlns:wsu="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T06:15:47.060Z</wsu:Expires>
    </t:Lifetime>
    <wsp:AppliesTo xmlns:wsp="http://schemas.xmlsoap.org/ws/2004/09/policy">
        <EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
            <Address>https://contoso.onmicrosoft.com/MyWebApp</Address>
        </EndpointReference>
    </wsp:AppliesTo>
    <t:RequestedSecurityToken>
        <Assertion xmlns="urn:oasis:names:tc:SAML:2.0:assertion" ID="_3ef08993-846b-41de-99df-b7f3ff77671b" IssueInstant="2014-12-24T05:20:47.060Z" Version="2.0">
            <Issuer>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</Issuer>
            <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
                <ds:SignedInfo>
                    <ds:CanonicalizationMethod Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
                    <ds:SignatureMethod Algorithm="https://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
                    <ds:Reference URI="#_3ef08993-846b-41de-99df-b7f3ff77671b">
                        <ds:Transforms>
                            <ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature" />
                            <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
                        </ds:Transforms>
                        <ds:DigestMethod Algorithm="https://www.w3.org/2001/04/xmlenc#sha256" />
                        <ds:DigestValue>cV1J580U1pD24hEyGuAxrbtgROVyghCqI32UkER/nDY=</ds:DigestValue>
                    </ds:Reference>
                </ds:SignedInfo>
                <ds:SignatureValue>j+zPf6mti8Rq4Kyw2NU2nnu0pbJU1z5bR/zDaKaO7FCTdmjUzAvIVfF8pspVR6CbzcYM3HOAmLhuWmBkAAk6qQUBmKsw+XlmF/pB/ivJFdgZSLrtlBs1P/WBV3t04x6fRW4FcIDzh8KhctzJZfS5wGCfYw95er7WJxJi0nU41d7j5HRDidBoXgP755jQu2ZER7wOYZr6ff+ha+/Aj3UMw+8ZtC+WCJC3yyENHDAnp2RfgdElJal68enn668fk8pBDjKDGzbNBO6qBgFPaBT65YvE/tkEmrUxdWkmUKv3y7JWzUYNMD9oUlut93UTyTAIGOs5fvP9ZfK2vNeMVJW7Xg==</ds:SignatureValue>
                <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
                    <X509Data>
                        <X509Certificate>MIIDPjCCAabcAwIBAgIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTQwMTAxMDcwMDAwWhcNMTYwMTAxMDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAkSCWg6q9iYxvJE2NIhSyOiKvqoWCO2GFipgH0sTSAs5FalHQosk9ZNTztX0ywS/AHsBeQPqYygfYVJL6/EgzVuwRk5txr9e3n1uml94fLyq/AXbwo9yAduf4dCHTP8CWR1dnDR+Qnz/4PYlWVEuuHHONOw/blbfdMjhY+C/BYM2E3pRxbohBb3x//CfueV7ddz2LYiH3wjz0QS/7kjPiNCsXcNyKQEOTkbHFi3mu0u13SQwNddhcynd/GTgWN8A+6SN1r4hzpjFKFLbZnBt77ACSiYx+IHK4Mp+NaVEi5wQtSsjQtI++XsokxRDqYLwus1I1SihgbV/STTg5enufuwIDAQABo2IwYDBeBgNVHQEEVzBVgBDLebM6bK3BjWGqIBrBNFeNoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAA4IBAQCJ4JApryF77EKC4zF5bUaBLQHQ1PNtA1uMDbdNVGKCmSp8M65b8h0NwlIjGGGy/unK8P6jWFdm5IlZ0YPTOgzcRZguXDPj7ajyvlVEQ2K2ICvTYiRQqrOhEhZMSSZsTKXFVwNfW6ADDkN3bvVOVbtpty+nBY5UqnI7xbcoHLZ4wYD251uj5+lo13YLnsVrmQ16NCBYq2nQFNPuNJw6t3XUbwBHXpF46aLT1/eGf/7Xx6iy8yPJX4DyrpFTutDz882RWofGEO5t4Cw+zZg70dJ/hH/ODYRMorfXEW+8uKmXMKmX2wyxMKvfiPbTy5LmAU8Jvjs2tLg4rOBcXWLAIarZ</X509Certificate>
                    </X509Data>
                </KeyInfo>
            </ds:Signature>
            <Subject>
                <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">m_H3naDei2LNxUmEcWd0BZlNi_jVET1pMLR6iQSuYmo</NameID>
                <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
            </Subject>
            <Conditions NotBefore="2014-12-24T05:15:47.060Z" NotOnOrAfter="2014-12-24T06:15:47.060Z">
                <AudienceRestriction>
                    <Audience>https://contoso.onmicrosoft.com/MyWebApp</Audience>
                </AudienceRestriction>
            </Conditions>
            <AttributeStatement>
                <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
                    <AttributeValue>a1addde8-e4f9-4571-ad93-3059e3750d23</AttributeValue>
                </Attribute>
                <Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">
                    <AttributeValue>b9411234-09af-49c2-b0c3-653adc1f376e</AttributeValue>
                </Attribute>
                <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
                    <AttributeValue>sample.admin@contoso.onmicrosoft.com</AttributeValue>
                </Attribute>
                <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">
                    <AttributeValue>Admin</AttributeValue>
                </Attribute>
                <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">
                    <AttributeValue>Sample</AttributeValue>
                </Attribute>
                <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
                    <AttributeValue>5581e43f-6096-41d4-8ffa-04e560bab39d</AttributeValue>
                    <AttributeValue>07dd8a89-bf6d-4e81-8844-230b77145381</AttributeValue>
                    <AttributeValue>0e129f4g-6b0a-4944-982d-f776000632af</AttributeValue>
                    <AttributeValue>3ee07328-52ef-4739-a89b-109708c22fb5</AttributeValue>
                    <AttributeValue>329k14b3-1851-4b94-947f-9a4dacb595f4</AttributeValue>
                    <AttributeValue>6e32c650-9b0a-4491-b429-6c60d2ca9a42</AttributeValue>
                    <AttributeValue>f3a169a7-9a58-4e8f-9d47-b70029v07424</AttributeValue>
                    <AttributeValue>8e2c86b2-b1ad-476d-9574-544d155aa6ff</AttributeValue>
                    <AttributeValue>1bf80264-ff24-4866-b22c-6212e5b9a847</AttributeValue>
                    <AttributeValue>4075f9c3-072d-4c32-b542-03e6bc678f3e</AttributeValue>
                    <AttributeValue>76f80527-f2cd-46f4-8c52-8jvd8bc749b1</AttributeValue>
                    <AttributeValue>0ba31460-44d0-42b5-b90c-47b3fcc48e35</AttributeValue>
                    <AttributeValue>edd41703-8652-4948-94a7-2d917bba7667</AttributeValue>
                </Attribute>
                <Attribute Name="http://schemas.microsoft.com/identity/claims/identityprovider">
                    <AttributeValue>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</AttributeValue>
                </Attribute>
            </AttributeStatement>
            <AuthnStatement AuthnInstant="2014-12-23T18:51:11.000Z">
                <AuthnContext>
                    <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
                </AuthnContext>
            </AuthnStatement>
        </Assertion>
    </t:RequestedSecurityToken>
    <t:RequestedAttachedReference>
        <SecurityTokenReference xmlns="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="https://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
            <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
    </t:RequestedAttachedReference>
    <t:RequestedUnattachedReference>
        <SecurityTokenReference xmlns="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="https://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
            <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
    </t:RequestedUnattachedReference>
    <t:TokenType>http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0</t:TokenType>
    <t:RequestType>http://schemas.xmlsoap.org/ws/2005/02/trust/Issue</t:RequestType>
    <t:KeyType>http://schemas.xmlsoap.org/ws/2005/05/identity/NoProofKey</t:KeyType>
</t:RequestSecurityTokenResponse>
```

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como gerenciar a política de tempo de vida de token usando a API de Microsoft Graph, consulte [visão geral de recursos de política do Azure ad](/graph/api/resources/policy).
* Adicione [declarações opcionais e personalizadas](active-directory-optional-claims.md) para os tokens do aplicativo.
* Use [Sign-On único (SSO) com SAML](single-sign-on-saml-protocol.md).
* Usar o [protocolo SAML de Sign-Out único do Azure](single-sign-out-saml-protocol.md)
