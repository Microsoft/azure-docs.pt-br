---
title: "Monitorar o desempenho do cluster – Azure HDInsight | Microsoft Docs"
description: Como monitorar um cluster HDInsight quanto a capacidade e desempenho.
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
ms.date: 09/27/2017
ms.author: maxluk
ms.openlocfilehash: f2333202cdccc82edea649ff1c295752a414c8b8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-cluster-performance"></a>Monitorar o desempenho do cluster

O monitoramento da integridade e do desempenho de um cluster HDInsight é essencial para manter o desempenho máximo e a utilização de recursos. O monitoramento também pode ajudá-lo a resolver possíveis erros de codificação ou de configuração de cluster.

As seções a seguir descrevem como otimizar o carregamento de cluster, a eficiência de fila YARN e a acessibilidade do armazenamento.

## <a name="cluster-loading"></a>Carregamento de cluster

Os clusters do Hadoop devem balancear o carregamento entre os nós do cluster. Esse balanceamento impede que as tarefas em processamento sejam limitadas por recursos de disco, CPU ou de RAM.

Para obter uma visão de alto nível dos nós do cluster e os respectivos carregamentos, faça logon na [Interface do usuário da Web do Ambari](hdinsight-hadoop-manage-ambari.md) e, em seguida, selecione a guia **Hosts**. Os hosts são listados por seus nomes de domínio totalmente qualificados. O status operacional de cada host é mostrado por um indicador de integridade colorido:

| Cor | Descrição |
| --- | --- |
| Vermelho | Pelo menos um componente mestre no host está inoperante. Passe o mouse para ver uma dica de ferramenta que lista os componentes afetados. |
| Laranja | Pelo menos um componente subordinado no host está inoperante. Passe o mouse para ver uma dica de ferramenta que lista os componentes afetados. |
| Amarelo | O Servidor Ambari não recebeu uma pulsação do host há mais de 3 minutos. |
| Verde | Estado de execução normal. |

Você também verá colunas mostrando o número de núcleos e a quantidade de RAM para cada host, bem como o uso do disco e a carga média.

![Guia Hosts](./media/hdinsight-key-scenarios-to-monitor/hosts-tab.png)

Selecione qualquer um dos nomes de host para ter uma visão detalhada dos componentes em execução nesse host, bem como suas métricas. As métricas são mostradas como uma linha do tempo selecionável de uso de CPU, carga, uso do disco, uso de memória, uso de rede e números de processos.

![Detalhes do host](./media/hdinsight-key-scenarios-to-monitor/host-details.png)

Consulte [Gerenciar clusters HDInsight usando a interface do usuário da Web do Ambari](hdinsight-hadoop-manage-ambari.md) para obter detalhes sobre a configuração de alertas e exibição de métricas.

## <a name="yarn-queue-configuration"></a>Configuração de fila do YARN

O Hadoop tem vários serviços em execução em sua plataforma distribuída. O Yet Another Resource Negotiator (YARN) coordena esses serviços, aloca recursos de cluster e gerencia o acesso a um conjunto de dados comum.

O YARN divide as duas responsabilidades do JobTracker, gerenciamento de recursos e agendamento/monitoramento de trabalho, em dois daemons: um ResourceManager global e um AM (ApplicationMaster) por aplicativo.

O ResourceManager é um *agendador puro* e somente arbitra os recursos disponíveis entre todos os aplicativos concorrentes. O ResourceManager garante que todos os recursos estejam sempre em uso, otimizando para várias constantes como SLAs, garantias de capacidade e assim por diante. O ApplicationMaster negocia recursos do ResourceManager e trabalha com o NodeManager(s) para executar e monitorar os contêineres e os respectivos consumos de recursos.

Quando vários locatários compartilham um grande cluster, ocorre uma competição pelos recursos do cluster. O CapacityScheduler é um agendador conectável que auxilia no compartilhamento de recurso através do enfileiramento de solicitações. O CapacityScheduler também dá suporte às *filas hierárquicas* a fim de garantir que os recursos sejam compartilhados entre as subfilas de uma organização, antes que outras filas de aplicativos tenham permissão para usar os recursos livres.

O YARN nos permite alocar recursos para essas filas e mostra se todos os recursos disponíveis estão atribuídos. Para exibir informações sobre as filas, faça logon na interface do usuário da Web do Ambari e selecione **Gerenciador de Filas do YARN** no menu superior.

![Gerenciador de Filas do YARN](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager.png)

A página do Gerenciador de Filas do YARN mostra uma lista de suas filas à esquerda, junto com o percentual da capacidade atribuída a cada uma.

![Página de detalhes do Gerenciador de Filas do YARN](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager-details.png)

Para obter informações mais detalhada sobre seu filas, no painel do Ambari, selecione o serviço **YARN** na lista à esquerda. Então, no menu suspenso **Links Rápidos**, selecione **Interface do usuário do ResourceManager** sob o nó ativo.

![Link do menu interface do usuário do ResourceManager](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu.png)

Na interface do usuário do ResourceManager, selecione **Agendador** no menu à esquerda. Você vê uma lista com as suas filas em *Filas de Aplicativo*. Aqui você pode ver a capacidade usada para cada uma das suas filas, como os trabalhos estão distribuídos entre elas e se algum trabalho está com limitação de recursos.

![Link do menu interface do usuário do ResourceManager](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui.png)

## <a name="storage-throttling"></a>Limitação de armazenamento

O afunilamento do desempenho de um cluster pode ocorrer no nível de armazenamento. Esse tipo de afunilamento geralmente é devido ao *bloqueio* de operações de E/S (entrada/saída), o que ocorre quando as tarefas em execução enviam mais E/S do que o serviço de armazenamento pode manipular. Esse bloqueio cria uma fila de solicitações de E/S aguardando para serem processadas até que as E/Ss atuais sejam processadas. Os bloqueios são devidos à *limitação de armazenamento*, que não é um limite físico, mas um limite imposto pelo serviço de armazenamento através de um SLA (Contrato de Nível de Serviço). Esse limite serve para garantir que um único cliente ou locatário não monopolize o serviço. O SLA limita o número de IOPS (E/S por segundo) para o Armazenamento do Azure – para mais detalhes, consulte [Escalabilidade e metas de desempenho do Armazenamento do Azure](https://docs.microsoft.com/azure/storage/storage-scalability-targets).

Caso você esteja usando o Armazenamento do Azure, para obter informações sobre monitoramento de problemas relacionados ao armazenamento, incluindo a limitação, consulte [Monitorar, diagnosticar e solucionar problemas do Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting).

Se o repositório de backup do seu cluster é o ADLS (Azure Data Lake Store), a limitação é provavelmente devida a limites de largura de banda. A limitação, nesse caso, poderia ser identificada pela observação de erros de limitação nos logs de tarefa. Para o ADLS, consulte a seção sobre limitação do serviço apropriado nesses artigos:

* [Diretrizes de ajuste do desempenho para o Hive no HDInsight e Azure Data Lake Store](../data-lake-store/data-lake-store-performance-tuning-hive.md)
* [Diretrizes de ajuste do desempenho para o MapReduce no HDInsight e Azure Data Lake Store](../data-lake-store/data-lake-store-performance-tuning-mapreduce.md)
* [Diretrizes de ajuste do desempenho para o Storm no HDInsight e Azure Data Lake Store](../data-lake-store/data-lake-store-performance-tuning-storm.md)

## <a name="next-steps"></a>Próximas etapas

Visite os links a seguir para obter mais informações sobre o monitoramento e a solução de problemas dos seus clusters:

* [Analisar logs do HDInsight](hdinsight-debug-jobs.md)
* [Depurar aplicativos com logs do YARN](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Habilitar despejos heap para serviços do Hadoop no HDInsight baseado em Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
