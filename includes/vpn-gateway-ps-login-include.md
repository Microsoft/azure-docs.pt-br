---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 853bd32cf3ea97571929d54fb7d3ba04bde16b81
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60405827"
---
Antes de iniciar essa configuração, você deve fazer conectar à sua conta do Azure. O cmdlet solicita as credenciais de conexão para sua conta do Microsoft Azure. Depois de entrar, ele baixa as configurações da conta para que elas estejam disponíveis para o Azure PowerShell. Para obter mais informações, consulte [Usando o Windows PowerShell com o Gerenciador de Recursos](../articles/powershell-azure-resource-manager.md).

Para entrar, abra o console do PowerShell com privilégios elevados e conecte-se à sua conta. Use o exemplo a seguir para ajudar a se conectar:

```powershell
Connect-AzAccount
```

Se você tiver várias assinaturas do Azure, verifique as assinaturas para a conta.

```powershell
Get-AzSubscription
```

Especifique a assinatura que você deseja usar.

```powershell
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
 ```
