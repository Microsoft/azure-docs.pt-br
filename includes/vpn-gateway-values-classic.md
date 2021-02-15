---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/08/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 11d2172d085fe9b47587f4084908f99d7b54437e
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92103230"
---
Quando você cria VNets clássicas no portal do Azure, o nome que você vê não é o nome completo usado para o PowerShell. Por exemplo, uma VNet que parece ter o nome **TestVNet1** no portal, pode ter um nome muito mais longo no arquivo de configuração de rede. Para uma VNet no grupo de recursos, o nome "ClassicRG" pode ser semelhante a: **Group ClassicRG TestVNet1**. Ao criar suas conexões, é importante usar os valores que você vê no arquivo de configuração de rede.

Nas etapas a seguir, você vai se conectar à sua conta do Azure, bem como baixar e exibir o arquivo de configuração de rede para obter os valores que são necessários para as conexões.

1. Baixe e instale a versão mais recente dos cmdlets do PowerShell do SM (Gerenciamento de Serviços) do Azure. A maioria das pessoas tem os módulos do Resource Manager instalados localmente, mas não têm módulos de gerenciamento de serviços. Os módulos de gerenciamento de serviços são herdados e devem ser instalados separadamente. Para obter mais informações, consulte [instalar cmdlets de gerenciamento de serviços](/powershell/azure/servicemanagement/install-azure-ps).

1. Abra o console do PowerShell com direitos elevados e conecte-se à sua conta. Use os exemplos a seguir para ajudá-lo a se conectar. Você deve executar esses comandos localmente usando o módulo de gerenciamento de serviços do PowerShell. Conecte-se à sua conta. Use o exemplo a seguir para ajudar a se conectar:

   ```powershell
   Add-AzureAccount
   ```
1. Verificar as assinaturas da conta.

   ```powershell
   Get-AzureSubscription
   ```
1. Se você tiver mais de uma assinatura, selecione a assinatura que deseja usar.

   ```powershell
   Select-AzureSubscription -SubscriptionId "Replace_with_your_subscription_ID"
   ```
1. Crie um diretório em seu computador. Por exemplo, C:\AzureVNet
1. Exporte o arquivo de configuração de rede para o diretório. Neste exemplo, o arquivo de configuração de rede é exportado para **C:\AzureNet**.

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
1. Abra o arquivo com um editor de texto e exiba os nomes dos sites e VNets. Esses nomes serão os nomes que você usa ao criar suas conexões.<br>Os nomes de **VNet** são listados como **VirtualNetworkSite Name =**<br>Os nomes de **site** são listados como **LocalNetworkSiteRef Name =**