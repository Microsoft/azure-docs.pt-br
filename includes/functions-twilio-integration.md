---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: b4a1891eadf2e36bcb94b9f605d91f227fa83a3f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96026661"
---
Este exemplo envolve o uso do serviço [Twilio](https://www.twilio.com/) para enviar mensagens SMS a um telefone celular. O Azure Functions já tem suporte para Twilio por meio da [Associação com o Twilio](../articles/azure-functions/functions-bindings-twilio.md) e o exemplo usa esse recurso.

A primeira coisa de que você precisa é uma conta do Twilio. É possível criar uma gratuitamente em https://www.twilio.com/try-twilio. Quando tiver uma conta, adicione as três seguintes **configurações de aplicativo** ao seu aplicativo de função.

| Nome da configuração do aplicativo | Descrição do valor |
| - | - |
| **TwilioAccountSid**  | A SID de sua conta do Twilio |
| **TwilioAuthToken**   | O token de autenticação de sua conta do Twilio |
| **TwilioPhoneNumber** | O número de telefone associado à sua conta do Twilio. Ele é usado para enviar mensagens SMS. |