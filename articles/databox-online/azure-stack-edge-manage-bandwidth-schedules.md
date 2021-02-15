---
title: Agendas de gerenciamento de largura de banda do Azure Stack Edge pro | Microsoft Docs
description: Descreve como usar o portal do Azure para gerenciar agendas de largura de banda no Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: e73a02c93807072e30c8ce2a1a7feb30e9d3c8c6
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978961"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-stack-edge-pro"></a>Use o portal do Azure para gerenciar agendas de largura de banda em seu Azure Stack Edge pro  

Este artigo descreve como gerenciar usuários em seu Azure Stack Edge pro. As agendas de largura de banda permitem configurar o uso da largura de banda da rede em diferentes agendamentos ao longo das horas do dia. Essas agendas podem ser aplicadas às operações de download e upload de seu dispositivo para a nuvem.

Você pode adicionar, modificar ou excluir as agendas de largura de banda para seu Azure Stack Edge pro por meio do portal do Azure.

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Adicionar uma agenda
> * modificar uma agenda
> * excluir uma agenda


## <a name="add-a-schedule"></a>Adicionar uma agenda

Para adicionar um agendamento, siga as etapas a seguir no portal do Azure.

1. Na portal do Azure para o recurso de borda do Azure Stack, vá para **largura de banda**.
2. No painel direito, selecione **+ Adicionar agendamento**.

    ![Selecionar Largura de Banda](media/azure-stack-edge-manage-bandwidth-schedules/add-schedule-1.png)

3. Em **Adicionar agendamento**: 

   1. Forneça o **dia de início**, o **dia de término**, a hora de **início**e a **hora de término** da agenda.
   2. Marque a opção **todos os dias** se essa agenda deve ser executada o dia inteiro.
   3. A **taxa de largura de banda** é a largura de banda em megabits por segundo (Mbps) usada pelo seu dispositivo em operações que envolvem a nuvem (tanto carregamentos quanto downloads). Forneça um número entre 20 e 1.000.000.007 para esse campo.
   4. Marque a opção de largura de banda **Ilimitada** se você não quiser restringir o download e o upload de dados.
   5. Selecione **Adicionar**.

      ![Adicionar agendamento](media/azure-stack-edge-manage-bandwidth-schedules/add-schedule-2.png)

3. É criada uma agenda com os parâmetros especificados. Em seguida, essa agenda é exibida na lista de agendas de largura de banda no portal.

    ![Lista atualizada de agendamentos da largura de banda](media/azure-stack-edge-manage-bandwidth-schedules/add-schedule-3.png)

## <a name="edit-schedule"></a>Editar agenda

Execute as etapas a seguir para editar uma agenda de largura de banda.

1. Na portal do Azure, vá para o recurso de borda do Azure Stack e, em seguida, vá para **largura de banda**. 
2. Na lista de agendas de largura de banda, selecione e selecione um agendamento que você deseja modificar.
    ![Selecionar agendamento da largura de banda](media/azure-stack-edge-manage-bandwidth-schedules/modify-schedule-1.png)

3. Faça as alterações desejadas e salve-as.

    ![Modificar usuário](media/azure-stack-edge-manage-bandwidth-schedules/modify-schedule-2.png)

4. Após a agenda ser modificada, a lista de agendas será atualizada para refletir a agenda modificada.

    ![Modificar usuário 2](media/azure-stack-edge-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>excluir uma agenda

Execute as etapas a seguir para excluir uma agenda de largura de banda associada ao dispositivo Azure Stack Edge pro.

1. Na portal do Azure, vá para o recurso de borda do Azure Stack e, em seguida, vá para **largura de banda**.  

2. Na lista de agendas de largura de banda, selecione uma agenda que você deseja excluir. Em **Editar agendamento**, selecione **Excluir**. Quando solicitado a confirmar, selecione **Sim**.

   ![Excluir um usuário](media/azure-stack-edge-manage-bandwidth-schedules/delete-schedule-2.png)

3. Após a agenda ser excluída, a lista de agendas será atualizada.


## <a name="next-steps"></a>Próximas etapas

- Saiba como [gerenciar compartilhamentos](azure-stack-edge-manage-shares.md).
