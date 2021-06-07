---
title: Notificações nos Serviços de Comunicação do Azure
titleSuffix: An Azure Communication Services concept document
description: Enviar notificações para usuários de aplicativos criados nos Serviços de Comunicação do Azure.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e1b7e091fe09bc2c093cc84473bd07917347f26d
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220778"
---
# <a name="communication-services-notifications"></a>Notificações de Serviços de Comunicação

Os SDKs de chat e chamada dos Serviços de Comunicação do Azure criam um canal de mensagens em tempo real que permite a sinalização de mensagens para serem enviadas por push de maneira eficiente e confiável a clientes conectados. Com isso, você pode criar funcionalidades de comunicação avançadas e em tempo real em seus aplicativos sem implementar uma lógica de sondagem HTTP complicada. No entanto, em aplicativos móveis, esse canal de sinalização permanece conectado somente quando o aplicativo está ativo em primeiro plano. Se desejar que os usuários recebam chamadas de entrada ou mensagens de chat com o aplicativo em segundo plano, você deverá usar notificações por push.

As notificações por push permitem que você envie informações do aplicativo para os dispositivos móveis dos usuários. Você pode usar notificações por push para mostrar uma caixa de diálogo, tocar um som ou exibir a interface do usuário de chamada de entrada. Os Serviços de Comunicação do Azure fornecem integrações com a [Grade de Eventos do Azure](../../event-grid/overview.md) e os [Hubs de Notificação do Azure](../../notification-hubs/notification-hubs-push-notification-overview.md), que permitem adicionar notificações por push aos aplicativos.

## <a name="trigger-push-notifications-via-azure-event-grid"></a>Disparar notificações por push usando a Grade de Eventos do Azure

Os Serviços de Comunicação do Azure integram-se à [Grade de Eventos do Azure](https://azure.microsoft.com/services/event-grid/) para fornecer notificações de eventos em tempo real de maneira confiável, escalonável e segura. Você pode aproveitar essa integração para criar um serviço de notificação que fornece notificações por push móveis para os usuários criando uma assinatura de grade de eventos que dispara uma [Função do Azure](../../azure-functions/functions-overview.md) ou webhook.

:::image type="content" source="./media/notifications/acs-events-int.png" alt-text="Diagrama mostrando como os Serviços de Comunicação integram-se à Grade de Eventos.":::

Saiba mais sobre o [processamento de eventos nos Serviços de Comunicação do Azure](https://docs.microsoft.com/azure/event-grid/event-schema-communication-services).

## <a name="deliver-push-notifications-via-azure-notification-hubs"></a>Entregar notificações por push pelos Hubs de Notificação do Azure

Você pode conectar um Hub de Notificação do Azure ao seu recurso de serviços de comunicação para enviar notificações por push automaticamente ao dispositivo móvel de um usuário quando ele receber uma chamada de entrada. Use essas notificações por push para ativar o aplicativo em segundo plano e exibir a interface do usuário que permite aos usuários aceitar ou recusar a chamada.

:::image type="content" source="./media/notifications/acs-anh-int.png" alt-text="Diagrama mostrando como os serviços de comunicação integram-se aos Hubs de Notificação do Azure.":::

Os Serviços de Comunicação usam o Hub de Notificação do Azure como um serviço de passagem para comunicar-se com os vários serviços de notificação por push específicos da plataforma usando a API de [Envio Direto](/rest/api/notificationhubs/direct-send). Isso permite reutilizar os recursos e as configurações do hub de notificação do Azure para fornecer notificações de chamada de baixa latência e confiáveis aos seus aplicativos.

> [!NOTE]
> No momento, há suporte apenas para notificações por push de chamada.

### <a name="notification-hub-provisioning"></a>Provisionamento do Hub de Notificação

Para entregar notificações por push a dispositivos cliente usando Hubs de Notificação, [crie um Hub de Notificação](../../notification-hubs/create-notification-hub-portal.md) na mesma assinatura que o recurso de Serviços de Comunicação. Você deve configurar o Hub de Notificações do Azure para o Sistema de Notificação de Plataforma que deseja usar. Para saber como obter notificações por push em seu aplicativo cliente desde Hubs de Notificação, veja [Introdução aos Hubs de Notificação](../../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md) e selecione a plataforma de cliente de destino na lista suspensa na parte superior da página.

> [!NOTE]
> No momento, há suporte para APNs e plataformas FCM.
A plataforma de APNs precisa ser configurada com o modo de autenticação de token. De agora em diante, o modo de autenticação de certificado não é mais compatível.

Depois que o hub de notificações estiver configurado, você poderá associá-lo ao recurso de Serviços de Comunicação fornecendo uma cadeia de conexão para o hub usando o Cliente do Azure Resource Manager ou por meio do portal do Azure. A cadeia de conexão deve conter permissões para `Send`. É recomendável criar outra política de acesso com permissões para `Send` apenas para o hub. Saiba mais sobre [políticas de acesso e segurança dos Hubs de Notificação](../../notification-hubs/notification-hubs-push-notification-security.md)

#### <a name="using-the-azure-resource-manager-client-to-link-your-notification-hub"></a>Como usar o cliente do Azure Resource Manager para vincular o Hub de Notificações

Para fazer logon no Azure Resource Manager, execute o comando a seguir e entre usando suas credenciais.

```console
armclient login
```

 Depois de fazer logon com êxito, execute o seguinte para provisionar o hub de notificação:

```console
armclient POST /subscriptions/<sub_id>/resourceGroups/<resource_group>/providers/Microsoft.Communication/CommunicationServices/<resource_id>/linkNotificationHub?api-version=2020-08-20-preview "{'connectionString': '<connection_string>','resourceId': '<resource_id>'}"
```

#### <a name="using-the-azure-portal-to-link-your-notification-hub"></a>Como usar o portal do Azure para vincular o Hub de Notificações

1. No portal, acesse o recurso dos Serviços de Comunicação do Azure.

1. Dentro do recurso dos Serviços de Comunicação, selecione **Notificações por Push** no menu à esquerda da página Serviços de Comunicação e conecte o Hub de Notificações provisionado antes.

1. Selecione **Conectar hub de notificações**. Você verá uma lista de hubs de notificação disponíveis para conexão.
 
1. Selecione o hub de notificações que você deseja usar para este recurso.
 
   - Se você precisar criar um hub, selecione **Criar hub de notificações** para obter um novo hub provisionado para este recurso.

   :::image type="content" source="./media/notifications/acs-anh-portal-int.png" alt-text="Captura de tela mostrando as configurações de notificações por push no portal do Azure.":::

Agora você verá o hub de notificações que você vinculou ao estado conectado.

Se você quiser usar um hub diferente para o recurso, selecione **Desconectar** e repita as etapas para vincular o hub de notificações diferente.

> [!NOTE]
> Qualquer alteração na maneira como o hub é vinculado é refletida no plano de dados (ou seja, ao enviar uma notificação) com o período máximo de 10 minutos. Esse mesmo comportamento se aplica quando o Hub é vinculado pela primeira vez, **se** as notificações foram enviadas antes da alteração.

### <a name="device-registration"></a>Registro de dispositivos

Confira o [guia de início rápido de chamada de voz](../quickstarts/voice-video-calling/getting-started-with-calling.md) para saber como registrar seu identificador de dispositivo com os Serviços de Comunicação.

### <a name="troubleshooting-guide-for-push-notifications"></a>Guia de solução de problemas de notificações por push

Quando você não vir notificações por push no dispositivo, haverá três locais em que as notificações poderão ter sido removidas:

- Os Hubs de Notificação do Azure não aceitaram a notificação dos Serviços de Comunicação do Azure
- O Sistema de Notificação de Plataforma (por exemplo, APNs e FCM) não aceitou a notificação dos Hubs de Notificação do Azure
- O Sistema de Notificação de Plataforma não entregou a notificação ao dispositivo.

O primeiro local em que uma notificação pode ser removida (os Hubs de Notificação do Azure não aceitaram as notificações dos Serviços de Comunicação do Azure) é abordado abaixo. Para os outros dois locais, confira [Diagnosticar notificações removidas nos Hubs de Notificação do Azure](../../notification-hubs/notification-hubs-push-notification-fixer.md).

Uma forma de ver se o recurso de serviços de comunicação envia notificações para os Hubs de Notificação do Azure é examinar a métrica `incoming messages` das [métricas do hub de notificações do Azure](../../azure-monitor/essentials/metrics-supported.md#microsoftnotificationhubsnamespacesnotificationhubs) vinculadas.

A seguir estão algumas configurações incorretas comuns que podem ser a causa pela qual o Hub de Notificações do Azure não aceita as notificações do recurso de Serviços de Comunicação.

#### <a name="azure-notification-hub-not-linked-to-the-communication-services-resource"></a>Hub de Notificações do Azure não vinculado ao recurso de Serviços de Comunicação

Pode haver o caso em que você não vinculou o Hub de Notificações do Azure ao recurso de Serviços de Comunicação. Você pode examinar a [Seção de provisionamento do Hub de Notificações](#notification-hub-provisioning) para ver como vincular.

#### <a name="the-linked-azure-notification-hub-isnt-configured"></a>O Hub de Notificações do Azure vinculado não está configurado

Você precisa configurar o Hub de Notificações vinculado com as credenciais do Sistema de Notificação de Plataforma da plataforma (por exemplo, iOS ou Android) que deseja usar. Para obter mais detalhes sobre como isso pode ser feito, você pode examinar [Configurar notificações por push em um hub de notificações](../../notification-hubs/configure-notification-hub-portal-pns-settings.md).

#### <a name="the-linked-azure-notification-hub-doesnt-exist"></a>O Hub de Notificações do Azure vinculado não existe

O Hub de Notificações do Azure vinculado ao recurso de Serviços de Comunicação não existe mais. Verifique se o Hub de Notificações vinculado ainda existe.

#### <a name="the-azure-notification-hub-apns-platform-is-configured-with-certificate-authentication-mode"></a>A plataforma APNs do Hub de Notificações do Azure está configurada com o modo de autenticação de certificado

Caso você queira usar a plataforma APNs com o modo de autenticação de certificado, não há suporte para isso no momento. Você deve configurar a plataforma APNs com o modo de autenticação de token, conforme especificado em [Configurar notificações por push em um hub de notificações](../../notification-hubs/configure-notification-hub-portal-pns-settings.md).

#### <a name="the-linked-connection-string-doesnt-have-send-permission"></a>A cadeia de conexão vinculada não tem a permissão `Send`

A cadeia de conexão que você usou para vincular o Hub de Notificações ao recurso de Serviços de Comunicação precisa ter a permissão `Send`. Para obter mais detalhes sobre como criar uma cadeia de conexão ou ver a cadeia de conexão atual do Hub de Notificações do Azure, você pode examinar [Políticas de acesso e segurança dos Hubs de Notificação](../../notification-hubs/notification-hubs-push-notification-security.md)

#### <a name="the-linked-connection-string-or-azure-notification-hub-resourceid-arent-valid"></a>A cadeia de conexão vinculada ou a ID de recurso do Hub de Notificações do Azure não é válida

Configure o recurso de Serviços de Comunicação com a cadeia de conexão correta e a ID de recurso apropriada do hub de notificações do Azure

#### <a name="the-linked-connection-string-is-regenerated"></a>A cadeia de conexão vinculada é regenerada

Caso você tenha regenerado a cadeia de conexão do Hub de Notificações do Azure vinculado, será necessário atualizar com a nova no recurso de Serviços de Comunicação [vinculando novamente o Hub de Notificações](#notification-hub-provisioning).

## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos do Azure, confira [O que é uma Grade de eventos?](../../event-grid/overview.md)
* Para saber mais sobre os conceitos do Hub de Notificação do Azure, confira a [documentação dos Hubs de Notificação do Azure](../../notification-hubs/index.yml)
