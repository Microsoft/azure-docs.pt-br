---
title: Elemento de interface do usuário ArmApiControl
description: Descreve o elemento de interface do usuário Microsoft. Solutions. ArmApiControl para portal do Azure. Usado para chamar operações de API.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: bbe36e072d10b81c421331b2212d8b161afd2693
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87095719"
---
# <a name="microsoftcommonarmapicontrol-ui-element"></a>Elemento de interface do usuário Microsoft. Common. ArmApiControl

ArmApiControl permite obter resultados de uma operação de API Azure Resource Manager. Use os resultados para popular o conteúdo dinâmico em outros controles.

## <a name="ui-sample"></a>Exemplo de interface do usuário

Não há interface do usuário para este controle.

## <a name="schema"></a>Esquema

O exemplo a seguir mostra o esquema para este controle:

```json
{
    "name": "testApi",
    "type": "Microsoft.Solutions.ArmApiControl",
    "request": {
        "method": "{HTTP-method}",
        "path": "{path-for-the-URL}",
        "body": {
            "key1": "val1",
            "key2": "val2"
        }
    }
}
```

## <a name="sample-output"></a>Saída de exemplo

A saída do controle não é exibida para o usuário. Em vez disso, o resultado da operação é usado em outros controles.

## <a name="remarks"></a>Comentários

- A `request.method` propriedade especifica o método http. Somente `GET` ou `POST` são permitidos.
- A `request.path` propriedade especifica o caminho relativo da URL. Pode ser um caminho estático ou pode ser construído dinamicamente por meio da referência de valores de saída dos outros controles.
- A propriedade `request.body` é opcional. Use-o para especificar um corpo JSON que é enviado com a solicitação. O corpo pode ser um conteúdo estático ou construído dinamicamente fazendo referência a valores de saída de outros controles.

## <a name="example"></a>Exemplo

No exemplo a seguir, o `providersApi` elemento chama uma API para obter uma matriz de objetos de provedor.

A `allowedValues` Propriedade do `providersDropDown` elemento é configurada para obter os nomes dos provedores. Ele os exibe na lista suspensa.

```json
{
    "name": "providersApi",
    "type": "Microsoft.Solutions.ArmApiControl",
    "request": {
        "method": "GET",
        "path": "[concat(subscription().id, '/providers/Microsoft.Network/expressRouteServiceProviders?api-version=2019-02-01')]"
    }
},
{
    "name": "providerDropDown",
    "type": "Microsoft.Common.DropDown",
    "label": "Provider",
    "toolTip": "The provider that offers the express route connection.",
    "constraints": {
        "allowedValues": "[map(steps('settings').providersApi.value, (item) => parse(concat('{\"label\":\"', item.name, '\",\"value\":\"', item.name, '\"}')))]",
        "required": true
    },
    "visible": true
}
```

Para obter um exemplo de como usar o ArmApiControl para verificar a disponibilidade de um nome de recurso, consulte [Microsoft. Common. TextBox](microsoft-common-textbox.md).

## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução à criação de definições de interface do usuário, consulte [Introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de interface do usuário, consulte [Elementos de CreateUiDefinition](create-uidefinition-elements.md).
