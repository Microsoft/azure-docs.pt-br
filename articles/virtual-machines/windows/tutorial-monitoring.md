---
title: Tutorial – Monitorar e atualizar máquinas virtuais do Windows no Azure | Microsoft Docs
description: Neste tutorial, você aprenderá a monitorar métricas de desempenho e diagnóstico de inicialização e a gerenciar atualizações de pacote em uma máquina virtual do Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 25160c50cd4844fdb5b3a3454213b2067ef91d01
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608032"
---
# <a name="tutorial-monitor-and-update-a-windows-virtual-machine-in-azure"></a>Tutorial: Monitorar e atualizar uma máquina virtual do Windows no Azure

O monitoramento do Azure usa agentes para coletar dados de desempenho e inicialização de VMs do Azure, armazenar esses dados no armazenamento do Azure e torná-los acessíveis por meio do Portal, do módulo do Azure PowerShell e da CLI do Azure. O Gerenciamento de Atualizações permite que você gerencie atualizações e patches para suas VMs do Windows do Azure.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Habilitar o diagnóstico de inicialização em uma VM
> * Exibir diagnóstico de inicialização
> * Exibir métricas de host VM
> * Instalar a extensão de diagnóstico
> * Exibir métricas de VM
> * Criar um alerta
> * Gerenciar atualizações do Windows
> * Monitoramento de alterações e inventário
> * Configurar monitoramento avançado

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Você também pode iniciar o Cloud Shell em uma guia separada do navegador indo até [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selecione **Copiar** para copiar os blocos de código, cole o código no Cloud Shell e depois pressione Enter para executá-lo.

## <a name="create-virtual-machine"></a>Criar máquina virtual

Para configurar o monitoramento do Azure e o gerenciamento de atualizações neste tutorial, você precisa de uma VM Windows no Azure. Primeiro, defina o nome de usuário e a senha de um administrador para a VM com [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Agora crie uma VM com [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). O exemplo a seguir cria uma VM chamada *myVM* na localização *EastUs*. Se ainda não existirem, o grupo de recursos *myResourceGroupMonitorMonitor* e os recursos de rede de suporte serão criados:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

Demora alguns minutos para que os recursos e a VM sejam criados.

## <a name="view-boot-diagnostics"></a>Exibir diagnóstico de inicialização

Conforme as máquinas virtuais do Windows são inicializadas, o agente de diagnóstico de inicialização captura a saída na tela que pode ser usada para a finalidade de solução de problemas. Essa funcionalidade é habilitada por padrão. As capturas de tela são armazenadas em uma conta de armazenamento do Azure, que também é criada por padrão.

Você pode obter os dados de diagnóstico de inicialização com o comando [Get-AzureRmVMBootDiagnosticsData](https://docs.microsoft.com/powershell/module/az.compute/get-azvmbootdiagnosticsdata). No exemplo a seguir, diagnóstico de inicialização é baixado para a raiz da unidade *c:\*.

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName "myResourceGroupMonitor" -Name "myVM" -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>Exibir métricas de host

Uma VM do Windows tem uma VM Host dedicada no Azure com a qual ele interage. As métricas são coletadas automaticamente para o Host e podem ser exibidas no Portal do Azure.

1. No Portal do Azure, clique em **Grupos de Recursos**, selecione **myResourceGroupMonitor** e, em seguida, selecione **myVM** na lista de recursos.
2. Clique em **Métricas** na folha da VM e, em seguida, selecione qualquer uma das métricas de Host em **Métricas disponíveis** para ver como está o desempenho da VM do Host.

    ![Exibir métricas de host](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="install-diagnostics-extension"></a>Instalar extensão de diagnóstico

As métricas de host básicas estão disponíveis, mas para ver métricas mais granulares e específicas à VM, você precisa instalar a extensão de Diagnóstico do Azure na VM. A extensão de Diagnóstico do Azure permite que dados de monitoramento e diagnóstico adicionais sejam recuperados da VM. Você pode exibir essas métricas de desempenho e criar alertas com base no desempenho de uma máquina virtual. A extensão de diagnóstico é instalada por meio do Portal do Azure, da seguinte maneira:

1. No Portal do Azure, clique em **Grupos de Recursos**, selecione **myResourceGroupMonitor** e, em seguida, selecione **myVM** na lista de recursos.
2. Clique em **Configurações de diagnóstico**. A lista mostra que o *Diagnóstico de inicialização* já está habilitado na seção anterior. Clique na caixa de seleção para as *Métricas básicas*.
3. Clique no botão **Habilitar o monitoramento em nível de convidado**.

    ![Exibir métricas de diagnóstico](./media/tutorial-monitoring/enable-diagnostics-extension.png)

## <a name="view-vm-metrics"></a>Exibir métricas de VM

Você pode exibir as métricas da VM da mesma maneira que você exibiu as métricas da VM host:

1. No Portal do Azure, clique em **Grupos de Recursos**, selecione **myResourceGroupMonitor** e, em seguida, selecione **myVM** na lista de recursos.
2. Para ver como está o desempenho da VM do Host, clique em **Métricas** na folha da VM e, em seguida, selecione qualquer uma das métricas de diagnóstico em **Métricas disponíveis**.

    ![Exibir métricas de VM](./media/tutorial-monitoring/monitor-vm-metrics.png)

## <a name="create-alerts"></a>Criar alertas

Você pode criar alertas com base em métricas de desempenho específicas. Alertas podem ser usados para lhe notificar quando uso médio da CPU excede um determinado limite ou o espaço em disco livre disponível cai abaixo de um determinado valor, por exemplo. Os alertas são exibidos no Portal do Azure, ou podem ser enviados por email. Você também pode disparar Runbooks da Automação do Azure ou Aplicativos Lógicos do Azure em resposta à geração de alertas.

O exemplo a seguir cria um alerta para uso médio da CPU.

1. No Portal do Azure, clique em **Grupos de Recursos**, selecione **myResourceGroupMonitor** e, em seguida, selecione **myVM** na lista de recursos.
2. Clique em **Regras de alerta** na folha da VM e, em seguida, clique em **Adicionar alerta de métrica** na parte superior da folha de alertas.
3. Forneça um **Nome** para o alerta, como *myAlertRule*
4. Para disparar um alerta quando o percentual de CPU excede 1.0 por cinco minutos, deixe todos os outros padrões selecionados.
5. Opcionalmente, marque a caixa de *Proprietários, colaboradores e leitores de email* para enviar uma notificação por email. A ação padrão é apresentar uma notificação no portal.
6. Clique no botão **OK**.

## <a name="manage-windows-updates"></a>Gerenciar atualizações do Windows

O Gerenciamento de Atualizações permite que você gerencie atualizações e patches para suas VMs do Windows do Azure.
Diretamente de sua VM, você pode rapidamente avaliar o status de atualizações disponíveis, agendar a instalação de atualizações necessárias e examinar os resultados de implantação para verificar se as atualizações foram aplicadas com êxito na VM.

Para obter informações sobre preços, consulte [Preços de automação de Gerenciamento de Atualizações](https://azure.microsoft.com/pricing/details/automation/)

### <a name="enable-update-management"></a>Habilitar Gerenciamento de Atualizações

Habilite o Gerenciamento de Atualizações para sua VM:

1. No lado esquerdo da tela, selecione **Máquinas virtuais**.
2. Na lista, selecione uma VM.
3. Na tela da VM, na seção **Operações**, clique em **Gerenciamento de Atualizações**. A tela **Habilitar Gerenciamento de Atualizações** é aberta.

Uma validação é executada para determinar se o Gerenciamento de Atualizações está habilitado para essa VM.
A validação inclui verificar se há um espaço de trabalho do Log Analytics e uma conta de automação vinculada e se a solução está no espaço de trabalho.

Um workspace do [Log Analytics](../../log-analytics/log-analytics-overview.md) é usado para coletar dados gerados por recursos e serviços, como o Gerenciamento de Atualizações.
O workspace fornece um único local para examinar e analisar dados de várias fontes.
Para executar ações adicionais em máquinas virtuais que requerem atualizações, a Automação do Azure permite que você execute runbooks em VMs, como download e aplicação de atualizações.

O processo de validação também verifica se a VM é provisionada com o MMA (Microsoft Monitoring Agent) e o Hybrid Runbook Worker da Automação.
Esse agente é usado para comunicar-se com a VM e obter informações sobre o status de atualização.

Escolha o workspace do Log Analytics e a conta de automação e clique em **Habilitar** para habilitar a solução. A solução demora até 15 minutos para habilitar.

Se algum dos seguintes pré-requisitos estiver ausente durante a integração, ele será adicionado automaticamente:

* Workspace do [Log Analytics](../../log-analytics/log-analytics-overview.md)
* [Automação](../../automation/automation-offering-get-started.md)
* Uma [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) está habilitada na VM

A tela **Gerenciamento de Atualizações** é exibida. Configure o local, o workspace de Log analytics e a conta de Automação a serem usados e clique em **Habilitar**. Caso os campos estejam esmaecidos, isso significa que outra solução de automação está habilitada para a VM e o mesmo workspace e conta de Automação devem ser usados.

![Habilitar a solução de Gerenciamento de Atualizações](./media/tutorial-monitoring/manageupdates-update-enable.png)

A habilitação da solução pode levar até 15 minutos. Durante esse tempo, não feche a janela do navegador. Depois que a solução for habilitada, as informações sobre atualizações ausentes na VM fluirão para os logs do Azure Monitor. Pode levar entre 30 minutos e 6 horas para que os dados fiquem disponíveis para análise.

### <a name="view-update-assessment"></a>Exibir avaliação de atualização

Depois que o **Gerenciamento de Atualizações** for habilitado, a tela **Gerenciamento de Atualizações** será exibida. Depois que a avaliação das atualizações estiver completa, você verá uma lista de atualizações ausentes na guia **Atualizações ausentes**.

 ![Exibir o status de atualização](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>Agendar uma implantação de atualização

Para instalar atualizações, agende uma implantação que siga o agendamento de versão e o período de serviço. Você pode escolher quais tipos de atualização deseja incluir na implantação. Por exemplo, você pode incluir atualizações críticas ou de segurança e excluir pacotes cumulativos de atualizações.

Agende uma nova implantação de atualização para a VM clicando em **Agendar implantação de atualização** na parte superior da tela **Gerenciamento de Atualizações**. Na tela **Nova implantação de atualização**, especifique as seguintes informações:

Para criar uma nova implantação de atualização, selecione **Agendar implantação de atualização**. A página **Nova implantação de atualizações** será aberta. Insira valores para as propriedades descritas na tabela a seguir e clique em **Criar**:

| Propriedade | Descrição |
| --- | --- |
| Nome |Nome exclusivo para identificar a Implantação de Atualizações. |
|Sistema operacional| Linux ou Windows|
| Grupos a serem atualizados |Para computadores do Azure, defina uma consulta com base em uma combinação de assinatura, grupos de recursos, localizações e marcas para criar um grupo dinâmico de VMs do Azure a ser incluído na implantação. </br></br>Para computadores que não são Azure, selecione uma pesquisa salva existente para selecionar um grupo de computadores que não são Azure a serem incluídos na implantação. </br></br>Para obter mais informações, consulte [grupos dinâmicos](../../automation/automation-update-management.md#using-dynamic-groups)|
| Computadores para atualizar |Selecione uma pesquisa salva, um grupo importado ou selecione a máquina na lista suspensa e selecione máquinas individuais. Se você escolher **Machines**, a prontidão da máquina é mostrada na coluna **UPDATE AGENT READINESS**.</br> Para saber mais sobre os diferentes métodos de criação de grupos de computadores nos logs do Azure Monitor, veja [Grupos de computadores nos logs do Azure Monitor](../../azure-monitor/platform/computer-groups.md) |
|Classificações de origem|Selecione todas as classificações de atualização necessárias|
|Incluir/excluir atualizações|Isso abre o **incluir/excluir** página. As atualizações a serem incluídas ou excluídas estão em guias separadas. Para mais informações sobre como a inclusão é tratada, consulte o [comportamento de inclusão](../../automation/automation-update-management.md#inclusion-behavior) |
|Configurações de agendamento|Selecione o tempo para iniciar e selecione Uma Vez ou recorrente para a recorrência|
| Pré-scripts + pós-scripts|Selecione os scripts sejam executados antes e após sua implantação|
| Janela de manutenção |Número de minutos definido para atualizações. O valor não pode ser inferior a 30 minutos e não superior a 6 horas |
| Reinicialize o controle| Determina como as reinicializações devem ser tratadas. As opções disponíveis são:</br>Reinicialização, se necessário (Padrão)</br>Sempre reinicializar</br>Nunca reinicializar</br>Somente reinicialização - não instalará as atualizações|

As implantações de atualização também podem ser criadas programaticamente. Para aprender a criar uma Implantação de atualização com a API REST, consulte [Configurações de atualização de software - Criar](/rest/api/automation/softwareupdateconfigurations/create). Também é um exemplo de runbook que pode ser usado para criar uma implantação de atualização semanal. Para saber mais sobre este runbook, consulte [Criar uma implantação de atualização semanal para uma ou mais VMs em um grupo de recursos](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

Depois de concluir a configuração da agenda, clique no botão **Criar** e retorne ao painel de status.
Observe que a tabela **Agendado** mostra a agenda de implantação criada.

### <a name="view-results-of-an-update-deployment"></a>Exibir resultados de uma implantação de atualização

Após o início da implantação agendada, você pode ver o status dessa implantação na guia **Implantações de atualização** na tela **Gerenciamento de Atualizações**.
Se ela estiver em execução, seu status será mostrado como **Em andamento**. Após a conclusão, em caso de êxito, ele será alterado para **Êxito**.
Se houver falha em uma ou mais atualizações na implantação, o status será **Falhou parcialmente**.
Clique na implantação de atualização concluída para ver o painel dessa implantação de atualização.

![Painel de status de implantação de atualização para implantação específica](./media/tutorial-monitoring/manageupdates-view-results.png)

No bloco **Resultados de atualização** há um resumo do número total de atualizações e os resultados da implantação na VM.
Na tabela à direita há uma análise detalhada de cada atualização e os resultados da instalação, que podem ser um dos seguintes valores:

* **Não foi tentada** – a atualização não foi instalada devido a tempo suficiente disponível com base na duração da janela de manutenção definida.
* **Êxito** – a atualização foi bem-sucedida
* **Falha** – Falha na atualização

Clique em **Todos os logs** para ver todas as entradas de log que a implantação criou.

Clique no bloco **Saída** para ver o fluxo de trabalho do runbook responsável por gerenciar a implantação de atualização na VM de destino.

Clique em **Erros** para ver informações detalhadas sobre os erros da implantação.

## <a name="monitor-changes-and-inventory"></a>Monitoramento de alterações e inventário

É possível coletar e exibir inventário para software, arquivos, daemons do Linux, serviços do Windows e chaves de Registro do Windows em seus computadores. Acompanhar as configurações de seus computadores pode ajudar a detectar problemas operacionais em seu ambiente e entender melhor o estado dos seus computadores.

### <a name="enable-change-and-inventory-management"></a>Habilitar Alterações e Gerenciamento de Estoque

Habilitar Alterações e Gerenciamento de Estoque para a sua VM:

1. No lado esquerdo da tela, selecione **Máquinas virtuais**.
2. Na lista, selecione uma VM.
3. Na tela da VM, na seção **Operações**, clique em **Estoque** ou **Rastrear mudanças**. A tela **Habilitar Controle de Alterações e Estoque** é aberta.

Configure o local, o workspace de Log analytics e a conta de Automação a serem usados e clique em **Habilitar**. Caso os campos estejam esmaecidos, isso significa que outra solução de automação está habilitada para a VM e o mesmo workspace e conta de Automação devem ser usados. Mesmo que as soluções estejam separadas no menu, elas são a mesma solução. Habilitar uma permite ambos para sua VM.

![Habilitar Controle de Alterações e Inventário](./media/tutorial-monitoring/manage-inventory-enable.png)

Depois que a solução foi habilitada levará algum tempo enquanto o inventário estiver sendo coletado na VM antes de os dados serem exibidos.

### <a name="track-changes"></a>Controle de alterações

Na sua VM, selecione **Controle de alterações** em **OPERAÇÕES**. Clique em **Editar configurações**, a página **Controle de Alterações** é exibida. Selecione o tipo de configuração que você deseja acompanhar e, em seguida, clique em **+ +Adicionar** para definir as configurações. As opções disponíveis para o Windows são:

* Registro do Windows
* Arquivos do Windows

Para obter informações detalhadas sobre o Controle de Alterações, consulte [Solucionar as alterações em uma máquina virtual](../../automation/automation-tutorial-troubleshoot-changes.md)

### <a name="view-inventory"></a>Exibição do Inventário

Na sua VM, selecione **Inventário** em **OPERAÇÕES**. Na guia **Software**, há uma lista de tabelas que o software havia encontrado. Os detalhes de alto nível de cada registro de software estão visíveis na tabela. Esses detalhes incluem o nome do software, a versão, publicador, hora da última atualização.

![Exibição do Inventário](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Monitorar os logs de atividades e alterações

Na página **Controle de alterações** em sua VM, selecione **Gerenciar Conexão do Log de Atividades**. Essa tarefa abre a página **Log de Atividades do Azure**. Selecione **Conectar** para conectar o Controle de alterações ao log de atividades do Azure para sua VM.

Com essa configuração habilitada, navegue até a página **Visão geral** para a VM e selecione **Parar** para interromper a VM. Quando solicitado, selecione **Sim** para interromper a VM. Quando ele for desalocado, selecione **Iniciar** para reiniciar a VM.

Interromper e iniciar uma VM registra um evento em seu log de atividades. Navegue de volta para a página **Controle de alterações**. Selecione a guia **Eventos** na parte inferior da página. Após algum tempo, os eventos são exibidos no gráfico e na tabela. Cada evento pode ser selecionado para exibir informações detalhadas sobre ele.

![Visualizar categorias no log de atividades](./media/tutorial-monitoring/manage-activitylog-view-results.png)

O gráfico mostra as alterações que ocorreram ao longo do tempo. Depois de adicionar uma conexão do Log de Atividades, o grafo da linha na parte superior exibe eventos do Log de Atividades do Azure. Cada linha de gráficos de barras representa um tipo de alteração controlável diferente. Esses tipos são daemons do Linux, arquivos, chaves do Registro do Windows, software e serviços do Windows. A guia de alteração mostra os detalhes para as alterações mostradas na visualização em ordem decrescente de hora em que a alteração ocorreu (mais recentes primeiro).

## <a name="advanced-monitoring"></a>Monitoramento avançado

Você pode fazer monitoramento mais avançado da sua VM usando soluções como o Gerenciamento de Atualizações e Alterações e Inventário fornecidos pela [Automação do Azure](../../automation/automation-intro.md).

Quando você tem acesso ao espaço de trabalho do Log Analytics, você pode encontrar a chave do espaço de trabalho e o identificador de espaço de trabalho selecionando **Configurações avançadas** em **CONFIGURAÇÕES**. Use o comando [Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) para adicionar a extensão do Microsoft Monitoring Agent à VM. Atualize os valores das variáveis na amostra abaixo para a chave do espaço de trabalho do Log Analytics e a ID do espaço de trabalho.

```powershell
$workspaceId = "<Replace with your workspace Id>"
$key = "<Replace with your primary key>"

Set-AzVMExtension -ResourceGroupName "myResourceGroupMonitor" `
  -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
  -VMName "myVM" `
  -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
  -ExtensionType "MicrosoftMonitoringAgent" `
  -TypeHandlerVersion 1.0 `
  -Settings @{"workspaceId" = $workspaceId} `
  -ProtectedSettings @{"workspaceKey" = $key} `
  -Location "East US"
```

Depois de alguns minutos, você deverá ver a nova VM no espaço de trabalho do Log Analytics.

![Folha do espaço de trabalho do Log Analytics](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou e revisou VMs com a Central de Segurança do Azure. Você aprendeu como:

> [!div class="checklist"]
> * Criar uma rede virtual
> * Criar um grupo de recursos e uma VM
> * Habilitar o diagnóstico de inicialização na VM
> * Exibir diagnóstico de inicialização
> * Exibir métricas de host
> * Instalar a extensão de diagnóstico
> * Exibir métricas de VM
> * Criar um alerta
> * Gerenciar atualizações do Windows
> * Monitoramento de alterações e inventário
> * Configurar monitoramento avançado

Avance para o próximo tutorial para saber mais sobre a Central de Segurança do Azure.

> [!div class="nextstepaction"]
> [Gerenciar a segurança da VM](../../security/fundamentals/overview.md)
