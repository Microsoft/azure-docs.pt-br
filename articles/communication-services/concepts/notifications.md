---
title: Notificações nos Serviços de Comunicação do Azure
titleSuffix: An Azure Communication Services concept document
description: Enviar notificações para usuários de aplicativos criados nos Serviços de Comunicação do Azure.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: a52188dc5058dbc74d3b03fba860b98540cd4a41
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608484"
---
# <a name="communication-services-notifications"></a>Notificações de Serviços de Comunicação

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

As bibliotecas de cliente de chat e chamada dos Serviços de Comunicação do Azure criam um canal de mensagens em tempo real que permite enviar mensagens de sinalização por push a clientes conectados de maneira eficiente e confiável. Com isso, você pode criar funcionalidades de comunicação avançadas e em tempo real em seus aplicativos sem implementar uma lógica de sondagem HTTP complicada. No entanto, em aplicativos móveis, esse canal de sinalização permanece conectado somente quando o aplicativo está ativo em primeiro plano. Se desejar que os usuários recebam chamadas de entrada ou mensagens de chat com o aplicativo em segundo plano, você deverá usar notificações por push.

As notificações por push permitem que você envie informações do aplicativo para os dispositivos móveis dos usuários. Você pode usar notificações por push para mostrar uma caixa de diálogo, tocar um som ou exibir a interface do usuário de chamada de entrada. Os Serviços de Comunicação do Azure fornecem integrações com a [Grade de Eventos do Azure](../../event-grid/overview.md) e os [Hubs de Notificação do Azure](../../notification-hubs/notification-hubs-push-notification-overview.md), que permitem adicionar notificações por push aos aplicativos.

## <a name="trigger-push-notifications-via-azure-event-grid"></a>Disparar notificações por push usando a Grade de Eventos do Azure

Os Serviços de Comunicação do Azure integram-se à [Grade de Eventos do Azure](https://azure.microsoft.com/services/event-grid/) para fornecer notificações de eventos em tempo real de maneira confiável, escalonável e segura. Você pode aproveitar essa integração para criar um serviço de notificação que fornece notificações por push móveis para os usuários criando uma assinatura de grade de eventos que dispara uma [Função do Azure](../../azure-functions/functions-overview.md) ou webhook.

:::image type="content" source="./media/notifications/acs-events-int.png" alt-text="Diagrama mostrando como os Serviços de Comunicação integram-se à Grade de Eventos.":::

Saiba mais sobre o [processamento de eventos nos Serviços de Comunicação do Azure](./event-handling.md).

## <a name="deliver-push-notifications-via-azure-notification-hubs"></a>Entregar notificações por push pelos Hubs de Notificação do Azure

Você pode conectar um Hub de Notificação do Azure ao seu recurso de serviços de comunicação para enviar notificações por push automaticamente ao dispositivo móvel de um usuário quando ele receber uma chamada de entrada. Use essas notificações por push para ativar seu aplicativo do segundo plano e exibir a interface do usuário que permite aos usuários aceitar ou recusar a chamada. 

:::image type="content" source="./media/notifications/acs-anh-int.png" alt-text="Diagrama mostrando como os serviços de comunicação integram-se ao Hub de Notificações do Azure.":::

Os Serviços de Comunicação usam o Hub de Notificação do Azure como um serviço de passagem para comunicar-se com os vários serviços de notificação por push específicos da plataforma usando a API de [Envio Direto](/rest/api/notificationhubs/direct-send). Isso permite reutilizar os recursos e as configurações do hub de notificação do Azure para fornecer notificações de chamada de baixa latência e confiáveis aos seus aplicativos.

> [!NOTE]
> No momento, há suporte apenas para notificações por push de chamada.

### <a name="notification-hub-provisioning"></a>Provisionamento do Hub de Notificação 

Para entregar notificações por push a dispositivos cliente usando Hubs de Notificação, [crie um Hub de Notificação](../../notification-hubs/create-notification-hub-portal.md) na mesma assinatura que o recurso de Serviços de Comunicação. Os Hubs de Notificação do Azure devem ser configurados para os Serviços de Notificações de Plataforma que você deseja usar. Para saber como obter notificações por push em seu aplicativo cliente desde Hubs de Notificação, veja [Introdução aos Hubs de Notificação](../../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md) e selecione a plataforma de cliente de destino na lista suspensa na parte superior da página.

> [!NOTE]
> No momento, há suporte para APNs e plataformas FCM.  
A plataforma de APNs precisa ser configurada com o modo de autenticação de token. O modo de autenticação de certificado não terá mais suporte a partir de agora. 

Depois que o hub de notificações estiver configurado, você poderá associá-lo ao recurso de Serviços de Comunicação fornecendo uma cadeia de conexão para o hub usando o Cliente do Azure Resource Manager ou por meio do portal do Azure. A cadeia de conexão deve conter permissões para "Enviar". É recomendável criar outra política de acesso com permissões para "Enviar" apenas para o seu hub. Saiba mais sobre [políticas de acesso e segurança dos Hubs de Notificação](../../notification-hubs/notification-hubs-push-notification-security.md)

#### <a name="using-the-azure-resource-manager-client-to-configure-the-notification-hub"></a>Como usar o cliente do Azure Resource Manager para configurar o Hub de Notificação

Para fazer logon no Azure Resource Manager, execute o comando a seguir e entre usando suas credenciais.

```console
armclient login
```

 Depois de fazer logon com êxito, execute o seguinte para provisionar o hub de notificação:

```console
armclient POST /subscriptions/<sub_id>/resourceGroups/<resource_group>/providers/Microsoft.Communication/CommunicationServices/<resource_id>/linkNotificationHub?api-version=2020-08-20-preview "{'connectionString': '<connection_string>','resourceId': '<resource_id>'}"
```

#### <a name="using-the-azure-portal-to-configure-the-notification-hub"></a>Como usar o portal do Azure para configurar o Hub de Notificação

No portal, navegue até o recurso Serviços de Comunicação do Azure. Dentro do recurso Serviços de Comunicação, selecione Notificações por Push no menu à esquerda da página Serviços de Comunicação e conecte o Hub de Notificação provisionado antes. Você precisará fornecer a cadeia de conexão e a ID do recurso aqui:

:::image type="content" source="./media/notifications/acs-anh-portal-int.png" alt-text="Captura de tela mostrando as configurações de notificações por push no portal do Azure.":::

> [!NOTE]
> Se a cadeia de conexão do Hub de Notificações do Azure for atualizada, o recurso dos Serviços de Comunicação precisará ser atualizado também.  
Qualquer alteração na maneira como o hub é vinculado será refletida no plano de dados (por exemplo, ao enviar uma notificação) com o período máximo de ``10`` minutos. Isso também é aplicável quando o hub é vinculado pela primeira vez **se** havia notificações enviadas antes.

#### <a name="device-registration"></a>Registro de dispositivos 

Confira o [guia de início rápido de chamada de voz](../quickstarts/voice-video-calling/getting-started-with-calling.md) para saber como registrar seu identificador de dispositivo com os Serviços de Comunicação.

## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos do Azure, confira [O que é uma Grade de eventos?](../../event-grid/overview.md)
* Para saber mais sobre os conceitos do Hub de Notificação do Azure, confira a [documentação dos Hubs de Notificação do Azure](../../notification-hubs/index.yml)