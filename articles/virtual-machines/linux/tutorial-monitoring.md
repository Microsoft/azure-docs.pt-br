---
title: "Monitorar e atualizar máquinas virtuais do Linux no Azure | Microsoft Docs"
description: "Saiba como monitorar as métricas de desempenho e diagnóstico de inicialização e gerenciar atualizações de pacote em uma máquina virtual do Linux no Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/08/2017
ms.author: davidmu
ms.custom: mvc
ms.openlocfilehash: 70c17d9a8f7bf6d9106efcb56eee7cd996460c18
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-monitor-and-update-a-linux-virtual-machine-in-azure"></a>Como monitorar e atualizar uma máquina virtual do Linux no Azure

Para garantir que suas VMs (máquinas virtuais) no Azure estejam sendo executadas corretamente, examine o diagnóstico de inicialização e as métricas de desempenho e gerencie as atualizações de pacote. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Habilitar o diagnóstico de inicialização na VM
> * Exibir diagnóstico de inicialização
> * Exibir métricas de host
> * Habilitar a extensão de diagnóstico na VM
> * Exibir métricas de VM
> * Criar alertas com base nas métricas de diagnóstico
> * Gerenciar atualizações de pacote
> * Configurar monitoramento avançado


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá que você execute a CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

## <a name="create-vm"></a>Criar VM

Para ver os diagnósticos e as métricas em ação, você precisa de uma VM. Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group#create). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroupMonitor* no local *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupMonitor --location eastus
```

Agora, crie uma VM com [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create). O exemplo a seguir cria uma VM chamada *myVM*:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="enable-boot-diagnostics"></a>Habilitar o diagnóstico de inicialização

Durante a inicialização das VMs do Linux, a extensão de diagnóstico de inicialização captura a saída de inicialização e a armazena no Armazenamento do Azure. Esses dados podem ser usados para solucionar problemas de inicialização da VM. Os diagnósticos de inicialização não são habilitados automaticamente quando você cria uma VM do Linux usando a CLI do Azure.

Antes de habilitar o diagnóstico de inicialização, é necessário criar uma conta de armazenamento para armazenar os logs de inicialização. As contas de armazenamento devem ter um nome exclusivo globalmente, com três a 24 caracteres e conter apenas números e letras minúsculas. Crie uma conta de armazenamento com o comando [az storage account create](/cli/azure/storage/account#create). Neste exemplo, uma cadeia de caracteres aleatória é usada para criar um nome de conta de armazenamento exclusivo. 

```azurecli-interactive 
storageacct=mydiagdata$RANDOM

az storage account create \
  --resource-group myResourceGroupMonitor \
  --name $storageacct \
  --sku Standard_LRS \
  --location eastus
```

Ao habilitar o diagnóstico de inicialização, o URI do contêiner de armazenamento de blobs é exigido. O comando a seguir consulta a conta de armazenamento para retornar esse URI. O valor do URI é armazenado em uma variável chamada *bloburi*, que é usada na próxima etapa.

```azurecli-interactive 
bloburi=$(az storage account show --resource-group myResourceGroupMonitor --name $storageacct --query 'primaryEndpoints.blob' -o tsv)
```

Agora, habilite o diagnóstico de inicialização com [az vm boot-diagnostics enable](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_enable). O valor `--storage` é o URI do blob coletado na etapa anterior.

```azurecli-interactive 
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```


## <a name="view-boot-diagnostics"></a>Exibir diagnóstico de inicialização

Quando o diagnóstico de inicialização for habilitado, sempre que você parar e iniciar a VM, as informações sobre o processo de inicialização serão gravadas em um arquivo de log. Para este exemplo, primeiro desaloque a VM com o comando [az vm deallocate](/cli/azure/vm#deallocate) da seguinte maneira:

```azurecli-interactive 
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

Agora, inicie a VM com o comando [az vm start]( /cli/azure/vm#stop) da seguinte maneira:

```azurecli-interactive 
az vm start --resource-group myResourceGroupMonitor --name myVM
```

Você pode obter os dados de diagnóstico de inicialização para *myVM* com o comando [az vm boot-diagnostics get-boot-log](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_get_boot_log) da seguinte maneira:

```azurecli-interactive 
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```


## <a name="view-host-metrics"></a>Exibir métricas de host

Uma VM do Linux tem um host dedicado no Azure com o qual ela interage. As métricas são coletadas automaticamente para o host e podem ser exibidas no Portal do Azure da seguinte maneira:

1. No Portal do Azure, clique em **Grupos de Recursos**, selecione **myResourceGroupMonitor** e, em seguida, selecione **myVM** na lista de recursos.
1. Para ver como está o desempenho da VM do host, clique em **Métricas** na folha da VM e, em seguida, selecione qualquer uma das métricas de *Host* em **Métricas disponíveis**.

    ![Exibir métricas de host](./media/tutorial-monitoring/monitor-host-metrics.png)


## <a name="install-diagnostics-extension"></a>Instalar extensão de diagnóstico

> [!IMPORTANT]
> Este documento descreve a versão 2.3 da Extensão de Diagnóstico do Linux, a qual está preterida. A versão 2.3 será suportada até 30 de junho de 2018.
>
> A versão 3.0 da Extensão de Diagnóstico do Linux pode ser ativada em vez disso. Para obter mais informações, consulte a [documentação](./diagnostic-extension.md).

As métricas de host básicas estão disponíveis, mas para ver métricas mais granulares e específicas de VM, você precisa instalar a extensão de Diagnóstico do Azure na VM. A extensão de Diagnóstico do Azure permite que dados de monitoramento e diagnóstico adicionais sejam recuperados da VM. Você pode exibir essas métricas de desempenho e criar alertas com base no desempenho de uma máquina virtual. A extensão de diagnóstico é instalada por meio do Portal do Azure, da seguinte maneira:

1. No Portal do Azure, clique em **Grupos de Recursos**, selecione **myResourceGroup** e, em seguida, selecione **myVM** na lista de recursos.
1. Clique em **Configurações de diagnóstico**. A lista mostra que o *Diagnóstico de inicialização* já está habilitado na seção anterior. Clique na caixa de seleção para as *Métricas básicas*.
1. Na seção *Conta de armazenamento*, navegue até a conta *mydiagdata[1234]* criada na seção anterior e selecione-a.
1. Clique no botão **Salvar** .

    ![Exibir métricas de diagnóstico](./media/tutorial-monitoring/enable-diagnostics-extension.png)


## <a name="view-vm-metrics"></a>Exibir métricas de VM

Você pode exibir as métricas da VM da mesma maneira que você exibiu as métricas da VM host:

1. No Portal do Azure, clique em **Grupos de Recursos**, selecione **myResourceGroup** e, em seguida, selecione **myVM** na lista de recursos.
1. Para ver como está o desempenho da VM do Host, clique em **Métricas** na folha da VM e, em seguida, selecione qualquer uma das métricas de diagnóstico em **Métricas disponíveis**.

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

## <a name="manage-package-updates"></a>Gerenciar atualizações de pacote

Usando o Gerenciamento de Atualizações, você pode gerenciar patches e atualizações de pacote para as VMs do Linux do Azure. Diretamente de sua VM, você pode rapidamente avaliar o status de atualizações disponíveis, agendar a instalação de atualizações necessárias e examinar os resultados de implantação para verificar se as atualizações foram aplicadas com êxito na VM.

Para obter informações sobre preços, consulte [Preços de automação de Gerenciamento de Atualizações](https://azure.microsoft.com/pricing/details/automation/)

### <a name="enable-update-management-preview"></a>Habilitar o Gerenciamento de Atualizações (versão prévia)

Habilitar o Gerenciamento de Atualizações para a VM

1. No lado esquerdo da tela, selecione **Máquinas virtuais**.
1. Na lista, selecione uma VM.
1. Na tela da VM, na seção **Operações**, clique em **Gerenciamento de Atualizações**. A tela **Habilitar Gerenciamento de Atualizações** é aberta.

Uma validação é executada para determinar se o Gerenciamento de Atualizações está habilitado para essa VM. A validação inclui verificar se há um espaço de trabalho do Log Analytics e uma conta de automação vinculada e se a solução está no espaço de trabalho.

Um espaço de trabalho do Log Analytics é usado para coletar dados que são gerados pelos recursos e serviços, como o Gerenciamento de Atualizações. O espaço de trabalho fornece um único local para examinar e analisar dados de várias fontes. Para executar uma ação adicional em VMs que exigem atualizações, a Automação do Azure permite executar scripts em VMs, como baixar e aplicar atualizações.

O processo de validação também verifica se a VM é provisionada com o MMA (Microsoft Monitoring Agent) e o Hybrid Worker. Esse agente é usado para comunicar-se com a VM e obter informações sobre o status de atualização. 

Se esses pré-requisitos não forem atendidos, será exibida uma faixa oferecendo a opção para habilitar a solução.

![Faixa de configuração integrada do Gerenciamento de Atualizações](./media/tutorial-monitoring/manage-updates-onboard-solution-banner.png)

Clique na faixa para habilitar a solução. Se algum dos seguintes pré-requisitos estiver ausente após a validação, ele será adicionado automaticamente:

* Espaço de trabalho do [Log Analytics](../../log-analytics/log-analytics-overview.md)
* [Automação](../../automation/automation-offering-get-started.md)
* Uma [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) está habilitada na VM

A tela **Habilitar Gerenciamento de Atualizações** é aberta. Defina as configurações e clique em **Habilitar**.

![Habilitar a solução de Gerenciamento de Atualizações](./media/tutorial-monitoring/manage-updates-update-enable.png)

A habilitação da solução pode levar até 15 minutos e, durante esse período, você não deve fechar a janela do navegador. Depois que a solução for habilitada, as informações sobre atualizações ausentes do gerenciador de pacotes na VM fluirão para o Log Analytics.
Pode levar entre 30 minutos e 6 horas para que os dados fiquem disponíveis para análise.

### <a name="view-update-assessment"></a>Exibir avaliação de atualização

Depois que a solução **Gerenciamento de Atualizações** for habilitada, a tela **Gerenciamento de Atualizações** será exibida. Você pode ver uma lista de atualizações ausentes na guia **Atualizações ausentes**.

![Exibir o status de atualização](./media/tutorial-monitoring/manage-updates-view-status-linux.png)

### <a name="schedule-an-update-deployment"></a>Agendar uma implantação de atualização

Para instalar atualizações, agende uma implantação que siga o agendamento de versão e a janela de manutenção.

Agende uma nova implantação de atualização para a VM clicando em **Agendar implantação de atualização** na parte superior da tela **Gerenciamento de Atualizações**. Na tela **Nova implantação de atualização**, especifique as seguintes informações:

* **Nome** – forneça um nome exclusivo para identificar a Implantação de atualizações.
* **Atualizações a serem excluídas** – selecione esta opção para inserir os nomes de pacotes a serem excluídos da atualização.
* **Configurações de agenda** – você pode aceitar a data e hora padrão, que é de 30 minutos após a hora atual ou especificar um horário diferente. Você também pode especificar se a implantação ocorre uma única vez ou configurar um agendamento recorrente. Clique na opção Recorrente em Recorrência para configurar um agendamento recorrente.

  ![Tela de configurações de agenda de atualização](./media/tutorial-monitoring/manage-updates-schedule-linux.png)

* **Janela de manutenção (minutos)** – especifique o período de tempo em que deseja que a implantação de atualização ocorra.  Isso ajuda a garantir que as alterações sejam executadas dentro das janelas de manutenção definidas. 

Depois de concluir a configuração da agenda, clique no botão **Criar** e retorne ao painel de status.
Observe que a tabela **Agendado** mostra a agenda de implantação criada.

> [!WARNING]
> A VM será reiniciada automaticamente após a instalação das atualizações, se houver tempo suficiente na janela de manutenção.

O Gerenciamento de Atualizações usa o gerenciador de pacotes existente na sua VM para instalar pacotes.

### <a name="view-results-of-an-update-deployment"></a>Exibir resultados de uma implantação de atualização

Após o início da implantação agendada, você pode ver o status dessa implantação na guia **Implantações de atualização** na tela **Gerenciamento de Atualizações**.
Se ela estiver em execução, seu status será mostrado como **Em andamento**. Após a conclusão, em caso de êxito, ele será alterado para **Êxito**.
Se houver uma falha com uma ou mais atualizações na implantação, o status será **Com falha**.
Clique na implantação de atualização concluída para ver o painel dessa implantação de atualização.

![Painel de status de implantação de atualização para implantação específica](./media/tutorial-monitoring/manage-updates-view-results.png)

No bloco **Resultados de atualização** há um resumo do número total de atualizações e os resultados da implantação na VM.
Na tabela à direita há uma análise detalhada de cada atualização e os resultados da instalação, que podem ser um dos seguintes valores:

* **Não foi tentada** – a atualização não foi instalada devido a tempo suficiente disponível com base na duração da janela de manutenção definida.
* **Êxito** – a atualização foi baixada e instalada na VM com êxito
* **Com falha** – falha da atualização ao ser baixada ou instalada na VM.

Clique em **Todos os logs** para ver todas as entradas de log que a implantação criou.

Clique no bloco **Saída** para ver o fluxo de trabalho do runbook responsável por gerenciar a implantação de atualização na VM de destino.

Clique em **Erros** para ver informações detalhadas sobre os erros da implantação.

## <a name="advanced-monitoring"></a>Monitoramento avançado 

Você pode fazer um monitoramento mais avançado da sua VM usando o [Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview). Caso ainda não tenha feito isso, você pode se inscrever para uma [avaliação gratuita](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-trial) do Operations Management Suite.

Quando você tem acesso ao portal do OMS, você pode encontrar a chave e o identificador de espaço de trabalho na folha Configurações. Substitua <workspace-key> e <workspace-id> pelos valores de seu espaço de trabalho OMS e, em seguida, use **az vm extension set** para adicionar a extensão do OMS à VM:

```azurecli-interactive 
az vm extension set \
  --resource-group myResourceGroupMonitor \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.3 \
  --protected-settings '{"workspaceKey": "<workspace-key>"}' \
  --settings '{"workspaceId": "<workspace-id>"}'
```

Na folha Pesquisa de Logsdo portal do OMS, você deve ver *myVM*, como mostra a figura a seguir:

![Folha do OMS](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou, examinou e gerenciou atualizações para uma VM. Você aprendeu como:

> [!div class="checklist"]
> * Habilitar o diagnóstico de inicialização na VM
> * Exibir diagnóstico de inicialização
> * Exibir métricas de host
> * Habilitar a extensão de diagnóstico na VM
> * Exibir métricas de VM
> * Criar alertas com base nas métricas de diagnóstico
> * Gerenciar atualizações de pacote
> * Configurar monitoramento avançado

Avance para o próximo tutorial para saber mais sobre a Central de Segurança do Azure.

> [!div class="nextstepaction"]
> [Gerenciar a segurança da VM](./tutorial-azure-security.md)
