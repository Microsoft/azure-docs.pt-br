---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171624"
---
Como com tokens de acesso, se um token do AD do Azure não for definido, você deve tratar o evento TokenRequired ou implementar o método tokenRequired no protocolo delegado.

Você pode manipular o evento de forma síncrona, definindo a propriedade nos argumentos de evento.
