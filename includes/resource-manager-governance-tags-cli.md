---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/20/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: c1259584e91461865b0c7e7bbbd6aced1781827b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67708433"
---
Para adicionar duas marcas a um grupo de recursos, use o comando [az group update](/cli/azure/group):

```azurecli-interactive
az group update -n myResourceGroup --set tags.Environment=Test tags.Dept=IT
```

Vamos supor que você quer adicionar uma terceira marca. Execute o comando novamente com a nova marca. Ele é adicionado às marcas existentes.

```azurecli-interactive
az group update -n myResourceGroup --set tags.Project=Documentation
```

Os recursos não herdam as marcas do grupo de recursos. Atualmente, o grupo de recursos tem três marcas, mas os recursos não têm nenhuma marca. Para aplicar todas as marcas de um grupo de recursos a seus recursos e reter as marcas existentes nos recursos, use o script a seguir:

```azurecli-interactive
# Get the tags for the resource group
jsontag=$(az group show -n myResourceGroup --query tags)

# Reformat from JSON to space-delimited and equals sign
t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')

# Get the resource IDs for all resources in the resource group
r=$(az resource list -g myResourceGroup --query [].id --output tsv)

# Loop through each resource ID
for resid in $r
do
  # Get the tags for this resource
  jsonrtag=$(az resource show --id $resid --query tags)
  
  # Reformat from JSON to space-delimited and equals sign
  rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
  
  # Reapply the updated tags to this resource
  az resource tag --tags $t$rt --id $resid
done
```

Como alternativa, você pode aplicar as marcas do grupo de recursos aos recursos sem manter as marcas existentes:

```azurecli-interactive
# Get the tags for the resource group
jsontag=$(az group show -n myResourceGroup --query tags)

# Reformat from JSON to space-delimited and equals sign
t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')

# Get the resource IDs for all resources in the resource group
r=$(az resource list -g myResourceGroup --query [].id --output tsv)

# Loop through each resource ID
for resid in $r
do
  # Apply tags from resource group to this resource
  az resource tag --tags $t --id $resid
done
```

Para combinar vários valores em uma única marca, use uma cadeia de caracteres JSON.

```azurecli-interactive
az group update -n myResourceGroup --set tags.CostCenter='{"Dept":"IT","Environment":"Test"}'
```

Para remover todas as marcas em um grupo de recursos, use:

```azurecli-interactive
az group update -n myResourceGroup --remove tags
```
