---
title: incluir arquivo
description: incluir arquivo
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 03/08/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: cd4d5de5d93e4aea862aaabd10fb5d3c6d45cb1c
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102511038"
---
## <a name="trusted-microsoft-services"></a>Serviços Microsoft confiáveis
Quando você habilita a opção **permitir que os serviços confiáveis da Microsoft ignorem essa** configuração de firewall, os serviços a seguir recebem acesso aos recursos do barramento de serviço.

| Serviço confiável | Cenários de uso com suporte | 
| --------------- | ------------------------- | 
| Grade de Eventos do Azure | Permite que a grade de eventos do Azure envie eventos para filas ou tópicos em seu namespace do barramento de serviço. Você também precisa executar as seguintes etapas: <ul><li>Habilitar a identidade atribuída pelo sistema para um tópico ou um domínio</li><li>Adicionar a identidade à função de remetente de dados do barramento de serviço do Azure no namespace do barramento de serviço</li><li>Em seguida, configure a assinatura de evento que usa uma fila ou um tópico do barramento de serviço como um ponto de extremidade para usar a identidade atribuída pelo sistema.</li></ul> <p>Para obter mais informações, consulte [entrega de eventos com uma identidade gerenciada](../articles/event-grid/managed-service-identity.md)</p>|
| Gerenciamento de API do Azure | <p>O serviço de gerenciamento de API permite que você envie mensagens para uma fila/tópico do barramento de serviço em seu namespace do barramento de serviço.</p><ul><li>Você pode disparar fluxos de trabalho personalizados enviando mensagens para a fila/tópico do barramento de serviço quando uma API é chamada usando a [política de solicitação de envio](../articles/api-management/api-management-sample-send-request.md).</li><li>Você também pode tratar uma fila/tópico do barramento de serviço como seu back-end em uma API. Para obter uma política de exemplo, consulte [autenticar usando uma identidade gerenciada para acessar uma fila ou um tópico do barramento de serviço](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Authenticate%20using%20Managed%20Identity%20to%20access%20Service%20Bus.xml). Você também precisa executar as seguintes etapas:<ol><li>Habilite a identidade atribuída pelo sistema na instância de gerenciamento de API. Para obter instruções, consulte [usar identidades gerenciadas no gerenciamento de API do Azure](../articles/api-management/api-management-howto-use-managed-service-identity.md).</li><li>Adicionar a identidade à função de **remetente de dados do barramento de serviço do Azure** no namespace do barramento de serviço</li></ol></li></ul> | 