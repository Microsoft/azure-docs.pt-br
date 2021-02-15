---
title: Remover o recurso de Gerenciamento de Atualizações de automação do Azure
description: Este artigo informa como parar de usar Gerenciamento de Atualizações e desvincular uma conta de automação do espaço de trabalho Log Analytics.
services: automation
ms.date: 07/28/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 1d83f859fce33b9499d01c4b58e69f56fdbbb293
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92221927"
---
# <a name="remove-update-management-from-automation-account"></a>Remover Gerenciamento de Atualizações da conta de automação

Depois de habilitar o gerenciamento de atualizações em suas máquinas virtuais usando a Gerenciamento de Atualizações de automação do Azure, você pode decidir parar de usá-la e remover a configuração da conta e do espaço de trabalho vinculado do Log Analytics.  Este artigo mostra como remover completamente Gerenciamento de Atualizações das VMs gerenciadas, da sua conta de automação e do espaço de trabalho Log Analytics.

## <a name="sign-into-the-azure-portal"></a>Entrar no portal do Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="remove-management-of-vms"></a>Remover o gerenciamento de VMs

Antes de remover Gerenciamento de Atualizações, você precisa primeiro parar de gerenciar suas VMs. Consulte [remover VMs de gerenciamento de atualizações](remove-vms.md) para cancelar seu registro do recurso.

## <a name="remove-updatemanagement-solution"></a>Remover solução UpdateManagement

Para poder desvincular a conta de automação do espaço de trabalho, você precisa seguir estas etapas para remover completamente Gerenciamento de Atualizações. Você removerá a solução de **atualizações** do espaço de trabalho.

1. Entre no [portal do Azure](https://portal.azure.com).

2. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite **Log Analytics**. Conforme você começa a digitar, a lista filtra as sugestões com base em sua entrada. Selecione o **Log Analytics**.

3. Na lista de espaços de trabalho do Log Analytics, selecione o espaço de trabalho que você escolheu quando habilitou Gerenciamento de Atualizações.

4. À esquerda, selecione **soluções**.  

5. Na lista de soluções, selecione **atualizações (nome do espaço de trabalho)**. Na página **visão geral** da solução, selecione **excluir**. Quando for solicitado a confirmar, selecione **Sim**.

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

## <a name="cleanup-automation-account"></a>Conta de automação de limpeza

Se Gerenciamento de Atualizações tiver sido configurado para dar suporte a versões anteriores do monitoramento do SQL do Azure, a instalação do recurso poderá ter criado ativos de automação que devem ser removidos. Para o Gerenciamento de Atualizações, convém remover os seguintes itens que não são mais necessários:

   * Atualizar agendamentos - cada um tem um nome que corresponde à implantação de atualização que você criou.
   * Grupos de Hybrid Worker criados para Gerenciamento de Atualizações-cada é nomeado de forma semelhante a *machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8)*.

## <a name="next-steps"></a>Próximas etapas

Para reabilitar esse recurso, consulte [habilitar gerenciamento de atualizações de uma conta de automação](enable-from-automation-account.md), [habilite gerenciamento de atualizações navegando na portal do Azure](enable-from-portal.md), [habilite gerenciamento de atualizações de um runbook](enable-from-runbook.md)ou [habilite gerenciamento de atualizações de uma VM do Azure](enable-from-vm.md).
