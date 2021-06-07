---
title: Monitoramento e gerenciamento de sessão do Azure Bastion | Microsoft Docs
description: Neste artigo, saiba como selecionar uma sessão em andamento e forçar a sua desconexão ou exclusão.
services: bastion
author: charwen
ms.service: bastion
ms.topic: how-to
ms.date: 05/21/2020
ms.author: charwen
ms.openlocfilehash: beb4f8fc5b9e22bc3e2bdd4732c8321dfcd70780
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90980733"
---
# <a name="session-monitoring-and-management-for-azure-bastion"></a>Monitoramento e gerenciamento de sessão para Azure Bastion

Depois que o serviço Bastion for provisionado e implantado na sua rede virtual, você poderá usá-lo para se conectar diretamente a qualquer VM nessa rede virtual. À medida que os usuários se conectam a cargas de trabalho, o Azure Bastion pode ser usado para monitorar as sessões remotas e executar ações de gerenciamento rápido. O monitoramento de sessão do Azure Bastion permite exibir quais usuários estão conectados a quais VMs. Ele mostra o IP em que o usuário se conectou e o tempo e o horário da conexão. A experiência de gerenciamento de sessão permite selecionar uma sessão em andamento e forçar a desconexão ou exclusão de uma sessão para desconectar o usuário da sessão em andamento.

## <a name="monitor-remote-sessions"></a><a name="monitor"></a>Monitorar sessões remotas

1. No [portal do Azure](https://portal.azure.com), navegue para o recurso do Azure Bastion e selecione **Sessões** na página do Azure Bastion.

   ![Captura de tela mostra as configurações do menu portal do Azure com sessões selecionadas.](./media/session-monitoring/sessions.png)
2. Na página **Sessões**, você pode ver as sessões remotas em andamento no lado direito.

   ![exibir sessão](./media/session-monitoring/view-session.png)
3. Selecione **Atualizar** para ver a lista atualizada de sessões remotas. Ao selecionar Atualizar, o Azure Bastion buscará as informações de monitoramento mais recentes e atualizará esses dados no portal.

   ![Captura de tela mostra uma sessão que usa a bastiões do Azure com a opção de atualização realçada.](./media/session-monitoring/refresh.png)


## <a name="delete-or-force-disconnect-an-ongoing-remote-session"></a><a name="view"></a>Excluir ou forçar a desconexão em uma sessão remota em andamento

Você pode selecionar um conjunto de sessões e forçar a desconexão. As etapas a seguir mostram como excluir sessões remotas:

1. Navegue para seu recurso Azure Bastion e selecione **Sessões** na página do Azure Bastion.

   ![Captura de tela mostra a portal do Azure com sessões selecionadas em configurações.](./media/session-monitoring/navigate.png)
2. Depois de selecionar “Sessões”, você verá uma lista das sessões remotas.

   ![listar sessões](./media/session-monitoring/list.png)
3. Selecione uma sessão remota específica e, em seguida, selecione as reticências na extremidade direita da linha da sessão e, em seguida, selecione **Excluir**.

   ![Captura de tela mostra a portal do Azure exibir uma sessão com o ícone Excluir selecionado.](./media/session-monitoring/delete.png)
4. Quando você seleciona “Excluir”, a sessão remota é desconectada e o usuário recebe a mensagem "Você foi desconectado" na sessão remota.

   ![Captura de tela mostra uma mensagem verificando se você foi desconectado.](./media/session-monitoring/disconnect.png)

## <a name="next-steps"></a>Próximas etapas

Leia as [perguntas frequentes do Bastion](bastion-faq.md).
