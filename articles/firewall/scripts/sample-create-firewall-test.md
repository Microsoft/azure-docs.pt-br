---
title: Criar um ambiente de teste do Firewall do Azure
description: Este exemplo de script cria um firewall e um ambiente de rede de teste. A rede tem uma VNet com três sub-redes.
services: virtual-network
author: vhorne
ms.service: firewall
ms.devlang: powershell
ms.topic: sample
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 4158bc07373a2d0aa6fb6ceaf2dce62b50bb6bd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94658356"
---
# <a name="create-an-azure-firewall-test-environment"></a>Criar um ambiente de teste do Firewall do Azure

Este exemplo de script cria um firewall e um ambiente de rede de teste. A rede tem uma VNet, com três sub-redes: uma *AzureFirewallSubnet*, uma *ServersSubnet* e uma *JumpboxSubnet*. O ServersSubnet e o JumpboxSubnet têm, cada um, um Windows Server de dois núcleos neles.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

O firewall está na AzureFirewallSubnet e é configurado com uma Coleção de regra de aplicativo com uma única regra que permite o acesso ao `www.microsoft.com`.

Uma rota definida pelo usuário é criada que aponta o tráfego de rede do ServersSubnet até o firewall, em que as regras de firewall são aplicadas.

Você pode executar o script do Azure [Cloud Shell](https://shell.azure.com/powershell) ou de uma instalação local do PowerShell. 

Se você executar o PowerShell localmente, esse script exigirá o Azure PowerShell. Para localizar a versão instalada, execute `Get-Module -ListAvailable Az`. 

Você pode usar `PowerShellGet` se precisar fazer upgrade, que é incorporado no Windows 10 e no Windows Server 2016.

> [!NOTE]
>Outra versão do Windows exigem a instalação do `PowerShellGet` antes que você possa usá-lo. Você pode executar `Get-Module -Name PowerShellGet -ListAvailable | Select-Object -Property Name,Version,Path` para determinar se ele está instalado em seu sistema. Se a saída estiver em branco, você precisará instalar a versão mais recente do [Windows Management framework](https://www.microsoft.com/download/details.aspx?id=54616).

Para obter mais informações, consulte [Instalar o Azure PowerShell](/powershell/azure/install-Az-ps)

Qualquer instalação existente do Azure PowerShell feita com o Web Platform installer entrará em conflito com a instalação do PowerShellGet e precisa ser removido.

Lembre-se de que, se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script


[!code-azurepowershell-interactive[main](../../../powershell_scripts/firewall/create-fw-test.ps1  "Create a firewall test environment")]

## <a name="clean-up-deployment"></a>Limpar a implantação 

Execute o comando a seguir para remover o grupo de recursos, a VM e todos os recursos relacionados:

```powershell
Remove-AzResourceGroup -Name AzfwSampleScriptEastUS -Force
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir para criar um grupo de recursos, uma rede virtual e grupos de segurança de rede. Cada comando na tabela a seguir redireciona para a documentação específica do comando:

| Comando | Observações |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Cria um objeto de configuração de sub-rede |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Cria uma rede virtual do Azure e uma sub-rede front-end. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | Cria regras de segurança a serem atribuídas a um grupo de segurança de rede. |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |Cria regras NSG que permitem ou bloqueiam portas específicas para sub-redes específicas. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Associa os NSGs às sub-redes. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Cria um endereço IP público para acessar a VM da Internet. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Cria as interfaces de rede virtual e as anexa às sub-redes de front-end e back-end da rede virtual. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Cria uma configuração de VM. Essa configuração inclui informações como nome da VM, sistema operacional e credenciais administrativas. A configuração é usada durante a criação da VM. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Crie uma máquina virtual. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos e todos os recursos contidos nele. |
|[New-AzFirewall](/powershell/module/az.network/new-azfirewall)| Cria um novo Firewall do Azure.|
|[Get-AzFirewall](/powershell/module/az.network/get-azfirewall)|Obtém um objeto de Firewall do Azure.|
|[New-AzFirewallApplicationRule](/powershell/module/az.network/new-azfirewallapplicationrule)|Cria uma nova regra de aplicativo do Firewall do Azure.|
|[Set-AzFirewall](/powershell/module/az.network/set-azfirewall)|Confirma as alterações para o objeto do Firewall do Azure.|

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira a [Documentação do Azure PowerShell](/powershell/azure/).