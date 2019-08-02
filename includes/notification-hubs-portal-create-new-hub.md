---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: notification-hubs
author: jwargo
ms.service: notification-hubs
ms.topic: include
ms.date: 01/17/2019
ms.author: jowargo
ms.custom: include file
ms.openlocfilehash: 5afcc8e4524a0e8353766ba239d5ab9161b29d86
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509128"
---
1. Entre no [Portal do Azure](https://portal.azure.com).

1. Selecione **Todos os serviços** no menu à esquerda e **Hubs de Notificação** na seção **Móvel**. Selecione o ícone de estrela ao lado do nome do serviço para adicionar o serviço na seção **FAVORITOS** no menu à esquerda. Depois de adicionar os **Hubs de Notificação** a **FAVORITOS**, selecione-o no menu à esquerda.

      ![Portal do Azure – selecionar Hubs de Notificação](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)

1. Na página **Hubs de Notificação**, selecione **Adicionar** na barra de ferramentas.

      ![Hubs de Notificação – adicionar botão de barra de ferramentas](./media/notification-hubs-portal-create-new-hub/add-toolbar-button.png)

1. Na página **Hub de Notificação**, execute as seguintes etapas:

    1. Insira um nome em **Hub de Notificação**.  

    1. Insira um nome em **Criar um namespace**. Um namespace contém um ou mais hubs.

    1. Selecione um valor da caixa de listagem suspensa **Local**. Esse valor especifica a localização na qual você deseja criar o hub.

    1. Selecione grupo de recursos existente em **Grupo de Recursos** ou crie um nome para um novo grupo de recursos.

    1. Selecione **Criar**.

        ![Portal do Azure - definir propriedades do hub de notificação](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

1. Selecione **Notificações** (ícone de sino) e selecione **Acessar recurso**. Atualize também a lista na página **Hubs de Notificação** e, em seguida, selecione seu hub.

      ![Portal do Azure - notificações -> Acessar recurso](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)

1. Selecione **Políticas de Acesso** na lista. Observe que as cadeias de caracteres de duas conexão estão disponíveis para você. Você precisará delas para manipular notificações por push mais tarde.

      >[!IMPORTANT]
      >*Não* use a política **DefaultFullSharedAccessSignature** em seu aplicativo. Ela é destinada a ser usada apenas em seu back-end.
      >

      ![Portal do Azure - cadeias de conexão do hub de notificação](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
