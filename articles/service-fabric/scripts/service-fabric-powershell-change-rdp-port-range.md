---
title: Exemplo de Script do PowerShell do Azure - Alterar o intervalo de porta do RDP | Microsoft Docs
description: Exemplo de Script do Microsoft Azure PowerShell - Altera o intervalo de porta do RDP de um cluster implantado.
services: service-fabric
tags: azure-service-management
author: athinanthny
ms.author: atsenthi
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 03/19/2018
ms.openlocfilehash: 750a2100d34e02cac7c613cc6b95160fc348b535
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576359"
---
# <a name="update-the-rdp-port-range-values"></a>Atualizar os valores de intervalo da porta RDP

Esse exemplo de script altera os valores de intervalo de porta do RDP nas VMs do nó de cluster após a implantação do cluster.  O Azure PowerShell é usado para que as VMs subjacentes não se movimentem ciclicamente.  O script obtém o recurso `Microsoft.Network/loadBalancers` no grupo de recursos do cluster e atualiza os valores `inboundNatPools.frontendPortRangeStart` e `inboundNatPools.frontendPortRangeEnd`. Personalize os parâmetros conforme necessário.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Se necessário, instale o Azure PowerShell usando a instrução encontrada no [guia do Azure PowerShell](/powershell/azure/).

## <a name="sample-script"></a>Exemplo de script

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-port-range/change-rdp-port-range.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Obtém o recurso `Microsoft.Network/loadBalancers`. |
|[Set-AzResource](/powershell/module/az.resources/set-azresource)|Atualiza o recurso `Microsoft.Network/loadBalancers`.|

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/).

Mais exemplos do Azure PowerShell para o Azure Service Fabric podem ser encontrados nos [exemplos do Azure PowerShell](../service-fabric-powershell-samples.md).
