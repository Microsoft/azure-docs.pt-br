---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: a5f829fc0e153c3a427fd1c7cc7d5c613cd624f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "83648257"
---
O Azure Functions oferece suporte a compartilhamento de recursos entre origens (CORS). O CORS está configurado [no portal](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md#cors) e por meio do [CLI do Azure](/cli/azure/functionapp/cors). A lista de origens permitidas pelo CORS aplica-se ao nível do aplicativo de funções. Com o CORS habilitado, as respostas incluem o cabeçalho `Access-Control-Allow-Origin`. Para obter mais informações, consulte [Compartilhamento de recursos entre origens](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md#cors). 