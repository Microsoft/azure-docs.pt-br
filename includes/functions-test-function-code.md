---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: a3f75b7273164abc5318f16e9ab8d9883ff0c0aa
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171704"
---
## <a name="test"></a>Testar a função no Azure

Use o cURL para testar a função implantada. Usando a URL que você copiou da etapa anterior, acrescente a cadeia de consulta `&name=<yourname>` à URL, como no seguinte exemplo:

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

![usando o cURL para chamar a função no Azure.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

Você também pode colar a URL copiada para o endereço do navegador da Web. Acrescente novamente o valor de cadeia de consulta `&name=<yourname>` à URL e execute a solicitação.

![Usando um navegador da Web para chamar a função.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
