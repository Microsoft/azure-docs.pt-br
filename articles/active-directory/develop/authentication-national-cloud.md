---
title: Autenticação do Azure Active Directory e nuvens nacionais | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre o registro do aplicativo e os pontos de extremidade de autenticação para nuvens nacionais.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: negoe
ms.reviewer: marsma, negoe,celested
ms.custom: aaddev,references_regions
ms.openlocfilehash: da1ea6462d22242d23629bcec192d7760314dfed
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96444808"
---
# <a name="national-clouds"></a>Nuvens nacionais

As nuvens nacionais são instâncias fisicamente isoladas do Azure. Essas regiões do Azure são projetadas para garantir que os requisitos de residência, de soberania e de conformidade de dados sejam respeitados dentro de limites geográficos.

Incluindo a nuvem global, o Azure Active Directory (AAD) é implantado nas seguintes nuvens nacionais:  

- Azure Government
- Azure Alemanha
- Azure China 21Vianet

As nuvens nacionais são exclusivas e um ambiente separado do Azure global. É importante conhecer as principais diferenças ao desenvolver seu aplicativo para esses ambientes. As diferenças incluem o registro de aplicativos, a aquisição de tokens e a configuração de pontos de extremidade.

## <a name="app-registration-endpoints"></a>Pontos de extremidade de registro do aplicativo

Há um portal do Azure separado para cada uma das nuvens nacionais. Para integrar aplicativos com a plataforma de identidade da Microsoft em uma nuvem nacional, você precisa registrar o aplicativo separadamente em cada portal do Azure específico ao ambiente.

A tabela a seguir lista as URLs base para os pontos de extremidade do Azure Active Directory usados para registrar um aplicativo para cada nuvem nacional.

| Nuvem nacional | Ponto de extremidade do portal do Azure AD |
|----------------|--------------------------|
| Azure AD for US Government | `https://portal.azure.us` |
| Azure AD Alemanha | `https://portal.microsoftazure.de` |
| Azure AD China operado pela 21Vianet | `https://portal.azure.cn` |
| Azure AD (serviço global) |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Pontos de extremidade de autenticação do Azure AD

Todas as nuvens nacionais autenticam usuários separadamente em cada ambiente e têm pontos de extremidade de autenticação separados.

A tabela a seguir lista as URLs base para os pontos de extremidade do Azure Active Directory usados para obter tokens para chamar o Microsoft Graph para cada nuvem nacional.

| Nuvem nacional | Ponto de extremidade de autenticação do Azure Active Directory |
|----------------|-------------------------|
| Azure AD for US Government | `https://login.microsoftonline.us` |
| Azure AD Alemanha| `https://login.microsoftonline.de` |
| Azure AD China operado pela 21Vianet | `https://login.chinacloudapi.cn` |
| Azure AD (serviço global)| `https://login.microsoftonline.com` |

Você pode formar solicitações para os pontos de extremidade de token ou de autorização do Azure Active Directory se usar a URL base correta específica à região. Por exemplo, para o Azure Alemanha:

- O ponto de extremidade de autorização comum é `https://login.microsoftonline.de/common/oauth2/v2.0/authorize`.
- O ponto de extremidade de token comum é `https://login.microsoftonline.de/common/oauth2/v2.0/token`.

Para aplicativos de locatário único, substitua o comum nas URLs anteriores por sua ID de locatário ou nome. Um exemplo é `https://login.microsoftonline.de/contoso.com`.

## <a name="microsoft-graph-api"></a>API do Microsoft Graph

Para saber como chamar as APIs do Microsoft Graph no ambiente de nuvem nacional, acesse [Microsoft Graph em implantações de nuvem nacional](/graph/deployments).

> [!IMPORTANT]
> Determinados serviços e recursos que estão em regiões específicas do serviço global podem não estar disponíveis em todas as nuvens nacionais. Para descobrir quais serviços estão disponíveis, acesse [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast).

Para saber como criar um aplicativo usando a plataforma de identidade da Microsoft, siga o [tutorial da Biblioteca de Autenticação da Microsoft (MSAL)](msal-national-cloud.md). Esse aplicativo especificamente conectará um usuário e obterá um token de acesso para chamar a API de Microsoft Graph.

## <a name="next-steps"></a>Próximas etapas

Saiba como usar a [MSAL (biblioteca de autenticação da Microsoft) em um ambiente de nuvem nacional](msal-national-cloud.md).

Documentação da nuvem nacional:

- [Azure Governamental](../../azure-government/index.yml)
- [Azure China 21Vianet](/azure/china/)
- [Azure Alemanha](../../germany/index.yml)
