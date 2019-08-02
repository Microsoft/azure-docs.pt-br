---
title: Exemplo do PowerShell – criar uma instância gerenciada no Banco de Dados SQL do Azure | Microsoft Docs
description: Script de exemplo do Azure PowerShell para criar uma instância gerenciada no Banco de Dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: 32b3c3b45c627d8dfdb42642228a7f9d9786111e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569970"
---
# <a name="use-powershell-to-create-an-azure-sql-database-managed-instance"></a>Use o PowerShell para criar uma instância gerenciada no Banco de Dados SQL do Azure

Este exemplo de script do PowerShell cria uma instância gerenciada do Banco de Dados SQL do Azure em uma sub-rede dedicada dentro de uma nova rede virtual. Ele também configura uma tabela de rotas e um grupo de segurança de rede para a rede virtual. Depois que o script tiver sido executado com êxito, a instância gerenciada poderá ser acessada de dentro da rede virtual ou de um ambiente local. Veja [Configurar a VM do Azure para se conectar a uma Instância Gerenciada do Banco de Dados SQL do Azure](../sql-database-managed-instance-configure-vm.md) e [Configurar uma conexão ponto a site para uma Instância Gerenciada do Banco de Dados SQL do Azure de local](../sql-database-managed-instance-configure-p2s.md).

> [!IMPORTANT]
> Para limitações, consulte [regiões com suporte](../sql-database-managed-instance-resource-limits.md#supported-regions) e [tipos de assinatura compatíveis](../sql-database-managed-instance-resource-limits.md#supported-subscription-types).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o AZ PowerShell 1.4.0 ou posterior. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

## <a name="sample-script"></a>Script de exemplo

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/managed-instance/create-and-configure-managed-instance.ps1 "Create managed instance")]

## <a name="clean-up-deployment"></a>Limpar a implantação

Use o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados.
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Cria uma rede virtual |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-AzVirtualNetworkSubnetConfig) | Cria uma configuração de sub-rede da rede virtual |
| [Get-AzVirtualNetwork](/powershell/module/az.network/Get-AzVirtualNetwork) | Obtém uma rede virtual em um grupo de recursos |
| [Set-AzVirtualNetwork](/powershell/module/az.network/Set-AzVirtualNetwork) | Define o estado esperado para uma rede virtual |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Get-AzVirtualNetworkSubnetConfig) | Obtém uma sub-rede em uma rede virtual |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-AzVirtualNetworkSubnetConfig) | Configura o estado esperado para uma configuração de sub-rede em uma rede virtual |
| [New-AzRouteTable](/powershell/module/az.network/New-AzRouteTable) | Cria uma tabela de rotas |
| [Get-AzRouteTable](/powershell/module/az.network/Get-AzRouteTable) | Exibe tabelas de rotas |
| [Set-AzRouteTable](/powershell/module/az.network/Set-AzRouteTable) | Define o estado esperado para uma tabela de rotas |
| [New-AzSqlInstance](/powershell/module/az.sql/New-AzSqlInstance) | Cria uma Instância Gerenciada do Banco de Dados SQL do Azure |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/overview).

Outros exemplos de script do PowerShell para Banco de Dados SQL podem ser encontrados nos [scripts do PowerShell para Banco de Dados SQL do Azure](../sql-database-powershell-samples.md).
