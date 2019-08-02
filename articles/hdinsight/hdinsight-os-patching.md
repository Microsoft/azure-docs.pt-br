---
title: Configurar a aplicação de patch no agendamento para clusters HDInsight baseados em Linux – Azure
description: Saiba como configurar o agendamento de aplicação de patch no SO para clusters HDInsight baseados em Linux.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: efe74618b269000749f7ba6c24d35903e540dcfb
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657046"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Configurar a aplicação de patch no agendamento para clusters HDInsight baseados em Linux 

> [!IMPORTANT]
> Imagens do Ubuntu ficam disponíveis para a nova criação do cluster HDInsight do Azure em três meses de que está sendo publicado. A partir de janeiro de 2019, clusters em execução não são o patch automaticamente. Os clientes devem usar ações de script ou outros mecanismos para aplicar o patch de um cluster em execução. Clusters recém-criados sempre terão as atualizações mais recentes disponíveis, incluindo os mais recentes patches de segurança.

Ocasionalmente, você deve reiniciar máquinas virtuais (VMs) em um cluster do HDInsight para instalar os patches de segurança importantes.

Usando as ações de script descritas neste artigo, você pode modificar a aplicação de patch no agendamento da seguinte maneira:

1. Instale todas as atualizações, ou apenas kernel + atualizações de segurança ou atualizações de kernel.
2. Fazer uma reinicialização imediata ou agendar uma reinicialização na VM.

> [!NOTE]  
> As ações de script descritas neste artigo funcionam apenas com clusters de HDInsight baseados em Linux criados após 1º de agosto de 2016. Patches estão em vigor somente após a reinicialização automáticos das VMs.
> Ações de script não aplicam automaticamente as atualizações para todos os ciclos de atualização futura. Execute os scripts sempre que novas atualizações devem ser aplicadas para instalar as atualizações e, em seguida, reinicie a VM.

## <a name="add-information-to-the-script"></a>Adicionar informações ao script

Usando um script requer as seguintes informações:

- Localização do script install-atualizações-agenda-reinicializações: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh.
    
   HDInsight usa esse URI para localizar e executar o script em todas as VMs no cluster. Esse script fornece opções para instalar atualizações e reinicie a VM.
  
- Localização do script as reinicializações de agenda: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh.
    
   HDInsight usa esse URI para localizar e executar o script em todas as VMs no cluster. Esse script reinicia a VM.
  
- Os tipos de nós de cluster que o script seja aplicado a são o nó de cabeçalho, nó de trabalho e zookeeper. Aplica o script a todos os tipos de nó no cluster. Se o script não é aplicado a um tipo de nó, as VMs para esse tipo de nó não ser atualizadas ou reiniciadas.

- O script install-atualizações de agenda reinicializações aceita dois parâmetros numéricos:

    | Parâmetro | Definição |
    | --- | --- |
    | Instalar somente atualizações de kernel / instalar todas as atualizações/instalar kernel + segurança atualiza apenas|0, 1 ou 2. Um valor de 0 instala somente as atualizações de kernel. Um valor de 1 instala todas as atualizações e o kernel de apenas 2 instalações + atualizações de segurança. Se nenhum parâmetro for fornecido, o padrão é 0. |
    | Nenhuma reinicialização imediata do reinicialização/habilitar agendamento reinicialização/habilitar |0, 1 ou 2. Um valor 0 desabilita a reinicialização. Um valor de 1 permite que a reinicialização de agendamento e 2 permite que a reinicialização imediata. Se nenhum parâmetro for fornecido, o padrão é 0. O usuário deve alterar o parâmetro de entrada 1 para 2 de parâmetro de entrada. |
   
 - O script de reinicializações de agendamento aceita um parâmetro numérico:

    | Parâmetro | Definição |
    | --- | --- |
    | Habilitar a reinicialização imediata de reinicialização/habilitar agendamento |1 ou 2. Um valor de 1 permite que a reinicialização da agenda (agendada em 12-24 horas). Um valor de 2 permite que a reinicialização imediata (em 5 minutos). Se nenhum parâmetro for fornecido, o padrão é 1. |  

> [!NOTE]
> Você deve marcar um script como persistente depois de aplicar a um cluster existente. Caso contrário, quaisquer nós novos criado por meio de operações de dimensionamento usarão o agendamento de aplicação de patch padrão. Se você aplicar o script como parte do processo de criação do cluster, ele ser mantido automaticamente.


## <a name="next-steps"></a>Próximas etapas

Para etapas específicas sobre como usar ações de script, consulte as seções a seguir [HDInsight baseado em Linux personalizar clusters usando a ação de script](hdinsight-hadoop-customize-cluster-linux.md):

* [Usar uma ação de script durante a criação do cluster](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Aplicar uma ação de script em um cluster em execução](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
