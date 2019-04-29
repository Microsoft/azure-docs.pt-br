---
title: Configurar uma política de autorização de chave de conteúdo usando REST - Azure | Microsoft Docs
description: Saiba como configurar uma política de autorização para uma chave de conteúdo usando a API REST dos Serviços de Mídia.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 7af5f9e2-8ed8-43f2-843b-580ce8759fd4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: cb9b784e612b1dca6b5251cb5a20140e908158c4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60598433"
---
# <a name="dynamic-encryption-configure-a-content-key-authorization-policy"></a>Criptografia dinâmica: Configurar uma política de autorização de chave de conteúdo  
[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Visão geral
 Você pode usar os Serviços de Mídia do Microsoft Azure para distribuir o conteúdo criptografado (dinamicamente) com a criptografia AES (usando chaves de criptografia de 128 bits) e a criptografia de gerenciamento de direitos digitais (DRM) do PlayReady ou Widevine. Os Serviços de Mídia também fornecem um serviço de entrega de chaves e licenças do PlayReady/Widevine a clientes autorizados.

Se você desejar que os Serviços de Mídia criptografem um ativo, associe uma chave de criptografia (CommonEncryption ou EnvelopeEncryption) ao ativo. Para saber mais, confira [Criar chaves de conteúdo com REST](media-services-rest-create-contentkey.md). Você também precisa configurar políticas de autorização para a chave (conforme descrito neste artigo).

Quando um fluxo é solicitado por um player, os Serviços de Mídia do Microsoft Azure usam a chave especificada para criptografar dinamicamente o conteúdo usando a criptografia AES ou PlayReady. Para descriptografar o fluxo, o player solicita a chave do serviço de distribuição de chaves. Para determinar se o usuário está autorizado a obter a chave, o serviço avalia as políticas de autorização que você especificou para a chave.

Os serviços de mídia oferecem suporte a várias maneiras de autenticar os usuários que fazem solicitações de chave. A política de autorização da chave de conteúdo pode ter uma ou mais restrições de autorização usando a restrição aberta ou de token. A política restrita do token deve ser acompanhada por um token emitido por um Serviço de Token de Segurança (STS). Os Serviços de Mídia oferecem suporte a tokens nos formatos simple web token ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) e Token Web JSON (JWT).

Os Serviços de Mídia não oferecem um STS. Você pode criar um STS personalizado ou usar os tokens de problema do Microsoft Azure Active Directory (Azure AD). O STS deve ser configurado para criar um token assinado com a chave especificada e declarações de emissão que você especificou na configuração de restrição do token (conforme descrito neste artigo). Se o token for válido e as declarações no token corresponderem às configuradas para a chave de conteúdo, o serviço de distribuição de chave dos Serviços de Mídia retornará a chave criptografada para o cliente.

Para obter mais informações, consulte os seguintes artigos:
- [Autenticação do token JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
- [Integrar um aplicativo OWIN com base no MVC dos Serviços de Mídia do Azure com o Azure Active Directorye restringir o fornecimento da chave de conteúdo com base em declarações JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

### <a name="some-considerations-apply"></a>Algumas considerações se aplicam
* Para usar o empacotamento dinâmico e a criptografia dinâmica, verifique se o ponto de extremidade de streaming do qual você deseja transmitir seu conteúdo está no estado “Executando”.
* O ativo deve conter um conjunto de MP4s de taxa de bits adaptável ou arquivos de Smooth Streaming de taxa de bits adaptável. Para obter mais informações, consulte [Codificar um ativo](media-services-encode-asset.md).
* Carregar e codificar seus ativos usando a opção AssetCreationOptions.StorageEncrypted.
* Se você planeja ter várias chaves de conteúdo que exigem a mesma configuração de política, recomendamos criar uma política de autorização única e reutilizá-la com várias chaves de conteúdo.
* O serviço de entrega de chave armazena em cache ContentKeyAuthorizationPolicy e seus objetos relacionados (opções e restrições da política) por 15 minutos. Você pode criar ContentKeyAuthorizationPolicy e especificar o uso de uma restrição de token, testá-la e, em seguida, atualizar a política para a restrição aberta. Esse processo leva aproximadamente 15 minutos antes de a política mudar para a versão aberta da política.
* Se você adicionar ou atualizar a política de entrega do ativo, deverá excluir um localizador existente e criar um novo localizador.
* No momento, não é possível criptografar downloads progressivos.
* Um ponto de extremidade de streaming dos Serviços de Mídia define o valor do cabeçalho Access-Control-Allow-Origin do CORS na resposta de simulação como o curinga “\*”. Esse valor funciona bem com a maioria dos players, incluindo o Player de Mídia do Azure, Roku e JWPlayer e outros. No entanto, alguns players que usam dashjs não funcionam, porque o modo de credenciais definido para "incluir" XMLHttpRequest em seu dashjs não permite o curinga “\*” como o valor de Access-Control-Allow-Origin. Como uma solução alternativa para essa limitação em dashjs, se você hospedar seu cliente a partir de um único domínio os Serviços de Mídia podem especificar esse domínio no cabeçalho da resposta de simulação. Para obter assistência, abra um tíquete de suporte por meio do portal do Azure.

## <a name="aes-128-dynamic-encryption"></a>Criptografia dinâmica AES-128
> [!NOTE]
> Ao trabalhar com os Serviços de Mídia do API REST, as seguintes considerações se aplicam.
> 
> Ao acessar entidades nos Serviços de Mídia, você deve definir valores e campos de cabeçalho específicos nas suas solicitações HTTP. Para obter mais informações, consulte [Configuração para desenvolvimento da API REST dos Serviços de Mídia](media-services-rest-how-to-use.md).
> 
> 
> 

### <a name="open-restriction"></a>Restrição aberta
Restrição aberta significa que o sistema entrega a chave a qualquer pessoa que faça uma solicitação de chave. Essa restrição pode ser útil para fins de teste.

O exemplo a seguir cria uma política de autorização aberta e o adiciona à chave de conteúdo.

#### <a id="ContentKeyAuthorizationPolicies"></a>Criar ContentKeyAuthorizationPolicies
Solicitação:

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: d732dbfa-54fc-474c-99d6-9b46a006f389
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 36

    {"Name":"Open Authorization Policy"}

Resposta:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 211
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies('nb%3Ackpid%3AUUID%3Adb4593da-f4d1-4cc5-a92a-d20eacbabee4')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: d732dbfa-54fc-474c-99d6-9b46a006f389
    request-id: aabfa731-e884-4bf3-8314-492b04747ac4
    x-ms-request-id: aabfa731-e884-4bf3-8314-492b04747ac4
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Tue, 10 Feb 2015 08:25:56 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicies/@Element","Id":"nb:ckpid:UUID:db4593da-f4d1-4cc5-a92a-d20eacbabee4","Name":"Open Authorization Policy"}

#### <a id="ContentKeyAuthorizationPolicyOptions"></a>Criar ContentKeyAuthorizationPolicyOptions
Solicitação:

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 3.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: d225e357-e60e-4f42-add8-9d93aba1409a
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 168

    {"Name":"policy","KeyDeliveryType":2,"KeyDeliveryConfiguration":"","Restrictions":[{"Name":"HLS Open Authorization Policy","KeyRestrictionType":0,"Requirements":null}]}

Resposta:    

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 349
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3A57829b17-1101-4797-919b-f816f4a007b7')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: d225e357-e60e-4f42-add8-9d93aba1409a
    request-id: 81bcad37-295b-431f-972f-b23f2e4172c9
    x-ms-request-id: 81bcad37-295b-431f-972f-b23f2e4172c9
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Tue, 10 Feb 2015 08:56:40 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicyOptions/@Element","Id":"nb:ckpoid:UUID:57829b17-1101-4797-919b-f816f4a007b7","Name":"policy","KeyDeliveryType":2,"KeyDeliveryConfiguration":"","Restrictions":[{"Name":"HLS Open Authorization Policy","KeyRestrictionType":0,"Requirements":null}]}

#### <a id="LinkContentKeyAuthorizationPoliciesWithOptions"></a>Link ContentKeyAuthorizationPolicies com opções
Solicitação:

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies('nb%3Ackpid%3AUUID%3A0baa438b-8ac2-4c40-a53c-4d4722b78715')/$links/Options HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 9847f705-f2ca-4e95-a478-8f823dbbaa29
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 154

    {"uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3A57829b17-1101-4797-919b-f816f4a007b7')"}

Resposta:

    HTTP/1.1 204 No Content

#### <a id="AddAuthorizationPolicyToKey"></a>Adicionar uma política de autorização para a chave de conteúdo
Solicitação:

    PUT https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeys('nb%3Akid%3AUUID%3A2e6d36a7-a17c-4e9a-830d-eca23ad1a6f9') HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: e613efff-cb6a-41b4-984a-f4f8fb6e76a4
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 78

    {"AuthorizationPolicyId":"nb:ckpid:UUID:c06cebb8-c4f0-4d1a-ba00-3273fb2bc3ad"}

Resposta:

    HTTP/1.1 204 No Content

### <a name="token-restriction"></a>Restrição de token
Esta seção descreve como criar uma política de autorização de chave de conteúdo e associá-la com a chave de conteúdo. A política de autorização descreve quais requisitos de autorização devem ser atendidos para determinar se o usuário está autorizado a receber a chave. Por exemplo, a lista de chave de verificação contém a chave com a qual o token foi assinado?

Para configurar a opção de restrição de token, você precisa usar um XML para descrever os requisitos da autorização do token. O XML de configuração de restrição de token deve estar de acordo com o esquema XML a seguir:


#### <a id="schema"></a>Esquema de restrição de token
    <?xml version="1.0" encoding="utf-8"?>
    <xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="https://www.w3.org/2001/XMLSchema">
      <xs:complexType name="TokenClaim">
        <xs:sequence>
          <xs:element name="ClaimType" nillable="true" type="xs:string" />
          <xs:element minOccurs="0" name="ClaimValue" nillable="true" type="xs:string" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenClaim" nillable="true" type="tns:TokenClaim" />
      <xs:complexType name="TokenRestrictionTemplate">
        <xs:sequence>
          <xs:element minOccurs="0" name="AlternateVerificationKeys" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
          <xs:element name="Audience" nillable="true" type="xs:anyURI" />
          <xs:element name="Issuer" nillable="true" type="xs:anyURI" />
          <xs:element name="PrimaryVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
          <xs:element minOccurs="0" name="RequiredClaims" nillable="true" type="tns:ArrayOfTokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenRestrictionTemplate" nillable="true" type="tns:TokenRestrictionTemplate" />
      <xs:complexType name="ArrayOfTokenVerificationKey">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenVerificationKey" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
      <xs:complexType name="TokenVerificationKey">
        <xs:sequence />
      </xs:complexType>
      <xs:element name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
      <xs:complexType name="ArrayOfTokenClaim">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenClaim" nillable="true" type="tns:TokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenClaim" nillable="true" type="tns:ArrayOfTokenClaim" />
      <xs:complexType name="SymmetricVerificationKey">
        <xs:complexContent mixed="false">
          <xs:extension base="tns:TokenVerificationKey">
            <xs:sequence>
              <xs:element name="KeyValue" nillable="true" type="xs:base64Binary" />
            </xs:sequence>
          </xs:extension>
        </xs:complexContent>
      </xs:complexType>
      <xs:element name="SymmetricVerificationKey" nillable="true" type="tns:SymmetricVerificationKey" />
    </xs:schema>

Ao configurar a política restrita do token, você deve especificar os parâmetros de chave de verificação primária, emissor e público. A chave de verificação primária contém a chave com a qual o token foi assinado. O emissor é o STS que emite o token. A audiência (às vezes chamada de escopo) descreve a intenção do token ou o recurso que o token autoriza o acesso. O serviço de distribuição de chaves dos serviços de mídia valida que esses valores no token correspondem aos valores no modelo.

O exemplo a seguir cria uma política de autorização com uma restrição de token. Neste exemplo, o cliente precisa apresentar um token que contenha a chave de assinatura (VerificationKey), um emissor de token e declarações necessárias.

### <a name="create-contentkeyauthorizationpolicies"></a>Criar ContentKeyAuthorizationPolicies
Criar uma política de restrição de token, conforme mostrado na seção "[Criar ContentKeyAuthorizationPolicies](#ContentKeyAuthorizationPolicies)".

### <a name="create-contentkeyauthorizationpolicyoptions"></a>Criar ContentKeyAuthorizationPolicyOptions
Solicitação:

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 3.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    x-ms-client-request-id: 2643d836-bfe7-438e-9ba2-bc6ff28e4a53
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 1079

    {"Name":"Token option for HLS","KeyDeliveryType":2,"KeyDeliveryConfiguration":null,"Restrictions":[{"Name":"Token Authorization Policy","KeyRestrictionType":1,"Requirements":"<TokenRestrictionTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1\"><AlternateVerificationKeys><TokenVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>BklyAFiPTQsuJNKriQJBZHYaKM2CkCTDQX2bw9sMYuvEC9sjW0W7GUIBygQL/+POEeUqCYPnmEU2g0o1GW2Oqg==</KeyValue></TokenVerificationKey></AlternateVerificationKeys><Audience>urn:test</Audience><Issuer>http://testissuer.com/</Issuer><PrimaryVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>E5BUHiN4vBdzUzdP0IWaHFMMU3D1uRZgF16TOhSfwwHGSw+Kbf0XqsHzEIYk11M372viB9vbiacsdcQksA0ftw==</KeyValue></PrimaryVerificationKey><RequiredClaims><TokenClaim><ClaimType>urn:microsoft:azure:mediaservices:contentkeyidentifier</ClaimType><ClaimValue i:nil=\"true\" /></TokenClaim></RequiredClaims><TokenType>SWT</TokenType></TokenRestrictionTemplate>"}]}

Resposta:    

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1260
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3Ae1ef6145-46e8-4ee6-9756-b1cf96328c23')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 2643d836-bfe7-438e-9ba2-bc6ff28e4a53
    request-id: 2310b716-aeaa-421e-913e-3ce2f6f685ca
    x-ms-request-id: 2310b716-aeaa-421e-913e-3ce2f6f685ca
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Tue, 10 Feb 2015 09:10:37 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicyOptions/@Element","Id":"nb:ckpoid:UUID:e1ef6145-46e8-4ee6-9756-b1cf96328c23","Name":"Token option for HLS","KeyDeliveryType":2,"KeyDeliveryConfiguration":null,"Restrictions":[{"Name":"Token Authorization Policy","KeyRestrictionType":1,"Requirements":"<TokenRestrictionTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1\"><AlternateVerificationKeys><TokenVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>BklyAFiPTQsuJNKriQJBZHYaKM2CkCTDQX2bw9sMYuvEC9sjW0W7GUIBygQL/+POEeUqCYPnmEU2g0o1GW2Oqg==</KeyValue></TokenVerificationKey></AlternateVerificationKeys><Audience>urn:test</Audience><Issuer>http://testissuer.com/</Issuer><PrimaryVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>E5BUHiN4vBdzUzdP0IWaHFMMU3D1uRZgF16TOhSfwwHGSw+Kbf0XqsHzEIYk11M372viB9vbiacsdcQksA0ftw==</KeyValue></PrimaryVerificationKey><RequiredClaims><TokenClaim><ClaimType>urn:microsoft:azure:mediaservices:contentkeyidentifier</ClaimType><ClaimValue i:nil=\"true\" /></TokenClaim></RequiredClaims><TokenType>SWT</TokenType></TokenRestrictionTemplate>"}]}

#### <a name="link-contentkeyauthorizationpolicies-with-options"></a>Link ContentKeyAuthorizationPolicies com opções
Link ContentKeyAuthorizationPolicies com opções, como mostrado na seção "[Criar ContentKeyAuthorizationPolicies](#ContentKeyAuthorizationPolicies)".

#### <a name="add-an-authorization-policy-to-the-content-key"></a>Adicionar uma política de autorização para a chave de conteúdo
Adicionar AuthorizationPolicy ao ContentKey, conforme mostrado na seção "[Adicionar uma política de autorização para a chave de conteúdo](#AddAuthorizationPolicyToKey)."

## <a name="playready-dynamic-encryption"></a>Criptografia dinâmica do PlayReady
Você pode usar os Serviços de Mídia para configurar os direitos e restrições que você deseja para que o tempo de execução do PlayReady DRM imponha quando um usuário tentar reproduzir conteúdo protegido. 

Ao proteger o conteúdo com PlayReady, uma das coisas que você precisa especificar na sua política de autorização é uma cadeia de caracteres XML que define o [modelo de licença do PlayReady](media-services-playready-license-template-overview.md). 

### <a name="open-restriction"></a>Restrição aberta
Restrição aberta significa que o sistema entrega a chave a qualquer pessoa que faça uma solicitação de chave. Essa restrição pode ser útil para fins de teste.

O exemplo a seguir cria uma política de autorização aberta e o adiciona à chave de conteúdo.

#### <a id="ContentKeyAuthorizationPolicies2"></a>Criar ContentKeyAuthorizationPolicies
Solicitação:

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 9e7fa407-f84e-43aa-8f05-9790b46e279b
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 58

    {"Name":"Deliver Common Content Key"}

Resposta:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 233
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies('nb%3Ackpid%3AUUID%3Acc3c64a8-e2fc-4e09-bf60-ac954251a387')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 9e7fa407-f84e-43aa-8f05-9790b46e279b
    request-id: b3d33c1b-a9cb-4120-ac0c-18f64846c147
    x-ms-request-id: b3d33c1b-a9cb-4120-ac0c-18f64846c147
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Tue, 10 Feb 2015 09:26:00 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicies/@Element","Id":"nb:ckpid:UUID:cc3c64a8-e2fc-4e09-bf60-ac954251a387","Name":"Deliver Common Content Key"}


#### <a name="create-contentkeyauthorizationpolicyoptions"></a>Criar ContentKeyAuthorizationPolicyOptions
Solicitação:

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 3.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: f160ad25-b457-4bc6-8197-315604c5e585
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 593

    {"Name":"","KeyDeliveryType":1,"KeyDeliveryConfiguration":"<PlayReadyLicenseResponseTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1\"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>false</AllowTestDevices><ContentKey i:type=\"ContentEncryptionKeyFromHeader\" /><LicenseType>Nonpersistent</LicenseType><PlayRight /></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>","Restrictions":[{"Name":"Open","KeyRestrictionType":0,"Requirements":null}]}

Resposta:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 774
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3A1052308c-4df7-4fdb-8d21-4d2141fc2be0')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: f160ad25-b457-4bc6-8197-315604c5e585
    request-id: 563f5a42-50a4-4c4a-add8-a833f8364231
    x-ms-request-id: 563f5a42-50a4-4c4a-add8-a833f8364231
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Tue, 10 Feb 2015 09:23:24 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicyOptions/@Element","Id":"nb:ckpoid:UUID:1052308c-4df7-4fdb-8d21-4d2141fc2be0","Name":"","KeyDeliveryType":1,"KeyDeliveryConfiguration":"<PlayReadyLicenseResponseTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1\"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>false</AllowTestDevices><ContentKey i:type=\"ContentEncryptionKeyFromHeader\" /><LicenseType>Nonpersistent</LicenseType><PlayRight /></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>","Restrictions":[{"Name":"Open","KeyRestrictionType":0,"Requirements":null}]}

#### <a name="link-contentkeyauthorizationpolicies-with-options"></a>Link ContentKeyAuthorizationPolicies com opções
Link ContentKeyAuthorizationPolicies com opções, como mostrado na seção "[Criar ContentKeyAuthorizationPolicies](#ContentKeyAuthorizationPolicies)".

#### <a name="add-an-authorization-policy-to-the-content-key"></a>Adicionar uma política de autorização para a chave de conteúdo
Adicionar AuthorizationPolicy ao ContentKey, conforme mostrado na seção "[Adicionar uma política de autorização para a chave de conteúdo](#AddAuthorizationPolicyToKey)."

### <a name="token-restriction"></a>Restrição de token
Para configurar a opção de restrição de token, você precisa usar um XML para descrever os requisitos da autorização do token. A configuração XML de restrição de token deve estar em conformidade com o esquema XML mostrado na seção “[Esquema de restrição de token](#schema)”.

#### <a name="create-contentkeyauthorizationpolicies"></a>Criar ContentKeyAuthorizationPolicies
Crie ContentKeyAuthorizationPolicies, como mostrado na seção "[Criar ContentKeyAuthorizationPolicies](#ContentKeyAuthorizationPolicies2)".

#### <a name="create-contentkeyauthorizationpolicyoptions"></a>Criar ContentKeyAuthorizationPolicyOptions
Solicitação:

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 3.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: ab079b0e-2ba9-4cf1-b549-a97bfa6cd2d3
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 1525

    {"Name":"Token option","KeyDeliveryType":1,"KeyDeliveryConfiguration":"<PlayReadyLicenseResponseTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1\"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>false</AllowTestDevices><ContentKey i:type=\"ContentEncryptionKeyFromHeader\" /><LicenseType>Nonpersistent</LicenseType><PlayRight /></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>","Restrictions":[{"Name":"Token Authorization Policy","KeyRestrictionType":1,"Requirements":"<TokenRestrictionTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1\"><AlternateVerificationKeys><TokenVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>w52OyHVqXT8aaupGxuJ3NGt8M6opHDOtx132p4r6q4hLI6ffnLusgEGie1kedUewVoIe1tqDkVE6xsIV7O91KA==</KeyValue></TokenVerificationKey></AlternateVerificationKeys><Audience>urn:test</Audience><Issuer>http://testissuer.com/</Issuer><PrimaryVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>dYwLKIEMBljLeY9VM7vWdlhps31Fbt0XXhqP5VyjQa33bJXleBtkzQ6dF5AtwI9gDcdM2dV2TvYNhCilBKjMCg==</KeyValue></PrimaryVerificationKey><RequiredClaims><TokenClaim><ClaimType>urn:microsoft:azure:mediaservices:contentkeyidentifier</ClaimType><ClaimValue i:nil=\"true\" /></TokenClaim></RequiredClaims><TokenType>SWT</TokenType></TokenRestrictionTemplate>"}]}

Resposta:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1706
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3Ae42bbeae-de42-4077-90e9-a844f297ef70')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: ab079b0e-2ba9-4cf1-b549-a97bfa6cd2d3
    request-id: ccf8a4ba-731e-4124-8192-079592c251cc
    x-ms-request-id: ccf8a4ba-731e-4124-8192-079592c251cc
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Tue, 10 Feb 2015 09:58:47 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicyOptions/@Element","Id":"nb:ckpoid:UUID:e42bbeae-de42-4077-90e9-a844f297ef70","Name":"Token option","KeyDeliveryType":1,"KeyDeliveryConfiguration":"<PlayReadyLicenseResponseTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1\"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>false</AllowTestDevices><ContentKey i:type=\"ContentEncryptionKeyFromHeader\" /><LicenseType>Nonpersistent</LicenseType><PlayRight /></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>","Restrictions":[{"Name":"Token Authorization Policy","KeyRestrictionType":1,"Requirements":"<TokenRestrictionTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1\"><AlternateVerificationKeys><TokenVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>w52OyHVqXT8aaupGxuJ3NGt8M6opHDOtx132p4r6q4hLI6ffnLusgEGie1kedUewVoIe1tqDkVE6xsIV7O91KA==</KeyValue></TokenVerificationKey></AlternateVerificationKeys><Audience>urn:test</Audience><Issuer>http://testissuer.com/</Issuer><PrimaryVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>dYwLKIEMBljLeY9VM7vWdlhps31Fbt0XXhqP5VyjQa33bJXleBtkzQ6dF5AtwI9gDcdM2dV2TvYNhCilBKjMCg==</KeyValue></PrimaryVerificationKey><RequiredClaims><TokenClaim><ClaimType>urn:microsoft:azure:mediaservices:contentkeyidentifier</ClaimType><ClaimValue i:nil=\"true\" /></TokenClaim></RequiredClaims><TokenType>SWT</TokenType></TokenRestrictionTemplate>"}]}

#### <a name="link-contentkeyauthorizationpolicies-with-options"></a>Link ContentKeyAuthorizationPolicies com opções
Link ContentKeyAuthorizationPolicies com opções, como mostrado na seção "[Criar ContentKeyAuthorizationPolicies](#ContentKeyAuthorizationPolicies)".

#### <a name="add-an-authorization-policy-to-the-content-key"></a>Adicionar uma política de autorização para a chave de conteúdo
Adicionar AuthorizationPolicy ao ContentKey, conforme mostrado na seção "[Adicionar uma política de autorização para a chave de conteúdo](#AddAuthorizationPolicyToKey)."

## <a id="types"></a>Tipos usados ao definir ContentKeyAuthorizationPolicy
### <a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType
    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1, 
        IPRestricted = 2, // IP restriction on content key is not currently supported, reserved for future.
    }


> [!NOTE]
> A restrição de IP em políticas de autorização de chaves de conteúdo ainda não está disponível no serviço.


### <a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType
    public enum ContentKeyDeliveryType
    {
        None = 0,
        PlayReadyLicense = 1,
        BaselineHttp = 2,
        Widevine = 3
    }


## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Próximas etapas
Agora que você configurou a política de autorização da chave de conteúdo, consulte [Configurar uma política de entrega de ativos](media-services-rest-configure-asset-delivery-policy.md).

