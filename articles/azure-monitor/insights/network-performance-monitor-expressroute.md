---
title: Solução Monitor de Desempenho de Rede no Azure Log Analytics | Microsoft Docs
description: Use a funcionalidade ExpressRoute Monitor no Monitor de Desempenho de Rede para monitorar a conectividade de ponta a ponta e o desempenho entre as filiais e o Azure, por meio do Azure ExpressRoute.
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 11/27/2018
ms.openlocfilehash: 0b005e46e9f9a740fee153e063bebf108c22b303
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101725689"
---
# <a name="expressroute-monitor"></a>ExpressRoute Monitor

> [!IMPORTANT]
> A partir de 1 de julho de 2021, você não poderá adicionar novos testes em um espaço de trabalho existente ou habilitar um novo espaço de trabalho no Monitor de Desempenho de Rede. Você pode continuar a usar os testes criados antes de 1 de julho de 2021. Para minimizar a interrupção do serviço para suas cargas de trabalho atuais, [migre seus testes de monitor de desempenho de rede para o novo monitor de conexão](../../network-watcher/migrate-to-connection-monitor-from-network-performance-monitor.md) no observador de rede do Azure antes de 29 de fevereiro de 2024.

Use a funcionalidade do Monitor do Azure ExpressRoute no [Monitor de Desempenho de Rede](network-performance-monitor.md) para monitorar a conectividade de ponta a ponta e o desempenho entre as filiais e o Azure, por meio do Azure ExpressRoute. As principais vantagens são: 

- Detecção automática de circuitos ExpressRoute associados à sua assinatura.
- Acompanhamento de utilização de largura de banda, perda e latência no circuito, emparelhamento e nível da Rede Virtual do Azure para ExpressRoute.
- Descoberta da topologia de rede dos circuitos do ExpressRoute.

![ExpressRoute Monitor](media/network-performance-monitor-expressroute/expressroute-intro.png)

## <a name="configuration"></a>Configuração 
Para abrir a configuração para Monitor de Desempenho de Rede, abra a [solução monitor de desempenho de rede](network-performance-monitor.md) e selecione **Configurar**.

### <a name="configure-network-security-group-rules"></a>Configurar regras do grupo de segurança de rede 
Para servidores no Azure que estão sendo usados para o monitoramento via Monitor de Desempenho de Rede, você deverá configurar as regras do NSG (grupo de segurança de rede) para permitir o tráfego TCP na porta usada pelo Monitor de Desempenho de Rede para transações sintéticas. A porta padrão é 8084. Essa configuração permite que o agente do Log Analytics instalado nas VMs do Azure se comunique com um agente de monitoramento local. 

Para saber mais sobre NSGs, confira  [Grupos de segurança de rede](../../virtual-network/manage-network-security-group.md). 

>[!NOTE]
> Antes de continuar com esta etapa, instale o agente do servidor local e o agente do servidor do Azure e execute o script PowerShell EnableRules.ps1. 

 
### <a name="discover-expressroute-peering-connections"></a>Descobrir conexões de emparelhamento do ExpressRoute 
 
1. Selecione a exibição **Emparelhamentos do ExpressRoute**.
2. Selecione **Descobrir Agora** para descobrir todos os emparelhamentos privados do ExpressRoute que estão conectados às redes virtuais na assinatura do Azure vinculada a este espaço de trabalho do Log Analytics.

    >[!NOTE]
    > A solução atualmente descobre apenas emparelhamentos privados do ExpressRoute. 

    >[!NOTE]
    > Somente os emparelhamentos privados conectados às redes associadas à assinatura vinculada a este espaço de trabalho do Log Analytics são descobertos. Se o ExpressRoute for conectado às redes virtuais fora da assinatura vinculada a este espaço de trabalho, crie um espaço de trabalho do Log Analytics nessas assinaturas. Em seguida, use o Monitor de Desempenho de Rede para monitorar os emparelhamentos. 

    ![Configuração do Monitor do ExpressRoute](media/network-performance-monitor-expressroute/expressroute-configure.png)
 
   Depois que a descoberta estiver concluída, as conexões de emparelhamento privadas descobertas serão listadas em uma tabela. O monitoramento para esses emparelhamentos está inicialmente no estado desabilitado. 

### <a name="enable-monitoring-of-the-expressroute-peering-connections"></a>Habilitar o monitoramento das conexões de emparelhamento do ExpressRoute 

1. Selecione a conexão de emparelhamento privado que deseja monitorar.
2. No painel à direita, selecione a caixa de seleção **Monitorar este Emparelhamento**. 
3. Se você pretender criar eventos de integridade para esta conexão, selecione **Habilitar Monitoramento de Integridade para esse emparelhamento**. 
4. Escolha as condições de monitoramento. Você pode definir limites personalizados para geração de eventos de integridade digitando os valores de limite. Sempre que o valor da condição ultrapassar o limite selecionado para a conexão de emparelhamento, será gerado um evento de integridade. 
5. Selecione **Adicionar Agentes** para escolher os agentes de monitoramentos que você pretende usar para monitorar essa conexão de emparelhamento. Certifique-se de adicionar agentes em ambas as extremidades da conexão. Você precisa de pelo menos um agente na rede virtual conectada a esse emparelhamento. Você também precisa de pelo menos um agente local conectado a esse emparelhamento. 
6. Selecione **salvar** para salvar a configuração. 

   ![Configuração de monitoramento do ExpressRoute](media/network-performance-monitor-expressroute/expressroute-configure-discovery.png)


Depois de habilitar as regras e selecionar os valores e agentes, aguarde de 30 a 60 minutos para os valores serem preenchidos e os blocos **Monitoramento do ExpressRoute** aparecerem. Depois de ver os blocos de monitoramentos, seus circuitos do ExpressRoute e os recursos de conexão estarão sendo monitorados pelo Monitor de Desempenho de Rede. 

>[!NOTE]
> Esse recurso funciona de modo confiável em workspaces que atualizaram para a nova linguagem de consulta.

## <a name="walkthrough"></a>Passo a passo 

O painel Monitor de Desempenho de Rede mostra uma visão geral da integridade de circuitos do ExpressRoute e das conexões de emparelhamento. 

![Painel do Monitor de Desempenho de Rede](media/network-performance-monitor-expressroute/npm-dashboard-expressroute.png) 

### <a name="circuits-list"></a>Lista de circuitos 

Para ver uma lista de todos os circuitos do ExpressRoute monitorados, selecione o bloco de circuitos do ExpressRoute. É possível selecionar um circuito e exibir seu estado de integridade, gráficos de tendências para perda de pacotes, utilização de largura de banda e latência. Os gráficos são interativos. É possível selecionar uma janela de tempo personalizada para plotar os gráficos. Arraste o mouse sobre uma área do gráfico para ampliar e ver os pontos de dados refinados. 

![Lista de circuitos do ExpressRoute](media/network-performance-monitor-expressroute/expressroute-circuits.png) 

### <a name="trends-of-loss-latency-and-throughput"></a>Tendências de perda, latência e taxa de transferência 

A utilização da largura de banda, latência e perda de gráficos são interativas. Você pode aplicar o zoom em qualquer seção destes gráficos usando os controles do mouse. Você também pode ver a largura de banda, latência e perda de dados com relação a outros intervalos. No canto superior esquerdo, no botão **Ações**, selecione  **Data/Hora**. 

![Latência do ExpressRoute](media/network-performance-monitor-expressroute/expressroute-latency.png) 

### <a name="peerings-list"></a>Lista de emparelhamentos 

Para obter uma lista de todas as conexões de redes virtuais sobre o emparelhamento privado, selecione o bloco **Emparelhamentos Privados** no painel. Aqui, é possível selecionar uma conexão de rede virtual e exibir seu estado de integridade, gráficos de tendências para perda de pacotes, utilização de largura de banda e latência. 

![Emparelhamentos do ExpressRoute](media/network-performance-monitor-expressroute/expressroute-peerings.png) 

### <a name="circuit-topology"></a>Topologia de circuito 

Para exibir a topologia de circuito, selecione o bloco **Topologia**. Esta ação leva você para a exibição de topologia do circuito ou emparelhamento selecionado. O diagrama de topologia fornece a latência de cada segmento na rede e cada salto de camada 3 é representado por um nó do diagrama. Selecionar um salto revela mais detalhes sobre ele. Para aumentar o nível de visibilidade para incluir saltos locais, mova a barra do controle deslizante para baixo dos **FILTROS**. Mover a barra do controle deslizante para a esquerda ou direita amplia ou reduz o número de saltos no grafo de topologia. A latência em cada segmento é visível, o que permite o isolamento mais rápido de segmentos de alta latência na sua rede.

![Topologia do ExpressRoute](media/network-performance-monitor-expressroute/expressroute-topology.png)

### <a name="detailed-topology-view-of-a-circuit"></a>Exibição de topologia detalhada de um circuito 

Esta exibição mostra as conexões de rede virtuais. 

![Conexões de rede virtual do ExpressRoute](media/network-performance-monitor-expressroute/expressroute-vnet.png)
 
## <a name="diagnostics"></a>Diagnósticos 

O Monitor de Desempenho de Rede ajuda a diagnosticar vários problemas de conectividade do circuito. Alguns dos problemas que você pode ver estão listadas abaixo.

Você pode ver os códigos de notificação e definir alertas sobre eles por meio do **LogAnalytics**. Na página **Diagnóstico NPM**, você pode ver as descrições para cada mensagem de diagnóstico disparado.

| Código de Notificação (Logs) | Descrição |
| --- | --- |
| 5501 | Não é possível percorrer a conexão secundária do circuito do ExpressRoute |
| 5502 | Não é possível percorrer a conexão primária do circuito do ExpressRoute |
| 5503 | Nenhum circuito localizado para a assinatura vinculada ao workspace | 
| 5508 | Não é possível determinar se o tráfego está passando pelo circuito para o caminho |
| 5510 | O tráfego não está passando pelo circuito pretendido | 
| 5511 | O tráfego não está passando pela rede virtual pretendida | 

**O circuito está inoperante.** O Monitor de Desempenho de Rede o notifica assim que a conectividade entre seus recursos locais e as redes virtuais do Azure é perdida. Esta notificação ajuda você a tomar medidas proativas antes de receber escalonamentos de usuário e reduzir o tempo de inatividade.

![O circuito do ExpressRoute está inoperante](media/network-performance-monitor-expressroute/expressroute-circuit-down.png)
 

**O tráfego não flui pelo circuito pretendido.** O Monitor de Desempenho de Rede notifica sempre que o tráfego não estiver fluindo através do circuito do ExpressRoute pretendido. Este problema poderá acontecer se o circuito estiver inoperante e o tráfego estiver fluindo pela rota de backup. Isso também pode ocorrer se houver um problema de roteamento. Essas informações ajudam a gerenciar proativamente quaisquer problemas de configuração em suas políticas de roteamentos e garantem que a rota mais ideal e segura seja usada. 

 

**O tráfego não flui pelo circuito primário.** O Monitor de Desempenho de Rede notifica quando o tráfego está fluindo pelo circuito do ExpressRoute secundário. Embora você não vá enfrentar nenhum problema de conectividade nesse caso, solucionar os problemas proativamente com o circuito primário o deixará mais bem preparado. 

 
![Fluxo de tráfego do ExpressRoute](media/network-performance-monitor-expressroute/expressroute-traffic-flow.png)


**Degradação devido à utilização de pico.** Você pode correlacionar a tendência de utilização de largura de banda com a tendência de latência para identificar se a degradação da carga de trabalho do Azure é devido a um pico de utilização de largura de banda ou não. Em seguida, você pode tomar as devidas providências.

![Utilização de largura de banda do ExpressRoute](media/network-performance-monitor-expressroute/expressroute-peak-utilization.png)

 

## <a name="next-steps"></a>Próximas etapas
[Pesquisar logs](../logs/log-query-overview.md) para exibir registros de dados de desempenho de rede detalhados.