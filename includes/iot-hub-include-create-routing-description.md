---
title: Arquivo de inclusão
description: Arquivo de inclusão
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/05/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: b640a3cb9382ad72bb48e06c6a7074c96409e2e4
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172543"
---
<!-- description of message routing used in the Azure CLI, PowerShell, and RM routing articles.-->

Você vai para rotear mensagens para recursos diferentes com base nas propriedades anexadas à mensagem pelo dispositivo simulado. As mensagens que não são roteadas de forma personalizada são enviadas ao ponto de extremidade padrão (mensagens/eventos). No próximo tutorial, você envia mensagens ao Hub IoT e as vê roteadas para os diferentes destinos.

|value |Result|
|------|------|
|level="storage" |Grave no Armazenamento do Azure.|
|level="critical" |Grave em uma fila do Barramento de Serviço. Um aplicativo lógico recupera a mensagem da fila e usa o Office 365 para enviar a mensagem por e-mail.|
|padrão |Exiba esses dados usando o Power BI.|

A primeira etapa é configurar o ponto de extremidade ao qual os dados serão roteados. A segunda etapa é configurar a rota de mensagem que usa esse ponto de extremidade. Depois de configurar o roteamento, você pode exibir as rotas de mensagem e os pontos de extremidade no portal.