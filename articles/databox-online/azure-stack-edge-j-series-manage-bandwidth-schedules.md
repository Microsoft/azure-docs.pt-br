---
title: Azure Stack Edge pro GPU gerenciar agendas de largura de banda | Microsoft Docs
description: Descreve como usar o portal do Azure para gerenciar agendas de largura de banda em sua GPU do Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/05/2021
ms.author: alkohli
ms.openlocfilehash: 3182258245701903e7b3d6d6163cf3e2bd55c1fc
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97915412"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-stack-edge-pro-gpu"></a>Usar o portal do Azure para gerenciar agendamentos de largura de banda em sua GPU do Azure Stack Edge pro 

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Este artigo descreve como gerenciar agendas de largura de banda em seu Azure Stack Edge pro. As agendas de largura de banda permitem configurar o uso da largura de banda da rede em diferentes agendamentos ao longo das horas do dia. Essas agendas podem ser aplicadas às operações de download e upload de seu dispositivo para a nuvem.

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

    ![Selecionar Largura de Banda](media/azure-stack-edge-j-series-manage-bandwidth-schedules/add-schedule-1.png)

3. Em **Adicionar agendamento**:

   1. Forneça o **dia de início**, o **dia de término**, a hora de **início** e a **hora de término** da agenda.
   2. Marque a opção **todos os dias** se essa agenda deve ser executada o dia inteiro.
   3. A **taxa de largura de banda** é a largura de banda em megabits por segundo (Mbps) usada pelo seu dispositivo em operações que envolvem a nuvem (tanto carregamentos quanto downloads). Forneça um número entre 20 e 2.147.483.647 para este campo.
   4. Selecione **largura de banda ilimitada** se você não quiser limitar a data de carregamento e download.
   5. Selecione **Adicionar**.

      ![Adicionar agendamento](media/azure-stack-edge-j-series-manage-bandwidth-schedules/add-schedule-2.png)

3. É criada uma agenda com os parâmetros especificados. Em seguida, essa agenda é exibida na lista de agendas de largura de banda no portal.

    ![Lista atualizada de agendamentos da largura de banda](media/azure-stack-edge-j-series-manage-bandwidth-schedules/add-schedule-3.png)

## <a name="edit-schedule"></a>Editar agenda

Execute as etapas a seguir para editar uma agenda de largura de banda.

1. Na portal do Azure, vá para o recurso de borda do Azure Stack e, em seguida, vá para **largura de banda**.
2. Na lista de agendamentos de largura de banda, selecione uma agenda que você deseja modificar.

   ![Selecionar agendamento da largura de banda](media/azure-stack-edge-j-series-manage-bandwidth-schedules/modify-schedule-1.png)

3. Faça as alterações desejadas e salve-as.

    ![Modificar usuário](media/azure-stack-edge-j-series-manage-bandwidth-schedules/modify-schedule-2.png)

4. Após a agenda ser modificada, a lista de agendas será atualizada para refletir a agenda modificada.

    ![Modificar usuário 2](media/azure-stack-edge-j-series-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>excluir uma agenda

Execute as etapas a seguir para excluir uma agenda de largura de banda associada ao dispositivo Azure Stack Edge pro.

1. Na portal do Azure, vá para o recurso de borda do Azure Stack e, em seguida, vá para **largura de banda**.  

2. Na lista de agendas de largura de banda, selecione uma agenda que você deseja excluir. Em **Editar agendamento**, selecione **Excluir**. Quando solicitado a confirmar, selecione **Sim**.

   ![Excluir um usuário](media/azure-stack-edge-j-series-manage-bandwidth-schedules/delete-schedule-2.png)

3. Após a agenda ser excluída, a lista de agendas será atualizada.


## <a name="next-steps"></a>Próximas etapas

- Saiba como [gerenciar compartilhamentos](azure-stack-edge-j-series-manage-shares.md).
