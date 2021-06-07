---
title: incluir arquivo
description: incluir arquivo
services: service-bus-messaging
author: clemensv
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/24/2020
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: ca483d0b71bde945a7e46da785dd6a76b3a8f177
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98693385"
---
A opção de protocolo AMQP-over-WebSockets é executada pela porta TCP 443 assim como a API HTTP/REST, mas, de outra forma, funcionalmente idêntica com AMQP simples. Essa opção tem uma latência de conexão inicial um pouco maior devido a viagens de ida e volta extra de handshake e um pouco mais de sobrecarga como a compensação para compartilhar a porta HTTPS. Se esse modo estiver selecionado, a porta TCP 443 será suficiente para comunicação. As opções a seguir permitem selecionar o modo de WebSockets AMQP ou AMQP simples:

| Idioma | Opção   |
| -------- | ----- |
| .NET     | Propriedade [ServiceBusConnection. TransportType](/dotnet/api/microsoft.azure.servicebus.servicebusconnection.transporttype) com [TransportType. AMQP](/dotnet/api/microsoft.azure.servicebus.transporttype) ou [TransportType. AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype) |
| Java     | [com. Microsoft. Azure. ServiceBus. ClientSettings](/java/api/com.microsoft.azure.servicebus.clientsettings.clientsettings) com com [. Microsoft. Azure. ServiceBus. Primitives. ExportType. AMQP](/java/api/com.microsoft.azure.servicebus.primitives.transporttype) ou [com.Microsoft.Azure.ServiceBus.Primitives.TransportType.AMQP_WEB_SOCKETS](/java/api/com.microsoft.azure.servicebus.primitives.transporttype) |
| Nó  | [ServiceBusClientOptions](/javascript/api/@azure/service-bus/servicebusclientoptions) tem um `webSocket` argumento de construtor. |
| Python | [ServiceBusClient.transport_type](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.ServiceBusClient) com [TransportType. AMQP](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.TransportType) ou [TransportType. AmqpOverWebSocket](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.TransportType) |