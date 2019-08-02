---
title: O que há de novo para o Azure Active Directory no Microsoft 365 Government - Azure Active Directory | Microsoft Docs
description: Saiba mais sobre algumas alterações ao Azure Active Directory (Azure AD) em que a instância de nuvem do governo do Microsoft 365, que pode afetar você.
services: active-directory
author: eross-msft
manager: daveba
ms.author: lizross
ms.reviewer: sumitp
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b077c7b5efbad2add971d42ff31938b56f6bc33
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66258888"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>O que há de novo para o Azure Active Directory no Microsoft 365 Government

Fizemos algumas alterações ao Azure Active Directory (Azure AD) em que a instância de nuvem de governo do Microsoft 365, que é aplicável aos clientes que usam os seguintes serviços:

- Microsoft Azure Governamental

- Governo do Microsoft 365 – GCC High

- Microsoft 365 Government – DoD

Este artigo não se aplica a Microsoft 365 Government – clientes GCC.

## <a name="changes-to-the-initial-domain-name"></a>Alterações no nome de domínio inicial

Durante à inscrição inicial sua organização para um serviço online da Microsoft 365 Government, você recebeu uma solicitação para escolher o nome de domínio da sua organização, `<your-domain-name>.onmicrosoft.com`. Se você já tiver um nome de domínio com o sufixo. com, nada será alterado.

No entanto, se você estiver se inscrevendo para um novo serviço do Microsoft 365 Government, você será solicitado para escolher um nome de domínio usando o `.us` sufixo. Portanto, ele será `<your-domain-name>.onmicrosoft.us`.

>[!Note]
>Essa alteração não se aplica a todos os clientes que são gerenciados por provedores de serviço de nuvem (CSPs).

## <a name="changes-to-portal-access"></a>Alterações no acesso ao portal

Nós atualizamos os pontos de extremidade do portal para o Microsoft Azure governamental, Microsoft 365 Government – alta GCC e Microsoft 365 Government – DoD, conforme mostrado na [tabela de mapeamento de ponto de extremidade](#endpoint-mapping).

Anteriormente, os clientes podem entrar usando o Azure em todo o mundo (portal.azure.com) e os portais do Office 365 (portal.office.com). Com essa atualização, os clientes agora devem entrar usando o Microsoft Azure governamental, Microsoft 365 Government - alta GCC e Microsoft 365 Government - portais DoD específico.

## <a name="endpoint-mapping"></a>Mapeamento de ponto de extremidade

A tabela a seguir mostra os pontos de extremidade para todos os clientes:

| NOME | detalhes do ponto de extremidade |
|------|------------------|
| Portais |Microsoft Azure Government: https://portal.azure.us<p>Governo do Microsoft 365 – GCC alta: https://portal.office365.us<p>Microsoft 365 Government – DoD: https://portal.apps.mil |
| Ponto de extremidade de autoridade do Active Directory do Azure | https://login.microsoftonline.us |
| API do Graph do Active Directory do Azure | https://graph.windows.net |
| API do Microsoft Graph para o governo do Microsoft 365 - GCC High | https://graph.microsoft.us |
| API do Microsoft Graph para o governo do Microsoft 365 - DoD | https://dod-graph.microsoft.us |
| Pontos de extremidade de serviços do Azure governamental | Para obter detalhes, consulte [guia do desenvolvedor do Azure governamental](https://docs.microsoft.com/azure/azure-government/documentation-government-developer-guide) |
| Governo do Microsoft 365 - pontos de extremidade alta GCC | Para obter detalhes, consulte [dos EUA do Office 365 Pontos de extremidade de alta GCC do governo](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-gcc-high-endpoints) |
| Microsoft 365 Government - DoD | Para obter detalhes, consulte [dos EUA do Office 365 Pontos de extremidade do governo DoD](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte estes artigos:

- [O que é o Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)

- [Atualização de ponto de extremidade de autoridade do Azure governamental AAD](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [Pontos de extremidade do Microsoft Graph na nuvem do governo dos EUA](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [O Office 365 US Government GCC High e DoD](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)