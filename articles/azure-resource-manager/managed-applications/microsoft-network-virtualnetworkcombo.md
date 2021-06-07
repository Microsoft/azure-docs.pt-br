---
title: Elemento de interface do usuário VirtualNetworkCombo
description: Descreve o elemento de interface do usuário Microsoft.Network.VirtualNetworkCombo para o Portal do Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 711f5293b205c1f500c6d9e08154342285ef959b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87033183"
---
# <a name="microsoftnetworkvirtualnetworkcombo-ui-element"></a>Elemento de interface do usuário Microsoft.Network.VirtualNetworkCombo

Um grupo de controles para selecionar uma rede virtual nova ou existente.

## <a name="ui-sample"></a>Exemplo de interface do usuário

Quando o usuário selecionar uma nova rede virtual, o usuário pode personalizar cada nome e prefixo de endereço da sub-rede. Configurar sub-redes é opcional.

![Novo Microsoft.Network.VirtualNetworkCombo](./media/managed-application-elements/microsoft-network-virtualnetworkcombo-new.png)

Quando o usuário escolher uma rede virtual existente, deve mapear cada sub-rede requerida pelo modelo de implantação para uma sub-rede existente. Configurar sub-redes, nesse caso, é necessário.

![Microsoft.Network.VirtualNetworkCombo existente](./media/managed-application-elements/microsoft-network-virtualnetworkcombo-existing.png)

## <a name="schema"></a>Esquema

```json
{
  "name": "element1",
  "type": "Microsoft.Network.VirtualNetworkCombo",
  "label": {
    "virtualNetwork": "Virtual network",
    "subnets": "Subnets"
  },
  "toolTip": {
    "virtualNetwork": "",
    "subnets": ""
  },
  "defaultValue": {
    "name": "vnet01",
    "addressPrefixSize": "/16"
  },
  "constraints": {
    "minAddressPrefixSize": "/16"
  },
  "options": {
    "hideExisting": false
  },
  "subnets": {
    "subnet1": {
      "label": "First subnet",
      "defaultValue": {
        "name": "subnet-1",
        "addressPrefixSize": "/24"
      },
      "constraints": {
        "minAddressPrefixSize": "/24",
        "minAddressCount": 12,
        "requireContiguousAddresses": true
      }
    },
    "subnet2": {
      "label": "Second subnet",
      "defaultValue": {
        "name": "subnet-2",
        "addressPrefixSize": "/26"
      },
      "constraints": {
        "minAddressPrefixSize": "/26",
        "minAddressCount": 8,
        "requireContiguousAddresses": true
      }
    }
  },
  "visible": true
}
```

## <a name="sample-output"></a>Saída de exemplo

```json
{
  "name": "vnet01",
  "resourceGroup": "rg01",
  "addressPrefixes": ["10.0.0.0/16"],
  "newOrExisting": "new",
  "subnets": {
    "subnet1": {
      "name": "subnet-1",
      "addressPrefix": "10.0.0.0/24",
      "startAddress": "10.0.0.1"
    },
    "subnet2": {
      "name": "subnet-2",
      "addressPrefix": "10.0.1.0/26",
      "startAddress": "10.0.1.1"
    }
  }
}
```

## <a name="remarks"></a>Comentários

- Se especificado, o primeiro prefixo de endereço ou tamanho `defaultValue.addressPrefixSize` não sobreposto é determinado automaticamente com base nas redes virtuais existentes na assinatura do usuário.
- O valor padrão para `defaultValue.name` e `defaultValue.addressPrefixSize` é **null**.
- `constraints.minAddressPrefixSize`deve ser especificado. Todas as redes virtuais existentes com um espaço de endereço menor do que o valor especificado estarão indisponíveis para seleção.
- `subnets`deve ser especificado e `constraints.minAddressPrefixSize` deve ser especificado para cada sub-rede.
- Ao criar uma nova rede virtual, cada prefixo de endereço de sub-rede será calculado automaticamente com base no prefixo de endereço da rede virtual e os respectivos `addressPrefixSize`.
- Ao usar uma rede virtual existente, todas as sub-redes menores que os respectivos `constraints.minAddressPrefixSize` ficarão indisponíveis para seleção. Além disso, se especificado, as sub-redes que não contêm pelo menos `minAddressCount` endereços disponíveis ficam indisponíveis para seleção. O valor padrão é **0**. Para garantir que os endereços disponíveis são contíguos, especifique **true** para `requireContiguousAddresses`. O valor padrão é **true**.
- Não há suporte para a criação de sub-redes em uma rede virtual existente.
- Se `options.hideExisting` é **true**, o usuário não pode escolher uma rede virtual existente. O valor padrão é **false**.

## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução à criação de definições de interface do usuário, consulte [Introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de interface do usuário, consulte [Elementos de CreateUiDefinition](create-uidefinition-elements.md).
