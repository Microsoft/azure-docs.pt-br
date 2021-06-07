---
title: incluir arquivo
description: incluir arquivo
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/05/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 3a5747912a18b8406cabd03c9823e3f6fe6898a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "72808851"
---
<!-- description of message routing used in the Azure CLI, PowerShell, and RM routing articles.-->

Você vai para rotear mensagens para recursos diferentes com base nas propriedades anexadas à mensagem pelo dispositivo simulado. As mensagens que não são roteadas de forma personalizada são enviadas ao ponto de extremidade padrão (mensagens/eventos). No próximo tutorial, você envia mensagens ao Hub IoT e as vê roteadas para os diferentes destinos.

|Valor |Result|
|------|------|
|level="storage" |Grave no Armazenamento do Azure.|
|level="critical" |Grave em uma fila do Barramento de Serviço. Um aplicativo lógico recupera a mensagem da fila e usa o Office 365 para enviar a mensagem por e-mail.|
|padrão |Exiba esses dados usando o Power BI.|

A primeira etapa é configurar o ponto de extremidade ao qual os dados serão roteados. A segunda etapa é configurar a rota de mensagem que usa esse ponto de extremidade. Depois de configurar o roteamento, você pode exibir as rotas de mensagem e os pontos de extremidade no portal.