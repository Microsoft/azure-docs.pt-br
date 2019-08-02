---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171635"
---
## <a name="update-properties"></a>Atualizar propriedades

Para atualizar as propriedades em uma âncora, você deve usar o `UpdateAnchorProperties()` método. Se dois ou mais dispositivos tentarem atualizar as propriedades para a mesma âncora ao mesmo tempo, usamos um modelo de simultaneidade otimista. O que significa que a primeira gravação será a vencedora.  Todas as outras gravações obterá um erro de "Simultaneidade": uma atualização das propriedades seria necessários antes de tentar novamente.
