---
title: Remover Controle de Alterações de automação do Azure e recurso de inventário
description: Este artigo informa como parar de usar Controle de Alterações e inventário e desvincular uma conta de automação do espaço de trabalho Log Analytics.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 2e3e5abdfbb2bf2e9d7a12a677422adc67336775
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92209474"
---
# <a name="remove-change-tracking-and-inventory-from-automation-account"></a>Remover Controle de Alterações e inventário da conta de automação

Depois de habilitar o gerenciamento de suas máquinas virtuais usando a automação do Azure Controle de Alterações e o inventário, você pode decidir parar de usá-la e remover a configuração da conta e do espaço de trabalho vinculado Log Analytics. Este artigo mostra como remover completamente Controle de Alterações e inventário das VMs gerenciadas, da sua conta de automação e do espaço de trabalho do Log Analytics.

## <a name="sign-into-the-azure-portal"></a>Entrar no portal do Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="remove-management-of-vms"></a>Remover o gerenciamento de VMs

Antes de remover Controle de Alterações e inventário, você precisa primeiro parar de gerenciar suas VMs. Consulte [remover VMs de controle de alterações](remove-vms-from-change-tracking.md) para cancelar seu registro do recurso.

## <a name="remove-changetracking-solution"></a>Remover solução ChangeTracking

Para poder desvincular a conta de automação do espaço de trabalho, você precisa seguir estas etapas para remover completamente Controle de Alterações e inventário. Você removerá a solução **ChangeTracking** do espaço de trabalho.

1. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite **Log Analytics**. Conforme você começa a digitar, a lista filtra as sugestões com base em sua entrada. Selecione o **Log Analytics**.

2. Na lista de espaços de trabalho do Log Analytics, selecione o espaço de trabalho que você escolheu ao habilitar Controle de Alterações e inventário.

3. À esquerda, selecione **soluções**.  

4. Na lista de soluções, selecione **ChangeTracking (nome do espaço de trabalho)**. Na página **visão geral** da solução, selecione **excluir**. Quando for solicitado a confirmar, selecione **Sim**.

## <a name="unlink-workspace-from-automation-account"></a>Desvincular o workspace da conta de Automação

1. No portal do Azure, selecione **Contas de Automação**.

2. Abra a conta da Automação e selecione **Workspace vinculado** em **Recursos Relacionados** à esquerda.

3. Na página **desvincular espaço de trabalho** , selecione **desvincular espaço de trabalho** e responder a prompts.

   ![Página Desvincular workspace](media/remove-feature/automation-unlink-workspace-blade.png)

Ao tentar desvincular o espaço de trabalho Log Analytics, você pode acompanhar o progresso em **notificações** no menu.

Como alternativa, você pode desvincular o workspace do Log Analytics da sua conta da Automação no workspace:

1. No portal do Azure, selecione **Log Analytics**.

2. No espaço de trabalho, selecione **conta de automação** em **recursos relacionados**.

3. Na página Conta da Automação, selecione **Desvincular conta**.

Enquanto tenta desvincular a conta de automação, você pode acompanhar o progresso em **notificações** no menu.

## <a name="next-steps"></a>Próximas etapas

Para reabilitar esse recurso, consulte [habilitar o controle de alterações e o inventário de uma conta de automação](enable-from-automation-account.md), [habilitar controle de alterações e inventário navegando na portal do Azure](enable-from-portal.md), [habilitar controle de alterações e inventário de um runbook](enable-from-runbook.md)ou [habilitar o controle de alterações e o inventário de uma VM do Azure](enable-from-vm.md).
