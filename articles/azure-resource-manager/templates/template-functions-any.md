---
title: Funções de modelo – qualquer
description: Descreve a função any que está disponível em bicep para converter tipos.
ms.topic: conceptual
author: tfitzmac
ms.author: tomfitz
ms.service: azure-resource-manager
ms.subservice: templates
ms.date: 03/02/2021
ms.openlocfilehash: b0cb51c9a79d1100de7f1ef32fe326eddcdd6dcc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101744256"
---
# <a name="any-function-for-bicep"></a>Qualquer função para bicep

O bicep dá suporte a uma função chamada `any()` para resolver erros de tipo no sistema de tipos bicep. Você usa essa função quando o formato do valor que você fornece não corresponde ao que o sistema de tipos espera. Por exemplo, se a propriedade exigir um número, mas você precisar fornecê-la como uma cadeia de caracteres, como `'0.5'` . Use a `any()` função para suprimir o erro relatado pelo sistema de tipos.

Essa função não existe no tempo de execução do Azure Resource Manager template. Ele é usado apenas por bicep e não é emitido no JSON para o modelo interno.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="any"></a>any

`any(value)`

Retorna um valor compatível com qualquer tipo de dados.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| value | Sim | todos os tipos | O valor a ser convertido em um tipo compatível. |

### <a name="return-value"></a>Retornar valor

O valor em um formulário que é compatível com qualquer tipo de dados.

### <a name="examples"></a>Exemplos

O modelo de exemplo a seguir mostra como usar a `any()` função para fornecer valores numéricos como cadeias de caracteres.

```bicep
resource wpAci 'microsoft.containerInstance/containerGroups@2019-12-01' = {
  name: 'wordpress-containerinstance'
  location: location
  properties: {
    containers: [
      {
        name: 'wordpress'
        properties: {
          ...
          resources: {
            requests: {
              cpu: any('0.5')
              memoryInGB: any('0.7')
            }
          }
        }
      }
    ]
  }
}
```

A função funciona em qualquer valor atribuído em bicep. O exemplo a seguir usa `any()` com uma expressão Ternário como um argumento.  

```bicep
publicIPAddress: any((pipId == '') ? null : {
  id: pipId
})
```

## <a name="next-steps"></a>Próximas etapas

Para usos mais complexos da `any()` função, consulte os exemplos a seguir:

* [Recursos filho que exigem nomes específicos](https://github.com/Azure/bicep/blob/main/docs/examples/201/api-management-create-all-resources/main.bicep#L246)
* [Uma propriedade de recurso não definida no tipo do recurso, mesmo que exista](https://github.com/Azure/bicep/blob/main/docs/examples/201/log-analytics-with-solutions-and-diagnostics/main.bicep#L26)

