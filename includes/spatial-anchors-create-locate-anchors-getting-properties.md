---
ms.openlocfilehash: c5ca85c0dfe8d601821a78c02b2230c0909c8003
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232497"
---
Não é possível atualizar o local de uma âncora, depois de ele ter sido criado no serviço – você deve criar uma nova âncora e excluir o antigo para acompanhar uma nova posição.

Se você não precisar localizar uma âncora para atualizar suas propriedades, você pode usar o `GetAnchorPropertiesAsync()` método, que retorna um `CloudSpatialAnchor` objeto com propriedades.
