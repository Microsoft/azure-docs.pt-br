---
title: Registrar os aplicativos do Azure Active Directory para a API do Azure para FHIR
description: Este tutorial explica quais aplicativos precisam ser registrados para a API do Azure para FHIR e o servidor FHIR para Azure.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 10/13/2019
ms.openlocfilehash: aa95dc5cc052fbff6c553de50f4f52dc5df850a5
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93398106"
---
# <a name="register-the-azure-active-directory-apps-for-azure-api-for-fhir"></a>Registrar os aplicativos do Azure Active Directory para a API do Azure para FHIR

Você pode escolher entre várias opções de configuração ao configurar a API do Azure para FHIR ou o servidor FHIR para Azure (OSS). Para software livre, você precisará criar seu próprio registro de aplicativo de recurso. Para a API do Azure para FHIR, esse aplicativo de recurso é criado automaticamente.

## <a name="application-registrations"></a>Registros de aplicativo

Para que um aplicativo interaja com o Azure AD, ele precisa ser registrado. No contexto do servidor FHIR, há dois tipos de registros de aplicativo a serem discutidos:

1. Registros de aplicativo de recurso.
1. Registros de aplicativo cliente.

Os **aplicativos de recurso** são representações no Azure AD de uma API ou recurso que é protegido com o Azure AD, especificamente, seria a API do Azure para FHIR. Um aplicativo de recurso para a API do Azure para FHIR será criado automaticamente quando você provisionar o serviço, porém, se você estiver usando o servidor de software livre, precisará [registrar um aplicativo de recurso](register-resource-azure-ad-client-app.md) no Azure AD. Esse aplicativo de recurso terá um URI identificador. É recomendável que esse URI seja igual ao URI do servidor FHIR. Esse URI deve ser usado como o `Audience` para o servidor FHIR. Um aplicativo cliente pode solicitar acesso a esse servidor FHIR quando ele solicita um token.

Os *aplicativos cliente* são registros dos clientes que solicitarão tokens. Geralmente, no OAuth 2.0, distinguimos entre pelo menos três tipos diferentes de aplicativos:

1. **Clientes confidenciais** , também conhecidos como aplicativos Web no Azure AD. Clientes confidenciais são aplicativos que usam um [fluxo de código de autorização](../active-directory/azuread-dev/v1-protocols-oauth-code.md) para obter um token em nome de um usuário conectado que apresente credenciais válidas. Eles são chamados de clientes confidenciais porque são capazes de manter um segredo e apresentam esse segredo ao Azure AD ao trocar o código de autenticação para um token. Como os clientes confidenciais podem se autenticar usando o segredo do cliente, eles são mais confiáveis do que clientes públicos e podem ter tokens com maior tempo de vida e receber um token de atualização. Leia os detalhes sobre como [registrar um cliente confidencial](register-confidential-azure-ad-client-app.md). Observe que é importante registrar a URL de resposta na qual o cliente receberá o código de autorização.
1. **Clientes públicos**. Esses são clientes que não podem manter um segredo. Normalmente, seria um aplicativo de dispositivo móvel ou um aplicativo JavaScript de página única, em que um segredo no cliente poderia ser descoberto por um usuário. Os clientes públicos também usam o fluxo de código de autorização, mas não têm permissão para apresentar um segredo ao obter um token e podem ter tokens de vida mais curta e nenhum token de atualização. Leia os detalhes sobre como [registrar um cliente público](register-public-azure-ad-client-app.md).
1. Clientes de serviço. Esses clientes obtêm tokens em nome de si mesmos (não em nome de um usuário) usando o [fluxo de credenciais de cliente](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md). Normalmente, eles representam aplicativos que acessam o servidor FHIR de maneira não interativa. Um exemplo seria um processo de ingestão. Ao usar um cliente de serviço, não é necessário iniciar o processo de obter um token com uma chamada para o ponto de extremidade `/authorize`. Um cliente de serviço pode ir direto para o ponto de extremidade `/token` e apresentar a ID do cliente e o segredo do cliente para obter um token. Leia os detalhes sobre como [registrar um cliente de serviço](register-service-azure-ad-client-app.md)

## <a name="next-steps"></a>Próximas etapas

Nesta visão geral, você passou pelos tipos de registros de aplicativo de que talvez precise para trabalhar com uma API do FHIR.

Com base na sua configuração, consulte os guias de instruções para registrar seus aplicativos

* [Registrar um aplicativo de recurso](register-resource-azure-ad-client-app.md)
* [Registrar um aplicativo cliente confidencial](register-confidential-azure-ad-client-app.md)
* [Registrar um aplicativo cliente público](register-public-azure-ad-client-app.md)
* [Registrar um aplicativo de serviço](register-service-azure-ad-client-app.md)

Depois de registrar seus aplicativos, você pode implantar a API do Azure para FHIR.

>[!div class="nextstepaction"]
>[Implantar a API do Azure para FHIR](fhir-paas-powershell-quickstart.md)