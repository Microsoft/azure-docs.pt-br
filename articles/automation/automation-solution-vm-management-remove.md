---
title: Remover a automação do Azure Iniciar/Parar VMs fora do horário comercial visão geral
description: Este artigo descreve como remover o recurso Iniciar/Parar VMs fora do horário comercial e desvincular uma conta de automação do espaço de trabalho Log Analytics.
services: automation
ms.subservice: process-automation
ms.date: 03/04/2021
ms.topic: conceptual
ms.openlocfilehash: 0bab5d8e82ce432e9b3834fe4c003316545eb338
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102122078"
---
# <a name="remove-startstop-vms-during-off-hours-from-automation-account"></a>Remover Iniciar/Parar VMs fora do horário comercial da conta de automação

Depois de habilitar o recurso de Iniciar/Parar VMs fora do horário comercial para gerenciar o estado de execução de suas VMs do Azure, você pode decidir parar de usá-lo. A remoção desse recurso pode ser feita usando um dos seguintes métodos com base nos modelos de implantação com suporte:

* Exclua o grupo de recursos que contém a conta de automação e vinculado Azure Monitor Log Analytics espaço de trabalho, cada um dedicado para dar suporte a esse recurso.
* Desvincule o espaço de trabalho Log Analytics da conta de automação e exclua a conta de automação dedicada a esse recurso.
* Exclua o recurso de uma conta de automação e um espaço de trabalho vinculado que dão suporte a outros objetivos de gerenciamento e monitoramento.

A exclusão desse recurso remove apenas os runbooks associados, não exclui os agendamentos ou as variáveis que foram criadas durante a implantação ou quaisquer que tenham sido criados após.

## <a name="delete-the-dedicated-resource-group"></a>Excluir o grupo de recursos dedicado

Para excluir o grupo de recursos, siga as etapas descritas na [Azure Resource Manager o grupo de recursos e o artigo de exclusão de recursos](../azure-resource-manager/management/delete-resource-group.md) .

## <a name="delete-the-automation-account"></a>Excluir a conta de automação

Para excluir sua conta de automação dedicada a Iniciar/Parar VMs fora do horário comercial, execute as etapas a seguir.

1. Entre no Azure em [https://portal.azure.com](https://portal.azure.com) .

2. Navegue até sua conta de automação e selecione **espaço de trabalho vinculado** em **recursos relacionados**.

3. Selecione **Acessar o workspace**.

4. Em **Geral**, clique em **Soluções**.

5. Na página Soluções, selecione **Start-Stop-VM[Workspace]** .

6. Na página **VMManagementSolution [espaço de trabalho]** , selecione **excluir** no menu.

7. Enquanto as informações são verificadas e o recurso é excluído, você pode acompanhar o andamento em **Notificações**, disponível no menu. Você retornará à página Soluções após o processo de remoção.

### <a name="unlink-workspace-from-automation-account"></a>Desvincular o workspace da conta de Automação

Há duas opções para desvincular o espaço de trabalho Log Analytics de sua conta de automação. Você pode executar esse processo da conta de automação ou do espaço de trabalho vinculado.

Para desvincular da sua conta de automação, execute as etapas a seguir.

1. No portal do Azure, selecione **Contas de Automação**.

2. Abra a conta da Automação e selecione **Workspace vinculado** em **Recursos Relacionados** à esquerda.

3. Na página **desvincular espaço de trabalho** , selecione **desvincular espaço de trabalho** e responder a prompts.

   ![Página Desvincular workspace](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Ao tentar desvincular o espaço de trabalho Log Analytics, você pode acompanhar o progresso em **notificações** no menu.

Para desvincular do espaço de trabalho, execute as etapas a seguir.

1. No portal do Azure, selecione **log Analytics espaços de trabalho**.

2. No espaço de trabalho, selecione **conta de automação** em **recursos relacionados**.

3. Na página conta de automação, selecione **desvincular conta** e responder a prompts.

Enquanto tenta desvincular a conta de automação, você pode acompanhar o progresso em **notificações** no menu.

### <a name="delete-automation-account"></a>Excluir conta de automação

1. No portal do Azure, selecione **Contas de Automação**.

2. Abra sua conta de automação e selecione **excluir** no menu.

Embora as informações sejam verificadas e a conta seja excluída, você pode acompanhar o progresso em **notificações**, escolhidas no menu.

## <a name="delete-the-feature"></a>Excluir o recurso

Para excluir Iniciar/Parar VMs fora do horário comercial de sua conta de automação, execute as etapas a seguir. A conta de Automação e o workspace do Log Analytics não serão excluídos como parte desse processo. Se não quiser manter o espaço de trabalho Log Analytics, você deverá excluí-lo manualmente. Para obter mais informações sobre como excluir seu espaço de trabalho, consulte [excluir e recuperar o Azure log Analytics espaço de trabalho](../azure-monitor/logs/delete-workspace.md).

1. Navegue até sua conta de automação e selecione **espaço de trabalho vinculado** em **recursos relacionados**.

2. Selecione **Acessar o workspace**.

3. Em **Geral**, clique em **Soluções**.

4. Na página Soluções, selecione **Start-Stop-VM[Workspace]** .

5. Na página **VMManagementSolution [espaço de trabalho]** , selecione **excluir** no menu.

    ![Excluir recurso de gerenciamento de VM](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. Na janela Excluir Solução, confirme que você deseja excluir o recurso.

7. Enquanto as informações são verificadas e o recurso é excluído, você pode acompanhar o andamento em **Notificações**, disponível no menu. Você retornará à página Soluções após o processo de remoção.

8. Se você não quiser manter os [recursos](automation-solution-vm-management.md#components) criados pelo recurso ou posteriormente (como, variáveis, agendas etc.), você precisará excluí-los manualmente da conta.

## <a name="next-steps"></a>Próximas etapas

Para reabilitar esse recurso, consulte [habilitar iniciar/parar fora do horário de expediente](automation-solution-vm-management-enable.md).