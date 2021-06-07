---
title: Tutorial – Monitorar um computador híbrido com o Azure Monitor para VMs
description: Saiba como coletar e analisar dados de um computador híbrido no Azure Monitor.
ms.topic: tutorial
ms.date: 09/23/2020
ms.openlocfilehash: 409ad0976e02e42e385e22a103cfc06af5a4f3f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100587702"
---
# <a name="tutorial-monitor-a-hybrid-machine-with-azure-monitor-for-vms"></a>Tutorial: Monitorar um computador híbrido com o Azure Monitor para VMs

O [Azure Monitor](../overview.md) pode coletar dados diretamente dos computadores híbridos em um workspace do Log Analytics para correlação e análise detalhadas. Normalmente, isso envolveria a instalação do [agente do Log Analytics](../../../azure-monitor/agents/agents-overview.md#log-analytics-agent) no computador usando um script, com um método automatizado ou manual seguindo os seus padrões de gerenciamento de configuração. Os servidores habilitados para Arc recentemente apresentaram o suporte à instalação das [extensões de VM](../manage-vm-extensions.md) do Log Analytics e do Dependency Agent para Windows e Linux, permitindo que o Azure Monitor colete dados das suas VMs não Azure.

Este tutorial mostra como configurar e coletar dados dos seus computadores Linux ou Windows habilitando o Azure Monitor para VMs seguindo um conjunto simplificado de etapas, que simplifica a experiência e é mais rápido.  

## <a name="prerequisites"></a>Pré-requisitos

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* A funcionalidade de extensão de VM está disponível somente na lista de [regiões com suporte](../overview.md#supported-regions).

* Confira [Sistemas operacionais com suporte](../../../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) para que o sistema operacional dos servidores que você está habilitando tenha suporte no Azure Monitor para VMs.

* Examine os requisitos de firewall do agente do Log Analytics fornecidos na [visão geral do agente do Log Analytics](../../../azure-monitor/agents/log-analytics-agent.md#network-requirements). O Dependency Agent de Mapa do Azure Monitor para VMs não transmite dados por conta própria e não exige alterações em firewalls ou portas.

## <a name="sign-in-to-azure-portal"></a>Entre no portal do Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="enable-azure-monitor-for-vms"></a>Habilitar o Azure Monitor para VMs

1. Inicie o serviço do Azure Arc no portal do Azure clicando em **Todos os serviços** e, em seguida, pesquisando e selecionando **Computadores – Azure Arc**.

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="Pesquisar os servidores habilitados para Arc em Todos os Serviços" border="false":::

1. Na página **Computadores – Azure Arc**, selecione o computador conectado que você criou no artigo [guia de início rápido](quick-enable-hybrid-vm.md).

1. No painel esquerdo, na seção **Monitoramento**, selecione **Insights** e **Habilitar**.

    :::image type="content" source="./media/tutorial-enable-vm-insights/insights-option.png" alt-text="Selecione a opção Insights no menu à esquerda" border="false":::

1. Na página **Integração dos Insights** do Azure Monitor, você será solicitado a criar um workspace. Para este tutorial, não recomendamos que você selecione um workspace do Log Analytics, se já tiver um. Selecione o padrão que é um workspace com um nome exclusivo na mesma região que o computador conectado registrado. Esse workspace é criado e configurado para você.

    :::image type="content" source="./media/tutorial-enable-vm-insights/enable-vm-insights.png" alt-text="Habilitar a página do Azure Monitor para VMs" border="false":::

1. Você recebe as mensagens de status enquanto a configuração é executada. Esse processo leva alguns minutos, pois as extensões são instaladas no seu computador conectado.

    :::image type="content" source="./media/tutorial-enable-vm-insights/onboard-vminsights-vm-portal-status.png" alt-text="Habilitar a mensagem de status de progresso do Azure Monitor para VMs" border="false":::

    Quando for concluído, você receberá uma mensagem informando que o computador foi integrado com êxito e que o insight foi implantado com êxito.

## <a name="view-data-collected"></a>Exibir os dados coletados

Depois que a implantação e a configuração forem concluídas, selecione **Insights** e, em seguida, selecione a guia **Desempenho**. Na guia Desempenho, é mostrado um grupo selecionado de contadores de desempenho coletados do sistema operacional convidado do seu computador. Role para baixo para exibir mais contadores e mova o mouse sobre um grafo para exibir a média e os percentuais obtidos a partir do momento em que a extensão da VM do Log Analytics foi instalada no computador.

:::image type="content" source="./media/tutorial-enable-vm-insights/insights-performance-charts.png" alt-text="Gráficos de desempenho do Azure Monitor para VMs do computador selecionado" border="false":::

Selecione **Mapa** para abrir o recurso de mapas, que mostra os processos em execução no computador e as dependências deles. Selecione **Propriedades** para abrir o painel de propriedades se ainda não estiver aberto.

:::image type="content" source="./media/tutorial-enable-vm-insights/insights-map.png" alt-text="Mapa do Azure Monitor para VMs do computador selecionado" border="false":::

Expanda os processos do seu computador. Selecione um dos processos para exibir seus detalhes e realçar suas dependências.

Selecione seu computador novamente e, em seguida, **Eventos de Log**. Você verá uma lista de tabelas que são armazenadas no workspace do Log Analytics para o computador. Essa lista será diferente dependendo se você estiver usando um computador Windows ou Linux. Selecione a tabela **Evento**. A tabela **Evento** inclui todos os eventos do log de eventos do Windows. O Log Analytics abre com uma consulta simples para recuperar entradas do log de eventos coletadas.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Azure Monitor confira o seguinte artigo:

> [!div class="nextstepaction"]
> [Visão geral do Azure Monitor](../../../azure-monitor/overview.md)