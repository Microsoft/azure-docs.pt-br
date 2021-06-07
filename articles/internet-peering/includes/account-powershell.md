---
title: incluir arquivo
titleSuffix: Azure
description: incluir arquivo
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: beffb2babefd86c2807e21e9337cba66f42fcfc2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "81678479"
---
Antes de começar a configuração, instale e importe os módulos necessários. Você precisa de privilégios de administrador para instalar os módulos no PowerShell.

1. Instale e importe o módulo AZ.
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. Instale e importe o módulo AZ. emparelhamento.
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. Verifique se os módulos foram importados corretamente usando este comando:
    ```powershell
    Get-Module
    ```
1. Conecte à sua conta do Azure usando este comando:
    ```powershell
    Connect-AzAccount
    ```
1. Verifique as assinaturas da conta e selecione a assinatura na qual você deseja criar um emparelhamento.
    ```powershell
    Get-AzSubscription
    Select-AzSubscription -SubscriptionId "subscription-id"
    ```
1. Se você ainda não tiver um grupo de recursos, deverá criar um antes de criar um emparelhamento. Faça isso ao executar o seguinte comando:

    ```powershell
    New-AzResourceGroup -Name "PeeringResourceGroup" -Location "Central US"
    ```
> [!IMPORTANT]
> Se você ainda não tiver associado seu ASN e sua assinatura, siga as etapas em [associar parceiro ASN](../howto-subscription-association-powershell.md). Essa ação é necessária para solicitar um emparelhamento.

> [!NOTE]
> O local de um grupo de recursos é independente do local em que você opta por configurar um emparelhamento.
&nbsp;
