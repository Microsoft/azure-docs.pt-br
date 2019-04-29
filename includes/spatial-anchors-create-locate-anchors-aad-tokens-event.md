---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232641"
---
Como com tokens de acesso, se um token do AD do Azure não for definido, você deve tratar o evento TokenRequired ou implementar o método tokenRequired no protocolo delegado.

Você pode manipular o evento de forma síncrona, definindo a propriedade nos argumentos de evento.
