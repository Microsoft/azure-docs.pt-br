---
title: Proteção de instância para instâncias de conjuntos de dimensionamento de máquinas virtuais do Azure
description: Saiba como proteger as instâncias do Conjunto de Dimensionamento de Máquinas Virtuais do Azure de operações de escalonamento horizontal e redução horizontal.
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: instance-protection
ms.date: 02/26/2020
ms.reviewer: jushiman
ms.custom: avverma
ms.openlocfilehash: 292abce3361c000eeeef2c399d5ffa2d2c4852e1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762849"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances"></a>Proteção de instância para instâncias de conjuntos de dimensionamento de máquinas virtuais do Azure

Os Conjuntos de Dimensionamento de Máquinas Virtuais do Azure permitem uma melhor elasticidade para suas cargas de trabalho por meio de [dimensionamento automático](virtual-machine-scale-sets-autoscale-overview.md), para que você possa configurar quando sua infraestrutura é expandida e quando ela é reduzida horizontalmente. Os conjuntos de dimensionamento também permitem que você gerencie, configure e atualize centralmente um grande número de VMs por meio de diferentes configurações de [política de atualização](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model). Você pode configurar uma atualização no modelo do conjunto de dimensionamento e, se você tiver definido a política de atualização como automática ou sem interrupção, a nova configuração será aplicada automaticamente a cada instância do conjunto de dimensionamento.

À medida que seu aplicativo processa o tráfego, pode haver situações em que você deseja que instâncias específicas sejam tratadas de maneira diferente do restante da instância do conjunto de dimensionamento. Por exemplo, determinadas instâncias no conjunto de dimensionamento podem estar executando operações de longa execução e você pode não querer que essas instâncias sejam reduzidas horizontalmente até que as operações sejam concluídas. Você também pode ter especializado algumas instâncias no conjunto de dimensionamento para executar tarefas adicionais ou diferentes dos outros membros do conjunto de dimensionamento. Você precisa que essas VMs ' especiais ' não sejam modificadas com as outras instâncias no conjunto de dimensionamento. A proteção de instância fornece os controles adicionais para habilitar esses e outros cenários para seu aplicativo.

Este artigo descreve como você pode aplicar e usar as diferentes funcionalidades de proteção de instância com instâncias de conjunto de dimensionamento.

## <a name="types-of-instance-protection"></a>Tipos de proteção de instância
Os conjuntos de dimensionamento fornecem dois tipos de funcionalidades de proteção de instância:

-   **Proteger contra redução horizontal**
    - Habilitado por meio da propriedade **protectFromScaleIn** na instância do conjunto de dimensionamento
    - Protege a instância de redução horizontal iniciada por dimensionamento automático
    - As operações de instância iniciadas pelo usuário (incluindo exclusão de instância) **não são bloqueadas**
    - As operações iniciadas no conjunto de dimensionamento (atualizar, refazer imagem, desalocar etc.) **não são bloqueadas**

-   **Proteger contra as ações do conjunto de dimensionamento**
    - Habilitado por meio da propriedade **protectFromScaleSetActions** na instância do conjunto de dimensionamento
    - Protege a instância de redução horizontal iniciada por dimensionamento automático
    - Protege a instância de operações iniciadas no conjunto de dimensionamento (como atualizar, refazer imagem, desalocar etc.)
    - As operações de instância iniciadas pelo usuário (incluindo exclusão de instância) **não são bloqueadas**
    - A exclusão do conjunto de dimensionamento inteiro **não é bloqueada**

## <a name="protect-from-scale-in"></a>Proteger contra redução horizontal
A proteção da instância pode ser aplicada às instâncias do conjunto de dimensionamento depois que as instâncias são criadas. A proteção é aplicada e modificada somente no [modelo de instância](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) e não no [modelo de conjunto de dimensionamento](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Há várias maneiras de aplicar a proteção contra redução horizontal em suas instâncias do conjunto de dimensionamento, conforme detalhado nos exemplos abaixo.

### <a name="azure-portal"></a>Portal do Azure

Você pode aplicar a proteção contra redução horizontal por meio do portal do Azure a uma instância no conjunto de dimensionamento. Você não pode ajustar mais de uma instância por vez. Repita as etapas para cada instância que deseja proteger.
 
1. Acesse um conjunto de dimensionamento de máquinas virtuais existente.
1. Selecione **Instâncias** no menu à esquerda, em **Configurações**.
1. Selecione o nome da instância que você deseja proteger.
1. Selecione a guia **Política de Proteção**.
1. Na folha **Política de Proteção**, selecione a opção **Proteger contra redução horizontal**.
1. Clique em **Salvar**. 

### <a name="rest-api"></a>API REST

O exemplo a seguir aplica a proteção contra redução horizontal a uma instância no conjunto de dimensionamento.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true
    }
  }        
}

```

> [!NOTE]
>A proteção de instância só é compatível com APIs de versão 2019-03-01 e posterior

### <a name="azure-powershell"></a>Azure PowerShell

Use o cmdlet [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) para aplicar a proteção contra redução horizontal à sua instância do conjunto de dimensionamento.

O exemplo a seguir aplica a proteção contra redução horizontal a uma instância no conjunto de dimensionamento que tem a ID de instância igual a 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>CLI do Azure 2.0

Use [az vmss update](/cli/azure/vmss#az_vmss_update) para aplicar a proteção contra redução horizontal à sua instância do conjunto de dimensionamento.

O exemplo a seguir aplica a proteção contra redução horizontal a uma instância no conjunto de dimensionamento que tem a ID de instância igual a 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>Proteger contra as ações do conjunto de dimensionamento
A proteção da instância pode ser aplicada às instâncias do conjunto de dimensionamento depois que as instâncias são criadas. A proteção é aplicada e modificada somente no [modelo de instância](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) e não no [modelo de conjunto de dimensionamento](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Quando uma instância é protegida de ações do conjunto de dimensionamento, ela consequentemente também é protegida de redução horizontal iniciada por dimensionamento automático.

Há várias maneiras de aplicar a proteção contra ações do conjunto de dimensionamento em suas instâncias do conjunto de dimensionamento, conforme detalhado nos exemplos abaixo.

### <a name="azure-portal"></a>Portal do Azure

Você pode aplicar a proteção contra ações do conjunto de dimensionamento por meio do portal do Azure a uma instância no conjunto de dimensionamento. Você não pode ajustar mais de uma instância por vez. Repita as etapas para cada instância que deseja proteger.
 
1. Acesse um conjunto de dimensionamento de máquinas virtuais existente.
1. Selecione **Instâncias** no menu à esquerda, em **Configurações**.
1. Selecione o nome da instância que você deseja proteger.
1. Selecione a guia **Política de Proteção**.
1. Na folha **Política de Proteção**, selecione a opção **Proteger contra as ações do conjunto de dimensionamento**.
1. Clique em **Salvar**. 

### <a name="rest-api"></a>API REST

O exemplo a seguir aplica a proteção contra ações do conjunto de dimensionamento a uma instância no conjunto de dimensionamento.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vMScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true,
      "protectFromScaleSetActions": true
    }
  }        
}

```

> [!NOTE]
>A proteção de instância só é compatível com APIs de versão 2019-03-01 e posterior.</br>
Quando uma instância é protegida de ações do conjunto de dimensionamento, ela consequentemente também é protegida de redução horizontal iniciada por dimensionamento automático. Você não pode especificar "protectFromScaleIn": false ao definir "protectFromScaleSetActions": true

### <a name="azure-powershell"></a>Azure PowerShell

Use o cmdlet [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) para aplicar a proteção contra ações do conjunto de dimensionamento à sua instância do conjunto de dimensionamento.

O exemplo a seguir aplica a proteção contra ações do conjunto de dimensionamento a uma instância no conjunto de dimensionamento que tenha a ID de instância igual a 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>CLI do Azure 2.0

Use [az vmss update](/cli/azure/vmss#az_vmss_update) para aplicar a proteção contra ações do conjunto de dimensionamento à sua instância do conjunto de dimensionamento.

O exemplo a seguir aplica a proteção contra ações do conjunto de dimensionamento a uma instância no conjunto de dimensionamento que tenha a ID de instância igual a 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>Solucionar problemas
### <a name="no-protectionpolicy-on-scale-set-model"></a>Nenhuma protectionPolicy no modelo de conjunto de dimensionamento
A proteção de instância só é aplicável em instâncias de conjunto de dimensionamento e não no modelo de conjunto de dimensionamento.

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>Nenhuma protectionPolicy no modelo de instância do conjunto de dimensionamento
Por padrão, a política de proteção não é aplicada a uma instância quando ela é criada.

A proteção da instância pode ser aplicada às instâncias do conjunto de dimensionamento depois que as instâncias são criadas.

### <a name="not-able-to-apply-instance-protection"></a>Não é possível aplicar a proteção de instância
A proteção de instância só é compatível com APIs de versão 2019-03-01 e posterior. Verifique a versão da API que está sendo usada e atualize conforme necessário. Talvez você também precise atualizar o PowerShell ou a CLI para a versão mais recente.

## <a name="next-steps"></a>Próximas etapas
Aprenda como [Implantar o aplicativo](virtual-machine-scale-sets-deploy-app.md) em conjuntos de dimensionamento de máquinas virtuais
