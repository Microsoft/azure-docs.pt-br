---
title: Gerenciar políticas de laboratório no Azure DevTest Labs| Microsoft Docs
description: Aprenda a definir as políticas do laboratório, como os tamanhos das VMs, o número máximo de VMs por usuário e o desligamento automático.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: 7756aa64-49ca-45a0-9f90-0fd101c7be85
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: aa0ffbd69e73ddbef72e0eabf79f2736079c3d23
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60636359"
---
# <a name="manage-all-policies-for-a-lab-in-azure-devtest-labs"></a>Gerenciar todas as políticas de um laboratório no Azure DevTest Labs

O Azure DevTest Labs permite controlar custos e minimizar o desperdício em seus laboratórios, gerenciando políticas (configurações) para cada laboratório. Este artigo explica em detalhes passo a passo como definir cada política.  

## <a name="set-allowed-virtual-machine-sizes"></a>Definir tamanhos de máquina virtual permitidos
A política para definir os tamanhos de VM permitidos ajuda a minimizar o desperdício de laboratório, permitindo que você especifique quais tamanhos de VM são permitidos no laboratório. Quando essa política é ativada, somente os tamanhos de VM nesta lista podem ser usados para criar VMs.

1. No [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040), selecione um laboratório e, em seguida, selecione **Configuração e políticas**.

    ![Acessar as políticas e configuração do laboratório](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. No painel **Configurações e políticas** do laboratório, selecione **Tamanhos de máquinas virtuais permitidos**.
   
    ![Tamanhos de máquinas virtuais permitidos](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)

1. Selecione **Ativado** para habilitar essa política e **Desativado** para desabilitá-la.

1. Se você habilitar essa política, selecione um ou mais tamanhos de VM que podem ser criados no laboratório.

1. Clique em **Salvar**.

## <a name="set-virtual-machines-per-user"></a>Conjunto de máquinas virtuais por usuário
A política para **Máquinas virtuais por usuário** permite especificar o número de VMs que podem ser criadas por um usuário individual. Se um usuário tentar criar ou reivindicar uma VM quando o limite do usuário for atingido, uma mensagem de erro indicará que a VM não pode ser criada/reivindicada. 

1. No painel **Configuração e políticas** do laboratório, selecione **Máquinas virtuais por usuário**.
   
    ![Máquinas virtuais por usuário](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Selecione **Sim** para limitar o número de VMs por usuário. Se você não quiser limitar o número de VMs por usuário, selecione **Não**. Se você selecionar **Sim**, insira um valor numérico indicando o número de VMs que podem ser criadas ou reivindicadas por um usuário. 

1. Selecione **Sim** para limitar o número de VMs que podem usar o SSD (disco de estado sólido). Se você não quiser limitar o número de VMs que podem usar o SSD, selecione **Não**. Se você selecionar **Sim**, insira um valor indicando o número de VMs que podem ser criadas usando o SSD. 

1. Clique em **Salvar**.

## <a name="set-virtual-machines-per-lab"></a>Conjunto de máquinas virtuais por laboratório
A política para **Máquinas virtuais por laboratório** permite especificar o número de VMs que podem ser criadas para o laboratório atual. Se um usuário tentar criar uma VM quando o limite de laboratório for atendido, uma mensagem de erro indicará que a VM não pode ser criada. 

1. No painel **Configuração e políticas** do laboratório, selecione **Máquinas virtuais por laboratório**.
   
    ![Máquinas virtuais por laboratório](./media/devtest-lab-set-lab-policy/max-vms-per-lab.png)

1. Selecione **Sim** para limitar o número de VMs por laboratório. Se você não quiser limitar o número de VMs por laboratório, selecione **Não**. Se você selecionar **Sim**, insira um valor numérico indicando o número de VMs que podem ser criadas ou reivindicadas por um usuário. 

1. Selecione **Sim** para limitar o número de VMs que podem usar o SSD (disco de estado sólido). Se você não quiser limitar o número de VMs que podem usar o SSD, selecione **Não**. Se você selecionar **Sim**, insira um valor indicando o número de VMs que podem ser criadas usando o SSD. 

1. Clique em **Salvar**.

## <a name="set-auto-shutdown"></a>Desligamento automático de conjunto
A política de desligamento automático ajuda a minimizar o desperdício no laboratório, permitindo que você especifique o tempo de desligamento das VMs deste laboratório.

1. No painel **Configuração e políticas** do laboratório, selecione **Desligamento automático**.
   
    ![Desligamento automático](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Selecione **Ativado** para habilitar essa política e **Desativado** para desabilitá-la.

1. Se você habilitar essa política, especifique a hora (e fuso horário) para desligar todas as VMs no laboratório atual.

1. Especifique **Sim** ou **Não** para a opção de enviar uma notificação 15 minutos antes do tempo de desligamento automático especificado. Se escolher **Sim**, insira um ponto de extremidade de URL de webhook ou endereço de email especificando onde você deseja que a notificação seja postada ou enviada. O usuário recebe uma notificação e recebe a opção de adiar o encerramento.

   Para saber mais sobre os webhooks, veja [Criar um webhook ou uma função da API do Azure](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

1. Clique em **Salvar**.

Por padrão, uma vez habilitada, essa política se aplicará a todas as VMs do laboratório atual. Para remover essa configuração de uma VM específica, abra o painel de gerenciamento da VM e altere sua configuração **Auto shutdown**.

## <a name="set-auto-shutdown-policy"></a>Definir a política de desligamento automático
Como proprietário de um laboratório, você pode configurar uma programação de desligamento para todas as VMs em seu laboratório. Ao fazer isso, você pode economizar custos com a execução de máquinas que não estão sendo usadas (ociosas). Você pode impor uma política de desligamento em todas as suas VMs de laboratório de forma centralizada, mas também salvar os usuários de seu laboratório com o esforço de configurar um cronograma para suas máquinas individuais. Esse recurso permite que você defina a política em sua programação de laboratório, desde a não oferta de controle a controle total, até seus usuários de laboratório. Como proprietário de um laboratório, você pode configurar essa política seguindo as etapas a seguir:

1. Na home page do seu laboratório, selecione **Configuração e políticas**.
2. Selecione **Política de desligamento automático** na seção **Horários** do menu à esquerda.
3. Selecione uma das opções. As seções a seguir oferecem mais detalhes sobre essas opções: Definir política se aplica somente a novas VMs criadas no laboratório e não para as VMs já existentes. 

    ![Opções de política de desligamento automático](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

### <a name="user-sets-a-schedule-and-can-opt-out"></a>O usuário define um agendamento e pode recusar
Se você definir seu laboratório para essa política, os usuários do laboratório poderão substituir ou desativar a programação do laboratório. Essa opção concede aos usuários de laboratório controle total sobre o cronograma de desligamento automático de suas VMs. Os usuários de laboratório não veem nenhuma alteração na página de programação de desligamento automático da VM.

![Opção de política de desligamento automático - 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>O usuário define um agendamento e não pode recusar
Se você definir seu laboratório para essa política, os usuários do laboratório poderão substituir a programação do laboratório. No entanto, eles não podem desativar a política de desligamento automático. Essa opção garante que todas as máquinas do seu laboratório estejam em um cronograma de desligamento automático. Os usuários de laboratório podem atualizar o cronograma de desligamento automático de suas VMs e configurar notificações de desligamento.

![Opção de política de desligamento automático - 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>O usuário não tem controle sobre o cronograma definido pelo administrador do laboratório
Se você definir seu laboratório para essa política, os usuários do laboratório não poderão substituir ou recusar a programação do laboratório. Essa opção oferece ao administrador do laboratório o controle completo da programação de cada máquina no laboratório. Os usuários de laboratório só podem configurar notificações de desligamento automático para suas VMs.

![Opção de política de desligamento automático - 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="set-autostart"></a>Conjunto autostart
A diretiva de início automático permite especificar quando as VMs no laboratório atual devem ser iniciadas.  

1. No painel **Configuração e políticas** do laboratório, selecione **Autostart**.
   
    ![Início automático](./media/devtest-lab-set-lab-policy/auto-start.png)

2. Selecione **Ativado** para habilitar essa política e **Desativado** para desabilitá-la.

3. Se você habilitar esta política, especifique o horário de início agendado, o fuso horário e os dias da semana para os quais o horário se aplica. 

4. Clique em **Salvar**.

Quando habilitada, essa política não será aplicada automaticamente a quaisquer máquinas virtuais do laboratório atual. Para aplicar essa configuração a uma VM específica, abra o painel de gerenciamento da VM e altere sua configuração **Autostart**.

## <a name="set-expiration-date"></a>Definir a data de validade
Você pode definir uma data de validade ao [criar a VM](devtest-lab-add-vm.md). Em **Configurações avançadas**, escolha o ícone de calendário para especificar uma data em que a VM será excluída automaticamente. Por padrão, a VM nunca expira.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Próximas etapas
Depois de definir e aplicar as várias configurações da política de VM em seu laboratório, aqui estão algumas opções para você tentar em seguida:

* [Entender os endereços IP compartilhados](devtest-lab-shared-ip.md) - explica como os endereços IP compartilhados são usados no DevTest Labs para minimizar o número de endereços IP públicos necessárias para conectar-se às VMs de seu laboratório.
* [Configurar o gerenciamento de custo](devtest-lab-configure-cost-management.md) – ilustra como usar o gráfico **Tendência de custo estimado mensal**  
  para exibir o custo até a data estimado do mês atual e o custo projetado do final de mês.
* [Criar imagem personalizada](devtest-lab-create-template.md) – durante a criação de uma VM, você especifica uma base, que pode ser uma imagem personalizada ou uma imagem do Marketplace. Este artigo ilustra como criar uma imagem personalizada de um arquivo VHD.
* [Configurar imagens do Marketplace](devtest-lab-configure-marketplace-images.md) – O Azure DevTest Labs dá suporte à criação de VMs com base em imagens do Azure Marketplace. Este artigo ilustra como especificar quais imagens (caso haja alguma) do Azure Marketplace podem ser usadas durante a criação de VMs em um laboratório.
* [Criar uma VM em um laboratório](devtest-lab-add-vm.md) – ilustra como criar uma VM de uma imagem base (personalizada ou do Marketplace) e como trabalhar com artefatos na VM.

