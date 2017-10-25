---
title: "Elemento de interface do usuário PublicIpAddressCombo de aplicativo gerenciado do Azure | Microsoft Docs"
description: "Descreve o elemento Microsoft.Compute.PublicIpAddressCombo da interface do usuário para aplicativos gerenciados do Azure"
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
ms.openlocfilehash: c6fb792852f24fd4bf4c634d1c29581e41e17793
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Elemento de interface do usuário Microsoft.Network.PublicIpAddressCombo
Um grupo de controles para selecionar um endereço IP público novo ou existente. Use esse elemento ao [criar um Aplicativo Gerenciado do Azure](managed-application-publishing.md).

## <a name="ui-sample"></a>Exemplo de interface do usuário
![Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft.network.publicipaddresscombo.png)

- Se o usuário selecionar 'Nenhum' como endereço IP público, a caixa de texto de rótulo do nome de domínio ficará oculta.
- Se o usuário selecionar um endereço IP público existente, a caixa de texto de rótulo do nome de domínio ficará desabilitada. Seu valor é o rótulo de nome de domínio do endereço IP selecionado.
- O sufixo do nome de domínio (por exemplo, westus.cloudapp.azure.com) é atualizado automaticamente com base no local selecionado.

## <a name="schema"></a>Esquema
```json
{
  "name": "element1",
  "type": "Microsoft.Network.PublicIpAddressCombo",
  "label": {
    "publicIpAddress": "Public IP address",
    "domainNameLabel": "Domain name label"
  },
  "toolTip": {
    "publicIpAddress": "",
    "domainNameLabel": ""
  },
  "defaultValue": {
    "publicIpAddressName": "ip01",
    "domainNameLabel": "foobar"
  },
  "constraints": {
    "required": {
      "domainNameLabel": true
    }
  },
  "options": {
    "hideNone": false,
    "hideDomainNameLabel": false,
    "hideExisting": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Comentários
- Se `constraints.required.domainNameLabel` é definido como **true**, o usuário deve fornecer um rótulo de nome de domínio ao criar um novo endereço IP público. Endereços IP públicos existentes sem um rótulo não ficam disponíveis para seleção.
- Se `options.hideNone` é definido como **true**, a opção de selecionar **Nenhum** para o endereço IP público fica oculta. O valor padrão é **false**.
- Se `options.hideDomainNameLabel` é definido como **true**, a caixa de texto do rótulo de nome de domínio fica oculta. O valor padrão é **false**.
- Se `options.hideExisting` é true, o usuário não pode escolher um endereço IP público existente. O valor padrão é **false**.

## <a name="sample-output"></a>Saída de exemplo
Se o usuário não seleciona nenhum endereço IP público,a seguinte saída é esperada:
```json
{
  "newOrExistingOrNone": "none"
}
```

Se o usuário seleciona um endereço IP novo ou existente,a seguinte saída é esperada:
```json
{
  "name": "ip01",
  "resourceGroup": "rg01",
  "domainNameLabel": "foobar",
  "newOrExistingOrNone": "new"
}
```
- Quando `options.hideNone` for especificado como **true**, `newOrExistingOrNone` terá somente o valor **new** ou **existing**.
- Quando `options.hideDomainNameLabel` for especificado como **true**, `domainNameLabel` não será declarado.

## <a name="next-steps"></a>Próximas etapas
* Para obter uma introdução aos aplicativos gerenciados, consulte [Visão geral de aplicativos gerenciados do Azure](managed-application-overview.md).
* Para obter uma introdução à criação de definições de interface do usuário, consulte [Introdução ao CreateUiDefinition](managed-application-createuidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de interface do usuário, consulte [Elementos de CreateUiDefinition](managed-application-createuidefinition-elements.md).
