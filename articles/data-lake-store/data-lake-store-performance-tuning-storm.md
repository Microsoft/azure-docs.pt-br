---
title: Diretrizes de ajuste de desempenho de Data Lake armazenamento Gen1 Storm do Azure | Microsoft Docs
description: Diretrizes de ajuste de desempenho de Data Lake armazenamento Gen1 Storm do Azure
services: data-lake-store
documentationcenter: ''
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: 8066a759cf80be6e9ca232bcd3693a5fa4d2f2f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61436470"
---
# <a name="performance-tuning-guidance-for-storm-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Diretrizes para o Storm no HDInsight e Azure Data Lake armazenamento Gen1 de ajuste de desempenho

Entenda os fatores que devem ser considerados ao ajustar o desempenho de uma topologia Storm do Azure. Por exemplo, é importante compreender as características do trabalho feito pelos spouts e bolts (se o trabalho está com uso intensivo de memória ou de E/S). Este artigo abrange uma gama de diretrizes de ajuste de desempenho, incluindo a solução de problemas comuns.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta do Azure Data Lake Storage Gen1**. Para obter instruções sobre como criar um, consulte [Introdução ao Azure Data Lake armazenamento Gen1](data-lake-store-get-started-portal.md).
* **Um cluster Azure HDInsight** com acesso a uma conta do Data Lake armazenamento Gen1. Veja [Criar um cluster HDInsight com Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Certifique-se de habilitar a área de trabalho remota para o cluster.
* **Executando um cluster do Storm no Data Lake armazenamento Gen1**. Para obter mais informações, consulte [Storm no HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-overview).
* **Diretrizes de ajuste de desempenho no Data Lake Storage Gen1**.  Para ver os conceitos gerais de desempenho, consulte [diretrizes de ajuste Data Lake armazenamento Gen1 desempenho](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance).  

## <a name="tune-the-parallelism-of-the-topology"></a>Ajuste do paralelismo da topologia

Você poderá melhorar o desempenho aumentando a simultaneidade de e/s para e do Data Lake armazenamento Gen1. Uma topologia Storm tem um conjunto de configurações que determinam o paralelismo:
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

### <a name="get-the-best-performance-from-data-lake-storage-gen1"></a>Get the best performance from Data Lake Storage Gen1

Ao trabalhar com o Data Lake Store, você obtém o melhor desempenho se fizer o seguinte:
* Una seus pequenos acréscimos em tamanhos maiores (o ideal é 4 MB).
* Faça o máximo possível de solicitações simultâneas. Como cada thread de bolt está fazendo leituras de bloqueio, é recomendável ter entre 8 e 12 threads por núcleo. Isso mantém o NIC e a CPU bem utilizados. Uma VM maior permite mais solicitações simultâneas.  

### <a name="example-topology"></a>Exemplo de topologia

Suponhamos que você tenha um cluster de 8 nós de trabalho com a VM D13v2 do Azure. Uma VM tem 8 núcleos, portanto, entre os 8 nós de trabalho, você tem 64 núcleos no total.

Digamos que façamos oito threads de bolt por núcleo. Dados 64 núcleos, isso significa que desejamos um total de 512 instâncias de executor de bolt (ou seja, threads). Nesse caso, digamos que podemos começar com uma JVM por VM e usar principalmente a simultaneidade de threads na JVM para obter a simultaneidade. Isso significa que precisamos de oito tarefas de trabalho (uma por VM do Azure) e 512 executores de bolt. Dada essa configuração, o Storm tenta distribuir os trabalhos uniformemente entre os nós de trabalho (também conhecidos como nós de supervisor), dando a cada nó de trabalho 1 JVM. Dentro dos supervisores, o Storm tenta distribuir os executores uniformemente entre supervisores, dando a cada supervisor (ou seja, JVM) 8 threads.

## <a name="tune-additional-parameters"></a>Ajuste de parâmetros adicionais
Depois que tiver a topologia básica, você pode considerar se deseja ajustar qualquer um dos parâmetros:
* **Número de JVMs por nó de trabalho.** Se você tiver uma grande estrutura de dados (por exemplo, uma tabela de pesquisa) hospedada na memória, cada JVM vai precisar de uma cópia separada. Como alternativa, você pode usar a estrutura de dados em vários threads, se tiver menos JVMs. Para E/S do bolt, o número de JVMs não faz tanta diferença quanto o número de threads adicionados entre essas JVMs. Para simplificar, é uma boa ideia ter uma JVM por trabalho. No entanto, dependendo do que o bolt esteja fazendo ou de que processamento de aplicativo você esteja precisando, talvez seja necessário alterar esse número.
* **Número de executores de spout.** Como o exemplo anterior usa bolts para gravar no Data Lake Store, o número de spouts não é diretamente relevante para o desempenho do bolt. No entanto, dependendo do volume de processamento ou de E/S ocorrendo no spout, é recomendável ajustar os spouts para melhor desempenho. Você precisa ter spouts suficientes para conseguir manter os bolts trabalhando. As taxas de saída dos spouts devem coincidir com a taxa de transferência dos bolts. A configuração real depende do spout.
* **Número de tarefas.** Cada bolt é executado como um único thread. Tarefas adicionais por bolt não fornecem simultaneidade adicional. A situação em que elas são benéficas é se o processo de confirmação da tupla utilizar uma grande proporção de seu tempo de execução de bolt. É recomendável agrupar muitas tuplas em um acréscimo maior antes de enviar uma confirmação do bolt. Portanto, na maioria dos casos, várias tarefas não oferecem qualquer benefício adicional.
* **Agrupamento local ou aleatório.** Quando essa configuração é habilitada, tuplas são enviadas a bolts dentro do mesmo processo de trabalho. Isso reduz as chamadas de rede e comunicação entre processos. Isso é recomendado para a maioria das topologias.

Esse cenário básico é um bom ponto de partida. Teste com seus próprios dados para ajustar os parâmetros anteriores para obter o desempenho ideal.

## <a name="tune-the-spout"></a>Ajustar o spout

Você pode modificar as seguintes configurações para ajustar o spout.

- **Tempo limite de tupla: topology.message.timeout.secs**. Essa configuração determina quanto tempo uma mensagem leva para ser concluída e receber a confirmação antes de ser considerada com falha.

- **Memória máxima por processo de trabalho: worker.childopts**. Essa configuração permite especificar parâmetros de linha de comando adicionais para os trabalhos Java. A configuração mais comumente usada aqui é XmX, que determina o máximo de memória alocado para o heap de uma JVM.

- **Máx. de spouts pendentes: topology.max.spout.pending**. Essa configuração determina o número de tuplas que podem estar em voo (ainda não confirmadas em todos os nós na topologia) por thread de spout em qualquer tempo.

  Um bom cálculo a fazer é estimar o tamanho de cada uma de suas tuplas. Em seguida, calcule quanta memória um thread de spout tem. A memória total alocada para um thread dividida por esse valor deve fornecer o limite superior para o parâmetro de máx. de spouts pendentes.

## <a name="tune-the-bolt"></a>Ajustar o bolt
Quando você estiver gravando no Data Lake Storage Gen1, defina uma política de sincronização de tamanho (buffer no lado do cliente) como 4 MB. Uma liberação ou hsync() será executada somente quando o tamanho do buffer tiver esse valor. O driver Data Lake Storage Gen1 na VM de trabalho faz esse buffer automaticamente, a menos que você execute explicitamente um hsync ().

O parafuso Storm do Data Lake Storage Gen1 padrão possui um parâmetro de política de sincronização de tamanho (fileBufferSize) que pode ser usado para ajustar esse parâmetro.

Em topologias com uso intensivo de E/S, é recomendável fazer com que cada thread de bolt grave seu próprio arquivo e definir uma política de rotação de arquivo (fileRotationSize). Quando o arquivo atingir um determinado tamanho, o fluxo será automaticamente liberado e a gravação ocorrerá em um novo arquivo. O tamanho do arquivo recomendado para a rotação é de 1 GB.

### <a name="handle-tuple-data"></a>Manipulação de dados de tupla

No Storm, um spout permanece com uma tupla até que ela seja explicitamente confirmada pelo bolt. Se uma tupla tiver sido lida pelo parafuso, mas ainda não tiver sido confirmada, o bico pode não ter persistido no back end do Data Lake Storage Gen1. Após a confirmação da tupla, o spout pode ter sua persistência garantida pelo bolt e, em seguida, pode excluir os dados de origem de qualquer fonte da qual esteja lendo.  

Para melhor desempenho no Data Lake armazenamento Gen1, ter o bolt do buffer de 4 MB de dados de tupla. Em seguida, grave a Gen1 de armazenamento do Data Lake volta final como uma gravação de 4 MB. Depois que os dados tiverem sido gravados com êxito no repositório (chamando hflush()), o bolt poderá confirmar os dados de volta para o spout. É isso que o bolt de exemplo fornecido aqui faz. Também é aceitável manter um número maior de tuplas antes de fazer a chamada de hflush() e confirmar as tuplas. No entanto, isso aumenta o número de tuplas em voo que o spout precisa armazenar e, portanto, aumenta a quantidade de memória exigida por JVM.

> [!NOTE]
> Determinados aplicativos podem ter um requisito de confirmar tuplas com maior frequência (em tamanhos de dados menores que 4 MB), por motivos não relacionados a desempenho. No entanto, isso pode afetar a taxa de transferência de E/S para o back-end de armazenamento. Avalie com cuidado essa compensação em relação ao desempenho de E/S do bolt.

Se a taxa de entrada de tuplas não for alta, fazendo com que o buffer de 4 MB demore muito para ser preenchido, é recomendável mitigar isso:
* Reduzindo o número de bolts, de modo que haja menos buffers a preencher.
* Adotando uma política baseada em tempo ou em contagem, na qual um hflush() é disparado a cada x liberações ou a cada y milissegundos e as tuplas acumuladas até esse ponto são confirmadas de volta.

Observe que neste caso a taxa de transferência é menor, mas de todo modo, com uma taxa de eventos baixa, a taxa de transferência máxima não é o objetivo principal. Essas reduções ajudam a reduzir o tempo total necessário para o fluxo de uma tupla até o repositório. Isso pode ser relevante se você quiser um pipeline em tempo real, mesmo com uma taxa baixa de evento. Observe também que, se a taxa de entrada de tuplas for baixa, você precisará ajustar o parâmetro topology.message.timeout_secs para que as tuplas não atinjam o tempo limite enquanto estiverem sendo armazenadas em buffer ou processadas.

## <a name="monitor-your-topology-in-storm"></a>Monitorar a topologia no Storm  
Enquanto a topologia estiver em execução, você pode monitorá-la na interface do usuário do Storm. Aqui estão os principais parâmetros examinar:

* **Latência total de execução do processo.** Tempo médio que uma tupla leva para ser emitida pelo spout, processada pelo bolt e confirmada.

* **Latência total de processo do bolt.** Tempo médio gasto pela tupla no bolt até receber uma confirmação.

* **Latência total de execute do bolt.** Tempo médio gasto pelo bolt no método execute.

* **Número de falhas.** Refere-se ao número de tuplas que deixaram de ser completamente processadas antes que atingissem o tempo limite.

* **Capacidade.** Medida da ocupação do sistema. Se esse número é 1, os bolts estão trabalhando tão rápido quanto possível. Se for menor que 1, aumente o paralelismo. Se for maior que 1, reduza o paralelismo.

## <a name="troubleshoot-common-problems"></a>Solução de problemas comuns
Eis alguns cenários comuns de solução de problemas.
* **Grande número de tuplas atingindo o tempo limite.** Procure em cada nó na topologia para determinar onde está o gargalo. O motivo mais comum para isso é que os bolts não estão conseguindo acompanhar os spouts. Isso faz com que as tuplas congestionem os buffers internos que estão aguardando para ser processados. Considere aumentar o valor de tempo limite ou diminuir o máx. de spouts pendentes.

* **Há uma latência alta de execução do processo total, mas uma latência baixa de processo do bolt.** Nesse caso, é possível que as tuplas não estejam sendo reconhecidas rápido o suficiente. Verifique se há um número suficiente de confirmadores. Outra possibilidade é que estejam aguardando na fila por tempo demais antes que os bolts comecem a processá-las. Diminua o máx. de spouts pendentes.

* **Latência de execute do bolt elevada.** Isso significa que o método execute() do seu bolt está demorando muito. Otimize o código ou examine os tamanhos de gravação e o comportamento de liberação.

### <a name="data-lake-storage-gen1-throttling"></a>Limitação do data Lake armazenamento Gen1
Se você atingir os limites de largura de banda fornecidos pelo Data Lake armazenamento Gen1, você pode ver falhas de tarefas. Verifique os logs de tarefa para erros de limitação. Você pode diminuir o paralelismo aumentando o tamanho do contêiner.    

Para verificar se há problemas de limitação, habilite o log de depuração no lado do cliente:

1. Em **Ambari** > **Storm** > **Config** > **Advanced storm-worker-log4j**, altere **&lt;root level="info"&gt;** para **&lt;root level=”debug”&gt;**. Reinicie todos os nós/serviços para que a configuração entre em vigor.
2. Monitorar logs de topologia do Storm em nós de trabalho (em /var/log/Storm/Worker-artifacts/ /&lt;TopologyName&gt;/&lt;porta&gt;/worker.log) para o Data Lake armazenamento Gen1 exceções de limitação.

## <a name="next-steps"></a>Próximas etapas
Ajuste de desempenho adicional para o Storm pode ser referenciado [neste blog](https://blogs.msdn.microsoft.com/shanyu/2015/05/14/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs/).

Para obter um exemplo adicional de execução, consulte [esse aqui no GitHub](https://github.com/hdinsight/storm-performance-automation).
