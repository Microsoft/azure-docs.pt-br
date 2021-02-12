---
title: incluir arquivo
description: incluir arquivo
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 11/19/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: c37e2c28eb6aee9edf4bdd97066ce5f15e7447c1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005616"
---
## <a name="message-headers"></a>Cabeçalhos de mensagem
Essas são as propriedades que você recebe nos cabeçalhos de mensagem:

| Nome da propriedade | Descrição |
| ------------- | ----------- | 
| aeg-subscription-name | Nome da assinatura do evento. |
| aeg-delivery-count | Número de tentativas feitas para o evento. |
| aeg-event-type | <p>Tipo do evento.</p><p>Pode ser um dos seguintes valores:</p><ul><li>SubscriptionValidation</li><li>Notificação</li><li>SubscriptionDeletion</li></ul> | 
| aeg-metadata-version | <p>A versão de metadados do evento.<p> Para o **esquema de evento da Grade de Eventos**, essa propriedade representa a versão de metadados e, para o **esquema de evento da nuvem**, ela representa a **versão de especificação**. </p>|
| aeg-data-version | <p>Versão de dados do evento.</p><p>Para o **esquema de evento da Grade de Eventos**, essa propriedade representa a versão dos dados e para o **esquema de evento da nuvem**, ela não se aplica.</p> |
| aeg-output-event-id | ID do evento da Grade de Eventos. |


