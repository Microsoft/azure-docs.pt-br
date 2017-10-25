---
title: "Políticas de recurso do Azure para marcas | Microsoft Docs"
description: "Fornece exemplos de políticas de recurso para gerenciamento de marcas em recursos"
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
ms.date: 08/24/2017
ms.author: tomfitz
ms.openlocfilehash: 469bd8d637337e5900ea84c6bfaf88064695fb7e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="apply-resource-policies-for-tags"></a>Aplicar políticas de recurso a marcas

Este tópico fornece regras de política comuns que podem ser aplicadas para garantir o uso consistente de marcas em recursos.

A aplicação de uma política de marca a um grupo de recursos ou a uma assinatura com recursos existentes não aplica retroativamente a política a esses recursos. Para impor as políticas a esses recursos, dispare uma atualização para os recursos existentes. Este artigo inclui um exemplo do PowerShell para disparar uma atualização.

## <a name="ensure-all-resources-in-a-resource-group-have-a-tagvalue"></a>Garantir que todos os recursos em um grupo de recursos tenham um valor/marca

Um requisito comum é o de que todos os recursos em um grupo de recursos tenham uma marca e um valor específicos. Esse requisito geralmente é necessário para controlar custos por departamento. As seguintes condições devem ser atendidas:

* A marca necessária e o valor são acrescentados aos recursos novos e atualizados que não têm a marca.
* A marca e o valor necessários não podem ser removidos de nenhum recurso existente.

Você pode atender a esse requisito aplicando a um grupo de recursos.

| ID | Descrição |
| ---- | ---- |
| 2a0e14a6-b0a6-4fab-991a-187a4f81c498 | Aplica uma marca necessária e seu valor padrão quando não for especificado pelo usuário. |
| 1e30110a-5ceb-460c-a204-c1c3969c6d62 | Impõe uma marca necessária e seu valor. |

### <a name="powershell"></a>PowerShell

O seguinte script PowerShell atribui as duas definições de diretiva interna para um grupo de recursos. Antes de executar o script, atribua todas as marcas necessárias para o grupo de recursos. Cada marca no grupo de recursos é necessária para os recursos do grupo. Para atribuir a todos os grupos de recursos em sua assinatura, não fornece o `-Name` parâmetro ao obter os grupos de recursos.

```powershell
$appendpolicy = Get-AzureRmPolicyDefinition | Where-Object {$_.Name -eq '2a0e14a6-b0a6-4fab-991a-187a4f81c498'}
$denypolicy = Get-AzureRmPolicyDefinition | Where-Object {$_.Name -eq '1e30110a-5ceb-460c-a204-c1c3969c6d62'}

$rgs = Get-AzureRMResourceGroup -Name ExampleGroup

foreach($rg in $rgs)
{
    $tags = $rg.Tags
    foreach($key in $tags.Keys){
        $key 
        $tags[$key]
        New-AzureRmPolicyAssignment -Name ("append"+$key+"tag") -PolicyDefinition $appendpolicy -Scope $rg.ResourceId -tagName $key -tagValue  $tags[$key]
        New-AzureRmPolicyAssignment -Name ("denywithout"+$key+"tag") -PolicyDefinition $denypolicy -Scope $rg.ResourceId -tagName $key -tagValue  $tags[$key]
    }
}
```

Depois de atribuir as políticas, você pode disparar uma atualização para todos os recursos existentes para impor as políticas de marca que você adicionou. O script a seguir mantém outras marcas que existiam nos recursos:

```powershell
$group = Get-AzureRmResourceGroup -Name "ExampleGroup" 

$resources = Find-AzureRmResource -ResourceGroupName $group.ResourceGroupName 

foreach($r in $resources)
{
    try{
        $r | Set-AzureRmResource -Tags ($a=if($r.Tags -eq $NULL) { @{}} else {$r.Tags}) -Force -UsePatchSemantics
    }
    catch{
        Write-Host  $r.ResourceId + "can't be updated"
    }
}
```

## <a name="require-tags-for-a-resource-type"></a>Exigir marcas para um tipo de recurso
O exemplo a seguir mostra como aninhar operadores lógicos para exigir uma marca de aplicativo somente para um tipo de recurso especificado (nesse caso, contas de armazenamento).

```json
{
    "if": {
        "allOf": [
          {
            "not": {
              "field": "tags",
              "containsKey": "application"
            }
          },
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          }
        ]
    },
    "then": {
        "effect": "audit"
    }
}
```

## <a name="require-tag"></a>Exigir marca
A seguinte política nega as solicitações que não têm uma marca contendo a chave "costCenter" (qualquer valor pode ser aplicado):

```json
{
  "if": {
    "not" : {
      "field" : "tags",
      "containsKey" : "costCenter"
    }
  },
  "then" : {
    "effect" : "deny"
  }
}
```

## <a name="next-steps"></a>Próximas etapas
* Depois de definir uma regra de política (conforme mostrado nos exemplos anteriores), você precisará criar a definição de política e atribuí-la a um escopo. O escopo pode ser uma assinatura, grupo de recursos ou recurso. Para atribuir políticas por meio do portal, consulte [Usar o portal do Azure para atribuir e gerenciar políticas de recurso](resource-manager-policy-portal.md). Para atribuir políticas por meio da API REST, do PowerShell ou da CLI do Azure, consulte [Atribuir e gerenciar políticas por meio de script](resource-manager-policy-create-assign.md).
* Para ver uma introdução às políticas de recurso, confira [Visão geral da política de recurso](resource-manager-policy.md).
* Para obter orientação sobre como as empresas podem usar o Resource Manager para gerenciar assinaturas de forma eficaz, consulte [Azure enterprise scaffold – controle de assinatura prescritivas](resource-manager-subscription-governance.md).

