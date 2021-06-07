---
title: Tutorial – Gerenciar a configuração de máquina virtual do Windows no Azure
description: Neste tutorial, você aprenderá a identificar alterações e a gerenciar atualizações de pacote em uma máquina virtual do Windows
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: db7c0f2dbc547d78e3caa9ab37450c86278929bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102555934"
---
# <a name="tutorial-monitor-changes-and-update-a-windows-virtual-machine-in-azure"></a>Tutorial: Monitorar alterações e atualizar uma máquina virtual do Windows no Azure

Com o [Controle de Alterações](../../automation/change-tracking/overview.md) e o [Gerenciamento de Atualizações](../../automation/update-management/overview.md) do Azure, você pode identificar com facilidade as alterações em suas máquinas virtuais do Windows no Azure e gerenciar as atualizações do sistema operacional para essas VMs.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Gerenciar as atualizações do Windows.
> * Monitorar as alterações e o inventário.

## <a name="open-azure-cloud-shell"></a>Abrir o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas comuns do Azure pré-instaladas e configuradas para uso com a sua conta do Azure.

Para abrir qualquer bloco de código no Cloud Shell, basta selecionar **Experimentar** no canto superior direito do bloco de código.

Você também pode abrir o Cloud Shell em uma guia separada do navegador indo até [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selecione **Copiar** para copiar blocos de código, cole-os na guia do Cloud Shell e selecione a tecla Enter para executar o código.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Para configurar o monitoramento do Azure e o gerenciamento de atualizações neste tutorial, você precisa de uma VM Windows no Azure.

Primeiro, defina o nome de usuário e a senha de um administrador para a VM com [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Em seguida, crie a VM com [New-AzVM](/powershell/module/az.compute/new-azvm). O exemplo a seguir cria uma VM chamada `myVM` na localização `East US`. Se ainda não existirem, o grupo de recursos `myResourceGroupMonitor` e os recursos de rede de suporte serão criados:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

Demora alguns minutos para que os recursos e a VM sejam criados.

## <a name="manage-windows-updates"></a>Gerenciar atualizações do Windows

O Gerenciamento de Atualizações ajuda você a gerenciar atualizações e patches para suas VMs do Windows no Azure. Diretamente na VM, com agilidade, você pode:

- Avaliar o status das atualizações disponíveis.
- Agendar a instalação de atualizações necessárias.
- Examinar os resultados da implantação para verificar se as atualizações foram aplicadas com êxito à VM.

Para obter informações sobre preços, confira [Preços de automação para gerenciamento de atualizações](https://azure.microsoft.com/pricing/details/automation/).

### <a name="enable-update-management"></a>Habilitar Gerenciamento de Atualizações

Para habilitar o Gerenciamento de Atualizações na VM:

1. No lado mais à esquerda da janela, selecione **Máquinas virtuais**.
1. Escolha uma VM na lista.
1. No painel **Operações** da janela da VM, selecione **Gerenciamento de Atualizações**.
1. A janela **Habilitar Gerenciamento de Atualizações** é aberta.

Uma validação é executada para determinar se o Gerenciamento de Atualizações está habilitado nessa VM. A validação inclui verificar se há um workspace do Log Analytics, uma conta de Automação vinculada e se a solução está no workspace.

Use um workspace do [Log Analytics](../../azure-monitor/logs/log-query-overview.md) para coletar dados que são gerados por recursos e serviços, como o Gerenciamento de Atualizações. O workspace fornece um único local para examinar e analisar dados de várias fontes.

Para executar ações adicionais em VMs que exigem atualizações, use a Automação do Azure para executar runbooks nas VMs. Essas ações incluem o download ou a aplicação de atualizações.

O processo de validação também verifica se a VM é provisionada com o MMA (Microsoft Monitoring Agent) e o Hybrid Runbook Worker de Automação. Use o agente para se comunicar com a VM e obter informações sobre o status de atualização.

Na janela **Habilitar o Gerenciamento de Atualizações**, escolha o workspace do Log Analytics e a conta de automação e, em seguida, selecione **Habilitar**. A solução leva até 15 minutos para ser habilitada.

Qualquer um dos seguintes pré-requisitos ausentes durante a integração é adicionado automaticamente:

* Workspace do [Log Analytics](../../azure-monitor/logs/log-query-overview.md)
* [Automação](../../automation/index.yml)
* Um [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md), que está habilitado na VM

Depois que a solução é habilitada, a janela **Gerenciamento de Atualizações** é aberta. Configure a localização, o workspace do Log Analytics e a conta de Automação a serem usados e, em seguida, selecione **Habilitar**. Se essas opções aparecerem esmaecidas, outra solução de automação será habilitada para a VM, e o workspace e a conta de automação dessa solução precisarão ser usados.

![Habilitar a solução Gerenciamento de Atualizações](./media/tutorial-monitoring/manageupdates-update-enable.png)

A solução Gerenciamento de Atualizações pode levar até 15 minutos para ser habilitada. Durante esse tempo, não feche a janela do navegador. Depois que a solução for habilitada, as informações sobre atualizações ausentes na VM fluirão para os logs do Azure Monitor. Pode levar de 30 minutos a 6 horas para que os dados fiquem disponíveis para análise.

### <a name="view-an-update-assessment"></a>Exibir uma avaliação de atualização

Depois que o Gerenciamento de Atualizações for habilitado, a janela **Gerenciamento de Atualizações** será exibida. Depois que a avaliação das atualizações for concluída, você verá uma lista de atualizações ausentes na guia **Atualizações ausentes**.

 ![Exibir o status de atualização](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>Agendar uma implantação de atualização

Para instalar atualizações, agende uma implantação que siga o agendamento de versão e o período de serviço. Escolha quais tipos de atualizações deseja incluir na implantação. Por exemplo, você pode incluir atualizações críticas ou de segurança e excluir pacotes cumulativos de atualizações.

Para agendar uma nova implantação de atualizações para a VM, selecione **Agendar implantação de atualizações** na parte superior da janela **Gerenciamento de Atualizações**. Na janela **Nova implantação de atualização**, especifique as seguintes informações:

| Opção | Descrição |
| --- | --- |
| **Nome** |Insira um nome exclusivo para identificar a implantação de atualizações. |
|**Sistema operacional**| Selecione **Linux** ou **Windows**.|
| **Grupos para atualização** |Para as VMs hospedadas no Azure, defina uma consulta com base em uma combinação de assinatura, grupos de recursos, localizações e marcas. Essa consulta cria um grupo dinâmico de VMs hospedadas no Azure a serem incluídas em sua implantação. </br></br>Para as VMs não hospedadas no Azure, selecione uma pesquisa salva existente. Com essa pesquisa, você pode selecionar um grupo dessas VMs a ser incluído na implantação. </br></br> Para saber mais, confira [Grupos dinâmicos](../../automation/update-management/configure-groups.md).|
| **Computadores para atualização** |Selecione **Pesquisa salva**, **Grupos importados** ou **Computadores**.<br/><br/>Se você selecionar **Computadores**, poderá escolher computadores individuais na lista suspensa. A preparação de cada computador é mostrada na coluna **PREPARAÇÃO DO AGENTE DE ATUALIZAÇÃO** da tabela.</br></br> Para saber mais sobre os diferentes métodos de criação de grupos de computadores nos logs do Azure Monitor, veja [Grupos de computadores nos logs do Azure Monitor](../../azure-monitor/logs/computer-groups.md) |
|**Classificações de atualização**|Escolha todas as classificações de atualização necessárias.|
|**Incluir/excluir atualizações**|Selecione essa opção para abrir o painel **Incluir/Excluir**. As atualizações a serem incluídas e aquelas a serem excluídas estão em guias separadas. Para obter mais informações sobre como a inclusão é tratada, confira [Agendar uma implantação de atualização](../../automation/update-management/deploy-updates.md#schedule-an-update-deployment). |
|**Configurações do agendamento**|Escolha a hora de início e selecione **Uma vez** ou **Recorrente**.|
| **Pré-scripts + Pós-scripts**|Escolha os scripts a serem executados antes e depois da implantação.|
| **Janela de manutenção** | Insira o número de minutos definido para atualizações. Os valores válidos vão de 30 a 360 minutos. |
| **Controle de reinicialização**| Selecione como as reinicializações são tratadas. As seleções disponíveis são:<ul><li>**Reinicializar se necessário**</li><li>**Sempre reinicializar**</li><li>**Nunca reinicializar**</li><li>**Somente reinicializar**</li></ul>**Reinicializar se necessário** é a seleção padrão. Se você selecionar **Somente reinicializar**, as atualizações não serão instaladas.|

Depois de concluir a configuração do agendamento, clique em **Criar** para retornar ao painel de status. A tabela **Agendado** mostra o agendamento de implantação criado.

Crie também implantações de atualizações de forma programática. Para saber como criar uma implantação de atualizações com a API REST, confira [Configurações de atualização de software – Criar](/rest/api/automation/softwareupdateconfigurations/create). Há também um runbook de exemplo que pode ser usado para criar uma implantação de atualizações semanal. Para saber mais sobre este runbook, consulte [Criar uma implantação de atualização semanal para uma ou mais VMs em um grupo de recursos](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

### <a name="view-results-of-an-update-deployment"></a>Exibir resultados de uma implantação de atualização

Após o início da implantação agendada, você poderá ver o status da implantação na guia **Implantações de atualizações** na janela **Gerenciamento de Atualizações**.

Se a implantação estiver em execução no momento, o status será mostrado como "Em andamento". Após a conclusão bem-sucedida, o status será alterado para "Êxito". Porém, se uma atualização na implantação falhar, o status será "Falha parcial".

Selecione a implantação de atualizações concluída para ver o painel dessa implantação.

![Painel de status de implantação de atualização para implantação específica](./media/tutorial-monitoring/manageupdates-view-results.png)

O bloco **Resultados da atualização** mostra um resumo do número total de atualizações e os resultados da implantação na VM. A tabela à direita mostra um detalhamento de cada atualização e os resultados da instalação. Cada resultado tem um dos seguintes valores:

* **Nenhuma tentativa**: A atualização não está instalada. Não houve tempo suficiente disponível com base na duração da janela de manutenção definida.
* **Bem-sucedido**: A atualização foi bem-sucedida.
* **Falhou**: Falha na atualização.

Selecione **Todos os logs** para ver todas as entradas de log que a implantação criou.

Selecione o bloco **Saída** para ver o fluxo de trabalho do runbook responsável por gerenciar a implantação de atualizações na VM de destino.

Selecione **Erros** para ver informações detalhadas sobre os erros de implantação.

## <a name="monitor-changes-and-inventory"></a>Monitoramento de alterações e inventário

Você pode coletar e ver um inventário do software, de arquivos, de daemons do Linux, de serviços Windows e das chaves do Registro do Windows em seus computadores. O acompanhamento das configurações dos computadores ajuda você a detectar problemas operacionais em seu ambiente e entender melhor o estado dos computadores.

### <a name="enable-change-and-inventory-management"></a>Habilitar o gerenciamento de alterações e de estoque

Para habilitar o gerenciamento de alterações e de estoque em sua VM:

1. No lado mais à esquerda da janela, selecione **Máquinas virtuais**.
1. Escolha uma VM na lista.
1. Em **Operações** na janela da VM, selecione **Inventário** ou **Controle de alterações**.
1. O painel **Habilitar Controle de Alterações e Inventário** será aberto.

Configure a localização, o workspace do Log Analytics e a conta de Automação a serem usados e, em seguida, selecione **Habilitar**. Se as opções aparecerem esmaecidas, uma solução de automação já estará habilitada para a VM. Nesse caso, o workspace e a conta de Automação já habilitados precisam ser usados.

Embora as soluções sejam exibidas separadamente no menu, elas constituem a mesma solução. Habilitar uma permite ambos para sua VM.

![Habilitar Controle de Alterações e Inventário](./media/tutorial-monitoring/manage-inventory-enable.png)

Depois que a solução é habilitada, pode levar algum tempo para o inventário ser coletado na VM antes que os dados sejam exibidos.

### <a name="track-changes"></a>Controle de alterações

Na VM em **OPERAÇÕES**, selecione **Controle de Alterações** e, em seguida, **Editar Configurações**. O painel **Controle de Alterações** será aberto. Selecione o tipo de configuração que você deseja acompanhar e, em seguida, selecione **+Adicionar** para definir as configurações.

As opções de configurações disponíveis para o Windows são:

* Registro do Windows
* Arquivos do Windows

Para obter informações detalhadas sobre o Controle de Alterações, confira [Solução de problemas de alterações em uma VM](../../automation/automation-tutorial-troubleshoot-changes.md).

### <a name="view-inventory"></a>Exibição do Inventário

Na sua VM, selecione **Inventário** em **OPERAÇÕES**. Na guia **Software**, há uma tabela que mostra o software encontrado. Os detalhes de alto nível de cada registro de software são exibidos na tabela. Esses detalhes incluem o nome do software, a versão, o fornecedor e a hora da última atualização.

![Exibição do Inventário](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Monitorar os logs de atividades e as alterações

Na janela **Controle de Alterações** da VM, selecione **Gerenciar a Conexão do Log de Atividades** para abrir o painel **Log de atividades do Azure**. Selecione **Conectar** para conectar o Controle de Alterações ao log de atividades do Azure para a VM.

Depois que o Controle de Alterações for habilitado, acesse o painel **Visão geral** da VM e selecione **Parar** para interromper a VM. Quando solicitado, selecione **Sim** para interromper a VM. Depois que a VM for desalocada, selecione **Iniciar** para reiniciar a VM.

A parada e a reinicialização de uma VM registram um evento no log de atividades dela. Volte ao painel **Controle de Alterações** e selecione a guia **Eventos** na parte inferior do painel. Após alguns instantes, os eventos serão exibidos no gráfico e na tabela. Selecione cada evento para exibir informações detalhadas desse evento.

![Visualizar categorias no log de atividades](./media/tutorial-monitoring/manage-activitylog-view-results.png)

O gráfico anterior mostra as alterações que ocorreram ao longo do tempo. Depois que você adicionar uma conexão do log de atividades do Azure, o grafo de linha na parte superior exibirá os eventos do log de atividades do Azure.

Cada linha de grafos de barras representa um tipo de alteração controlável diferente. Esses tipos são daemons do Linux, arquivos, chaves do Registro do Windows, software e serviços Windows. A guia **Alteração** mostra os detalhes da alteração. As alterações são exibidas na ordem de cada ocorrência, com a alteração mais recente mostrada primeiro.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou e revisou o Controle de Alterações e o Gerenciamento de Atualizações de sua VM. Você aprendeu a:

> [!div class="checklist"]
> * Criar um grupo de recursos e uma VM.
> * Gerenciar as atualizações do Windows.
> * Monitorar as alterações e o inventário.

Vá para o próximo tutorial para saber mais sobre como monitorar sua VM.

> [!div class="nextstepaction"]
> [Monitorar máquinas virtuais](tutorial-monitor.md)
