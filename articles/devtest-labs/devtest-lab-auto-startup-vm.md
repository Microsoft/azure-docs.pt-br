---
title: Definir configurações de inicialização automática para uma VM no Azure DevTest Labs | Microsoft Docs
description: Saiba como definir configurações de inicialização automática para VMs em um laboratório. Essa configuração permite que as VMs no laboratório sejam iniciadas automaticamente em um agendamento.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 83e7b0836273a59eaaf66471bd0cb42d63ccf1c3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91328472"
---
# <a name="auto-startup-lab-virtual-machines"></a>Máquinas virtuais do laboratório de inicialização automática  
Azure DevTest Labs permite que você configure máquinas virtuais em seu laboratório para que sejam iniciadas e desligadas automaticamente com base em uma agenda. Para obter informações sobre como definir as configurações de desligamento automático, consulte [gerenciar políticas de desligamento automático para um laboratório no Azure DevTest Labs](devtest-lab-auto-shutdown.md). 

Diferentemente do desligamento automático, onde todas as VMs são incluídas quando a política é ativada, a política de inicialização automática exige que um usuário de laboratório selecione explicitamente uma VM e opte por essa agenda. Dessa forma, você não encontrará facilmente a situação em que as VMs indesejadas são iniciadas automaticamente e causam gastos inesperados.

Este artigo mostra como configurar a política de inicialização automática para um laboratório.

## <a name="configure-autostart-settings-for-a-lab"></a>Definir configurações de inicialização automática para um laboratório 
1. Navegue até a home page do seu laboratório. 
2. Selecione **configuração e políticas** no menu à esquerda. 

    ![Captura de tela que mostra o menu "configuração e políticas" no laboratório de DevTest.](./media/devtest-lab-auto-startup-vm/configuration-policies-menu.png)
3. Na página **configuração e políticas** , execute as seguintes etapas:
    
    1. Selecione **ativado** para **permitir que as máquinas virtuais sejam agendadas para início automático** para habilitar o recurso de inicialização automática para este laboratório. 
    2. Selecione uma hora de início (por exemplo: 8:00:00 AM) para o campo **início da agenda** . 
    3. Selecione um **fuso horário** a ser usado. 
    4. Selecione os **dias da semana** em que as VMs precisam ser iniciadas automaticamente. 
    5. Em seguida, selecione **salvar** na barra de ferramentas para salvar as configurações. 

        ![Configurações de inicialização automática](./media/devtest-lab-auto-startup-vm/auto-start-configuration.png)

        > [!IMPORTANT]
        > Essa política não aplica automaticamente o início automático a todas as máquinas virtuais no laboratório. Para **aceitar** máquinas virtuais individuais, acesse a página da máquina virtual e habilite a **inicialização automática** para essa VM.

## <a name="enable-autostart-for-a-vm-in-the-lab"></a>Habilitar a inicialização automática para uma VM no laboratório
O procedimento a seguir fornece etapas para optar por aceitar uma VM na política de início automática do laboratório. 

1. No home page para seu laboratório, selecione a **VM** na lista **minhas máquinas virtuais** . 

    ![Menu de configuração e políticas](./media/devtest-lab-auto-startup-vm/select-vm.png)
2. Na página **máquina virtual** , selecione **inicialização automática** no menu à esquerda ou na lista **agendas** . 

    ![Selecionar menu de inicialização automática](./media/devtest-lab-auto-startup-vm/select-auto-start.png)
3. Na página **inicialização** automática, selecione **ativado** para a opção **permitir que esta máquina virtual seja agendada para o início automático** .

    ![Habilitar inicialização automática para a VM](./media/devtest-lab-auto-startup-vm/auto-start-vm.png)
4. Em seguida, selecione **salvar** na barra de ferramentas para salvar a configuração. 


## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre a política de desligamento automático de configuração para um laboratório, consulte [gerenciar políticas de desligamento automático para um laboratório no Azure DevTest Labs](devtest-lab-auto-shutdown.md)
