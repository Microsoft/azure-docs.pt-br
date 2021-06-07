---
title: Controle do tráfego de rede no Azure HDInsight
description: Aprenda técnicas para controlar o tráfego de entrada e de saída para clusters do Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: 66b078e8dfa1daa100978f04283e9bba7158bddf
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867025"
---
# <a name="control-network-traffic-in-azure-hdinsight"></a>Controle do tráfego de rede no Azure HDInsight

O tráfego de rede em Redes Virtuais do Azure pode ser controlado com os seguintes métodos:

* Os **NSGs** (grupos de segurança de rede) permitem filtrar o tráfego de entrada e de saída para a rede. Para obter mais informações, consulte o documento [Filtrar o tráfego de rede com grupos de segurança de rede](../virtual-network/network-security-groups-overview.md).

* **As soluções de virtualização de rede** (NVA) podem ser usadas apenas com o tráfego de saída. As NVAs replicam a funcionalidade de dispositivos, como firewalls e roteadores. Para obter mais informações, consulte o documento [Dispositivos de rede](https://azure.microsoft.com/solutions/network-appliances).

Como um serviço gerenciado, o HDInsight exige acesso irrestrito para a integridade do HDInsight e gerenciamento de serviços para tráfego de entrada e saída da rede virtual. Quando usa NSGs, você deve garantir que esses serviços ainda possam se comunicar com o cluster HDInsight.

:::image type="content" source="./media/control-network-traffic/hdinsight-vnet-diagram.png" alt-text="Diagrama de entidades do HDInsight criadas na rede virtual personalizada do Azure" border="false":::

## <a name="hdinsight-with-network-security-groups"></a>HDInsight com grupos de segurança de rede

Se você pretende usar **grupos de segurança de rede** para controlar o tráfego de rede, execute as seguintes ações antes de instalar o HDInsight:

1. Identifique a região do Azure que você pretende usar para o HDInsight.

2. Identifique as marcas de serviço exigidas pelo HDInsight para a sua região. Há várias maneiras de obter essas marcas de serviço:
    1. Consulte a lista de marcas de serviço publicadas em [marcas de serviço do NSG (grupo de segurança de rede) para o Azure HDInsight](hdinsight-service-tags.md). 
    2. Se sua região não estiver presente na lista, use a [API de descoberta de marca de serviço](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) para localizar uma marca de serviço para sua região.
    3. Se não for possível usar a API, baixe o [arquivo JSON da marca de serviço](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) e procure a região desejada.


3. Crie ou modifique os grupos de segurança de rede da sub-rede na qual você pretende instalar o HDInsight.

    * __Grupos de segurança de rede__: permita o tráfego de __entrada__ na porta __443__ dos endereços IP. Isso garantirá que os serviços de gerenciamento do HDInsight podem acessar o cluster de fora da rede virtual. Para clusters habilitados do __proxy REST do Kafka__, permita tráfego de __entrada__ na porta __9400__. Dessa maneira, o servidor proxy REST do Kafka fica acessível.

Para saber mais sobre os grupos de segurança de rede, confira [visão geral dos grupos de segurança de rede](../virtual-network/network-security-groups-overview.md).

## <a name="controlling-outbound-traffic-from-hdinsight-clusters"></a>Controle do tráfego de saída de clusters HDInsight

Para obter mais informações sobre como controlar o tráfego de saída de clusters HDInsight, veja [Configuração de restrição de tráfego de rede de saída para clusters do Azure HDInsight](hdinsight-restrict-outbound-traffic.md).

### <a name="forced-tunneling-to-on-premises"></a>Túnel forçado para localização local

O túnel forçado é uma configuração de roteamento definida pelo usuário em que todo o tráfego de uma sub-rede é forçado para uma rede ou localização específica, como a rede local ou Firewall. O túnel forçado de todas as transferências de dados de volta para o local _não_ é recomendado devido a grandes volumes de transferência de dados e potencial impacto no desempenho.

Os clientes que estão interessados em configurar o túnel forçado devem usar [metastores personalizados](./hdinsight-use-external-metadata-stores.md) e configurar a conectividade apropriada a partir da sub-rede do cluster ou da rede local para esses metastores personalizados.

Para ver um exemplo de configuração do UDR com o Firewall do Azure, veja [Configuração de restrição de tráfego de rede de saída para clusters do Azure HDInsight](hdinsight-restrict-outbound-traffic.md).

## <a name="required-ports"></a>Portas obrigatórias

Se você pretende usar um **firewall** e acessar o cluster de fora em determinadas portas, será preciso permitir o tráfego nas portas necessárias para o seu cenário. Por padrão, nenhuma filtragem especial de portas é necessária, contanto que o tráfego de gerenciamento do Azure explicado na seção anterior tenha permissão para alcançar o cluster na porta 443.

Para obter uma lista de portas para serviços específicos, consulte o documento [Portas usadas pelos serviços do Apache Hadoop no HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Para obter mais informações sobre as regras de firewall para soluções de virtualização, consulte o documento [Cenário de solução de virtualização](../virtual-network/virtual-network-scenario-udr-gw-nva.md).

## <a name="next-steps"></a>Próximas etapas

* Para obter exemplos de código e exemplos de criação de Redes Virtuais do Azure, veja [Criação de redes virtuais para clusters do Azure HDInsight](hdinsight-create-virtual-network.md).
* Para obter um exemplo de ponta a ponta de como configurar o HDInsight para se conectar a uma rede local, consulte [Conectar o HDInsight a uma rede local](./connect-on-premises-network.md).
* Para obter mais informações sobre redes virtuais do Azure, consulte a [Visão geral da Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md).
* Para obter mais informações sobre os Grupos de Segurança de Rede, veja [Grupos de segurança de rede](../virtual-network/network-security-groups-overview.md).
* Para obter mais informações sobre as rotas definidas pelo usuário, confira [Rotas definidas pelo usuário e encaminhamento IP](../virtual-network/virtual-networks-udr-overview.md).
* Para obter mais informações sobre redes virtuais, veja [Planejamento de redes virtuais para o HDInsight](./hdinsight-plan-virtual-network-deployment.md).