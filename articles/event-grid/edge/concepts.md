---
title: Conceitos-IoT Edge de grade de eventos do Azure | Microsoft Docs
description: Conceitos na grade de eventos em IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 8314447e7d5d282eb428ec9316c4eef6844a7423
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98682372"
---
# <a name="event-grid-concepts"></a>Conceitos da Grade de Eventos

Este artigo descreve os principais conceitos da Grade de Eventos do Azure.

## <a name="events"></a>Eventos

Um evento é a menor quantidade de informações que descreve por completo algo que aconteceu no sistema. Todos os eventos apresentam informações comuns: origem do evento, hora em que o evento ocorreu e identificador exclusivo. Cada evento também apresenta informações específicas que são relevantes somente para o tipo de evento em questão. O suporte para um evento de tamanho de até 1 MB está atualmente em visualização.

Para as propriedades incluídas em um evento, consulte esquema de [eventos da grade de eventos do Azure](event-schemas.md).

## <a name="publishers"></a>Publicadores

Um editor é o usuário ou a organização que decide enviar eventos para a Grade de Eventos. Você pode publicar eventos em seu próprio aplicativo.

## <a name="event-sources"></a>Origens de eventos

A origem de um evento é onde o evento acontece. Cada origem do evento está relacionada a um ou mais tipos de evento. Por exemplo, o Armazenamento do Azure é a origem dos eventos criados pelo blob. Seu aplicativo é a origem dos eventos personalizados definidos. As origens do evento são responsáveis por enviar eventos para a Grade de Eventos.

## <a name="topics"></a>Tópicos

O tópico da grade de eventos fornece um ponto de extremidade em que a fonte envia eventos. O editor cria o tópico de grade de eventos e decide se uma origem do evento precisa de um tópico ou mais de um tópico. Um tópico é usado para uma coleção de eventos relacionados. Para reagir a determinados tipos de evento, os assinantes decidem quais tópicos assinar.

Ao projetar seu aplicativo, você tem a flexibilidade de decidir quantos tópicos devem ser criados. Para soluções maiores, crie um tópico personalizado para cada categoria de eventos relacionados. Por exemplo, considere um aplicativo que envia eventos relacionados à modificação de contas de usuário e ordens de processamento. É improvável que qualquer manipulador de eventos queira ambas as categorias de eventos. Crie dois tópicos personalizados e permita que os manipuladores de eventos assinem o que for interessante para eles. Para soluções pequenas, você pode preferir enviar todos os eventos para um único tópico. Assinantes de evento podem filtrar par os tipos de evento que desejam.

Consulte a [documentação da API REST](api.md) sobre como gerenciar tópicos na grade de eventos.

## <a name="event-subscriptions"></a>Assinaturas de evento

Uma assinatura informa à Grade de Eventos do Azure com eventos em um tópico você está interessado em receber. Ao criar a assinatura, você fornece um ponto de extremidade para manipular o evento. Você pode filtrar os eventos que são enviados ao ponto de extremidade. 

Consulte a [documentação da API REST](api.md) sobre como gerenciar assinaturas na grade de eventos.

## <a name="event-handlers"></a>Manipuladores de eventos

Sob a perspectiva de uma Grade de Eventos, um manipulador de eventos é o local em que o evento é enviado. O manipulador executa uma ação adicional para processar o evento. Grade de eventos do Azure dá suporte a vários tipos de manipulador. Você pode usar um serviço do Azure com suporte ou seu próprio gancho da Web como o manipulador. Dependendo do tipo de manipulador, a Grade de Eventos segue diferentes mecanismos para assegurar a entrega do evento. Se o manipulador de eventos de destino for um gancho da Web HTTP, o evento será repetido até que o manipulador retorne um código de status `200 – OK` . Para o Hub do Edge, se o evento for entregue sem nenhuma exceção, ele será considerado com êxito.

## <a name="security"></a>Segurança

A Grade de Eventos proporciona segurança na assinatura em tópicos e na publicação de tópicos. Para saber mais, confira [Event Grid security and authentication](security-authentication.md) (Segurança e autenticação da Grade de Eventos).

## <a name="event-delivery"></a>Entrega de eventos

Se a Grade de Eventos não puder confirmar que um evento foi recebido pelo ponto de extremidade do assinante, ela repetirá a entrega do evento. Para saber mais, confira [Event Grid message delivery and retry](delivery-retry.md) (Entrega e repetição de mensagens da Grade de Eventos).

## <a name="batching"></a>Separação em lotes

Ao usar um tópico personalizado, os eventos sempre devem ser publicados em uma matriz. Para cenários de baixa taxa de transferência, a matriz terá apenas um valor. Para casos de uso de alto volume, é recomendável que você execute vários eventos em lote por publicação para obter maior eficiência. Lotes podem ter até 1 MB. Cada evento ainda deve ser maior que 1 MB (versão prévia).