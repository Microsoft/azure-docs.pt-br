---
title: Referência de artefato de definição de exibição
description: Fornece um exemplo de artefato de definição de exibição para aplicativos gerenciados do Azure. O nome do arquivo é viewDefinition.jsem.
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: 5173db54abef132a4a4d5d117881352ca37d6b23
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "75651196"
---
# <a name="reference-view-definition-artifact"></a>Referência: Exibir artefato de definição

Este artigo é uma referência para um *viewDefinition.jsno* artefato em aplicativos gerenciados do Azure. Para obter mais informações sobre criação de configurações de exibições, consulte [Exibir artefato de definição](concepts-view-definition.md).

## <a name="view-definition"></a>Exibir definição

O JSON a seguir mostra um exemplo de *viewDefinition.jsno* arquivo para aplicativos gerenciados do Azure:

```json
{
  "views": [{
    "kind": "Overview",
    "properties": {
      "header": "Welcome to your Demo Azure Managed Application",
      "description": "This Managed application with Custom Provider is for demo purposes only.",
      "commands": [{
          "displayName": "Ping Action",
          "path": "/customping",
          "icon": "LaunchCurrent"
      }]
    }
  },
  {
    "kind": "CustomResources",
    "properties": {
      "displayName": "Users",
      "version": "1.0.0.0",
      "resourceType": "users",
      "createUIDefinition": {
        "parameters": {
          "steps": [{
            "name": "add",
            "label": "Add user",
            "elements": [{
              "name": "name",
              "label": "User's Full Name",
              "type": "Microsoft.Common.TextBox",
              "defaultValue": "",
              "toolTip": "Provide a full user name.",
              "constraints": { "required": true }
            },
            {
              "name": "location",
              "label": "User's Location",
              "type": "Microsoft.Common.TextBox",
              "defaultValue": "",
              "toolTip": "Provide a Location.",
              "constraints": { "required": true }
            }]
          }],
          "outputs": {
            "name": "[steps('add').name]",
            "properties": {
              "FullName": "[steps('add').name]",
              "Location": "[steps('add').location]"
            }
          }
        }
      },
      "commands": [{
        "displayName": "Custom Context Action",
        "path": "users/contextAction",
        "icon": "Start"
      }],
      "columns": [
        { "key": "properties.FullName", "displayName": "Full Name" },
        { "key": "properties.Location", "displayName": "Location", "optional": true }
      ]
    }
  }]
}
```

## <a name="next-steps"></a>Próximas etapas

- [Tutorial: criar um aplicativo gerenciado com ações e recursos personalizados](tutorial-create-managed-app-with-custom-provider.md)
- [Referência: artefato dos elementos da interface do usuário](reference-createuidefinition-artifact.md)
- [Referência: artefato do modelo de implantação](reference-main-template-artifact.md)