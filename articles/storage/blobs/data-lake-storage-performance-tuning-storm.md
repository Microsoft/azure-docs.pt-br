---
title: 'Ajustar o desempenho: o Storm, o HDInsight & Azure Data Lake Storage Gen2 | Microsoft Docs'
description: Entenda as diretrizes para ajustar o desempenho de uma topologia do Azure Storm em um cluster Azure HDInsight e Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 4db85357ee970d13d6b4fcce195cae66932bed18
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95912783"
---
# <a name="tune-performance-storm-hdinsight--azure-data-lake-storage-gen2"></a>Ajustar o desempenho: o Storm, o HDInsight & Azure Data Lake Storage Gen2

Entenda os fatores que devem ser considerados ao ajustar o desempenho de uma topologia Storm do Azure. Por exemplo, é importante compreender as características do trabalho feito pelos spouts e bolts (se o trabalho está com uso intensivo de memória ou de E/S). Este artigo abrange uma gama de diretrizes de ajuste de desempenho, incluindo a solução de problemas comuns.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta de Azure data Lake Storage Gen2**. Para obter instruções sobre como criar uma, consulte [início rápido: criar uma conta de armazenamento para análise](../common/storage-account-create.md).
* **Cluster do Azure HDInsight** com acesso a uma conta do Azure Data Lake Storage Gen2. Consulte [Usar o Azure Data Lake Storage Gen2 com clusters de HDInsight do Azure](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md). Certifique-se de habilitar a área de trabalho remota para o cluster.
* **Executando um cluster do Storm no Data Lake Storage Gen2**. Para obter mais informações, consulte [Storm no HDInsight](../../hdinsight/storm/apache-storm-overview.md).
* **Diretrizes de ajuste de desempenho no Data Lake Storage Gen2**.  Para obter os conceitos gerais de desempenho, confira [Diretrizes de ajuste de desempenho do Data Lake Storage Gen2](data-lake-storage-performance-tuning-guidance.md).   

## <a name="tune-the-parallelism-of-the-topology"></a>Ajuste do paralelismo da topologia

Você poderá melhorar o desempenho aumentando a simultaneidade de E/S para e do Data Lake Storage Gen2. Uma topologia Storm tem um conjunto de configurações que determinam o paralelismo:
* Número de processos de trabalho (os trabalhos são distribuídos uniformemente entre as VMs).
* Número de instâncias de executor de spout.
* Número de instâncias de bolt executor.
* Número de tarefas de spout.
* Número de tarefas de bolt.

Por exemplo, em um cluster com 4 VMs e 4 processos de trabalho, 32 executores de spout e 32 tarefas de spout, 256 executores de bolt e 512 tarefas de bolt, considere o seguinte:

Cada supervisor, que é um nó de trabalho, tem um único processo de trabalho de máquina virtual Java (JVM). Esse processo JVM gerencia 4 threads de spout e 64 threads de bolt. Em cada thread, as tarefas são executadas sequencialmente. Com a configuração anterior, cada thread de spout tem 1 tarefa, e cada thread de bolt tem 2 tarefas.

No Storm, aqui estão os diversos componentes envolvidos e como eles afetam o nível de paralelismo que você tem:
* O nó principal (chamado Nimbus no Storm) é usado para enviar e gerenciar trabalhos. Esses nós não têm impacto sobre o grau de paralelismo.
* Nós de supervisor. No HDInsight, isso corresponde a uma VM do Azure de nó de trabalho.
* As tarefas de trabalho são processos Storm em execução nas VMs. Cada tarefa de trabalho corresponde a uma instância da JVM. O Storm distribui o número de processos de trabalho que você especificar para os nós de trabalho tão uniformemente quanto possível.
* Instâncias de executores de bolt e spout. Cada instância de executor corresponde a um thread em execução dentro dos trabalhos (JVMs).
* Tarefas do Storm. Essas são tarefas lógicas que cada um desses threads executa. Isso não altera o nível de paralelismo, então você deve avaliar se precisa de várias tarefas por executor ou não.

### <a name="get-the-best-performance-from-data-lake-storage-gen2"></a>Obter o melhor desempenho do Data Lake Storage Gen2

Ao trabalhar com o Data Lake Storage Gen2, você terá o melhor desempenho se fizer o seguinte:
* una seus pequenos acréscimos em tamanhos maiores.
* Faça o máximo possível de solicitações simultâneas. Como cada thread de bolt está fazendo leituras de bloqueio, é recomendável ter entre 8 e 12 threads por núcleo. Isso mantém o NIC e a CPU bem utilizados. Uma VM maior permite mais solicitações simultâneas.  

### <a name="example-topology"></a>Exemplo de topologia

Suponhamos que você tenha um cluster de 8 nós de trabalho com a VM D13v2 do Azure. Uma VM tem 8 núcleos, portanto, entre os 8 nós de trabalho, você tem 64 núcleos no total.

Digamos que façamos oito threads de bolt por núcleo. Dados 64 núcleos, isso significa que desejamos um total de 512 instâncias de executor de bolt (ou seja, threads). Nesse caso, digamos que podemos começar com uma JVM por VM e usar principalmente a simultaneidade de threads na JVM para obter a simultaneidade. Isso significa que precisamos de oito tarefas de trabalho (uma por VM do Azure) e 512 executores de bolt. Dada essa configuração, o Storm tenta distribuir os trabalhos uniformemente entre os nós de trabalho (também conhecidos como nós de supervisor), dando a cada nó de trabalho 1 JVM. Dentro dos supervisores, o Storm tenta distribuir os executores uniformemente entre supervisores, dando a cada supervisor (ou seja, JVM) 8 threads.

## <a name="tune-additional-parameters"></a>Ajuste de parâmetros adicionais
Depois que tiver a topologia básica, você pode considerar se deseja ajustar qualquer um dos parâmetros:
* **Número de JVMs por nó de trabalho.** Se você tiver uma grande estrutura de dados (por exemplo, uma tabela de pesquisa) hospedada na memória, cada JVM vai precisar de uma cópia separada. Como alternativa, você pode usar a estrutura de dados em vários threads, se tiver menos JVMs. Para E/S do bolt, o número de JVMs não faz tanta diferença quanto o número de threads adicionados entre essas JVMs. Para simplificar, é uma boa ideia ter uma JVM por trabalho. No entanto, dependendo do que o bolt esteja fazendo ou de que processamento de aplicativo você esteja precisando, talvez seja necessário alterar esse número.
* **Número de executores de spout.** Como o exemplo anterior usa bolts para gravar no Data Lake Storage Gen2, o número de spouts não é diretamente relevante para o desempenho do bolt. No entanto, dependendo do volume de processamento ou de E/S ocorrendo no spout, é recomendável ajustar os spouts para melhor desempenho. Você precisa ter spouts suficientes para conseguir manter os bolts trabalhando. As taxas de saída dos spouts devem coincidir com a taxa de transferência dos bolts. A configuração real depende do spout.
* **Número de tarefas.** Cada bolt é executado como um único thread. Tarefas adicionais por bolt não fornecem simultaneidade adicional. A situação em que elas são benéficas é se o processo de confirmação da tupla utilizar uma grande proporção de seu tempo de execução de bolt. É uma boa ideia agrupar várias tuplas em um acréscimo maior antes de enviar uma confirmação do parafuso. Portanto, na maioria dos casos, várias tarefas não oferecem qualquer benefício adicional.
* **Agrupamento local ou aleatório.** Quando essa configuração é habilitada, tuplas são enviadas a bolts dentro do mesmo processo de trabalho. Isso reduz as chamadas de rede e comunicação entre processos. Isso é recomendado para a maioria das topologias.

Esse cenário básico é um bom ponto de partida. Teste com seus próprios dados para ajustar os parâmetros anteriores para obter o desempenho ideal.

## <a name="tune-the-spout"></a>Ajustar o spout

Você pode modificar as seguintes configurações para ajustar o spout.

- **Tempo limite de tupla: topology.message.timeout.secs**. Essa configuração determina a quantidade de tempo que uma mensagem leva para ser concluída e recebe a confirmação antes de ser considerada falha.

- **Memória máxima por processo de trabalho: worker.childopts**. Essa configuração permite especificar parâmetros de linha de comando adicionais para os trabalhos Java. A configuração mais comumente usada aqui é XmX, que determina o máximo de memória alocado para o heap de uma JVM.

- **Máx. de spouts pendentes: topology.max.spout.pending**. Essa configuração determina o número de tuplas que podem estar em voo (ainda não confirmadas em todos os nós na topologia) por thread de spout em qualquer tempo.

  Um bom cálculo a fazer é estimar o tamanho de cada uma de suas tuplas. Em seguida, calcule quanta memória um thread de spout tem. A memória total alocada para um thread dividida por esse valor deve fornecer o limite superior para o parâmetro de máx. de spouts pendentes.

O bolt do Storm do Data Lake Storage Gen2 padrão tem um parâmetro de política de sincronização de tamanho (fileBufferSize) que pode ser usado para ajustar esse parâmetro.

Em topologias com uso intensivo de E/S, é recomendável fazer com que cada thread de bolt grave seu próprio arquivo e definir uma política de rotação de arquivo (fileRotationSize). Quando o arquivo atingir um determinado tamanho, o fluxo será automaticamente liberado e a gravação ocorrerá em um novo arquivo. O tamanho do arquivo recomendado para a rotação é de 1 GB.

## <a name="monitor-your-topology-in-storm"></a>Monitorar a topologia no Storm  
Enquanto a topologia estiver em execução, você pode monitorá-la na interface do usuário do Storm. Aqui estão os principais parâmetros examinar:

* **Latência total de execução do processo.** Tempo médio que uma tupla leva para ser emitida pelo spout, processada pelo bolt e confirmada.

* **Latência total de processo do bolt.** Este é o tempo médio gasto pela tupla no raio até receber uma confirmação.

* **Latência total de execute do bolt.** Tempo médio gasto pelo bolt no método execute.

* **Número de falhas.** Refere-se ao número de tuplas que deixaram de ser completamente processadas antes que atingissem o tempo limite.

* **Recurso.** Medida da ocupação do sistema. Se esse número é 1, os bolts estão trabalhando tão rápido quanto possível. Se for menor que 1, aumente o paralelismo. Se for maior que 1, reduza o paralelismo.

## <a name="troubleshoot-common-problems"></a>Solução de problemas comuns
Eis alguns cenários comuns de solução de problemas.
* **Muitas tuplas estão atingindo o tempo limite.** Examine cada nó na topologia para determinar onde está o afunilamento. O motivo mais comum para isso é que os bolts não estão conseguindo acompanhar os spouts. Isso faz com que as tuplas congestionem os buffers internos que estão aguardando para ser processados. Considere aumentar o valor de tempo limite ou diminuir o máx. de spouts pendentes.

* **Há uma latência alta de execução do processo total, mas uma latência baixa de processo do bolt.** Nesse caso, é possível que as tuplas não estejam sendo reconhecidas rápido o suficiente. Verifique se há um número suficiente de confirmadores. Outra possibilidade é que estejam aguardando na fila por tempo demais antes que os bolts comecem a processá-las. Diminua o máx. de spouts pendentes.

* **Latência de execute do bolt elevada.** Isso significa que o método execute() do seu bolt está demorando muito. Otimize o código ou examine os tamanhos de gravação e o comportamento de liberação.

### <a name="data-lake-storage-gen2-throttling"></a>Limitação do Data Lake Storage Gen2
Se atingir os limites de largura de banda fornecidos pelo Data Lake Storage Gen2, você poderá ver falhas em tarefas. Verifique os logs de tarefa para erros de limitação. Você pode diminuir o paralelismo aumentando o tamanho do contêiner.    

Para verificar se há problemas de limitação, habilite o log de depuração no lado do cliente:

1. Em **Ambari**  >  **Storm**  >  **config**  >  **avançado Storm-Worker-Log4J**, altere **&lt; raiz Level = "info" &gt;** para **&lt; root Level = "debug" &gt;**. Reinicie todos os nós/serviços para que a configuração entre em vigor.
2. Monitore logs de topologia do Storm em nós de trabalho (em /var/log/storm/worker-artifacts/&lt;TopologyName&gt;/&lt;porta&gt;/worker.log) para ver as exceções de limitação do Data Lake Storage Gen2.

## <a name="next-steps"></a>Próximas etapas
Ajuste de desempenho adicional para o Storm pode ser referenciado [neste blog](/archive/blogs/shanyu/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs).

Para obter um exemplo adicional de execução, consulte [esse aqui no GitHub](https://github.com/hdinsight/storm-performance-automation).