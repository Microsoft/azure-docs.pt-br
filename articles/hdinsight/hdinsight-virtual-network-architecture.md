---
title: Arquitetura de rede virtual do Azure HDInsight
description: Conheça os recursos disponíveis ao criar um cluster HDInsight em uma rede virtual do Azure.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: d5b8bdf5577642290cee7250e0f21f8a69dfd248
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98931151"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Arquitetura de rede virtual do Azure HDInsight

Este artigo explica os recursos que estão presentes quando você implanta um cluster HDInsight em uma rede virtual do Azure personalizada. Essas informações ajudarão você a conectar recursos locais ao cluster HDInsight no Azure. Para obter mais informações sobre redes virtuais do Azure, consulte [o que é a rede virtual do Azure?](../virtual-network/virtual-networks-overview.md).

## <a name="resource-types-in-azure-hdinsight-clusters"></a>Tipos de recursos em clusters do Azure HDInsight

Os clusters do Azure HDInsight têm diferentes tipos de máquinas virtuais ou nós. Cada tipo de nó desempenha uma função na operação do sistema. A tabela a seguir resume esses tipos de nó e suas funções no cluster.

| Tipo | Descrição |
| --- | --- |
| Nó de cabeçalho |  Para todos os tipos de cluster, exceto Apache Storm, os nós de cabeçalho hospedam os processos que gerenciam a execução do aplicativo distribuído. O nó de cabeçalho também é o nó no qual você pode realizar SSH e executar aplicativos que são coordenados para serem executados nos recursos do cluster. O número de nós de cabeçalho é corrigido em dois para todos os tipos de cluster. |
| Nó ZooKeeper | O Zookeeper coordena as tarefas entre os nós que estão fazendo o processamento de dados. Ele também faz eleição de líder do nó principal e controla qual nó principal está executando um serviço mestre específico. O número de nós ZooKeeper é corrigido em três. |
| Nó de trabalho | Representa os nós que dão suporte à funcionalidade de processamento de dados. Os nós de trabalho podem ser adicionados ou removidos do cluster para dimensionar a capacidade de computação e gerenciar os custos. |
| Nó de borda do servidor R | O nó de borda do servidor R representa o nó em que você pode realizar SSH e executar aplicativos que são coordenados para execução nos recursos do cluster. Um nó de borda não participa da análise de dados no cluster. Esse nó também hospeda o servidor do R Studio, permitindo que você execute o aplicativo R usando um navegador. |
| Nó de região | Para o tipo de cluster HBase, o nó de região (também conhecido como nó de dados) executa o servidor de região. Os servidores de região servem e gerenciam uma parte dos dados gerenciados pelo HBase. Nós de região podem ser adicionados ou removidos do cluster para dimensionar a capacidade de computação e gerenciar custos.|
| Nó Nimbus | Para o tipo de cluster Storm, o nó Nimbus fornece funcionalidade semelhante ao nó principal. O nó Nimbus atribui tarefas a outros nós em um cluster por meio de Zookeeper, que coordena a execução de topologias Storm. |
| Nó do supervisor | Para o tipo de cluster Storm, o nó supervisor executa as instruções fornecidas pelo nó Nimbus para fazer o processamento. |

## <a name="resource-naming-conventions"></a>Convenções de nomenclatura de recursos

Use nomes de domínio totalmente qualificados (FQDNs) ao endereçar nós em seu cluster. Você pode obter os FQDNs para vários tipos de nó no cluster usando a [API Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

Esses FQDNs estarão no formato `<node-type-prefix><instance-number>-<abbreviated-clustername>.<unique-identifier>.cx.internal.cloudapp.net` .

O `<node-type-prefix>` será *HN* para cabeçalho, *WN* para nós de trabalho e *Zn* para nós Zookeeper.

Se você precisar apenas do nome do host, use apenas a primeira parte do FQDN: `<node-type-prefix><instance-number>-<abbreviated-clustername>`

## <a name="basic-virtual-network-resources"></a>Recursos básicos da rede virtual

O diagrama a seguir mostra o posicionamento de nós do HDInsight e recursos de rede no Azure.

![Diagrama de entidades do HDInsight criadas na rede virtual personalizada do Azure](./media/hdinsight-virtual-network-architecture/hdinsight-vnet-diagram.png)

Os recursos padrão em uma rede virtual do Azure incluem os tipos de nó de cluster mencionados na tabela anterior. E dispositivos de rede que dão suporte à comunicação entre a rede virtual e as redes externas.

A tabela a seguir resume os nove nós de cluster criados quando o HDInsight é implantado em uma rede virtual do Azure personalizada.

| Tipo de recurso | Número presente | Detalhes |
| --- | --- | --- |
|Nó de cabeçalho | dois |    |
|Nó do ZooKeeper | três | |
|Nó de trabalho | dois | Esse número pode variar com base na configuração e no dimensionamento do cluster. Um mínimo de três nós de trabalho é necessário para Apache Kafka.  |
|Nó de gateway | dois | Os nós de gateway são máquinas virtuais do Azure que são criadas no Azure, mas não são visíveis em sua assinatura. Contate o suporte se você precisar reinicializar esses nós. |

Os seguintes recursos de rede presentes são criados automaticamente dentro da rede virtual usada com o HDInsight:

| Recurso de rede | Número presente | Detalhes |
| --- | --- | --- |
|Balanceador de carga | três | |
|Interfaces de Rede | alto | Esse valor se baseia em um cluster normal, em que cada nó tem sua própria interface de rede. As nove interfaces são para: dois nós de cabeça, três nós Zookeeper, dois nós de trabalho e dois nós de gateway mencionados na tabela anterior. |
|Endereços IP públicos | dois |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>Pontos de extremidade para se conectar ao HDInsight

Você pode acessar o cluster HDInsight de três maneiras:

- Um ponto de extremidade HTTPS fora da rede virtual em `CLUSTERNAME.azurehdinsight.net` .
- Um ponto de extremidade SSH para se conectar diretamente ao cabeçalho em `CLUSTERNAME-ssh.azurehdinsight.net` .
- Um ponto de extremidade HTTPS dentro da rede virtual `CLUSTERNAME-int.azurehdinsight.net` . Observe o " `-int` " nesta URL. Esse ponto de extremidade será resolvido para um IP privado nessa rede virtual e não poderá ser acessado pela Internet pública.

Esses três pontos de extremidade são atribuídos a cada um balanceador de carga.

Os endereços IP públicos também são fornecidos para os dois pontos de extremidade que permitem a conexão de fora da rede virtual.

1. Um IP público é atribuído ao balanceador de carga para o FQDN (nome de domínio totalmente qualificado) a ser usado ao conectar-se ao cluster da Internet `CLUSTERNAME.azurehdinsight.net` .
1. O segundo endereço IP público é usado para o nome de domínio somente SSH `CLUSTERNAME-ssh.azurehdinsight.net` .

## <a name="next-steps"></a>Próximas etapas

- [Proteger o tráfego de entrada para clusters HDInsight em uma rede virtual com ponto de extremidade privado](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
