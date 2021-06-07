---
title: Planejar uma rede virtual para o Azure HDInsight
description: Saiba como planejar uma implantação de rede virtual do Azure para conectar o HDInsight a outros recursos de nuvem ou recursos em seu datacenter.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 01/12/2021
ms.openlocfilehash: fe974a96b7f349c9d525d0cd0bb01a83ace57a4f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98939284"
---
# <a name="plan-a-virtual-network-for-azure-hdinsight"></a>Planejar uma rede virtual para o Azure HDInsight

Este artigo fornece informações básicas sobre como usar [redes virtuais do Azure](../virtual-network/virtual-networks-overview.md) (VNets) com o Azure HDInsight. Ele também aborda decisões de design e implementação que devem ser feitas antes que você possa implementar uma rede virtual para o cluster HDInsight. Depois que a fase de planejamento for concluída, você poderá continuar [criando redes virtuais para clusters do Azure HDInsight](hdinsight-create-virtual-network.md). Para obter mais informações sobre os endereços IP de gerenciamento do HDInsight que são necessários para configurar corretamente NSGs (grupos de segurança de rede) e rotas definidas pelo usuário, consulte [endereços IP de gerenciamento do hdinsight](hdinsight-management-ip-addresses.md).

O uso de uma Rede Virtual do Azure permite os seguintes cenários:

* Conectar-se ao HDInsight diretamente em uma rede local.
* Conectar o HDInsight a armazenamentos de dados em uma Rede virtual do Azure.
* Acessando diretamente os serviços Apache Hadoop que não estão disponíveis publicamente pela Internet. Por exemplo,APIs Apache Kafka ou a API Java do Apache HBase.

> [!IMPORTANT]
> Criar um cluster HDInsight em uma VNET criará vários recursos de rede, como NICs e balanceadores de carga. Não **exclua** esses recursos de rede, pois eles são necessários para que o cluster funcione corretamente com a VNET.
>
> Após 28 de fevereiro de 2019, os recursos de rede (como NICs, LBs, etc.) para novos clusters HDInsight criados em uma VNET serão provisionados no mesmo grupo de recursos de cluster HDInsight. Anteriormente, esses recursos eram provisionados no grupo de recursos de VNET. Não há nenhuma alteração nos clusters em execução atuais e nos clusters criados sem uma VNET.

## <a name="planning"></a>Planejamento

Estas são as perguntas que você deve responder ao planejar a instalação do HDInsight em uma rede virtual:

* Você precisa instalar o HDInsight em uma rede virtual existente? Ou você está criando uma nova rede?

    Se você estiver usando uma rede virtual existente, talvez seja necessário modificar a configuração de rede para poder instalar o HDInsight. Para obter mais informações, consulte a seção [Adicionar o HDInsight a uma rede virtual existente](#existingvnet).

* Você deseja conectar a rede virtual que contém o HDInsight a outra rede virtual ou à rede local?

    Para trabalhar com recursos em redes com facilidade, talvez você precise criar um DNS personalizado e configurar o encaminhamento de DNS. Para obter mais informações, consulte a seção [Conectando várias redes](#multinet).

* Você deseja restringir/redirecionar o tráfego de entrada ou de saída para o HDInsight?

    O HDInsight deve ter comunicação irrestrita com endereços IP específicos no data center do Azure. Também há diversas portas que devem receber permissão por meio de firewalls para a comunicação do cliente. Para obter mais informações, consulte [controlar o tráfego de rede](./control-network-traffic.md).

## <a name="add-hdinsight-to-an-existing-virtual-network"></a><a id="existingvnet"></a>Adicionar o HDInsight uma rede virtual existente

Use as etapas descritas nesta seção para descobrir como adicionar um novo HDInsight a uma Rede Virtual do Azure existente.

> [!NOTE]  
> - Não é possível adicionar um cluster HDInsight existente a uma rede virtual.
> - A VNET e o cluster que está sendo criado devem estar na mesma assinatura.

1. Você está usando um modelo de implantação clássico ou do Resource Manager para a rede virtual?

    O HDInsight 3.4 e posterior exige uma rede virtual do Resource Manager. Versões anteriores do HDInsight exigiam uma rede virtual clássica.

    Se a rede existente for uma rede virtual clássica, é necessário criar uma rede virtual do Resource Manager e, em seguida, conectar as duas. [Conectando VNets clássicas a novas VNets](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

    Depois de ingressar, o HDInsight instalado na rede do Resource Manager pode interagir com os recursos da rede clássica.

2. Você usa grupos de segurança de rede, rotas definidas pelo usuário ou Soluções de Virtualização de Rede para restringir o tráfego para dentro ou fora da rede virtual?

    Como um serviço gerenciado, o HDInsight exige acesso irrestrito a vários endereços IP no data center do Azure. Para permitir a comunicação com esses endereços IP, atualize os grupos de segurança de rede ou as rotas definidas pelo usuário existentes.

    O HDInsight hospeda vários serviços, que usam uma variedade de portas. Não bloqueie o tráfego para essas portas. Para obter uma lista de portas para permissão por meio de firewalls de solução de virtualização, confira a seção Segurança.

    Para encontrar a configuração de segurança existente, use os seguintes comandos do Azure PowerShell ou da CLI do Azure:

    * Grupos de segurança de rede

        Substitua `RESOURCEGROUP` pelo nome do grupo de recursos que contém a rede virtual e, em seguida, insira o comando:

        ```powershell
        Get-AzNetworkSecurityGroup -ResourceGroupName  "RESOURCEGROUP"
        ```

        ```azurecli
        az network nsg list --resource-group RESOURCEGROUP
        ```

        Para obter mais informações, consulte o documento [Solução de problemas dos grupos de segurança de rede](../virtual-network/diagnose-network-traffic-filter-problem.md).

        > [!IMPORTANT]  
        > As regras do grupo de segurança de rede são aplicadas em ordem, com base na prioridade da regra. A primeira regra que corresponde ao padrão de tráfego é aplicada e nenhuma outra é aplicada ao tráfego. Ordene as regras da mais permissiva para a menos permissiva. Para obter mais informações, consulte o documento [Filtrar o tráfego de rede com grupos de segurança de rede](../virtual-network/network-security-groups-overview.md).

    * Rotas definidas pelo usuário

        Substitua `RESOURCEGROUP` pelo nome do grupo de recursos que contém a rede virtual e, em seguida, insira o comando:

        ```powershell
        Get-AzRouteTable -ResourceGroupName "RESOURCEGROUP"
        ```

        ```azurecli
        az network route-table list --resource-group RESOURCEGROUP
        ```

        Para obter mais informações, consulte o documento [Solução de problemas de rotas](../virtual-network/diagnose-network-routing-problem.md).

3. Crie um cluster HDInsight e selecione a Rede Virtual do Azure durante a configuração. Use as etapas nos documentos a seguir para entender o processo de criação de cluster:

    * [Criar o HDInsight usando o portal do Azure](hdinsight-hadoop-create-linux-clusters-portal.md)
    * [Criar o HDInsight usando o Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
    * [Criar o HDInsight usando a CLI Clássica do Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [Criar o HDInsight usando um modelo do Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

   > [!IMPORTANT]  
   > Adicionar HDInsight a uma rede virtual é uma etapa de configuração opcional. Verifique se você selecionou a rede virtual ao configurar o cluster.

## <a name="connecting-multiple-networks"></a><a id="multinet"></a>Conectando várias redes

O maior desafio em uma configuração de várias redes é a resolução de nomes entre as redes.

O Azure fornece a resolução de nomes para os serviços do Azure instalados em uma rede virtual do Azure. Essa resolução de nomes interna permite que o HDInsight se conecte aos seguintes recursos usando um FQDN (nome de domínio totalmente qualificado):

* Qualquer recurso que está disponível na Internet. Por exemplo, microsoft.com, windowsupdate.com.

* Qualquer recurso que está na mesma Rede Virtual do Azure, usando o __nome DNS interno__ do recurso. Por exemplo, ao usar a resolução de nome padrão, os seguintes são exemplos de nomes DNS internos atribuídos aos nós de trabalho do HDInsight:

  * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net
  * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net

    Esses dois nós podem se comunicar diretamente um com o outro e com outros nós no HDInsight, usando nomes DNS internos.

A resolução de nomes padrão __não__ permite que o HDInsight resolva os nomes de recursos em redes ingressadas na rede virtual. Por exemplo, é comum unir sua rede local à rede virtual. Com apenas a resolução de nomes padrão, o HDInsight não pode acessar recursos na rede local por nome. O oposto também é verdadeiro, os recursos em sua rede local não podem acessar recursos na rede virtual por nome.

> [!WARNING]  
> É necessário criar o servidor DNS personalizado e configurar a rede virtual para usá-lo antes de criar o cluster HDInsight.

Para permitir a resolução de nomes entre a rede virtual e os recursos em redes ingressadas, execute as seguintes ações:

1. Crie um servidor DNS personalizado na Rede Virtual do Azure na qual você pretende instalar o HDInsight.

2. Configure a rede virtual para usar o servidor DNS personalizado.

3. Encontre o sufixo DNS atribuído pelo Azure à rede virtual. Esse valor é semelhante a `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net`. Para obter informações sobre como encontrar o sufixo DNS, consulte a seção [Exemplo: DNS personalizado](hdinsight-create-virtual-network.md#example-dns).

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

     Para obter um exemplo de cada configuração, consulte a seção [Exemplo: DNS personalizado](hdinsight-create-virtual-network.md#example-dns).

Para obter mais informações, consulte o documento [Resolução de nomes para VMs e instâncias de função](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="directly-connect-to-apache-hadoop-services"></a>Conectar-se diretamente aos serviços do Apache Hadoop

Você pode se conectar ao cluster em `https://CLUSTERNAME.azurehdinsight.net`. Esse endereço usa um IP público, que pode não estar acessível se você tiver usado os NSGs para restringir o tráfego de entrada da Internet. Além disso, quando você implanta o cluster em uma rede virtual você pode acessá-lo usando o ponto de extremidade privado `https://CLUSTERNAME-int.azurehdinsight.net`. Esse ponto de extremidade é resolvido para um endereço IP privado dentro da VNet para acesso ao cluster.

Para se conectar ao Apache Ambari e outras páginas da Web por meio da rede virtual, use as seguintes etapas:

1. Para descobrir os FQDNs (nomes de domínio totalmente qualificados) internos dos nós do cluster HDInsight, use um dos seguintes métodos:

    Substitua `RESOURCEGROUP` pelo nome do grupo de recursos que contém a rede virtual e, em seguida, insira o comando:

    ```powershell
    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP" | where-object {$_.Name -like "*node*"}

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
    az network nic list --resource-group RESOURCEGROUP --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    Na lista de nós retornados, encontre o FQDN dos nós de cabeçalho e use-os para se conectar ao Ambari e a outros serviços Web. Por exemplo, use `http://<headnode-fqdn>:8080` para acessar o Ambari.

    > [!IMPORTANT]  
    > Alguns serviços hospedados em nós de cabeçalho ficam ativos apenas em um nó por vez. Se você tentar acessar um serviço em um nó de cabeçalho e ele retornar um erro 404, mude para o outro nó de cabeçalho.

2. Para determinar o nó e a porta nos quais um serviço está disponível, consulte o documento [Portas usadas pelos serviços do Hadoop no HDInsight](./hdinsight-hadoop-port-settings-for-services.md).

## <a name="load-balancing"></a>Balanceamento de carga

Quando você cria um cluster HDInsight, um balanceador de carga também é criado. O tipo desse balanceador de carga está no [nível de SKU básico](../load-balancer/skus.md), que tem determinadas restrições. Uma dessas restrições é que, se você tiver duas redes virtuais em regiões diferentes, não poderá se conectar aos balanceadores de carga básicos. Consulte [perguntas frequentes sobre redes virtuais: restrições em emparelhamento vnet global](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers), para obter mais informações.

## <a name="next-steps"></a>Próximas etapas

* Para obter exemplos de código e exemplos de criação de Redes Virtuais do Azure, veja [Criação de redes virtuais para clusters do Azure HDInsight](hdinsight-create-virtual-network.md).
* Para obter um exemplo de ponta a ponta de como configurar o HDInsight para se conectar a uma rede local, consulte [Conectar o HDInsight a uma rede local](./connect-on-premises-network.md).
* Para obter mais informações sobre redes virtuais do Azure, consulte a [Visão geral da Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md).
* Para obter mais informações sobre os Grupos de Segurança de Rede, veja [Grupos de segurança de rede](../virtual-network/network-security-groups-overview.md).
* Para obter mais informações sobre as rotas definidas pelo usuário, confira [Rotas definidas pelo usuário e encaminhamento IP](../virtual-network/virtual-networks-udr-overview.md).
* Para obter mais informações sobre como controlar o tráfego, incluindo a integração de firewall, consulte [controlar o tráfego de rede](./control-network-traffic.md).
