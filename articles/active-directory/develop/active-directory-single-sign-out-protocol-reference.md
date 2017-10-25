---
title: "Protocolo SAML de Logout Único do Azure | Microsoft Docs"
description: "Este artigo descreve o protocolo SAML de Logout Único no Azure Active Directory"
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: 
ms.assetid: 0e4aa75d-d1ad-4bde-a94c-d8a41fb0abe6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: priyamo
ms.custom: aaddev
ms.openlocfilehash: 45e4705f53d80b5fe852c484b5e64d18a8e24f09
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# Protocolo SAML de Logout Único
O Azure AD (Azure Active Directory) dá suporte ao perfil de logout único de navegador Web SAML 2.0. Para que o logout único funcione corretamente, o **LogoutURL** do aplicativo deve ser explicitamente registrado com o Azure AD durante o registro do aplicativo. O Azure AD usa o LogoutURL para redirecionar os usuários depois que eles são desconectados.

Este diagrama mostra o fluxo de trabalho do processo de logout único do Azure AD.

![Fluxo de trabalho do Logout Único](media/active-directory-single-sign-out-protocol-reference/active-directory-saml-single-sign-out-workflow.png)

## LogoutRequest
O serviço de nuvem envia uma mensagem `LogoutRequest` ao Azure AD para indicar que uma sessão foi encerrada. O trecho a seguir mostra um exemplo de elemento `LogoutRequest` .

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### LogoutRequest
O elemento `LogoutRequest` enviado ao Azure AD requer os seguintes atributos:

* `ID` : isso identifica a solicitação de saída. O valor de `ID` não deve começar com um número. A prática comum é acrescentar **id** à representação de cadeia de caracteres de um GUID.
* `Version` : Defina o valor desse elemento como **2.0**. Esse valor é obrigatório.
* `IssueInstant` : esta é uma cadeia de caracteres `DateTime` com um valor de UTC (Tempo Universal Coordenado) e [formato de ida e volta ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). O Azure AD espera um valor desse tipo, mas não é obrigatório.

### Emissor
O elemento `Issuer` em uma `LogoutRequest` deve corresponder exatamente a um do **ServicePrincipalNames** no serviço de nuvem no Azure AD. Normalmente, isso é definido como o **URI da ID do aplicativo** que é especificado durante o registro do aplicativo.

### NameID
O valor do elemento `NameID` deve corresponder exatamente ao `NameID` do usuário que está sendo desconectado.

## LogoutResponse
O Azure AD envia uma `LogoutResponse` em resposta a um elemento `LogoutRequest`. O trecho a seguir mostra um exemplo de `LogoutResponse`.

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### LogoutResponse
O Azure AD define os valores `ID`, `Version` e `IssueInstant` no elemento `LogoutResponse`. Ele também define o elemento `InResponseTo` como o valor do atributo `ID` da `LogoutRequest` que emitiu a resposta.

### Emissor
O Azure AD define esse valor como `https://login.microsoftonline.com/<TenantIdGUID>/`, em que <TenantIdGUID> é a ID de locatário do locatário do Azure AD.

Para avaliar o valor do elemento `Issuer` , use o valor do **URI da ID do aplicativo** fornecido durante o registro do aplicativo.

### Status
O Azure AD usa o elemento `StatusCode` no elemento `Status` para indicar o sucesso ou a falha da desconexão. Quando a tentativa de desconexão falha, o elemento `StatusCode` também pode conter mensagens de erro personalizadas.