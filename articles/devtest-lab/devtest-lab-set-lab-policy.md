---
title: "Gerenciar políticas de laboratório no Azure DevTest Labs| Microsoft Docs"
description: "Aprenda a definir as políticas do laboratório, como os tamanhos das VMs, o número máximo de VMs por usuário e o desligamento automático."
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 7756aa64-49ca-45a0-9f90-0fd101c7be85
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: tarcher
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 328a4d893637d7150807855e118b485a2c3bbfc5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-all-policies-for-a-lab-in-azure-devtest-labs"></a>Gerenciar todas as políticas de um laboratório no Azure DevTest Labs

O Azure DevTest Labs permite controlar o custo e minimize o desperdício nos laboratórios gerenciando políticas (configurações) de cada laboratório. Este artigo explica em detalhes passo a passo como definir cada política.  

## <a name="set-allowed-virtual-machine-sizes"></a>Definir tamanhos de máquina virtual permitidos
A política para definir os tamanhos de VM permitidos ajuda a minimizar o desperdício de laboratório, permitindo que você especifique quais tamanhos de VM são permitidos no laboratório. Quando essa política é ativada, somente os tamanhos de VM nesta lista podem ser usados para criar VMs.

1. Na folha **Configurações e políticas** do laboratório, selecione **Tamanhos de máquinas virtuais permitidos**.
   
    ![Tamanhos de máquinas virtuais permitidos](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)

1. Selecione **Ativado** para habilitar essa política e **Desativado** para desabilitá-la.

1. Se você habilitar essa política, selecione um ou mais tamanhos de VM que podem ser criados no laboratório.

1. Selecione **Salvar**.

## <a name="set-virtual-machines-per-user"></a>Conjunto de máquinas virtuais por usuário
A política de **Máquinas virtuais por usuário** permite que você especifique o número máximo de VMs que podem ser criadas por um usuário individual. Se um usuário tentar criar ou reivindicar uma VM quando o limite de usuários for atingido, uma mensagem de erro indicará que a VM não poderá ser criada/reivindicada. 

1. No menu **Configuração e políticas** do laboratório, selecione **Máquinas virtuais por usuário**.
   
    ![Máquinas virtuais por usuário](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Selecione **Sim** para limitar o número de VMs por usuário. Se você não quiser limitar o número de VMs por usuário, selecione **Não**. Se você selecionar **Sim**, insira um valor numérico indicando o número máximo de VMs que podem ser criadas ou reivindicadas por um usuário. 

1. Selecione **Sim** para limitar o número de VMs que podem usar o SSD (disco de estado sólido). Se você não quiser limitar o número de VMs que podem usar o SSD, selecione **Não**. Se você selecionar **Sim**, insira um valor indicando o número máximo de VMs que podem ser criadas usando SSD. 

1. Selecione **Salvar**.

## <a name="set-virtual-machines-per-lab"></a>Conjunto de máquinas virtuais por laboratório
A política de **Máquinas virtuais por laboratório** permite que você especifique o número máximo de VMs que podem ser criadas para o laboratório atual. Se um usuário tentar criar uma VM quando o limite de laboratórios for alcançado, uma mensagem de erro indicará que a VM não pode ser criada. 

1. No menu **Configuração e políticas** do laboratório, selecione **Máquinas virtuais por laboratório**.
   
    ![Máquinas virtuais por laboratório](./media/devtest-lab-set-lab-policy/max-vms-per-lab.png)

1. Selecione **Sim** para limitar o número de VMs por laboratório. Se você não quiser limitar o número de VMs por laboratório, selecione **Não**. Se você selecionar **Sim**, insira um valor numérico indicando o número máximo de VMs que podem ser criadas ou reivindicadas por um usuário. 

1. Selecione **Sim** para limitar o número de VMs que podem usar o SSD (disco de estado sólido). Se você não quiser limitar o número de VMs que podem usar o SSD, selecione **Não**. Se você selecionar **Sim**, insira um valor indicando o número máximo de VMs que podem ser criadas usando SSD. 

1. Selecione **Salvar**.

## <a name="set-auto-shutdown"></a>Definir desligamento automático
A política de desligamento automático ajuda a minimizar o desperdício de laboratório, permitindo que você especifique a hora em que as VMs desse laboratório serão desligadas.

1. Na folha **Configuração e políticas** do laboratório, selecione **Desligamento automático**.
   
    ![Desligamento automático](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Selecione **Ativado** para habilitar essa política e **Desativado** para desabilitá-la.

1. Se você habilitar essa política, especifique a hora (e fuso horário) para desligar todas as VMs no laboratório atual.

1. Especifique **Sim** ou **Não** para a opção de enviar uma notificação 15 minutos antes do tempo de desligamento automático especificado. Se você especificar **Sim**, insira um ponto de extremidade de URL de webhook para receber a notificação. Para saber mais sobre os webhooks, veja [Criar um webhook ou uma função da API do Azure](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

1. Selecione **Salvar**.

    Por padrão, uma vez habilitada, essa política se aplicará a todas as VMs do laboratório atual. Para remover essa configuração de uma VM específica, abra a folha da VM e altere sua configuração de **Desligamento Automático** 

## <a name="set-auto-start"></a>Definir início automático
A política de início automático permite que você especifique quando as VMs do laboratório atual deverão ser iniciadas.  

1. Na folha **Configuração e políticas** do laboratório, selecione **Início automático**.
   
    ![Início automático](./media/devtest-lab-set-lab-policy/auto-start.png)

2. Selecione **Ativado** para habilitar essa política e **Desativado** para desabilitá-la.

3. Se você habilitar esta política, especifique o horário de início agendado, o fuso horário e os dias da semana para os quais o horário se aplica. 

4. Selecione **Salvar**.

    Quando habilitada, essa política não será aplicada automaticamente a quaisquer máquinas virtuais do laboratório atual. Para aplicar essa configuração a uma VM específica, abra a folha da VM e altere sua configuração de **Início automático** 

## <a name="set-expiration-date"></a>Definir a data de validade
Você pode definir uma data de validade ao [criar a VM](devtest-lab-add-vm.md). Em **Configurações avançadas**, escolha o ícone de calendário para especificar uma data em que a VM será excluída automaticamente.  Por padrão, a VM nunca expirará.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Próximas etapas
Depois de definir e aplicar as várias configurações da política de VM em seu laboratório, aqui estão algumas opções para você tentar em seguida:

* [Entender os endereços IP compartilhados](devtest-lab-shared-ip.md) - explica como os endereços IP compartilhados são usados no DevTest Labs para minimizar o número de endereços IP públicos necessárias para conectar-se às VMs de seu laboratório.
* [Configurar o gerenciamento de custo](devtest-lab-configure-cost-management.md) – ilustra como usar o gráfico **Tendência de custo estimado mensal**  
  para exibir o custo até a data estimado do mês atual e o custo projetado do final de mês.
* [Criar imagem personalizada](devtest-lab-create-template.md) – durante a criação de uma VM, você especifica uma base, que pode ser uma imagem personalizada ou uma imagem do Marketplace. Este artigo ilustra como criar uma imagem personalizada de um arquivo VHD.
* [Configurar imagens do Marketplace](devtest-lab-configure-marketplace-images.md) – O Azure DevTest Labs dá suporte à criação de VMs com base em imagens do Azure Marketplace. Este artigo ilustra como especificar quais imagens (caso haja alguma) do Azure Marketplace podem ser usadas durante a criação de VMs em um laboratório.
* [Criar uma VM em um laboratório](devtest-lab-add-vm-with-artifacts.md) – ilustra como criar uma VM de uma imagem base (personalizada ou do Marketplace) e como trabalhar com artefatos na VM.

