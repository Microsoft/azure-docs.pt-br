---
ms.openlocfilehash: e7e3a31663e6c166b912ba20166ba0f0f769008a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "83006453"
---
## <a name="setting-up-the-library"></a>Configurar a biblioteca

Invoque `Start()` para habilitar sua sessão a processar os dados do ambiente.

Para manipular eventos gerados pela sua sessão, defina a propriedade `delegate` de sua sessão para um objeto, como o modo de exibição. Esse objeto deve implementar o protocolo `SSCCloudSpatialAnchorSessionDelegate`.
