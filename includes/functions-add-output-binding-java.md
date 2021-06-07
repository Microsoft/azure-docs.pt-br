---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: c27f3fea6d2dd8b891220ba06b375b33e6cd950b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "80673462"
---
Em um projeto Java, as associações são definidas como anotações de associação no método de função. O arquivo *function.json* é então gerado automaticamente com base nessas anotações.

Procure a localização do código de função em _src/main/java_, abra o arquivo de projeto *Function.java* e adicione o seguinte parâmetro à definição do método `run`:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="20-21":::

O parâmetro `msg` é um tipo [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding), que representa uma coleção de cadeias de caracteres que são gravadas como mensagens em uma associação de saída quando a função é concluída. Nesse caso, a saída é uma fila de armazenamento denominada `outqueue`. A cadeia de conexão para a conta de armazenamento é definida pelo método `connection`. Em vez da própria cadeia de conexão, passe a configuração de aplicativo que contém a cadeia de conexão da conta de armazenamento.

A definição do método `run` agora deverá ser semelhante ao seguinte exemplo:  

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="16-22":::