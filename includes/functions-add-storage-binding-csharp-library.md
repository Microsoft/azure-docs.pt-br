---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 5e1a2622df0038141dd5cb05237f93d5e33e0bfb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "78190901"
---
Em um projeto de biblioteca de classes C#, as associações são definidas como atributos de associação no método de função. O arquivo *function.json* exigido por Functions é gerado automaticamente com base nesses atributos.

Abra o arquivo de projeto *HttpExample.cs* e adicione o seguinte parâmetro à definição do método `Run`:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="17":::

O parâmetro `msg` é um tipo `ICollector<T>`, que representa uma coleção de mensagens que são gravadas em uma associação de saída quando a função é concluída. Nesse caso, a saída é uma fila de armazenamento denominada `outqueue`. A cadeia de conexão para a Conta de armazenamento é definida pelo `StorageAccountAttribute`. Esse atributo indica a configuração que contém a cadeia de conexão da Conta de armazenamento e pode ser aplicada no nível de classe, método ou parâmetro. Nesse caso, você poderia omitir `StorageAccountAttribute` porque já está usando a conta de armazenamento padrão.

A definição do método Executar deve agora se parecer com o seguinte:  

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="14-18":::
