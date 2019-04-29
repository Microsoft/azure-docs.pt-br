---
title: Configurar o agendamento de aplicação de patch no SO para clusters Azure HDInsight baseados em Linux
description: Saiba como configurar o agendamento de aplicação de patch no SO para clusters HDInsight baseados em Linux.
author: omidm1
ms.author: omidm
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: a887d6c69b9fa80f3144434e72a097e80d123a1b
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766482"
---
# <a name="os-patching-for-hdinsight"></a>Aplicação de patch no HDInsight 

> [!IMPORTANT]
> As imagens do Ubuntu ficam disponíveis para a criação do novo cluster HDInsight dentro de 3 meses depois de publicado. A partir de janeiro de 2019, os clusters em execução **não** contam com patch automaticamente. Os clientes devem usar ações de script ou outros mecanismos para aplicar o patch de um cluster em execução. Clusters recém-criados sempre terão as atualizações mais recentes disponíveis, incluindo os mais recentes patches de segurança.

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Como configurar o agendamento de aplicação de patch no SO para clusters HDInsight baseados em Linux
As máquinas virtuais em um cluster HDInsight precisam ser reinicializadas ocasionalmente para que os patches de segurança importantes possam ser instalados. 

Com a ação de script descrita neste artigo, você pode modificar a agenda de aplicação de patch no SO da seguinte maneira:
1. Habilitar ou desabilitar reinicializações automáticas
2. Definir a frequência de reinicializações (dias entre reinicializações)
3. Definir o dia da semana no qual uma reinicialização ocorre

> [!NOTE]  
> Essa ação de script só funcionará com clusters HDInsight baseados em Linux criados após 1º de agosto de 2016. Os patches terão efeito somente após a reinicialização das VMs. 

## <a name="how-to-use-the-script"></a>Como usar o script 

Ao usar esse script você precisará das seguintes informações:
1. O local do script: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv01/os-patching-reboot-config.sh.  O HDInsight usa esse URI para localizar e executar o script em todas as máquinas virtuais no cluster.
  
2. Os tipos de nós do cluster ao qual o script é aplicado: nó de cabeçalho, nó de trabalho, zookeeper. Esse script deve ser aplicado a todos os tipos de nó do cluster. Se não for aplicado a um tipo de nó, as máquinas virtuais desse tipo de nó continuarão a usar a agenda anterior de aplicação de patches.


3.  Parâmetro: Esse script aceita três parâmetros numéricos:

    | Parâmetro | Definição |
    | --- | --- |
    | Habilitar/desabilitar reinicializações automáticas |0 ou 1. Um valor 0 desabilita reinicializações automáticas, enquanto 1 habilita as reinicializações automáticas. |
    | Frequência |7 a 90 (inclusive). O número de dias a aguardar antes de reinicializar as máquinas virtuais, para patches que exijam uma reinicialização. |
    | Dia da semana |1 a 7 (inclusive). Um valor 1 indica que a reinicialização deve ocorrer em uma segunda-feira, e 7 indica domingo. For exemplo, usar os parâmetros 1 60 2 resulta em reinicializações automáticas a cada 60 dias (no máximo), às terças-feiras. |
    | Persistência |Ao aplicar uma ação de script em um cluster existente, você pode marcar o script como persistente. Os scripts persistentes são aplicados quando novos nós de trabalho são adicionados ao cluster por meio de operações de dimensionamento. |

> [!NOTE]  
> Marque esse script como persistente ao aplicar a um cluster existente. Caso contrário, quaisquer nós novos criado por meio de operações de dimensionamento usarão o agendamento de aplicação de patch padrão.  Se você aplicar o script como parte do processo de criação de cluster, ele será persistido automaticamente.

## <a name="next-steps"></a>Próximas etapas

Para conhecer as etapas específicas sobre como usar a ação de script, confira as seções a seguir em [Personalizar clusters HDInsight baseados em Linux usando a ação de script](hdinsight-hadoop-customize-cluster-linux.md):

* [Usar uma ação de script durante a criação do cluster](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Aplicar uma ação de script em um cluster em execução](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
