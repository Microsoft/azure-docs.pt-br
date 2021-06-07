---
title: 'PowerShell: Criar uma instância gerenciada'
titleSuffix: Azure SQL Managed Instance
description: Este artigo oferece um script de exemplo do Azure PowerShell para criar uma instância gerenciada.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: fd091cedb67c07b7de2c7e8540e99c3e8daf7dcf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91323629"
---
# <a name="use-powershell-to-create-a-managed-instance"></a>Usar o PowerShell para criar uma instância gerenciada

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Este exemplo de script do PowerShell cria uma instância gerenciada em uma sub-rede dedicada dentro de uma nova rede virtual. Ele também configura uma tabela de rotas e um grupo de segurança de rede para a rede virtual. Depois que o script tiver sido executado com êxito, a instância gerenciada poderá ser acessada de dentro da rede virtual ou de um ambiente local. Confira [Configurar a VM do Azure para se conectar à Instância Gerenciada do Banco de Dados SQL do Azure](../connect-vm-instance-configure.md) e [Configurar uma conexão ponto a site com uma Instância Gerenciada de SQL do Azure do local](../point-to-site-p2s-configure.md).

> [!IMPORTANT]
> Para limitações, consulte [regiões com suporte](../resource-limits.md#supported-regions) e [tipos de assinatura compatíveis](../resource-limits.md#supported-subscription-types).

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Quando você opta por instalar e usar o PowerShell localmente, este tutorial exige o Azure PowerShell 1.4.0 ou posterior. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

## <a name="sample-script"></a>Exemplo de script

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/managed-instance/create-and-configure-managed-instance.ps1 "Create managed instance")]

## <a name="clean-up-deployment"></a>Limpar a implantação

Use o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa alguns dos comandos a seguir. Para obter mais informações sobre os comandos usados e outros comandos na tabela a seguir, clique nos links para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados.
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Cria uma rede virtual. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-AzVirtualNetworkSubnetConfig) | Adiciona uma configuração de sub-rede a uma rede virtual. |
| [Get-AzVirtualNetwork](/powershell/module/az.network/Get-AzVirtualNetwork) | Obtém uma rede virtual em um grupo de recursos. |
| [Set-AzVirtualNetwork](/powershell/module/az.network/Set-AzVirtualNetwork) | Define a meta do estado de uma rede virtual. |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Get-AzVirtualNetworkSubnetConfig) | Obtém uma sub-rede em uma rede virtual. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-AzVirtualNetworkSubnetConfig) | Configura a meta do estado da configuração de uma sub-rede em uma rede virtual. |
| [New-AzRouteTable](/powershell/module/az.network/New-AzRouteTable) | Cria uma tabela de rotas. |
| [Get-AzRouteTable](/powershell/module/az.network/Get-AzRouteTable) | Obtém tabelas de rotas. |
| [Set-AzRouteTable](/powershell/module/az.network/Set-AzRouteTable) | Define a meta de estado para uma tabela de rotas. |
| [New-AzSqlInstance](/powershell/module/az.sql/New-AzSqlInstance) | Cria uma instância gerenciada. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira a [Documentação do Azure PowerShell](/powershell/azure/).

Exemplos adicionais de scripts do PowerShell para a Instância Gerenciada de SQL do Azure podem ser encontrados nos [Scripts de PowerShell da Instância Gerenciada de SQL do Azure](../../database/powershell-script-content-guide.md).
