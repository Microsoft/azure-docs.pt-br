---
title: Criar agendamento para laboratórios de salas de aula no Azure Lab Services | Microsoft Docs
description: Saiba como criar agendamentos para laboratórios de sala de aula no Azure Lab Services para que as VMs nos laboratórios inicializem e desliguem em um horário especificado.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2019
ms.author: spelluru
ms.openlocfilehash: f607ba68563aa92797f45cf77db0575ae6802fee
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385593"
---
# <a name="create-and-manage-schedules-for-classroom-labs-in-azure-lab-services"></a>Criar e gerenciar agendamentos para laboratórios de sala de aula do Azure Lab Services 
Agendamentos permitem que você configure um laboratório de sala de aula, de modo que as VMs no laboratório iniciam e desligam automaticamente em um horário especificado. Você pode definir um agendamento único ou recorrente. Os procedimentos a seguir fornecem as etapas para criar e gerenciar agendamentos para um laboratório de sala de aula: 

> [!IMPORTANT]
> O tempo de execução agendado de VMs não conta com a [cota alocada a um usuário](how-to-configure-student-usage.md#set-quotas-for-users). A cota é para o tempo fora dos horários agendados que um aluno passa em VMs. 

## <a name="add-a-schedule-once"></a>Adicionar um agendamento (uma vez)

1. Alterne para a página **Agendamentos** e selecione **Adicionar agendamento** na barra de ferramentas. 

    ![Botão Adicionar agendamento na página Agendamentos](../media/how-to-create-schedules/add-schedule-button.png)
2. Na página **Adicionar agendamento**, verifique se a opção **Uma vez** está selecionada na parte superior. Se não estiver, selecione **Uma vez**. 
3. Para **Data de agendamento (obrigatório)** , insira a data ou selecione o ícone de calendário para selecionar uma data. 
4. Para **Hora de início**, selecione a hora em que você deseja que as VMs sejam iniciadas. A hora de início é necessária se a hora de término não está definida. Selecione **Remover evento de início** se você quiser especificar apenas a hora de término. Se a **Hora de início** é desabilitada, selecione **Adicionar evento de início** ao lado da lista suspensa para habilitá-lo. 
5. Para **Hora de término**, selecione a hora em que você deseja que as VMs sejam desligadas. A hora de término é necessária se a hora de início não está definida. Selecione **Remover evento de interrupção** se você quiser especificar apenas a hora de início. Se a **Hora de término** é desabilitada, selecione **Adicionar evento de interrupção** ao lado da lista suspensa para habilitá-lo.
6. Para **Fuso horário (obrigatório)** , selecione o fuso horário das horas de início e de término especificadas por você. 
7. Para **Anotações**, insira qualquer descrição ou anotações para o agendamento. 
8. Clique em **Salvar**. 

    ![Agendamento único](../media/how-to-create-schedules/add-schedule-page.png)

## <a name="add-a-recurring-schedule-weekly"></a>Adicionar um agendamento recorrente (semanal)

1. Alterne para a página **Agendamentos** e selecione **Adicionar agendamento** na barra de ferramentas. 

    ![Botão Adicionar agendamento na página Agendamentos](../media/how-to-create-schedules/add-schedule-button.png)
2. Na página **Adicionar agendamento**, alterne para **Semanal** na parte superior. 
3. Para **Dias do agendamento (obrigatório)** , selecione os dias em que você deseja que o agendamento entre em vigor. No exemplo abaixo, a marca Segunda-Sexta foi selecionada. 
4. No campo **De**, insira a **data de início do agendamento** ou escolha uma data, selecionando o botão **calendário**. O campo é obrigatório. 
5. Para **data de término do agendamento**, insira ou selecione uma data de término em que as VMs devem ser desligadas. 
6. Para **Hora de início**, selecione a hora em que você deseja que as VMs sejam iniciadas. A hora de início é necessária se a hora de término não está definida. Selecione **Remover evento de início** se você quiser especificar apenas a hora de término. Se a **Hora de início** é desabilitada, selecione **Adicionar evento de início** ao lado da lista suspensa para habilitá-lo. 
7. Para **Hora de término**, selecione a hora em que você deseja que as VMs sejam desligadas. A hora de término é necessária se a hora de início não está definida. Selecione **Remover evento de interrupção** se você quiser especificar apenas a hora de início. Se a **Hora de término** é desabilitada, selecione **Adicionar evento de interrupção** ao lado da lista suspensa para habilitá-lo.
8. Para **Fuso horário (obrigatório)** , selecione o fuso horário das horas de início e de término especificadas por você.  
9. Para **Anotações**, insira qualquer descrição ou anotações para o agendamento. 
10. Clique em **Salvar**. 

    ![Agendamento semanal](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>Exibir agendamentos no calendário
Você pode ver as datas e horas agendadas realçadas na exibição de calendário, conforme mostrado na imagem a seguir:

![Agendamentos na exibição de calendário](../media/how-to-create-schedules/schedules-in-calendar.png)

Selecione o botão **Hoje** no canto superior direito para alternar para a data atual no calendário. Selecione **seta para a esquerda** para alternar para a semana anterior e **seta para a direita** para alternar para a próxima semana no calendário. 

## <a name="edit-a-schedule"></a>Editar um agendamento
Quando você clica duas vezes em um agendamento realçado no calendário ou seleciona o botão de **lápis** na barra de ferramentas, você vê a página **Editar agendamento**. Atualizar as configurações nessa página é o mesmo que atualizar as configurações na página **Adicionar agendamento**, conforme descrito na seção [Adicionar um agendamento recorrente](#add-a-recurring-schedule-weekly). 

![Página editar agendamento](../media/how-to-create-schedules/edit-schedule-page.png)

## <a name="delete-a-schedule"></a>excluir uma agenda

1. Para excluir um agendamento, selecione o botão de lixeira (excluir) na barra de ferramentas, conforme mostrado na imagem a seguir:

    ![Botão excluir na barra de ferramentas](../media/how-to-create-schedules/delete-schedule-button.png)

    Você pode usar o botão excluir para qualquer uma das datas e horários agendados no calendário e selecionar **excluir**. 
2. Na página **Excluir agendamentos**, selecione **Sim**.

    ![Confirmação de exclusão de agendamentos](../media/how-to-create-schedules/delete-schedules-confirmation.png)




## <a name="next-steps"></a>Próximas etapas
Confira os seguintes artigos:

- [Como administrador, crie e gerencie contas de laboratório](how-to-manage-lab-accounts.md)
- [Como um proprietário de laboratório, crie e gerencie laboratórios](how-to-manage-classroom-labs.md)
- [Como um proprietário de laboratório, configure e controle o uso de um laboratório](how-to-configure-student-usage.md)
- [Como um usuário de laboratório, acesse os laboratórios de sala de aula](how-to-use-classroom-lab.md)
