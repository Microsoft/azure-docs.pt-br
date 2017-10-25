---
title: "Elemento de interface do usuário VirtualNetworkCombo de aplicativo gerenciado do Azure | Microsoft Docs"
description: "Descreve o elemento Microsoft.Compute.VirtualNetworkCombo da interface do usuário para aplicativos gerenciados do Azure"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/12/2017
ms.author: tomfitz
ms.openlocfilehash: 35f4400a9c9f8e1dd03ac24ab3e023b91d774673
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="microsoftnetworkvirtualnetworkcombo-ui-element"></a>Elemento de interface do usuário Microsoft.Network.VirtualNetworkCombo
Um grupo de controles para selecionar uma rede virtual nova ou existente. Use esse elemento ao [criar um Aplicativo Gerenciado do Azure](managed-application-publishing.md).

## <a name="ui-sample"></a>Exemplo de interface do usuário
![Microsoft.Network.VirtualNetworkCombo](./media/managed-application-elements/microsoft.network.virtualnetworkcombo.png)

- No wireframe superior, o usuário selecionou uma nova rede virtual e pode personalizar o prefixo de nome e endereço de cada sub-rede. Configurar sub-redes, nesse caso, é opcional.
- No wireframe inferior, o usuário escolheu uma rede virtual existente e deve mapear cada sub-rede requerida pelo modelo de implantação para uma sub-rede existente. Configurar sub-redes, nesse caso, é necessário.

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

## <a name="remarks"></a>Comentários
- Se especificado, o primeiro prefixo de endereço ou tamanho `defaultValue.addressPrefixSize` não sobreposto é determinado automaticamente com base nas redes virtuais existentes na assinatura do usuário.
- O valor padrão para `defaultValue.name` e `defaultValue.addressPrefixSize` é **null**.
- `constraints.minAddressPrefixSize`deve ser especificado. Todas as redes virtuais existentes com um espaço de endereço menor do que o valor especificado estarão indisponíveis para seleção.
- `subnets`deve ser especificado e `constraints.minAddressPrefixSize` deve ser especificado para cada sub-rede.
- Ao criar uma nova rede virtual, cada prefixo de endereço de sub-rede será calculado automaticamente com base no prefixo de endereço da rede virtual e os respectivos `addressPrefixSize`.
- Ao usar uma rede virtual existente, todas as sub-redes menores que os respectivos `constraints.minAddressPrefixSize` ficarão indisponíveis para seleção. Além disso, se especificado, as sub-redes que não contêm pelo menos `minAddressCount` endereços disponíveis ficam indisponíveis para seleção.
O valor padrão é **0**. Para garantir que os endereços disponíveis são contíguos, especifique **true** para `requireContiguousAddresses`. O valor padrão é **true**.
- Não há suporte para a criação de sub-redes em uma rede virtual existente.
- Se `options.hideExisting` é **true**, o usuário não pode escolher uma rede virtual existente. O valor padrão é **false**.

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

## <a name="next-steps"></a>Próximas etapas
* Para obter uma introdução aos aplicativos gerenciados, consulte [Visão geral de aplicativos gerenciados do Azure](managed-application-overview.md).
* Para obter uma introdução à criação de definições de interface do usuário, consulte [Introdução ao CreateUiDefinition](managed-application-createuidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de interface do usuário, consulte [Elementos de CreateUiDefinition](managed-application-createuidefinition-elements.md).
