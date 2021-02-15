---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 24d146da7946176c92902698d0f52ae01baf79ee
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061579"
---
Se você estiver executando o PowerShell localmente, abra o console do PowerShell com privilégios elevados e conecte-se à sua conta do Azure. O cmdlet *Connect-AzAccount* solicita as credenciais. Depois de entrar, ele baixa as configurações da conta para que estejam disponíveis para o Microsoft Azure PowerShell.

Se estiver usando Azure Cloud Shell em vez de executar o PowerShell localmente, você observará que não precisa executar o *Connect-AzAccount*. Azure Cloud Shell se conecta automaticamente à sua conta do Azure depois de selecionar **experimentar**.

1. Se você estiver executando o PowerShell localmente, entre.

   ```azurepowershell
   Connect-AzAccount
   ```

1. Se você tiver mais de uma assinatura, obtenha uma lista das assinaturas do Azure.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

1. Especifique a assinatura que você deseja usar.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```
