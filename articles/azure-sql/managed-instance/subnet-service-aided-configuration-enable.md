---
title: Habilitando a configuração de sub-rede auxiliada por serviço para o Azure SQL Instância Gerenciada
description: Habilitando a configuração de sub-rede auxiliada por serviço para o Azure SQL Instância Gerenciada
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.date: 03/12/2020
ms.openlocfilehash: 67b398194d9094cd99fccaa85ed0df3be362ce2b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91618005"
---
# <a name="enabling-service-aided-subnet-configuration-for-azure-sql-managed-instance"></a>Habilitando a configuração de sub-rede auxiliada por serviço para o Azure SQL Instância Gerenciada
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

A configuração de sub-rede auxiliada por serviço fornece gerenciamento automatizado de configuração de rede para sub-redes que hospedam instâncias gerenciadas. Com a configuração de sub-rede auxiliada pelo serviço, o usuário permanece no controle total do acesso aos dados (fluxos de tráfego TDS) enquanto a instância gerenciada tem a responsabilidade de garantir o fluxo ininterrupto do tráfego de gerenciamento para atender ao SLA.

Grupos de segurança de rede configurados automaticamente e regras de tabela de rotas são visíveis para o cliente e anotados com _o prefixo Microsoft. SQL-managedInstances_UseOnly_ _.

A configuração auxiliada por serviço é habilitada automaticamente quando você ativa [a delegação de sub-rede](../../virtual-network/subnet-delegation-overview.md) para o `Microsoft.Sql/managedInstances` provedor de recursos.

> [!IMPORTANT] 
> Depois que a delegação de sub-rede for ativada, você não poderá desativá-la até remover o último cluster virtual da sub-rede. Para obter mais detalhes sobre como excluir o cluster virtual, consulte o [artigo](virtual-cluster-delete.md#delete-a-virtual-cluster-from-the-azure-portal)a seguir.

> [!NOTE] 
> Como a configuração de sub-rede auxiliada por serviço é o recurso essencial para manter o SLA, a partir de 1º de maio de 2020, não será possível implantar instâncias gerenciadas em sub-redes que não são delegadas ao provedor de recursos de instância gerenciada. Em 1º de julho 2020 todas as sub-redes que contêm instâncias gerenciadas serão automaticamente delegadas ao provedor de recursos de instância gerenciada. 

## <a name="enabling-subnet-delegation-for-new-deployments"></a>Habilitando a delegação de sub-rede para novas implantações
Para implantar a instância gerenciada no em uma sub-rede vazia, você precisa delegá-la ao `Microsoft.Sql/managedInstances` provedor de recursos, conforme descrito no [artigo](../../virtual-network/manage-subnet-delegation.md)a seguir. _Observe que o artigo referenciado usa o `Microsoft.DBforPostgreSQL/serversv2` provedor de recursos, por exemplo. Em vez disso, você precisará usar o `Microsoft.Sql/managedInstances` provedor de recursos._

## <a name="enabling-subnet-delegation-for-existing-deployments"></a>Habilitando a delegação de sub-rede para implantações existentes

Para habilitar a delegação de sub-rede para sua implantação de instância gerenciada existente, você precisa descobrir a sub-rede da rede virtual onde ela é colocada. 

Para saber isso, você pode verificar `Virtual network/subnet` a `Overview` folha do portal da sua instância gerenciada.

Como alternativa, você pode executar os comandos do PowerShell a seguir para saber isso. Substitua **Subscription-ID** pela sua ID de assinatura. Também substitua **RG-Name** pelo grupo de recursos da instância gerenciada e substitua **mi** pelo nome da instância gerenciada.

```powershell
Install-Module -Name Az

Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount

# Use your subscription ID in place of subscription-id below

Select-AzSubscription -SubscriptionId {subscription-id}

# Replace rg-name with the resource group for your managed instance, and replace mi-name with the name of your managed instance

$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}

$mi.SubnetId
```

Depois de encontrar a sub-rede da instância gerenciada, você precisará delegá-la para `Microsoft.Sql/managedInstances` o provedor de recursos, conforme descrito no [artigo](../../virtual-network/manage-subnet-delegation.md)a seguir. _Observe que o artigo referenciado usa o `Microsoft.DBforPostgreSQL/serversv2` provedor de recursos, por exemplo. Em vez disso, você precisará usar o `Microsoft.Sql/managedInstances` provedor de recursos._


> [!IMPORTANT]
> A habilitação da configuração auxiliada por serviço não causa failover ou interrupção na conectividade para instâncias gerenciadas que já estão na sub-rede.
