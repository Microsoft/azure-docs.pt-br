---
ms.openlocfilehash: a165061b2f927d6302504819ef27c846d76d0f30
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "83006469"
---
## <a name="pause-reset-or-stop-the-session"></a>Pausar, redefinir ou interromper a sessão

Para interromper a sessão temporariamente, você pode invocar `Stop()`. Isso interromperá inspetores e o processamento de ambiente, mesmo se você invocar `ProcessFrame()`. Em seguida, você pode invocar `Start()` para retomar o processamento. Ao retomar, os dados de ambiente já capturados na sessão são mantidos.
