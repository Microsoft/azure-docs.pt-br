---
title: "Estender o HDInsight com a Rede Virtual – Azure | Microsoft Docs"
description: Saiba como usar a Rede Virtual do Azure para conectar o HDInsight a outros recursos de nuvem ou recursos no seu datacenter
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 37b9b600-d7f8-4cb1-a04a-0b3a827c6dcc
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/21/2017
ms.author: larryfr
ms.openlocfilehash: 17059c5861962a56ed2011a8f8874fc23673ed68
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="extend-azure-hdinsight-using-an-azure-virtual-network"></a>Estender o Azure HDInsight usando uma Rede Virtual do Azure

Saiba como usar o HDInsight com uma [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md). O uso de uma Rede Virtual do Azure permite os seguintes cenários:

* Conectar-se ao HDInsight diretamente em uma rede local.

* Conectar o HDInsight a armazenamentos de dados em uma Rede virtual do Azure.

* Acessar diretamente os serviços do Hadoop que não estão disponíveis publicamente na Internet. Por exemplo, as APIs do Kafka ou a API Java do HBase.

> [!WARNING]
> As informações descritas neste documento exigem noções básicas da rede TCP/IP. Se você não estiver familiarizado com a rede TCP/IP, trabalhe junto com alguém que está antes de fazer modificações nas redes de produção.

> [!IMPORTANT]
> Se estiver buscando orientações passo a passo sobre como conectar o HDInsight à sua rede local usando uma Rede Virtual do Azure, consulte o documento [Conectar o HDInsight à sua rede local](connect-on-premises-network.md).

## <a name="planning"></a>Planejamento

Estas são as perguntas que você deve responder ao planejar a instalação do HDInsight em uma rede virtual:

* Você precisa instalar o HDInsight em uma rede virtual existente? Ou você está criando uma nova rede?

    Se você estiver usando uma rede virtual existente, talvez precise modificar a configuração de rede antes de instalar o HDInsight. Para obter mais informações, consulte a seção [Adicionar o HDInsight a uma rede virtual existente](#existingvnet).

* Você deseja conectar a rede virtual que contém o HDInsight a outra rede virtual ou à rede local?

    Para trabalhar com recursos em redes com facilidade, talvez você precise criar um DNS personalizado e configurar o encaminhamento de DNS. Para obter mais informações, consulte a seção [Conectando várias redes](#multinet).

* Você deseja restringir/redirecionar o tráfego de entrada ou de saída para o HDInsight?

    O HDInsight deve ter comunicação irrestrita com endereços IP específicos no data center do Azure. Também há diversas portas que devem receber permissão por meio de firewalls para a comunicação do cliente. Para obter mais informações, consulte a seção [Controlando o tráfego de rede](#networktraffic).

## <a id="existingvnet"></a>Adicionar o HDInsight uma rede virtual existente

Use as etapas descritas nesta seção para descobrir como adicionar um novo HDInsight a uma Rede Virtual do Azure existente.

> [!NOTE]
> Não é possível adicionar um cluster HDInsight existente a uma rede virtual.

1. Você está usando um modelo de implantação clássico ou do Resource Manager para a rede virtual?

    O HDInsight 3.4 e posterior exige uma rede virtual do Resource Manager. Versões anteriores do HDInsight exigiam uma rede virtual clássica.

    Se a rede existente for uma rede virtual clássica, é necessário criar uma rede virtual do Resource Manager e, em seguida, conectar as duas. [Conectando VNets clássicas a novas VNets](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

    Depois de ingressar, o HDInsight instalado na rede do Resource Manager pode interagir com os recursos da rede clássica.

2. Você usa o túnel forçado? O túnel forçado é uma configuração de sub-rede que força o tráfego da Internet de saída para um dispositivo para inspeção e log. O HDInsight não dá suporte ao túnel forçado. Remova o túnel forçado antes de instalar o HDInsight em uma sub-rede ou crie uma nova sub-rede para o HDInsight.

3. Você usa grupos de segurança de rede, rotas definidas pelo usuário ou Soluções de Virtualização de Rede para restringir o tráfego para dentro ou fora da rede virtual?

    Como um serviço gerenciado, o HDInsight exige acesso irrestrito a vários endereços IP no data center do Azure. Para permitir a comunicação com esses endereços IP, atualize os grupos de segurança de rede ou as rotas definidas pelo usuário existentes.

    O HDInsight hospeda vários serviços, que usam uma variedade de portas. Não bloqueie o tráfego para essas portas. Para obter uma lista de portas para permissão por meio de firewalls de solução de virtualização, consulte a seção [Segurança](#security).

    Para encontrar a configuração de segurança existente, use os seguintes comandos do Azure PowerShell ou da CLI do Azure:

    * Grupos de segurança de rede

        ```powershell
        $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
        get-azurermnetworksecuritygroup -resourcegroupname $resourceGroupName
        ```

        ```azurecli-interactive
        read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
        az network nsg list --resource-group $RESOURCEGROUP
        ```

        Para obter mais informações, consulte o documento [Solução de problemas dos grupos de segurança de rede](../virtual-network/virtual-network-nsg-troubleshoot-portal.md).

        > [!IMPORTANT]
        > As regras do grupo de segurança de rede são aplicadas em ordem, com base na prioridade da regra. A primeira regra que corresponde ao padrão de tráfego é aplicada e nenhuma outra é aplicada ao tráfego. Ordene as regras da mais permissiva para a menos permissiva. Para obter mais informações, consulte o documento [Filtrar o tráfego de rede com grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md).

    * Rotas definidas pelo usuário

        ```powershell
        $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
        get-azurermroutetable -resourcegroupname $resourceGroupName
        ```

        ```azurecli-interactive
        read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
        az network route-table list --resource-group $RESOURCEGROUP
        ```

        Para obter mais informações, consulte o documento [Solução de problemas de rotas](../virtual-network/virtual-network-routes-troubleshoot-portal.md).

4. Crie um cluster HDInsight e selecione a Rede Virtual do Azure durante a configuração. Use as etapas nos documentos a seguir para entender o processo de criação de cluster:

    * [Criar o HDInsight usando o portal do Azure](hdinsight-hadoop-create-linux-clusters-portal.md)
    * [Criar o HDInsight usando o Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
    * [Criar o HDInsight usando a CLI do Azure 1.0](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [Criar o HDInsight usando um modelo do Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

  > [!IMPORTANT]
  > Adicionar HDInsight a uma rede virtual é uma etapa de configuração opcional. Verifique se você selecionou a rede virtual ao configurar o cluster.

## <a id="multinet"></a>Conectando várias redes

O maior desafio em uma configuração de várias redes é a resolução de nomes entre as redes.

O Azure fornece a resolução de nomes para os serviços do Azure instalados em uma rede virtual do Azure. Essa resolução de nomes interna permite que o HDInsight se conecte aos seguintes recursos usando um FQDN (nome de domínio totalmente qualificado):

* Qualquer recurso que está disponível na Internet. Por exemplo, microsoft.com, google.com.

* Qualquer recurso que está na mesma Rede Virtual do Azure, usando o __nome DNS interno__ do recurso. Por exemplo, ao usar a resolução de nomes padrão, estes são nomes DNS internos de exemplo atribuídos aos nós de trabalho do HDInsight:

    * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net
    * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net

    Esses dois nós podem se comunicar diretamente um com o outro e com outros nós no HDInsight, usando nomes DNS internos.

A resolução de nomes padrão __não__ permite que o HDInsight resolva os nomes de recursos em redes ingressadas na rede virtual. Por exemplo, é comum ingressar a rede local na rede virtual. Com apenas a resolução de nomes padrão, o HDInsight não pode acessar recursos na rede local por nome. O oposto também é verdadeiro: os recursos na rede local não podem acessar recursos na rede virtual por nome.

> [!WARNING]
> É necessário criar o servidor DNS personalizado e configurar a rede virtual para usá-lo antes de criar o cluster HDInsight.

Para permitir a resolução de nomes entre a rede virtual e os recursos em redes ingressadas, execute as seguintes ações:

1. Crie um servidor DNS personalizado na Rede Virtual do Azure na qual você pretende instalar o HDInsight.

2. Configure a rede virtual para usar o servidor DNS personalizado.

3. Encontre o sufixo DNS atribuído pelo Azure à rede virtual. Esse valor é semelhante a `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net`. Para obter informações sobre como encontrar o sufixo DNS, consulte a seção [Exemplo: DNS personalizado](#example-dns).

4. Configure o encaminhamento entre os servidores DNS. A configuração depende do tipo de rede remota.

    * Se a rede remota for uma rede local, configure o DNS da seguinte maneira:
        
        * __DNS personalizado__ (na rede virtual):

            * Encaminhe as solicitações de sufixo DNS da rede virtual para o resolvedor recursivo do Azure (168.63.129.16). O Azure administra as solicitações de recursos na rede virtual

            * Encaminhe todas as outras solicitações para o servidor DNS local. O DNS local manipula todas as outras solicitações de resolução de nomes, até mesmo solicitações de recursos da Internet, como Microsoft.com.

        * __DNS local__: encaminhe solicitações do sufixo DNS da rede virtual para o servidor DNS personalizado. Em seguida, o servidor DNS personalizado encaminha-as para o resolvedor recursivo do Azure.

        Essa configuração encaminha as solicitações de nomes de domínio totalmente qualificados que contêm o sufixo DNS da rede virtual para o servidor DNS personalizado. Todas as outras solicitações (até mesmo para endereços da Internet pública) são manipuladas pelo servidor DNS local.

    * Se a rede remota for outra Rede Virtual do Azure, configure o DNS da seguinte maneira:

        * __DNS personalizado__ (em cada rede virtual):

            * As solicitações do sufixo DNS das redes virtuais são encaminhadas para os servidores DNS personalizados. O DNS em cada rede virtual é responsável por resolver recursos em sua rede.

            * Encaminhe todas as outras solicitações para o resolvedor recursivo do Azure. O resolvedor recursivo é responsável por resolver recursos locais e da Internet.

        O servidor DNS de cada rede encaminha solicitações para a outra, com base em sufixo DNS. Outras solicitações são resolvidas com o resolvedor recursivo do Azure.

    Para obter um exemplo de cada configuração, consulte a seção [Exemplo: DNS personalizado](#example-dns).

Para obter mais informações, consulte o documento [Resolução de nomes para VMs e instâncias de função](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="directly-connect-to-hadoop-services"></a>Conectar-se diretamente aos serviços do Hadoop

A maior parte da documentação no HDInsight supõe que você tenha acesso ao cluster via Internet. Por exemplo, você pode se conectar ao cluster em https://NOMEDOCLUSTER.azurehdinsight.net. Esse endereço usa o gateway público, que não estará disponível se você tiver usado NSGs ou UDRs para restringir o acesso da Internet.

Para se conectar ao Ambari e a outras páginas da Web por meio da rede virtual, use as seguintes etapas:

1. Para descobrir os FQDNs (nomes de domínio totalmente qualificados) internos dos nós do cluster HDInsight, use um dos seguintes métodos:

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    Na lista de nós retornados, encontre o FQDN dos nós de cabeçalho e use-os para se conectar ao Ambari e a outros serviços Web. Por exemplo, use `http://<headnode-fqdn>:8080` para acessar o Ambari.

    > [!IMPORTANT]
    > Alguns serviços hospedados em nós de cabeçalho ficam ativos apenas em um nó por vez. Se você tentar acessar um serviço em um nó de cabeçalho e ele retornar um erro 404, mude para o outro nó de cabeçalho.

2. Para determinar o nó e a porta nos quais um serviço está disponível, consulte o documento [Portas usadas pelos serviços do Hadoop no HDInsight](./hdinsight-hadoop-port-settings-for-services.md).

## <a id="networktraffic"></a> Controlando o tráfego de rede

O tráfego de rede em Redes Virtuais do Azure pode ser controlado com os seguintes métodos:

* Os **NSGs** (grupos de segurança de rede) permitem filtrar o tráfego de entrada e de saída para a rede. Para obter mais informações, consulte o documento [Filtrar o tráfego de rede com grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md).

    > [!WARNING]
    > O HDInsight não dá suporte à restrição do tráfego de saída.

* As **UDRs** (rotas definidas pelo usuário) definem como o tráfego flui entre os recursos na rede. Para obter mais informações, consulte o documento [Rotas definidas pelo usuário e encaminhamento IP](../virtual-network/virtual-networks-udr-overview.md).

* As **soluções de virtualização de rede** replicam a funcionalidade de dispositivos, como firewalls e roteadores. Para obter mais informações, consulte o documento [Dispositivos de rede](https://azure.microsoft.com/solutions/network-appliances).

Como um serviço gerenciado, o HDInsight exige acesso irrestrito aos serviços de integridade e de gerenciamento do Azure na nuvem do Azure. Ao usar NSGs e UDRs, verifique se o HDInsight e esses serviços podem se comunicar com o HDInsight.

O HDInsight expõe serviços em várias portas. Ao usar um firewall de solução de virtualização, você deve permitir o tráfego nas portas usadas para esses serviços. Para obter mais informações, consulte a seção [Portas necessárias].

### <a id="hdinsight-ip"></a> HDInsight com grupos de segurança de rede e rotas definidas pelo usuário

Se você pretende usar **grupos de segurança de rede** ou **rotas definidas pelo usuário** para controlar o tráfego de rede, execute as seguintes ações antes de instalar o HDInsight:

1. Identifique a região do Azure que você pretende usar para o HDInsight.

2. Identifique os endereços IP necessários para o HDInsight. Para obter mais informações, consulte a seção [Endereços IP necessários para o HDInsight](#hdinsight-ip).

3. Crie ou modifique os grupos de segurança de rede ou as rotas definidas pelo usuário da sub-rede na qual você pretende instalar o HDInsight.

    * __Grupos de segurança de rede__: permita o tráfego de __entrada__ na porta __443__ dos endereços IP.
    * __Rotas definidas pelo usuário__: crie uma rota para cada endereço IP e defina o __Tipo do próximo salto__ como __Internet__.

Para obter mais informações sobre grupos de segurança de rede ou rotas definidas pelo usuário, consulte a seguinte documentação:

* [Grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md)

* [Rotas definidas pelo usuário](../virtual-network/virtual-networks-udr-overview.md)

#### <a name="forced-tunneling"></a>Túnel forçado

O túnel forçado é uma configuração de roteamento definido pelo usuário em que todo o tráfego de uma sub-rede é forçado para uma rede ou localização específica, como a rede local. O HDInsight __não__ dá suporte ao túnel forçado.

## <a id="hdinsight-ip"></a> Endereços IP obrigatórios

> [!IMPORTANT]
> Os serviços de integridade e gerenciamento do Azure devem ser capazes de se comunicar com o HDInsight. Se você usar grupos de segurança de rede ou rotas definidas pelo usuário, permita o tráfego de endereços IP para que esses serviços acessem o HDInsight.
>
> Se você não usar grupos de segurança de rede ou rotas definidas pelo usuário para controlar o tráfego, ignore esta seção.

Se você usar grupos de segurança de rede ou rotas definidas pelo usuário, deverá permitir o tráfego dos serviços de integridade e gerenciamento do Azure para acessar o HDInsight. Use as seguintes etapas para encontrar os endereços IP que devem ser permitidos:

1. Você sempre deve permitir o tráfego dos seguintes endereços IP:

    | Endereço IP | Porta permitida | Direção |
    | ---- | ----- | ----- |
    | 168.61.49.99 | 443 | Entrada |
    | 23.99.5.239 | 443 | Entrada |
    | 168.61.48.131 | 443 | Entrada |
    | 138.91.141.162 | 443 | Entrada |

2. Se o cluster HDInsight estiver em uma das seguintes regiões, você deverá permitir o tráfego dos endereços IP listados para a região:

    > [!IMPORTANT]
    > Se a região do Azure que você está usando não estiver listada, use apenas os quatro endereços IP da etapa 1.

    | País | Região | Endereços IP permitidos | Porta permitida | Direção |
    | ---- | ---- | ---- | ---- | ----- |
    | Ásia | Ásia Oriental | 23.102.235.122</br>52.175.38.134 | 443 | Entrada |
    | &nbsp; | Sudeste Asiático | 13.76.245.160</br>13.76.136.249 | 443 | Entrada |
    | Austrália | Leste da Austrália | 104.210.84.115</br>13.75.152.195 | 443 | Entrada |
    | &nbsp; | Sudeste da Austrália | 13.77.2.56</br>13.77.2.94 | 443 | Entrada |
    | Brasil | Sul do Brasil | 191.235.84.104</br>191.235.87.113 | 443 | Entrada |
    | Canadá | Leste do Canadá | 52.229.127.96</br>52.229.123.172 | 443 | Entrada |
    | &nbsp; | Canadá Central | 52.228.37.66</br>52.228.45.222 | 443 | Entrada |
    | China | Norte da China | 42.159.96.170</br>139.217.2.219 | 443 | Entrada |
    | &nbsp; | Leste da China | 42.159.198.178</br>42.159.234.157 | 443 | Entrada |
    | Europa | Norte da Europa | 52.164.210.96</br>13.74.153.132 | 443 | Entrada |
    | &nbsp; | Europa Ocidental| 52.166.243.90</br>52.174.36.244 | 443 | Entrada |
    | Alemanha | Alemanha Central | 51.4.146.68</br>51.4.146.80 | 443 | Entrada |
    | &nbsp; | Nordeste da Alemanha | 51.5.150.132</br>51.5.144.101 | 443 | Entrada |
    | Índia | Índia Central | 52.172.153.209</br>52.172.152.49 | 443 | Entrada |
    | Japão | Leste do Japão | 13.78.125.90</br>13.78.89.60 | 443 | Entrada |
    | &nbsp; | Oeste do Japão | 40.74.125.69</br>138.91.29.150 | 443 | Entrada |
    | Coreia | Coreia Central | 52.231.39.142</br>52.231.36.209 | 433 | Entrada |
    | &nbsp; | Sul da Coreia | 52.231.203.16</br>52.231.205.214 | 443 | Entrada
    | Reino Unido | Oeste do Reino Unido | 51.141.13.110</br>51.141.7.20 | 443 | Entrada |
    | &nbsp; | Sul do Reino Unido | 51.140.47.39</br>51.140.52.16 | 443 | Entrada |
    | Estados Unidos | Centro dos EUA | 13.67.223.215</br>40.86.83.253 | 443 | Entrada |
    | &nbsp; | Centro-Norte dos EUA | 157.56.8.38</br>157.55.213.99 | 443 | Entrada |
    | &nbsp; | Centro-Oeste dos EUA | 52.161.23.15</br>52.161.10.167 | 443 | Entrada |
    | &nbsp; | Oeste dos EUA 2 | 52.175.211.210</br>52.175.222.222 | 443 | Entrada |

    Para obter informações sobre os endereços IP a serem usados para o Azure Governamental, consulte o documento [Inteligência + Análise do Azure Governamental](https://docs.microsoft.com/azure/azure-government/documentation-government-services-intelligenceandanalytics).

3. Se você usar um servidor DNS personalizado com a rede virtual, também deverá permitir o acesso de __168.63.129.16__. Esse endereço é o resolvedor recursivo do Azure. Para obter mais informações, consulte o documento [Resolução de nomes para VMs e instâncias de função](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

Para obter mais informações, consulte a seção [Controlando o tráfego de rede](#networktraffic).

## <a id="hdinsight-ports"></a> Portas obrigatórias

Se você pretende usar um **firewall de solução de virtualização** de rede para proteger a rede virtual, permita o tráfego de saída nas seguintes portas:

* 53
* 443
* 1433
* 11000-11999
* 14000-14999

Para obter uma lista de portas de serviços específicos, consulte o documento [Portas usadas pelos serviços do Hadoop no HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Para obter mais informações sobre as regras de firewall para soluções de virtualização, consulte o documento [Cenário de solução de virtualização](../virtual-network/virtual-network-scenario-udr-gw-nva.md).

## <a id="hdinsight-nsg"></a>Exemplo: grupos de segurança de rede com o HDInsight

Os exemplos desta seção demonstram como criar regras do grupo de segurança de rede que permitem que o HDInsight se comunique com os serviços de gerenciamento do Azure. Antes de usar os exemplos, ajuste os endereços IP para que eles correspondam àqueles da região do Azure que estão sendo usados. Encontre essas informações na seção [HDInsight com grupos de segurança de rede e rotas definidas pelo usuário](#hdinsight-ip).

### <a name="azure-resource-management-template"></a>Modelo do Azure Resource Manager

O modelo de Gerenciamento de Recursos a seguir cria uma rede virtual que restringe o tráfego de entrada, mas permite o tráfego dos endereços IP necessários para o HDInsight. Esse modelo também cria um cluster HDInsight na rede virtual.

* [Implantar uma Rede Virtual protegida do Azure e um cluster HDInsight Hadoop](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

> [!IMPORTANT]
> Altere os endereços IP usados neste exemplo para que eles correspondam à região do Azure que você está usando. Encontre essas informações na seção [HDInsight com grupos de segurança de rede e rotas definidas pelo usuário](#hdinsight-ip).

### <a name="azure-powershell"></a>Azure PowerShell

Use o script do PowerShell a seguir para criar uma rede virtual que restrinja o tráfego de entrada e permita o tráfego dos endereços IP para a região Europa Setentrional.

> [!IMPORTANT]
> Altere os endereços IP usados neste exemplo para que eles correspondam à região do Azure que você está usando. Encontre essas informações na seção [HDInsight com grupos de segurança de rede e rotas definidas pelo usuário](#hdinsight-ip).

```powershell
$vnetName = "Replace with your virtual network name"
$resourceGroupName = "Replace with the resource group the virtual network is in"
$subnetName = "Replace with the name of the subnet that you plan to use for HDInsight"
# Get the Virtual Network object
$vnet = Get-AzureRmVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $resourceGroupName
# Get the region the Virtual network is in.
$location = $vnet.Location
# Get the subnet object
$subnet = $vnet.Subnets | Where-Object Name -eq $subnetName
# Create a Network Security Group.
# And add exemptions for the HDInsight health and management services.
$nsg = New-AzureRmNetworkSecurityGroup `
    -Name "hdisecure" `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -name "hdirule1" `
        -Description "HDI health and management address 52.164.210.96" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "52.164.210.96" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 300 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "hdirule2" `
        -Description "HDI health and management 13.74.153.132" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "13.74.153.132" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 301 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "hdirule2" `
        -Description "HDI health and management 168.61.49.99" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.49.99" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 302 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "hdirule2" `
        -Description "HDI health and management 23.99.5.239" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "23.99.5.239" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 303 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "hdirule2" `
        -Description "HDI health and management 168.61.48.131" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.48.131" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 304 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "hdirule2" `
        -Description "HDI health and management 138.91.141.162" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "138.91.141.162" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 305 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "blockeverything" `
        -Description "Block everything else" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "*" `
        -SourceAddressPrefix "Internet" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Deny `
        -Priority 500 `
        -Direction Inbound
# Set the changes to the security group
Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
# Apply the NSG to the subnet
Set-AzureRmVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name $subnetName `
    -AddressPrefix $subnet.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

> [!IMPORTANT]
> Este exemplo demonstra como adicionar regras para permitir o tráfego de entrada nos endereços IP necessários. Ele não contém uma regra para restringir o acesso de entrada de outras fontes.
>
> O exemplo a seguir demonstra como habilitar o acesso SSH por meio da Internet:
>
> ```powershell
> Add-AzureRmNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 306 -Direction Inbound
> ```

### <a name="azure-cli"></a>CLI do Azure

Use as etapas a seguir para criar uma rede virtual que restringe o tráfego de entrada, mas permite o tráfego dos endereços IP necessários para o HDInsight.

1. Use o seguinte comando para criar um novo grupo de segurança de rede chamado `hdisecure`. Substitua **RESOURCEGROUPNAME** pelo grupo de recursos que contém a Rede Virtual do Azure. Substitua **LOCATION** pelo local (região) em que o grupo foi criado.

    ```azurecli
    az network nsg create -g RESOURCEGROUPNAME -n hdisecure -l LOCATION
    ```

    Após a criação do grupo, você receberá informações sobre o novo grupo.

2. Use o seguinte para adicionar regras ao novo grupo de segurança de rede que permitem a comunicação de entrada na porta 443 por meio do serviço de integridade e gerenciamento do Azure HDInsight. Substitua **RESOURCEGROUPNAME** pelo nome do grupo de recursos que contém a Rede Virtual do Azure.

    > [!IMPORTANT]
    > Altere os endereços IP usados neste exemplo para que eles correspondam à região do Azure que você está usando. Encontre essas informações na seção [HDInsight com grupos de segurança de rede e rotas definidas pelo usuário](#hdinsight-ip).

    ```azurecli
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "52.164.210.96" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "13.74.153.132" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 305 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n block --protocol "*" --source-port-range "*" --destination-port-range "*" --source-address-prefix "Internet" --destination-address-prefix "VirtualNetwork" --access "Deny" --priority 500 --direction "Inbound"
    ```

3. Para recuperar o identificador exclusivo desse grupo de segurança de rede, use o seguinte comando:

    ```azurecli
    az network nsg show -g RESOURCEGROUPNAME -n hdisecure --query 'id'
    ```

    Esse comando retorna um valor semelhante ao texto a seguir:

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

    Use aspas duplas em torno da identificação no comando se você não obtiver os resultados esperados.

4. Use o comando a seguir para aplicar o grupo de segurança de rede a uma sub-rede. Substitua os valores __GUID__ e __RESOURCEGROUPNAME__ por aqueles retornados na etapa anterior. Substitua __VNETNAME__ e __SUBNETNAME__ pelo nome da rede virtual e da sub-rede que você deseja criar.

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUPNAME --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    Depois que esse comando for concluído, instale o HDInsight na Rede Virtual.

> [!IMPORTANT]
> Essas etapas apenas abrem o acesso ao serviço de integridade e gerenciamento do HDInsight na nuvem do Azure. Qualquer outro acesso ao cluster HDInsight de fora da Rede Virtual permanecerá bloqueado. Para habilitar o acesso de fora da rede virtual, você deve acrescentar regras de Grupo de Segurança de Rede adicionais.
>
> O exemplo a seguir demonstra como habilitar o acesso SSH por meio da Internet:
>
> ```azurecli
> az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 306 --direction "Inbound"
> ```

## <a id="example-dns"></a> Exemplo: configuração de DNS

### <a name="name-resolution-between-a-virtual-network-and-a-connected-on-premises-network"></a>Resolução de nomes entre uma rede virtual e uma rede local conectada

Este exemplo faz as seguintes suposições:

* Você tem uma Rede Virtual do Azure conectada a uma rede local usando um gateway de VPN.

* O servidor DNS personalizado na rede virtual executa o Linux ou Unix como o sistema operacional.

* O [Bind](https://www.isc.org/downloads/bind/) está instalado no servidor DNS personalizado.

No servidor DNS personalizado da rede virtual:

1. Use o Azure PowerShell ou a CLI do Azure para encontrar o sufixo DNS da rede virtual:

    ```powershell
    $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
    $NICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli-interactive
    read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
    az network nic list --resource-group $RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. No servidor DNS personalizado da rede virtual, use o seguinte texto como o conteúdo do arquivo `/etc/bind/named.conf.local`:

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # Azure recursive resolver
    };
    ```

    Substitua o valor `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` pelo sufixo DNS da rede virtual.

    Essa configuração encaminha todas as solicitações DNS do sufixo DNS da rede virtual para o resolvedor recursivo do Azure.

2. No servidor DNS personalizado da rede virtual, use o seguinte texto como o conteúdo do arquivo `/etc/bind/named.conf.options`:

    ```
    // Clients to accept requests from
    // TODO: Add the IP range of the joined network to this list
    acl goodclients {
        10.0.0.0/16; # IP address range of the virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            # All other requests are sent to the following
            forwarders {
                192.168.0.1; # Replace with the IP address of your on-premises DNS server
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```
    
    * Substitua o valor `10.0.0.0/16` pelo intervalo de endereços IP da rede virtual. Essa entrada permite endereços de solicitações de resolução de nomes dentro desse intervalo.

    * Adicione o intervalo de endereços IP da rede local à seção `acl goodclients { ... }`.  A entrada permite solicitações de resolução de nomes de recursos na rede local.
    
    * Substitua o valor `192.168.0.1` pelo endereço IP do servidor DNS local. Essa entrada encaminha todas as outras solicitações DNS para o servidor DNS local.

3. Para usar a configuração, reinicie o Bind. Por exemplo: `sudo service bind9 restart`.

4. Adicione um encaminhador condicional ao servidor DNS local. Configure o encaminhador condicional para enviar solicitações para o sufixo DNS da etapa 1 para o servidor DNS personalizado.

    > [!NOTE]
    > Consulte a documentação do software DNS para obter informações específicas sobre como adicionar um encaminhador condicional.

Depois de concluir essas etapas, você poderá se conectar aos recursos em uma das redes usando FQDNs (nomes de domínio totalmente qualificados). Agora você pode instalar o HDInsight na rede virtual.

### <a name="name-resolution-between-two-connected-virtual-networks"></a>Resolução de nomes entre duas redes virtuais conectadas

Este exemplo faz as seguintes suposições:

* Você tem duas Redes Virtuais do Azure conectadas usando um gateway de VPN ou um emparelhamento.

* O servidor DNS personalizado nas duas redes virtuais executa o Linux ou Unix como o sistema operacional.

* O [Bind](https://www.isc.org/downloads/bind/) está instalado nos servidores DNS personalizados.

1. Use o Azure PowerShell ou a CLI do Azure para encontrar o sufixo DNS das duas redes virtuais:

    ```powershell
    $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
    $NICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli-interactive
    read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
    az network nic list --resource-group $RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. Use o texto a seguir como o conteúdo do arquivo `/etc/bind/named.config.local` no servidor DNS personalizado. Faça essa alteração no servidor DNS personalizado em ambas as redes virtuais.

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The IP address of the DNS server in the other virtual network
    };
    ```

    Substitua o valor `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` pelo sufixo DNS da __outra__ rede virtual. Essa entrada encaminha solicitações do sufixo DNS da rede remota para o DNS personalizado na rede.

3. Nos servidores DNS personalizados das duas redes virtuais, use o seguinte texto como o conteúdo do arquivo `/etc/bind/named.conf.options`:

    ```
    // Clients to accept requests from
    acl goodclients {
        10.1.0.0/16; # The IP address range of one virtual network
        10.0.0.0/16; # The IP address range of the other virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            forwarders {
            168.63.129.16;   # Azure recursive resolver         
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```
    
    * Substitua os valores `10.0.0.0/16` e `10.1.0.0/16` pelos intervalos de endereços IP das redes virtuais. Essa entrada permite que os recursos em cada rede façam solicitações dos servidores DNS.

    Todas as solicitações não referentes aos sufixos DNS das redes virtuais (por exemplo, microsoft.com) são manipuladas pelo resolvedor recursivo do Azure.

4. Para usar a configuração, reinicie o Bind. Por exemplo, `sudo service bind9 restart` em ambos os servidores DNS.

Depois de concluir essas etapas, você poderá se conectar aos recursos na rede virtual usando FQDNs (nomes de domínio totalmente qualificados). Agora você pode instalar o HDInsight na rede virtual.

## <a name="next-steps"></a>Próximas etapas

* Para obter um exemplo de ponta a ponta de como configurar o HDInsight para se conectar a uma rede local, consulte [Conectar o HDInsight a uma rede local](./connect-on-premises-network.md).
* Para configurar clusters Hbase em redes virtuais do Azure, consulte [Criar clusters HBase no HDInsight na rede virtual do Azure](./hdinsight-hbase-provision-vnet.md).
* Para configurar a replicação geográfica do HBase, consulte [Configurar a replicação de cluster HBase em redes virtuais do Azure](./hdinsight-hbase-replication.md).
* Para obter mais informações sobre redes virtuais do Azure, consulte a [Visão geral da Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md).

* Para obter mais informações sobre os Grupos de Segurança de Rede, veja [Grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md).

* Para saber mais sobre rotas definidas pelo usuário, confira [Rotas definidas pelo usuário e encaminhamento IP](../virtual-network/virtual-networks-udr-overview.md).