---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 872164dfee9f35881fca97b0339bd95c64a2f5ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99070169"
---
Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Use o cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) do Azure PowerShell para criar um grupo de recursos chamado *myResourceGroup* na localização *eastus*. 

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```
