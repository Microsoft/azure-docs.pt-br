---
title: Entrega e repetição-grade de eventos do Azure IoT Edge | Microsoft Docs
description: Entrega e nova tentativa na grade de eventos em IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: aa0b3a05fb26f6be951b697145d7b22e03b7792d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "86171611"
---
# <a name="delivery-and-retry"></a>Entregar e tentar novamente

A entrega proporcionada pela Grade de Eventos tem um tempo de duração. Ele tenta entregar cada mensagem pelo menos uma vez para cada assinatura correspondente imediatamente. Se o ponto de extremidade de um assinante não confirmar o recebimento de um evento ou se houver uma falha, a grade de eventos tentará a entrega com base em uma **agenda de repetição** fixa e uma **política de repetição**.  Por padrão, o módulo de grade de eventos entrega um evento por vez ao Assinante. A carga é, no entanto, uma matriz com um único evento. Você pode fazer com que o módulo entregue mais de um evento por vez, habilitando o recurso de envio em lote de saída. Para obter detalhes sobre esse recurso, consulte [envio em lote de saída](delivery-output-batching.md).  

> [!IMPORTANT]
>Não há suporte para persistência para dados de evento. Isso significa que a reimplantação ou reinicialização do módulo de grade de eventos fará com que você perca todos os eventos que ainda não foram entregues.

## <a name="retry-schedule"></a>Agenda de repetição

A grade de eventos aguarda até 60 segundos por uma resposta depois de entregar uma mensagem. Se o ponto de extremidade do assinante não ACK da resposta, a mensagem será enfileirada em uma de nossas filas de retirada para novas tentativas.

Há duas filas de back-configure pré-configuradas que determinam o agendamento no qual uma nova tentativa será tentada. São eles:

| Agenda | Descrição |
| ---------| ------------ |
| 1 minuto | As mensagens que acabam aqui são tentadas a cada minuto.
| 10 minutos | As mensagens que terminam aqui são tentadas a cada 10 minutos.

### <a name="how-it-works"></a>Como ele funciona

1. A mensagem chega ao módulo de grade de eventos. É feita uma tentativa de entregá-la imediatamente.
1. Se a entrega falhar, a mensagem será enfileirada em uma fila de 1 minuto e repetida após um minuto.
1. Se a entrega continuar a falhar, a mensagem será enfileirada na fila de 10 minutos e repetida a cada 10 minutos.
1. As entregas são tentadas até que os limites de política bem-sucedidos ou de repetição sejam atingidos.

## <a name="retry-policy-limits"></a>Limites de política de repetição

Há duas configurações que determinam a política de repetição. São eles:

* Número máximo de tentativas
* Tempo de vida (TTL) do evento

Um evento será Descartado se qualquer um dos limites da política de repetição for atingido. O agendamento de repetição em si foi descrito na seção agendamento de repetição. A configuração desses limites pode ser feita para todos os assinantes ou por assinatura. A seção a seguir descreve cada um deles mais detalhadamente.

## <a name="configuring-defaults-for-all-subscribers"></a>Configurando padrões para todos os assinantes

Há duas propriedades: `brokers__defaultMaxDeliveryAttempts` e `broker__defaultEventTimeToLiveInSeconds` que podem ser configuradas como parte da implantação da grade de eventos, que controla os padrões de política de repetição para todos os assinantes.

| Nome da propriedade | Descrição |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | Número máximo de tentativas para entregar um evento. Valor padrão: 30.
| `broker__defaultEventTimeToLiveInSeconds` | TTL do evento em segundos após o qual um evento será Descartado se não for entregue. Valor padrão: **7200** segundos

## <a name="configuring-defaults-per-subscriber"></a>Configurando padrões por assinante

Você também pode especificar limites de política de repetição em uma base por assinatura.
Consulte nossa [documentação de API](api.md) para obter informações sobre como configurar padrões por assinante. Os padrões de nível de assinatura substituem as configurações de nível de módulo.

## <a name="examples"></a>Exemplos

O exemplo a seguir configura a política de repetição no módulo de grade de eventos com maxNumberOfAttempts = 3 e TTL de evento de 30 minutos

```json
{
  "Env": [
    "broker__defaultMaxDeliveryAttempts=3",
    "broker__defaultEventTimeToLiveInSeconds=1800"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```

O exemplo a seguir configura uma assinatura de gancho da Web com maxNumberOfAttempts = 3 e TTL do evento de 30 minutos

```json
{
 "properties": {
  "destination": {
   "endpointType": "WebHook",
   "properties": {
    "endpointUrl": "<your_webhook_url>",
    "eventDeliverySchema": "eventgridschema"
   }
  },
  "retryPolicy": {
   "eventExpiryInMinutes": 30,
   "maxDeliveryAttempts": 3
  }
 }
}
```
