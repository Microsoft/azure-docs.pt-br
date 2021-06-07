---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: 27da28073b01c6bc43868172d6c989d9518d8f53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95558982"
---
## <a name="start-your-powershell-session"></a>Iniciar sua sessão do PowerShell
 

Execute o cmdlet [**Connect-Az Account**](/powershell/module/Az.Accounts/Connect-AzAccount) e você verá uma tela de entrada para inserir suas credenciais. Use as mesmas credenciais que você usa para entrar no Portal do Azure.

```powershell
Connect-AzAccount
```

Se você tiver várias assinaturas, use o cmdlet [**Set-AzContext**](/powershell/module/az.accounts/set-azcontext) para selecionar qual assinatura sua sessão do PowerShell deve usar. Para ver qual assinatura a sessão atual do PowerShell está usando, execute [**Get-AzContext**](/powershell/module/az.accounts/get-azcontext). Para ver todas as suas assinaturas, execute [**Get-AzSubscription**](/powershell/module/az.accounts/get-azsubscription).

```powershell
Set-AzContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
```