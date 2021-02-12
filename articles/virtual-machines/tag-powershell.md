---
title: Como marcar uma VM usando o PowerShell
description: Saiba mais sobre como marcar uma máquina virtual usando o PowerShell
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: cynthn
ms.openlocfilehash: bf13d5c0caeb0bf31a383cd23155a6856c81c53b
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897384"
---
# <a name="how-to-tag-a-virtual-machine-in-azure-using-powershell"></a>Como marcar uma máquina virtual no Azure usando o PowerShell

Este artigo descreve como marcar uma VM no Azure usando o PowerShell. As marcas são pares de chave/valor definidos pelo usuário que podem ser colocados diretamente em um recurso ou grupo de recursos. Atualmente, o Azure dá suporte a até 50 marcas por recurso e grupo de recursos. As marcas podem ser colocadas em um recurso no momento da criação ou adicionadas a um recurso existente. Se você quiser marcar uma máquina virtual usando o CLI do Azure, consulte [como marcar uma máquina virtual no Azure usando o CLI do Azure](tag-cli.md).

Use o `Get-AzVM` cmdlet para exibir a lista atual de marcas para sua VM.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" | Format-List -Property Tags
```

Se sua máquina virtual já contiver marcas, você verá todas as marcas em formato de lista.

Para adicionar marcas, use o `Set-AzResource` comando. Ao atualizar marcas por meio do PowerShell, as marcas são atualizadas como um todo. Se você estiver adicionando uma marca a um recurso que já tem marcas, será necessário incluir todas as marcas que você deseja que sejam colocadas no recurso. Veja abaixo um exemplo de como adicionar mais marcas a um recurso usando cmdlets do PowerShell.

Atribua todas as marcas atuais da VM à `$tags` variável, usando a `Get-AzResource` `Tags` propriedade e.

```azurepowershell-interactive
$tags = (Get-AzResource -ResourceGroupName myResourceGroup -Name myVM).Tags
```

Para ver as marcas atuais, digite a variável.

```azurepowershell-interactive
$tags
```

A saída pode ser semelhante a esta:

```output
Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
```

No exemplo a seguir, adicionamos uma marca chamada `Location` com o valor `myLocation` . Use `+=` para acrescentar o novo par de chave/valor à `$tags` lista.

```azurepowershell-interactive
$tags += @{Location="myLocation"}
```

Use `Set-AzResource` para definir todas as marcas definidas na variável *$Tags* na VM.

```azurepowershell-interactive
Set-AzResource -ResourceGroupName myResourceGroup -Name myVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags
```

Use `Get-AzResource` para exibir todas as marcas no recurso.

```azurepowershell-interactive
(Get-AzResource -ResourceGroupName myResourceGroup -Name myVM).Tags

```

A saída deve ser semelhante à seguinte, que agora inclui a nova marca:

```output

Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
Location      MyLocation
```

### <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como marcar seus recursos do Azure, consulte [Visão geral do Azure Resource Manager](../azure-resource-manager/management/overview.md) e [Usando marcas para organizar os Recursos do Azure](../azure-resource-manager/management/tag-resources.md).
- Para ver como as marcas podem ajudá-lo a gerenciar seu uso de recursos do Azure, consulte [noções básicas sobre sua fatura do Azure](../cost-management-billing/understand/review-individual-bill.md).
