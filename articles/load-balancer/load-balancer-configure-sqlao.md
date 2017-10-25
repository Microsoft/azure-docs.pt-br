---
title: Configurar balanceador de carga para SQL sempre ativo | Microsoft Docs
description: "Configurar o balanceador de carga para trabalhar com o SQL sempre ativo e como aproveitar o PowerShell para criar o balanceador de carga para a implementação do SQL"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: d7bc3790-47d3-4e95-887c-c533011e4afd
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 3ebbf1c4009d89b1f18b2ff8ff5dd243c456dff8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-load-balancer-for-sql-always-on"></a>Configurar o balanceador de carga para SQL sempre ativo

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Grupos de Disponibilidade AlwaysOn do SQL Server agora podem ser executados com ILB. O Grupo de Disponibilidade é uma solução fundamental do SQL Server para alta disponibilidade e recuperação de desastres. O Ouvinte do Grupo de Disponibilidade permite que aplicativos cliente conectem-se continuamente com a réplica primária, independentemente do número de réplicas na configuração.

O nome do ouvinte (DNS) é mapeado para um endereço IP com balanceamento de carga e o balanceador de carga do Azure direciona o tráfego de entrada apenas para o servidor primário no conjunto de réplicas.

Você pode usar o suporte do ILB para pontos de extremidade AlwaysOn do SQL Server (ouvinte). Agora você tem controle sobre a acessibilidade do ouvinte e pode escolher o endereço IP de balanceamento de carga de uma sub-rede específica em sua VNet (Rede Virtual).

Usando o ILB no ouvinte, o ponto de extremidade do SQL Server (por exemplo, Server=tcp:ListenerName,1433;Database=DatabaseName) é acessível somente por:

* Serviços e VMs na mesma rede virtual
* Serviços e VMs da rede local conectada
* Serviços e VMs de VNets interconectadas

![ILB_SQLAO_NewPic](./media/load-balancer-configure-sqlao/sqlao1.png)

Figura 1 – SQL AlwaysOn configurado com o balanceador de carga para a Internet

## <a name="add-internal-load-balancer-to-the-service"></a>Adicionar balanceador de carga interno ao serviço

1. No exemplo a seguir, configuraremos uma rede virtual que contém uma sub-rede chamada "Subnet-1":

    ```powershell
    Add-AzureInternalLoadBalancer -InternalLoadBalancerName ILB_SQL_AO -SubnetName Subnet-1 -ServiceName SqlSvc
    ```
2. Adicionar pontos de extremidade de balanceamento de carga ao ILB em cada VM

    ```powershell
    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc1 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -
    DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc2 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM
    ```

    No exemplo acima, você tem 2 VMs chamadas "sqlsvc1" e "sqlsvc2" em execução no serviço de nuvem "Sqlsvc". Depois de criar o ILB com a opção `DirectServerReturn`, adicione pontos de extremidade com balanceamento de carga ao ILB para permitir que o SQL configure os ouvintes para os grupos de disponibilidade.

Para obter mais informações sobre o SQL AlwaysOn, consulte [Configure an internal load balancer for an AlwaysOn availability group in Azure (Configurar um balanceador de carga interno para um grupo de disponibilidade do AlwaysOn no Azure)](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

## <a name="see-also"></a>Consulte também
[Introdução à configuração de um balanceador de carga para a Internet](load-balancer-get-started-internet-arm-ps.md)

[Introdução à configuração de um balanceador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurar um modo de distribuição do balanceador de carga](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite de TCP ocioso para o balanceador de carga](load-balancer-tcp-idle-timeout.md)
