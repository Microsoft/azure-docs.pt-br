---
title: Alterar tipo de preço do namespace de Hubs de Notificação | Microsoft Docs
description: Saiba como alterar o tipo de preço de um namespace de hubs de notificação do Azure.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 08/03/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/28/2019
ms.openlocfilehash: 1455259bc42aea9d506a9a2a19d725cac3d643f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87562762"
---
# <a name="change-pricing-tier-of-an-azure-notification-hubs-namespace"></a>Alterar tipo de preço de um namespace de hubs de notificações do Microsoft Azure

Os Hubs de Notificação é oferecido em três tipos: **gratuito**, **básico** e **standard**. Este artigo mostra como alterar o tipo de preço de um namespace de Hubs de Notificação do Microsoft Azure.

## <a name="overview"></a>Visão geral

Nos hubs de notificação do Azure, um *Hub de notificação* é o menor recurso/entidade. Em geral, ele é mapeado para um aplicativo e pode conter um certificado para cada Sistema de Notificação de Plataforma (PNS) que damos suporte para o aplicativo. O aplicativo pode ser um aplicativo híbrido ou nativo e de plataforma cruzada.

Um *namespace* é uma coleção de hubs de notificações. Geralmente, cada namespace é composto por hubs relacionados e usados para um fim específico. Por exemplo, você pode ter três namespaces diferentes para fins de desenvolvimento, teste e produção, respectivamente.

Você pode associar um namespace com os tipos de preço **gratuito**, **básico**ou **padrão** . Para qualquer namespace, você pode usar a camada que atenda às suas necessidades. As seções a seguir mostram como alterar o tipo de preço de um namespace de Hubs de Notificação.

## <a name="use-azure-portal"></a>Usar o portal do Azure

Ao usar o portal do Azure, você pode alterar o tipo de preço de um namespace na página de namespace ou em uma página de Hub. Quando você o altera em uma página de hub, você realmente o altera no nível do namespace. Ele altera o tipo de preço do namespace e todos os hubs no namespace.

### <a name="change-tier-on-the-namespace-page"></a>Alterar a camada na página do namespace

O procedimento a seguir mostra como alterar o tipo de preço para um namespace na página namespace. Quando você altera a camada de um namespace, ela se aplica a todos os hubs nesse namespace.

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione **todos os serviços** no menu à esquerda.
3. Selecione **Namespaces do Hub de Notificação** na seção **Internet das Coisas**. Se você selecionar o asterisco ( `*` ) ao lado do texto, ele será adicionado à barra de navegação à esquerda em **favoritos**. Isso ajuda a acessar a página de namespaces mais rápido na próxima vez. Depois de adicioná-lo aos **favoritos**, selecione **namespaces do hub de notificação**.

    ![Todos os serviços -> Namespaces do Hub de Notificação](./media/change-pricing-tier/all-services-nhub.png)

4. Na página **Namespaces do Hub de Notificação**, selecione o namespace para o qual você deseja alterar o tipo de preço.
5. Na página **Namespace do Hub de Notificação** do namespace, é possível ver o tipo de preço atual dele na seção **Essentials**. Na imagem a seguir, é possível ver que o tipo de preço do namespace é **Gratuito**.

    ![Tipo de preço atual na página do namespace](./media/change-pricing-tier/pricing-tier-before.png)

6. Na página **namespace do hub de notificação** para seu namespace, selecione **tipo de preço** na seção **gerenciar** .

    ![Selecionar o tipo de preço na página do namespace](./media/change-pricing-tier/namespace-select-pricing-menu.png)

7. Altere o tipo de preço e, em seguida, clique no botão **selecionar** .
8. Você pode ver o status da ação de alteração de camada nos **alertas**.
9. Alterne para a página **Visão geral**. Confirme que a nova camada é mostrada no campo **Tipo de Preço** na seção **Essentials**.
10. Esta etapa é opcional. Selecione qualquer hub no namespace. Confirme que você vê o mesmo tipo de preço na seção **Essentials**. Você deve ver o mesmo tipo de preço para todos os hubs no namespace.

### <a name="change-tier-on-the-hub-page"></a>Alterar o tipo na página do hub

Siga estas etapas para alterar o tipo de preço de um namespace na página de Hub. Embora você execute essas etapas a partir da página de Hub, você realmente altera o tipo de preço para o namespace e todos os hubs no namespace:

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione **todos os serviços** no menu à esquerda.
3. Selecione **Hubs de Notificação** na seção **Internet das Coisas**.
4. Selecione o **hub** de notificação.
5. Selecione **Tipo de Preço** no menu esquerdo.
6. Altere o tipo de preço e clique no botão **Selecionar**. Esta ação altera a configuração do tipo de preço do namespace que contém o hub. Assim, você verá o novo tipo de preço na página do namespace e todas as páginas do hub.

> [!NOTE]
> Todas as alterações de tipo de preço entram em vigor imediatamente.

## <a name="use-rest-api"></a>Usar a API REST

É possível usar as seguintes APIs REST do Provedor de Recursos para obter o tipo de preço atual e atualizá-lo.

### <a name="get-current-pricing-tier-for-a-namespace"></a>Obter o tipo de preço atual para um namespace

Para obter a camada de namespace atual, envie um comando GET, conforme mostrado no exemplo a seguir:

```REST
GET: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
```

### <a name="update-pricing-tier-for-a-namespace"></a>Atualizar tipo de preço para um namespace

Para atualizar a camada de namespace, envie um comando PUT, conforme mostrado no exemplo a seguir:

```REST
PUT: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
Body: <NotificationHubPlan xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"><SKU>Standard</SKU></NotificationHubPlan>
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre esses tipos e preços, confira [Notification Hubs pricing](https://azure.microsoft.com/pricing/details/notification-hubs/) (Preços dos Hubs de Notificação).
