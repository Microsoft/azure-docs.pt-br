---
title: Visão geral do SDK de SMS para os Serviços de Comunicação do Azure
titleSuffix: An Azure Communication Services concept document
description: Fornece uma visão geral do SDK de SMS e suas ofertas.
author: mikben
manager: jken
services: azure-communication-services
ms.author: prakulka
ms.date: 03/26/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: c25dfea077510580daf2c1aab584ab9ff5caa7fe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105930415"
---
# <a name="sms-sdk-overview"></a>Visão geral do SDK de SMS

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-phone-numbers.md)]

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Os SDKs de SMS dos Serviços de Comunicação do Azure podem ser usados para adicionar mensagens SMS aos seus aplicativos.

## <a name="sms-sdk-capabilities"></a>Funcionalidades do SDK de SMS

A lista a seguir apresenta o conjunto de recursos disponíveis atualmente em nossos SDKs.

| Grupo de recursos | Funcionalidade                                                                            | JS  | Java | .NET | Python |
| ----------------- | ------------------------------------------------------------------------------------- | --- | ---- | ---- | ------ |
| Principais funcionalidades | Enviar e receber mensagens SMS                                                         | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Habilitar notificações de entrega para mensagens enviadas                                             | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Todos os conjuntos de caracteres (suporte a idioma/Unicode)                                         | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Suporte para mensagens longas (até 2048 bytes)                                          | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Concatenação automática de mensagens longas                                                   | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Enviar mensagens para vários destinatários por vez                                        | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Suporte para idempotência                                                               | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Marcas personalizadas para mensagens.                                                             | ✔️   | ✔️    | ✔️    | ✔️      |
| Eventos            | Usar a Grade de Eventos para configurar webhooks para receber mensagens de entrada e relatórios de entrega | ✔️   | ✔️    | ✔️    | ✔️      |
| Número do telefone      | Números de chamada gratuita                                                                     | ✔️   | ✔️    | ✔️    | ✔️      |
| Chamada PSTN      | Adicionar funcionalidades de chamada PSTN ao seu número de chamada gratuita habilitado para SMS                    | ✔️   | ✔️    | ✔️    | ✔️      |

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Introdução ao envio de SMS](../../quickstarts/telephony-sms/send.md)

Os seguintes documentos podem ser do seu interesse:

- Familiarize-se com os conceitos gerais de [SMS](../telephony-sms/concepts.md)
- Obter um [número de telefone](../../quickstarts/telephony-sms/get-phone-number.md) compatível com SMS
- [Tipos de número de telefone nos Serviços de Comunicação do Azure](../telephony-sms/plan-solution.md)
