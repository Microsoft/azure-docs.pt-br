---
title: Exemplo de Script do Azure PowerShell - Gerenciar o tráfego de rede | Microsoft Docs
description: Exemplo de Script do Azure PowerShell - Gerenciar o tráfego da web com um gateway de aplicativo e um conjunto de escala de máquinas virtuais.
services: application-gateway
documentationcenter: networking
author: vhorne
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: a4f1961f9c7d79a95a0e8f4cc3fc18bdeac2f36f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89078700"
---
# <a name="manage-web-traffic-with-azure-powershell"></a>Gerenciar o tráfego da Web com o Azure PowerShell

Este script cria um gateway de aplicativo que usa um conjunto de dimensionamento de máquinas virtuais para servidores de back-end. O gateway de aplicativo pode ser configurado para gerenciar o tráfego da web. Depois de executar o script, você pode testar o gateway de aplicativo usando seu endereço IP público.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script

[!code-powershell[main](../../../powershell_scripts/application-gateway/create-vmss/create-vmss.ps1 "Create application gateway")]

## <a name="clean-up-deployment"></a>Limpar a implantação 

Execute o comando a seguir para remover o grupo de recurso, o gateway de aplicativo, e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para criar a implantação. Cada item em que a tabela contém links para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Cria as configurações de sub-rede. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Cria a rede virtual usando com as configurações de sub-rede. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Cria o endereço IP público para o gateway do aplicativo. |
| [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) | Cria a configuração que associa uma sub-rede com o application gateway. |
| [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) | Cria a configuração que associa um endereço IP público com o gateway de aplicativo. |
| [New-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport) | Atribui uma porta a ser usada para acessar o gateway de aplicativo. |
| [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) | Cria um pool de back-end para o gateway de aplicativo. |
| [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting) | Define as configurações para um pool de back-end. |
| [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) | Cria um ouvinte. |
| [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) | Cria uma regra de roteamento. |
| [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) | Especifique a camada e a capacidade de um gateway de aplicativo. |
| [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) | Criar um gateway de aplicativo. |
| [Set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) | Crie um perfil de armazenamento para o conjunto de escala. |
| [Set-AzVmssOsProfile](/powershell/module/az.compute/set-azvmssosprofile) | Defina o sistema operacional para o conjunto de escala. |
| [Add-AzVmssNetworkInterfaceConfiguration](/powershell/module/az.compute/add-azvmssnetworkinterfaceconfiguration) | Defina o adaptador de rede para o conjunto de escala. |
| [New-AzVmss](/powershell/module/az.compute/new-azvm) | Crie um conjunto de dimensionamento de máquina virtual. |
| [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) | Pega o endereço de IP público do gateway do aplicativo. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos e todos os recursos contidos nele. | 

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/).

Exemplos de script PowerShell de máquinas virtuais adicionais podem ser encontrados na [documentação de Gateway de Aplicativo do Azure](../powershell-samples.md).
