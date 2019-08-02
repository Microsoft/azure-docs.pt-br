---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: deabef0c2c3540e515fe72a161710c95a20fa86f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171785"
---
Abra o console do PowerShell com privilégios elevados.



Se você estiver executando o Microsoft Azure PowerShell localmente, conecte-se à sua conta do Azure. O cmdlet *Connect-AzAccount* solicita as credenciais. Depois de entrar, ele baixa as configurações da conta para que estejam disponíveis para o Microsoft Azure PowerShell. Se você não estiver executando o PowerShell localmente e está usando o navegador o Azure Cloud Shell 'Try it', você pode pular para a etapa 2 desta seção. Você se conecta à sua conta do Azure automaticamente.

```azurepowershell
Connect-AzAccount
```

Se você tiver mais de uma assinatura, obtenha uma lista das assinaturas do Azure.

```azurepowershell-interactive
Get-AzSubscription
```

Especifique a assinatura que você deseja usar.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Name of subscription"
```