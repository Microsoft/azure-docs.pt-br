---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: ca8963ed8928745a6d5918c86021199432339c83
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104612713"
---
| Propriedade | Descrição |
|:--- |:---|
|**identity / type** | O tipo de autenticação que foi usado para fazer a solicitação. Por exemplo: `OAuth` , `Kerberos` , `SAS Key` , `Account Key` ou `Anonymous` |
|**identity / tokenHash**|O hash SHA-256 do token de autenticação usado na solicitação. <br>Quando o tipo de autenticação é `Account Key` , o formato é "key1 \| Key2 (SHA256 hash da chave)". Por exemplo: `key1(5RTE343A6FEB12342672AFD40072B70D4A91BGH5CDF797EC56BF82B2C3635CE)`. <br>Quando o tipo de autenticação é `SAS Key` , o formato é "key1 \| Key2 (SHA 256 hash da chave), SasSignature (hash SHA 256 do token SAS)". Por exemplo: `key1(0A0XE8AADA354H19722ED12342443F0DC8FAF3E6GF8C8AD805DE6D563E0E5F8A),SasSignature(04D64C2B3A704145C9F1664F201123467A74D72DA72751A9137DDAA732FA03CF)`. Quando o tipo de autenticação é `OAuth` , o formato é "hash SHA 256 do token OAuth". Por exemplo: `B3CC9D5C64B3351573D806751312317FE4E910877E7CBAFA9D95E0BE923DW25C`<br> Para outros tipos de autenticação, não há nenhum campo tokenHash. |
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
