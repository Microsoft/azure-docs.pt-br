---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 63725d55e2b2935ec6a899789249259b096865c3
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171617"
---
### <a name="access-tokens"></a>Tokens de acesso

Tokens de acesso são um método mais robusto para autenticar com âncoras espacial do Azure. Especialmente à medida que você prepare seu aplicativo para uma implantação de produção. O resumo dessa abordagem é configurar um serviço de back-end que seu aplicativo cliente pode autenticar com segurança. Suas interfaces de serviço de back-end com o AAD em tempo de execução e com o Azure espacial âncoras de serviço de Token seguro para solicitar um Token de acesso. Esse token é, em seguida, entregue ao aplicativo cliente e usado no SDK para autenticar com âncoras espacial do Azure.
