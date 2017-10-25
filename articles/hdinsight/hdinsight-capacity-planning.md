---
title: Planejamento de capacidade de cluster no Azure HDInsight | Microsoft Docs
description: Como especificar um cluster HDInsight quanto a capacidade e desempenho.
services: hdinsight
documentationcenter: 
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: maxluk
ms.openlocfilehash: b4bdf3339e585a7b22a1945871f802854020fb94
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>Planejamento de capacidade para clusters HDInsight

Antes de implantar um cluster HDInsight, planeje a capacidade desejada do cluster, determinando o desempenho e a escala necessários. Este planejamento ajuda a otimizar os custos e a usabilidade. Algumas decisões sobre a capacidade do cluster não podem ser alteradas após a implantação. Se os parâmetros de desempenho se alterarem, um cluster poderá ser desmontado e criado novamente sem perder os dados armazenados.

As principais perguntas para o planejamento de capacidade são:

* Em qual região geográfica você deve implantar o cluster?
* Qual a quantidade de armazenamento que você precisa?
* Qual tipo de cluster você deve implantar?
* Qual o tamanho e o tipo de VM (máquina virtual) os nós de cluster devem usar?
* Quantos nós de trabalho seu cluster deve ter?

## <a name="choose-an-azure-region"></a>Escolher uma região do Azure

A região do Azure determina o local em que o cluster é provisionado fisicamente. Para minimizar a latência de leituras e gravações, o cluster deve ficar próximo aos seus dados.

O HDInsight está disponível em muitas regiões do Azure. Para localizar a região mais próxima, consulte a entrada *Linux HDInsight* em *Dados + Análise* em [Produtos do Azure disponíveis por região](https://azure.microsoft.com/regions/services/).

## <a name="choose-storage-location-and-size"></a>Escolher o tamanho e o local de armazenamento

### <a name="location-of-default-storage"></a>Local do armazenamento padrão

O armazenamento padrão, seja uma conta de Armazenamento do Azure ou um Azure Data Lake Store, deve estar no mesmo local que o cluster. O Armazenamento do Azure está disponível em todos os locais. O Data Lake Store está disponível em algumas regiões – consulte a disponibilidade atual do Data Lake Store em *Armazenamento*, em [Produtos do Azure disponíveis por região](https://azure.microsoft.com/regions/services/).

### <a name="location-of-existing-data"></a>Local dos dados existentes

Se você já tem uma conta de armazenamento ou o um Data Lake Store que contém seus dados e deseja usar este armazenamento como o armazenamento de padrão do seu cluster, é necessário implantar o cluster nesse mesmo local.

### <a name="storage-size"></a>Tamanho do armazenamento

Depois de implantar um cluster HDInsight, você poderá anexar mais contas do Armazenamento do Azure ou acessar outros Data Lake Stores. Todas as suas contas de armazenamento devem residir no mesmo local que o cluster. Um Data Lake Store pode estar em um local diferente, embora isso possa causar alguma latência na leitura/gravação de dados.

O Armazenamento do Azure tem alguns [limites de capacidade](../azure-subscription-service-limits.md#storage-limits), enquanto que o Data Lake Store é praticamente ilimitado.

Um cluster pode acessar uma combinação de contas de armazenamento diferentes. Exemplos comuns incluem:

* Quando é provável que a quantidade de dados exceda a capacidade de armazenamento de um único contêiner de armazenamento de blobs.
* Quando a taxa de acesso ao contêiner de blob pode exceder o máximo em que a limitação ocorre.
* Quando você quiser tornar os dados que você já carregou em um contêiner de blob disponíveis para o cluster.
* Quando você quiser isolar diferentes partes do armazenamento por questões de segurança ou para simplificar a administração.

Para um cluster de 48 nós, recomendamos de quatro a oito contas de armazenamento. Embora já possa haver um armazenamento total suficiente, cada conta de armazenamento fornece largura de banda de rede adicional para os nós de computação. Quando você tiver várias contas de armazenamento, use um nome aleatório para cada conta de armazenamento, sem um prefixo. A finalidade da nomeação aleatória é a redução da chance de afunilamentos (limitações) de armazenamento ou falhas de modo comum em todas as contas. Para obter melhor desempenho, use apenas um contêiner por conta de armazenamento.

## <a name="choose-a-cluster-type"></a>Escolher um tipo de cluster

O tipo de cluster determina a carga de trabalho que o cluster HDInsight está configurado para executar, como Hadoop, Storm, Kafka ou Spark. 
<!-- For a detailed description of the available cluster types, see [HDInsight Architecture](hdinsight-architecture.md). -->
Cada tipo de cluster tem uma topologia de implantação específica que inclui os requisitos de tamanho e número de nós.

## <a name="choose-the-vm-size-and-type"></a>Escolher tipo e tamanho da VM

Cada tipo de cluster tem um conjunto de tipos de nós e cada tipo de nó tem opções específicas quanto ao tamanho e o tipo de VM.

Para determinar o tamanho de cluster ideal para sua aplicação, você pode submeter a capacidade do cluster a submeter a benchmark e aumentar o tamanho conforme indicado. Por exemplo, você pode usar uma carga de trabalho simulada ou uma *consulta canary*. Com uma carga de trabalho simulada, você executa suas cargas de trabalho planejadas em clusters de tamanhos diferentes, aumentando gradualmente o tamanho, até que o desempenho desejado seja atingido. Uma consulta canary pode ser inserida periodicamente entre as outras consultas de produção a fim de mostrar se o cluster tem ou não recursos suficientes.

O tamanho e o tipo da VM são determinados pelo poder de processamento da CPU, pelo tamanho da RAM e pela latência da rede:

* CPU: o tamanho da VM determina o número de núcleos. Quanto mais núcleos, maior o grau de computação paralela que cada nó pode alcançar. Além disso, alguns tipos de VM tem núcleos mais rápidos.

* RAM: o tamanho da VM também determina a quantidade de RAM disponível na VM. Para cargas de trabalho que armazenam dados na memória para processamento em vez de ler no disco, verifique se os nós de trabalho têm memória suficiente que se ajuste aos dados.

* Rede: para a maioria dos tipos de cluster, os dados processados pelo cluster não estão no disco local mas, em vez disso, ficam em um serviço de armazenamento externo como o Data Lake Store ou o Armazenamento do Azure. Considere a largura de banda e taxa de transferência da rede entre a VM do nó e o serviço de armazenamento. Geralmente, a largura de banda da rede disponível para uma VM aumenta com tamanhos maiores. Para mais detalhes, consulte [Visão geral sobre tamanhos de VM](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

## <a name="choose-the-cluster-scale"></a>Escolher a escala do cluster

A escala do cluster é determinada pela quantidade de seus nós de VM. Para todos os tipos de cluster, há tipos de nós que têm uma escala específica e tipos de nós que dão suporte à expansão. Por exemplo, um cluster pode exigir exatamente três nós ZooKeeper ou dois nós de cabeçalho. Os nós de trabalho que fazem o processamento de dados de maneira distribuída podem se beneficiar de expansão através da adição de nós de trabalho.

Dependendo de seu tipo de cluster, aumentar o número de nós de trabalho gerará capacidade adicional de computação (por exemplo, mais núcleos), mas também poderá ocasionar aumento na quantidade total de memória necessária para que todo o cluster dê suporte ao armazenamento na memória de dados que estão sendo processados. Assim como acontece com a escolha do tamanho e tipo de VM, a seleção da escala correta do cluster é normalmente alcançada empiricamente, usando consultas canary ou cargas de trabalho simuladas.

Você pode aumentar o cluster para atender ao pico das demandas de carga e reduzi-lo novamente quando esses nós adicionais não forem mais necessários.
<!-- - see [Scaling - best practices](hdinsight-scaling-best-practices.md). -->

### <a name="cluster-lifecycle"></a>Ciclo de vida do cluster

Você é cobrado pelo tempo de vida do cluster. Se houver apenas momentos específicos em que você precisa de seu cluster em execução, você poderá criar clusters de sob demanda usando o Azure Data Factory.
<!-- [create on-demand clusters using Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md). -->
Você também pode criar scripts do PowerShell que provisionam e excluem o cluster e, em seguida, agendar esses scripts usando a [Automação do Azure](https://azure.microsoft.com/services/automation/).

> [!NOTE]
> Quando um cluster é excluído, seu metastore do Hive padrão também é excluído. Para manter o metastore para a próxima recriação de cluster, use um repositório de metadados externo, como o Banco de Dados do Azure ou o Oozie.
<!-- see [Using external metadata stores](hdinsight-using-external-metadata-stores.md). -->

### <a name="isolate-cluster-job-errors"></a>Isolar os erros de trabalho do cluster

Às vezes, os erros podem ocorrer devido à execução paralela de vários componentes de mapeamento e redução em um cluster de vários nós. Para ajudar a isolar o problema, experimente os testes distribuídos através da execução simultânea de vários trabalhos em um cluster de nó único e, em seguida, expanda essa abordagem para executar várias tarefas simultaneamente em clusters que contenham mais de um nó. Para criar um cluster HDInsight de nó único no Azure, use a opção *avançada*.

Você também pode instalar um ambiente de desenvolvimento de nó único em seu computador local e testar a solução nesse ambiente. A Hortonworks fornece um ambiente de desenvolvimento local de nó único para soluções baseadas em Hadoop que é útil para o desenvolvimento inicial, a prova de conceito e o teste. Para obter mais informações, consulte a [Área Restrita do Hortonworks](http://hortonworks.com/products/hortonworks-sandbox/).

Para identificar o problema em um cluster local de nó único, você pode executar novamente os trabalhos com falha e ajustar os dados de entrada ou usar conjuntos de dados menores. A forma como você executa esses trabalhos depende da plataforma e do tipo da aplicação.

## <a name="quotas"></a>Cotas

Depois de determinar o tamanho, a escala e o tipo da VM do cluster de destino, verifique os limites de capacidade de cota atuais da sua assinatura. Ao atingir um limite de cota, você não poderá implantar novos clusters ou aumentar os clusters existentes, adicionando mais nós de trabalho. O limite de cota alcançado com mais frequência é a cota de Núcleos de CPU que existe nos níveis de assinatura, região e de série de VM. Por exemplo, sua assinatura pode ter um limite total de 200 núcleos, com um limite de 30 núcleos em sua região e um limite de 30 núcleos em instâncias de VM. Você pode [entrar em contato com o suporte para solicitar um aumento de cota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

No entanto, há alguns limites de cota fixos, por exemplo, uma única assinatura do Azure pode ter, no máximo, 10 mil núcleos. Para detalhes sobre esses limites, confira [Assinatura do Azure e limites, cotas e restrições de serviços](https://docs.microsoft.com/azure/azure-subscription-service-limits#limits-and-the-azure-resource-manager).

## <a name="next-steps"></a>Próximas etapas

* [Configurar clusters no HDInsight com Hadoop, Spark, Kafka e muito mais](hdinsight-hadoop-provision-linux-clusters.md): saiba como instalar e configurar clusters no HDInsight com Hadoop, Spark, Kafka, Hive Interativo, HBase, R Server ou Storm.
* [Monitorar o desempenho do cluster](hdinsight-key-scenarios-to-monitor.md): saiba mais sobre os principais cenários a serem monitorados em seu cluster HDInsight e que podem afetar a capacidade do seu cluster.
