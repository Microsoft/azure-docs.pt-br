---
title: Configurar uma rede virtual existente
titleSuffix: Azure SQL Managed Instance
description: Este artigo descreve como configurar uma rede virtual e uma sub-rede existentes onde você pode implantar o Azure SQL Instância Gerenciada.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 03/17/2020
ms.openlocfilehash: 461acc07ee2217a38f7bb59805d4c7e0de4a1e22
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91617642"
---
# <a name="configure-an-existing-virtual-network-for-azure-sql-managed-instance"></a>Configurar uma rede virtual existente para uma Instância Gerenciada de SQL do Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

O Azure SQL Instância Gerenciada deve ser implantado em uma [rede virtual](../../virtual-network/virtual-networks-overview.md) do Azure e a sub-rede dedicada apenas para instâncias gerenciadas. Você pode usar a rede virtual e a sub-rede existentes se elas estiverem configuradas de acordo com os [requisitos de rede virtual do SQL instância gerenciada](connectivity-architecture-overview.md#network-requirements).

Se um dos seguintes casos se aplica a você, você pode validar e modificar sua rede usando o script explicado neste artigo:

- Você tem uma nova sub-rede que ainda não está configurada.
- Você não tem certeza de que a sub-rede está alinhada com os [requisitos](connectivity-architecture-overview.md#network-requirements).
- Você deseja verificar se a sub-rede ainda está em conformidade com os [requisitos de rede](connectivity-architecture-overview.md#network-requirements) depois que você fez alterações.

> [!Note]
> Você pode criar uma instância gerenciada somente em redes virtuais criadas por meio do modelo de implantação Azure Resource Manager. Redes virtuais do Azure criadas por meio do modelo de implantação clássico não são compatíveis para esse fim. Calcule o tamanho da sub-rede seguindo as diretrizes no artigo [determinar o tamanho da sub-rede para o SQL instância gerenciada](vnet-subnet-determine-size.md) . Você não poderá redimensionar a sub-rede depois de implantar os recursos dentro dela.
>
> Depois que a instância gerenciada é criada, não há suporte para mover a instância ou VNet para outro grupo de recursos ou assinatura.

## <a name="validate-and-modify-an-existing-virtual-network"></a>Validar e modificar uma rede virtual existente

Se você quiser criar uma instância gerenciada dentro de uma sub-rede existente, recomendamos o seguinte script do PowerShell para preparar a sub-rede:

```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/delegate-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/delegateSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```

O script prepara a sub-rede em três etapas:

1. Validar: valida a rede virtual e a sub-rede selecionadas para os requisitos de rede do SQL Instância Gerenciada.
2. Confirmar: mostra ao usuário um conjunto de alterações que precisam ser feitas para preparar a sub-rede para a implantação do SQL Instância Gerenciada. Ele também solicita seu consentimento.
3. Preparar: configura corretamente a rede virtual e a sub-rede.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral, consulte [o que é o SQL instância gerenciada?](sql-managed-instance-paas-overview.md).
- Para obter um tutorial que mostra como criar uma rede virtual, criar uma instância gerenciada e restaurar um banco de dados de um backup de banco de dados, consulte [criar uma instância gerenciada](instance-create-quickstart.md).
- Para problemas de DNS, consulte [Configurando um DNS personalizado](custom-dns-configure.md).
