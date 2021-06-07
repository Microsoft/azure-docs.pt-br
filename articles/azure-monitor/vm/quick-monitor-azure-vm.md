---
title: Monitorar uma máquina virtual do Azure com o Azure Monitor
description: Saiba como coletar e analisar dados para uma máquina virtual do Azure no Azure Monitor.
ms.service: azure-monitor
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 03/10/2020
ms.openlocfilehash: 7efd8baf54aeacbd2f55640240a15f2517dcd904
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102046917"
---
# <a name="quickstart-monitor-an-azure-virtual-machine-with-azure-monitor"></a>Monitorar uma máquina virtual do Azure com o Azure Monitor.
O [Azure Monitor](../overview.md) inicia a coleta de dados das máquinas virtuais do Azure no momento em que elas são criadas. Neste guia de início rápido você obterá uma breve explicação dos dados que são coletados automaticamente para uma VM do Azure e como exibi-los no portal do Azure. Em seguida, você habilitará os [insights da VM](../vm/vminsights-overview.md) para sua VM, o que permitirá que os agentes na VM coletem e analisem dados do sistema operacional convidado, incluindo processos e suas dependências.

Este guia de início rápido pressupõe que você tenha uma máquina virtual do Azure existente. Se não tiver, crie uma [VM do Windows](../../virtual-machines/windows/quick-create-portal.md) ou uma [VM do Linux](../../virtual-machines/linux/quick-create-cli.md) seguindo nossos inícios rápidos de VM.

Para obter descrições mais detalhadas dos dados de monitoramento coletados de recursos do Azure, confira [Como monitorar máquinas virtuais do Azure com o Azure Monitor](./monitor-vm-azure.md).


## <a name="complete-the-monitor-an-azure-resource-quickstart"></a>Conclua o início rápido Monitorar um recurso do Azure.
Conclua o [Monitorar um recurso do Azure com o Azure Monitor](../essentials/quick-monitor-azure-resource.md) para exibir a página de visão geral, o log de atividades e as métricas para uma VM em sua assinatura. As VMs do Azure coletam os mesmos dados de monitoramento que qualquer outro recurso do Azure, mas isso serve apenas para a VM do host. O restante deste guia de início rápido se concentrará no monitoramento do sistema operacional convidado e de suas cargas de trabalho.


## <a name="enable-vm-insights"></a>Habilitar insights da VM
Embora os logs de atividades e as métricas sejam coletados para a VM do host, você precisa de um agente e de alguma configuração para coletar e analisar dados de monitoramento do sistema operacional convidado e de suas cargas de trabalho. Os insights da VM instalam esses agentes e fornece recursos avançados adicionais para monitoramento de suas máquinas virtuais.

1. Acesse o menu da sua máquina virtual.
2. Clique em **Acessar o Insights** no bloco da página **Visão geral** ou clique em **Insights** no menu **Monitoramento**.

    ![Página de visão geral](media/quick-monitor-azure-vm/overview-insights.png)

3. Se os insights da VM ainda não foram habilitados para a máquina virtual, clique em **Habilitar**. 

    ![Habilitar o Insights](media/quick-monitor-azure-vm/enable-insights.png)

4. Se a máquina virtual ainda não estiver anexada a um workspace do Log Analytics, você será solicitado a selecionar um workspace existente ou criar um. Selecione o padrão que é um workspace com um nome exclusivo na mesma região que sua máquina virtual.

    ![Selecione o workspace](media/quick-monitor-azure-vm/select-workspace.png)

5. A integração levará alguns minutos, pois as extensões serão habilitadas e os agentes serão instalados em sua máquina virtual. Quando estiver concluído, você receberá uma mensagem informando que o Insights foi implantado com êxito. Clique em **Azure Monitor** para abrir os insights da VM.

    ![Abrir o Azure Monitor](media/quick-monitor-azure-vm/azure-monitor.png)

6. Você verá sua VM com outras VMs que estão integradas na assinatura. Selecione a guia **Não monitoradas** se quiser exibir as máquinas virtuais em sua assinatura que não estão integradas.

    ![Introdução](media/quick-monitor-azure-vm/get-started.png)


## <a name="configure-workspace"></a>Configurar o workspace
Quando você cria um workspace do Log Analytics, ele precisa ser configurado para coletar logs. Essa configuração só precisa ser executada uma vez, pois a configuração é enviada para qualquer máquina virtual que se conecta a ela.

1. Selecione **Configuração do workspace** e, em seguida, selecione seu workspace.

2. Selecione **Configurações avançadas**

    ![Configurações avançadas do Log Analytics](../vm/media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)

### <a name="data-collection-from-windows-vm"></a>Coleta de dados de VM Windows


2. Selecione **Dados** e, em seguida, **Logs de Eventos do Windows**.

3. Adicione um log de eventos digitando o nome do log.  Digite **Sistema** e, em seguida, selecione o sinal de adição **+** .

4. Na tabela, verifique as severidades **Erro** e **Aviso**.

5. Selecione **Salvar** na parte superior da página para salvar a configuração.

### <a name="data-collection-from-linux-vm"></a>Coleta de dados de VM Linux

1. Selecione **Dados** e **Syslog**.

2. Adicione um log de eventos digitando o nome do log.  Digite **Syslog** e, em seguida, selecione o sinal de adição **+** .  

3. Na tabela, anule a seleção das gravidades **Informações**, **Aviso** e **Depuração**. 

4. Selecione **Salvar** na parte superior da página para salvar a configuração.

## <a name="view-data-collected"></a>Exibir os dados coletados

7. Clique em sua máquina virtual e selecione a guia **Desempenho**, que fica no bloco **Insights** no menu **Monitoramento**. Isso mostra um grupo selecionado de contadores de desempenho coletados do sistema operacional convidado de sua VM. Role para baixo para exibir mais contadores e mova o mouse sobre um gráfico para exibir a média e os percentis em momentos diferentes.

    ![A captura de tela mostra o painel Desempenho.](media/quick-monitor-azure-vm/performance.png)

9. Selecione **Mapa** para abrir o recurso de mapas, que mostra os processos em execução na máquina virtual e suas dependências. Selecione **Propriedades** para abrir o painel de propriedades se ainda não estiver aberto.

    ![A captura de tela mostra o painel Mapa.](media/quick-monitor-azure-vm/map.png)

11. Expanda os processos da sua máquina virtual. Selecione um dos processos para exibir seus detalhes e realçar suas dependências.

    ![A captura de tela mostra o painel Mapa com processos para uma máquina virtual expandida.](media/quick-monitor-azure-vm/processes.png)

12. Selecione sua máquina virtual novamente e, em seguida, selecione **Eventos de Log**. 

    ![Eventos de log](media/quick-monitor-azure-vm/log-events.png)

13. Você verá uma lista de tabelas que são armazenadas no workspace do Log Analytics para a máquina virtual. Essa lista será diferente dependendo se você estiver usando uma máquina virtual Windows ou Linux. Clique na tabela **Evento**. Ela inclui todos os eventos do log de eventos do Windows. O Log Analytics abre com uma consulta simples para recuperar entradas do log de eventos.

    ![Log Analytics](media/quick-monitor-azure-vm/log-analytics.png)

## <a name="next-steps"></a>Próximas etapas
Neste guia de início rápido, você habilitou os insights da VM para uma máquina virtual e configurou o workspace do Log Analytics para coletar eventos para o sistema operacional convidado. Para saber como exibir e analisar os dados, continue lendo o tutorial.

> [!div class="nextstepaction"]
> [Exibir ou analisar dados no Log Analytics](../logs/log-analytics-tutorial.md)