---
title: Integrar soluções de Gerenciamento de Atualizações, Controle de Alterações e Inventário a partir de uma VM do Azure
description: Saiba como integrar uma máquina virtual do Azure com soluções de Gerenciamento de Atualizações, Controle de Alterações e Inventário que fazem parte da Automação do Azure.
services: automation
author: bobbytreed
ms.author: robreed
ms.date: 03/20/2019
ms.topic: conceptual
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 6b8693768e08f7ed80765015efa5af1a73b850c7
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476597"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>Integrar soluções de Gerenciamento de Atualizações, Controle de Alterações e Inventário a partir de uma máquina virtual do Azure

A Automação do Azure fornece soluções para ajudá-lo a gerenciar atualizações de segurança do sistema operacional, controlar alterações e inventariar o que está instalado em seus computadores. Há várias maneiras de integrar computadores. É possível integrar a solução de uma máquina virtual, [a partir de sua conta de Automação](automation-onboard-solutions-from-automation-account.md), [procurando em várias máquinas ](automation-onboard-solutions-from-browse.md), ou usando um [runbook](automation-onboard-solutions.md). Este artigo aborda a integração dessas soluções a partir de uma máquina virtual do Azure.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="enable-the-solutions"></a>Habilitar as soluções

Acesse uma máquina virtual existente. Em **OPERAÇÕES** , selecione **Gerenciamento de atualizações**, **Inventário** ou **Controle de alterações**. A máquina virtual pode existir em qualquer região não importa o local de sua conta de automação. Quando uma solução de uma VM de integração que você precisa ter o `Microsoft.OperationalInsights/workspaces/read` permissão para determinar se a VM estiver integrado a um espaço de trabalho. Para saber mais sobre permissões adicionais que são necessários em geral, consulte [permissões necessárias para integrar máquinas](automation-role-based-access-control.md#onboarding).

Para habilitar a solução somente para a VM, assegure-se de que **Habilitar para esta VM** está selecionado. Para integrar várias máquinas à solução, selecione **Habilitar para VMs nesta assinatura** e, em seguida, selecione **Clique para selecionar as máquinas para habilitar**. Para saber como integrar várias máquinas de uma só vez, consulte [Integrar soluções de Gerenciamento de Atualizações, Controle de Alterações e Inventário](automation-onboard-solutions-from-automation-account.md).

Selecione o espaço de trabalho do Azure Log Analytics e a conta de Automação e selecione **Habilitar** para habilitar a solução. A solução demora até 15 minutos para habilitar.

![Integrar solução de Gerenciamento de Atualizações](media/automation-onboard-solutions-from-vm/onboard-solution.png)

Acesse outras soluções e selecione **Habilitar**. O espaço de trabalho do Log Analytics e listas de menu suspenso de conta de automação estão desabilitadas porque essas soluções usam o mesmo espaço de trabalho e a conta de automação como a solução anteriormente habilitada.

> [!NOTE]
> O **Controle de Alterações** e **Inventário** usam a mesma solução. Quando uma dessas soluções estiver habilitada, a outra também estará habilitada.

## <a name="scope-configuration"></a>Configuração de escopo

Cada solução usa uma configuração de escopo no workspace para direcionar os computadores que obtêm a solução. A configuração do escopo é um grupo de uma ou mais pesquisas salvas que são usadas para limitar o escopo da solução a computadores específicos. Para acessar as configurações de escopo, na sua conta de Automação, em **RECURSOS RELACIONADOS**, selecione **Workspace**. No workspace, em **FONTES DE DADOS DO WORKSPACE**, selecione **Configurações de Escopo**.

Se o workspace selecionado ainda não tiver as soluções Gerenciamento de Atualizações ou Controle de Alterações, as seguintes configurações de escopo serão criadas:

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-Updates**

Se o workspace selecionado já tiver a solução, a solução não será implantada novamente e a configuração do escopo não será adicionada.

Selecione as reticências ( **...** ) em qualquer uma das configurações e, em seguida, selecione **Editar**. No painel **Editar configuração de escopo**, selecione **Selecionar Grupos de Computadores**. O painel **Grupos de Computadores** mostra as pesquisas salvas que são usadas para criar a configuração de escopo.

## <a name="saved-searches"></a>Pesquisas salvas

Quando um computador é adicionado às soluções Gerenciamento de Atualizações, Controle de Alterações ou Inventário, o computador é adicionado a uma das duas pesquisas salvas no workspace. As pesquisas salvas são consultas que contêm os computadores que são direcionados para essas soluções.

Vá até seu workspace. Em **Geral**, selecione **Pesquisas salvas**. As duas pesquisas salvas usadas por essas soluções são mostradas na tabela a seguir:

|NOME     |Categoria  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Atualizações        | Updates__MicrosoftDefaultComputerGroup         |

Selecione uma das pesquisas salvas para exibir a consulta usada para preencher o grupo. A imagem a seguir mostra a consulta e seus resultados:

![Pesquisas salvas](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="unlink-workspace"></a>Desvincular o workspace

As seguintes soluções são dependentes de um espaço de trabalho do Log Analytics:

* [Gerenciamento de atualizações](automation-update-management.md)
* [Controle de alterações](automation-change-tracking.md)
* [Iniciar/parar VMs durante os horários fora de pico](automation-solution-vm-management.md)

Se você decidir que você não deseja mais integrar sua conta de automação com um espaço de trabalho do Log Analytics, você pode desvincular sua conta diretamente do portal do Azure.  Antes de prosseguir, você precisa remover as soluções mencionadas anteriormente, caso contrário, esse processo será impedido de continuar. Examine o artigo sobre a solução específica que você importou para entender as etapas necessárias para removê-la.

Depois de remover essas soluções, você poderá executar as etapas a seguir para desvincular sua conta de Automação.

> [!NOTE]
> Algumas soluções, incluindo versões anteriores da solução de monitoramento do Azure SQL, podem ter criado ativos de automação e também podem precisar ser removidas antes de desvincular o workspace.

1. No portal do Azure, abra sua conta da Automação e, na página da conta da Automação, selecione **Workspace vinculado** na seção **Recursos Relacionados** à esquerda.

2. Na página Desvincular o workspace, clique em **Desvincular o workspace**.

   ![Página Desvincular workspace](media/automation-onboard-solutions-from-vm/automation-unlink-workspace-blade.png).

   Você receberá uma solicitação perguntando se deseja prosseguir.

3. Enquanto a Automação do Azure tenta desvincular a conta do seu espaço de trabalho do Log Analytics, você pode acompanhar o progresso no menu **Notificações**.

Se você tiver usado a solução Gerenciamento de Atualizações, como opção, convém remover os itens a seguir que não serão mais necessários após a remoção da solução.

* Atualizar agendas - Cada uma terá nomes que correspondam às implantações de atualizações que você criou)

* Grupos de trabalho híbrido criados para a solução - Cada um receberá um nome semelhante a machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8).

Se você tiver usado a solução Iniciar/parar VMs durante os horários fora de pico, como opção, convém remover os itens a seguir que não serão mais necessários após a remoção da solução.

* Iniciar e parar agendas de runbook da VM
* Iniciar e parar runbooks da VM
* variáveis

Como alternativa, você também pode desvincular seu espaço de trabalho de sua conta de automação do espaço de trabalho do Log Analytics. No espaço de trabalho, selecione **conta de automação** sob **recursos relacionados**. Na página conta de automação, selecione **desvincular conta**.

## <a name="next-steps"></a>Próximas etapas

Continue com os tutoriais das soluções para aprender como utilizá-las:

* [Tutorial - Gerenciar atualizações de sua VM](automation-tutorial-update-management.md)
* [Tutorial - Identificar o software em uma VM](automation-tutorial-installed-software.md)
* [Tutorial - Solucionar problemas de alterações em uma VM](automation-tutorial-troubleshoot-changes.md)
