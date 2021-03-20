---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 3e5afeffa6f47ff79e31f189abc6d5e26d8f2586
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "86050501"
---
Todos os blobs no armazenamento do Azure devem residir em um contêiner. O contêiner faz parte do nome do blob. Por exemplo, `mycontainer` é o nome do contêiner nesses URIs do blob de exemplo:

```http
https://storagesample.blob.core.windows.net/mycontainer/blob1.txt
https://storagesample.blob.core.windows.net/mycontainer/photos/myphoto.jpg
```

Um nome de contêiner deve ser um nome DNS válido e estar em conformidade com as seguintes regras de nomenclatura:

1. Os nomes de contêiner devem começar com uma letra ou número e podem conter apenas letras, números e o caractere traço (-).
2. Cada caractere traço (-) deve ser imediatamente precedido e seguido por uma letra ou número. Não são permitidos traços consecutivos em nomes de contêiner.
3. Todas as letras do nome de um contêiner devem ser minúsculas.
4. Os nomes de contêiner devem ter de 3 a 63 caracteres.

> [!IMPORTANT]
> Observe que o nome de um contêiner deve sempre estar em minúsculas. Se você incluir uma letra maiúscula em um nome de contêiner ou de alguma forma violar as regras de nomenclatura do contêiner, você receberá um erro 400 (solicitação incorreta). 
> 
> 

