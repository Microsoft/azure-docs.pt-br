---
title: incluir arquivo
description: incluir arquivo
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/02/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 6fc1f4152b2e16e1597c018e5af6e0245b075c3b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "78255809"
---
## <a name="robots933456-in-logs"></a>robots933456 em logs

Você poderá ver a seguinte mensagem nos logs de contêiner:

```
2019-04-08T14:07:56.641002476Z "-" - - [08/Apr/2019:14:07:56 +0000] "GET /robots933456.txt HTTP/1.1" 404 415 "-" "-"
```

Você pode ignorar essa mensagem com segurança. `/robots933456.txt` é um caminho de URL fictício que o Serviço de Aplicativo usa para verificar se o contêiner é capaz de atender a solicitações. Uma resposta 404 indica apenas que o caminho não existe, mas informa ao Serviço de Aplicativo que o contêiner está íntegro e pronto para responder a solicitações.

