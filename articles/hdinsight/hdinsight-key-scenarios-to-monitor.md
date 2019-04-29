---
title: Monitorar o desempenho do cluster – Azure HDInsight
description: Como monitorar um cluster HDInsight quanto a capacidade e desempenho.
author: maxluk
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: arindamc
ms.openlocfilehash: 22484885663a4f9a908ae988882b87612129251a
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763785"
---
# <a name="monitor-cluster-performance"></a>Monitorar o desempenho do cluster

Monitorar a integridade e o desempenho de um cluster HDInsight é essencial para manter o desempenho ideal e a utilização de recursos. O monitoramento também pode ajudar a detectar e solucionar erros de configuração de cluster e problemas de código de usuário.

As seções a seguir descrevem como monitorar e otimizar a carga em seus clusters, filas do YARN do Apache Hadoop e detectar problemas de limitação de armazenamento.

## <a name="monitor-cluster-load"></a>Carga de cluster de monitor

Os clusters do Hadoop podem oferecer o desempenho mais ideal quando a carga no cluster é distribuída uniformemente em todos os nós. Isso permite que as tarefas de processamento sejam executadas sem serem restringidas por recursos de RAM, CPU ou disco em nós individuais.

Para obter uma visão de alto nível de nós do cluster e os respectivos carregamentos, entrar para o [IU da Web do Ambari](hdinsight-hadoop-manage-ambari.md), em seguida, selecione a guia **Hosts**. Os hosts são listados por seus nomes de domínio totalmente qualificados. O status operacional de cada host é mostrado por um indicador de integridade colorido:

| Cor | DESCRIÇÃO |
| --- | --- |
| Vermelho | Pelo menos um componente mestre no host está inoperante. Passe o mouse para ver uma dica de ferramenta que lista os componentes afetados. |
| Laranja | Pelo menos um componente subordinado no host está inoperante. Passe o mouse para ver uma dica de ferramenta que lista os componentes afetados. |
| Amarelo | O Servidor Ambari não recebeu uma pulsação do host há mais de 3 minutos. |
| Verde | Estado de execução normal. |

Você também verá colunas mostrando o número de núcleos e a quantidade de RAM para cada host, bem como o uso do disco e a carga média.

![Guia Hosts](./media/hdinsight-key-scenarios-to-monitor/hosts-tab.png)

Selecione qualquer um dos nomes de host para ter uma visão detalhada dos componentes em execução nesse host, bem como suas métricas. As métricas são mostradas como uma linha do tempo selecionável de uso de CPU, carga, uso do disco, uso de memória, uso de rede e números de processos.

![Detalhes do host](./media/hdinsight-key-scenarios-to-monitor/host-details.png)

Consulte [Gerenciar clusters HDInsight usando a interface do usuário da Web do Apache Ambari](hdinsight-hadoop-manage-ambari.md) para obter detalhes sobre a configuração de alertas e exibição de métricas.

## <a name="yarn-queue-configuration"></a>Configuração de fila do YARN

O Hadoop tem vários serviços em execução em sua plataforma distribuída. YARN (Yet Another Resource Negotiator) coordena esses serviços e aloca recursos de cluster para garantir que qualquer carga seja distribuída uniformemente pelo cluster.

O YARN divide as duas responsabilidades do JobTracker, o gerenciamento de recursos e o agendamento / monitoramento de tarefas em dois daemons: um Gerenciador de Recursos global e um ApplicationMaster (AM) por aplicativo.

O Resource Manager é um *planejador puro* e arbitra unicamente os recursos disponíveis entre todos os aplicativos concorrentes. O Gerenciador de Recursos garante que todos os recursos estejam sempre em uso, otimizando para várias constantes, como SLAs, garantias de capacidade e assim por diante. O ApplicationMaster negocia recursos do Gerenciador de Recursos e trabalha com o NodeManager para executar e monitorar os contêineres e seu consumo de recursos.

Quando vários locatários compartilham um grande cluster, ocorre uma competição pelos recursos do cluster. O CapacityScheduler é um agendador conectável que auxilia no compartilhamento de recurso através do enfileiramento de solicitações. O CapacityScheduler também dá suporte às *filas hierárquicas* a fim de garantir que os recursos sejam compartilhados entre as subfilas de uma organização, antes que outras filas de aplicativos tenham permissão para usar os recursos livres.

O YARN nos permite alocar recursos para essas filas e mostra se todos os recursos disponíveis estão atribuídos. Para visualizar informações sobre suas filas, entre na interface do usuário do Ambari Web e selecione **YARN Queue Manager** no menu superior.

![Gerenciador de Filas do YARN](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager.png)

A página do Gerenciador de Filas do YARN mostra uma lista de suas filas à esquerda, junto com o percentual da capacidade atribuída a cada uma.

![Página de detalhes do Gerenciador de Filas do YARN](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager-details.png)

Para obter informações mais detalhada sobre seu filas, no painel do Ambari, selecione o serviço **YARN** na lista à esquerda. Em seguida, no menu suspenso **Links rápidos**, selecione **Interface do usuário do Gerenciador de Recursos** abaixo do nó ativo.

![Link do menu Gerenciador de Recursos de UI](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu.png)

Na interface do usuário do Gerenciador de Recursos, selecione **Agendador** no menu à esquerda. Você vê uma lista com as suas filas em *Filas de Aplicativo*. Aqui você pode ver a capacidade usada para cada uma das suas filas, como os trabalhos estão distribuídos entre elas e se algum trabalho está com limitação de recursos.

![Link do menu Gerenciador de Recursos de UI](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui.png)

## <a name="storage-throttling"></a>Limitação de armazenamento

O gargalo do desempenho de um cluster pode ocorrer no nível de armazenamento. Esse tipo de gargalo geralmente é devido ao *bloqueio* de operações de E/S (entrada/saída), o que ocorre quando as tarefas em execução enviam mais E/S do que o serviço de armazenamento pode manipular. Esse bloqueio cria uma fila de solicitações de E/S aguardando para serem processadas até que as E/Ss atuais sejam processadas. Os bloqueios são devidos à *limitação de armazenamento*, que não é um limite físico, mas um limite imposto pelo serviço de armazenamento através de um SLA (Contrato de Nível de Serviço). Esse limite serve para garantir que um único cliente ou locatário não monopolize o serviço. O SLA limita o número de IOPS (E/S por segundo) para o Armazenamento do Azure – para mais detalhes, consulte [Escalabilidade e metas de desempenho do Armazenamento do Azure](https://docs.microsoft.com/azure/storage/storage-scalability-targets).

Caso você esteja usando o Armazenamento do Azure, para obter informações sobre monitoramento de problemas relacionados ao armazenamento, incluindo a limitação, consulte [Monitorar, diagnosticar e solucionar problemas do Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting).

Se o repositório de backup do seu cluster for o ADLS (Azure Data Lake Storage), a limitação será provavelmente devida a limites de largura de banda. A limitação, nesse caso, poderia ser identificada pela observação de erros de limitação nos logs de tarefa. Para o ADLS, consulte a seção sobre limitação do serviço apropriado nesses artigos:

* [Diretrizes de ajuste do desempenho para o Apache Hive no HDInsight e Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-hive.md)
* [Diretrizes de ajuste do desempenho para o MapReduce no HDInsight e Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-mapreduce.md)
* [Diretrizes de ajuste do desempenho para o Apache Storm no HDInsight e Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-storm.md)

## <a name="next-steps"></a>Próximas etapas

Visite os links a seguir para obter mais informações sobre o monitoramento e a solução de problemas dos seus clusters:

* [Analisar logs do HDInsight](hdinsight-debug-jobs.md)
* [Depurar aplicativos com logs do YARN do Apache Hadoop](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Habilitar despejos de heap para serviços do Apache Hadoop no HDInsight baseado em Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
