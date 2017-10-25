---
title: "Políticas de recursos do Azure para convenções de nomenclatura | Microsoft Docs"
description: "Descreve as políticas do Azure Resource Manager para convenções de nomenclatura de recursos."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2017
ms.author: tomfitz
ms.openlocfilehash: 51b3519bbba8cb4c768bfdd7dadf92fced434f22
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="apply-resource-policies-for-names-and-text"></a>Aplicar políticas de recursos a nomes e texto
Este tópico mostra várias [políticas de recursos](resource-manager-policy.md) que podem ser aplicadas para estabelecer as convenções de nomenclatura e texto. Essas políticas garantem a consistência de nomes de recursos e valores de marcação. 

## <a name="set-naming-convention-with-wildcard"></a>Definir a convenção de nomenclatura com um curinga
O exemplo a seguir mostra o uso de curingas, que é compatível com a condição **like**. A condição declara que, se o nome corresponder ao padrão mencionado (namePrefix\*nameSuffix), a solicitação será negada:

```json
{
  "if": {
    "not": {
      "field": "name",
      "like": "namePrefix*nameSuffix"
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="set-naming-convention-with-pattern"></a>Definir a convenção de nomenclatura com um padrão

Para especificar que nomes de recurso correspondem a um padrão, use a condição match. O exemplo a seguir exige que os nomes comecem com `contoso` e contenham seis letras adicionais:

```json
{
  "if": {
    "not": {
      "field": "name",
      "match": "contoso??????"
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="set-date-pattern-for-tag-value"></a>Definir um padrão de data para um valor de marcação

Para exigir um padrão de data de dois dígitos, hífen, três letras, traço e quatro dígitos, use:

```json
{
  "if": {
    "field": "tags.date",
    "match": "##-???-####"
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="next-steps"></a>Próximas etapas
* Depois de definir uma regra de política (conforme mostrado nos exemplos anteriores), você precisará criar a definição de política e atribuí-la a um escopo. O escopo pode ser uma assinatura, grupo de recursos ou recurso. Para atribuir políticas por meio do portal, consulte [Usar o portal do Azure para atribuir e gerenciar políticas de recurso](resource-manager-policy-portal.md). Para atribuir políticas por meio da API REST, do PowerShell ou da CLI do Azure, consulte [Atribuir e gerenciar políticas por meio de script](resource-manager-policy-create-assign.md). 
* Para obter orientação sobre como as empresas podem usar o Resource Manager para gerenciar assinaturas de forma eficaz, consulte [Azure enterprise scaffold – controle de assinatura prescritivas](resource-manager-subscription-governance.md).

