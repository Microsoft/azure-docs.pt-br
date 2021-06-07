---
title: Enviar notificações de VOIP do APNS com os hubs de notificação do Azure
description: Saiba como enviar notificações de VOIP do APNS por meio de hubs de notificação do Azure (sem suporte oficial).
author: sethmanheim
ms.author: sethm
ms.date: 3/23/2020
ms.topic: how-to
ms.service: notification-hubs
ms.openlocfilehash: c99af881b8f93b75633741c2352dc5df17dd2963
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "80146882"
---
# <a name="use-apns-voip-through-notification-hubs-not-officially-supported"></a>Usar o VOIP do APNS por meio de hubs de notificação (sem suporte oficial)

É possível usar as notificações de VOIP do APNS por meio dos hubs de notificação do Azure; no entanto, não há nenhum suporte oficial para esse cenário.

## <a name="considerations"></a>Considerações

Se você ainda optar por enviar notificações VOIP de APNS por meio de hubs de notificação, esteja ciente das seguintes limitações:

- O envio de uma notificação VOIP requer que o `apns-topic` cabeçalho seja definido como a ID do pacote de aplicativos + o `.voip` sufixo. Por exemplo, para um aplicativo de exemplo com a ID do pacote `com.microsoft.nhubsample` , o `apns-topic` cabeçalho deve ser definido como `com.microsoft.nhubsample.voip.`

   Esse método não funciona bem com os hubs de notificação do Azure, porque a ID do pacote do aplicativo deve ser configurada como parte das credenciais de APNS do Hub, e o valor não pode ser alterado. Além disso, os hubs de notificação não permitem que o valor do `apns-topic` cabeçalho seja substituído em tempo de execução.

   Para enviar notificações VOIP, você deve configurar um hub de notificação separado com a `.voip` ID do pacote de aplicativo.

- O envio de uma notificação VOIP requer que o `apns-push-type` cabeçalho seja definido como o valor `voip` .

   Para ajudar os clientes com a transição para o iOS 13, os hubs de notificação tentam inferir o valor correto para o `apns-push-type` cabeçalho. A lógica de inferência é intencionalmente simples, em um esforço para evitar a interrupção de notificações padrão. Infelizmente, esse método causa problemas com notificações de VOIP, pois a Apple trata as notificações de VOIP como um caso especial que não segue as mesmas regras que as notificações padrão.

   Para enviar notificações VOIP, você deve especificar um valor explícito para o `apns-push-type` cabeçalho.

- Os hubs de notificação limitam as cargas de APNS a 4 KB, conforme documentado pela Apple. Para notificações de VOIP, a Apple permite cargas de até 5 KB. Os hubs de notificação não diferenciam as notificações padrão e VOIP; Portanto, todas as notificações são limitadas a 4 KB.

   Para enviar notificações VOIP, você não deve exceder o limite de tamanho de carga de 4 KB.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes links:

- [Documentação para `apns-topic` `apns-push-type` cabeçalhos e valores, incluindo os casos especiais de notificações de VoIP](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns).

- [Documentação para o limite de tamanho da carga](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification).

- [Atualizações de hubs de notificação para IOS 13](push-notification-updates-ios-13.md#apns-push-type).
