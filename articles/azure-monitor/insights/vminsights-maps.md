---
title: Como exibir dependências de aplicativo com o Azure Monitor para VMs (versão prévia) | Microsoft Docs
description: Mapa é um recurso do Azure Monitor para VMs. Automaticamente detecta os componentes de aplicativos em sistemas Windows e Linux e mapeia a comunicação entre serviços. Este artigo fornece detalhes sobre como usar o recurso de mapa em vários cenários.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2019
ms.author: magoedte
ms.openlocfilehash: f6273e9b6c7ed0c4685479976343497f01201b0b
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206754"
---
# <a name="use-the-map-feature-of-azure-monitor-for-vms-preview-to-understand-application-components"></a>Usar o recurso de mapa do Azure Monitor para máquinas virtuais (versão prévia) para compreender os componentes de aplicativo
No Azure Monitor para máquinas virtuais, você pode exibir os componentes de aplicativos descobertos no Windows e Linux VMs (máquinas virtuais) que são executados no Azure ou em seu ambiente. Você pode observar as VMs de duas maneiras. Exibir um mapa diretamente de uma VM ou exibir um mapa do Azure Monitor para ver os componentes em grupos de VMs. Este artigo ajudará você a entender esses dois métodos de exibição e como usar o recurso de mapa. 

Para obter informações sobre como configurar o Monitor do Azure para VMs, consulte [Ativar o Monitor do Azure para VMs](vminsights-enable-overview.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure
Entre no [Portal do Azure](https://portal.azure.com).

## <a name="introduction-to-the-map-experience"></a>Introdução à experiência do mapa
Antes de mergulhar na experiência de mapa, você deve compreender como ela apresenta e visualiza informações. Se você selecionar o recurso mapa diretamente de uma VM ou do Azure Monitor, o recurso mapa apresenta uma experiência consistente. A única diferença é que do Azure Monitor, um mapa mostra todos os membros de um aplicativo de várias camadas ou um cluster.

O recurso mapa visualiza as dependências VM com a descoberta de processos em execução que têm: 

- Conexões de rede ativas entre servidores.
- Latência de conexão de entrada e saída.
- Portas em qualquer arquitetura conectada a TCP em um intervalo de tempo especificado.  
 
Expanda uma VM para mostrar somente os processos que se comunicam com a VM e os detalhes do processo. O grupo de cliente mostra a contagem de clientes front-end que se conectam à VM. Os grupos de porta do servidor mostram a contagem de servidores de back-end, que a VM se conecta ao. Expanda um grupo de porta do servidor para ver a lista detalhada dos servidores que se conectam por essa porta.  

Quando você seleciona a máquina virtual, o **propriedades** painel à direita mostra propriedades da VM. Propriedades incluem informações de sistema relatadas pelo sistema operacional, as propriedades da VM do Azure e um gráfico de rosca que resume as conexões de descobertas. 

![O painel Propriedades](./media/vminsights-maps/properties-pane-01.png)

No lado direito do painel, selecione **Log de eventos** para mostrar uma lista de dados que enviou a VM para o Azure Monitor. Esses dados estão disponíveis para consulta.  Selecione qualquer tipo de registro para abrir o **Logs** página, onde você pode ver os resultados para esse tipo de registro. Você também pode ver uma consulta pré-configurada que é filtrada em relação à VM.  

![O painel de eventos de Log](./media/vminsights-maps/properties-pane-logs-01.png)

Fechar o **Logs** página e retornar para o **propriedades** painel. Nele, selecione **alertas** para exibir alertas de critérios de integridade VM. O recurso mapa se integra com os alertas do Azure para mostrar os alertas para o servidor selecionado no intervalo de tempo selecionado. O servidor exibirá um ícone para alertas atuais e o **alertas de máquina** painel lista os alertas. 

![O painel de alertas](./media/vminsights-maps/properties-pane-alerts-01.png)

Para tornar o recurso de mapa para exibir alertas relevantes, crie uma regra de alerta que se aplica a um computador específico:

- Inclua uma cláusula para agrupar alertas por computador (por exemplo, **intervalo 1 minuto por computador**).
- Base de dados de alerta em uma métrica.

Para obter mais informações sobre alertas do Azure e criar regras de alerta, consulte [unificada de alertas no Azure Monitor](../../azure-monitor/platform/alerts-overview.md).

No canto superior direito, o **legenda** opção descreve as funções e os símbolos no mapa. Para uma análise mais detalhada em seu mapa e mova-o, use os controles de zoom no canto inferior direito. Você pode definir o nível de zoom e ajustar o mapa para o tamanho da página.  

## <a name="connection-metrics"></a>Métricas de conexão
O **conexões** painel exibe as métricas padrão para a conexão selecionada da VM pela porta TCP. As métricas incluem tempo de resposta, solicitações por minuto, taxa de transferência de tráfego e links.  

![Gráficos de conectividade de rede no painel conexões](./media/vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>Conexões com falha
O mapa mostra as conexões com falha para processos e computadores. Uma linha vermelha tracejada indica que um sistema cliente não puder acessar um processo ou uma porta. Para sistemas que usam o agente de dependência, o agente de relatórios sobre tentativas de conexão com falha. O recurso mapa monitora um processo, observando os soquetes TCP que falham ao estabelecer uma conexão. Essa falha pode resultar de um firewall, uma configuração errada no cliente ou servidor ou um serviço remoto não está disponível.

![Uma conexão com falha no mapa](./media/vminsights-maps/map-group-failed-connection-01.png)

Noções básicas sobre conexões com falha podem ajudá-lo a solucionar problemas, validar migração, analisar a segurança e entender a arquitetura geral do serviço. Conexões com falha, às vezes, são inofensivas, mas normalmente apontam para um problema. As conexões podem falhar, por exemplo, quando um ambiente de failover se torna inacessível repentinamente ou quando não podem se comunicar umas com as outras duas camadas de aplicativo após uma migração na nuvem.

### <a name="client-groups"></a>Grupos de clientes
No mapa, grupos de clientes representam computadores cliente que se conectar à máquina mapeada. Um grupo de clientes único representa os clientes para um computador ou processo individual.

![Um grupo de clientes no mapa](./media/vminsights-maps/map-group-client-groups-01.png)

Para ver os endereços IP dos sistemas em um grupo de clientes e clientes monitorados, selecione o grupo. O conteúdo do grupo aparece abaixo.  

![Lista do grupo de um cliente de endereços IP no mapa](./media/vminsights-maps/map-group-client-group-iplist-01.png)

Se o grupo inclui clientes não monitorados e monitorados, você pode selecionar a seção apropriada do gráfico de rosca do grupo para filtrar os clientes.

### <a name="server-port-groups"></a>Grupos de porta do servidor
Grupos de porta do servidor representam portas em servidores que têm conexões do computador mapeada de entrada. O grupo contém a porta do servidor e uma contagem do número de servidores que têm conexões para essa porta. Selecione o grupo para ver as conexões e servidores individuais. 

![Um grupo de porta do servidor no mapa](./media/vminsights-maps/map-group-server-port-groups-01.png)  

Se o grupo inclui servidores não monitorados e monitorados, você pode selecionar a seção apropriada do gráfico de rosca do grupo para filtrar os servidores.

## <a name="view-a-map-from-a-vm"></a>Exibir um mapa de uma VM 

Para acessar o Azure Monitor para VMs diretamente de uma VM:

1. No Portal do Azure, selecione **Máquinas Virtuais**. 
2. Na lista, escolha uma máquina virtual. No **Monitoring** , escolha **Insights (visualização)** .  
3. Selecione a guia **Mapa**.

O mapa visualiza as dependências da VM, detectando executando grupos de processos e processos que têm conexões de rede ativas em um intervalo de tempo especificado.  

Por padrão, o mapa mostra os últimos 30 minutos. Se você quiser ver como eram as dependências no passado, você pode consultar para intervalos de tempo históricos de até uma hora. Para executar a consulta, use o **TimeRange** seletor no canto superior esquerdo. Você pode executar uma consulta, por exemplo, durante um incidente ou para ver o status antes de uma alteração.  

![Visão geral do mapa de VM direta](./media/vminsights-maps/map-direct-vm-01.png)

## <a name="view-a-map-from-a-virtual-machine-scale-set"></a>Exibir um mapa de um conjunto de dimensionamento de máquina virtual

Para acessar o Azure Monitor para VMs diretamente de um conjunto de dimensionamento de máquina virtual:

1. No portal do Azure, selecione **conjuntos de dimensionamento de máquina Virtual**.
2. Na lista, escolha uma máquina virtual. Em seguida, nos **Monitoring** , escolha **Insights (visualização)** .  
3. Selecione a guia **Mapa**.

O mapa visualiza todas as instâncias no conjunto de dimensionamento como um nó de grupo juntamente com as dependências do grupo. O nó expandido lista as instâncias no conjunto de dimensionamento. Você pode rolar esses 10 instâncias por vez. 

Para carregar um mapa para uma instância específica, primeiro selecione essa instância do mapa. Em seguida, selecione a **reticências** botão (...) à direita e escolha **carregar mapa do servidor**. O mapa que aparece, você verá a grupos de processos e processos que têm conexões de rede ativas em um intervalo de tempo especificado. 

Por padrão, o mapa mostra os últimos 30 minutos. Se você quiser ver como eram as dependências no passado, você pode consultar para intervalos de tempo históricos de até uma hora. Para executar a consulta, use o **TimeRange** seletor. Você pode executar uma consulta, por exemplo, durante um incidente ou para ver o status antes de uma alteração.

![Visão geral do mapa de VM direta](./media/vminsights-maps/map-direct-vmss-01.png)

>[!NOTE]
>Você também pode acessar um mapa para uma instância específica do **instâncias** modo de exibição para seu conjunto de dimensionamento de máquina virtual. No **as configurações** seção, acesse **instâncias** > **Insights (visualização)** .

## <a name="view-a-map-from-azure-monitor"></a>Exibir um mapa do Azure Monitor
No Azure Monitor, o recurso de mapa fornece uma exibição global de suas VMs e suas dependências. Para acessar o recurso de mapa no Azure Monitor:

1. No portal do Azure, selecione **Monitor**. 
2. No **Insights** , escolha **máquinas virtuais (versão prévia)** .
3. Selecione a guia **Mapa**.

   ![Mapa de visão geral do Monitor do Azure de várias VMs](./media/vminsights-maps/map-multivm-azure-monitor-01.png)

Escolha um espaço de trabalho usando o **espaço de trabalho** seletor na parte superior da página. Se você tiver mais de um espaço de trabalho do Log Analytics, escolha o espaço de trabalho que é habilitada com a solução e que tem VMs subordinados a ele. 

O **grupo** seletor retorna assinaturas, grupos de recursos [grupos de computadores](../../azure-monitor/platform/computer-groups.md)e os conjuntos de dimensionamento de máquina virtual de computadores que estão relacionados ao espaço de trabalho selecionado. Sua seleção se aplica apenas ao recurso de mapa e não é transferida para o desempenho ou integridade.

Por padrão, o mapa mostra os últimos 30 minutos. Se você quiser ver como eram as dependências no passado, você pode consultar para intervalos de tempo históricos de até uma hora. Para executar a consulta, use o **TimeRange** seletor. Você pode executar uma consulta, por exemplo, durante um incidente ou para ver o status antes de uma alteração.  

## <a name="next-steps"></a>Próximas etapas
- Para saber como usar o recurso de integridade, consulte [integridade da VM do Azure de modo de exibição](vminsights-health.md). 
- Para identificar gargalos, verificar o desempenho e entender a utilização geral de suas VMs, consulte [exibir o status de desempenho do Azure Monitor para VMs](vminsights-performance.md). 
