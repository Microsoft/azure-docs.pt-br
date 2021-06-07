---
title: Gerenciar políticas de desligamento automático em VMs de Azure DevTest Labs e computação | Microsoft Docs
description: Saiba como definir a política de desligamento automático para um laboratório para que as máquinas virtuais sejam desligadas automaticamente quando não estiverem em uso.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: cd7974580ea30c9d0591c88380a4e626711bad1e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93318982"
---
# <a name="configure-autoshutdown-for-lab-and-compute-virtual-machines-in-azure-devtest-labs"></a>Configurar o desligamento automático para máquinas virtuais de laboratório e computação no Azure DevTest Labs

Este artigo explica como definir as configurações de desligamento automático para VMs de laboratório no DevTest Labs e VMs de computação.

## <a name="configure-autoshutdown-for-lab-vms-devtest-labs"></a>Configurar o desligamento automático para VMs de laboratório (DevTest Labs)

O Azure DevTest Labs permite que você controle o custo e minimize o desperdício nos laboratórios gerenciando políticas (configurações) para cada laboratório. Este artigo mostra como configurar a política de desligamento automático para um laboratório.  Ele também mostra como definir as configurações de desligamento automático para uma VM de laboratório. Para exibir como definir cada política de laboratório, confira [Definir políticas de laboratório no Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

### <a name="set-autoshutdown-policy-for-a-lab"></a>Definir a política de desligamento automático para um laboratório

Como proprietário de um laboratório, você pode configurar uma programação de desligamento para todas as VMs em seu laboratório. Ao fazer isso, você pode economizar custos com a execução de máquinas que não estão sendo usadas (ociosas). Você pode impor uma política de desligamento em todas as VMs do laboratório centralmente e também salvar os usuários do laboratório do esforço de configurar um agendamento para seus computadores individuais. Esse recurso permite que você defina a política em sua agenda de laboratório, desde permitir que os usuários do laboratório tenham controle total sobre o agendamento de desligamento da VM para nenhum controle sobre o desligamento da VM. Como proprietário de um laboratório, você pode configurar essa política seguindo as etapas a seguir:

1. Na home page do seu laboratório, selecione **Configuração e políticas**.
2. Selecione **Política de desligamento automático** na seção **Horários** do menu à esquerda.
3. Selecione uma das opções. As seções a seguir fornecem mais detalhes sobre essas opções:

    ![Opções de política de desligamento automático](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

> [!IMPORTANT]
> As alterações na política de desligamento aplicam-se somente às novas VMs criadas no laboratório e não às VMs já existentes.

### <a name="configure-autoshutdown-settings"></a>Definir configurações de desligamento automático

A política de desligamento automático ajuda a minimizar o desperdício de laboratório, permitindo que você especifique a hora em que as VMs desse laboratório serão desligadas.

Para exibir ou alterar as políticas de um laboratório, siga estas etapas:

1. Na home page do seu laboratório, selecione **Configuração e políticas**.
2. Selecione **desligamento automático** na seção **agendas** do menu à esquerda.
3. Selecione **Ativado** para habilitar essa política e **Desativado** para desabilitá-la.
     ![Detalhes de desligamento automático](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
4. Se você habilitar essa política, especifique a hora (e fuso horário) para desligar todas as VMs no laboratório atual.
5. Especifique **Sim** ou **não** para a opção de enviar uma notificação 30 minutos antes do tempo de desligamento automático especificado. Se escolher **Sim**, insira um ponto de extremidade de URL de webhook ou endereço de email especificando onde você deseja que a notificação seja postada ou enviada. O usuário recebe a notificação e recebe a opção para atrasar o desligamento. Para obter mais informações, consulte a seção [notificações](#notifications) .
6. Clique em **Salvar**.

    Por padrão, uma vez habilitada, essa política se aplicará a todas as VMs do laboratório atual. Para remover essa configuração de uma VM específica, abra o painel de gerenciamento da VM e altere sua configuração de **Desligamento Automático**.

> [!NOTE]
> Se você atualizar a agenda de desligamento automático para seu laboratório ou para uma máquina virtual de laboratório específica dentro de 30 minutos da hora agendada atual, o tempo de desligamento atualizado será aplicado na agenda do dia seguinte.

### <a name="user-sets-a-schedule-and-can-opt-out"></a>O usuário define um agendamento e pode recusar

Se você definir seu laboratório para essa política, os usuários do laboratório poderão substituir ou desativar a programação do laboratório. Essa opção concede aos usuários de laboratório controle total sobre o cronograma de desligamento automático de suas VMs. Os usuários de laboratório não veem nenhuma alteração na página de programação de desligamento automático da VM.

![Opção de política de desligamento automático-1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>O usuário define um agendamento e não pode recusar

Se você definir seu laboratório para essa política, os usuários do laboratório poderão substituir a programação do laboratório. No entanto, eles não podem recusar a política de desligamento automático. Essa opção garante que cada computador em seu laboratório esteja sob um agendamento de desligamento automático. Os usuários do laboratório podem atualizar a agenda de desligamento automático de suas VMs e configurar notificações de desligamento.

![Opção de política de desligamento automático-2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>O usuário não tem controle sobre o cronograma definido pelo administrador do laboratório

Se você definir seu laboratório para essa política, os usuários do laboratório não poderão substituir ou recusar a programação do laboratório. Essa opção oferece ao administrador do laboratório o controle completo da programação de cada máquina no laboratório. Os usuários de laboratório só podem configurar notificações de desligamento automático para suas VMs.

![Opção de política de desligamento automático-3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="configure-autoshutdown-for-compute-vms"></a>Configurar o desligamento automático para VMs de computação

1. Na página **máquina virtual** , selecione **desligamento automático** no menu à esquerda na seção **operações** .
2. Na página **desligamento automático** , selecione **ativado** para habilitar essa política e **desativado** para desabilitá-la.
3. Se você habilitar essa política, especifique a **hora** (e o **fuso horário**) em que a VM deve ser desligada.
4. Escolha **Sim** ou **não** para a opção de enviar uma notificação 30 minutos antes do tempo de desligamento automático especificado. Se escolher **Sim**, insira um ponto de extremidade de URL de webhook ou endereço de email especificando onde você deseja que a notificação seja postada ou enviada. O usuário recebe a notificação e recebe a opção para atrasar o desligamento. Para obter mais informações, consulte a seção [notificações](#notifications) .
5. Clique em **Salvar**.

    ![Configurar o desligamento automático para uma VM de computação](./media/devtest-lab-auto-shutdown/comnpute-auto-shutdown.png)

### <a name="view-activity-logs-for-auto-shutdown-updates"></a>Exibir logs de atividade para atualizações de desligamento automático

Ao atualizar a configuração de desligamento automático, você verá a atividade registrada no log de atividades da VM.

1. Na [portal do Azure](https://portal.azure.com), navegue até o Home Page da VM.
2. Selecione **log de atividades** no menu à esquerda.
3. Remover **recurso: mycomputevm** dos filtros.
4. Confirme que você vê a operação **Adicionar ou modificar agendas** no log de atividades. Se você não o vir, aguarde algum tempo e atualize o log de atividades.

    ![Entrada do log de atividades](./media/devtest-lab-auto-shutdown/activity-log-entry.png)
5. Selecione a operação **Adicionar ou modificar agendas** para ver as seguintes informações na página **Resumo** :

    - Nome da operação (adicionar ou modificar agendas)
    - A data e a hora em que a configuração de desligamento automático foi atualizada.
    - O endereço de email do usuário que atualizou a configuração.

        ![Resumo da entrada do log de atividades](./media/devtest-lab-auto-shutdown/activity-log-entry-summary.png)
6. Alterne para a guia **histórico de alterações** na página **Adicionar ou modificar agendas** , você vê o histórico de alterações para a configuração. No exemplo a seguir, o tempo de desligamento foi alterado de 7 para 6 PM em 10 de abril de 2020 à EST 15:18:47. E, a configuração foi desabilitada em 15:25:09 EST.

    ![Log de atividades – histórico de alterações](./media/devtest-lab-auto-shutdown/activity-log-entry-change-history.png)
7. Para ver mais detalhes sobre a operação, alterne para a guia **JSON** na página **Adicionar ou modificar agendas** .

## <a name="notifications"></a>Notificações

Quando o desligamento automático estiver configurado, as notificações serão enviadas para os usuários do laboratório 30 minutos antes do desligamento automático ser disparado se qualquer uma de suas VMs for afetada. Essa opção dá aos usuários do laboratório a oportunidade de salvar seu trabalho antes do desligamento. A notificação também fornece links para cada VM para as ações a seguir, caso alguém precise continuar trabalhando em sua VM.

- Ignorar o desligamento automático para este tempo
- Adiar o desligamento automático por uma hora
- Adiar o desligamento automático por 2 horas

A notificação será enviada para a URL do webhook se um webhook tiver sido especificado.  Se um endereço de email tiver sido especificado nas configurações de desligamento automático, um email será enviado para esse endereço de email. WebHooks permitem que você crie ou configure integrações que assinam determinados eventos. Quando um desses eventos for disparado, o DevTest Labs enviará uma carga HTTP POST para a URL configurada do webhook. Para obter mais informações sobre como responder a WebHooks, consulte [Azure Functions visão geral de gatilhos e associações http](../azure-functions/functions-bindings-http-webhook.md) ou [Adicionar um gatilho http para aplicativos lógicos do Azure](../connectors/connectors-native-http.md#add-an-http-trigger).

Recomendamos que você use os ganchos da Web porque eles são amplamente suportados por vários aplicativos, como os aplicativos lógicos do Azure e a margem de atraso.  Os WebHooks permitem que você implemente sua própria maneira de enviar notificações. Por exemplo, este artigo explica como configurar a notificação de desligamento automático para enviar um email para o proprietário da VM usando os aplicativos lógicos do Azure. Primeiro, vamos percorrer rapidamente as etapas básicas para habilitar a notificação de desligamento automático em seu laboratório.

### <a name="create-a-logic-app-that-receives-email-notifications"></a>Criar um aplicativo lógico que recebe notificações por email

Os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) fornecem muitos conectores que facilitam a integração de um serviço com outros clientes, como o Office 365 e o Twitter. No alto nível, as etapas para configurar um aplicativo lógico para notificação por email podem ser divididas em quatro fases:

- Crie um aplicativo lógico.
- Configure o modelo interno.
- Integre com seu cliente de email
- Obtenha a URL do webhook.

### <a name="create-a-logic-app"></a>Criar um aplicativo lógico

Para começar, crie um aplicativo lógico em sua assinatura do Azure usando as seguintes etapas:

1. Selecione **+ criar um recurso** no menu à esquerda, selecione **integração** e selecione **aplicativo lógico**.

    ![Novo menu de aplicativo lógico](./media/devtest-lab-auto-shutdown/new-logic-app.png)
2. Na página **aplicativo lógico – criar** , siga estas etapas:
    1. Insira um **nome** para o aplicativo lógico.
    2. Selecione sua **assinatura** do Azure.
    3. Crie um novo **grupo de recursos** ou selecione um grupo de recursos existente.
    4. Selecione um **local** para o aplicativo lógico.

        ![Novo aplicativo lógico-configurações](./media/devtest-lab-auto-shutdown/new-logic-app-page.png)
3. Nas **notificações**, selecione **ir para o recurso** na notificação.

    ![Ir para o recurso](./media/devtest-lab-auto-shutdown/go-to-resource.png)
4. Selecione **Designer de aplicativo lógico** na categoria **ferramentas de implantação** .

    ![Selecionar solicitação/resposta HTTP](./media/devtest-lab-auto-shutdown/select-http-request-response-option.png)
5. Na página **solicitação-resposta http** , selecione **usar este modelo**.

    ![Selecione usar esta opção de modelo](./media/devtest-lab-auto-shutdown/select-use-this-template.png)
6. Copie o JSON a seguir na seção **esquema JSON do corpo da solicitação** :

    ```json
    {
        "$schema": "http://json-schema.org/draft-04/schema#",
        "properties": {
            "delayUrl120": {
                "type": "string"
            },
            "delayUrl60": {
                "type": "string"
            },
            "eventType": {
                "type": "string"
            },
            "guid": {
                "type": "string"
            },
            "labName": {
                "type": "string"
            },
            "owner": {
                "type": "string"
            },
            "resourceGroupName": {
                "type": "string"
            },
            "skipUrl": {
                "type": "string"
            },
            "subscriptionId": {
                "type": "string"
            },
            "text": {
                "type": "string"
            },
            "vmName": {
                "type": "string"
            },
            "vmUrl": {
                "type": "string"
            },
            "minutesUntilShutdown": {
                "type": "string"
            }
        },
        "required": [
            "skipUrl",
            "delayUrl60",
            "delayUrl120",
            "vmName",
            "guid",
            "owner",
            "eventType",
            "text",
            "subscriptionId",
            "resourceGroupName",
            "labName",
            "vmUrl",
            "minutesUntilShutdown"
        ],
        "type": "object"
    }
    ```

    ![Captura de tela que mostra o "esquema JSON do corpo da solicitação".](./media/devtest-lab-auto-shutdown/request-json.png)
7. Selecione **+ nova etapa** no designer e siga estas etapas:
    1. Pesquise pelo **Office 365 Outlook-enviar um email**.
    2. Selecione **enviar um email** de **ações**.

        ![Opção Enviar email](./media/devtest-lab-auto-shutdown/select-send-email.png)
    3. Selecione **entrar** para entrar em sua conta de email.
    4. Selecione **para** o campo e escolha proprietário.
    5. Selecione **assunto** e insira um assunto da notificação por email. Por exemplo: "desligamento do Machine vmName for Lab: labName."
    6. Selecione **corpo** e defina o conteúdo do corpo para notificação por email. Por exemplo: "vmName está agendado para desligar em 15 minutos. Ignore esse desligamento clicando em: URL. Atrasar o desligamento por uma hora: delayUrl60. Atraso no desligamento por 2 horas: delayUrl120. "

        ![Esquema JSON do corpo da solicitação](./media/devtest-lab-auto-shutdown/email-options.png)
8. Selecione **Salvar** na barra de ferramentas. Agora, você pode copiar a **URL http post**. Selecione o botão Copiar para copiar a URL para a área de transferência.

    ![URL do webhook](./media/devtest-lab-auto-shutdown/webhook-url.png)

## <a name="next-steps"></a>Próximas etapas

Para saber como definir todas as políticas, consulte [definir políticas de laboratório no Azure DevTest Labs](devtest-lab-set-lab-policy.md).
