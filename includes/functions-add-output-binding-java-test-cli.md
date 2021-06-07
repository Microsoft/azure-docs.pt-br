---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 6a359cdd44cc0c0cfbd93bd23b69a67a641c7fbb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "80673266"
---
## <a name="update-the-tests"></a>Atualizar os testes

Como o arquétipo também cria um conjunto de testes, você precisa atualizar esses testes para manipular o novo parâmetro `msg` na assinatura do método `run`.  

Procure a localização do código de teste em _src/test/java_, abra o arquivo de projeto *Function.java* e substitua a linha de código em `//Invoke` pelo código a seguir.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/test/java/com/function/FunctionTest.java" range="48-50":::