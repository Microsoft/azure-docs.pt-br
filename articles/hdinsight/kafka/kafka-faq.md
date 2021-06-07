---
title: Perguntas frequentes sobre Apache Kafka no Azure HDInsight
description: Obtenha respostas para perguntas comuns sobre o Apache Kafka no Azure HDInsight, um serviço de nuvem do Hadoop gerenciado.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 851d7ceb44d2466ed31e26c1442fde9acea9fd22
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98939105"
---
# <a name="frequently-asked-questions-about-apache-kafka-in-azure-hdinsight"></a>Perguntas frequentes sobre Apache Kafka no Azure HDInsight

Este artigo aborda algumas perguntas comuns sobre como usar Apache Kafka no Azure HDInsight.

## <a name="what-kafka-versions-are-supported-by-hdinsight"></a>Quais versões do Kafka são compatíveis com o HDInsight?

Encontre mais informações sobre as versões de componente com suporte oficial do HDInsight em [quais são os componentes Apache Hadoop e as versões disponíveis com o HDInsight?](../hdinsight-component-versioning.md#supported-hdinsight-versions). Recomendamos sempre usar a versão mais recente para garantir o melhor desempenho possível e a experiência do usuário.

## <a name="what-resources-are-provided-in-an-hdinsight-kafka-cluster-and-what-resources-am-i-charged-for"></a>Quais recursos são fornecidos em um cluster HDInsight Kafka e em quais recursos sou cobrado?

Um cluster Kafka do HDInsight inclui os seguintes recursos:

* Nós de cabeçalho
* Nós do Zookeeper
* Nós do agente (trabalhador) 
* Managed Disks do Azure anexados aos nós do agente
* Nós de gateway

Todos esses recursos são cobrados com base no nosso [modelo de preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/), exceto nos nós de gateway. Você não é cobrado por nós de gateway.

Para obter uma descrição mais detalhada de vários tipos de nó, consulte [arquitetura de rede virtual do Azure HDInsight](../hdinsight-virtual-network-architecture.md). O preço é baseado no uso de nós por minuto. Os preços variam dependendo do tamanho do nó, do número de nós, do tipo de disco gerenciado usado e da região.

## <a name="do-apache-kafka-apis-work-with-hdinsight"></a>Apache Kafka APIs funcionam com o HDInsight?

Sim, o HDInsight usa APIs Kafka nativas. O código do aplicativo cliente não precisa ser alterado. Consulte [tutorial: usar o Apache Kafka produtor e as APIs de consumidor](./apache-kafka-producer-consumer-api.md) para ver como você pode usar APIs de produtor/consumidor baseadas em Java com seu cluster.

## <a name="can-i-change-cluster-configurations"></a>Posso alterar as configurações de cluster?

Sim, por meio do portal do Ambari. Cada componente no portal **tem uma seção Configurações,** que pode ser usada para alterar as configurações de componentes. Algumas alterações podem exigir reinicializações do agente.

## <a name="what-type-of-authentication-does-hdinsight-support-for-apache-kafka"></a>A qual tipo de autenticação o HDInsight dá suporte para Apache Kafka?

Usando o [Enterprise Security Package (ESP)](../domain-joined/apache-domain-joined-architecture.md), você pode obter segurança em nível de tópico para seus clusters Kafka. Consulte [tutorial: configurar políticas de Apache Kafka no HDInsight com Enterprise Security Package (versão prévia)](../domain-joined/apache-domain-joined-run-kafka.md)para obter mais informações.

## <a name="is-my-data-encrypted-can-i-use-my-own-keys"></a>Meus dados estão criptografados? Posso usar minhas próprias chaves?

Todas as mensagens Kafka nos discos gerenciados são criptografadas com o [Azure criptografia do serviço de armazenamento (SSE)](../../storage/common/storage-service-encryption.md). Dados em trânsito (por exemplo, dados sendo transmitidos de clientes para agentes e o contrário) não são criptografados por padrão. É possível criptografar esse tráfego [Configurando o TLS por conta própria](./apache-kafka-ssl-encryption-authentication.md). Além disso, o HDInsight permite que você gerencie suas próprias chaves para criptografar os dados em repouso. Consulte [criptografia de disco de chave gerenciada pelo cliente](../disk-encryption.md)para obter mais informações.

## <a name="how-do-i-connect-clients-to-my-cluster"></a>Como fazer conectar clientes ao meu cluster?

Para que os clientes do Kafka se comuniquem com os agentes do Kafka, eles devem ser capazes de alcançar os agentes pela rede. Para clusters HDInsight, a rede virtual (VNet) é o limite de segurança. Portanto, a maneira mais fácil de conectar clientes ao cluster HDInsight é criar clientes na mesma VNet que o cluster. Outros cenários incluem:

* Conectar clientes em uma VNet do Azure diferente – emparelhar a VNet do cluster e a VNet do cliente e configurar o cluster para [publicidade de IP](apache-kafka-connect-vpn-gateway.md#configure-kafka-for-ip-advertising). Ao usar o anúncio de IP, os clientes do Kafka devem usar endereços IP do agente para se conectarem aos agentes, em vez de FQDNs (nomes de domínio totalmente qualificados).

* Conectar clientes locais – usando uma rede VPN e Configurando servidores DNS personalizados, conforme descrito em [planejar uma rede virtual para o Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md).

* Criando um ponto de extremidade público para seu serviço Kafka – se os requisitos de segurança da sua empresa permitirem, você poderá implantar um ponto de extremidade público para seus agentes do Kafka, ou um ponta de extremidade REST autogerenciada de código aberto com um local de ponto de extremidade público.

## <a name="can-i-add-more-disk-space-on-an-existing-cluster"></a>Posso adicionar mais espaço em disco em um cluster existente?

Para aumentar a quantidade de espaço disponível para mensagens Kafka, você pode aumentar o número de nós. Atualmente, não há suporte para a adição de mais discos a um cluster existente.

## <a name="can-a-kafka-cluster-work-with-databricks"></a>Um cluster Kafka pode funcionar com o databricks? 

Sim, os clusters Kafka podem trabalhar com o databricks desde que estejam na mesma VNet. Para usar um cluster Kafka com o databricks, crie uma VNet com um cluster Kafka do HDInsight e, em seguida, especifique essa VNet ao criar seu espaço de trabalho do databricks e use a injeção de VNet. Para obter mais informações, consulte [Implantar o Azure Databricks em sua Rede Virtual do Azure (Injeção de VNet)](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject). Você precisará fornecer os nomes do agente de inicialização do cluster Kafka ao criar o espaço de trabalho do databricks. Para obter informações sobre como recuperar os nomes do Kafka Broker, consulte [obter as informações do host do Apache Zookeeper e do agente](./apache-kafka-get-started.md#getkafkainfo).

## <a name="how-can-i-have-maximum-data-durability"></a>Como posso ter a durabilidade máxima dos dados?

A durabilidade dos dados permite alcançar o menor risco de perda de mensagens. Para atingir a durabilidade máxima dos dados, recomendamos as seguintes configurações:

* Use um fator de replicação mínimo de 3 na maioria das regiões
* Use um fator de replicação mínimo de 4 em regiões com apenas dois domínios de falha
* desabilitar eleições de líder não limpas
* definir **min. insincronia. réplicas** como 2 ou mais-isso altera o número de réplicas que devem ser completamente sincronizadas com o líder antes que uma gravação possa continuar
* definir a propriedade **ACKs** como **All** -essa propriedade requer que todas as réplicas reconheçam todas as mensagens

Configurar o Kafka para maior consistência de dados afeta a disponibilidade de agentes para produzir solicitações.

## <a name="can-i-replicate-my-data-to-multiple-clusters"></a>Posso replicar meus dados para vários clusters?

Sim, os dados podem ser replicados para vários clusters usando Kafka MirrorMaker. Veja detalhes sobre como configurar MirrorMaker pode ser encontrado em [Tópicos de Apache Kafka de espelho](apache-kafka-mirroring.md). Além disso, existem outras tecnologias de software livre e fornecedores autogerenciados que podem ajudar a atingir a replicação em vários clusters, como o [Brooklin](https://github.com/linkedin/Brooklin/).

## <a name="can-i-upgrade-my-cluster-how-should-i-upgrade-my-cluster"></a>Posso atualizar meu cluster? Como devo atualizar meu cluster?

Atualmente, não há suporte para atualizações de versão de cluster in-loco. Para atualizar o cluster para uma versão mais alta do Kafka, crie um novo cluster com a versão que você deseja e migre seus clientes do Kafka para usar o novo cluster.

## <a name="how-do-i-monitor-my-kafka-cluster"></a>Como fazer monitorar meu cluster do Kafka?

Use o Azure monitor para analisar os [logs do Kafka](./apache-kafka-log-analytics-operations-management.md).

## <a name="next-steps"></a>Próximas etapas

* [Configurar a criptografia e a autenticação TLS para Apache Kafka no Azure HDInsight](./apache-kafka-ssl-encryption-authentication.md)
* [Usar o MirrorMaker para replicar tópicos do Apache Kafka com Kafka no HDInsight](./apache-kafka-mirroring.md)