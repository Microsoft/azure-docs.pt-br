---
title: "Monitoramento do Azure e atualização de Máquinas Virtuais do Windows | Microsoft Docs"
description: "Tutorial – monitore e atualize uma máquina Virtual do Windows com o Azure PowerShell"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/04/2017
ms.author: davidmu
ms.custom: mvc
ms.openlocfilehash: a37aed8b3321d3518ffd73e09f5bb21266a7e577
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-and-update-a-windows-virtual-machine-with-azure-powershell"></a>Monitorar e atualizar uma máquina Virtual do Windows com o Azure PowerShell

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
> * Configurar monitoramento avançado

Este tutorial requer o módulo do Azure PowerShell, versão 3.6 ou posterior. Execute ` Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps).

Para concluir o exemplo neste tutorial, você deverá ter uma máquina virtual. Se necessário, este [exemplo de script](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) pode criar uma para você. Ao trabalhar com este tutorial, substitua o grupo de recursos, o nome da VM e a localização quando for necessário.

## <a name="view-boot-diagnostics"></a>Exibir diagnóstico de inicialização

Conforme as máquinas virtuais do Windows são inicializadas, o agente de diagnóstico de inicialização captura a saída na tela que pode ser usada para a finalidade de solução de problemas. Essa funcionalidade é habilitada por padrão. As capturas de tela são armazenadas em uma conta de Armazenamento do Azure, que também é criada por padrão. 

Você pode obter os dados de diagnóstico de inicialização com o comando [Get-AzureRmVMBootDiagnosticsData](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmbootdiagnosticsdata). No exemplo a seguir, diagnóstico de inicialização é baixado para a raiz da unidade *c:\*. 

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName myResourceGroup -Name myVM -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>Exibir métricas de host

Uma VM do Windows tem uma VM Host dedicada no Azure com a qual ele interage. As métricas são coletadas automaticamente para o Host e podem ser exibidas no Portal do Azure.

1. No Portal do Azure, clique em **Grupos de Recursos**, selecione **myResourceGroup** e, em seguida, selecione **myVM** na lista de recursos.
2. Clique em **Métricas** na folha da VM e, em seguida, selecione qualquer uma das métricas de Host em **Métricas disponíveis** para ver como está o desempenho da VM do Host.

    ![Exibir métricas de host](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="install-diagnostics-extension"></a>Instalar extensão de diagnóstico

As métricas de host básicas estão disponíveis, mas para ver métricas mais granulares e específicas de VM, você precisa instalar a extensão de Diagnóstico do Azure na VM. A extensão de Diagnóstico do Azure permite que dados de monitoramento e diagnóstico adicionais sejam recuperados da VM. Você pode exibir essas métricas de desempenho e criar alertas com base no desempenho de uma máquina virtual. A extensão de diagnóstico é instalada por meio do Portal do Azure, da seguinte maneira:

1. No Portal do Azure, clique em **Grupos de Recursos**, selecione **myResourceGroup** e, em seguida, selecione **myVM** na lista de recursos.
2. Clique em **Configurações de diagnóstico**. A lista mostra que o *Diagnóstico de inicialização* já está habilitado na seção anterior. Clique na caixa de seleção para as *Métricas básicas*.
3. Clique no botão **Habilitar o monitoramento em nível de convidado**.

    ![Exibir métricas de diagnóstico](./media/tutorial-monitoring/enable-diagnostics-extension.png)

## <a name="view-vm-metrics"></a>Exibir métricas de VM

Você pode exibir as métricas da VM da mesma maneira que você exibiu as métricas da VM host:

1. No Portal do Azure, clique em **Grupos de Recursos**, selecione **myResourceGroup** e, em seguida, selecione **myVM** na lista de recursos.
2. Para ver como está o desempenho da VM do Host, clique em **Métricas** na folha da VM e, em seguida, selecione qualquer uma das métricas de diagnóstico em **Métricas disponíveis**.

    ![Exibir métricas de VM](./media/tutorial-monitoring/monitor-vm-metrics.png)

## <a name="create-alerts"></a>Criar alertas

Você pode criar alertas com base em métricas de desempenho específicas. Alertas podem ser usados para lhe notificar quando uso médio da CPU excede um determinado limite ou o espaço em disco livre disponível cai abaixo de um determinado valor, por exemplo. Os alertas são exibidos no Portal do Azure, ou podem ser enviados por email. Você também pode disparar Runbooks da Automação do Azure ou Aplicativos Lógicos do Azure em resposta à geração de alertas.

O exemplo a seguir cria um alerta para uso médio da CPU.

1. No Portal do Azure, clique em **Grupos de Recursos**, selecione **myResourceGroup** e, em seguida, selecione **myVM** na lista de recursos.
2. Clique em **Regras de alerta** na folha da VM e, em seguida, clique em **Adicionar alerta de métrica** na parte superior da folha de alertas.
4. Forneça um **Nome** para o alerta, como *myAlertRule*
5. Para disparar um alerta quando o percentual de CPU excede 1.0 por cinco minutos, deixe todos os outros padrões selecionados.
6. Opcionalmente, marque a caixa de *Proprietários, colaboradores e leitores de email* para enviar uma notificação por email. A ação padrão é apresentar uma notificação no portal.
7. Clique no botão **OK**.

## <a name="manage-windows-updates"></a>Gerenciar atualizações do Windows

O Gerenciamento de Atualizações permite que você gerencie atualizações e patches para suas VMs do Windows do Azure.
Diretamente de sua VM, você pode rapidamente avaliar o status de atualizações disponíveis, agendar a instalação de atualizações necessárias e examinar os resultados de implantação para verificar se as atualizações foram aplicadas com êxito na VM.

Para obter informações sobre preços, consulte [Preços de automação de Gerenciamento de Atualizações](https://azure.microsoft.com/pricing/details/automation/)

### <a name="enable-update-management"></a>Habilitar Gerenciamento de Atualizações

Habilite o Gerenciamento de Atualizações para sua VM:
 
1. No lado esquerdo da tela, selecione **Máquinas virtuais**.
2. Na lista, selecione uma VM.
3. Na tela da VM, na seção **Operações**, clique em **Gerenciamento de Atualizações**. A tela **Habilitar Gerenciamento de Atualizações** é aberta.

Uma validação é executada para determinar se o Gerenciamento de Atualizações está habilitado para essa VM. A validação inclui verificar se há um espaço de trabalho do Log Analytics e uma conta de automação vinculada e se a solução está no espaço de trabalho.

Um espaço de trabalho do Log Analytics é usado para coletar dados que são gerados pelos recursos e serviços, como o Gerenciamento de Atualizações. O espaço de trabalho fornece um único local para examinar e analisar dados de várias fontes. Para executar uma ação adicional em VMs que exigem atualizações, a Automação do Azure permite executar scripts em VMs, como baixar e aplicar atualizações.

O processo de validação também verifica se a VM é provisionada com o MMA (Microsoft Monitoring Agent) e o Hybrid Worker. Esse agente é usado para comunicar-se com a VM e obter informações sobre o status de atualização. 

Se esses pré-requisitos não forem atendidos, será exibida uma faixa oferecendo a opção para habilitar a solução.

![Faixa de configuração integrada do Gerenciamento de Atualizações](./media/tutorial-monitoring/manageupdates-onboard-solution-banner.png)

Clique na faixa para habilitar a solução. Se algum dos seguintes pré-requisitos estiver ausente após a validação, ele será adicionado automaticamente:

* Espaço de trabalho do [Log Analytics](../../log-analytics/log-analytics-overview.md)
* [Automação](../../automation/automation-offering-get-started.md)
* Uma [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) está habilitada na VM

A tela **Habilitar Gerenciamento de Atualizações** é aberta. Defina as configurações e clique em **Habilitar**.

![Habilitar a solução de Gerenciamento de Atualizações](./media/tutorial-monitoring/manageupdates-update-enable.png)

A habilitação da solução pode levar até 15 minutos e, durante esse período, você não deve fechar a janela do navegador. Depois que a solução for habilitada, as informações sobre atualizações ausentes na VM fluirão para o Log Analytics.
Pode levar entre 30 minutos e 6 horas para que os dados fiquem disponíveis para análise.

### <a name="view-update-assessment"></a>Exibir avaliação de atualização

Depois que o **Gerenciamento de Atualizações** for habilitado, a tela **Gerenciamento de Atualizações** será exibida. Você pode ver uma lista de atualizações ausentes na guia **Atualizações ausentes**.

 ![Exibir o status de atualização](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>Agendar uma implantação de atualização

Para instalar atualizações, agende uma implantação que siga o agendamento de versão e o período de serviço.
Você pode escolher quais tipos de atualização deseja incluir na implantação. Por exemplo, você pode incluir atualizações críticas ou de segurança e excluir pacotes cumulativos de atualizações.

Agende uma nova implantação de atualização para a VM clicando em **Agendar implantação de atualização** na parte superior da tela **Gerenciamento de Atualizações**. Na tela **Nova implantação de atualização**, especifique as seguintes informações:

* **Nome** – forneça um nome exclusivo para identificar a Implantação de atualizações.
* **Classificação de atualização** – selecione os tipos de software que a implantação de atualização incluiu na implantação. Os tipos de classificação são:
  * Atualizações críticas
  * Atualizações de segurança
  * Pacotes cumulativos de atualização
  * Feature packs
  * Service packs
  * Atualizações de definição
  * Ferramentas
  * Atualizações

* **Configurações de agenda** – você pode aceitar a data e hora padrão, que é de 30 minutos após a hora atual ou especificar um horário diferente.
  Você também pode especificar se a implantação ocorre uma única vez ou configurar um agendamento recorrente. Clique na opção Recorrente em Recorrência para configurar um agendamento recorrente.

  ![Tela de configurações de agenda de atualização](./media/tutorial-monitoring/manageupdates-schedule-win.png)

* **Janela de manutenção (minutos)** – especifique o período de tempo em que deseja que a implantação de atualização ocorra.  Isso ajuda a garantir que as alterações sejam executadas dentro das janelas de serviço definidas.

Depois de concluir a configuração da agenda, clique no botão **Criar** e retorne ao painel de status.
Observe que a tabela **Agendado** mostra a agenda de implantação criada.

> [!WARNING]
> Para atualizações que exigem uma reinicialização, a VM será reiniciada automaticamente.

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

## <a name="advanced-monitoring"></a>Monitoramento avançado 

Você pode fazer um monitoramento mais avançado da sua VM usando o [Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview). Caso ainda não tenha feito isso, você pode se inscrever para uma [avaliação gratuita](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-trial) do Operations Management Suite.

Quando você tem acesso ao portal do OMS, você pode encontrar a chave e o identificador de espaço de trabalho na folha Configurações. Use o comando [Set-AzureRmVMExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmextension) para adicionar a extensão do OMS na VM. Atualize os valores das variáveis na amostra abaixo para refletir a ID e a chave do espaço de trabalho do OMS.  

```powershell
$omsId = "<Replace with your OMS Id>"
$omsKey = "<Replace with your OMS key>"

Set-AzureRmVMExtension -ResourceGroupName myResourceGroup `
  -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
  -VMName myVM `
  -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
  -ExtensionType "MicrosoftMonitoringAgent" `
  -TypeHandlerVersion 1.0 `
  -Settings @{"workspaceId" = $omsId} `
  -ProtectedSettings @{"workspaceKey" = $omsKey} `
  -Location eastus
```

Depois de alguns minutos, você deverá ver a nova VM no espaço de trabalho do OMS. 

![Folha do OMS](./media/tutorial-monitoring/tutorial-monitor-oms.png)

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
> * Configurar monitoramento avançado

Avance para o próximo tutorial para saber mais sobre a Central de Segurança do Azure.

> [!div class="nextstepaction"]
> [Gerenciar a segurança da VM](./tutorial-azure-security.md)