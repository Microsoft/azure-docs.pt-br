---
title: Coletar dados de uma máquina virtual do Azure com o Azure Monitor | Microsoft Docs
description: Saiba como habilitar a Extensão de VM do agente do Log Analytics e como habilitar a coleta de dados das VMs do Azure com o Log Analytics.
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 08/19/2019
ms.custom: mvc, sec-javascript-october2019
ms.openlocfilehash: 2f73614253a1ce442d2a93ce5e9b9d7051542511
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102047019"
---
# <a name="collect-data-from-an-azure-virtual-machine-with-azure-monitor"></a>Coletar dados de uma máquina virtual do Azure com o Azure Monitor

O [Azure Monitor](../overview.md) pode coletar dados diretamente das máquinas virtuais do Azure em um workspace do Log Analytics para análise de detalhes e correlações. A instalação da extensão de VM do Log Analytics para [Windows](../../virtual-machines/extensions/oms-windows.md) e [Linux](../../virtual-machines/extensions/oms-linux.md) permite que o Azure Monitor colete dados de suas VMs do Azure. Este início rápido mostra como configurar e coletar dados de VMs Linux ou Windows do Azure usando a extensão de VM com algumas etapas simples.  
 
Este guia de início rápido pressupõe que você tenha uma máquina virtual do Azure existente. Se não puder [criar uma VM Windows](../../virtual-machines/windows/quick-create-portal.md) nem [criar uma VM Linux](../../virtual-machines/linux/quick-create-cli.md), siga nossos guias de início rápido de VM.

## <a name="sign-in-to-azure-portal"></a>Entre no portal do Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Criar um workspace

1. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Escolha **workspaces do Log Analytics**.

    ![Portal do Azure](media/quick-collect-azurevm/azure-portal-log-analytics-workspaces.png)<br>  

2. Selecione **Criar** e, em seguida, selecione opções para os seguintes itens:

   * Forneça um nome para o novo **Espaço de Trabalho do Log Analytics**, como *DefaultLAWorkspace*.  
   * Selecione uma **Assinatura** a vincular escolhendo uma na lista suspensa, se a selecionada por padrão não é adequada.
   * Para **Grupo de Recursos**, selecione um grupo de recursos existente que contém uma ou mais máquinas virtuais do Azure.  
   * Selecione o **Local** no qual as VMs serão implantadas.  Para obter mais informações, consulte em quais [regiões o Log Analytics está disponível](https://azure.microsoft.com/regions/services/).
   * Se você estiver criando um workspace em uma nova assinatura feita depois de 2 de abril de 2018, ele usará o plano de preços *por GB* e a opção de selecionar um tipo de preço não estará disponível.  Se você estiver criando um workspace para uma assinatura existente feita antes de 2 de abril ou uma assinatura associada a uma inscrição de EA existente, selecione seu tipo de preço preferido.  Para obter mais informações sobre os tipos específicos, consulte [Detalhes de preço do Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).
  
        ![Criar folha de recursos do Log Analytics](media/quick-collect-azurevm/create-log-analytics-workspace-azure-portal.png) 

3. Depois de fornecer as informações necessárias no painel do **workspace do Log Analytics**, selecione **OK**.  

Enquanto as informações são verificadas e o workspace é criado, você pode acompanhar seu progresso no menu **Notificações**. 

## <a name="enable-the-log-analytics-vm-extension"></a>Habilitar a Extensão de VM do Log Analytics

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Para máquinas virtuais Windows e Linux já implantadas no Azure, instale o agente do Log Analytics com a Extensão de VM do Log Analytics. Usar a extensão simplifica o processo de instalação e configura automaticamente o agente para enviar dados para o espaço de trabalho do Log Analytics que você especificar. O agente também será automaticamente atualizado quando uma versão mais recente for lançada, garantindo que você disponha dos recursos e correções mais recentes. Antes de prosseguir, verifique se a VM está em execução, caso contrário, a conclusão do processo com êxito falhará.  

>[!NOTE]
>O agente do Log Analytics para Linux não pode ser configurado para se reportar a mais de um espaço de trabalho do Log Analytics. 

1. No portal do Azure, selecione **Todos os serviços**, localizado no canto superior esquerdo. Na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Escolha **workspaces do Log Analytics**.

2. Na lista de workspaces do Log Analytics, selecione *DefaultLAWorkspace* criado anteriormente.

3. No menu à esquerda, em Fontes de Dados do Workspace, selecione **Máquinas virtuais**.  

4. Na lista de **Máquinas virtuais**, selecione uma máquina virtual em que deseja instalar o agente. Observe que o **status de conexão do Log Analytics** da VM indica que ele **Não está conectado**.

5. Nos detalhes de sua máquina virtual, selecione **Conectar**. O agente é automaticamente instalado e configurado para o seu espaço de trabalho do Log Analytics. Esse processo leva alguns minutos, período durante o qual o **Status** é mostrado como **Conectando**.

6. Depois de instalar e conectar o agente, o **status da conexão do Log Analytics** será atualizado com **Este workspace**.

## <a name="collect-event-and-performance-data"></a>Coletar dados de desempenho e eventos

O Azure Monitor pode coletar eventos dos logs de eventos do Windows ou do Syslog do Linux e de contadores de desempenho especificados para relatórios e análise de prazo mais longo e tomar uma ação quando determinada condição for detectada. Siga estas etapas para configurar a coleta de eventos do log do sistema do Windows e do Syslog do Linux e de vários contadores de desempenho comuns para começar.  

### <a name="data-collection-from-windows-vm"></a>Coleta de dados de VM Windows

1. Selecione **Configurações avançadas**.

    ![Configurações avançadas do Log Analytics](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)

2. Selecione **Dados** e, em seguida, **Logs de Eventos do Windows**.

3. Adicione um novo log de eventos digitando o nome do log.  Digite **Sistema** e, em seguida, selecione o sinal de adição **+** .

4. Na tabela, verifique as severidades **Erro** e **Aviso**.

5. Selecione **Salvar** na parte superior da página para salvar a configuração.

6. Selecione **Dados de Desempenho do Windows** para habilitar a coleta de contadores de desempenho em um computador Windows.

7. Quando você configura os contadores de desempenho do Windows para um novo espaço de trabalho do Log Analytics pela primeira vez, você tem a opção de criar rapidamente vários contadores comuns. Eles são listados com uma caixa de seleção ao lado de cada um.

    ![Captura de tela do painel Contadores de Desempenho do Windows com uma lista de contadores selecionados e o botão Adicionar contadores de desempenho selecionado.](media/quick-collect-azurevm/windows-perfcounters-default.png)

    Selecione **Adicionar os contadores de desempenho selecionados**.  Eles são adicionados e predefinidos com um intervalo de amostragem de coleta de dez segundos.
  
8. Selecione **Salvar** na parte superior da página para salvar a configuração.

### <a name="data-collection-from-linux-vm"></a>Coleta de dados de VM Linux

1. Selecione **Syslog**.  

2. Adicione um novo log de eventos digitando o nome do log.  Digite **Syslog** e, em seguida, selecione o sinal de adição **+** .  

3. Na tabela, anule a seleção das gravidades **Informações**, **Aviso** e **Depuração**. 

4. Selecione **Salvar** na parte superior da página para salvar a configuração.

5. Selecione **Dados de Desempenho do Linux** para habilitar a coleta de contadores de desempenho em um computador Linux. 

6. Quando você configura os contadores de desempenho do Linux para um novo espaço de trabalho do Log Analytics pela primeira vez, você tem a opção de criar rapidamente vários contadores comuns. Eles são listados com uma caixa de seleção ao lado de cada um.

    ![Captura de tela do painel Contadores de Desempenho do Linux com uma lista de contadores selecionados e o botão Adicionar contadores de desempenho selecionado.](media/quick-collect-azurevm/linux-perfcounters-azure-monitor.png)

    Selecione **Aplicar configuração abaixo aos meus computadores** e, em seguida, selecione **Adicionar os contadores de desempenho selecionados**.  Eles são adicionados e predefinidos com um intervalo de amostragem de coleta de dez segundos.  

7. Selecione **Salvar** na parte superior da página para salvar a configuração.

## <a name="view-data-collected"></a>Exibir os dados coletados

Agora que você habilitou a coleta de dados, vamos executar um exemplo simples de pesquisa de logs para ver alguns dados das VMs de destino.  

1. No workspace selecionado, no painel esquerdo, selecione **Logs**.

2. Na página de consulta de logs, digite `Perf` no editor de consulta e selecione **Executar**.

    ![Exemplo de consulta da pesquisa de logs do Log Analytics](./media/quick-collect-windows-computer/log-analytics-portal-queryexample.png) 

    Por exemplo, a consulta na imagem a seguir retornou 10 mil registros de desempenho. Os resultados serão significativamente menores.

    ![Resultado da pesquisa de logs do Log Analytics](media/quick-collect-azurevm/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, exclua o espaço de trabalho do Log Analytics. Para fazer isso, selecione o workspace do Log Analytics criado anteriormente e, na página de recursos, selecione **Excluir**.


![Excluir um recurso do Log Analytics](media/quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você está coletando dados operacionais e de desempenho das máquinas virtuais Windows ou Linux, comece a explorar, analisar e tomar ações com facilidade em relação aos dados coletados *gratuitamente*.  

Para saber como exibir e analisar os dados, continue lendo o tutorial.

> [!div class="nextstepaction"]
> [Exibir ou analisar dados no Log Analytics](../logs/log-analytics-tutorial.md)