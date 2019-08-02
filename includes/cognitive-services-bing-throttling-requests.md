---
author: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/09/2018
ms.author: nitinme
ms.openlocfilehash: 1d8340054ace25a0cdc36eef9c3d5b4238a6f99b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172206"
---
O serviço e o seu tipo de subscrição determinam o número de consultas por segundo (QPS) que você pode efetuar. Verifique se seu aplicativo inclui a lógica para permanecer dentro da sua cota. Se o limite de QPS for atingido ou excedido, a solicitação falhará e um código de status HTTP 429 será retornado. A resposta inclui o cabeçalho `Retry-After`, que indica o quanto você deve aguardar antes de enviar outra solicitação.

## <a name="denial-of-service-versus-throttling"></a>Negação de serviço versus limitação

O serviço faz uma diferenciação entre um ataque de negação de serviço (DoS) e uma violação QPS. Se o serviço suspeitar de um ataque DoS, a solicitação terá êxito (o código de status HTTP será 200 OK). No entanto, o corpo da resposta estará vazio.