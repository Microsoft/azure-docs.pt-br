---
title: "Recuperação de desastre - máquinas virtuais do Azure - grupos de disponibilidade do SQL Server | Microsoft Docs"
description: "Este artigo explica como configurar um grupo de disponibilidade do SQL Server em máquinas virtuais do Azure com uma réplica em uma região diferente."
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: jhubbard
editor: monicar
tags: azure-service-management
ms.assetid: 388c464e-a16e-4c9d-a0d5-bb7cf5974689
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.openlocfilehash: 1ce90cf4bae66bfd6387a2698fd9b1ba7fc64595
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-an-always-on-availability-group-on-azure-virtual-machines-in-different-regions"></a>Configurar um Grupo de Disponibilidade Always On em máquinas virtuais do Azure em diferentes regiões

Este artigo explica como configurar uma réplica do SQL Server sempre no grupo de disponibilidade em máquinas virtuais do Azure em uma localização remota do Azure. Use essa configuração para dar suporte à recuperação de desastre.

Este artigo se aplica às máquinas virtuais do Azure no modo do Resource Manager.

A imagem a seguir mostra uma implementação comum de um grupo de disponibilidade em máquinas virtuais do Azure:

   ![Grupo de disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic.png)

Nessa implantação, todas as máquinas virtuais estão em uma região do Azure. As réplicas do grupo de disponibilidade podem ter de confirmação síncrona com failover automático no SQL-1 e SQL-2. Para criar essa arquitetura, veja [Modelo ou tutorial de Grupo de Disponibilidade](virtual-machines-windows-portal-sql-availability-group-overview.md).

Essa arquitetura é vulnerável a tempo de inatividade se a região do Azure se tornar inacessível. Para superar essa vulnerabilidade, adicione uma réplica em uma região do Azure diferente. O diagrama a seguir mostra como a nova arquitetura:

   ![Recuperação de desastre de grupo de disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic-dr.png)

O diagrama acima mostra uma nova máquina virtual chamada SQL-3. SQL-3 está em outra região do Azure. SQL-3 é adicionado para o Cluster de Failover do Windows Server. SQL-3 pode hospedar uma réplica do grupo de disponibilidade. Finalmente, observe que a região do Azure para o SQL-3 tem um novo balanceador de carga do Azure.

>[!NOTE]
> Um conjunto de disponibilidade do Azure é necessário quando mais de uma máquina virtual está na mesma região. Se houver apenas uma máquina virtual na região, o conjunto de disponibilidade não é necessário. Você só pode colocar uma máquina virtual em um momento da criação do conjunto de disponibilidade. Se a máquina virtual já estiver em um conjunto de disponibilidade, você poderá adicionar uma máquina virtual para fazer uma réplica adicional mais tarde.

Nesta arquitetura, a réplica na região remota normalmente é configurada com o modo de disponibilidade de confirmação assíncrona e modo de failover manual.

Quando as réplicas de grupo de disponibilidade em máquinas virtuais do Azure em diferentes regiões do Azure, requer que cada região:

* Um gateway de rede virtual
* Uma conexão de gateway de rede virtual

O diagrama a seguir mostra como as redes se comunicam entre data centers.

   ![Grupo de Disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-dr/01-vpngateway-example.png)

>[!IMPORTANT]
>Essa arquitetura incorre em encargos de dados de saída para os dados replicados entre regiões do Azure. Veja [Preços de Largura de Banda](http://azure.microsoft.com/pricing/details/bandwidth/).  

## <a name="create-remote-replica"></a>Criar réplica remota

Para criar uma réplica em um data center remoto, execute as seguintes etapas:

1. [Criar uma rede virtual na nova região](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

1. [Configurar uma conexão de rede virtual usando o portal do Azure](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

   >[!NOTE]
   >Em alguns casos, você talvez precise usar o PowerShell para criar a conexão de VNet para VNet. Por exemplo, se você usar diferentes contas do Azure, será possível configurar a conexão no portal. Neste caso, veja [Configurar uma conexão de Rede Virtual para Rede Virtual usando o portal do Azure](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

1. [Crie um controlador de domínio na nova região](../../../active-directory/active-directory-new-forest-virtual-machine.md).

   Esse controlador de domínio fornecerá autenticação se o controlador de domínio no site primário não estiver disponível.

1. [Criar uma máquina virtual do SQL Server na nova região](virtual-machines-windows-portal-sql-server-provision.md).

1. [Criar um balanceador de carga do Azure na rede em que a nova região](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

   Este balanceador de carga deve:

   - Estar na mesma rede e sub-rede como a nova máquina virtual.
   - Ter um endereço IP estático para o ouvinte do grupo de disponibilidade.
   - Inclua um pool de back-end que consiste somente as máquinas virtuais na mesma região que o balanceador de carga.
   - Use um teste de porta TCP específico para o endereço IP.
   - Ter uma regra específica para o SQL Server na mesma região balanceamento de carga.  

1. [Adicionar o recurso cluster de Failover para o novo servidor SQL](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

1. [Adicionar o novo SQL Server ao domínio](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

1. [Definir a nova conta de serviço do SQL Server para usar uma conta de domínio](virtual-machines-windows-portal-sql-availability-group-prereq.md#setServiceAccount).

1. [Adicionar o novo SQL Server para o Cluster de Failover do Windows Server](virtual-machines-windows-portal-sql-availability-group-tutorial.md#addNode).

1. Crie um recurso de endereço IP do cluster.

   Você pode criar o recurso de endereço IP no Gerenciador de Cluster de Failover. Clique a função do grupo de disponibilidade, clique em **adicionar recurso**, **mais recursos**e clique em **endereço IP**.

   ![Criar endereço IP](./media/virtual-machines-windows-portal-sql-availability-group-dr/20-add-ip-resource.png)

   Configure o endereço IP da seguinte maneira:

   - Use a rede do data center remoto.
   - Atribua o endereço IP do balanceador de carga do Azure novo. 

1. No novo servidor SQL no SQL Server Configuration Manager, [habilitar grupos de disponibilidade AlwaysOn](http://msdn.microsoft.com/library/ff878259.aspx).

1. [Abrir portas de firewall no novo servidor SQL](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall).

   Os números de porta que você precisa abrir dependem de seu ambiente. Investigação de integridade do balanceador de carga de portas abertas para o ponto de extremidade de espelhamento e o Azure.

1. [Adicionar uma réplica para o grupo de disponibilidade no novo servidor SQL](http://msdn.microsoft.com/library/hh213239.aspx).

   Para uma réplica em uma região remota do Azure, configurá-lo para a replicação assíncrona com failover manual.  

1. Adicione o recurso de endereço IP como uma dependência para o cluster do ouvinte cliente acesso ponto (nome da rede).

   Captura de tela a seguir mostra um recurso de cluster de endereço IP configurado corretamente:

   ![Grupo de Disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-dr/50-configure-dependency-multiple-ip.png)

   >[!IMPORTANT]
   >O grupo de recursos de cluster inclui os dois endereços IP. Os dois endereços IP são dependências para o ponto de acesso de cliente do ouvinte. Use o **ou** operador na configuração de dependência do cluster.

1. [Definir os parâmetros do cluster no PowerShell](virtual-machines-windows-portal-sql-availability-group-tutorial.md#setparam).

Execute o script do PowerShell com o nome de rede do cluster, o endereço IP e a porta de investigação configurado no balanceador de carga na nova região.

   ```PowerShell
   $ClusterNetworkName = "<MyClusterNetworkName>" # The cluster name for the network in the new region (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name).
   $IPResourceName = "<IPResourceName>" # The cluster name for the new IP Address resource.
   $ILBIP = “<n.n.n.n>” # The IP Address of the Internal Load Balancer (ILB) in the new region. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn> # The probe port you set on the ILB.

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

## <a name="set-connection-for-multiple-subnets"></a>Definir conexão para várias sub-redes

A réplica no data center remoto é parte do grupo de disponibilidade, mas ele está em uma sub-rede diferente. Se essa réplica se tornar a réplica primária, tempo limite de conexão do aplicativo podem ocorrer. Esse comportamento é o mesmo que um grupo de disponibilidade local em uma implantação de várias sub-redes. Para permitir conexões de cliente de aplicativos, atualize a conexão de cliente ou configurar a resolução de nome do recurso de nome de rede de cluster de cache.

Preferencialmente, atualize as cadeias de conexão do cliente para definir `MultiSubnetFailover=Yes`. Consulte [conectar-se ao MultiSubnetFailover](http://msdn.microsoft.com/library/gg471494#Anchor_0).

Se você não pode modificar as cadeias de conexão, você pode configurar o cache de resolução de nome. Consulte [tempos limite de conexão no grupo de disponibilidade de várias sub-redes](http://blogs.msdn.microsoft.com/alwaysonpro/2014/06/03/connection-timeouts-in-multi-subnet-availability-group/).

## <a name="fail-over-to-remote-region"></a>Failover para região remota

Para testar a conectividade do ouvinte para a região remota, é possível realizar failover da réplica para a região remota. Enquanto a réplica é assíncrona, o failover é vulnerável à perda de dados. Para fazer failover sem perda de dados, alterar o modo de disponibilidade para síncrono e defina o modo de failover como automático. Use as seguintes etapas:

1. Em **Pesquisador**, conecte-se à instância do SQL Server que hospeda a réplica primária.
1. Em **grupos de disponibilidade do AlwaysOn**, **grupos de disponibilidade**, clique com botão direito seu grupo de disponibilidade e clique em **propriedades**.
1. No **geral** página, em **réplicas de disponibilidade**, definir a réplica secundária no site para usar a recuperação de desastres **confirmação síncrona** modo de disponibilidade e **automáticas** modo de failover.
1. Se você tiver uma réplica secundária no mesmo site que a réplica primária para alta disponibilidade, definido desta réplica como **confirmação assíncrona** e **Manual**.
1. Clique em OK.
1. Em **Pesquisador**, com o botão direito no grupo de disponibilidade e clique em **Mostrar painel**.
1. No painel, verifique se a réplica no site de recuperação de desastre está sincronizada.
1. Em **Pesquisador**, com o botão direito no grupo de disponibilidade e clique **Failover...** . SQL Server Management Studio abre um Assistente de failover do SQL Server.  
1. Clique em **Avançar** e selecione a instância do SQL Server no site de recuperação de desastre. Clique em **Avançar** novamente.
1. Conecte-se à instância do SQL Server no site de recuperação de desastre e clique em **Avançar**.
1. Sobre o **resumo** página, verifique as configurações e clique em **concluir**.

Depois de testar a conectividade, mova a réplica primária de volta para seu data center principal e definir o modo de disponibilidade para suas configurações operacionais normais. A tabela a seguir mostra as configurações operacionais normais para a arquitetura descrita neste documento:

| Local | Instância do servidor | Função | Modo de Disponibilidade | Modo de failover
| ----- | ----- | ----- | ----- | -----
| Data center principal | SQL-1 | Primário | Síncrono | Automático
| Data center principal | SQL-2 | Secundário | Síncrono | Automático
| Centro de dados remotos ou secundários | SQL-3 | Secundário | Assíncrono | Manual


### <a name="more-information-about-planned-and-forced-manual-failover"></a>Para obter mais informações sobre failover manual forçado e não planejado

Para saber mais, consulte os tópicos a seguir:

- [Executar um Failover Manual planejado de um grupo de disponibilidade (SQL Server)](http://msdn.microsoft.com/library/hh231018.aspx)
- [Executar um Failover Manual forçado de um grupo de disponibilidade (SQL Server)](http://msdn.microsoft.com/library/ff877957.aspx)

## <a name="additional-links"></a>Links adicionais

* [Grupos de disponibilidade AlwaysOn](http://msdn.microsoft.com/library/hh510230.aspx)
* [Máquinas Virtuais do Azure](http://docs.microsoft.com/azure/virtual-machines/windows/)
* [Balanceadores de Carga do Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)
* [Conjuntos de Disponibilidade do Azure](../manage-availability.md)
