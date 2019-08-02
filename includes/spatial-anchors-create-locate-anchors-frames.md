---
ms.openlocfilehash: 10eb16dc1757255c77ed8967dc132f42660e070f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171625"
---
## <a name="provide-frames-to-the-session"></a>Fornecer quadros à sessão

A sessão de âncora espacial funciona, mapeando o espaço em torno do usuário. Isso ajuda a determinar onde se encontram as âncoras. Plataformas móveis (iOS e Android) requerem uma chamada de nativa para o feed para obter quadros da biblioteca de AR de sua plataforma de câmera. Em contraste, HoloLens está constantemente verificando o ambiente, portanto, não há nenhuma necessidade de uma chamada específica, como com o Mobile.