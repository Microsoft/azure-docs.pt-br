---
title: Use o portal do Azure para gerenciar agendamentos da largura de banda no Azure Data Box Gateway | Microsoft Docs
description: Descreve como usar o portal do Azure para gerenciar agendas de largura de banda em seu Azure Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: 55676128f482c8fb4dbdeab0523e4556f3957af7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96581444"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-data-box-gateway"></a>Use o portal do Azure para gerenciar agendas de largura de banda em seu Azure Data Box Gateway  

Este artigo descreve como gerenciar usuários em seu Azure Data Box Gateway. As agendas de largura de banda permitem configurar o uso da largura de banda da rede em diferentes agendamentos ao longo das horas do dia. Essas agendas podem ser aplicadas às operações de download e upload de seu dispositivo para a nuvem.

Você pode adicionar, modificar ou excluir as agendas de largura de banda de seu Data Box Gateway usando o portal do Azure.

Neste artigo, você aprenderá como:

> [!div class="checklist"]
>
> * Adicionar uma agenda
> * modificar uma agenda
> * excluir uma agenda

## <a name="add-a-schedule"></a>Adicionar uma agenda

Para adicionar um usuário, siga estas etapas no portal do Azure.

1. No portal do Azure de seu recurso do Data Box Gateway, acesse **Largura de banda**.
2. No painel direito, clique em **+ Adicionar agendamento**.

    ![Clique em adicionar usuário](media/data-box-gateway-manage-bandwidth-schedules/add-schedule-1.png)

3. Em **Adicionar agendamento**: 

   1. Forneça o **dia de início**, o **dia de término**, a hora de **início** e a **hora de término** da agenda. 
   2. Se essa agenda é executada o dia inteiro, você pode marcar a opção **O dia inteiro**. 
   3. A **taxa de largura de banda** é a largura de banda em megabits por segundo (Mbps) usada pelo seu dispositivo em operações que envolvem a nuvem (tanto carregamentos quanto downloads). Forneça um número entre 1 e 1.000 para esse campo. 
   4. Marque a opção de largura de banda **Ilimitada** se você não quiser restringir o download e o upload de dados. 
   5. Clique em **Adicionar**.

      ![Clique em Adicionar usuário 2](media/data-box-gateway-manage-bandwidth-schedules/add-schedule-2.png)

3. É criada uma agenda com os parâmetros especificados. Em seguida, essa agenda é exibida na lista de agendas de largura de banda no portal.


## <a name="edit-schedule"></a>Editar agenda

Execute as etapas a seguir para editar uma agenda de largura de banda. 

1. No portal do Azure, vá até seu recurso do Data Box Gateway e acesse Largura de banda. 
2. Na lista de agendas de largura de banda, selecione e clique em uma agenda que você deseja modificar.

    ![Modificar usuário](media/data-box-gateway-manage-bandwidth-schedules/modify-schedule-1.png)

3. Faça as alterações desejadas e salve-as.

    ![Modificar usuário 2](media/data-box-gateway-manage-bandwidth-schedules/modify-schedule-2.png)

4. Após a agenda ser modificada, a lista de agendas será atualizada para refletir a agenda modificada.

    ![Modificar usuário 3](media/data-box-gateway-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>excluir uma agenda

Execute as seguintes etapas para excluir uma agenda de largura de banda associada a seu dispositivo de Data Box Gateway.

1. Na portal do Azure, vá para o recurso de Gateway do Data Box e, em seguida, vá para **largura de banda**.  

2. Na lista de agendas de largura de banda, selecione uma agenda que você deseja excluir. Clique com o botão direito do mouse para exibir o menu de contexto e clique em **Excluir**. 

   ![Excluir um usuário](media/data-box-gateway-manage-bandwidth-schedules/delete-schedule-1.png)

3.  Após a agenda ser excluída, a lista de agendas será atualizada.



## <a name="next-steps"></a>Próximas etapas

- Saiba como [Gerenciar a largura de banda](data-box-gateway-manage-bandwidth-schedules.md). 
