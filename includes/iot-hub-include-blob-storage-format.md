---
title: Arquivo de inclusão
description: Arquivo de inclusão
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/15/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: c779147e464a592d45da8a9a2d8e812320dc23e8
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172545"
---
<!-- This is the note explaining about the avro and json formats when routing to blob storage. -->
> [!NOTE]
> Os dados podem ser gravados no armazenamento de blobs no formato [Apache Avro](https://avro.apache.org/), que é o padrão, ou em JSON (versão prévia). 
>    
> A capacidade de codificar o formato JSON está na versão prévia em todas as regiões em que o Hub IoT está disponível, exceto Leste dos EUA, oeste dos EUA e Oeste da Europa. O formato de codificação pode ser definido somente no momento em que o ponto de extremidade de armazenamento de blobs é configurado. O formato não pode ser alterado para um ponto de extremidade que já foi configurado. Ao usar a codificação JSON, você deve definir o contentType como JSON e o contentEncoding como UTF-8 nas propriedades do sistema de mensagem. 
>
> Para obter informações mais detalhadas sobre como usar um ponto de extremidade de armazenamento de blobs, veja as [orientações sobre o roteamento para o armazenamento de blobs](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#azure-blob-storage).
>