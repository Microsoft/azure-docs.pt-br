---
title: Conceitos da Grade de Eventos do Azure
description: Descreve a Grade de Eventos do Azure e seus conceitos. Define vários componentes importantes da Grade de Eventos.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: spelluru
ms.openlocfilehash: 1c77d0ea9e67c8d69f3f632cace164d8a0c4d921
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60562348"
---
# <a name="concepts-in-azure-event-grid"></a>Conceitos da Grade de Eventos do Azure

Este artigo descreve os principais conceitos da Grade de Eventos do Azure.

## <a name="events"></a>Eventos

Um evento é a menor quantidade de informações que descreve por completo algo que aconteceu no sistema. Todos os eventos apresentam informações comuns: origem do evento, hora em que o evento ocorreu e identificador exclusivo. Cada evento também apresenta informações específicas que são relevantes somente para o tipo de evento em questão. Por exemplo, um evento sobre um novo arquivo que está sendo criado no Armazenamento do Azure tem detalhes sobre o arquivo, como o valor `lastTimeModified`. Ou, um evento de Hubs de Eventos tem a URL do arquivo de Captura. 

Cada evento é limitado a 64 KB de dados.

Para encontrar as propriedades que são enviadas em um evento, confira [Esquema de evento da Grade de Eventos do Azure](event-schema.md).

## <a name="publishers"></a>Publicadores

Um editor é o usuário ou a organização que decide enviar eventos para a Grade de Eventos. A Microsoft publica os eventos para vários serviços do Azure. Você pode publicar eventos de seu próprio aplicativo. As organizações que hospedam serviços fora do Azure podem publicar eventos por meio da Grade de Eventos.

## <a name="event-sources"></a>Origens de eventos

A origem de um evento é onde o evento acontece. Cada origem do evento está relacionada a um ou mais tipos de evento. Por exemplo, o Armazenamento do Microsoft Azure é a origem dos eventos criados pelo blob. O Hub IoT é a origem do evento para os eventos criados pelo dispositivo. Seu aplicativo é a origem do evento para eventos personalizados que você definir. As fontes dos eventos são responsáveis por enviar eventos na Grade de Eventos.

Para obter informações sobre como implementar qualquer uma das origens de Grade de Eventos compatíveis, consulte [Origens de evento na Grade de Eventos do Azure](event-sources.md).

## <a name="topics"></a>Tópicos

O tópico da grade de eventos fornece um ponto de extremidade em que a fonte envia eventos. O editor cria o tópico de grade de eventos e decide se uma origem do evento precisa de um tópico ou mais de um tópico. Um tópico é usado para uma coleção de eventos relacionados. Para reagir a determinados tipos de evento, os assinantes decidem quais tópicos assinar.

Os tópicos do sistema são tópicos internos fornecidos pelos serviços do Azure. Você não vê tópicos do sistema na sua assinatura do Azure porque o editor é o proprietário dos tópicos, mas você pode assiná-los. Para assinar, você deve fornecer informações sobre o recurso do qual deseja receber eventos. Desde que você tem acesso ao recurso, você pode assinar seus eventos.

Os tópicos personalizados são tópicos de aplicativo e de terceiros. Quando criar ou receber acesso a um tópico personalizado, você verá o tópico personalizado na assinatura.

Ao projetar o seu aplicativo, você terá flexibilidade ao decidir sobre quantos tópicos criar. Para soluções maiores, crie um tópico personalizado para cada categoria de eventos relacionados. Por exemplo, considere um aplicativo que envia eventos relacionados à modificação de contas de usuários e ordens de processamento. É improvável que qualquer manipulador de eventos queira ambas as categorias de eventos. Crie dois tópicos personalizados e permita que os manipuladores de eventos assinem o que for interessante para eles. Para soluções pequenas, você pode preferir enviar todos os eventos para um único tópico. Assinantes de evento podem filtrar par os tipos de evento que desejam.

## <a name="event-subscriptions"></a>Assinaturas de evento

Uma assinatura informa à Grade de Eventos do Azure com eventos em um tópico você está interessado em receber. Ao criar a assinatura, você fornece um ponto de extremidade para manipular o evento. Você pode filtrar os eventos que são enviados ao ponto de extremidade. É possível filtrar por tipo de evento ou padrão de assunto. Para saber mais, confira [Esquema de assinatura da Grade de Eventos](subscription-creation-schema.md).

Para obter exemplos de criação de assinaturas, confira:

* [Exemplos da CLI do Azure para Grade de Eventos](cli-samples.md)
* [Exemplos do Azure PowerShell para a Grade de Eventos](powershell-samples.md)
* [Modelos do Azure Resource Manager para Grade de Eventos](template-samples.md)

Para obter informações sobre como obter as assinaturas de grade de eventos atuais, confira [Consultar assinaturas de Grade de Eventos](query-event-subscriptions.md).

## <a name="event-subscription-expiration"></a>Validade da assinatura de evento

A [extensão da Grade de Eventos](/cli/azure/azure-cli-extensions-list) para a CLI do Azure permite que você defina uma data de expiração ao criar uma assinatura de evento. Se você estiver usando a API REST, use `api-version=2018-09-15-preview`

A assinatura do evento é expirada automaticamente após essa data. Defina uma expiração para assinaturas de eventos que são necessárias apenas por um tempo limitado e você não quer se preocupar com a limpeza dessas assinaturas. Por exemplo, ao criar uma assinatura de evento para testar um cenário, você pode querer definir uma expiração. 

Para um exemplo de configuração de expiração, consulte [Inscrever-se com filtros avançados](how-to-filter-events.md#subscribe-with-advanced-filters).

## <a name="event-handlers"></a>Manipuladores de eventos

Sob a perspectiva de uma Grade de Eventos, um manipulador de eventos é o local em que o evento é enviado. O manipulador usa alguma ação adicional para processar o evento. Grade de eventos do Azure dá suporte a vários tipos de manipulador. Você pode usar um serviço do Azure compatível ou seu próprio webhook como o manipulador. Dependendo do tipo de manipulador, a Grade de Eventos segue diferentes mecanismos para assegurar a entrega do evento. Para manipuladores de eventos de webhook HTTP, o evento é repetido até que o manipulador retorne um código de status de `200 – OK`. Para o Armazenamento do Microsoft Azure Queue, os eventos são repetidos até que o serviço de fila processe com êxito o envio de mensagens para a fila.

Para obter informações sobre como implementar qualquer um dos manipuladores de Grade de Eventos compatíveis, consulte [Manipuladores de evento na Grade de Eventos do Azure](event-handlers.md).

## <a name="security"></a>Segurança

A Grade de Eventos proporciona segurança na assinatura em tópicos e na publicação de tópicos. Ao fazer a assinatura, você deve ter permissões adequadas para o recurso ou tópico da grade de eventos. Ao publicar, você deve ter um token SAS ou autenticação de chave para o tópico. Para saber mais, confira [Event Grid security and authentication](security-authentication.md) (Segurança e autenticação da Grade de Eventos).

## <a name="event-delivery"></a>Entrega de eventos

Se a Grade de Eventos não puder confirmar que um evento foi recebido pelo ponto de extremidade do assinante, ela repetirá a entrega do evento. Para saber mais, confira [Event Grid message delivery and retry](delivery-and-retry.md) (Entrega e repetição de mensagens da Grade de Eventos).

## <a name="batching"></a>Envio em lote

Ao usar um tópico personalizado, os eventos sempre devem ser publicados em uma matriz. Isso pode ser um lote de um para cenários de baixo rendimento, no entanto, para casos de uso de alto volume, é recomendável agrupar vários eventos juntos por publicação para obter maior eficiência. Lotes podem ter até 1 MB. Cada evento ainda deve ser maior que 64 KB não.

## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos, confira [About Event Grid](overview.md) (Sobre a Grade de Eventos).
* Para começar a usar rapidamente a Grade de Eventos, confira [Create and route custom events with Azure Event Grid](custom-event-quickstart.md) (Criar e rotear eventos personalizados com a Grade de Eventos do Azure).
