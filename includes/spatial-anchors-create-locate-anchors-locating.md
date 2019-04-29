---
ms.openlocfilehash: 52dfbfca5f79a7f92848ea39eddc00aa10f05ff1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232618"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Localize uma âncora de nuvem espacial

Ser capaz de localizar uma âncora de nuvem carregado anteriormente espacial é um dos motivos principais para usar a biblioteca âncoras espacial do Azure. Para localizar as âncoras de nuvem espacial, você precisará saber seus identificadores. As IDs de âncora podem ser armazenadas no serviço de back-end do seu aplicativo e acessível a todos os dispositivos que podem autenticar corretamente a ele. Para obter um exemplo desse tipo, consulte [Tutorial: Compartilhar âncoras espacial em todos os dispositivos](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Criar uma instância de um `AnchorLocateCriteria` do objeto, defina os identificadores que você está procurando e invocar o `CreateWatcher` método na sessão, fornecendo seu `AnchorLocateCriteria`.
