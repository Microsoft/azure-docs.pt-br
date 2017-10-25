---
title: Conceitos da Grade de Eventos do Azure
description: "Descreve a Grade de Eventos do Azure e seus conceitos. Define vários componentes importantes da Grade de Eventos."
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 09/18/2017
ms.author: babanisa
ms.openlocfilehash: 5b69478bf00284594b984fde452f6bed4e73859b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="concepts-in-azure-event-grid"></a>Conceitos da Grade de Eventos do Azure

Os principais conceitos da Grade de Eventos do Azure são:

## <a name="events"></a>Eventos

Um evento é a menor quantidade de informações que descreve por completo algo que aconteceu no sistema.  Todos os eventos apresentam informações comuns: origem do evento, hora em que o evento ocorreu e identificador exclusivo.  Cada evento também apresenta informações específicas que são relevantes somente para o evento em questão. Por exemplo, um evento sobre um novo arquivo que está sendo criado no Armazenamento do Microsoft Azure contém detalhes sobre o arquivo, como o valor lastTimeModified. Ou, um evento sobre a reinicialização de uma máquina virtual contém o nome da máquina virtual e o motivo da reinicialização. Cada evento é limitado a 64 KB de dados.

## <a name="event-sourcespublishers"></a>Origens/fornecedores do evento

A origem de um evento é onde o evento acontece. Por exemplo, o Armazenamento do Microsoft Azure é a origem dos eventos criados pelo blob. A Malha de VMs do Azure é a origem dos eventos de máquina virtual. As origens dos eventos são responsáveis por publicar eventos na Grade de Eventos.

## <a name="topics"></a>Tópicos

Os fornecedores categorizam eventos em tópicos. O tópico inclui um ponto de extremidade em que o fornecedor envia eventos. Para reagir a determinados tipos de evento, os assinantes decidem quais tópicos assinar. Os tópicos também fornecem um esquema de evento para que os assinantes saibam como consumir os eventos adequadamente.

Os tópicos do sistema são tópicos internos fornecidos pelos serviços do Azure. Os tópicos personalizados são tópicos de aplicativo e de terceiros.

## <a name="event-subscriptions"></a>Assinaturas de evento

Uma assinatura orienta a Grade de Eventos sobre quais eventos em um tópico um assinante está interessado em receber.  Uma assinatura também contém informações sobre como os eventos devem ser fornecidos ao assinante.

## <a name="event-handlers"></a>Manipuladores de eventos

Sob a perspectiva de uma Grade de Eventos, um manipulador de eventos é o local em que o evento é enviado. O manipulador usa alguma ação adicional para processar o evento.  A Grade de Eventos dá suporte a vários tipos de assinante. Dependendo do tipo de assinante, a Grade de Eventos segue diferentes mecanismos para garantir a entrega do evento.  Para manipuladores de eventos de webhook HTTP, o evento é repetido até que o manipulador retorne um código de status de `200 – OK`. Na Fila de Armazenamento do Microsoft Azure, os eventos são repetidos até que o serviço Fila possa processar com êxito o push de mensagens na fila.

## <a name="filters"></a>Filtros

Ao assinar um tópico, você pode filtrar os eventos que são enviados ao ponto de extremidade. É possível filtrar por tipo de evento ou padrão de assunto. Para saber mais, confira [Esquema de assinatura da Grade de Eventos](subscription-creation-schema.md).

## <a name="security"></a>Segurança

O evento proporciona segurança na assinatura em tópicos e na publicação de tópicos. Ao fazer a assinatura, você deve ter permissões adequadas para o recurso ou tópico. Ao publicar, você deve ter um token SAS ou autenticação de chave para o tópico. Para saber mais, confira [Event Grid security and authentication](security-authentication.md) (Segurança e autenticação da Grade de Eventos).

## <a name="failed-delivery"></a>Falha na entrega

Quando a Grade de Eventos não puder confirmar que um evento foi recebido pelo ponto de extremidade do assinante, ela repetirá a entrega do evento. Para saber mais, confira [Event Grid message delivery and retry](delivery-and-retry.md) (Entrega e repetição de mensagens da Grade de Eventos).

## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos, confira [About Event Grid](overview.md) (Sobre a Grade de Eventos).
* Para começar a usar rapidamente a Grade de Eventos, confira [Create and route custom events with Azure Event Grid](custom-event-quickstart.md) (Criar e rotear eventos personalizados com a Grade de Eventos do Azure).