---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 6a359cdd44cc0c0cfbd93bd23b69a67a641c7fbb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "80673266"
---
## <a name="update-the-tests"></a>Atualizar os testes

Como o arquétipo também cria um conjunto de testes, você precisa atualizar esses testes para manipular o novo parâmetro `msg` na assinatura do método `run`.  

Procure a localização do código de teste em _src/test/java_, abra o arquivo de projeto *Function.java* e substitua a linha de código em `//Invoke` pelo código a seguir.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/test/java/com/function/FunctionTest.java" range="48-50":::