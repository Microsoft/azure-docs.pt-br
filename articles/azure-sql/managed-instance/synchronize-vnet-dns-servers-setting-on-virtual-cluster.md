---
title: Sincronizar a configuração de servidores DNS da rede virtual no cluster virtual do SQL Instância Gerenciada
description: Saiba como sincronizar a configuração de servidores DNS de rede virtual no cluster virtual do SQL Instância Gerenciada.
services: sql-database
ms.service: sql-managed-instance
author: srdan-bozovic-msft
ms.author: srbozovi
ms.topic: how-to
ms.date: 01/17/2021
ms.openlocfilehash: 0da38475c0e3c766cabbf765ea89dc5714a5b830
ms.sourcegitcommit: 3c8964a946e3b2343eaf8aba54dee41b89acc123
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98747559"
---
# <a name="synchronize-virtual-network-dns-servers-setting-on-sql-managed-instance-virtual-cluster"></a>Sincronizar a configuração de servidores DNS da rede virtual no cluster virtual do SQL Instância Gerenciada
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este artigo explica quando e como sincronizar a configuração de servidores DNS de rede virtual no cluster virtual do SQL Instância Gerenciada.

## <a name="when-to-synchronize-the-dns-setting"></a>Quando sincronizar a configuração de DNS

Há alguns cenários (por exemplo, db mail, servidores vinculados a outras instâncias do SQL Server em seu ambiente de nuvem ou híbrido) que exigem nomes de hosts privados para serem resolvidos na Instância Gerenciada de SQL. Nesse caso, você precisa configurar um DNS personalizado dentro do Azure. Consulte [configurar um DNS personalizado para o Azure SQL instância gerenciada](custom-dns-configure.md) para obter detalhes.

Se essa alteração for implementada após a criação de Instância Gerenciada de Hospedagem de [cluster virtual](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture) , você precisará sincronizar a configuração de servidores DNS no cluster virtual com a configuração de rede virtual.

> [!IMPORTANT]
> A configuração de servidores DNS de sincronização afetará todas as instâncias gerenciadas hospedadas no cluster virtual.

## <a name="how-to-synchronize-the-dns-setting"></a>Como sincronizar a configuração de DNS

### <a name="azure-rbac-permissions-required"></a>Permissões do RBAC do Azure necessárias

A configuração do servidor DNS de sincronização do usuário precisará ter uma das seguintes funções do Azure:

- Função de proprietário da assinatura ou
- Instância Gerenciada função colaborador ou
- Função personalizada com a seguinte permissão:
  - `Microsoft.Sql/virtualClusters/updateManagedInstanceDnsServers/action`

### <a name="use-azure-powershell"></a>Usar PowerShell do Azure

Obter a rede virtual onde a configuração de servidores DNS foi atualizada.

```PowerShell
$ResourceGroup = 'enter resource group of virtual network'
$VirtualNetworkName = 'enter virtual network name'
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroup $ResourceGroup -Name $VirtualNetworkName
```
Use o comando [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) do PowerShell para sincronizar a configuração de servidores DNS para todos os clusters virtuais na sub-rede.

```PowerShell
Get-AzSqlVirtualCluster `
    | where SubnetId -match $virtualNetwork.Id `
    | select Id `
    | Invoke-AzResourceAction -Action updateManagedInstanceDnsServers -Force
```
### <a name="use-the-azure-cli"></a>Usar a CLI do Azure

Obter a rede virtual onde a configuração de servidores DNS foi atualizada.

```Azure CLI
resourceGroup="auto-failover-group"
virtualNetworkName="vnet-fog-eastus"
virtualNetwork=$(az network vnet show -g $resourceGroup -n $virtualNetworkName --query "id" -otsv)
```

Use CLI do Azure comando [AZ Resource Invoke-Action](/cli/azure/resource?view=azure-cli-latest#az_resource_invoke_action) para sincronizar a configuração de servidores DNS para todos os clusters virtuais na sub-rede.

```Azure CLI
az sql virtual-cluster list --query "[? contains(subnetId,'$virtualNetwork')].id" -o tsv \
    | az resource invoke-action --action updateManagedInstanceDnsServers --ids @-
```
## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como configurar um DNS personalizado [configurar um DNS personalizado para o Azure SQL instância gerenciada](custom-dns-configure.md).
- Para obter uma visão geral, consulte [o que é o Azure SQL instância gerenciada?](sql-managed-instance-paas-overview.md).
