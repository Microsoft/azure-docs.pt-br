---
title: Ver dependências de aplicativo com o Azure Monitor para VMs
description: O MAP é um recurso do Azure Monitor para VMs. Ele descobre automaticamente os componentes de aplicativos em sistemas Windows e Linux e mapeia a comunicação entre os serviços. Este artigo fornece detalhes sobre como usar o recurso de mapa em vários cenários.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2020
ms.openlocfilehash: 32ad92b333eda8cd429303177dba160f0b58af54
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95985791"
---
# <a name="use-the-map-feature-of-azure-monitor-for-vms-to-understand-application-components"></a>Usar o recurso de mapa do Azure Monitor para VMs para entender os componentes do aplicativo
No Azure Monitor para VMs, você pode exibir componentes de aplicativos descobertos em VMs (máquinas virtuais) do Windows e Linux que são executadas no Azure ou no seu ambiente. Você pode observar as VMs de duas maneiras. Exiba um mapa diretamente de uma VM ou exiba um mapa de Azure Monitor para ver os componentes em grupos de VMs. Este artigo o ajudará a entender esses dois métodos de exibição e como usar o recurso de mapa. 

Para obter informações sobre como configurar o Monitor do Azure para VMs, consulte [Ativar o Monitor do Azure para VMs](vminsights-enable-overview.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure
Entre no [portal do Azure](https://portal.azure.com).

## <a name="introduction-to-the-map-experience"></a>Introdução à experiência de mapa
Antes de mergulhar na experiência de mapa, você deve entender como ele apresenta e visualiza informações. Se você selecionar o recurso de mapa diretamente de uma VM ou de Azure Monitor, o recurso de mapa apresentará uma experiência consistente. A única diferença é que, de Azure Monitor, um mapa mostra todos os membros de um aplicativo ou cluster de várias camadas.

O recurso de mapa visualiza as dependências de VM ao descobrir processos em execução que têm: 

- Conexões de rede ativas entre servidores.
- Latência de conexão de entrada e de saída.
- Portas em qualquer arquitetura conectada a TCP em um intervalo de tempo especificado.  
 
Expanda uma VM para mostrar os detalhes do processo e somente os processos que se comunicam com a VM. O grupo de clientes mostra a contagem de clientes de front-end que se conectam à VM. Os grupos de portas de servidor mostram a contagem de servidores back-end aos quais a VM se conecta. Expanda um grupo de portas de servidor para ver a lista detalhada de servidores que se conectam através dessa porta.  

Quando você seleciona a VM, o painel **Propriedades** à direita mostra as propriedades da VM. As propriedades incluem informações do sistema relatadas pelo sistema operacional, propriedades da VM do Azure e um gráfico de rosca que resume as conexões descobertas. 

![O painel Propriedades](./media/vminsights-maps/properties-pane-01.png)

No lado direito do painel, selecione eventos de **log** para mostrar uma lista de dados que a VM enviou para Azure monitor. Esses dados estão disponíveis para consulta.  Selecione qualquer tipo de registro para abrir a página **logs** , onde você vê os resultados para esse tipo de registro. Você também verá uma consulta pré-configurada que é filtrada em relação à VM.  

![O painel eventos de log](./media/vminsights-maps/properties-pane-logs-01.png)

Feche a página **logs** e retorne ao painel **Propriedades** . Lá, selecione **alertas** para exibir alertas de critérios de integridade da VM. O recurso de mapa integra-se com os alertas do Azure para mostrar alertas para o servidor selecionado no intervalo de tempo selecionado. O servidor exibe um ícone para os alertas atuais e o painel de **alertas do computador** lista os alertas. 

![O painel alertas](./media/vminsights-maps/properties-pane-alerts-01.png)

Para fazer com que o recurso de mapa exiba alertas relevantes, crie uma regra de alerta que se aplique a um computador específico:

- Inclua uma cláusula para agrupar alertas por computador (por exemplo, **por intervalo de computador 1 minuto**).
- Basear o alerta em uma métrica.

Para obter mais informações sobre alertas do Azure e criar regras de alerta, consulte [alertas unificados no Azure monitor](../platform/alerts-overview.md).

No canto superior direito, a opção **legenda** descreve os símbolos e as funções no mapa. Para uma análise mais detalhada do mapa e para movê-lo, use os controles de zoom no canto inferior direito. Você pode definir o nível de zoom e ajustar o mapa ao tamanho da página.  

## <a name="connection-metrics"></a>Métricas de conexão
O painel **conexões** exibe as métricas padrão para a conexão selecionada da VM na porta TCP. As métricas incluem tempo de resposta, solicitações por minuto, taxa de transferência de tráfego e links.  

![Gráficos de conectividade de rede no painel conexões](./media/vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>Conexões com falha
O mapa mostra conexões com falha para processos e computadores. Uma linha vermelha tracejada indica que um sistema cliente está falhando em alcançar um processo ou uma porta. Para sistemas que usam o Dependency Agent, o agente relata sobre tentativas de conexão com falha. O recurso de mapa monitora um processo observando os soquetes TCP que falham ao estabelecer uma conexão. Essa falha pode resultar de um firewall, uma configuração incorreta no cliente ou servidor ou um serviço remoto indisponível.

![Uma conexão com falha no mapa](./media/vminsights-maps/map-group-failed-connection-01.png)

A compreensão das conexões com falha pode ajudá-lo a solucionar problemas, validar a migração, analisar a segurança e compreender a arquitetura geral do serviço. As conexões com falha são algumas vezes inofensivas, mas elas geralmente apontam para um problema. As conexões podem falhar, por exemplo, quando um ambiente de failover fica inacessível repentinamente ou quando duas camadas de aplicativo não podem se comunicar entre si após uma migração de nuvem.

### <a name="client-groups"></a>Grupos de clientes
No mapa, os grupos de clientes representam computadores cliente que se conectam ao computador mapeado. Um único grupo de clientes representa os clientes de um computador ou processo individual.

![Um grupo de clientes no mapa](./media/vminsights-maps/map-group-client-groups-01.png)

Para ver os clientes monitorados e os endereços IP dos sistemas em um grupo de clientes, selecione o grupo. O conteúdo do grupo aparece abaixo.  

![Lista de endereços IP de um grupo de clientes no mapa](./media/vminsights-maps/map-group-client-group-iplist-01.png)

Se o grupo incluir clientes monitorados e não monitorados, você poderá selecionar a seção apropriada do gráfico de rosca do grupo para filtrar os clientes.

### <a name="server-port-groups"></a>Grupos de portas de servidor
Os grupos de portas de servidor representam portas em servidores que têm conexões de entrada do computador mapeado. O grupo contém a porta do servidor e uma contagem do número de servidores que têm conexões com essa porta. Selecione o grupo para ver os servidores e conexões individuais. 

![Um grupo de portas de servidor no mapa](./media/vminsights-maps/map-group-server-port-groups-01.png)  

Se o grupo incluir servidores monitorados e não monitorados, você poderá selecionar a seção apropriada do gráfico de rosca do grupo para filtrar os servidores.

## <a name="view-a-map-from-a-vm"></a>Exibir um mapa de uma VM 

Para acessar Azure Monitor para VMs diretamente de uma VM:

1. No Portal do Azure, selecione **Máquinas Virtuais**. 
2. Na lista, escolha uma VM. Na seção **monitoramento** , escolha **insights**.  
3. Selecione a guia **Mapa**.

O mapa visualiza as dependências da VM ao descobrir a execução de processos e grupos de processos que têm conexões de rede ativas em um intervalo de tempo especificado.  

Por padrão, o mapa mostra os últimos 30 minutos. Se você quiser ver como as dependências foram examinadas no passado, poderá consultar intervalos de tempo históricos de até uma hora. Para executar a consulta, use o seletor de **intervalo de timerange** no canto superior esquerdo. Você pode executar uma consulta, por exemplo, durante um incidente ou para ver o status antes de uma alteração.  

![Captura de tela da guia mapa na seção monitoramento de informações do portal do Azure mostrando um diagrama das dependências entre as máquinas virtuais.](./media/vminsights-maps/map-direct-vm-01.png)

## <a name="view-a-map-from-a-virtual-machine-scale-set"></a>Exibir um mapa de um conjunto de dimensionamento de máquinas virtuais

Para acessar Azure Monitor para VMs diretamente de um conjunto de dimensionamento de máquinas virtuais:

1. No portal do Azure, selecione **Conjuntos de dimensionamento de máquinas virtuais**.
2. Na lista, escolha uma VM. Em seguida, na seção **monitoramento** , escolha **insights**.  
3. Selecione a guia **Mapa**.

O mapa visualiza todas as instâncias no conjunto de dimensionamento como um nó de grupo junto com as dependências do grupo. O nó expandido lista as instâncias no conjunto de dimensionamento. Você pode percorrer essas instâncias 10 por vez. 

Para carregar um mapa para uma instância específica, primeiro selecione essa instância no mapa. Em seguida, selecione o botão de **reticências** (...) à direita e escolha **carregar mapa do servidor**. No mapa que aparece, você vê processos e grupos de processo que têm conexões de rede ativas em um intervalo de tempo especificado. 

Por padrão, o mapa mostra os últimos 30 minutos. Se você quiser ver como as dependências foram examinadas no passado, poderá consultar intervalos de tempo históricos de até uma hora. Para executar a consulta, use o seletor de **intervalo de timerange** . Você pode executar uma consulta, por exemplo, durante um incidente ou para ver o status antes de uma alteração.

![Captura de tela da guia mapa na seção monitoramento de informações do portal do Azure mostrando um diagrama de dependências entre conjuntos de dimensionamento de máquinas virtuais.](./media/vminsights-maps/map-direct-vmss-01.png)

>[!NOTE]
>Você também pode acessar um mapa para uma instância específica do modo de exibição de **instâncias** para o conjunto de dimensionamento de máquinas virtuais. Na seção **configurações** , vá para **instâncias**  >  **percepções**.

## <a name="view-a-map-from-azure-monitor"></a>Exibir um mapa do Azure Monitor

No Azure Monitor, o recurso de mapa fornece uma exibição global de suas VMs e suas dependências. Para acessar o recurso de mapa no Azure Monitor:

1. No portal do Azure, selecione **Monitor**. 
2. Na seção **insights** , escolha **máquinas virtuais**.
3. Selecione a guia **Mapa**.

   ![Azure Monitor mapa de visão geral de várias VMs](./media/vminsights-maps/map-multivm-azure-monitor-01.png)

Escolha um espaço de trabalho usando o seletor de **espaço de trabalho** na parte superior da página. Se você tiver mais de um espaço de trabalho Log Analytics, escolha o espaço de trabalho que está habilitado com a solução e que tem VMs subordinadas a ele. 

O seletor de **grupo** retorna assinaturas, grupos de recursos, [grupos](../platform/computer-groups.md)de computadores e conjuntos de dimensionamento de máquinas virtuais de computadores relacionados ao espaço de trabalho selecionado. Sua seleção aplica-se somente ao recurso de mapa e não é transportada para desempenho ou integridade.

Por padrão, o mapa mostra os últimos 30 minutos. Se você quiser ver como as dependências foram examinadas no passado, poderá consultar intervalos de tempo históricos de até uma hora. Para executar a consulta, use o seletor de **intervalo de timerange** . Você pode executar uma consulta, por exemplo, durante um incidente ou para ver o status antes de uma alteração.  

## <a name="next-steps"></a>Próximas etapas

Para identificar afunilamentos, verificar o desempenho e entender a utilização geral de suas VMs, consulte [Exibir status de desempenho para Azure monitor para VMs](vminsights-performance.md). 

