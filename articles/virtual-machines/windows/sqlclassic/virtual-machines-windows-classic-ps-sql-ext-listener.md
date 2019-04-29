---
title: Configurar um ouvinte externo para grupos de disponibilidade AlwaysOn | Microsoft Docs
description: Este tutorial explica as etapas de criação de um ouvinte de grupo de disponibilidade AlwaysOn no Azure que é acessível externamente usando o endereço IP virtual público do serviço de nuvem associado.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a2453032-94ab-4775-b976-c74d24716728
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.openlocfilehash: 89623adbddce07cbc3c3ead811f5174d108c9b0e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62101618"
---
# <a name="configure-an-external-listener-for-always-on-availability-groups-in-azure"></a>Configurar um ouvinte externo para grupos de disponibilidade AlwaysOn no Azure
> [!div class="op_single_selector"]
> * [Ouvinte interno](../classic/ps-sql-int-listener.md)
> * [Ouvinte externo](../classic/ps-sql-ext-listener.md)
> 
> 

Este tópico mostra como configurar um ouvinte para um grupo de disponibilidade AlwaysOn que está acessível externamente na Internet. Isso se tornou possível associando o endereço **VIP (IP virtual público)** do serviço de nuvem ao ouvinte.

> [!IMPORTANT] 
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação Clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos.

O seu grupo de disponibilidade pode conter somente réplicas locais, somente no Azure ou locais e no Azure para configurações híbridas. As réplicas do Azure podem residir na mesma região ou em várias regiões usando várias redes virtuais (VNets). As etapas a seguir pressupõem que você já tenha [configurado um grupo de disponibilidade](../classic/portal-sql-alwayson-availability-groups.md) , mas não configurou um ouvinte.

## <a name="guidelines-and-limitations-for-external-listeners"></a>Diretrizes e limitações para ouvintes externos
Observe as seguintes limitações sobre o ouvinte do grupo de disponibilidade no Azure ao implantar usando o endereço VIP público do serviço de nuvem:

* Há suporte para o ouvinte do grupo de disponibilidade no Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2.
* O aplicativo cliente deve residir em um serviço de nuvem diferente daquele que contém as VMs do grupo de disponibilidade. O Azure não dá suporte a retorno de servidor direto com cliente e servidor no mesmo serviço de nuvem.
* Por padrão, as etapas neste artigo mostram como configurar um ouvinte para usar o endereço de IP Virtual (VIP) do serviço de nuvem. No entanto, é possível reservar e criar vários endereços VIP para seu serviço de nuvem. Isso permite que você use as etapas neste artigo para criar vários ouvintes associados a cada VIP diferente. Para obter informações sobre como criar vários endereços VIP, consulte [VIPs múltiplos por serviço de nuvem](../../../load-balancer/load-balancer-multivip.md).
* Se você estiver criando um ouvinte para um ambiente híbrido, a rede local deve ter conectividade com a Internet pública além da VPN site a site com a rede virtual do Azure. Quando estiver na sub-rede do Azure, o ouvinte do grupo de disponibilidade será alcançável somente pelo endereço IP público do respectivo serviço de nuvem.
* Não há suporte para criar um ouvinte externo no mesmo serviço de nuvem em que você também tem um ouvinte externo usando o Balanceador de Carga Interna (ILB).

## <a name="determine-the-accessibility-of-the-listener"></a>Determine a acessibilidade do ouvinte
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Este artigo se concentra na criação de um ouvinte que use o **balanceamento de carga externo**. Se você quiser um ouvinte privado em sua rede virtual, consulte a versão deste artigo que fornece as etapas para configurar um [ouvinte com ILB](../classic/ps-sql-int-listener.md)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Criar pontos de extremidade da VM com balanceamento de carga com retorno de servidor direto
O Balanceamento de carga externo usa o endereço IP Virtual público do serviço de nuvem que hospeda suas VMs. Portanto, você não precisa criar ou configurar o balanceador de carga nesse caso.

Você deve criar um ponto de extremidade de carga equilibrada para cada VM que hospeda uma réplica do Azure. Se você tiver réplicas em várias regiões, cada réplica para essa região deve estar no mesmo serviço de nuvem no mesmo VNet. Criação de réplicas do Grupo de Disponibilidade que abrangem várias regiões do Azure requer configurar diversas VNets. Para saber mais sobre como configurar através de conectividade VNet cruzada, consulte [Configurar conectividade VNet para VNet](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. No portal do Azure, navegue até cada VM hospedando uma réplica e visualize os detalhes.
2. Clique na guia **Pontos de extremidade** para cada uma das VMs.
3. Verifique se o **Nome** e a **Porta Pública** do ponto de extremidade do ouvinte que você deseja usar não esteja em uso. No exemplo abaixo, o nome é "MyEndpoint" e a porta é "1433".
4. No seu cliente local, baixe e instale [o último módulo do PowerShell](https://azure.microsoft.com/downloads/).
5. Inicie o **PowerShell do Azure**. Uma nova sessão do PowerShell é aberta com os módulos administrativos do Azure carregados.
6. Executar **Get-AzurePublishSettingsFile**. Esse cmdlet direciona você a um navegador para baixar um arquivo de configurações de publicação para um diretório local. É possível que seja solicitado para inserir as suas credenciais para a assinatura do Aure.
7. Execute o comando **Import-AzurePublishSettingsFile** com o caminho do arquivo de configurações de publicação que você baixou:
   
        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>
   
    Após o arquivo de configurações de publicação tiver sido importado, você pode gerenciar sua assinatura do Azure na sessão do PowerShell.
    
1. Copie o script do PowerShell abaixo em um editor de texto e defina os valores de variáveis para se adequar ao seu ambiente (os padrões foram fornecidos para alguns parâmetros). Note que o seu grupo de disponibilidade abrange regiões do Azure, você deve executar o script uma vez em cada datacenter para o serviço de nuvem e os nós que residem naquele datacenter.
   
        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
   
        # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
        }

2. Depois de ter definido as variáveis, copie o script do editor de texto para a sua sessão do Azure PowerShell para executá-lo. Se o prompt ainda mostrar >>, digite ENTER novamente para certificar-se de que o script comece a ser executado.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Verifique se KB2854082 está instalado, se necessário
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Abra as portas de firewall em nós do grupo de disponibilidade
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Crie o ouvinte do grupo de disponibilidade

Crie o ouvinte do grupo de disponibilidade em duas etapas. Primeiro, crie o recurso de cluster de ponto de acesso do cliente e configure as dependências. Depois disso, configure os recursos de cluster com o PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Criar o ponto de acesso de cliente e configurar as dependências do cluster
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Configurar os recursos de cluster no PowerShell
1. Para o balanceamento de carga externo, você deve obter o endereço IP virtual público do serviço de nuvem que contém suas réplicas. Faça logon no portal do Azure. Navegue até o serviço de nuvem que contém o grupo de disponibilidade da VM. Abra a exibição **Painel** .
2. Anote o endereço mostrado em **Endereço IP Virtual Público (VIP)**. Se a sua solução abrange VNets, repita essa etapa para cada serviço de nuvem que contém uma VM que hospeda uma réplica.
3. Em uma das VMs, copie o script do PowerShell abaixo em um editor de texto e defina as variáveis para os valores que você anotou anteriormente.
   
        # Define variables
        $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service
   
        Import-Module FailoverClusters
   
        # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.
   
        # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
        # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255
4. Assim que você tiver definido as variáveis, abra uma janela elevada do Windows PowerShell, depois copie o script do editor de texto e cole-o na sua sessão do Azure PowerShell para executá-lo. Se o prompt ainda mostrar >>, digite ENTER novamente para certificar-se de que o script comece a ser executado.
5. Repita esse procedimento em cada VM. Esse script configura o recurso de endereço IP com o endereço IP do serviço de nuvem e define outros parâmetros como a porta de investigação. Quando o recurso de endereço IP é colocado online, ele pode responder à sondagem na porta de investigação do ponto de extremidade com balanceamento de carga criado anteriormente neste tutorial.

## <a name="bring-the-listener-online"></a>Coloque o ouvinte online
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Itens de acompanhamento
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>Testar o ouvinte do grupo de disponibilidade (na mesma VNet)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="test-the-availability-group-listener-over-the-internet"></a>Testar o ouvinte de grupo de disponibilidade (pela Internet)
Para acessar o ouvinte de fora da rede virtual, você deve estar usando balanceamento de carga externa/pública (descrito neste tópico), em vez de ILB, que é acessível somente dentro da mesma VNet. Na cadeia de conexão, você deve especificar o nome do serviço de nuvem. Por exemplo, se você tiver um serviço de nuvem com o nome *meuserviçodenuvem*, a instrução sqlcmd seria a seguinte:

    sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

Ao contrário do exemplo anterior, a autenticação do SQL deve ser usada, porque o chamador não pode usar a autenticação do Windows pela Internet. Para obter mais informações, consulte [grupo de disponibilidade AlwaysOn na VM do Azure: Cenários de conectividade de cliente](https://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx). Ao usar a autenticação do SQL, certifique-se de que você crie o mesmo logon em ambas as réplicas. Para saber mais sobre como solucionar problemas de logons com Grupos de Disponibilidade, consulte [Como mapear logons ou usar um usuário de banco de dados SQL contido para se conectar a outras réplicas e mapear para bancos de dados de disponibilidade](https://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx).

Se as réplicas do AlwaysOn estiverem em sub-redes diferentes, os clientes deverão especificar **MultisubnetFailover=True** na cadeia de conexão. Isso resulta em tentativas de conexão paralela para réplicas em sub-redes diferentes. Observe que esse cenário inclui uma implantação de grupo de disponibilidade do AlwaysOn entre regiões.

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]

