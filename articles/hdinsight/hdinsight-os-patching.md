---
title: "Configurar o agendamento de aplicação de patch no SO para clusters HDInsight baseados em Linux – Azure | Microsoft Docs"
description: "Saiba como configurar o agendamento de aplicação de patch no SO para clusters HDInsight baseados em Linux."
services: hdinsight
documentationcenter: 
author: bprakash
manager: asadk
editor: bprakash
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/21/2017
ms.author: bhanupr
ms.openlocfilehash: af3c5a19ae8e2e606e4b0506f9f6dddb41192e40
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="os-patching-for-hdinsight"></a>Aplicação de patch no HDInsight 
Como um serviço Hadoop gerenciado, o HDInsight se encarrega da aplicação de patch no SO das VMs subjacentes usadas por clusters HDInsight. Desde 1º de agosto de 2016, alteramos a política de aplicação de patch no SO convidado para clusters HDInsight baseados em Linux (versão 3.4 ou superior). O objetivo da nova política é reduzir significativamente o número de reinicializações devido à aplicação de patch. A nova política continuará corrigindo VMs (máquinas virtuais) em clusters Linux todas as segundas-feiras ou quintas-feiras, começando às 0:00 UTC de maneira irregular entre nós em qualquer cluster determinado. No entanto, qualquer determinada VM será reinicializada somente, no máximo, uma vez a cada 30 dias devido à aplicação de patch do SO convidado. Além disso, a primeira reinicialização para um cluster recém-criado não ocorrerá antes de 30 dias a contar da data de criação do cluster. Os patches terão efeito após a reinicialização das VMs.

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Como configurar o agendamento de aplicação de patch no SO para clusters HDInsight baseados em Linux
As máquinas virtuais em um cluster HDInsight precisam ser reinicializadas ocasionalmente para que os patches de segurança importantes possam ser instalados. Desde 1º de agosto de 2016, novos clusters HDInsight baseados em Linux (versão 3.4 ou superior) são reinicializados usando a seguinte agenda:

1. Uma máquina virtual no cluster só pode ser reinicializada para patches no máximo uma vez a cada 30 dias.
2. A reinicialização começa à 00h UTC.
3. O processo de reinicialização é realizado em estágios entre as máquinas virtuais no cluster, assim o cluster ainda fica disponível durante o processo de reinicialização.
4. A primeira reinicialização de um cluster recém-criado não ocorrerá antes de 30 dias a contar da data de criação do cluster.

Com a ação de script descrita neste artigo, você pode modificar a agenda de aplicação de patch no SO da seguinte maneira:
1. Habilitar ou desabilitar reinicializações automáticas
2. Definir a frequência de reinicializações (dias entre reinicializações)
3. Definir o dia da semana no qual uma reinicialização ocorre

> [!NOTE]
> Essa ação de script só funcionará com clusters HDInsight baseados em Linux criados após 1º de agosto de 2016. Os patches terão efeito somente após a reinicialização das VMs. 
>

## <a name="how-to-use-the-script"></a>Como usar o script 

Ao usar esse script você precisará das seguintes informações:
1. O local de script: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv01/os-patching-reboot-config.sh.  O HDInsight usa esse URI para localizar e executar o script em todas as máquinas virtuais no cluster.
  
2. Os tipos de nós do cluster ao qual o script é aplicado: nó de cabeçalho, nó de trabalho, zookeeper. Esse script deve ser aplicado a todos os tipos de nó do cluster. Se não for aplicado a um tipo de nó, as máquinas virtuais desse tipo de nó continuarão a usar a agenda anterior de aplicação de patches.


3.  Parâmetro: esse script aceita três parâmetros numéricos:

    | . | Definição |
    | --- | --- |
    | Habilitar/desabilitar reinicializações automáticas |0 ou 1. Um valor 0 desabilita reinicializações automáticas, enquanto 1 habilita as reinicializações automáticas. |
    | Frequência |7 a 90 (inclusive). O número de dias a aguardar antes de reinicializar as máquinas virtuais, para patches que exijam uma reinicialização. |
    | Dia da semana |1 a 7 (inclusive). Um valor 1 indica que a reinicialização deve ocorrer em uma segunda-feira, e 7 indica domingo. For exemplo, usar os parâmetros 1 60 2 resulta em reinicializações automáticas a cada 60 dias (no máximo), às terças-feiras. |
    | Persistência |Ao aplicar uma ação de script em um cluster existente, você pode marcar o script como persistente. Os scripts persistentes são aplicados quando novos nós de trabalho são adicionados ao cluster por meio de operações de dimensionamento. |

> [!NOTE]
> Marque esse script como persistente ao aplicar a um cluster existente. Caso contrário, quaisquer nós novos criado por meio de operações de dimensionamento usarão o agendamento de aplicação de patch padrão.
Se você aplicar o script como parte do processo de criação de cluster, ele será persistido automaticamente.
>

## <a name="next-steps"></a>Próximas etapas

Para conhecer as etapas específicas sobre como usar a ação de script, confira as seções a seguir em [Personalizar clusters HDInsight baseados em Linux usando a ação de script](hdinsight-hadoop-customize-cluster-linux.md):

* [Usar uma ação de script durante a criação do cluster](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Aplicar uma ação de script em um cluster em execução](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
