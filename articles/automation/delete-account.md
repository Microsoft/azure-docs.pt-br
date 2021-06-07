---
title: Excluir sua conta de Automação do Azure
description: Este artigo informa como excluir sua conta de Automação do Azure em diferentes cenários de configuração.
services: automation
ms.service: automation
ms.subservice: process-automation
ms.date: 03/18/2021
ms.topic: conceptual
ms.openlocfilehash: c3a514aa507fcf069671f987e175b7ae5be59d10
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105735052"
---
# <a name="how-to-delete-your-azure-automation-account"></a>Como Excluir sua conta de Automação do Azure

Depois de habilitar uma conta de Automação do Azure para ajudar a automatizar o processo de TI ou de negócios, ou habilitar outros recursos dele para dar suporte ao gerenciamento de operações de seus computadores Azure e não Azure, como Gerenciamento de Atualizações, você pode optar por parar de usar a conta de Automação. Se você tiver habilitado recursos que dependem da integração com um workspace do Log Analytics do Azure Monitor Log Analytics, são necessárias mais etapas para concluir esta ação.

A remoção da sua conta de Automação do Azure pode ser feita usando um dos seguintes métodos com base nos modelos de implantação com suporte:

* Excluir o grupo de recursos que contém a conta de Automação do Azure.
* Excluir o grupo de recursos que contém a conta de Automação do Azure e o vinculado o workspace do Log Analytics do Azure Monitor, se:

    * A conta e o workspace forem dedicados a dar suporte ao Gerenciamento de Atualizações, Controle de Alterações e Estoque e/ou Iniciar/Parar VMs fora do horário comercial.
    * A conta é dedicada à Automação do Azure do processo e está integrada a um workspace para enviar fluxos de trabalho e status do trabalho de runbook.

* Desvincular o workspace do Log Analytics da conta de Automação do Azure e excluir a conta de automação.
* Exclua o recurso do workspace vinculado, desvincule a conta do workspace e exclua a conta de Automação do Azure.

Este artigo mostra como remover completamente sua conta de automação por meio do portal do Azure, do PowerShell, da CLI do Azure ou da API REST.

## <a name="delete-the-dedicated-resource-group"></a>Excluir o grupo de recursos dedicados

Para excluir sua conta de automação e também o workspace do Log Analytics se estiver vinculado à conta, criado no mesmo grupo de recursos dedicado à conta, siga as etapas descritas no artigo [grupo de recursos e exclusão de recursos do Azure Resource Manager](../azure-resource-manager/management/delete-resource-group.md).

## <a name="delete-a-standalone-automation-account"></a>Excluir uma conta de Automação do Azure autônoma

Se sua conta de Automação do Azure não estiver vinculada a um workspace do Log Analytics, execute as etapas a seguir para excluí-la.

# <a name="azure-portal"></a>[Portal do Azure](#tab/azure-portal)

1. Entre no Azure em [https://portal.azure.com](https://portal.azure.com).

2. No portal do Azure, navegue até **Contas de Automação**.

3. Abra sua conta de Automação do Azure e selecione **Excluir** no menu.

Enquanto as informações são verificadas e o recurso é excluído, você pode acompanhar o andamento em **Notificações**, disponível no menu.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Esse comando remove a conta de Automação do Azure sem solicitar uma validação.

```powershell
Remove-AzAutomationAccount -Name "automationAccountName" -Force -ResourceGroupName "resourceGroupName"
```

---

## <a name="delete-a-standalone-automation-account-linked-to-workspace"></a>Excluir uma conta de Automação do Azure autônoma vinculada ao workspace

Se sua conta de Automação do Azure estiver vinculada a um workspace do Log Analytics para coletar fluxos e logs de trabalho, execute as etapas a seguir para excluir a conta.

Há duas opções para desvincular o workspace do Log Analytics de sua conta de Automação do Azure. Você pode executar esse processo da conta de automação ou do espaço de trabalho vinculado.

Para desvincular da sua conta de Automação do Azure, execute as etapas a seguir.

1. No portal do Azure, navegue até **Contas de Automação**.

2. Abra a conta da Automação e selecione **Workspace vinculado** em **Recursos Relacionados** à esquerda.

3. Na página **Desvincular workspace**, clique em **Desvincular workspace** e responda às solicitações.

   ![Página Desvincular workspace](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Enquanto tenta desvincular o workspace do Log Analytics, você pode acompanhar o progresso em **Notificações** no menu.

Para desvincular do workspace execute as etapas a seguir.

1. No portal do Azure, navegue até o **workspace do Log Analytics**.

2. No workspace, selecione **Conta de Automação** em **Recursos Relacionados**.

3. Na página conta de automação, selecione **Desvincular conta** e responda às solicitações.

Enquanto tenta desvincular a conta de Automação, você pode acompanhar o andamento em **Notificações** no menu.

Depois que a conta de automação for desvinculada com êxito do workspace, execute as etapas na seção [conta de Automação do Azure autônoma](#delete-a-standalone-automation-account) para excluir a conta.

## <a name="delete-a-shared-capability-automation-account"></a>Excluir uma conta de automação de recurso compartilhado

Para excluir sua conta de automação vinculada a um workspace do Log Analytics em suporte de Gerenciamento de Atualizações, Controle de Alterações e Estoque e/ou Iniciar/Parar VMs fora do horário comercial, execute as etapas a seguir.

### <a name="step-1-delete-the-solution-from-the-linked-workspace"></a>Etapa 1. Excluir a solução do espaço de workspace vinculado

# <a name="azure-portal"></a>[Portal do Azure](#tab/azure-portal)

1. Entre no Azure em [https://portal.azure.com](https://portal.azure.com).

2. Navegue até sua conta de Automação do Azure e selecione **workspace vinculado** em **Recursos Relacionados**.

3. Selecione **Acessar o workspace**.

4. Em **Geral**, clique em **Soluções**.

5. Na página Soluções, selecione uma das seguintes opções com base nos recursos implantados na conta:

    * Para Iniciar/Parar VMs fora do horário comercial, selecione **Start-Stop-VM [nome do workspace]** .
    * Para Gerenciamento de Atualizações, selecione **Atualizações (nome do workspace)** .
    * Para Controle de Alterações e Estoque, selecione **ChangeTracking (nome do workspace)** .

6. Na página **Solução**, selecione **Excluir** no menu. Se mais de um dos recursos listados acima for implantado na conta de Automação do Azure e no workspace vinculado, você precisará selecionar e excluir cada um antes de continuar.

7. Enquanto as informações são verificadas e o recurso é excluído, você pode acompanhar o andamento em **Notificações**, disponível no menu. Você retornará à página Soluções após o processo de remoção.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para remover uma solução instalada usando o Azure PowerShell, use o cmdlet [Remove-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/remove-azmonitorloganalyticssolution).

```powershell
Remove-AzMonitorLogAnalyticsSolution -ResourceGroupName "resourceGroupName" -Name "solutionName"
```

---

### <a name="step-2-unlink-workspace-from-automation-account"></a>Etapa 2. Desvincular o workspace da conta de Automação

Há duas opções para desvincular o workspace do Log Analytics de sua conta de Automação do Azure. Você pode executar esse processo da conta de automação ou do espaço de trabalho vinculado.

Para desvincular da sua conta de Automação do Azure, execute as etapas a seguir.

1. No portal do Azure, navegue até **Contas de Automação**.

2. Abra a conta da Automação e selecione **Workspace vinculado** em **Recursos Relacionados** à esquerda.

3. Na página **Desvincular workspace**, clique em **Desvincular workspace** e responda às solicitações.

   ![Página Desvincular workspace](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Enquanto tenta desvincular o workspace do Log Analytics, você pode acompanhar o progresso em **Notificações** no menu.

Para desvincular do workspace execute as etapas a seguir.

1. No portal do Azure, navegue até o **workspace do Log Analytics**.

2. No workspace, selecione **Conta de Automação** em **Recursos Relacionados**.

3. Na página conta de automação, selecione **Desvincular conta** e responda às solicitações.

Enquanto tenta desvincular a conta de Automação, você pode acompanhar o andamento em **Notificações** no menu.

### <a name="step-3-delete-automation-account"></a>Etapa 3. Excluir conta de Automação do Azure

Depois que a conta de automação for desvinculada com êxito do workspace, execute as etapas na seção [conta de Automação do Azure autônoma](#delete-a-standalone-automation-account) para excluir a conta.

## <a name="next-steps"></a>Próximas etapas

Para criar uma conta de Automação no portal do Azure, veja [Criar uma conta de Automação do Azure autônoma](automation-create-standalone-account.md). Se preferir criar sua conta usando um modelo, veja [Criar uma conta de Automação usando um modelo do Azure Resource Manager](quickstart-create-automation-account-template.md).