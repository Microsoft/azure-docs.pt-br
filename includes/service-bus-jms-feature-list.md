---
title: arquivo de inclusão
description: arquivo de inclusão
services: service-bus-messaging
author: axisc
ms.service: service-bus-messaging
ms.topic: include
ms.date: 6/9/2020
ms.author: aschhab
ms.custom: include file
ms.openlocfilehash: 9030080d0b8c8e032cb2992a62275efcdb04aabc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87798130"
---
A tabela a seguir lista os recursos do Java Message Service (JMS) que o barramento de serviço do Azure suporta atualmente. Ele também mostra recursos sem suporte.


| Recurso | API |Status |
|---|---|---|
| Filas   | <ul> <li> JMSContext. createQueue (String QueueName) </li> </ul>| **Com suporte** |
| Tópicos   | <ul> <li> JMSContext. createtopic (tópico de cadeia de caracteres) </li> </ul>| **Com suporte** |
| Filas temporárias |<ul> <li> JMSContext.createTemporaryQueue() </li> </ul>| **Com suporte** |
| Tópicos temporários |<ul> <li> JMSContext.createTemporaryTopic() </li> </ul>| **Com suporte** |
| Produtor de mensagem/<br/> JMSProducer |<ul> <li> JMSContext. createproduzr () </li> </ul>| **Com suporte** |
| Navegadores de fila |<ul> <li> JMSContext. createbrowser (fila de fila) </li> <li> JMSContext. createbrowser (fila de fila, Cadeia de caracteres messageSelector) </li> </ul> | **Com suporte** |
| Consumidor de mensagens/ <br/> JMSConsumer | <ul> <li> JMSContext. createconsumer (destino destino) </li> <li> JMSContext. createconsumer (destino de destino, Cadeia de caracteres messageSelector) </li> <li> JMSContext. createconsumer (destino de destino, Cadeia de caracteres messageSelector, booliano nolocal)</li> </ul>  <br/> nolocal não tem suporte no momento | **Com suporte** |
| Assinaturas duráveis compartilhadas | <ul> <li> JMSContext. createSharedDurableConsumer (tópico de tópico, nome da cadeia de caracteres) </li> <li> JMSContext. createSharedDurableConsumer (tópico topic, String name, String messageSelector) </li> </ul>| **Com suporte**|
| Assinaturas duráveis não compartilhadas | <ul> <li> JMSContext. createDurableConsumer (tópico de tópico, nome da cadeia de caracteres) </li> <li> createDurableConsumer (tópico de tópico, String name, String messageSelector, Boolean nolocal) </li> </ul> <br/> nolocal não tem suporte no momento e deve ser definido como false | **Com suporte** |
| Assinaturas não duráveis compartilhadas |<ul> <li> JMSContext. createSharedConsumer (tópico topic, String sharedSubscriptionName) </li> <li> JMSContext. createSharedConsumer (tópico topic, String sharedSubscriptionName, String messageSelector) </li> </ul> | **Com suporte** |
| Assinaturas não duráveis não compartilhadas |<ul> <li> JMSContext. createconsumer (destino destino) </li> <li> JMSContext. createconsumer (destino de destino, Cadeia de caracteres messageSelector) </li> <li> JMSContext. createconsumer (destino de destino, Cadeia de caracteres messageSelector, booliano nolocal) </li> </ul> <br/> nolocal não tem suporte no momento e deve ser definido como false | **Com suporte** |
| Seletores de mensagem | depende do consumidor criado | **Com suporte** |
| Atraso de entrega (mensagens agendadas) | <ul> <li> JMSProducer. setDeliveryDelay (Long deliveryDelay) </li> </ul>|**Com suporte**|
| Mensagem criada |<ul> <li> JMSContext. CreateMessage () </li> <li> JMSContext.createBytesMessage() </li> <li> JMSContext.createMapMessage() </li> <li> JMSContext. createObjectMessage (objeto serializável) </li> <li> JMSContext.createStreamMessage() </li> <li> JMSContext.createTextMessage() </li> <li> JMSContext. createTextMessage (texto da cadeia de caracteres) </li> </ul>| **Com suporte** |
| Transações distribuídas || Sem suporte |
