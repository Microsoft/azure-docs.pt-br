---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232604"
---
## <a name="pause-reset-or-stop-the-session"></a>Pausar, redefinir ou parar a sessão

Para parar a sessão temporária, você pode invocar `Stop()`. Isso irá parar qualquer processamento de ambiente e observadores, mesmo se você invocar ProcessFrame(). Em seguida, você pode invocar `Start()` para continuar o processamento. Ao retomar, dados de ambiente já capturados na sessão são mantidos.
