---
title: Erros de recurso pai do Azure | Microsoft Docs
description: Descreve como resolver erros ao trabalhar com um recurso pai.
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 09/13/2017
ms.author: tomfitz
ms.openlocfilehash: e59147c4ac18f730f27b9d4aa9c008f219881065
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-errors-for-parent-resources"></a>Resolva erros de recursos pai

Este artigo descreve os erros com que você pode se deparar durante a implantação de um recurso que é dependente de um recurso pai.

## <a name="symptom"></a>Sintoma

Ao implantar um recurso que é filho de outro recurso, você receberá o seguinte erro:

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

## <a name="cause"></a>Causa

Quando um recurso é filho de outro recurso, o recurso pai deve existir antes da criação do recurso filho. O nome do recurso filho inclui o nome do pai. Por exemplo, um Banco de Dados SQL pode ser definido como:

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

No entanto, se você não especificar uma dependência no servidor, a implantação do banco de dados poderá ser iniciada antes que o servidor tenha sido implantado.

## <a name="solution"></a>Solução

Para resolver esse erro, inclua uma dependência.

```json
"dependsOn": [
    "[variables('databaseServerName')]"
]
```

Para obter mais informações, consulte [Definir a ordem de implantação dos recursos em modelos do Azure Resource Manager](resource-group-define-dependencies.md).