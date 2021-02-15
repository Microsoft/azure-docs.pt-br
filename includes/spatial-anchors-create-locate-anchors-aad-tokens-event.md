---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "67171624"
---
Assim como os tokens de acesso, se um token do Azure AD não estiver definido, você deverá manipular o evento TokenRequired ou implementar o método tokenRequired no protocolo delegado.

Você pode manipular o evento de maneira síncrona definindo a propriedade nos argumentos do evento.
