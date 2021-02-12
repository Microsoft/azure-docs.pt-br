---
title: Guia de dimensionamento do Cluster de Consulta Interativa do HDInsight (LLAP)
description: Guia de dimensionamento de LLAP
ms.service: hdinsight
ms.topic: troubleshooting
author: aniket-ms
ms.author: aadnaik
ms.reviewer: HDI HiveLLAP Team
ms.date: 05/05/2020
ms.openlocfilehash: 7df75077785c66215008e045ef0b1e451ba29f57
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98931100"
---
# <a name="azure-hdinsight-interactive-query-cluster-hive-llap-sizing-guide"></a>Guia de dimensionamento do Cluster de Consulta Interativa do Azure HDInsight (Hive LLAP)

Este documento descreve o dimensionamento do cluster de Consulta Interativa do HDInsight (cluster Hive LLAP) para uma carga de trabalho normal para obter um desempenho aceitável. As recomendações deste documento são diretrizes genéricas e cargas de trabalho específicas que podem precisar de ajuste específico.

### <a name="azure-default-vm-types-for-hdinsight-interactive-query-clusterllap"></a>**Tipos de VM padrão do Azure para o Cluster de consulta Interativa do HDInsight (LLAP)**

| Tipo de nó      | Instância | Tamanho     |
| :---        |    :----:   | :---     |
| Head      | D13 v2       | 8 vCPUs, 56 GB de RAM, SSD de 400 GB   |
| Trabalho   | **D14 v2**        | **16 vCPUs, 112 GB de RAM, SSD de 800 GB**       |
| ZooKeeper   | A4 v2        | 4 vCPUs, 8 GB de RAM, SSD de 40 GB       |

**_Observação: todos os valores de configuração recomendados se baseiam no tipo D14 v2 nó de trabalho_* _  

### <a name="_configuration"></a>_ *Configuração:**    
| Chave de configuração      | Valor recomendado  | Descrição |
| :---        |    :----:   | :---     |
| yarn.nodemanager.resource.memory-mb | 102400 (MB) | Memória total fornecida, em MB, para todos os contêineres de YARN em um nó | 
| yarn.scheduler.maximum-allocation-mb | 102400 (MB) | A alocação máxima de cada solicitação de contêiner no RM, em MBs. As solicitações de memória maiores que esse valor não entrarão em vigor |
| yarn.scheduler.maximum-allocation-vcores | 12 |O número máximo de núcleos de CPU de cada solicitação de contêiner no Resource Manager. As solicitações maiores que esse valor não entrarão em vigor. |
| yarn.nodemanager.resource.cpu-vcores | 12 | Número de núcleos de CPU por NodeManager que podem ser alocados para contêineres. |
| yarn.scheduler.capacity.root.llap.capacity | 85 (%) | Alocação de capacidade do YARN para fila de LLAP  |
| tez.am.resource.memory.mb | 4096 (MB) | A quantidade de memória em MB a ser usada pelo tez AppMaster |
| hive.server2.tez.sessions.per.default.queue | <número_de_nós_de_trabalho> |O número de sessões de cada fila chamada no hive.server2.tez.default.queues. Esse número corresponde ao número de coordenadores de consulta (Tez AMs) |
| hive.tez.container.size | 4096 (MB) | Tamanho do contêiner Tez especificado em MB |
| hive.llap.daemon.num.executors | 19 | Número de executores por daemon de LLAP | 
| hive.llap.io.threadpool.size | 19 | Tamanho do pool de threads para executores |
| hive.llap.daemon.yarn.container.mb | 81920 (MB) | Memória total em MB usada por daemons de LLAP individuais (memória por daemon)
| hive.llap.io.memory.size | 242688 (MB) | Tamanho do cache em MB por daemon LLAP, desde que o cache de SSD esteja habilitado |
| hive.auto.convert.join.noconditionaltask.size | 2048 (MB) | tamanho da memória em MB para fazer a Junção de mapa |

### <a name="llap-architecturecomponents"></a>**Arquitetura/componentes do LLAP:**  

![' Arquitetura/componentes do LLAP '](./media/hive-llap-sizing-guide/LLAP_architecture_sizing_guide.png "Arquitetura/componentes do LLAP")

### <a name="llap-daemon-size-estimations"></a>**Estimativas de tamanho do daemon de LLAP:** 

#### <a name="1-determining-total-yarn-memory-allocation-for-all-containers-on-a-node"></a>**1. Determinação de alocação total de memória YARN para todos os contêineres em um nó**    
Configuração: **_yarn. NodeManager. Resource. Memory-MB_* _  

Esse valor indica uma soma máxima da memória em MB que pode ser usada pelos contêineres YARN em cada nó. O valor especificado deve ser menor que a quantidade total de memória física nesse nó.   
Memória total para todos os contêineres de YARN em um nó = (memória física total – memória para so + outros serviços)  
Defina esse valor como aproximadamente 90% da RAM disponível.  
Para D14 v2, o valor recomendado é _ * 102.400 MB * *. 

#### <a name="2-determining-maximum-amount-of-memory-per-yarn-container-request"></a>**2. Determinação da quantidade máxima de memória por solicitação de contêiner do YARN**  
Configuração: **_yarn. scheduler. Maximum-Allocation-MB_* _

Esse valor indica a alocação máxima de cada solicitação de contêiner no Azure Resource Manager, em MB. As solicitações de memória maiores que o valor especificado não entrarão em vigor. O Gerenciador de recursos pode fornecer memória a contêineres em incrementos de _yarn. scheduler. Minimum-Allocation-MB * e não pode exceder o tamanho especificado por *yarn. scheduler. Maximum-Allocation-MB*. O valor especificado não deve ser maior que a memória total especificada para todos os contêineres no nó especificado por *yarn.nodemanager.resource.memory-mb*.    
Para nós de trabalho D14 v2, o valor recomendado é **102400 MB**

#### <a name="3-determining-maximum-amount-of-vcores-per-yarn-container-request"></a>**3. Determinação da quantidade máxima de vcores por solicitação de contêiner do YARN**  
Configuração: **_yarn. scheduler. Maximum-Allocation-vcores_* _  

Esse valor indica o número máximo de núcleos de CPU virtual de cada solicitação de contêiner no Resource Manager. Solicitar um número maior de vcores do que esse valor não terá efeito. É uma propriedade global do Agendador do YARN. Para o contêiner do daemon de LLAP, esse valor pode ser definido como 75% do total de vcores disponíveis. Os 25% restantes devem ser reservados para NodeManager, DataNode e outros serviços em execução nos nós de trabalho.  
Há 16 vcores em VMs D14 v2 e 75% do total de 16 vcores podem ser usadas pelo contêiner do daemon de LLAP.  
Para D14 v2, o valor recomendado é _ * 12 * *.  

#### <a name="4-number-of-concurrent-queries"></a>**4. Número de consultas simultâneas**  
Configuração: **_Hive. Server2. tez. Sessions. per. padrão. Queue_* _

Esse valor de configuração determina o número de sessões de Tez que podem ser iniciadas paralelamente. Essas sessões de Tez serão iniciadas para cada uma das filas especificadas por "hive.server2.tez.default.queues". Isso corresponde ao número de Tez AMs (Coordenadores de Consulta). É recomendável que seja igual ao número de nós de trabalho. O número de Tez AMs pode ser maior que o número de nós do daemon de LLAP. Sua principal responsabilidade é coordenar a execução da consulta e atribuir fragmentos do plano de consulta aos daemons de LLAP correspondentes para execução. Mantenha esse valor como múltiplo de vários nós daemon LLAP para obter maior taxa de transferência.  

O cluster HDInsight padrão tem quatro daemons LLAP em execução em quatro nós de trabalho, portanto, o valor recomendado é _ * 4 * *.  

**Controle deslizante da interface do usuário Ambari para variável de configuração do hive `hive.server2.tez.sessions.per.default.queue` :**

![' LLAP máximo de consultas simultâneas '](./media/hive-llap-sizing-guide/LLAP_sizing_guide_max_concurrent_queries.png "LLAP número máximo de consultas simultâneas")

#### <a name="5-tez-container-and-tez-application-master-size"></a>**5. O contêiner Tez e o tamanho mestre do aplicativo Tez**    
Configuração: **_tez. am. Resource. Memory. MB, Hive. tez. Container. Size_* _  

_tez. am. Resource. Memory. MB *-define o tamanho mestre do aplicativo tez.  
O valor recomendado é **4096 MB**.
   
*hive.tez.container.size* - define a quantidade de memória fornecida para o contêiner Tez. Esse valor deve ser definido entre o tamanho mínimo do contêiner YARN (*yarn.scheduler.minimum-allocation-mb*) e o tamanho máximo de contêiner YARN (*yarn.scheduler.maximum-allocation-mb*). Os executores de daemon de LLAP usam esse valor para limitar o uso de memória por executor.  
O valor recomendado é **4096 MB**.  

#### <a name="6-llap-queue-capacity-allocation"></a>**6. Alocação de capacidade de fila de LLAP**   
Configuração: **_yarn. scheduler. Capacity. raiz. LLAP. Capacity_* _  

Esse valor indica uma porcentagem da capacidade fornecida para a fila de LLAP. As alocações de capacidade podem ter valores diferentes para cargas de trabalho diferentes, dependendo de como as filas do YARN estão configuradas. Se sua carga de trabalho for de operações somente leitura, defini-la como 90% da capacidade deve funcionar. No entanto, se sua carga de trabalho for uma combinação de operações de atualização/exclusão/mesclagem usando tabelas gerenciadas, é recomendável fornecer 85% da capacidade para a fila LLAP. A capacidade restante de 15% pode ser usada por outras tarefas, como compactação, etc. para alocar contêineres da fila padrão. Dessa forma, as tarefas na fila padrão não retirarão os recursos do YARN.    

Para nós de trabalho D14v2, o valor recomendado para fila LLAP é _ * 85 * *.     
(Para cargas de trabalho somente leitura, pode ser aumentado até 90 como for adequado.)  

#### <a name="7-llap-daemon-container-size"></a>**7. Tamanho do contêiner do daemon de LLAP**    
Configuração: **_Hive. LLAP. daemon. yarn. Container. MB_* _  
   
O daemon de LLAP é executado como um contêiner do YARN em cada nó de trabalho. O tamanho total da memória para o contêiner do daemon de LLAP depende dos seguintes fatores,    
_ Configurações do tamanho do contêiner YARN (yarn. scheduler. Minimum-Allocation-MB, yarn. scheduler. Maximum-Allocation-MB, yarn. NodeManager. Resource. Memory-MB)
*  Número de Tez AMs em um nó
*  Memória total configurada para todos os contêineres em um nó e capacidade de fila de LLAP  

A memória necessária pelo Tez AM (Tez Application Masters) pode ser calculada da seguinte maneira.  
Tez atua como um coordenador de consultas e o número de tez AMs deve ser configurado com base em várias consultas simultâneas a serem atendidas. Teoricamente, podemos considerar um tez AM por nó de trabalho. No entanto, é possível que você veja mais de uma tez em um nó de trabalho. Para fins de cálculo, presumimos a distribuição uniforme do tez AMs em todos os nós/nós de trabalho do LLAP daemon.
É recomendável ter 4 GB de memória por Tez AM.  

Número de tez AMS = valor especificado pela configuração do hive ***Hive. Server2. tez. Sessions. per. padrão. Queue** _.  
Número de nós do daemon LLAP = especificado pela variável env _*_num_llap_nodes_for_llap_daemons_*_ na interface do usuário do amAmbari.  
Tamanho de contêiner tez AM = valor especificado por tez config _*_tez. am. Resource. Memory. MB_*_.  

Tez am Memory por nó = _ *(** ceil **(** número de tez **/** de nós do daemon do LLAP **)** **x** tez am tamanho do contêiner do **AMS)**  
Para D14 v2, a configuração padrão tem quatro Tez AMs e quatro nós de daemon de LLAP.  
Memória do Tez AM por nó = (ceil(4/4) x 4 GB) = 4 GB

A memória total disponível para a fila LLAP por nó de trabalho pode ser calculada da seguinte maneira:  
Esse valor depende da quantidade total de memória disponível para todos os contêineres de YARN em um nó (*yarn. NodeManager. Resource. Memory-MB*) e a porcentagem de capacidade configurada para LLAP Queue (*yarn. scheduler. Capacity. root. LLAP. Capacity*).  
Memória total para a fila de LLAP no nó de trabalho = total de memória disponível para todos os contêineres de YARN em um nó x percentual de capacidade para a fila de LLAP.  
Para D14 v2, esse valor é (100 GB x 0,85) = 85 GB.

O tamanho do contêiner do daemon de LLAP é calculado da seguinte maneira;

**Tamanho do contêiner do daemon LLAP = (memória total para fila LLAP em um workernode) – (tez AM Memory por nó)-(tamanho do contêiner de serviço mestre)**  
Há apenas um mestre de serviço (aplicativo mestre para o serviço LLAP) no cluster gerado em um dos nós de trabalho. Para fins de cálculo, consideramos um mestre de serviço por nó de trabalho.  
Para o nó de trabalho D14 v2, HDI 4,0-o valor recomendado é (85 GB-4 GB-1 GB)) = **80 GB**   
(Para HDI 3,6, o valor recomendado é **79 GB** porque você deve reservar mais ~ 2 GB para o controle deslizante am.)  

#### <a name="8-determining-number-of-executors-per-llap-daemon"></a>**8. Determinação do número de executores por daemon de LLAP**  
Configuração: **_hive.llap.daemon.num.executors_* _, _*_Hive. LLAP. IO. ThreadPool. Size_*_

_*_hive.llap.daemon.num.executors_*_:   
Essa configuração controla o número de executores que podem realizar tarefas em paralelo por daemon LLAP. Esse valor depende do número de vcores, da quantidade de memória usada por executor e da quantidade de memória total disponível para o contêiner do daemon do LLAP.    O número de executores pode ser superassinado em 120% de vcores disponíveis por nó de trabalho. No entanto, ele deve ser ajustado se não atender aos requisitos de memória com base na memória necessária por executor e o tamanho do contêiner LLAP daemon.

Cada executor é equivalente a um contêiner tez e pode consumir 4 GB (tamanho do contêiner tez) de memória. Todos os executores no daemon LLAP compartilham a mesma memória de heap. Com a suposição de que nem todos os executores executam operações com uso intensivo de memória ao mesmo tempo, você pode considerar 75% do tamanho do contêiner tez (4 GB) por executor. Dessa forma, você pode aumentar o número de executores dando a cada executor menos memória (por exemplo, 3 GB) para maior paralelismo. No entanto, é recomendável ajustar essa configuração para sua carga de trabalho de destino.

Há 16 vcores em VMs D14 v2.
Para D14 v2, o valor recomendado para num dos executores é (16 vcores x 120%) ~ = _ *19** em cada nó de trabalho considerando 3GB por executor.

**_Hive. LLAP. IO. ThreadPool. Size_*_: esse valor especifica o tamanho do pool de threads para executores. Como os executores são fixos conforme especificado, ele será o mesmo número de executores por daemon LLAP. Para D14 v2, o valor recomendado é _* 19**.

#### <a name="9-determining-llap-daemon-cache-size"></a>**9. Determinando o tamanho do cache do daemon de LLAP**  
Configuração: **_Hive. LLAP. IO. Memory. Size_* _

A memória do contêiner do LLAP daemon consiste nos seguintes componentes; _ Sala de cabeça
*  Memória de heap usada pelos executores (Xmx)
*  Cache na memória por daemon (seu tamanho de memória fora do heap, não aplicável quando o cache SSD está habilitado)
*  Tamanho de metadados de cache na memória (aplicável somente quando o cache de SSD está habilitado)

**Tamanho da reserva dinâmica**: Este tamanho indica uma parte da memória fora do heap usada para sobrecarga de VM Java (metaespaço, pilha de threads, estruturas de dados GC etc.). Em geral, essa sobrecarga é de cerca de 6% do tamanho do heap (Xmx). Para estar no lado mais seguro, este valor pode ser calculado como 6% do tamanho total da memória do daemon de LLAP.  
Para D14 v2, o valor recomendado é ceil (80 GB x 0, 6) ~ = **4 GB**.  

**Tamanho do heap(Xmx)** : É a quantidade de memória heap disponível para todos os executores.
Tamanho total do heap = número de executores x 3 GB  
Para D14 v2, esse valor é 19 x 3 GB = **57 GB**  

`Ambari environment variable for LLAP heap size:`

![' Tamanho do heap LLAP '](./media/hive-llap-sizing-guide/LLAP_sizing_guide_llap_heap_size.png "Tamanho do heap LLAP")

Quando o cache de SSD está desabilitado, o cache na memória é uma quantidade de memória que é deixada após a retirada do tamanho de reserva e o tamanho do heap do tamanho do contêiner do LLAP daemon.

O cálculo do tamanho do cache difere quando o cache de SSD está habilitado.
A configuração de *hive.llap.io.allocator.mmap* = true habilitará o cache de SSD.
Quando o cache SSD está habilitado, parte da memória será usada para armazenar metadados para o cache de SSD. Os metadados são armazenados na memória e espera-se que seja ~ 8% do tamanho do cache SSD.   
Tamanho de metadados na memória do cache SSD = tamanho do contêiner do daemon LLAP-(sala de cabeçalho + tamanho do heap)  
Para D14 v2, com HDI 4,0, tamanho de metadados na memória do cache SSD = 80 GB-(4 GB + 57 GB) = **19 GB**  
Para D14 v2, com HDI 3,6, tamanho de metadados na memória do cache SSD = 79 GB-(4 GB + 57 GB) = **18 GB**

Considerando o tamanho da memória disponível para armazenar metadados de cache de SSD, podemos calcular o tamanho do cache de SSD que pode ser suportado.  
Tamanho dos metadados na memória para o cache SSD = tamanho do contêiner do daemon LLAP-(sala principal + tamanho do heap) = 19 GB     
Tamanho do cache SSD = tamanho dos metadados na memória para o cache SSD (19 GB)/0, 8 (8 por cento)  

Para D14 V2 e HDI 4,0, o tamanho recomendado do cache SSD = 19 GB/0, 8 ~ = **237 GB**  
Para D14 V2 e HDI 3,6, o tamanho recomendado do cache SSD = 18 GB/0, 8 ~ = **225 GB**

#### <a name="10-adjusting-map-join-memory"></a>**10. Ajuste da memória de junção de mapa**   
Configuração: **_Hive. auto. converter. Join. noconditionaltask. Size_* _

Verifique se você tem _hive. auto. converter. Join. noconditionaltask * habilitado para que esse parâmetro entre em vigor.
Essa configuração determina o limite para a seleção de MapJoin pelo otimizador do hive que considera a superassinatura da memória de outros executores para ter mais espaço para tabelas de hash na memória para permitir mais conversões de junção de mapa. Considerando 3GB por executor, esse tamanho pode ser superassinado para 3GB, mas alguma memória de heap também pode ser usada para buffers de classificação, buffers de ordem aleatória, etc., por outras operações.   
Portanto, para D14 v2, com 3 GB de memória por executor, é recomendável definir esse valor como **2048 MB**.  

(Observação: esse valor pode precisar de ajustes adequados para sua carga de trabalho. Se você definir esse valor muito baixo, não poderá usar o recurso de conversão automática. E configurá-lo muito alto pode resultar em indisponibilidade de exceções de memória ou em pausas de GC que podem resultar em desempenho adverso.)  

#### <a name="11-number-of-llap-daemons"></a>**11. número de daemons do LLAP**
Variáveis de ambiente Ambari: **_num_llap_nodes, num_llap_nodes_for_llap_daemons_* _  

_ *num_llap_nodes**-especifica o número de nós usados pelo serviço LLAP do hive, isso inclui os nós que executam o daemon do LLAP, o serviço mestre do LLAP e o mestre do aplicativo tez (tez AM).  

![' Número de nós para o serviço LLAP '](./media/hive-llap-sizing-guide/LLAP_sizing_guide_num_llap_nodes.png "Número de nós para o serviço LLAP")  

**num_llap_nodes_for_llap_daemons** -número especificado de nós usado somente para daemons LLAP. Os tamanhos de contêiner do daemon LLAP são definidos para o nó de ajuste máximo, portanto, ele resultará em um daemon de LLAP em cada nó.

![' Número de nós para daemons LLAP '](./media/hive-llap-sizing-guide/LLAP_sizing_guide_num_llap_nodes_for_llap_daemons.png "Número de nós para daemons LLAP")

É recomendável manter os dois valores iguais aos do número de nós de trabalho no cluster de consulta interativa.

### <a name="considerations-for-workload-management"></a>**Considerações sobre o gerenciamento de carga de trabalho**  
Se você quiser habilitar o gerenciamento de carga de trabalho para LLAP, lembre-se de reservar capacidade suficiente para que o gerenciamento de carga de trabalho funcione conforme o esperado. O gerenciamento de carga de trabalho requer a configuração de uma fila de YARN personalizada, que está além da `llap` fila. Certifique-se de dividir a capacidade total do recurso de cluster entre a fila LLAP e o gerenciamento de carga de trabalho de acordo com seus requisitos de carga de trabalho.
O gerenciamento de carga de trabalho gera tez AMs (tez Application Masters) quando um plano de recursos é ativado.
Observação:

* O tez AMs foi gerado por meio da ativação de um plano de recursos para consumir recursos da fila de gerenciamento de carga de trabalho conforme especificado pelo `hive.server2.tez.interactive.queue` .  
* O número de tez AMs dependerá do valor de `QUERY_PARALLELISM` especificado no plano de recursos.  
* Depois que o gerenciamento de carga de trabalho estiver ativo, o tez AMs na fila do LLAP não será usado. Somente tez AMs da fila de gerenciamento de carga de trabalho são usados para coordenação de consultas. O tez AMs na `llap` fila é usado quando o gerenciamento de carga de trabalho está desabilitado.
 
Por exemplo: capacidade total do cluster = 100 GB de memória, dividida entre LLAP, gerenciamento de carga de trabalho e filas padrão, da seguinte maneira:
 - capacidade da fila de LLAP = 70 GB
 - Capacidade da fila de gerenciamento de carga de trabalho = 20 GB
 - Capacidade de fila padrão = 10 GB

Com 20 GB na capacidade da fila de gerenciamento de carga de trabalho, um plano de recursos pode especificar o `QUERY_PARALLELISM` valor como cinco, o que significa que o gerenciamento de carga de trabalho pode iniciar cinco tez AMS com 4 GB de tamanho de contêiner cada. Se `QUERY_PARALLELISM` for maior do que a capacidade, você verá que um AMS tez parar de responder no `ACCEPTED` estado. O Hiveserver2 Interactive não pode enviar fragmentos de consulta para o AMs tez que não estão no `RUNNING` estado.


#### <a name="next-steps"></a>**Próximas etapas**
Se definir esses valores não resolver o problema, visite um dos seguintes...

* Obtenha respostas de especialistas do Azure por meio do [Suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando-se à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o hub **Ajuda + suporte**. Para obter informações mais detalhadas, confira [Como criar uma solicitação de suporte do Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso ao Gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o Suporte técnico é fornecido por meio de um dos [Planos de Suporte do Azure](https://azure.microsoft.com/support/plans/).  

* ##### <a name="other-references"></a>**Outras referências:**
  * [Configurar outras propriedades de LLAP](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_setup_llap.html)  
  * [Configurar o tamanho do heap do servidor do Apache Hive](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_hiveserver_heap_sizing.html)  
  * [Tamanho da memória do mapa de junção para LLAP](https://community.cloudera.com/t5/Community-Articles/Map-Join-Memory-Sizing-For-LLAP/ta-p/247462)  
  * [Propriedades do mecanismo de execução do Tez](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_tez_engine_properties.html)  
  * [Aprofundamento do LLAP do Apache Hive](https://community.cloudera.com/t5/Community-Articles/Hive-LLAP-deep-dive/ta-p/248893)