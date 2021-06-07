---
title: incluir arquivo
description: incluir arquivo
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/15/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: b20c97bf919073b01f564b59b3a55eccb7614005
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89043005"
---
<!-- This is the note explaining about the avro and json formats when routing to blob storage. -->
> [!NOTE]
> Os dados podem ser gravados no armazenamento de blobs no formato [Apache Avro](https://avro.apache.org/), que é o padrão, ou em JSON (versão prévia). 
>    
> A capacidade de codificar o formato JSON está na versão prévia em todas as regiões em que o Hub IoT está disponível, exceto Leste dos EUA, oeste dos EUA e Oeste da Europa. O formato de codificação pode ser definido somente no momento em que o ponto de extremidade de armazenamento de blobs é configurado. O formato não pode ser alterado para um ponto de extremidade que já foi configurado. Ao usar a codificação JSON, você deve definir o contentType como JSON e o contentEncoding como UTF-8 nas propriedades do sistema de mensagem. 
>
> Para obter informações mais detalhadas sobre como usar um ponto de extremidade do Armazenamento de Blobs, veja as [orientações sobre o roteamento para o armazenamento](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#azure-storage-as-a-routing-endpoint).
>