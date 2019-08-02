---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171643"
---
## <a name="pause-reset-or-stop-the-session"></a>Pausar, redefinir ou parar a sessão

Para parar a sessão temporária, você pode invocar `Stop()`. Isso irá parar qualquer processamento de ambiente e observadores, mesmo se você invocar ProcessFrame(). Em seguida, você pode invocar `Start()` para continuar o processamento. Ao retomar, dados de ambiente já capturados na sessão são mantidos.
