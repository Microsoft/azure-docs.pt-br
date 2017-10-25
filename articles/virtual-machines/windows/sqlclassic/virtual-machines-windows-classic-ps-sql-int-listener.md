---
title: Configurar um ouvinte de ILB para grupos de disponibilidade AlwaysOn no Azure | Microsoft Docs
description: "Este tutorial usa os recursos criados com o modelo de implantação clássico e cria um ouvinte para grupo de disponibilidade AlwaysOn no Azure que usa um balanceador de carga interno."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 291288a0-740b-4cfa-af62-053218beba77
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.openlocfilehash: fea70b389b1f1d6af963e3f14fdc48e8d857dd53
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-an-ilb-listener-for-always-on-availability-groups-in-azure"></a>Configurar um ouvinte de ILB para grupos de disponibilidade AlwaysOn no Azure
> [!div class="op_single_selector"]
> * [Ouvinte interno](../classic/ps-sql-int-listener.md)
> * [Ouvinte externo](../classic/ps-sql-ext-listener.md)
>
>

## <a name="overview"></a>Visão geral

> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Azure Resource Manager e Clássico](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação clássico. Recomendamos que a maioria das novas implantações use o modelo do Resource Manager.

Para configurar um ouvinte para um grupo de disponibilidade AlwaysOn no modelo do Resource Manager, veja [Configurar um balanceador de carga para um grupo de disponibilidade AlwaysOn no Azure](../sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

O seu grupo de disponibilidade pode conter réplicas somente locais, somente no Azure ou tanto locais quanto no Azure para configurações híbridas. As réplicas do Azure podem residir na mesma região ou em várias regiões que usam várias redes virtuais. Os procedimentos neste artigo pressupõem que você já tenha [configurado um grupo de disponibilidade](../classic/portal-sql-alwayson-availability-groups.md), mas ainda não configurou um ouvinte.

## <a name="guidelines-and-limitations-for-internal-listeners"></a>Diretrizes e limitações para ouvintes internos
O uso de um ILB (balanceador de carga interno) com um ouvinte do grupo de disponibilidade no Azure está sujeito às diretrizes a seguir:

* Há suporte para o ouvinte do grupo de disponibilidade no Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2.
* Há suporte apenas para um ouvinte de grupo de disponibilidade para cada serviço de nuvem, pois o ouvinte é configurado para o ILB, e há apenas um ILB por serviço de nuvem. No entanto, é possível criar vários ouvintes externos. Para obter mais informações, veja [Configurar um ouvinte externo para grupos de disponibilidade AlwaysOn no Azure](../classic/ps-sql-ext-listener.md).

## <a name="determine-the-accessibility-of-the-listener"></a>Determine a acessibilidade do ouvinte
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Este artigo concentra-se na criação de um ouvinte que use um ILB. Se você precisar de um ouvinte externo/público, consulte a versão deste artigo que discute a configuração de um [ouvinte externo](../classic/ps-sql-ext-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Criar pontos de extremidade da VM com balanceamento de carga com retorno de servidor direto
Primeiro você cria um ILB executando o script mais adiante nesta seção.

Crie um ponto de extremidade com carga equilibrada para cada VM que hospeda uma réplica do Azure. Se você tiver réplicas em várias regiões, cada réplica para essa região deve estar no mesmo serviço de nuvem na mesma rede virtual do Azure. A criação de réplicas do grupo de disponibilidade que abrangem várias regiões do Azure exige a configuração de diversas redes virtuais. Para saber mais sobre como configurar a conectividade entre redes virtuais, confira [Configurar rede virtual para conectividade de rede virtual](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. No Portal do Azure, acesse cada VM que hospeda uma réplica para exibir os detalhes.

2. Clique na guia **Pontos de extremidade** para cada VM.

3. Verifique se o **Nome** e a **Porta Pública** do ponto de extremidade do ouvinte que você deseja usar não esteja em uso. No exemplo nesta seção, o nome é *MyEndpoint*, e a porta é *1433*.

4. Em seu cliente local, baixe e instale [o módulo mais recente do PowerShell](https://azure.microsoft.com/downloads/).

5. Iniciar o Azure PowerShell.  
    Uma nova sessão do PowerShell é aberta com os módulos administrativos do Azure carregados.

6. Execute `Get-AzurePublishSettingsFile`. Esse cmdlet direciona você a um navegador para baixar um arquivo de configurações de publicação para um diretório local. Talvez você receba uma solicitação para inserir as suas credenciais de entrada de sua assinatura do Aure.

7. Execute o comando `Import-AzurePublishSettingsFile` com o caminho do arquivo de configurações de publicação que você baixou:

        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>

    Após o arquivo de configurações de publicação tiver sido importado, você pode gerenciar sua assinatura do Azure na sessão do PowerShell.

8. Para o *ILB*, atribua um endereço IP estático. Examine a configuração atual da rede virtual executando o seguinte comando:

        (Get-AzureVNetConfig).XMLConfiguration
9. Anote o nome da *Sub-rede* que contém as VMs que hospedam as réplicas. Esse nome é usado no parâmetro $SubnetName no script.

10. Anote o nome do *VirtualNetworkSite* e do *AddressPrefix* inicial da sub-rede que contém as VMs que hospedam as réplicas. Procure um endereço IP disponível, passando os dois valores para o comando `Test-AzureStaticVNetIP` e examinando os *AvailableAddresses*. Por exemplo, se a rede virtual fosse chamada de *MyVNet* e tivesse um intervalo de endereços de sub-rede iniciado em *172.16.0.128*, o comando a seguir listaria os endereços disponíveis:

        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses
11. Selecione um dos endereços disponíveis e use-o no parâmetro $ILBStaticIP do script na próxima etapa.

12. Copie o seguinte script do PowerShell em um editor de texto e defina os valores de variáveis para se adequar ao seu ambiente. Alguns parâmetros já receberam valores padrão.  

    Observe que as implantações existentes que usam grupos de afinidade não podem adicionar o ILB. Para saber mais sobre os requisitos do ILB, consulte [Visão geral do balanceador de carga interno](../../../load-balancer/load-balancer-internal-overview.md).

    Além disso, se o seu grupo de disponibilidade abrange regiões do Azure, você deve executar o script uma vez em cada datacenter para o serviço de nuvem e os nós que residem naquele datacenter.

        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
        $SubnetName = "<MySubnetName>" # subnet name that the replicas use in the virtual network
        $ILBStaticIP = "<MyILBStaticIPAddress>" # static IP address for the ILB in the subnet
        $ILBName = "AGListenerLB" # customize the ILB name or use this default value

        # Create the ILB
        Add-AzureInternalLoadBalancer -InternalLoadBalancerName $ILBName -SubnetName $SubnetName -ServiceName $ServiceName -StaticVNetIPAddress $ILBStaticIP

        # Configure a load-balanced endpoint for each node in $AGNodes by using ILB
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -LBSetName "ListenerEndpointLB" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ILBName -DirectServerReturn $true | Update-AzureVM
        }

13. Depois de definir as variáveis, copie o script do editor de texto para sua sessão do PowerShell a fim de executá-lo. Se o prompt ainda mostrar **>>**, pressione Enter novamente para ter certeza de que o script será executado.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Verifique se KB2854082 está instalado, se necessário
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Abra as portas de firewall em nós do grupo de disponibilidade
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Crie o ouvinte do grupo de disponibilidade

Crie o ouvinte do grupo de disponibilidade em duas etapas. Primeiro, crie o recurso de cluster de ponto de acesso do cliente e configure as dependências. Segundo, configure os recursos de cluster no PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Criar o ponto de acesso de cliente e configurar as dependências do cluster
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Configurar os recursos de cluster no PowerShell
1. Para o ILB, você deve usar o endereço IP do ILB criado anteriormente. Para obter o endereço IP no PowerShell, use este script:

        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

2. Em uma das VMs, copie o script do PowerShell de seu sistema operacional em um editor de texto e defina as variáveis para os valores que você anotou anteriormente.

    Para o Windows Server 2012 ou posterior, use este script:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = “<X.X.X.X>” # the IP address of the ILB

        Import-Module FailoverClusters

        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

    Para o Windows Server 2008 R2, use este script:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = “<X.X.X.X>” # the IP address of the ILB

        Import-Module FailoverClusters

        cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255

3. Depois de definir as variáveis, abra uma janela elevada do Windows PowerShell, cole o script do editor de texto em sua sessão do PowerShell para executá-lo. Se o prompt ainda mostrar **>>**, pressione Enter novamente para ter certeza de que o script será executado.

4. Repita as etapas anteriores para cada VM.  
    Esse script configura o recurso de endereço IP com o endereço IP do serviço de nuvem e define outros parâmetros, como a porta de investigação. Quando o recurso de endereço IP é colocado online, ele pode responder à sondagem na porta de investigação do ponto de extremidade com balanceamento de carga criado anteriormente.

## <a name="bring-the-listener-online"></a>Coloque o ouvinte online
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Itens de acompanhamento
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-virtual-network"></a>Testar o ouvinte do grupo de disponibilidade (na mesma rede virtual)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]
