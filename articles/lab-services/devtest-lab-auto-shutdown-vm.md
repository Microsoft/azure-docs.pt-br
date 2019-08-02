---
title: Definir configurações de desligamento automático para uma VM no Azure DevTest Labs | Microsoft Docs
description: Saiba como definir as configurações de desligamento automático para uma VM (máquina virtual) para que a VM seja desligada automaticamente quando não estiver em uso.
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
ms.date: 07/19/2019
ms.author: spelluru
ms.openlocfilehash: 934e8fd71c901c89f328c777103a8cb39bf21ac4
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68361564"
---
# <a name="configure-autoshutdown-settings-for-a-vm-in-azure-devtest-labs"></a>Definir configurações de desligamento automático para uma VM no Azure DevTest Labs
O Azure DevTest Labs permite que você controle o custo e minimize o desperdício nos laboratórios gerenciando políticas (configurações) para cada laboratório. Este artigo mostra como configurar a política de desligamento automático para uma conta de laboratório e definir as configurações de desligamento automático para um laboratório na conta do laboratório. Para exibir como definir cada política de laboratório, confira [Definir políticas de laboratório no Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

## <a name="autoshutdown-policy-for-a-lab"></a>Política de desligamento automático para um laboratório
Como proprietário de um laboratório, você pode configurar uma programação de desligamento para todas as VMs em seu laboratório. Ao fazer isso, você pode economizar custos com a execução de máquinas que não estão sendo usadas (ociosas). Você pode impor uma política de desligamento em todas as suas VMs de laboratório de forma centralizada, mas também salvar os usuários de seu laboratório com o esforço de configurar um cronograma para suas máquinas individuais. Esse recurso permite que você defina a política em sua programação de laboratório, desde a não oferta de controle a controle total, até seus usuários de laboratório. Como proprietário de um laboratório, você pode configurar essa política seguindo as etapas a seguir:

1. Na home page do seu laboratório, selecione **Configuração e políticas**.
2. Selecione **Política de desligamento automático** na seção **Horários** do menu à esquerda.
3. Selecione uma das opções. As seções a seguir fornecem mais detalhes sobre essas opções: A política de conjunto se aplica somente a novas VMs criadas no laboratório e não às VMs já existentes. 

    ![Opções de política de desligamento automático](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

## <a name="configure-autoshutdown-settings-for-a-lab"></a>Definir configurações de desligamento automático para um laboratório
A política de desligamento automático ajuda a minimizar o desperdício de laboratório, permitindo que você especifique a hora em que as VMs do laboratório são desligadas.

Para exibir (e alterar) as políticas de um laboratório, siga estas etapas:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
3. Na lista de laboratórios, selecione o laboratório desejado.   
4. Selecione **Configuração e políticas**.

    ![Painel de configurações de política](./media/devtest-lab-set-lab-policy/policies-menu.png)
5. No painel **configuração e políticas** do laboratório, selecione desligamento **automático** em **agendas**.
   
    ![Desligamento automático](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
6. Selecione **Ativado** para habilitar essa política e **Desativado** para desabilitá-la.
7. Se você habilitar essa política, especifique a hora (e fuso horário) para desligar todas as VMs no laboratório atual.
8. Especifique **Sim** ou **não** para a opção de enviar uma notificação 15 minutos antes do tempo de desligamento automático especificado. Se escolher **Sim**, insira um ponto de extremidade de URL de webhook ou endereço de email especificando onde você deseja que a notificação seja postada ou enviada. O usuário recebe a notificação e recebe a opção para atrasar o desligamento. Para obter mais informações, consulte a seção [notificações](#notifications) . 
9. Clique em **Salvar**.

    Por padrão, uma vez habilitada, essa política se aplicará a todas as VMs do laboratório atual. Para remover essa configuração de uma VM específica, abra o painel Gerenciamento da VM e altere sua  configuração de desligamento automático.

## <a name="configure-autoshutdown-settings-for-a-vm"></a>Definir configurações de desligamento automático para uma VM

### <a name="user-sets-a-schedule-and-can-opt-out"></a>O usuário define um agendamento e pode recusar
Se essa opção de política for definida para o laboratório, os usuários poderão substituir ou recusar a agenda de laboratório. Essa opção concede aos usuários de laboratório controle total sobre o cronograma de desligamento automático de suas VMs. Os usuários de laboratório não veem nenhuma alteração na página de programação de desligamento automático da VM.

![Opção de política de desligamento automático-1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>O usuário define um agendamento e não pode recusar
Se essa opção de política for definida para o laboratório, os usuários poderão substituir a agenda do laboratório. No entanto, eles não podem desativar a política de desligamento automático. Essa opção garante que todas as máquinas do seu laboratório estejam em um cronograma de desligamento automático. Os usuários de laboratório podem atualizar o cronograma de desligamento automático de suas VMs e configurar notificações de desligamento.

![Opção de política de desligamento automático-2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>O usuário não tem controle sobre o cronograma definido pelo administrador do laboratório
Se essa opção de política for definida para o laboratório, os usuários não poderão substituir nem recusar a agenda de laboratório. Essa opção oferece ao administrador do laboratório o controle completo da programação de cada máquina no laboratório. Os usuários de laboratório só podem configurar notificações de desligamento automático para suas VMs.

![Opção de política de desligamento automático-3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="notifications"></a>Notificações
Após o desligamento automático configurado pelo proprietário do laboratório, as notificações serão enviadas para os usuários do laboratório 15 minutos antes do desligamento automático ser disparado se qualquer uma de suas VMs for afetada. Essa opção dá aos usuários do laboratório a oportunidade de salvar seu trabalho antes do desligamento. A notificação também fornece links para cada VM para as seguintes ações:

- Ignorar o desligamento automático para este tempo
- Adiar o desligamento automático por uma hora ou 2 horas, para que eles possam continuar trabalhando na VM.

A notificação é enviada por meio do ponto de extremidade de gancho da Web configurado ou de um endereço de email especificado pelos proprietários do laboratório nas configurações de desligamento automático. WebHooks permitem que você crie ou configure integrações que assinam determinados eventos. Quando um desses eventos for disparado, o DevTest Labs enviará uma carga HTTP POST para a URL configurada do webhook. Para saber mais sobre os webhooks, veja [Criar um webhook ou uma função da API do Azure](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

Recomendamos que você use os ganchos da Web porque eles são amplamente suportados por vários aplicativos (por exemplo, margem de atraso, aplicativos lógicos do Azure e assim por diante) e permite que você implemente sua própria maneira de enviar notificações. Para obter um exemplo de como receber notificações de desligamento automático de emails usando aplicativos lógicos do Azure, consulte[criar um aplicativo lógico que recebe notificações por email](devtest-lab-auto-shutdown.md#create-a-logic-app-that-receives-email-notifications). 


## <a name="next-steps"></a>Próximas etapas
Consulte [gerenciar políticas de desligamento automático para um laboratório no Azure DevTest Labs](devtest-lab-auto-shutdown.md)
