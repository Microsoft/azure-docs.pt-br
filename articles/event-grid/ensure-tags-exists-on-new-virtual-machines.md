---
title: Integrar a Automação do Azure com a Grade de Eventos | Microsoft Docs
description: Saiba como adicionar uma marcação automaticamente quando uma nova VM é criada e enviar uma notificação para o Microsoft Teams.
keywords: automação, runbook, teams, grade de eventos, máquina virtual, VM
services: automation,event-grid
author: eamonoreilly
ms.service: automation
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 07/07/2020
ms.author: eamono
ms.openlocfilehash: 3b9b49a4d38566891f442a3d2d7eac9bf1d36465
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87461996"
---
# <a name="tutorial-integrate-azure-automation-with-event-grid-and-microsoft-teams"></a>Tutorial: Integrar a Automação do Azure à Grade de Eventos e ao Microsoft Teams

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Importar um runbook de exemplo da Grade de Eventos.
> * Criar um hebwook opcional no Microsoft Teams.
> * Criar um webhook para o runbook.
> * Criar uma assinatura na Grade de Eventos.
> * Criar uma VM que dispara o runbook.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

Para concluir este tutorial, uma [conta de Automação do Azure](../automation/index.yml) é necessária para armazenar o runbook que é disparado da assinatura da Grade de Eventos do Azure.

* O módulo `AzureRM.Tags` precisa ser carregado em sua conta de automação, veja [Como importar os módulos na automação do Azure](../automation/automation-update-azure-modules.md) para aprender a importar os módulos para a Automação do Azure.

## <a name="import-an-event-grid-sample-runbook"></a>Importar um runbook de exemplo da Grade de Eventos

1. Selecione sua conta de Automação e selecione a página de **Runbooks**.

   ![Selecione runbooks](./media/ensure-tags-exists-on-new-virtual-machines/select-runbooks.png)

2. Clique no botão **Procurar na galeria**.

3. Pesquise por **Grade de Eventos** e selecione **Integração da Automação do Azure com a Grade de Eventos**.

    ![Importe a galeria de runbook](media/ensure-tags-exists-on-new-virtual-machines/gallery-event-grid.png)

4. Selecione **Importar** e nomeie-a como **Watch-VMWrite**.

5. Depois que a galeria foi importada, selecione **Editar** para exibir a fonte do runbook. 
6. Atualize a linha 74 no script para usar `Tag` em vez de `Tags`.

    ```powershell
    Update-AzureRmVM -ResourceGroupName $VMResourceGroup -VM $VM -Tag $Tag | Write-Verbose
    ```
7. Clique no botão **Publicar**.

## <a name="create-an-optional-microsoft-teams-webhook"></a>Criar um hebwook opcional no Microsoft Teams

1. No Microsoft Teams, selecione **Mais Opções** próximo ao nome do canal e, em seguida, selecione **Conectores**.

    ![Conexões do Microsoft Teams](media/ensure-tags-exists-on-new-virtual-machines/teams-webhook.png)

2. Percorra a lista de conectores até **Webhook de Entrada** e clique em **Adicionar**.

3. Digite **AzureAutomationIntegration** para nomeá-lo e clique em **Criar**.

4. Copie a URL do webhook na área de transferência e salve-a. A URL do webhook é usada para enviar informações para o Microsoft Teams.

5. Selecione **Concluído** para salvar o webhook.

## <a name="create-a-webhook-for-the-runbook"></a>Criar um webhook para o runbook

1. Abra o runbook Watch-VMWrite.

2. Selecione **Webhooks** e clique no botão **Adicionar Webhook**.

3. Nomeie-o como **WatchVMEventGrid**. Copie a URL para a área de transferência e salve-a.

    ![Configurar o nome do webhook](media/ensure-tags-exists-on-new-virtual-machines/copy-url.png)

4. Selecione **Configurar parâmetros e configurações de execução** e insira a URL do webhook do Microsoft Teams para **CHANNELURL**. Deixe **WEBHOOKDATA** em branco.

    ![Configurar parâmetros de webhook](media/ensure-tags-exists-on-new-virtual-machines/configure-webhook-parameters.png)

5. Selecione **Criar** para criar o webhook de runbook de Automação.

## <a name="create-an-event-grid-subscription"></a>Criar uma assinatura na Grade de Eventos

1. Na página de visão geral da **Conta de Automação**, selecione **Grade de evento**.

    ![Selecionar a Grade de Eventos](media/ensure-tags-exists-on-new-virtual-machines/select-event-grid.png)

2. Clique em **+ Assinatura de Evento**.

3. Configure a assinatura com as seguintes informações:
    1. Para **Tipo de Tópico**, selecione **Assinaturas do Azure**.
    2. Desmarque a caixa de seleção **Assinar todos os tipos de evento**.
    3. Digite **AzureAutomation** para nomeá-la.
    4. Na lista suspensa **Tipos de Evento Definidos**, desmarque todas as opções, exceto **Gravação de Recurso Bem-Sucedida**.

        > [!NOTE] 
        > Atualmente o Azure Resource Manager não diferencia Criar e Atualizar, portanto a implementação deste tutorial para todos os eventos Microsoft.Resources.ResourceWriteSuccess em sua assinatura do Azure poderá resultar em um alto volume de chamadas.
    1. Para **Tipo de Ponto de Extremidade**, selecione **Webhook**.
    2. Clique em **Selecionar um ponto de extremidade**. Na página **Selecionar Web hook** aberta, cole a URL do web hook que você criou para o runbook Watch-VMWrite.
    3. Em **FILTROS**, insira a assinatura e o grupo de recursos onde você deseja procurar as novas VMs criadas. O resultado deve ser assim: `/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines`

4. Selecione **Criar** para salvar a assinatura na Grade de Eventos.

## <a name="create-a-vm-that-triggers-the-runbook"></a>Crie uma VM que dispara o runbook

1. Crie uma nova VM no grupo de recursos que você especificou no filtro de prefixos na assinatura da Grade de Eventos.

2. O runbook Watch-VMWrite deve ser chamado e uma nova marcação deve ser adicionada à VM.

    ![Marcação VM](media/ensure-tags-exists-on-new-virtual-machines/vm-tag.png)

3. Uma nova mensagem é enviada para o canal do Microsoft Teams.

    ![Notificação do Microsoft Teams](media/ensure-tags-exists-on-new-virtual-machines/teams-vm-message.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou a integração entre a Grade de Eventos e a Automação. Você aprendeu a:

> [!div class="checklist"]
> * Importar um runbook de exemplo da Grade de Eventos.
> * Criar um hebwook opcional no Microsoft Teams.
> * Criar um webhook para o runbook.
> * Criar uma assinatura na Grade de Eventos.
> * Criar uma VM que dispara o runbook.

> [!div class="nextstepaction"]
> [Criar e encaminhar eventos personalizados com a Grade de Eventos](../event-grid/custom-event-quickstart.md)
