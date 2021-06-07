---
title: Criar um hub de notificação do Azure usando o portal do Azure | Microsoft Docs
description: Neste tutorial, você aprenderá a criar um hub de notificação do Azure usando o portal do Azure.
services: notification-hubs
author: sethmanheim
ms.author: sethm
manager: femila
ms.reviewer: thsomasu
ms.date: 08/04/2020
ms.lastreviewed: 02/14/2019
ms.topic: quickstart
ms.service: notification-hubs
ms.workload: mobile
ms.custom:
- mode-portal
ms.openlocfilehash: 4381c9259788982832a02ecbe2c87c6f6e723772
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533593"
---
# <a name="quickstart-create-an-azure-notification-hub-in-the-azure-portal"></a>Início Rápido: Criar um hub de notificação do Azure no portal do Azure

Os Hubs de Notificação do Azure fornecem um mecanismo de push expansível e fácil de usar que permite que você envie notificações para qualquer plataforma (iOS, Android, Windows, Kindle, Baidu etc.) de qualquer back-end (nuvem ou local). Para obter mais informações sobre o serviço, confira [O que são os Hubs de Notificação do Azure?](notification-hubs-push-notification-overview.md).

Neste Início Rápido, você criará um hub de notificação no portal do Azure. A primeira seção fornece etapas para criar um namespace dos Hubs de Notificação e um hub nesse namespace. A segunda seção fornece etapas para criar um hub de notificação em um namespace existente dos Hubs de Notificação.

## <a name="create-a-namespace-and-a-notification-hub"></a>Criar um namespace e um hub de notificação

Nesta seção, você criará um namespace e um hub no namespace.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

## <a name="create-a-notification-hub-in-an-existing-namespace"></a>Criar um hub de notificação em um namespace existente

Nesta seção, você criará um hub de notificação em um namespace existente.

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** no menu esquerdo, pesquise **Hub de Notificação**, selecione **estrela** (`*`) ao lado dos **Namespaces do Hub de Notificação** para adicioná-lo à seção **FAVORITOS** no menu esquerdo. Selecione **Namespaces do Hub de Notificação**.

      ![Portal do Azure – selecionar Namespaces do Hub de Notificação](./media/create-notification-hub-portal/select-notification-hub-namespaces-all-services.png)
3. Na página **Namespaces do Hub de Notificação**, selecione o namespace na lista.

      ![Selecionar o namespace na lista](./media/create-notification-hub-portal/select-namespace.png)
4. Na página **Namespace do Hub de Notificação**, selecione **Adicionar Hub** na barra de ferramentas.

      ![Namespaces do Hub de Notificação – botão Adicionar Hub](./media/create-notification-hub-portal/add-hub-button.png)
5. Na página **Novo Hub de Notificação**, insira um nome para o hub de notificação e selecione **OK**.

      ![Página Novo Hub de Notificação -> insira um nome para o hub](./media/create-notification-hub-portal/new-notification-hub-page.png)
6. Selecione **Notificações** (ícone de sino) na parte superior para ver o status da implantação do novo hub. Selecione **X** no canto direito para fechar a janela de notificação.

      ![Notificação de implantação](./media/create-notification-hub-portal/deployment-notification.png)
7. Atualize a página da Web **Namespaces do Hub de Notificação** para ver o novo hub na lista.

      ![Captura de tela que mostra a página da Web Namespaces do Hub de Notificações para ver o novo hub na lista.](./media/create-notification-hub-portal/new-hub-in-list.png)
8. Selecione o **hub de notificação** para ver a home page dele.

      ![Captura de tela que mostra a página inicial do hub de notificações.](./media/create-notification-hub-portal/hub-home-page.png)

## <a name="next-steps"></a>Próximas etapas

Neste Início Rápido, você criou um hub de notificação. Para saber como definir o hub com as configurações do PNS (Sistema de Notificação de Plataforma), confira [Definir um hub de notificação com as configurações do PNS](configure-notification-hub-portal-pns-settings.md).
