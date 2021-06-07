---
title: Agregação de eventos com Linux Diagnóstico do Azure
description: Saiba mais sobre a agregação e coleta de eventos utilizando o LAD para monitoramento e diagnóstico de clusters do Azure Service Fabric.
ms.topic: conceptual
ms.date: 2/25/2019
ms.openlocfilehash: bd5efc6b661bf25ce7946cda0bcfa01be4c351a7
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628927"
---
# <a name="event-aggregation-and-collection-using-linux-azure-diagnostics"></a>Coleta e agregação de eventos utilizando o Diagnóstico do Linux Azure
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Quando você estiver executando um cluster de Service Fabric do Azure, é uma boa ideia coletar os logs de todos os nós em um local central. Ter os logs em um local central ajuda a analisar e solucionar problemas no cluster ou nos aplicativos e serviços em execução nesse cluster.

Uma maneira de carregar e coletar logs é utilizar a extensão LAD (Diagnóstico do Linux Azure) que carrega os logs no Armazenamento do Microsoft Azure e, além disso, possui a opção de enviar os logs para o Azure Application Insights ou Hubs de Evento. Você também pode usar um processo externo para ler os eventos do armazenamento e colocá-los em um produto da plataforma de análise, como [logs de Azure monitor](./service-fabric-diagnostics-oms-setup.md) ou outra solução de análise de log.

## <a name="log-and-event-sources"></a>Origem do evento e log

### <a name="service-fabric-platform-events"></a>Eventos de plataforma do Service Fabric
O Service Fabric emite alguns logs prontos para uso via [LTTng](https://lttng.org), incluindo eventos operacionais ou eventos de runtime. Esses logs são armazenados na localização especificada pelo modelo do Resource Manager do cluster. Para obter ou definir os detalhes da conta de armazenamento, pesquise pela marca **AzureTableWinFabETWQueryable** e procure por **StoreConnectionString**.

### <a name="application-events"></a>Eventos de aplicativo
 Eventos emitidos pelo código de seus serviços e aplicativos, conforme especificado por você durante a instrumentação do software. Você pode usar qualquer solução de log que grave arquivos de log baseados em texto, por exemplo, LTTng. Para mais informações, consulte a documentação do LTTng no rastreamento do aplicativo.

[Monitorar e diagnosticar serviços em uma configuração de desenvolvimento de computador local](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).

## <a name="deploy-the-diagnostics-extension"></a>Implantar a extensão de Diagnóstico
A primeira etapa para coletar logs será implantar a extensão Diagnóstico em cada uma das VMs no cluster do Service Fabric. A extensão de Diagnóstico coleta logs em cada VM e os carrega para a conta de armazenamento especificada. 

Para implantar a extensão de diagnóstico nas VMs no cluster como parte da criação do cluster, defina **Diagnóstico** como **Ativado**. Depois de criar o cluster, você não pode alterar essa configuração usando o portal, portanto, você precisará fazer as alterações apropriadas no modelo do Gerenciador de Recursos.

Isso configura o agente LAD para monitorar os arquivos de log especificados. Sempre que uma nova linha for anexada ao arquivo, ela cria uma entrada de syslog que é enviada para o armazenamento (tabela) especificado.


## <a name="next-steps"></a>Próximas etapas

1. Confira [Documentação do LTTng](https://lttng.org/docs) e [Usando o LAD](../virtual-machines/extensions/diagnostics-linux.md) para entender mais detalhadamente quais eventos você deve examinar ao solucionar problemas.
2. [Configurar o agente do Log Analytics](service-fabric-diagnostics-event-analysis-oms.md) para ajudar a coletar métricas, monitorar Contêineres implantados em seu cluster e visualizar seus logs 
