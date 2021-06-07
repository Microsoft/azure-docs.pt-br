---
title: incluir arquivo
description: incluir arquivo
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 54f4835a904b897370cf9f075ae3c005b1114992
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95561614"
---
[Obter integridade do ponto de extremidade](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) na API REST fornece o status de integridade dos pontos de extremidades, bem como o último erro conhecido, para identificar o motivo pelo qual um ponto de extremidade não está íntegro. A tabela a seguir lista os erros mais comuns.

|Último erro conhecido|Descrição/quando ocorre|Possível mitigação|
|-----|-----|-----|
|Transitório|Ocorreu um erro transitório e o Hub IoT tentará executar a operação novamente.|Observe [os logs de recursos de rotas](../articles/iot-hub/monitor-iot-hub-reference.md#routes).|
|InternalError|Ocorreu um erro ao entregar uma mensagem a um ponto de extremidade.|Essa é uma exceção interna, mas também observa os [logs de recursos de rotas](../articles/iot-hub/monitor-iot-hub-reference.md#routes).|
|Não Autorizado|O Hub IoT não está autorizado a enviar mensagens para o ponto de extremidade especificado.|Valide se a cadeia de conexão está atualizada para o ponto de extremidade. Se ele foi alterado, considere uma atualização no Hub IoT. Se o ponto de extremidade usar identidade gerenciada, verifique se a entidade de segurança do Hub IoT tem as permissões necessárias no destino.|
|Acelerado|O Hub IoT está sendo limitado durante a gravação de mensagens no ponto de extremidade.|Examine os limites de limitação para o ponto de extremidade afetado. Modifique as configurações do ponto de extremidade para escalar verticalmente, se necessário.|
|Tempo limite|Tempo limite da operação.|Repita a operação.|
|Não encontrado|O recurso de destino não existe.|Verifique se o recurso de destino existe.|
|Contêiner não encontrado|O contêiner de armazenamento não existe.|Verifique se o contêiner de armazenamento existe.|
|Contêiner desabilitado|O contêiner de armazenamento está desabilitado.|Verifique se o contêiner de armazenamento está habilitado.|
|MaxMessageSizeExceeded|O roteamento de mensagens tem um limite de tamanho de mensagem de 256Kb. o tamanho da mensagem que está sendo roteado excedeu esse limite.|Verifique se o tamanho da mensagem pode ser reduzido usando menos Propriedades de aplicativo ou menos enriquecimentos de mensagem.|
|PartitioningAndDuplicateDetectionNotSupported|O barramento de serviço pode não ter a detecção de duplicidades habilitada.|Desabilite a detecção de duplicidades do barramento de serviço ou considere usar uma entidade sem detecção de duplicidades.|
|SessionfulEntityNotSupported|O barramento de serviço pode não ter sessões habilitadas.|Desabilite a sessão do barramento de serviço ou considere usar uma entidade sem sessões.|
|NoMatchingSubscriptionsForMessage|Não há nenhuma assinatura para gravar a mensagem no tópico do barramento de serviço.|Crie uma assinatura para mensagens do Hub IoT a serem roteadas.|
|EndpointExternallyDisabled|O ponto de extremidade não está em um estado ativo para que o Hub IoT possa enviar mensagens para ele.|Habilite o ponto de extremidade para trazê-lo de volta para o estado ativo.|
|DeviceMaximumQueueDepthExceeded|O limite de tamanho do barramento de serviço foi atingido.|Considere remover mensagens dos hubs de eventos de destino para permitir que novas mensagens sejam ingeridas nos hubs de eventos.|