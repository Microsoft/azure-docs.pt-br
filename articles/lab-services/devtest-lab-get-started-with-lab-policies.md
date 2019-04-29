---
title: Gerenciar as políticas básicas de laboratório no Azure DevTest Labs| Microsoft Docs
description: Saiba como definir algumas políticas básicas (configurações) para um laboratório no DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 8cc529fbf9b24335be1bec07f81c732ced7a2b72
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60773835"
---
# <a name="manage-basic-policies-for-a-lab-in-azure-devtest-labs"></a>Gerenciar políticas básicas para um laboratório no Azure DevTest Labs

O Azure DevTest Labs permite que você controle o custo e minimize o desperdício nos laboratórios gerenciando políticas (configurações) para cada laboratório. Neste artigo, você começa com políticas aprendendo como definir duas das políticas mais importantes - limitando o número de VMs (máquinas virtuais) que podem ser criadas ou declaradas por um único usuário e configurando o desligamento automático. Para exibir como definir cada política de laboratório, confira [Definir políticas de laboratório no Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

## <a name="accessing-a-labs-policies-in-azure-devtest-labs"></a>Acessar as políticas de laboratório no Azure DevTest Labs
As etapas a seguir orientam você durante a definição de políticas para um laboratório no Azure DevTest Labs:

Para exibir (e alterar) as políticas de um laboratório, siga estas etapas:

1. Entre no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **Todos os serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.

1. Na lista de laboratórios, selecione o laboratório desejado.   

1. Selecione **Configuração e políticas**.

    ![Painel de configurações de política](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. O painel **Configurações e políticas** contém um menu de configurações que você pode especificar. Este artigo aborda somente as configurações de **Máquinas virtuais por usuário**, **Desligamento automático** e **Início automático**. Para saber mais sobre o restante das configurações, consulte [Gerenciar todas as políticas de um laboratório no Azure DevTest Labs](./devtest-lab-set-lab-policy.md). 
   
## <a name="set-virtual-machines-per-user"></a>Conjunto de máquinas virtuais por usuário
A política de **Máquinas virtuais por usuário** permite que você especifique o número máximo de VMs que podem ser criadas por um usuário individual. Se um usuário tentar criar ou reivindicar uma VM quando o limite de usuários for atingido, uma mensagem de erro indicará que a VM não poderá ser criada/reivindicada. 

1. No menu **Configuração e políticas** do laboratório, selecione **Máquinas virtuais por usuário**.
   
    ![Máquinas virtuais por usuário](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Selecione **Sim** para limitar o número de VMs por usuário. Se você não quiser limitar o número de VMs por usuário, selecione **Não**. Se você selecionar **Sim**, insira um valor numérico indicando o número máximo de VMs que podem ser criadas ou reivindicadas por um usuário. 

1. Selecione **Sim** para limitar o número de VMs que podem usar o SSD (disco de estado sólido). Se você não quiser limitar o número de VMs que podem usar o SSD, selecione **Não**. Se você selecionar **Sim**, insira um valor indicando o número máximo de VMs que podem ser criadas usando SSD. 

1. Clique em **Salvar**.

## <a name="set-auto-shutdown"></a>Definir desligamento automático
A política de desligamento automático ajuda a minimizar o desperdício de laboratório, permitindo que você especifique a hora em que as VMs desse laboratório serão desligadas.

1. No painel **Configuração e políticas** do laboratório, selecione **Desligamento automático**.
   
    ![Desligamento automático](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Selecione **Ativado** para habilitar essa política e **Desativado** para desabilitá-la.

1. Se você habilitar essa política, especifique a hora (e fuso horário) para desligar todas as VMs no laboratório atual.

1. Especifique **Sim** ou **Não** para a opção de enviar uma notificação 15 minutos antes do tempo de desligamento automático especificado. Se escolher **Sim**, insira um ponto de extremidade de URL de webhook ou endereço de email especificando onde você deseja que a notificação seja postada ou enviada. O usuário recebe a notificação e recebe a opção para atrasar o desligamento.

   Para saber mais sobre os webhooks, veja [Criar um webhook ou uma função da API do Azure](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

1. Clique em **Salvar**.

Por padrão, uma vez habilitada, essa política se aplicará a todas as VMs do laboratório atual. Para remover essa configuração de uma VM específica, abra o painel de gerenciamento da VM e altere sua configuração de **Desligamento Automático**.

## <a name="set-auto-start"></a>Definir início automático
A política de início automático permite que você especifique quando as VMs do laboratório atual deverão ser iniciadas.  

1. No painel **Configuração e políticas** do laboratório, selecione **Início automático**.
   
    ![Início automático](./media/devtest-lab-set-lab-policy/auto-start.png)

2. Selecione **Ativado** para habilitar essa política e **Desativado** para desabilitá-la.

3. Se você habilitar esta política, especifique o horário de início agendado, o fuso horário e os dias da semana para os quais o horário se aplica. 

4. Clique em **Salvar**.

Quando habilitada, essa política não será aplicada automaticamente a quaisquer máquinas virtuais do laboratório atual. Para aplicar essa configuração a uma VM existente, abra o painel de gerenciamento da VM e altere sua configuração de **Início automático**.

## <a name="next-steps"></a>Próximas etapas

- [Definir políticas de laboratório no Azure DevTest Labs](devtest-lab-set-lab-policy.md) - saiba como modificar outras políticas de laboratório.
