---
title: Tutorial – Gerenciar a configuração de máquina virtual do Linux no Azure
description: Neste tutorial, você aprenderá a identificar alterações e a gerenciar atualizações de pacote em uma máquina virtual do Linux
services: virtual-machines
documentationcenter: virtual-machines
author: mgoedtel
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines
ms.collection: linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/27/2019
ms.author: magoedte
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 16e8cfd9c6b20e3a8d17ba335079b1b6fe8b499f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785011"
---
# <a name="tutorial-monitor-changes-and-update-a-linux-virtual-machine-in-azure"></a>Tutorial: Monitorar alterações e atualizar uma máquina virtual do Linux no Azure

O [Controle de Alterações](../../automation/change-tracking/overview.md) do Windows permite que você identifique com facilidade alterações e o [Gerenciamento de Atualizações](../../automation/update-management/overview.md) permite que você gerencie atualizações do sistema operacional para suas VMs Linux do Azure.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Gerenciar atualizações do Linux
> * Monitoramento de alterações e inventário

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Este tutorial requer a versão 2.0.30 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-vm"></a>Criar VM

Para ver os diagnósticos e as métricas em ação, você precisa de uma VM. Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group#az_group_create). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroupMonitor* no local *eastus*.

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

Agora, crie uma VM com [az vm create](/cli/azure/vm#az_vm_create). O seguinte exemplo cria uma VM chamada *myVM* e gera as chaves SSH, caso ainda não existam em *~/.ssh/* :

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="manage-software-updates"></a>Gerenciar atualizações de software

O Gerenciamento de Atualizações permite que você gerencie atualizações e patches para suas VMs Linux do Azure.
Diretamente de sua VM, você pode rapidamente avaliar o status de atualizações disponíveis, agendar a instalação de atualizações necessárias e examinar os resultados de implantação para verificar se as atualizações foram aplicadas com êxito na VM.

Para obter informações sobre preços, consulte [Preços de automação de Gerenciamento de Atualizações](https://azure.microsoft.com/pricing/details/automation/)

### <a name="enable-update-management"></a>Habilitar Gerenciamento de Atualizações

Habilite o Gerenciamento de Atualizações para sua VM:

1. No lado esquerdo da tela, selecione **Máquinas virtuais**.
2. Na lista, selecione uma VM.
3. Na tela da VM, na seção **Operações**, selecione **Gerenciamento de atualizações**. A tela **Habilitar Gerenciamento de Atualizações** é aberta.

Uma validação é executada para determinar se o Gerenciamento de Atualizações está habilitado para essa VM.
A validação inclui verificar se há um espaço de trabalho do Log Analytics e uma conta de automação vinculada e se a solução está no espaço de trabalho.

Um workspace do [Log Analytics](../../azure-monitor/logs/log-query-overview.md) é usado para coletar dados gerados por recursos e serviços, como o Gerenciamento de Atualizações.
O workspace fornece um único local para examinar e analisar dados de várias fontes.
Para executar ações adicionais em máquinas virtuais que requerem atualizações, a Automação do Azure permite que você execute runbooks em VMs, como download e aplicação de atualizações.

O processo de validação também verifica se a VM é provisionada com o agente do Log Analytics e o Hybrid Runbook Worker de Automação. Esse agente é usado para comunicar-se com a VM e obter informações sobre o status de atualização.

Escolha o workspace do Log Analytics e a conta de automação e selecione **Habilitar** para habilitar a solução. A solução demora até 15 minutos para habilitar.

Se algum dos seguintes pré-requisitos estiver ausente durante a integração, ele será adicionado automaticamente:

* Workspace do [Log Analytics](../../azure-monitor/logs/log-query-overview.md)
* [Conta de automação](../../automation/index.yml)
* Uma [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) está habilitada na VM

A tela **Gerenciamento de Atualizações** é exibida. Configure o local, o workspace do Log Analytics e a conta de Automação a serem usados e selecione **Habilitar**. Caso os campos estejam esmaecidos, isso significa que outra solução de automação está habilitada para a VM e o mesmo workspace e conta de Automação devem ser usados.

![Habilitar a solução de Gerenciamento de Atualizações](./media/tutorial-monitoring/manage-updates-update-enable.png)

A habilitação da solução pode levar até 15 minutos. Durante esse tempo, não feche a janela do navegador. Depois que a solução for habilitada, as informações sobre atualizações ausentes na VM fluirão para os logs do Azure Monitor. Pode levar entre 30 minutos e 6 horas para que os dados fiquem disponíveis para análise.

### <a name="view-update-assessment"></a>Exibir avaliação de atualização

Depois que o **Gerenciamento de Atualizações** for habilitado, a tela **Gerenciamento de Atualizações** será exibida. Depois que a avaliação das atualizações estiver completa, você verá uma lista de atualizações ausentes na guia **Atualizações ausentes**.

 ![Exibir o status de atualização](./media/tutorial-monitoring/manage-updates-view-status-linux.png)

### <a name="schedule-an-update-deployment"></a>Agendar uma implantação de atualização

Para instalar atualizações, agende uma implantação que siga o agendamento de versão e o período de serviço. Você pode escolher quais tipos de atualização deseja incluir na implantação. Por exemplo, você pode incluir atualizações críticas ou de segurança e excluir pacotes cumulativos de atualizações.

Agende uma nova implantação de atualização para a VM clicando em **Agendar implantação de atualização** na parte superior da tela **Gerenciamento de Atualizações**. Na tela **Nova implantação de atualização**, especifique as seguintes informações:

Para criar uma nova implantação de atualização, selecione **Agendar implantação de atualização**. A página **Nova implantação de atualizações** será aberta. Insira valores para as propriedades descritas na tabela a seguir e clique em **Criar**:

| Propriedade | Descrição |
| --- | --- |
| Nome |Nome exclusivo para identificar a Implantação de Atualizações. |
|Sistema operacional| Linux ou Windows|
| Grupos a serem atualizados |Para computadores do Azure, defina uma consulta com base em uma combinação de assinatura, grupos de recursos, localizações e marcas para criar um grupo dinâmico de VMs do Azure a ser incluído na implantação. </br></br>Para computadores que não são Azure, selecione uma pesquisa salva existente para selecionar um grupo de computadores que não são Azure a serem incluídos na implantação. </br></br>Para obter mais informações, consulte [grupos dinâmicos](../../automation/update-management/configure-groups.md)|
| Computadores para atualizar |Selecione uma pesquisa salva, um grupo importado ou selecione a máquina na lista suspensa e selecione máquinas individuais. Se você escolher **Machines**, a prontidão da máquina é mostrada na coluna **UPDATE AGENT READINESS**.</br> Para saber mais sobre os diferentes métodos de criação de grupos de computadores nos logs do Azure Monitor, veja [Grupos de computadores nos logs do Azure Monitor](../../azure-monitor/logs/computer-groups.md) |
|Classificações de origem|Selecione todas as classificações de atualização necessárias|
|Incluir/excluir atualizações|Isso abre o **incluir/excluir** página. As atualizações a serem incluídas ou excluídas estão em guias separadas. Para obter mais informações sobre como a inclusão é tratada, confira [Agendar uma implantação de atualizações](../../automation/update-management/deploy-updates.md#schedule-an-update-deployment) |
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
Selecione a implantação de atualização concluída para ver o painel dessa implantação de atualização.

![Painel de status de implantação de atualização para implantação específica](./media/tutorial-monitoring/manage-updates-view-results.png)

No bloco **Resultados de atualização** há um resumo do número total de atualizações e os resultados da implantação na VM.
Na tabela à direita há uma análise detalhada de cada atualização e os resultados da instalação, que podem ser um dos seguintes valores:

* **Não foi tentada** – a atualização não foi instalada devido a tempo suficiente disponível com base na duração da janela de manutenção definida.
* **Êxito** – a atualização foi bem-sucedida
* **Falha** – Falha na atualização

Selecione **Todos os logs** para ver todas as entradas de log que a implantação criou.

Selecione o bloco **Saída** para ver o fluxo de trabalho do runbook responsável por gerenciar a implantação de atualização na VM de destino.

Selecione **Erros** para ver informações detalhadas sobre quaisquer erros da implantação.

## <a name="monitor-changes-and-inventory"></a>Monitoramento de alterações e inventário

É possível coletar e exibir inventário para software, arquivos, daemons do Linux, serviços do Windows e chaves de Registro do Windows em seus computadores. Acompanhar as configurações de seus computadores pode ajudar a detectar problemas operacionais em seu ambiente e entender melhor o estado dos seus computadores.

### <a name="enable-change-and-inventory-management"></a>Habilitar Alterações e Gerenciamento de Estoque

Habilitar Alterações e Gerenciamento de Estoque para a sua VM:

1. No lado esquerdo da tela, selecione **Máquinas virtuais**.
2. Na lista, selecione uma VM.
3. Na tela da VM, na seção **Operações**, selecione **Estoque** ou **Rastrear mudanças**. A tela **Habilitar Controle de Alterações e Estoque** é aberta.

Configure o local, o workspace do Log Analytics e a conta de Automação a serem usados e selecione **Habilitar**. Caso os campos estejam esmaecidos, isso significa que outra solução de automação está habilitada para a VM e o mesmo workspace e conta de Automação devem ser usados. Mesmo que as soluções estejam separadas no menu, elas são a mesma solução. Habilitar uma permite ambos para sua VM.

![Habilitar Controle de Alterações e Inventário](./media/tutorial-monitoring/manage-inventory-enable.png)

Depois que a solução é habilitada, leva algum tempo para os dados serem exibidos enquanto o inventário está sendo coletado na VM.

### <a name="track-changes"></a>Controle de alterações

Na sua VM, selecione **Controle de alterações** em **OPERAÇÕES**. Selecione **Editar configurações**, a página **Controle de alterações** é exibida. Selecione o tipo de configuração que você deseja acompanhar e, em seguida, selecione **+Adicionar** para definir as configurações. A opção disponível para Linux é **Arquivos Linux**

Para obter informações detalhadas sobre o Controle de Alterações, consulte [Solucionar as alterações em uma máquina virtual](../../automation/automation-tutorial-troubleshoot-changes.md)

### <a name="view-inventory"></a>Exibição do Inventário

Na sua VM, selecione **Inventário** em **OPERAÇÕES**. Na guia **Software**, há uma lista de tabelas que o software havia encontrado. Os detalhes de alto nível de cada registro de software estão visíveis na tabela. Esses detalhes incluem o nome do software, a versão, publicador, hora da última atualização.

![Exibição do Inventário](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Monitorar os logs de atividades e alterações

Na página **Controle de alterações** em sua VM, selecione **Gerenciar Conexão do Log de Atividades**. Essa tarefa abre a página **Log de Atividades do Azure**. Selecione **Conectar** para conectar o Controle de alterações ao log de atividades do Azure para sua VM.

Com essa configuração habilitada, navegue até a página **Visão geral** para a VM e selecione **Parar** para interromper a VM. Quando solicitado, selecione **Sim** para interromper a VM. Quando ele for desalocado, selecione **Iniciar** para reiniciar a VM.

Interromper e iniciar uma VM registra um evento em seu log de atividades. Navegue de volta para a página **Controle de alterações**. Selecione a guia **Eventos** na parte inferior da página. Após algum tempo, os eventos são exibidos no gráfico e na tabela. Cada evento pode ser selecionado para exibir informações detalhadas sobre ele.

![Visualizar categorias no log de atividades](./media/tutorial-monitoring/manage-activitylog-view-results.png)

O gráfico mostra as alterações que ocorreram ao longo do tempo. Depois de adicionar uma conexão do Log de Atividades, o grafo da linha na parte superior exibe eventos do Log de Atividades do Azure. Cada linha de gráficos de barras representa um tipo de alteração controlável diferente. Esses tipos são daemons, arquivos e software Linux. A guia de alteração mostra os detalhes para as alterações mostradas na visualização em ordem decrescente de hora em que a alteração ocorreu (mais recentes primeiro).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou e revisou o Controle de Alterações e o Gerenciamento de Atualizações de sua VM. Você aprendeu a:

> [!div class="checklist"]
> * Criar um grupo de recursos e uma VM
> * Gerenciar atualizações do Linux
> * Monitoramento de alterações e inventário

Passe para o próximo tutorial para saber mais sobre como monitorar sua VM.

> [!div class="nextstepaction"]
> [Monitorar máquinas virtuais](/previous-versions/azure/virtual-machines/linux/tutorial-monitor)