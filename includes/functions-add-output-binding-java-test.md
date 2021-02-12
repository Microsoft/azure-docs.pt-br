---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: 179ae760f146a5ac3041a54065ae12147f3f9bf0
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/23/2020
ms.locfileid: "97739800"
---
Como o arquétipo também cria um conjunto de testes, você precisa atualizar esses testes para manipular o novo parâmetro `msg` na assinatura do método `run`.  

Procure a localização do código de teste em _src/test/java_, abra o arquivo de projeto *Function.java* e substitua a linha de código em `//Invoke` pelo código a seguir.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/test/java/com/function/FunctionTest.java" range="48-50":::
