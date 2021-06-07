---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 7ef3bd0f401ba54d56ed42df34cd2e761681dbc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96904050"
---
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="add-an-output-binding-definition-to-the-function"></a>Adicionar uma definição de associação de saída à função

Embora uma função possa ter apenas um gatilho, ela pode ter várias associações de entrada e saída, o que permite que você se conecte a outros serviços e recursos do Azure sem escrever um código de integração personalizado. 
::: zone-end
::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
Você declara essas associações no arquivo *function.json* em sua pasta de funções. No início rápido anterior, o arquivo *function.json* na pasta *HttpExample* contém duas associações na coleção `bindings`:  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-python"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-powershell"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json" range="2-18":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript, programming-language-powershell, programming-language-typescript"  
Cada associação tem, pelo menos, um tipo, uma direção e um nome. No exemplo acima, a primeira associação é do tipo `httpTrigger` com a direção `in`. Para a direção de `in`, `name` especifica o nome de um parâmetro de entrada que é enviado para a função quando invocado pelo gatilho.  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
A segunda associação na coleção é denominada `res`. Essa associação `http` é uma associação de saída (`out`) usada para gravar a resposta HTTP. 

Para fazer uma gravação em uma fila do Armazenamento do Azure por meio dessa função, adicione uma associação `out` do tipo `queue` com o nome `msg`, conforme mostrado no código abaixo:

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python"  
A segunda associação na coleção é do tipo `http` com a direção `out`; nesse caso, o `name` especial de `$return` indica que essa associação usa o valor retornado da função em vez de fornecer um parâmetro de entrada.

Para fazer uma gravação em uma fila do Armazenamento do Azure por meio dessa função, adicione uma associação `out` do tipo `queue` com o nome `msg`, conforme mostrado no código abaixo:

:::code language="json" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-powershell"  
A segunda associação na coleção é denominada `res`. Essa associação `http` é uma associação de saída (`out`) usada para gravar a resposta HTTP. 

Para fazer uma gravação em uma fila do Armazenamento do Azure por meio dessa função, adicione uma associação `out` do tipo `queue` com o nome `msg`, conforme mostrado no código abaixo:

:::code language="json" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
Nesse caso, `msg` é fornecido à função como um argumento de saída. Para um tipo `queue`, também é necessário especificar o nome da fila em `queueName` e fornecer o *nome* da conexão do Armazenamento do Azure (por meio de *local.settings.json*) em `connection`. 
::: zone-end  
