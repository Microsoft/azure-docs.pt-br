---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: d0377e3e918a8431c35c1f5b15a1c4b54eeae3b4
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2020
ms.locfileid: "98760646"
---
## <a name="retrying-after-errors-and-exceptions"></a>Tentar novamente após erros e exceções

Você deve implementar suas próprias políticas de repetição ao chamar MSAL. O MSAL faz chamadas HTTP para o serviço do Azure AD e, ocasionalmente, podem ocorrer falhas. Por exemplo, a rede pode ficar inoperante ou o servidor está sobrecarregado.  

### <a name="http-429"></a>HTTP 429

Quando o STS estiver sobrecarregado com muitas solicitações, ele retornará o erro HTTP 429 com uma dica sobre quanto tempo você deve aguardar até poder tentar novamente no campo de resposta `Retry-After`.