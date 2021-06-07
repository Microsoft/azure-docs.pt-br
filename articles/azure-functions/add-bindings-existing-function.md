---
title: Conectar funções a outros serviços do Azure
description: Saiba como adicionar associações que se conectam a outros serviços do Azure a uma função existente no seu projeto Azure Functions.
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: d1c6f5bb8ca5fcf995b8a8d326abbec96f1d2e35
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258194"
---
# <a name="connect-functions-to-azure-services-using-bindings"></a>Conectar funções a serviços do Azure usando associações

Quando você cria uma função, um código de gatilho específico a um idioma é adicionado em seu projeto de um conjunto de modelos de gatilho. Se você quiser conectar sua função a outros serviços usando associações de entrada ou saída, você precisará adicionar definições de associação específicas em sua função. Para saber mais sobre associações, confira [Conceitos de gatilhos e de associações do Azure Functions](functions-triggers-bindings.md).

## <a name="local-development"></a>Desenvolvimento local       

Ao desenvolver funções localmente, você precisa atualizar o código da função para adicionar associações. Usar Visual Studio Code pode facilitar a adição de associações a uma função.  

### <a name="visual-studio-code"></a>Visual Studio Code

Quando você usa Visual Studio Code para desenvolver sua função e sua função usa uma function.jsno arquivo, a extensão Azure Functions pode adicionar automaticamente uma associação a um function.jsexistente no arquivo. Para saber mais, consulte [Adicionar associações de entrada e saída](functions-develop-vs-code.md#add-input-and-output-bindings).   

### <a name="manually-add-bindings-based-on-examples"></a>Adicionar associações manualmente com base em exemplos

Ao adicionar uma associação a uma função existente, você precisará atualizar o código de função e o function.jsno arquivo de configuração, se usado pelo seu idioma. Tanto a biblioteca de classes do .NET quanto as funções Java usam atributos em vez de function.jsem, portanto, você precisará atualizá-la em vez disso.

Use a tabela a seguir para encontrar exemplos de tipos de ligação específicos que você pode usar para orientá-lo na atualização de uma função existente. Primeiro, escolha a guia idioma que corresponde ao seu projeto. 

[!INCLUDE [functions-bindings-code-example-chooser](../../includes/functions-bindings-code-example-chooser.md)]

## <a name="azure-portal"></a>Portal do Azure

Ao desenvolver suas funções no [portal do Azure](https://portal.azure.com), você adiciona associações de entrada e saída na guia **integrar** para uma determinada função. As novas associações são adicionadas ao function.jsno arquivo ou aos atributos do método, dependendo do seu idioma. Os artigos a seguir mostram exemplos de como adicionar associações a uma função existente no Portal:

+ [Associação de saída de armazenamento de filas](functions-integrate-storage-queue-output-binding.md)
+ [Azure Cosmos DB Associação de saída](functions-integrate-store-unstructured-data-cosmosdb.md)

## <a name="next-steps"></a>Próximas etapas

+ [Conceitos de gatilhos e de associações do Azure Functions](functions-triggers-bindings.md)
