---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: e3732823be1c8391f2bec9018a094200c7c93a5e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91711127"
---
| Propriedade | Descrição |
|:--- |:---|
|**identity / type** | O tipo de autenticação que foi usado para fazer a solicitação. Por exemplo: `OAuth`, `SAS Key`, `Account Key` ou `Anonymous` |
|**identity / tokenHash**|Este campo é reservado somente para uso interno. |
|**authorization / action** | A ação que é atribuída à solicitação. |
|**authorization / roleAssignmentId** | ID de atribuição de função. Por exemplo: `4e2521b7-13be-4363-aeda-111111111111`.|
|**authorization / roleDefinitionId** | A ID de definição de função. Por exemplo: `ba92f5b4-2d11-453d-a403-111111111111"`.|
|**principals / id** | A ID da entidade de segurança. Por exemplo: `a4711f3a-254f-4cfb-8a2d-111111111111`.|
|**principals / type** | O tipo de entidade de segurança. Por exemplo: `ServicePrincipal`. |
|**requester / appID** | A ID do aplicativo de Open Authorization (OAuth) que é usada como solicitante. <br> Por exemplo: `d3f7d5fe-e64a-4e4e-871d-333333333333`.|
|**requester / audience** | O público OAuth da solicitação. Por exemplo: `https://storage.azure.com`. |
|**requester / objectId** | A ID do objeto OAuth da solicitação. No caso da autenticação Kerberos, representa o identificador de objeto do usuário autenticado do Kerberos. Por exemplo: `0e0bf547-55e5-465c-91b7-2873712b249c`. |
|**requester / tenantId** | A ID de locatário OAuth de identidade. Por exemplo: `72f988bf-86f1-41af-91ab-222222222222`.|
|**requester / tokenIssuer** | O emissor do token OAuth. Por exemplo: `https://sts.windows.net/72f988bf-86f1-41af-91ab-222222222222/`.|
|**requester / upn** | O UPN (nome UPN) do solicitante. Por exemplo: `someone@contoso.com`. |
|**requester / userName** | Este campo é reservado somente para uso interno.|