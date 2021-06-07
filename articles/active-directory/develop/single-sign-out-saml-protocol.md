---
title: Protocolo SAML de saída única do Azure
description: Este artigo descreve o protocolo SAML de Logout Único no Azure Active Directory
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 03/22/2021
ms.author: kenwith
ms.custom: aaddev
ms.reviewer: paulgarn
ms.openlocfilehash: 040e49ee870704107e1d4118f1484119d98a9ebf
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781204"
---
# <a name="single-sign-out-saml-protocol"></a>Protocolo SAML de Logout Único

O Azure AD (Azure Active Directory) dá suporte ao perfil de logout único de navegador Web SAML 2.0. Para que o logout único funcione corretamente, o **LogoutURL** do aplicativo deve ser explicitamente registrado com o Azure AD durante o registro do aplicativo. Se o aplicativo for [adicionado à galeria de Azure app](v2-howto-app-gallery-listing.md) , esse valor poderá ser definido por padrão. Caso contrário, o valor deve ser determinado e definido pela pessoa que está adicionando o aplicativo ao seu locatário do Azure AD. O Microsoft Azure Active Directory usa o LogoutURL para redirecionar os usuários depois que eles são desconectados. 

O Azure AD dá suporte à associação de redirecionamento (HTTP GET) e não à associação HTTP POST.

O diagrama a seguir mostra o fluxo de trabalho do processo de logout único do Microsoft Azure Active Directory.

![Fluxo de trabalho do Logout Único do Azure Active Directory](./media/single-sign-out-saml-protocol/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>LogoutRequest
O serviço de nuvem envia uma mensagem `LogoutRequest` ao Azure AD para indicar que uma sessão foi encerrada. O trecho a seguir mostra um exemplo de elemento `LogoutRequest` .

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>LogoutRequest
O elemento `LogoutRequest` enviado ao Azure AD requer os seguintes atributos:

* `ID` - isso identifica a solicitação de saída. O valor de `ID` não deve começar com um número. A prática comum é acrescentar **id** à representação de cadeia de caracteres de um GUID.
* `Version` - Defina o valor desse elemento como **2.0**. Esse valor é necessário.
* `IssueInstant` - esta é uma cadeia de caracteres `DateTime` com um valor de UTC (Tempo Universal Coordenado) e [formato de ida e volta ("o")](/dotnet/standard/base-types/standard-date-and-time-format-strings). O Microsoft Azure Active Directory espera um valor desse tipo, mas não é obrigatório.

### <a name="issuer"></a>Emissor
O elemento `Issuer` em uma `LogoutRequest` deve corresponder exatamente a um do **ServicePrincipalNames** no serviço de nuvem no Azure AD. Normalmente, isso é definido como o **URI da ID do aplicativo** que é especificado durante o registro do aplicativo.

### <a name="nameid"></a>NameID
O valor do elemento `NameID` deve corresponder exatamente ao `NameID` do usuário que está sendo desconectado.

## <a name="logoutresponse"></a>LogoutResponse
O Azure AD envia uma `LogoutResponse` em resposta a um elemento `LogoutRequest`. O trecho a seguir mostra um exemplo de `LogoutResponse`.

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>LogoutResponse
O Azure AD define os valores `ID`, `Version` e `IssueInstant` no elemento `LogoutResponse`. Ele também define o elemento `InResponseTo` como o valor do atributo `ID` da `LogoutRequest` que emitiu a resposta.

### <a name="issuer"></a>Emissor
O Azure AD define esse valor como `https://login.microsoftonline.com/<TenantIdGUID>/`, em que \<TenantIdGUID> é a ID de locatário do locatário do Azure AD.

Para avaliar o valor do elemento `Issuer` , use o valor do **URI da ID do aplicativo** fornecido durante o registro do aplicativo.

### <a name="status"></a>Status
O Azure AD usa o `StatusCode` elemento no `Status` elemento para indicar o êxito ou a falha da saída. Quando a tentativa de saída falha, o `StatusCode` elemento também pode conter mensagens de erro personalizadas.
