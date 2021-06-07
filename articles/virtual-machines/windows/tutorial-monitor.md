---
title: Tutorial – Monitorar máquinas virtuais do Windows no Azure
description: Neste tutorial, você aprenderá a monitorar o desempenho e os componentes do aplicativo descobertos em execução nas Máquinas Virtuais do Windows.
author: mgoedtel
manager: carmonm
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 09/27/2018
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 8c4345d16efe296413ea9d995c2307bba62d33f8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102560694"
---
# <a name="tutorial-monitor-a-windows-virtual-machine-in-azure"></a>Tutorial: Monitorar uma máquina virtual do Windows no Azure

O monitoramento do Azure usa agentes para coletar dados de desempenho e inicialização de VMs do Azure, armazenar esses dados no armazenamento do Azure e torná-los acessíveis por meio do portal, do módulo do Azure PowerShell e da CLI do Azure. O monitoramento avançado é fornecido com o Azure Monitor para VMs coletando métricas de desempenho, descobre componentes do aplicativo instalados na VM e inclui gráficos de desempenho e o mapa de dependência.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Habilitar o diagnóstico de inicialização em uma VM
> * Exibir diagnóstico de inicialização
> * Exibir métricas de host VM
> * Habilitar o Azure Monitor para VMs
> * Exibir métricas de desempenho da VM
> * Criar um alerta

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Você também pode iniciar o Cloud Shell em uma guia separada do navegador indo até [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selecione **Copiar** para copiar os blocos de código, cole o código no Cloud Shell e depois pressione Enter para executá-lo.

## <a name="create-virtual-machine"></a>Criar máquina virtual

Para configurar o monitoramento do Azure e o gerenciamento de atualizações neste tutorial, você precisa de uma VM Windows no Azure. Primeiro, defina o nome de usuário e a senha de um administrador para a VM com [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Agora crie uma VM com [New-AzVM](/powershell/module/az.compute/new-azvm). O exemplo a seguir cria uma VM chamada *myVM* na localização *EastUs*. Se ainda não existirem, o grupo de recursos *myResourceGroupMonitorMonitor* e os recursos de rede de suporte serão criados:

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

Você pode obter os dados de diagnóstico de inicialização com o comando [Get-AzureRmVMBootDiagnosticsData](/powershell/module/az.compute/get-azvmbootdiagnosticsdata). No exemplo a seguir, diagnóstico de inicialização é baixado para a raiz da unidade *c:\*.

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName "myResourceGroupMonitor" -Name "myVM" -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>Exibir métricas de host

Uma VM do Windows tem uma VM Host dedicada no Azure com a qual ele interage. As métricas são coletadas automaticamente para o Host e podem ser exibidas no Portal do Azure.

1. No Portal do Azure, clique em **Grupos de Recursos**, selecione **myResourceGroupMonitor** e, em seguida, selecione **myVM** na lista de recursos.
2. Clique em **Métricas** na folha da VM e, em seguida, selecione qualquer uma das métricas de Host em **Métricas disponíveis** para ver como está o desempenho da VM do Host.

    ![Exibir métricas de host](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="enable-advanced-monitoring"></a>Habilitar o monitoramento avançado

Para habilite o monitoramento da VM do Azure com o Azure Monitor para VMs:

1. No Portal do Azure, clique em **Grupos de Recursos**, selecione **myResourceGroupMonitor** e, em seguida, selecione **myVM** na lista de recursos.

2. Na página da VM, na seção **Monitoramento**, selecione **Insights (versão prévia)** .

3. Na página **Insights (versão prévia)** , selecione **Experimentar agora**.

    ![Habilitar o Azure Monitor para VMs em uma VM](../../azure-monitor/vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png)

4. Na página **Integração de Insights do Azure Monitor**, caso você tenha um espaço de trabalho do Log Analytics existente na mesma assinatura, selecione-o na lista suspensa.  

    A lista selecione previamente o workspace e a localização padrão em que a VM está implantada na assinatura. 

    >[!NOTE]
    >Para criar um workspace do Log Analytics para armazenar os dados de monitoramento da VM, confira [Criar um workspace do Log Analytics](../../azure-monitor/logs/quick-create-workspace.md). O workspace deve pertencer a uma das [regiões com suporte](../../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).

Depois de habilitar o monitoramento, talvez você precise aguardar vários minutos antes de poder exibir as métricas de desempenho da VM.

![Habilitar o Azure Monitor para o processamento da implantação do monitoramento das VMs](../../azure-monitor/vm/media/vminsights-enable-portal/onboard-vminsights-vm-portal-status.png)

## <a name="view-vm-performance-metrics"></a>Exibir métricas de desempenho da VM

O Monitor do Azure para VMs inclui um conjunto de gráficos de desempenho que segmentam vários KPIs (principais indicadores de desempenho) para ajudá-lo a determinar o desempenho de uma máquina virtual. Para acessar em sua VM, siga estas etapas.

1. No Portal do Azure, clique em **Grupos de Recursos**, selecione **myResourceGroupMonitor** e, em seguida, selecione **myVM** na lista de recursos.

2. Na página da VM, na seção **Monitoramento**, selecione **Insights (versão prévia)** .

3. Selecione o **desempenho** guia.

Esta página inclui não apenas gráficos de utilização de desempenho, mas também uma tabela mostrando cada disco lógico descoberto, sua capacidade, utilização e média total de cada medida.

## <a name="create-alerts"></a>Criar alertas

Você pode criar alertas com base em métricas de desempenho específicas. Alertas podem ser usados para lhe notificar quando uso médio da CPU excede um determinado limite ou o espaço em disco livre disponível cai abaixo de um determinado valor, por exemplo. Os alertas são exibidos no Portal do Azure, ou podem ser enviados por email. Você também pode disparar Runbooks da Automação do Azure ou Aplicativos Lógicos do Azure em resposta à geração de alertas.

O exemplo a seguir cria um alerta para uso médio da CPU.

1. No Portal do Azure, clique em **Grupos de Recursos**, selecione **myResourceGroupMonitor** e, em seguida, selecione **myVM** na lista de recursos.

2. Clique em **Regras de alerta** na folha da VM e, em seguida, clique em **Adicionar alerta de métrica** na parte superior da folha de alertas.

3. Forneça um **Nome** para o alerta, como *myAlertRule*

4. Para disparar um alerta quando o percentual de CPU excede 1.0 por cinco minutos, deixe todos os outros padrões selecionados.

5. Opcionalmente, marque a caixa de *Proprietários, colaboradores e leitores de email* para enviar uma notificação por email. A ação padrão é apresentar uma notificação no portal.

6. Clique no botão **OK**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou e exibiu o desempenho de sua VM. Você aprendeu a:

> [!div class="checklist"]
> * Criar um grupo de recursos e uma VM
> * Habilitar o diagnóstico de inicialização na VM
> * Exibir diagnóstico de inicialização
> * Exibir métricas de host
> * Habilitar o Azure Monitor para VMs
> * Exibir métricas de VM
> * Criar um alerta

Avance para o próximo tutorial para saber mais sobre a Central de Segurança do Azure.

> [!div class="nextstepaction"]
> [Gerenciar a segurança da VM](../../security/fundamentals/overview.md)
