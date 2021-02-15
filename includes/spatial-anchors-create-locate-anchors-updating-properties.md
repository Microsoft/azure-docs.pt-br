---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "67171635"
---
## <a name="update-properties"></a>Atualizar as propriedades

Para atualizar as propriedades em uma âncora, use o método `UpdateAnchorProperties()`. Se dois ou mais dispositivos tentarem atualizar as propriedades para a mesma âncora ao mesmo tempo, usaremos um modelo de simultaneidade otimista. Ou seja, a primeira gravação ganhará.  Todas as outras gravações receberão um erro de "Simultaneidade": uma atualização das propriedades será necessária antes que seja possível tentar novamente.
