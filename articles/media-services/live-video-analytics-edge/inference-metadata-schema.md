---
title: Esquema de metadados de inferência – Azure
description: Neste artigo, você conhecerá o esquema de metadados de inferência.
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: 2de437577dc00692fb98c46fec32bfaa6612dc99
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92019487"
---
# <a name="inference-metadata-schema"></a>Esquema de metadados de inferência 

Cada objeto de inferência, independentemente de usar o contrato baseado em HTTP ou o contrato baseado em gRPC, segue o modelo de objeto descrito neste tópico.

## <a name="object-model"></a>Modelo de objeto

![Modelo de objeto](./media/inference-metadata-schema/object-model.png)
 
|Definição de tipo|Descrição|
|---|---|
|Marca|Marca ou rótulo associado ao resultado. Alng com marcação, você até mesmo obtém o valor de confiança associado à marca.|
|Atributo|Atributos adicionais associados ao resultado. Você pode adicionar novos atributos que recebe do mecanismo inferência junto com o valor de confiança.|
|Lista de Atributos|Lista de atributos opcionais.|
|Retângulo|Região retangular relativa ao canto superior esquerdo da imagem. As propriedades obrigatórias serão "tamanho", "largura", "altura" e "distância da borda superior em relação à origem".|
|classificação|O rótulo do classificador geralmente é aplicável a todo o exemplo. Com a ajuda da "marca", você pode classificar o resultado.|
|Entidade|Entidade detectada ou identificada no exemplo. Quando uma entidade é detectada pelo mecanismo de inferência, ela recebe uma "marca", atributos adicionais que foram inferidos e as coordenadas de uma caixa retangular em torno da entidade encontrada são retornadas.|
|Evento|Evento detectado no exemplo. Quando um evento é detectado no exemplo, o nome do evento e as propriedades específicas do evento são retornadas.|
|Movimento|Movimento detectado no exemplo. Quando o movimento é detectado no exemplo, as coordenadas de uma caixa delimitadora retangular em que o movimento é detectado são retornadas.|
|Text|O texto associado ao exemplo junto com o carimbo de data/hora inicial e final do texto é retornado.|
|Outro|Retorna outras informações genéricas de conteúdo.|

O exemplo abaixo contém um só evento com alguns tipos de inferência compatíveis:

```
[ 
  // Light Detection 
  { 
    "type": "classification", 
    "subtype": "lightDetection", 
    "classification": { 
      "tag": { "value": "daylight", "confidence": 0.86 }, 
      "attributes": [ 
          { "name": "isBlackAndWhite", "value": "false", "confidence": 0.71 } 
      ] 
    } 
  }, 
 
  // Motion Detection 
  { 
    "type": "motion", 
    "subtype": "motionDetection", 
    "motion": 
    { 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    } 
  }, 
 
  // Yolo V3 
  { 
    "type": "entity", 
    "subtype": "objectDetection",     
    "entity": 
    { 
      "tag": { "value": "dog", "confidence": 0.97 }, 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    } 
  }, 
 
  // Vehicle Identification 
  { 
    "type": "entity", 
    "subtype": "vehicleIdentification",     
    "entity": 
    { 
      "tag": { "value": "007-SPY", "confidence": 0.82 }, 
      "attributes":[   
        { "name": "color", "value": "black", "confidence": 0.90 }, 
        { "name": "body", "value": "coupe", "confidence": 0.87 }, 
        { "name": "make", "value": "Aston Martin", "confidence": 0.35 }, 
        { "name": "model", "value": "DBS V12", "confidence": 0.33 } 
      ], 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    } 
  }, 
 
  // People Identification 
  { 
    "type": "entity", 
    "subtype": "peopleIdentification",     
    "entity": 
    { 
      "tag": { "value":"Erwin Schrödinger", "confidence": 0.50 }, 
      "attributes":[   
        { "name": "age", "value": "73", "confidence": 0.87 }, 
        { "name": "glasses", "value": "yes", "confidence": 0.94 } 
      ], 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    }, 
 
    // Open type coming from the gRPC Map 
    "extensions":  
    { 
      "vector": "e1xkaXNwbGF5c3R5bGUgaVxoYmFyIHtcZnJhYyB7ZH17ZHR9fVx2ZXJ0IFxQc2kgKHQpXHJhbmdsZSA9e1xoYXQge0h9fVx2ZXJ0IFxQc2kgKHQpXHJhbmdsZSB9KQ==", 
      "skeleton": "p1,p2,p3,p4" 
    } 
  }, 
 
  // Captions 
  {     
    "type": "text", 
    "subtype": "speechToText",   
    "text": 
    { 
      "value": "Humor 75%. Confirmed. Self-destruct sequence in T minus 10… 9…", 
      "language": "en-US", 
      "startTimestamp": 12000, 
      "endTimestamp": 13000 
    } 
]
```

## <a name="next-steps"></a>Próximas etapas

- [Contrato de dados gRPC](./grpc-extension-protocol.md)
- [Contrato de dados HTTP](./http-extension-protocol.md)