---
ms.openlocfilehash: 849777ac3ec11915c602861fea0c42a49fb08bcb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "83006433"
---
## <a name="provide-frames-to-the-session"></a>Fornecer quadros para a sessão

A sessão de âncora espacial funciona pelo mapeamento do espaço em torno do usuário. Fazer isso ajuda a determinar o local em que se encontram as âncoras. As plataformas móveis (iOS e Android) exigem uma chamada nativa ao feed da câmera para obter quadros da biblioteca RA da plataforma. Por outro lado, o HoloLens examina constantemente o ambiente e, portanto, não há necessidade de uma chamada específica como em plataformas móveis.